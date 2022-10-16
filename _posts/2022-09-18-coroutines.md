---
title: "코루틴"
categories:
- 코루틴
---

# 고급 코루틴 가이드

- [docs: 코루틴](https://developer.android.com/kotlin/coroutines?hl=ko)

## 코루틴 소개: 안드로이드의 코루틴

- 비동기적으로 실행되는 코드를 간소화하기 위해 코루틴 사용
- 코틀린 버전 1.3에서 코루틴 추가됨
- 기본 스레드를 차단하여 앱이 응답하지 않게 만드는 장기 실행 작업을 관리하는 데 도움이 됨
- 코루틴 사용시 50% 이상의 생산성이 향상됨

### 기능

- 코루틴은 비동기 프로그래밍에 권장되는 솔루션
  - 경량
    - 코루틴을 실행 중인 스레드를 차단하지 않는 '정지'를 지원
    - 단일 스레드에서 많은 코루틴 실행 가능
    - '정지'는 많은 동시 작업을 지원하면서도 '차단'보다 메모리를 절약함
  - 메모리 누수 감소
    - 구조화된 동시 실행을 사용하여 범위 내에서 작업을 실행
  - 기본적으로 제공되는 취소 지원
    - 실행 중인 코루틴 계층 구조를 사용하여 자동으로 취소가 전달됨
  - Jackpack 통합

### 예시 개요

- ViewModel에서는 코루틴과 직접 연동되는 KTX 확장 프로그램 집합이 포함됨
- lifecycle-viewmodel-ktx 라이브러리임

### 종속 항목 정보

```
dependencies {
    implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.3.9'
}
```

### 백그라운드 스레드에서 실행

- 기본 스레드에서 네트워크 요청을 보내면 응답을 받을 때까지 스레드가 대기하거나 차단됨
- 스레드가 차단되는 경우 이로 인해 OS는 onDraw()를 호출할 수 없으므로 앱이 정지되고 애플리케이션 응답 없음 (ANR) 대화상자가 표시될 수 있음
- 사용자 환경을 개선하기 위해 백그라운드 스레드에서 이 작업을 실행

```kotlin
sealed class Result<out R> {
    data class Success<out T>(val data: T) : Result<T>()
    data class Error(val exception: Exception) : Result<Nothing>()
}

class LoginRepository(private val responseParser: LoginResponseParser) {
    private const val loginUrl = "https://example.com/login"

    // Function that makes the network request, blocking the current thread
    fun makeLoginRequest(
        jsonBody: String
    ): Result<LoginResponse> {
        val url = URL(loginUrl)
        (url.openConnection() as? HttpURLConnection)?.run {
            requestMethod = "POST"
            setRequestProperty("Content-Type", "application/json; utf-8")
            setRequestProperty("Accept", "application/json")
            doOutput = true
            outputStream.write(jsonBody.toByteArray())
            return Result.Success(responseParser.parse(inputStream))
        }
        return Result.Error(Exception("Cannot open HttpURLConnection"))
    }
}
```

- makeLoginRequest은 현재 동기식, 호출 스레드를 차단함

```kotlin
class LoginViewModel(
    private val loginRepository: LoginRepository
): ViewModel() {

    fun login(username: String, token: String) {
        val jsonBody = "{ username: \"$username\", token: \"$token\"}"
        loginRepository.makeLoginRequest(jsonBody)
    }
}
```

- ViewModel은 사용자가 예를 들어 버튼을 클릭할 때 네트워크 요청을 함

```kotlin
class LoginViewModel(
    private val loginRepository: LoginRepository
): ViewModel() {

    fun login(username: String, token: String) {
        // Create a new coroutine to move the execution off the UI thread
        viewModelScope.launch(Dispatchers.IO) {
            val jsonBody = "{ username: \"$username\", token: \"$token\"}"
            loginRepository.makeLoginRequest(jsonBody)
        }
    }
}
```

- 위에 ViewModel 코드랑 비교하면 viewModelScope.launch 코드가 추가됨
- viewModelScope는 ViewModel KTX 확장 프로그램에 포함된 사전 정의된 CoroutineScope 임
- launch는 코루틴을 만들고 함수 본문의 실행을 해당하는 디스페처에 전달하는 함수
- Dispatchers.IO는 이 코루틴을 I/O 작업용으로 예약된 스레드에서 실행해야 함을 나타냄

1. 앱이 기본 스레드의 View 레이어에서 login 함수를 호출
2. launch가 새 코루틴을 만들고 I/O 작업용으로 예약된 스레드에서 독립적으로 네트워크 요청이 이루어짐
3. 코루틴이 실행되는 동안 네트워크 요청이 완료되기 전에 login 함수가 계속 실행되어 결과를 반환, 편의를 위해 지금은 네트워크 응답이 무시됨

- ViewModel 소멸 시 viewModelScope 도 자동으로 취소되고 실행 중인 모든 코루틴도 취소됨


### 기본 안전을 위해 코루틴 사용

```kotlin
class LoginRepository(...) {
    ...
    suspend fun makeLoginRequest(
        jsonBody: String
    ): Result<LoginResponse> {

        // Move the execution of the coroutine to the I/O dispatcher
        return withContext(Dispatchers.IO) {
            // Blocking network request code
        }
    }
}
```

- 기본 스레드에서 UI 업데이트를 차단하지 않는 함수를 기본 안전 함수로 간주
- 기본 스레드에서 makeLoginRequest를 호출하면 UI가 차단되므로 makeLoginRequest 함수는 기본 안전 함수가 아님
- 코루틴 라이브러리의 withContext() 함수를 사용하여 코루틴 실행을 다른 스레드로 이동

- withContext(Dispatchers.IO)는 코루틴 실행을 I/O 스레드로 이동하여 호출 함수를 기본 안전 함수로 만듦, 필요에 따라 UI를 업데이트 하도록 설정
- makeLoginRequest 에는 suspend 키워드도 표시
- suspend는 코루틴 내에서 함수가 호출되도록 하는 코루틴의 방법

```kotlin
class LoginViewModel(
    private val loginRepository: LoginRepository
): ViewModel() {

    fun login(username: String, token: String) {

        // Create a new coroutine on the UI thread
        viewModelScope.launch {
            val jsonBody = "{ username: \"$username\", token: \"$token\"}"

            // Make the network call and suspend execution until it finishes
            val result = loginRepository.makeLoginRequest(jsonBody)

            // Display result of the network request to the user
            when (result) {
                is Result.Success<LoginResponse> -> // Happy path
                else -> // Show error in UI
            }
        }
    }
}
```

- makeLoginRequest가 suspend 함수이므로 코루틴은 여전히 필요
- 모든 suspend 함수는 코루틴에서 실행되어야 함

- 위 코드는 저 위에 login 예시외 몇 가지 차이가 있음
  - launch가 Disaptchers.IO 매개변수를 사용하지 않음
  - Dispatcher를 launch에 전달하지 않으면 viewModelScope에서 실행된 코루틴은 기본 스레드에서 실행됨
  - 네트워크 요청의 결과가 이제 성공 혹은 실패를 UI에 표시되도록 처리됨

1. 앱이 기본 스레드의 View 레이어에서 login() 함수를 호출함
2. launch가 기본 스레드에서 네트워크 요청을 보낼 때 새 코루틴을 만들고 코루틴을 실행함
3. 코루틴 내에서 이제 loginRepository.makeLoginRequest() 호출은 makeLoginRequest()의 withContext 블록 실행이 끝날 때까지 코루틴의 추가 실행을 정지함
4. withContext 블록이 완료되면 login() 의 코루틴이 네트워크 요청의 결과와 함꼐 기본 스레드에서 실행을 재개함

### 예외 처리

```kotlin
class LoginViewModel(
    private val loginRepository: LoginRepository
): ViewModel() {

    fun makeLoginRequest(username: String, token: String) {
        viewModelScope.launch {
            val jsonBody = "{ username: \"$username\", token: \"$token\"}"
            val result = try {
                loginRepository.makeLoginRequest(jsonBody)
            } catch(e: Exception) {
                Result.Error(Exception("Network request failed"))
            }
            when (result) {
                is Result.Success<LoginResponse> -> // Happy path
                else -> // Show error in UI
            }
        }
    }
}
```

- Repository 레이어에서 발생할 수 있는 예외를 처리하려면 코틀린에서 기본적으로 제공되는 예외 지원 사용
- 위 예시에서는 try-catch 블록 사용
