---
title: Platforma Azure Service Fabric Skalowanie automatyczne usług i kontenerów
description: Usługa Azure Service Fabric umożliwia ustawianie zasad skalowania automatycznego dla usług i kontenerów.
author: radicmilos
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: miradic
ms.custom: devx-track-csharp
ms.openlocfilehash: 060bb9dcdd504846c76ab4c782b2857fdddfa394
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91354806"
---
# <a name="introduction-to-auto-scaling"></a>Wprowadzenie do automatycznego skalowania
Skalowanie automatyczne to dodatkowa funkcja Service Fabric do dynamicznego skalowania usług na podstawie obciążenia, które usługi są raportowane lub na podstawie ich użycia. Skalowanie automatyczne zapewnia doskonałą elastyczność i umożliwia obsługę dodatkowych wystąpień lub partycji usługi na żądanie. Cały proces skalowania automatycznego jest zautomatyzowany i przejrzysty, a po skonfigurowaniu zasad w ramach usługi nie ma potrzeby ręcznego skalowania operacji na poziomie usługi. Skalowanie automatyczne może być włączane podczas tworzenia usługi lub w dowolnym momencie przez aktualizację usługi.

Typowy scenariusz, w którym funkcja automatycznego skalowania jest użyteczna, polega na tym, że obciążenie określonej usługi różni się w miarę upływu czasu. Na przykład usługa, taka jak Brama, może skalować w zależności od ilości zasobów potrzebnych do obsługi żądań przychodzących. Przyjrzyjmy się przykładowi, w jaki sposób te reguły skalowania mogą wyglądać następująco:
* Jeśli wszystkie wystąpienia mojej bramy używają średnio więcej niż dwóch rdzeni, należy skalować usługę bramy przez dodanie jednego wystąpienia. Zrób to co godzinę, ale w sumie nie ma więcej niż siedem wystąpień.
* Jeśli wszystkie wystąpienia mojej bramy używają średnio mniej niż 0,5 rdzeni, wówczas skalowanie usługi w programie przez usunięcie jednego wystąpienia. Zrób to co godzinę, ale w sumie nie ma więcej niż trzy wystąpienia.

Automatyczne skalowanie jest obsługiwane zarówno w przypadku kontenerów, jak i zwykłych Service Fabric usług. Aby można było używać skalowania automatycznego, należy uruchomić program w wersji 6,2 lub nowszej środowiska uruchomieniowego Service Fabric. 

W pozostałej części tego artykułu opisano zasady skalowania, sposoby włączania lub wyłączania skalowania automatycznego oraz przedstawiono przykłady użycia tej funkcji.

## <a name="describing-auto-scaling"></a>Opisywanie automatycznego skalowania
Zasady skalowania automatycznego można definiować dla każdej usługi w klastrze Service Fabric. Każda zasada skalowania składa się z dwóch części:
* **Wyzwalacz skalowania** opisuje, kiedy zostanie wykonana skalowanie usługi. Warunki, które są zdefiniowane w wyzwalaczu są okresowo sprawdzane w celu określenia, czy usługa powinna być skalowana, czy nie.

* **Mechanizm skalowania** opisuje, jak skalowanie zostanie wykonane po jego wyzwoleniu. Mechanizm jest stosowany tylko wtedy, gdy spełnione są warunki wyzwalacza.

Wszystkie aktualnie obsługiwane wyzwalacze pracują z [metrykami obciążenia logicznego](service-fabric-cluster-resource-manager-metrics.md)lub z metrykami fizycznymi, takimi jak użycie procesora CPU lub pamięci. W obu przypadkach Service Fabric będzie monitorować zgłoszone obciążenie dla metryki i okresowo oblicza wyzwalacz, aby określić, czy jest konieczne skalowanie.

Istnieją dwa mechanizmy, które są obecnie obsługiwane do skalowania automatycznego. Pierwszy z nich jest przeznaczony dla usług bezstanowych lub kontenerów, w których automatyczne skalowanie jest wykonywane przez dodawanie lub usuwanie [wystąpień](service-fabric-concepts-replica-lifecycle.md). W przypadku usług stanowych i bezstanowych automatyczne skalowanie można wykonać, dodając lub usuwając nazwane [partycje](service-fabric-concepts-partitioning.md) usługi.

> [!NOTE]
> Obecnie jest obsługiwana tylko jedna zasada skalowania na usługę i tylko jeden wyzwalacz skalowania na zasady skalowania.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Średni wyzwalacz ładowania partycji z skalowaniem opartym na wystąpieniach
Pierwszy typ wyzwalacza jest oparty na ładowaniu wystąpień w bezstanowej partycji usługi. Obciążenia metryk są najpierw wygładzone w celu uzyskania obciążenia dla każdego wystąpienia partycji, a następnie te wartości są uśredniane we wszystkich wystąpieniach partycji. Istnieją trzy czynniki, które określają, kiedy usługa będzie skalowana:

* _Dolny próg obciążenia_ jest wartością, która określa, kiedy usługa będzie **skalowana w programie**. Jeśli średnie obciążenie wszystkich wystąpień partycji jest mniejsze niż ta wartość, usługa zostanie przeskalowana w poziomie.
* _Górny próg obciążenia_ jest wartością, która określa, kiedy usługa będzie **skalowana w poziomie**. Jeśli średnie obciążenie wszystkich wystąpień partycji jest wyższe niż ta wartość, usługa zostanie przeskalowana w poziomie.
* _Interwał skalowania_ określa, jak często wyzwalacz zostanie sprawdzony. Po sprawdzeniu wyzwalacza, jeśli jest wymagany, mechanizm zostanie zastosowany. Jeśli skalowanie nie jest wymagane, nie zostanie podjęta żadna akcja. W obu przypadkach wyzwalacz nie zostanie ponownie sprawdzony przed ponownym upływem interwału skalowania.

Tego wyzwalacza można używać tylko w przypadku usług bezstanowych (kontenerów bezstanowych lub usług Service Fabricch). W przypadku gdy usługa ma wiele partycji, wyzwalacz jest obliczany osobno dla każdej partycji, a każda partycja będzie miała określony mechanizm. W tym przypadku jest możliwe, że niektóre partycje usługi będą skalowane w poziomie, niektóre będą skalowane w, a niektóre nie będą skalowane w tym samym czasie, na podstawie ich obciążenia.

Jedynym mechanizmem, który może być używany z tym wyzwalaczem jest PartitionInstanceCountScaleMechanism. Istnieją trzy czynniki, które określają sposób stosowania tego mechanizmu:
* _Przyrost skali_ określa liczbę wystąpień, które zostaną dodane lub usunięte, gdy mechanizm zostanie wyzwolony.
* _Maksymalna liczba wystąpień_ definiuje górny limit skalowania. Jeśli liczba wystąpień partycji osiągnie ten limit, usługa nie będzie skalowana w poziomie, niezależnie od obciążenia. Można pominąć ten limit, określając wartość-1, a w takim przypadku usługa zostanie przeskalowana w poziomie możliwie największej liczby węzłów, które są dostępne w klastrze.
* _Minimalna liczba wystąpień_ definiuje dolny limit skalowania. Jeśli liczba wystąpień partycji osiągnie ten limit, usługa nie będzie skalowana w przypadku, gdy jest to możliwe.

## <a name="setting-auto-scaling-policy-for-instance-based-scaling"></a>Ustawianie zasad automatycznego skalowania dla skalowania opartego na wystąpieniach

### <a name="using-application-manifest"></a>Korzystanie z manifestu aplikacji
``` xml
<LoadMetrics>
<LoadMetric Name="MetricB" Weight="High"/>
</LoadMetrics>
<ServiceScalingPolicies>
<ScalingPolicy>
    <AveragePartitionLoadScalingTrigger MetricName="MetricB" LowerLoadThreshold="1" UpperLoadThreshold="2" ScaleIntervalInSeconds="100"/>
    <InstanceCountScalingMechanism MinInstanceCount="3" MaxInstanceCount="4" ScaleIncrement="1"/>
</ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Korzystanie z interfejsów API języka C#
```csharp
FabricClient fabricClient = new FabricClient();
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//set up the rest of the ServiceDescription
AveragePartitionLoadScalingTrigger trigger = new AveragePartitionLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new PartitionInstanceCountScaleMechanism();
mechanism.MaxInstanceCount = 3;
mechanism.MinInstanceCount = 1;
mechanism.ScaleIncrement = 1;
trigger.MetricName = "servicefabric:/_CpuCores";
trigger.ScaleInterval = TimeSpan.FromMinutes(20);
trigger.LowerLoadThreshold = 1.0;
trigger.UpperLoadThreshold = 2.0;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceDescription.ScalingPolicies.Add(policy);
//as we are using scaling on a resource this must be exclusive service
//also resource monitor service needs to be enabled
serviceDescription.ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```
### <a name="using-powershell"></a>Przy użyciu programu PowerShell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.PartitionInstanceCountScaleMechanism
$mechanism.MinInstanceCount = 1
$mechanism.MaxInstanceCount = 6
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AveragePartitionLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_CpuCores"
$trigger.LowerLoadThreshold = 0.3
$trigger.UpperLoadThreshold = 0.8
$trigger.ScaleInterval = New-TimeSpan -Minutes 10
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
Update-ServiceFabricService -Stateless -ServiceName "fabric:/AppName/ServiceName" -ScalingPolicies $scalingpolicies
```

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Średni wyzwalacz obciążenia usługi z skalowaniem opartym na partycji
Drugi wyzwalacz jest oparty na obciążeniu wszystkich partycji jednej usługi. Obciążenia metryk są najpierw wygładzone w celu uzyskania obciążenia dla każdej repliki lub wystąpienia partycji. W przypadku usług stanowych obciążenie partycji jest uznawane za załadowanie repliki podstawowej, natomiast w przypadku usług bezstanowych obciążenie partycji jest średnim obciążeniem wszystkich wystąpień partycji. Te wartości są uśredniane we wszystkich partycjach usługi, a ta wartość jest używana do wyzwalania skalowania automatycznego. Analogicznie jak w poprzednim mechanizmie, istnieją trzy czynniki, które określają, kiedy usługa będzie skalowana:

* _Dolny próg obciążenia_ jest wartością, która określa, kiedy usługa będzie **skalowana w programie**. Jeśli średnie obciążenie wszystkich partycji usługi jest mniejsze niż ta wartość, usługa zostanie przeskalowana w poziomie.
* _Górny próg obciążenia_ jest wartością, która określa, kiedy usługa będzie **skalowana w poziomie**. Jeśli średnie obciążenie wszystkich partycji usługi jest wyższe niż ta wartość, usługa zostanie przeskalowana w poziomie.
* _Interwał skalowania_ określa, jak często wyzwalacz zostanie sprawdzony. Po sprawdzeniu wyzwalacza, jeśli jest wymagany, mechanizm zostanie zastosowany. Jeśli skalowanie nie jest wymagane, nie zostanie podjęta żadna akcja. W obu przypadkach wyzwalacz nie zostanie ponownie sprawdzony przed ponownym upływem interwału skalowania.

Tego wyzwalacza można używać zarówno w przypadku stanowych, jak i bezstanowych usług. Jedynym mechanizmem, który może być używany z tym wyzwalaczem jest AddRemoveIncrementalNamedPartitionScalingMechanism. Gdy usługa jest skalowana w poziomie, dodawana jest Nowa partycja, a gdy usługa jest skalowana w jednej z istniejących partycji, zostanie usunięta. Istnieją ograniczenia, które będą sprawdzane po utworzeniu lub zaktualizowaniu usługi, a utworzenie usługi/aktualizacja zakończy się niepowodzeniem, jeśli te warunki nie zostaną spełnione:
* Dla usługi należy użyć schematu nazwanej partycji.
* Nazwy partycji muszą być kolejnymi liczbami całkowitymi, takimi jak "0", "1",...
* Pierwsza nazwa partycji musi mieć wartość "0".

Na przykład, jeśli usługa jest początkowo tworzona z trzema partycjami, jedyną prawidłową możliwością dla nazw partycji są "0", "1" i "2".

Wykonywana operacja automatycznego skalowania będzie uwzględniać również ten schemat nazewnictwa:
* Jeśli bieżące partycje usługi mają nazwy "0", "1" i "2", wówczas partycja, która zostanie dodana do skalowania, będzie mieć nazwę "3".
* Jeśli bieżące partycje usługi mają nazwy "0", "1" i "2", wówczas partycja, która zostanie usunięta do skalowania w programie, jest partycją o nazwie "2".

Analogicznie jak w przypadku mechanizmu, który używa skalowania poprzez dodawanie lub usuwanie wystąpień, istnieją trzy parametry, które określają sposób stosowania tego mechanizmu:
* _Przyrost skali_ określa, ile partycji zostanie dodanych i usuniętych po wyzwoleniu mechanizmu.
* _Maksymalna liczba partycji_ definiuje górny limit skalowania. Jeśli liczba partycji usługi osiągnie ten limit, usługa nie będzie skalowana w poziomie, niezależnie od obciążenia. Można pominąć ten limit, określając wartość-1, a w takim przypadku usługa zostanie przeskalowana w poziomie możliwie największej liczby (limit jest rzeczywistą pojemnością klastra).
* _Minimalna liczba wystąpień_ definiuje dolny limit skalowania. Jeśli liczba partycji usługi osiągnie ten limit, wówczas usługa nie będzie skalowana w przypadku, gdy jest to możliwe.

> [!WARNING] 
> Gdy AddRemoveIncrementalNamedPartitionScalingMechanism jest używany z usługami stanowymi, Service Fabric doda lub usunie partycje **bez powiadomienia lub ostrzeżenia**. Ponowne Partycjonowanie danych nie zostanie wykonane, gdy zostanie wyzwolony mechanizm skalowania. W przypadku operacji skalowania w poziomie nowe partycje będą puste i w przypadku operacji skalowania **partycja zostanie usunięta wraz ze wszystkimi danymi, które zawiera**.

## <a name="setting-auto-scaling-policy-for-partition-based-scaling"></a>Ustawianie zasad skalowania automatycznego dla skalowania na podstawie partycji

### <a name="using-application-manifest"></a>Korzystanie z manifestu aplikacji
``` xml
<NamedPartition>
    <Partition Name="0" />
</NamedPartition>
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Korzystanie z interfejsów API języka C#
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedPartitionScalingMechanism();
mechanism.MaxPartitionCount = 4;
mechanism.MinPartitionCount = 1;
mechanism.ScaleIncrement = 1;
//expecting that the service already has metric NumberOfConnections
trigger.MetricName = "NumberOfConnections";
trigger.ScaleInterval = TimeSpan.FromMinutes(15);
trigger.LowerLoadThreshold = 10000;
trigger.UpperLoadThreshold = 20000;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceUpdate.ScalingPolicies = new List<ScalingPolicyDescription>;
serviceUpdate.ScalingPolicies.Add(policy);
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```
### <a name="using-powershell"></a>Przy użyciu programu PowerShell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedPartitionScalingMechanism
$mechanism.MinPartitionCount = 1
$mechanism.MaxPartitionCount = 3
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AverageServiceLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_MemoryInMB"
$trigger.LowerLoadThreshold = 5000
$trigger.UpperLoadThreshold = 10000
$trigger.ScaleInterval = New-TimeSpan -Minutes 25
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -TargetReplicaSetSize 3 -MinReplicaSetSize 2 -HasPersistedState true -PartitionNames @("0","1") -ServicePackageActivationMode ExclusiveProcess -ScalingPolicies $scalingpolicies
```

## <a name="auto-scaling-based-on-resources"></a>Automatyczne skalowanie na podstawie zasobów

Aby umożliwić skalowanie usługi Monitor zasobów na podstawie rzeczywistych zasobów

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Istnieją dwie metryki reprezentujące rzeczywiste zasoby fizyczne. Jednym z nich jest element servicefabric:/_CpuCores reprezentujący rzeczywiste użycie procesora CPU (więc 0,5 reprezentuje połowę rdzenia), a drugi to w przypadku elementu servicefabric:/_MemoryInMB, który reprezentuje użycie pamięci w MB.
ResourceMonitorService jest odpowiedzialny za śledzenie użycia procesora i pamięci przez usługi użytkownika. Ta usługa będzie stosowała ważoną średnią przesunięcia w celu uwzględnienia potencjalnych gwałtownych skoków. Monitorowanie zasobów jest obsługiwane w przypadku aplikacji kontenerowych i niekontenerowych w systemie Windows oraz dla kontenerów w systemie Linux. Skalowanie automatyczne na zasobach jest włączone tylko dla usług aktywowanych w [modelu procesów wyłącznych](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
