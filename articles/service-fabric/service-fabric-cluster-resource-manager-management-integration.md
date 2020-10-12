---
title: Menedżer zasobów klastra — Integracja zarządzania
description: Przegląd punktów integracji między Menedżer zasobów klastra a zarządzaniem Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 50751c7d23797a597dc5e2d209c1e3eecf6f7a40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85847861"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integracja z usługą Resource Manager za pomocą Service Fabric zarządzania klastrami
Menedżer zasobów klastra Service Fabric nie ma na dysku uaktualnień w Service Fabric, ale jest to konieczne. Pierwszy sposób, w jaki klaster Menedżer zasobów pomaga zarządzać, to śledzenie żądanego stanu klastra i usług w nim. Klaster Menedżer zasobów wysyła raporty kondycji, gdy nie można umieścić klastra w żądanej konfiguracji. Na przykład jeśli nie ma wystarczającej pojemności, klaster Menedżer zasobów wysyła ostrzeżenia dotyczące kondycji i błędy wskazujące problem. Inna część integracji polega na tym, jak działają uaktualnienia. Klaster Menedżer zasobów nieco zmienia zachowanie podczas uaktualnień.  

## <a name="health-integration"></a>Integracja z kondycją
Klaster Menedżer zasobów ciągle śledzi reguły zdefiniowane do umieszczania usług. Śledzi również pozostałe pojemności dla każdej metryki w węzłach i w klastrze oraz w klastrze jako całość. Jeśli nie można spełnić tych zasad lub jeśli jest za mało miejsca, są emitowane ostrzeżenia i błędy kondycji. Na przykład, jeśli węzeł przekracza pojemność, a klaster Menedżer zasobów podejmie próbę rozwiązania problemu przez przeniesienie usług. Jeśli nie można poprawić sytuacji, która emituje ostrzeżenie o kondycji wskazujące, który węzeł przekracza pojemność i dla których metryk.

Innym przykładem ostrzeżeń dotyczących kondycji Menedżer zasobów są naruszenia ograniczeń umieszczania. Na przykład, jeśli zdefiniowano ograniczenie umieszczania (na przykład `“NodeColor == Blue”` ), a Menedżer zasobów wykryje naruszenie tego ograniczenia, emituje ostrzeżenie o kondycji. Dotyczy to ograniczeń niestandardowych i ograniczeń domyślnych (takich jak domena błędów i ograniczenia domeny uaktualnienia).

Oto przykład tego raportu kondycji. W takim przypadku Raport kondycji dotyczy jednej z partycji usługi systemowej. Komunikat o kondycji wskazuje, że repliki tej partycji są tymczasowo pakowane na zbyt mało domen uaktualnienia.

```posh
PS C:\Users\User > Get-ServiceFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Oto, co ten komunikat o kondycji informuje nas:

1. Wszystkie repliki są w dobrej kondycji: Każda z nich ma AggregatedHealthState: ok
2. Ograniczenie dystrybucji domeny uaktualnienia jest obecnie naruszane. Oznacza to, że określona domena uaktualnienia ma więcej replik z tej partycji niż powinna.
3. Który węzeł zawiera replikę powodującą naruszenie. W tym przypadku jest to węzeł o nazwie "Node. 8"
4. Czy uaktualnienie jest aktualnie wykonywane dla tej partycji ("aktualnie uaktualnia--false")
5. Zasady dystrybucji dla tej usługi: "zasady dystrybucji--pakowanie". Podlega to `RequireDomainDistribution` [zasadom umieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). "Pakowanie" wskazuje, że w tym przypadku DomainDistribution _nie_ jest wymagany, dlatego wiemy, że zasady umieszczania nie zostały określone dla tej usługi. 
6. Gdy raport wystąpił — 8/10/2015 7:13:02 PM

Informacje, takie jak ta, umożliwiają zgłaszanie alertów w środowisku produkcyjnym informujących o niepowodzeniu i służy również do wykrywania i zatrzymania nieprawidłowych uaktualnień. W tym przypadku chcemy sprawdzić, czy w przypadku Menedżer zasobów należało spakować repliki do domeny uaktualnienia. Zwykle pakowanie jest przejściowe, ponieważ węzły w innych domenach uaktualnienia zostały wyłączone, na przykład.

Załóżmy, że klaster Menedżer zasobów próbuje umieścić niektóre usługi, ale nie ma żadnych rozwiązań, które działają. Gdy usługi nie mogą być umieszczone, zwykle z jednego z następujących powodów:

1. Pewien warunek przejściowy uniemożliwiał poprawne umieszczenie tego wystąpienia usługi lub repliki
2. Wymagania dotyczące umieszczania usługi są unsatisfiable.

W takich przypadkach raporty kondycji z klastra Menedżer zasobów pomóc w ustaleniu, dlaczego nie można umieścić usługi. Wywołujemy ten proces w sekwencji eliminacji ograniczenia. W tym systemie system przeprowadzi przez skonfigurowane ograniczenia wpływające na usługę i rejestruje, co eliminuje. W ten sposób, gdy usługi nie będą mogły zostać umieszczone, można zobaczyć, które węzły zostały wyeliminowane i dlaczego.

## <a name="constraint-types"></a>Typy ograniczeń
Porozmawiamy o każdym z różnych ograniczeń w tych raportach kondycji. Komunikaty o kondycji dotyczące tych ograniczeń są wyświetlane, gdy nie można umieścić replik.

* **ReplicaExclusionStatic** i **ReplicaExclusionDynamic**: te ograniczenia wskazują, że rozwiązanie zostało odrzucone, ponieważ dwa obiekty usługi z tej samej partycji byłyby umieszczane w tym samym węźle. Nie jest to dozwolone, ponieważ w przypadku awarii tego węzła nie wpłynie to na tę partycję. ReplicaExclusionStatic i ReplicaExclusionDynamic są prawie tą samą regułą i różnice nie są naprawdę istotne. Jeśli widzisz sekwencję eliminacji ograniczenia zawierającą ograniczenie ReplicaExclusionStatic lub ReplicaExclusionDynamic, klaster Menedżer zasobów uważa, że nie ma wystarczającej liczby węzłów. Wymaga to, aby pozostałe rozwiązania używały nieprawidłowych miejsc, które są niedozwolone. Pozostałe ograniczenia w sekwencji zwykle informują nas o tym, dlaczego węzły są eliminowane w pierwszym miejscu.
* **PlacementConstraint**: Jeśli zobaczysz ten komunikat, oznacza to, że niektóre węzły nie pasują do ograniczeń umieszczania usługi. Śledzimy aktualnie skonfigurowane ograniczenia umieszczania w ramach tej wiadomości. Jest to normalne, jeśli masz zdefiniowane ograniczenie umieszczania. Jeśli jednak ograniczenie umieszczania jest nieprawidłowo przyczyną wyeliminowania zbyt wielu węzłów, jest to w jaki sposób.
* **NodeCapacity**: to ograniczenie oznacza, że klaster Menedżer zasobów nie mógł umieścić replik w wskazanych węzłach, ponieważ spowodowałoby to przekroczenie pojemności.
* **Koligacja**: to ograniczenie wskazuje, że nie można umieścić repliki w węzłach, których to dotyczy, ponieważ spowodowałoby to naruszenie ograniczenia koligacji. Więcej informacji na temat koligacji znajduje się w [tym artykule](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** i **UpgradeDomain**: to ograniczenie eliminuje węzły, jeśli umieszczenie repliki we wskazanych węzłach spowoduje, że pakowanie w konkretnym błędzie lub domenie uaktualnienia. Kilka przykładów omawiających to ograniczenie przedstawiono w temacie dotyczącym [ograniczeń dotyczących błędów i uaktualniania domeny oraz zachowanie wyników](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: nie powinno się normalnie wyświetlać tego ograniczenia, usuwając węzły z rozwiązania, ponieważ jest ono domyślnie uruchamiane jako Optymalizacja. W trakcie uaktualniania występuje również preferowane ograniczenie lokalizacji. Podczas uaktualniania służy do przenoszenia usług z powrotem do miejsca, w którym zostały uruchomione.

## <a name="blocklisting-nodes"></a>Blocklisting węzły
Inny komunikat o kondycji, Menedżer zasobów raporty klastra, gdy węzły są blocklisted. Możesz traktować blocklisting jako ograniczenie tymczasowe, które jest automatycznie stosowane. Węzły uzyskują blocklisted, gdy wystąpią powtarzające się błędy podczas uruchamiania wystąpień tego typu usługi. Węzły są blocklisted dla poszczególnych typów usług. Węzeł może być blocklisted dla jednego typu usługi, ale nie do innego. 

Zobaczysz blocklisting często w trakcie programowania: Niektóre usterki spowodują awarię hosta usługi podczas uruchamiania. Service Fabric próbuje utworzyć hosta usługi kilka razy i nadal Wystąpił błąd. Po kilku próbach węzeł zostanie blocklisted, a klaster Menedżer zasobów spróbuje utworzyć usługę w innym miejscu. Jeśli ten błąd będzie się powtarzał na wielu węzłach, możliwe jest zablokowanie wszystkich prawidłowych węzłów w klastrze. Blocklisting może również usunąć tyle węzłów, które nie wystarczą do pomyślnej skali. Zwykle widzisz dodatkowe błędy lub ostrzeżenia z klastra Menedżer zasobów wskazujące, że usługa znajduje się poniżej żądanej repliki lub liczby wystąpień, a także komunikaty dotyczące kondycji wskazujące, co się stało z blocklisting w pierwszym miejscu.

Blocklisting nie jest trwałym warunkiem. Po kilku minutach węzeł zostanie usunięty z listy blokowania i Service Fabric może ponownie aktywować usługi w tym węźle. Jeśli usługi nie powiodą się, węzeł jest blocklisted dla tego typu usługi ponownie. 

### <a name="constraint-priorities"></a>Priorytety ograniczeń

> [!WARNING]
> Zmiana priorytetów ograniczeń nie jest zalecana i może mieć znaczący niekorzystny wpływ na klaster. Poniższe informacje są dostępne na potrzeby odwołania do domyślnych priorytetów ograniczeń i ich zachowania. 
>

Ze względu na wszystkie te ograniczenia możesz zastanawiać się "Hej, że ograniczenia domeny błędów są najważniejszym elementem w systemie. W celu zapewnienia, że ograniczenie domeny błędów nie zostanie naruszone, chcę naruszać inne ograniczenia. "

Ograniczenia można skonfigurować przy użyciu różnych poziomów priorytetów. Są to:

   - "twarda" (0)
   - "miękkie" (1)
   - "optymalizacja" (2)
   - "off" (-1). 
   
Większość ograniczeń jest domyślnie skonfigurowanych jako ograniczenia stałe.

Zmiana priorytetu ograniczeń jest nietypowe. Istnieją przypadki, w których nie trzeba zmieniać priorytetów ograniczeń, zwykle obejść inne usterki lub zachowania, które miały wpływ na środowisko. Ogólnie elastyczność infrastruktury priorytetu ograniczenia działała bardzo dobrze, ale nie jest to często konieczne. Większość czasu jest zależała od ich domyślnych priorytetów. 

Poziom priorytetów nie oznacza, że określone ograniczenie _zostanie_ naruszone, ani że zawsze zostanie spełnione. Priorytety ograniczeń definiują kolejność, w której są wymuszane ograniczenia. Priorytety definiują kompromisy, gdy nie można spełnić wszystkich ograniczeń. Zwykle wszystkie ograniczenia mogą być spełnione, chyba że istnieje inna sytuacja w środowisku. Niektóre przykłady scenariuszy, które będą prowadzić do naruszeń ograniczeń, powodują konflikt lub wiele współbieżnych awarii.

W sytuacjach zaawansowanych można zmienić priorytety ograniczeń. Załóżmy na przykład, że chcesz upewnić się, że koligacja jest zawsze naruszona, gdy jest to konieczne do rozwiązywania problemów z pojemnością węzła. Aby to osiągnąć, można ustawić priorytet ograniczenia koligacji na "miękki" (1) i pozostawić ograniczenie pojemności ustawione na "twarda" (0).

Domyślne wartości priorytetów dla różnych ograniczeń są określone w następującej konfiguracji:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

za pomocą ClusterConfig.jsna potrzeby wdrożeń autonomicznych lub Template.jsna potrzeby klastrów hostowanych na platformie Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Ograniczenia domeny błędów i uaktualniania domeny
Klaster Menedżer zasobów chce zachować rozłożenie usług między domenami błędów i uaktualniania. Modeluje to ograniczenie w ramach aparatu Menedżer zasobów klastra. Aby uzyskać więcej informacji na temat sposobu użycia i ich określonego zachowania, zapoznaj się z artykułem dotyczącym [konfiguracji klastra](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

Aby można było zaradzić sobie z uaktualnieniami, błędami lub innymi naruszeniami ograniczeń, Menedżer zasobów klastra musi być konieczne spakowanie różnych replik w domenie uaktualnienia. Pakowanie do domen błędów lub uaktualnień zwykle odbywa się tylko wtedy, gdy w systemie występuje kilka błędów lub innych zmian uniemożliwiających poprawne umieszczanie. Jeśli chcesz zapobiec pakowaniu nawet w tych sytuacjach, możesz użyć `RequireDomainDistribution` [zasad umieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Należy zauważyć, że może to mieć wpływ na dostępność usługi i niezawodność jako efekt uboczny, dlatego należy rozważyć uważnie.

Jeśli środowisko jest prawidłowo skonfigurowane, wszystkie ograniczenia są w pełni przestrzegane, nawet podczas uaktualniania. Kluczową kwestią jest to, że klaster Menedżer zasobów się w celu wyszukania ograniczeń. Gdy program wykryje naruszenie, natychmiast zgłosi je i podejmie próbę rozwiązania problemu.

## <a name="the-preferred-location-constraint"></a>Ograniczenie preferowanej lokalizacji
Ograniczenie PreferredLocation jest nieco inne, ponieważ ma dwa różne zastosowania. Jednym z zastosowań tego ograniczenia jest podczas uaktualniania aplikacji. Klaster Menedżer zasobów automatycznie zarządza tym ograniczeniem podczas uaktualnień. Jest on używany do upewnienia się, że po zakończeniu uaktualnień, które zostaną zwrócone do lokalizacji początkowych. Inne użycie ograniczenia PreferredLocation dotyczy [ `PreferredPrimaryDomain` zasad umieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Obie te funkcje są optymalizacjami, dlatego ograniczenie PreferredLocation jest jedynym ograniczeniem ustawionym na "optymalizacja" domyślnie.

## <a name="upgrades"></a>Uaktualnienia
Menedżer zasobów klastra pomaga również w procesie uaktualniania aplikacji i klastra, podczas którego ma dwa zadania:

* Upewnij się, że reguły klastra nie zostały naruszone.
* Staraj się, aby zapewnić bezproblemową pracę z uaktualnieniem

### <a name="keep-enforcing-the-rules"></a>Kontynuuj Wymuszanie reguł
Głównym aspektem, który należy wiedzieć, jest to, że reguły — ścisłe ograniczenia, takie jak ograniczenia umieszczenia i pojemności — są nadal wymuszane podczas uaktualniania. Ograniczenia umieszczania zapewniają, że obciążenia są uruchamiane tylko wtedy, gdy są dozwolone, nawet podczas uaktualnień. Gdy usługi są wysoce ograniczone, uaktualnienia mogą trwać dłużej. Gdy usługa lub węzeł, w którym jest uruchomiona, zostanie przesunięty w celu przełączenia aktualizacji, może być kilka opcji, dla których może to zrobić.

### <a name="smart-replacements"></a>Inteligentne zamienniki
Po rozpoczęciu uaktualnienia Menedżer zasobów wykonuje migawkę bieżącego rozmieszczenia klastra. Gdy każda domena uaktualnienia zostanie ukończona, próbuje zwrócić usługi, które znajdowały się w tej domenie uaktualnienia do ich oryginalnego rozmieszczenia. W ten sposób co najwyżej dwa przejścia dla usługi podczas uaktualniania. Istnieje jedno przejście z węzła, którego to dotyczy, i przeniesienie go do tyłu. Przywrócenie klastra lub usługi do tego, jak była przed uaktualnieniem, również gwarantuje, że uaktualnienie nie ma wpływu na układ klastra. 

### <a name="reduced-churn"></a>Zmniejszone zmiany
Innym skutkiem podczas uaktualniania jest to, że klaster Menedżer zasobów wyłącza równoważenie. Uniemożliwianie równoważenia obciążenia zapobiega niepotrzebnej reakcji na uaktualnienie, np. przeniesienie usług do węzłów, które zostały opróżnione w celu uaktualnienia. Jeśli uaktualnienie dotyczy uaktualnienia klastra, cały klaster nie jest zrównoważony podczas uaktualniania. Sprawdzanie ograniczeń pozostaje aktywne, tylko przeniesienie oparte na aktywnym równoważeniu metryk jest wyłączone.

### <a name="buffered-capacity--upgrade"></a>Przebuforowana pojemność & uaktualnienie
Na ogół chcesz przeprowadzić uaktualnienie nawet wtedy, gdy klaster jest ograniczony lub zamknięty. Zarządzanie pojemnością klastra jest jeszcze ważniejsze w przypadku uaktualnień niż zwykle. W zależności od liczby domen uaktualnienia należy przeprowadzić migrację między 5 a 20 procent pojemności w miarę uaktualniania w ramach klastra. Ta współpraca musi znajdować się w dowolnym miejscu. Jest to miejsce, w którym koncepcje [buforowanych pojemności](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) są użyteczne. Pojemność buforowana jest przestrzegana podczas normalnego działania. Klaster Menedżer zasobów może wypełnić węzły do całkowitej pojemności (zużywanej przez bufor) w razie potrzeby.

## <a name="next-steps"></a>Następne kroki
* Zacznij od początku i [zapoznaj się z wprowadzeniem do klastra Service Fabric Menedżer zasobów](service-fabric-cluster-resource-manager-introduction.md)
