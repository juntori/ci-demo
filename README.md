# GitHub Actions CI Practice

Spring Boot 프로젝트에서 **GitHub Actions를 이용해 CI(Continuous Integration)를 자동화하는 과정을 실습한 프로젝트**입니다.

코드를 GitHub에 Push하거나 Pull Request를 생성하면 GitHub Actions가 자동으로 테스트와 빌드를 수행하도록 구성했습니다.

---

## CI란?

CI(Continuous Integration)는 개발자가 작성한 코드를 저장소에 반영할 때마다 **테스트와 빌드를 자동으로 실행하여 코드에 문제가 없는지 확인하는 과정**입니다.

```text
코드 작성
   ↓
Git Push / Pull Request
   ↓
GitHub Actions 실행
   ↓
자동 테스트
   ↓
자동 빌드
```

수동으로 매번 테스트와 빌드를 실행하지 않아도 GitHub Actions가 이를 대신 수행합니다.

---

## GitHub Actions

GitHub Actions는 GitHub Repository에서 발생하는 이벤트를 기준으로 작업을 자동 실행할 수 있는 기능입니다.

Workflow 파일은 다음 위치에 작성했습니다.

```text
.github/workflows/ci.yml
```

현재는 `main` 브랜치에 Push하거나 Pull Request가 생성되면 CI가 실행됩니다.

```yaml
on:
  push:
    branches: [main]

  pull_request:
    branches: [main]
```

---

## CI 실행 과정

현재 Workflow는 다음 순서로 진행됩니다.

```text
Git Push / Pull Request
        ↓
1. Checkout
        ↓
2. Java 환경 설정
        ↓
3. Gradle Cache
        ↓
4. Test
        ↓
5. Build
```

### 1. Checkout

```yaml
- name: Checkout code
  uses: actions/checkout@v6
```

GitHub Repository에 있는 코드를 GitHub Actions의 실행 환경인 **Runner**로 가져옵니다.

```text
GitHub Repository
        ↓
      Checkout
        ↓
GitHub Actions Runner
```

---

### 2. Java 환경 설정

Spring Boot 프로젝트를 테스트하고 빌드하기 위해 Java 환경을 준비합니다.

```yaml
- name: Setup Java 21
  uses: actions/setup-java@v5
  with:
    java-version: '21'
    distribution: 'corretto'
```

GitHub Actions Runner에는 프로젝트 실행에 필요한 환경이 항상 준비되어 있는 것이 아니기 때문에 필요한 Java 버전을 직접 설정합니다.

---

### 3. Gradle Cache

```yaml
- name: Cache Gradle packages
  uses: actions/cache@v5
```

Gradle이 사용하는 Dependency와 관련 파일을 Cache에 저장합니다.

```text
첫 번째 실행

Dependency 다운로드
       ↓
Cache 저장


다음 실행

Cache 재사용
       ↓
CI 실행 시간 감소
```

매번 같은 Dependency를 다시 다운로드하는 것을 줄이기 위한 설정입니다.

---

### 4. Test

```yaml
- name: Run tests
  run: ./gradlew test
```

작성한 코드가 정상적으로 동작하는지 자동으로 테스트합니다.

```text
코드
 ↓
Test
 ├─ 성공 → 다음 단계
 └─ 실패 → CI 실패
```

테스트가 실패하면 GitHub Actions에서 실패 상태를 확인할 수 있습니다.

---

### 5. Build

테스트를 통과한 코드는 Spring Boot 애플리케이션으로 빌드합니다.

```yaml
- name: Build JAR
  run: ./gradlew bootJar
```

이를 통해 **테스트를 통과한 코드만 정상적으로 빌드되는지 자동으로 확인**할 수 있습니다.

---

## GitHub Actions Runner

GitHub Actions의 작업은 **Runner**라는 별도의 실행 환경에서 동작합니다.

현재 프로젝트에서는 Ubuntu 환경을 사용합니다.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
```

즉,

```text
GitHub
   ↓
Ubuntu Runner 생성
   ↓
Repository 코드 가져오기
   ↓
Java 설치
   ↓
Test
   ↓
Build
```

순서로 작업이 실행됩니다.

Workflow 실행이 끝나면 Runner도 종료됩니다.

---

## CI 자동화 전후

### 자동화 전

```text
코드 수정
 ↓
직접 테스트 실행
 ↓
직접 빌드 실행
 ↓
문제 확인
```

### GitHub Actions 적용 후

```text
코드 수정
 ↓
Git Push
 ↓
GitHub Actions
 ↓
Test + Build 자동 실행
 ↓
결과 확인
```

반복적으로 수행해야 하는 테스트와 빌드를 자동화할 수 있습니다.

---

## 실습 내용

이번 프로젝트를 통해 다음 내용을 실습했습니다.

* CI의 기본 개념
* GitHub Actions Workflow 작성
* Push / Pull Request를 이용한 CI 실행
* GitHub Actions Runner
* Repository Checkout
* Java 실행 환경 설정
* Gradle Cache
* 테스트 자동화
* Spring Boot 빌드 자동화

---

## 핵심 정리

```text
CI
=
코드가 변경될 때마다
테스트와 빌드를 자동으로 실행하여
문제가 없는지 지속적으로 확인하는 과정
```

이 프로젝트에서는 **GitHub Actions를 이용하여 Spring Boot 프로젝트의 테스트와 빌드를 자동화하는 CI 환경을 구성했습니다.**
