---
title: "Selecting Multiple Entries in Tkinter GUI with Arrows and Shift/Ctrl Keys"
tags:
  - tkinter
  - multiple selection
  - arrow keys
  - shift key
  - ctrl key
---

## Core Problem

Selecting multiple entries in a Tkinter GUI is essential for various applications, such as file explorers or list viewers. The traditional way to select multiple items is by using the Shift+Click method or Ctrl+Click method. However, some users may prefer to use arrow keys along with the Shift and Ctrl keys to select multiple items. Unfortunately, this functionality is not enabled out of the box in Tkinter.

## Solution & Analysis

To enable selecting multiple entries using arrows and Shift/Ctrl keys, we need to use a different approach than the traditional `selectmode="extended"` method or mimicking code from other sources. Instead, we can utilize the `set` option of the `Treeview` widget to manually handle the selection of items.

```python
import tkinter as tk
from tkinter import ttk

class MultipleSelectionTreeView(tk.Tk):
    def __init__(self):
        super().__init__()

        self.tree = ttk.Treeview(self)

        # Create columns
        self.tree['columns'] = ('col1')

        # Format our column
        self.tree.column("#0", width=200)
        for col in self.tree['columns']:
            self.tree.column(col, anchor="w")

        # Create headings
        self.tree.heading("#0", text="Branch1/2", anchor="w")
        for col in self.tree['columns']:
            self.tree.heading(col, text=col, anchor="w")

        # Insert items into the treeview
        self.tree.insert('', '0', 'item1', text='Branch1')
        self.tree.insert('', '0', 'item2', text='Branch2')

        self.tree.insert('item1', '1', 'FRED', text='FRED')
        self.tree.insert('item1', '1', 'MAVIS', text='MAVIS')
        self.tree.insert('item1', '1', 'BRIGHT', text='BRIGHT')

        self.tree.insert('item2', '2', 'SOME', text='SOME')
        self.tree.insert('item2', '2', 'NODES', text='NODES')
        self.tree.insert('item2', '2', 'HERE', text='HERE')

        # Configure the tree to allow multi-select
        self.tree.selection_mode = "extended"

        # Bind arrow key press to select item
        self.tree.bind_all("<Key>", lambda event: self.select_item(event))

        # Pack the treeview
        self.tree.pack(fill=tk.BOTH, expand=True)

    def select_item(self, event):
        if event.keysym in ('Left', 'Right'):
            selected_items = list(self.tree.selection())
            index = selected_items.index(selected_items[0])

            if event.keysym == 'Left':
                new_index = max(0, index - 1)
            else:
                new_index = min(len(selected_items) - 1, index + 1)

            self.tree.selection_set(selected_items[new_index])
        elif event.keysym in ('Shift', 'Ctrl'):
            if event.keysym == 'Shift':
                # Shift selection
                selected_items = list(self.tree.selection())
                for i in range(len(selected_items)):
                    item = self.tree.item(selected_items[i])
                    index = tree.index(item)
                    new_item = tree.insert('', 'end', text=tree.get(index, 'text'))
                    if i < len(selected_items) - 1:
                        tree.selection_set(new_item)

            elif event.keysym == 'Ctrl':
                # Ctrl selection
                selected_items = list(self.tree.selection())
                for item in selected_items:
                    self.tree.selection_set(item)
        else:
            return

if __name__ == "__main__":
    app = MultipleSelectionTreeView()
    app.mainloop()
```

## Conclusion

Selecting multiple entries in a Tkinter GUI can be achieved by utilizing the `set` option and binding arrow key press events to select items. This approach allows for manual handling of item selection, enabling users to use arrows along with Shift/Ctrl keys to select multiple items.

<!-- ADSENSE_PLACEHOLDER -->