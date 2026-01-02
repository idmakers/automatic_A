---
title: "Propagating Logging from Multiple Modules to a Single Log File"
tags:
  - Python Logging
  - Module-Specific Logging
  - Centralized Logging

---

# Propagating Logging from Multiple Modules to a Single Log File

## Core Problem

In a multi-module project, it's often necessary to log events and errors in different modules. However, when logging is set up independently in each module, the logs may not be propagated to a central location, such as a single log file. In this case, only the logs from the main script are written to the log file.

## Solution & Analysis

To propagate logging from multiple modules to a single log file, we need to configure the logger to use a root logger that is shared by all modules. Here's an updated version of the `script.py` and `utils.py` files:

```python
# script.py
import logging
from utils.utils import load_query

# Set up the root logger
logging.basicConfig(filename='etl.log', level=logging.INFO)

logger = logging.getLogger(__name__)

def main():
    logger.info("Starting")
    load_query()
    logger.info("Finished")

if __name__ == "__main__":
    main()
```

```python
# utils/utils.py
import logging

# Get the root logger and set its name to 'utils'
logger = logging.getLogger('utils')
logger.setLevel(logging.INFO)

# Create a file handler for logging to etl.log
file_handler = logging.FileHandler('etl.log')
logger.addHandler(file_handler)
```

In this updated version, we've created a `main` function in the `script.py` file that serves as the entry point for our application. We set up the root logger using the `basicConfig` function from the `logging` module, which specifies the log file and level.

In the `utils.py` file, we get the root logger using `logging.getLogger('utils')`, set its name to `'utils'`, and add a file handler for logging to the same `etl.log` file. This ensures that logs from all modules are propagated to the central log file.

## Conclusion

By configuring the logger to use a shared root logger, we can propagate logging from multiple modules to a single log file. This provides a centralized location for logging events and errors in our application, making it easier to monitor and diagnose issues.