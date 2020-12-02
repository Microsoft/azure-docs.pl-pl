---
title: Połącz z linkiem usługi Azure Synapse (wersja zapoznawcza) dla Azure Cosmos DB
description: Dowiedz się, jak połączyć bazę danych Azure Cosmos DB z obszarem roboczym usługi Azure Synapse za pomocą usługi Azure Synapse link.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 0e4bcc13a2eaddbf394d8468b29eeff475119e2d
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459027"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Połącz z linkiem usługi Azure Synapse dla Azure Cosmos DB

W tym artykule opisano, jak uzyskać dostęp do bazy danych Azure Cosmos DB z usługi Azure Synapse Analytics Studio za pomocą usługi Azure Synapse link.

## <a name="prerequisites"></a>Wymagania wstępne

Przed nawiązaniem połączenia z bazą danych Azure Cosmos DB z obszarem roboczym będziesz potrzebować:

* Istniejąca baza danych Azure Cosmos DB lub Utwórz nowe konto, wykonując czynności opisane w [przewodniku szybki start: Zarządzanie kontem Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account).
* Istniejący obszar roboczy usługi Azure Synapse lub Utwórz nowy obszar roboczy, wykonując czynności opisane w [przewodniku szybki start: Tworzenie obszaru roboczego Synapse](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace).

> [!IMPORTANT]
> Link Synapse platformy Azure dla Azure Cosmos DB jest obecnie obsługiwany w przypadku obszarów roboczych, dla których nie włączono zarządzanej sieci wirtualnej.

## <a name="enable-synapse-link-on-an-azure-cosmos-db-database-account"></a>Włącz link Synapse na koncie bazy danych Azure Cosmos DB

Aby uruchomić analizę na dużą skalę w Azure Cosmos DB bez wpływu na wydajność operacyjną, zalecamy włączenie linku Synapse dla Azure Cosmos DB. Link Synapse umożliwia HTAP funkcji kontenera i wbudowaną obsługę usługi Azure Synapse.

## <a name="go-to-synapse-studio"></a>Przejdź do Synapse Studio

W obszarze roboczym usługi Azure Synapse wybierz pozycję **Uruchom Synapse Studio**. Na stronie głównej programu Synapse Studio wybierz pozycję **dane**, co spowoduje przejście do Eksplorator obiektów danych.

## <a name="connect-an-azure-cosmos-db-database-to-an-azure-synapse-workspace"></a>Łączenie bazy danych Azure Cosmos DB z obszarem roboczym usługi Azure Synapse

Łączenie Azure Cosmos DB bazą danych odbywa się jako połączona usługa. Za pomocą połączonej usługi Azure Cosmos DB można przeglądać i eksplorować dane, czytać i pisać z Apache Spark na potrzeby usługi Azure Synapse Analytics lub SQL w Azure Cosmos DB.

Korzystając z Eksplorator obiektów danych, możesz połączyć się bezpośrednio z bazą danych Azure Cosmos DB, wykonując następujące czynności:

1. Wybierz **+** ikonę obok **danych**.
1. Wybierz pozycję **Połącz z danymi zewnętrznymi**.
1. Wybierz interfejs API, z którym chcesz nawiązać połączenie, na przykład **SQL API** lub **API for MongoDB**.
1. Wybierz opcję **Kontynuuj**.
1. Użyj przyjaznej nazwy, aby nazwać połączoną usługę. Nazwa zostanie wyświetlona w Eksplorator obiektów danych i będzie używana przez środowisko uruchomieniowe usługi Azure Synapse do łączenia się z bazą danych i kontenerami.
1. Wybierz **nazwę konta Azure Cosmos DB** i **nazwę bazy danych**.
1. Obowiązkowe Jeśli region nie zostanie określony, operacje środowiska uruchomieniowego usługi Azure Synapse są kierowane do najbliższego regionu, w którym jest włączony magazyn analityczny. Można również ręcznie ustawić region, w którym użytkownicy mają uzyskiwać dostęp do magazynu analitycznego Azure Cosmos DB. Wybierz pozycję **dodatkowe właściwości połączenia**, a następnie wybierz pozycję **Nowy**. W obszarze **Nazwa właściwości** wprowadź **PreferredRegions**. Ustaw **wartość** na żądany region, na przykład **WestUS2**. (Nie ma spacji między wyrazami i cyfrą).
1. Wybierz pozycję **Utwórz**.

Bazy danych Azure Cosmos DB są wyświetlane na karcie **połączonej** w sekcji **Azure Cosmos DB** . Za pomocą Azure Cosmos DB można odróżnić kontener z obsługą HTAP z kontenera "tylko OLTP" przez następujące ikony:

**Kontener tylko OLTP**:

![Wizualizacja pokazująca ikonę kontenera OLTP.](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**Kontener z włączonym HTAP**:

![Wizualizacja pokazująca ikonę kontenera HTAP.](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Szybkie współdziałanie z akcjami generowanymi przez kod

Klikając prawym przyciskiem myszy kontener, masz listę gestów, które będą wyzwalać środowisko uruchomieniowe Spark lub SQL. Zapis do kontenera odbywa się za pomocą transakcyjnego magazynu Azure Cosmos DB i będzie korzystać z jednostek żądania.  

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, co jest obsługiwane między usługą Azure Synapse i Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Dowiedz się, jak badać magazyn analityczny przy użyciu platformy Spark](./how-to-query-analytical-store-spark.md)
