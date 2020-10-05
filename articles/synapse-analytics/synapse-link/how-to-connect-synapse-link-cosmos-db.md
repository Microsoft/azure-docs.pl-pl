---
title: Połącz z linkiem usługi Azure Synapse (wersja zapoznawcza) dla Azure Cosmos DB
description: Jak połączyć Azure Cosmos DB z obszarem roboczym Synapse za pomocą usługi Azure Synapse link
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: f453c074126e448eb54d432532b9064b3eb3cb35
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91287896"
---
# <a name="connect-to-azure-synapse-link-preview-for-azure-cosmos-db"></a>Połącz z linkiem usługi Azure Synapse (wersja zapoznawcza) dla Azure Cosmos DB

W tym artykule opisano, jak uzyskać dostęp do bazy danych Azure Cosmos DB z usługi Azure Synapse Analytics Studio za pomocą usługi Azure Synapse link.

## <a name="prerequisites"></a>Wymagania wstępne

Przed nawiązaniem połączenia z bazą danych Azure Cosmos DB z obszarem roboczym będziesz potrzebować:

> [!IMPORTANT]
> Link Synapse platformy Azure dla Azure Cosmos DB jest obecnie obsługiwany w przypadku obszarów roboczych, dla których nie włączono zarządzanej sieci wirtualnej. 

* Istniejąca baza danych Azure Cosmos DB lub Utwórz nowe konto po tym [przewodniku szybki start](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account)
* Istniejący obszar roboczy Synapse lub Utwórz nowy obszar roboczy po tym [przewodniku szybki start](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace) 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Włącz Azure Cosmos DB magazyn analityczny

Aby uruchomić analizę na dużą skalę w Azure Cosmos DB bez wpływu na wydajność operacyjną, zalecamy włączenie linku Synapse dla Azure Cosmos DB. Link Synapse umożliwia HTAP funkcji kontenera i wbudowaną obsługę usługi Azure Synapse.

## <a name="navigate-to-synapse-studio"></a>Przejdź do Synapse Studio

W obszarze roboczym usługi Synapse wybierz pozycję **Uruchom Synapse Studio**. Na stronie głównej programu Synapse Studio wybierz pozycję * * dane, co spowoduje przejście do **Eksplorator obiektów danych**.

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Łączenie bazy danych Azure Cosmos DB z obszarem roboczym Synapse

Łączenie Azure Cosmos DB bazą danych odbywa się jako połączona usługa. Połączona usługa Azure Cosmos DB umożliwia użytkownikom przeglądanie i Eksplorowanie danych, odczytywanie i zapisywanie z Apache Spark usługi Azure Synapse Analytics lub SQL w Azure Cosmos DB.

Korzystając z Eksplorator obiektów danych, możesz połączyć się bezpośrednio z bazą danych Azure Cosmos DB, wykonując następujące czynności:

1. Wybierz ***+*** ikonę obok danych
2. Wybierz pozycję **Połącz z danymi zewnętrznymi**
3. Wybierz interfejs API, z którym chcesz nawiązać połączenie: interfejs API SQL lub interfejs API dla MongoDB
4. Wybierz przycisk ***Kontynuuj***
5. Nazwij połączoną usługę. Nazwa zostanie wyświetlona w Eksplorator obiektów i użyta przez Synapse Run-Times do łączenia się z bazą danych i kontenerami. Zalecamy używanie przyjaznej nazwy.
6. Wybierz **nazwę konta Azure Cosmos DB** i **nazwę bazy danych**
7. Obowiązkowe Jeśli region nie zostanie określony, operacje czasu wykonywania Synapse będą kierowane do najbliższego regionu, w którym jest włączony magazyn analityczny. Można jednak ręcznie ustawić region, do którego użytkownicy mają uzyskiwać dostęp Azure Cosmos DB do magazynu analitycznego. Wybierz opcję **dodatkowe właściwości połączenia** , a następnie pozycję **nowe**. W obszarze **Nazwa właściwości**wpisz ***PreferredRegions*** i ustaw **wartość** na żądany region (przykład: WestUS2, nie ma spacji między wyrazami i cyfrą).
8. Wybierz pozycję ***Utwórz***

Bazy danych Azure Cosmos DB są widoczne na karcie **połączonej** w sekcji Azure Cosmos DB. Za pomocą Azure Cosmos DB można odróżnić kontener z włączonym HTAP z kontenera tylko OLTP przez następujące ikony:

**Kontener tylko OLTP**:

![Kontener OLTP](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**HTAP włączony kontener**:

![Kontener HTAP](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Szybkie współdziałanie z akcjami generowanymi przez kod

Klikając prawym przyciskiem myszy kontener, masz listę gestów, które będą wyzwalać czas wykonywania platformy Spark lub SQL. Zapis do kontenera odbywa się za pomocą transakcyjnego magazynu Azure Cosmos DB i będzie korzystać z jednostek żądania.  

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, co jest obsługiwane między Synapse i Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Dowiedz się, jak badać magazyn analityczny przy użyciu platformy Spark](./how-to-query-analytical-store-spark.md)