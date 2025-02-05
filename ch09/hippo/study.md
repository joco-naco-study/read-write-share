# Chapter 09. 코드를 재사용하고 일반화할 수 있도록 하라

향후에 재사용이 가능하도록 의도적으로 로드를 작성하고 구조화하는 것이 바람직하다.

이것은 초기에 노력이 좀 더 필요할 수 있지만(대개의 경우 아주 많이 필요하지는 않다)

장기적으로 보면 자신과 팀 동료들의 시간과 노력을 절약할 수 있다.

간결한 추상화 계층을 만들고 코드를 모듈화하면 하위 문제의 해결책이 서로 느슨하게 결합하는 코드로 나누어지는 경향이 있다.

이렇게 되면 보통 코드를 재사용하고 일반화하기가 훨씬 쉽고 안전해 진다.

---

## 가정을 주의하라

가정으로 인해 코드가 더 취약해지고 활용도가 낮아져 재사용하기에 안전하지 않을 수 있다.

코드의 어느 부분에서 어떤 가정이 이루어졌는지 정확히 추적하는 것은 극도로 어렵기 때문에 이런 가정은 다른 개발자들이 주의하지 않으면 빠질 수 있는 함정이 될 수 있다.

처음에 코드를 개선하는 쉬운 방법처럼 보였던 것이 코드가 재사용되지마자 버그와 이상한 동작이 나타나고 실제로는 의도한 것과는 반대의 효과를 가져올 수 있다.

---

## 가정은 코드 재사용 시 버그를 초래할 수 있다.

```java
class Article {
    private List<Section> sections;
    // ...
    
    List<Image> getAllImages() {
        for (Section section in sections) {
            if (section.containsImages()) {
                // 기사 내에 이미지를 포함하는 섹션은 최대
                // 하나만 있다.
                return section.getImages();
            }
        }
        return [];
    }
}
```

위 코드는 이미지가 있는 섹션이 두 개 이상인 기사에 getAllImages() 함수가 사용될 경우 모든 이미지를 반환하지 않는다!

그로 인해 버그를 초래할 가능성이 있다.

---

## 해결책 : 불필요한 가정을 피하라

> 섣부른 최적화
> 
> 대부분의 경우에는 큰 효과 없는 코드 최적화를 하느라고 애쓰기보다는 코드를 읽을 수 있고, 유지보수 가능하며, 견고하게 만드는 데 집중하는 것이 좋다.
> 코드의 어떤 부분이 여러 번 실행되고 그 부분을 최적화하는 것이 성능 향상에 큰 효과를 볼 수 있다는 점이 명백해질 때에라야 최적화 작업을 해도 무방하다.

```java
import java.util.ArrayList;

class Article {
    private List<Section> sections;
    // ...

    List<Image> getAllImages() {
        List<Image> images = new ArrayList<>();
        for (Section section : sections) {
            images.addAll(section.getImages());
        }
    }
}
```

코드를 작성할 때 필요 이상으로 성능비용과 같은 문제에 주의를 기울이는 경우가 있다.

그러나 코드에 가정이 들어가면 취약성의 측면에서도 관련 비용을 수반한다는 것을 기억하자!

---

## 해결책 : 가정이 필요하다면 강제적으로 하라

우리가 상정한 가정으로 인해 다른 개발자들이 무의식중에 곤란을 겪지 않도록 하기 위해 가정을 강제적으로 시행해야 한다.

1. 가정이 '깨지지 않게' 만들라
   - 가정이 깨지만 컴파일되지 않는 방식으로 코드를 작성할 수 있다면 가정이 항상 유지될 수 있다.

2. 오류 전달 기술을 사용하라
   - 가정을 깨는 것이 불가능하게 만들 수 없는 경우에는 오류를 감지하고 오류 신호 전달 기술을 사용하여 신속하게 실패하도록 코드를 작성할 수 있다.


**문제의 소지가 있는, 강제되지 않은 가정**

가정을 포함하는 코드
```java
class Article {
    private List<Section> sections;
    
    // ...
    
    Section? getImageSection() {
        // 기사 내에 이미지를 포함하는 섹션은 최대
        // 하나만 있다.
        return sections
                .filter(section -> section.containsImage())
                .first();
    }
}
```

가정에 의존하는 호출자
```java
class ArticleRender {
    // ...

    void render(Article article) {
        // ...
        // 기사 템플릿은 이미지를 갖는 섹션을 최대 하나만 처리할 수 있다.
        Section? imageSection = article.getImageSection();
        if (imageSection != null) {
            templateData.setImageSection(imageSection);
        }
    }
}
```


**가정의 강제적 확인**

```java
class Article {
    private List<Section> sections;
    // ...
    
    Section? getOnlyImageSection() {
        List<Section> imageSections = sections
                .filter(section -> section.containsImages());
        // 어서션은 이 가정을 강제로 확인한다.
        assert(imageSections.size() <= 1, "기사가 여러 개의 이미지 섹션을 갖는다.");
        
        // ImageSections 리스트에서 첫 번째 항목을 반환하거나 리스트가 비어 있으면 널을 반환한다.
        return imageSections.first();
    }
}
```

---

## 전역 상태를 주의하라

전역 상태 또는 전역 변수는 실행되는 프로그램 내의 모든 콘텍스트 사이에 공유된다.

전역 변수를 갖는 클래스
```java
class Article {
    private Int a = 3; // 인스턴스 변수
    private static Int b = 4; // static으로 표시되어 있기 때문에 전역 변수다.

    void setA(Int value) {
        a = value;
    }
    
    Int getA() { return a; }
    
    void setB(Int value) { b = value; }
    
    Int getB() { return b; }
    
    static Int getBStatically() { return b; } // 정적 함수
}
```

> 전역성과 가시성을 혼동하지 말라
> 
> 변수가 전역인지의 여부를 가시성과 혼동해서는 안 된다.
> 변수의 가시성은 변수가 퍼블릭인지 혹은 프라이빗인지를 나타내며, 코드의 다른 부분이 해당 변수에 접근할 수 있는지를 지시한다.
> 변수는 전역 여부와 관계없이 퍼블릭이나 프라이빗일 수 있다. 
> 요점은 전역변수가 클래스의 인스턴스나 함수의 자체 버전 대신 프로그램의 모든 콘텍스트 간에 공유된다는 것이다.

전역변수는 프로그램 내의 모든 콘텍스트에 영향을 미치기 때문에 전역 변수를 사용할 때는 누구도 해당 코드를 다른 목적으로 재사용하지 않을 것이다는 암묵적인 가정을 전제한다.

이전 절에서 살펴봤들이 가정에는 비용이 수반된다. 전역 상태는 코드를 매우 취약하게 만들고 재사용하기도 안전하지 않기 때문에 일반적으로 이점보다 비용이 더 크다.


---

## 전역 상태를 갖는 코드는 재사용하기에 안전하지 않을 수 있다.

ShoppingBasket
```java
class ShoppingBasket {
    private static List<Item> items = [];

    static void addItem(Item item) {
        items.add(item);
    }
    
    static void List<Item> getItems() {
        return List.copyOf(items);
    }
}
```

ShoppingBasket을 사용하는 클래스
```java
class ViewItemWidget {
    private final Item item;
    
    ViewItemWidget(Item item) {
        this.item = item;
    }
    //...
    
    void addItemToBasket() {
        ShoppingBasket.addItem(item); // 전역 상태를 수정한다.
    }
}

class ViewBasketWidget {
    //...
    void displayItems() {
        List<Item> items = ShoppingBasket.getItems(); // 전역 상태를 읽는다.
    }
}
```

장바구니의 내용물을 읽고 수정하기 너무 쉽기 때문에 이런 방식으로 전역 변수를 사용하고 싶은 마음이 들 수 있다.

하지만 이렇게 전역 상태를 사용하는 코드는 재사용 시 작동이 안되고 이상한 일이 일어날 가능성이 있다.

두 개의 코드가 동일한 전역 상태를 읽고 수정한다. 이로 인해 서로에게 의도하지 않은 결과를 초래한다!

---

## 해결책 : 공유 상태에 의존성 주입하라

수정된 ShoppingBasket 클래스

```java
import java.util.ArrayList;

class ShoppingBasket {
    private final List<Item> items = new ArrayList<>();
    
    void addItem(Item item) {
        items.add(item);
    }
    
    void List<Item> getItems() {
        return List.copyOf(items);
    }
}

```

```java
class ViewItemWidget {
    private final Item item;
    private final ShoppingBasket basket;

    // 의존성이 주입된 ShoppingBasket
    ViewItemWidget(Item item, ShoppingBasket basket) {
        this.item = item;
        this.basket = basket;
    }
    
    //...
    
    void addItemToBasket() {
        basket.addItem(item);
    }
}

class ViewBasketWidget {
    private final ShoppingBasket basket;
    
    ViewBasketWidget(ShoppingBasket basket) {
        this.basket = basket;
    }
    
    // 주입된 ShoppingBasket 인스턴스에 대해 호출된다.
    void displayItems() {
        List<Item> items = basket.getItems();
    }
}
```

개별 쇼핑 바스켓 인스턴스 나열
```java
ShoppingBasket normalBasket = new ShoppingBasket();
ViewBasketWidget normalBasketWidget = new ViewBasketWidget(normalBasket);


ShoppingBasket freshBasket = new ShoppingBasket();
ViewBasketWidget freshBasketWidget = new ViewBasketWidget(freshBasket);
```

전역 상태를 사용하는 것은 코드 재사용이 전혀 안전하지 않을 수 있다.

전역 상태가 사용된다는 사실을 다른 개발자는 모를 수 있기 때문에 코드를 재사용하려고 하면 이상한 동작과 버그가 발생할 수 있다.

프로그램의 서로 다른 부분 간에 상태를 공유해야 할 경우, 의존성 주입을 사용해 보다 통제된 방식으로 수행하는 것이 더 안전하다.

---

## 기본 반환값을 적절하게 사용하라

합리적인 기본값은 사용자 친화적인 소프트웨어를 만들기 위한 좋은 방법이다.

기본값을 제공하려면 두 가지 가정이 필요하다.

- 어떤 기본값이 합리적인지
- 더 상위 계층의 코드는 기본값을 받든지 명시적으로 설정된 값을 받든지 상관하지 않는다.

상위 수준의 코드는 특정 사용 사례에 더 밀접하게 결합하므로 코드의 모든 용도에 맞는 기본값을 선택하기가 더 쉽다.

반면에 낮은 수준의 코드는 보다 근본적인 하위 문제를 해결하여 여러 사용 사례에 더 광범위하게 재사용되는 경향이 있다.

따라서 낮은 층위에서는 모든 용도에 적합한 기본값을 선택하기가 훨씬 더 어렵다.


## 낮은 층위의 코드의 기본 반환값은 재사용성을 해칠 수 있다.

기본값을 정의하는 코드의 계층이 낮을수록 그 가정이 적용되는 상위 계층은 더 많아진다.


## 해결책 : 상위 수준의 코드에서 기본값을 제공하라

널 값 반환
```java
class UserDocumentSettings {
    private final Font? font;
    
    Font? getPreferredFont() {
        return font; // 사용자가 선호하는 폰트가 없는 경우 널값을 반환한다.
    }
}
```

상위 수준 코드에서 기본값을 제공하는 하위 문제를 해결하기 위한 전용 클래스
기본값을 캡슐화하기 위한 클래스
```java
class DefaultDocumentSettings{
    // ...
    Font getDefaultFont() {
        return Font.ARIAL;
    }
}
```

설정에 대한 추상화 계층 나열
```java
class DocumentSettings {
    private final UserDocunmentSettings userSettings;
    private final DefaultDocumentSettings defaultSettings;
    
    DocumentSettings(
            UserDocumentSettings userSettings,
            DefaultDocumentSettings defaultSettings
    ) {
        this.userSettings = userSettings;
        this.defaultSettings = defaultSettings;
    }
    
    // ...
    
    Font getFont() {
        Font? userFont = userSettings.getPreferredFont();
        if (userFont != null) {
            return userFont;
        }
        return defaultSettings.getFont();
    }
    
    // 널 병합 연산자 이용
    Font getFont() {
        return userSettings.getPreferredFont() ?? defaultSettings.getFont();
    }
}
```

> 기본 반환값 매개변수
> 
> 어떤 기본값을 사용할지에 대한 결정을 호출하는 쪽에서 하도록 하면 코드의 재사용성이 향상된다.
> 그러나 널 병합 연산자를 지원하지 않는 언어에서 널을 반환하면 호출하는 쪽에서는 널 처리를 위한 반복적인 코드를 작성해햐 한다.
> 
> 코드의 일부에서 사용하는 한 가지 방법은 기본 반환값 매개변수를 사용하는 것이다.
> 
> ```java String value = map.getOrDefault(key, "default value"); ```
>
> 이렇게 하면 호출하는 쪽에서는 널값을 처리할 필요 없이 적잘한 기본값을 결정할 수 있다.

기본값을 반환하면 그 위의 모든 상위 계층에서 해당 값을 사용할 것이라고 가정하기 때문에 코드 재사용과 적응성을 제한할 수 있다.

단순히 널을 반환하고 더 높은 층위에서 기본값을 구현하는 것이 나을 수 있는데, 상위 수준에서 상정한 가정은 유효할 가능성이 크다.

---

## 함수의 매개변수를 주목하라

### 필요 이상으로 매개변수를 받는 함수는 재사용하기 어려울 수 있다.

너무 많은 매개변수를 받는 함수
```java
void styleAsWarning(TextBox textBox) {
    TextOptions style = new TextOptions(
            Font,ARIAL,
            12.0,
            14.0,
            Color.RED
    );
    textBox.setTextColor(style);
}
```

## 해결책 : 함수는 필요한 것만 매개변수로 받도록 하라.

일반적으로 함수가 필요한 것만 받도록 하면 코드는 재사용성이 향상되고 이해하기도 쉬워진다.

---

## 제네릭의 사용을 고려하라

많은 언어가 제네릭 혹은 템플릿을 지원한다.

제네릭을 통해 참조하는 모든 유형을 구체적으로 명시할 필요 없이 클래스를 작성할 수 있다.

다른 클래스를 참조하는 코드를 작성하지만 그 클래스가 어떤 클래스인지 신경 쓰지 않는다면 제네릭의 사용을 고려해야 한다.


### 특정 유형에 의존하면 일반화를 제한한다

문자열 유형을 하드 코딩해서 사용

```java
import java.util.ArrayList;

class RandomizedQueue {
    private final List<String> values = new ArrayList<>();

    void add(String value) {
        values.add(value);
    }
    
    /*
     * 큐로부터 무작위로 한 항목을 삭제하고 그 항목을 반환한다.
     * */
    String? getNext() {
        if (values.isEmpty()) {
            return null;
        }

        Int randomIndex = Math.randomInt(0, values.size());
        values.swap(randomIndex, values.size() - 1);
        return values.removeLast();
    }
}
```

### 해결책 : 제네릭을 사용하라.

하드 코딩으로 String에 대한 의존성을 갖는 대신, 이 클래스를 나중에 사용할 때 어떤 유형을 사용할지 지시할 수 있는 자리 표시자를 지정할 수 있다.

제네릭 사용

```java
import java.util.ArrayList;

class RandomizedQueue<T> {
    private final List<T> values = new ArrayList<>();

    void add(T value) {
        values.add(value);
    }

    /*
     * 큐로부터 무작위로 한 항목을 삭제하고 그 항목을 반환한다.
     * */
    T? getNext() {
        if (values.isEmpty()) {
            return null;
        }

        Int randomIndex = Math.randomInt(0, values.size());
        values.swap(randomIndex, values.size() - 1);
        return values.removeLast();
    }
}
```

<T>는 컴파일러에게 T가 타입의 자리 표시자로 사용할 것이라고 알려준다.

---

높은 수준의 문제를 하위 문제로 세분화하다 보면 다양항 사례에 적용할 수 있는 매우 근본적인 문제를 접한다.

하위 문제에 대한 해결책이 모든 데이터 유형에 쉽게 적용될 수 있을 때 특정 유형에 의존하는 대신 제네릭을 사용하더라도 추가적인 노력이 거의 들어가지 않는다.

이렇게 하면 코드는 보다 더 일반화되고 재사용이 가능하다는 측면에서 쉽게 효과를 볼 수 있다.

---

### 요약

- 동일한 하위 문제가 자주 발생하므로 코드를 재사용하면 미래의 자신과 팀 동료의 시간과 노력을 절약할 수 있다.

- 다른 개발자가 여러분이 해결하려는 문제와는 다른 상위 수준의 문제를 해결하더라도 특정 하위 문제에 대해서는 여러분이 작성한 해결책을 재사용할 수 있도록 근본적인 하위 문제를 식별하고 코드를 구성하도록 노력해야 한다.

- 간결한 추상화 계층을 만들고 코드를 모듈식으로 만들면 코드를 재사용하고 일반화하기가 월씬 쉽고 안전해진다.

- 가정을 하게 되면 코드는 종종 더 취약해지고 재사용하기 어렵다는 측면에서 비용이 발생한다.

    - 가정을 하는 경우의 이점이 비용보다 큰지 확인하라
    - 가정을 해야 할 경우 그 가정이 코드의 적절한 계층에 대해 이루어지는 것인지 확인하고 가능하다면 가정을 강제적으로 적용하라.

- 전역 상태를 사용하면 특히 비용이 많이 발생하는 가정을 하는 것이 되고 재사용하기에 전혀 안전하지 않은 코드가 된다. 대부분의 전역 상태를 피하는 것이 가장 바람직하다.