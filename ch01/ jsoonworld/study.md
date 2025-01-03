# 1. 코드 품질

코품질 코드와 저품질 코드의 비교

1. 최초의 요구사항
	고품질 코드 : 완전하게 충족
	저품질 코드 : 경계 조건을 처리하지 못하기 때문에 완전하게 충족하지 못함
2. 요구 사항 변화
	고품질 코드: 사소한 추가 작업 필요
	저품질 코드: 대규모의 코드 변경 및 리팩터링 필요
3. 오류 발생 시
	고품질 코드: 시스템이 복구되거나 부분적으로 작동
	저품질 코드: 시스템은 미리 정의되지 않은 상태에 놓이고 데이터는 손상될 가능성이 있음.
4. 처음 접하는 상황
	고품질 코드: 명백히 예상되지 않은 상황도 처리
	저품질 코드: 시스템은 미리 정의되지 않은 상태에 놓이고 데이터는 손상될 가능성이 있음.
5. 시스템 공격
	고품질 코드: 시스템은 안전한 상태이고 손상되지 않음
	저품질 코드: 시스템은 미리 정의되지 않은 상태에 놓이고 손상될 가능성이 있음

코품질의 코드는 신뢰할 수 있고, 유지보수가 쉬우며, 요구 사항을 충족하는 소프트웨어가 될 가능성을 최대한으로 높여준다. 저품질의 코드는 그 반대다.

## 1.1 코드는 어떻게 소프트웨어가 되는가

1. 개발자가 코드베이스의 로컬 복사본을 가지고 작업하면서 코드를 변경한다.
2. 작업이 끝나면 코드 검토를 위해 변경된 코드를 가지고 병합 요청을 한다.
3. 다른 개발자가 코드를 검토하고 변경을 제안할 수 있다.
4. 작성자와 검토자가 모두 동의하면 코드가 코드베이스에 병합된다.
5. 배포는 코드베이스를 가지고 주기적으로 일어난다. 얼마나 자주 배포되는지는 조직과 팀마다 다를 수 있다(몇 분에서 몇 개월까지 다양하다).
6. 테스트에 실패하거나 코드가 컴파일되지 않으면 코드베이스에 병합되는 것을 막거나 코드가 배포되는 것을 막는다.

## 1. 2 코드 품질의 목표

필자는 코드를 작성할 때 다음과 같은 네 가지 상위 수준의 목표를 달성하려고 한다.

1. 작동해야 한다.
2. 작동이 멈춰서는 안 된다.
3. 변화하는 요구 사항에 적응해야 한다.
4. 이미 존재하는 기능을 또다시 구현해서는 안 된다.

### 1.2.1 코드는 작동해야 한다

### 1.2.2 코드는 작동이 멈추면 안 된다

- 코드는 다른 코드에 의존할 수 있는데, 그 코드가 수정되고 변경될 수 있다.
- 새로운 기능이 필요할 때 코드를 수정해야 할 수도 있다.
- 우리가 해결하려고 하는 문제는 시간이 지남에 따라 변경된다. 소비자 선호, 비즈니스 요구, 고려해야 할 기술 등이 바뀔 수 있다.

### 1.2.3 코드는 변경된 요구 사항에 적응할 수 있어야 한다

요구사항은 계속 변한다.

- 비즈니스 환경이 변한다.
- 사용자 선호가 변한다.
- 가정이 더 이상 유효하지 않다.
- 새로운 기능이 계촉 추가된다.

### 1.2.4 코드는 이미 존재하는 기능을 중복 구현해서는 안 된다

이미 구현된 코드를 재사용하면 좋은 몇 가지 이유가 있다.

- 시간과 노력을 절약한다.
- 버그 가능성이 줄어든다.
- 기존 전문지식을 활용한다.
- 코드가 이해하기 쉽다.


## 1.3 코드 품질의 핵심 요소

1. 코드는 읽기 쉬워야 한다.
2. 코드는 예측 가능해야 한다.
3. 코드를 오용하기 어렵게 만들라.
4. 코드를 모듈화하라.
5. 코드를 재사용 가능하고 일반화할 수 있게 작성하라.
6. 테스트가 용이한 코드를 작성하고, 제대로 테스트하라.

### 1.3.1 코드는 읽기 쉬워야 한다

코드를 읽을 때 다음과 같은 사항을 이해하기 위해 애쓸 것이다.

1. 코드가 하는 일
2. 어떻게 그 일을 수행하는지
3. (입력이나 상태 등) 어떤 것을 필요로 하는지
4. 코드 실행 결과물                   

### 1.3.2 코드는 예측 가능해야 한다

### 1.3.3 코드 오용하기 어렵게 만들라

### 1.3.4 코드를 모듈화 하라

### 1.3.5 코드를 재사용 가능하고 일반화할 수 있게 작성하라

재사용성과 일반화성은 유사하지만 약간 다른 개념이다.

- 재사용성`reusability`은 어떤 문제를 해결하기 위한 무언가가 여러 가지 다른 상황에서도 사용될 수 있음을 의미한다. 핸드 드릴은 벽, 바닥 판 및 천장에 구멍을 뚫는 데 사용할 수 있기 때문에 재사용 가능한 도구다. 문제는 동일하지만(드릴로 구멍을 뚫어야 한다), 상황은 다르다(벽을 뚫는 것과 바닥을 뚫는 것과 천장을 뚫는 것).

- 일반화성`generalizability` 은 개념적으로는 유사하지만 서로 미묘하게 다른 문제들을 해결할 수 있음을 의미한다. 핸드 드릴은 구멍을 뚫는 데 사용될 뿐만 아니라 나사를 박을 때도 사용될 수 있어서 일반화성을 갖는다. 드릴 제조사는 무언가를 돌린다는 것은 드릴로 구멍을 뚫는 일과 나사를 도렬 넣을 일에 둘 다 적용할 수 있는 일반적이라는 문제라는 것을 알고 두 가지 문제를 다 해결할 수 잇는 일반화된 도구를 만들었다.
### 1.3.6 테스트가 용이한 코드를 작성하고 제대로 테스트하라

테스트가 중요한 이유는 다음과 같다.

- 소프트웨어 시스템과 코드베이스는 너무 크고 복잡해 한 사람이 모든 세부 사항을 알 수 없고,
- (매우 똑똑한 개발자라 해도) 사람은 실수를 하는 존재다.

## 1.4 고품질 코드 작성은 일정을 지연시키는가?

이 질문에 대한 답은 단기적으로는 고품질 코드를 작성하는 데 시간이 더 걸릴 수 있다는 것이다.

코드 품질을 고려하지 않고 먼저 떠오르는 대로 코딩하면 처음에는 시간을 절약할 수 있다. 그러나 이런 코드는 머지 않아 취약하고 복잡한 코드베이스로 귀결될 것이며, 점점 더 이해하기 어렵고 추론할 수 없는 코드가 된다. 새로운 기능을 추가하거나 버그를 수정하는 것이 점점 더 어려워지고 시간도 더 많이 걸리는데, 작동하지 않는 코드를 처리하고 재설계해야 하기 때문이다.



