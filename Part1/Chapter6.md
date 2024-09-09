# 6장

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
> **공용 equals**
>
> 공용 times

앞선 5장에서 테스트를 빨리 통과하기 위해 코드를 복사해서 붙여넣었다.

이를 청소하기 위해 두 클래스의 공통 상위 클래스를 만들어보자.

```
          +---------------------+
          |       Money         |
          +---------------------+
                    |
       +------------+------------+
       |                         |
+--------------+         +--------------+
|    Dollar    |         |    Franc     |
+--------------+         +--------------+
```

Money 클래스가 공통의 equals 코드를 갖도록 하자.

```java
//java
  class Money{}
```

```javascript
//javascript
class Money {}
```

Dollar가 Money를 상속하도록 하자.

```java
//java
  class Dollar extends Money{
    private int amount;
  }
```

```javascript
//javascript
class Dollar extends Money {
  constructor(amount) {
    super(); // 부모 클래스의 생성자 호출, 실행되어야 초기화가 완료된다.(this사용 전에 호출되어야 함)
    this.amount = amount;
  }
}
```

amount를 Money로 옮기자.

```java
//java
  class Money {
    protected int amount; // 하위 클래스에서 접근 가능하도록 protected로 변경
  }

  class Dollar extends Money {
    Dollar(int amount) {
      this.amount = amount;
    }
    public boolean equals(Object object) {
        Dollar dollar = (Dollar) object;
        return amount == dollar.amount;
    }
  }
```

```javascript
//javascript
class Money {
  constructor(amount) {
    this.amount = amount; // amount는 하위 클래스에서 접근 가능
  }
}

class Dollar extends Money {
  constructor(amount) {
    super(amount); // 상위 클래스의 생성자 호출
  }

  equals(object) {
    let dollar = object; // 임시 변수 선언
    return this.amount === dollar.amount;
  }
}
```

equals 메서드를 Money로 옮기자. 일단 임시변수를 선언하는 부분을 변경한다.

```java
//java
  class Dollar extends Money {
    Dollar(int amount) {
      this.amount = amount;
    }
    public boolean equals(Object object) {
      Money dollar = (Dollar) object;
      return amount == dollar.amount;
    }
  }
```

```javascript
//javascript
class Dollar extends Money {
  constructor(amount) {
    super(amount);
  }

  equals(object) {
    const dollar = object;
    return this.amount === dollar.amount;
  }
}
```

캐스트 부분을 변경하고 임시 변수의 이름을 변경한다.

```java
//java
  class Dollar extends Money {
    Dollar(int amount) {
      this.amount = amount;
    }
    public boolean equals(Object object) {
      Money money = (Money) object;
      return amount == money.amount;
    }
  }
```

```javascript
//javascript
class Dollar extends Money {
  constructor(amount) {
    super(amount);
  }

  equals(object) {
    const money = object;
    return this.amount === money.amount;
  }
}
```

이제 equals 메서드를 Dollar에서 Money로 옮길 수 있다.

```java
//java
  class Money {
    protected int amount;

    public boolean equals(Object object) {
      Money money = (Money) object;
      return amount == money.amount;
    }
  }

  class Dollar extends Money {
    Dollar(int amount) {
      this.amount = amount;
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
    return this.amount === money.amount;
  }
}

class Dollar extends Money {
  constructor(amount) {
    super(amount);
  }
}
```

이제 Franc.equals를 제거해야한다. 테스트가 없는 코드를 리팩토링하는 것은 위험하다. Dollar 테스트를 복사해서 Franc 테스트를 만들어보자.

```java
// java
public void testEquality() {
    assertTrue(new Dollar(5).equals(new Dollar(5)));
    assertFalse(new Dollar(5).equals(new Dollar(6)));
    assertTrue(new Franc(5).equals(new Franc(5)));
    assertFalse(new Franc(5).equals(new Franc(6)));
}
```

```javascript
// javascript
test("testEquality", () => {
  expect(new Dollar(5).equals(new Dollar(5))).toBeTruthy();
  expect(new Dollar(5).equals(new Dollar(6))).toBeFalsy();
  expect(new Franc(5).equals(new Franc(5))).toBeTruthy();
  expect(new Franc(5).equals(new Franc(6))).toBeFalsy();
});
```

이제 다시 Franc 코드를 수정해보자.

```java
//java
class Franc extends Money {
  private int amount;
}
```

```javascript
//javascript
class Franc extends Money {
  constructor(amount) {
    this.amount = amount;
  }
}
```

Money클래스의 필드를 사용해 Franc의 amount필드를 제거하고 Dollar와 같이 equals 메서드를 수정한다.

```java
//java
class Franc extends Money {
  private int amount;
  public boolean equals(Object object) {
    Money money = (Money) object;
    return amount == money.amount;
  }
}
```

```javascript
//javascript
class Franc extends Money {
  constructor(amount) {
    super(amount);
  }
  equals(object) {
    const money = object;
    return this.amount === money.amount;
  }
}
```

Franc.equals와 Money.equals가 같은 코드를 가지므로 Franc.equals를 제거하자.

이 장에서 한 작업을 정리해보면

- 공통된 코드를 첫 번째 클래스(Dollar)에서 상위 클래스(Money)로 단계적으로 옮겼다.
- 두 번째 클래스(Franc)도 Money의 하위 클래스로 만들었다.
- 불필요한 구현을 제거하기 전에 두 equals()구현을 일치시켰다.

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
> Franc와 Dollar 비교하기

다음 장에서 Franc와 Dollar를 비교해보자
