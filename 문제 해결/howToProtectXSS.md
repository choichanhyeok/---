

### 1. XSS 문제 분석

XSS는 Reflected-XSS, Stored-XSS로 나뉜다.
본 상황에서 해결해야 하는 XSS는 Reflected-XSS인데, 그 이전에 각 XSS의 차이에 대해 기술한다.

<br>

![image](https://github.com/choichanhyeok/---/assets/68278903/718f210a-2518-4515-b34a-61825c43661b)


<br>

(1) Reflected XSS (반사형 XSS)
> 먼저 reflected xss의 경우는 위 그림에서 보이는 'query' 영역에 담겨 전달되는 쿼리 파라미터를 이용해 스크립트를 삽입하는 공격 방법이다.
> 공격자가 이런 GET 형식의 요청의 query param을 이용한 스크립트 삽입을 하면 바로 "반사"되는 공격이어서 Reflected XSS라고 부른다.

* ex: 링크를 클릭토록 피해자를 유인해 스크립트를 삽입해 사용자 쿠키, 세션을 하이재킹하는 경우가 이에 속한다.
  > 현재 발생한 문제
  

(2) Stored XSS (저장형 XSS)
> 실제 블로그 댓글 같은거로 서버 DB에 스크립트가 저장되고, 다른 사용자들이 이런 댓글들을 조회할 시에 이를 브라우저에서 랜더링 받는 사람들에게 스크립트 공격을 가하는 방법이다.
> Reflected XSS와 다르게 지속성이 있어 위험성이나 영향도가 비교적 크다.




### 2. 일반적인 대응 방법

<br>

> 1. 입력 값 제한: 검색등의 작업을 통해 쿼리 파라미터로 값이 넘어갈 때 (ex. http://www.qubitsec.io?page=1) 쿼리 파라미터의 형식을 "숫자만", "< 같은 기호 허용하지 않음"등의 정책을 설정해 스크립트 삽입을 막는다.
> 2. 입력 값 치환: '<'의 등의 태그 문자나 XSS 관련 공격 수행을 위해 필요한 태그 문자들을 동일한 의미지만 스크립트 동작을 허용치 않는 html entity로 변경한다.
> 3. 스크립트 영역에 출력 자제: 애초에 사용자로부터 받은 입력 자체를 다시 랜더링하게 두지 말라는 의미

<br><br>

애석하게도 위 3 방법을 다혼용해서 써야 할 거 같다.


### 3. 일반적으로 사용하는 lucy-xss-servlet-filter 분석과 적용 가능 여부 검토

<br>

서비스 요구사항은 아래 기재한 2가지 경우로 XSS 공격에 노출된다고 가정한다.
<br>
> (1) 검색 기능은 url의 query param을 이용해 사용자의 입력값을 전달한다. reflected xss 노출 => reflected
> 
> (2) 꼭 검색 기능이 아니더라도 url의 query param으로 넘겨주는 경우가 있음. 단, 해당 부분들이 실제로 사용자에게 랜더링돼 reflected xss에 노출되는지 여부는 검토 안함 => reflected, stored xss
> 
> (3) ajax call을 래핑한 별도 클래스를 통해 post에 body값으로 다양한 값을 넘겨주거나 get으로 queryParam 넘겨줄 수 있음 (rest) => reflected, stored xss
> 
> (4) form 형식을 래핑한 movePage를 사용하는 과정에서 reflected, stored xss 둘다 노출 가능성 있음: 해당 form에서 Post, GET 둘 다 지원하기 때문에 => reflected, stored xss

<br><br>

> 결론은 다양한 xss 공격 가능 경로가 존재하기 때문에 이에 대해 입력 값 제한이나 치환을 해줘야 하는데 입력 값 제한은 굉장히 큰 공수가 들고 유지보수 비용을 감당할 수 없다. (클라이언트 레벨에서의 개별적 치환은 불가능)
> 그래서 서버에서 필터를 이용해 해결하려 했는데 일반적으로 사용하는 XSS-filter를 적용할 수 없는 상황이다. 이유는 아래와 같다.

<br><br>

> + lucy xss servlet filter는 @requestBody를 필터링 하지 못한다.

표현은 requestBody라고 했지만 json으로 매핑되서 넘어오는 것들은 필터링 하지 못한다는 의미이다. form-data처럼 get 형식으로 넘어오거나 post로 넘어오더라도 enctype이 APPLICATION/JSON인 경우에는 필터링이 불가능한데,
애석하게도 우리가 쓰는 ajaxCall을 매핑한 클래스에서는 모든 전달인자를 json으로 묶어 전달한다. 따라서 (1), (2), (4)의 경우엔 naver-lucy-filter를 통해 우리 문제를 해결할 수 있을지에 대해선 다시한번 검토가 필요하다.

