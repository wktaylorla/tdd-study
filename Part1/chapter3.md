# 3장

```java
//java
class Dollar {
    int amount;
    Dollar(int amount) {
        this.amount= amount;
    }
    void times(int multiplier){
        return new Dollar(amount * multiplier);
    }

}
```

```javascript
//javascript
class Dollar {
  constructor(amount) {
    this.amount = amount;
  }
  times(multiplier) {
    return new Dollar(this.amount * multiplier);
  }
}
```

> 지금의 Dollar 객체와 같이 객체를 값처럼 쓸 수 있는데 이것을 값 객체 패턴(value object pattern)이라고 한다.
>
> 값 객체 패턴에 대한 제약사항 중 하나는 객체의 인스턴스 변수가 생성자를 통해서 일단 설정된 후에는 결코 변하지 않는다는 것이다.

- 값 객체는 객체지향 프로그래밍에서 데이터를 담는 용도로 사용되는 객체입니다.
- 이 객체는 한 번 생성되면 그 상태가 변하지 않는 **불변성**을 가지며, 주로 간단한 데이터 구조(예: 돈의 금액, 좌표, 날짜 등)를 표현하는 데 사용됩니다.
- 값 객체 패턴에서는 객체의 상태(즉, 인스턴스 변수)가 생성자를 통해 한 번 설정된 이후에는 절대 변경되지 않는다는 제약사항이 있습니다. 이는 객체가 불변성을 유지하게 하여 코드의 안정성과 신뢰성을 높여줍니다.

위의 Dollar 코드와 같이 Dollar 객체의 amount는 생성자를 통해 설정된 후 절대 변하지 않는다.

이렇게하면 프로그램의 다른 부분에서 객체가 예기치 않게 변경되지 않을 것이라고 신뢰할 수 있다.

> 값 객체가 암시하는 것 중 하나는 2장에서와 같이 모든 연산은 새 객체를 반환해야한다는 것이다.

- 값 객체의 중요한 특징 중 하나는, 모든 연산이 새로운 객체를 반환해야 한다는 것입니다. 즉, 값 객체에 어떤 연산(예: 덧셈, 곱셈)을 수행할 때, 그 연산의 결과는 **기존 객체를 변경하는 대신** **새로운 객체를 생성하여 반환**해야 합니다.

위의 Dollar코드의 times 메서드와 같이 새로운 Dollar 객체를 반환한다. 이렇게 Dollar 객체는 불변 상태를 유지하며, 안전하게 연산을 수행한다.

> 또 다른 암시는 값 객체는 equals()를 구현해야한다는 것인데 왜냐하면 $5라는 것은 항상 다른 $5만큼이나 똑같이 좋은 것이기 때문이다.

- 값 객체는 동일한 값을 가지면 동일한 것으로 간주되어야 합니다. 즉, 두 Dollar 객체가 같은 금액을 가지고 있다면, 이 두 객체는 동일한 것으로 여겨져야 합니다.
- 이를 위해 equals() 메서드를 구현하여, 두 값 객체의 상태를 비교하고, 상태가 같으면 동일한 객체로 간주합니다.

**요구사항**

> $5 + 10CHF = $10(환율이 2:1일 경우)
>
> ~~$5 X 2 = $10~~
>
> amount를 private로 만들기
>
> ~~Dollar 부작용?~~
>
> Money 반올림?
>
> **equals()**
>
> hashCode()

Dollar를 해시 테이블의 키로 쓸 생각이면 equals()를 구현할 때에 hashCode()를 같이 구현해야한다. 후에 이에 대해 다루자.

```java
//java
public void testEquality() {
    assertTrue(new Dollar(5).equals(new Dollar(5)));
}
```

실패한다. 가짜로 구현하는 방법은 단순히 true를 반환하는 것이다.

```java
//java
public void testEquality() {
    return true;
}
```

만약 라디오 신호를 두 수신국이 감지하고 있을 때, 수신국 사이의 거리가 알려져 있고 각 수신국이 신호의 방향을 알고 있다면, 이 정보들만으로 충분히 신호의 거리와 방위를 알 수 있다. 이 계산법을 **삼각측량**이라고 한다.

삼각측량을 이용하려면 예제가 두 개이상 있어야만 코드를 일반화할 수 있다.

\*삼각 측량은 하나의 테스트만으로는 충분히 검증되지 않은 동작을 추가적인 테스트를 통해 확인하는 방법이다. 즉, 다른 입력값을 사용하여 코드의 동작이 올바른지 더 확실하게 검증하는 것이다.

```java
//java
public void testEquality() {
    assertTrue(new Dollar(5).equals(new Dollar(5)));
    // 두 객체가 같은 금액을 가지므로 true를 반환해야 함
    assertFalse(new Dollar(5).equals(new Dollar(6)));
    // 다른 금액을 가지므로 false를 반환해야 함
}
```

```javascript
//javascript
test("Dollar equality", () => {
  expect(new Dollar(5).equals(new Dollar(5))).toBe(true); // true를 반환해야 함
  expect(new Dollar(5).equals(new Dollar(6))).toBe(false); // false를 반환해야 함
});
```

이 두 테스트를 통해 코드가 다양한 상황에서 예상대로 동작하는지 확인할 수 있다.

```java
//java
public boolean equals(Object object) {
    Dollar dollar = (Dollar) object;
    return amount == dollar.amount;
}
```

```javascript
//javascript
equals(other) {
    return this.amount === other.amount;
}
```

**요구사항**

> $5 + 10CHF = $10(환율이 2:1일 경우)
>
> ~~$5 X 2 = $10~~
>
> amount를 private로 만들기
>
> ~~Dollar 부작용?~~
>
> Money 반올림?
>
> ~~**equals()**~~
>
> hashCode()
>
> Equal null
>
> Equal object

널 값이나 다른 객체들과 비교한다면? 이는 할일 목록에 적어두자.

동질성 기능을 구현했으므로 Dollar와 Dollar를 직접 비교할 수 있게 됐다.

따라서 올바른 인스턴스 변수들이 그렇듯이 amount를 private으로 만들 수 있게 됐다.

동질성 기능을 통해 두 Dollar 객체를 직접 비교할 수 있게 되었으므로, amount와 같은 중요한 인스턴스 변수를 private으로 설정하여 외부에서 접근하지 못하게 하고, 캡슐화 원칙을 적용할 수 있게 되었다는 뜻이다.
