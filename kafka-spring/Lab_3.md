
Sending Messages to Apache Kafka with Spring Boot
=================================================


Spring for Apache Kafka\'s `KafkaTemplate` is a thin
wrapper around a Kafka producer that plays nicely with other Spring
features, like dependency injection and automatic configuration. It
provides a number of convenience methods for producing to Kafka topics.
You can define a default topic in your configuration and always send
messages there. Or, you can send them to a particular partition, a
particular key, and so on. `KafkaTemplate` is a rather
easy-to-use API, particularly if you already know how to use the Kafka
producer API.

In order to create a new instance of the Kafka template, you need to
create a `ProducerFactory`, which is responsible for
instantiating the underlying Kafka producer (because Kafka producer is
threadsafe, it can generate a singleton). A
`ProducerFactory` requires a configuration, which is a
simple map if you are using code-based configuration (you can also use
automatic configuration based on property files).


``` {.language-java}
  @Bean
  public ProducerFactory<String, String> producerFactory() {
    return new DefaultKafkaProducerFactory<>(
        Map.of(BOOTSTRAP_SERVERS_CONFIG, "localhost:9092",
               RETRIES_CONFIG, 0,
               BUFFER_MEMORY_CONFIG, 33554432,
               KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class,
               VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class
        ));
  }
```


Once you have your `ProducerFactory` bean, you can
create a `KafkaTemplate`.


``` {.language-java}
  @Bean
  public KafkaTemplate<String, String> kafkaTemplate() {
    return new KafkaTemplate<>(producerFactory());
  }
```


Kafka is an asynchronous system by default, so its
`send` method always returns a `Future`.
Spring provides its own capability for handling asynchronous
communication, so methods in `KafkaTemplates` always
return a `ListenableFuture`. You can also define a
callback to introspect the results, or you can use
`ListenableFuture.get()` to inspect results immediately.
