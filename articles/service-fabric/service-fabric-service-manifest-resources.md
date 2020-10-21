---
title: Określanie punktów końcowych usługi Service Fabric
description: Jak opisać zasoby punktu końcowego w manifeście usługi, w tym sposób konfigurowania punktów końcowych HTTPS
ms.topic: conceptual
ms.date: 09/16/2020
ms.custom: contperfq1
ms.openlocfilehash: 5e8f39fe25011d02b989614fdc6538cd92c12d4e
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92313564"
---
# <a name="specify-resources-in-a-service-manifest"></a>Określanie zasobów w manifeście usługi
## <a name="overview"></a>Omówienie
Service Fabric aplikacje i usługi są zdefiniowane i są używane przy użyciu plików manifestu. Więcej informacji na temat ServiceManifest.xml i ApplicationManifest.xml można znaleźć w temacie [Service Fabric manifesty aplikacji i usług](service-fabric-application-and-service-manifests.md).

Manifest usługi umożliwia deklarowanie lub zmianę zasobów używanych przez usługę, bez konieczności zmiany skompilowanego kodu. Service Fabric obsługuje konfigurację zasobów punktu końcowego dla usługi. Dostęp do zasobów określonych w manifeście usługi można kontrolować za pośrednictwem funkcji zabezpieczeń w manifeście aplikacji. Deklaracja zasobów pozwala na zmianę tych zasobów w czasie wdrażania, co oznacza, że usługa nie musi wprowadzać nowego mechanizmu konfiguracji. Definicja schematu dla pliku ServiceManifest.xml jest instalowana z zestawem SDK Service Fabric i narzędziami do *folderu C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*i jest udokumentowana w [dokumentacji schematu ServiceFabricServiceModel. xsd](service-fabric-service-model-schema.md).

## <a name="endpoints"></a>Punkty końcowe
Gdy zasób punktu końcowego jest zdefiniowany w manifeście usługi, Service Fabric przypisuje porty z zakresu portów aplikacji zastrzeżonej, gdy port nie jest jawnie określony. Na przykład poszukaj w punkcie końcowym *ServiceEndpoint1* określonym we fragmencie kodu manifestu dostarczonym po tym akapicie. Ponadto usługi mogą również zażądać określonego portu w zasobie. Do replik usługi uruchomionych w różnych węzłach klastra można przypisać różne numery portów, natomiast repliki usługi uruchomionej w tym samym węźle współużytkują port. Repliki usług mogą następnie używać tych portów w razie potrzeby replikacji i nasłuchiwania żądań klientów.

Po aktywowaniu usługi, która określa punkt końcowy https, Service Fabric ustawi wpis kontroli dostępu dla portu, powiąże określony certyfikat serwera z portem, a także przyznaje tożsamość, która jest uruchomiona przez usługę jako uprawnienia do klucza prywatnego certyfikatu. Przepływ aktywacji jest wywoływany przy każdym uruchomieniu Service Fabric lub po zmianie deklaracji certyfikatu aplikacji przy użyciu uaktualnienia. Certyfikat punktu końcowego będzie również monitorowany pod kątem zmian/odnowień, a uprawnienia będą okresowo stosowane ponownie w razie potrzeby.

Po zakończeniu usługi Service Fabric spowoduje wyczyszczenie wpisu kontroli dostępu punktu końcowego i usunięcie powiązania certyfikatu. Jednak wszelkie uprawnienia zastosowane do klucza prywatnego certyfikatu nie zostaną wyczyszczone.

> [!WARNING] 
> Przez projektowanie portów statycznych nie należy nakładać się na zakres portów aplikacji określony w ClusterManifest. Jeśli określisz port statyczny, przypisz go poza zakresem portów aplikacji, w przeciwnym razie spowoduje to konflikty portów. Za pomocą programu Release 6.5 ZASTOSUJESZ pakietu CU2 zostanie **wyświetlone ostrzeżenie o kondycji** , gdy wykryjesz taki konflikt, ale zezwolisz na synchronizację wdrożenia z przesłanym zachowaniem 6,5. Jednak firma Microsoft może uniemożliwić wdrożenie aplikacji z następnych głównych wersji.
>
> W wersji 7,0 zostanie **wyświetlone ostrzeżenie o kondycji** , gdy zostanie wykryte użycie zakresu portów aplikacji wykraczające poza HostingConfig:: ApplicationPortExhaustThresholdPercentage (domyślny 80%).
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

W przypadku wielu pakietów kodu w jednym pakiecie usługi, w sekcji **punkty końcowe** należy również odwołać się do pakietu kodu.  Jeśli na przykład **ServiceEndpoint2a** i **ServiceEndpoint2b** są punktami końcowymi z tego samego pakietu usługi, które odwołują się do różnych pakietów kodu, pakiet kodu odpowiadający każdemu punktowi końcowemu jest wyjaśniony w następujący sposób:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Zapoznaj się z tematem [konfigurowanie Reliable Services stanowych](service-fabric-reliable-services-configuration.md) , aby dowiedzieć się więcej na temat odwołań do punktów końcowych z pliku ustawień pakietu konfiguracji (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Przykład: Określanie punktu końcowego HTTP dla usługi
Poniższy manifest usługi definiuje jeden zasób punktu końcowego TCP i dwa zasoby punktu końcowego HTTP w &lt; elemencie Resources &gt; .

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

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Przykład: Określanie punktu końcowego HTTPS dla usługi
Protokół HTTPS zapewnia uwierzytelnianie serwera i jest również używany do szyfrowania komunikacji klient-serwer. Aby włączyć obsługę protokołu HTTPS w usłudze Service Fabric, określ protokół w sekcji *> punkty końcowe zasobów — > punkt końcowy* manifestu usługi, jak pokazano wcześniej dla punktu końcowego *ServiceEndpoint3*.

> [!NOTE]
> Nie można zmienić protokołu usługi podczas uaktualniania aplikacji. Jeśli zostanie ona zmieniona podczas uaktualniania, jest to istotna zmiana.
> 

> [!WARNING] 
> W przypadku korzystania z protokołu HTTPS nie należy używać tego samego portu i certyfikatu dla różnych wystąpień usługi (niezależnie od aplikacji) wdrożonych w tym samym węźle. Uaktualnienie dwóch różnych usług przy użyciu tego samego portu w różnych wystąpieniach aplikacji spowoduje niepowodzenie uaktualnienia. Aby uzyskać więcej informacji, zobacz [Uaktualnianie wielu aplikacji za pomocą punktów końcowych https ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Poniżej znajduje się przykład ApplicationManifest pokazujący konfigurację wymaganą dla punktu końcowego HTTPS. Certyfikat serwera/punktu końcowego można zadeklarować za pomocą odcisku palca lub nazwy pospolitej podmiotu, a wartość musi być podana. EndpointRef jest odwołaniem do EndpointResource w servicemanifest i którego protokół musi być ustawiony na protokół https. Można dodać więcej niż jeden EndpointCertificate.  

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

W przypadku klastrów systemu Linux magazyn **My** default jest domyślnie używany w folderze **/var/lib/sfcerts**.

Aby zapoznać się z przykładem pełnej aplikacji korzystającej z punktu końcowego HTTPS, zobacz [Dodawanie punktu końcowego HTTPS do usługi frontonu internetowego interfejsu API ASP.NET Core przy użyciu Kestrel](./service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest).

## <a name="port-acling-for-http-endpoints"></a>ACLing portów dla punktów końcowych HTTP
W Service Fabric automatycznie są określane punkty końcowe HTTP (S) listy ACL. **Nie** zostanie wykonana automatyczna ACLing, jeśli punkt końcowy nie ma skojarzonego z nim [SecurityAccessPolicy](service-fabric-assign-policy-to-endpoint.md) i Service Fabric jest skonfigurowany do uruchamiania przy użyciu konta z uprawnieniami administratora.

## <a name="overriding-endpoints-in-servicemanifestxml"></a>Zastępowanie punktów końcowych w ServiceManifest.xml

W ApplicationManifest Dodaj sekcję ResourceOverrides, która będzie częścią elementu równorzędnego do ConfigOverrides. W tej sekcji można określić przesłonięcie dla sekcji punktów końcowych w sekcji Resources (zasoby) określonej w manifeście usługi. Przesłanianie punktów końcowych jest obsługiwane w środowisku uruchomieniowym 5.7.217/SDK 2.7.217 i wyższym.

Aby przesłonić punkt końcowy w elemencie servicemanifest przy użyciu elementu applicationparameters, Zmień ApplicationManifest w taki sposób, aby:

W sekcji ServiceManifestImport Dodaj nową sekcję "ResourceOverrides".

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

W poniższej tabeli Dodaj następujące parametry:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Podczas wdrażania aplikacji można przekazać te wartości jako elementu applicationparameters.  Na przykład:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Uwaga: Jeśli wartość podana dla danego ApplicationParameter jest pusta, wracamy do wartości domyślnej podanej w elemencie servicemanifest dla odpowiadającego punktu Końcowegoname.

Na przykład:

Jeśli w określonym elemencie servicemanifest

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

Załóżmy, że wartość PORT1 i Protocol1 dla parametrów aplikacji ma wartość null lub jest pusta. Port zostanie ustalony przez sieć szkieletową, a protokół będzie TCP.

Załóżmy, że określono nieprawidłową wartość. Załóżmy, że port określił wartość ciągu "foo" zamiast int.  New-ServiceFabricApplication polecenie zakończy się niepowodzeniem z powodu błędu: `The override parameter with name 'ServiceEndpoint1' attribute 'Port1' in section 'ResourceOverrides' is invalid. The value specified is 'Foo' and required is 'int'.`

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak definiować punkty końcowe w manifeście usługi Service Fabric. Aby uzyskać bardziej szczegółowe przykłady, zobacz:

> [!div class="nextstepaction"]
> [Przykłady manifestu aplikacji i usługi](service-fabric-manifest-examples.md)

Aby zapoznać się z przewodnikiem tworzenia pakietów i wdrażania istniejącej aplikacji w klastrze Service Fabric, zobacz:

> [!div class="nextstepaction"]
> [Pakowanie i wdrażanie istniejącego pliku wykonywalnego do Service Fabric](service-fabric-deploy-existing-app.md)