### Angular 개념

\- HTML 및 Typescript를 사용하여 응용 프로그램을 빌드하기 위한 플랫폼 및 프레임워크

\- Angular 앱은 NgModules 집합으로 정의됨


\- NgModules: 컴파일 컨텍스트를 제공, 관련 코드를 기능 집합으로 수집. 컴포넌트, 디렉티브, 파이프, 서비스 등의 Angular 구성요소는 모듈에 등록되어야 사용 가능함

\- Component: 템플릿과 메타데이터, 컴포넌트 클래스로 구성되며 데이터 바인딩에 의해 연결됨. 화면을 구성하는 뷰를 생성하고 관리하는 것이 주된 역할 (화면은 1개 이상의 컴포넌트가 연동되어 구성됨)

\- Directive: 애플리케이션 전역에서 사용할 수 있는 공통 관심사를 컴포넌트에서 분리하여 구현한 것. 컴포넌트의 복잡도를 낮추고 가독성을 높여줌. Structural / Attribute Directive로 구분하며, 넓은 관점에서 보면 컴포넌트 또한 디렉티브로 구분할 수 있음

\- Service: 애플리케이션의 공통 로직을 담당. 애플리케이션 전역의 공통 관심사를 컴포넌트에서 분리하기 위해 사용. 의존성 주입(DI)이 가능한 클래스로 작성

\- Router: 컴포넌트를 교체하는 방법으로 뷰를 전환하여 화면 이동을 구현

\- decorators: type을 표시하고 메타 데이터를 제공함

\- Component의 메타 데이터: 뷰를 정의하는 template과 연결

\- service클래스의 메타 데이터: Angular가 DI를 통해 구성 요소에서 사용할 수 있도록 필요한 정보를 제공

\- template: 일반 HTML과 Angular 디렉티브 및 바인딩 마크업을 결합하여 HTML을 표시하기 전에 HTML을 수정할 수 있도록 함

\- data binding: View와 Component에서 발생한 데이터의 변경 사항을 자동으로 일치시키는 Angular 기능. 바인딩 방법에 따라 양방향 바인딩과 단방향 바인딩으로 분류됨. 양방향 바인딩은 컴포넌트와 뷰 중 어느 쪽이 변하든 두 상태 정보를 자동으로 일치시켜 주는 기능. 단방향 바인딩은 컴포넌트에서 뷰쪽으로 혹은 뷰에서 컴포넌트 쪽으로 한 뱡향으로 상태 정보를 일치시켜주는 기능


### Angular 파일 구조

1\. Angular CLI

Angular CLI는 간단한 명령어를 사용하여 Angular 프로젝트의 Scaffolding(기본 골격)을 생성, 실행, 빌드 할 수 있도록 지원해주는 Command Line Interface다. Angular CLI는 개발환경 구축에 소요되는 시간을 최소화해준다.


<Angular CLI가 지원하는 기능>

\- Angular 프로젝트 Scaffolding 생성

\- Angular 프로젝트에 component, directive, service, class, interface 등의 구성요소 추가

\- 내장 개발 서버를 사용한 Angular 프로젝트 실행

\- Unit/E2E 테스트 환경 지원

\- 배포를 위한 Angular 프로젝트 패키징 지원


2\. Angular 애플리케이션의 파일 구조

2.1 src 폴더

Angular 프로젝트의 src폴더는 모든 구성요소, css, image, font와 같은 정적 파일 및 설정 파일 등 애플리케이션에 필수로 필요한 파일을 담고 있다.

\- /app: Angular 구성요소가 위치

\- /app/app.component(.ts, .html, .css, .spec.ts): 모든 component의 부모인 root component의 구성요소가 위치

\- /app/app.module.ts: Angular 구성요소를 등록하는 root module

\- /assets: image, font와 같은 정적 파일이 위치

\- /environments: build시 사용할 환경 설정 파일이 위치

\- browserlist: Autoprefixer, babel과 같은 frontend 적용 대상 브라우저를 공유하는 라이브러리 설정 파일

\- index.html: 웹 애플리케이션 실행시 처음으로 로딩되는 기본 페이지

\- karma.conf.js: karma test runner 설정 파일, ng test 명령어 실행시 참조됨

\- main.ts: 프로젝트의 메인 파일, root module을 사용하여 애플리케이션 실행

\- polyfills.ts: 크로스 브라우징을 위한 polyfill들을 import하는 파일

\- style.css: 애플리케이션 전역에 사용되는 global css 파일

\- test.ts: unit test를 위한 메인 파일

\- tsconfig.json: TypeScript 컴파일 옵션 설정 파일

\- typings.d.ts: TypeScript 타입 선언 파일


2.2 기타 설정 파일

테스트, 빌드, 배포 등을 위한 설정 파일

\- /e2e: e2e테스트 관련 파일들이 위치, ng e2e 명령어 실행시 참조

\- /node\_modules: package.json에 등록된 의존 모듈이 패키지 매니저에 의해 설치되는 저장소

\- .editorconfig: 코드 에디터 기본 설정 파일

\- .gitignore: Git 소스 관리를 제외할 대상에 대한 설정 파일

\- angular.json: Angular 및 Angular CLI를 위한 설정 파일

\- package.json: 의존 모듈 관리를 위한 패키지 매니저가 참조하는 파일

\- tsconfig.json: TypeScript 트랜스파일 옵션 설정 파일

\- tslint.json: TSlint가 사용하는 구문 체크 설정 파일, ng lint 명령어 실행시 참조


### Angular 애플리케이션의 처리 흐름

[##_Image|kage@kDiKz/btqG9TNFI8m/suTSEEAmDL3VNKCOXS92jk/img.png|alignCenter|width="100%" data-origin-width="324" data-origin-height="425" data-ke-mobilestyle="widthContent"|||_##]

\- index.html

브라우저에서 웹 애플리케이션 실행시 가장 먼저 로딩하는 파일, angular.json 파일의 index 프로퍼티 설정에 의해 로드됨

\- main.ts

프로젝트의 메인 진입 포인트로 root module을 부트스트랩하여 애플리케이션을 실행함. angular.json 파일의 main 프로퍼티 설정에 의해 로드됨

\- app.module.ts

@NgModule 데코레이터의 파라미터로 전달되는 메타데이터에 애플리케이션 전체 설정 정보를 저장한 root module, root component를 부트스트랩하여 실행함

\- app.component.ts

모든 component들의 부모 역할을 담당하는 root component. /src/index.html의 에 component view가 로드되어 브라우저에 출력됨


### Angular 생명주기와 훅 메소드

1\. 생명주기

Angular의 컴포넌트와 디렉티브는 생명주기를 갖는데, 생명주기는 Angular가 컴포넌트와 디렉티브를 생성하여 소멸하기까지의 과정을 관리하는 것을 의미한다. 이와 관련하여 생명주기 이름 앞에 ng가 붙은 훅 메소드를 제공하는데 이를 구현하여 생명주기의 각 단계에서 처리해야하는 내용을 정의할 수 있다.

Angular는 다음의 순서대로 생명주기를 관리한다.

[##_Image|kage@ceYXl4/btqHcf31EDZ/j6LtE059ibC9U180nLGXXk/img.png|alignCenter|width="100%" data-origin-width="301" data-origin-height="450" data-ke-mobilestyle="widthContent"|||_##]

2\. 생명주기 훅 메소드

생명주기 훅 메소드는 인터페이스의 형태로 제공된다. 모든 생명주기 훅 메소드를 구현할 필요는 없으며, 특정 생명주기에 대해 구현해야 할 기능이 있을 때 필요한 메소드를 구현하면 된다.

\- ngOnChanges

부모 컴포넌트에서 자식 컴포넌트의 입력 프로퍼티(@Input 데코레이터가 적용된 프로퍼티)에 바인딩한 값이 초기화 또는 변경되었을 때 호출된다. ngOnInit 호출 이전에 최소 1회 호출되며 이후 입력 프로퍼티가 변경될 때마다 호출된다. ngOnChanges 메소드는 입력 프로퍼티의 정보를 담고 있는 SimpleChanges 객체를 파라미터로 전달 받을 수 있다. 이 객체는 입력 프로퍼티의 현재값과 이전값을 포함하고 있다.

\- ngOnInit

ngOnChanges 메소드 동작 이후 입력 프로퍼티를 포함한 모든 프로퍼티의 초기화가 완료된 시점에 한 번만 호출된다. 일반적으로 프로퍼티의 초기화는 TypeScript에서는 constructor에서 하는 것이 일반적이지만 Angular에서는 ngOnInit에서 수행하는 것이 좋다.

\- ngDoCheck

ngOnInit 메소드 동작 이후 컴포넌트나 디렉티브의 상태 변화가 발생할 때마다 호출된다. 즉, 변화 감지 로직이 실행될 때 호출되는데 컴포넌트의 프로퍼티 값이 변경되거나 DOM 이벤트, Timer 함수의 tick 이벤트. Ajax 통신과 같은 비동기 처리 등이 수행되는 경우가 이에 해당된다. 하지만 ngDoCheck메소드는 모든 상태 변화가 발생할 때마다 매번 호출되어 성능에 영향을 줄 수 있기 때문에 이를 고려하여 사용해야 한다.

\* ngOnChanges와 ngDoCheck의 차이

ngOnChanges는 입력 프로퍼티의 값에 변화에 따라 호출되지만, ngDoCheck은 모든 상태 변화에 따라 호출된다.

\- ngAfterContentInit

ngContent 디렉티브를 사용하여 자식 컴포넌트에 부모 컴포넌트의 템플릿 조각을 전달한 이후 호출된다. ngDoCheck 메소드 호출 이후에 한 번만 호출되며 해당 컴포넌트에서만 동작한다.

\- ngAfterContentChecked

부모 컴포넌트가 전달한 템플릿 조각을 체크한 후 ngAfterContentInit 메소드 호출 이후에 호출된다. ngAfterContentInit 메소드와 마찬가지로 해당 컴포넌트에서만 동작한다.

\- ngAfterViewInit

컴포넌트의 View와 ViewChild가 초기화된 이후 호출된다. HTML에 작성된 내용이 화면에 모두 출력되고나서 호출되며 해당 컴포넌트에서만 동작한다.

\- ngAfterViewChecked

컴포넌트의 View와 ViewChiled를 체크한 이후 호출된다. ngAfterViewInit 메소드 호출 이후에 호출되며 컴포넌트의 View에 대한 변화 감지가 이루어질 때 동작한다. ngAfterViewInit 메소드와 마찬가지로 해당 컴포넌트에서만 동작한다.

\- ngOnDestroy

컴포넌트나 디렉티브가 소멸하기 이전에 호출된다. RxJS의 unsubscribe()메소드와 같이 메모리 누수 등을 방지하기 위한 내용들을 정의한다.
