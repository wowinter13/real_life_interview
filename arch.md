## Architecture & System Design

***

### Table of Content
  - [Microservices](#microservices)
  - []()



### <a id="base"></a> Microservices


**How to solve N+1 in microservices**

The N+1 problem happens when we have a situation where we're making numerous, potentially unnecessary, calls to a database or another microservice. This could lead to degraded performance due to the increased latency and load, especially as the number of calls grows.

**Solutions:**

1. **Batching**
    Bundling several requests into a single one.

    Proto: implement queries like `GetUserIds` and `GetUsersBatch`

    Ruby/GraphQL: `graphql-batch` or `batch-loader`


2. **Caching**

    This method requires a strategy for invalidating the cache when the data changes.

3. **Data denormalization**

    For instance, we could store the same piece of data in a couple of services.

4. **Change DB architecture**

    Read data directly from a slave replica of the other microservice DB.
    Write data to master through async jobs. (so the original service will still be responsible for inserting)


**How to work with microservices locally**

**Solutions:**

1. **Docker**

    Run multiple containers locally.

2. **Use Mocks/Stubs**

    We could potentially implement some library that will duplicate all remote queries and populate required mocks for all services.

3. **Contract testing**

    In case of gRPC (proto schemas) we could just rely on the given contract.

4. **Advanced DevEnv**

    We can use Minikube or localstack tools to run all services locally (depending on RAM limitations). Or DevOps could generate cloud dev envs for all developers (depending on the budget).