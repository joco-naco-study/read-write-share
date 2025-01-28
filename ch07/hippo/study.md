# Chapter 07. 코드를 오용하기 어렵게 만들라

**코드를 잘못 사용할 수 있는 일반적인 경우**

- 호출하는 쪽에서 잘못된 입력을 제공
- 다른 코드의 부수 효과 (입력 매개변수 수정 등)
- 정확한 시간이나 순서에 따라 함수를 호출하지 않음
- 관련 코드에서 가정과 맞지 않게 수정이 이루어짐

---

## 불변 객체로 만드는 것을 고려하라

**가변 객체가 일으킬 수 있는 문제?**

- 설정 함수를 갖는 가변 클래스에서 잘못된 설정이 쉽게 이루어지게 되면 잘못된 상태를 가지게 되는 문제
- 입력 매개변수를 변경하는 함수가 어떻게 예상을 벗어나는 동작을 초래하는지
- 가변 객체는 추론하기 어렵다.
  - 객체가 불변이라면, 그것은 마치 누구도 해제할 수 없는 변징 방지 봉인을 붙여 놓은 것과 같다!!
- 가변 객체는 다중 스레드에서 문제가 발생할 수 있다.
  - 한 스레드가 객체를 읽는 동안 다른 스레드가 그 객체를 수정하는 경우 오류가 발생할 수 있다.
  - 예를 들어 한 스레드가 리스트에서 마지막 요소를 제거하는 동안 다른 스레드가 그 요소를 읽으려는 경우다.

객체를 불변으로 만드는 것이 항상 가능하지는 않고, 또 항상 적절한 것도 아니다.
기본적으로는 불변적인 객체를 만들되 필요한 곳에서만 가변적이 되도록 하는 것이 바람직하다!

### 가변 클래스는 오용하기 쉽다.

### 해결책 : 객체를 생성할 때만 값을 할당하라

모든 값이 객체의 생성 시에 제공되고 그 이후로는 변경할 수 없도록 함으로써 클래스를 불변적으로 만들 수 있고 오용도 방지할 수 있다.

불변 TextOptions 클래스
```java
class TextOptions {
  
    // 멤버 변수는 final로 표시된다.
    private final Font font;
    private final Double fontSize;

  TextOptions(Font font, Double fontSize) {
      // 멤버 변수는 인스턴스 생성 시에만 설정된다.
      this.font = font;
      this.fontSize = fontSize;
  }
    
  Font getFont() {
      return font;
  }
  
  Double getFontSize() {
      return fontSize;
  }
}
```

### 해결책 : 불변성에 대한 디자인 패턴을 사용하라

클래스에서 세터 함수를 제거하고 멤버 변수를 파이널로 표시하면 클래스가 불변적이 되고 버그를 방지할 수 있다.

그러나 방금 지적한 바와 같이 이렇게 하면 클래스가 별로 쓸모없어질 수 있다.

일부 값이 반드시 필요하지 않거나 불변적인 클래스의 가변적 버전을 만들어야 하는 경우,

클래스를 보다 다용도로 구현해야 할 필요가 있다.

- 빌더 패턴
- 쓰기 시 복사 패턴

---

### 빌더 패턴

클래스를 구성할 수 있는 일부 값이 선택 사항인 경우 생성자를 통해 해당 값을 모두 설정하는 것은 까다롭다.

이런 경우 세터 함수를 추가하여 클래스를 변경할 수 있도록 만드는 것도가는 빌더 패턴을 사용하는 것이 더 낫다.

**빌더 패턴은 한 클래스를 두 개로 나누는 효과를 갖는다!**

- 값을 하나씩 설정할 수 있는 빌더 클래스
- 빌더에 의해 작덩된 불변적인 읽기 전용 클래스

빌더 패턴
```java
class TextOptions {
    private final Font font;
    private final Double? fontSize;

  TextOptions(Font font, Double?fontSize) {
      this.font = font;
      this.fontSize = Size;
  }
  
  // TextOptions 클래스는 읽기 전용 게터 함수만 갖는다.
  Font getFont() {
      return font;
  }
  
  Double? getFontSize() {
      return fontSize;
  }
}

class TextOptionsBuilder {
    private final Font font;
    private Double? fontSize;
    
    // 빌더는 생성자를 통해 필수 값을 받는다.
    TextOptionsBuilder(Font font) {
      this.font = font;
    }

    // 빌더는 세터 함수를 통해 필수적이지 않은 값을 받는다.
    TextOptionsBuilder setFontSize(Double fontSize) {
      this.fontSize = fontSize;
      // 세터 함수는 함수 호출을 연이어 할 수 있도록 this를 반환한다.
      return this;
    }
  
    TextOptions build() {
        // 모든 값이 정해지고 나면 호출하는 쪽에서는
        // TextOptions 객체를 얻기 위해 build를 호출한다.
        return new TextOptions(font, setFontSize());
    }
}
```

빌더 패턴은 클래스를 둘로 나누는 효과를 갖는다.

값을 설정하기 위해 빌더 클래스는 가변적이다. 

그 다음 build() 함수를 호출하면 클래스의 불변적 인스턴스를 반환하는데 여기에는 설정된 값이 들어 있다.

빌더 패턴은 값의 일부(또는 전체)가 선택 사항일 때 불변적 객체를 만드는 매우 유용한 방법이다.

---

### 쓰기 시 복사 패턴

클래스의 인스턴스를 변경해야 하는 경우도 잇다.

쓰기 시 복사 패턴
```java
class TextOptions {
  private final Font font;
  private final Double?fontSize;

  TextOptions(Font font) { // 필수 값을 받는 퍼블릭 생성자
    this(font, null); // 프라이빗 생성자 호출
  }

  private TextOptions(Font font, Double?fontSize) { // 모든 값을 받는 프라이빗 생성자
    this.font = font;
    this.fontSize = fontSize;
  }
  
  Font getFont() {
    return font;
  }
  
  Double? getFontSize() {
      return fontSize;
  }

  TextOptions withFont(Font newFont) { // 폰트만 변경된 TextOptions 객체 반환
    return new TextOptions(newFont, fontSize);
  }

  TextOptions withFontSize(Double newFontSize) { // 폰트 크기만 변경된 TextOptions 객체 반환
    return new TextOptions(font, newFontSize);
  }
}
```

쓰기 시 복사 패턴의 경우 값을 변경하면 클래스의 새 인스턴스가 생성되며, 이 새로운 인스턴스에는 원하는 변경 사항이 반영되지만 기존 인스턴스는 수정되지 않는다.

---

클래스를 변경할 수 없게 하는 것은 클래스가 오용될 가능성을 최소화하는 좋은 방법이다!

---

## 객체를 깊은 수준까지 불변적으로 만드는 것을 고려하라

클래스가 실수로 가변적으로 될 수 있는 일반적인 경우는 깊은 가변성(deep mutability) 때문이다.

---

### 깊은 가변성은 오용을 초래할 수 있다

깊은 가변성을 갖는 클래스
```java
class TextOptions {
  private final List<Font> fontFamily; // fontFamily는 여러 폰트를 가지고 있는 리스트다.
  private final Double fontSize;

  TextOptions(List<Font> fontFamily, Double fontSize) {
      this.fontFamily = fontFamily;
      this.fontSize = fontSize;
  }

  List<Font> getFontFamily() {
      return fontFamily;
  }
  
  Double getFontSize() {
      return fontSize;
  }
}
```

객체는 종종 참조를 통해 값을 갖는데,

이는 코드의 여러 부분에서 모두 동일한 객체를 참조할 수 있음을 의미한다.

**이는 깊은 가변성의 원인이 될 수 있다.**

---

생성된 후 수정되는 리스트
```java
public static void main(String[] args) {

  List<Font> fontFamily = [Font.ARIAL, Font,VERDANA]

  TextOptions textOptions = new TextOptions(fontFamily, 12.0); // fontFamily 리스트에 대한 참조가 TextOptions 생성자에 전달된다.
  
  // fontFamily 리스트가 수정된다. 이것은 textOptions가 참조하는 리스트와 동일한 리스트다.
  fontFamily.clear();
  fontFamily.add(Font.COMIC_SANS);
}
```
호출하는 쪽에 의해 수정된 리스트
```java
public static void main(String[] args) {
  TextOptions textOptions = new TextOptions([Font.ARIAL, Font.VERDANA], 12.0);
  
  List<Font> fontFamily  = textOptions.getFontFamily(); // textOptions가 참조하는 리스트와 동일한 리스트에 대한 참조를 받는다.
  fontFamily.clear(); // textOptions가 참조하는 리스트와 동일한 리스트를 수정한다.
  fontFamily.add(Font.COMIC_SANS);
}
```

위와 같은 방식으로 코드를 변경할 수 있게 되면 코드를 오용하기 쉬워진다.

개발자가 textOptions.getFontFamily()와 같은 함수를 호출할 때 리스트가 다른 함수나 생성자가 호출됨에 따라 여러 번 전달될 수 있다.

그것이 어디서 왔는지 그리고 그것을 수정하는 것이 안전한지 추적하기 쉽지 않다.

---

### 해결책 : 방어적으로 복사하라

클래스가 참조하는 객체가 클래스 외부의 코드에서는 참조할 수 없도록 하여 문제를 해결하자!

```java
import java.awt.*;

class TextOptions {
  private final List<Font> fontFamily; // 이 클래스만이 참조를 가지고 있는 fontFamily의 복사본
  private final Double fontSize;

  TextOptions(List<Font> fontFamily, Double fontSize) {
    this.fontFamily = List.copyOf(fontFamily); // 생성자는 리스트를 복사하고 그 복사본에 대한 참조를 갖는다.
    this.fontSize = fontSize;
  }
  
  List<Font> getFontFamily() {
    return List.copyOf(fontFamily); // 리스트의 복사본을 반환한다.
  }
  
  Double getFontSize() {
      return fontSize;
  }
}
```

방어적 복사의 단점

- 복사하는 데 비용이 많이 들 수 있다.
- 클래스 내부에서 발생하는 변경을 막아주지 못하는 경우가 많다.

---

### 해결책 : 불변적 자료구조를 사용하라

불변적 자료구조를 이용하면 코드의 어디선가 동일한 참조가 있는지는 중요하지 않고 어떤 것도 방어적으로 복사할 필요가 없다!

```java
class TextOptions {
    private final ImmutableList<Font> fontFamily; // 심지어 클래스 내에서도 ImmutableList의 내용을 변경할 수 없다.
    private final Double fontSize;
    
    TextOptions(ImmutableList<Font> fontFamily, Double fontSize) { // 생성자를 호출하는 쪽에서 나중에 리스트를 변경할 수 있는 방법이 없다.
        this.fontFamily = fontFamily;
        this.fontSize = fontSize;
    }
    
    ImmutableList<Font> getFontFamily() { // 호출하는 쪽에서 수정할 수 없다는 것을 알고 있기 때문에 ImmutableList는 아무런 문제없이 반환된다.
        return fontFamily;
    }
    
    Double getFontSize() {
        return fontSize;
    }
}
```

불변적인 자료구조를 사용하는 것은 클래스가 깊은 불변성을 갖도록 보장하기 위한 좋은 방법 중 하나다.

방어적으로 복사해야 하는 단점을 피하고 실수로라도 클래스내의 코드에서 변경되지 않도록 보장한다.

---

## 지나치게 일반적인 데이터 유형은 피하라

### 지나치게 일반적인 유형은 오용될 수 있다

지나치게 일반적인 데이터 유형
```java
class LocationDisplay {
    private final DrawbleMap map;

  /*
   * 지도 위에 제공된 모든 좌표의 위치를 표시한다.
   *
   * 리스트의 리스트를 받아들이는데, 내부의 리스트는 정확히
   * 두 개의 값을 가지고 있다. 첫 번째 값은 위치의 위도이고
   * 두 번째 값은 경도를 나타낸다(둘 다 각도 값) // => 입력 매개변수를 설명하기 위해 약간 복잡한 문적 ㅏ필요하다.
   * */
  void markLocationOnMap(List<List<Double>> locations) {
      for (List<Double> location : locations) {
          map.markLocation(location[0, location[1]]); // 내부에 있는 각각의 리스트로부터 첫 번째와 두 번째 항목을 읽는다.
    }
  }
}
```

**위 코드의 문제점**

- 유형 자체로는 아무것도 설명해주지 않는다.
- 개발자는 리스트에서 어떤 항목이 위도와 경도인지 혼동하기 쉽다.
- 형식 안전성이 거의 없다.
  - 컴파일러가 목록 내에 몇 개의 요소가 있는지 보장할 수 없다.
  - 내부 리스트에 들어 있는 항목의 수가 잘못될 수 있다.
  
---

### 페어 유형은 오용하기 쉽다.

페어 데이터 유형은 요점은 동일하거나 다른 종류의 값을 두 개 저장한다는 것이다.

페어 데이터 유형
```java
class Pair<A, B> {
    private final A first;
    private final B second;

  Pair(A first, B second) {
      this.first = first;
      this.second = second;
  }
  
  A getFirst() {
      return first;
  }
  
  B getSecond() {
      return second;
  }
}
```

위치를 위해 페어 유형 사용
```java
class LocationDisplay {
    private final DrawableMap map;

  /*
   * 지도 위에 제공된 좌표의 모든 위치를 표시한다.
   *
   * 페어의 리스트를 받아들이는데, 각 페어는 하나의 위치를 나타낸다.
   * 페어에서 첫 번째 값은 위도이고, 두 번째 값은 경도를 나타낸다.
   * (둘 다 각도 값)
   * */
  void markLocationOnMap(List<Pair<Double, Double>> locations) {
    for (Pair<Double, Double> location : locations) {
        map.markLocation(
                location.getFirst(),
                location.getSecond()
        );
    }
  }
}
```

페어는 정확히 두 개의 값을 포함해야 하므로 호출하는 쪽에서 실수로 너무 적거나 많은 값을 제공하는 것을 방지한다.

그러나 여전히 해결되지 않는 문제들이 있다.

- List<Pair<Double,Double>>가 무슨 의미인지 여전히 이해하기 어렵다.
- 개발자는 여전히 위도와 경도의 순서에 대해 혼동하기 쉽다.

---

### 해결책 : 전용 유형 사용

LatLong 클래스
```java

/*
 * 위도와 경도를 각도로 나타낸다.
 * */

class LatLong {
    private final Double latitude;
    private final Double longitude;

  LatLong(Double latitude, Double longitude) {
      this.latitude = latitude;
      this.longitude = longitude;
  }
  
  Double getLatitude() {
      return latitude;
  }
  
  Double getLongitude() {
      return longitude;
  }
}
```

LatLong 사용
```java
class LocationDisplay {
    private final DrawableMap map;

  /*
   * 지도 위에 제공된 좌표의 모든 위치를 표시한다.
   * */
  void markLocationOnMap(List<LatLong> locations) {
    for (Pair<Double, Double> location : locations) {
        map.markLocation(
                location.getLatitude(),
                location.getLongitude()
        );
    }
  }
}
```

LatLong 클래스를 사용함으로써 의미가 자명하기 때문에 복잡한 입력 매개변수를 설명하기 위한 문서가 필요하지 않다.

또한 유형 안전성이 높아졌고 위도와 경도를 혼동할 일이 없다.

---

### 데이터 객체

객체 지향 프로그래밍에 대한 전통적인 관점을 지지하는 사람들은 때때로 데이터 전용 객체를 정의하는 것을 잘못된 관행으로 가눚한다.

이들은 데이터와 데이터에 대한 기능이 모두 동일한 클래스에 캡슐화되어야 한다고 주장한다.

데이터가 어떤 기능과 밀접하게 결합되어 있는 경우 이 주장은 상당히 타당하다.

그러나 특정 기능에 데이터를 연결하지 않고도 데이터를 그룹화하는 것이 유용한 상황이 있다.

이 경우에는 데이터 전용 객체가 매우 유용할 수 있다.

---

## 시간 처리
  
### 정수로 시간을 나타내는 것은 문제가 될 수 있다.

정수는 매우 일반적인 유형이기 때문에 시잔을 나타내는 데 사용하는 경우 코드가 오용되기 쉽다.

**한순간의 시간인가 아니면 시간의 양인가?**
```java
/*
 * @param message 보낼 메시지
 * @param deadline 데드라인은 초 단위다.
 *      데드라인이 경과하기까지 메시지가 전송
 *      되지 않으면 전송은 취소된다.
 * @return 메시지가 전송되면 참을 그렇지 않으면 거짓
 */
Boolean sendMessage(String message, Int64 deadline) {
    // ...
}
```

정수 유형은 값이 어떤 간위에 있는지 나타내는 데 전혀 도움이 되지 않는다.
함수 이름, 매개변수 이름, 주석문을 사용하여 단위를 나타낼 수 있지만, 여전히 코드를 오용하기가 상대적으로 쉽다.

**시간 단위의 불일치**
```java
class UiSettings {
    // ...
  /*
   * @return UI 메시지를 보여주는 초 단위 기간 // 이 부분은 초를 사용한다.
   */
  Int64 getMessageTimeout() {
      return 5;
  }
}

// ...

/*
 * @param message  보여줄 메시지
 * @param timeoutMs 메시지를 보여주는 밀리초 단위의 기간
 * */
void showMessage(String message, Int64 timeoutMs) {
    // ...
}
```

**시간대 처리 오류**

사용자가 날짜를 입력하고 이를 로컬 표준시 내의 날짜 및 시간으로 해석하면 다른 표준시 사용자가 정보에 액세스할 때 다른 날짜가 표시될 수 있다.


### 해결책 : 적절한 자료구조를 사용하라

대부분은 타사 혹은 제3자 오픈소스 라이브러리를 통해 보다 강력한 유틸리티로 제공된다.

따라서 시간에 기초한 개념을 견고하게 처리하는 방법이 있지만, 사용하는 언어에 가장 적합한 라이브러리를 찾기 위해 노력을 기울어야 한다.

- java에서는 java.time 패키지의 클래스를 사용할 수 있다.


**양으로서의 시간과 순간으로서의 시간의 구분**

Duration 유형 사용
```java
/*
 * @param message 보낼 메시지
 * @param deadline 데드라인이 경과하기까지 메시지가 전송
 *      되지 않으면 전송은 취소된다.
 * @return 메시지가 전송되면 참을, 그렇지 않으면 거짓
 */
Boolean sendMessage(String message, Duration deadline) {
    // Duration 유형으로 인해 deadline이 나타내고자 하는 바가 명백하다.
}
```
이를 사용하면 함수 매개변수의 유형이 나타내는 것이 순산인지 아니면 시간의 양인지 알 수 있다.


**더 이상 단위에 대한 혼동이 없다.**

Instant 및 Duration과 같은 유형이 제공하는 이점은 단위가 유형 내에 캡슐화 되어 있다는 점이다.

```java
import java.time.Duration;

class UiSetting {
  // ...
  /*
   * @return UI 메시지를 보여주는 기간
   * */
  Duration getMessageTimeout() {
    return Duration.ofSeconds(5);
  }
}

//...

/*
 * @param message 보여줄 메시지
 * @param timeout 메시지를 보여주는 시간의 양
 * */
void showMessage(String message, Duration timeout) {
  // ...
}

public static void main(String[] args) {
  Duration duration1 = Duration.ofSeconds(5);
  print(duration1.toMillis()); // 출력 : 5000

  Duration duration2 = Duration.ofMinutes(2);
  print(duration2.toMillis()); // 출력 120000
}
```

어떤 단위가 사용되어야 하는지 설명하기 위한 계약의 세부 조항이 필요하지 않으며, 실수로 잘못된 단위를 제공하는 것이 불가능하다.


**시간대 처리 개선**

시간을 다루는 라이브러리는 정확한 순간과 연결하지 않고도 날짜를 나타낼 수 있는 방법을 제공한다.

LocalDateTime 이라는 클래스를 제공하는데, 이 클래스를 통해 해당 작업을 정확하게 수행할 수 있다.

---

## 데이터에 대한 진실의 원천을 하나만 가져야 한다.

- 기본 데이터 (primary data) : 코드에 제공해야 할 데이터, 코드에 이 데이터를 알려주지 않고는 코드가 처리할 방법이 없다.

- 파생 데이터 (derived data) : 주어진 기본 데이터에 기반해서 코드가 계산할 수 있는 데이터


### 또 다른 진실의 원천은 유효하지 않은 상태를 초래할 수 있다.

기본 데이터와 파생 데이터를 모두 처리하는 코드를 작성할 때, 논리적으로 잘못된 상태가 발생할 수 있다.

논리적으로 잘못된 상태가 발생할 수 있는 코드를 작성하면 코드의 오용이 너무 쉬워진다.

잔액에 대한 또 다른 진실의 원천
```java
class UserAccount() {
    private final Double credit;
    private final Double debit;
    private final Double balance;
    
    UserAccount(Double credit, Double debit, Double balance) {
        this.credit = credit;
        this.debit = debit;
        this.balance = balance;
    }
    
    Double getCredit() {
        return credit;
    }
    
    Double getDebit() {
        return debit;
    }
    
    Double getBalance() {
        return balance;
    }
}

public static void main(String[] args) {
  UserAccount account = new UserAccont(credit, debit, debit - credit); // 잔액이 차변에서 대변을 빼는 잘못된 방법으로 계산된다.
}
```

### 해결책 : 게본 데이터를 유일한 진실의 원천으로 사용하라.

요청 시 잔액 계산
```java
class UserAccount() {
    private final Double credit;
    private final Double debit;
    
    UserAccount(Double credit, Double debit) {
        this.credit = credit;
        this.debit = debit;
    }
    
    Double getCredit() {
        return credit;
    }
    
    Double getDebit() {
        return debit;
    }
    
    Double getBalance() {
        return credit - debit;
    }
}
```

잔고는 대변과 차변만 가지고 완전히 계산할 수 있기 때문에 필요할 때에만 그 값을 계산하는 것이 훨씬 낫다.


**데이터 계산에 비용이 많이 드는 경우**

때로는 파생된 값을 계산하는 데 훨씬 더 많은 비용이 들 수 있다.

파생된 값을 계산하는 데 많은 비용이 든다면, 그 값을 지연 계산한 수에 결과를 캐싱하는 것이 좋다. 

지연 계산 및 캐싱

```java
import javax.sql.rowset.spi.TransactionalWriter;

class UserAccount {
  private final ImmutableList<Transaction> transactions;
  
  private Double? cachedCredit;
  private Double? cachedDebit;

  UserAccount(ImmutableList<Transaction> transactions) {
      this.transactions = transactions;
  }
  
  // ...
  
  Double getCredit() {
    if (cachedCredit == null) {
      cachedCredit == transactions
              .map(transactions -> transactions.getCredit())
              .sum();
    }
    return cachedCredit;
  }
  
  Double getDebit() {
    if (cachedDebit == null) {
      cachedDebit == transactions
              .map(transactions -> transactions.getDebit())
              .sum();
    }
    return cachedDebit;
  }
  
  Double getBalance() {
      return getCredit() - getDebit();
  }
}
```

---

## 논리에 대한 진실의 원천을 하나만 가져야 한다.

진실의 원천(sources of truth)은 코드에 제공된 데이터에만 적용되는 것이 아니라 코드에 포함된 논리에도 적용된다.

코드의 한 부분에서 수행되는 일이 다른 부분에서 수행되는 일과 일치해야 하는 경우가 많다.

코드의 두 부분이 서로 일치하지 않으면 소프트웨어가 제대로 작동하지 않을 것이다.

그러므로 논리를 위한 진실의 원천 역시 단 하나만 존재하도록 하는 것이 중요하다.


### 논리에 대한 진실의 원천이 여러 개 있으면 버그를 유발할 수 있다.

값을 직렬화하고 저장하는 코드
```java
class DataLogger {
    private final List<Int> loggedValues;
    
    //...

  saveValues(FileHandler fileHandler) {
    String serializedValues = loggedValues
            .map(value -> value.toString(Radix.BASE_10))
            .join(",");
    file.write(serializedValues);
  }
}
```

값을 읽고 역직렬화하는 코드
```java
class DataLoader {
    
    //...

  List<Int> loadValues(FileHandler file) {
    return file.readAsString()
            .split(",") // 파일의 내용을 쉼표를 이용해 분할해서 문자열의 목록으로 만든다.
            .map(str -> Int.parse(str, Radix.BASE_10)); // 각 문자열을 십진수 정수로 읽어 들인다.
  }
}
```

값이 파일에 저장되는 형식은 논리의 중요한 부분이지만, 이 형식이 무엇인지에 대해서는 진실의 원천이 두 개 존재한다.

클래스가 모두 동일한 논리를 포함하면 모든 것이 잘 작동하지만 한 클래스가 수정되고 다른 클래스가 수정되지 않으면 문제가 발생한다.


### 해결책 : 진실의 원천은 단 하나만 있어야 한다.

직렬화된 정수를 저장하기 위한 형식은 같은 하위 문제지만 코드에서 각각의 하위 문제로 반복된다.

이렇게 되면 논리가 서로 달라지고 문제를 초래하기 쉽다.

직렬화된 정수를 저장하는 형식에 대한 진실의 원천을 하나만 갖게 되면 코드가 더 견고해지고 오류의 가능성을 줄일 수 있다.

정수 목록의 직렬화와 역직렬화를 재사용이 가능한 하나의 코드 계층으로 구현하면 단 하나만의 진실의 원천을 가질 수 있다.

IntListFormat 클래스
```java
class IntListFormat {
  private final String DELIMITER = ",";
  private final Radix RADIX = Radix.BASE_10; // 구분자와 진법이 상수로 지정된다.

  String serialize(List<Int> values) {
    return values
            .map(value -> value.toString(RADIX))
            .join(DELIMITER);
  }

  List<Int> deserialize(String serialized) {
    return serialized
            .split(DELIMITER)
            .map(str -> Int.parse(str, RADIX));
  }
}
```

DataLogger 및 DataLoader
```java
class DataLogger {
    private final List<Int> loggedValues;
    private final IntListFormat intListFormat;
    
    // ...
  saveValues(FileHandler file) {
    file.write(intListFormat.serialize(loggedValues));
  }
  
  //...
  
}

class DataLoader {
    private final IntListFormat intListFormat;
    
    //...
  
  List<Int> loadValues(FileHandler file) {
    return intListFormat.deserialize(file.readAsString());
  }
} 
```

IntListFormat 클래스가 직렬화된 정수의 저장 형식에 대한 유일한 진실의 원천임을 알 수 있다.
이렇게 하면 앞서 DataLogger 클래스에서 사용하는 형식을 변경하지만 실수로 DataLoader 클래스는 변경하지 않은 것과 같은 위험은 제거된다.

---

## 요약

- 코드가 오용되기 쉽게 작성되고 나면 어느 시점에선가는 오용될 가능성이 크고 이것은 버그로 이어질 수 있다.

- 코드가 오용되는 몇 가지 일반적인 사례는 다음과 같다.
  - 호출하는 쪽에서 잘못된 입력을 제공
  - 다른 코드에서 일어나는 부수 효과
  - 함수 호출 시점이 잘못되거나 올바른 순서로 호출되지 않은 경우
  - 원래의 코드에 연관된 코드를 수정할 때 원래의 코드가 내포한 가정과 어긋나게 수정하는 경우

- 오용이 어렵거나 불가능하도록 코드를 설계하고 구조화하는 것이 종종 가능하다. 이를 통해 버그 발생 가능성이 크게 줄어들고 중장기적으로 개발자의 시간을 많이 절약할 수 있다.