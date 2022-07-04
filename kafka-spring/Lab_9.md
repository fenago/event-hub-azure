Process Messages with KafkaStreams and Spring Boot
==================================================


The great thing about using Kafka Streams with Spring Boot is that you
can quickly start focusing on your Kafka Streams topologies---your
KStreams and your KTables---because you don't need to worry about
lifecycles. Spring takes care of them. This means that you don't need to
manually create an instance of a Kafka Streams object, start it and stop
it, etc. However, you can still get access to the object if you need it.

To autoconfigure Kafka Streams support in Spring Boot, you simply need
to add the annotation `@EnableKafkaStreams`. If you have
Kafka Streams JARs in your classpath, they will be picked up by the
autoconfiguration. Alternatively, you can enable your configuration
explicitly.

Spring provides a Jackson-based JSON SerDes plus a SerDes for Kafka
Streams. So in the use cases where you don't need to rely on Confluent
Schema Registry---for example, if you are writing a standalone
application---the JSON serialization works out of the box.

If you need to monitor your application, which you generally should,
Kafka Streams exposes some methods through JMX, and Spring for Apache
Kafka provides a wrapper around those metrics and makes them available
through the Micrometer framework. This allows you to consume the metrics
with other frameworks and dashboard tools. In addition, Spring provides
some out-of-the-box implementations for error handling.

Here is a typical configuration for Kafka Streams in Spring Boot:


``` {.language-java}
    @Configuration
    @EnableKafka
    @EnableKafkaStreams 
    public class KafkaStreamsConfig {

        @Bean(name = 
        KafkaStreamsDefaultConfiguration.DEFAULT_STREAMS_CONFIG_BEAN_NAME)
        public KafkaStreamsConfiguration kStreamsConfigs() {
            return new KafkaStreamsConfiguration(Map.of(
                APPLICATION_ID_CONFIG, "testStreams",
                BOOTSTRAP_SERVERS_CONFIG, "localhost:9092",
                DEFAULT_KEY_SERDE_CLASS_CONFIG, Serdes.Integer().getClass().getName(),
                DEFAULT_VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName(),
                DEFAULT_TIMESTAMP_EXTRACTOR_CLASS_CONFIG, WallclockTimestampExtractor.class.getName()
            ));
        }

    }
```


Note the explicit annotations at the top for enabling Kafka and Kafka
Streams. Also note that in the bean here, you can provide a customized
configuration for your Kafka Streams application, providing application
ID, bootstrap server connection, details of a Kafka broker, etc. Or, you
can define your config in `application.properties`, and
this Kafka Streams bean is created by default and would be available for
instantiating a Kafka Streams instance.

To build your Streams topology, you need a
`StreamsBuilder` as an input parameter. Spring Boot can
create it with defaults or you can do it explicitly. Once you have your
`StreamsBuilder` in place, you will get access to all of
the APIs available in Kafka Streams and it becomes just like a regular
Kafka Streams application. Spring's wrapper on top of Kafka Streams is
very thin so you can focus on your business logic.


``` {.language-java}
    @Bean 
    public KStream <Integer, String> kStream (StreamsBuilder kStreamBuilder) {
        KStream<integer, String> stream = kStreamBuilder.stream("streamingTopic1");
        stream
            .mapValues((ValueMapper<String, String>) String::toUpperCase)
        .groupByKey()
            .windowedBy(TimeWindows.of(Duration.ofMillis(1000)))
            .reduce((String value1, String value2) -> value1 + value2, Named.as("windowStore"))
            .toStream()
            .map((windowedId, value) -> new KeyValue <>(windowedId.key(), value))
            .filter((i, s) -> s.length() > 40)
            .to("streamingTopic2");
        stream.print(Printed.toSysOut());
        return stream;
}

```
