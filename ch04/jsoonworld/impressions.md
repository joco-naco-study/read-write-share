## 에러 처리에 대한 학습과 고민

이번 단원을 학습하면서 **에러 처리의 다양한 방법**에 대해 알게 되었다.  
명시적으로 에러를 처리할 때와 암시적으로 처리할 때 각각의 상황에 맞는 방법들을 살펴볼 수 있었다.

---

### 고민과 의문

1. **요란스러운 에러 발생의 필요성**
   - 시스템 종료를 동반하는 **요란스러운 에러**가 과연 항상 좋은가에 대해 의문이 들었다.
   - 상황마다, 그리고 요구사항에 따라 다를 것 같다는 생각이 든다.  
     예를 들어, 어떤 서비스에서는 에러가 발생하더라도 **시스템이 지속 가능한 것이 더 유리**할 수도 있기 때문이다.

2. **객체지향과 에러 책임**
   - 객체지향 설계에서는 **에러의 책임**이 해당 객체에 있으며, 호출하는 쪽에서도 신경을 써야 하는지에 대해 설명한다.
   - 그러나, 에러 처리의 책임 범위가 어디까지인지에 대한 고민이 여전히 남아 있다.  
     이는 아마도 경험 부족과 함께, 이 주제가 **정답이 없는 문제**이기 때문일 것이다.

---

### 앞으로의 생각

- 개발 경험을 쌓으면서, 에러 처리에 대한 **나만의 기준**이 생기고,  
  상황에 맞는 **판단 능력**이 점점 개선될 것이라고 기대한다.