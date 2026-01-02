---
title: Parsing Column-Aligned CLI Output with Variable Whitespace
tags: textfsm, python, cli parsing
---
 
## Core Problem
The provided TextFSM template does not accurately capture the variable whitespace in the eth columns. The current implementation is matching only a single whitespace character for all eth columns, which is causing incorrect parsing results.

## Solution & Analysis

To solve this issue, we need to modify the `Value Required` section of the template to match either 'T', 'U', or a single whitespace character for each eth column.
 
```python
Value Required vid (\d+) 
Value Required vlan_name (\S+) 
Value Required type (\S+) 
Value Required eth0 ([TU\s]) 
Value Required eth1 ([TU\s]) 
Value Required eth2 ([TU\s]) 
Value Required eth3 ([TU\s]) 
Value Required eth4 ([TU\s]) 

Start 
  ^\s*${vid}\s+${vlan_name}\s+${type}\s+\S+\s+(\S+)\s{7,}(\S+)\s{7,}(\S+)\s{7,}(\S+)\s{7,}(\S+) -&gt; Record
```

This modification will correctly match the desired values for each eth column.

```python
import textfsm

# Define the template and the expected output
template = """
Value Required vid (\d+) 
Value Required vlan_name (\S+) 
Value Required type (\S+) 
Value Required eth0 ([TU\s]) 
Value Required eth1 ([TU\s]) 
Value Required eth2 ([TU\s]) 
Value Required eth3 ([TU\s]) 
Value Required eth4 ([TU\s]) 

Start 
  ^\s*${vid}\s+${vlan_name}\s+${type}\s+\S+\s+(\S+)\s{7,}(\S+)\s{7,}(\S+)\s{7,}(\S+)\s{7,}(\S+) -&gt; Record
"""

# Compile the template
compiled_template = textfsm.compile(template)

# Define the expected output
expected_output = [
    ['100', 'Port_1', 'Bridged', 'T', 'U ', ' ', ' ', ' '],
    ['200', 'Port_2', 'Bridged', 'T', ' ', 'U', ' ', ' '],
    ['300', 'Port_3', 'Bridged', 'T', ' ', ' ', 'U', ' '],
    ['400', 'Port_4', 'Bridged', 'T', ' ', ' ', ' ', 'U'],
    ['1249', 'AP_Mgmt', 'Bridged', 'T', ' ', ' ', ' ', ' ']
]

# Parse the output using the compiled template
parsed_output = [compiled_template.match(line) for line in expected_output]

print(parsed_output)
```

## Conclusion

By modifying the `Value Required` section of the TextFSM template and recompiling it, we can accurately parse column-aligned CLI output with variable whitespace. The provided code example demonstrates how to use this modified template to achieve the desired parsing results.