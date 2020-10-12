---
title: Opisywanie usług i aplikacji Service Fabric platformy Azure
description: Opisuje, w jaki sposób manifesty są używane do opisywania Service Fabric aplikacji i usług.
ms.topic: conceptual
ms.date: 8/12/2019
ms.openlocfilehash: fcf4c7611f0a6f52c28b234717b9244ac58ad2d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86248224"
---
# <a name="service-fabric-application-and-service-manifests"></a>Service Fabric manifesty aplikacji i usług
W tym artykule opisano sposób, w jaki Service Fabric aplikacje i usługi są zdefiniowane i używane do obsługi wersji przy użyciu plików ApplicationManifest.xml i ServiceManifest.xml.  Aby uzyskać bardziej szczegółowe przykłady, zobacz [przykłady manifestów aplikacji i usług](service-fabric-manifest-examples.md).  Schemat XML dla tych plików manifestu jest udokumentowany w [dokumentacji schematu ServiceFabricServiceModel. xsd](service-fabric-service-model-schema.md).

> [!WARNING]
> Schemat pliku XML manifestu wymusza prawidłową kolejność elementów podrzędnych.  Jako częściowe obejście, Otwórz "C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd" w programie Visual Studio podczas tworzenia lub modyfikowania dowolnych manifestów Service Fabric. Umożliwi to sprawdzenie kolejności elementów podrzędnych i zapewnia korzystać projekcjami.

## <a name="describe-a-service-in-servicemanifestxml"></a>Opisywanie usługi w ServiceManifest.xml
Manifest usługi deklaratywnie definiuje typ i wersję usługi. Określa on metadane usługi, takie jak typ usługi, właściwości kondycji, metryki równoważenia obciążenia, pliki binarne usługi i plików konfiguracyjnych.  Można to zrobić w inny sposób, opisując kod, konfigurację i pakiety danych tworzące pakiet usługi do obsługi jednego lub kilku typów usług. Manifest usługi może zawierać wiele pakietów kodu, konfiguracji i danych, które mogą być niezależnie od wersji. Oto manifest usługi dla usługi ASP.NET Core Internet frontonu [przykładowej aplikacji do głosowania](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (i przedstawiono kilka [bardziej szczegółowych przykładów](service-fabric-manifest-examples.md)):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

Atrybuty **wersji** są ciągami bez struktury i nie są analizowane przez system. Atrybuty wersji są używane do wersji każdego składnika do uaktualnienia.

Elementy **ServiceType** deklarują, jakie typy usług są obsługiwane przez **CodePackages** w tym manifeście. W przypadku wystąpienia usługi dla jednego z tych typów usług wszystkie pakiety kodu zadeklarowane w tym manifeście są uaktywniane przez uruchomienie ich punktów wejścia. Procesy powstające powinny rejestrować obsługiwane typy usług w czasie wykonywania. Typy usług są deklarowane na poziomie manifestu, a nie na poziomie pakietu kodu. Tak więc w przypadku wielu pakietów kodu są one aktywowane, gdy system szuka jednego z zadeklarowanych typów usług.

Plik wykonywalny określony przez **punkt wejścia** jest zwykle długotrwałym hostem usługi. **SetupEntryPoint** to uprzywilejowany punkt wejścia, który jest uruchamiany z tymi samymi poświadczeniami co Service Fabric (zazwyczaj konto *LocalSystem* ) przed innym punktem wejścia.  Obecność oddzielnego punktu wejścia Instalatora pozwala uniknąć konieczności uruchamiania hosta usługi z wysokim poziomem uprawnień przez dłuższy czas. Plik wykonywalny określony przez **punkt wejścia** jest uruchamiany po pomyślnym zamknięciu **SetupEntryPoint** . Jeśli proces kiedykolwiek się zakończy lub ulegnie awarii, proces wynikający jest monitorowany i uruchamiany ponownie (od **SetupEntryPoint**).  

Typowymi scenariuszami używania **SetupEntryPoint** są uruchamianie plików wykonywalnych przed uruchomieniem usługi lub wykonywanie operacji z podniesionymi uprawnieniami. Na przykład:

* Konfigurowanie i Inicjowanie zmiennych środowiskowych wymaganych przez plik wykonywalny usługi. Nie jest to ograniczone tylko do plików wykonywalnych pisanych za pośrednictwem modeli programowania Service Fabric. Na przykład npm.exe potrzebuje pewnych zmiennych środowiskowych skonfigurowanych do wdrażania aplikacji node.js.
* Konfigurowanie kontroli dostępu przez zainstalowanie certyfikatów zabezpieczeń.

Aby uzyskać więcej informacji na temat konfigurowania SetupEntryPoint, zobacz [Konfigurowanie zasad dla punktu wejścia Instalatora usługi](service-fabric-application-runas-security.md)

**EnvironmentVariables** (nie ustawiono w poprzednim przykładzie) zawiera listę zmiennych środowiskowych ustawionych dla tego pakietu kodu. Zmienne środowiskowe można przesłaniać w programie, `ApplicationManifest.xml` Aby zapewnić różne wartości dla różnych wystąpień usługi. 

**Pakiet** danych (nie został ustawiony w poprzednim przykładzie) deklaruje folder **o nazwie,** który zawiera dowolne dane statyczne do użycia przez proces w czasie wykonywania.

**ConfigPackage** deklaruje folder **o nazwie,** który zawiera plik *Settings.xml* . Plik ustawień zawiera sekcje ustawień zdefiniowanych przez użytkownika, pary klucz-wartość, które proces odczytuje z powrotem w czasie wykonywania. W trakcie uaktualniania, jeśli tylko wersja **ConfigPackage** **version** została zmieniona, uruchomiony proces nie zostanie ponownie uruchomiony. Zamiast tego wywołanie zwrotne powiadamia proces, że ustawienia konfiguracji zostały zmienione, aby można je było ponownie załadować dynamicznie. Oto przykładowy plik *Settings.xml* :

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

**Punkt końcowy** usługi Service Fabric jest przykładem zasobu Service Fabric. Zasób Service Fabric można zadeklarować/zmienić bez zmiany skompilowanego kodu. Dostęp do Service Fabric zasobów, które są określone w manifeście usługi, można kontrolować za pomocą **zabezpieczeń** w manifeście aplikacji. Gdy zasób punktu końcowego jest zdefiniowany w manifeście usługi, Service Fabric przypisuje porty z zakresu portów aplikacji zastrzeżonej, gdy port nie jest jawnie określony. Przeczytaj więcej na temat [określania lub zastępowania zasobów punktu końcowego](service-fabric-service-manifest-resources.md).

 
> [!WARNING]
> Przez projektowanie portów statycznych nie należy nakładać się na zakres portów aplikacji określony w ClusterManifest. Jeśli określisz port statyczny, przypisz go poza zakresem portów aplikacji, w przeciwnym razie spowoduje to konflikty portów. Za pomocą programu Release 6.5 ZASTOSUJESZ pakietu CU2 zostanie **wyświetlone ostrzeżenie o kondycji** , gdy wykryjesz taki konflikt, ale zezwolisz na synchronizację wdrożenia z przesłanym zachowaniem 6,5. Jednak firma Microsoft może uniemożliwić wdrożenie aplikacji z następnych głównych wersji.
>

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Opisywanie aplikacji w ApplicationManifest.xml
Manifest aplikacji deklaratywnie opisuje typ i wersję aplikacji. Określa metadane kompozycji usługi, takie jak nazwy stabilne, schemat partycjonowania, liczba wystąpień/współczynnik replikacji, zasady zabezpieczeń/izolacji, ograniczenia umieszczania, zastąpień konfiguracji i typy usług składowych. Opisano również domeny równoważenia obciążenia, w których znajduje się aplikacja.

W ten sposób manifest aplikacji opisuje elementy na poziomie aplikacji i odwołuje się do co najmniej jednego manifestu usługi do redagowania typu aplikacji. Oto manifest aplikacji dla [przykładowej aplikacji do głosowania](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (i Oto kilka [bardziej szczegółowych przykładów](service-fabric-manifest-examples.md)):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
         <PlacementConstraints>(NodeType==NodeType0)</PlacementConstraints
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

Podobnie jak w przypadku manifestów usługi, atrybuty **wersji** są ciągami bez struktury i nie są analizowane przez system. Atrybuty wersji są również używane do wersji każdego składnika do uaktualnienia.

**Parametry** definiują parametry używane w ramach manifestu aplikacji. Wartości tych parametrów można podać podczas tworzenia wystąpienia aplikacji i mogą zastąpić ustawienia konfiguracji aplikacji lub usługi.  Wartość domyślna parametru jest używana, jeśli wartość nie zostanie zmieniona podczas tworzenia wystąpienia aplikacji. Aby dowiedzieć się, jak zarządzać różnymi parametrami aplikacji i usług dla poszczególnych środowisk, zobacz [Zarządzanie parametrami aplikacji w wielu środowiskach](service-fabric-manage-multiple-environment-app-configuration.md).

**ServiceManifestImport** zawiera odwołania do manifestów usługi tworzących ten typ aplikacji. Manifest aplikacji może zawierać wiele importów manifestu usługi, każdy z nich może być niezależnie w wersji. Zaimportowane manifesty usług określają, jakie typy usług są prawidłowe w ramach tego typu aplikacji. W ramach ServiceManifestImport zastąpisz wartości konfiguracyjne w Settings.xml i zmiennych środowiskowych w plikach ServiceManifest.xml. **Zasady** (nie ustawiane w poprzednim przykładzie) dla powiązania punktu końcowego, zabezpieczeń i dostępu oraz udostępniania pakietów można ustawić dla zaimportowanych manifestów usługi.  Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad zabezpieczeń dla aplikacji](service-fabric-application-runas-security.md).

**DefaultServices** deklaruje wystąpienia usługi, które są tworzone automatycznie za każdym razem, gdy aplikacja zostanie utworzona na podstawie tego typu aplikacji. Usługi domyślne są tylko wygodą i zachowują się jak normalne usługi w każdym przypadku, gdy zostały utworzone. Są one uaktualniane wraz z innymi usługami w wystąpieniu aplikacji i można je również usunąć. Manifest aplikacji może zawierać wiele domyślnych usług.

**Certyfikaty** (nie są ustawiane w poprzednim przykładzie) deklaruje certyfikaty używane do [konfiguracji punktów końcowych https](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) lub [Szyfruj wpisy tajne w manifeście aplikacji](service-fabric-application-secret-management.md).

**Ograniczenia umieszczania** to instrukcje określające, gdzie mają być uruchamiane usługi. Te instrukcje są dołączone do poszczególnych usług wybranych dla jednej lub wielu właściwości węzła. Aby uzyskać więcej informacji, zobacz temat [ograniczenia umieszczania i składnia właściwości węzła](./service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-property-syntax) .

**Zasady** (nie zostały ustawione w poprzednim przykładzie) opisują zbieranie dzienników, [domyślne zasady uruchamiania](service-fabric-application-runas-security.md)i [kondycji](service-fabric-health-introduction.md#health-policies), aby [security access](service-fabric-application-runas-security.md) ustawić na poziomie aplikacji, w tym czy usługi mają dostęp do Service Fabric środowiska uruchomieniowego.

> [!NOTE] 
> Domyślnie aplikacje Service Fabric mają dostęp do środowiska uruchomieniowego Service Fabric, w postaci punktu końcowego akceptującego żądania specyficzne dla aplikacji, a zmienne środowiskowe wskazujące ścieżki plików na hoście zawierającym pliki sieci szkieletowej i specyficzne dla aplikacji. Należy rozważyć wyłączenie tego dostępu, gdy aplikacja zawiera kod niezaufany (tj. kod, którego pochodzenie jest nieznany lub którego właściciel aplikacji nie ma być bezpiecznie wykonywany). Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania w zakresie zabezpieczeń w Service Fabric](service-fabric-best-practices-security.md#platform-isolation). 
>

**Podmioty** zabezpieczeń (nie są ustawiane w poprzednim przykładzie) opisują nazwy główne (użytkowników lub grup) wymagane do [uruchamiania usług i zabezpieczonych zasobów usług](service-fabric-application-runas-security.md).  W sekcjach **zasad** istnieją odwołania do podmiotów zabezpieczeń.





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Następne kroki
- Utwórz [pakiet aplikacji](service-fabric-package-apps.md) i przygotuj go do wdrożenia.
- [Wdrażaj i usuwaj aplikacje](service-fabric-deploy-remove-applications.md).
- [Konfigurowanie parametrów i zmiennych środowiskowych dla różnych wystąpień aplikacji](service-fabric-manage-multiple-environment-app-configuration.md).
- [Skonfiguruj zasady zabezpieczeń dla aplikacji](service-fabric-application-runas-security.md).
- [Skonfiguruj punkty końcowe HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Szyfruj wpisy tajne w manifeście aplikacji](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
