# 2장

## TDD의 주기

1장에서 정리한 TDD의 기본 아이디어와 같다.

1. **테스트 작성**
2. **실행 가능하게 만든다**
3. **올바르게 만든다**

목적은 **작동하는 깔끔한 코드**를 얻는 것이다.

## 타락한 객체

**요구사항**

> $5 + 10CHF = $10(환율이 2:1일 경우)
>
> ~~$5 X 2 = $10~~
>
> amount를 private로 만들기
>
> **Dollar 부작용?**
>
> Money 반올림?

테스트를 통과했지만 한 가지 이상하다.

Dollar에 대해 연산을 수행한 후에 해당 Dollar의 값이 바뀐다.

아래와 같이 사용할 것이다.

### 설계상의 결함(Dollar 부작용)을 그 결함으로 인해 실패하는 테스트로 반환.

```java
//java
public void testMultiplication() {
    Dollar five = new Dollar(5);
    five.times(2);
    assertEquals(10, product.amount);
    five.times(3);
    assertEquals(15, product.amount);
}
```

```javascript
//javascript
test("multiplies dollar amount correctly", () => {
  const five = new Dollar(5);
  five.times(2);
  expect(product.amount).toBe(10);
  five.times(3);
  expect(product.amount).toBe(15);
});
```

times를 처음 호출한 후 five는 더이상 5가 아니다.

그렇다면 times()에서 새로운 객체를 반환하게 만든다면 five는 5로 유지될 것이다.

이를 위해 Dollar의 인터페이스와 테스트를 수정해야한다.

### 스텁 구현으로 빠르게 컴파일을 통과하도록 만들기

```java
//java
public void testMultiplication() {
    Dollar five = new Dollar(5);
    Dollar Product = five.times(2);
    assertEquals(10, product.amount);
    product = five.times(3);
    assertEquals(15, product.amount);
}
```

```javascript
//javascript
test("multiplies dollar amount correctly", () => {
  const five = new Dollar(5);
  let product = five.times(2);
  expect(product.amount).toBe(10);
  product = five.times(3);
  expect(product.amount).toBe(15);
});
```

위와 같이 테스트를 수정하면 컴파일이 되지 않을 것이다.
일단 코드를 컴파일되도록 수정한다.

### 올바르다고 생각하는 코드 입력하여 테스트 통과하기

```java
//java
class Dollar {
    int amount;
    Dollar(int amount) {
        this.amount= amount;
    }
    void times(int multiplier){
        amount *= multiplier;
        return null;
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
    this.amount *= multiplier;
    return null;
  }
}
```

이제 컴파일은 되지만 실행되지 않는다. 테스트를 통과하도록 수정한다.

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

**요구사항**

> $5 + 10CHF = $10(환율이 2:1일 경우)
>
> ~~$5 X 2 = $10~~
>
> amount를 private로 만들기
>
> ~~**Dollar 부작용?**~~
>
> Money 반올림?

빨리 통과되기 위해 취할 수 있는 전략

1. 가짜로 구현하기
   : 상수를 반환하게 만들고 진짜 코드를 얻을 때까지 단계적으로 상수를 변수로 바꾸어간다.
2. 명백한 구현 사용하기
   : 실제 구현을 입력한다.
3. 삼각측량(triangulation)
   : 3장에서 배워보자.
