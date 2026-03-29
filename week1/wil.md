# 📘 WIL (Spring Security - Week 1)

## 1. 인증(Authentication)과 인가(Authorization)

### 🔐 인증 (Authentication)
- 사용자가 **누구인지 확인하는 과정**
- 대표적인 예: 로그인 (아이디 / 비밀번호)
- 서버가 사용자의 신원을 검증

👉 한 줄 정리  
→ "너 누구야?"

---

### 🔑 인가 (Authorization)
- 인증된 사용자가 **특정 자원에 접근할 수 있는지 확인하는 과정**
- 예: 일반 사용자는 관리자 페이지 접근 불가

👉 한 줄 정리  
→ "너 여기 들어갈 수 있어?"

---

### 🔥 핵심 차이

| 구분 | 설명 |
|------|------|
| 인증 | 사용자 신원 확인 |
| 인가 | 권한 확인 |

---

## 2. Spring Security 개념

### 🛡 Spring Security란?
- Spring에서 인증과 인가를 처리하는 보안 프레임워크
- 모든 HTTP 요청이 Controller에 도달하기 전에 **보안 검증 수행**

👉 비유  
→ 건물 보안 시스템

---

## 3. Filter 기반 동작 원리

### 🧩 Servlet Filter
- 요청과 응답을 가로채는 구조
- 전처리 / 후처리 수행

---

### 🔗 Security Filter Chain
- 여러 필터가 체인 형태로 연결됨
- 요청은 위에서 아래로 순차적으로 통과

👉 특징
- 중간에 실패하면 요청 차단
- 모든 필터 통과 시 Controller 도달

👉 비유  
→ 공항 보안 검색대

---

### ⚙ 동작 흐름

1. 클라이언트 요청
2. Security Filter Chain 진입
3. 인증 처리
4. 인가 처리
5. Controller 전달

---

## 4. 인증 처리 흐름

1. 로그인 요청 발생
2. Authentication Filter가 요청 가로챔
3. AuthenticationManager에 전달
4. DB 조회 및 검증
5. 성공 시 인증 객체 생성
6. 실패 시 예외 발생

---

## 5. 인가 처리 (권한 설정)

```java
//.authorizeHttpRequests()
//.requestMatchers("/admin/**").hasRole("ADMIN")
//.requestMatchers("/my/**").authenticated()
//.anyRequest().permitAll();
  
```

---

## 6.DTO를 Record 타입으로 설계하기

이번 과제에서는 기존 클래스 기반 DTO를 Record 타입으로 변경하였다.

수정 대상
MemberCreateRequest
MemberInfoResponse
MemberUpdateRequest
ErrorResponse

---

## 7. DTO를 Record 타입으로 설계하기

이번 과제에서는 기존 클래스 기반 DTO를 Record 타입으로 변경하였다.

### 수정 대상
- MemberCreateRequest
- MemberInfoResponse
- MemberUpdateRequest
- ErrorResponse

DTO는 데이터를 전달하는 객체이기 때문에 상태 변경이 필요 없는 경우가 대부분이다.  
따라서 불변 객체로 설계하는 것이 적합하며, Record를 사용하면 이를 간결하게 표현할 수 있다.

---

### 7-1. DTO Record 적용 코드 (전체)

```java
// MemberCreateRequest
public record MemberCreateRequest(
        String username,
        String password
) {
}

// MemberInfoResponse
public record MemberInfoResponse(
        Long memberId,
        String username
) {
    public MemberInfoResponse(Member member) {
        this(member.getId(), member.getUsername());
    }
}

// MemberUpdateRequest
public record MemberUpdateRequest(
        String username
) {
}

// ErrorResponse
public record ErrorResponse(
        String code,
        String message
) {
}

```

### 7-2. Record를 사용하는 이유

#### 1. 불변성 보장
Record는 모든 필드가 final 성격을 가지므로 객체 생성 이후 값 변경이 불가능하다.  
DTO는 데이터 전달 용도이기 때문에 불변성이 유지되는 것이 안정성 측면에서 유리하다.

#### 2. 코드 간결화
기존에는 getter, 생성자 등을 작성하거나 Lombok을 사용해야 했지만,  
Record는 선언만으로 생성자와 getter 등이 자동 생성되어 코드가 훨씬 짧아진다.

#### 3. 가독성 향상
DTO의 역할이 “데이터 전달”이라는 것이 명확하게 드러난다.

#### 4. 유지보수 용이
코드가 단순해지고 수정 포인트가 줄어들어 유지보수가 쉬워진다.

#### 5. 버그 감소
값 변경이 불가능하기 때문에 의도하지 않은 상태 변경을 방지할 수 있다.

---

### 7-3. 적용 시 주의사항

Record는 getter 방식이 다르다.

기존 코드
```java
//request.getUsername();
```

8. 느낀 점 (300자 이상)

이번 과제를 통해 인증과 인가의 개념을 단순히 이론이 아니라 실제 서비스 흐름에서 어떻게 적용되는지 이해할 수 있었다. 특히 Spring Security가 Filter Chain을 기반으로 모든 요청을 제어한다는 점이 인상 깊었고, 단순히 로그인 기능이 아니라 보안 구조 전체를 담당하는 프레임워크라는 것을 알게 되었다. 처음에는 구조가 복잡하게 느껴졌지만 요청이 필터를 순차적으로 통과한다는 흐름으로 이해하니 훨씬 명확해졌다. 또한 DTO를 Record 타입으로 변경하면서 코드가 매우 간결해지고, 불변성을 자연스럽게 보장할 수 있다는 점이 큰 장점이라고 느꼈다. 기존에는 Lombok을 사용하는 방식이 익숙했지만, Record를 사용하면 DTO의 역할이 더 명확해지고 유지보수도 쉬워진다고 생각한다. 앞으로도 상태 변경이 필요 없는 객체에는 Record를 적극적으로 활용하고 싶다.

