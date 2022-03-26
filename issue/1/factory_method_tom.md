# 팩터리 메서드 패턴(Factory Method Pattern)

### 의도

객체를 생성하기 위해 인터페이스를 정의하지만, **어떤 클래스의 인스턴스를 생성할지에 대한 결정은 서브클래스** 가 내리도록 한다.

### 구조
![팩터리 메서드 구조](./../../images/factory_method_tom.png)

1. Product
    - 팩터리 메서드로 생성될 객체의 공통 인터페이스
2. ConcreteProduct
    - 구체적으로 객체가 생성되는 클래스
3. Creator
    - 팩터리 메서드를 갖는 클래스
4. ConcreteCreator
    - 팩터리 메서드를 구현하는 클래스, ConcreteProduct 객체를 생성

## 사용하는 이유

클래스의 생성과 사용 처리로직을 분리하여 결합도를 낮추기 위한 것이다.

## 예제

||가렌|애쉬|
|------|---|---|
|체력 및 마력|150 / 0|100 / 50|
|공격력|30|20|
|이동속도|300|290|
|레벨업 시 증가하는 수치| 3/0/2/5 | 2/1/4/3|

리그오브레전드라는 게임에 가렌과 애쉬라는 두개의 챔피언이 있다. 이 게임이 엄청나게 인기가 많아져 챔피언 수를 늘려야할 것을 대비하여, 게임 이용자가 챔피언을 인스턴스화 할 때, 팩터리 메서드 패턴을 통해 이를 구성해보려고 한다.

### Product

``` java
public abstract class Champion {
    protected int hp;
    protected int mp;
    protected int damage;
    protected int speed;

    public abstract void levelUp();
}
```

### ConcreteProduct

``` java
public class Garen extends Champion {
    @Override
    public void levelUp() {
        this.hp += 3;
        this.damage += 2;
        this.speed += 5;
    }
}
```

``` java
public class Ashe extends Champion {
    @Override
    public void levelUp() {
        this.hp += 2;
        this.mp += 1;
        this.damage += 4;
        this.speed += 3;
    }
}
```

### Creator
``` java
public abstract class ChampionCreator {

    public Champion PickNewChampion() {
        final Champion champion = createChampion();
        return champion;
    }
    
    // Factory Method
    protected abstract Champion createChampion();
}
```

### ConcreteCreator

``` java
public class GarenCreator extends ChampionCreator {
    @Override
    protected Champion createChampion() {
        Champion garen = new Garen();
        garen.setHp(150);
        garen.setMp(0);
        garen.setDamage(30);
        garen.setSpeed(300);
        return garen;
    }
}
```

``` java
public class AsheCreator extends ChampionCreator {
    @Override
    protected Champion createChampion() {
        Champion ashe = new Ashe();
        ashe.setHp(100);
        ashe.setMp(50);
        ashe.setDamage(20);
        ashe.setSpeed(290);
        return ashe;
    }
}
```

### 실제 호출

``` java
    public static void main(String[] args) {
    final AsheCreator asheCreator = new AsheCreator();

    final Champion ashe = asheCreator.createChampion();
    ...

    ashe.levelUp();
}
```

이와 같이 챔피언 객체를 구성하는 단과 생성하는 단을 분리하여 만듬으로서 새로운 챔피언의 추가와 삭제가 쉬워진다.