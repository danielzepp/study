# Programing Design Pattern, Facade
- - - -

## Why Design pattern?
인간이 코드로 프로그램을 작성할 때, 코드의 재 사용을 고려하지 않게된다면 어마어마하게 길거나 코드가 실행 되었을 때 무슨 일이 벌어질지 알아내기 위해 긴 시간을 투자해야한다.
이렇게 정돈되지 않고 중복코드가 많은 상태를 **스파게티 코드** 라고 칭하고, -속된 말로는 똥을 싸놨다고 한다.-

내가 열심히 작성한 코드가 누군가에게 기피해야할 똥덩어리가 되고 싶지 않다면 어떤 코드를 작성해야할까?
개인적으로 아래의 요건을 충족한다면 좋은 코드라고 볼 수 있다 생각된다.
1. 다양한 상황에서 기존 코드를 수정할 필요 없이 재사용 할 수 있다. 
2. 모든 코드를 읽지 않더라도 코드의 실행 결과를 알 수 있다.
3. 동료들이 내가 작성한 모듈을 import 하여 사용하고 있다. (?)

즉, **나**만이 읽기 쉬운 코드가 아니라 **우리**가 쉽게 협업할 수 있는 방식을 가져가야한다.


## Facade
![](Programing%20Design%20Pattern,%20Facade/B02E9A30-0759-4F71-B4AC-992B7AE2839B.png)

> A facade is generally one exterior side of a building, usually the front. It is a foreign loan word from the French façade, which means “frontage” or “face”. In architecture, the facade of a building is often the most important aspect from a design standpoint, as it sets the tone for the rest of the building.  [Wikipedia](https://en.wikipedia.org/wiki/Facade)   
> Facade는 프랑스어 Façade에서 차용된 단어이다.   
> 일반적으로는 건물의 출입구로 이용되는 **정면 외벽 부분**을 가르키는 말이라고 한다.  

![](Programing%20Design%20Pattern,%20Facade/E89AAAB2-E6F5-466F-B67B-87298B70BBD4.png)
> 이미지 출처: [Design Patterns in Python — Facade - Hardik Patel - Medium](https://medium.com/@hardikpatel_6314/design-patterns-in-python-facade-65b8a393ff68)  

### Facade는 구조 디자인 패턴입니다
파사드 패턴은 시스템이 가지고 있는 복잡한 코드를 숨기고, 사용자에게는 인터페이스만을 제공하는 방식입니다.
여러 서브 시스템을 합쳐 한개의 인터페이스를 만들어낼 수 있기 때문에 사용자는 이 파사드의 인터페이스만 사용하면 포함된 모든 기능들을 사용할 수 있게됩니다.
사용자는 각 서브시스템에 직접 접근할 필요 없이 파사드 인터페이스만을 가져다 편리하게 사용할 수 있습니다.
하지만 서브시스템에 존재하는 기능들을 모두 사용할 수 있게 구현해야합니다.

### 편리한 Facade의 함정에 빠지지 말자.
인터페이스를 통합하는 과정은 꽤나 짜릿하고 속 시원하다.  허나 파사드를 어플리케이션 내에 여러개 만드는 것은 추천하고싶지 않다. 결국 파사드는 서브 시스템을 엮어 만들어낸 새로운 시스템인데, 거미줄처럼 서로 엮이고 엮여 나중에는 수정하고 싶어도 손도 못대게되며 결국엔 또 새로운 파사드를 만들게 될것이다.

- - - -

## 파사드 디자인 패턴 적용 예시

### 유지보수의 측면
기존에 존재하는 코드들을 짜집기해서 새로운 인터페이스를 추가해 복잡성을 낮출 수 있다.
	* 복잡한 코드를 **당장** 숨길 수 있다.
		* 구린 코드, 기술 부채를 당장 정리하는 것이 가장 좋지만, 항상 시간은 촉박하고 잘 돌아가고 있는 코드를 수정하기는 무섭다.
	* 새로운 개발자는 인터페이스만을 보고 기존 기능을 사용할 수 있다.
		* 코드가 어떻게 작동하는지 리딩해보는것이 가장 좋지만, 사실 우린 다운받아 사용하는 라이브러리 코드조차 읽지 않는다.

#### 더러운 함수 감추기
일단 잘 작동하는데 건들기 싫은 함수들은 숨기고 인터페이스만 깔-끔하게 만들어준다.
```
def dirty_code_return_today_weather():
    # 선조의 선조의 선조가 남긴 더럽고 만지기 싫은 코드
	  # 약 200줄
    return today_weather

def dirty_code_return_today_temperature():
    # 잘 돌아가는데 손대면 바로 버그날거같은 코드
	  # 약 500줄
    return today_temperature

class Today:
    def __init__(self):
        self._get_weather = dirty_code_return_today_weather
        self._get_temperature = dirty_code_return_today_temperature

    def weather(self):
        return self._get_weather()

    def temperature(self):
        return self._get_temperature()

today = Today()
weather = today.weather()
temperature = today.temperature()
```

#### 더러운 클래스 감추기
위 예시와 비슷하나 인터페이스가 각 class들을 서브시스템으로 포함하는 구조이고, 
사용자가 서브시스템의 함수들을 호출하는데 제한을 두지 않는것이 포인트.
```
class _Leg(object):
    def walk():
    	print("Robot walking")

class _Hand(object):
    def handshake():
    	print("handshaking")

class Robot:
    def __init__(self):
        self.hand = _Hand()
        self.leg = _Leg()
    def walking_with_handshake(self):
		  self.leg.walk()
		  self.hand.handshake()

robot = Robot()
robot.hand.handshake()
robot.leg.walk()
robot.walking_with_handshake()
```


- - - -

### 외부 라이브러리 사용시 모듈 확장
* 라이브러리에서 지원해주지 않는 기능을 추가해 사용할 수 있다.
* 연계되어 실행되는 함수들을 묶는 함수를 만들기 쉽다.

#### datetime 모듈을 사용할 때, 현 시각을 String으로 바로 받아보고싶다.
자주 사용하는 date format인 `%Y-%m-%d %H:%M:%S`  으로 변환된 현 시각을 문자열로 반환하는 함수 확장.
유용하게 사용될만한 예시는 아니지만, 이런식으로 라이브러리를 확장해서 사용할 수 있다!
```
from datetime import datetime

class Datetime:
    def __init__(self):
        self._datetime = datetime

    def now_string(self):
        return self._datetime.now().strftime("%Y-%m-%d %H:%M:%S")

    def __getattr__(self, method):
        return getattr(self._datetime, method)

date = Datetime()
date.now_string()  # <class 'str'> 2020-02-06 03:29:01 
date.now()  # <class 'datetime.datetime'> 2020-02-06 03:29:01.374202
```


### Ref
* [Design Patterns in Python — Facade - Hardik Patel - Medium](https://medium.com/@hardikpatel_6314/design-patterns-in-python-facade-65b8a393ff68)
* [Façade pattern in Python | Giacomo Debidda](https://www.giacomodebidda.com/facade-pattern-in-python/)