# 4장

**요구사항**

> $5 + 10CHF = $10(환율이 2:1일 경우)
>
> ~~$5 X 2 = $10~~
>
> **amount를 private로 만들기**
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

단언(assertion)을 Dollar와 Dollar를 비교하는 것으로 재작성.

```java
//java
public void testMultiplication() {
    Dollar five = new Dollar(5);
    Dollar Product = five.times(2);
    assertEquals(new Dollar(10), product);
    product = five.times(3);
    assertEquals(new Dollar(15), product);
}
```

```javascript
//javascript
test("multiplies dollar amount correctly", () => {
  const five = new Dollar(5);
  let product = five.times(2);
  expect(product).toBe(new Dollar(10));
  product = five.times(3);
  expect(product).toBe(new Dollar(15));
});
```

그러면 임시 변수인 product는 더 이상 쓸모없어 보인다.

```java
//java
public void testMultiplication() {
    Dollar five = new Dollar(5);
    assertEquals(new Dollar(10), five.times(2));
    assertEquals(new Dollar(15), five.times(3));
}
```

```javascript
//javascript
test("multiplies dollar amount correctly", () => {
  const five = new Dollar(5);
  expect(five.times(2)).toBe(new Dollar(10));
  expect(five.times(3)).toBe(new Dollar(15));
});
```

이 테스트는 일련의 오퍼레이션이 아니라 참인 명제에 대한 단언들이므로 우리의 의도를 더 명확하게 이야기해준다.

테스트를 고치고 나니 이제 Dollar의 amount 인스턴스 변수를 사용하는 코드는 Dollar 자신밖에 없게 됐다. 따라서 변수를 private으로 변경할 수 있다.

\*Dollar 클래스 외부에서 amount에 접근할 필요가 없어졌으므로, 이 변수를 private으로 변경하여 클래스 내부에서만 접근할 수 있도록 제한할 수 있다는 말이다.

```java
//java
public class Dollar {
    private int amount;

    public Dollar(int amount) {
        this.amount = amount;
    }

    public Dollar times(int multiplier) {
        return new Dollar(amount * multiplier);
    }

    @Override
    public boolean equals(Object object) {
        Dollar dollar = (Dollar) object;
        return amount == dollar.amount;
    }
}
```

```javascript
//javascript
class Dollar {
  #amount; // private 필드

  constructor(amount) {
    this.#amount = amount;
  }

  times(multiplier) {
    return new Dollar(this.#amount * multiplier);
  }

  equals(other) {
    return this.#amount === other.#amount;
  }
}
```

**요구사항**

> $5 + 10CHF = $10(환율이 2:1일 경우)
>
> ~~$5 X 2 = $10~~
>
> ~~**amount를 private로 만들기**~~
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

amount 변수는 private으로 선언되어, Dollar 클래스 내부에서만 접근 가능하다.

할일 목록의 하나를 제거했지만 위험한 상황을 만들었다.

만약 동치성 테스트가 실패한다면, 곱하기 테스트 역시 곱하기에 대한 코드가 정확하게 작동한다는 것을 검증하는 데 실패하게 된다.

\*동치성 테스트는 객체의 기본적인 비교 기능을 검증하는 중요한 부분으로 이 테스트가 실패하면 그 객체를 사용하는 다른 연산도 그 결과를 신뢰할 수 없게된다.
