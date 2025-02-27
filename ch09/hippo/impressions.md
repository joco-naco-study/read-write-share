이번 장을 읽고 인상 깊은 내용은 아래와 같다.


## 낮은 층위의 코드의 기본 반환값은 재사용성을 해칠 수 있다.

기본값을 정의하는 코드의 계층이 낮을수록 그 가정이 적용되는 상위 계층은 더 많아진다.

## 해결책 : 상위 수준의 코드에서 기본값을 제공하라

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
