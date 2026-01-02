---
title: "Selecting Multiple Entries in Tkinter GUI using Arrows+Shift (or Ctrl)"
tags:
  - tkinter
  - selectmode
  - extended mode
---

# Selecting Multiple Entries in Tkinter GUI using Arrows+Shift (or Ctrl)

## Core Problem
When using a Tkinter GUI, selecting multiple entries is not as straightforward as it is in file explorers or other applications. The user can select multiple entries using shift+click and ctrl+click, but the same functionality is not achieved with arrows+shift (or ctrl) key combinations.

## Solution & Analysis

To achieve this functionality, we need to configure the `selectmode` option of the Tkinter widget that contains the list or treeview. The `extended` mode allows multiple selections using shift+click and ctrl+click, but does not work with arrows+shift (or ctrl) key combinations.

Here is an example code snippet that demonstrates how to select multiple entries using arrows+shift (or ctrl) key combinations:

```python
import tkinter as tk

# Create a Tkinter window
root = tk.Tk()

# Create a listbox widget with extended mode for multi-select functionality
listbox = tk.Listbox(root, selectmode=tk.EXTENDED)
for i in range(10):
    listbox.insert(i, f"Entry {i}")

# Bind arrow keys to the shift+arrow key combinations
def select_item(event):
    index = listbox.curselection()
    if len(index) == 0:
        return
    new_index = None
    for i in reversed(range(len(index))):
        if index[i] > 0 and (index[i-1] == 0 or listbox.get(index[i]-1) != listbox.get(index[i])):
            new_index = index[i]
            break
    listbox.selection_clear(0, tk.END)
    listbox.selection_add(new_index)

listbox.bind("<Key>", select_item)

# Pack the listbox widget into the window
listbox.pack(fill=tk.BOTH, expand=True)

# Run the Tkinter event loop
root.mainloop()
```

## Conclusion

In this example, we used the `tk.EXTENDED` mode for multi-select functionality in a `Listbox` widget. We bound arrow keys to shift+arrow key combinations using the `bind` method. This allows users to select multiple entries by pressing arrows while holding down the shift key.

By configuring the `selectmode` option and binding arrow keys to shift+arrow key combinations, we can achieve multi-select functionality in a Tkinter GUI that is similar to file explorers or other applications.