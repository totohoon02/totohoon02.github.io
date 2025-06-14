# 레디스의 키 만료 로직

```java
public class RedisKeyExpiredListener extends KeyExpirationEventMessageListener {
    public RedisKeyExpiredListener(RedisMessageListenerContainer listenerContainer) {
        super(listenerContainer);
    }

    @Override
    public void onMessage(Message message, byte[] pattern) {
        String key = message.toString();

        if (key.startsWith("my-key")) {
            ...
        }
    }
}
```

- Redis는 TTL을 설정한 키를 만료시키기 위해 주기적으로 키를 스캔
- 키가 만료되어 삭제 시 키 삭제 이벤트 발생
- 만료된 키는 Pub/Sub을 통해 메시지를 퍼블리싱
- KeyExpirationEventMessageListener가 Redis의 만료 메시지를 구독하고 있음
- "my-key"라는 메시지가 들어오면 로직 실행
