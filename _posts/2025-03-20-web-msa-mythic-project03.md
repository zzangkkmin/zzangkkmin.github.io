---  
layout: post  
title: "MSA 프로젝트 도전기 03"
subtitle: "MSA project 03"  
categories: web  
tags: [msa]   
comments: true  
# header-img: img/review/2019-11-22-review-book-pycharm-1.jpg  
---  
  
## 개요

각자 맡은 도메인별로 CRUD를 구성하고 있다. 그래서 어느정도 기본적인 것은 구현이 된 상태이다. 이제 남은 건 서비스간의 호출이다. Feign클라이언트로 이용해서 다른 서비스 api 호출을 할 수 있도 다른 방법은 이벤트 메세징을 통해 다른 서비스를 호출하는 방법이 있다. 그 중 내가 맡은 건 **Kafka를 이용한 이벤트 메세징** 이다.

## Kafka 

우선 **Kafka**가 뭔지, 그리고 메세징 큐가 뭔지에 대해서 간략하게 알아본다. Kafka는 분산 스트리밍 플랫폼으로, 주로 실시간 데이터 피드의 빅 데이터 처리를 목적으로 사용된다. **메시지 큐**로 사용할 수 있으며, 대용량 데이터 스트림을 저장하고 실시간으로 분석하거나 처리하는 데 중점을 둔다. 여기서 현 프로젝트의 메세징 큐 처리를 kafka를 이용하여 처리할 것이다.  
kafka를 사용하면 좋은 점은 대용량 데이터를 실시간으로 처리하고, 다양한 소스에서 데이터를 수집하고 이를 통합, 분석하는데 이점이 있다. 또한 데이터 손실없이 안정적으로 데이터를 저장할 수 있다. 다만, 설정과 운영을 하는 데 꽤나 복잡하고 러닝 커브가 높아 이해하는 데 꽤 걸릴 수 있다. 이점에 대해서 하루동안은 카프카를 어떻게 사용하는지 설정하는지 파보는 시간을 도중에 잡았었다. 그래도 어려운 편은 맞다.  

Kafka의 기본 구성 요소는 다음과 같다.
- **메세지**: Kafka 데이터 전달 단위, key-value-timestamp, 그리고 몇 가지 메타데이터로 구성되어 있다.
- **프로듀서**: 메세지 생성하고 Kafka에 보내는 역할, 정확히는 설정된 Kafka 서버의 특정 토픽에 메세지를 전송한다.
- **토픽**: 메세지 저장 장소, 토픽에 저장 되었다 소비자에게 전달된다.
- **파티션**: 토픽을 물리적으로 나눈 단위, 데이터 병렬 처리
- **키**: 메세지를 특정 파티션에 할당하는 데 사용되는 값 
- **컨슈머**: 토픽에서 메세지를 가져와 처리하는 역할, 특정 컨슈머 그룹에 속하며 같은 그룹 내 컨슈머들끼리 토픽의 파티션을 분산 처리
- **브로커**: Kafka 클러스터의 각 서버를 의미하며, 메시지를 저장하고 전송하는 역할.
- **주키퍼**: Kafka 클러스터를 관리하고 조정하는데 사용되는 분산 코디네이션 서비스

위의 구성요소들을 도식화 및 구조화하면 아래 그림과 같다.
![공통 모듈](https://zzangkkmin.github.io/assets/img/postImages/2025-03-20-prj-kafka-structure.png)

## 설정 및 테스트
카프카가 무엇인지 알았다면 바로 설정부터 들어가본다. 우선 kafka 서버가 존재해야 한다. 그 서버는 이미 docker-compose에 따라 도커 컨테이너에 자리잡았다.  
그렇다면 KafkaConfig 파일을 만들어 설정을 해줘야 한다. 모든 서비스가 카프카를 통해 메세징 큐를 이용할 것이니 공통 모듈에 구성을 한다.
```java
@Configuration
@EnableKafka
public class KafkaConfig {

    /**
     * 각 서비스별 kafka서버 및 그룹아이디 지정 필요
     */
    @Value("${spring.kafka.bootstrap-servers}")
    private String bootstrapServers;

    @Value("${spring.kafka.consumer.group-id}")
    private String groupId;

    /**
     * 공통 kafka template
     * @return
     */
    @Bean
    public KafkaTemplate<String, Object> kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }

    /**
     * 공통 Producer 설정: 이벤트 타입에 관계없이 Object로 처리
     * @return
     */
    @Bean
    public ProducerFactory<String, Object> producerFactory() {
        Map<String, Object> configProps = new HashMap<>();
        configProps.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
        configProps.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        configProps.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, JsonSerializer.class);
        return new DefaultKafkaProducerFactory<>(configProps);
    }

    /**
     * 공통 Consumer 설정: Object 타입으로 수신하고, 각 서비스에서 원하는 타입으로 캐스팅/변환
     * @return
     */
    @Bean
    public ConsumerFactory<String, Object> consumerFactory() {
        Map<String, Object> configProps = new HashMap<>();
        configProps.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
        configProps.put(ConsumerConfig.GROUP_ID_CONFIG, groupId);
        configProps.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        configProps.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, JsonDeserializer.class);
        // 모든 패키지의 POJO를 신뢰하도록 설정
        configProps.put(JsonDeserializer.TRUSTED_PACKAGES, "*");
        return new DefaultKafkaConsumerFactory<>(configProps, new StringDeserializer(), new JsonDeserializer<>(Object.class));
    }

    /**
     * 공통 kafka listener container factory
     * @return
     */
    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, Object> kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory<String, Object> factory =
            new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory());
        return factory;
    }
}
```
우선 먼저 yml파일에 `spring.kafka.bootstrap-servers`와 `spring.kafka.consumer.group-id`를 설정한 후, KafkaConfig 클래스를 만든다. `@EnableKafka`를 통해 kafka 사용 설정임을 선언해야 한다.  
이 설정에선 프로듀서와 컨슈머 그리고 kafka 리스너 설정을 스프링 빈으로 등록되어 공통 모듈로서 사용할 수 있게 만든다. 프로듀서엔 서버와 키-벨류 직렬화 설정을, 컨슈머엔 서버와 키-벨류 직렬화 설정 그리고 그룹아이디를, kafka 리스너 설정은 컨슈머 팩토리 설정을 해준다. 또한 형태가 모두 <Sring, Object>로 이루어져 있는데, 이 말은 String 키에 Object 벨류로서 벨류가 Object인 것을 json으로 나타낼 것이다.  
  
그렇다면 이를 어떻게 적용할 것인가? 예를 들어 주문 도메인에 주문 생성이란 api를 호출시 제품 도메인이 이 호출 이벤트를 받고 어떤 작업을 한다 가정을 한다.  
그러면 프로듀서는 주문 도메인이 되겠고 컨슈머는 제품 도메인이 될것이다. 그러면 Kafka용 이벤트 프로듀셔 서비스를 주문 도메인에 만들고, 해당 이벤트를 읽을 컨슈머 서비스를 제품 도메인에 만들면 되겠다.

```java
@Service
public class OrderEventProducer {
    private final KafkaTemplate<String, Object> kafkaTemplate;

    public OrderEventProducer(KafkaTemplate<String, Object> kafkaTemplate) {
        this.kafkaTemplate = kafkaTemplate;
    }

    public void sendOrderCreateEvent(OrderCreateEvent orderCreateEvent) {
        kafkaTemplate.send("order-create-event",
            orderCreateEvent.getOrderId().toString(), orderCreateEvent);
    }
}
```

우선 이벤트 메세지를 보내는 프로듀서에선 KafkaTemplate을 이용하여 이벤트를 보낼 것이다. 이 부분이 바로 sendOrderCreateEvent 메서드 부분이다. 해당 메서드에서 "order-create-event"란 토픽을 지정하여 이벤트를 kafka 서버에 보내지게 될것이다.

```java
@Service
public class OrderEventConsumer {
    private final KafkaTemplate<String, Object> kafkaTemplate;

    public OrderEventConsumer(KafkaTemplate<String, Object> kafkaTemplate) {
        this.kafkaTemplate = kafkaTemplate;
    }

    @KafkaListener(topics = "order-create-event", groupId = "order-group")
    public void handleOrderCreateEvent(Object response) {
        System.out.println(response);
    }
}
```

다음으로 이벤트를 받는 컨슈머에선 KafkaListener 어노테이션으로 지정된 handleOrderCreateEvent메서드를 통해 보낸 이벤트를 받을 것이다. 받은 이벤트를 토대로 컨슈머에서 작업을 해주면 된다.

## 여기서 겪은 문제점

kafka 이벤트 메세지를 통해 프로듀서/컨슈머, 즉 도메인 간 주고 받는 것을 시도할 수 있었다. 하지만 여기서 유의해야 할 점이 몇가지 있다.  

첫번째 문제는 보낼 메세지 형태가 Object라 **A서비스에서 A서비스만이 있는 이벤트 타입으로 보낼 때 B서비스에서 해당 타입을 인식할 수 없다**는 점이다.  
설정에 보시면 value가 Object로 되어있다. String이라면, String값으로 보내지겠지만, json형태로 직렬화/역직렬화를 거쳐 서비스간의 메세지를 주고 받아야 한다. 그러나, Json임에도 불구하고, 컨슈머는 메세지를 받을 때 해당 타입을 자기 서비스 내에서 찾게 될 것이다. 그러나 맞는 타입이 없어서 오류가 난다.
그렇다면 서비스간에 해당 이벤트 타입을 서로 알아야 한다라는 조건이 붙게 되고 이를 해결하는 방법은 **공용 이벤트 타입으로 만들어서 서로 알게 만드는 것**이다. 이미 공통 모듈이 있으니 여기에 집어넣으면 딱일 것이다....  
근데 좀 더 생각해보면 공통적인 양식만 있으면 될 것이다. A서비스는 공용 이벤트 양식 C를 상속받은 a이벤트를 생성하고, 보낼 때 공용 이벤트 양식 C로 보내면 될 것이다.
  
두번째 문제는 **보낼 메세지 형태인 Object가 JSON으로 만들수 있게 되어 있는지** 확인해야 하는 점이다.  
Object에서 Json으로 변형시 직렬화 역질렬화 선언하는 것이 이미 설정에 나와있다. 그렇게 때문에 Object가 JSON으로 변형하려면 몇가지 조건이 필요하다.
Object가 일반 자바 빈, 즉 POJO형태여야 한다. 더 정확히 말하자면, **getter, setter 메서드가 있어야 하고, 기본 생성자(매개변수 없는 생성자)가 있어야 한다.**
이 조건을 지키지 않는다면 이벤트 메세지 보낼 때 JSON으로 변환할 수 없는 오류가 발생한다.  
  
그리고 세번째 문제는 **kafka 메세지 오류시 반복적으로 알아서 호출한다는 점**이다.  
전에 두 문제로 인하여 kafka 메세지 발송이 오류가 발생한다. 그때 오류가 나는 서비스에서 해당 kafka 메세지 수신을 반복적으로 진행하고 있었다. 이 서비스를 껏다 켜도 같은 상황이 반복되었고, 해당 kafka 컨테이너를 닫으니 서버 연결할수 없다고 반복적으로 뜨고 있다. 즉, 이는 서비스 내부적으로로 kafka 메세징 처리는 반복적으로 이루어지고 있다는 점이다. 이를 해결하려면 서버를 닫는게 아니라 **해당 메세지의 토픽을 삭제**하면 된다. 삭제하는 방법은 kafka UI 설정에 접속을 하여 문제가 있는 토픽을 제거하면 된다. 다만 해당 토픽에 있는 다른 메세지들도 사라질 수 있으니 신중히 고민해보고 삭제하기를 권장한다.

![kafka-ui](https://zzangkkmin.github.io/assets/img/postImages/2025-03-20-prj-kafka-ui.png)