---
layout: post
title: Image resize with Lambda@edge (feat.AWS-SDK v.3)
tags: [lambda, lambda edge, aws, aws-sdk, image-resize, image, serverless]
excerpt_separator: <!--more-->
---

# Lambda@edge를 이용한 Image resize 시스템 개발기

서비스 성능 개선에 필요한 image resize 시스템을 개발해보았습니다! 🤩

- AWS Lambda@edge, (Runtime Env: Node v.16)
- sharp
- aws-sdk v.3

![image-resize-main]({{ "../assets/img/imageResizeLambdaEdge/Resize-images-main.jpeg" | relative_url }})

<!--more-->

# Image resize

Image resize는 원본 이미지를 원하는 크기와 형식(jpeg, png, webp 등)에 맞게 조절하는 것을 말합니다.

## Image resize가 왜 필요한가요?

서비스 기능 중에 사용자들의 그림 파일을 올릴 수 있는 기능이 있습니다.

주로, 스마트폰의 그림들을 많이 올리게 되는데, iPhone13을 기준으로 사진을 알아보면,

세로로 찍었을 때, 보통 3.5MB 정도의 용량에 3024 x 4032 픽셀 크기에 12MP의 해상도를 가지고 있습니다.

해당 이미지가 3024 x 4032 픽셀 크기에 꽉 채워져 표현된다면, 선명하게 보여지겠지만, **보통 서비스가 표현되는 브라우저는 그정도로 크게 표현되는 경우는 거의 없습니다.**

또한, **3.5MB나 되는 무거운 용량의 이미지를 Internet 통신을 통해 전부 받아오려면 많은 시간이 소요됩니다.**

이미지를 받아오기 까지, 그 내용을 제대로 보지 못하는 사용자의 입장에선 이건 굉장히 불편할 수 있습니다. 🤬

![load-image-partially]({{ "../assets/img/imageResizeLambdaEdge/load-image-partially.gif" | relative_url }})

**따라서, 대용량 이미지를 적절한 크기로 줄여주는 것이 좋을 것 같습니다!**

# 여러가지 image resize 방법들

현 회사의 서비스에선 AWS S3를 이용해 이미지 같은 static asset들을 저장하고 있습니다.

여기서 여러가지 Image resize 방법들을 생각해볼 수 있습니다.

## 원본 이미지를 저장할 때, resize해서 보내기

사용자에게 원본 이미지를 받아서 client에서 resizing한 후, AWS S3에 저장하는 방법입니다.

브라우저의 HTML 환경에선 \<canvas\>를 이용해 image를 resize할 수 있습니다.

간편하게 HTML2Canvas라는 라이브러리를 이용할 수도 있습니다.

![client-resizing]({{ "../assets/img/imageResizeLambdaEdge/client-image-resize.png" | relative_url }})

하지만 아래와 같은 문제가 있습니다.

- 원본 이미지의 부재

이 경우엔, **원본 이미지를 아예 잃어버리기 때문에, 훗날 서비스의 변동으로 다른 크기와 해상도의 이미지를 제공해야 할 때, 문제가 될 수 있습니다.**

- 클라이언트 성능 저하 및 UX 저하

유연한 대응을 위해 여러 size의 이미지로 변환해 보낼 수 있지만, **클라이언트의 computing 자원을 소모하게 되고, 통신량 또한 많아져 사용자의 불편을 초래할 수 있습니다.**

## AWS serverless 서비스 이용하기

AWS S3는 AWS serverless 서비스들의 트리거가 됩니다.

따라서, AWS S3를 update하거나 접근 등등의 S3에 발생할 수 있는 여러 event들에, AWS Lambda나 AWS Lambda@edge같은 serverless 서비스들을 연동하여 사용할 수 있습니다.

### AWS Lambda로 여러 size의 이미지로 변환해 저장

S3에 원본 이미지 저장한 후에, 원본 이미지를 가지고 여러 size의 이미지로 변환해 저장하는 방법입니다.

"On-The-Fly 이미지 리사이징"이라고도 합니다.

AWS Lambda@edge가 나오기 전에 많은 기업들에서 이용하던 방법입니다.

![on-the-fly]({{ "../assets/img/imageResizeLambdaEdge/on-the-fly.png" | relative_url }})

- S3 저장 용량과 과금

클라이언트의 자원을 뺏지 않고, 원본 이미지도 지킬 수 있지만, **S3에 저장하는 용량이 크게 늘어납니다.**

AWS S3의 요금 책정은 기본적으로 저장된 용량에 기반합니다.

따라서, 저장된 용량이 많아지면 많아질수록 과금이 커질 수 밖에 없습니다.

- 유연하지 못한 resizing

또한, 정해놓은 size 정책 이외의 image가 필요하게 되면 문제가 될 수 있습니다.

원본 이미지를 저장 할 때, Lambda를 통해서 만들어 놓은 image만 사용해야 하기 때문입니다.

이외의 image가 필요하다면 수동으로 직접 resizing 해야합니다.

# AWS Lambda@edge

AWS Lambda@edge는 Amazon CloudFront에서만 실행되는 특별한 AWS Lambda라고 할 수 있습니다.

![lambda@edge]({{ "../assets/img/imageResizeLambdaEdge/lambda@edge.png" | relative_url }})

S3 - CloudFront - Client의 흐름을 더 자세히 펼쳐보면 다음과 같습니다.

![lambda@edge]({{ "../assets/img/imageResizeLambdaEdge/cloudfront-events-that-trigger-lambda-functions.png" | relative_url }})

여기서 **Origin response** 부분에 image resize를 위한 Lambda@edge를 발동시키도록 설정했습니다.

### Origin response를 이용한 이유

우선 "Viewer request", "Viewer response"의 Lambda@edge는 client 요청 때마다 발동되게 됩니다.

하지만, "Origin response", "Origin request"의 Lambda@edge는 CloudFront가 요청 url의 cache를 가지고 있지 않을 때, origin server 즉, S3에 요청하게 되는데, 이 때 발동되게 됩니다.

Origin response로 resize된 image를 넘겨주면 CloudFront는 resize된 image를 cache하고 일정 시간동안 같은 요청이 있을 때마다, cache된 image를 내려주게 됩니다.

cache된 이미지를 내려주게 되면 origin으로 요청하지 않게 되고, **Lambda@edge 발동을 최소화할 수 있어 인프라 비용을 최소화할 수 있습니다.**

## AWS Lambda@edge를 이용했을 때 이점!

on-the-fly 방법의 단점이 해결됩니다. 😁

- 유연한 resizing

원본 image를 가로채어 resizing하기 때문에, 기본적으로 원본 image를 요청합니다.

여기서 queryString을 통한 구체적인 resize 옵션을 설정하는데, queryString을 다르게 하면 해당 **옵션에 맞게 image가 resize되어 오게 할 수 있습니다.**

```javascript
${url}?w=1000&h=1000&e="webp"
```

- 인프라 비용 절감

S3에는 원본 이미지만 저장하게 되므로 S3 비용을 최소화 할 수 있습니다.

on-the-fly 방식에선 S3에도 저장되고, CloudFront에도 저장이 되는데, **Lambda@edge를 사용하면 resize된 image가 CloudFront에만 저장되어 비용 절감을 기대할 수 있습니다!**

# Image resize 시스템 개발기

기본적으로 Amazon 공식 예시가 잘 돼있어 많이 참고가 되었습니다!

다만, aws-sdk v.2 기준으로 코드가 되어있기 때문에, 최신 aws-sdk v.3에 맞춘 수정이 필요했습니다.

(aws-sdk v.2는 2023년부터 maintain 상태에 도입한다고 합니다.)

## 로컬 테스트

Lambda@edge 함수 작성에 앞서 로컬 테스트를 진행했습니다.

### sharp 라이브러리



## aws-sdk v.3

문제 response가 달랐다.

# Lambda@edge 사용시 유의 사항과 몇가지 개발 팁



# 결과!



# Refs

[Amazon S3 트리거를 사용하여 썸네일 이미지 생성 by AWS Official](https://docs.aws.amazon.com/ko_kr/lambda/latest/dg/with-s3-tutorial.html)

[AWS Lambda@Edge에서 실시간 이미지 리사이즈 & WebP 형식으로 변환 by 당근마켓](https://medium.com/daangn/lambda-edge%EB%A1%9C-%EA%B5%AC%ED%98%84%ED%95%98%EB%8A%94-on-the-fly-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EB%A6%AC%EC%82%AC%EC%9D%B4%EC%A7%95-f4e5052d49f3)
