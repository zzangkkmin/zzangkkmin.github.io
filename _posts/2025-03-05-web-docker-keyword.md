---  
layout: post  
title: "Docker 주요 키워드 & 명령어"  
subtitle: "docker keyword"  
categories: web  
tags: [msa]   
comments: true  
# header-img: img/review/2019-11-22-review-book-pycharm-1.jpg  
---  
  
## Docker 주요 키워드
- **이미지**
  - 애플리케이션과 모든 실행에 필요한 파일을 포함한 읽기 전용 템플릿
  - 코드, 런타임, 라이브러리, 환경 변수, 구성 파일 등이 포함
  - 컨테이너를 생성하기 위한 청사진 역할
- **컨테이너**
  - 이미지를 실행하여 동작하는 애플리케이션 인스턴스
  - 실제로 애플리케이션이 실행되는 동적인 환경
  - 격리된 공간에서 애플리케이션을 실행, 필요한 모든 의존성을 포함
  - 하나의 시스템에서 여러 개의 컨테이너를 독립적으로 실행 가능
- **Dockerfile**
  - 이미지를 생성하기 위한 명령어가 담긴 스크립트 파일
  - 빌드시 필요 명령어 포함
- **Docker Hub**
  - 이미지를 저장하고 공유하는 중앙 저장소
  - 사용자 계정으로 자기 이미지 다운/업로드 및 공개 이미지 다운 가능
- **볼륨**
  - 컨테이너 데이터를 지속적으로 저장하는 메커니즘
  - 테이너가 삭제되더라도 볼륨에 저장된 데이터는 유지
  - 데이터를 컨테이너와 독립적으로 관리
- **네트워크**
  - 컨테이너 간의 통신을 관리하는 방식
  - 네트워크 종류
    - **Bridge Network (브리지 네트워크)**
      - 기본적으로 Docker가 컨테이너를 실행할 때 사용하는 네트워크
      - 동일한 브리지 네트워크에 연결된 컨테이너들은 서로 통신 가능
      - 외부 네트워크와는 NAT (*내부 네트워크의 여러 장치가 하나의 공용 IP 주소를 통해 외부 네트워크와 통신할 수 있도록 IP 주소를 변환하는 기술*) 를 통해 통신
      - 일반적으로 단일 호스트에서 여러 컨테이너를 연결할 때 사용
      - 명시하지 않으면 모두 브리지 네트워크에서 실행
            
        ```bash
        docker network create my-bridge-network
        docker run -d --name container1 --network my-bridge-network nginx
        docker run -d --name container2 --network my-bridge-network nginx
        ```
            
    - **Host Network (호스트 네트워크)**
      - 컨테이너가 호스트의 네트워크 스택을 직접 사용
      - 네트워크 격리가 없기 때문에 성능상 이점이 있지만, 보안 및 네트워크 충돌 위험 가능성
      - 일반적으로 성능이 중요한 애플리케이션에 사용
            
        ```bash
        docker run -d --network host nginx
        ```
            
    - **Overlay Network (오버레이 네트워크)**
        - 여러 Docker 호스트에 걸쳐 있는 컨테이너를 연결할 때 사용
        - Swarm 모드(*Docker 컨테이너의 오케스트레이션과 클러스터링을 지원하여 여러 호스트에서 컨테이너를 관리하고 배포할 수 있는 기능*)나 Kubernetes 같은 오케스트레이션 도구와 함께 사용
        - 데이터 센터 또는 클라우드 환경에서 분산 시스템을 구축할 때 유용.

## Docker 주요 명령어
- **Docker 이미지 관련 명령어**
  - 이미지 빌드
  
    ```bash
    docker build -t myapp:latest .
    # Dockerfile 필수
    # -t 옵션: myapp 이름의 latest 태그를 붙임가능
    ``` 

  - 이미지 가져오기
  
    ```bash
    docker pull myboys
    # 도커 허브에서 이미지 가져오기
    # docker pull {계정/이름}:{태그}
    ``` 

  - 이미지 목록 보기
  
    ```bash
    docker images
    ``` 

  - 이미지 삭제
  
    ```bash
    docker rmi myapp:latest
    ``` 

- **Docker 컨테이너 관련 명령어**
  - 컨테이너 실행
    
    ```bash
    docker run -d -p 8080:80 myapp:latest
    # -d: 백그라운드 실행
    # -p: 포트맵핑 8080을 80으로
    ```
    
  - 컨테이너 내부 접속
    
    ```bash
    docker exec -it {containerId} /bin/bash
    # -i: 컨테이너의 표준 입력(STDIN) 열기
    # (컨테이너 내부에서 사용자 입력을 받을 수 있음)
    # -t: 가상 터미널 할당
    # (컨테이너 내부에서 터미널 사용 가능)
    ```
    
  - 실행 중인 컨테이너 목록 보기
    
    ```bash
    docker ps -al
    # -a: 중지된 컨테이너 포함, 모든 컨테이너 목록표시
    # -l: 마지막 실행 컨테이너 먼저 나열 
    ```
    
  - 컨테이너 중지
    
    ```bash
    docker stop {containerId}
    ```
    
  - 컨테이너 시작
    
    ```bash
    docker start {containerId}
    ```
    
  - 컨테이너 삭제
 
    ```bash
    docker rm {containerId}
    ```

- **Docker 네트워크 및 불륨 관련 명령어**
  - 네트워크 생성
    
    ```bash
    docker network create mynetwork
    ```
    
  - 네트워크 목록 보기
    
    ```bash
    docker network ls
    ```
    
  - 네트워크 삭제
    
    ```bash
    docker nework rm mynetwork
    ```
    
  - 볼륨 생성
    
    ```bash
    docker volume create myvolume
    ```
    
  - 볼륨 목록 보기
    
    ```bash
    docker volume ls
    ```
    
  - 볼륨 삭제
    
    ```bash
    docker volume rm myvolume
    ```
