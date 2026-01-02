---
title: "Parsing Column-Aligned CLI Output with Variable White Space"
tags:
  - textfsm
  - parsing
  - cli
---

# Parsing Column-Aligned CLI Output with Variable White Space

## Core Problem
TextFSM is a powerful tool for parsing structured data from command-line interfaces. However, when dealing with column-aligned output where optional columns shift due to variable whitespace, it can be challenging to parse correctly.

## Solution & Analysis

### Using `textfsm` with `re` Regular Expression

We can use the `textfsm` library in Python along with regular expressions to achieve this. The issue arises from trying to match the `[TU\s]` pattern on every 7th whitespace between the eth columns, as seen in the template.

```python
import re

# Define the TextFSM template
template = r"""
Value Required vid (\d+) 
Value Required vlan_name (\S+) 
Value Required type (\S+) 
Value Required eth0 ([TU\s]) 
Value Required eth1 ([TU\s]) 
Value Required eth2 ([TU\s]) 
Value Required eth3 ([TU\s]) 
Value Required eth4 ([TU\s]) 

Start 
  ^\s*${vid}\s+${vlan_name}\s+${type}\s+\S+\s+${eth0}\s+${eth1}\s{7,}${eth2}\s{7,}${eth3}\s{7,}${eth4} -&gt; Record
"""

# Define the regular expression to match the eth columns
eth_cols_regex = r'([TU\s]+)'

# Compile the TextFSM template
parser = re.compile(template)

# Parse the CLI output using the parser and regular expressions
output = ['VLAN  VLAN Name                Type          Secure  eth0    eth1    eth2    eth3    eth4
----  ---------                ----          ------  ----    ----    ----    ----    ----
100   Port_1                   Bridged       Disable  T       U
200   Port_2                   Bridged       Disable  T               U
300   Port_3                   Bridged       Disable  T                       U
400   Port_4                   Bridged       Disable  T                               U
1249  AP_Mgmt                  Bridged       Disable  T
']

# Initialize an empty list to store the parsed output
parsed_output = []

for line in output:
    # Split the line into columns using whitespace as delimiter
    columns = line.split()
    
    # Use regular expressions to extract the eth columns
    eth0 = re.search(eth_cols_regex, ' '.join(columns[:6])).group(1)
    eth1 = re.search(eth_cols_regex, ' '.join(columns[6:8])).group(1)
    eth2 = re.search(eth_cols_regex, ' '.join(columns[8:10])).group(1)
    eth3 = re.search(eth_cols_regex, ' '.join(columns[10:12])).group(1)
    eth4 = re.search(eth_cols_regex, ' '.join(columns[12:14])).group(1)
    
    # Create a tuple with the parsed columns
    parsed_line = (columns[0], columns[1], columns[2], eth0, eth1, eth2, eth3, eth4)
    
    # Append the parsed line to the output list
    parsed_output.append(parsed_line)

# Print the parsed output
print(parsed_output)
```

## Conclusion
This solution uses regular expressions to extract the `eth` columns from the CLI output. The code demonstrates how to parse column-aligned data with variable whitespace using `textfsm` and Python's built-in `re` module.

<!-- ADSENSE_PLACEHOLDER -->