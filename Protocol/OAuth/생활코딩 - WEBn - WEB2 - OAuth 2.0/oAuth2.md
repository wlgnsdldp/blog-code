# 1. OAuth란?
OAuth는 인터넷 사용자들이 비밀번호를 제공하지 않고 다른 웹사이트 상의 자신들의 정보에 대해 웹사이트나 애플리케이션의 접근 권한을 부여할 수 있는 공통적인 수단으로서 사용되는, 접근 위임을 위한 개방형 표준으로 웹, 앱 서비스에서 **제한적으로 권한을 요청해 사용 할 수 있는 키를 발급해주는 것을 뜻한다.**


# 2. OAuth 용어 정리

## 1. Resource Owner
Application을 이용하는 사람을 뜻한다. 즉, 사용자(User)를 뜻한다.

## 2. Client
Resource Server에서 제공하는 자원을 사용하는 Application을 뜻한다.

## 3. Resource Server
Client에서 필요한 Resource(데이터)를 제공하는 서버이다.

## 4. Authorization Server
인증과 관련된 처리를 전담하는 서버로, 공식 메뉴얼에서는 공식 메뉴얼에서는 Resource Server와 Authorization Server를 분리해서 설명하고 있으나, 같은 서버에 있는 경우가 많다.


# 3. OAuth register
Client가 Resource Server를 이용하기 위해서는 Resource Server에 승인을 사전에 받아야하는데, 이를 Register이라 한다.

Register는 Service마다 다르지만, 기본적으로 Client ID, Client Secret, Authorized redirect URIs는 공통적인 부분이다.

## 1. Client ID
* Application을 식별하는 식별자로 외부에 노출되도 문제가 발생하지 않는다.

## 2. Client Secret
* Client ID에 대한 비밀번호로 외부에 노출시 보안에 큰 문제가 발생한다.

## 3. Authorized redirect URIs
* Authorized redirect URIs은 Resource Server가 권한 부여시 authorization code를 Application에 전달해 주는데 authorization code를 해당 주소로 넘겨준다.
* authorization code는 자신이 권한 허가를 받았다는 것을 뜻힌다.


# 4. Resource Owner의 승인

Resource Owner가 Client 사용시 Client는 특정 작업을 처리하기 위해 Resource Server의 특정 기능이 필요할 때가 있다.

이때 모든 기능에 대해 인증을 받는 것이 아닌 필요한 기능에 대해서만 인증을 받으면 된다. 

## 인증 과정
1. Resource Owner는 Client에 접속하여, Client가 Resource Server를 사용해야 하는 일이 발생한다.

2. Client는 Resource Owner에게 Resource Server에게 권한을 부여받기 위해 버튼(링크)를 제공한다.(일반적으로 Facebook, Twitter, Google 등 있다.)
    * 링크 예시
        * Resource Server와 register시 생성된 client_id와 redirect_uri 그리고 권한 범위인 scope로 이루어져 있다.
        * 아래 링크 구조는 예시 일뿐 실제 구조는 서비스 업체 마다 다르다.
        * http://resoucre.server/?client_id=1&scope=B,C&redirect_uri=https://client/callback

3. 링크 클릭시 Resource Owner는 Resource Server에 접속한다.
    * Resource Server은 Resource Owner가 현재 로그인 되어있는지 판단하여 로그인이 안되어 있으면 로그인 화면을 띄워준다.
    
4. Resource Owner가 로그인 되었으면, Resource Server는 Client가 요청한 client_id가 Resource Server에 있는지 확인하고, 일치하는 client_id가 있으면 해당 client_id의 redirect URIs과 요청한 redirect URIs이 일치하는지 확인하고 일치하면 Resource Owner에게 scope에 대한 권한을 허용할지 Resource Owner에게 보여준다.
    * 만약 불일치하는 부분이 있다면 해당 부분에서 작업이 종료된다.

5. Resource Owner가 해당 scope를 허용하면 Resource Server는 해당 정보를 저장한다.


# 5. Access token

Client가 Resource Server를 사용하기 위해서는 Access token를 발급받아야 하는데, 발급 과정은 아래와 같다.

## Access token 발급 과정

1. Client는 register시 받은 authorization code와 client_secret를 Resource Server에 전송한다.
    * 예시
        * http://resoucre.server/token?grant_type=authorization_code=3&redirect_uri=https://client/callback?client_id=1&client_secret=2

2. authorization code로 Resource Server에 인증완료후 Client와 Resource Server는 authorization code를 삭제하며, Resource Server는 Access Token을 Client에게 전달한다.

3. Client는 발급받은 Access Token으로 Resource Server에 API를 요청하면 Resource Server는 Access Token을 보고 해당 Access Token의 user와 권한을 확인후 API에 대한 결과를 제공한다.


# 6. Refresh token

발급받은 Access Token은 수명이 있다.

Access Token이 만료되면 Refresh token을 Resource Server에게 전달하여 새로운 Access Token을 발급받는다.

> Access Token을 발급할때 Refresh token을 같이 발급하는 경우도 많음.

> 개념은 비슷하나 서비스 업체마다 Refresh token에 대한 정보다 다르므로 확인이 필요하다.

# 7. oAuthFlow
![236c70435940026011](https://user-images.githubusercontent.com/31675104/53391776-c444ae80-39da-11e9-9a52-2e827e1c086a.png)
