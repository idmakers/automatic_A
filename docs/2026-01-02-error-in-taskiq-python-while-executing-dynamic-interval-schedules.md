---
title: Taskiq Python Error in Dynamic Interval Schedules
tags: taskiq, python, aiogram, redis, redis.exceptions.ResponseError, NOGROUP, consumer group 'taskiq'

## Core Problem
I have an aiogram bot with taskiq worker and scheduler (taskiq redis with taskiq_aiogram), but after some time (30-40 minutes), I'm getting a `redis.exceptions.ResponseError: NOGROUP No such key 'taskiq' or consumer group 'taskiq' in XREADGROUP with GROUP option`. This error occurs when running dynamic interval scheduled tasks, while static interval scheduled tasks work without errors.

## Solution & Analysis

The issue arises from the fact that taskiq Redis uses a consumer group to manage tasks. However, when using dynamic interval schedules, the consumer group is not created until after the first task has been executed. To fix this, we need to create the consumer group at startup or before running any tasks.

Here's an updated version of the `on_worker_startup` function that creates the consumer group:

```python
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

    # Create a key for the taskiq consumer group
    await broker.create_key("taskiq")

    # Wait for the consumer group to be created
    while True:
        try:
            await r.xinfo_group(
                groupname="taskiq",
                pattern="taskiq:*",
            )
            break
        except redis.exceptions.RedisError as e:
            if "NOGROUP" not in str(e):
                raise
            time.sleep(1)
```

Additionally, we need to update the `broker` initialization to include a check for the consumer group:

```python
@tasks_broker.broker.task
async def send_time_diff(start_seconds: float, user_id: int, context: Annotated[Context, TaskiqDepends()], bot: Bot = TaskiqDepends(), ):
    # Check if the consumer group exists
    await broker.create_key("taskiq")

    schedule_id = context.message.labels["schedule_id"]

    text = f"({time() - start_seconds) / 60} {schedule_id}"
    await bot.send_message(user_id, text, disable_notification=True)
```

## Conclusion

By creating the consumer group at startup and checking for its existence before running tasks, we can resolve the `redis.exceptions.ResponseError: NOGROUP` issue when using dynamic interval schedules with taskiq.