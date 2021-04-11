---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: include
ms.date: 01/08/2021
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 698219a446240950b9a661f923109fbb2f863556
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106178553"
---
Jeśli obecnie używasz usługi Azure Table Storage, po rozpoczęciu korzystania z interfejsu API tabel usługi Azure Cosmos DB uzyskasz następujące korzyści:

|Cecha | Usługa Azure Table Storage | Interfejs API tabel usługi Azure Cosmos DB |
| --- | --- | --- |
| Opóźnienie | Niewielkie, ale brak górnych granic opóźnienia. | Milisekundowe opóźnienie odczytu i zapisu oraz odczyt o opóźnieniu <10 ms i zapis o opóźnieniu <15 ms w 99. percentylu, w dowolnej skali, w dowolnym miejscu na świecie. |
| Przepływność | Zmienny model przepływności. Tabele mają limit skalowalności 20 000 operacji/s. | Wysoka skalowalność dzięki [dedykowanej zarezerwowanej przepływności na tabelę](../articles/cosmos-db/request-units.md), gwarantowanej umowami SLA. Konta nie mają górnego limitu przepływności i obsługują >10 000 000 operacji/s na tabelę (w trybie przepływności). |
| Dystrybucja globalna | Pojedynczy region z jednym opcjonalnym dodatkowym regionem odczytu w celu zapewnienia wysokiej dostępności, który obsługuje automatyczne i ręczne przełączenie do trybu failover. | [Kompleksowa dystrybucja globalna](../articles/cosmos-db/distribute-data-globally.md) do ponad 30 regionów. Obsługa [automatycznego i ręcznego trybu failover](../articles/cosmos-db/high-availability.md) w dowolnym momencie i każdym miejscu na świecie. |
| Indeksowanie | Tylko podstawowy indeks PartitionKey i RowKey. Brak dodatkowych indeksów. | Automatyczne i kompletne indeksowanie wszystkich właściwości, brak zarządzania indeksem. |
| Zapytanie | Wykonanie zapytania wykorzystuje indeks klucza podstawowego, a w przeciwnym przypadku skanuje. | Zapytania mogą korzystać z automatycznego indeksowania właściwości, co skraca czas odpowiedzi. |
| Spójność | Na poziomie „strong” w regionie podstawowym, na poziomie „eventual” w regionie pomocniczym. | [Pięć dobrze zdefiniowanych poziomów spójności](../articles/cosmos-db/consistency-levels.md) , aby zapewnić dostępność, opóźnienia, przepływność i spójność w zależności od potrzeb aplikacji. |
| Ceny | Na podstawie użycia. | Dostępne zarówno w trybie dyspozycyjności, jak [i z](../articles/cosmos-db/serverless.md) [obsługą administracyjną](../articles/cosmos-db/set-throughput.md) . |
| Umowy SLA | Dostępność na poziomie 99,99%. | Umowa SLA gwarantująca dostępność na poziomie co najmniej 99,99% dla wszystkich kont w obrębie jednego regionu i wszystkich kont w wielu regionach w przypadku rozluźnionej spójności, a także dostępność do odczytu na poziomie co najmniej 99,999% dla wszystkich kont bazy danych w wielu regionach w ramach [wiodących w branży, kompleksowych umów SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) dotyczących ogólnej dostępności. |
