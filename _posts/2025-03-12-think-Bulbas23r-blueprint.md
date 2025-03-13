---  
layout: post  
title: "2차 프로젝트 23조 이삼해꽃 S.A."  
subtitle: "내일배움캠프 백엔드 심화 3기 2차 프로젝트 23조 이삼해꽃 프로젝트 청사진"  
categories: think  
tags: [think]  
comments: true  
# header-img: img/review/2019-11-22-review-book-pycharm-1.jpg  
---  
  
## 개요  
- 프로젝트 명: 내일배움캠프 스파르타로직
- 개발 기간: 2025.03.11 ~ 2025.03.26
- 개발 인원: 강민, 김형찬, 박주희, 이동하

## 인프라 아키텍쳐 & 기술 스택
![인프라](https://zzangkkmin.github.io/assets/img/postImages/2025-03-12-architecture-23team.png)

- 기술 스택
    - **개발 언어**: Java 17
    - **백엔드:** Spring Boot 3.x
    - **데이터베이스:** PostgreSQL, Redis
    - **빌드 툴:**  Gradle
    - **API 문서화:** Swagger
    - **API 게이트웨이:** Spring Cloud Gateway
    - **서비스 디스커버리:** Spring Cloud Eureka
    - **버전 관리:** Git GitHub
    - **메세징 큐**: Kafka
    - **서킷 브레이커**: Resilience4j
    - **설정**: Spring Config
    - **분산 추적**: zipkin
    - **실행 컨테이너**: Docker
    - **외부 API**: gemini AI, 네이버 Map API


## ERD

![ERD](https://zzangkkmin.github.io/assets/img/postImages/2025-03-12-ERD.png)
![ERD1](https://zzangkkmin.github.io/assets/img/postImages/2025-03-12-ERD1.png)
![ERD2](https://zzangkkmin.github.io/assets/img/postImages/2025-03-12-ERD2.png)
![ERD3](https://zzangkkmin.github.io/assets/img/postImages/2025-03-12-ERD3.png)
![ERD4](https://zzangkkmin.github.io/assets/img/postImages/2025-03-12-ERD4.png)
![ERD5](https://zzangkkmin.github.io/assets/img/postImages/2025-03-12-ERD5.png)

## DB 테이블

테이블 노션 페이지 : [https://www.notion.so/teamsparta/1b32dc3ef5148090bef4d2a9e98d3343?pvs=4](https://www.notion.so/teamsparta/1b32dc3ef5148090bef4d2a9e98d3343?pvs=4)  

### p_user: 사용자 테이블

| 컬럼 ID    | 컬럼 설명  | 타입 및 길이   | Not null | PK | Unique | 기본값  | 제약조건   |
|------------|-----------|----------------|----------|---|--------|---------|-----------|
| id         | 사용자 고유 값    | UUID           | Y   | Y           | Y      |         | 
| username   | 사용자 아이디 이름 | VARCHAR(10)    | Y   |             | Y      |         |  
| password   | 사용자 비밀번호    | VARCHAR(15)    | Y   |             |        |         | 
| slack_id   | 슬랙 아이디       | VARCHAR(255)   | Y   |             | Y      |         |     
| role       | 사용자 역할       | role_type      | Y    |             |        |         |   
| created_at | 레코드 생성 시간   | TIMESTAMP      | Y   |             |        | NOW()   |        
| created_by | 레코드 생성자     | VARCHAR(100)   | Y    |             | Y      | username|  
| updated_at | 레코드 수정 시간  | TIMESTAMP      | Y  |             |        | NOW()   |  
| updated_by | 레코드 수정자    | VARCHAR(100)   | Y   |             |        | username|   
| is_deleted | 레코드 삭제 여부 | BOOLEAN        | Y  |             |        | false   |   
| deleted_at | 레코드 삭제 시간  | TIMESTAMP      |   |             |        |         |   
| deleted_by | 레코드 삭제자     | VARCHAR(100)   |   |             |        |         |   

- username: 최소 4자 이상, 10자 이하 알파벳 소문자(a~z), 숫자(0~9)
- password: 최소 8자 이상, 15자 이하<br>알파벳 대소문자(a~z, A~Z), 숫자(0~9), 특수문자
- role: (CUSTOMER, HUB, DELIVERY, COMPANY, MASTER)

### p_company: 업체 테이블

| 컬럼 ID     | 컬럼 설명                     | 타입 및 길이  | Not null | PK | Unique | 기본값   | 
|-------------|-------------------------------|---------------|----------|----|--------|----------|
| id          | 업체 ID                       | UUID          | Y        | Y  |        |          | 
| hub_id      | 업체 허브 ID                  | UUID          | Y        |    |        |          | 
| manager_id  | 업체 담당자                   | UUID          | Y        |   |        |          |  
| name        | 업체 이름                     | VARCHAR(255)  | Y        |    |        |          |   
| type        | 업체 종류                     | ENUM          | Y        |    |        |          |   
| phone       | 업체 번호                     | VARCHAR(20)   | Y        |    |        |          |     
| road_address| 도로명 주소                   | VARCHAR(500)  | Y        |    |        |          |    
| jibun_address| 지번 주소                    | VARCHAR(500)  | Y        |    |        |          |   
| city        | 시                            | VARCHAR(255)  | Y        |   |        |          |  
| district    | 구/군                         | VARCHAR(255)  | Y        |   |        |          |   
| town        | 읍/면/동                      | VARCHAR(255)  | Y        |   |        |          | 
| postal_code | 우편 번호                     | VARCHAR(255)  | Y        |   |        |          |  
| updated_by  | 레코드 수정자                  | VARCHAR(50)   | Y        |   |        | username |   
| is_deleted  | 레코드 삭제 여부              | BOOLEAN       | Y        |    |        | FALSE    |   
| deleted_at  | 레코드 삭제 시간              | TIMESTAMP     |          |    |        |          |  
| deleted_by  | 레코드 삭제자                 | VARCHAR(50)   |          |    |        |          |   

### p_hub: 허브 테이블

| 컬럼 ID      | 컬럼 설명                    | 타입 및 길이   | Not null | PK | Unique | 기본값 | 
|--------------|------------------------------|----------------|----------|-------------|--------|--------|
| id           | 허브 ID                      | UUID           | Y        | Y           |        |        | 
| name         | 허브 이름                    | VARCHAR(255)   | Y        |             | Y      |        |  
| manager_id   | 허브 관리자 ID               | INTEGER        |          |             |        |        | 
| road_address | 도로명 주소                  | VARCHAR(500)   | Y        |             |        |        |  
| jibun_address| 지번 주소                    | VARCHAR(500)   | Y        |             |        |        |
| city         | 시                           | VARCHAR(255)   | Y        |             |        |        | 
| district     | 구/군                        | VARCHAR(255)   | Y        |             |        |        | 
| town         | 읍/면/동                     | VARCHAR(255)   | Y        |             |        |        | 
| postal_code  | 우편 번호                    | VARCHAR(255)   | Y        |             |        |        | 
| latitude     | 위도                         | NUMERIC(9,6)   | Y        |             |        |        |  
| longitude    | 경도                         | NUMERIC(9,6)   | Y        |             |        |        | 
| created_at   | 레코드 생성 시간             | TIMESTAMP      | Y        |             |        | NOW()  |  
| created_by   | 레코드 생성자                | VARCHAR(50)    | Y        |             |        |        |      
| updated_at   | 레코드 수정 시간             | TIMESTAMP      | Y        |             |        | NOW()  |    
| updated_by   | 레코드 수정자                | VARCHAR(50)    | Y        |             |        |        |   
| is_deleted   | 레코드 삭제 여부             | BOOLEAN        | Y        |             |        | FALSE  |  
| deleted_at   | 레코드 삭제 시간             | TIMESTAMP      |          |             |        |        |  
| deleted_by   | 레코드 삭제자                | VARCHAR(50)    |          |             |        |        |   

### p_hub_transit_info: 허브 이동 경로 테이블

| 컬럼 ID            | 컬럼 설명                   | 타입 및 길이 | Not null | PK | Unique | 기본값 | 
|--------------------|-----------------------------|--------------|----------|-------------|--------|--------|
| departure_hub_id   | 출발 허브 ID                | UUID         | Y        | Y           |        |        |  
| arrival_hub_id     | 도착 허브 ID                | UUID         | Y        | Y           |        |        | 
| transit_time       | 소요 시간(분)               | INTEGER      | Y        |             |        |        |
| transit_distance   | 이동 거리(미터)              | INTEGER      | Y        |             |        |        |
| created_at         | 레코드 생성일자             | TIMESTAMP    | Y        |             |        | NOW()  | 
| created_by         | 레코드 생성자     | VARCHAR(50)  | Y        |             |        |        |   
| updated_at         | 레코드 수정일자             | TIMESTAMP    | Y        |             |        | NOW()  |  
| updated_by         | 레코드 수정자     | VARCHAR(50)  | Y        |             |        |        |   
| is_deleted         | 레코드 삭제 여부             | BOOLEAN      | Y        |             |        | FALSE  |  
| deleted_at         | 레코드 삭제일자             | TIMESTAMP    |          |             |        |        |   
| deleted_by         | 레코드 삭제자      | VARCHAR(50)  |          |             |        |        |  

### p_hub_stock: 허브 상품 재고 테이블

| 컬럼 ID    | 컬럼 설명                   | 타입 및 길이 | Not null | PK | Unique | 기본값 | 
|------------|-----------------------------|--------------|----------|-------------|--------|--------|
| hub_id     | 소속 허브 ID                | UUID         | Y        | Y           |        |        | 
| item_id    | 상품 ID                     | UUID         | Y        | Y           |        |        | 
| quantity   | 재고 수량                   | INTEGER      | Y        |             |        |        |  
| created_at | 레코드 생성일자             | TIMESTAMP    | Y        |             |        | NOW()  | 
| created_by | 레코드 생성자     | VARCHAR(50)  | Y        |             |        |        |  
| updated_at | 레코드 수정일자             | TIMESTAMP    | Y        |             |        | NOW()  |   
| updated_by | 레코드 수정자     | VARCHAR(50)  | Y        |             |        |        |  
| is_deleted | 레코드 삭제 여부             | BOOLEAN      | Y        |             |        | FALSE  | 
| deleted_at | 레코드 삭제일자             | TIMESTAMP    |          |             |        |        |  
| deleted_by | 레코드 삭제자      | VARCHAR(50)  |          |             |        |        |  


### p_product: 상품 테이블

| 컬럼 ID    | 컬럼 설명                     | 타입 및 길이   | Not null | PK | Unique | 기본값  | 
|------------|-------------------------------|----------------|----------|-------------|--------|---------|
| id         | 메뉴 ID                       | UUID           | Y        | Y           |        |         |  
| company_id | 업체 ID                       | UUID           | Y        |             |        |         |  
| hub_id     | 상품 관리 허브 ID             | UUID           | Y        |             |        |         |  
| name       | 상품 이름                     | VARCHAR(100)   | Y        |             |        |         |   
| price      | 상품 가격                     | DECIMAL(10,2)  | Y        |             |        |         | 
| decription | 상품 설명                     | TEXT           |          |             |        |         | 
| created_at | 레코드 생성 시간              | TIMESTAMP      | Y        |             |        | NOW()   |   
| created_by | 레코드 생성자       | VARCHAR(50)    | Y        |             |        | username| 
| updated_at | 레코드 수정 시간              | TIMESTAMP      | Y        |             |        | NOW()   | 
| updated_by | 레코드 수정자       | VARCHAR(50)    | Y        |             |        | username| 
| is_deleted | 레코드 삭제 여부              | BOOLEAN        | Y        |             |        | false   |
| deleted_at | 레코드 삭제 시간              | TIMESTAMP      |          |             |        |         |  
| deleted_by | 레코드 삭제자       | VARCHAR(50)    |          |             |        |         |  


### p_order: 주문 테이블

| 컬럼 ID               | 컬럼 설명               | 타입 및 길이  | Not null | PK | Unique | 기본값 | 
|-----------------------|-------------------------|--------------|----------|----|-------|--------|
| id                    | 주문 아이디             | UUID          | Y        | Y  | Y     |         |  
| provide_<br>company_id    | 공급 업체 아이디        | UUID          | Y        |    | Y      |        | 
| recieve_<br>company_id    | 수령 업체 아이디        | UUID          | Y        |    | Y      |         |  
| created_at            | 레코드 생성 시간        | TIMESTAMP     | Y        |    |        | NOW()  |  
| created_by            | 레코드 생성자           | VARCHAR(100)  | Y        |    | Y      | username |  
| updated_at            | 레코드 수정 시간        | TIMESTAMP     | Y        |    |        |  NOW()  |
| updated_by            | 레코드 수정자           | VARCHAR(100)  | Y        |    |        | username | 
| is_deleted            | 레코드 삭제 여부        | BOOLEAN       | Y        |    |        |  false  | 
| deleted_at            | 레코드 삭제 시간        | TIMESTAMP     |          |    |        |          |  
| deleted_by            | 레코드 삭제자           | VARCHAR(100)  |          |    |        |         |   


### p_order_product: 주문 상세조회 테이블

| 컬럼 ID      | 컬럼 설명                     | 타입 및 길이   | Not null | PK | Unique | 기본값 | 
|--------------|------------------------------|---------------|----------|----|--------|---------|
| id           | 주문 상세 ID                  | UUID           | Y        | Y |        |         |
| order_id     | 주문 ID                       | UUID           | Y        |   |        |         |  
| hub_id       | 허브 ID                       | UUID           | Y        |   | Y      |         | 
| product_id   | 상품 ID                       | UUID           | Y        |   | Y      |         |
| product_name | 상품명                        | UUID           | Y        |   |        |         | 
| quantity     | 수량                          | INT            | Y        |   |        |         | 
| price        | 가격                          | DECIMAL(10,2)  | Y        |   |        |         |  
| created_at   | 레코드 생성 시간              | TIMESTAMP      | Y         |   |        |  now()  | 
| created_by   | 레코드 생성자                 | VARCHAR(50)    | Y        |    |        | username | 
| updated_at   | 레코드 수정 시간              | TIMESTAMP      | Y        |    |        |   now()  | 
| updated_by   | 레코드 수정자                 | VARCHAR(50)    | Y        |   |        | username | 
| is_deleted   | 레코드 삭제 여부              | BOOLEAN        | Y        |   |        |  false  | 
| deleted_at   | 레코드 삭제 시간              | TIMESTAMP      |          |   |        |         | 
| deleted_by   | 레코드 삭제자                 | VARCHAR(50)    |          |   |        |         |  


### p_delivery: 배송 테이블

| 컬럼 ID               | 컬럼 설명                    | 타입 및 길이 | Not null | PK | Unique | 기본값 | 
|-----------------------|------------------------------|--------------|----------|-------------|--------|--------|
| id                    | 배송 ID                      | UUID         | Y        | Y           | Y      |        | 
| order_id              | 주문 ID                      | UUID         | Y        |             |        |        | 
| start_hub_id          | 출발 허브 ID                 | UUID         | Y        |             |        |        | 
| end_hub_id            | 도착 허브 ID                 | UUID         | Y        |             |        |        | 
| deliver_<br>manager_id    | 업체 배송 담당자 ID          | UUID         | Y        |             |        |        | 
| status                | 상태                         | ENUM         | Y        |             |        |        | 
| receiver_<br>company_id   | 수령 업체 ID                 | UUID         | Y        |             |        |        |  
| receiver_company_<br>slack_id | 수령 업체 슬랙 ID         | UUID         |          |             |        |        |  
| created_at            | 레코드 생성 시간             | TIMESTAMP    | Y        |             |        | NOW()  |   
| created_by            | 레코드 생성자                 | VARCHAR(50)  | Y        |             |        | username |
| updated_at            | 레코드 수정 시간             | TIMESTAMP    | Y        |             |        | NOW()  | 
| updated_by            | 레코드 수정자                | VARCHAR(50)  | Y        |             |        | username |
| is_deleted            | 레코드 삭제 여부             | BOOLEAN      | Y        |             |        | FALSE  |
| deleted_at            | 레코드 삭제 시간             | TIMESTAMP    |          |             |        |        | 
| deleted_by            | 레코드 삭제자                | VARCHAR(50)  |          |             |        |        |  


### p_delivery_path: 배송 경로 테이블

| 컬럼 ID               | 컬럼 설명                         | 타입 및 길이 | Not null | PK | Unique | 기본값 | 
|-----------------------|-----------------------------------|--------------|----------|-------------|--------|--------|
| id                    | 배송 경로 ID                      | UUID         | Y        | Y           |        |        |  
| delivery_id           | 배송 ID                           | UUID         | Y        |             |        |        |   
| hub_transit_info_id   | 허브 간 이동 정보 ID               | UUID         | Y        |             |        |        |  
| start_hub_id          | 출발 허브 ID                      | UUID         | Y        |             |        |        |   
| end_hub_id            | 도착 허브 ID                      | UUID         | Y        |             |        |        | 
| delivery_manager_id   | 허브 배송 담당자 ID               | UUID         | Y        |             |        |        |  
| status                | 상태                              | ENUM         | Y        |             |        |        |  
| sequence              | 허브의 순번                       | NUMERIC      | Y        |             |        |        | 
| estimated_distance    | 예상 거리                         | NUMERIC      |          |             |        |        |  
| estimated_duration    | 예상 소요 시간                    | INTERVAL     |          |             |        |        | 
| actual_distance       | 실제 거리                         | NUMERIC      |          |             |        |        |  
| actual_duration       | 실제 소요 시간                    | INTERVAL     |          |             |        |        |  
| created_at            | 레코드 생성 시간                  | TIMESTAMP    | Y        |             |        | NOW()  | 
| created_by            | 레코드 생성자                     | VARCHAR(50)  | Y        |             |        | username |   
| updated_at            | 레코드 수정 시간                  | TIMESTAMP    | Y        |             |        | NOW()  |   
| updated_by            | 레코드 수정자                     | VARCHAR(50)  | Y        |             |        | username |  
| is_deleted            | 레코드 삭제 여부                  | BOOLEAN      | Y        |             |        | FALSE  |
| deleted_at            | 레코드 삭제 시간                  | TIMESTAMP    |          |             |        |        |    
| deleted_by            | 레코드 삭제자                     | VARCHAR(50)  |          |             |        |        | 


### p_delivery_manager: 배송 담당자 테이블

| 컬럼 ID    | 컬럼 설명                     | 타입 및 길이    | Not null | PK | Unique | 기본값 | 
|------------|-------------------------------|-----------------|----------|-------------|--------|--------|
| user_id    | 유저 ID                       | INTEGER         | Y        | Y           |        |        |
| hub_id     | 소속 허브 ID                  | UUID            | Y        |             |        |        |
| slack_id   | 슬랙 아이디                   | VARCHAR(255)    | Y        |             |        |        | 
| type       | 배송 담당자 타입              | delivery_<br>manager_type | Y  |             |        |        |   
| sequence   | 배송 순서                     | INTEGER         | Y        |             |        |        |  
| created_at | 레코드 생성일자               | TIMESTAMP       | Y        |             |        | NOW()  | 
| created_by | 레코드 생성자        | VARCHAR(50)     | Y        |             |        | username |  
| updated_at | 레코드 수정일자               | TIMESTAMP       | Y        |             |        | NOW()  |   
| updated_by | 레코드 수정자        | VARCHAR(50)     | Y        |             |        | username |   
| is_deleted | 레코드 삭제 여부              | BOOLEAN         | Y        |             |        | FALSE  | 
| deleted_at | 레코드 삭제일자               | TIMESTAMP       |          |             |        |        |  
| deleted_by | 레코드 삭제자        | VARCHAR(50)     |          |             |        |        |   


### p_slack_message: 슬랙 메세지 테이블

| 컬럼 ID    | 컬럼 설명                          | 타입 및 길이 | Not null | PK | Unique | 기본값 | 
|------------|------------------------------------|--------------|----------|-------------|--------|--------|
| id         | 슬랙 메세지 ID                     | UUID         | Y        | Y           |        |        |  
| sender_id  | 발신 ID (발신자 슬랙 ID)            | VARCHAR(50)  | Y        |             |        |        | 
| receiver_id| 수신 ID (수신자 슬랙 ID)            | VARCHAR(50)  | Y        |             |        |        |
| message    | 메세지 내용                        | TEXT         | Y        |             |        |        | 
| delivery_time | 발송 시간                      | TIMESTAMP    | Y        |             |        |        | 
| created_at | 레코드 생성일자                    | TIMESTAMP    | Y        |             |        | NOW()  | 
| created_by | 레코드 생성자            | VARCHAR(50)  | Y        |             |        | username | 
| updated_at | 레코드 수정일자                    | TIMESTAMP    | Y        |             |        | NOW()  | 
| updated_by | 레코드 수정자            | VARCHAR(50)  | Y        |             |        | username |
| is_deleted | 레코드 삭제 여부                   | BOOLEAN      | Y        |             |        | false  | 
| deleted_at | 레코드 삭제일자                    | TIMESTAMP    |          |             |        |        |  
| deleted_by | 레코드 삭제자            | VARCHAR(50)  |          |             |        |        |  

### p_ai_log: AI 사용 기록 테이블

| 컬럼 ID    | 컬럼 설명                       | 타입 및 길이 | Not null | PK | Unique | 기본값 | 
|------------|---------------------------------|--------------|----------|-------------|--------|--------|
| id         | AI 사용 기록 ID                 | UUID         | Y        | Y           |        |        | 
| request    | AI 요청                         | TEXT         | Y        |             |        |        |
| response   | AI 응답                         | TEXT         | Y        |             |        |        | 
| created_at | 레코드 생성일자                 | TIMESTAMP    | Y        |             |        | NOW()  | 
| created_by | 레코드 생성자          | VARCHAR(50)  | Y        |             |        | username |  
| updated_at | 레코드 수정일자                 | TIMESTAMP    | Y        |             |        | NOW()  |  
| updated_by | 레코드 수정자          | VARCHAR(50)  | Y        |             |        | username | 
| is_deleted | 레코드 삭제 여부                | BOOLEAN      | Y        |             |        | false  |
| deleted_at | 레코드 삭제일자                 | TIMESTAMP    |          |             |        |        | 
| deleted_by | 레코드 삭제자          | VARCHAR(50)  |          |             |        |        |  


## API

[노션 페이지로 대체합니다.](https://www.notion.so/teamsparta/API-1b32dc3ef5148050be4bf9f74d9da688?pvs=4)  
  
[ERD5](https://zzangkkmin.github.io/assets/files/api-list.pdf)