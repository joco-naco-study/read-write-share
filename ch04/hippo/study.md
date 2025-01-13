# Chapter 4 오류

오류 사례를 신중하게 생각하지 않고는 견고하고 신뢰성 높은 코드를 작성할 수 없다.

---

## 복구 가능성


### 복구 가능한 오류

오류가 치명적이지 않으며, 발생하더라도 사용자는 알아채지 못하고 프로그램을 계속 작동시킬 수 있는 오류들을 말한다.

- 네트워크 오류
  - 자신의 코드가 의존하는 서비스에 연결할 수 없는 경우 몇 초 동안 기다렸다가 다시 시도하거나, 그 코드가 사용자의 장치에서 실행되는 경우라면 사용자에게 네트워크 연결을 확인하도록 요청하는 것이 최상이다.
  
- 중요하지 않은 작업 오류
  - 예를 들어 서비스 사용에 대한 어떤 통계를 기록하는 부분에서 오류가 발생한다면 실행을 계속해도 무방할 것이다.


### 복구할 수 없는 오류

프로그래밍 오류 때문에 발생하는 경우가 많으며, 이 경우는 개발자가 코드의 어느 부분에서 '뭔가를 망쳐놓은 것이다'

- 잘못된 입력 인수로 호출
- 일부 필요한 상태를 사전에 초기화하지 않음


### 호출하는 쪽에서만 오류 복구 가능 여부를 알 때가 많다.

코드는 종종 재사용되고 여러 곳에서 호출된다.

간결한 추상화 계층을 만들고자 한다면 일반적으로 코드의 잠재적 호출자에 대한 가정을 가능한 한 하지 않는 것이 좋다.

함수를 작성하거나 수정하는 시점에 오류로부터 복구할 수 있는지 혹인 복구해야 하는지 항상 알수 있는 것은 아니기 때문이다.

### 아래와 같은 상황이라면, 함수에 제공된 값으로 인해 발생하는 오류는 호출하는 쪽에서 복구하고자 하는 것으로 간주해야 한다.

- 함수가 어디서 호출될지 그리고 호출 시 제공되는 값이 어디서 올지 정확한 지식이 없다.
- 코드가 미래에 재사용될 가능성이 아주 희박하다. 재사용이 된다면 코드가 어디에서 호출되고 값이 어디서 오는지에 대한 가정이 의미가 없어질 수 있음을 뜻한다.

이것의 유일한 예외는 특정 입력이 무효라는 점이 코드 계약을 통해 명확하고, 호출하는 쪽에서 함수 호출하기 전에 입력 검증하는 쉽고 분명한 방법을 가지고 있는 경우이다.

호출하는 쪽에서 오류로부터 복구하기를 원할 것이라고 판단하는 것은 좋은 일이지만,

오류가 발생할 수 있다는 것조차 인식하지 못한다면 그것을 제대로 처리하지 못한 것이다!

### 호출하는 쪽에서 복구하고자 하는 오류에 대해 인지하도록 하라


---

## 견고성 vs 실패

오류가 발생할 때, 다음 중 하나를 선택해야 한다.

- 실패, 더 높은 코드 계층이 오류를 처리하게 하거나 전체 프로그램의 작동을 멈추게 한다.
- 오류를 처리하고 계속 진행한다.

오류가 있더라도 처리하고 계속 진행하면 더 견고한 코드라고 볼 수 있지만,

반대로 오류가 감지되지 않고 이상한 일이 발생하기 시작한다는 의미가 될 수 있다.


### 신속하게 실패하라

신속하게 실패하기(failing test)는 가능한 한 문제의 실제 발생 지점으로부터 가까운 곳에서 오류를 나타내는 것이다.

버그의 실제 발생 지점과 별로 상관없는 곳에서 오류 발생 시 문제 해결에 별로 도움이 되지 않는다.

- 오류가 발생했을 때 신속하게 실패하지 않으면, 오류는 실제 위치에서 멀리 떨어진 코드에서 나타날 수 있다. 이렇게 되면 문제를 추적하고 해결하기 위해 상당한 노력이 필요할 수 있다.
- 오류가 발생했을 때 코드가 신속하게 실패하면, 대개의 경우 오류의 정확한 위치가 즉시 확인된다.


### 요란하게 실패하라

요란한 실패는 오류가 발생하는데도 불구하고 아무도 모르는 상황을 막고자 하는 것이다.

이를 위한 가장 명백한 방법은 예외를 발생해 프로그램이 중단되게 하는 것이다.

코드가 실패할 때 신속하고, 요란하게 오류를 나타내면 개발 도중이나 테스트하는 동안 버그가 발견될 가능성이 크다.

그렇지 않더라도 배포된 후에 오류 보고를 보기 시작할 것이고 보고 내용으로부터 버그가 발생한 위치를 정확히 알 수 있는 이점이 있다.


### 복구 가능성의 범위

일반적으로 소프트웨어를 견고하게 작정하는 것이 좋다.

한 번의 잘못된 요청으로 인해 전체 서버의 동작이 멈추는 것은 바람직하지 않다.

그러나 오류를 알아차리지 못한 태 시스템이 계속 동작하지 않도록 하는 것 또한 중요하기 때문에 코드가 요란하게 실패해야한다.

두 목표는 양립하지 못할 때가 많지만 이에 대한 해결책은 프로그래밍 오류가 발견되면 개발자가 이를 알아차릴 수 있도록 프로그래밍 오류를 기록하고 모니터링하는 것이다.

**흐름**

1. 들어오는 요청
2. 요청 처리 로직 (잠재적인 프로그래밍 오류 포함)
3. 프로그래밍 오류에 대해 비검사 예외 발생
4. 서버 전체가 동작을 멈추지 않도록 단일 요청에 대한 예외 발생
5. 오류 기록
6. 모니터링과 알림!

> 서버 프레임워크
> 
> 대부분의 서버 프레임워크에는 개별 요청에 대한 오류를 격리하고 특정 유형의 오류를 다른 오류 응답 및 처리에 매핑할 수 있는 기능이 내장되어 있다.
> 
> 따라서 개발자가 직접 try-catch 문장을 작성해야 할 가능성은 작지만, 개념적으로 이와 유사한 일이 서버 프레임워크 내부에서 일어난다.


### 오류를 숨기지 않음

전체 소프트웨어 동작을 멈추지 않도록 하기 위해 코드의 독립적이거나 중요하지 않은 부분을 분리하면 견고한 시스템을 구축할 수 있다.

독립적이지 않거나, 중요하거나, 낮은 계층에서 오류가 발생함에도 불구하고 계속 진행하면 소프트웨어가 의도한 대로 작동하지 않는 경우가 많다.

오류를 숨기는 것은 복구할 수 있는 오류와 복구할 수 없는 오류 모두에 문제를 일으킨다!

- 호출하는 쪽에서 복구하고자 할 수도 있는 오류를 숨기면, 호출하는 쪽에서 오류로부터 복구할 수 있는 기회를 없애는 것이다!
- 복구할 수 없는 오류를 숨기면 프로그래밍 오류가 감춰진다.

위 두 경우 모두 에러가 발생하면 일반적으로 호출하는 쪽에서 예측한 대로 코드가 실행되지 않는다는 것을 의미한다.

오류를 감추면 호출하는 쪽에서는 모든 것이 잘 작동하고 있다고 가정하지만 실제로 코드는 제대로 동작을 못할 것이다.


---

**기본값 반환**

```java
class AccountManager {
    private final AccountStore accountStore;

  Double getAccountBalanceUsd(Int customerId) {
    AccountResult result = accountStore.lookup(customerId);
    if (!result.success()) {
        return 0.0; // 오류가 발생하면 기본값 0 이 반환된다.
    }
    return result.getAccount().getBalanceUsd();
  }
}
```

오류가 발생하고 함수가 원하는 값을 반환할 수 없는 경우 기본값을 반환하는 것이 간단하고 쉬운 해결책처럼 보일 때가 있다.

기본값의 문제점은 오류가 발생했다는 사실을 숨긴다는 것인데, 이는 코드를 호출하는 쪽에서 모든 것이 정상인 것처럼 계속 진행한다는 것을 의미한다.

코드에 기본값을 두는 것이 유용한 경우가 있을 수 있지만,

이는 잘못된 데이터로 시스템이 제대로 작동하지 못하게 만들고 오류가 나중에 이상한 방식으로 나타날수 있기 때문에 신속한 실패와 요란한 실패의 원리를 위반하는 것이다.

---

**널 객체 패턴**

```java
class InvoiceManager{
    private final InvoiceStore invoiceStore;

  List<Invoice> getUnpaidInvoices(Int customerId) {
    InvoiceResult result = invoiceStore.query(customerId);
    if (!result.success()) {
        return []; // 오류가 발생하면 빈 리스트를 반환한다.
    }
    return result
            .getInvoices()
            .fileter(invoice -> !invoice.isPaid());
  }
}
```

널 객체는 개념적으로 기본값과 유사하지만 이것을 더 확장해서 더 복잡한 객체를 다룬다.

널 객체는 실제 반환값처럼 보이지만 모든 멤버 함수는 아무것도 하지 않거나 의미 없는 기본값을 반환한다.

널 객체 패턴을 사용하는 것이 꽤 유용한 경우가 있지만, 앞의 예에서 알 수 있듯이 오류 처리에 사용하는 것은 바람직하지 않다.

---

**아무것도 하지 않음**

문제가 발생할 때 가능한 한 가지 옵션은 오류가 발생했다는 신호를 보내지 않는 것이다.

호출하는 쪽에서는 코드에서 작업이 의도대로 완료되었다고 가정하기 때문에 일반적으로 이렇게 하는 것은 바람직하지 않다.

오류 발생 시 아무것도 하지 않음
```java
class MutableInvoice {
  void addItem(InvoiceItem item) {
    if (item.getPrice().getCurrency() != this.getCurency) {
      return; // 통화가 일치하지 않으면 함수는 되돌아간다.
    }
    this.items.add(item);
  }
}
```

예외가 발생하면 코드는 이를 억제하고 호출하는 쪽에 실패에 대한 신호를 보내지 않는다.
이로 인해 소프트웨어 버그가 일어날 가능성이 매우 높으며, 이 함수의 호출자는 이메일이 전송되지 않은 경우에도 전송된 것으로 가정할 것이다.

예외 억제
```java
class InvoiceSender {
    private final EmailService emailService;

  void emailInvoice(String emailAddress, Invoice invoice) {
      try {
        emailService.sendPlainText(
                emailAddress,
                InvoiceFormat.plainText(invoice)
        ); 
      } catch (EmailException e) {}; // EmailException이 발생하지만 무시된다.
  }
}
```

오류가 발생할 때 이 오류를 기록하면 약간 개선된 것이긴 하지만, 개발자가 로그를 확인한 경우만 이러한 오류를 알아차릴 수 있다.

여전히 호출하는 쪽에 오류를 숨기고 있어서 이메일이 실제로 발송되지 않는 경우 발송되었다고 가정할 수 있다.

예외 탐지 및 오류 기록
```java
class InvoiceSender {
    private final EmailService emailService;

  void emailInvoice(String emailAddress, Invoice invoice) {
      try {
          emailService.sendPlainText(
                  emailAddress,
                  InvoiceFormat.plainText(invoice)
          );
      } catch (EmailException e) {
          logger.logError(e); // EmailException이 기록된다.
      }
  }
}
```

> 기록되는 내용에 주의해야 한다.
> 
> 사용자의 개인 정보가 포함될 가능성인데,
> 
> 이 경우에는 특정 데이터 처리 정책의 적용을 받을 수 있다.
> 
> 개인 정보를 오류 로그에 기록하면 해당 데이터 처리 정책에 위반될 수 있다.


**오류를 숨기는 것은 바람직하지 않다!!**

---

## 오류 전달 방법

오류가 발생하면 일반적으로 더 높은 계층으로 오류를 알려야 한다.

오류로부터 복구할 수 없는 경우 이는 일반적으로 프로그램의 훨씬 더 높은 계층에서 실행을 중지하고, 오류를 기록하거나 전체 프로그램의 실행을 종료하는 것을 의미한다.

오류로부터의 복구가 잠재적으로 가능한 경우, 일반적으로 즉시 호출하는 쪽에 오류를 알려 정상적으로 처리할 수 있도록 해야 한다.


### 오류 전달 작업을 수행하는 방법

- 명시적 방법
  - 코드를 직접 호출한 쪽에서 오류가 발생할 수 있음을 인지할 수밖에 없도록 한다.
  - 오류가 발생할 가능성이 코드 계약의 명확한 부분에 나타나 있기 때문에 오류를 모르고 넘어갈 수 있는 방법은 거의 없다.

- 암시적 방법
  - 코드를 호출하는 쪽에 오류를 알리지만, 호출하는 쪽에서 그 오류를 신경 쓰지 않아도 된다.
  - 오류가 발생할 수 있음을 알기 위해서는 문서나 코드를 읽는 등의 적극적인 노력이 필요하다.
  - 문서에 오류가 언급되어 있다면 코드 계약의 숨겨진 세부 조항이다.
  - 가끔 오류가 언급되지 않을 때도 있는데, 이 경우엔 오류가 계약 내용에 전혀 없는 것이 된다.

---

### 명시적 방법 : 검사 예외

컴파일러는 검사 예외에 대해 호출하는 쪽에서 예외를 인지하도록 강제적으로 조치하는데, 

호출하는 쪽에서는 예외 처리를 위한 코드를 작성하거나 자신의 함수 시그니터에 해당 예외 발생을 선언해야 한다.

검사 예외를 사용하는 것은 오류를 전달하기 위한 명시적인 방법이다.


**검사 예외를 사용한 오류 전달**

검사 예외 발생
```java
class NegativeNumberException extends Exception { // 검사 예외의 구체적 유형임을 나타내는 클래스
    private final Double erroneousNumber; // 오류를 유발한 숫자를 캡슐화해서 추가 정보를 제공

  NegativeNumberException(Double erroneousNumber) {
      this.erroneousNumber = erroneousNumber;
  }
  
  Double getErroneousNumber() {
      return erroneousNumber;
  }

  Double getSquareRoot(Double value) throws NegativeNumberException { // 함수는 검사 예외를 발생시킬 수 있음을 선언해야 한다.
    if (value < 0.0) {
        throw new NegativeNumberException(value); // 오류가 있을 시 검사 예외를 발생시킨다.
    }
    return Math.sqrt(value);
  }
}
```

검사 예외의 포착
```java
void displaySquareRoot() {
    Double value = ui.getInputNumber();
    try {
        ui.setOutput("Square root is: " + getSquareRoot(value));
    } catch (NegativeNumberException e) { // 예외를 방생기키면 이 예외를 포착한다.
      ui.setError("Can`t get square root of negative number: " + e.getErroneousNumber()); 
      // 예외 객체 안에 들어 있는 오류에 대한 정보를 표시한다.
    }
}
```

검사 예외를 포착하지 않음
```java
void displaySquareRoot() throws NegativeNumberException { // NegativeNumberException이 displaySquareRoot() 함수의 시그니처에 선언되어 있다.
    Double valur = ui.getInputNumber();
  ui.setOutput("Square root is: " + getSquareRoot(valur));
}
```

displaySquareRoot() 함수가 NegativeNumberException 예외를 포착하지도 않고, 자신의 함수 시그니터에 선언하지도 않으면 코드는 컴파일되지 않는다. 

**호출하는 쪽에더는 어떤 형태로든 해당 오류를 강제적인 방식으로 인지할 수밖에 없기 때문에 검사 예외는 오류를 전달하는 명시적인 방법이 된다.**

---

### 암시적 방법 : 비검사 예외

비검사 예외를 사용하면 다른 개발자들은 코드가 이 예외를 발생시킬 수 있다는 사실을 전혀 모를 수 있다.

이 경우에는 함수레서 어떤 예외를 발생시키는지 문서화하는 것이 바람직하지만 개발자가 문서화하는 것을 잊어버릴 때가 있다.

설사 문서화를 하더라도 이것은 코드 계약의 세부 조항이다.

따라서 비검사 예외는 오류가 발생할 수 있다는 것을 호출하는 쪽에서 인지하리라는 보장이 없기 때문에 오류를 암시적으로 알리는 방법이다.


**비검사 예외를 사용한 오류 전달**

```java
class NegativeNumberException extends RuntimeException { // 비검사 예외의 구체적인 유형임을 나타내는 클래스
    private final Double erroneousNumber;

  NegativeNumberException(Double erroneousNumber) {
      this.erroneousNumber = erroneousNumber;
  }
  
  Double getErroneousNumber() {
      return erroneousNumber;
  }

  
  /*
   * @throws NegativeNumberException 값이 음수일 경우  => 이떤 종류의 비검사 예외가 발생할 수 있는지 문서화하는 것이 권장된다 (하지만 강제 사항은 아니다.)
   * */
  Double getSquareRoot(Double value) {
    if (value < 0.0) {
        throw new NegativeNumberException(value); // 오류에 대해 비검사 예외를 발생시킨다.
    }
    return Math.sqrt(value);
  }
}
```

**비검사 예외 처리**

```java
void displaySquareRoot() {
    Double value = ui.getInputNumber();
    try {
        ui.setOutput("Square root is: " + getSquareRoot(value));
    } catch (NegativeNumberException e) { // getSquareRoot()가 NegativeNumberException 예외를 발생시키면 이 예외를 포착하고 처리한다.
      ui.setError("Can`t get square root of negative number: " + e.getErroneousNumber());
    }
}
```

**비검사 예외를 처리하지 않음**

```java
void displaySquareRoot() {
    Double value = ui.getInputNumber();
  ui.setOutput("Square root is: " + getSquareRoot(value));
}
```

getSquareRoot() 함수를 호출하는 다른 함수는 NegativeNumberException에 대한 이전 예와 동일한 방식으로 예외를 포착하고 처리할 수 있다.

중요한 것은 getSquareRoot()를 호출하는 함수가 예외를 확인하고 처리하지 않아도 된다는 점이다.

예외를 포착해서 처리하지 않는 경우 자체 함수 시그니처에 선언하지 않아도 되고 심지어 문서화 하지 않아도 된다.

비검사 예외이기 떄문에 이 코드는 문제없이 컴파일된다.

getSquareRoot()가 NegativeNumberException 예외를 발생시키면, 이 예외를 처리하는 코드를 만날 때까지 계속 올라가거나, 끝까지 그 코드가 없으면 프로그램이 종료된다.

이 코드에서 알 수 있듯이 비검사 예외를 발생시키는 함수를 호출하는 쪽에서는 예외가 발생할 수 있다는 사실을 전혀 몰라도 된다.

이로 인해 비검사 예외는 오류를 암시적을 전달하는 방법이다.

---

### 명시적 방법 : 널값이 가능한 반환 유형

함수에서 널값을 반환하는 것은 특정값을 계산하거나 얻는 것이 불가능함을 나타내기 위한 효과적이고 간단한 방법이다.

사용 중인 언어가 널 안전성을 지원하는 경우 널값이 반환될 수 있다는 것을 호출하는 쪽에서 강제적으로 인자하고, 그에 따라 처리할 수밖에 없다.

따라서 널 안전성을 지원할 때 널 값이 가능한 반환 유형을 사용하는 것은 오류를 전달하기 위한 명시적인 방법이다.

널 안전성을 지원하지 않는 언어를 사용하는 경우 옵셔널 반환 유형을 사용하는 것이 좋다.

**널 값을 이용한 오류 전달**

```java
// 제공되는 값이 음수면 널을 반환한다. // 언제 널값이 반환되는지 설명하는 주석문
Double? getSquareRoot(Double value) { // Double? 에서 ? 는 널값이 반환될 수 있음을 나타낸다.
  if (value < 0.0) {
      return null; // 오류가 발생하면 널 값이 반환된다.
  }
  return Math.sqrt(value);
}
```

널값을 반환할 때의 한 가지 문제점은 오류가 발생한 이유에 대한 정보를 제공하지 않기 때문에 널값이 의미하는 바를 설명하기 위해 주석문이나 문서를 추가해야 한다.

**널값 처리**

널 처리
```java
void displaySquareRoot() {
    Double? squareRoot = getSquareRoot(ui.getInputNumber());
            
  if (squareRoot == null) { // getSquareRoot()이 반환값이 널값인지 확인해야 한다.
    ui.setError("Can`t get square root of a negative number");
  } else {
      ui.setOutput("Square root is: " + squareRoot);
  }
}
```

이 책에서 사용하는 의사코드는 널 안전성을 지원하기 때문에 호출하는 쪽에서 getSquareRoot()에 의해 반환된 값을 사용하기 전에 널인지 반드시 확인해야 한다.

--- 

### 명시적 방법 : 리절트 반환 유형

널값이나 옵셔널 타입을 반환할 때의 문제 중 하나는 오류 정보를 전달할 수 없다는 것이다.

호출자에세 값을 얻을 수 없을을 알릴 뿐만 아니라 값을 얻을 수 없는 이유까지 알려주면 유용하다.

이러한 경우 리절드(result) 유형을 사용하는 것이 적절할 수 있다.

간단한 리절트 유형
```java
class Result<V, E> {
    // 제네릭/템플릿 유형이 사용되기 떄문에 이 클래스에는 어떤 유형의 값이나 오류에 대해서도 사용할 수 있다.
    private final Optional<V> value;
    private final Optional<E> error;

  private Result(Optional<V> value, Optional<E> error) { // 생성자는 프라이빗이기 때문에 호출하는 쪽에서 정적 팩토리 함수를 쓸 수밖에 없다.
      this.value = value;
      this.error = error;
  }
  
  // 정적 팩토리 함수, 이 클래스는 값이나 오류 중 한 가지로만 인스턴스를 만들 수 있고 둘 다 가능한 인스턴스는 생성할 수 없다.

  static Result<V, E> ofValue(V value) {
    return new Result(Optional.of(value), Optional.empty());
  }
  
  static Result<V, E> ofError(E error) {
    return new Result(Optional.empty(), Optional.of(error));
  }
  
  Boolean hasError() {
      return error.isPresent();
  }
  
  V getValue() {
      return value.get();
  }
  
  E getError() {
      return error.get();
  }
}

```

**리절트 유형을 이용한 전달**

```java
class NegativeNumberError extends Error { // 오류의 구체적 유형을 나타내는 클래스
    private final Double erroneousNumber;
    
    NegativeNumberError(Double erroneousNumber) { // 추가 정보, 즉 오류를 유발한 값을 갖는다.
        this.erroneousNumber = erroneousNumber;
    }
    
    Double getErroneousNumber() {
        return erroneousNumber;
    }
}

Result<Double, NegativeNumberError> getSquareRoot(Double value) { // 리절트 유형을 통해 NegativeNumberError가 발생할 수 있다는 것을 알 수 있다.
    if(value < 0.0) {
      return Result.ofError(new NegativeNumberError(value)); // 오류가 발생하면 오류 리절트가 반환된다.
    }
    return Result.ofValue(Math.sqrt(value)); // 함수의 결괏값은 리절트 유형에 들어 있다.
    
}

```

**리절트 처리**

getSquareRoot() 함수를 호출하는 개발자는 이 함수가 반환하는 값이 리절트 유형이라는 것을 명백히 알 수 있다.

오류가 발생했는지 확인하기 위해 먼저 hasError()를 호출해야 하고, 오류가 발생하지 않았다는 것을 확인하고 나면 getValue()를 호출하여 결괏값을 얻을 수 있다는 것을 알 것이다.

오류가 발생한 경우라면 반환된 객체의 getError()를 호출해서 세부 정보를 얻을 수 있다.

```java
void displaySquareRoot() {
  Result<Double, NegativeNumberError> squareRoot = getSquareRoot(ui.getInputNumber());

  if (squareRoot.hasError()) { // squareRoot 결괏값은 오류를 확인해야 한다.
    ui.setError("Can`t get square root of a negative number: " + squareRoot.getError().getErroneousNumber()); // 자세한 오류 정보를 사용자에게 보여준다.
  } else {
    ui.setOutput("Square root is: " + squareRoot.getValue());
  }
}
```
---

### 명시적 방법 : 아웃컴 반환 유형

어떤 함수들은 값을 반환하기 보다는 단지 무언가를 수행하고 값을 반환하지 않는다.

어떤 일을 하는 동안 오류가 발생할 수 있고 그것을 호출한 쪽에 알리고자 한다면, 

함수가 수행한 동작의 결과를 나타내는 값을 반환하도록 함수를 수정하는 것이 한 가지 방법이 될 수 있다.

아웃컴 반환 유형을 반환할 때 호출하는 쪽에서 반환값을 강제적으로 확인해야 한다면 이것은 오류를 알리는 명백한 방법이다.

**아웃컴을 이용한 오류 전달**

아웃컴 반환
```java
Boolean sendMessage(Channel channel, String message) { // 이 함수는 불리언 값을 반환한다.
  if (channel.isOpen()) {
    channel.send(message);
    return true; // 메시지가 전송되면 참을 반환한다.
  }
  return false; // 오류가 발생하면 거짓을 반환한다.
}
```

더 복잡한 시나리오를 구현해야 한다면, 단순한 불리언보다 더 정교한 아웃컴 유형을 사용하는 것이 적절할 수 있다.

가능한 결과 상태가 두 개 이상 있거나, 참과 거짓이 무엇을 의미하는지 분명하지 않은 경우에 열거형을 사용하면 유용하다.


**아웃컴 처리**

아웃컴 처리
```java
void sayHello(Channel channel) {
  if (sendMessage(channel, "hello")) {
    ui.setOutput("Hello sent"); // 성공한 경우 처리
  } else {
    ui.setError("Unable to send hello"); // 실패한 경우 처리
  }
}
```
함수 호출은 if-else 문에 두고 각 분기에 적절한 처리 로직을 두면 된다.


**아웃컴이 무시되지 않도록 보장**

아웃컴 반환 유형에 대한 문제점 중 하나는 호출하는 쪽에서 반환값을 무시하거나 함수가 값을 반환한다는 사실조차 인식 못할 수 있다는 점이다.

이로 인해 아웃컴 반환 유형은 오류를 알리는 명시적 방법으로 한계가 있다.

```java
void sayHello(Channel channel) {
  sendMessage(channel, "hello"); // 성과 반환값이 무시된다.
  ui.setOutput("Hello sent");
}
```

호출하는 쪽에서 함수의 반환값을 무시하면 컴파일러가 경고를 생성하도록 함수를 표시할 수 있다.

- 자바의 CheckReturnValue 애너테이션

CheckReturnValue 애너테이션을 사용
```java
@CheckReturnValue // 호출하는 쪽에서 함수의 반환값을 무시하면 안 된다는 것을 나타낸다.
Boolean sendMessage(Chnnel chnnel, String message) {
    // ...
}
```

반환값의 강제적 확인
```java
void sayHello(Channel channel) {
  if (sendMessage(channel, "hello")) {
    ui.setOutput("Hello sent"); // 성공한 경우 처리
  } else {
    ui.setError("Unable to send hello"); // 실패한 경우 처리
  }
}
```
---

### 암시적 방법 : 프로미스 또는 퓨처

비동기적으로 실행하는 코드를 작성할 때 프로미스(promise)나 퓨처(future)를 반환하는 함수를 작성하는 것이 일반적이다.

프로미스나 퓨처를 사용할 때 일반적으로 오류 처리를 강제로 해야 하는 것은 아니고,

해당 함수에 대한 코드 계약의 세부 조항을 잘 알지 못하면 오류 처리 코드를 추가로 작성해야 한다는 것을 모를 수 있다.

따라서 프로미스나 퓨처를 사용한 오류 전달은 암시적인 방법이다!

> 비동기적?
> 
> 동기적 프로세스 : 작업을 한 번에 하나씩 순차적으로 수행하는 것을 의미
> 
> 비동시적 프로세스 : 다른 작업이 끝나기를 기다리는 동안 다른 작업을 수행할 수 있다는 것을 의미
> 
> 응답을 반환하는 서버처럼 코드가 어떤 일이 일어나기를 기다려야 할 때 비동기적으로 작성하는 것이 일반적이다.
> 
> 서버로 부터 응답을 디라이는 동안 코드는 다른 작업을 수행할 수 있다.


**프로미스를 이용한 전달**

비동기 함수
```java
class NegativeNumberError extends Error { // 특정 유형의 오류임을 나타내는 클래스
    // ...
}

Promise<Double> getSquareRoot(Double value) async { // async는 이 함수가 비동기적임을 표시한다.
    await Timer.wait(Duration.ofSeconds(1)); // 실제 실행하기 전 1초를 기다린다.
  if (value < 0.0) {
    throw new NegativeNumberError(value); // 함수 내에서 오류를 발생하고 프로미스는 거부된다.
  }
  return Math.sqrt(value); // 값을 반환하면 프로미스는 이행된다.
}
```

**프로미스 처리**

프로미스 사용
```java
void displaySquareRoot() {
    getSquareRoot(ui.getInputNumer())
            .then(squareRoot -> ui.setOutput("Square root is: " + squareRoot)) // then() 콜백은 프로미스가 이행되면 호출된다.
            .catch(error -> ui.setError("An error occurred: " + error.toString())); // catch() 콜백은 프로미스가 거부되면 호출된다.
}
```

**왜 프로미스는 암묵적인 오류 전달 기법인가?**

오류가 발생하고 프로미스가 거부될 수 있음을 알려면 프로미스를 생성하는 함수의 세부 조항이나 구현 세부 사항을 확인해야 한다.

이 내용을 모르면 프로미스의 사용자는 잠재적인 오류 상태를 쉽게 알 수 없으며, then() 함수를 통해서만 콜백을 제공할 것이다.

catch() 함수를 통해 콜백이 제공되지 않으면, 오류는 일부 상위 수준의 오류 처리 코드에 의해 포착되거나 완전히 눈에 띄지 않을 수 있다.

프로미스와 퓨처는 비동기 함수로부터 값을 반환하는 훌륭한 방법이다.

그러나 호출하는 쪽에서는 잠재적인 오류 시나리오를 완전히 알지 못하기 때문에 프로미스나 퓨처를 사용하는 것은 오류를 알리는 암시적인 방법이 된다.


**프로미스를 명시적으로 만들기**

프로미스나 퓨처를 반환할 때 명시적 오류 전달 기법으로 사용하려면, 

리절트 유형의 프로미스를 반환하는 것이 한 가지 방법일 수 있다.

다만 코드가 복잡해지기 때문에 모든 사람이 다 이렇게 사용하진 않을 것이다..?

리절트 유형 프로미스
```java
Promise<Result<Double, NegativeNumberError>> getSquareRoot(Double value) async { // 반환 유형이 상당히 복잡하다.
    await Timer.wait(Duration.ofSeconds(1));
  if (value < 0.0) {
    return Result.ofError(new NegativeNumberError(value));
  }
  return Resule.ofValue(Math.sqrt(value));
}
```

---

### 암시적 방법 : 매직값 반환

매직값 magic value은 함수의 정상적인 반환 유형에 적합하지만 특별한 의미를 부여하는 값이다.

매직값이 반환될 수 있다는 것을 알려면 문서나 코드를 읽어야 한다.

따라서 이것은 암시적 오류 전달 기법이다.

매직값을 사용하여 오류를 알리는 일반적인 방법은 -1을 반환하는 것이다.

매직값 반환
```java
// 음숫값이 입력으로 제공되면 -1을 반환한다 => 함수가 -1을 반환할 수 있음을 알리는 주석문
Double getSquareRoot(Double value) { 
    if (value < 0.0) {
        return -1.0; // 오류가 발생하면 -1이 반환된다.
    }
    return Math.sqrt(value);
}
```

매직값은 코드 계약의 명백한 부분을 통해 호출하는 쪽에 알릴 수 없어서 예상을 벗어나는 결과를 가져올 수도 있고 버그로 이어질 수 있다.

매직값은 오류를 알리는 좋은 방법이 아니다!!

---

## 복구할 수 없는 오류의 전달

복구할 가능성이 없는 오류가 발생하면 신속하게 실패하고 요란하게 실패하는 것이 최선의 방법!!

이를 달성하기 위한 방법

1. 비검사 예외를 발생
2. 프로그램이 패닉이 되로록
3. 체크나 어서션의 사용

---

## 호출하는 쪽에서 복구하기를 원할 수도 있는 오류의 전달

> 구현 세부 사항 노출
> 
> 호출하는 쪽에서 복구하기를 원하는 오류에 대한 또 다른 고려사항은 
> 호출하는 쪽에서 전달받은 오류를 처리하기 위해 굳이 호출하는 코드의 구현 세부 사항을 알 필요가 없다는 것이다!


### 명시적 방식을 사용하라 (필자 추천)

호출하는 쪽에서 복구하기를 원할 수도 있는 오류에 대해 비검사 예외를 사용하지 않는 것이 최상이다!

비검사 예외의 사용은 완전히 문서화되는 경우가 매우 드물며, 

해당 함수에 대해 발생 가능한 오류와 이레 대한 처리를 어떻게 해야 하는지 개발자가 확실하게 알기란 거의 불가능하다.

따라서 호출하는 쪽에서 복구하기를 원할 가능성이 있다면 명시적 오류 전달 방식을 사용하는 것이 좋다.

---

## 컴파일러 경고를 무시하지 말라

컴파일러 경고는 어떤 식으로든 코드가 의심스러우면 표시를 하는데, 이것은 버그에 대한 조기 경고일 수 있다.

컴파일러 경고를 중요하지 않을 것으로 생각하고 싶을 수 있다.

하지만 경고는 종종 코드에 문제가 있다는 신호일 수 있으며, 어떤 상황에서는 심각한 버그가 될 수 있다.

모든 이슈는 수정되었거나,정당한 이유가 있는 경우 유효한 설명과 함께 명시적으로 억제하기 때문에 코드를 컴파일할 때 경고가 없는 것이 이상적이다.

---

## 요약

- 오류에는 크게 두 가지 종류가 있다.
  - 시스템이 복구할 수 있는 오류
  - 시스템이 복구할 수 없는 오류

- 해당 코드에 의해 생성된 오류로부터 복구할 수 있는지 여부를 해당 코드를 호출하는 쪽에서만 알 수 있는 경우가 많다.

- 에러가 발생하면 신속하게 실패하는 것이 좋고, 에러를 복구할 수 없는 경우에는 요란하게 실패하는 것이 바람직하다!

- 오류를 숨기는 것은 바람직하지 않을 때가 많으며, 오류가 발생했다는 신호를 보내는 것이 바람직하다.

- 오류 전달 기법은 두 가지 범주로 나눌 수 있다.
  - 명시적 방법 : 코드 계약의 명확한 부분. 호출하는 쪽에서는 오류가 발생할 수 있음을 인지한다.
  - 암시적 방법 : 코드 계약의 세부 조항을 통해 오류에 대한 설명이 제공되거나 전혀 설명이 없을 수도 있다. 오류가 발생할 수 있다는 것을 호출하는 쪽에서 반드시 인지하는 것은 아니다.
  
- 복구할 수 없는 오류에 대해서는 암시적 오류 전달 기법을 사용해야 한다.

- 잠재적으로 복구할 수 있는 오류에 대해서는
  - 명시적 혹은 암시적 기법 중 어느 것을 사용할지에 대해서는 개발자들 사이에서도 일치되는 의견이 없다.
  - 필자의 의견으로는 명시적인 기법이 사용되어야 한다.

- 컴파일러 경고는 종종 코드에 문제가 있을 때 이에 대해 표시해준다. 이 경고에 주의를 기울이는 것이 바람직하다.

