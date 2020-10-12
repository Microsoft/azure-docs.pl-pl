---
title: Nadzór nad zasobami dla kontenerów i usług
description: Usługa Azure Service Fabric pozwala określić limity zasobów dla usług uruchomionych wewnątrz lub na zewnątrz kontenerów.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 11ca6e29829d911717a829b3e4dee0a190856a52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81115144"
---
# <a name="resource-governance"></a>Nadzór nad zasobami

W przypadku uruchamiania wielu usług w tym samym węźle lub klastrze istnieje możliwość, że jedna usługa może zużywać więcej zasobów, blokują inne usługi w procesie. Ten problem jest określany jako problem "sąsiada z zakłóceniami". Usługa Azure Service Fabric umożliwia deweloperom określenie rezerwacji i limitów dla usługi w celu zagwarantowania zasobów i ograniczenia użycia zasobów.

> Przed przejściem do tego artykułu zalecamy zapoznanie się z [modelem aplikacji Service Fabric](service-fabric-application-model.md) i [Service Fabric modelem hostingu](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Metryki ładu zasobów

Zarządzanie zasobami jest obsługiwane w Service Fabric zgodnie z [pakietem usługi](service-fabric-application-model.md). Zasoby przypisane do pakietu usługi mogą być dalej dzielone między pakietami kodu. Określone limity zasobów oznaczają również rezerwację zasobów. Service Fabric obsługuje określanie procesora CPU i pamięci na pakiet usługi przy użyciu dwóch wbudowanych [metryk](service-fabric-cluster-resource-manager-metrics.md):

* *Procesor* (Nazwa metryki `servicefabric:/_CpuCores` ): rdzeń logiczny, który jest dostępny na komputerze-hoście. Wszystkie rdzenie we wszystkich węzłach są ważone jako takie same.

* *Pamięć* (Nazwa metryki `servicefabric:/_MemoryInMB` ): pamięć jest wyrażona w megabajtach i mapowana na pamięć fizyczną, która jest dostępna na komputerze.

W przypadku tych dwóch metryk [klaster Menedżer zasobów](service-fabric-cluster-resource-manager-cluster-description.md) śledzi łączną pojemność klastra, obciążenie każdego węzła w klastrze oraz pozostałe zasoby w klastrze. Te dwie metryki są równoważne z jakimkolwiek innym użytkownikiem lub metryką niestandardową. Z nimi można korzystać ze wszystkich istniejących funkcji:

* Klaster można [zrównoważyć](service-fabric-cluster-resource-manager-balancing.md) zgodnie z tymi dwiema metrykami (zachowanie domyślne).
* Klaster może być [pofragmentowany](service-fabric-cluster-resource-manager-defragmentation-metrics.md) zgodnie z tymi dwiema metrykami.
* Podczas [opisywania klastra](service-fabric-cluster-resource-manager-cluster-description.md)można ustawić buforowaną pojemność dla tych dwóch metryk.

> [!NOTE]
> [Dynamiczne raportowanie obciążenia](service-fabric-cluster-resource-manager-metrics.md) nie jest obsługiwane w przypadku tych metryk; obciążenia dla tych metryk są definiowane podczas tworzenia.

## <a name="resource-governance-mechanism"></a>Mechanizm ładu zasobów

Obecnie środowisko uruchomieniowe Service Fabric nie zapewnia rezerwacji dla zasobów. Gdy zostanie otwarty proces lub kontener, środowisko uruchomieniowe ustawia limity zasobów dla obciążeń, które zostały zdefiniowane podczas tworzenia. Ponadto środowisko uruchomieniowe odrzuca otwieranie nowych pakietów usługi, które są dostępne po przekroczeniu zasobów. Aby lepiej zrozumieć, jak działa proces, przyjrzyjmy się przykładowi do węzła z dwoma rdzeniami procesora (mechanizm zarządzania pamięcią jest równoważny):

1. Najpierw kontener jest umieszczany w węźle, żądając jednego rdzenia procesora. Środowisko uruchomieniowe otwiera kontener i ustawia limit CPU na jeden rdzeń. Kontener nie będzie mógł używać więcej niż jednego rdzenia.

2. Następnie replika usługi jest umieszczana w węźle, a odpowiedni pakiet usługi określa limit jednego rdzenia procesora. Środowisko uruchomieniowe otwiera pakiet kodu i ustawia jego limit CPU na jeden rdzeń.

W tym momencie suma limitów jest równa pojemności węzła. Proces i kontener są uruchamiane z jednym rdzeniem i nie zakłócają siebie nawzajem. Service Fabric nie umieszcza więcej kontenerów ani replik podczas określania limitu procesora CPU.

Istnieją jednak dwie sytuacje, w których inne procesy mogą będą konkurować o dla procesora CPU. W takich sytuacjach proces i kontener z naszego przykładu mogą napotkać problem z sąsiadem:

* *Mieszanie usług i kontenerów objętych zasadami i niezarządzanymi*: Jeśli użytkownik tworzy usługę bez określonego ładu zarządzania zasobami, środowisko uruchomieniowe widzi je jako zużywające brak zasobów i może umieścić je w węźle w naszym przykładzie. W takim przypadku ten nowy proces efektywnie zużywa jakiś procesor CPU kosztem usług, które są już uruchomione w węźle. Istnieją dwa rozwiązania tego problemu. Nie należy mieszać usług objętych usługami ani w tym samym klastrze ani używać [ograniczeń umieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) , aby te dwa typy usług nie kończyły się w tym samym zestawie węzłów.

* *Gdy inny proces jest uruchamiany w węźle, poza Service Fabric (na przykład usługa systemu operacyjnego)*: w takiej sytuacji proces poza Service Fabric jest również przeznaczony dla procesorów z istniejącymi usługami. Rozwiązaniem tego problemu jest poprawne skonfigurowanie pojemności węzłów w celu uwzględnienia obciążenia systemu operacyjnego, jak pokazano w następnej sekcji.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Konfiguracja klastra do włączenia zarządzania zasobami

Gdy węzeł zostanie uruchomiony i przyłączy się do klastra, Service Fabric wykrywa dostępną ilość pamięci i liczbę rdzeni, a następnie ustawia możliwości węzła dla tych dwóch zasobów.

Aby pozostawić miejsce w buforze dla systemu operacyjnego, a dla innych procesów może być uruchomiony w węźle, Service Fabric używa tylko 80% zasobów dostępnych w węźle. Ta wartość procentowa jest konfigurowalna i można ją zmienić w manifeście klastra.

Oto przykład sposobu poinstruować Service Fabric, aby użyć 50% dostępnego procesora i 70% dostępnej pamięci:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

W przypadku większości klientów i scenariuszy zalecaną konfiguracją jest automatyczne wykrywanie pojemności węzłów dla procesora CPU i pamięci (automatyczne wykrywanie jest domyślnie włączone). Jeśli jednak potrzebna jest pełna ręczna konfiguracja pojemności węzłów, można skonfigurować te typy dla każdego typu węzła przy użyciu mechanizmu opisywania węzłów w klastrze. Oto przykład sposobu konfigurowania typu węzła z czterema rdzeniami i 2 GB pamięci:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
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
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> Począwszy od Service Fabric w wersji 7,0, zaktualizowaliśmy regułę dotyczącą sposobu obliczania pojemności zasobów węzłów w przypadkach, gdy użytkownik ręcznie udostępnia wartości dla dyspozycyjności zasobów węzła. Rozważmy następujący scenariusz:
>
> * W węźle istnieje 10 rdzeni procesora
> * SF jest skonfigurowany do używania 80% łącznej liczby zasobów dla usług użytkowników (ustawienie domyślne), co pozostawia bufor 20% dla innych usług uruchomionych w węźle (w tym Service Fabric usługi systemowe).
> * Użytkownik decyduje się ręcznie przesłonić pojemność zasobów węzła dla metryki rdzeni procesora CPU i ustawia ją na 5 rdzeni
>
> Zmieniono regułę dotyczącą sposobu obliczania dostępnej pojemności Service Fabric usług użytkownika w następujący sposób:
>
> * Przed Service Fabric 7,0 przepustowość dostępna dla usług użytkowników zostanie obliczona na **5 rdzeni** (bufor pojemności 20% zostanie zignorowany)
> * Począwszy od Service Fabric 7,0, dostępna pojemność dla usług użytkowników zostanie obliczona na **4 rdzenie** (bufor pojemności 20% nie zostanie zignorowany)

## <a name="specify-resource-governance"></a>Określanie ładu zasobów

Limity zarządzania zasobami są określone w manifeście aplikacji (sekcja ServiceManifestImport), jak pokazano w następującym przykładzie:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```

W tym przykładzie pakiet usługi o nazwie **Servicepackage** . otrzymuje jeden rdzeń w węzłach, w których został umieszczony. Ten pakiet usługi zawiera dwa pakiety kodu (**CodeA1** i **CodeA2**), a oba określają `CpuShares` parametr. Proporcja CpuShares 512:256 dzieli rdzeń na dwa pakiety kodu.

W ten przykład, w tym przykładzie, CodeA1 pobiera dwie trzecie rdzenia, a CodeA2 pobiera jedną trzecią z rdzenia (i rezerwacji nietrwałej w taki sam sposób). Jeśli CpuShares nie są określone dla pakietów kodu, Service Fabric dzieli rdzenie równomiernie między nimi.

Limity pamięci są bezwzględne, więc oba pakiety kodu są ograniczone do 1024 MB pamięci (i rezerwacja nietrwałej gwarancji tego samego). Pakiety kodu (kontenerów lub procesów) nie mogą alokować większej ilości pamięci niż ten limit i próba wykonania tej czynności spowoduje wyjątek braku pamięci. Aby wymuszanie limitu zasobów działało, wszystkie pakiety kodu w ramach pakietu usług powinny mieć określone limity pamięci.

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

Aby zapobiec wystąpieniu tych sytuacji, Service Fabric umożliwia *wymuszenie limitów zasobów dla wszystkich Service Fabric usług użytkowników działających w węźle* (podlegających i niezarządzana) w celu zagwarantowania, że usługi użytkownika nigdy nie będą używać więcej niż określonej ilości zasobów. Można to osiągnąć przez ustawienie wartości właściwości EnforceUserServiceMetricCapacities w sekcji PlacementAndLoadBalancing w ClusterManifest na true. To ustawienie jest domyślnie wyłączone.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Dodatkowe uwagi:

* Wymuszanie limitu zasobów dotyczy tylko `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` metryk zasobów i
* Wymuszanie limitu zasobów działa tylko wtedy, gdy pojemność węzłów dla metryk zasobów jest dostępna do Service Fabric, za pomocą mechanizmu autowykrywania, lub przez użytkowników ręcznie określając pojemności węzła (zgodnie z opisem w sekcji [Konfiguracja klastra dla włączania zarządzania zasobami](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance) ).Jeśli nie skonfigurowano pojemności węzła, nie można użyć możliwości wymuszania limitu zasobów, ponieważ Service Fabric nie może znać ilości zasobów zarezerwowanych dla usług użytkownika.Service Fabric wystawia ostrzeżenia o kondycji, jeśli "EnforceUserServiceMetricCapacities" ma wartość true, ale pojemności węzła nie są skonfigurowane.

## <a name="other-resources-for-containers"></a>Inne zasoby dla kontenerów

Oprócz procesora CPU i pamięci można określić inne limity zasobów dla kontenerów. Limity te są określane na poziomie pakietu kodu i są stosowane po rozpoczęciu kontenera. W przeciwieństwie do procesora i pamięci, klaster Menedżer zasobów nie wie o tych zasobach i nie wykonuje żadnych testów wydajności ani równoważenia obciążenia dla nich.

* *MemorySwapInMB*: ilość pamięci wymiany, która może być używana przez kontener.
* *MemoryReservationInMB*: limit Soft dotyczący zarządzania pamięcią wymuszany tylko w przypadku wykrycia rywalizacji o pamięć w węźle.
* *CpuPercent*: procent użycia procesora CPU, który może być używany przez kontener. Jeśli dla pakietu usługi są określone limity procesora CPU, ten parametr jest skutecznie ignorowany.
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
* Aby dowiedzieć się więcej na temat modelu aplikacji, pakietów usług i pakietów kodu — oraz sposobu mapowania replik na nie — odczytywanie [modelu aplikacji w Service Fabric](service-fabric-application-model.md).
