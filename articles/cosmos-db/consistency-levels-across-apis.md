---
title: Poziomy spójności i interfejsy API usługi Azure Cosmos DB
description: Zrozumienie mapowania poziomu spójności między różnymi interfejsami API w Azure Cosmos DB i Apache Cassandra, MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/6/2020
ms.reviewer: sngun
ms.openlocfilehash: bb8a413f2e2a3aa4a8facd533d822312bb61fa0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613564"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Poziomy spójności i interfejsy API usługi Azure Cosmos DB

Azure Cosmos DB zapewnia natywną obsługę interfejsów API zgodnych z protokołem przewodowym dla popularnych baz danych. Obejmują one MongoDB, Apache Cassandra, Gremlin i Azure Table Storage. Te bazy danych nie oferują precyzyjnie zdefiniowanych modeli spójności ani gwarancji objętych umową SLA dla poziomów spójności. Zwykle zapewniają tylko podzestaw pięciu modeli spójności oferowanych przez Azure Cosmos DB.

W przypadku korzystania z interfejsu API SQL, interfejsu API Gremlin i interfejs API tabel, używany jest domyślny poziom spójności skonfigurowany na koncie usługi Azure Cosmos. 

W przypadku korzystania z interfejsu API interfejs API Cassandra lub Azure Cosmos DB dla MongoDB aplikacje uzyskają pełny zestaw poziomów spójności oferowanych przez Apache Cassandra i MongoDB, a nawet silniejsze gwarancje spójności i trwałości. W tym dokumencie przedstawiono odpowiednie Azure Cosmos DB poziomów spójności dla poziomów spójności Apache Cassandra i MongoDB.

> [!NOTE]
> Domyślny model spójności dla Azure Cosmos DB jest sesją. Sesja jest modelem spójności zorientowanym na klienta, który nie jest natywnie obsługiwany przez Cassandra lub MongoDB. Aby uzyskać więcej informacji o tym, który model spójności wybrać, zobacz [poziomy spójności w Azure Cosmos DB](consistency-levels.md)

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Mapowanie między Cassandra Apache i Azure Cosmos DB poziomów spójności

W przeciwieństwie do Azure Cosmos DB technologia Apache Cassandra nie zapewnia natywnej dokładnej kontroli spójności.  Zamiast tego Apache Cassandra zapewnia poziom spójności zapisu i poziom spójności odczytu, aby zapewnić wysoką dostępność, spójność i wady opóźnienia. W przypadku korzystania z interfejs API Cassandra Azure Cosmos DB: 

* Poziom spójności zapisu usługi Apache Cassandra jest mapowany na domyślny poziom spójności skonfigurowany na koncie usługi Azure Cosmos. Nie można zmienić spójności operacji zapisu (CL) dla poszczególnych żądań.

* Azure Cosmos DB dynamicznie mapuje poziom spójności odczytu określony przez sterownik klienta Cassandra na jeden z Azure Cosmos DB poziomów spójności skonfigurowanych dynamicznie na żądanie odczytu. 

W poniższej tabeli przedstawiono, jak natywne poziomy spójności Cassandra są mapowane na poziomy spójności Azure Cosmos DB podczas korzystania interfejs API Cassandra:  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Mapowanie modelu spójności Cassandra" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>Mapowanie między MongoDB i Azure Cosmos DB poziomów spójności

W przeciwieństwie do Azure Cosmos DB, natywny MongoDB nie zapewnia precyzyjnie zdefiniowanych gwarancji spójności. Zamiast tego natywny MongoDB umożliwia użytkownikom skonfigurowanie następujących gwarancji spójności: uwagi dotyczące zapisu, Odczyt i dyrektywa IsMaster — aby skierować operacje odczytu do replik podstawowych lub pomocniczych w celu osiągnięcia żądanego poziomu spójności.

W przypadku korzystania z interfejsu API Azure Cosmos DB dla MongoDB, sterownik MongoDB traktuje region zapisu jako replikę podstawową i wszystkie pozostałe regiony są odczytywane z repliki. Możesz wybrać region skojarzony z kontem usługi Azure Cosmos jako replikę podstawową. 

Podczas korzystania z interfejsu API Azure Cosmos DB dla MongoDB:

* Problem dotyczący zapisu jest mapowany na domyślny poziom spójności skonfigurowany na koncie usługi Azure Cosmos.

* Azure Cosmos DB dynamicznie mapuje problem odczytu określony przez sterownik klienta MongoDB na jeden z Azure Cosmos DB poziomów spójności skonfigurowanych dynamicznie na żądanie odczytu.  

* Można dodać adnotacje do określonego regionu skojarzonego z kontem usługi Azure Cosmos jako "podstawowe", tworząc region jako pierwszy zapisywalny region. 

W poniższej tabeli pokazano, w jaki sposób natywne zagadnienia dotyczące zapisu/odczytu MongoDB są mapowane na poziomy spójności usługi Azure Cosmos w przypadku korzystania z interfejsu API Azure Cosmos DB dla MongoDB:

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="Mapowanie modelu spójności Cassandra" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat poziomów spójności i zgodności między interfejsami API Azure Cosmos DB przy użyciu interfejsów API Open Source. Zobacz następujące artykuły:

* [Wady dostępności i wydajności dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Funkcje MongoDB obsługiwane przez interfejs API Azure Cosmos DB dla MongoDB](mongodb-feature-support.md)
* [Funkcje Apache Cassandra obsługiwane przez Azure Cosmos DB interfejs API Cassandra](cassandra-support.md)
