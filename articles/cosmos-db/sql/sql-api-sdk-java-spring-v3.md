---
title: 'Spring Data Azure Cosmos DB v3 for SQL API release notes and resources'
description: Learn about the Spring Data Azure Cosmos DB v3 for SQL API, including release dates, retirement dates, and changes made between each version of the Azure Cosmos DB SQL Async Java SDK.
author: seesharprun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: sidandrews
ms.reviewer: mjbrown
ms.custom: devx-track-java
---

# Spring Data Azure Cosmos DB v3 for Core (SQL) API: Release notes and resources
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

[!INCLUDE[appliesto-sql-api](../includes/cosmos-db-sdk-list.md)]

The Spring Data Azure Cosmos DB version 3 for Core (SQL) allows developers to use Azure Cosmos DB in Spring applications. Spring Data Azure Cosmos DB exposes the Spring Data interface for manipulating databases and collections, working with documents, and issuing queries. Both Sync and Async (Reactive) APIs are supported in the same Maven artifact. 

The [Spring Framework](https://spring.io/projects/spring-framework) is a programming and configuration model that streamlines Java application development. Spring streamlines the "plumbing" of applications by using dependency injection. Many developers like Spring because it makes building and testing applications more straightforward. [Spring Boot](https://spring.io/projects/spring-boot) extends this handling of the plumbing with an eye toward web application and microservices development. [Spring Data](https://spring.io/projects/spring-data) is a programming model and framework for accessing datastores like Azure Cosmos DB from the context of a Spring or Spring Boot application. 

You can use Spring Data Azure Cosmos DB in your [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) applications.

## Spring Boot support policy

Azure Spring Data Cosmos supports multiple [Spring Boot Versions](https://aka.ms/spring/versions). For complete list of currently supported versions, please visit our [Spring Version Mapping](https://aka.ms/spring/versions).

Spring Boot releases are marked as "End of Life" when they are no longer supported or released in any form. If you are running an EOL version, you should upgrade as soon as possible. 

Please note that a version can be out of support before it is marked as "End of Life." During this time you should only expect releases for critical bugs or security issues.

For more information on Spring Boot supported versions, please visit [Spring Boot Supported Versions](https://github.com/spring-projects/spring-boot/wiki/Supported-Versions).

> [!IMPORTANT]  
> These release notes are for version 3 of Spring Data Azure Cosmos DB. 
>
> Azure Spring Data Cosmos SDK has dependency on the Spring Data framework, and supports only the SQL API. 
>
> See these articles for information about Spring Data on other Azure Cosmos DB APIs:
> * [Spring Data for Apache Cassandra with Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB with Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
>

## Get started fast

  Get up and running with Spring Data Azure Cosmos DB by following our [Spring Boot Starter guide](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db). The Spring Boot Starter approach is the recommended way to get started using the Spring Data Azure Cosmos DB connector.

  Alternatively, you can add the Spring Data Azure Cosmos DB dependency to your `pom.xml` file as shown below:

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest-version</version>
  </dependency>
  ```

## Helpful content

| Content | Link |
|---|---|
| **Release notes** | [Release notes for Spring Data Cosmos SDK v3](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md) |
| **SDK download** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-cosmos) |
| **API documentation** | [Java API reference documentation](/java/api/overview/azure/spring-data-cosmos-readme?view=azure-java-stable&preserve-view=true) |
| **Contribute to SDK** | [Azure SDK for Java Central Repo on GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
| **Get started** | [Quickstart: Build a Spring Data Azure Cosmos DB app to manage Azure Cosmos DB SQL API data](./create-sql-api-spring-data.md) <br> [GitHub repo with quickstart code](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
| **Basic code samples** | [Azure Cosmos DB: Spring Data Azure Cosmos DB examples for the SQL API](sql-api-spring-data-sdk-samples.md) <br> [GitHub repo with sample code](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **Performance tips**| [Performance tips for Java SDK v4 (applicable to Spring Data)](performance-tips-java-sdk-v4-sql.md)| 
| **Troubleshooting** | [Troubleshoot Java SDK v4 (applicable to Spring Data)](troubleshoot-java-sdk-v4-sql.md) | 
| **Azure Cosmos DB workshops and labs** |[Cosmos DB workshops home page](https://aka.ms/cosmosworkshop)

> [!IMPORTANT]
> * The 3.5.0 release supports Spring Boot 2.4.3 and above.

## Release history
Release history is maintained in the azure-sdk-for-java repo, for detailed list of releases, see the [changelog file](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md).

## Recommended version

It's strongly recommended to use version 3.22.0 and above.

## Additional notes

* Spring Data Azure Cosmos DB supports Java JDK 8 and Java JDK 11.
* Spring Data 2.3 is currently supported, Spring Data 2.4 is not supported currently.

## FAQ

[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## Next steps

Learn more about [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Learn more about the [Spring Framework](https://spring.io/projects/spring-framework).

Learn more about [Spring Boot](https://spring.io/projects/spring-boot).

Learn more about [Spring Data](https://spring.io/projects/spring-data).
