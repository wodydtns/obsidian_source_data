- 1. Is-A 관계 (상속 관계)
	- 부모 클래스와 자식 클래스 간의 관계를 나타냅니다
	- "~는 ~이다"라는 관계가 성립됩니다
	- 예시
```Java
class Animal {
    void breathe() {
        System.out.println("숨을 쉽니다");
    }
}

class Dog extends Animal {  // Dog "is a" Animal
    void bark() {
        System.out.println("멍멍!");
    }
}
```
- 2. Has-A 관계 (포함 관계)
	- 한 클래스가 다른 클래스를 포함하는 관계입니다
	- "~는 ~를 가지고 있다"라는 관계가 성립됩니다
	- 예시
```Java
class Engine {
    void start() {
        System.out.println("엔진 시동");
    }
}

class Car {  // Car "has a" Engine
    private Engine engine = new Engine();
    
    void startCar() {
        engine.start();
        System.out.println("자동차가 출발합니다");
    }
}
```
- 주요 차이점
	- Is-A는 상속을 통해 구현되며, 코드 재사용과 다형성을 제공합니다
	- Has-A는 합성(Composition)이나 집합(Aggregation)을 통해 구현되며, 객체 간의 결합도를 낮추는데 도움이 됩니다