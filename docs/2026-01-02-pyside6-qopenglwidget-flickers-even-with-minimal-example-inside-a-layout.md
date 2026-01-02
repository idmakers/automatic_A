---
title: "PySide6 QOpenGLWidget Flicker Issue Solved"
tags:
  - PySide6
  - QOpenGLWidget
  - Flickering Issue
---

# Embedding QOpenGLWidget without Flickering in PySide6

## Core Problem
The `QOpenGLWidget` from `PySide6` flickers even with a minimal setup inside a layout, causing an unresponsive GUI experience.

## Solution & Analysis
To embed `QOpenGLWidget` in a layout without flickering, follow these steps:

### Step 1: Set up the QSurfaceFormat

```python
# Explicit OpenGL format
fmt = QSurfaceFormat()
fmt.setRenderableType(QSurfaceFormat.OpenGL)
fmt.setProfile(QSurfaceFormat.CoreProfile)
fmt.setVersion(3, 3)
fmt.setSwapBehavior(QSurfaceFormat.DoubleBuffer)
QSurfaceFormat.setDefaultFormat(fmt)
```

### Step 2: Disable Updates

```python
class GLWidget(QOpenGLWidget):
    def __init__(self):
        super().__init__()

# Remove self.update() and self.show() calls in your code
```

### Step 3: Set the Central Widget

```python
central = QWidget()
layout = QVBoxLayout(central)

self.gl = GLWidget()
layout.addWidget(self.gl)

central.resize(800, 600)
```

### Step 4: Use a Vertical Layout for QOpenGLWidget

```python
import sys
from PySide6.QtWidgets import QApplication, QMainWindow, QWidget, QVBoxLayout
from PySide6.QtOpenGLWidgets import QOpenGLWidget
from PySide6.QtGui import QSurfaceFormat
from PySide6.QtCore import Qt

class GLWidget(QOpenGLWidget):
    def __init__(self):
        super().__init__()

# Initialize OpenGL context here if needed
```

### Step 5: Create the Main Window and Run the Application

```python
if __name__ == "__main__":
    app = QApplication(sys.argv)

    w = MainWindow()
    w.show()

    sys.exit(app.exec())
```

## Conclusion
By following these steps, you can embed `QOpenGLWidget` in a layout without flickering using PySide6. Remember to set up the QSurfaceFormat explicitly and disable updates for `QOpenGLWidget`.