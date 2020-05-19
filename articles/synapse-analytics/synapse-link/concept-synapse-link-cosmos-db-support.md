---
title: Link usługi Azure Synapse dla Azure Cosmos DB obsługiwanych funkcji
description: Zapoznaj się z bieżącą listą akcji obsługiwanych przez link Synapse platformy Azure dla Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 1b94d7677026f3695d07be4d83a5059373078c2e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599949"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Link usługi Azure Synapse dla Azure Cosmos DB obsługiwanych funkcji

W tym artykule opisano funkcje, które są obecnie obsługiwane w usłudze Azure Synapse link do Azure Cosmos DB. 

## <a name="azure-synapse-support"></a>Pomoc techniczna platformy Azure Synapse

Istnieją dwa typy kontenerów w Azure Cosmos DB:
* Kontener HTAP — kontener z włączonym linkiem Synapse. Ten kontener ma magazyn transakcyjny i magazyn analityczny. 
* Kontener OLTP — kontener z tylko magazynem transakcji; Łącze Synapse nie jest włączone. 

Można nawiązać połączenie z kontenerem Azure Cosmos DB bez włączania linku Synapse, w takim przypadku można tylko odczyt/zapis w magazynie transakcyjnym. Poniżej znajduje się lista obecnie obsługiwanych funkcji w ramach linku Synapse dla Azure Cosmos DB. 

| Kategoria              | Opis |[platforma Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [Bezserwerowy SQL](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------- |
| **Obsługa czasu wykonywania** |Obsługa odczytu lub zapisu przez usługę Azure Synapse w czasie wykonywania| ✓ | [Skontaktuj się z nami](mailto:AskSynapse@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB)|
| **Obsługa interfejsu API Azure Cosmos DB** |Obsługa interfejsu API jako linku Synapse| SQL/MongoDB | SQL/MongoDB |
| **Stream**  |Obiekty takie jak tabela, która może być utworzona, wskazująca bezpośrednio na kontener Azure Cosmos DB| Widok, tabela | Widok |
| **Przeczytaj**    |Odczytywanie danych z kontenera Azure Cosmos DB| OLTP/HTAP | HTAP  |
| **Prawem**   |Zapisywanie danych z czasu wykonywania do kontenera Azure Cosmos DB| OLTP | nie dotyczy |

* Jeśli zapisujesz dane do kontenera Azure Cosmos DB z platformy Spark, ten proces odbywa się za pośrednictwem transakcyjnego magazynu Azure Cosmos DB i wpłynie na transakcyjną wydajność Azure Cosmos DB przez zużywanie jednostek żądania.
* Integracja puli SQL za poorednictwem tabel zewnętrznych nie jest obecnie obsługiwana.

## <a name="supported-code-generated-actions-for-spark"></a>Obsługiwane akcje generowane przez kod dla platformy Spark

| Gest              | Opis |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Załaduj do ramki Dataframe** |Ładowanie i odczytywanie danych w ramce Dataframe platformy Spark |X| ✓ |
| **Utwórz tabelę platformy Spark** |Tworzenie tabeli wskazującej kontener Azure Cosmos DB|X| ✓ |
| **Zapisz ramkę danych do kontenera** |Zapisywanie danych w kontenerze|✓| ✓ |
| **Załaduj ramkę danych przesyłania strumieniowego z kontenera** |Przesyłanie strumieniowe danych przy użyciu kanału informacyjnego zmiany Azure Cosmos DB|✓| ✓ |
| **Zapisz ramkę danych przesyłania strumieniowego do kontenera** |Przesyłanie strumieniowe danych przy użyciu kanału informacyjnego zmiany Azure Cosmos DB|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>Obsługiwane akcje generowane w kodzie dla programu SQL Server

| Gest              | Opis |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Wybierz górną 100** |Podgląd pierwszych 100 elementów z kontenera|X| ✓ |
| **Utwórz widok** |Utwórz widok, aby bezpośrednio uzyskiwać dostęp do usługi BI w kontenerze za pomocą Synapse SQL|X| ✓ |

## <a name="next-steps"></a>Następne kroki

* Zobacz, jak [nawiązać połączenie z Synapse linkiem dla Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [Dowiedz się, jak badać magazyn analityczny przy użyciu platformy Spark](how-to-query-analytical-store-spark.md)