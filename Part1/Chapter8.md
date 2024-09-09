# 8장

**요구사항**

> $5 + 10CHF = $10(환율이 2:1일 경우)
>
> ~~$5 X 2 = $10~~
>
> ~~amount를 private로 만들기~~
>
> ~~Dollar 부작용?~~
>
> Money 반올림?
>
> ~~equals()~~
>
> hashCode()
>
> Equal null
>
> Equal object
>
> ~~5CHF X 2 = 10CHF~~
>
> **Dollar/Franc 중복**
>
> ~~공용 equals~~
>
> 공용 times
>
> ~~Franc와 Dollar 비교하기~~
>
> 통화?

Dollar와 Franc의 times 구현 코드가 거의 똑같다.

```java
//java
class Franc extends Money{
  ...
  Franc times(int multiplier) {
      return new Franc(amount * multiplier);
  }
}

class Dollar extends Money{
  ...
  Dollar times(int multiplier) {
      return new Dollar(amount * multiplier);
  }
}
```

```javascript
//javascript
class Franc extends Money{
  ...
  times(multiplier) {
      return new Franc(this.amount * multiplier);
  }
}

class Dollar extends Money{
  ...
  times(multiplier) {
      return new Dollar(this.amount * multiplier);
  }
}
```

양쪽 모두 Money를 반환하게하면 더 비슷하게 만들 수 있다.

```java
//java
class Franc extends Money{
  ...
  Money times(int multiplier) {
      return new Franc(amount * multiplier);
  }
}

class Dollar extends Money{
  ...
  Money times(int multiplier) {
      return new Dollar(amount * multiplier);
  }
}
```

**위 코드에 대한 설명을 추가한다. "Money times"에서 "Money"는 메서드의 반환 타입을 나타낸다. 즉 times 메서드가 호출되면 Money 타입의 객체를 반환한다는 뜻이다. times는 메서드의 이름이다.**

```javascript
//javascript
//여전히 Franc나 Dollar객체를 반환하지만 이를 상위 클래스인 Money 타입으로 처리한다고 가정
//javascript는 명시적인 타입 시스템이 없어서 이렇게 처리할 수 없다.
class Franc{
  ...
  times(multiplier) {
      return new Franc(this.amount * multiplier);
  }
}

class Dollar{
  ...
  times(multiplier) {
      return new Dollar(this.amount * multiplier);
  }
}
```

Money의 두 하위 클래스는 그다지 많은 일을 하는 것 같지 않으므로 제거하고자 한다.

일단 하위 클래스에 대한 직접적인 참조를 줄여나가 보겠다.

Money에 Dollar를 반환하는 팩토리 메서드를 도입한다.

먼저, 테스트를 작성한다.

```java
//java
public void testMultiplication() {
  Dollar five = Money.dollar(5);
  assertEquals(new Dollar(10), five.times(2));
  assertEquals(new Dollar(15), five.times(3));
}
```

```javascript
//javascript
test("testMultiplication", () => {
  const five = Money.dollar(5);
  expect(new Dollar(10)).toEqual(five.times(2));
  expect(new Dollar(15)).toEqual(five.times(3));
});
```

이제 테스트를 통과하는 코드를 작성한다.

```java
//java
class Money {
  ...
  static Dollar dollar(int amount) {
    return new Dollar(amount);
  }
}
```

```javascript
//javascript
class Money {
  ...
  static dollar(amount) {
    return new Dollar(amount);
  }
}
```

Dollar에 대한 참조를 줄이기 위해 테스트 수정

```java
//java
public void testMultiplication() {
  Money five = Money.dollar(5);
  assertEquals(new Dollar(10), five.times(2));
  assertEquals(new Dollar(15), five.times(3));
}
```

앞서 말했듯이 js는 명시적인 타입 시스템이 없어서 수정이 없다.

Money.times()를 추상 메서드로 만들어 하위 클래스에서 구현하도록 한다.

```java
//java
abstract class Money {
  ...
  abstract Money times(int multiplier);
}
```

```javascript
//javascript
class Money {
  ...
  times(multiplier) {
    throw new Error("This method must be overridden");
  }
}
```

이제 팩토리 메서드의 선언을 바꾼다.

```java
//java
class Money {
  ...
  static Money dollar(int amount) {
    return new Dollar(amount);
  }
}
```

```javascript
//javascript
class Money {
  ...
  static dollar(amount) {
    return new Dollar(amount);
  }
}
```

이제 팩토리 메서드를 테스트 코드의 나머지 모든 곳에서 사용할 수 있다.

```java
//java
public void testMultiplication() {
  Money five = Money.dollar(5);
  assertEquals(Money.dollar(10), five.times(2));
  assertEquals(Money.dollar(15), five.times(3));
}

public void testEquality() {
  assertTrue(Money.dollar(5).equals(Money.dollar(5)));
  assertFalse(Money.dollar(5).equals(Money.dollar(6)));
  assertTrue(Money.franc(5).equals(Money.franc(5)));
  assertFalse(Money.franc(5).equals(Money.franc(6)));
}
```

```javascript
//javascript
test("testMultiplication", () => {
  const five = Money.dollar(5);
  expect(Money.dollar(10)).toEqual(five.times(2));
  expect(Money.dollar(15)).toEqual(five.times(3));
});

test("testEquality", () => {
  expect(Money.dollar(5).equals(Money.dollar(5))).toBeTruthy();
  expect(Money.dollar(5).equals(Money.dollar(6))).toBeFalsy();
  expect(Money.franc(5).equals(Money.franc(5))).toBeTruthy();
  expect(Money.franc(5).equals(Money.franc(6))).toBeFalsy();
});
```
