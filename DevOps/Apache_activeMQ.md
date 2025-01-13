# Apache ActiveMQ Artemis Pub-Sub 구조 스터디

## 1. Apache ActiveMQ Artemis 소개
Apache ActiveMQ Artemis는 고성능 메시징을 제공하는 메시지 브로커입니다. Pub-Sub (Publish-Subscribe) 구조를 지원하며, 비동기 메시징 시스템을 구현하는 데 적합합니다.

## 2. Pub-Sub 구조란?
Pub-Sub 구조는 메시지를 발행(Publish)하는 Publisher와 메시지를 구독(Subscribe)하는 Subscriber 간의 비동기 통신 모델입니다. 이 구조는 메시지를 주고받는 두 구성 요소가 직접 연결되지 않고, 메시지 브로커를 통해 통신합니다.

### 특징
- 메시지를 여러 Subscriber에게 동시에 전달 가능
- Subscriber는 특정 주제(Topic)에 대해 메시지를 구독
- Publisher는 구독자에 대해 알 필요 없이 메시지를 브로커에 발행

## 3. 주요 개념
### Topic
- 메시지가 발행되고 구독되는 채널입니다.
- 여러 Subscriber가 동일한 Topic을 구독할 수 있습니다.

### Publisher
- 특정 Topic에 메시지를 발행하는 주체입니다.

### Subscriber
- 특정 Topic에서 메시지를 구독하고 처리하는 주체입니다.

## 4. Apache ActiveMQ Artemis 설정

### 4.1 설치
1. [Apache ActiveMQ Artemis 다운로드](https://activemq.apache.org/components/artemis/download/)
2. 압축 해제 후 Artemis 인스턴스 생성:
   ```bash
   ./bin/artemis create mybroker
   ```
3. 브로커 실행:
   ```bash
   ./mybroker/bin/artemis run
   ```

### 4.2 브로커 설정
- `broker.xml` 파일에서 Topic 관련 설정을 확인하거나 수정합니다.
  ```xml
  <address-settings>
    <address-setting match="#">
      <dead-letter-address>DLQ</dead-letter-address>
      <expiry-address>ExpiryQueue</expiry-address>
      <redelivery-delay>0</redelivery-delay>
      <max-size-bytes>10485760</max-size-bytes>
      <address-full-policy>PAGE</address-full-policy>
    </address-setting>
  </address-settings>
  ```

## 5. Pub-Sub 구현 예제

### 5.1 Maven 의존성 추가
```xml
<dependency>
  <groupId>org.apache.activemq</groupId>
  <artifactId>artemis-core-client</artifactId>
  <version>2.28.0</version>
</dependency>
<dependency>
  <groupId>org.apache.activemq</groupId>
  <artifactId>artemis-jms-client</artifactId>
  <version>2.28.0</version>
</dependency>
```

### 5.2 Java 코드 구현
#### Publisher
```java
import javax.jms.*;
import org.apache.activemq.artemis.jms.client.ActiveMQConnectionFactory;

public class Publisher {
    public static void main(String[] args) {
        String url = "tcp://localhost:61616";
        String topicName = "exampleTopic";

        try (ActiveMQConnectionFactory connectionFactory = new ActiveMQConnectionFactory(url);
             JMSContext context = connectionFactory.createContext()) {

            Topic topic = context.createTopic(topicName);
            JMSProducer producer = context.createProducer();

            for (int i = 0; i < 10; i++) {
                String message = "Message " + i;
                producer.send(topic, message);
                System.out.println("Sent: " + message);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

#### Subscriber
```java
import javax.jms.*;
import org.apache.activemq.artemis.jms.client.ActiveMQConnectionFactory;

public class Subscriber {
    public static void main(String[] args) {
        String url = "tcp://localhost:61616";
        String topicName = "exampleTopic";

        try (ActiveMQConnectionFactory connectionFactory = new ActiveMQConnectionFactory(url);
             JMSContext context = connectionFactory.createContext()) {

            Topic topic = context.createTopic(topicName);
            JMSConsumer consumer = context.createConsumer(topic);

            consumer.setMessageListener(message -> {
                if (message instanceof TextMessage) {
                    try {
                        System.out.println("Received: " + ((TextMessage) message).getText());
                    } catch (JMSException e) {
                        e.printStackTrace();
                    }
                }
            });

            System.out.println("Subscriber is now listening to the topic...");
            Thread.sleep(10000); // Keep the program running to receive messages
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## 6. 테스트
1. 브로커 실행
2. Subscriber 실행
3. Publisher 실행
4. Subscriber에서 메시지 수신 확인

## 7. 주의사항
- 메시지 브로커와 클라이언트 간의 연결 상태를 항상 확인
- 메시지 유실 방지를 위해 적절한 QoS 설정 필요

## 8. 참고 자료
- [Apache ActiveMQ Artemis 공식 문서](https://activemq.apache.org/components/artemis/)
- [JMS API 소개](https://docs.oracle.com/javaee/7/tutorial/jms-concepts.htm)
