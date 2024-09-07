## CSRF란?

Cross-Site Request Forgery (CSRF) 공격에 대해서 이해하기 위해서, 가장 먼저 Cross-Site에서의 Request가 무엇이고 이것의 문제점을 알아보자

> Cross-Site := 도메인이 다른 웹 사이트



* **Cross Site Request가 굳이 왜 필요할까?**

  * 현대의 웹 사이트에서 특정 리소스를 표현하기 위해서 다른 서버에 있는 자원을 요청을 통해서 받아올 때 필요하다.
  * 웹 페이지들끼리의 상호작용을 위해서는 다른 웹 사이트의 컨텐츠를 받아오는 수단이 필요한데 이 과정에서 Cross Site Request가 필연적으로 발생한다.
  * e.g.)  블로그 포스팅에 광고를 부착하였을 때, 해당 광고업체의 서버로 요청이 전송되어 최신 광고 목록 데이터를 응답으로 받게 된다.

* ```mermaid
  flowchart LR
  A((Website A))
  B((Website B))
  
  subgraph Browser
  	subgraph sA[Alice]
  		pa[Page from website A]	
  		ac[(A's cookie)]
  	end
  
  	subgraph sB[Bob]
          pb[Page from website B]
  		bc[(B's cookie)]	
  	end
  end
  
  pa ==Same-site request==> A
  pa ==Cross-site request==> B
  pb ==Same-site request==> B
  
  ```

* **Cross Site Request의 문제점**
  * 특정 페이지에서 Request가 전달되었을 때, 브라우저는 이 요청이 Same Site 에서 온 지 Cross Site에서 온 지 알고 있지만, 이러한 정보를 서버에게 전달하지 않는다.
  * 위 예시를 참고해보자
    * Alice의 입장에서 웹 사이트 A로 부터 전달받은 웹 페이지에서 cross-site request가 발생했다
    * Alice는 `A's cookie` 정보를 포함한 Request를 웹 사이트 B로 전달한다.
    * 이 때, `A's cookie`에는 민감 정보가 포함되어 있다.
  * **브라우저의 행동 특성** 때문에, Alice의 민감정보가 웹 사이트 B로 전달되는 문제가 발생한다.
    * <u>사용자의 브라우저에서 보내진 모든 Request에 대해서 same site에서 시작된 요청인지, cross site로 부터 시작된 요청인지 웹서버는 알지 못한다.</u>

### CSRF Attack

<img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot%202024-09-07%20at%203.08.55%E2%80%AFAM.png" alt="Screenshot 2024-09-07 at 3.08.55 AM" style="zoom:80%;" />

CSRF 공격을 3가지 요소로 설명하겠다.

1. 희생자 (victim)
2. 대상 웹 사이트 또는 웹 서버 (targeted website)
3. 해커의 웹 사이트



아래의 상황을 가정해보자

* victim은 target website에 session을 유지하고 있는 상태
* victim이 이러한 상황에서 malicious website에 방문하였을 때

해커의 목표는 `malicious website에 공격 페이로드를 삽입하여 victim의 browser가 위조된 요청(Forged cross-site request)를 보내게 만드는 것`이다 



자세한 공격과정은 생략하고, 이러한 공격이 주는 영향력에 대해서 생각해보자

`http://www.bank.com/transfer?to=1234&amount=9999` 와 같은 요청이 user의 cookie와 함께 Target Website에 전달되었을 때, target website는 이것이 Same Origin에서 온 요청인지 Cross Origin에서 온 요청인지 구분하지 못한다. 이에 따라, 실제 송금이 이루어지게 되는 것이다.

이러한 GET 요청은 공격자 입장에서 malicious website에 간단하게 `<img>` 를 넣는 것으로 쉽게 구현할 수 있다. (img 태그가 이미지 컨텐츠를 가져오기 위해서 GET 요청을 보내기 때문)

실제, victim은 invisible한 img태그에 대해서 자신의 쿠키가 탈취되어 target website에 전달되었음을 파악하기 어려울 수 있다.


> CSRF 공격은 브라우저에서 전송된 모든 요청들의 Origin을 웹 서버가 모르는 것에 기인한다.
{: .prompt-info}

### Countermeasure
* 웹 서버가 요청을 처리할 때, 요청이 어디에서 시작되었는지 파악하는 것이 중요하다
  * 브라우저는 요청이 어디에서 시작하는지 알 수 있지만, 요청을 전달받은 웹 서버는 이 요청의 시작점을 모른다
  * 브라우저에서 `요청의 시작점` 을 알려주는 것으로 쉽게 방어할 수 있다.

1. `referer` 헤더 사용 - 권장 ❌

   * 모든 요청에 `요청의 시작점`의 정보를 헤더로 포함시키는 방식이다.
   * 하지만, 프라이버시 상의 이유로 특정 브라우저나 프록시 서버는 이러한 정보를 제거한다.
     * 이러한 이유로 Same Orgin에서 시작된 요청도 cross-site로 오인할 수 있다.

2. `Same-Site` Cookies 사용

   * 위 방식과 유사하지만 프라이버시 정보(e.g. 방문 기록) 가 없음
   * 단순하게 cross-site에서 시작된 요청인지 / same-site에서 시작된 요청인지만 알려줌
     * 브라우저 단에서 구현되는 메커니즘
   * cross-site에서 시작된 요청은 요청에 `SameSite` attribute가 설정된 쿠키가 전달되지 않는다. 반대로 same-site에서 시작된 요청은 `SameSite` attribute가 설정된 쿠키가 전달된다.  (`SameSite: strict`)
     * 또는 `SameSite: Lax` 일 경우, top-level domain의 cross-site request 까지는 허용해준다.

3. `Secret Token` 사용

   * <img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/image.png" alt="image" style="zoom:50%;" />

   2번 방식은 브라우저의 도움을 받는 방식이다.

   * 현대에는 대부분의 브라우저가 이를 지원한다.
   * SameSite cookie 메커니즘이 구현되지 않은 브라우저에서는 동작하지 않는다.

   

   1. Random Secret Value를 웹 페이지 내부에 삽입하는 방식

      * 해당 페이지에서 시작된 요청에는 모두 secret value를 포함하는 값을 서버로 전달한다.

      * 서버는 secret value의 검증을 통해서 same site로부터 시작된 요청인지 알 수 있다.

   2. Random Secret Value를 쿠키에 설정하는 방식

      * 쿠키에 접근가능한 domain을 신뢰할 수 있는 웹 사이트로 설정해둔다면, 다른 도메인에서 쿠키의 정보를 Read할 수 없다 (쿠키의 특성 활용)

      * 서버는 쿠키에 포함된 secret value를 검증하여 same site로부터 시작된 요청인지 알 수 있다.

   

   

## XSS(Cross-site Scripting)란?

* code injection attack의 일종
  * victim, attacker, target website 로 설명할 예정
* victim의 browser에서 target website에 삽입된 javascript 코드가 실행되면서, target website에서 사용자가 의도하지 않은 행위가 실행되는 것

<img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot 2024-09-07 at 6.12.40 PM.png" alt="Screenshot 2024-09-07 at 6.12.40 PM" style="zoom:80%;" />

> cross site scripting 의 cross는 target website를 교차(cross)하여 malicious code를 실행한다는 것이다. 여기서 [CSRF](https://joonamin.github.io/posts/csrf/)의 Cross-Site 개념과는 다르다는 것을 상기하자
> {: .prompt-warning}

XSS에서 malicious code에 의하여 발생되는 요청은 same-site에서 발생하는 요청이기 때문에 해당 사이트에서 접근할 수 있는 쿠키 정보들을 모두 가져올 수 있다.

* 공격 상황을 예시로 생각해보자
  1. Attacker는 XSS 취약점이 존재하는 target website에 malicious code를 적재
     * Non-persistent (Reflected) XSS Attack
     * Persistent XSS Attack
  2. victim은 특정 페이지에 접속하였을 때, 내장된 malicious code를 브라우저에서 실행하게 된다.



### Persistent XSS Attack

<img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot 2024-09-07 at 6.56.00 PM.png" alt="Screenshot 2024-09-07 at 6.56.00 PM" style="zoom:80%;" />

* attacker가 target website의 Persistent Storage에 malicious code를 삽입하는 방식
* 특정 페이지를 가져올 때 마다, malicious code를 가져오게되고 이를 통하여 `공격자-피해자` 사이의 채널을 생성하여 유지하는 방법

예를들자면 아래와 같은 상황이 있을 수 있겠다

1. 특정 사용자의 페이지를 방문하게 된다면, 해당 사용자를 친구 추가하는 script를 실행
2. 이 후, victim의 페이지에도 malicious code를 적재하도록 설정
3. 추후, victim의 페이지를 방문하는 모든 사용자들은 1,2 번 과정을 거쳐서 또 다른 피해자를 만들어낸다.

위 과정에서 `특정 사용자의 페이지` 에 해당하는 부분은 웹 서버의 persistent storage에 저장되는 정보이며, 지속적으로 추가적인 피해자를 만들어낼 수 있다.  (<u>Self Propagating XSS Attack</u>)

<img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot 2024-09-07 at 7.18.20 PM.png" alt="Screenshot 2024-09-07 at 7.18.20 PM" style="zoom:67%;" />



### Non Persistent (Reflected) XSS Attack

<img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot 2024-09-07 at 7.05.08 PM.png" alt="Screenshot 2024-09-07 at 7.05.08 PM" style="zoom:80%;" />

* 대부분의 웹 사이트들은 응답 정보에 요청 정보를 일부 포함하는 `Reflective`한 특성을 가지고 있음
  * `"너 ~~에 대한 요청을 보냈고, 해당하는 정보는 ~~이다"` 
* 이런 경우에, `Reflected Response` 에 대해서 적절한 **sanitizing 작업이 수행되지 않는다면**, <u>Reflected XSS 취약점을 가질 수 있다.</u>
  * `http://www.test.com/search?input=<script>alert("attack");</script>` 의 요청주소로 GET요청을 보낼 경우, 적절하게 user input에 대한 sanitize작업이 이루어지지 않는다면
  * `<script>alert("attack")</script>` 가 응답 메세지에 포함되면서, alert 창을 띄우게 된다.



## XSS의 영향력

* same site에서 사용자의 privilege를 포함하여 malicious code를 실행한다는 것을 상기하자
* javascript 코드는 `DOM 객체`에 접근 가능하다.
  * 사용자의 민감정보가 DOM 객체에 표현되는데, 이 정보들이 외부로 탈취당할 수 있다.
  * 또한, DOM 객체를 조작하여 사용자에게 잘못된 정보를 보여줄 수 있다.
* 사용자의 권한을 이용하여 웹 서버에 위조된 요청을 보낼 수 있다.
  * Spoofing request



## Countermeasure

> XSS 취약점이 존재하는 웹 서버는 user input을 적절하게 sanitizing하지 않아서 실행 가능한 코드가 삽입되어, 브라우저에서 실행하는 것에서 기인한다.
> {: .prompt-info}

1. **Filter** 

   * 사용자의 input에 대한 필터링 모듈을 도입하는 방식
   * 블랙 리스트 기반의 방식

2. **Encoding**

   * 실행가능한 코드가 삽입되는 것을 방지하기 위해 특수 문자를 escape 처리
   * e.g.) `<script>alert('XSS')</script>` ➡️ `&lt; script&gt; alert('XSS') ...`

3. **Content Security Policy**

   * **HTML에 JS 코드가 포함될 수 있고, 데이터와 실행 코드를 분리하지 않는 것에서 문제가 발생**
     * 신뢰할 수 있는 script에 대한 정보를 서버가 제공해준다면 브라우저가 이 정보를 기반으로 실행하는 script를 구분할 수 있게된다.
   * `Content-Security-Policy` 헤더로 응답 메세지에 포함된다.
     * 브라우저는 이 헤더를 읽어서, 신뢰할 수 있는 script 코드에 대해서만 실행하도록 할 수 있다.
     * `Content-Security-Policy: script-src 'self' 'https://apis.google.com'`
       * 코드의 출처가 자기 자신(self)인 경우에만 실행
       * 코드의 출처가 `https://apis.google.com` 인 경우에만 실행
   * inline javascript에 대해서는 서버에서 생성한 `nonce` 정보를 포함시키는 것으로 컨텐트 보안 정책을 구현할 수 있다.
     * `Content-Security-Policy: script-src 'nounce-12345'`
     * `<script nonce=12345> ... </script>` 는 신뢰할 수 있는 inline script

   
