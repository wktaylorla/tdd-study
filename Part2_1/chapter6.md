# 6장. 돌아온 모두를 위한 평등

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
공용 equals
공용 times
```

위에 만든 중복 코드를 청소할시간.

Money 클래스가 공통의 equals코드를 갖게한다면?

-   Dollar
-   Franc

-   money
    -   Dollar
    -   Franc

Money

```tsx
class Money
```

Dollar

```tsx

class Dollar extends Money {
	private int amount;
}
```

amount인스턴스 변수를 Money로 옮길 수 있습니다.

Money

```tsx

class Money {
	protected int amount;
	// 하위클래스에서 변수를 볼 수있도록 가시성을 변경함
}
```

JS코드

Dollar

```tsx
class Dollar extends Money {}
```

```tsx
class Money {
    constructor(amount) {
        this.amount = amount;
    }
}

class Dollar extends Money {
    constructor(amount) {
        super(amount); // 부모 클래스의 생성자를 호출하여 amount를 초기화합니다.
    }
}
```

`amount` 인스턴스 변수를 `Money` 클래스로 옮기는 이유는 **코드 중복을 줄이고 상속 구조에서 공통된 로직을 관리하기 위해서**입니다. ( + 상위 클래스에서 공통 기능 관리 , + 확장성 및 재사용성)

`Dollar`와 같은 통화 클래스는 모두 `amount`(금액)를 가지며, 이 금액을 비교하는 로직도 유사합니다.

만약 `amount`를 각각의 통화 클래스에 두면 모든 클래스에서 동일한 `amount`와 관련된 로직을 중복해서 작성해야 합니다.

`amount`를 공통 상위 클래스인 `Money`로 옮기면, 상속받는 모든 통화 클래스(`Dollar`, `Franc` 등)가 이 변수를 공유할 수 있어 **중복을 제거**하고 코드의 유지보수성을 높일 수 있습니다.

이제 equal()코드를 위로 올리는 일을 할 수 있게되었습니다.

임시변수 선언하는 부분을 변경합니다. 이제 이 메서드를 money로 옮길 수 있습니다.

Dollar

money

```tsx
public boolean equals(Object object) {
	money **dollar** = (**Dollar**) object;
	retrun amount == dollar.amount;
}
```

```tsx
public boolean equals(Object object) {
	money **money** = (**Money**) object;
	retrun amount == money.amount;
}
```

JS코드

이 코드에서는 `Money` 클래스에서 `equals` 메서드를 정의하여 `amount` 값을 비교합니다.

`Dollar` 클래스는 `Money` 클래스를 상속받기 때문에 `equals` 메서드를 자동으로 사용할 수 있습니다. `Dollar` 클래스에서 `equals` 메서드를 특별히 오버라이드할 필요가 없다면, `Money` 클래스의 `equals` 메서드를 그대로 사용할 수 있습니다.

```tsx
class Money {
    constructor(amount) {
        this.amount = amount;
    }

    equals(object) {
        if (object instanceof Money) {
            return this.amount === object.amount;
        }
        return false;
    }
}

class Dollar extends Money {
    constructor(amount) {
        super(amount);
    }

    // 'equals' 메서드는 Money 클래스에서 이미 상속되므로, 여기서는 오버라이드하지 않아도 됩니다.
    // 만약 특별한 동작이 필요하다면, 여기에서 추가 구현할 수 있습니다.
}
```

이젠 Franc.equals()를 제거해야합니다.

```tsx
public void testEqulity() {
	assertTrue(new Dollar(5).equals(new Dallar(5)));
	assertFalse(new Dollar(5).equals(new Dallar(5)));
	assertTrue(new Dollar(5).equals(new Franc(5)));
	assertFalse(new Dollar(5).equals(new Franc(5)));
}
```

하지만 이것또한 중복입니다.

Franc

Franc

```tsx
class Franc extends Money {
	private int amount;
}
```

```tsx
class Franc extends Money {}
```

money 클래스에 있는 필드를 이용하면 Franc의 amount필드를 제거할 수 있습니다.

franc.equals()는 Monry.equals()와 거의 비슷해보입니다.

Franc

```tsx
public boolean equals(Object object) {
	money  **franc** = (**Franc**) object;
	retrun amount == **franc**.amount;
}
```

Franc

```tsx
public boolean equals(Object object) {
	Money  **money** = (**Money**) object;
	retrun amount == **money**.amount;
}
```

이제 franc.equals()와 Money.equals()사이에 차이가 없어보이므로 Franc의 불필요한 코드를 제거해봅시다.

그럼 잘 돌아가는 것을 확인할 수 있습니다.

JS코드

이 코드에서는 `assert.strictEqual`을 사용하여 두 값이 같음을 확인합니다.

`Dollar`과 `Franc` 클래스는 모두 `Money`를 상속받기 때문에 `equals` 메서드를 통해 비교할 수 있습니다. `testEquality` 함수는 다양한 조건을 테스트하여 결과를 검증합니다.

```tsx
class Money {
    constructor(amount) {
        this.amount = amount;
    }

    equals(object) {
        if (object instanceof Money) {
            return this.amount === object.amount;
        }
        return false;
    }
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
    console.assert(new Dollar(5).equals(new Dollar(5)) === true, "Dollar(5) should equal Dollar(5)");
    // 다른 금액의 Dollar 비교
    console.assert(new Dollar(5).equals(new Dollar(10)) === false, "Dollar(5) should not equal Dollar(10)");
    // Dollar와 Franc 비교 (같은 금액이라도 다른 종류의 화폐이므로 false)
    console.assert(new Dollar(5).equals(new Franc(5)) === false, "Dollar(5) should not equal Franc(5)");
    // Franc와 Dollar 비교 (같은 금액이라도 다른 종류의 화폐이므로 false)
    console.assert(new Franc(5).equals(new Dollar(5)) === false, "Franc(5) should not equal Dollar(5)");
}

// 테스트 실행
testEquality();
```

지금까지 한 작업 돌아보기

-   공통된 코드를 첫번째 클래스(Dollar)에서 상위클래스(Money)로 단계적으로 옮겼습니다.
-   두번째 클래스(Franc)도 Money의 하위 클래스로 만들었습니다.
-   불필요한 구현을 제거하기 위해 두 equals()구현을 일치시켰습니다.

그러나 Franc과 Dollar를 비교하면 어떻게 될까요? 이건 7장에서 다루어봅니다.
