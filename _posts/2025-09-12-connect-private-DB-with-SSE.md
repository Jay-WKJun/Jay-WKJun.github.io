---
layout: post
title: connect private DB with SSM
tags: [security, http, DB, SSH, Bastion host, SSM, EC2 endpoint]
excerpt_separator: <!--more-->
---

![ssm_thumbnail]({{ "../sdfassets/img/ssm_thumbnail.png" | relative_url }})

SSE를 통해 private DB를 외부에 노출하지 않고 접근할 수 있는 방법을 알아보겠습니다.

<!--more-->

# private Instance에 접근하기

중요한 데이터와 비즈니스 로직이 들어있는 Instance들의 경우에는 보안을 위해 인터넷에 노출시키지 않고 사설망에 두고 운영하는 경우가 많습니다.

사설망에 있는 Instance들을 private Instance라고 합니다.

인터넷에 노출되지 않아 보안이 강력해지지만 관리자들도 접근할 수 없게되어 private Instance에 접근하기 위한 별도의 방법이 필요합니다.

private Instance에 접근하는 방법은 대표적으로 3가지가 있습니다.

- **SSH - bastion host**
- **SSM**
- **EC2 endpoint**

## SSH - bastion host

![bastion_arch]({{ "../assets/img/aboutSSM/bastion_arch.webp" | relative_url }})

SSH - bastion host 방법은 AWS 이전에도 사용되어 온 전통적인 방법으로,

HTTP보다 보안이 강력한 SSH 프로토콜을 이용해 private Instance에 접근하는 우회 통로를 만드는 방법입니다.

bastion host는 인터넷에 노출되는 서버로, private Instance들에 접근하기 위한 유일한 통로 역할을 합니다.

이렇게 private Instance들에 접근할 수 있는 입구가 bastion host로 제한되어 통신 확인 및 제어에 유리합니다.

하지만, SSH 프로토콜 특성상 접근을 위한 .pem 등의 인증서를 관리해야 하는 번거움이 있습니다.

## SSM

![ssm_arch]({{ "../assets/img/aboutSSM/ssm_arch.webp" | relative_url }})

AWS 리소스 관리 서비스인 System Manager(SSM) 기능입니다.

Session Manager를 통해 session을 열고 SSM Agent가 설치된 Instance에 접근해 다른 private Instance에 접근하는 방법입니다.

user -> Session Manager -> SSM Agent (EC2 Instance) -> private Instance

SSH 방식과 달리 SSM agent가 설치된 EC2 Instance(bastion host)를 인터넷에 노출시키지 않아도 되어 보안이 더 강력합니다.

(하지만, 내부망에 있다면 VPC endpoint나 NAT gateway 등을 통해 Session Manager와 통신할 수 있도록 설정해야 하며, 이로인해 고정 비용이 증가한다는 단점이 있습니다.)

또한, 각자의 .pem 등의 SSH 인증서가 아닌 aws IAM으로 권한 관리가 가능하여 더 편리합니다.

[AWS Systems Manager를 사용하여 RDS Custom DB 인스턴스에 연결 - AWS docs](https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/custom-creating-sqlserver.ssm.html)

## EC2 Instance Connect Endpoint

![eice_arch]({{ "../assets/img/aboutSSM/eice_arch.webp" | relative_url }})

내부망에 있는 EC2 instance에 접근하기 위한 또다른 방법입니다.

SSM 방식과 동일하게 우회 통로를 만들어 접근하는 방법이지만, 차이점이 있으며 비용면에서 더 저렴합니다.

- SSM을 이용하지 않기 때문에 SSM Agent를 설치하지 않아도 됨.
- SSM과 연결을 위한 VPC Endpoint나 NAT gateway 등을 설정할 필요가 없음. -> 고정비 절감.

비용은 데이터 전송 비용만 발생합니다.

[프라이빗 IP 주소와 EC2 Instance Connect 엔드포인트를 사용하여 인스턴스에 연결 - AWS docs](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/connect-with-ec2-instance-connect-endpoint.html)

[EC2 Instance Connect Endpoint를 이용해 Amazon EC2및 Amazon RDS 인스턴스에 안전하게 접속하기 - AWS tech blog](https://aws.amazon.com/ko/blogs/tech/ec2-instance-connect-endpoint-bastion/)

# SSM 선정 이유

고정 비용은 EC2 Instance Connect Endpoint 더 저렴하지만, 다음과 같은 이유로 SSM을 사용하기로 결정했습니다.

- **세션 로깅/감시면에서 SSM이 기능이 더 강력하고 편리함**
- 이미 해당 VPC에서 NAT gateway를 사용중

SSM은 어떤 세션이 열렸는지 실시간으로 console에서 감시가 가능하며 세션 히스토리도 자동으로 남아 보안 관리에 용이합니다.

또한, 로깅도 편리하게 설정할 수 있습니다.

이를 eventBridge와 연동하여 별도의 자동화 기능 구축도 가능합니다.

## 실시간 세션 확인

AWS console에서 실시간으로 세션을 감시할 수 있습니다.

![session_log_demo]({{ "../assets/img/aboutSSM/session_log_demo.png" | relative_url }})

## 세션 히스토리 확인

연결된 세션의 히스토리를 확인할 수 있습니다.

![session_history_demo]({{ "../assets/img/aboutSSM/session_history_demo.png" | relative_url }})

# SSE를 통해 private DB에 접근하기

크게 다음 3단계로 진행이 됩니다.

- 세션 사용자 IAM 설정
- Bastion host 생성 및 프로비저닝
- 로컬에서 private DB 접근

## 세션 사용자 IAM 설정

SSM을 이용하려면 세션을 생성할 사용자의 IAM 권한 설정이 필요합니다.

최소 필요한 권한은 `AmazonSSMManagedInstanceCore` 권한입니다.

![session_user_iam_demo]({{ "../assets/img/aboutSSM/session_user_iam.png" | relative_url }})

## Bastion host 생성 및 프로비저닝

Bastion host로 사용할 EC2 instance를 생성합니다.

다음 3가지를 주의하여 설정해야합니다.

- IAM
- secutiry group
- SSM Agent supported OS

### IAM

user의 권한과 마찬가지로 최소 필요한 권한은 `AmazonSSMManagedInstanceCore` 권한입니다.

![session_bastion_iam]({{ "../assets/img/aboutSSM/session_bastion_iam.png" | relative_url }})

### security group

Inbound는 없어도 됩니다.

![session_bastion_sg_inbound]({{ "../assets/img/aboutSSM/session_bastion_sg_inbound.png" | relative_url }})

Outbound는 SSM과의 통신을 위해 모두 허용, 443 port를 추가합니다.

또한, DB 연결을 위한 설정도 추가합니다. 전 PostgreSQL DB이기 때문에 5432 port를 추가합니다.

![session_bastion_sg_outbound]({{ "../assets/img/aboutSSM/session_bastion_sg_outbound.png" | relative_url }})

### SSM Agent supported OS

EC2를 생성시, SSM Agent를 지원하는 OS를 선택해야합니다.

[SSM Agent 지원 OS 목록](https://docs.aws.amazon.com/systems-manager/latest/userguide/operating-systems-and-machine-types.html)

수동 설치를 원하시다면 [여기](https://docs.aws.amazon.com/systems-manager/latest/userguide/agent-install-al2.html?utm_source=chatgpt.com)를 참고하세요.

참고로, Amazon Linux의 경우 SSM Agent가 이미 설치되어 있습니다.

![ssm_agent_install_doc]({{ "../assets/img/aboutSSM/ssm_agent_install_doc.png" | relative_url }})

Amazon Linux를 선택하면 별도로 SSM Agent를 설치할 필요가 없습니다.

![session_bastion_create]({{ "../assets/img/aboutSSM/session_bastion_create.png" | relative_url }})

## 로컬에서 private DB 접근

이제 접속 준비가 완료되었습니다! 이제 로컬에서 session을 열고 private DB에 접근할 수 있습니다.

aws cli에 먼저 자신의 계정 정보를 설정해줍니다.

```bash
$ aws configure
```

이제 아래와 같이 cli를 입력하면 세션을 열고 private DB에 접근할 수 있습니다.

```bash
$ aws ssm start-session \
    --document-name AWS-StartPortForwardingSessionToRemoteHost \
    --region {접속한 region} \
    --target {Bastion host의 EC2 instance ID} \
    --parameters "host={private DB의 endpoint},portNumber={private DB의 port},localPortNumber={로컬에서 접근할 port}"
```

그럼 아래와같은 화면이 나오며 SSM에 접속되고 DB와 접근하기 위한 터널이 열립니다.

![session_start]({{ "../assets/img/aboutSSM/session_start.png" | relative_url }})

### DBeaver와 연동

열린 터널을 통해 DBeaver로 DB에 직접 접근할 수 있습니다.

**주의! 전 local port를 `15432`로 설정하여 로컬에선 `15432` port로 접근하도록 설정했습니다.**

![session_dbeaver_config]({{ "../assets/img/aboutSSM/session_dbeaver_config.png" | relative_url }})

그렇게 테스트 connect를 시도해 봅시다.

성공입니다. 👍

![session_dbeaver_test_complete]({{ "../assets/img/aboutSSM/session_dbeaver_test_complete.png" | relative_url }})

# Ref.

https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/custom-creating.ssm.html

https://docs.aws.amazon.com/ko_kr/systems-manager/latest/userguide/setup-create-vpc.html

https://aws.amazon.com/ko/blogs/tech/ec2-instance-connect-endpoint-bastion/

https://musma.github.io/2019/11/29/about-aws-ssm.html

https://medium.com/cloudvillains/bastion%EC%84%9C%EB%B2%84%EA%B0%80-%ED%95%84%EC%9A%94-%EC%97%86%EB%8B%A4-ec2-instance-connext-endpoint-%EC%B6%94%EC%B2%9C-%ED%8F%AC%EC%9D%B8%ED%8A%B8-1-910fb5ec9833

https://minjii-ya.tistory.com/66

https://hshine1226.medium.com/%EC%99%B8%EB%B6%80-%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8-%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98%EC%97%90%EC%84%9C-private-subnet%EC%97%90-%EA%B5%AC%EC%B6%95%ED%95%9C-aws-rds-db-%EC%97%90-%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%A0%91%EA%B7%BC%ED%95%98%EB%82%98%EC%9A%94-615ee2507bef

https://malangcow.medium.com/aws-ssm-%ED%8F%AC%ED%8A%B8%ED%8F%AC%EC%9B%8C%EB%94%A9%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC-private-subnet-rds-dns-%EC%A0%91%EC%86%8D%ED%95%98%EA%B8%B0-0e90351aa0a9

https://93960028.tistory.com/93

https://jaykos96.tistory.com/74

https://cloudguardians.medium.com/aws-session-manager-%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC-private-subnet-ec2-%EC%A0%91%EC%86%8D%ED%95%98%EA%B8%B0-a5717201cc57

https://worldoverthecloud.tistory.com/17
