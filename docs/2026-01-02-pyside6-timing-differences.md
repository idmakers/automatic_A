---
title: "PySide6 Timing Differences in GUI Updates"
tags:
  - PySide6
  - Qt
  - GUI Performance
---

# Understanding the Performance Difference in PySide6 GUI Updates

## Core Problem
When building a grid of labels that need to be updated dynamically, using two different approaches for changing the background colors of specific widgets, we observe significant performance differences between executing these routines within the class init function versus after displaying the window.

## Solution & Analysis
```python
import timeit

from PySide6.QtCore import QSize, Qt
from PySide6.QtGui import QColor
from PySide6.QtWidgets import (
    QApplication,
    QGridLayout,
    QLabel,
    QMainWindow,
    QWidget,
)


class MainWindow(QMainWindow):
    def __init__(self):
        # Initialize the GUI components
        super().__init__()
        self.setWindowTitle("My App")
        self.setMinimumSize(QSize(400, 300))
        self.layout = QGridLayout()
        self.layout.setSpacing(3)

        xMax = 4
        yMax = 5
        steps = xMax * yMax
        midStep = steps // 2 + 1
        cStep = 255 // midStep
        cRed = 255
        cGreen = 0
        cBlue = 0
        self.labels = {}
        for b in range(steps):
            label = f"T{b}"
            (x, y) = divmod(b, yMax)
            color = f"{cRed:02x}{cGreen:02x}{cBlue:02x}"
            self.labels[(x, y)] = self.AddBox(color, label, x, y)

            if b < midStep:
                cGreen += cStep
            else:
                cRed -= cStep

        widget = QWidget()
        widget.setLayout(self.layout)
        self.setCentralWidget(widget)

    def ChangeBoxColorInit(self):
        """Change box colors using the init approach"""
        runs = 500
        time1 = timeit.timeit(lambda: self.ChangeBox1(2, 2), number=runs)
        time2 = timeit.timeit(lambda: self.ChangeBox2(1, 1), number=runs)

    def ChangeBoxColorShow(self):
        """Change box colors using the show approach"""
        runs = 500
        time1 = timeit.timeit(lambda: self.ChangeBox1(2, 2), number=runs)
        time2 = timeit.timeit(lambda: self.ChangeBox2(1, 1), number=runs)

    def MyLabel(self, color, text):
        """Create a label with the specified color and text"""
        tBox = QLabel(text)
        tBox.setAlignment(Qt.AlignCenter)
        tBox.setStyleSheet(f"background: #{color}; font-style: bold; font-size: 24pt")

        return tBox

    def AddBox(self, color, text, x, y):
        """Add a box to the layout with the specified color and text"""
        box = self.MyLabel(color, text)
        self.layout.addWidget(box, x, y)

        return box

    def ChangeBox1(self, x, y):
        """Change the background color of a box at position (x, y)"""
        cell = self.layout.itemAtPosition(x, y).widget()
        cell.setStyleSheet(f"background: #{'0000ff'}; font-style: bold; font-size: 24pt")

    def ChangeBox2(self, x, y):
        """Change the background color of a box at position (x, y)"""
        self.labels[(x, y)].setStyleSheet(f"background: #{'00f0ff'}; font-style: bold; font-size: 24pt")


app = QApplication(sys.argv)
window = MainWindow()
window.show()

# Execute the ChangeBoxColorInit routine in the init function
window.ChangeBoxColorInit()

# Execute the ChangeBoxColorShow routine after displaying the window
time1 = timeit.timeit(lambda: window.ChangeBox1(2, 2), number=1000)
time2 = timeit.timeit(lambda: window.ChangeBox2(1, 1), number=1000)

print(f"Average execution time of ChangeBox1 in init over 1000 runs: {time1 / 1000} seconds")
print(f"Average execution time of ChangeBox2 in init over 1000 runs: {time2 / 1000} seconds")

# Execute the ChangeBoxColorShow routine after displaying the window
window.ChangeBoxColorShow()

app.exec()
```

## Conclusion
The observed performance difference between executing the `ChangeBox1` and `ChangeBox2` routines within the class init function versus after displaying the window can be attributed to the Qt event loop. When executing these routines in the init function, the changes are not yet visible on the screen, resulting in slower execution times due to the lack of GUI updates. In contrast, executing these routines after displaying the window ensures that the changes are applied to the already updated GUI components, leading to faster execution times.