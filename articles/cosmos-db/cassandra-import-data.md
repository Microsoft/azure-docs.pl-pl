---
title: Migrowanie danych do konta interfejs API Cassandra w programie Azure Cosmos DB — samouczek
description: W tym samouczku dowiesz się, jak używać polecenia copy CQL & Spark do kopiowania danych z platformy Apache Cassandra do konta usługi interfejs API Cassandra w Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: b450d8463c09647b8f87547afe9d714c4b8659c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82858564"
---
# <a name="tutorial-migrate-your-data-to-cassandra-api-account-in-azure-cosmos-db"></a>Samouczek: migrowanie danych do konta interfejsu API Cassandra w usłudze Azure Cosmos DB

Jako deweloper być może masz istniejące obciążenia bazy danych Cassandra, które są uruchomione w środowisku lokalnym lub w chmurze, a które chcesz zmigrować na platformę Azure. Możesz zmigrować te obciążenia do konta interfejsu API Cassandra w usłudze Azure Cosmos DB. Ten samouczek zawiera instrukcje dotyczące różnych opcji migrowania danych bazy danych Apache Cassandra do konta interfejsu API w usłudze Azure Cosmos DB.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Planowanie migracji
> * Wymagania wstępne dotyczące migracji
> * Migrowanie danych przy użyciu polecenia cqlsh COPY
> * Migrowanie danych przy użyciu platformy Spark

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites-for-migration"></a>Wymagania wstępne dotyczące migracji

* **Szacowanie potrzebnej przepływności:** przed rozpoczęciem migracji danych do konta interfejsu API Apache Cassandra w usłudze Azure Cosmos DB należy oszacować przepływność potrzebną dla obciążenia. Na ogół zalecane jest rozpoczęcie od średniej przepływności wymaganej przez operacje CRUD, a następnie dołączenie dodatkowej przepływności wymaganej dla operacji ETL (wyodrębnianie, transformacja, ładowanie) lub operacji powodujących gwałtowne wzrosty obciążenia. Do zaplanowania migracji potrzebne są następujące szczegóły: 

  * **Istniejący rozmiar danych lub szacowany rozmiar danych.** Definiuje minimalne wymagania dotyczące rozmiaru bazy danych i przepływności. Jeśli szacujesz rozmiar danych dla nowej aplikacji, możesz założyć, że dane są równomiernie rozłożone między wiersze i oszacować wartość, mnożąc ich liczbę przez rozmiar danych. 

  * **Wymagana przepływność:** przybliżona przepływność w przypadku operacji odczytu (query/get) i zapisu (update/delete/insert). Ta wartość jest wymagana do obliczenia wymaganych jednostek żądań wraz z rozmiarem danych w stanie stabilności.  

  * **Schemat:** połącz się z istniejącym klastrem Cassandra za pośrednictwem poleceń cqlsh i wyeksportuj schemat z bazy danych Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Po zidentyfikowaniu wymagań dotyczących istniejącego obciążenia musisz utworzyć konto usługi Azure Cosmos, bazę danych i kontenery zgodnie z zebranymi wymaganiami dotyczącymi przepływności.  

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

* **Utworzenie tabel na koncie interfejsu API Cassandra:** przed rozpoczęciem migracji danych utwórz wstępnie wszystkie tabele z poziomu witryny Azure Portal lub serwera cqlsh. W przypadku migracji do konta usługi Azure Cosmos, która ma przepływność na poziomie bazy danych, pamiętaj o podaniu klucza partycji podczas tworzenia kontenerów usługi Azure Cosmos.

* **Zwiększenie przepływności.** Czas trwania migracji danych zależy od przepływności aprowizowanej dla tabel w usłudze Azure Cosmos DB. Na czas trwania migracji zwiększ przepływność. Dzięki większej przepływności można uniknąć ograniczania przepustowości i przeprowadzać migrację w krótszym czasie. Po ukończeniu migracji zmniejsz przepływność, aby ograniczyć koszty. Zalecane jest również posiadanie konta usługi Azure Cosmos w tym samym regionie, w którym znajduje się źródłowa baza danych. 

* **Włącz protokół TLS:** Azure Cosmos DB ma rygorystyczne wymagania i standardy dotyczące zabezpieczeń. Należy pamiętać o włączeniu protokołu TLS podczas korzystania z konta. W przypadku korzystania z programu CQL z protokołem SSH istnieje możliwość zapewnienia informacji o protokole TLS.

## <a name="options-to-migrate-data"></a>Opcje migracji danych

Dane z istniejących obciążeń bazy danych Cassandra można przenieść do usługi Azure Cosmos DB przy użyciu następujących opcji:

* [Przy użyciu polecenia cqlsh COPY](#migrate-data-using-cqlsh-copy-command)  
* [Przy użyciu platformy Spark](#migrate-data-using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Migrowanie danych przy użyciu polecenia cqlsh COPY

[Polecenie COPY języka CQL](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) służy do kopiowania danych lokalnych na konto interfejsu API Cassandra w usłudze Azure Cosmos DB. Aby skopiować dane, wykonaj następujące kroki:

1. Uzyskaj informacje o parametrach połączenia konta interfejsu API Cassandra:

   * Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i przejdź do swojego konta usługi Azure Cosmos.

   * Otwórz okienko **Parametry połączenia** zawierające wszystkie informacje, których potrzebujesz, aby połączyć się z kontem interfejsu API Cassandra z poziomu serwera cqlsh.

2. Zaloguj się do serwera cqlsh przy użyciu informacji o połączeniu z portalu.

3. Użyj polecenia CQL COPY, aby skopiować dane lokalne na konto interfejsu API Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Migrowanie danych przy użyciu platformy Spark 

Aby przeprowadzić migrację danych na konto interfejsu API Cassandra za pomocą platformy Spark, wykonaj następujące kroki:

- Przeprowadź aprowizację [klastra platformy Azure Databricks](cassandra-spark-databricks.md) lub [klastra usługi HDInsight](cassandra-spark-hdinsight.md) 

- Przenieś dane do docelowego punktu końcowego interfejsu API Cassandra przy użyciu [operacji kopiowania tabeli](cassandra-spark-table-copy-ops.md) 

Migracja danych przy użyciu zadań platformy Spark jest opcją zalecaną, gdy Twoje dane znajdują się w istniejącym klastrze na maszynach wirtualnych platformy Azure lub w jakiejkolwiek innej chmurze. Ta opcja wymaga skonfigurowania platformy Spark jako elementu pośredniczącego na potrzeby jednorazowego lub regularnego pozyskiwania. Migrację możesz przyspieszyć, używając łączności usługi Azure ExpressRoute między środowiskiem lokalnym i platformą Azure. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz usunąć grupę zasobów, konto usługi Azure Cosmos oraz wszystkie powiązane zasoby, gdy nie będą już potrzebne. Aby to zrobić, wybierz grupę zasobów maszyny wirtualnej, wybierz pozycję **Usuń**, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób migrowania danych na konto interfejsu API Cassandra w usłudze Azure Cosmos DB. Możesz teraz przejść do następującego artykułu, aby poznać inne zagadnienia dotyczące usługi Azure Cosmos DB:

> [!div class="nextstepaction"]
> [Dostosowywalne poziomy spójności danych w usłudze Azure Cosmos DB](../cosmos-db/consistency-levels.md)


