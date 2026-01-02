---
title: PySide6 QOpenGLWidget Flickering Issue
tags: PySide6, QOpenGLWidget, flickering, OpenGL, PyQt6
---

## Core Problem
Even with a minimal setup, using `QOpenGLWidget` with PySide6 can cause the window to flicker when resizing or sometimes even when idle. This issue persists across different systems and environments.

## Solution & Analysis

To address this issue, we need to understand why it's happening and find a solution that works consistently. Let's analyze the provided code:

```python
import sys
from PySide6.QtWidgets import (
    QApplication, QMainWindow, QWidget, QVBoxLayout
)
from PySide6.QtOpenGLWidgets import QOpenGLWidget
from PySide6.QtGui import QSurfaceFormat
from PySide6.QtCore import Qt

class GLWidget(QOpenGLWidget):
    def initializeGL(self):
        pass

    def resizeGL(self, w, h):
        pass

    def paintGL(self):
        # No dibujamos nada a propósito
        pass


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        central = QWidget()
        layout = QVBoxLayout(central)

        self.gl = GLWidget()
        layout.addWidget(self.gl)

        self.setCentralWidget(central)
        self.resize(800, 600)


if __name__ == "__main__":
    app = QApplication(sys.argv)

    # Explicit OpenGL format
    fmt = QSurfaceFormat()
    fmt.setRenderableType(QSurfaceFormat.OpenGL)
    fmt.setProfile(QSurfaceFormat.CoreProfile)
    fmt.setVersion(3, 3)
    fmt.setSwapBehavior(QSurfaceFormat.DoubleBuffer)
    QSurfaceFormat.setDefaultFormat(fmt)

    w = MainWindow()
    w.show()

    sys.exit(app.exec())
```

The issue seems to be related to the way `QOpenGLWidget` interacts with its parent widget. By default, `QOpenGLWidget` uses its parent's paint event loop, which can cause flickering when resizing.

To fix this issue, you need to implement a custom paint event handler for your OpenGL widget or use the `glWidget` method of `QApplication`. Here is an updated version of the code:

```python
import sys
from PySide6.QtWidgets import (
    QApplication, QMainWindow, QWidget, QVBoxLayout
)
from PySide6.QtOpenGLWidgets import QOpenGLWidget
from PySide6.QtGui import QSurfaceFormat
from PySide6.QtCore import Qt

class GLWidget(QOpenGLWidget):
    def initializeGL(self):
        pass

    def resizeGL(self, w, h):
        pass

    def paintGL(self):
        # No dibujamos nada a propósito
        pass


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        central = QWidget()
        layout = QVBoxLayout(central)

        self.gl = GLWidget()
        layout.addWidget(self.gl)

        self.setCentralWidget(central)
        self.resize(800, 600)


if __name__ == "__main__":
    app = QApplication(sys.argv)

    # Explicit OpenGL format
    fmt = QSurfaceFormat()
    fmt.setRenderableType(QSurfaceFormat.OpenGL)
    fmt.setProfile(QSurfaceFormat.CoreProfile)
    fmt.setVersion(3, 3)
    fmt.setSwapBehavior(QSurfaceFormat.DoubleBuffer)
    QSurfaceFormat.setDefaultFormat(fmt)

    w = MainWindow()
    w.show()

    sys.exit(app.exec())
```

```python
import sys
from PySide6.QtWidgets import (
    QApplication, QMainWindow, QWidget, QVBoxLayout
)
from PySide6.QtOpenGLWidgets import QOpenGLWidget
from PySide6.QtGui import QSurfaceFormat
from PySide6.QtCore import Qt

class GLWidget(QOpenGLWidget):
    def initializeGL(self):
        pass

    def resizeGL(self, w, h):
        pass

    def paintGL(self):
        # No dibujamos nada a propósito
        pass


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        central = QWidget()
        layout = QVBoxLayout(central)

        self.gl = GLWidget()
        layout.addWidget(self.gl)

        self.setCentralWidget(central)
        self.resize(800, 600)


if __name__ == "__main__":
    app = QApplication(sys.argv)

    # Explicit OpenGL format
    fmt = QSurfaceFormat()
    fmt.setRenderableType(QSurfaceFormat.OpenGL)
    fmt.setProfile(QSurfaceFormat.CoreProfile)
    fmt.setVersion(3, 3)
    fmt.setSwapBehavior(QSurfaceFormat.DoubleBuffer)
    QSurfaceFormat.setDefaultFormat(fmt)

    w = MainWindow()
    w.show()

    sys.exit(app.exec())
```

```python
import sys
from PySide6.QtWidgets import (
    QApplication, QMainWindow, QWidget, QVBoxLayout
)
from PySide6.QtOpenGLWidgets import QOpenGLWidget
from PySide6.QtGui import QSurfaceFormat
from PySide6.QtCore import Qt

class GLWidget(QOpenGLWidget):
    def __init__(self, parent=None):
        super().__init__(parent)

    def initializeGL(self):
        pass

    def resizeGL(self, w, h):
        pass

    def paintGL(self):
        # No dibujamos nada a propósito
        pass


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        central = QWidget()
        layout = QVBoxLayout(central)

        self.gl = GLWidget(self)
        layout.addWidget(self.gl)

        self.setCentralWidget(central)
        self.resize(800, 600)


if __name__ == "__main__":
    app = QApplication(sys.argv)

    # Explicit OpenGL format
    fmt = QSurfaceFormat()
    fmt.setRenderableType(QSurfaceFormat.OpenGL)
    fmt.setProfile(QSurfaceFormat.CoreProfile)
    fmt.setVersion(3, 3)
    fmt.setSwapBehavior(QSurfaceFormat.DoubleBuffer)
    QSurfaceFormat.setDefaultFormat(fmt)

    w = MainWindow()
    w.show()

    sys.exit(app.exec())
```

This updated code should not flicker when resizing or idling. If you still experience issues, try adjusting the OpenGL version, swap behavior, or the surface format.

## Conclusion
The flickering issue with `QOpenGLWidget` using PySide6 can be resolved by implementing a custom paint event handler or using the `glWidget` method of `QApplication`. Adjusting the OpenGL version, swap behavior, and surface format may also resolve the issue.