

#### 1. -ano와 anp 헷갈리다.
웹 서버 포트 확인을 위해 netstat -ano를 쳤는데 이게 왠 걸 나오지 않았다.
> 리눅스는 -anp를 이용해 포트를 확인 가능하다. (단, 권한이 없으면 pid 조회 불가)


#### 2. 서버에 사용 가능한 디스크 용량 확인
> (1) df -h
> (2) df -h /경로/이름

> Q. 찬혁씨 서버 디스크 사용률이 90퍼센트가 넘었어요 ~ 확인해주세요!
> A. 찬혁쓰 해결법
>  
> (1) `df -h`로 사용률 90퍼센트 넘는 디렉토리 탐색
> 
> (2) `df -h /LOG/`로 로그크기 분석
> 
> (3) `du -ah /LOG/Was | sort -rh`이용해서 큰 용량을 먹는 디렉토리 검색


#### 3. SLF4J 로그 레벨별 크기 검토

> (1) AWK 이용 방법: 
 ``` awk '/ERROR/ { total += length($0) + 1 } END { print total }' your_log_file.log ```
 
> (2) grep 이용 방법: 
 ``` grep "ERROR" your_log_file.log > temp_error.log du -sh temp_error.log rm temp_error.log```
