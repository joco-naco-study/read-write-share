# Chapter 08. 코드를 모듈화하라

## 의존성 주입의 사용을 고려하라.

### 하드 코드화된 의존성은 문제가 될 수 있다.

하드 코드로 구현된 종속성
```java
class RoutePlanner {
    private final RoadMap roadMap; // RouterPlanner는 ReadMap에 의존한다.
    
    RoutePlanner() {
        this.roadMap = new NorthAmericaRoadMap(); // RouterPlanner 클래스는 NorthAmericaRoadMap 객체를 생성한다. 
    }

    Route planRoute(LatLong startPoint, LatLong endPoint) {
         // ...
    }
}

interface RoadMap {
    List<Road> getRoads();
    List<Junction> getJunctions();
}

class NorthAmericaRoadMap implements RoadMap { // NorthAmericaRoadMap은 RoadMap의 수 많은 구현 클래스 중 하나다.
    // ...
    override List<Road> getRoads() { 
        // ... 
    }
    override List<Junction> getJunctions() {
        // ...
    }
}
```

RoadMap의 특정 구현에 의존해서 코드를 구현하면 다른 구현으로 코드를 재설정할 수 없다.

설정 가능한 종속성
```java
class NorthAmericaRoadMap implements RoadMap {
    // ...
    
    NorthAmericaRoadMap(
            Boolean useOnlineVersion,
            Boolean includeSeasonalRoads
    ) {
        // ...
    }
    
    override List<Road> getRoads() {
        // ...
    }
    
    override List<Junction> getJunctions() {
        // ...
    }
}
```

하드 코드화된 종속성 설정
```java
class RoutePlanner {
    private final Boolean USE_ONLINE_MAP = true;
    private final Boolean INCLUDE_SEASONAL_ROADS = false;
    
    private final RoadMap roadMap;
    
    RoutePlanner() {
        this.roadMap = new NorthAmericaRoadMap(
                USE_ONLINE_MAP, INCLUDE_SEASONAL_ROADS
        );
    }

    Route planRoute(LatLong startPoint, LatLong endPoint) {
        // ...
    }
}
```

### 해결책 : 의존성 주입을 사용하라

생성자의 매개변수를 통해 로드맵을 제공함으로써 RoadMap을 주입하면 클래스는 훨씬 더 모듈화되고 다용도로 쓰일 수 있다.

```java
class RoutePlanner {
    private final RoadMap roadMap;
    
    RoutePlanner(RoadMap roadMap) {
        this.roadMap = roadMap;
    }

    Route planRoute(LatLong startPoint, LatLong endPoint) {
        // ...
    }
}

public static void main(String[] args) {
    RoutePlanner europeRoutePlanner = new RoutePlanner(new EuropeRoadMap());

    RoutePlanner northAmericaRoutePlanner = new RoutePlanner(new NorthAmericaRoadMap(true, false));
}
```


팩토리 함수
```java
class RoutePlannerFactory {
    static RoutePlanner createEuropeRoutePlanner() {
        return new RoutePlanner(new EuropeRoadMap());
    }
    
    static RoutePlanner createDefaultNorthAmericaRoutePlanner() {
        return new RoutePlanner(
                new NorthAmericaRoadMap(true, false)
        );
    }
}
```

팩토리 함수를 직접 작정하는 것의 대란으로 의존성 주입 프레임워크를 사용할 수도 있다.


### 의존성 주입을 염두에 두고 코드를 설계하라

> 정적 매달림
> 
> 정적 함수에 과도하게 의존하는 것을 정적 매달림(static cling)이라고 한다. 이에 대한 잠재적 문제는 잘 알려져 있고 문서화도 잘 되어 있다.
> 단위 테스트 코드에서 특히 문제가 될 수 있는데, 그 이유는 정적 매달림이 많은 코드에 대해서는 테스트 더블을 사용할 수 없기 때문이다.

인스턴스를 만들 수 있는 클래스
```java
interface RoadMap {
    List<Road> getRoads();
    List<Junction> getJunctions();
}

class NorthAmericaRoadMap implements RoadMap {
    // ...
    override List<Road> getRoads() {
        //...
    }
    
    override List<Junction> getJunctions() {
        // ...
    }
}
```

의존성 주입은 코드를 모듈화하고 다른 시나리오에도 적용할 수 있게 해주는 훌륭한 방법이다.
하위 문제에 대한 해결책이 여러 개 있는 경우에는 의존성 주입이 특별히 중요할 수 있다.

---

## 인터페이스에 의존하라

어떤 클래스에 의존하고 있는데 그 클래스가 어떤 인터페이스를 구현하고 필요한 기능이 그 인터페이스에 모두 정의되어 있으면,

클래스에 직접 의존하기보다는 인터페이스에 의존하는 것이 일반적으로 더 바람직하다.

### 구체적인 구현에 의존하면 적응성이 제한된다.

### 해결책 : 가능한 경우 인터페이스에 의존하라

```java
class RoutePlanner {
    private final RoadMap roadMap;

    RoutePlanner(RoadMap roadMap) {
        this.roadMap = roadMap;
    }

    Route planRoute(LatLong startPoint, LatLong endPoint) {
        // ...
    }
}
```

> 의존성 역전 원리
> 
> 보다 구체적인 구현보다는 추상화에 의존하는 것이 낫다


---

## 클래스 상속을 주의하라

클래스는 다른 클래스를 상속할 수 있고 이렇게 해서 클래스 계층이 형성된다.

### 클래스 상속은 문제가 될 수 있다

**상속은 추상화 계층에 방해가 될 수 있다.**

한 클래스가 다른 클래스를 확장하면 슈퍼클래스의 모든 기능을 상속한다.

이 기능은 close() 함수의 경우처럼 유용할 때가 있지만, 원하는 것보다 더 많은 기능을 노출할 수도 있다.

이로 인해 추상화 계층이 복잡해지고 구현 세부 정보가 드러날 수 있다.

**상속은 적응성 높은 코드의 작성을 어렵게 만들 수 있다**



### 해결책 : 구성을 사용하라

클래스를 확장하기보다는 해당 클래스의 인스턴스를 가지고 있음으로써 하나의 클래스를 다른 클래스로부터 구성한다는 것을 의미한다.


**더 간결한 추상화 계층**

**적응성이 높은 코드**


### 진정한 is-a 관계는 어떤가?

진정한 is-a 관계가 있다 하더라도 상속은 문제가 될 수 있다.

- 취약한 베이스 클래스 문제 (the fragile base class problem) : 서브 클래스가 슈퍼 클래스에서 상속되고 해당 슈퍼 클래스가 나중에 수정되면 서브 클래스가 작동하지 않을 수도 잇다.
- 다이아몬드 문제 (diamond problem) : 여러 슈퍼 클래스가 동일한 함수의 각각 다른 버전을 제공하는 경우 문제가 발생. 어떤 슈퍼클래스로부터 해당 함수를 상속해야 하는지 모호하기 때문이다.
- 문제가 있는 계층 구조 : 많은 언어가 다중 상속을 지원하지 않으므로 클래스는 오직 하나의 클래스만 직접 확장할 수 있다. 단일 상속은 문제가 발생할 ㅅ ㅜ있다.

---

## 클래스는 자신의 기능에만 집중해야 한다.

### 다른 클래스와 지나치게 연관되어 있으면 문제가 될 수 있다.

### 해결책 : 자신의 기능에만 충실한 클래스를 만들라

> 디미터의 법칙
> 
> 한 객체가 다른 객체의 내용이나 구조에 대해 가능한 한 최대한으로 가정하지 않아야 한다는 소프트웨어 공학의 원칙이다.
> 이 원칙은 특히 한 객체는 직접 관련된 객체와만 상호작용해야 한다고 주장한다


코드 모듈화를 위한 핵심 목적 중 하나는 요구 사항의 변경이 필요하면 해당 요구 사항과 직접 관련된 코드만 변경하고자 하는 것이다.

클래스는 서로에 대한 어느 정도의 지식을 필요로 할 때고 있지만, 가능한 한 이것을 최소화하는 것이 좋을 때가 많다.

이를 통해 코드 모듈화를 유지할 수 있으며 적응성과 유지 관리성을 크게 개선할 수 있다.

---

## 관련 있는 데이터는 함께 캡슐화하라

### 캡슐화되지 않은 데이터는 취급하기 어려울 수 있다.

### 해결책 : 관련된 데이터는 객체 또는 클래스로 그룹화하라.

여러 데이터가 따로 떨어져서는 별 의미가 없을 정도로 서로 밀접하게 연관되어 있거나, 캡슐화된 데이터 중에서 일부만 원하는 경우가 아니라면 캡슐화하는 것이 합리적이다.

---

## 반환 유형에 구현 세부 정보가 유출되지 않도록 주의하라

구현 세부 정보가 유출되면 코드의 하위 계층에 대한 정보가 노출될 수 있으며, 향후 수정이나 재설정이 매우 어려워질 수 있다.

### 반환 형식에 구현 세부 사항이 유출될 경우 문제가 될 수 있다.

### 해결책 : 추상화 계층에 적합한 유형을 반환하라

외부로 노출할 개념을 최소화하는 유형을 새로 정의해 사용하면 좀 더 모듈화된 코드와 간결한 추상화 계층을 얻을 수 있다.

---

## 예외 처리 시 구현 세부 사항이 유출되지 않도록 주의하라

호출하는 쪽에서 복구하고자 하는 오류에 대해 비검사 예외를 사용하는 경우 예외 처리 시 구현 세부 정보를 유출하는 것은 특히 문제가 될 수 있다.


### 예외 처리 시 구현 세부 사항이 유출되면 문제가 될 수 있다.

구현 세부 정보가 유출될 위험이 비검사 예외에만 있는 것은 아니지만, 이 예에서 비검사 예외로 인해 문제가 더욱 악화되어 발생한다.

비검사 예외를 발생할 수 있다는 점을 개발자가 문서화하지 않을 가능성이 크고, 인터페이스를 구현하는 클래스가 반드시 인터페이스가 규정하는 오류만 발생시켜야만 하는 것은 아니다.

### 해결책 : 추상화 계층에 적절한 예외를 만들라

구현 세부 사항의 유출을 방지하기 위해 코드의 각 계층은 주어진 추상화 계층을 반영하는 오류 오형만을 드러내는 것이 이상적이다.

이것은 하위 계층의 오류를 현재 계층에 적합한 오류 유형으로 감싸면 가능하다.

이렇게 하면 호출하는 쪽에 적절한 추상화 계층이 제시되면서 동시에 원래의 오류 정보가 손실되지 않는다는 것을 의미한다.

---

## 요약

- 코드가 모듈화되어 있으면 변경된 요구 사항을 적용하기 위한 코드를 작성하기 쉽다.
- 모듈화의 주요 목표 중 하나는 요구 사항의 변경이 해당 요구 사항과 직접 관련된 코드에만 영향을 미치도록 하는 것이다.
- 코드를 모듈식으로 만드는 것은 간결한 추상화 계층을 만드는 것과 깊은 관련이 있다.
- 다음의 기술을 사용하여 코드를 모듈화할 수 있다.
  - 의존성 주입
  - 구체적인 클래스가 아닌 인터페이스에 의존
  - 클래스 상속 대신 인터페이스 및 구성의 활용
  - 관련된 데이터의 캡슐화
  - 반환 유형 및 예외 처리 시 구현 세부 정보 유출 방지