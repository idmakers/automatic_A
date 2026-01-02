---
title: "Django Model with Foreign Key to Settings.AUTH_USER_MODEL Fails on Postgres"
tags:
  - Django
  - Foreign Keys
  - Postgres
---

# A Catchy, SEO-Friendly Title

## Core Problem

When incorporating a Django app with a foreign key to `settings.AUTH_USER_MODEL` into another app using Postgres as the database, the migration process fails due to null values in the `owner_id` column of the survey table.

## Solution & Analysis

The issue arises because the foreign key is defined as `models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)`, which creates a relationship between the survey model and the user model. However, when the app is incorporated into another app using Postgres, the migration process attempts to create the `owner_id` column in the survey table.

To resolve this issue, you need to ensure that the `owner_id` column is created with a default value of `NULL`, allowing existing surveys to remain unchanged.

Here's an example of how to modify the `models.py` file to achieve this:

```python
from django.db import models
from django.conf import settings

class Survey(models.Model):
    title = models.CharField(max_length=255)
    description = models.TextField()
    owner = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE, null=True, default=None)

    def __str__(self):
        return self.title
```

In the `models.py` file, we've added `null=True` and `default=None` to the `owner` field. This allows the `owner_id` column to be created with a default value of `NULL`, allowing existing surveys to remain unchanged.

Additionally, you may need to update your migration files to reflect the changes in the model definition. You can do this by running the following command:

```bash
python manage.py makemigrations
```

This will generate new migration files that reflect the updated model definition.

## Conclusion

By modifying the `models.py` file to include a default value of `NULL` for the `owner_id` column, you can resolve the issue with null values in the survey table. Remember to update your migration files accordingly to ensure a smooth transition when incorporating the app into another project using Postgres as the database.