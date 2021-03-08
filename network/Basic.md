# Network Basic

# HTTP - GET, POST

- HTTP 프로토콜을 이용해서 서버에 요청할 때 사용하는 방식

## GET

- HTTP Request Message의 `Header` 부분의 Url에 데이터를 담아서 전송
- Url 끝에 `?` 뒤에 데이터를 붙여 Request를 보냄
- 데이터의 크기가 제한적이며 보안이 취약
- 캐싱되었던 데이터가 응답될 가능성이 있음

## POST

- HTTP Request Message의 `Body` 부분의 데이터를 담아서 전송
- Binary 데이터를 요청하거나 데이터 크기의 제한이 비교적 자유롭다

## TCP 3-way Handshake

![image](https://user-images.githubusercontent.com/5853404/110327315-253cfd80-805d-11eb-9cdd-93069825886d.png)
