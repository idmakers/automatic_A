---
title: "Django App Structure for Multiple Account Types"
tags:
  - Django app structure
  - multiple account types
---

# Choosing the Right App Structure for Multiple Account Types in Django

## Core Problem

When building a Django project with multiple account types, developers often face the dilemma of whether to create separate apps for each type or a single app. The decision is crucial as it affects the overall maintainability and scalability of the application.

## Solution & Analysis

In your case, you initially created three separate apps for Schools, professors, and users. However, you now feel that having multiple apps might be too much, especially when some models and views are repeated across account types. A common approach is to merge these accounts into a single app called "accounts" where each account type is represented as a subclass of a base model.

### Step 1: Define the Base Model

Create a `base_account.py` file with a `BaseAccount` model that contains fields common to all account types, such as username and email. This will serve as the foundation for your separate account types.
```python
# accounts/models/base_account.py

from django.db import models

class BaseAccount(models.Model):
    username = models.CharField(max_length=255)
    email = models.EmailField(unique=True)

    class Meta:
        abstract = True
```

### Step 2: Create Account Type Models

Create separate classes for each account type (Schools, professors, and users) that inherit from the `BaseAccount` model. This will encapsulate the unique fields specific to each account type.
```python
# accounts/models/school.py

from .base_account import BaseAccount

class School(BaseAccount):
    school_name = models.CharField(max_length=255)
    school_code = models.CharField(max_length=10)

# accounts/models/professor.py

from .base_account import BaseAccount

class Professor(BaseAccount):
    professor_title = models.CharField(max_length=50)
    department = models.CharField(max_length=100)

# accounts/models/user.py

from .base_account import BaseAccount

class User(BaseAccount):
    password = models.CharField(max_length=255)
```

### Step 3: Create the Accounts App and Register Models

Create an `accounts` app in your Django project and register the separate account type models.
```python
# accounts/apps__init__.py

from django.apps import AppConfig
from .models import School, Professor, User

class AccountsConfig(AppConfig):
    name = 'accounts'
    verbose_name = 'Accounts'

    def ready(self):
        from django.db import migrations, models
        from .models import School, Professor, User

        # Register the models here
        migrations.create_model(
            self.get_model_root(),
            'School',
            fields=[
                ('id', models.AutoField(primary_key=True)),
                ('username', models.CharField(max_length=255)),
                ('email', models.EmailField(unique=True)),
                ('school_name', models.CharField(max_length=255)),
                ('school_code', models.CharField(max_length=10)),
            ],
        )

        migrations.create_model(
            self.get_model_root(),
            'Professor',
            fields=[
                ('id', models.AutoField(primary_key=True)),
                ('username', models.CharField(max_length=255)),
                ('email', models.EmailField(unique=True)),
                ('professor_title', models.CharField(max_length=50)),
                ('department', models.CharField(max_length=100)),
            ],
        )

        migrations.create_model(
            self.get_model_root(),
            'User',
            fields=[
                ('id', models.AutoField(primary_key=True)),
                ('username', models.CharField(max_length=255)),
                ('email', models.EmailField(unique=True)),
                ('password', models.CharField(max_length=255)),
            ],
        )
```

## Conclusion

Merging multiple account types into a single app called "accounts" can be an effective approach for managing code duplication and maintainability. However, it's essential to consider the specific requirements of your project and weigh the pros and cons before making a decision.