---
layout: post
title: CSRF(Cross Site Request Forgery)
date: 2024-09-07 01:22 +0900
pin: false
math: true
mermaid: true
description:
categories:
- Computer Science
- Web
- Security
tags:
- csrf
- security
- countermeasure
---
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

   

   




