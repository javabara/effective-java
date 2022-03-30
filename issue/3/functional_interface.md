# 함수형 인터페이스 (functional interface)

함수형 인터페이스는 **정확히** 하나의 추상 메서드를 지정하는 인터페이스이다.

## 함수 디스크립터 (function descriptor)
람다 표현식의 시그니처를 서술하는 메서드를 함수 디스크립터라고 한다.

## @FunctionalInterface
`@FunctionalInterface`는 함수형 인터페이스임을 가리키는 어노테이션이다. `@FunctionalInterface`로 인터페이스를 선언했지만 실제로 함수형 인터페이스가 아니면 컴파일러가 에러를 호출한다.

## java.util.function의 다양한 함수형 인터페이스

### Predicate (T)-> boolean
`java.util.function.Predicate<T>` 인터페이스는 test라는 추상 메서드를 정의하며 test는 제네릭 형식 T의 객체를 인수로 받아 불리언을 반환한다.

``` java
@FunctionalInterface
public interface Predicate<T> {
    boolean test(T t);
}
public <T> List<T> filter(List<T> list, Predicate<T> p) {
    List<T> results = new ArrayList<>();
    for(T t: list) {
        if(p.test(t)) {
            results.add(t);
        }
    }
    return results;
}
Predicate<String> nonEmptyStringPredicate = (String s) -> !s.is.Empty(); // Predicate의 test 메서드
List<String> nonEmpty = filter(listOfStrings, nonEmptyStringPredicate);
```

`Predicate` 인터페이스의 자바독 명세를 보면 `and`나 `or`같은 메서드도 있다.

### Consumer (T) -> void
`java.util.function.Consumer<T>` 인터페이스는 제네릭 형식 T 객체를 받아서 void를 반환하는 `accept`라는 추상 메서드를 정의한다. T 형식의 객체를 인수로 받아서 어떤 동작을 수행하고 싶을 때 사용한다.

``` java
@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);
}
public <T> void forEach(List<T> list, Consumer<T> c) {
    for(T t: list) {
        c.accept(t);
    }
}
forEach(
    Arrays.asList(1,2,3,4,5),
    (Integer i) -> System.out.println(i) // Consumer의 accept 메서드
);
```

### Function (T) -> R
`java.util.function.Function<T>` 인터페이스는 제네릭 형식 T를 인수로 받아서 제네릭 형식 R 객체를 반환하는 추상 메서드 `apply`를 정의한다. 입력을 출력으로 매핑하는 람다를 정의할 때 `Function` 인터페이스를 활용할 수 있다.

``` java
@FunctionalInterface
public interface Function<T, R> {
    R apply(T t);
}
public <T, R> List<R> forEach(List<T> list, Function<T, R> f) {
    List<R> result = new ArrayList<>();
    for(T t: list) {
        result.add(f.apply(t));
    }
    return result;
}
// [7, 2, 6]
List<Integer> l = map(
    Arrays.asList("lambdas", "in", "action"),
    (String s) -> s.length() // Function의 apply 메서드
)
```

## 기본형 특화
자바의 모든 형식은 참조형 또는 기본형에 해당한다. 하지만 제네릭 파라미터는 참조형만 사용 가능하다. (제네릭 내부 구현 때문에 어쩔 수 없다.) 자바에서는 기본형을 참조형으로 변환하는 기능을 제공한다. 이 기능을 **박싱**이라고 한다. (그 반대는 **언박싱**, 그리고 자동으로 박싱과 언박싱이 이루어지는 기능을 **오토박싱**이라고 한다.)

하지만 이러한 변환 과정은 비용이 소모된다. 따라서 자바 8에서는 오토박싱 동작을 피할 수 있도록 특별한 버전의 함수형 인터페이스를 제공한다. 

``` java
public interface IntPredicate {
    boolean test(int t);
}

IntPredicate evenNumbers = (int i) -> i % 2 == 0;
eventNumbers.test(1000); // 참 | 박싱 없음

Predicate<Integer> oddNumbers = (Integer i) -> i % 2 != 0;
oddNumbers.test(1000); // 거짓 | 박싱 사용
```

일반적으로 특정 형식을 입력으로 받는 함수형 인터페이스의 이름 앞에는 `DoublPredicate`, `IntConsumer` 등 처럼 형식명이 앞에 붇는다. 


### 함수형 인터페이스
|함수형 인터페이스|함수 디스크립터|기본형 특화|
|----|---|---|
|`Predicate<T>`|`T -> boolean`|`IntPredicate`, `LongPredicate`, `DoublePredicate`|
|`Consumer<T>`|`T -> void`|`IntConsumer`, `LongConsumer`, `DoubleConsumer`|
|`Function<T, R>`|`T -> R`|`IntFunction<R>`, `IntToDoubleFunction`, `IntToLongFunction`, `LongFunction<R>`, `LongToDoubleFunction`, `LongToIntFunction`, `DoubleFunction<R>`, `DoubleToIntFunction`, `DoubleToLongFunction`, `ToIntFunction<T>`, `ToDoubleFunction<T>`, `ToLongFunction<T>`|
|`Supplier<T>`|`() -> T`|`BooleanSupplier`, `IntSupplier`, `LongSupplier`, `DoubleSupplier`|
|`UnaryOperator<T>`|`T -> T`|`IntUnaryOperator`, `LongUnaryOperator`, `DoubleUnaryOperator`|
|`BinaryOperator<T>`|`(T, T) -> T`|`IntBinaryOperator`, `LongBinaryOperator`, `DoubleBinaryOperator`|
|`BiPredicate<T, U>`|`(T, U) -> boolean`||
|`BiConsumer<T, U>`|`(T, U) -> void`|`ObjIntConsumer<T>`, `ObjLongConsumer<T>`, `ObjDoubleConsumer<T>`|
|`BiFunction<T, U, R>`|`(T, U) -> R`|`ToIntBiFunction<T, U>`, `ToLongBiFunction<T, U>`, `ToDoubleBiFunction<T, U>`|

## 람다와 함수형 인터페이스 예제
|사용 사례|람다 예제|대응하는 함수형 인터페이스|
|------|---|---|
|불리언 표현|`(List<String> list) -> list.isEmpty()`|`Predicate<List<String>>`|
|객체 생성|`() -> new Apple(10)`|`Supplier<Apple>`|
|객체에서 소비|`(Apple a) -> System.out.println(a.getWeight())`|`Consumer<Apple>`|
|객체에서 선택/추출|`(String s) -> s.length()`|`Function<String, Integer>` 또는 `ToIntFunction<String>`|
|두 값 조합|`(int a, int b) -> a * b`|`IntBinaryOperator`|
|두 객체 비교|`(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight())`|`Comparator<Apple>` 또는 `Bifunction<Apple, Apple, Integer>` 또는 `ToIntBiFunction<Apple, Apple>`|