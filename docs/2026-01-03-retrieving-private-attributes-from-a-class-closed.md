---
title: "Retrieving Private Attributes in Python"
tags:
  - private attributes
  - getter
  - setter
  - Python best practices
---

# Retrieving Private Attributes in Python

## Core Problem
In Python, private attributes are intended to be accessed only through the class's methods, specifically through the use of double underscore prefix (`__`) and the `@property` decorator. However, there is a common question about whether it makes sense to retrieve private attributes directly.

## Solution & Analysis

```python
class Person:
    def __init__(self, name, surname):
        self.__name = name
        self.__surname = surname
    
    @property
    def name(self):
        print("Retrieving private attribute (getter)")
        return self.__name

    @name.setter
    def name(self, m):
        if isinstance(m, str) and len(m) > 0:
            self.__name = m
        else:
            print("Error: Must be a text with letters!")
            print("Name changed")
        #self.m = self.__name + self.__surname

    def show(self):
        print('Miss: ', self.__name, self.__surname)

os = Person('Alexandra', 'Houston')  # object storing a person
os.name = 'Elizabeth'
print(os.name)
os.show()
```

**Analysis**: The use of `@property` and `@setter` allows for controlled access to private attributes. However, directly accessing private attributes through the class's namespace can lead to unexpected behavior.

```python
# Directly accessing private attribute through the class's namespace
# is discouraged in Python best practices.
class Person:
    def __init__(self, name, surname):
        self.__name = name
        self.__surname = surname

p = Person('Alexandra', 'Houston')
print(p.__name)  # Raises AttributeError: 'Person' object has no attribute '__name'
```

**Alternative Way**: If you want to retrieve private attributes, consider using the `__dict__` attribute of an object. However, this approach is generally discouraged as it bypasses Python's access control mechanism.

```python
class Person:
    def __init__(self, name, surname):
        self.__name = name
        self.__surname = surname

p = Person('Alexandra', 'Houston')
print(p.__dict__)  # Prints: {'__name': 'Alexandra', '__surname': 'Houston'}
```

**Best Practice**: In general, it's recommended to stick with the `@property` and `@setter` approach for accessing private attributes. This ensures that access is controlled and follows Python best practices.

## Conclusion
In summary, while it's technically possible to retrieve private attributes directly, it's generally discouraged in favor of using `@property` and `@setter`. This approach provides better control over access to private attributes and follows Python best practices.