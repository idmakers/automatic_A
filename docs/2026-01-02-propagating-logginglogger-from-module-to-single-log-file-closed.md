---
title: "Logging Configuration Propagation in Multi-Module Python Projects"
tags: logging, python, propagation, configuration, multi-module

## Core Problem
The issue arises when using a single logger instance across multiple modules in a Python project. In this scenario, the logger's configuration is set up in one module but not propagated to other modules, resulting in logs being written to separate files or not at all.

## Solution & Analysis

To propagate the logging configuration from one module to another, we can use a combination of the `logging.config` and `logging.config.dictConfig` functions. Here's an example:

### Step 1: Create a Configuration File
Create a file named `logging_config.py` with the following content:
```python
import logging.config

LOGGING_CONFIG = {
    'version': 1,
    'formatters': {
        'default': {
            'format': '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
        }
    },
    'handlers': {
        'file_handler': {
            'class': 'logging.FileHandler',
            'filename': 'etl.log',
            'formatter': 'default'
        }
    },
    'loggers': {
        '__main__': {
            'level': 'INFO',
            'handlers': ['file_handler']
        }
    }
}

def configure_logging():
    logging.config.dictConfig(LOGGING_CONFIG)
```
### Step 2: Import and Configure Logging in Modules
In each module that needs to use the shared logger, import `logging_config` and call `configure_logging()`:
```python
# script.py
import logging_config

logging_config.configure_logging()

logger = logging.getLogger(__name__)
logger.setLevel(logging.INFO)
```

```python
# utils/utils.py
import logging_config

logging_config.configure_logging()

def load_query():
    logger = logging.getLogger(__name__)
    logger.info("Running function")
```
### Step 3: Verify Logging Output
After making these changes, verify that logs are being written to the `etl.log` file by running your script or module.

## Conclusion
By using a configuration file and importing it in each module, you can propagate logging configurations across multiple modules in a Python project. This approach ensures consistency and simplifies logger management.