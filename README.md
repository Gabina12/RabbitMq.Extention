# RabbitMq Extention

Bondx.RabbitMQ.Extentions is a extention methods, for make communication easy.

  - Easy to publish object to queue
  - Easy to subscribe them
  - Easy to make exchanges, queues and bindings itself

# New Features!

  - Now you can use ConsumerFactory | DynamicConsumerFactory to binde consumers to queue
  - Add Stop method in Factory, to unsubscribe all consumers


You can also:
  - Configure Queue to have dlx echange and route, priprity and retries
  - push events with custom retry

### Installation


Install package from nuget.

```sh
$ Install-Package Bondx.RabbitMQ.Extentions -Version 0.0.3
```

Add injection to startup.cs file if you net to publish events..

```sh
$ services.AddProducer(configuration, "mq");
```
where mq is section name
```sh
"mq": {
    "Host": "localhost",
    "Port": 5672,
    "UserName": "guest",
    "Password": "guest"
}
```
## Now inject IProducer to the constructor and use it.

### example of rise event:
```sh
producer.PublishDynamic<T>(msg);
producer.Publish<BxMessage>(exchange, routingkey, msg);
...
```


### example of register subscriber
```sh
var _consumers = ConsumerFactory.Build(ConsumerType.Classic, cf, 3, ServiceProvider);
_consumers.Subscribe<BxMessage>((msg, provider) => {
          Console.WriteLine(JsonConvert.SerializeObject(msg));
          return Task.CompletedTask;
});
```

### example of ConfigurationObject, for ConsumerType.Classic
```sh
ConsumerConfiguration cf = new ConsumerConfiguration {
                Configuration = new MqConfiguration {
                    Host = "localhost",
                    Port = 5672,
                    UserName = "guest",
                    Password = "guest"
                },
                Queue = new QueueParams {
                    Name = "CUSTOMERS",
                    RoutingKey = "1.1.1",
                    QueueArgs = new QueueArg[] { new QueueArg
                            {
                                Key = QueueArgKey.DeadLetterExchage,
                                Value = "bx.exchange.fanout.dl"
                            },
                            new QueueArg
                            {
                                Key = QueueArgKey.MaxPriority,
                                Value = 5
                            }
                        }
                },
                Exchange = new ExchangeParams() {
                    Name = "bx.exchange.direct",
                    Type = ExchangeType.Direct
                },
                ExchangeDLQ = new ExchangeParams() {
                    Name = "bx.exchange.fanout.dl",
                    Type = ExchangeType.Fanout
                }
            };
```

### example of ConfigurationObject, for ConsumerType.Dynamic
```sh
ConsumerConfiguration cf = new ConsumerConfiguration {
                Configuration = new MqConfiguration {
                    Host = "localhost",
                    Port = 5672,
                    UserName = "guest",
                    Password = "guest"
                }
            };
```
