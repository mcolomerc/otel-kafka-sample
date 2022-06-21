

```shell
gradle build
```

```sh
gradle shadowJar
```

```sh
java -cp build/libs/java-otel-0.0.1.jar demo.otel.producer.DemoProducer build/resources/main/kafka.properties
```

```sh
java -cp build/libs/java-otel-0.0.1.jar demo.otel.consumer.DemoConsumer build/resources/main/kafka.properties
```