# Chapter 5. 가독성 높은 코드를 작성하라

가독성은 본질적으로 주괸적인 것

가독성의 핵심은 개발자가 코드의 기능을 빠르고 정확하게 이해할 수 있도록 하는 것!

---

## 서술형 명칭 사용

클래스, 함수, 변수와 같은 것들을 고유하게 식별하기 위해 이름이 필요하다.

이름을 붙이는 것은 그것이 설명되는 방식으로 언습함으로써 읽기 쉬운 코드 작성을 위한 기회이기도 하다.


### 서술적이지 않은 이름은 코드를 읽기 어렵게 만든다.

### 주석문으로 서술적인 이름을 대체할 수 없다.

```java
class Team {
    Set<String> playerNames = new Set();
    int score = 0;

    Boolean containsPlayer(String playerName) {
        return playerNames.contains(playerName);
    }
    
    int getScore() {
        return score;
    }
    
}

Int getTeamScoreForPlayer(List<Team> teams, String playerName) {
    for (Team team : teams) {
        if (team.containsPlayer(playerName)) {
            return team.getScore();
        }    
    }
    return null;
}
```

---

## 주석문의 적절한 사용

### 중복된 주석문은 유해할 수 있다.

### 주석문으로 가독성 높은 코드를 대체할 수 없다.

가독성이 좋은 코드 - 코드 자체로 설명되는 코드
```java
String generateId(String[] data) {
    return firstName(data) + "." + lastName(data);
}

String firstName(String[] data) {
    return date[0];
}

String lastName(String[] data) {
    return data[1];
}
```

### 주석문은 코드의 이유를 설명하는 데 유용하다.

다음과 같은 경우에는 주석문을 사용해 코드가 존재하는 이유를 설명하면 좋다.

1. 제품 또는 비즈니스 의사 결정
2. 이상하고 명확하지 않은 버그에 대한 해결책
3. 의존하는 코드의 예상을 벗어나는 동작에 대처

```java
class User {
    private final Int username;
    private final String firstName;
    private final String lastName;
    private final Version signupVersion;
    
    String getUserId() {
        if (signupVersion.isOlderThan("2.0")) {
            // (v2.0 이전에 등록한) 레거시 유저는 이름으로 ID가 부여된다.
            // 자세한 내용은 #4218 이유룰 보라.
            return firstName.toLowerCase() + "." + lastName.toLowerCase();
        }
        //(v2.0 이휴로 등록한) 새 유저는 username으로 ID가 부여된다.
        return username;
    }
}
```

### 주석문은 유용한 상위 수준의 요약 정보를 제공할 수 있다.

---

## 코드 줄 수를 고정하지 말라

코드 줄 수는 우리가 실제로 신경 쓰는 것들을 간접적으로 측정해줄 뿐이다.

대부분의 간접 측정이 그렇듯이 유용한 지침 원칙이긴 하지만 반드시 지켜야 할 엄격한 규칙은 아니라는 점을 기억해야 한다!

### 간결하지만 이해하기 어려운 코드는 피하라

간결하지만 이해하기 어려운 코드
```java
Boolean isIdValid(UInt16 id) {
    return countSetzBits(id & 0x7FFF) % 2 == ((id & 0x8000) >> 15);
}
```

### 더 많은 줄이 필요하더라도 가독성 높은 고드를 작성하라

코드의 양은 더 많지만 가독성은 높은 코드
```java
import java.rmi.server.UID;

Boolean isIdValid(UInt16 id) {
    return extractEncodedParity(id) == calculateParity(getIdValue(id));
}

private final UInt16 PARITY_BIT_INDEX = 15;
private final UInt16 PARITY_BIT_MASK = (1 << PARITY_BIT_INDEX);
private final UInt16 VALUE_BIT_MASK = ~PARITY_BIT_MASK;

private UInt16 getIdValue(UInt16 id) {
    return id & VALUE_BIT_MASK;
}

private UInt16 extractEncodedParity(UInt16 id) {
    return (id & PARITY_BIT_MASK) >> PARITY_BIT_INDEX;
}

// 패리티 비트는 1인 비트의 수가 짝수이면 0이고
// 홀수이면 1이다.
private UInt16 calculateParity(UInt16 value) {
    return constSetBits(value) % 2;
}
```

---

## 일관된 코딩 스타일을 고수하라

### 일관적이지 않은 코딩 스타일은 혼동을 일으킬 수 있다

### 스타일 가이드를 채택하고 따르라

---

## 깊이 중첩된 코드를 피하라

- 함수가 호출되면 그 함수가 실행되는 코드가 하나의 블록이 된다.
- if 문의 조건이 참일 때 실행되는 코드는 하나의 블록이 된다.
- for 루프의 각 반복 시 실행되는 코드는 하나의 블록이 된다.

### 깊이 중첩된 코드는 읽기 어려울 수 있다.

### 중첩을 최소화하기 위한 구조 변경 - 반환문

### 중첩은 너무 많은 일을 한 결과물이다.

### 더 작은 함수로 분리

---

## 함수 호출도 가독성이 있어야 한다.

> 많은 함수 인수
> 
> 함수 호출은 인수의 개수가 늘어나면 이해하기 힘들어진다.
> 함수나 생성자가 많은 수의 매개변수를 가지고 있으면 이것은 보다 근본적인 문제를 나타내는 것일 수 있다.
> 예를 들어 추상화 계층을 적절하게 정의하지 않았거나 코드가 충분히 모듈화되지 않았음을 의미할 수 있다.

### 매개변수는 이해하기 어려울 수 있다.

### 명명된 매개변수 사용

### 서술적 유형 사용

함수 호출에서의 서술적 유형
```java
class MessagePriority {
    // ...
    MessagePriority(Int priority) {
        // ...
    }
    // ...
}

enum RetryPolicy {
    ALLOW_RETRY,
    DISALLOW_RETRY
}

void sendMessage(
        String message,
        MessagePriority priority,
        RetryPolicy retryPolicy) {
    // ...
}

public static void main(String[] args) {
    sendMessage("hello", new MessagePriority(1), RetryPolicy);
}
```

---

## 설명되지 않은 값을 사용하지 말라

### 설명되지 않은 값은 혼란스러울 수 있다.

코드에 설명되지 않은 값이 있으면 (특히 하드코딩 된 값) 혼한을 초래하고 이로 인해 버그가 발생할 수 있다.

그 값이 무엇을 의미하는지를 다른 개발자들에게 명확하게 해주는 것이 중요하다

### 잘 명명된 상수를 사용하라

### 잘 명명된 함수를 사용하라

**상수를 반환하는 공급자 함수**

```java
class Vehicle {
    // ...
    // 차량의 현재 운동에너지를 줄 단위로 반환한다.
    Double getKineticEnergy() {
        return 0.5 * getMassUsTon() * kilogramPerUsTon() * Math.pow(getSpeedMph() * metersPerSecondPerMph(), 2);
    }
    
    private static Double kilogramPerUsTon() {
        return 907.1847;
    }
    
    private static Double metersPerSecondPerMph() {
        return 0.44704;
    }
}
```

**변환을 수행하는 헬퍼 함수**

```java
class Vehicle {
    // ...
    // 차량의 현재 운동에너지를 줄 단위로 반환한다.
    // 헬퍼 함수 호출
    Double getKineticEnergy() {
        return 0.5 * getMassUsTon() * kilogramPerUsTon() * Math.pow(getSpeedMph() * metersPerSecondPerMph(), 2);
    }
    
    // 헬퍼 함수
    private static Double usTonsToKilogramPerUsTon(Double usTons) {
        return usTons * 907.1847;
    }

    // 헬퍼 함수
    private static Double mphToMetersPerSecondPerMph(Double mph) {
        return mph * 0.44704;
    }
}
```

---

## 익명 함수를 적절하게 사용하라

### 익명 함수는 간단한 로직에 좋다.

### 익명 함수는 가독성이 떨어질 수 있다.

### 대신 명명 함수를 사용하라.

### 익명 함수가 길면 문제가 될 수 있다.

### 긴 익명 함수를 여러 개의 명명 함수로 나누라

--- 

## 프로그래밍 언어의 새로운 기능을 적절하게 사용하라

### 새 기능은 코드를 개선할 수 있다.

### 불분명한 기능은 혼동을 일으킬 수 있다.

일반적으로 코드 품질을 거선한다면 언어가 제공하는 기능을 사용하는 것이 바람직하다.

그러나 개선 사항이 적거나 다른 개발자가 그 기능에 익숙하지 않다면 차라리 사용하지 않는 것이 좋을 때도 있다.

### 작업에 가장 적합한 도구를 사용하라.

---

## 요약

- 코드의 가독성이 떨어져서 이해하기 어려울 때 다음과 같은 문제가 발생할 수 있다.
  - 다른 개발자가 코드를 이해하느라 시간을 허비함
  - 버그를 유발하는 오해
  - 잘 작동하던 코드가 다른 개발자가 수정한 후에 작동하지 않음

- 코드의 가독성을 높이다 보면 때로는 코드가 더 장황하게 되고 더 많은 줄을 작성해야 할 수도 있다. 이것은 종종 가치 있는 절충이다.

- 코드의 가독성을 높이다 보면 때로는 코드가 더 장황하게 되고 더 많은 줄을 작성해야 할 수도 있다. 이것은 종종 가치 있는 절충이다.

- 코드의 가독성을 높이려면 다른 개발자의 입장을 공감하고, 그들이 코드를 읽을 때 어떻게 혼란스러워할지를 상상해보는 것이 필요하다.

- 실제 시나리오는 다양하며 보통 그 상황에 해당하는 어려움이 있다. 가독성이 좋은 코드를 작성하려면 어제나 상식을 적용하고 판단력을 사용해야 한다.
