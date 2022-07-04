
Confluent Cloud Schema Registry and Spring Boot
===============================================

Confluent Cloud Schema Registry is fully managed and works easily: When
you create a Confluent Cloud cluster, you have the ability to set up
Confluent Cloud Schema Registry, and the only thing you need to do is
specify where you want to create it. It supports Avro, Protobuf, and
JSON Schema formats for producers, consumers, and Kafka Streams. You can
also put multiple schemas in one application. Confluent provides
implementations of serializers and deserializers for all of the formats,
but you do need to put the libraries in your classpath.

To configure a Spring Boot application for use with Confluent Cloud
Schema Registry, you need to adjust
`application.properties`:


``` {.language-java}
# Confluent Cloud Schema Registry
spring.kafka.properties.basic.auth.credentials.source=USER_INFO
spring.kafka.properties.basic.auth.user.info={{ SR_API_KEY }}:{{ SR_API_SECRET }}
spring.kafka.properties.schema.registry.url=https://sr.confluent.cloud

spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.IntegerSerializer
spring.kafka.producer.value-serializer=io.confluent.kafka.serializers.KafkaAvroSerializer

spring.kafka.consumer.value-deserializer=io.confluent.kafka.serializers.KafkaAvroDeserializer
spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.IntegerDeserializer
```


You'll need an API key, a password, and your Confluent Cloud Schema
Registry URL from Confluent Cloud, and you'll need to specify the
various serializers that you are using. Also, as you can see, some of
the serializers/deserializers come from the
`io.confluent.kafka` package, so you will need to add a
JAR to your local application to use them. You can do so in
`build.gradle`:


``` {.language-gradle}
repositories {
    mavenCentral()
    maven {
        url "https://packages.confluent.io/maven"
    }
}
```


Then you need to add implementations for the serializers/deserializers
that you are using, for example:


``` {.language-gradle}
implementation 'org.apache.avro:avro:1.10.2'
implementation 'io.confluent:kafka-avro-serializer:6.1.0'
```
