# Observer Pattern
- - - -

## What is Observer pattern?
![](Observer%20Pattern/C02A0E9C-B404-4BFE-B80E-4F5A8A8C0F97.png)
-이거 아님-
옵저버 패턴은 어느 한 객체의 상태 변화에 맞춰 다른 객체들의 상태를 변화시키는 패턴이다.
이는 **행동 패턴** 중 한 종류이며,  이벤트를 발생시키는 객체에서 이 모든 상태 변화를 책임지지 않고, 각 객체에 이벤트를 잘 전파시키는것이 중요하다. 
한 객체에서 모든 로직을 처리하지 않게 되기 때문에 비교적 결합도가 떨어지게된다. -(떨어지게 짜야한다..)-

#### 느낌이 와닿도록 현실 세계의 예를 들어보면
1. 12월 31일 11시 59분에서 1분이 더 지나면 년도가 바뀌게된다.
2. 한국형 나이를 적용한 나는 1살을 더 먹게된다.
3. 19살의 인간은 갓 20살이 되어 술집에 출입이 가능한 상태가 된다.
4. 후불제 교통카드의 누적 사용 금액이 0원으로 초기화된다.
위 두개의 이벤트들은 모두 독립적인것 처럼 보이지만 모두 **시간** 이라는 상태 변화에 맞추어 자신의 상태를 변경하게된다.
그럴 일은 없겠지만, 시간이라는 객체가 정상 작동하지 않아 시간이 흐르지 않게된다면?
나는 나이를 먹지 않게된다. -(야호!)-

#### 스타크래프트의 옵저버
> 처음에 옵저버 스크린샷을 사용했는데, 사실 저 옵저버도 옵저버 패턴에 해당한다.  
> 정찰이 되지 않은 구역에 옵저버를 보내게 되면 옵저버의 시야가 닿는 구역을 실시간으로 감시할 수 있게 되는것이다.  
> 저 옵저버가 제대로 상태를 전파하지 않았다면 제대로된 맵리딩이 불가능할 것이다.  

### Observer의 구성
![](Observer%20Pattern/74648353-2327-43BB-9A97-505FCB2CBE7F.png)
옵저버 패턴에서 객체는 크게 두 가지로 구분된다.
* Subject = 상태 변화 이벤트를 발생시킨다, Observer에 변화를 전파한다. (Fire Event)
* Observer = Subject에서 발생한 이벤트를 수신해 작업을 수행한다.
여기에서 Subject가 Observer로 상태를 전파할 때, 직접 전달하는 역할까지 수행하기 때문에 대부분의 Observer pattern은 동기로 이루어진다.

### ConcreteSubject, ConcreteObserver
![](Observer%20Pattern/E0032464-5EDA-4915-AB5C-ACB5F61B6DDE.png)
여기에 ConcreteSubject, ConcreteObserver 라는 개념이 추가되는데
기존의 Subject와 Observer는 상태 변경에 따른 로직 수행에만 전념시키고, 
이벤트의 흐름을 관리하고 Observer 패턴의 확장성을 증가시킨다.
* ConcreteSubject
	* Subject의 데이터 변경될 때 ConcreteObserver 객체에 통보한다.
	* 변경을 통보해야할 Observer들의 리스트를 가지고 있다. (데이터 보관)
	* 이벤트를 전파하고 싶은 Observer를 늘리고 싶다면 이 인터페이스를 확장한다.
* ConcreteObserver
	* ConcreteSubject에서 데이터를 받아 Observer에 전파한다.
	* 한 옵저버에 여러개의 Subject의 이벤트를 받아 처리하고 싶다면 이 인터페이스를 확장한다.

### Pub/Sub이랑 비슷한거같은데?!
![](Observer%20Pattern/351E8CB9-E959-4217-B63A-2431CC00FB0C.png)
옵저버 패턴을 살펴보며, 우리 회사에서 많이 사용하는 Pub/Sub 패턴과 비슷한 점이 많다 생각되었는데, 여기에서 크게 다른점은 Event Channel, 즉 Message broker나 Event Bus의 존재 유무이다.

Observer pattern은 Subject와 Observer가 서로 인지하고, 이벤트를 주고 받게되지만
Pub/Sub pattern은 서로 존재 유무를 알 수 없으며 중간의 Event channel과의 통신으로 이벤트를 각각 처리하게됩니다. (메세지 스킴을 잘 구성해야하며, 만약 결합도가 생긴다면 잘못 구성한것..!)
-나는 Pub/Sub이 더 조아..-

## 자 실제 코드에 적용해볼까나!!!!
자료조사를 하며 수 많은 Java의 built-in Observable을 만났지만 정작 Python에서는 기대하기 힘들었다.
그리고 누군가 Python Observable을 만들어놨더라더라리..
![](Observer%20Pattern/4A28D511-77B4-417F-AC36-65BACE713F17.png)
[GitHub - timofurrer/observable: minimalist event system for Python](https://github.com/timofurrer/observable)
인터페이스는 깔끔했다. Observable 객체를 만들고 annotation만 달아주면 이벤트를 전파하게된다.
자바 Observable의 ConcreteSubject, ConcreteObserver처럼 확장성이 뛰어나지는 않지만, 작은 규모의 옵저버 패턴에서 필요한 기능은 잘 갖추고 있었다. 코드 자체가 간결해 옵저버 패턴을 파이썬으로 익혀보고 싶다면 한번 쯤 봐둬도 좋을듯?

이 라이브러리는 크게 3가지 메소드만을 지원한다. 
* on        :  Observable._event에 핸들러를 추가한다
* off        :  Observable._event 등록한 핸들러를 삭제한다.
* trigger : Observable._event에 등록된 핸들러를 모두 실행한다.

![](Observer%20Pattern/6CE41722-F3FB-41C6-B4FD-66C74467D676.png)
Subject class를 작성할 때 프로퍼티에 옵저버를 달아놓고, on methed를 이용해 dynamic하게 추가된 핸들러를 실행하는 모습. -Pythonic하냐?-
on method는 아래와 같은 문자열을 가진 이벤트가 등록되면 해당 이벤트명이 뜻하는 시점에 트리거된다고한다. 제법 변태같다.
* “before_get_<name>”() and “after_get_<name>”(value)
* “before_set_<name>”(value) and “after_set_<name>”(value)
* “before_del_<name>”() and “after_del_<name>”


## Ref
* [Design Pattern 옵저버 패턴이란](https://gmlwjd9405.github.io/2018/07/08/observer-pattern.html)
