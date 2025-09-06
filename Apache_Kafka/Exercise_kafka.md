
## Exercise for Kafka command

1. **Create a new topic named `weather`:**
    ```sh
    bin/kafka-topics.sh --create --topic weather--bootstrap-server localhost:9092
    ```

2. **Post messages to the topic `weather`:**
    ```sh
    bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic weather
    ```

3. **Read the messages from the topic `weather`:**
    ```sh
    bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic weather
    ```
```