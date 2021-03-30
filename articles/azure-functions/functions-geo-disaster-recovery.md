---
title: Azure Functions geograficznie — odzyskiwanie po awarii i wysoka dostępność
description: Jak używać regionów geograficznych w celu zapewnienia nadmiarowości i przełączania do trybu failover w Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 446a76b823ce3592a83d0c2f898041951361b47e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86506285"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Functions geograficznie — odzyskiwanie po awarii

Gdy cały region lub centra danych platformy Azure są nieprzerwane, ma kluczowe znaczenie dla obliczeń, które kontynuują przetwarzanie w innym regionie.  W tym artykule opisano niektóre strategie, których można użyć do wdrożenia funkcji w celu umożliwienia odzyskiwania po awarii.

## <a name="basic-concepts"></a>Podstawowe pojęcia

Azure Functions uruchamiane w określonym regionie.  Aby uzyskać większą dostępność, można wdrożyć te same funkcje w wielu regionach.  W przypadku wielu regionów funkcje działające w wzorcu *aktywny/aktywny* lub we wzorcu *aktywny/pasywny* .  

* Aktywny/aktywny. Oba regiony są aktywne i pobierają zdarzenia (duplikowane lub obracane). Funkcja Active/Active jest zalecana dla funkcji HTTPS w połączeniu z usługami frontonu platformy Azure.
* Aktywne/pasywne. Jeden region jest aktywny i otrzymuje zdarzenia, podczas gdy pomocniczy jest bezczynny.  Gdy jest wymagane przejście w tryb failover, region pomocniczy jest aktywowany i przetrwa przetwarzanie.  Jest to zalecane w przypadku funkcji innych niż HTTP, takich jak Service Bus i Event Hubs.

Przeczytaj, jak [uruchamiać aplikacje w wielu regionach](/azure/architecture/reference-architectures/app-service-web-app/multi-region) , aby uzyskać więcej informacji na temat wdrożeń z wieloma regionami.

## <a name="activeactive-for-https-functions"></a>Aktywne/aktywne dla funkcji HTTPS

Aby osiągnąć aktywne/aktywne wdrożenia funkcji, wymaga pewnego składnika, który może koordynować zdarzenia między obydwoma regionami.  W przypadku funkcji HTTPS Ta koordynacja jest realizowana przy użyciu [zewnętrznych drzwi platformy Azure](../frontdoor/front-door-overview.md).  Drzwi frontonu platformy Azure umożliwiają kierowanie żądań HTTPS i działanie okrężne między wieloma funkcjami regionalnymi.  Okresowo sprawdza również kondycję każdego punktu końcowego.  Jeśli funkcja regionalna przestanie odpowiadać na kontrolę kondycji, drzwi frontonu platformy Azure przestają ją na zewnątrz i przekazują ruch do funkcji w dobrej kondycji.  

![Architektura dla drzwi i funkcji platformy Azure](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>Aktywne/aktywne dla funkcji innych niż HTTPS

Nadal można uzyskać aktywne/aktywne wdrożenia dla funkcji innych niż HTTPS.  Należy jednak wziąć pod uwagę, w jaki sposób dwa regiony będą współdziałać ze sobą.  Jeśli ta sama aplikacja funkcji została wdrożona w dwóch regionach, każde wyzwalane w tej samej kolejce Service Bus będzie pełnić rolę konkurujących odbiorców w odniesieniu do tej kolejki.  Chociaż oznacza to, że każdy komunikat jest przetwarzany tylko przez jedno z wystąpień, oznacza to, że nadal istnieje single point of failure na pojedynczym Service Bus.  W przypadku wdrożenia dwóch kolejek Service Bus (jeden w regionie podstawowym, jeden w regionie pomocniczym), a dwie aplikacje funkcji wskazywane przez ich kolejkę regionów, wyzwanie teraz zawiera sposób dystrybuowania komunikatów w kolejce między tymi dwoma regionami.  Często oznacza to, że każdy wydawca próbuje opublikować komunikat w *obu* regionach, a każdy komunikat jest przetwarzany przez obie aktywne aplikacje funkcji.  Podczas tworzenia wzorca aktywnego/aktywnego powstaje inne wyzwania dotyczące duplikowania obliczeń oraz czasu, w którym dane są konsolidowane.  Z tego powodu zaleca się, aby wyzwalacze inne niż HTTPS korzystały ze wzorca aktywnego/pasywnego.

## <a name="activepassive-for-non-https-functions"></a>Aktywne/pasywne dla funkcji innych niż HTTPS

Usługa Active/pasywna umożliwia tylko pojedynczej funkcji przetworzenie poszczególnych komunikatów, ale zapewnia mechanizm przełączenia w tryb failover do regionu pomocniczego w przypadku awarii.  Azure Functions działa wraz z [odzyskiwaniem geograficznym Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md) i odzyskiwaniem [geograficznym Event Hubs platformy Azure](../event-hubs/event-hubs-geo-dr.md).

Użycie wyzwalaczy usługi Azure Event Hubs jako przykładu wzorzec aktywny/pasywny obejmuje następujące elementy:

* Usługa Azure Event Hub została wdrożona w regionie podstawowym i pomocniczym.
* Geograficzna z włączonym awarią parowania podstawowego i pomocniczego centrum zdarzeń.  Powoduje to również utworzenie "aliasu", którego można użyć do nawiązania połączenia z centrami zdarzeń i przełączenia z podstawowego do pomocniczego bez zmiany informacji o połączeniu.
* Aplikacje funkcji wdrożone w regionie podstawowym i pomocniczym.
* Aplikacje funkcji są wyzwalane przez *bezpośrednie* (nie alias) parametry połączenia dla odpowiedniego centrum zdarzeń. 
* Wydawcy do centrum zdarzeń powinni publikować w parametrach połączenia aliasu. 

![Przykładowa architektura aktywna-pasywna](media/functions-geo-dr/active-passive.png)

Przed przejściem w tryb failover wydawcy wysyłający do aliasu udostępnionego będą kierować do głównego centrum zdarzeń.  Podstawowa aplikacja funkcji nasłuchuje wyłącznie na podstawowym centrum zdarzeń.  Aplikacja funkcji pomocniczej będzie pasywna i bezczynna.  Po zainicjowaniu trybu failover wydawcy wysyłający do udostępnionego aliasu będą teraz kierowani do pomocniczego centrum zdarzeń.  Aplikacja funkcji pomocniczej stanie się teraz aktywna i rozpocznie automatyczne wyzwalanie.  Efektywne przejście w tryb failover do regionu pomocniczego może być prowadzone całkowicie z centrum zdarzeń, a funkcje stają się aktywne tylko wtedy, gdy odpowiednie centrum zdarzeń jest aktywne.

Zapoznaj się z informacjami i zagadnieniami dotyczącymi trybu failover w przypadku [Service Bus](../service-bus-messaging/service-bus-geo-dr.md) i [centrów zdarzeń](../event-hubs/event-hubs-geo-dr.md).

## <a name="next-steps"></a>Następne kroki

* [Utwórz drzwi frontonu platformy Azure](../frontdoor/quickstart-create-front-door.md)
* [Zagadnienia dotyczące Event Hubs trybu failover](../event-hubs/event-hubs-geo-dr.md#considerations)
