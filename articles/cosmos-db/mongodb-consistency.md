---
title: Mapowanie poziomów spójności dla Azure Cosmos DB interfejsu API dla MongoDB
description: Mapowanie poziomów spójności dla Azure Cosmos DB interfejsu API dla MongoDB.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 37c128a42ca68134b770b32c940d59834261ce44
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096584"
---
# <a name="consistency-levels-for-azure-cosmos-db-and-the-api-for-mongodb"></a>Poziomy spójności dla Azure Cosmos DB i interfejsu API dla MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

W przeciwieństwie do Azure Cosmos DB, natywny MongoDB nie zapewnia precyzyjnie zdefiniowanych gwarancji spójności. Zamiast tego natywny MongoDB umożliwia użytkownikom skonfigurowanie następujących gwarancji spójności: uwagi dotyczące zapisu, Odczyt i dyrektywa IsMaster — aby skierować operacje odczytu do replik podstawowych lub pomocniczych w celu osiągnięcia żądanego poziomu spójności.

W przypadku korzystania z interfejsu API Azure Cosmos DB dla MongoDB, sterownik MongoDB traktuje region zapisu jako replikę podstawową i wszystkie pozostałe regiony są odczytywane z repliki. Możesz wybrać region skojarzony z kontem usługi Azure Cosmos jako replikę podstawową.

> [!NOTE]
> Domyślny model spójności dla Azure Cosmos DB jest sesją. Sesja jest modelem spójności zorientowanym na klienta, który nie jest natywnie obsługiwany przez Cassandra lub MongoDB. Aby uzyskać więcej informacji o tym, który model spójności wybrać, zobacz [poziomy spójności w Azure Cosmos DB](consistency-levels.md)

Podczas korzystania z interfejsu API Azure Cosmos DB dla MongoDB:

* Problem dotyczący zapisu jest mapowany na domyślny poziom spójności skonfigurowany na koncie usługi Azure Cosmos.

* Azure Cosmos DB dynamicznie mapuje problem odczytu określony przez sterownik klienta MongoDB na jeden z Azure Cosmos DB poziomów spójności skonfigurowanych dynamicznie na żądanie odczytu.  

* Można dodać adnotacje do określonego regionu skojarzonego z kontem usługi Azure Cosmos jako "podstawowe", tworząc region jako pierwszy zapisywalny region. 

## <a name="mapping-consistency-levels"></a>Mapowanie poziomów spójności

W poniższej tabeli pokazano, w jaki sposób natywne zagadnienia dotyczące zapisu/odczytu MongoDB są mapowane na poziomy spójności usługi Azure Cosmos w przypadku korzystania z interfejsu API Azure Cosmos DB dla MongoDB:

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="Mapowanie modelu spójności MongoDB" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

Jeśli Twoje konto usługi Azure Cosmos jest skonfigurowane z poziomem spójności innym niż silna spójność, można sprawdzić prawdopodobieństwo, że klienci mogą uzyskać mocne i spójne odczyty dla obciążeń, patrząc na metrykę *probabilistically z Nieodświeżoną* (PBS). Ta Metryka jest dostępna w Azure Portal, aby dowiedzieć się więcej, zobacz [monitorowanie metryki probabilistically ograniczonej (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistyczne ograniczone nieaktualność pokazuje, jak to jest spójność ostateczna. Ta Metryka zawiera szczegółowe informacje o tym, jak często można uzyskać większą spójność niż poziom spójności skonfigurowany obecnie na koncie usługi Azure Cosmos. Innymi słowy, można zobaczyć prawdopodobieństwo (mierzone w milisekundach), aby uzyskać silnie spójne odczyty dla kombinacji regionów zapisu i odczytu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o globalnej dystrybucji i poziomach spójności dla Azure Cosmos DB:

* [Omówienie dystrybucji globalnej](distribute-data-globally.md)
* [Przegląd poziomu spójności](consistency-levels.md)
* [Wysoka dostępność](high-availability.md)
