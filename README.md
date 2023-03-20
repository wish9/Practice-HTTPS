블로깅 주소
https://velog.io/@wish17/%EC%BD%94%EB%93%9C%EC%8A%A4%ED%85%8C%EC%9D%B4%EC%B8%A0-%EB%B0%B1%EC%97%94%EB%93%9C-%EB%B6%80%ED%8A%B8%EC%BA%A0%ED%94%84-61%EC%9D%BC%EC%B0%A8-%EC%9D%B8%EC%A6%9D%EB%B3%B4%EC%95%88-%EA%B8%B0%EC%B4%88

# Section4 - [인증/보안] 기초

## HTTPS

> HTTPS = HTTP + Secure
- HTTP 프로토콜 내용을 암호화한 것
    - SSL 혹은 TLS라는 알고리즘을 이용해 암호화

### 암호화


>- 제 3자가 서버와 클라이언트가 주고받는 정보를 탈취할 수 없도록 하는 것
    - 데이터를 탈취 당하더라도 그 내용을 알아볼 수 없게 하는 것

#### 개념 설명

- ``public-key``와 ``private-key``는 한 세트로 존재해야만 한다.

``public-key``

- ``public-key``는 누구나 알 수 있고 탈취당해도 상관 없는 키다.
- ``public-key``를 이용해 암호화 할 수 있다.
- ``public-key``를 이용해 암호화 한 것은 같은 세트인 ``private-key``로만 복호화 할 수 있다.

``private-key``

- ``private-key``는 탈취 당하면 안되는 키다.
- 따라서 ``private-key``는 전송하지 않고 생성한곳에서 갖고 있는다.
- ``private-key``를 이용해 암호화 할 수 있다.
- ``private-key``로 암호화 한 것은 같은 세트인 ``public-key``로 복호화 가능하다.
- 하지만 ``public-key``는 누구나 알 수 있는 ``key``이기 때문에 ``private-key``로 암호환 한 것은 암호화라고 볼 수 없다. 따라서 이것은 암호화 하는게 아니라 ``전자서명``에 해당한다.

``대칭키 방식``
- 양쪽이 공통의 비밀 키를 공유하여 데이터를 암호화 및 복호화하는 것

``비대칭키 방식``
- 각각 공개키와 비밀키(개인키)를 가지고 상대가 나의 공개키로 암호화한 데이터를 개인이 가진 비밀키로 복호화하는 것

***

### HTTPS 방식의 Client <-> Server 통신 과정

    
- 비대칭키 방식과 대칭키 방식을 혼용하여 암호화
    - 클라이언트와 서버가 데이터를 주고받을 때는 대칭키를 사용
    (대칭키를 사용하여 데이터를 암호화 및 복호화하는 것이 훨씬 컴퓨터에 부담을 덜 주기 때문)
    - 대칭키를 주고받을 때는 비대칭키 사용

[![](https://velog.velcdn.com/images/wish17/post/b794ca8b-4d02-402b-af58-a33adb771b28/image.png)](https://yozm.wishket.com/magazine/detail/1852/)

위 그림과 같이 비대칭키 방식으로 대칭키를 암호화한 뒤 서버측에 전달해주고 이후에 대칭키를 이용해 통신을 한다.



~~#### 비대칭키 방식만을 사용한다면?~~

~~클라이언트는 서버의 공개키를 사용하여 데이터를 암호화하고, 서버는 자신의 개인키를 사용하여 이를 복호화한다. 반대로, 서버가 클라이언트로 데이터를 전송할 때는, 서버는 클라이언트의 공개키를 사용하여 데이터를 암호화하고, 클라이언트는 자신의 개인키를 사용하여 이를 복호화해야 한다.~~
이 방법은 매우 비효율적이다.

~~### 암호화 예시~~

~~100이라는 데이터를 클라이언트에서 서버로 보낸다고 생각해 보자.
public-key는 ``*2``이라고 하면 클라이언트에서 서버로 데이터를 보낼때 ``200``과 public-key(``*2``)가 암호화 된 ``?2``가 함께 보내게 된다.``200``, ``?2``를 데이터로 받은 서버는 private-key를 이용해 ``?2``가 ``*2``인 것을 알 수 있게 된다.따라서 ``클라이언트 -> 서버`` 데이터 이동 상태에서 데이터가 탈취된다고 해도 ``?=*``이라는 사실을 서버만 알고 있기 때문에 탈취자는 온전한 데이터가 뭔지 유추하기 매우 어렵다.~~

***

### 인증서

- 브라우저가 서버의 응답과 함께 전달된 인증서를 확인할 수 있다.
    - 인증서가 서버의 신원을 보증
    - 이때 보증하는 제 3자를 Certificate Authority, CA라고 부른다.

***

### [실습] 인증서 발급 및 HTTPS 서버 구현

1. [Spring Initializr](https://start.spring.io/)에서 프로젝트 생성
    -  Spring Web 필수

2. [mkcert](https://github.com/FiloSottile/mkcert) 설치
	- 로컬 환경에서만 인증 가능한 사설 인증서 (연습용으로 쓰는거임)
    -  배포 환경에서는 사용 X

```
$ cd {프로젝트 저장 위치}
혹은 설치 후 mv로 옮겨도 됨
-------------------------
$ sudo apt-get update # 업데이트 필요시에만
-------------------------
$ sudo apt install libnss3-tools
$ wget -O mkcert https://github.com/FiloSottile/mkcert/releases/download/v1.4.3/mkcert-v1.4.3-linux-amd64
$ chmod +x mkcert
$ sudo cp mkcert /usr/local/bin/
```
3. 인증서 생성

```
$ mkcert -install # CA 생성
$ mkcert -pkcs12 localhost # PKCS12 형식 인증서 생성
```
이렇게 생생한 ``localhost.p12`` 인증서 파일을 resource 파일로 이동시켜준다.

![](https://velog.velcdn.com/images/wish17/post/6521cfde-45ed-4072-bdcc-d76870e5b3fa/image.png)

4. application.properties에서 관련 설정을 추가

```
server.ssl.key-store=classpath:localhost.p12  #  -> 인증서 경로를 적습니다.
server.ssl.key-store-type=PKCS12              #  -> 인증서 형식을 적습니다.
server.ssl.key-store-password=changeit        #  -> 인증서 비밀번호를 적습니다.

# 여기서 비밀번호인 changeit은 비밀번호를 설정하지 않았을 때의 기본값입니다.
# 인증서 비밀번호는 인증서를 생성할 때 설정하거나 생성 후 변경해줄 수 있습니다.
```


5. 애플리케이션 실행

![](https://velog.velcdn.com/images/wish17/post/5971ac21-36dd-4080-b769-2dfeb2cea0e1/image.png)


``8080 (https)`` 확인 가능

cf. 인증서를 찾지 못하면 아래와 같이 중단 된다.

![](https://velog.velcdn.com/images/wish17/post/ef9188b8-0dfe-42a6-b69d-d86dc4ef4a12/image.png)



***

## Hashing

> 어떠한 데이터에 임의의 연산을 적용하여 다른 데이터로 변환하는 것
- Hashing한 데이터를 DB에 저장함으로써 DB의 데이터가 유출되더라도 원래의 데이터를 알 수 없도록 할 수 있다.

### Hashing 적용을 위한 세 가지 철칙

``해쉬값 = 암호화 시킨 값``이라고 생각 하고 읽으면 이해가 쉽다.

1. 해독(decoding)할 때는 긴 시간이 걸려야 하지만, 반대로 해시값을 만드는 데엔 오래 걸리지 않아야 한다.

2. 모든 값은 고유한 해시값을 가져야 한다.
    - 서로 다른 데이터가 같은 해시값을 갖으면 안된다.

3. 아주 작은 단위의 변경이 있더라도 반환되는 해시값은 완전히 다른 값을 가져야 한다.
	- ``Hello``와 ``hello``는 다른 해시값을 가져야 한다.
    - 2번이랑 사실상 같은 말

### Salt

> 기존 데이터에 추가값을 더한 다음에 암호화 하는 것
- ``암호화 하려는 값`` + ``Salt 값`` = ``hash 값``
- 암호화 알고리즘이 노출되더라도 원본 비밀번호 데이터를 보호할 수 있는 안전장치 역할

#### Salt 사용 시 주의점

1. Salt는 유저와 패스워드 별로 유일한 값을 가져야 한다.
2. 사용자 계정을 생성할 때와 비밀번호를 변경할 때 마다 새로운 임의의 Salt를 사용해서 해싱해야 한다.
3. Salt는 재사용하지 말아야 한다.
4. Salt는 DB의 유저 테이블에 같이 저장되어야 한다.

***

## Cookie

> 서버에서 클라이언트에 데이터를 저장하는 방법 중 하나
- 서버는 클라이언트에서 쿠키를 이용하여 데이터를 가져올 수 있다.
- 특정 조건들이 만족하는 경우에만 다시 가져올 수 있으며 이러한 특정 조건을 **쿠키 옵션**으로 표현할 수 있다.

### 쿠키 옵션

1. Domain (도메인 옵션)
- 만약 쿠키 옵션에서 도메인 정보가 존재한다면 클라이언트에서는 쿠키의 도메인 옵션과 서버의 도메인이 일치해야만 쿠키를 전송할 수 있다.
- ``https://www.coupang.com/np/categories/393760``와 같은 경우에는 ``coupang`` 도메인에서만 쿠키 전송 가능하게 하는 쿠키 옵션이다.

2. Path (경로 옵션)

- Path(세부 경로)는 서버가 라우팅할 때 사용하는 경로
    - ``https://www.coupang.com/np/categories/393760`` 중 ``/np/categories``가 path다.
    
- ``/xxxxp/categories``의 경우 쿠키 전송 불가, ``/np/categories/xxx/xxxx``의 경우에는 전송 가능하게 제한할 수 있는 쿠키 옵션이다.

3. MaxAge or Expires 옵션
- 쿠키가 유효한 기간을 설정하는 옵션
- MaxAge는 앞으로 몇 초 동안 쿠키가 유효한지 설정하고, Expires는 Date를 지정하여 언제까지 유효한지 설정한다.

> 세션 쿠키
- MaxAge 또는 Expires 옵션이 없는 쿠키
- 브라우저가 실행 중일 때 사용할 수 있는 임시 쿠키 브라우저를 종료하면 해당 쿠키는 삭제된다.

> 영속성 쿠키
- 브라우저의 종료 여부와 상관없이 MaxAge 또는 Expires에 지정된 유효시간만큼 사용가능한 쿠키

4. Secure 옵션
- HTTPS 프로토콜을 이용하여 통신하는 경우에만 쿠키를 전송할 수 있도록 결정하는 옵션.

5. HttpOnly 옵션
- 자바스크립트에서 브라우저의 쿠키에 접근할 수 있는지 결정하는 옵션
    - 명시되지 않는 경우 기본값은 ``false``다.
    - ``false``인 경우 자바스크립트에서 쿠키에 접근이 가능
    	- ``false``인 경우 XSS 공격에 취약할 수 있으므로 주의해야 한다.

6. SameSite 옵션
- Cross-Origin 요청을 받은 경우 요청에서 사용한 메소드와 해당 옵션(e.g. GET, POST, PUT, PATCH …)의 조합으로 서버의 쿠키 전송 여부를 결정하게 된다.
- 사용할 수 있는 옵션
    - Lax: Cross-Origin 요청이면 'GET' 메소드에 대해서만 쿠키를 전송할 수 있다.
    - Strict: Cross-Origin이 아닌 same-site 인 경우에만 쿠키를 전송할 수 있다.
    - None: 항상 쿠키를 보내줄 수 있다. 다만 쿠키 옵션 중 Secure 옵션이 필요하다.

> Cross-Origin 요청
-  현재 페이지의 도메인, 포트, 프로토콜 중 하나라도 다른 서버의 요청


### 쿠키를 이용한 상태 유지

쿠키의 특성을 이용하여 서버는 클라이언트에 인증정보를 담은 쿠키를 전송하고, 클라이언트는 전달받은 쿠키를 요청과 같이 전송하여 Stateless 한 인터넷 연결을 Stateful 하게 유지할 수 있다.

but 기본적으로는 쿠키는 오랜 시간 동안 유지될 수 있고, 자바스크립트를 이용해서 쿠키에 접근할 수 있기 때문에 쿠키에 민감한 정보를 담는 것은 위험하다!!

### Cookie 초간단 정리

domain - 서버와 요청의 도메인이 일치하는 경우 쿠키 전송
path - 서버 요청의 세부 경로가 일치하는 경우 쿠키 전송
maxage/expires - 쿠키의 유효기간 설정
httpOnly - 스크립트의 쿠키 접근 가능 여부 설정
secure - HTTPS에서만 쿠키 전송 여부 설정
sameSite - 같은 사이트에서만 쿠키를 사용할 수 있게 하는 설정



***

## Session

> 사용자가 인증에 성공한 상태
- 주로 서버의 in-memory, 또는 세션 스토어(redis 등과 같은 트랜잭션이 빠른 DB)에 저장

[![](https://velog.velcdn.com/images/wish17/post/a7494248-c121-41d6-84d4-75645e43e7f5/image.png)](https://hudi.blog/session-based-auth-vs-token-based-auth/)

- 세션이 만들어지면, 각 세션을 구분할 수 있는 세션 아이디가 만들어 진다.
    - 보통 클라이언트에 세션 성공(ex.로그인 성공)을 증명할 수단으로써 세션 아이디를 전달한다.
- 웹사이트에서 로그인을 유지하기 위한 수단으로 쿠키를 사용한다.
    - 쿠키에는 서버에서 발급한 세션 아이디를 저장

### 로그아웃

세션 아이디가 담긴 쿠키가 클라이언트에 저장되어 있다. 서버는 해당 쿠키에 있는 세션아이디를 통해 사용자 인증을 진행하기 때문에 공공PC에서는 반드시 로그아웃을 해야 하는 것이다.
(쿠키가 지워지기 전까지 로그인한 상태로 인식되기 떄문)

따라서 로그아웃은 아래 두 작업이 진행되어야 한다.
1. 서버 : 세션 정보를 삭제
2. 클라이언트 : 쿠키를 갱신


***

## Cookie와 Session 차이 정리

<table style="border-collapse: collapse; height: 158px;" data-ke-align="alignLeft">
<tbody>
<tr style="height: 20px;">
<td style="border: 1px solid #d8d8d8; border-left: 0px; width: 20.7752%; height: 20px;">&nbsp;</td>
<td style="padding: 7px; border: 1px solid #d8d8d8; width: 38.217%; height: 20px; text-align: center; line-height: 20px;"><b>쿠키(Cookie)</b></td>
<td style="padding: 7px; border: 1px solid #d8d8d8; width: 41.0077%; height: 20px; text-align: center; border-right: 0px; line-height: 20px;"><b>세션(Session)</b></td>
</tr>
<tr style="height: 20px;">
<td style="padding: 7px; border: 1px solid #d8d8d8; border-left: 0px; width: 20.7752%; height: 20px; text-align: center; line-height: 20px;"><b>저장 위치</b></td>
<td style="padding: 7px; border: 1px solid #d8d8d8; width: 38.217%; height: 20px; line-height: 20px;"><span style="color: #6f45ec;">클라이언트(=접속자 PC)</span></td>
<td style="padding: 7px; border: 1px solid #d8d8d8; width: 41.0077%; height: 20px; border-right: 0px; line-height: 20px;"><span style="color: #6f45ec;">웹 서버</span></td>
</tr>
<tr style="height: 20px;">
<td style="padding: 7px; border: 1px solid #d8d8d8; border-left: 0px; width: 20.7752%; height: 20px; text-align: center; line-height: 20px;"><b>저장 형식</b></td>
<td style="padding: 7px; border: 1px solid #d8d8d8; width: 38.217%; height: 20px; line-height: 20px;">text</td>
<td style="padding: 7px; border: 1px solid #d8d8d8; width: 41.0077%; height: 20px; border-right: 0px; line-height: 20px;">Object</td>
</tr>
<tr style="height: 20px;">
<td style="padding: 7px; border: 1px solid #d8d8d8; border-left: 0px; width: 20.7752%; height: 20px; text-align: center; line-height: 20px;"><b>만료 시점</b></td>
<td style="padding: 7px; border: 1px solid #d8d8d8; width: 38.217%; height: 20px; line-height: 20px;">쿠키 저장시 설정<br>(브라우저가 종료되도, 만료시점이 지나지 않으면 자동삭제되지 않음)</td>
<td style="padding: 7px; border: 1px solid #d8d8d8; width: 41.0077%; height: 20px; border-right: 0px; line-height: 20px;">브라우저 종료시 삭제<br>(기간 지정 가능)</td>
</tr>
<tr style="height: 20px;">
<td style="padding: 7px; border: 1px solid #d8d8d8; border-left: 0px; width: 20.7752%; height: 20px; text-align: center; line-height: 20px;"><b>사용하는 자원(리소스)</b></td>
<td style="padding: 7px; border: 1px solid #d8d8d8; width: 38.217%; height: 20px; line-height: 20px;">클라이언트 리소스</td>
<td style="padding: 7px; border: 1px solid #d8d8d8; width: 41.0077%; height: 20px; border-right: 0px; line-height: 20px;">웹 서버 리소스</td>
</tr>
<tr style="height: 18px;">
<td style="padding: 7px; border: 1px solid #d8d8d8; border-left: 0px; width: 20.7752%; height: 18px; text-align: center; line-height: 20px;"><b>용량 제한</b></td>
<td style="padding: 7px; border: 1px solid #d8d8d8; width: 38.217%; height: 18px; line-height: 20px;">총 300개<br>하나의 도메인 당 20개<br>하나의 쿠키 당 4KB(=4096byte)</td>
<td style="padding: 7px; border: 1px solid #d8d8d8; width: 41.0077%; height: 18px; border-right: 0px; line-height: 20px;">서버가 허용하는 한 용량제한 없음.<br> but 서버의 가용 메모리가 줄어들어 서버의 성능이 안좋아질 수 있다.</td>
</tr>
<tr style="height: 20px;">
<td style="padding: 7px; border: 1px solid #d8d8d8; border-left: 0px; width: 20.7752%; height: 20px; text-align: center; line-height: 20px;"><b>속도</b></td>
<td style="padding: 7px; border: 1px solid #d8d8d8; width: 38.217%; height: 20px; line-height: 20px;"><span style="color: #6f45ec;">세션보다 빠름</span></td>
<td style="padding: 7px; border: 1px solid #d8d8d8; width: 41.0077%; height: 20px; border-right: 0px; line-height: 20px;">쿠키보다 느림</td>
</tr>
<tr style="height: 20px;">
<td style="padding: 7px; border: 1px solid #d8d8d8; border-left: 0px; width: 20.7752%; height: 20px; text-align: center; line-height: 20px;"><b>보안</b></td>
<td style="padding: 7px; border: 1px solid #d8d8d8; width: 38.217%; height: 20px; line-height: 20px;">세션보다 안좋음</td>
<td style="padding: 7px; border: 1px solid #d8d8d8; width: 41.0077%; height: 20px; border-right: 0px; line-height: 20px;"><span style="color: #6f45ec;">쿠키보다 좋음</span></td>
</tr>
</tbody>
</table>

***

# 웹 보안 공격

## SQL Injection(SQL 삽입)

> 데이터베이스에서 임의의 SQL 문을 실행할 수 있도록 명령어를 삽입하는 공격 유형


### SQL injection 대응 방안

1. 입력(요청)값 검증
- 모든 입력값을 검증하고, 올바른 형식의 값만 허용
- 입력값에 SQL 쿼리문이 포함되어 있는지 확인하고, 적절한 예외 처리를 수행합니다.
- 모든 예외를 차단하는 블랙리스트 방식에는 한계가 있기 때문에 화이트리스트 방식으로 대응한다.
    - 화이트리스트(Whitelist) = 특정 목록에 있는 항목들만 허용하는 방식
2. Prepared Statement 구문 사용
- 사용자의 입력과 SQL 문을 분리 시키는 방법
3. Error Message 노출 금지
- 공격자가 Error Message를 통해 DB의 정보를 얻을 수 있기 때문에 클라이언트에 Error Message가 노출되지 않도록 하는 방법

***

## Cross-Site Request Forgery (CSRF)

> 다른 오리진(cross-site)에서 유저가 보내는 요청(request)을 조작 하는 것
- 다른 사이트에 대한 요청 위장 공격
- 악의적인 웹사이트에서 사용자의 브라우저를 이용하여 다른 웹사이트로 공격 요청을 전송하는 방식

#### CSRF 공격을 하기 위한 조건
- 쿠키를 사용한 로그인
- 예측할 수 있는 요청/parameter를 가지고 있어야 함
    - ex. 이미 로그인 했어도 비밀번호 변경을 위해 추가로 비번을 입력해야 하는데, 이를 해커가 모르고 ``비밀번호 바꿔줘``를 계속 요청해도 아무일도 일어나지 않는다.

#### CSRF 공격 예시
- 사용자가 A사이트에 로그인 한 상태에서, 악성 웹사이트에 접속한다.
- 악성 웹사이트는 사용자의 브라우저를 이용하여 A사이트에 게시물 등록 요청을 전송한다.
- 이때, 사용자가 게시물을 등록한 것처럼 위장하여 전송되므로, A사이트는 사용자의 요청을 정상적으로 처리하게 된다.
#### CSRF 공격 대응 방법
- CSRF 토큰 사용
    - 사용자의 요청에 대한 유효성을 검증하는 CSRF 토큰을 생성하여 요청에 함께 전송하고, 서버에서 검증한다.
- SameSite 쿠키 속성 사용
    - SameSite 쿠키 속성을 설정하여, 해당 쿠키가 같은 사이트에서만 전송되도록 제한한다.
- Referrer 검증
    - 요청한 Referer 헤더를 검증하여, 요청이 유효한지 판단한다.
- HTTP Method 제한
    - POST 방식으로만 요청을 처리하도록 제한한다.
- CAPTCHA 적용
    - 사용자가 직접 입력한 문자를 인증하는 방식을 적용하여, 봇에 의한 요청을 제한한다.
    
    
사용자가 민감한 작업을 수행하는 요청에 대해서는 반드시 CSRF 방어 기능을 적용하는 것이 좋다.
