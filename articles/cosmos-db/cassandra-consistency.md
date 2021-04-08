---
title: Poziomy spójności Apache Cassandra i Azure Cosmos DB
description: Cassandra Apache Azure Cosmos DB i poziom spójności.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 3107610215d5b37c43124ce4129b2eb5437e3b62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516832"
---
# <a name="apache-cassandra-and-azure-cosmos-db-cassandra-api-consistency-levels"></a>Azure Cosmos DB Apache Cassandra i interfejs API Cassandra poziomów spójności
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

W przeciwieństwie do Azure Cosmos DB technologia Apache Cassandra nie zapewnia natywnej dokładnej kontroli spójności. Zamiast tego Apache Cassandra zapewnia poziom spójności zapisu i poziom spójności odczytu, aby zapewnić wysoką dostępność, spójność i wady opóźnienia. W przypadku korzystania z interfejs API Cassandra Azure Cosmos DB:

* Poziom spójności zapisu usługi Apache Cassandra jest mapowany na domyślny poziom spójności skonfigurowany na koncie usługi Azure Cosmos. Nie można zmienić spójności operacji zapisu (CL) dla poszczególnych żądań.

* Azure Cosmos DB dynamicznie mapuje poziom spójności odczytu określony przez sterownik klienta Cassandra na jeden z Azure Cosmos DB poziomów spójności skonfigurowanych dynamicznie na żądanie odczytu.

## <a name="multi-region-writes-vs-single-region-writes"></a>Zapisy wieloregionowe i zapisy w jednym regionie

Baza danych Apache Cassandra to system z wieloma wzorcami domyślnie i nie udostępnia opcji out-of-box dla zapisów w jednym regionie z replikacją z wieloma regionami dla operacji odczytu. Jednak Azure Cosmos DB zapewnia możliwość posiadania jednego regionu lub [wieloregionowej](how-to-multi-master.md) konfiguracji zapisu. Jedną z zalet możliwości wyboru konfiguracji zapisu obejmującą jeden region w wielu regionach jest uniknięcie scenariuszy konfliktów między regionami oraz możliwość utrzymania silnej spójności w wielu regionach. 

Dzięki zapisom w jednym regionie można zachować silną spójność, zachowując jednocześnie poziom wysokiej dostępności w regionach z [automatycznym trybem failover](high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage). W tej konfiguracji można w dalszym ciągu wykorzystać ustawienia lokalizacji danych w celu zmniejszenia opóźnień odczytu w celu obniżenia spójności ostatecznej na podstawie żądania. Oprócz tych możliwości platforma Azure Cosmos DB zapewnia również możliwość włączenia [nadmiarowości strefy](high-availability.md#availability-zone-support) podczas wybierania regionu. W przeciwieństwie do natywnych Cassandra Apache, Azure Cosmos DB pozwala na nawigowanie po Theoremniu [spektrum](consistency-levels.md#rto) z większą szczegółowością.

## <a name="mapping-consistency-levels"></a>Mapowanie poziomów spójności

Platforma Azure Cosmos DB zapewnia zestaw pięciu dobrze zdefiniowanych ustawień spójności w przypadku użycia w firmie w odniesieniu do replikacji i kompromisów zdefiniowanych przez [theorem Cap](https://en.wikipedia.org/wiki/CAP_theorem) i [PACLC theorem](https://en.wikipedia.org/wiki/PACELC_theorem). Ponieważ ta metoda różni się znacznie od Cassandra Apache, zalecamy zapoznanie się z informacjami na temat ustawień spójności w naszej [dokumentacji](consistency-levels.md)Azure Cosmos DB oraz zapoznanie się z tym krótkim przewodnikiem [wideo](https://www.youtube.com/watch?v=t1--kZjrG-o) w celu zrozumienia ustawień spójności na platformie Azure Cosmos DB.

W poniższej tabeli przedstawiono możliwe mapowania między Cassandraą Apache a Azure Cosmos DB poziomów spójności podczas korzystania z interfejs API Cassandra. Przedstawiono w nim konfiguracje dla jednego regionu, odczyty z jednego regionu i zapisów w wieloregionach.

> [!NOTE]
> Nie są to dokładne mapowania. Zamiast tego podałeś najbliższe analogki do Apache Cassandra i odróżnić wszelkie różnice jakościowe w kolumnie z skrajnym prawej stronie. Jak wspomniano powyżej, zalecamy przejrzenie [ustawień spójności](consistency-levels.md)Azure Cosmos DB. 

:::image type="content" source="./media/cassandra-consistency/account.png" alt-text="Mapowanie poziomu konta spójności Cassandra" lightbox="./media/cassandra-consistency/account.png" :::

:::image type="content" source="./media/cassandra-consistency/dynamic.png" alt-text="Dynamiczne mapowanie spójności Cassandra" lightbox="./media/cassandra-consistency/dynamic.png" :::

Jeśli Twoje konto usługi Azure Cosmos jest skonfigurowane z poziomem spójności innym niż silna spójność, można sprawdzić prawdopodobieństwo, że klienci mogą uzyskać mocne i spójne odczyty dla obciążeń, patrząc na metrykę *probabilistically z Nieodświeżoną* (PBS). Ta Metryka jest dostępna w Azure Portal, aby dowiedzieć się więcej, zobacz [monitorowanie metryki probabilistically ograniczonej (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistyczne ograniczone nieaktualność pokazuje, jak to jest spójność ostateczna. Ta Metryka zawiera szczegółowe informacje o tym, jak często można uzyskać większą spójność niż poziom spójności skonfigurowany obecnie na koncie usługi Azure Cosmos. Innymi słowy, można zobaczyć prawdopodobieństwo (mierzone w milisekundach), aby uzyskać silnie spójne odczyty dla kombinacji regionów zapisu i odczytu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o globalnej dystrybucji i poziomach spójności dla Azure Cosmos DB:

* [Omówienie dystrybucji globalnej](distribute-data-globally.md)
* [Przegląd poziomu spójności](consistency-levels.md)
* [Wysoka dostępność](high-availability.md)
