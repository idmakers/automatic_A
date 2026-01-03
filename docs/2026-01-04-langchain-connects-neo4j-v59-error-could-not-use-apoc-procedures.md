---
title: "Resolving APOC Procedures Error in Langchain with Neo4j v5.9"
tags:
  - langchain
  - neo4j
  - apoc
  - graph database

---

# Resolving APOC Procedures Error in Langchain with Neo4j v5.9

## Core Problem

When using the `Neo4jGraph` class from the Langchain library to connect to a Neo4j instance, an error is reported despite having successfully installed the APOC plugin and verified its version.

ValueError: Could not use APOC procedures. Please ensure the APOC plugin is installed in Neo4j and that 'apoc.meta.data()' is allowed in Neo4j configuration

## Solution & Analysis

To resolve this issue, we need to configure the Neo4j instance to allow the use of APOC procedures.

### Step 1: Verify APOC Plugin Installation

Ensure that the APOC plugin has been installed correctly by running the following command on your Neo4j client:
```python
return apoc.version()
```
This should return the version number of the APOC plugin, confirming its installation.

### Step 2: Configure Neo4j to Allow APOC Procedures

Update the Neo4j configuration file (`neo4j.conf`) to allow the use of APOC procedures. Add the following line to the `security` section:
```properties
apoc.meta.data=true
```
Restart the Neo4j server to apply the changes.

### Step 3: Update Langchain Configuration

Modify the Langchain configuration to include the updated APOC plugin settings. Create a new file (`langchain_config.py`) with the following content:
```python
import os

# Neo4j connection settings
neo4j_server = 'bolt://localhost:7687'
neo4j_username = 'neo4j'
neo4j_password = 'chenhuabc'

# APOC plugin settings
apoc_enabled = True
```
### Step 4: Test the Connection

Restart the Langchain server and reconnect to the Neo4j instance using the updated configuration:
```python
from langchain.graphs import Neo4jGraph

graph = Neo4jGraph(
    neo4j_server,
    neo4j_username,
    neo4j_password
)

print(graph)
```
This should resolve the error and establish a successful connection to the Neo4j instance.

## Conclusion

By following these steps, you can resolve the APOC procedures error in Langchain with Neo4j v5.9. Ensure that the APOC plugin is installed correctly, configure the Neo4j instance to allow its use, update the Langchain configuration, and test the connection.