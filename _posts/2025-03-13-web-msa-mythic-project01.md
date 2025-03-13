---  
layout: post  
title: "MSA 프로젝트 도전기 01"
subtitle: "MSA project 01"  
categories: web  
tags: [msa]   
comments: true  
# header-img: img/review/2019-11-22-review-book-pycharm-1.jpg  
---  
  
## 개요  
어쩌다 프로젝트 팀장을 맡게 되었고, 어찌저찌 해서 테이블/API/ERD 등등을 다 같이 설계를 해왔다. 고래서 설계까지는 완성이 되었다. 이제 개발을 시작할 때이다. 그런데 말입니다... 뭐부터 해야되죠?

## MSA 프로젝트 구성

기존에는 한 프로젝트내 여러개 서비스, 컨트롤러, 기능들이 있어서 그 프로젝트만 구성하면 되었었다. 그러니까 모놀리식의 경우 뭐만 추가 좀 해주면 되는 거였는데.. 이번엔 MSA.. 각기 다른 서비스들이다.

일단 아키텍쳐를 살펴본다.

![인프라](https://zzangkkmin.github.io/assets/img/postImages/2025-03-12-architecture-23team.png)

보면은... 서비스 클라이언트들이 있고, 서버도 있고, 게이트웨이가 있다. 그리고 여기에 숨겨진건 Config 서비스도 있다. 그래서 구성한 프로젝트 수는 도메인 8개 / 게이트웨이 1개 / 서버 1개 / Config 1개로 구성을 했다.

그러나 각 프로젝트를 Spring Initailizer로 만들 때 Dependency 설정이 다르고 또 챙겨야 할 것이 많다. 그래서 여기에 정리해본다.

- 서버: Spring Cloud Eureka Server
- 게이트웨이: Spring Web, Spring Cloud Gateway, Lombok, Spring Cloud Eureka Client
- Config: Spring Cloud Config Server, Spring Cloud Eureka Client
- 서비스:
  - Spring JPA, postgresql, Spring Web, Lombok
  - Zipkin, resilience4j, config, openfeign, kafka
  - Spring Cloud Eureka Client
  - redis(캐싱 정보 필요 시)

이거 선정하기 은근 어려웠다. 빼먹어서 나는 오류도 있고, 있어서 나는 오류가 있었다. 그렇게 시행착오를 겪으며 나온 구성설정이다.

## 연결 테스트
설정이 끝났다싶으면 실제로 서비스 흐름대로 가는 지 확인해본다. 아직 다 만들기 전에 서버-게이트웨이-서비스 시도만 해본다는 것이다. 그렇기 위해선 각 서비스별로 application.yml 설정을 건드려 줘야한다.

**서버**
- 서버 어플리케이션에 `@EnableEurekaServer`를 붙여서 서버 어플리케이션이라 지칭
- application.yml의 `server.port` 지정
- application.yml의 `eureka.instance.hostname` 지정
- application.yml의 `client.register-with-eureka`를 false로 설정  
  (다른 Eureka 서버에 이 서버를 등록하지 않음)
- application.yml의 `client.fetch-registry`를 false로 설정  
  (다른 Eureka 서버의 레지스트리를 가져오지 않음)
- application.yml의 `server.enable-self-preservation`를 false로 설정  
  (자기 보호 모드 비활성화)

**게이트웨이**
- application.yml의 `spring.main.web-application-type`을 reactive로 설정
- application.yml의 `server.port` 지정
- application.yml의 `cloud.gateway.routes`에 게이트웨이 라우팅을 설정
  - id: 서비스 이름
  - uri: 로드밸런싱된 서비스 이름 (`lb://{service_id}`)
  - predicates
    - path: 설정된 경로로 들어오는 요청을 이 라우트로 처리(`- Path=/api/orders/**`)
- application.yml의 `discovery.locator.enabled`를 true로 설정  
  (서비스 디스커버리를 통해 동적으로 라우트를 생성하도록 설정)
- application.yml의 `eureka.client.service-url.defaultZone`에 서버주소 지정  
  (예: `http://localhost:19090/eureka/`)

**서비스**
- 서비스 어플리케이션에 `@EnableFeignClients`를 붙여서 서비스 어플리케이션이라 지칭
- application.yml의 `server.port` 지정
- application.yml의 `eureka.client.service-url.defaultZone`에 서버주소 지정  
  (예: `http://localhost:19090/eureka/`)

위 설정 후 서비스에 간단하게 RestController를 만들어서 테스트를 해본다. 그렇게 하려면 서버와 게이트웨이 그리고 서비스를 IDE내 실행을 해본다. 그러면 서버(19090)에서 게이트웨이와 서비스가 올라온 것이 확인이 될 것이다.

![server](https://zzangkkmin.github.io/assets/img/postImages/2025-03-13-server.png)

그리고 서비스 포트에서 간단하게 만든 API를 실행하면 될 것이고, 이를 게이트웨이 포트에서도 실행해서 되면 서버-게이트웨이-서비스 간 연결을 확인할 수 있을 것이다.

![api-test](https://zzangkkmin.github.io/assets/img/postImages/2025-03-13-api-test.png)

## 다음 단계는...

실행하고, 연결되는 것 까진 해보았다. 그러나 아직 나머지 설정들이 남아있다. 메세징 큐 / 남은 서비스 연결 / 흐름 / 인증 및 보안 등등 설정하고 서비스를 짜는 일이 남았다. 그건 후에 해보고 기록을 남기도록 한다.