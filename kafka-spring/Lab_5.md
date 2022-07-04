

Receiving Messages with KafkaListener
=====================================

`Factories` drive a lot of functionality in Spring Boot.
You learned in [Sending Messages to Confluent Cloud with Spring
Boot]
that `ProducerFactory` instantiates [Apache
Kafka](https://www.confluent.io/what-is-apache-kafka/) producers.
Similarly, `ConsumerFactory` instantiates Kafka
consumers.


``` {.language-java}
  @Bean
  public ConsumerFactory<String, String> consumerFactory() {
    return new DefaultKafkaConsumerFactory<>(consumerProperties());
  }
```


For a `ConsumerFactory`, you need to provide the
property files or configurations that your consumer will use. So you
need to create a `consumerProperties` bean, which is
essentially a `Map` with config key pairs:


``` {.language-java}
  @Bean
  public Map<String, Object> consumerProperties() {
    return Map.of(
        ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092",
        GROUP_ID_CONFIG, "spring-ccloud",
        ENABLE_AUTO_COMMIT_CONFIG, false,
        SESSION_TIMEOUT_MS_CONFIG, 15000,
        KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class,
        VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
}
```


### Message-Driven POJOs

Inside of Spring Boot, the components that integrate with messaging
systems follow the pattern of "message-driven POJOs." If you've been
around the Java community for a while, you know that in the Java EE
specification message-driven beans were introduced as part of the
enterprise JavaBean specification. It was a good idea but difficult to
put into practice, requiring an application server to run. Fortunately,
the Spring Framework came up with a simplified version of those beans,
not enterprise beans, but just regular POJOs made into Spring-managed
beans.

Message-driven POJOs enable asynchronous communication between systems,
so essentially you define a message listener and the framework takes
care of the functionality (in the past, you would have had to implement
an interface with the method `listen()`; the old
functionality still exists, but it's not necessarily useful anymore
because of annotations).

This approach to message listening doesn't have a direct coupling to the
Kafka consumer API, and that's why the layer of message-driven POJO
support can be implemented as something that is abstracted out. So you
don't put any Kafka-related code in your POJO---you can just have one
method with one input parameter. This is also a good opportunity to use
an annotation. When you annotate the method, Spring takes care of
instantiating the underlying containers that will run your Kafka
consumers and read messages from your Kafka topics and handle
serialization. All of these things are managed by Spring so you can
focus on your application code. The annotation
`KafkaListener` instantiates a facility called
`MessageListenerContainer`, which handles
parallelization, configuration, retries, and other things that the Kafka
application requires, such as offsets.
