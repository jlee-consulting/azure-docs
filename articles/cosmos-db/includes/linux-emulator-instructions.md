1. Retrieve the IP address of your local machine. This step is required when Direct mode setting is configured using Cosmos DB SDKs (.NET, Java).

    ```bash
    ipaddr="`ifconfig | grep "inet " | grep -Fv 127.0.0.1 | awk '{print $2}' | head -n 1`"
    ```

1. Pull the Docker image from the registry.

    ```bash
    docker pull mcr.microsoft.com/cosmosdb/linux/azure-cosmos-emulator
    ```

1. Run the Docker image with the following configurations:

    ### [SQL API](#tab/sql-api)
   
    ```bash
    docker run \
        --publish 8081:8081 \
        --publish 10251-10254:10251-10254 \
        --memory 3g --cpus=2.0 \
        --name=test-linux-emulator \
        --env AZURE_COSMOS_EMULATOR_PARTITION_COUNT=10 \
        --env AZURE_COSMOS_EMULATOR_ENABLE_DATA_PERSISTENCE=true \
        --env AZURE_COSMOS_EMULATOR_IP_ADDRESS_OVERRIDE=$ipaddr \
        --interactive \
        --tty \
        mcr.microsoft.com/cosmosdb/linux/azure-cosmos-emulator
    ```

    ### [MongoDB API](#tab/mongodb-api)
   
    ```bash
    docker run \
        --publish 8081:8081 \
        --publish 10251-10254:10251-10254 \
        --name=test-linux-emulator-mongo \
        --env AZURE_COSMOS_EMULATOR_PARTITION_COUNT=10 \
        --env AZURE_COSMOS_EMULATOR_ENABLE_DATA_PERSISTENCE=true \
        --env AZURE_COSMOS_EMULATOR_IP_ADDRESS_OVERRIDE=$ipaddr \
        --env AZURE_COSMOS_EMULATOR_ENABLE_MONGODB_ENDPOINT=4.0 \
        --interactive \
        --tty \
        mcr.microsoft.com/cosmosdb/linux/azure-cosmos-emulator:mongodb
    ```
