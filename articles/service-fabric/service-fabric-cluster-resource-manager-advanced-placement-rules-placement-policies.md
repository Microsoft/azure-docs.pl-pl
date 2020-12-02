---
title: Service Fabric Menedżer zasobów klastra — zasady umieszczania
description: Omówienie dodatkowych zasad umieszczania i reguł dotyczących usług Service Fabric Services
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 5a4586c9c1be51b0ebbdebcf0c23289fc39f9eda
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485505"
---
# <a name="placement-policies-for-service-fabric-services"></a>Zasady umieszczania dla usług Service Fabric
Zasady umieszczania są dodatkowymi regułami, które mogą służyć do zarządzania umieszczaniem usługi w określonych, mniej typowych scenariuszach. Oto kilka przykładów tych scenariuszy:

- Klaster Service Fabric obejmuje odległości geograficzne, takie jak wiele lokalnych centrów danych lub w regionach platformy Azure
- Środowisko obejmuje wiele obszarów kontroli geopolitycznej lub prawnej, a także inne przypadki, w których należy wymusić granice zasad
- Występują problemy z wydajnością komunikacji lub opóźnieniami z powodu dużych odległości lub korzystania z wolniejszych lub mniej wiarygodnych linków sieciowych
- Należy zachować pewne obciążenia rozmieszczone jako najlepsze nakłady pracy, z innymi obciążeniami lub w sąsiedztwie z klientami
- Wymagana jest wiele wystąpień bezstanowych partycji na jednym węźle

Większość z tych wymagań jest wyrównana do fizycznego układu klastra reprezentowanego jako domeny błędów klastra. 

Zaawansowane zasady umieszczania, które pomagają rozwiązać te scenariusze, są następujące:

1. Nieprawidłowe domeny
2. Wymagane domeny
3. Preferowane domeny
4. Niezezwalanie na pakowanie repliki
5. Zezwalaj na wiele wystąpień bezstanowych w węźle

Większość następujących kontrolek można skonfigurować za pomocą właściwości węzła i ograniczeń umieszczania, ale niektóre są bardziej skomplikowane. Aby uprościć, klaster Service Fabric Menedżer zasobów udostępnia te dodatkowe zasady umieszczania. Zasady umieszczania są konfigurowane na podstawie nazwanego wystąpienia usługi. Można je również aktualizować dynamicznie.

## <a name="specifying-invalid-domains"></a>Określanie nieprawidłowych domen
Zasady umieszczania **InvalidDomain** umożliwiają określenie, że określona domena błędów jest nieprawidłowa dla określonej usługi. Te zasady zapewniają, że określona usługa nigdy nie działa w określonym obszarze, na przykład na potrzeby geopolityczne lub z zasad firmowych. Za pomocą oddzielnych zasad można określić wiele nieprawidłowych domen.

<center>

![Nieprawidłowy przykład domeny][Image1]
</center>

Kod:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Program PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Określanie wymaganych domen
Wymagane zasady umieszczania domeny wymagają, aby usługa była obecna tylko w określonej domenie. Wiele wymaganych domen można określić za pomocą oddzielnych zasad.

<center>

![Przykład wymaganej domeny][Image2]
</center>

Kod:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

Program PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Określanie preferowanej domeny dla replik podstawowych usługi stanowej
Preferowana domena podstawowa określa domenę błędów, w której ma zostać umieszczony podstawowy. Podstawowa konfiguracja zostanie zakończona w tej domenie, gdy wszystko jest w dobrej kondycji. Jeśli domena lub replika podstawowa ulegnie awarii lub nie zostanie ZAMKNIĘTA, podstawowy przejdzie do innej lokalizacji, najlepiej w tej samej domenie. Jeśli ta nowa lokalizacja nie znajduje się w preferowanej domenie, klaster Menedżer zasobów przenieść ją z powrotem do preferowanej domeny najszybciej, jak to możliwe. Naturalnie to ustawienie ma sens tylko w przypadku usług stanowych. Te zasady są najbardziej przydatne w klastrach, które są łączone w regiony platformy Azure lub w wielu centrach danych, ale zawierają usługi, które preferują umieszczanie w określonej lokalizacji. Utrzymywanie Primaries blisko swoich użytkowników lub innych usług pomaga zapewnić mniejsze opóźnienia, szczególnie w przypadku operacji odczytu, które są domyślnie obsługiwane przez program Primaries.

<center>

![Preferowane domeny podstawowe i tryb failover][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(primaryDomain);
```

Program PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Wymaganie dystrybucji replik i niezezwalanie na pakowanie
Repliki są _zwykle_ dystrybuowane w domenach błędów i uaktualnień, gdy klaster jest w dobrej kondycji. Istnieją jednak przypadki, w których więcej niż jedna replika dla danej partycji może tymczasowo kończyć się w jednej domenie. Załóżmy na przykład, że klaster ma dziewięć węzłów w trzech domenach błędów, FD:/0, FD:/1 i FD:/2. Załóżmy również, że usługa ma trzy repliki. Załóżmy, że węzły, które były używane dla tych replik w FD:/1 i FD:/2, zostały wyłączone. Zwykle Menedżer zasobów klastra woli inne węzły w tych samych domenach błędów. W takim przypadku Załóżmy, że z powodu problemów z pojemnością żaden z pozostałych węzłów w tych domenach nie był prawidłowy. Jeśli klaster Menedżer zasobów kompilacje zamienników dla tych replik, konieczne będzie wybranie węzłów w FD:/0. Jednak spowoduje to utworzenie sytuacji _,_ w której nastąpiło naruszenie ograniczenia domeny błędów. Repliki pakowania zwiększają prawdopodobieństwo wyłączenia lub utraty całego zestawu replik. 

> [!NOTE]
> Aby uzyskać więcej informacji na temat ograniczeń i priorytetów ograniczeń na ogół, zapoznaj się z [tym tematem](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Jeśli kiedykolwiek zobaczysz komunikat o kondycji, taki jak " `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain` ", osiągnięto ten warunek lub coś innego. Zwykle tylko jedna lub dwie repliki są pakowane tymczasowo. Tak długo, jak jest mniej niż kworum replik w danej domenie, jesteś bezpieczny. Pakowanie jest rzadkie, ale może się tak zdarzyć, a zazwyczaj sytuacje są przejściowe, ponieważ węzły wracają do tyłu. Jeśli węzły nie działają, a klaster Menedżer zasobów musi tworzyć zamiany, zazwyczaj istnieją inne węzły dostępne w domenach idealnego błędu.

Niektóre obciążenia wolą zawsze mieć docelową liczbę replik, nawet jeśli są zapakowane do mniejszej liczby domen. Te obciążenia są skierowane do całkowitej liczby równoczesnych niepowodzeń domeny i zazwyczaj mogą odzyskać stan lokalny. Inne obciążenia spowodują skrócenie czasu przestoju niż poprawność ryzyka lub utratę danych. Większość obciążeń produkcyjnych działa z więcej niż trzema replikami, więcej niż trzema domenami błędów i wieloma prawidłowymi węzłami na domenę błędów. W związku z tym domyślne zachowanie domyślnie zezwala na pakowanie domeny. Domyślne zachowanie umożliwia normalne Równoważenie i przełączenie w tryb failover w celu obsługi tych skrajnych przypadków, nawet jeśli oznacza to tymczasowe pakowanie domen.

Jeśli chcesz wyłączyć takie pakowanie dla danego obciążenia, możesz określić `RequireDomainDistribution` zasady usługi. Po ustawieniu tych zasad klaster Menedżer zasobów nie gwarantuje, że żadne repliki z tej samej partycji są uruchamiane w tej samej domenie błędów lub uaktualnienia.

Kod:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Program PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Czy można teraz używać tych konfiguracji dla usług w klastrze, który nie jest geograficznie łączony? Możesz, ale nie istnieje zbyt dobry powód. Należy unikać wymaganych, nieprawidłowych i preferowanych konfiguracji domeny, chyba że są one wymagane. Nie ma żadnego sensu, aby wymusić wymuszenie uruchomienia danego obciążenia w jednym stojaku lub preferowanie pewnego segmentu lokalnego klastra. Różne konfiguracje sprzętu powinny być rozłożone między domenami błędów i obsługiwane przez normalne ograniczenia umieszczania i właściwości węzła.

## <a name="placement-of-multiple-stateless-instances-of-a-partition-on-single-node"></a>Umieszczanie wielu bezstanowych wystąpień partycji na pojedynczym węźle
Zasady umieszczania **AllowMultipleStatelessInstancesOnNode** umożliwiają rozmieszczenie wielu wystąpień bezstanowych partycji w jednym węźle. Domyślnie wiele wystąpień pojedynczej partycji nie może zostać umieszczonych w węźle. Nawet w przypadku usługi-1 nie jest możliwe skalowanie liczby wystąpień poza liczbą węzłów w klastrze dla danej nazwanej usługi. Te zasady umieszczania usuwają to ograniczenie i umożliwiają określenie InstanceCount wyższych niż liczba węzłów.

Jeśli kiedykolwiek zobaczysz komunikat o kondycji, taki jak " `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: ReplicaExclusion` ", osiągnięto ten warunek lub coś innego. 

Określając `AllowMultipleStatelessInstancesOnNode` zasady w usłudze, InstanceCount można ustawić poza liczbą węzłów w klastrze.

Kod:

```csharp
ServicePlacementAllowMultipleStatelessInstancesOnNodePolicyDescription allowMultipleInstances = new ServicePlacementAllowMultipleStatelessInstancesOnNodePolicyDescription();
serviceDescription.PlacementPolicies.Add(allowMultipleInstances);
```

Program PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless –PartitionSchemeSingleton –PlacementPolicy @(“AllowMultipleStatelessInstancesOnNode”) -InstanceCount 10 -ServicePackageActivationMode ExclusiveProcess 
```

> [!NOTE]
> Zasady umieszczania są obecnie dostępne w wersji zapoznawczej i za `EnableUnsupportedPreviewFeatures` ustawieniem klastra. Ponieważ jest to funkcja w wersji zapoznawczej, ustawienie opcji konfiguracja wersji zapoznawczej uniemożliwia pobranie klastra do/z programu. Innymi słowy, aby wypróbować tę funkcję, należy utworzyć nowy klaster.
>

> [!NOTE]
> Obecnie zasady są obsługiwane tylko w przypadku usług bezstanowych z [trybem aktywacji pakietu usługi](/dotnet/api/system.fabric.description.servicepackageactivationmode?view=azure-dotnet)ExclusiveProcess.
>

> [!WARNING]
> Zasady nie są obsługiwane w przypadku używania z statycznymi punktami końcowymi portów. Użycie obydwu w połączeniu może prowadzić do klastra w złej kondycji, ponieważ wiele wystąpień w tym samym węźle próbuje powiązać z tym samym portem i nie może się pojawić. 
>

> [!NOTE]
> Użycie wysokiej wartości [MinInstanceCount](/dotnet/api/system.fabric.description.statelessservicedescription.mininstancecount?view=azure-dotnet) z tymi zasadami umieszczania może prowadzić do zablokowanych uaktualnień aplikacji. Na przykład jeśli masz klaster z pięcioma węzłami i ustawisz InstanceCount = 10, na każdym węźle będą dostępne dwa wystąpienia. Jeśli ustawisz MinInstanceCount = 9, próba uaktualnienia aplikacji może zostać zablokowana; w przypadku MinInstanceCount = 8 można to uniknąć.
>

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat konfigurowania usług, [Dowiedz się więcej o konfigurowaniu usług](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png