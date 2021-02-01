---
layout: post
title: Big O Notation
tags: [ComputerScience]
excerpt_separator: <!--more-->
---

# Which code is Best?

*****

프로그램의 코드를 평가하는데는 3가지의 관점에서 평가할 수 있다.

1.Readable
2.Space Complexity
3.Time Complexity

<!--more-->

## Readable

즉 가독성이다. 읽기 쉽고 직관적인 코드는 유지보수에 큰 도움이 되고 프로그램의 수명도 길어진다.

## Space Complexity = Memory

메모리 공간을 얼마나 사용하는 지에 대한 관점이다.

메모리 공간은 한정되있고 그것을 최대한 효율적으로 사용하는 코드일 수록 효율적인 코드이다.

ps. Memory의 종류 - heap : 변수와 여러 값들을 직접 저장하는 곳, stack :  where we keep track of our function calls

## Time Complexity = Speed

처리 속도에 대한 관점이다.

처리 속도가 빠를 수록 효율적인 코드라 할 수 있다.

위 3가지를 모두 항상 만족할 수 없겠지만 그 타협점에서 가장 효율적인 코드를 완성하는 것을 목표로 해야한다!

  
  
   

# Big O Notation

*****

어떤 처리 과정이 얼마나 효율적인가를 표현하는 코드의 처리 효율성 Index같은 것이다.

## 	Definition

여러가지 복잡도 표기법중에서는 Big O Notation을 가장 많이 사용한다.

Big O Notation은 밑에 표처럼 여러가지로 구분 될 수 있다.

![Big O Complexity Chart]({{ "/assets/img/IT_Pic/BigOComplexityChart.jpeg" | relative_url }})

## 	Rules

Big O Notation으로 복잡도를 판단하는 4가지의 룰이 있다.

## 1. Worst Case

```java
String [] nemo = {"asd", "sdf", "qwe", "nemo", "zxc"};

findNemo(nemo);

public void findNemo(String []array) {
		Date date = new Date();
		long startD = date.getTime();
		for (int i = 0; i < array.length; i++) {
			if (array[i] == "nemo") {
				System.out.println("Found Nemo!!");
				break;
			}
		}
		long endD = date.getTime();
		System.out.println("Call to find Nemo = " + (endD - startD));
	}
```

nemo 배열안에 nemo는 4번째에 있어서 for문을 모두 돌지 않고 4번째에서 멈추기 때문에 복잡도는 4라고 할 수 있지만

Notation은 항상 최대로 for문이 실행되었을 경우를 가정해야 한다. 따라서 *O(n)*이 된다.

## 2. Remove Constants

```java
public void findNemo(String []array) {
		
		for (int i = 0; i < array.length; i++) {
			if (array[i] == "nemo") {
				System.out.println("Found Nemo!!");
				break;
			}
		}
		
		for (int i = 0; i < array.length; i++) {
			if (array[i] == "nemo") {
				System.out.println("Found Nemo!!");
				break;
			}
		}
	}
```

for문을 2번 실행하기 때문에 O(2n)이 맞지만 constant는 지워줘야하는 룰에 의해 *O(n)*이 된다.

## 3. Different terms for inputs

```java
public void findNemo(String []array, String []array2) {
		
		for (int i = 0; i < array.length; i++) {
			if (array[i] == "nemo") {
				System.out.println("Found Nemo!!");
				break;
			}
		}
		
		for (int i = 0; i < array2.length; i++) {
			if (array2[i] == "nemo") {
				System.out.println("Found Nemo!!");
				break;
			}
		}
	}
```

넣어주는 parameter값이 여러개라면 모두 다른 이름으로 넣어주어야한다.

## 4. Drop Non Dominants

```java
public void rule4(String []array, String []array2) {
		for (int i = 0; i < array.length; i++) {
			if (array[i] == "nemo") {
				System.out.println("Found Nemo!!");
			}
		}
		
		for (int i = 0; i < array.length; i++) {
			for (int j = 0; j < array2.length; j++) {
				System.out.println(array[i]+array[j]);
			}
		}
	}
```
	
안에 2가지 for가 있다. O(n + n^2)로 표현가능하지만

rule인 drop non dominants에 의하면 *O(n^2)*가 더 중요하므로 중요한것만 남겨 써야한다.



# Space complexity

*****

공간 복잡도의 계산은 밑의 코드와 같이 계산할 수 있다.

```java
/*
	 * 밖에서 booos의 관점에서는 밖에서 받아온 변수는 메모리에 포함이 안되기 때문에
	 * booos불러 사용한다면 O(1)의 공간복잡도라고 할 수 있다.
	 */
	public void booos(int [] n) {
		for (int i = 0; i < n.length; i++) {
			System.out.println("boooo!!");
		}
	}
	
	
	/*
	 * n개의 배열을 메소드안에서 생성하기 때문에 그만큼의 메모리가 필요하고
	 * for문을 위한 i 변수의 매모리공간이 또 필요하기 때문에 O(n)의 공간 복잡도라고 할 수 있다.
	 */
	public String[] makeArrayWithNum(int n) {
		String [] array = new String[n];	//여기서 변수를 만들때 메모리를 소모
		for (int i = 0; i < array.length; i++) {	 //iteration을 위한 i 변수에 의한 메모리 소모
			array[i] = "hi";
		}
		return array;
	}
```

   

# 더 좋은 프로그램을 만들기 위한 팁

*****

## Javascript String의 length Property

자바스크립트에서 String.length는 과연 얼마의 Big O notation을 가질까 한다면

그것은 String의 글자 수에 달렸다고 말할 수 있지만

자바스크립트 언어 자체에 내장된 length라는 것은 String 함수 자체가 가진 length를 그냥 읽어온 것이다.

String함수를 작성하고 실행하여 String객체를 만들때 이미 length라는 property도 작성된다는 얘기.

즉 함수가 아닌 String 함수내의 하나의 property이기 때문의 O(1)을 가진다.(즉, 그냥 변수 값을 가져오는것으로 생각 할 수 있다.)

*즉, 각 언어가 어떻게 동작하는지 알아서 기본적으로 내장된 기능들과 여러 api들의 big O notation을 파악할 수 있으면 훨씬 더 좋은 프로그램을 만들 수 있다는 것이다.*

## Big O notation이 중요한 이유

시간과 메모리 공간은 cost이고 이것을 측정하는 개념인 Big O notation이다.

이것을 항상 염두해두며 최소한으로 줄일 줄 아는 엔지니어야 말로 회사의 황금과 같은 존재.

*따라서 쓰이는 개념은 아니지만 회사가 자주 물어보는 주제이며 engineer로서 항상 염두하고 해결하기 위해 노력해야한다.*

하지만 3가지의 좋은 프로그램의 조건을 모두 고려해야하므로 그 3가지를 잘 조절해서 가장 효율적인 코드를 짜내야한다.