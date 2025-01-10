## 과제 셀프회고

### 기술적 성장

<!-- 예시
- 새로 학습한 개념
- 기존 지식의 재발견/심화
- 구현 과정에서의 기술적 도전과 해결
-->

React 프레임워크에 대한 간단한 이해로 웹을 개발해왔기에 자바스크립트나 DOM과 같은 중요한 개념에 대한 이해가 부족했습니다. 이번 과제를 계기로 **React의 근간을 이루는 SPA의 개념에 대해 깊게 공부해볼 수 있는 좋은 계기**가 된 것 같습니다. 주로 학습한 내용은 다음과 같습니다.

- 히스토리 기반 라우터의 동작과 해시 기반 라우터 동작의 이해
- 이벤트 버블링과 이벤트 위임
- 클래스 기반 컴포넌트와 함수 기반 컴포넌트에 대한 이해

### 코드 품질

이번 과제에서 설계에 관한 부분을 가장 오랫동안 고민하였습니다. 비록 한 주 동안 작성하고 떠나보낼 코드이지만 그럼에도 소소하게 개발해오며 쌓아온 원칙에 어긋나지 않기 위해 노력하였습니다. 제가 중요하게 생각하는 소소한 원칙은 다음과 같습니다.

1. **유지보수성과 확장성을 고려하여 개발하자**
   다소 뻔한 이야기일 수 있지만 언제나 가장 중요한 원칙 중 하나라고 생각합니다. 저는 주로 유지보수성, 확장성을 고려할 때 **무한 확장**에 대한 간단한 예시를 들어보는 편입니다. 예를 들어 이번 과제에서는 _"페이지가 수십개이고 이벤트 리스너가 무수히 많이 늘어난다면?"_ 라는 가정을 해보았습니다.  
   <br/>

2. **무의미한 혹은 목적성에 맞지 않는 개발은 지양하자**
   과거 실무 프로젝트에서 리팩토링을 진행한 경험이 있습니다. 당시 [MVVM 패턴](https://junilhwang.github.io/TIL/CodeSpitz/Object-Oriented-Javascript/02-MVVM/#mvvm%E1%84%8B%E1%85%B4-%E1%84%80%E1%85%A2%E1%84%82%E1%85%A7%E1%86%B7)으로 설계되어 있었는데 이때 API를 호출하는 경우 불필요하게 4개의 레이어를 거쳐야 하는 과도한 추상화 문제가 존재했습니다.(Repository Interface → Repository Implement → DataSource Interface → DataSource Implement) 이는 패턴을 잘못 이해하고 무조건 General한 케이스를 적용하여 했던 문제였습니다.

   금주 멘토링에서 로직 분리와 추상화의 적절한 수준에 대해 코치님과 얘기를 나누게 되었습니다. 그 과정에서 **무조건적인 로직 분리와 추상화가 반드시 좋은 설계로 이어지지 않는다**는 코치님의 의견에 다시금 공감하게 되었습니다. 저 또한 준일 코치님의 클래스 기반 컴포넌트 블로그 글을 참고하면서 코드를 무의식적으로 복사하여 적용하는 경우가 있었습니다. 하지만 다시 고민해보니 내가 개발하고자 하는 패턴과 구조에 맞지 않게 무의미한 개발을 하고 있다는 점을 인지하였습니다.
   <br/>

3. **통일감있게 코드를 작성하자**
   팀 혹은 개인이 스스로 정한 컨벤션(Convention)을 통해 일관성을 지키자는 원칙입니다. 파일명, 변수명, 주석 등 코드의 전반적인 부분에서 일관된 스타일과 명명 규칙을 적용하기 위해 노력했습니다.
   <br/>

이번 프로젝트에서는 단일책임원칙에 따라 크게 4개의 핵심 요소를 분류하였습니다.

- 컴포넌트 : UI에 대한 요소 관리
- 컨트롤러 : 컴포넌트에 관한 상태 및 이벤트 리스너 관리
- 라우터 : 페이지, URL 라우팅 관리
- 바인딩(Binding) : 컴포넌트, 컨트롤러 등에 대한 의존성 주입

```js
class Component {
  $target;      // 상위 DOM 엘리먼트
  controller;   // 컴포넌트에 대한 컨트롤러
  ...
}

class MainPage extends Component { ... }
```

먼저 컴포넌트에 관한 추상화 클래스입니다. 해당 클래스를 확장하여 페이지 컴포넌트 등을 생성할 수 있습니다.

```js
class Controller {
  state;
  static _instance = null;

  static get instance() {...} // instance getter

  class ProfileController extends Controller { ... }
  ...
}
```

다음은 컨트롤러 추상화 클래스입니다. 컨트롤러는 앞서 설명한 바와 같이 상태 및 이벤트 리스너를 관리할 수 있습니다.

```js
setState(newState) {
    this.state = { ...this.state, ...newState };
    this.onStateChange();
  }

  setOnStateChange(callback) {
    this.onStateChange = callback;
  }
```

컨트롤러를 통해 상태를 변경했을 때 컴포넌트에서 콜백으로 등록한 렌더 함수를 작동시켜 UI 상에서 업데이트될 수 있도록 하였습니다.

```js
router.addRoute(ROUTES.MAIN, pages.main);
router.addRoute(ROUTES.PROFILE, pages.profile, profileGuard, "/login");
router.addRoute(ROUTES.LOGIN, pages.login, loginGuard, "/");
router.addRoute(ROUTES.ERROR, pages.error);
```

라우터의 경우 `main.js` 파일에서 페이지 컴포넌트와 함께 등록하여 라우트 동작 시 마다 해당하는 라우트의 페이지 컴포넌트를 렌더링하는 방식으로 구축하였습니다. 이때 라우트가드에 대한 검증 함수와 가드 이벤트가 동작할 때 리다이렉트 URL을 함께 등록할 수 있도록 해주었습니다.

```js
class Binding {
  _dependencies() {} // 의존성 주입
}

class PageBinding extends Binding {
  _dependencies() {
    new ProfileController(this.$target);
    new LoginController(this.$target);
  }
}
```

Binding의 경우 Controller를 초기 웹 로드 시 사전에 Controller 객체에 대한 싱글턴 인스턴스 의존성을 주입하여 캐싱하여 사용할 수 있도록 하는 전략을 사용하였습니다.

### 학습 효과 분석

<!-- 예시
- 가장 큰 배움이 있었던 부분
- 추가 학습이 필요한 영역
- 실무 적용 가능성
-->

직접적으로 과제와는 연관이 없지만 **테스트 기반으로 코드를 검증**하는 게 매우 흥미로웠습니다. 그동안 TDD를 개념적으로만 접했을 뿐 실제로 적용해본 경험은 없기 때문입니다. 이번 과제에서 테스트 코드를 함께 검토해보며 단순히 코드의 논리적 검증 뿐만 아니라 UI가 기대한 대로 동작하는지 화면 상에서 확인하는 방법에 대해서도 알게되었습니다. 특히 실무에서 매주 앱 배포를 할 때 CI/CD 파이프라인에 좀 더 공부하여 테스트 기반으로 자동화된 QA를 도입해볼걸 하는 아쉬움도 있었습니다..
