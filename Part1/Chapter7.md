# 7장

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
> Dollar/Franc 중복
>
> ~~공용 equals~~
>
> 공용 times
>
> **Franc와 Dollar 비교하기**

Franc와 Dollar를 비교하는 테스트를 추가해보자.

```java
//java
public class MoneyTest {
    @Test
    public void testEquality() {
        assertTrue(new Dollar(5).equals(new Dollar(5)));
        assertFalse(new Dollar(5).equals(new Dollar(6)));
        assertTrue(new Franc(5).equals(new Franc(5)));
        assertFalse(new Franc(5).equals(new Franc(6)));
    }
}
```

```javascript
//javascript
describe("MoneyTest", () => {
  it("testEquality", () => {
    expect(new Dollar(5).equals(new Dollar(5))).toBeTruthy();
    expect(new Dollar(5).equals(new Dollar(6))).toBeFalsy();
    expect(new Franc(5).equals(new Franc(5))).toBeTruthy();
    expect(new Franc(5).equals(new Franc(6))).toBeFalsy();
  });
});
```

일단 테스트는 실패한다. Dollar와 Franc이 같다고 하지만, 실제로는 다르다.

오직 금액과 클래스가 같을 때만 같다고 판단하도록 수정해보자.

```java
//java
public class Money {
    protected int amount;

    public boolean equals(Object object) {
        Money money = (Money) object;
        return amount == money.amount && getClass().equals(money.getClass());
    }
}
```

```javascript
//javascript
class Money {
  constructor(amount) {
    this.amount = amount;
  }

  equals(object) {
    const money = object;
    return (
      this.amount === money.amount && this.constructor === money.constructor
    );
  }
}
```

모델 코드에서 클래스를 이런 식으로 사용하는 것은 지저분해 보인다만 현재 통화(currency)개념이 없고 도입할 충분한 이유가 없기 때문에 이렇게 구현해야 한다.

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
> Dollar/Franc 중복
>
> ~~공용 equals~~
>
> 공용 times
>
> ~~Franc와 Dollar 비교하기~~
>
> 통화?

이젠 공통 times코드를 만들어보자 따라서 혼합된 통화간의 연산에 대해 다뤄본다.
