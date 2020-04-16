---
title: Określanie punktów końcowych usługi sieci szkieletowej usług
description: Jak opisać zasoby punktu końcowego w manifeście usługi, w tym jak skonfigurować punkty końcowe HTTPS
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 88e71d15829e68bde635f5b4d40224b8fa914f40
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417591"
---
# <a name="specify-resources-in-a-service-manifest"></a>Określanie zasobów w manifeście usługi
## <a name="overview"></a>Omówienie
Manifest usługi umożliwia zasoby, które są używane przez usługę, które mają być zadeklarowane lub zmienione, bez zmiany skompilowanego kodu. Usługa Sieci szkieletowej obsługuje konfigurację zasobów punktu końcowego dla usługi. Dostęp do zasobów, które są określone w manifeście usługi mogą być kontrolowane za pośrednictwem SecurityGroup w manifeście aplikacji. Deklaracja zasobów umożliwia te zasoby, które mają być zmieniane w czasie wdrażania, co oznacza, że usługa nie musi wprowadzać nowy mechanizm konfiguracji. Definicja schematu pliku ServiceManifest.xml jest instalowana z zestawem SDK sieci szkieletowej usług i narzędziami do *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Punkty końcowe
Gdy zasób punktu końcowego jest zdefiniowany w manifeście usługi, usługa Sieci szkieletowej przypisuje porty z zakresu portów aplikacji zastrzeżonych, gdy port nie jest jawnie określony. Na przykład spójrz na punkt końcowy *ServiceEndpoint1* określony w fragmentie manifestu podanym po tym akapicie. Ponadto usługi można również zażądać określonego portu w zasobie. Replikom usługi uruchomionym w różnych węzłach klastra można przypisać różne numery portów, podczas gdy repliki usługi uruchomionej w tym samym węźle współużytkują port. Repliki usługi można następnie użyć tych portów, zgodnie z potrzebami do replikacji i nasłuchiwania żądań klientów.

Po aktywowaniu usługi, która określa punkt końcowy https, sieć szkieletowa usług ustawi wpis kontroli dostępu dla portu, powiąże określony certyfikat serwera z portem, a także udzieli tożsamości, że usługa jest uruchomiona jako uprawnienia do klucza prywatnego certyfikatu. Przepływ aktywacji jest wywoływany przy każdym uruchomieniu sieci szkieletowej usług lub gdy deklaracja certyfikatu aplikacji jest zmieniana za pomocą uaktualnienia. Certyfikat punktu końcowego będzie również monitorowany pod kątem zmian/odnowień, a uprawnienia będą okresowo ponownie stosowane w razie potrzeby.

Po zakończeniu usługi sieci szkieletowej usług czyści wpis kontroli dostępu punktu końcowego i usunie powiązanie certyfikatu. Jednak wszelkie uprawnienia zastosowane do klucza prywatnego certyfikatu nie zostaną wyczyszczone.

> [!WARNING] 
> Zgodnie z projektem porty statyczne nie powinny pokrywać się z zakresem portów aplikacji określonym w ClusterManifest. Jeśli określisz port statyczny, przypisz go poza zakresem portów aplikacji, w przeciwnym razie spowoduje to konflikty portów. Wraz z wersją 6.5CU2 wydamy **ostrzeżenie zdrowotne,** gdy wykryjemy taki konflikt, ale pozwolimy, aby wdrożenie było kontynuowane z zachowaniem wysłanego 6.5. Możemy jednak uniemożliwić wdrożenie aplikacji z następnych głównych wersji.
>
> Wraz z wydaniem 7.0 wydamy **ostrzeżenie o kondycji,** gdy wykryjemy użycie zakresu portów aplikacji wykracza poza HostingConfig::ApplicationPortExhaustThresholdPercentage (domyślnie 80%).
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Jeśli istnieje wiele pakietów kodu w jednym pakiecie usługi, pakiet kodu również musi się odwoływać w sekcji **Punkty końcowe.**  Na przykład jeśli **ServiceEndpoint2a** i **ServiceEndpoint2b** są punktami końcowymi z tego samego pakietu usług odwołujących się do różnych pakietów kodu, pakiet kodu odpowiadający każdemu punktowi końcowemu jest wyjaśniony w następujący sposób:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Aby dowiedzieć się więcej o odwoływaniu się do punktów końcowych, zapoznaj się z [artykułem Konfigurowanie stanowych usług niezawodnych](service-fabric-reliable-services-configuration.md) w pliku (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Przykład: określanie punktu końcowego HTTP dla usługi
Poniższy manifest usługi definiuje jeden zasób punktu końcowego &lt;TCP i dwa zasoby punktu końcowego HTTP w elemencie Zasoby.&gt;

Punkty końcowe HTTP są automatycznie ACL'd przez sieci szkieletowej usług.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
      <Endpoint Name="ServiceEndpoint4" Protocol="https" Port="14023"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Przykład: określanie punktu końcowego HTTPS dla usługi
Protokół HTTPS zapewnia uwierzytelnianie serwera i jest również używany do szyfrowania komunikacji klient-serwer. Aby włączyć protokół HTTPS w usłudze sieci szkieletowej usług, należy określić protokół w sekcji *Resources -> Endpoints -> Endpoint* manifestu usługi, jak pokazano wcześniej dla punktu końcowego *ServiceEndpoint3*.

> [!NOTE]
> Nie można zmienić protokołu usługi podczas uaktualniania aplikacji. Jeśli zostanie zmieniona podczas uaktualniania, jest to zmiana przełomowa.
> 

> [!WARNING] 
> Korzystając z protokołu HTTPS, nie należy używać tego samego portu i certyfikatu dla różnych wystąpień usługi (niezależnie od aplikacji) wdrożonych w tym samym węźle. Uaktualnienie dwóch różnych usług przy użyciu tego samego portu w różnych wystąpieniach aplikacji spowoduje niepowodzenie uaktualnienia. Aby uzyskać więcej informacji, zobacz [Uaktualnianie wielu aplikacji za pomocą punktów końcowych HTTPS ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Oto przykład ApplicationManifest demonstrujące konfigurację wymaganą dla punktu końcowego HTTPS. Certyfikat serwera/punktu końcowego może być zadeklarowany przez odcisk palca lub nazwę pospolitą podmiotu i należy podać wartość. EndpointRef jest odwołaniem do endpointResource w ServiceManifest, którego protokół musi być ustawiony na protokół "https". Można dodać więcej niż jeden certyfikat endpointcertificate.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint3"/>
      <EndpointBindingPolicy CertificateRef="SslCertByCN" EndpointRef="ServiceEndpoint4"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="SslCertByTP" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
    <EndpointCertificate Name="SslCertByCN" X509FindType="FindBySubjectName" X509FindValue="ServiceFabric-EndpointCertificateBinding-Test" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

W przypadku klastrów systemu Linux **domyślnie** w folderze /var/lib/sfcerts domyślnie znajduje się folder **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Zastępowanie punktów końcowych w pliku ServiceManifest.xml

W ApplicationManifest dodać ResourceOverrides sekcji, która będzie równorzędne do ConfigOverrides sekcji. W tej sekcji można określić zastąpienie sekcji Punkty końcowe w sekcji zasobów określonych w manifeście Usługa. Zastępowanie punktów końcowych jest obsługiwane w czasie wykonywania 5.7.217/SDK 2.7.217 i nowszym.

Aby zastąpić program EndPoint w servicemanifest przy użyciu parametrów ApplicationParameters, zmień parametr ApplicationManifest w następujący sposób:

W servicemanifestImport sekcji dodaj nową sekcję "ResourceOverrides".

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

W parametrach dodaj poniżej:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Podczas wdrażania aplikacji można przekazać w tych wartościach jako ApplicationParameters.  Przykład:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Uwaga: Jeśli wartości dla ApplicationParameters jest pusty, wracamy do wartości domyślnej podanej w ServiceManifest dla odpowiedniego EndPointName.

Przykład:

Jeśli w ServiceManifest określono

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

A wartość Port1 i Protocol1 dla parametrów aplikacji jest zerowa lub pusta. O porcie nadal decydują ServiceFabric. I protokół będzie tcp.

Załóżmy, że określisz niewłaściwą wartość. Podobnie jak w przypadku portu określono wartość ciągu "Foo" zamiast int.  New-ServiceFabricApplication polecenie zakończy się niepowodzeniem z powodu błędu: Parametr zastępowania o nazwie "ServiceEndpoint1" atrybut "Port1" w sekcji "ResourceOverrides" jest nieprawidłowy. Określona wartość to "Foo" i wymagana jest "int".
