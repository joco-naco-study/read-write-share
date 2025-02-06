# Chapter 11: 단위 테스트의 실제

- 코드의 문제를 정확히 감지한다.
- 구현 세부 정보에 구애받지 않는다.
- 실패는 잘 설명된다.
- 테스트 코드가 이해하기 쉽다.
- 테스트를 쉽고 빠르게 실행할 수 있다.

테스트 코드가 위의 특징을 나타낼 가능성을 극대화하기 위해 적용할 수 있는 실제적인 방법이 존재한다.

---

# 11.1 기능뿐만 아니라 동작을 시험하라

테스트 대상 코드가 수행하는 작업으 여러 가지가 있으며, 이 각각의 작업에 대해 테스트 케이스를 따로 작성해야 한다.

이때, 함수별로 테스트 케이스를 하나만 작성하면 중요한 동작을 놓칠 수 있다.

- 👉🏻 함수가 수행하는 모든 동작으로 테스트 목록을 채우는 것이 좋다.

## 11.1.1 함수당 하나의 테스트 케이스만 있으면 적절하지 않을 때가 많다

## 11.1.2 해결책: 각 동작을 테스트하는 데 집중하라

함수와 동작 사이에 일대일로 연결이 안 되는 경우가 많다. _(작성해야 할 테스트 케이스가 훨씬 많다)_

만약 테스트 코드의 양이 실제 코드의 양보다 많지 않다면, 모든 동작이 제대로 테스트되고 있지 않음을 나타내는 경고일 수 있다.

```
테스트를 위해 코드가 보일 행동을 생각해내는 연습을 하는 것은, 코드의 잠재적인 문제를 발견하는 좋은 방법이다.
```

### 모든 동작이 테스트되었는지 거듭 확인하라

코드가 제대로 테스트 되는지 측정하기 위한 좋은 방법은, 수정된 코드에 버그나 오류가 있음에도 **여전히 테스트를 통과할 수 있는지**에 대해 생각하는 것이다.

만약 아래 항목 중 어느 하나라도 대답이 **_'예'_** 라면, **모든 행동이 테스트되지 못하고 있다**는 것을 의미한다.

- 삭제해도 여전히 컴파일되거나 테스트가 통과하는 코드 라인이 있는가?
- if문의 참 거짓 논리를 반대로 해도 테스트가 통과하는가?
- 논리 연산자나 산술 연산자를 다른 것으로 대체해도 테스트가 통과하는가?
- 상숫값이나 하드 코딩된 값을 변경해도 테스트가 통과하는가?

```
요점은 코드의 각 줄은 그것이 존재하는 이유가 있어야 한다는 것이다.

불필요한 코드라면 그것은 제거되어야 한다.
```

`mutation test`를 사용하면, 기능의 변화가 테스트 실패를 초래하는지 확인하는 것을 어느정도 자동화할 수 있다.

### 오류 시나리오를 잊지 말라

코드가 오류 시나리오를 포함하고 있다면, 이 또한 테스트 해야한다.

오류 시나리오에서 오류가 발생하는지 확인할 뿐만 아니라, 예상한 `오류 메시지`가 포함되어 있는지도 확인해야 한다.

---

# 11.2 테스트만을 위해 퍼블릭으로 만들지 말라

private 함수는 특정 시나리오 내부에 포함되는 `구현 세부사항`에 해당한다.

- 이를 public 함수로 전환하여 테스트 코드에 포함한다면, 결국 **구현 세부사항과 밀접하게 연관된 테스트**가 만들어질 것이다.

## 11.2.1 프라이빗 함수를 테스트하는 것은 바람직하지 않을 때가 많다

개발자가 엉뚱한 것을 테스트하게 되는 일반적인 방법 중 하나는, 원하는 최종 결과와 중간 구현 세부 사항을 섞는 것이다.

- 예를 들어, 실제로 우리가 신경써야 하는 `테스트 결과`보다 `과정`에 집중하다보면 private 함수를 public으로 전환하는 실수를 범하기도 한다.

이처럼 private 함수를 public으로 전환하는 것은 테스트가 **구현 세부 사항에 의존하게** 만든다.  
그뿐만 아니라, 퍼블릭 API를 변경한 효과를 갖게 되어 해당 클래스에 의존하는 **외부 객체가 이를 오용할 가능성도 높아진다.**

좋은 단위 테스트는 궁극적으로 **중요한 행동을 테스트해야 한다.**

- 테스트는 코드의 문제점을 정확히 감지할 가능성을 극대화 한다.
- 구현 세부 사항에 독립적이게 된다.

```
[ private 함수를 테스트하는 것을 피하는 방법 ]

1. 퍼블릭 API를 통해 테스트하라
2. 코드를 적절한 추상화 계층으로 나눠라
```

## 11.2.2 해결책: 퍼블릭 API를 통해 테스트하라

```
테스트를 위해 private 함수를 public으로 만들어 외부로 보이게 하는 것은, 대부분 구현 세부 사항을 테스트한다는 것을 보여주는 경고이다.

하지만 일부 중요한 행동(ex. 부수 효과)은 퍼블릭 API의 범위를 벗어날 수 있다.
👉🏻 그럼에도 불구하고 중요하고 궁극적으로 신경 써야하는 것이라면, 퍼블릭 API 여부와 상관 없이 테스트되어야 한다.
```

퍼블릭 API를 통해 모든 동작을 테스트하는 것이 까다로울 수 있다.  
👉🏻 이 경우는 **코드의 추상화 계층이 너무 크다**는 것을 의미하기 때문에, `코드를 더 작은 단위로 분할하는 것`이 유익하다.

## 11.2.3 해결책: 코드를 더 작은 단위로 분할하라

`private` 함수가 복잡한 논리를 갖게 되면, 이 함수를 `public`으로 만들어 테스트하고자 하는 마음이 들 수도 있다.

고객의 신용등급을 확인하는 논리는 다음과 같이 복잡하게 구성된다.

- `MorbageAssessor` 클래스는 `CreditScoreService`에 의존
- 고객의 신용 점수를 조회하기 위해 고객 ID로 `CreditScoreService`에 질의
- `CreditScoreService`에 대한 호출이 실패할 수 있으므로 오류 시나리오를 처리
- 호출이 성공하면 반환된 점수를 기준값과 비교하여 고객의 신용 등급이 양호한지 결정

```java
class MortgageAssessor {
    private const Double MORTGAGE_MULTIPLIER = 10.0;
    private const Double GOOD_GREDIT_THRESHOLD = 880.0;

    private final CreditScoreService creditScoreService;
    ...

    MortageDecision assess(Customer customer) {
        ...
    }

    private Result<Boolean, Error> isEligibleForMortgage(Customer customer) {
        if (customer.hasExistingMortgage() || customer.isBanned()) {
            return Result.ofValue(false);
        }
        return isCreditRatingGood(customer.getId());
    }

    /** 태스트를 위해서만 공개 */
    public Result<Boolean, Error> isCreditRatingGood(Integer customerId) {
        CreditScoreResponse response = creditScoreService.query(customerId);

        if (response.errorOccured()) {
            return Result.ofError(response.getError());
        }
        return Result.ofValue(
            response.getCreditScore() >= GOOD_CREDIT_SCORE_THRESHOLD
        );
    }

    ...
}
```

2장에서 하나의 클래스가 너무 많은 여러 개념을 다루지 않도록 하는 것이 바람직하다는 점을 살펴봤다.

- `MorbageAssessor` 클래스는 너무 많은 개념을 포함하고 있다.
  - 👉🏻 퍼블릭 API로 모든 것을 완벽하게 테스트하는 것이 어려워 보이는 진짜 이유

코드를 더 작은 계층으로 나누기 위해, 아래와 같이 신용등급이 좋은지 판단하는 논리를 **별도의 클래스로** 옮긴다.

```java
class CreditRatingChecker {
    private const Double GOOD_GREDIT_THRESHOLD = 880.0;

    private final CreditScoreService creditScoreService;
    ...

    public Result<Boolean, Error> isCreditRatingGood(Integer customerId) {
        CreditScoreResponse response = creditScoreService.query(customerId);

        if (response.errorOccured()) {
            return Result.ofError(response.getError());
        }
        return Result.ofValue(
            response.getCreditScore() >= GOOD_CREDIT_SCORE_THRESHOLD
        );
    }
}

class MortgageAssessor {
    private const Double MORTGAGE_MULTIPLIER = 10.0;

    private final CreditRatingChecker creditRatingChecker;
    ...

    MortageDecision assess(Customer customer) {
        ...
    }

    private Result<Boolean, Error> isEligibleForMortgage(Customer customer) {
        if (customer.hasExistingMortgage() || customer.isBanned()) {
            return Result.ofValue(false);
        }
        return creditRatingChecker.isCreditRatingGood(customer.getId());
    }
}
```

> `MortgageAssessor` 클래스는 하위 문제를 해결하기 위한 모든 논리를 더 이상 포함하지 않으므로 매우 단순해졌다.

---

# 11.3 한 번에 하나의 동작만 테스트하라

## 11.3.1 여러 동작을 한꺼번에 테스트하면 테스트가 제대로 안 될 수 있다

아래 함수는 유효한 쿠폰만 추려내는 코드이다.

```java
// 유효한 쿠폰을 추려내기 위한 코드
public List<Coupon> getValidCoupons(
    List<Coupon> coupons, Customer customer
) {
    return coupons
        .filter(coupon -> !coupon.alreadyRedeemed())
        .filter(coupon -> !coupon.hasExpired())
        .filter(coupon -> coupon.issuedTo() == customer)
        .filter(coupon -> coupon.getValue(), SortOrder.DESCENDING);
}
```

위 코드의 동작을 테스트하기 위한 한 가지 방법은, **하나의 테스트 케이스에 모든 동작을 테스트**하는 것이다.

- 하지만, 하나의 테스트 케이스로 모든 행동을 테스트하게 되면, 테스트 코드는 이해하기 어려워진다. _(교재 363쪽 참고)_

## 11.3.2 해결책: 각 동작은 자체 테스트 케이스에서 테스트하라

더 나은 접근 법은 잘 명명된 테스트 케이스를 사용하여, 각 동작을 개별적으로 테스트하는 것이다.

- 즉, **한 번에 하나의 케이스만** 테스트하는 것이다.

```java
// 유효한 쿠폰만 반환하는지
void testGetValidCoupons_validCoupon_included() {...}

// 이미 사용한 쿠폰은 제외하는지
void testGetValidCoupons_alreadyRedeemed_excluded() {...}

// 유효기간이 지난 쿠폰은 제외하는지
void testGetValidCoupons_expired_excluded() {...}

// 다른 고객에게 발행한 쿠폰은 제외하는지
void testGetValidCoupons_issuedToDifferentCustomer_excluded() {...}

// 내림차순으로 정렬되는지
void testGetValidCoupons_returnedInDescendingValueOrder() {...}
```

> 테스트가 실패할 경우, 어떤 동작이 실패했는지 잘 알 수 있게 된다.

하지만 이 경우 코드 중복이 많아지는 단점도 존재한다.

이처럼 설정 값의 차이를 제외하고 코드가 거의 동일한 경우, **매개변수화된 테스트**를 사용할 수 있다.

## 11.3.3 매개변수를 사용한 테스트

프레임워크는 매개변수를 사용하는 테스트 기능을 제공한다.

- 예시로 JUnit의 `Parameterized test`가 있다.

---

# 공유 설정을 적절하게 사용하라

**의존성**을 설정하거나, **테스트 데이터 저장소**에 값을 채우거나, 다른 종류의 **상태를 초기화**하는 등 어느정도의 `설정`이 필요할 때가 있다.

많은 프레임워크에서는 테스트 케이스 간에 **설정을 쉽게 공유**할 수 있는 기능을 제공한다.

- `BeforeAll`
  - 모든 테스트 케이스가 실행되기 전, 단 한 번만 실행된다.
- `BeforeEach`
  - 각 테스트 케이스가 실행되기 전, 매번 실행된다.
- `AfterAll`
  - 모든 테스트 케이스가 실행된 후, 단 한 번 실행된다.
- `AfterEach`
  - 각 테스트 케이스가 실행된 후, 매번 실행된다.

이러한 설정 코드 블록을 사용하면, 설정을 **서로 다른 테스트 케이스 간에 공유**할 수 있다.

- 상태 공유 _(`BeforeAll`)_
  - 설정된 모든 상태가 모든 테스트 케이스 간에 공유된다.
  - 한 테스트 케이스의 실행 결과가 다른 테스트 케이스에 악영향을 미칠 수 있다.
- 설정 공유 _(`BeforeEach`)_
  - 테스트 케이스는 이 코드에 의한 모든 설정을 공유한다.
  - 테스트 케이스 간에 공유되는 상태는 없다.

## 11.4.1 상태 공유는 문제가 될 수 있다

한 테스트 케이스가 수행하는 모든 조치는 다른 테스트 케이스의 결과에 영향을 미치지 않아야 한다.

- 서로 다른 테스트 케이스 간에 `가변적인 상태`를 공유하면 문제가 발생하기 쉽다.

## 11.4.2 해결책: 상태를 공유하지 않거나 초기화하라

가장 좋은 해결책은 상태를 공유하지 않는 것이다.

- `BeforeEach`를 사용하여 각 테스트 케이스에 대해 **새 인스턴스를 생성**하는 것도 고려할 수 있다.
- 이 과정에서 `페이크`를 활용하면, 각 테스트 케이스에 대해 `새로운 페이크 인스턴스`를 사용할 수 있다.

만약 페이크를 사용할 수 없는 경우, 데이터베이스 인스턴스를 테스트 케이스 간에 공유하는 것을 피할 수 없다.

- 이 경우, 각 테스트 케이스 간에 반드시 상태를 초기화해야한다.
- `AfterEach`를 사용하여, 다음 테스트 케이스 실행 전에 반드시 상태가 초기화 되는 것을 보장해야 한다.

## 11.4.3 설정 공유는 문제가 될 수 있다

`테스트 결과가 설정 값에 의존`하는 경우에는 설정 공유가 문제가 될 수 있다.

- 이때 새로운 테스트 케이스가 추가되면서 설정 값이 변하는 경우, 다른 테스트 케이스에 영향을 줄 수 있다.
- 일반적으로 **테스트 케이스에 중요한 값**이나 **상태**는 **_공유하지 않는 것_** 이 최선이다.

## 11.4.4 해결책: 중요한 설정은 테스트 케이스 내에서 정의하라

앞선 내용처럼, 테스트 케이스에 중요한 설정 값은 서로 공유하기보단 **테스트 케이스 내부에서 직접 설정**해주는 것이 좋다.

- 👉🏻 이때, **인스턴스 생성**을 돕는 `헬퍼 함수`를 활용하면 코드의 중복도 줄일 수 있다.

## 11.4.5 설정 공유가 적절한 경우

테스트 케이스의 결과에 직접적인 영향을 미치지 않는 설정이 있는 경우

```
⭐️ 코드 동작에는 별로 관련 없는 설정을 해야한다면,
함수(or 생성자) 매개변수의 목적이 명확하지 않다는 신호일 수도 있다.
```

---

# 11.5 적절한 어서션 확인자를 사용하라

어서션 확인자는 `테스트 실패 이유`를 설명하는 메시지를 생성한다.

- 가장 적절한 어서션 확인자를 선택하는 것이 중요한 이유

## 11.5.1 부적합한 확인자는 테스트 실패를 잘 설명하지 못할 수 있다

## 11.5.2 해결책: 적절한 확인자를 사용하라

_ex. 리스트가 순서에 관련 없이 특정 항목을 포함하고 있는지 검증하고자 하는 경우_

적절한 확인자를 사용하면 테스트 코드 이해가 조금 더 쉬워진다.

```java
assertThat(someList).contains("expected value");
assertThat(someList.contains("expected value")).isTrue();
```

> 👉🏻 테스트가 어떻게 실패할지에 대해 생각해보는 것이 중요하다.

---

# 11.6 테스트 용이성을 위해 의존성 주입을 사용하라

`의존성 주입`을 사용하면 코드의 **테스트 용이성**이 크게 향상된다.

앞서 살펴봤듯, 테스트 코드가 의존성의 특정 인스턴스를 제공해야 하는 경우가 있다.

## 11.6.1 하드 코딩된 의존성은 테스트를 불가능하게 할 수 있다

예를들어 데이터베이스에 연결하는 인스턴스나 이메일 전송에 필요한 작업을 처리하는 인스턴스를 `생성자 내부에서 직접 생성`하는 경우이다.

- 이 경우에는 실제 서비스에 영향을 주는 서버와 연결을 한 상태에서 테스트를 수행하므로, **독립적인 테스트를 수행하기 어려울 것**이다.

## 11.6.2 해결책: 의존성 주입을 사용하라

`의존성 주입`을 활용하면 앞서 살펴본 `페이크 객체`를 손쉽게 생성할 수 있다.

- 즉, 독립적인 테스트를 수행할 수 있다.

```
[ 테스트 용이성 ]

테스트 용이성은 모듈화와 밀접한 관련이 있다.

서로 다른 코드가 느슨하게 결합하고 재설정이 가능하면, 테스트는 훨씬 더 쉬워지는 경향을 띤다.
```

---

# 11.7 테스트에 대한 몇 가지 결론

- **통합 테스트**
  - `구성 요소`와 `하위 시스템`을 **서로 연결하는 프로세스가 제대로 작동**하는지 확인하기 위한 테스트
- **종단 간 테스트**
  - 처음부터 끝까지 전체 소프트웨어 시스템을 통과하는 여정(or 작업 흐름)을 테스트
    - _ex. 온라인 쇼핑몰 - 웹 브라우저를 자동으로 구동하고, 사용자가 구매를 완료하는 과정까지_

단위 테스트만으로는 테스트의 모든 요구 사항을 충족할 수 없기 때문에, 다양한 테스트 유형과 수준에 대해 알아보고 새로운 툴과 기술에 대한 최신 정보를 유지하는 것이 좋다.
