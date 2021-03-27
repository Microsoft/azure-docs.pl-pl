---
title: 'Szybki Start: łączenie z linkiem usługi Azure Synapse dla Azure Cosmos DB'
description: Jak połączyć Azure Cosmos DB z obszarem roboczym Synapse za pomocą linku Synapse
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.subservice: synapse-link
ms.topic: quickstart
ms.date: 04/21/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 7d77431f5caa1a2ac67428326dcd6d4ce75a4a93
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625844"
---
# <a name="quickstart-connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Szybki Start: łączenie z linkiem usługi Azure Synapse dla Azure Cosmos DB

W tym artykule opisano, jak uzyskać dostęp do bazy danych Azure Cosmos DB z usługi Azure Synapse Analytics Studio przy użyciu linku Synapse. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed połączeniem konta Azure Cosmos DB z obszarem roboczym istnieje kilka rzeczy, które są potrzebne.

* Istniejące konto Azure Cosmos DB lub Utwórz nowe konto po tym [przewodniku szybki start](../cosmos-db/how-to-manage-database-account.md)
* Istniejący obszar roboczy Synapse lub Utwórz nowy obszar roboczy po tym [przewodniku szybki start](./quickstart-create-workspace.md) 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Włącz Azure Cosmos DB magazyn analityczny

Aby uruchomić analizę na dużą skalę w Azure Cosmos DB bez wpływu na wydajność operacyjną, zalecamy włączenie linku Synapse dla Azure Cosmos DB. Ta funkcja umożliwia HTAP funkcji kontenera i wbudowaną obsługę usługi Azure Synapse. Postępuj zgodnie z tym przewodnikiem Szybki Start, aby włączyć link Synapse dla kontenerów Cosmos DB.

## <a name="navigate-to-synapse-studio"></a>Przejdź do Synapse Studio

W obszarze roboczym usługi Synapse wybierz pozycję **Uruchom Synapse Studio**. Na stronie głównej programu Synapse Studio wybierz pozycję * * dane, co spowoduje przejście do **Eksplorator obiektów danych**.

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Łączenie bazy danych Azure Cosmos DB z obszarem roboczym Synapse

Łączenie Azure Cosmos DB bazą danych odbywa się jako połączona usługa. Połączona usługa Cosmos DB umożliwia użytkownikom przeglądanie i Eksplorowanie danych, odczytywanie i zapisywanie z Apache Spark usługi Azure Synapse Analytics lub SQL w Azure Cosmos DB.

Korzystając z Eksplorator obiektów danych, możesz połączyć się bezpośrednio z bazą danych Azure Cosmos DB, wykonując następujące czynności:

1. Wybierz ***+*** ikonę obok danych
2. Wybierz pozycję **Połącz z danymi zewnętrznymi**
3. Wybierz interfejs API, z którym chcesz nawiązać połączenie: SQL lub MongoDB
4. Wybierz przycisk ***Kontynuuj***
5. Nazwij połączoną usługę. Nazwa zostanie wyświetlona w Eksplorator obiektów i użyta przez Synapse Run-Times do łączenia się z bazą danych i kontenerami. Zalecamy używanie przyjaznej nazwy.
6. Wybierz **nazwę konta Cosmos DB** i **nazwę bazy danych**
7. Obowiązkowe Jeśli region nie zostanie określony, operacje czasu wykonywania Synapse będą kierowane do najbliższego regionu, w którym jest włączony magazyn analityczny. Można jednak ręcznie ustawić region, do którego użytkownicy mają uzyskiwać dostęp Cosmos DB do magazynu analitycznego. Wybierz opcję **dodatkowe właściwości połączenia** , a następnie pozycję **nowe**. W obszarze **Nazwa właściwości** wpisz **_PreferredRegions_*_ i ustaw wartość _*** na żądany region (przykład: WestUS2, nie ma spacji między wyrazami i liczbami).
8. Wybierz pozycję ***Utwórz***

Bazy danych Azure Cosmos DB są widoczne na karcie **połączonej** w sekcji Azure Cosmos DB. Można odróżnić HTAP włączony kontener Azure Cosmos DB z kontenera tylko OLTP o następujących ikonach:

**Kontener Synapse**:

![Kontener HTAP](./media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

**Kontener tylko OLTP**:

![Kontener OLTP](./media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Szybkie współdziałanie z akcjami generowanymi przez kod

Po kliknięciu prawym przyciskiem myszy w kontenerze będzie dostępna lista gestów, które będą wyzwalać czas wykonywania platformy Spark lub SQL. Zapis do kontenera odbywa się za pomocą transakcyjnego magazynu Azure Cosmos DB i będzie korzystać z jednostek żądania.  

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, co jest obsługiwane między Synapse i Azure Cosmos DB](./synapse-link/concept-synapse-link-cosmos-db-support.md)
* [Dowiedz się, jak badać magazyn analityczny za pomocą Apache Spark usługi Azure Synapse Analytics](synapse-link/how-to-query-analytical-store-spark.md)