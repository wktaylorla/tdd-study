# 1장

## TDD의 기본 아이디어

테스트를 먼저 작성하고, 그 테스트를 통과하는 최소한의 코드를 작성한 후, 코드를 리팩토링하는 방식으로 개발을 진행합니다.

1. **실패하는 테스트 작성**: 아직 기능이 구현되지 않았기 때문에 실패합니다.
2. **코드 작성하여 테스트 통과**: 테스트를 통과하는 코드를 작성합니다.
3. **리팩토링**: 코드의 구조를 개선하고, 중복을 제거하며, 유지보수성을 높이는 리팩토링을 수행합니다.

## 다중 통화를 지원하는 Money 객체

**요구사항**

> $5 + 10CHF = $10(환율이 2:1일 경우)
>
> **$5 X 2 = $10**
>
> amount를 private로 만들기
>
> Dollar 부작용?
>
> Money 반올림?

### 실패하는 테스트 작성

```java
 public void testMultiplication() {
    Dollar five = new Dollar(5);
    // 5달러를 나타내는 Dollar 객체 생성
    five.times(2);
    // 이 객체의 금액을 두 배로 늘리는 times 메서드 호출
    assertEquals(10, five.amount);
    // 객체의 금액이 10으로 증가했는지 확인
 }
```

\*js로 변환한 테스트 코드

```javascript
test("multiplies dollar amount correctly", () => {
  const five = new Dollar(5);
  five.times(2);
  expect(five.amount).toBe(10);
});
```

위의 테스트로 인해 발생하는 컴파일 에러

- Dollar 클래스가 없음
- 생성자가 없음
- times(int) 메서드가 없음
- amount 필드가 없음

1. Dollar 클래스 정의

```java
// java
class Dollar {

}
```

```javascript
// javascript
class Dollar {}
```

2. 생성자 만들기

```java
// java
Dollar(int amount) {
    this.amount = amount;
}
```

```javascript
// javascript
class Dollar {
  constructor(amount) {
    this.amount = amount;
  }
}
```

3. times() 메서드의 스텁 구현

```java
// java
void times(int multiplier) {

}
```

```javascript
// javascript
class Dollar {
    constructor(amount){
        this.amount = amount;
    }
    times(multiplier) P

}

```

4. amount 필드 추가

```java
//java
int amount;
```

```javascript
// javascript
class Dollar {
  constructor(amount) {
    this.amount = amount;
  }
}
```

컴파일 성공 !! 그럼 이제 테스트가 실패한다!

당장의 목표는 완벽한 해법을 구하는 것이 아니라 **테스트를 통과**하는 것.

### 코드 작성하여 테스트 통과

테스트가 통과하기 위한 최소 작업은 아래와 같다.

```java
//java
class Dollar {
    int amount = 10;
    Dollar(int amount) {

    }
    void times(int multiplier){

    }
}
```

```javascript
//javascript
class Dollar {
  constructor(amount) {
    this.amount = 10;
  }
  times(multiplier) {}
}
```

## 리팩토링

중복을 제거한다.

```java
//java
int amount = 5 * 2;
```

```javascript
//javascript
class Dollar {
  constructor(amount) {
    this.amount = 10;
  }
}
```

객체의 초기화 단계에 있는 설정 코드를 times()메서드 안으로 옮기면?

```java
//java
class Dollar {
    int amount;
    void times(int multiplier){
        amount= 5 * 2;
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
    this.amount = 5 * 2;
  }
}
```

여전히 테스트는 통과된다.
5는 생성자에서 넘어도는 값이고 2는 인자 multiplier의 값이니 아래와 같이.

```java
//java
class Dollar {
    int amount;
    Dollar(int amount) {
        this.amount= amount;
    }
    void times(int multiplier){
        amount *= multiplier;
        // amount= amount * multiplier;
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
    // this.amount = this.amount * multiplier;
  }
}
```

**요구사항**

> $5 + 10CHF = $10(환율이 2:1일 경우)
>
> ~~**$5 X 2 = $10**~~
>
> amount를 private로 만들기
>
> Dollar 부작용?
>
> Money 반올림?

## TDD 예시: JavaScript

**요구사항**

> 문자열을 입력받아 그 문자열을 거꾸로 반환하는 함수를 작성합니다. 예를 들어, "hello"를 입력받으면 "olleh"를 반환합니다.

### 1. 첫 번째 테스트 작성

```javascript
// reverseString.test.js

import { reverseString } from "./reverseString";

test("reverses a string", () => {
  expect(reverseString("hello")).toBe("olleh");
});
```

### 2. 코드 작성하여 테스트 통과

```javascript
// reverseString.js

export const reverseString = (str) => {
  return str.split("").reverse().join("");
};
```

### 3. 리팩토링

```javascript
export const reverseString = (str) => {
  return [...str].reverse().join("");
};
```

_리팩토링은 코드의 가독성을 높이기 위한 예시입니다._

## TDD 예시: UI Test

**요구사항**

> 버튼 클릭으로 화면에 표시된 텍스트가 변경됩니다.

### 1. 첫 번째 테스트 작성

```javascript
// Button.test.js

import { render, screen, fireEvent } from "@testing-library/react";
import Button from "./Button";

test("changes text when button is clicked", () => {
  render(<Button />);
  const buttonElement = screen.getByRole("button");
  fireEvent.click(buttonElement);
  const textElement = screen.getByText("Button Clicked");
  expect(textElement).toBeInTheDocument();
});
```

### 2. 코드 작성하여 테스트 통과

```javascript
// Button.js

import React, { useState } from "react";

function Button() {
  const [text, setText] = useState("Initial Text");

  return (
    <div>
      <button onClick={() => setText("Button Clicked")}>Click me</button>
      <p>{text}</p>
    </div>
  );
}

export default Button;
```

### 3. 리팩토링

코드의 가독성을 높이거나 성능을 개선하기 위해 필요에 따라 컴포넌트를 더 작게 나누거나 스타일링을 추가할 수 있습니다.

## TDD 예시 : 페이지 전체를 새로 만드는 경우 TDD 도입 방법

페이지의 주요 기능을 작은 단위로 나누고, 각 단위에 대해 테스트를 작성한 후, 해당 기능을 구현하고 리팩토링을 반복하는 방식으로 작업을 진행합니다.

**요구사항**

1. 사용자는 이메일과 비밀번호를 입력할 수 있어야 합니다.
2. "로그인" 버튼을 클릭하면, 유효한 자격 증명인지 검증해야 합니다.
3. 잘못된 자격 증명이 제공되면 오류 메시지가 표시됩니다.
4. 유효한 자격 증명이 제공되면 대시보드로 이동합니다.

### 1) UI 요소가 있는지 확인

```javascript
// LoginPage.test.js

import { render, screen } from "@testing-library/react";
import LoginPage from "./LoginPage";

test("renders email and password fields and login button", () => {
  render(<LoginPage />);
  const emailInput = screen.getByLabelText(/email/i);
  const passwordInput = screen.getByLabelText(/password/i);
  const loginButton = screen.getByRole("button", { name: /login/i });

  expect(emailInput).toBeInTheDocument();
  expect(passwordInput).toBeInTheDocument();
  expect(loginButton).toBeInTheDocument();
});
```

### 2) 코드 작성

```javascript
// LoginPage.js

import React from "react";

function LoginPage() {
  return (
    <form>
      <label htmlFor="email">Email:</label>
      <input type="email" id="email" />

      <label htmlFor="password">Password:</label>
      <input type="password" id="password" />

      <button type="submit">Login</button>
    </form>
  );
}

export default LoginPage;
```

### 3) 리팩토링

입력 필드를 별도의 컴포넌트로 분리합니다.

```javascript
function InputField({ label, type, id }) {
  return (
    <div>
      <label htmlFor={id}>{label}</label>
      <input type={type} id={id} />
    </div>
  );
}

function LoginPage() {
  return (
    <form>
      <InputField label="Email:" type="email" id="email" />
      <InputField label="Password:" type="password" id="password" />
      <button type="submit">Login</button>
    </form>
  );
}
```

### 4) 다음 테스트 작성

로그인 로직을 테스트합니다.

```javascript
// LoginPage.test.js

import { render, screen, fireEvent } from "@testing-library/react";
import LoginPage from "./LoginPage";

test("shows error message on invalid credentials", async () => {
  render(<LoginPage />);

  fireEvent.change(screen.getByLabelText(/email/i), {
    target: { value: "invalid@example.com" },
  });
  fireEvent.change(screen.getByLabelText(/password/i), {
    target: { value: "wrongpassword" },
  });
  fireEvent.click(screen.getByRole("button", { name: /login/i }));

  const errorMessage = await screen.findByText(/invalid credentials/i);
  expect(errorMessage).toBeInTheDocument();
});
```

### 5) 로그인 로직 구현

```javascript
import React, { useState } from "react";

function LoginPage() {
  const [error, setError] = useState("");

  const handleSubmit = (event) => {
    event.preventDefault();
    const email = event.target.email.value;
    const password = event.target.password.value;

    if (email === "user@example.com" && password === "correctpassword") {
      // 로그인 성공 시 리다이렉트 또는 다른 동작 수행
    } else {
      setError("Invalid credentials");
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <InputField label="Email:" type="email" id="email" />
      <InputField label="Password:" type="password" id="password" />
      <button type="submit">Login</button>
      {error && <p>{error}</p>}
    </form>
  );
}
```

### 6) 이런 식으로 반복

위와 같은 방식으로 TDD를 통해 페이지를 점진적으로 완성해 나갑니다.
