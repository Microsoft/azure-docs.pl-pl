---
title: Service Fabric Menedżer zasobów klastra — koligacja
description: Omówienie koligacji usługi dla usług Azure Service Fabric i wskazówki dotyczące konfiguracji koligacji usługi.
services: service-fabric
documentationcenter: .net
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c141cd96877fd140b858d0aaed9197f2de80eca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89005738"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Konfigurowanie i używanie koligacji usługi w Service Fabric
Koligacja jest formantem, który jest przeznaczony głównie do ułatwienia przejścia większej liczby aplikacji monolitycznych do chmury i mikrousług na świecie. Jest on również używany jako Optymalizacja do poprawy wydajności usług, chociaż może to mieć wpływ na skutki uboczne.

Załóżmy, że nastąpi przełączenie większej lub jednej aplikacji, która nie została zaprojektowana z myślą o mikrousługach, w celu Service Fabric (lub dowolnego środowiska rozproszonego). Ten typ przejścia jest typowy. Zacznij od przetworzenia całej aplikacji do środowiska, pakowania go i upewnienia się, że działa bezproblemowo. Następnie należy rozpocząć podzielenie go na różne mniejsze usługi, które komunikują się ze sobą.

Ostatecznie może się okazać, że w aplikacji występują pewne problemy. Te problemy zwykle należą do jednej z następujących kategorii:

1. Część składnika X w aplikacji monolitycznej miała nieudokumentowaną zależność w składniku Y, a te składniki zostały po prostu wyłączone w osobnych usługach. Ponieważ te usługi działają teraz w różnych węzłach w klastrze, są one przerwane.
2. Te składniki komunikują się za pośrednictwem (lokalnych potoków nazwanych | udostępnionych pamięci | plików na dysku) i naprawdę muszą mieć możliwość zapisu do udostępnionego zasobu lokalnego ze względów wydajnościowych. Ta twarda zależność zostanie usunięta później, być może.
3. Wszystkie te elementy są odpowiednie, ale wystąpiły, że te dwa składniki są faktycznie nachodzące i podatne na wydajność. Po przeniesieniu ich do odrębnych usług ogólna wydajność aplikacji lub zwiększenie opóźnień. W związku z tym ogólna aplikacja nie spełnia oczekiwań.

W takich przypadkach nie chcemy tracić działania refaktoryzacji i nie chcesz wrócić do monolitu. Ostatni warunek może nawet być pożądany jako zwykła Optymalizacja. Jednak do momentu, w którym można ponownie zaprojektować składniki, aby działały w sposób naturalny jako usługi (lub do momentu, w którym można rozwiązać problemy z wydajnością w inny sposób), będziemy potrzebować pewnego sensu dotyczącego lokalizacji.

Postępowanie Możesz również spróbować włączyć koligację.

## <a name="how-to-configure-affinity"></a>Jak skonfigurować koligację
Aby skonfigurować koligację, należy zdefiniować relację koligacji między dwiema różnymi usługami. Koligację można traktować jako "wskazujące" jedną usługę na drugim i mówiąc "Ta usługa może działać tylko wtedy, gdy ta usługa jest uruchomiona". Czasami odwołujemy się do koligacji jako relacji nadrzędnej/podrzędnej (gdzie wskazuje element podrzędny w obiekcie nadrzędnym). Koligacja zapewnia, że repliki lub wystąpienia jednej usługi są umieszczane w tych samych węzłach, co w przypadku innej usługi.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Usługa podrzędna może uczestniczyć tylko w jednej relacji koligacji. Jeśli chcesz, aby dziecko miało koligację z dwoma usługami nadrzędnymi, gdy masz kilka opcji:
> - Odwróć relacje (parentService1 i parentService2 punkt w bieżącej usłudze podrzędnej) lub
> - Wyznacz jeden z elementów nadrzędnych jako centrum w ramach Konwencji i zajrzyj do niego wszystkie usługi. 
>
> Zachowanie podczas umieszczania w klastrze powinno być takie samo.
>

## <a name="different-affinity-options"></a>Różne opcje koligacji
Koligacja jest reprezentowana przez jeden z kilku schematów korelacji i ma dwa różne tryby. Najbardziej typowym trybem koligacji jest nazywana NonAlignedAffinity. W NonAlignedAffinity repliki lub wystąpienia różnych usług są umieszczane w tych samych węzłach. Inny tryb to AlignedAffinity. Wyrównanie koligacji jest przydatne tylko w przypadku usług stanowych. Skonfigurowanie dwóch usług stanowych w celu wyrównania koligacji zapewnia, że Primaries tych usług są umieszczane w tych samych węzłach. Powoduje również, że poszczególne pary serwerów pomocniczych dla tych usług są umieszczane w tych samych węzłach. Istnieje również możliwość skonfigurowania NonAlignedAffinity dla usług stanowych. W przypadku NonAlignedAffinity różne repliki dwóch usług stanowych zostaną uruchomione w tych samych węzłach, ale ich Primaries może zakończyć się w różnych węzłach.

<center>

![Tryby koligacji i ich efekty][Image1]
</center>

### <a name="best-effort-desired-state"></a>Żądany stan najlepszego nakładu pracy
Relacja koligacji jest optymalna. Nie zapewnia tego samego gwarancje kolokacji lub niezawodności, która działa w tym samym procesie wykonywalnym. Usługi w relacji koligacji są zasadniczo różnymi jednostkami, które mogą kończyć się niepowodzeniem i można je przenosić niezależnie. Relacja koligacji może również zostać przerwana, chociaż te przerwy są tymczasowe. Na przykład ograniczenia pojemności mogą oznaczać, że tylko niektóre obiekty usługi w relacji koligacji mogą pasować do danego węzła. W takich przypadkach, mimo że istnieje relacja koligacji, nie można jej wymusić z powodu innych ograniczeń. Jeśli jest to możliwe, naruszenie zostanie automatycznie poprawione później.

### <a name="chains-vs-stars"></a>Łańcuchy a gwiazdki
Obecnie klaster Menedżer zasobów nie może modelować łańcuchów relacji koligacji. Oznacza to, że usługa będąca elementem podrzędnym w jednej relacji koligacji nie może być elementem nadrzędnym w innej relacji koligacji. Jeśli chcesz modelować ten typ relacji, możesz efektywnie zamodelować go jako gwiazdkę zamiast łańcucha. Aby przenieść z łańcucha do gwiazdki, najniższego elementu podrzędnego będzie miało miejsce nadrzędne względem elementu nadrzędnego pierwszego elementu podrzędnego. W zależności od rozmieszczenia usług może być konieczne wykonanie tej czynności wiele razy. Jeśli nie istnieje naturalna usługa nadrzędna, może być konieczne utworzenie jednej, która służy jako symbol zastępczy. W zależności od wymagań warto również zapoznać się z [grupami aplikacji](service-fabric-cluster-resource-manager-application-groups.md).

<center>

![Łańcuchy a gwiazdki w kontekście relacji koligacji][Image2]
</center>

Kolejną kwestią, aby zauważyć, że relacje koligacji już dzisiaj są domyślne. Oznacza to, że reguła koligacji wymusza tylko, że element podrzędny jest umieszczony z elementem nadrzędnym. Nie gwarantuje to, że element nadrzędny znajduje się w elemencie podrzędnym. W związku z tym w przypadku naruszenia koligacji i skorygowania naruszenia z jakiegoś powodu nie jest możliwe przeniesienie elementu podrzędnego do węzła nadrzędnego, a następnie — nawet jeśli przeniesienie elementu nadrzędnego do węzła podrzędnego spowodowałoby skorygowanie naruszenia — obiekt nadrzędny nie zostanie przeniesiony do węzła podrzędnego. Ustawienie konfiguracji [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) na wartość true spowoduje usunięcie kierunkowości. Należy również pamiętać, że relacja koligacji nie może być idealnym lub natychmiastowo wymuszana, ponieważ różne usługi mają różne cykle życia i mogą się niezależnie przenosić. Załóżmy na przykład, że element nadrzędny został nagle przejdzie w tryb failover do innego węzła, ponieważ uległ awarii. Klaster Menedżer zasobów i Menedżer trybu failover najpierw obsłużyć przejście do trybu failover, ponieważ zapewnia priorytet usług, spójnych i dostępnych. Po zakończeniu pracy w trybie failover relacja koligacji zostanie przerwana, ale klaster Menedżer zasobów zauważa, że wszystko jest prawidłowo do momentu zauważenia, że element podrzędny nie znajduje się w elemencie nadrzędnym. Te rodzaje kontroli są wykonywane okresowo. Więcej informacji na temat sposobu, w jaki klaster Menedżer zasobów ocenia ograniczenia jest dostępny w [tym artykule](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), a [tym](service-fabric-cluster-resource-manager-balancing.md) , jak skonfigurować erze, na którym są oceniane te ograniczenia.   


### <a name="partitioning-support"></a>Obsługa partycjonowania
Ostatnim krokiem, aby zauważyć o koligacji, jest to, że relacje koligacji nie są obsługiwane w przypadku partycjonowania elementu nadrzędnego. Podzielone na partycje usługi nadrzędne mogą być obsługiwane ostatecznie, ale dzisiaj nie jest to dozwolone.

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat konfigurowania usług, [Dowiedz się więcej o konfigurowaniu usług](service-fabric-cluster-resource-manager-configure-services.md)
- Aby ograniczyć usługi do małego zestawu maszyn lub agregowania obciążeń usług, należy użyć [grup aplikacji](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png