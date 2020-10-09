---
title: Dostępność usług Service Fabric Services
description: Opisuje wykrywanie błędów, tryb failover i odzyskiwanie usługi w aplikacji Service Fabric platformy Azure.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5306439184561e8dec8303a7b149f51d6c2f6e08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75551866"
---
# <a name="availability-of-service-fabric-services"></a>Dostępność usług Service Fabric Services
Ten artykuł zawiera omówienie sposobu, w jaki platforma Azure Service Fabric utrzymuje dostępność usługi.

## <a name="availability-of-service-fabric-stateless-services"></a>Dostępność Service Fabricych usług bezstanowych
Usługi Service Fabric mogą być stanowe lub bezstanowe. Usługa bezstanowa to usługa aplikacji, która nie ma [stanu lokalnego](service-fabric-concepts-state.md) , która musi być wysoce dostępna lub niezawodna.

Tworzenie usługi bezstanowej wymaga zdefiniowania `InstanceCount` . Liczba wystąpień definiuje liczbę wystąpień logiki aplikacji bezstanowej usługi, która powinna być uruchomiona w klastrze. Zwiększenie liczby wystąpień jest zalecanym sposobem skalowania usługi bezstanowej.

Gdy wystąpienie bezstanowej nazwy usługi nie powiedzie się, nowe wystąpienie jest tworzone w uprawnionym węźle w klastrze. Na przykład wystąpienie usługi bezstanowej może zakończyć się niepowodzeniem w Węzeł1 i zostać utworzone na komputerze Węzeł5.

## <a name="availability-of-service-fabric-stateful-services"></a>Dostępność Service Fabricych usług stanowych
Z usługą stanową jest skojarzona stan. W Service Fabric usługa stanowa jest modelowana jako zbiór replik. Każda replika jest uruchomionym wystąpieniem kodu usługi. Replika ma również kopię stanu dla tej usługi. Operacje odczytu i zapisu są wykonywane w jednej replice nazywanej *podstawowym*. Zmiany stanu z operacji zapisu są *replikowane* do innych replik w zestawie replik o nazwie *Active webbinds*i stosowane. 

Może istnieć tylko jedna replika podstawowa, ale może istnieć wiele aktywnych replik pomocniczych. Liczba aktywnych replik pomocniczych jest konfigurowalna, a większa liczba replik może tolerować większą liczbę współbieżnych błędów oprogramowania i sprzętu.

Jeśli replika podstawowa ulegnie awarii, Service Fabric udostępnia jedną z aktywnych replik pomocniczych nową replikę podstawową. Ta aktywna replika pomocnicza ma już zaktualizowaną wersję stanu za pośrednictwem *replikacji*i może kontynuować przetwarzanie dalszych operacji odczytu/zapisu. Ten proces jest nazywany *ponowną konfiguracją* i został szczegółowo opisany w artykule ponownej [konfiguracji](service-fabric-concepts-reconfiguration.md) .

Koncepcja repliki jest podstawowa lub aktywna pomocnicza, znana jako *Rola repliki*. Te repliki są szczegółowo opisane w artykule [repliki i wystąpienia](service-fabric-concepts-replica-lifecycle.md) . 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pojęć Service Fabric, zobacz następujące artykuły:

- [Skalowanie Service Fabric usług](service-fabric-concepts-scalability.md)
- [Partycjonowanie Service Fabric usług](service-fabric-concepts-partitioning.md)
- [Definiowanie stanu i zarządzanie nim](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

