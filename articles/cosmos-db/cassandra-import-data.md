---
title: Migrowanie danych do konta interfejs API Cassandra w programie Azure Cosmos DB — samouczek
description: W tym samouczku dowiesz się, jak skopiować dane z platformy Apache Cassandra do konta interfejs API Cassandra w Azure Cosmos DB.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 7c55cbf4b8739a885c499c820a7e68825522ea4e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449294"
---
# <a name="tutorial-migrate-your-data-to-a-cassandra-api-account"></a>Samouczek: Migrowanie danych do konta interfejs API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Jako deweloper być może masz istniejące obciążenia bazy danych Cassandra, które są uruchomione w środowisku lokalnym lub w chmurze, a które chcesz zmigrować na platformę Azure. Możesz zmigrować te obciążenia do konta interfejsu API Cassandra w usłudze Azure Cosmos DB. Ten samouczek zawiera instrukcje dotyczące różnych opcji migrowania danych bazy danych Apache Cassandra do konta interfejsu API w usłudze Azure Cosmos DB.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Planowanie migracji
> * Wymagania wstępne dotyczące migracji
> * Migrowanie danych przy użyciu `cqlsh` `COPY` polecenia
> * Migrowanie danych przy użyciu platformy Spark

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites-for-migration"></a>Wymagania wstępne dotyczące migracji

* **Szacowanie potrzebnej przepływności:** przed rozpoczęciem migracji danych do konta interfejsu API Apache Cassandra w usłudze Azure Cosmos DB należy oszacować przepływność potrzebną dla obciążenia. Ogólnie rzecz biorąc, Rozpocznij od średniej przepływności wymaganej przez operacje CRUD, a następnie Uwzględnij dodatkową przepływność wymaganą dla operacji wyodrębniania i ładowania transformacji. Do zaplanowania migracji potrzebne są następujące szczegóły: 

  * **Istniejący rozmiar danych lub szacowany rozmiar danych.** Definiuje minimalne wymagania dotyczące rozmiaru bazy danych i przepływności. W przypadku szacowania rozmiaru danych dla nowej aplikacji można założyć, że dane są równomiernie dystrybuowane między wierszami i oszacować wartość przez pomnożenie rozmiaru danych. 

  * **Wymagana przepływność:** Przybliżona szybkość przepływności odczytu (zapytania/Get) i operacji Write (Update/Delete/INSERT). Ta wartość jest wymagana do obliczenia wymaganych jednostek żądań wraz z rozmiarem danych o stałym stanie.  

  * **Schemat:** Połącz się z istniejącym klastrem Cassandra za pośrednictwem programu `cqlsh` i wyeksportuj schemat z programu Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Po określeniu wymagań istniejących obciążeń Utwórz konto Azure Cosmos DB, bazę danych i kontenery zgodnie z zebranymi wymaganiami dotyczącymi przepływności.  

  * **Określenie opłaty za jednostkę żądania dla operacji:** jednostki żądań możesz określić przy użyciu dowolnego zestawu SDK obsługiwanego przez interfejs API Cassandra. W tym przykładzie przedstawiono sposób określania opłaty za jednostki żądań z użyciem wersji platformy .NET.

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         double value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **Przydzielenie wymaganej przepływności.** Usługa Azure Cosmos DB potrafi automatycznie skalować magazyn i przepływność w miarę wzrostu wymagań. Potrzebną przepływność możesz oszacować, korzystając z [kalkulatora jednostek żądań usługi Azure Cosmos DB](https://www.documentdb.com/capacityplanner). 

* **Utwórz tabele na koncie interfejs API Cassandra:** Przed rozpoczęciem migrowania danych Utwórz wstępnie wszystkie tabele z Azure Portal lub z programu `cqlsh` . W przypadku migrowania do konta Azure Cosmos DB, które ma przepływność na poziomie bazy danych, należy podać klucz partycji podczas tworzenia kontenerów.

* **Zwiększenie przepływności.** Czas trwania migracji danych zależy od przepływności aprowizowanej dla tabel w usłudze Azure Cosmos DB. Na czas trwania migracji zwiększ przepływność. Dzięki większej przepływności można uniknąć ograniczania przepustowości i przeprowadzać migrację w krótszym czasie. Po ukończeniu migracji zmniejsz przepływność, aby ograniczyć koszty. Zalecamy również korzystanie z konta Azure Cosmos DB w tym samym regionie, w którym znajduje się źródłowa baza danych. 

* **Włącz protokół TLS:** Azure Cosmos DB ma rygorystyczne wymagania i standardy dotyczące zabezpieczeń. Należy pamiętać o włączeniu protokołu TLS podczas korzystania z konta. W przypadku korzystania z programu CQL z protokołem SSH istnieje możliwość zapewnienia informacji o protokole TLS.

## <a name="options-to-migrate-data"></a>Opcje migracji danych

Dane z istniejących obciążeń Cassandra można przenieść do Azure Cosmos DB przy użyciu `cqlsh` `COPY` polecenia lub przy użyciu platformy Spark. 

### <a name="migrate-data-by-using-the-cqlsh-copy-command"></a>Migrowanie danych za pomocą polecenia COPY cqlsh

Za pomocą [polecenia copy CQL](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) skopiuj dane lokalne do konta interfejs API Cassandra w Azure Cosmos DB.

1. Uzyskaj informacje o parametrach połączenia konta interfejsu API Cassandra:

   * Zaloguj się do [Azure Portal](https://portal.azure.com)i przejdź do swojego konta Azure Cosmos DB.

   * Otwórz okienko **parametrów połączenia** . W tym miejscu zobaczysz wszystkie informacje potrzebne do nawiązania połączenia z kontem usługi interfejs API Cassandra `cqlsh` .

1. Zaloguj się do programu przy `cqlsh` użyciu informacji o połączeniu z portalu.

1. Użyj `CQL` `COPY` polecenia, aby skopiować dane lokalne do konta interfejs API Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

### <a name="migrate-data-by-using-spark"></a>Migrowanie danych przy użyciu platformy Spark 

Aby przeprowadzić migrację danych na konto interfejsu API Cassandra za pomocą platformy Spark, wykonaj następujące kroki:

1. Udostępnij [klaster Azure Databricks](cassandra-spark-databricks.md) lub [klaster usługi Azure HDInsight](cassandra-spark-hdinsight.md). 

1. Przenieś dane do docelowego punktu końcowego interfejs API Cassandra przy użyciu [operacji kopiowania tabeli](cassandra-spark-table-copy-ops.md). 

Migracja danych przy użyciu zadań platformy Spark jest opcją zalecaną, gdy Twoje dane znajdują się w istniejącym klastrze na maszynach wirtualnych platformy Azure lub w jakiejkolwiek innej chmurze. W tym celu należy skonfigurować platformę Spark jako pośrednik do jednorazowego lub regularnego pozyskiwania. Można przyspieszyć tę migrację, korzystając z łączności ExpressRoute Azure między środowiskiem lokalnym i platformą Azure. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie są już potrzebne, możesz usunąć grupę zasobów, konto Azure Cosmos DB i wszystkie powiązane zasoby. Aby to zrobić, wybierz grupę zasobów maszyny wirtualnej, wybierz pozycję **Usuń**, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zawarto informacje na temat migrowania danych do konta interfejs API Cassandra w programie Azure Cosmos DB. Teraz możesz poznać inne koncepcje w Azure Cosmos DB:

> [!div class="nextstepaction"]
> [Dostosowywalne poziomy spójności danych w usłudze Azure Cosmos DB](../cosmos-db/consistency-levels.md)




