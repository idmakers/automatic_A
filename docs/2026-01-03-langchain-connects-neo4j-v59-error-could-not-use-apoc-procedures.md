**Connecting Langchain to Neo4j v5.9: Resolving APOC Procedure Errors**

# Connecting Langchain to Neo4j v5.9: Resolving APOC Procedure Errors

## Core Problem

When using the `Neo4jGraph` class in Langchain, users encounter an error message indicating that the APOC procedures are not enabled or allowed in the Neo4j configuration. This issue arises despite successfully installing the apoc plugin and running the `apoc.version()` command on the Neo4j client.

## Solution & Analysis

To resolve this issue, follow these steps:

1. **Verify APOC Plugin Installation**: Ensure that the apoc plugin is installed in your Neo4j instance by checking the version using the `apoc.version()` command.
2. **Configure APOC Procedures**: Check if the APOC procedures are enabled in your Neo4j configuration. You can do this by running the following Cypher query:
```cypher
CALL apoc.meta.data()
```
If the procedure is not allowed, you will need to update your Neo4j configuration to allow it.

3. **Update Langchain Configuration**: In your Langchain code, ensure that the `Neo4jGraph` class is configured to use the correct APOC procedures. You can do this by passing the `apoc_procedures` parameter when creating the `Neo4jGraph` instance:
```python
graph = Neo4jGraph(
    'bolt://localhost:7687',
    'neo4j',
    'chenhuabc',
    apoc_procedures=['apoc.meta.data']
)
```
## Conclusion

By following these steps, you should be able to resolve the APOC procedure error and successfully connect your Langchain model to your Neo4j instance. Remember to verify that the APOC plugin is installed and configured correctly in your Neo4j instance.

### Additional Resources

* [Official Neo4j Documentation](https://neo4j.com/docs/)
* [APOC Plugin Documentation](https://apoc.druwalabs.com/)
* [Langchain Documentation](https://langchain.ai/)