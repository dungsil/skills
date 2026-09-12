# Spring 모듈 구성

모듈형 Spring Boot Gradle 프로젝트를 설계·이전·검토할 때 읽는다.

## 기본 원칙과 컨텍스트 경계

- 템플릿의 제품명보다 책임을 기준으로 모듈을 구성한다.
- 실행 프로세스는 `apps/`, 재사용 모듈은 `packages/`, Gradle 관례 플러그인은 `build-logic/`에 둔다.
- 작은 프로젝트는 작게 유지한다. 동작 보호, 소유권 명확화나 반복되는 인프라 구성을 줄이는 경계가 있을 때만 나눈다.
- 템플릿의 앱 이름, 플랫폼·프로퍼티·환경 변수 접두사, 조직명과 도메인명을 대상 프로젝트에 맞춘다.
- 바운디드 컨텍스트는 테이블이나 컨트롤러 그룹이 아닌 업무 능력과 언어에서 찾는다.
- 용어, 규칙, 출시 주기, 소유권이나 외부 통합이 독립적으로 달라지면 컨텍스트를 나눈다. 항상 함께 변경하고 이해하는 객체는 같은 모델에 둔다.
- 이벤트 스토밍 결과, 유스케이스 집합, 도메인 이벤트와 정책 차이로 경계를 확인한다.
- 유비쿼터스 언어는 해당 컨텍스트 안에 유지한다. 여러 컨텍스트가 의도적으로 공유할 때만 공유 커널로 옮긴다.
- 고유한 언어나 동작이 없는 단일 엔티티를 위해 컨텍스트를 만들지 않는다.

## 기본 디렉터리 구성

```text
.
|-- apps/<app>/
|-- packages/shared/
|-- packages/<platform>-spring-boot-starter/
|-- packages/<platform>-spring-boot-starter-<concern>/
|-- packages/<context>-domain/
|-- packages/<context>-usecase-<usecase>/
|-- packages/<context>-adapter-rest/
|-- packages/<context>-adapter-persistence/
|-- build-logic/src/main/kotlin/
|-- gradle/
|-- gradlew
|-- gradlew.bat
|-- settings.gradle.kts
|-- build.gradle.kts
|-- gradle.properties
|-- .editorconfig
|-- .env.example
`-- .gitignore
```

## 모듈 역할과 의존 방향

- `apps/<app>`은 애플리케이션, 워커, 배치, 마이그레이션 실행기, CLI 같은 런타임이다. 필요한 스타터·공유 커널·공통·지원·도메인·유스케이스·어댑터만 조합한다.
- 앱에 재사용 인프라 동작, 도메인 규칙, 유스케이스 조합, 어댑터 매핑, 공유 계약이나 공통 유틸리티를 두지 않는다.
- `packages/shared`는 여러 컨텍스트가 합의한 작고 안정적인 개념·값 객체·계약·정책을 담는 DDD 공유 커널이다. 기술 공통 모듈에 `shared-*`를 사용하지 않는다.
- 의존성 종류, 재사용 범위나 프레임워크 결합 때문에 공유 커널이 부적절하면 명시적인 공통·지원 모듈로 분리한다. 프레임워크 의존성이 적은 기술 계약은 `common-<concern>`, 기술·계층 지원은 `<concern>-support`로 이름을 짓는다.
- 도메인 지향 공통 모듈은 어댑터·앱에 의존하지 않는다. 영속성 공통 모듈은 JPA API·Spring Data JPA에 의존할 수 있지만 재사용 가능한 영속성 기반 동작만 담는다.
- `packages/<context>-domain`은 도메인 모델, 값 객체, 도메인 서비스, 검증 오류와 예외를 소유한다. 유스케이스·어댑터·실행 앱·프레임워크 영속성·HTTP·직렬화·Spring 런타임에 의존하지 않는다.
- `packages/<context>-usecase-<usecase>`는 하나 또는 밀접한 유스케이스 묶음의 조합, 명령·쿼리, 포트와 애플리케이션 예외를 소유한다. 도메인과 포트에 의존하며 구체 어댑터나 실행 앱에는 의존하지 않는다.
- 도메인과 유스케이스를 나눌 실익이 없는 작은 컨텍스트에서만 `<context>-core`를 사용한다. 헥사곤 내부의 모든 것을 넣는 기본 위치로 삼지 않는다.
- `packages/<context>-adapter-<boundary>`는 REST, 영속성, 메시징, 외부 클라이언트, 캐시, 파일, 스케줄러 등의 외부 어댑터다. 필요한 도메인·유스케이스와 공유 커널·공통·지원·스타터에만 의존한다.
- `packages/<platform>-spring-boot-starter[-<concern>]`는 하나의 인프라 관심사에 대한 재사용 Spring Boot 자동 설정을 제공한다. 도메인 동작을 노출하지 않는다.
- `build-logic`은 공통 Java, 공유 커널, 기술 공통 모듈, 앱과 스타터의 Gradle 관례 플러그인을 담는다.
- 공유 커널과 공통·지원 모듈은 기능 모듈보다 작게 유지한다. 모든 앱에 모든 스타터·어댑터를 기본으로 추가하지 않는다.

## 이름과 패키지

- Gradle 모듈 이름은 하이픈으로 구분한다.
- 공유 커널은 `shared`, 기술 공통·지원 모듈은 `common-validation`, `common-pagination`, `persistence-support` 등 역할이 드러나는 이름을 사용한다.
- 도메인은 `<context>-domain`, 독립할 만큼 동작이나 의존성이 있는 유스케이스는 `<context>-usecase-<usecase>`, 어댑터는 `<context>-adapter-<technology-or-boundary>`로 이름을 짓는다.
- 자체 스타터는 `<platform>-spring-boot-starter`, `<platform>-spring-boot-starter-<concern>`으로 이름을 짓는다. 실제 프로젝트 관례일 때만 외부 기술명을 사용한다.
- 책임 이름을 쓸 수 있으면 `domain`, `api`, `infra`, 단순한 `common` 같은 포괄적인 모듈명을 피한다.
- 모듈 이름은 패키징 책임을, Java 패키지 이름은 코드 책임을 나타낸다.
- Java 패키지 구간은 모듈명보다 짧게 유지한다. 모듈 경로에 어댑터 역할이 있으면 `<base>.<context>.adapter.rest`보다 `<base>.<context>.rest`를 우선한다.
- `<base>.shared.<kernel-concept>`, `<base>.common.<concern>`, `<base>.<concern>.support`, `<base>.boot`, `<base>.rest`, `<base>.<context>.domain`, `<base>.<context>.application`, `<base>.<context>.persistence`, `<base>.<app>`처럼 간결한 패키지를 사용한다.

## 소스 세트와 Gradle

- 기본 소스 경로는 `src/main/java`, `src/test/java`다.
- 여러 모듈에서 공유하는 픽스처에만 `src/testFixtures/java`를 사용한다. 앱 테스트가 엔티티나 어댑터 픽스처를 필요로 하면 해당 타입을 소유한 어댑터에 둔다. 단일 어댑터 데이터를 위한 별도 픽스처 모듈은 만들지 않는다.
- Gradle 프로젝트명이 평평해도 앱과 패키지 디렉터리는 분리한다. `projectDir`로 `apps/<app>` 또는 `packages/<module>`에 연결한다.
- `settings.gradle.kts`에서 `build-logic`을 포함 빌드로 사용한다.
- 모듈 `build.gradle.kts`는 관례 플러그인 하나, 모듈 의존성과 `java-test-fixtures` 같은 필요한 추가 설정만 선언한다.
- `convention.java.gradle.kts`에 Java 도구 체인, 컴파일러 인자, 인코딩, JSpecify, Lombok 애너테이션 처리, JUnit, Mockito, JaCoCo, 테스트 기본값, 재현 가능한 JAR, Spring Boot BOM과 `useJUnitPlatform()`을 둔다.
- 공유 커널에는 `<platform>.shared.gradle.kts`, 기술 공통 모듈에는 `<platform>.commons.gradle.kts`, 앱에는 `<platform>.app.gradle.kts`, 스타터에는 `<platform>.starter.gradle.kts`를 사용한다.
- 관심사별 스타터 관례에는 기본 스타터 의존성과 Spring Boot 테스트 지원을 포함한다.

## 빌드와 환경

- 루트 계약에는 보통 `.editorconfig`, `.env.example`, `.gitignore`, `gradle.properties`, `settings.gradle.kts`, 루트 `build.gradle.kts`, `build-logic/`, `gradle/`, `gradlew`, `gradlew.bat`가 포함된다.
- 전역 Gradle 설치에 의존하지 않도록 래퍼 파일을 커밋한다.
- 런타임 환경 변수는 `.env.example`에 문서화하고 실제 값은 버전 관리에서 제외한다.
- 명시적으로 관리하는 플러그인 버전과 Boot가 관리하지 않는 의존성 버전은 `gradle.properties`에 둔다.
- 프로젝트가 버전 카탈로그를 표준으로 삼지 않으면 프레임워크 버전은 Spring Boot BOM을 우선한다. 카탈로그와 `gradle.properties`를 임의로 혼용하지 않는다.
- BOM이나 관례 설정이 버전을 관리하면 모듈에서는 버전 없는 의존성 좌표를 사용한다.
- UTF-8, LF, 공백, 들여쓰기 `2`, 최대 줄 길이 `120`, 마지막 개행과 줄 끝 공백 제거를 사용한다. 필요하면 `application*.yml`의 줄 길이만 예외로 둔다.
- 루트 키·값 파일에는 `### Section ###` 헤더를 사용하고 Gradle 그룹, 루트 프로젝트명, Java 버전과 컴파일러 인자의 의미를 구분한다.
- Spring MVC나 생성자·매개변수 이름 바인딩이 메타데이터에 의존하면 `java.compiler-args=-parameters`를 유지한다.
- 로컬 환경 파일, Gradle 캐시, 빌드 결과, IDE 메타데이터와 로컬 생성 도구 파일은 무시한다. 저장소 스킬을 관리하면 에이전트 임시 공간은 무시하되 스킬 디렉터리를 명시적으로 허용한다.

## Spring 런타임 구성

- 앱은 얇게 유지하고 재사용 Spring 동작은 스타터에 둔다.
- Spring 동작과 인프라 연결은 스타터·앱 설정 클래스의 Java 설정을 우선한다. 환경별 스칼라 값이나 Java로 명확하게 설정하기 어려운 예외적 연결점은 `application.yml`에 둔다.
- 실행 앱마다 작은 메인 클래스 하나를 우선한다. 공통 부트 도우미나 스타터 진입점이 있으면 공통 설정을 위임한다.
- 스캔 패키지와 부트 기본값을 일치시켜야 하면 공통 메인 애너테이션이나 메타 애너테이션을 사용한다. 프로젝트 관례가 있으면 `MainClass` 접미사를 사용한다.
- 공통 `SpringApplication` 설정을 감싸는 재사용 부트 도우미는 `Application` 접미사로 이름을 짓는다.
- 배너, 지연 초기화, keep-alive, 로깅 초기화, 컴포넌트·엔티티·저장소 스캔, 데이터소스, JPA 설정, 저장소 활성화, 감사와 환경 변수 대체 정책은 스타터·도우미에 모아 앱의 반복을 줄인다.
- 마이그레이션이 API 런타임과 다른 생명주기를 요구하면 전용 실행 앱에 도구와 실행을 둔다. 모든 런타임 앱에 도구를 추가하지 않는다.
- IDE 전용 설정을 기준으로 삼지 않는다. Gradle이나 프로젝트 래퍼로 실행할 수 있어야 한다.

## 스타터 범위

- 스타터마다 REST, 영속성, 캐시나 공통 앱 초기화 같은 인프라 관심사 하나를 담당한다.
- 자동 설정, 기본 빈, 프레임워크 통합, 프로퍼티 바인딩, 오류 처리, 테스트 지원과 해당 관심사에 직접 결합된 운영·문서화·관측 도구를 포함할 수 있다.
- 의존성 그래프, 생명주기, 설정 범위나 소비 모듈이 실질적으로 다르면 별도 스타터로 나눈다.
- 도메인 모델, 유스케이스 조합과 바운디드 컨텍스트 정책을 넣지 않는다.
- 해당 인프라 없이도 유용한 지원 코드는 책임에 따라 공유 커널, 공통·지원 모듈이나 더 좁은 스타터로 옮긴다.
