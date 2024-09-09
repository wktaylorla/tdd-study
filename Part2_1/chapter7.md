# 7장. 사과와 오렌지

```tsx
$5 + 10CHF = $10 (환율이 2:1일경우)
~~$5 X 2 = $10
amount를 private로 만들자~~
~~Dollar부작용?~~
Money반올림?
~~equals()~~
hashCode()
Equal null
Equal object
5CHF X 2 = 10CHF
Dollar/Franc 중복
~~공용 equals~~
공용 times
**Franc과 Dollar비교하기**
```

```tsx
// 테스트 함수
function testEquality() {
    // 동일한 금액의 Dollar 비교
    console.assert(new Dollar(5).equals(new Dollar(5)) === true, "Dollar(5) should equal Dollar(5)");
    // 다른 금액의 Dollar 비교
    console.assert(new Dollar(5).equals(new Dollar(10)) === false, "Dollar(5) should not equal Dollar(10)");
    // Dollar와 Franc 비교 (같은 금액이라도 다른 종류의 화폐이므로 false)
    console.assert(new Dollar(5).equals(new Franc(5)) === false, "Dollar(5) should not equal Franc(5)");
    // Franc와 Dollar 비교 (같은 금액이라도 다른 종류의 화폐이므로 false)
    console.assert(new Franc(5).equals(new Dollar(5)) === false, "Franc(5) should not equal Dollar(5)");
}
```

Dollar가 Franc이라고 뜨는 오류를 발견할 수 있습니다.

동치성 코드에서는 Dollar가 Franc과 비교되지 않는지 검사 해야합니다.

두 객체의 클래스를 비교함으로써 이러한 검사를 쉽게 할 수 있습니다.

금액과 클래스가 서로 동일할때만 두 Money가 같은것입니다.

Money

```tsx
public boolean equals(Object object) {
	Money money = (Money) object;
	return amount == money.amount;
		&& getClass().equals(money.getClss());
}
```

모델코드에서 클래스를 이런식으로 사용하는것은 좀 지저분하다고 합니다.

“자바 객체의 용어를 사용하는 것 보다 재정분야에 맞는 용어를 사용하고 싶다. 하지만 현재는 통화(currency)개념같은것은 없고 통화 개념을 도입할 충분한 이유가 없어보이므로 잠시 이대로 두자”

= 더 많은 동기가 있기 전에는 더 많은 설계를 도입하지 않기로 했습니다.

JS코드

자바스크립트로 변환된 `equals` 메서드는 두 객체의 클래스와 금액을 비교하여 동치성을 확인하도록 수정할 수 있습니다. 자바스크립트에서는 `instanceof` 연산자를 사용하여 객체의 타입을 확인하고, `constructor` 속성을 사용하여 클래스 비교를 할 수 있습니다.

다음은 수정된 `Money` 클래스와 `equals` 메서드입니다:

이 코드에서는 `equals` 메서드가 다음을 확인합니다:

-   `object`가 `Money`의 인스턴스인지 확인합니다.
-   `this.amount`와 `object.amount`가 동일한지 확인합니다.
-   `this.constructor`와 `object.constructor`가 동일한지 확인하여 클래스도 비교합니다.

이렇게 하면 `Dollar`와 `Franc` 같은 서로 다른 클래스의 객체가 금액이 같더라도 동일하지 않다고 간주됩니다.

```tsx
class Money {
  constructor(amount) {
    this.amount = amount;
  }

  **equals(object) {
    if (object instanceof Money) {
      return this.amount === object.amount && this.constructor === object.constructor;
    }
    return false;
  }**
}

class Dollar extends Money {
  constructor(amount) {
    super(amount);
  }
}

class Franc extends Money {
  constructor(amount) {
    super(amount);
  }
}

// 테스트 함수
function testEquality() {
  // 동일한 금액의 Dollar 비교
  console.assert(new Dollar(5).equals(new Dollar(5)) === true, 'Dollar(5) should equal Dollar(5)');
  // 다른 금액의 Dollar 비교
  console.assert(new Dollar(5).equals(new Dollar(10)) === false, 'Dollar(5) should not equal Dollar(10)');
  // Dollar와 Franc 비교 (금액이 같더라도 서로 다른 클래스이므로 false)
  console.assert(new Dollar(5).equals(new Franc(5)) === false, 'Dollar(5) should not equal Franc(5)');
  // Franc와 Dollar 비교 (금액이 같더라도 서로 다른 클래스이므로 false)
  console.assert(new Franc(5).equals(new Dollar(5)) === false, 'Franc(5) should not equal Dollar(5)');
}

// 테스트 실행
testEquality();

```

이번장의 성과

-   괴롭혔던 결함을 끄집어내서 테스트에 담아봤습니디.
-   완벽하지 않지만 그럭저럭 봐줄만한 방법 getClass()로 테스트를 통과하게 만들었습니다.
-   더 많은 동기가 있기 전에는 더 많은 설계를 도입하지 않기로 했습니다.

이제 공통 times()코드를 처리해야합니다.
