---
title: Nadzór nad zasobami dla kontenerów i usług
description: Usługa Azure Service Fabric pozwala określić żądania zasobów i limity dla usług uruchomionych jako procesy lub kontenery.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: d760766870c8c2be0a2d2384f6d012b75bc92fbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735662"
---
# <a name="resource-governance"></a>Nadzór nad zasobami

W przypadku uruchamiania wielu usług w tym samym węźle lub klastrze istnieje możliwość, że jedna usługa może zużywać więcej zasobów, blokują inne usługi w procesie. Ten problem jest określany jako problem "sąsiada z zakłóceniami". Usługa Azure Service Fabric umożliwia deweloperom sterowanie tym zachowaniem przez określenie żądań i limitów na usługę w celu ograniczenia użycia zasobów.

> Przed przejściem do tego artykułu zalecamy zapoznanie się z [modelem aplikacji Service Fabric][application-model-link] i [Service Fabric modelem hostingu][hosting-model-link].
>

## <a name="resource-governance-metrics"></a>Metryki ładu zasobów

Zarządzanie zasobami jest obsługiwane w Service Fabric zgodnie z [pakietem usługi][application-model-link]. Zasoby przypisane do pakietu usługi mogą być dalej dzielone między pakietami kodu. Service Fabric obsługuje zarządzanie procesorami CPU i pamięci na pakiet usług, z dwiema wbudowanymi [metrykami](service-fabric-cluster-resource-manager-metrics.md):

* *Procesor* (Nazwa metryki `servicefabric:/_CpuCores` ): rdzeń logiczny, który jest dostępny na komputerze-hoście. Wszystkie rdzenie we wszystkich węzłach są ważone jako takie same.

* *Pamięć* (Nazwa metryki `servicefabric:/_MemoryInMB` ): pamięć jest wyrażona w megabajtach i mapowana na pamięć fizyczną, która jest dostępna na komputerze.

W przypadku tych dwóch metryk [Menedżer zasobów klastra (CRM)][cluster-resource-manager-description-link] śledzi łączną pojemność klastra, obciążenie każdego węzła w klastrze oraz pozostałe zasoby w klastrze. Te dwie metryki są równoważne z jakimkolwiek innym użytkownikiem lub metryką niestandardową. Z nimi można korzystać ze wszystkich istniejących funkcji:

* Klaster można [zrównoważyć](service-fabric-cluster-resource-manager-balancing.md) zgodnie z tymi dwiema metrykami (zachowanie domyślne).
* Klaster może być [pofragmentowany](service-fabric-cluster-resource-manager-defragmentation-metrics.md) zgodnie z tymi dwiema metrykami.
* Podczas [opisywania klastra][cluster-resource-manager-description-link]można ustawić buforowaną pojemność dla tych dwóch metryk.

> [!NOTE]
> [Dynamiczne raportowanie obciążenia](service-fabric-cluster-resource-manager-metrics.md) nie jest obsługiwane w przypadku tych metryk; obciążenia dla tych metryk są definiowane podczas tworzenia.

## <a name="resource-governance-mechanism"></a>Mechanizm ładu zasobów

Począwszy od wersji 7,2, środowisko uruchomieniowe Service Fabric obsługuje specyfikację żądań i limitów zasobów procesora i pamięci.

> [!NOTE]
> Service Fabric wersje starsze niż 7,2 obsługują tylko model, w którym pojedyncza wartość służy zarówno jako **żądanie** , jak i **Limit** określonego zasobu (procesor lub pamięć). Jest to opisane jako Specyfikacja **RequestsOnly** w tym dokumencie.

* *Żądania:* Wartości żądań procesora i pamięci przedstawiają obciążenia używane przez [Menedżer zasobów klastra (CRM)][cluster-resource-manager-description-link] dla `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` metryk i. Innymi słowy, program CRM traktuje użycie zasobów przez usługę tak, aby była równa jego wartości żądania, i używa tych wartości podczas podejmowania decyzji dotyczących umieszczania.

* *Limity:* Wartości limitu procesora CPU i pamięci reprezentują rzeczywiste limity zasobów stosowane podczas aktywowania procesu lub kontenera w węźle.

Service Fabric zezwala na **RequestsOnly, LimitsOnly** i obie specyfikacje **RequestsAndLimits** dla procesora i pamięci.
* Gdy jest używana specyfikacja RequestsOnly, Usługa Service Fabric używa również wartości żądań jako limitów.
* Gdy jest używana specyfikacja LimitsOnly, Usługa Service Fabric traktuje wartości żądania jako 0.
* W przypadku użycia specyfikacji RequestsAndLimits wartość limitu musi być większa lub równa wartościom żądania.

Aby lepiej zrozumieć mechanizm zarządzania zasobami, przyjrzyjmy się przykładowym scenariuszowi umieszczania ze specyfikacją **RequestsOnly** dla zasobu procesora CPU (mechanizm zarządzania pamięcią jest równoważny). Rozważmy węzeł z dwoma rdzeniami procesora i dwoma pakietami usług, które zostaną umieszczone na nim. Pierwszy pakiet usługi, który ma zostać umieszczony, składa się tylko z jednego pakietu kodu kontenera i określa tylko żądanie jednego rdzenia procesora. Drugi pakiet usługi, który ma zostać umieszczony, składa się tylko z jednego pakietu kodu opartego na procesie, a także określa tylko żądanie jednego rdzenia procesora. Ponieważ oba pakiety usług mają specyfikację RequestsOnly, ich wartości limitu są ustawiane na ich wartości żądania.

1. Najpierw pakiet usługi oparty na kontenerach żądający jednego rdzenia procesora CPU jest umieszczany w węźle. Środowisko uruchomieniowe aktywuje kontener i ustawia limit CPU na jeden rdzeń. Kontener nie będzie mógł używać więcej niż jednego rdzenia.

2. Następnie pakiet usługi oparty na procesie żądający jednego rdzenia procesora jest umieszczany w węźle. Środowisko uruchomieniowe aktywuje proces usługi i ustawia jego limit CPU na jeden rdzeń.

W tym momencie suma żądań jest równa pojemności węzła. W tym węźle program CRM nie umieści więcej kontenerów ani procesów usługi z żądaniami procesora. W węźle proces i kontener są uruchamiane z jednym rdzeniem, a każdy z nich nie będzie będą konkurować o ze sobą dla procesora CPU.

Przejdźmy teraz do naszego przykładu przy użyciu specyfikacji **RequestsAndLimits** . Tym razem pakiet usługi oparty na kontenerach Określa żądanie jednego rdzenia procesora i limit dwóch rdzeni procesora. Pakiet usługi bazującej na procesie określa zarówno żądanie, jak i limit jednego rdzenia procesora.
  1. Najpierw zostanie umieszczony pakiet usługi oparty na kontenerach w węźle. Środowisko uruchomieniowe aktywuje kontener i ustawia limit CPU na dwa rdzenie. Kontener nie będzie mógł używać więcej niż dwóch rdzeni.
  2. Następnie pakiet usługi oparty na procesie zostanie umieszczony w węźle. Środowisko uruchomieniowe aktywuje proces usługi i ustawia jego limit CPU na jeden rdzeń.

  W tym momencie suma żądań procesorów usługi, które są umieszczone w węźle, jest równa pojemności procesora CPU w węźle. W tym węźle program CRM nie umieści więcej kontenerów ani procesów usługi z żądaniami procesora. Jednak w węźle suma limitów (dwa rdzenie dla kontenera + jeden rdzeń dla procesu) przekracza pojemność dwóch rdzeni. Jeśli kontener i seria procesów w tym samym czasie, istnieje możliwość rywalizacji o zasoby procesora CPU. Takie rywalizacje będzie zarządzane przez podstawowy system operacyjny dla danej platformy. Na potrzeby tego przykładu kontener może przetwarzać do dwóch rdzeni procesora, co spowodowało, że nie zagwarantowano żądania jednego rdzenia procesora.

> [!NOTE]
> Jak pokazano w poprzednim przykładzie, wartości żądania dla procesora CPU i pamięci nie **powodują rezerwacji zasobów w węźle**. Te wartości reprezentują użycie zasobów, które Menedżer zasobów klaster podczas podejmowania decyzji dotyczących umieszczania. Wartości graniczne reprezentują rzeczywiste limity zasobów stosowane podczas aktywowania procesu lub kontenera w węźle.


Istnieje kilka sytuacji, w których może być rywalizacja o procesor CPU. W takich sytuacjach proces i kontener z naszego przykładu mogą napotkać problem z sąsiadem:

* *Mieszanie usług i kontenerów objętych zasadami i niezarządzanymi*: Jeśli użytkownik tworzy usługę bez określonego ładu zarządzania zasobami, środowisko uruchomieniowe widzi je jako zużywające brak zasobów i może umieścić je w węźle w naszym przykładzie. W takim przypadku ten nowy proces efektywnie zużywa jakiś procesor CPU kosztem usług, które są już uruchomione w węźle. Istnieją dwa rozwiązania tego problemu. Nie należy mieszać usług objętych usługami ani w tym samym klastrze ani używać [ograniczeń umieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) , aby te dwa typy usług nie kończyły się w tym samym zestawie węzłów.

* *Gdy inny proces jest uruchamiany w węźle, poza Service Fabric (na przykład usługa systemu operacyjnego)*: w takiej sytuacji proces poza Service Fabric jest również przeznaczony dla procesorów z istniejącymi usługami. Rozwiązaniem tego problemu jest poprawne skonfigurowanie pojemności węzłów w celu uwzględnienia obciążenia systemu operacyjnego, jak pokazano w następnej sekcji.

* *Kiedy żądania nie są równe limitom*: zgodnie z wcześniejszym opisem w RequestsAndLimits przykładzie żądania nie powodują rezerwacji zasobów w węźle. Gdy usługa o limicie przekraczającym żądania jest umieszczana w węźle, może zużywać zasoby (jeśli są dostępne) do limitów. W takich przypadkach inne usługi w węźle mogą nie być w stanie zużywać zasobów do ich wartości żądania.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Konfiguracja klastra do włączenia zarządzania zasobami

Gdy węzeł zostanie uruchomiony i przyłączy się do klastra, Service Fabric wykrywa dostępną ilość pamięci i liczbę rdzeni, a następnie ustawia możliwości węzła dla tych dwóch zasobów.

Aby pozostawić miejsce w buforze dla systemu operacyjnego, a dla innych procesów, które mogą być uruchomione w węźle, Service Fabric używa tylko 80% zasobów dostępnych w węźle. Ta wartość procentowa jest konfigurowalna i można ją zmienić w manifeście klastra.

Oto przykład sposobu poinstruować Service Fabric, aby użyć 50% dostępnego procesora i 70% dostępnej pamięci:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

W przypadku większości klientów i scenariuszy zalecaną konfiguracją jest automatyczne wykrywanie pojemności węzłów w przypadku procesora CPU i pamięci (automatyczne wykrywanie jest domyślnie włączone). Jeśli jednak potrzebna jest pełna ręczna konfiguracja pojemności węzłów, można je skonfigurować dla każdego typu węzła przy użyciu mechanizmu opisywania węzłów w klastrze. Oto przykład sposobu konfigurowania typu węzła z czterema rdzeniami i 2 GB pamięci:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Po włączeniu automatycznego wykrywania dostępnych zasobów i pojemności węzłów są ręcznie definiowane w manifeście klastra, Service Fabric sprawdza, czy węzeł ma wystarczającą ilość zasobów do obsługi pojemności zdefiniowanej przez użytkownika:

* Jeśli pojemności węzłów, które są zdefiniowane w manifeście, są mniejsze lub równe dostępnym zasobom w węźle, a następnie Service Fabric korzysta z pojemności określonych w manifeście.

* Jeśli pojemności węzłów, które są zdefiniowane w manifeście, są większe niż dostępne zasoby, Service Fabric używa dostępnych zasobów jako pojemności węzłów.

Funkcję automatycznego wykrywania dostępnych zasobów można wyłączyć, jeśli nie jest to wymagane. Aby ją wyłączyć, Zmień następujące ustawienie:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

W celu uzyskania optymalnej wydajności należy również włączyć następujące ustawienie w manifeście klastra:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> Począwszy od Service Fabric w wersji 7,0, zaktualizowaliśmy regułę dotyczącą sposobu obliczania pojemności zasobów węzłów w przypadkach, gdy użytkownik ręcznie udostępnia wartości dla dyspozycyjności zasobów węzła. Rozważmy następujący scenariusz:
>
> * W węźle istnieje łącznie 10 rdzeni procesora CPU
> * SF jest skonfigurowany do używania 80% łącznej liczby zasobów dla usług użytkowników (ustawienie domyślne), co pozostawia bufor 20% dla innych usług uruchomionych w węźle (w tym Service Fabric usługi systemowe).
> * Użytkownik decyduje się ręcznie przesłonić pojemność zasobów węzła dla metryki rdzeni procesora CPU i ustawia ją na 5 rdzeni
>
> Zmieniono regułę dotyczącą sposobu obliczania dostępnej pojemności Service Fabric usług użytkownika w następujący sposób:
>
> * Przed Service Fabric 7,0 przepustowość dostępna dla usług użytkowników zostanie obliczona na **5 rdzeni** (bufor pojemności 20% zostanie zignorowany)
> * Począwszy od Service Fabric 7,0, dostępna pojemność dla usług użytkowników zostanie obliczona na **4 rdzenie** (bufor pojemności 20% nie zostanie zignorowany)

## <a name="specify-resource-governance"></a>Określanie ładu zasobów

Żądania i limity ładu zasobów są określone w manifeście aplikacji (sekcja ServiceManifestImport). Przyjrzyjmy się kilku przykładom:

**Przykład 1: Specyfikacja RequestsOnly**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1024" />
    </Policies>
  </ServiceManifestImport>
```

W tym przykładzie `CpuCores` atrybut jest używany do określenia żądania 1 rdzenia procesora CPU dla **servicepackage**. Ponieważ limit CPU ( `CpuCoresLimit` atrybut) nie jest określony, Service Fabric używa również określonej wartości żądania równej 1 rdzeń jako limitu procesora CPU dla pakietu usługi.

**Servicepackage** a zostanie umieszczona tylko w węźle, w którym pozostała pojemność procesora CPU po odjęciu **sumy żądań procesora CPU wszystkich pakietów usług umieszczonych w tym węźle** jest większa lub równa 1 rdzeń. W węźle pakiet usługi będzie ograniczony do jednego rdzenia. Pakiet usługi zawiera dwa pakiety kodu (**CodeA1** i **CodeA2**), a oba określają `CpuShares` atrybut. Część CpuShares 512:256 służy do obliczania limitów procesorów dla poszczególnych pakietów kodu. W ten sposób CodeA1 będzie ograniczony do dwóch trzecich rdzeni, a CodeA2 będzie ograniczona do jednej trzeciej części rdzenia. Jeśli CpuShares nie są określone dla wszystkich pakietów kodu, Service Fabric dzieli limit CPU jednocześnie między nimi.

Chociaż CpuShares określona dla pakietów kodu reprezentuje względną wartość całkowitego limitu czasu procesora CPU pakietu usługi, wartości pamięci dla pakietów kodu są określane jako bezwzględne. W tym przykładzie `MemoryInMB` atrybut jest używany do określania żądań pamięci o 1024 MB dla zarówno CodeA1, jak i CodeA2. Ponieważ limit pamięci ( `MemoryInMBLimit` atrybut) nie został określony, Service Fabric używa także określonych wartości żądania jako limitów pakietów kodu. Żądanie dotyczące pamięci (i limit) dla pakietu usługi jest obliczane jako suma wartości żądań pamięci (i limit) pakietów kodu jego składników. W tym przypadku dla **servicepackage**, żądanie i limit pamięci są obliczane jako 2048 MB.

**Servicepackage** a zostanie umieszczona tylko w węźle, w którym pozostała pojemność pamięci po odjęciu **sumy żądań pamięci wszystkich pakietów usług umieszczonych w tym węźle** jest większa lub równa 2048 MB. W węźle Oba pakiety kodu będą ograniczone do 1024 MB pamięci każdego z nich. Pakiety kodu (kontenerów lub procesów) nie będą mogły przydzielić większej ilości pamięci niż ten limit, a próby wykonania tej czynności spowodują wyjątek braku pamięci.

**Przykład 2: Specyfikacja LimitsOnly**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCoresLimit="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMBLimit="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMBLimit="1024" />
    </Policies>
  </ServiceManifestImport>
```
Ten przykład używa `CpuCoresLimit` i `MemoryInMBLimit` atrybutów, które są dostępne tylko w SF w wersji 7,2 lub nowszej. Atrybut CpuCoresLimit służy do określania limitu procesora CPU 1 rdzeń dla **servicepackage**. Ponieważ żądanie procesora CPU ( `CpuCores` atrybut) nie jest określone, przyjmuje się, że wartość wynosi 0. `MemoryInMBLimit` atrybut służy do określania limitów pamięci wynoszącej 1024 MB dla CodeA1 i CodeA2 i ponieważ żądania ( `MemoryInMB` atrybut) nie są określone, są uważane za 0. Żądanie dotyczące pamięci i limit dla **servicepackage** jest odpowiednio obliczane jako 0 i 2048. Ponieważ żądania dotyczące procesora CPU i pamięci dla **pakietu servicepackage** są równe 0, nie ma żadnego obciążenia dla programu CRM, które należy wziąć pod uwagę podczas umieszczania, dla `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` metryk i. Z tego względu, z perspektywy ładu zasobów, **Servicepackagea** można umieścić w dowolnym węźle **niezależnie od pozostałej pojemności**. Podobnie jak w przypadku przykładu 1, w węźle CodeA1 będzie ograniczona do dwóch trzecich rdzeni i 1024 MB pamięci, a CodeA2 będzie ograniczona do jednej z nich i 1024 MB pamięci.

**Przykład 3: Specyfikacja RequestsAndLimits**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1" CpuCoresLimit="2"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" MemoryInMBLimit="3072" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="2048" MemoryInMBLimit="4096" />
    </Policies>
  </ServiceManifestImport>
```
W poniższym przykładzie pokazano, jak można utworzyć dwa pierwsze przykłady, aby określić zarówno żądania, jak i limity dla procesora CPU i pamięci. **Servicepackagee** ma żądania procesora CPU i pamięci o wartości 1 rdzeń i 3072 (1024 + 2048) MB odpowiednio. Można ją umieścić tylko w węźle, który ma co najmniej 1 rdzeń (i 3072 MB) pojemności pozostawionej po odjęciu wszystkich żądań procesora CPU (i pamięci) wszystkich pakietów usługi umieszczonych w węźle z łącznej pojemności procesora (i pamięci) węzła. W węźle CodeA1 będzie ograniczony do dwóch trzecich 2 rdzeni i 3072 MB pamięci, podczas gdy CodeA2 będzie ograniczona do jednej trzeciej 2 rdzeni i 4096 MB pamięci.

### <a name="using-application-parameters"></a>Używanie parametrów aplikacji

Podczas określania ustawień zarządzania zasobami można używać [parametrów aplikacji](service-fabric-manage-multiple-environment-app-configuration.md) do zarządzania wieloma konfiguracjami aplikacji. W poniższym przykładzie pokazano użycie parametrów aplikacji:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

W tym przykładzie domyślne wartości parametrów są ustawiane dla środowiska produkcyjnego, w którym każdy pakiet usługi będzie miał 4 rdzenie i 2 GB pamięci. Istnieje możliwość zmiany wartości domyślnych przy użyciu plików parametrów aplikacji. W tym przykładzie jeden plik parametrów może służyć do lokalnego testowania aplikacji, w której byłyby mniej zasobów niż w środowisku produkcyjnym:

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> Określanie nadzoru zasobów przy użyciu parametrów aplikacji jest dostępne począwszy od Service Fabric w wersji 6,1.<br>
>
> Gdy parametry aplikacji są używane do określania ładu zasobów, Service Fabric nie może zostać obniżone do wersji starszej niż 6,1.

## <a name="enforcing-the-resource-limits-for-user-services"></a>Wymuszanie limitów zasobów dla usług użytkownika

Podczas stosowania nadzoru zasobów do Service Fabric usług gwarantuje, że te usługi, które nie mogą przekraczać limitu przydziału zasobów, wielu użytkowników nadal muszą uruchamiać niektóre usługi Service Fabric w trybie niezarządzanym. W przypadku korzystania z nieregulowanej Service Fabric usług można uruchamiać w sytuacjach, w których "przemijające" usługi niezarządzane zużywają wszystkie dostępne zasoby w węzłach Service Fabric, co może prowadzić do poważnych problemów, takich jak:

* Zablokowanie zasobów dla innych usług uruchomionych w węzłach (w tym Service Fabric usługach systemowych)
* Węzły kończące się w złej kondycji
* Nieodpowiadające interfejsy API zarządzania klastrem Service Fabric

Aby zapobiec wystąpieniu tych sytuacji, Service Fabric umożliwia *wymuszenie limitów zasobów dla wszystkich Service Fabric usług użytkowników działających w węźle* (podlegających i niezarządzana) w celu zagwarantowania, że usługi użytkownika nigdy nie będą używać więcej niż określonej ilości zasobów. Można to osiągnąć przez ustawienie wartości właściwości EnforceUserServiceMetricCapacities w sekcji PlacementAndLoadBalancing w ClusterManifest na true. To ustawienie jest domyślnie wyłączone.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Dodatkowe uwagi:

* Wymuszanie limitu zasobów dotyczy tylko `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` metryk zasobów i
* Wymuszanie limitu zasobów działa tylko wtedy, gdy pojemność węzłów dla metryk zasobów jest dostępna do Service Fabric, za pomocą mechanizmu autowykrywania, lub przez użytkowników ręcznie określając pojemności węzła (zgodnie z opisem w sekcji [Konfiguracja klastra dla włączania zarządzania zasobami](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance) ). Jeśli nie skonfigurowano pojemności węzła, nie można użyć możliwości wymuszania limitu zasobów, ponieważ Service Fabric nie może znać ilości zasobów zarezerwowanych dla usług użytkownika. Service Fabric wystawia ostrzeżenia o kondycji, jeśli "EnforceUserServiceMetricCapacities" ma wartość true, ale pojemności węzła nie są skonfigurowane.

## <a name="other-resources-for-containers"></a>Inne zasoby dla kontenerów

Oprócz procesora CPU i pamięci można określić inne limity zasobów dla kontenerów. Limity te są określane na poziomie pakietu kodu i są stosowane po rozpoczęciu kontenera. W przeciwieństwie do procesora i pamięci, klaster Menedżer zasobów nie wie o tych zasobach i nie wykonuje żadnych testów wydajności ani równoważenia obciążenia dla nich.

* *MemorySwapInMB*: ilość pamięci wymiany, która może być używana przez kontener.
* *MemoryReservationInMB*: limit Soft dotyczący zarządzania pamięcią wymuszany tylko w przypadku wykrycia rywalizacji o pamięć w węźle.
* *CpuPercent*: procent użycia procesora CPU, który może być używany przez kontener. Jeśli dla pakietu usługi określono żądania lub limity procesora CPU, ten parametr jest skutecznie ignorowany.
* *MaximumIOps*: Maksymalna liczba operacji we/wy, która może być używana przez kontener (odczyt i zapis).
* *MaximumIOBytesps*: maksymalna wartość operacji we/wy (w bajtach na sekundę), która może być używana przez kontener (odczyt i zapis).
* *BlockIOWeight*: Zablokuj wagi we/wy dla innych kontenerów.

Te zasoby mogą być połączone z PROCESORAmi i pamięcią. Oto przykład sposobu określania dodatkowych zasobów dla kontenerów:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o Menedżer zasobów klastra, przeczytaj artykuł [wprowadzenie do Service Fabric Menedżer zasobów klastra](service-fabric-cluster-resource-manager-introduction.md).
* Aby dowiedzieć się więcej na temat modelu aplikacji, pakietów usług i pakietów kodu — oraz sposobu mapowania replik na nie — odczytywanie [modelu aplikacji w Service Fabric][application-model-link].

<!-- Links -->
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[cluster-resource-manager-description-link]: service-fabric-cluster-resource-manager-cluster-description.md
