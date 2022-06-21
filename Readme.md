# Open Telemetry sample 

## Confluent Cloud Kafka Cluster

```sh
cd java-otel/config 
```

**kafka.properties** 

```properties
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username=<API_KEY> password=<API_SECRET>;
```

## Kafka Java Producer & Consumer 

# Docker Compose 
 
 - Prometheus 
 - Jaeger
 - Zipkin 
 - OpenTelemetry collector 
 - Java Producer 
 - Java Consumer 

```sh
docker-compose up -d
```


