# 8장. 객체 만들기

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
**~~Franc과 Dollar비교하기~~
통화?**
```

Franc

```tsx
Franc times(int multiplier) {
	return new Franc(amount * multiplier);
}
```

Dollar

```tsx
Dollar times(int multiplier) {
	return new Dollar(amount * multiplier);
}
```

양쪽모두 Money를 반환하게 만들면 더 비슷하게 만들 수 있습니다.

Franc

```tsx
**Money** times(int multiplier) {
	return new Franc(amount * multiplier);
}
```

Dollar

```tsx
**Money** times(int multiplier) {
	return new Dollar(amount * multiplier);
}
```

Money의 두 하위 클래스는 그다지 많은 일을 하지 않아서 지우고 싶으나 , 한번에 큰 단계를 밟는건 TDD를 효과적으로 보여주기 적절하지 않을 것 같다는 저자의 생각.

하위 클래스에 대한 직접적 참조가 적어진다면 하위클래스를 제거하기위해 한벌짝 다가섰다고 할 수 있겠습니다.

Money에 Dollar를 반환하는 팩토리메서드를 도입할 수 있습니다.

```tsx
public void testMultiplication() {
	Dollar five = Money.dollar(5);
	assertEquals(new Dollar(10), five.times(2));
	assertEquals(new Dollar(15), five.times(3));
}

```

구현 코드는 Dollar를 생성하여 번환합니다.

Dollar

```tsx
static Dollar dollar(int amount) {
	return new Dollar(amount)
}
```

Dollar에 대한 참조가 사라지길 바라므로 테스트의 선언부를 다음과 같이 바꿔야합니다.

```tsx
public void testMultiplication() {
	**Money** five = Money.dollar(5);
	assertEquals(new Dollar(10), five.times(2));
	assertEquals(new Dollar(15), five.times(3));
}
```

컴파일러가 Money에는 times()가 정의되어 있지 않았다는 사실을 알려줍니다.

지금은 구현준비가 안되어있으므로 , Money를 추상클래스로 변경 후 Money.times()를 선언합니다.

Money

```tsx
abstract class Money
abstract Money times(int multiplier);
```

이제 팩토리 메서드의 선언을 바꿀 수 있습니다.

Money

```tsx
static Money dollar(int amount) {
	return new Dollar(amount);
}
```

JS

`Money` 클래스를 추상 클래스로 변경하고 `times` 메서드를 선언하는 방식으로 구현할 수 있습니다. 자바스크립트는 클래스에 추상 메서드를 직접 선언할 수 없기 때문에, 추상 메서드는 서브클래스에서 구현해야 하는 메서드로 정의하고, `Money` 클래스에서는 이를 명시적으로 에러를 발생시켜 구현되지 않았음을 알려줄 수 있습니다.

```tsx
class Money {
  constructor(amount) {
    if (new.target === Money) {
      throw new Error("Cannot instantiate abstract class Money directly.");
    }
    this.amount = amount;
  }

  **// 추상 메서드로 정의하고 서브클래스에서 구현하도록 강제합니다.
  times(multiplier) {
    throw new Error("Method 'times()' must be implemented.");
  }**

  equals(object) {
    if (object instanceof Money) {
      return this.amount === object.amount && this.constructor === object.constructor;
    }
    return false;
  }

  // 팩토리 메서드
  static dollar(amount) {
    return new Dollar(amount);
  }
}

class Dollar extends Money {
  constructor(amount) {
    super(amount);
  }

  times(multiplier) {
    return new Dollar(this.amount * multiplier);
  }
}

class Franc extends Money {
  constructor(amount) {
    super(amount);
  }

  times(multiplier) {
    return new Franc(this.amount * multiplier);
  }
}

// 테스트 함수
function testMultiplication() {
  const five = Money.dollar(5);
  console.assert(five.times(2).equals(new Dollar(10)), 'Dollar(5) times 2 should equal Dollar(10)');
  console.assert(five.times(3).equals(new Dollar(15)), 'Dollar(5) times 3 should equal Dollar(15)');
}

// 테스트 실행
testMultiplication();

```

```tsx
public void testMultiplication() {
	**Money** five = Money.dollar(5);
	assertEquals(new Dollar(10), five.times(2));
	assertEquals(new Dollar(15), five.times(3));
}

public void testEqulity() {
	assertTrue(Money.dollar(5).equals(Money.dollar(5)));
	assertFalse(Money.dollar(5).equals(Money.dollar(6)));
	assertTrue(new Franc(5).equals(new Franc(5)));
	assertFalse(new Franc(5).equals(new Franc(5)));
	assertFalse(new Franc(5).equals(new Dollar(5)));
}

public void testFrancMultiplication() {
	**Money** five = Money.Franc(5);
	assertEquals(money Franc(10), five.times(2));
	assertEquals(money Franc(15), five.times(3));
}
```

JS

-   `Money` 클래스는 추상 클래스 역할을 하며 `times` 메서드는 서브클래스에서 구현하도록 강제합니다.
-   `Dollar`와 `Franc` 클래스는 각각 `times` 메서드를 구현합니다.
-   **`Money.dollar` 및 `Money.franc`는 `Dollar` 및 `Franc` 인스턴스를 생성하는 팩토리 메서드입니다.**
-   `console.assert`를 사용하여 테스트 조건을 확인하고, 예상 결과와 실제 결과를 비교합니다.

```tsx
class Money {
  constructor(amount) {
    if (new.target === Money) {
      throw new Error("Cannot instantiate abstract class Money directly.");
    }
    this.amount = amount;
  }

  **// 추상 메서드
  times(multiplier) {
    throw new Error("Method 'times()' must be implemented.");
  }**

  equals(object) {
    if (object instanceof Money) {
      return this.amount === object.amount && this.constructor === object.constructor;
    }
    return false;
  }

  // 팩토리 메서드
  static dollar(amount) {
    return new Dollar(amount);
  }

  static franc(amount) {
    return new Franc(amount);
  }
}

class Dollar extends Money {
  constructor(amount) {
    super(amount);
  }

  times(multiplier) {
    return new Dollar(this.amount * multiplier);
  }
}

class Franc extends Money {
  constructor(amount) {
    super(amount);
  }

  times(multiplier) {
    return new Franc(this.amount * multiplier);
  }
}

// 테스트 함수
function testMultiplication() {
  const five = Money.dollar(5);
  console.assert(five.times(2).equals(new Dollar(10)), 'Dollar(5) times 2 should equal Dollar(10)');
  console.assert(five.times(3).equals(new Dollar(15)), 'Dollar(5) times 3 should equal Dollar(15)');
}

function testEquality() {
  console.assert(Money.dollar(5).equals(Money.dollar(5)), 'Dollar(5) should equal Dollar(5)');
  console.assert(!Money.dollar(5).equals(Money.dollar(6)), 'Dollar(5) should not equal Dollar(6)');
  console.assert(new Franc(5).equals(new Franc(5)), 'Franc(5) should equal Franc(5)');
  console.assert(!new Franc(5).equals(new Franc(6)), 'Franc(5) should not equal Franc(6)');
  console.assert(!new Franc(5).equals(new Dollar(5)), 'Franc(5) should not equal Dollar(5)');
}

function testFrancMultiplication() {
  const five = Money.franc(5);
  console.assert(five.times(2).equals(new Franc(10)), 'Franc(5) times 2 should equal Franc(10)');
  console.assert(five.times(3).equals(new Franc(15)), 'Franc(5) times 3 should equal Franc(15)');
}

// 테스트 실행
testMultiplication();
testEquality();
testFrancMultiplication();

```

-   동일한 메서드(times)의 두 변이형 메서드 서명부를 통일시켜 중복제거에 한단계 전진했습니다.
-   최소한 메서드 선언부만이라도 공통 상위 클래스(superclass)로 옮겼습니다.
-   팩토리메서드를 도입하여 테스트코드에서 콘트리트 하위 클래스의 존재사실을 분리해냈습니다.
-   하위 클래스가 사라지면 몇몇 테스트는 불필요한 여분의 것이 된다는 것을 인식했습니다. (일단 놔둠)
