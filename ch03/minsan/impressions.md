# 1. 다른 사람에 의해 코드가 변경될 수 있음을 유의해야 한다.

협업을 하다보면 타인에 의해 내가 작성했던 코드가 변경될 여지가 충분히 존재한다. 이때 발생할 수 있는 문제점은 `변경된 코드가 본래 의도에서 벗어날 수 있다`는 점이다. 이러한 예기치 못한 변경사항에 대응하기 위해서는, **본래 의도했던 동작을 테스트할 수 있는 테스트 코드를 미리 설계해 두는 것이 중요하다는 것**을 깨달았다.

> 즉, 사소한 부분이라도 추후 코드가 변경될 상황을 고려해서 **테스트 코드를 작성하는 습관을 길들일 필요가 있다**는 생각이 들었다.

# 2. 코드의 세부 구현 내용을 확인해야 하는 경우, 추상화를 살펴보자

만약 **코드의 동작을 이해하기 위해 세부 구현 사항을 확인해야** 하는 상황이라면, 현재 코드에 `추상화`를 도입하는 방안을 검토해볼 수 있을 것이다.

```md
- `추상화`는 세부 구현사항에 대한 완벽한 이해가 없어도, 문제 해결 방법을 충분히 이해할 수 있다는 이점이 있다.
- 하지만 이러한 이점에 반하는 상황이 발생한다면 `추상화`를 고려해볼만 하다.
```

# 3. 세부 조약은 코드 자체로 표현해야 한다.

프로젝트를 처음 시작했을 때는 세부 조약을 모두 주석으로 처리했던 기억이 난다. 하지만 프리코스에 참여하면서 이러한 **세부 조약을 코드로 표현하는 방식**에 관해서 무의식적으로 고민해볼 수 있었고, 이 내용을 책에서도 다루고 있다.

주석문과 문서에 관해서 돌이켜보면, 실제 개발 과정에서 주석문과 문서는 최대한 읽지 않는 경향이 짙었다. 따라서 `세부 조약`을 **주석문 및 문서**로 표현하기 보다는, **코드 자체로 표현할 수 있도록** 코드 퀄리티를 높이는 것에 집중하는 자세가 중요하다는 점을 깨달았다.

특히 _"이 함수를 호출하기 전에 어떤 상태가 전제되어야 하며, 다른 함수가 호출된 상태임을 보장해야 한다"_ 와 같은 세부 조항을 **가능한 명확한 부분으로 드러내는 것**이 중요하다는 점을 상기할 수 있었다.

- 여기서 말하는 명확한 부분에는 `함수 및 클래스명`, `인자 타입`, `리턴 타입`, `컴파일 예외`가 존재한다.
  - 즉, `인자 타입`로 **선결 조건**을, `리턴 타입` 및 `함수 명`으로 **사후 조건**을 충분히 유추할 수 있게끔 설계해야 한다는 것이다.  
    👉🏻 함수를 호출하는 개발자는 인자 타입으로 전제 조건을 충족하기 위한 상태를 고려하여 호출 코드를 작성할 수 있다.

> 즉, 핵심은 **세부 조항을 코드에 포함시켜서** 코드가 오용될 가능성을 차단하는 것이다.

# 4. 널 값을 리턴할 수 있는 메소드

반환 타입에 널 값이 포함되는 경우, `널 값의 의미가 중복되면 안된다`는 점을 상기할 수 있었다. 교재 예제처럼 널 값이 `해당 값이 존재하지 않아서 발생하는 것`인지, 아니면 `오류로 인해 발생한 것인지` 코드를 통해 그 의미가 하나로 귀결되어야 한다는 것이다.

- `getUiColor` 메소드 예시 -> 함수 명을 통해서 보통 개발자들은 null 리턴 값을 Color가 존재하지 않는 것으로 이해하는 것처럼 말이다.

> 즉, 함수명과 리턴 값을 통해 **개발자가 유추할 수 있는 범위 내에서 동작하게끔 설계하는 것**이 매우 중요하다.