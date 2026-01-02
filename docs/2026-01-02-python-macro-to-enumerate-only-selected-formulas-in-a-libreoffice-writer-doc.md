---
title: "Python Macro to Enumerate Only Selected Formulas in LibreOffice Writer Doc"
tags: python, libreoffice, writer, macro, formulas, selection
---

## Core Problem
The current Python macro prints the inner code of all formulas within a LibreOffice Writer document, regardless of the selected area. The goal is to modify this macro to only print the formulas included in the currently selected region.

## Solution & Analysis

To achieve this, we need to identify shapes within the selected area and then check if they contain embedded objects with formulas. We'll use the `uno` library to interact with LibreOffice and the `getSelection()` method to retrieve the current selection.

```python
import uno

def print_formulas_in_selection():
    # Get the document and selection context
    doc = XSCRIPTCONTEXT.getDocument()
    selection = doc.getCurrentSelection()

    # Initialize an empty list to store shapes within the selection
    selected_shapes = []

    # Iterate over each draw page in the document
    for draw_page in doc.getDrawPages():
        # Check if any shape is within the current selection
        for shape in draw_page:
            if shape.isWithinSelection(selection):
                # Add the shape to the list of selected shapes
                selected_shapes.append(shape)

    # Iterate over each selected shape and print its formula
    for shape in selected_shapes:
        assert hasattr(shape.getEmbeddedObject(), 'Formula')
        print(shape.getEmbeddedObject().Formula)
```

In this modified macro, we first retrieve the current selection using `doc.getCurrentSelection()`. We then iterate over each draw page in the document and check if any shape is within the current selection using the `isWithinSelection()` method. If a shape is within the selection, we add it to the list of selected shapes.

Finally, we iterate over each selected shape and print its formula using the same logic as before.

## Conclusion
By modifying the original macro to use the `isWithinSelection()` method, we can now print only the formulas included in the currently selected region of a LibreOffice Writer document. This solution demonstrates how to leverage the `uno` library and LibreOffice's built-in selection functionality to achieve this goal.