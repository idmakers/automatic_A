---
title: "Error in Taskiq Python while Executing Dynamic Interval Schedules"
tags:
  - taskiq
  - python
  - aiogram
  - redis

---

# Error in Taskiq Python while Executing Dynamic Interval Schedules

## Core Problem
When running dynamic interval schedules with Taskiq and Redis, the bot encounters an error after approximately 30-40 minutes. The specific error message is `redis.exceptions.ResponseError: NOGROUP No such key 'taskiq' or consumer group 'taskiq' in XREADGROUP with GROUP option`.

## Solution & Analysis

To resolve this issue, we need to ensure that the Taskiq Redis connection and the Redis Stream Broker are properly configured.

### Configuring Taskiq Redis Connection

```python
# bot/config.py
import os

REDIS_CLIENT_HOST = os.environ.get('REDIS_CLIENT_HOST', 'redis')
REDIS_CLIENT_PORT = int(os.environ.get('REDIS_CLIENT_PORT', 6379))
REDIS_CLIENT_DB = int(os.environ.get('REDIS_CLIENT_DB', 0))

redis_url = f"redis://{REDIS_CLIENT_HOST}:{REDIS_CLIENT_PORT}/{REDIS_CLIENT_DB}"
```

### Configuring Redis Stream Broker

```python
# bot/tasks.py
from taskiq import TaskiqDepends, Context
from aiogram import Bot

from bot.config import REDIS_CLIENT_HOST, REDIS_CLIENT_PORT, REDIS_CLIENT_DB
from bot.database.engine import session as db_session
from bot import redis_client
from bot.tasks import broker as tasks_broker

# Create a Redis Stream Broker instance with the Taskiq Scheduler
broker = RedisStreamBroker(url=redis_url).with_result_backend(RedisAsyncResultBackend(redis_url))

# Initialize Taskiq Aiogram with the Redis Stream Broker and scheduler
taskiq_aiogram.init(broker, "bot.main:dp", "bot.main:bot")
```

### Ensuring Proper Group Creation

```python
# bot/tasks.py
from taskiq import TaskiqDepends, Context
from aiogram import Bot

from bot.config import REDIS_CLIENT_HOST, REDIS_CLIENT_PORT, REDIS_CLIENT_DB
from bot.database.engine import session as db_session
from bot import redis_client
from bot.tasks import broker as tasks_broker

# Define a function to create the 'taskiq' group in Redis
@broker.on_event(TaskiqEvents.WORKER_STARTUP)
async def on_worker_startup(context: Annotated[Context, TaskiqDepends()]):
    r = Redis.from_url(redis_url)

    try:
        await r.xgroup_create(
            name="taskiq",
            groupname="taskiq",
            id="$",
            mkstream=True,
        )
    except Exception as e:
        print(e)
        if "BUSYGROUP" not in str(e):
            raise
```

### Ensuring Proper Group Maintenance

```python
# bot/tasks.py
from taskiq import TaskiqDepends, Context
from aiogram import Bot

from bot.config import REDIS_CLIENT_HOST, REDIS_CLIENT_PORT, REDIS_CLIENT_DB
from bot.database.engine import session as db_session
from bot import redis_client
from bot.tasks import broker as tasks_broker

# Define a function to delete the 'taskiq' group in Redis when it's no longer needed
async def delete_schedule(id_: str):
    await dynamic_schedule_source.delete_schedule(id_)

async def delete_all_schedules():
    schedules = await dynamic_schedule_source.get_schedules()
    for schedule in schedules:
        await delete_schedule(schedule.schedule_id)
```

## Conclusion

By implementing the above solutions, we can ensure that Taskiq and Redis are properly configured to handle dynamic interval schedules without encountering errors.