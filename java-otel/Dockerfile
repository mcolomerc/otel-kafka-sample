ARG BUILD_HOME=/app

# BUILDER ##################################################################### 
FROM gradle:jdk11 as builder

ARG OTEL_VERSION=1.15.0 
ARG BUILD_HOME
ENV APP_HOME=$BUILD_HOME
WORKDIR $APP_HOME 

RUN  apt-get update \
  && apt-get install -y wget \
  && rm -rf /var/lib/apt/lists/*

RUN wget https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/download/v${OTEL_VERSION}/opentelemetry-javaagent.jar

RUN wget https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/0.17.0/jmx_prometheus_javaagent-0.17.0.jar

COPY --chown=gradle:gradle . $APP_HOME
 
RUN gradle --no-daemon clean build

RUN gradle --no-daemon shadowJar 

# APP #####################################################################
 
FROM openjdk:11-jre-slim as app

ARG MAIN_CLASS=demo.otel.producer.DemoProducer
ARG PROPS=kafka.properties
ARG BUILD_HOME 

ENV MAIN_CLASS ${MAIN_CLASS}
ENV PROPS ${PROPS} 
ENV TOPIC mcolomer-inventory 
ENV APP_HOME=$BUILD_HOME
ENV APP_NAME="app"  

ENV COLLECTOR_ENDPOINT="http://collector:4317"
 
ENV JAVA_XMS=2048m
ENV JAVA_XMX=2048m
 
ENV JAVA_TOOL_OPTIONS="-Xms${JAVA_XMS} \
  -Xmx${JAVA_XMX} \
  -Dapplication.name=${APP_NAME} \
  -Dapplication.home=${APP_HOME} \ 
  -javaagent:opentelemetry-javaagent.jar \
  -Dotel.traces.exporter=otlp \
  -Dotel.metrics.exporter=otlp \
  -Dotel.exporter.otlp.endpoint=${COLLECTOR_ENDPOINT} \
  -Dotel.resource.attributes=service.name=${APP_NAME} \
  -Dcom.sun.management.jmxremote.ssl=false \
  -Dcom.sun.management.jmxremote.authenticate=false \
  -Dcom.sun.management.jmxremote.port=5000 \
  -Dcom.sun.management.jmxremote.rmi.port=5000 \
  -Dcom.sun.management.jmxremote.host=0.0.0.0 \
  -Djava.rmi.server.hostname=0.0.0.0 \
  -javaagent:jmx_prometheus_javaagent-0.17.0.jar=1234:kafka-client.yml"

COPY ./prometheus/kafka-client.yml kafka-client.yml

COPY --from=builder $APP_HOME/jmx_prometheus_javaagent-0.17.0.jar jmx_prometheus_javaagent-0.17.0.jar
COPY --from=builder $APP_HOME/opentelemetry-javaagent.jar opentelemetry-javaagent.jar
COPY --from=builder $APP_HOME/build/libs/*.jar app.jar
COPY --from=builder $APP_HOME/config/kafka.properties kafka.properties
#
# The command to run when the container starts.
#
ENTRYPOINT java -cp /app.jar ${MAIN_CLASS} ${PROPS}  