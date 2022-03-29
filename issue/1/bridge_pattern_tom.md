# 브릿지 패턴 (Bridge Pattern)

## 방식
구현부에서 추상층을 분리하여 각자 독립적으로 변형할 수 있게 하는 패턴. 기능과 구현을 분리한다.

## 구조
![브릿지 패턴 구조](./../../images/bridge_pattern.png)

1. Abstraction
    - 추상화된 인터페이스를 정의. Implementor 객체에 대한 참조를 유지 관리.
2. RefinedAbstraction
    - Abstraction을 확장
3. Implementor
    - 구현 클래스의 인터페이스를 정의
4. ConcreteImplementor
    - Implementor를 구현하는 구체 클래스

## 사용하는 이유
기능의 확장과 구현의 확장을 따로 계층화하기 위해 사용한다. 기능과 구현의 결합도가 낮기 때문에 기능이 구현 방식에 얽매이지 않는다. 클라이언트는 기능을 사용하기 때문에 구체적인 구현단의 코드를 숨길 수 있다.

## 예제

리그오브레전드의 각 챔피언은 Q,W,E,R 총 4가지의 스킬을 가지고 있다. 브릿지 패턴을 이용해서 애쉬의 Q,W,E,R 스킬을 구현해보자.

### Abstraction
``` java
public abstract class ChampionAbstraction {
    protected ChampionImplementor championImplementor;

    public Abstraction(ChampionImplementor championImplementor) {
        this.championImplementor = championImplementor;
    }

    abstract void useQ();

    abstract void useW();

    abstract void useE();

    abstract void useR();
}
```
### RefinedAbstraction
``` java
public class AsheAbstraction extends ChampionAbstraction {
    public AsheAbstraction(ChampionImplementor championImplementor) {
        super(championImplementor);
    }

    @Override
    void doQ() {
        championImplementor.useQ();
    }

    @Override
    void doW() {
        championImplementor.useW();
    }

    @Override
    void doE() {
        championImplementor.useE();
    }

    @Override
    void doR() {
        championImplementor.useR();
    }
}
```
### Implementor
``` java
public abstract class ChampionImplementor {
    abstract void useQ();
    abstract void useW();
    abstract void useE();
    abstract void useR();
}
```
### ConcreteImplementor
``` java
public class AsheImplementor extends ChampionImplementor {
    @Override
    void useQ() {
        System.out.println("궁사의 집중");
    }

    @Override
    void useW() {
        System.out.println("일제사격");
    }

    @Override
    void useE() {
        System.out.println("매 날리기");
    }

    @Override
    void useR() {
        System.out.println("마법의 수정화살");
    }
}
```

### 실제 호출
``` java
public static void main(String[] args) {
    ChampionAbstraction championAbstraction = new AsheAbstraction(new AsheImplementor()); // Abstraction과 Implementor 조합 가능.
    championAbstraction.useQ();
    championAbstraction.useW();
    championAbstraction.useE();
    championAbstraction.useR();
}
```
