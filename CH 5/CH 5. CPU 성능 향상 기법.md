# CH 5. CPU 성능 향상 기법

## 5-1 빠른 CPU를 위한 설계 기법

### 클럭

**클럭 속도**가 높은 CPU는 일반적으로 성능이 좋다. 그래서 클럭 속도는 CPU 속도 단위로 간주되기도 한다. 클럭 속도는 헤르츠(Hz) 단위로 측정한다. 이는 1초에 클럭이 몇 번 반복되는지를 나타낸다.

클럭 속도가 2.5GHz라면 1초에 클럭이 25억(2.5 \* 10^9)번 반복된다는 것을 나타낸다. 이때 CPU는 계속 일정한 클럭 속도를 유지하기보다는 고성능을 요하는 순간에는 순간적으로 클럭 속도를 높이고, 그렇지 않을 때는 유연하게 클럭 속도를 낮추기도 한다. 최대 클럭 속도를 강제로 더 끌어올릴 수도 있는데, 이런 기법을 **오버클럭킹**이라고 한다.

그렇다고 클럭 속도를 무작정 높이면 발열 문제가 생기므로 클럭 속도만으로 CPU의 성능을 올리는 것에는 한계가 있다.

### 코어와 멀티코어

CPU의 성능을 높이는 다른 방법으로는 CPU의 코어와 스레드 수를 늘리는 방법이 있다. 우리가 지금까지 CPU의 정의로 알고 있었던 '명령어를 실행하는 부품'은 오늘날 **코어**라는 용어로 사용된다. 다시 말해, 오늘날의 CPU는 단순히 '명령어를 실행하는 부품'에서 '명령어를 실행하는 부품을 여러 개 포함하는 부품'으로 명칭의 범위가 확장되었다.

코어를 여러 개 포함하고 있는 CPU를 **멀티코어 CPU** 또는 **멀티코어 프로세서**라고 부른다. 코어가 많을수록 성능이 좋아지긴 하지만 꼭 코어마다 처리할 연산이 적절히 분배되지 않는다면 코어 수에 비례하여 연산 속도가 증가하지 않는다. 또한 처리하고자 하는 작업량보다 코어 수가 지나치게 많아도 성능에는 크게 영향이 없다.

중요한 것은 코어마다 처리할 명령어들을 얼마나 적절하게 분배하느냐이고 그에 따라서 연산 속도는 크게 달라진다.

### 스레드와 멀티스레드

**스레드**의 사전적 의미는 '실행 흐름의 단위'이다. 스레드에는 CPU에서 사용되는 **하드웨어적 스레드**가 있고, 프로그램에서 사용되는 **소프트웨어적 스레드**가 있다.

#### 하드웨어적 스레드

스레드를 하드웨어적으로 정의하면 '하나의 코어가 동시에 처리하는 명령어 단위'를 의미한다. CPU에서 사용하는 스레드라는 용어는 보통 CPU 입장에서 정의된 하드웨어적 스레드를 의미한다. 여러 스레드를 지원하는 CPU는 하나의 코어로도 여러 개의 명령어를 동시에 실행할 수 있다. 예를 들어 2코어 4스레드 CPU는 명령어를 실행하는 부품을 두 개 포함하고, 한 번에 네 개의 명령어를 처리할 수 있는 CPU를 의미한다.

이처럼 하나의 코어로 여러 명령어를 동시에 처리하는 CPU를 **멀티스레드 프로세서** 또는 **멀티스레드 CPU**라고 한다.

#### 소프트웨어적 스레드

소프트웨어적으로 정의된 스레드는 '하나의 프로그램에서 독립적으로 실행되는 단위'를 의미한다. 프로그래밍 언어나 운영체제를 학습할 때 접하는 스레드는 보통 소프트웨어적 스레드를 의미한다.

하나의 프로그램은 실행되는 과정에서 한 부분만 실행될 수도 있지만, 프로그램의 여러 부분이 동시에 실행될 수도 있다. 여러 기능들을 작동시키는 코드를 각각의 스레드로 만들면 동시에 실행할 수 있다.

한번에 하나씩 명령어를 처리하는 1코어 1스레드 CPU도 소프트웨어적 스레드를 수십 개 실행할 수 있다.

#### 멀티스레드 프로세서

**멀티스레드 프로세서**는 하나의 코어로 여러 명령어를 동시에 처리하는 CPU이다. 멀티스레드 프로세서의 가장 큰 핵심은 레지스터이다. 하나의 코어로 여러 명령어를 동시에 처리하도록 만들려면 하나의 명령어를 처리하기 위해 꼭 필요한 레지스터를 여러개 가지고 있으면 된다.

하드웨어 스레드를 이용해 하나의 코어로도 여러 명령어를 동시에 처리할 수 있다고 했지만 프로그램 입장에서 하드웨어 스레드는 '한 번에 하나의 명령어를 처리하는 CPU'나 다름 없다. 2코어 4스레드 CPU는 한번에 네 개의 명령어를 처리할 수 있지만 프로그램 입장에선 한 번에 하나의 명령어를 처리하는 CPU가 네 개 있는 것처럼 보인다. 그래서 하드웨어 스레드를 **논리 프로세서**라고 부르기도 한다.

## 5-2 명령어 병렬 처리 기법

### 명령어 파이프라인

명령어 처리 과정을 클럭 단위로 나누어 보면 일반적으로 다음과 같다.

1. 명령어 인출
2. 명령어 해석
3. 명령어 실행
4. 결과 저장

여기서 중요한 점은 같은 단계가 겹치지만 않는 다면 CPU는 '각 단계를 동시에 실행할 수 있다'는 것이다. 이처럼 마치 공장 생산 라인과 같이 명령어들을 **명령어 파이프라인**에 넣고 동시에 처리하는 기법을 **명령어 파이프라이닝**이라고 한다.

파이프라이닝이 높은 성능을 가져오기는 하지만, 특정 상황에서는 성능 향상에 실패하는 경우도 있다. 이러한 상황을 **파이프라인 위험**이라고 부른다. 파이프라인 위험에는 크게 **데이터 위험, 제어 위험, 구조적 위험**이 있다.

#### 데이터 위험

**데이터 위험**은 명령어 간 '데이터 의존성'에 의해 발생한다. 어떤 명령어는 이전 명령어를 끝까지 실행해야만 비로소 실행할 수 있는 경우가 있는데 이처럼 데이터 의존적인 두 명령어를 무작정 동시에 실행하려고 하면 '데이터 위험'이 발생할 수 있다.

#### 제어 위험

**제어 위험**은 주로 분기 등으로 인한 '프로그램 카운터의 갑작스러운 변화'에 의해 발생한다. 기본적으로 프로그램 카운터는 '현재 실행 중인 명령어의 다음 주소'로 갱신된다. 하지만 프로그램 실행 흐름이 바뀌어 명령어가 실행되면서 프로그램 카운터 값에 갑작스러운 변화가 생긴다면 명령어 파이프라인에 미리 가지고 와서 처리 중이었던 명령어들은 쓸모가 없어진다. 이를 '제어 위험'이라고 한다. 이를 위해 사용하는 기술 중 하나가 **분기 예측**이다. 분기 예측은 프로그램이 어디로 분기할지 미리 예측한 후 그 주소를 인출하는 기술이다.

#### 구조적 위험

**구조적 위험**은 명령어들을 겹쳐 실행하는 과정에서 서로 다른 명령어가 동시에 ALU, 레지스터 등과 같은 CPU 부품을 사용하려고 할 때 발생한다. 구조적 위험은 **자원 위험**이라고도 부른다.

### 슈퍼스칼라

CPU 내부에 여러 개의 명령어 파이프라인을 포함한 구조를 **슈퍼스칼라**라고 한다. 슈퍼스칼라 구조로 명령어 처리가 가능한 CPU를 슈퍼스칼라 프로세서 또는 슈퍼스칼라 CPU라고 한다. 슈퍼스칼라 프로세서는 매 클럭 주기마다 동시에 여러 명령어를 인출할 수도, 실행할 수도 있어야 한다. 멀티 프로세서는 슈퍼스칼라 구조를 사용할 수 있다.

슈퍼스칼라 방식을 차용한 CPU는 파이프라인 위험을 방지하기 위해 고도로 설계되어야 한다. 여러 개의 파이프라인을 이용하면 하나의 파이프라인을 사용할 때 보다 데이터 위험, 제어 위험, 자원 위험을 피하기가 더욱 까다롭기 때문이다.

### 비순차적 명령어 처리

**비순차적 명령어 처리 기법**은 명령어들을 순차적으로 실행하지 않는 기법이다. 보통 OoOE(Out-of-order execution)로 줄여 부른다. 명령어를 순차적으로만 실행하지 않고 순서를 바꿔 실행해도 무방한 명령어를 먼저 실행하여 명령어 파이프라인이 멈추는 것을 방지한다. 이를 위해서 CPU는 명령어들이 어떤 명령어와 데이터 의존성을 가지고 있는지, 순서를 바꿔 실행할 수 있는 명령어에는 어떤 것들이 있는지를 판단할 수 있어야 한다.

## 5-3 CISC와 RISC

CPU가 파이프라이닝과 슈퍼스칼라 기법을 효과적으로 사용하려면 CPU가 인출하고 해석하고 실행하는 명령어가 파이프라이닝 하기 쉽게 생겨야 한다.

### 명령어 집합

CPU가 이해할 수 있는 명령어들의 모음을 **명령어 집합** 또는 **명령어 집합 구조**(**ISA**: Instruction Set Architecture)라고 한다. 즉, CPU마다 ISA가 다를 수 있다. ISA가 다르면 어셈블리어도 달라진다.

ISA가 다르면 그에 따라 많은 것이 달라진다. 제어장치가 명령어를 해석하는 방식, 사용되는 레지스터의 종류와 개수, 메모리 관리 방법 등이 달라진다. 이는 곧 CPU 하드웨어 설계에도 큰 영향을 미친다.

ISA는 CPU의 언어임과 동시에 CPU를 비롯한 하드웨어가 소프트웨어를 어떻게 이해할지에 대한 약속이라고도 볼 수 있다.

### CISC

**CISC**는 Complex Instruction Set Computer의 약자이다. CISC는 이름 그대로 복잡하고 다양한 명령어들을 활용하는 CPU 설계 방식이다.

CISC는 다양하고 강력한 기능의 명령어 집합을 활용하기 때문에 명령어의 형태와 크기가 다양한 **가변 길이 명령어**를 활용한다. 메모리에 접근하는 주소 지정 방식도 다양해서 아주 특별한 상황에서만 사용되는 독특한 주소 지정 방식들도 있다.

다양하고 강력한 명령어를 활용한다는 말은 상대적으로 적은 수의 명령어로도 프로그램을 실행할 수 있다는 것을 의미한다. 이는 곧 메모리 절약으로 이어진다. 하지만 활용하는 명령어가 워낙 복잡하고 다양한 기능을 제공하는 탓에 명령어의 크기와 실행되기까지의 시간이 일정하지 않다. 그리고 복잡한 명령어 때문에 명령어 하나를 실행하는 데에 여러 클럭 주기를 필요로 한다. 이는 명령어 파이프라인을 구현하는 데에 큰 걸림돌이 된다.

명령어 파이프라인이 제대로 동작하지 않는다는 것은 현대 CPU에서 아주 치명적인 약점이다. 또한, CISC의 대다수의 복잡한 명령어들은 그 사용 빈도가 낮다. 이러한 이유로 CISC 기반 CPU는 성장에 한계가 있다.

### RISC

**RISC**는 Reduced Instruction Set Computer의 약자이다. 이름처럼 RISC는 CISC에 비해 명령어 종류가 적다. 그리고 짧고 규격화된 명령어, 되도록 1클럭 내외로 실행되는 명령어를 지향한다. 즉, RISC는 **고정 길이 명령어**를 활용한다. RISC 명령어 집합은 명령어 파이프라이닝에 최적화되어 있다.

RISC는 메모리에 직접 접근하는 명령어를 load, store 두 개로 제한할 만큼 메모리 접근을 단순화하고 최소화를 추구한다. 그렇기 때문에 CISC보다 주소 지정 방식의 종류가 적은 경우가 많다.

RISC는 메모리 접근을 단순화, 최소화하는 대신 레지스터를 적극적으로 활용한다. 그렇기에 CISC보다 레지스터를 이용하는 연산이 많고, 일반적인 경우보다 범용 레지스터 개수도 많다. 다만 사용 가능한 명령어 개수가 CISC보다 적기 때문에 RISC는 CISC보다 많은 명령으로 프로그램을 작동시킨다.
