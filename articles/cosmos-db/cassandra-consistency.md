---
title: Poziomy spójności Apache Cassandra i Azure Cosmos DB
description: Cassandra Apache Azure Cosmos DB i poziom spójności.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: f640ad85fd34dd5a4803e7dd96f1c0283f0c859a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100434"
---
# <a name="apache-cassandra-and-azure-cosmos-db-consistency-levels"></a>Poziomy spójności Apache Cassandra i Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

W przeciwieństwie do Azure Cosmos DB technologia Apache Cassandra nie zapewnia natywnej dokładnej kontroli spójności. Zamiast tego Apache Cassandra zapewnia poziom spójności zapisu i poziom spójności odczytu, aby zapewnić wysoką dostępność, spójność i wady opóźnienia. W przypadku korzystania z interfejs API Cassandra Azure Cosmos DB:

* Poziom spójności zapisu usługi Apache Cassandra jest mapowany na domyślny poziom spójności skonfigurowany na koncie usługi Azure Cosmos. Nie można zmienić spójności operacji zapisu (CL) dla poszczególnych żądań.

* Azure Cosmos DB dynamicznie mapuje poziom spójności odczytu określony przez sterownik klienta Cassandra na jeden z Azure Cosmos DB poziomów spójności skonfigurowanych dynamicznie na żądanie odczytu.

## <a name="mapping-consistency-levels"></a>Mapowanie poziomów spójności

W poniższej tabeli przedstawiono, jak natywne poziomy spójności Cassandra są mapowane na poziomy spójności Azure Cosmos DB podczas korzystania interfejs API Cassandra:  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Mapowanie modelu spójności Cassandra" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

Jeśli Twoje konto usługi Azure Cosmos jest skonfigurowane z poziomem spójności innym niż silna spójność, można sprawdzić prawdopodobieństwo, że klienci mogą uzyskać mocne i spójne odczyty dla obciążeń, patrząc na metrykę *probabilistically z Nieodświeżoną* (PBS). Ta Metryka jest dostępna w Azure Portal, aby dowiedzieć się więcej, zobacz [monitorowanie metryki probabilistically ograniczonej (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistyczne ograniczone nieaktualność pokazuje, jak to jest spójność ostateczna. Ta Metryka zawiera szczegółowe informacje o tym, jak często można uzyskać większą spójność niż poziom spójności skonfigurowany obecnie na koncie usługi Azure Cosmos. Innymi słowy, można zobaczyć prawdopodobieństwo (mierzone w milisekundach), aby uzyskać silnie spójne odczyty dla kombinacji regionów zapisu i odczytu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o globalnej dystrybucji i poziomach spójności dla Azure Cosmos DB:

* [Omówienie dystrybucji globalnej](distribute-data-globally.md)
* [Przegląd poziomu spójności](consistency-levels.md)
* [Wysoka dostępność](high-availability.md)
