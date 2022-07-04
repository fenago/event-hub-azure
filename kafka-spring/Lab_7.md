Create Kafka Topics with TopicBuilder
=====================================


For many years, [Apache
Kafka](https://www.confluent.io/what-is-apache-kafka/) administrators
used command line tools to perform admin operations like creating
topics, changing topic configurations, assigning partitions, etc. But
with the Kafka Admin API (`AdminClient` class), those
operations can now be done programmatically. Spring Boot and Spring for
Apache Kafka include integration for `AdminClient` so
you can perform admin operations from within your application's
configuration.

In the Spring for Apache Kafka framework, `AdminClient`
is supported in a few ways. There is a `KafkaAdmin`
class, which is a wrapper for `AdminClient`. It
implements a Spring `FactoryBean` that is used to
maintain and support the lifecycle of an `AdminClient`.
Then there's the `TopicBuilder` class, which provides a
convenient API for creating topic configurations.

You need to supply a configuration to the
`KafkaAdmin FactoryBean` so that it can connect to a
broker, and this configuration can potentially include information about
a bootstrap server or certain security configurations.


``` {.language-java}
 @Bean 
  public KafkaAdmin admin() {
    return new KafkaAdmin(Map.of(AdminClientConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092"));
  }
```


After that step, a `KafkaAdmin` client is created.
However, it's much easier to use Spring Boot rather than define the bean
yourself, so that your configuration can be outsourced and placed in
`application.properties` or
`application.yaml`, where Spring Boot can access it and
instantiate a corresponding `KafkaAdmin` instance.

Once the `KafkaAdmin` bean is available in the classpath
one way or another, you can create multiple other beans with type
`NewTopic`, which is Kafka's
`AdminClient` type. Those topics will then be created in
a Kafka cluster.


``` {.language-java}
  @Bean
  public NewTopic topic1() {
    return TopicBuilder.name("thing1")
        .partitions(10)
        .replicas(3)
        .compact()
        .build();
  }

  @Bean
  public NewTopic topic2() {
    return TopicBuilder.name("thing2")
        .partitions(10)
        .replicas(3)
        .config(TopicConfig.COMPRESSION_TYPE_CONFIG, "zstd")
        .build();
  }

```


You can use `TopicBuilder` methods to provide some of
the standard configurations like number of partitions, number of
replicas, compaction, and so on. You can also use some extra
configuration parameters that may not have API methods, for example,
there is a configuration you can use to change compression type (the
algorithm used to compress data in a topic).

In addition, `TopicBuilder` also provides methods that
let you manually assign replicas for your topic when the topic is
created:


``` {.language-java}
  @Bean
  public NewTopic topic3() {
    return TopicBuilder.name("thing3")
        .assignReplicas(0, Arrays.asList(0, 1))
        .assignReplicas(1, Arrays.asList(1, 2))
        .assignReplicas(2, Arrays.asList(2, 0))
        .config(TopicConfig.COMPRESSION_TYPE_CONFIG, "zstd")
        .build();
  }
```
