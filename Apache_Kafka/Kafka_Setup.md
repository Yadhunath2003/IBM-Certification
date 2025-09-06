# Create a Topic and Start Producer

You need to create a topic before posting messages.

Open a new terminal and navigate to the `kafka_2.13-3.8.0` directory:

```sh
cd kafka_2.13-3.8.0
```

To create a topic named `news`, run:

```sh
bin/kafka-topics.sh --create --topic news --bootstrap-server localhost:9092
```

You should see the message: `Created topic news`.

To start a producer and send messages to Kafka, run:

```sh
bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic news
```

After the producer starts and you see the `>` prompt, type any text message and press enter. For example, you can send these three messages:

```
Good morning
Good day
Enjoy the Kafka lab
```

---

# Start Consumer

To read messages from Kafka, start a consumer.

Open a new terminal and navigate to the `kafka_2.13-3.8.0` directory:

```sh
cd kafka_2.13-3.8.0
```

Run the following command to listen to messages in the `news` topic:

```sh
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic news --from-beginning
```

You should see all the messages sent from the producer appear here. You can continue sending messages from the producer terminal, and they will appear in the consumer terminal.

---

# Explore Kafka Directories

Kafka uses the `/tmp/kraft-combined-logs` directory to store messages.

Open a new terminal and navigate to the `kafka_2.13-3.8.0` directory:

```sh
cd kafka_2.13-3.8.0
```

List the contents of the root directory:

```sh
ls
```

Notice the `tmp` directory. The `kraft-combined-logs` inside the `tmp` directory contains all the logs. To check the logs generated for the `news` topic, run:

```sh
ls /tmp/kraft-combined-logs/news-0
```

---

# Clean Up

- **To stop the producer:** In the terminal running the producer, press `CTRL+C`.
- **To stop the consumer:** In the terminal running the consumer, press `CTRL+C`.
- **To stop the Kafka server:** In the terminal running the Kafka server, press `CTRL+C`.
