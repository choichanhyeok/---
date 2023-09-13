

### 1. XSS 문제 분석

XSS는 Reflected-XSS, Stored-XSS로 나뉜다.
본 상황에서 해결해야 하는 XSS는 Reflected-XSS인데, 그 이전에 각 XSS의 차이에 대해 기술한다.

<br>

![image](https://github.com/choichanhyeok/---/assets/68278903/718f210a-2518-4515-b34a-61825c43661b)


<br>

(1) Reflected XSS (반사형 XSS)
먼저 reflected xss의 경우는 위 그림에서 보이는 'query' 영역에 담겨 전달되는 쿼리 파라미터를 이용해 스크립트를 삽입하는 공격 방법이다.
공격자가 이런 GET 형식의 요청의 query param을 이용한 스크립트 삽입을 하면 바로 "반사"되는 공격이어서 Reflected XSS라고 부른다.

* ex: 링크를 클릭토록 피해자를 유인해 스크립트를 삽입해 사용자 쿠키, 세션을 하이재킹하는 경우가 이에 속한다.
  > 현재 발생한 문제
  

(2) Stored XSS (저장형 XSS)
실제 블로그 댓글 같은거로 서버 DB에 스크립트가 저장되고, 다른 사용자들이 이런 댓글들을 조회할 시에 이를 브라우저에서 랜더링 받는 사람들에게 스크립트 공격을 가하는 방법이다.
Reflected XSS와 다르게 지속성이 있어 위험성이나 영향도가 비교적 크다.


