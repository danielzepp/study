# Singleton Pattern
- - - -

## What is Singleton?
![](Singleton%20Pattern/0AD2957D-EE5A-4DBC-8D14-B37FEFE80152.png)
Singleton Pattern이란, 시스템에 단 하나만의 인스턴스를 메모리에 올리고 글로벌 엑세스가 가능하게 만드는 방식이다. (반드시 one, and only one!)

### 장점
* 여러번 생성될 수 있을 인스턴스를 고정된 메모리 영역에만 할당하니 자원을 효율적으로 사용할 수 있다.
* 글로벌 인스턴스를 사용하게되므로, 인스턴스에 데이터를 저장해 공유해 사용하기 좋다. 

### 단점
* Multi thread 사용시 타이밍 이슈로 인해 Singleton 인스턴스가 생길 수 있다. Lock을 걸어 여러개 생성하지 못하도록 제어한다.
* 대부분의 싱글톤을 잘못 사용하는 경우 -> 어플리케이션에서 전역 변수를 대체하기 위해 사용되어서는 안된다. 정확한 의도와 목적을 가진 글로벌 데이터를 관리하기 위해 사용해야한다.
	* Database connection pool
	* Web server Request, Response 
	* Logger

### 구현 방식
이 싱글톤은 크게 두 가지 방식으로 캡슐화 할 수 있습니다. 싱글톤 객체를 언제 생성하느냐에 따라 구분됩니다.
* Eager initialization
* Lazy initialization
Thread-safe 하게 작업하기 위해 Lazy initialization방식이 권장되며, 이 방식에서도 더 나은 성능을 위한 해결 방안이 존재합니다. (Synchronize Thread lock, Double-check Locking…)

## Example
### Lazy initialization
Metaclass를 이용한 Lazy initialization 방식의 Singleton. (Metaclass는 Class의 template같은 느낌쓰)
m1, m2 두개의 인스턴스를 만들었음에도 메모리 주소가 완전히 동일한 것을 확인 할 수 있다.
```
class Singleton(type):
    def __init__(cls, name, bases, attrs, **kwargs):
        super().__init__(name, bases, attrs)
        cls._instance = None

    def __call__(cls, *args, **kwargs):
        if cls._instance is None:
            cls._instance = super().__call__(*args, **kwargs)
        return cls._instance

class Score(metaclass=Singleton):
    def __init__(self):
        self.point = 0

    def point_up(self):
        self.point += 1
        print(self.point)

def main():
    m1 = Score()
    m2 = Score()
    m1.point_up() # 1
    m2.point_up() # 2
    print(m1, m2) # <__main__.Score object at 0x106393eb8> <__main__.Score object at 0x106393eb8>
    assert m1 is m2 # pass

if __name__ == "__main__":
    main()

```


### Thread Safe, synchronized
위에 있는 Lazy initialization 예제의 단점은 thread safe하지 않다는 것.
thread lock을 이용해 인스턴스 생성시 메소드를 잠궈 두개의 인스턴스가 생기지 않도록한다.
무조건 synchronize하는게 아니라, lock이전에 인스턴스가 존재하는지 검사하기 때문에 완전히 비효율적인 방법은 아니다. (Double-checked locking [Double-checked locking - Wikipedia](https://en.wikipedia.org/wiki/Double-checked_locking))
[python - Why is this singleton implementation “not thread safe”? - Stack Overflow](https://stackoverflow.com/questions/50566934/why-is-this-singleton-implementation-not-thread-safe/50567397)
```
class SingletonOptmized(type):
    _instances = {}

    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._locked_call(*args, **kwargs)
        return cls._instances[cls]

    @synchronized(lock)
    def _locked_call(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super(SingletonOptmized, cls).__call__(*args, **kwargs)

class SingletonClassOptmized(metaclass=SingletonOptmized):
    pass
```

## Ref
* [python - Why is this singleton implementation “not thread safe”? - Stack Overflow](https://stackoverflow.com/questions/50566934/why-is-this-singleton-implementation-not-thread-safe/50567397)
* https://en.wikipedia.org/wiki/Double-checked_locking
* [Design_Pattern Singleton(싱글톤)의 고도화](https://limkydev.tistory.com/67)
* [Singleton Design Pattern](https://sourcemaking.com/design_patterns/singleton)
