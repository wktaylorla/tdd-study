# 5장. 솔직히 말하자면

```tsx
public void testFrancMultiplication() {
	Frans five = new Franc(5);
	assertEquals(new Franc(10), five.times(2));
	assertEquals(new Franc(15), five.times(3));
}

class Franc {
	Private int amount;
	Franc(int amount) {
		this.amount = amount;
	}

	Fran times(int multiplier) {
		return new Franc(amount*multiplier);
	}

	public boolean equals(Object object) {
		franc franc = (Franc) object;
		return amount == franc.amount
	}
}
```

```tsx
function testFrancMultiplication() {
    const five = new Franc(5);
    console.assert(JSON.stringify(five.times(2)) === JSON.stringify(new Franc(10)), "Test failed: Franc(5) * 2 should equal Franc(10)");
    console.assert(JSON.stringify(five.times(3)) === JSON.stringify(new Franc(15)), "Test failed: Franc(5) * 3 should equal Franc(15)");
}

class Franc {
    constructor(amount) {
        this.amount = amount; // 5
    }

    times(multiplier) {
        return new Franc(this.amount * multiplier);
        5 * 2;
    }

    equals(other) {
        return this.amount === other.amount;
    }
}

// 실행 테스트
testFrancMultiplication();
```

### Java:

-   *`assertEquals`*를 사용하여 두 객체가 같은지 확인합니다.
    -   *`assertEquals`*는 두 객체를 비교할 때 **객체의 `equals` 메서드를 호출**하여 비교합니다.
    -   즉, 두 객체가 같은지 여부를 판단할 때, **메모리 주소를 비교하는 것이 아니라 `equals` 메서드의 결과**를 통해 비교합니다.

### JavaScript:

-   *`console.assert`*를 사용하여 비교합니다.
    -   객체를 비교할 때 **`JSON.stringify`를 이용**하거나, 직접 **`equals` 메서드를 구현**하여 비교할 수 있습니다.
    -   (위 코드에서는 \**`JSON.stringify`*를 이용하여 객체의 속성을 문자열로 변환한 후 비교하고 있습니다.)

1. 테스트 작성
2. 컴파일되게 하기
3. 실패하는지 확인하기위해 실행
4. 실행하게 만듦
5. **중복제거**

각 단계엔 서로 다른 목적이 있고 처음 네단계는 빨리 진행해야합니다.

코드를 실행시키기 까지 단계가 짧았기 때문에 컴파일 되게 하기로 넘어갈수있었습니다.

중복이 많기때문에 다음 테스트 작성전까지 이것들을 제거해야합니다.

equals()를 일반화 하는 것 부터 시작.

-   큰 테스트를 공략 할수없다. 진전을 나타낼 수 있는 작은 테스트로 만들어보았다.
-   중복을 만들고 조금 고쳐서 테스트를 시작했다.
-   중복이 사라지기 전에는 집에 가지않겠다고 약속했다.
    -   설계의 모든원칙을 무시할수없게…
    -   속도를 위해 훌륭한 설계의 모든 교리를 어겼으니 바로잡으라는 말로 보입니다.
