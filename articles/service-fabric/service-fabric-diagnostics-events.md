---
title: Zdarzenia Service Fabric platformy Azure
description: Dowiedz się więcej na temat Service Fabricych zdarzeń, które są dostępne w usłudze Box, aby ułatwić monitorowanie klastra Service Fabric platformy Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 638b650e485ad3e83bd6021639a7e55b540d9cdc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75451727"
---
# <a name="service-fabric-events"></a>Zdarzenia usługi Service Fabric 

Platforma Service Fabric zapisuje kilka zdarzeń strukturalnych dla najważniejszych działań operacyjnych, które są wykonywane w ramach klastra. Ten zakres od uaktualnień klastra do decyzji dotyczących umieszczania replik. Każde zdarzenie, które Service Fabric uwidacznia mapowanie do jednej z następujących jednostek w klastrze:
* Klaster
* Aplikacja
* Usługa
* Partycja
* Replika 
* Kontener

Aby wyświetlić pełną listę zdarzeń uwidocznionych przez [listę zdarzeń Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Poniżej przedstawiono kilka przykładów scenariuszy, w których powinny zostać wyświetlone zdarzenia w klastrze. 
* Zdarzenia cyklu życia węzła: w miarę, jak węzły są dostępne, przejdź w dół, Skaluj w poziomie i w dół, uruchom ponownie i są aktywowane/dezaktywowane, te zdarzenia zostaną uwidocznione i pomagają w ustaleniu, czy wystąpił problem z maszyną, czy też interfejs API, który został wywołany przez SF w celu zmodyfikowania stanu węzła.
* Uaktualnianie klastra: po uaktualnieniu klastra (wersja SF lub zmiana konfiguracji) zobaczysz, że zainicjowano uaktualnienie, przechodzenie przez wszystkie domeny uaktualnienia i zakończenie (lub wycofanie). 
* Uaktualnianie aplikacji: podobnie jak w przypadku uaktualnień klastra, istnieje obszerny zestaw zdarzeń w ramach uaktualniania. Te zdarzenia mogą być przydatne w przypadku zaplanowania uaktualnienia, bieżącego stanu uaktualnienia i ogólnej sekwencji zdarzeń. Jest to przydatne w celu sprawdzenia, jakie uaktualnienia zostały pomyślnie wdrożone lub czy wycofanie zostało wyzwolone.
* Wdrożenie/usuwanie aplikacji/usługi: istnieją zdarzenia dla każdej aplikacji, usługi i kontenera, które są tworzone lub usuwane i przydatne podczas skalowania lub wyróżnienia, na przykład zwiększając liczbę replik
* Partycja przenoszona (ponowna konfiguracja): zawsze, gdy partycja stanowa przechodzi przez ponowną konfigurację (zmiany w zestawie replik), rejestrowane jest zdarzenie. Jest to przydatne, jeśli próbujesz zrozumieć, jak często zestaw replik partycji jest zmieniany lub przełączany w tryb failover, lub Śledź węzeł, w którym uruchomiono replikę podstawową w dowolnym momencie.
* Zdarzenia chaos: w przypadku korzystania z usługi [Chaos](service-fabric-controlled-chaos.md) Service Fabric zdarzenia będą widoczne przy każdym uruchomieniu lub zatrzymaniu usługi lub po wprowadzeniu błędu w systemie.
* Zdarzenia dotyczące kondycji: Service Fabric uwidacznia zdarzenia dotyczące kondycji za każdym razem, gdy zostanie utworzony raport lub Kondycja błędu, lub jednostka przechodzi z powrotem do stanu kondycji lub raport o kondycji wygaśnie. Te zdarzenia są bardzo przydatne do śledzenia historycznych statystyk kondycji dla jednostki. 

## <a name="how-to-access-events"></a>Jak uzyskać dostęp do zdarzeń

Istnieje kilka różnych sposobów, w których można uzyskać dostęp do Service Fabric zdarzeń:
* Zdarzenia są rejestrowane za pomocą standardowych kanałów, takich jak ETW/dzienniki zdarzeń systemu Windows i mogą być wizualizowane przez dowolne narzędzie monitorujące, które obsługuje takie jak dzienniki Azure Monitor. Domyślnie klastry utworzone w portalu mają włączoną diagnostykę i umożliwiają agentowi diagnostyki systemu Windows Azure wysyłanie zdarzeń do usługi Azure Table Storage, ale nadal trzeba zintegrować ją z zasobem analizy dzienników. Przeczytaj więcej na temat konfigurowania [agenta Diagnostyka Azure](service-fabric-diagnostics-event-aggregation-wad.md) , aby zmodyfikować konfigurację diagnostyki klastra w celu pobrania większej liczby dzienników lub liczników wydajności oraz [integracji dzienników Azure monitor](service-fabric-diagnostics-event-analysis-oms.md)
* Interfejsy API REST usługi EventStore, które umożliwiają bezpośrednie wysyłanie zapytań do klastra, lub za pomocą biblioteki klienta Service Fabric. Zobacz [interfejsy API EventStore zapytań dla zdarzeń klastra](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Następne kroki
* Więcej informacji na temat monitorowania klastra — [monitorowanie klastra i platformy](service-fabric-diagnostics-event-generation-infra.md).
* Dowiedz się więcej na temat usługi EventStore Service — [Omówienie usługi EventStore](service-fabric-diagnostics-eventstore.md)
