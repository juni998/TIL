# 웹 프로그래밍 기초
> 주요 내용
> - URL
> - 웹 브라우저와 웹 서버
> - HTML과 HTTP

<br>

### URL의 주요 구성 요소
```
http://www.11st.co.kr/html/category/1.html?xzone=ctgr1^html
```
- `http` : 프로토콜
- `www.11st.co.kr` : 서버 이름
- `/html/category/1.html` : 경로
- `xzone=ctgr1^html` : 쿼리 문자열

<br>

### 웹 브라우저와 웹 서버
![다운로드](https://user-images.githubusercontent.com/103838236/188817052-15aab2ca-2dae-48b7-9bd5-7e73a439ce05.png)

- 웹 브라우저 -> 웹 서버 : 요청(request)
- 웹 서버 -> 웹 브라우저 : 응답(response)

<br>

### DNS(Domain Name Server)
사람이 읽을 수 있는 도메인 이름( 예 : www.naver.com )을 머신이 읽을 수 있는 주소(예: 192.0.2.44)로 변환

<br>

### Port
- 컴퓨터에서 실행되고 있는 서버를 구분하기 위해 존재
- 16bit의 논리적인 할당으로 0 ~ 65536번이 존재

<br>

### HTTP의 요청 / 응답 데이터의 구성 요소
| 구성 요소 | 요청 데이터 | 응답 데이터 |
| ------- | --------- | -------- |
| request / response | GET이나 POST 같은 HTTP 요청 방식과 요청하는 경로를 지정 | 요청에 대해 200이나 404같은 응답 코드를 전송한다 |
| header | 서버가 응답을 생성하는데 참조할 수 있는 정보를 전송한다 | 응답에 대한 정보를 전송한다. 응답의 몸체가 어떤 데이터인지, 길이는 어떤지 등의 정보 |
| body | 정보를 전송해야 할 때 사용한다 | 웹 브라우저가 요청한 자원의 내용을 담는다 |

<br>



