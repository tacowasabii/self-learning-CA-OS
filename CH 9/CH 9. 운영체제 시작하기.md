# CH 9. 운영체제 시작하기

## 9-1 운영체제를 알아야 하는 이유

### 운영체제란

프로그램 실행에 마땅히 필요한 요소들을 가리켜 **시스템 자원**, 혹은 줄여서 **자원**이라고 한다. CPU, 메모리, 보조기억장치 등과 같은 컴퓨터 부품들은 모두 자원이라고 볼 수 있다. 모든 프로그램은 실행되기 위해 반드시 자원이 필요하다. 여기서 프로그램에 필요한 자원을 할당하고, 프로그램이 올바르게 실행되도록 돕는 특별한 프로그램이 바로 **운영체제**이다.

운영체제 또한 프로그램이므로 메모리에 적재되어야 한다. 다만 운영체제는 매우 특별한 프로그램이기 때문에 항상 컴퓨터가 부팅될 때 메모리 내 **커널 영역**이라는 공간에 따로 적재되어 실행된다. 커널 영역을 제외한 나머지 영역, 사용자가 이용하는 응용 프로그램이 적재되는 영역을 **사용자 영역**이라고 한다.

운영체제는 실행할 프로그램을 메모리에 적재하고, 더 이상 실행되지 않는 프로그램을 메모리에서 삭제하며 지속적으로 메모리 자원을 관리한다. 또한 어느 한 프로그램이 CPU를 독점하지 않도록 최대한 공정하게 여러 프로그램에 CPU 자원을 할당한다.

이처럼 운영체제는 응용 프로그램과 하드웨어 사이에서 응용 프로그램에 필요한 자원을 할당하고, 응용 프로그램이 올바르게 실행되도록 관리하는 역할을 한다.

### 운영체제를 알아야 하는 이유

운영체제는 현재 하드웨어 상태는 어떠한지, 코드가 어떻게 실행되었는지, 하드웨어 상에 어떤 문제가 있었는지 등을 여러분에게 상세히 알려줄 수 있고, 이를 통해 **문제 해결**의 실마리를 찾을 수 있다.

## 9-2 운영체제의 큰 그림

### 운영체제의 심장, 커널

운영체제가 응용 프로그램에 제공하는 서비스 종류는 다양하지만, 그중에서도 가장 핵심적인 서비스가 있다. 자원에 접근하고 조작하는 기능, 프로그램이 올바르고 안전하게 실행되게 하는 기능이 운영체제의 핵심 서비스에 속한다. 이러한 운영체제의 핵심 서비스를 담당하는 **커널**이라고 한다.

운영체제가 설치된 모든 기기에는 커널이 있다. 어떤 커널을 사용하는지에 따라 프로그램이 하드웨어를 이용하는 양상이 달라지고, 결과적으로 컴퓨터 전체의 성능도 달라질 수 있다.

운영체제가 제공하는 서비스 중 커널에 포함되지 않는 서비스도 있는데, 대표적으로 사용자 인터페이스가 있다. **사용자 인터페이스**는 윈도우 바탕화면과 같이 사용자가 컴퓨터와 상호작용할 수 있는 통로이다.

운영체제가 제공하는 사용자 인터페이스의 종류에는 **그래픽 유저 인터페이스**와 **커맨드 라인 인터페이스**가 있다.

### 이중 모드와 시스템 호출

운영체제는 응용 프로그램들이 자원에 접근하려고 할 때 오직 자신을 통해서만 접근하도록 하여 자원을 보호한다. 응용 프로그램이 자원에 접근하기 위해서는 운영체제에 도움을 요청해야 한다. 이때 '운영체제에 도움을 요청한다'는 말은 '운영체제 코드를 실행하려고 한다'는 말과 같다. 응용 프로그램의 요청을 받은 운영체제는 은용 프로그램 대신 자원에 접근하여 요청한 작업을 수행한다.

이러한 운영체제의 문지기 역할은 이중 모드로써 구현된다. **이중 모드**란 CPU가 명령어를 실행하는 모드를 크게 사용자 모드와 커널 모드로 구분하는 방식이다. CPU는 명령어를 사용자 모드로써 실행할 수도 있고, 커널 모드로써 실행할 수도 있다.

**사용자 모드**는 운영체제 서비스를 제공받을 수 없는 실행 모드이다. 즉, 커널 영역의 코드를 실행할 수 없는 모드이다. 일반적인 응용 프로그램은 기본적으로 사용자 모드로 실행된다. 사용자 모드로 실행 중인 CPU는 하드웨어 자원에 접근하는 명령어를 실행할 수 없다. 그래서 사용자 모드로 실행되는 일반적인 응용 프로그램은 자원에 접근할 수 없다.

반면 **커널 모드**는 운영체제 서비스를 제공받을 수 있는 실행 모드이다. 즉, 커널 영역의 코드를 실행할 수 있는 모드이다. CPU가 커널 모드로 명령어를 실행하면 자원에 접근하는 명령어를 비롯한 모든 명령어를 실행할 수 있다. 운영체제는 커널 모드로 실행되기 때문에 자원에 접근할 수 있다.

사용자 모드로 실행되는 프로그램이 자원에 접근하는 운영체제 서비스를 제공받으려면 운영체제에 요청을 보내 커널 모드로 전환되어야 한다. 이때 운영체제 서비스를 제공받기 위한 요청을 **시스템 호출**이라고 한다. 사용자 모드로 실행되는 프로그램은 시스템 호출을 통해 커널 모드로 전환하여 운영체제 서비스를 제공받을 수 있다.

시스템 호출은 일종의 인터럽트이다. 정확히는 소프트웨어적인 인터럽트이다. 인터럽트는 입출력장치에 의해 발생하기도 하지만 인터럽트를 발생시키는 특정 명령어에 의해 발생하기도 하는데, 이를 **소프트웨어 인터럽트**라고 한다.

시스템 호출을 발생시키는 명령어가 실행되면 CPU는 지금까지의 작업을 백업하고, 커널 영역 내에 시스템 호출을 수행하는 코드(인터럽트 서비스 루틴)를 실행한 뒤 다시 기존에 실행하던 응용 프로그램으로 복귀하여 실행을 계속해 나간다.

일반적으로 응용 프로그램은 실행 과정에서 운영체제 서비스들을 매우 빈번하게 이용한다. 그 과정에서 빈번하게 시스템 호출을 발생시키고 사용자 모드와 커널 모드를 오가며 실행된다.

### 운영체제의 핵심 서비스

#### 프로세스 관리

실행 중인 프로그램을 **프로세스**라고 한다. 일반적으로 하나의 CPU는 한 번에 하나의 프로세스만 실행할 수 있다. CPU는 한 프로세스를 실행하다가 다른 프로세스로 실행을 전환하고, 그 프로세스를 실행하다가 또 다른 프로세스로 실행을 전환하는 것을 반복한다.

이때 각 프로세스는 상태도, 사용하고자 하는 자원도 다양하다. 그래서 운영체제는 다양한 프로세스를 일목요연하게 관리하고 실행할 수 있어야 한다.

#### 자원 접근 및 할당

모든 프로세스는 실행을 위해 자원을 필요로 한다. 그리고 운영체제는 프로세스들이 사용할 자원에 접근하고 조작함으로써 프로세스에 필요한 자원을 할당해 준다.

#### CPU

일반적으로 CPU는 한 번에 하나의 프로세스만 실행할 수 있으므로 하나의 프로세스가 CPU를 이용하고 있다면 다른 프로세스는 기다려야 한다. 이에 운영체제는 프로세스들이 공정하게 CPU를 할당하기 위해 어떤 프로세스부터 CPU를 이용하게 할 것인지, 얼마나 오래 CPU를 이용하게 할지를 결정할 수 있어야 한다. 이를 **CPU 스케줄링**이라고 한다.

#### 메모리

메모리에 적재된 프로세스들은 크기도, 적재되는 주소도 가지각색이다. 같은 프로세스라 할지라도 실행할 때마다 적재되는 주소가 달라질 수 있다. 그래서 운영체제는 새로운 프로세스가 적재될 때마다 어느 주소에 적재해야 할지를 결정해야 한다.

때로는 메모리가 이미 꽉 차 있어 꼭 실행해야 할 프로세스를 적재할 공간이 없는 경우도 있고, 메모리에 공간이 남았는 데도 불구하고 프로세스를 적재하지 못하는 상황도 발생한다.

#### 입출력장치

인터럽트 서비스 루틴은 운영체제가 제공하는 기능으로 커널 영역에 있다. 운영체는 인터럽트를 처리하는 프로그램, 즉 인터럽트 서비스 루틴을 제공함으로써 입출력 작업을 수행한다.

### 파일 시스템 관리

파일 시스템도 운영체제가 제공하는 핵심 서비스이다.
