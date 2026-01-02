---
title: "Consuming RabbitMQ Streams with MQTT Plugin in Python"
tags:
  - rabbitmq
  - mqtt
  - python
  - fanout pattern

---

# Consuming RabbitMQ Streams with MQTT Plugin in Python

## Core Problem

Implementing the fanout pattern for MQTT messages using RabbitMQ, where IoT data is published to an MQTT exchange and then routed to a RabbitMQ stream, which is consumed by multiple Python applications.

## Solution & Analysis

To solve this problem, we need to modify our existing code to consume messages from a RabbitMQ stream instead of an MQTT topic. We will use the `rstream` client provided by RabbitMQ to connect to the stream and subscribe to it.

Here's an updated version of the code that consumes messages from the stream:

```python
import asyncio

from rstream import (
    AMQPMessage,
    Consumer,
    ConsumerOffsetSpecification,
    MessageContext,
    OffsetType,
)

STREAM_NAME = "mqtt_stream"
STREAM_RETENTION = 5000000000


async def receive():
    async with Consumer(host="localhost", username="guest", password="guest") as consumer:

        async def on_message(msg: AMQPMessage, message_context: MessageContext):
            # Decode the message body from bytes to string
            msg_body = msg.body.decode("utf-8")
            print(f"Got message: {msg_body} from stream {message_context.stream}")

        print("Press control + C to close")
        await consumer.start()
        await consumer.subscribe(
            stream=STREAM_NAME,
            callback=on_message,
            offset_specification=ConsumerOffsetSpecification(OffsetType.LAST, None),
        )
        try:
            await consumer.run()
        except (KeyboardInterrupt, asyncio.CancelledError):
            print("Closing Consumer...")
            return


with asyncio.Runner() as runner:
    runner.run(receive())
```

In this updated code, we decode the message body from bytes to a string using the `decode` method. This allows us to access the original message content without any binary data.

## Conclusion

By modifying our existing code to consume messages from a RabbitMQ stream instead of an MQTT topic, we can implement the fanout pattern for MQTT messages using RabbitMQ. The updated code provides a clear solution and analysis for this problem, making it easier to understand and apply in real-world scenarios.