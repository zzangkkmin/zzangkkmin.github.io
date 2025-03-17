---  
layout: post  
title: "MSA 프로젝트 도전기 02"
subtitle: "MSA project 02"  
categories: web  
tags: [msa]   
comments: true  
# header-img: img/review/2019-11-22-review-book-pycharm-1.jpg  
---  
  
## 개요  
지난 주엔 프로젝트 설계와 설정 그리고 서버-게이트웨이-서비스 연결 테스트를 진행하였다. 그렇다면 이제 각자 맡은 역할에 대해서 우선 가장 기본적인 CRUD와 써치 기능을 구현하고 테스트를 해봐야 한다.

## 개발하면서 나온 공통적인 매커니즘 

일단 내가 맡은 부분은 제품 부분이다. 이 부분의 기능으로는 생성, 수정, 삭제, 단건 조회, 전체 조회, 찾기가 있다. 즉 일단 가장 기본적인 CRUD의 기능이라 할 수 있겠다. 그러나 이 제품 도메인 말고, 다른 도메인들도 CRUD의 기능들을 가지고 있다. 이 말은 즉, 도메인들이 공통적으로 사용할 수 있는 부분들이 있다는 것이다. 이를 위해 도메인이 사용할 수 있는 공통 기능을 `공통 모듈`로 관리를 해야 한다는 것이다. **그리하여 공통 모듈 프로젝트를 만들고 도메인들이 이 프로젝트를 사용하도록 만들어야 한다.**

현재 모노리포에 서비스 모듈들이 있었다. 이제 여기서 공통모듈 프로젝트를 추가한다. 이 공통모듈 프로젝트에 추가해야할 건 추후에 변경될 것이다. 일단 추가되는 공통요소로는 예외처리, 공통적인 유효성 검사, Audit필드용 기본 엔티티, 페이징 처리, 공통 응답 양식 등이 있다. 이러한 부분들을 공통모듈 내 적절한 폴더로 추가를 해놓는다. 
![공통 모듈](https://zzangkkmin.github.io/assets/img/postImages/2025-03-17-common-module.png)

공통모듈들을 설정했다면 이제 다른 서비스 내에서 이 공통모듈을 사용해야 한다. 논의 하면서 나온 사용 방법은 여러가지가 있었다.
1. 공통모듈들을 jar로 만들어 각 서비스에 넣어둔다.
2. 공통모듈을 다른 레포지토리로 만들고 서비스내 서브모듈화하여 넣어둔다.

방법 1같은 경우 공동모듈들을 만들때 마다 빌드를 해야하고 각 서비스에 심어주어야 하는 불편함이 존재한다. 방법 2같은 경우 따로 관릴르 해야되고, git 서브모듈을 이용하다보면 꼬일 수 있는 문제, 즉 잘못 사용할 가능성이 높은 편이다.  
그래서 나온 방법으로는 **공통모듈을 각 서비스의 의존성 주입해준다** 였다.
일단 현재 프로젝트 구조를 간단히 설명하자면 다음과 같다. 상위 프로젝트에 빌드 파일이 있고 그 빌드 파일로 하위 프로젝트 빌드도 되는 구조이다.
그래서 현재 상위 프로젝트의 setting.gradle에 만든 공통 모듈을 포함한 모든 서비스를 모두 include를 시키고, 나머지 하위 프로젝트에는 setting.gradle이 없어야 한다.
![공통 모듈](https://zzangkkmin.github.io/assets/img/postImages/2025-03-17-prj-structure.png)

그리고 공통모듈을 사용할 서비스의 build.gradle과 실행클래스도 편집이 필요하다. 먼저 build.gradle의 dependencies에는 아래의 내용을 추가해 줌으로써 서비스 내 공통모듈 사용을 할 수 있게 된다.
```YAML
implementation project(':com.bulbas23r.common')
```

그 다음, 실행클래스인 application 클래스의 `@SpringBootApplication`의 설정을 아래와 같이 변경해준다. 이는 공통모듈에 지정한 bean객체도 스캔되어 사용할 수 있다는 것이다.
```java
@SpringBootApplication(scanBasePackages = {"서비스 패키지 ID", "common"})
```

## Q 객체
공통 모듈을 적용시키고 미리 CRUD가 완성된 다른 도메인도 git pull로 당겨왔다. 그런데, 검색기능에서 JpaQueryRepository를 구현하는 데서 이상한 점이 발견되었다. 특정 도메인 객체에 Q가 붙은 객체이다.
```java
@Repository
@RequiredArgsConstructor
public class ProductQueryRepositoryImpl implements ProductQueryRepository {

    private final JPAQueryFactory queryFactory;

    QProduct product = QProduct.product;

    ...

}
```

위 검색기능을 이용하려 해도 QProduct라는 게 소스상에선 없었다. 그럼 작성자가 잘못 올린것일까? 그건 아니다.
Q가 붙은 객체는 **Q객체**라 불리며 QueryDSL에서 엔티티의 필드와 속성을 타입 안전하게 참조할 수 있도록 만들어진 메타모델 클래스이다. 
예를 들어, 엔티티 클래스 Product가 있다면, QueryDSL의 애너테이션 프로세서가 이를 분석해 QProduct라는 클래스를 생성하게 된다. 
이 Q 객체를 사용하면, product.name과 같이 엔티티의 속성에 대해 컴파일 타임에 타입 체크를 할 수 있어 쿼리 작성 시 실수를 줄이고, 코드 자동 완성 등의 이점이 있다는 것이다.
아래는 사용 예시이다.
```java
  QProduct product = QProduct.product;
  product.name.containsIgnoreCase(keyword);
```

Q객체임을 알게되었다면 이제 왜 오류가 날까? 그 이유는 **QueryDsl의 컴파일 과정**을 거치지 않았기 때문이다.
QueryDSL은 컴파일 타임에 동작하는 애너테이션 프로세서를 통해 엔티티 클래스를 스캔하고, 그에 상응하는 Q 객체들을 자동으로 생성해준다.
그리고 해당 과정은 프로젝트 빌드 시 수행되기 때문에, 빌드가 완료되어야 QueryDSL이 컴파일 과정을 거치며 Q 객체를 자동으로 생성되는 것이다.
그렇다면 이를 위해 프로젝트를 빌드해야 한다. 빌드하는 방법은 간단하다. 해당 프로젝트 경로에서 gradlew파일을 이용하여 build를 해주면 된다.
빌드 중 따로 test 작업을 제외하려면 `-x test`를 붙인다.
```
./gradlew build -x test
```

## 다음 단계는...

이리하여 공통 모듈을 설정하고 QueryDsl의 Q객체에 대하여 알아보았다. 그리고 남은건 kafka 이벤트 메시징과 다른 도메인 개발 그리고 남은 설정들도 남아있다.
물론 개발하면서 공통 모듈들도 업데이트 될 것이다.