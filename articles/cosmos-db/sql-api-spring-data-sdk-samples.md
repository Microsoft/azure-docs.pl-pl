---
title: 'Azure Cosmos DB SQL API: wiosenne przykłady danych 3'
description: Znajdź wiosnę przykładowych danych V3 w witrynie GitHub, aby poznać typowe zadania przy użyciu interfejsu API Azure Cosmos DB SQL, w tym operacji CRUD.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: adbec15e252a7b5cbd55f1b571d55455fb313a01
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91348530"
---
# <a name="azure-cosmos-db-sql-api-spring-data-azure-cosmos-db-v3-examples"></a>Azure Cosmos DB SQL API: wiosenne dane Azure Cosmos DB v3

> [!div class="op_single_selector"]
> * [Przykłady zestawu SDK dla platformy .NET V2](sql-api-dotnet-samples.md)
> * [Przykłady zestawu SDK dla platformy .NET v3](sql-api-dotnet-v3sdk-samples.md)
> * [Przykłady zestawu SDK dla języka Java v4](sql-api-java-sdk-samples.md)
> * [Przykłady zestawu SDK dla danych wiosennych v3](sql-api-spring-data-sdk-samples.md)
> * [Przykłady dla platformy Node.js](sql-api-nodejs-samples.md)
> * [Przykłady języka Python](sql-api-python-samples.md)
> * [Galeria przykładów kodu dla platformy Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> Te informacje o wersji dotyczą wersji 3 danych wiosennych Azure Cosmos DB. [Informacje o wersji 2](sql-api-sdk-java-spring-v2.md)można znaleźć tutaj. 
>
> Dane sprężynowe Azure Cosmos DB obsługują tylko interfejs API SQL.
>
> Zobacz następujące artykuły, aby uzyskać informacje na temat sprężyny danych na innych Azure Cosmos DB interfejsów API:
> * [Sprężynowe dane dla Apache Cassandra z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [MongoDB danych wiosennych z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Gremlin danych wiosennych z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- Możesz [aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): w ramach subskrypcji programu Visual Studio co miesiąc otrzymasz środki, które można przeznaczyć na płatne usługi platformy Azure.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

Najnowsze przykładowe aplikacje, które wykonują operacje CRUD i innych typowych operacji dotyczących zasobów Azure Cosmos DB są zawarte w repozytorium [Azure-sprężyn-Data-Cosmos-Java-SQL-API-Samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples) . Ten artykuł zawiera:

* Linki do zadań w każdym z przykładowych danych wiosennych Azure Cosmos DB pliki projektu. 
* Linki do powiązanej dokumentacji interfejsu API.

**Wymagania wstępne**

Do uruchomienia tej aplikacji przykładowej potrzebne są następujące elementy:

* Zestaw Java Development Kit 8
* Dane wiosenne Azure Cosmos DB v3

Opcjonalnie możesz użyć Maven, aby uzyskać najnowsze dane binarne Azure Cosmos DB v3 do użycia w projekcie. Rozwiązanie Maven automatycznie dodaje wszystkie wymagane zależności. W przeciwnym razie można bezpośrednio pobrać zależności wymienione w pliku **pom.xml** i dodać je do ścieżki kompilacji.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Uruchamianie aplikacji przykładowych**

Sklonuj repozytorium przykładowe:
```bash
$ git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples

$ cd azure-spring-data-cosmos-java-sql-api-samples
```

Możesz uruchomić próbki przy użyciu środowiska IDE (zaćmienie, IntelliJ lub programu VSCODE) lub z wiersza polecenia przy użyciu Maven.

W **aplikacji. właściwości** należy ustawić te zmienne środowiskowe

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

w celu udzielenia przykładom dostępu do odczytu i zapisu do konta, baz danych i kontenerów.

Środowisko IDE może zapewnić możliwość wykonywania kodu przykładowego danych sprężyny. W przeciwnym razie możesz użyć następującego polecenia terminalu, aby wykonać przykład:

```bash
mvn spring-boot:run
```

## <a name="document-crud-examples"></a>Przykłady dokumentu CRUD
Plik [przykładów](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej o dokumentach usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) .

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie dokumentu](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L46-L47) | CosmosRepository. Save |
| [Odczytaj dokument według identyfikatora](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L56-L58) | CosmosRepository.derivedQueryMethod |
| [Usuń wszystkie dokumenty](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L39-L41) | CosmosRepository.deleteAll |

## <a name="derived-query-method-examples"></a>Przykłady metod zapytania pochodnego
Plik [przykładów](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej o Azure Cosmos DB zapytaniach przed uruchomieniem poniższych przykładów, warto zapoznać się z informacjami na temat [metod zapytań pochodnych Baeldung w artykule wiosennym](https://www.baeldung.com/spring-data-derived-queries) .

| [Zapytanie o dokumenty](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L73-L77) | CosmosRepository. derivedQueryMethod |

## <a name="custom-query-examples"></a>Przykłady zapytań niestandardowych
Plik [przykładów](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) przedstawia sposób wykonywania następujących zadań przy użyciu gramatyki zapytań SQL. Aby dowiedzieć się więcej na temat odwołania do zapytań SQL w Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [przykłady zapytań SQL dotyczących Azure Cosmos DB](how-to-sql-query.md). 


| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Wykonywanie zapytań dotyczących wszystkich dokumentów](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L20-L22) | @Query wskazani |
| [Wykonywanie zapytań dotyczących równości przy użyciu operatora ==](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L24-L26) | @Query wskazani |
| [Wykonywanie zapytań dotyczących nierówności przy użyciu operatorów != i NOT](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L28-L38) | @Query wskazani |
| [Wykonywanie zapytań przy użyciu operatorów zakresu, takich jak >, <, >=, <=](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L40-L42) | @Query wskazani |
| [Wykonywanie zapytań przy użyciu operatorów zakresu względem ciągów](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L44-L46) | @Query wskazani |
| [Zapytanie z KOLEJNOŚCIą według](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L48-L50) | @Query wskazani |
| [Zapytanie z instrukcją DISTINCT](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L52-L54) | @Query wskazani |
| [Zapytanie z funkcjami agregującymi](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L56-L62) | @Query wskazani |
| [Praca z dokumentami podrzędnymi](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L64-L66) | @Query wskazani |
| [Wykonywanie zapytań przy użyciu sprzężeń wewnątrz dokumentu](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L68-L85) | @Query wskazani |
| [Zapytanie z operatorami String, Math i Array](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L87-L97) | @Query wskazani |