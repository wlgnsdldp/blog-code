## Server returns invaild timezone Error

### 문제

Intellij에서 MySQL에 Connection시 아래 사진과 함께 연결이 되지 않는 문제가 있었다.

![오류사진](./img/Server_returns_invaild_timezone_Error_img.png)

### 해결방법
JDBC 문자열에 아래와 같이 serverTimezone를 추가하였다.

`jdbc:mysql://127.0.0.1:3306/test?serverTimezone=UTC`