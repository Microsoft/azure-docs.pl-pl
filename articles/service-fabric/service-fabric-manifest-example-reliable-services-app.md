---
title: Przykłady manifestu aplikacji niezawodnych usług
description: Dowiedz się, jak skonfigurować ustawienia manifestu aplikacji i usługi dla niezawodnych usług Service Fabric aplikacji.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: f40e54f5260f827f0b18c833d23d1f57b5ebc3a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84701102"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Przykłady manifestu aplikacji i usługi Reliable Services
Poniżej przedstawiono przykłady manifestów aplikacji i usług dla Service Fabric aplikacji z frontonem sieci Web ASP.NET Core i zapasowym zapleczem. Te przykłady przedstawiają, jakie ustawienia są dostępne i jak z nich korzystać. Te manifesty aplikacji i usług są oparte na Service Fabric manifestach [szybkiego startu programu .NET](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) .

Wyświetlane są następujące funkcje:

|Manifest|Funkcje|
|---|---|
|[Manifest aplikacji](#application-manifest)| [Zarządzanie zasobami](service-fabric-resource-governance.md), [Uruchamianie usługi jako konta administratora lokalnego](service-fabric-application-runas-security.md), [stosowanie zasad domyślnych do wszystkich pakietów kodu usługi](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages), [Tworzenie podmiotów nazw użytkowników i grup](service-fabric-application-runas-security.md), udostępnianie pakietu danych między wystąpieniami usługi, [przesłonięcie punktów końcowych usługi](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|Manifest usługi FrontEndService| [Uruchamianie skryptu podczas uruchamiania usługi](service-fabric-run-script-at-service-startup.md), [Definiowanie punktu końcowego HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|Manifest usługi BackEndService| [Zadeklaruj pakiet konfiguracji](service-fabric-application-and-service-manifests.md), [Zadeklaruj pakiet danych](service-fabric-application-and-service-manifests.md), [Skonfiguruj punkt końcowy](service-fabric-service-manifest-resources.md)| 

Aby uzyskać więcej informacji na temat określonych elementów XML, zobacz elementy [manifestu aplikacji](#application-manifest-elements), [elementy manifestu usługi VotingWeb](#votingweb-service-manifest-elements)oraz [elementy manifestu usługi VotingData](#votingdata-service-manifest-elements) .

## <a name="application-manifest"></a>Manifest aplikacji

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="Memory" DefaultValue="4084" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="Port" DefaultValue="8081" />
    <Parameter Name="Protocol" DefaultValue="tcp" />
    <Parameter Name="Type" DefaultValue="internal" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <!-- Override endpoints declared in the service manifest. -->
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="DataEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
      </Endpoints>
    </ResourceOverrides>

    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[Memory]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[Memory]" BlockIOWeight="[BlockIOWeight]" 
                                MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" 
                                MemorySwapInMB="[MemorySwapInMB]"/>

      <!-- Share the data package across multiple instances of the VotingData service-->
      <PackageSharingPolicy PackageRef="Data"/>

      <!-- Give read rights on the "DataEndpoint" endpoint to the Customer2 account.-->
      <SecurityAccessPolicy GrantRights="Read" PrincipalRef="Customer2" ResourceRef="DataEndpoint" ResourceType="Endpoint"/>         
    </Policies>
  </ServiceManifestImport>
  
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Run the setup entry point (defined in the imported service manifest) as the SetupAdminUser account 
      (declared in the following Principals section). -->
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      
    </Policies>

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
      </StatelessService>
    </Service>
  </DefaultServices>
  <!-- Define users and groups required to run the services and access resources.  Principals are used in the Policies section(s). -->
  <Principals>
    <!-- Declare a set of groups as security principals, which can be referenced in policies. Groups are useful if there are multiple users 
    for different service entry points and they need to have certain common privileges that are available at the group level. -->
    <Groups>
      <!-- Create a group that has administrator privileges. -->
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <!-- Declare a user and add the user to the Administrators system group. The SetupAdminUser account is used to run the 
      setup entry point of the VotingWebPkg code package (described in the preceding Policies section).-->
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
      <!-- Create a user. Local user accounts are created on the machines where the application is deployed. By default, these accounts 
      do not have the same names as those specified here. Instead, they are dynamically generated and have random passwords. -->
      <User Name="Customer1" >
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as a local user with the specified account name and password. Local user accounts are created on the machines 
      where the application is deployed. -->
      <User Name="Customer2" AccountType="LocalUser" AccountName="Customer1" Password="MyPassword">
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as NetworkService. -->
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <!-- Policies applied at the application level. -->
  <Policies>
    <!-- Specify a default user account for all code packages that don’t have a specific RunAsPolicy defined in 
    the ServiceManifestImport section(s). -->
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
    
  </Policies>
</ApplicationManifest>

```

## <a name="votingweb-service-manifest"></a>Manifest usługi VotingWeb

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
    <!-- A privileged entry point that by default runs with the same credentials as Service Fabric (typically the NetworkService account) before 
    any other entry point. Use the setup entry point to set system environment variables, give the account running the service (NETWORK SERVICE, by default) 
    access to a certificate's private key, or perform other setup tasks. In the application manifest, you can change the security permissions to run the startup script 
    under a local system account or an administrator account.  -->
    <SetupEntryPoint>
      <ExeHost>
        <!-- The setup script to run. -->
        <Program>Setup.bat</Program>
        <!-- Pass arguments to the script when it runs.-->
        <Arguments>MyValue</Arguments>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
        <!-- Warning! Do not use console redirection in a production application, only use it for local development and debugging. Redirects console output from the startup
        script to an output file in the application folder called "log" on the cluster node where the application is deployed and run. Also set the number of output files
        to retain and the maximum file size (in KB). -->
        <ConsoleRedirection FileRetentionCount="10" FileMaxSizeInKb="20480"/>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- Configure a HTTPS endpoint on port 443. This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

## <a name="votingdata-service-manifest"></a>Manifest usługi VotingData

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingDataPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="VotingDataType"  HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingData.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Declares a folder, named by the Name attribute, under PackageRoot that contains a Settings.xml file. This file contains sections of user-defined, 
  key-value pair settings that the process can read back at run time. During an upgrade, if only the ConfigPackage version has changed, 
  then the running process is not restarted. Instead, a callback notifies the process that configuration settings have changed so they can be reloaded dynamically. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Declares a folder, named by the Name attribute, under PackageRoot which contains static data files to be consumed by the process at run time. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- Define an internal (used for intra-application communication) TCP endpoint. Since no port is specified, one is created and assigned dynamically
           to the service.-->
      <Endpoint Name="DataEndpoint" Protocol="tcp" Type="Internal" />
    </Endpoints>
  </Resources>

</ServiceManifest>

```

## <a name="application-manifest-elements"></a>Elementy manifestu aplikacji
### <a name="applicationmanifest-element"></a>ApplicationManifest, element
W sposób deklaratywny opisano typ i wersję aplikacji. Co najmniej jeden manifest usługi składowej jest przywoływany do redagowania typu aplikacji. Ustawienia konfiguracji usług składowych można zastąpić przy użyciu sparametryzowanych ustawień aplikacji. Domyślne usługi, szablony usług, podmioty zabezpieczeń, zasady, konfigurację diagnostyki oraz certyfikaty można także deklarować na poziomie aplikacji. Aby uzyskać więcej informacji, zobacz [ApplicationManifest element](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Element parameters
Deklaruje parametry, które są używane w manifeście aplikacji. Wartość tych parametrów można podać podczas tworzenia wystąpienia aplikacji i może służyć do przesłaniania ustawień konfiguracji aplikacji lub usługi. Aby uzyskać więcej informacji, zobacz [element parameters](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Parameter — Element
Parametr aplikacji, który ma być używany w tym manifeście. Wartość parametru można zmienić podczas tworzenia wystąpienia aplikacji lub, jeśli nie zostanie podana wartość, zostanie użyta wartość domyślna. Aby uzyskać więcej informacji, zobacz [element parametru](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>ServiceManifestImport, element
Importuje manifest usługi utworzony przez dewelopera usługi. Manifest usługi musi zostać zaimportowany dla każdej usługi składowanej w aplikacji. Zastąpień konfiguracji i zasady można zadeklarować dla manifestu usługi. Aby uzyskać więcej informacji, zobacz [ServiceManifestImport element](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>ServiceManifestRef, element
Importuje manifest usługi przez odwołanie. Obecnie plik manifestu usługi (ServiceManifest.xml) musi być obecny w pakiecie kompilacji. Aby uzyskać więcej informacji, zobacz [ServiceManifestRef element](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="resourceoverrides-element"></a>ResourceOverrides, element
Określa zastąpienia zasobów dla punktów końcowych zadeklarowanych w zasobach manifestu usługi. Aby uzyskać więcej informacji, zobacz [ResourceOverrides element](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Element Endpoints
Punkty końcowe, które mają zostać przesłonięte. Aby uzyskać więcej informacji, zobacz [punkty końcowe elementu](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Element Endpoint
Punkt końcowy zadeklarowany w manifeście usługi, który ma zostać przesłonięty. Aby uzyskać więcej informacji, zobacz [element Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Element zasad
Opisuje zasady (powiązanie punktu końcowego, udostępnianie pakietów, uruchamianie jako i dostęp zabezpieczeń), które mają zostać zastosowane do zaimportowanego manifestu usługi. Aby uzyskać więcej informacji, zobacz [element zasad](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy, element
Definiuje zasady zarządzania zasobami stosowane na poziomie całego pakietu usługi. Aby uzyskać więcej informacji, zobacz [ServicePackageResourceGovernancePolicy element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy, element
Określa limity zasobów dla codepackage. Aby uzyskać więcej informacji, zobacz [ResourceGovernancePolicy element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>PackageSharingPolicy, element
Wskazuje, czy kod, konfiguracja lub pakiet danych mają być współużytkowane przez wystąpienia usług tego samego typu usługi. Aby uzyskać więcej informacji, zobacz [PackageSharingPolicy element](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>SecurityAccessPolicy, element
Przyznaje uprawnienia dostępu do podmiotu zabezpieczeń w ramach zasobu (takiego jak punkt końcowy) zdefiniowanego w manifeście usługi. Zwykle jest bardzo przydatne do kontrolowania i ograniczania dostępu do usług do różnych zasobów w celu minimalizacji zagrożeń bezpieczeństwa. Jest to szczególnie ważne, gdy aplikacja jest zbudowana z kolekcji usług z portalu Marketplace, które są opracowywane przez różnych deweloperów. Aby uzyskać więcej informacji, zobacz [SecurityAccessPolicy element](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>RunAsPolicy, element
Określa konto użytkownika lokalnego lub lokalne, na którym będzie uruchamiany pakiet kodu usługi. Konta domen są obsługiwane w przypadku wdrożeń systemu Windows Server, w których Azure Active Directory jest dostępna. Domyślnie aplikacje są uruchamiane w ramach konta, w ramach którego działa proces Fabric.exe. Aplikacje mogą również działać jako inne konta, które muszą być zadeklarowane w sekcji podmiotów zabezpieczeń. W przypadku zastosowania zasad RunAs do usługi, a manifest usługi deklaruje zasoby punktów końcowych przy użyciu protokołu HTTP, należy również określić SecurityAccessPolicy, aby upewnić się, że porty przystosowane do tych punktów końcowych są prawidłowo dostępne dla konta użytkownika RunAs, w ramach którego działa usługa. W przypadku punktu końcowego HTTPS trzeba również zdefiniować EndpointBindingPolicy, aby wskazać nazwę certyfikatu do zwrócenia klientowi. Aby uzyskać więcej informacji, zobacz [RunAsPolicy element](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>DefaultServices, element
Deklaruje wystąpienia usługi, które są tworzone automatycznie za każdym razem, gdy aplikacja zostanie utworzona na podstawie tego typu aplikacji. Aby uzyskać więcej informacji, zobacz [DefaultServices element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Element usługi
Deklaruje usługę, która ma zostać utworzona automatycznie podczas tworzenia wystąpienia aplikacji. Aby uzyskać więcej informacji, zobacz [element usługi](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>Klasy statefulservice, element
Definiuje usługę stanową. Aby uzyskać więcej informacji, zobacz [klasy statefulservice element](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>StatelessService, element
Definiuje usługę bezstanową. Aby uzyskać więcej informacji, zobacz [StatelessService element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Elementy główne
Opisuje podmioty zabezpieczeń (użytkowników, grupy) wymagane przez tę aplikację do uruchamiania usług i zabezpieczania zasobów. W sekcjach zasad istnieją odwołania do podmiotów zabezpieczeń. Aby uzyskać więcej informacji, zobacz [element Principals](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType) .

### <a name="groups-element"></a>Groups, element
Deklaruje zestaw grup jako podmioty zabezpieczeń, do których można odwoływać się w zasadach. Grupy są przydatne, jeśli istnieje wielu użytkowników dla różnych punktów wejścia usługi i że muszą mieć pewne typowe uprawnienia, które są dostępne na poziomie grupy. Aby uzyskać więcej informacji, zobacz [grupy element](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Group, element
Deklaruje grupę jako podmiot zabezpieczeń, do której można odwoływać się zasady. Aby uzyskać więcej informacji, zobacz [element Group](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Element Membership
 Aby uzyskać więcej informacji, zobacz [element Membership](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>Element systemowy
 Aby uzyskać więcej informacji, zobacz [element System](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement) .

### <a name="users-element"></a>Element users
Deklaruje zestaw użytkowników jako podmioty zabezpieczeń, do których można odwoływać się w zasadach. Aby uzyskać więcej informacji, zobacz [Użytkownicy — element](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Element User
Deklaruje użytkownika jako podmiot zabezpieczeń, do którego można się odwoływać w ramach zasad. Aby uzyskać więcej informacji, zobacz [element User](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>MemberOf, element
Można dodać użytkowników do dowolnej istniejącej grupy członkostwa, aby można było odziedziczyć wszystkie właściwości i ustawienia zabezpieczeń tej grupy członkostwa. Grupa członkostwa może służyć do zabezpieczania zasobów zewnętrznych, do których należy uzyskać dostęp za pomocą różnych usług lub tej samej usługi (na innym komputerze). Aby uzyskać więcej informacji, zobacz [memberOf element](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>Element systemowy
Grupa systemowa, do której zostanie dodany użytkownik.  Grupa systemowa musi być zdefiniowana w sekcji grup. Aby uzyskać więcej informacji, zobacz [element System](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement) .

### <a name="group-element"></a>Group, element
Grupa, do której ma zostać dodany użytkownik.  Grupa musi być zdefiniowana w sekcji grup. Aby uzyskać więcej informacji, zobacz [element Group](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Element zasad
Opisuje zasady (zbieranie dzienników, domyślne informacje o stanie Uruchom jako, kondycja i zabezpieczenia), które mają być stosowane na poziomie aplikacji. Aby uzyskać więcej informacji, zobacz [element zasad](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>DefaultRunAsPolicy, element
Określ domyślne konto użytkownika dla wszystkich pakietów kodu usługi, które nie mają określonego RunAsPolicy zdefiniowanego w sekcji ServiceManifestImport. Aby uzyskać więcej informacji, zobacz [DefaultRunAsPolicy element](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>Elementy manifestu usługi VotingWeb
### <a name="servicemanifest-element"></a>Element servicemanifest
W sposób deklaratywny opisano typ i wersję usługi. Zawiera listę niezależnie do uaktualnienia kod, konfigurację i pakiety danych, które razem tworzą pakiet usługi do obsługi jednego lub kilku typów usług. Określone są również zasoby, ustawienia diagnostyki i metadane usługi, takie jak typ usługi, właściwości kondycji i metryki równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [element Servicemanifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Element ServiceTypes
Definiuje, jakie typy usług są obsługiwane przez CodePackage w tym manifeście. W przypadku wystąpienia usługi dla jednego z tych typów usług wszystkie pakiety kodu zadeklarowane w tym manifeście są uaktywniane przez uruchomienie ich punktów wejścia. Typy usług są deklarowane na poziomie manifestu, a nie na poziomie pakietu kodu. Aby uzyskać więcej informacji, zobacz [ServiceTypes element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType, element
Opisuje typ usługi bezstanowej. Aby uzyskać więcej informacji, zobacz [StatelessServiceType element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage, element
Opisuje pakiet kodu, który obsługuje zdefiniowany typ usługi. W przypadku wystąpienia usługi dla jednego z tych typów usług wszystkie pakiety kodu zadeklarowane w tym manifeście są uaktywniane przez uruchomienie ich punktów wejścia. Procesy powstające powinny rejestrować obsługiwane typy usług w czasie wykonywania. W przypadku wielu pakietów kodu są one aktywowane, gdy system szuka jednego z zadeklarowanych typów usług. Aby uzyskać więcej informacji, zobacz [CodePackage element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>SetupEntryPoint, element
Uprzywilejowany punkt wejścia, który domyślnie jest uruchamiany z tymi samymi poświadczeniami co Service Fabric (zazwyczaj konto NETWORKSERVICE) przed innym punktem wejścia. Plik wykonywalny określony przez punkt wejścia jest zwykle długotrwałym hostem usługi. Obecność oddzielnego punktu wejścia Instalatora pozwala uniknąć konieczności uruchamiania hosta usługi z wysokim poziomem uprawnień przez dłuższy czas. Aby uzyskać więcej informacji, zobacz [SetupEntryPoint element](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost, element
 Aby uzyskać więcej informacji, zobacz [ExeHost element](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Element programu
Nazwa pliku wykonywalnego.  Na przykład "MySetup.bat" lub "MyServiceHost.exe". Aby uzyskać więcej informacji, zobacz [element programu](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Arguments, element
 Aby uzyskać więcej informacji, zobacz [element arguments](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Element workingfolder, element
Katalog roboczy procesu w pakiecie kodu w węźle klastra, w którym wdrażana jest aplikacja. Można określić trzy wartości: służbowe (domyślne), CodePackage lub CodeBase. Baza kodu określa, że katalog roboczy jest ustawiony na katalog, w którym plik EXE jest zdefiniowany w pakiecie kodu. CodePackage ustawia katalog roboczy jako katalog główny pakietu kodu bez względu na to, gdzie EXE jest zdefiniowany w katalogu pakietu kodu. Work ustawia unikatowy folder utworzony w węźle.  Ten folder jest taki sam dla całego wystąpienia aplikacji. Domyślnie katalog roboczy wszystkich procesów w aplikacji jest ustawiony na folder roboczy aplikacji. Jest to miejsce, w którym procesy mogą zapisywać dane. Nie zaleca się zapisywania danych w pakiecie kodu lub w bazie kodu, ponieważ te foldery mogą być współużytkowane przez różne wystąpienia aplikacji i mogły zostać usunięte. Aby uzyskać więcej informacji, zobacz [element workingfolder element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>ConsoleRedirection, element

> [!WARNING]
> Nie używaj przekierowania konsoli w aplikacji produkcyjnej, używaj go tylko do lokalnego projektowania i debugowania. Przekierowuje dane wyjściowe konsoli z skryptu uruchamiania do pliku wyjściowego w folderze aplikacji o nazwie "log" w węźle klastra, w którym aplikacja jest wdrażana i uruchamiana. Aby uzyskać więcej informacji, zobacz [ConsoleRedirection element](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>Element EntryPoint
Plik wykonywalny określony przez punkt wejścia jest zwykle długotrwałym hostem usługi. Obecność oddzielnego punktu wejścia Instalatora pozwala uniknąć konieczności uruchamiania hosta usługi z wysokim poziomem uprawnień przez dłuższy czas. Plik wykonywalny określony przez punkt wejścia jest uruchamiany po pomyślnym zamknięciu SetupEntryPoint. Proces wynikający z tego procesu jest monitorowany i uruchamiany ponownie (od SetupEntryPoint), jeśli kiedykolwiek się zakończy lub ulegnie awarii. Aby uzyskać więcej informacji, zobacz [element EntryPoint](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost, element
 Aby uzyskać więcej informacji, zobacz [ExeHost element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage, element
Deklaruje folder o nazwie w atrybucie Name, w obszarze PackageRoot, który zawiera plik Settings.xml. Ten plik zawiera sekcje ustawień zdefiniowanych przez użytkownika, par klucz-wartość, które proces może odczytywać z powrotem w czasie wykonywania. W trakcie uaktualniania, jeśli tylko wersja ConfigPackage została zmieniona, uruchomiony proces nie zostanie ponownie uruchomiony. Zamiast tego wywołanie zwrotne powiadamia proces, że ustawienia konfiguracji zostały zmienione, aby można je było ponownie załadować dynamicznie. Aby uzyskać więcej informacji, zobacz [ConfigPackage element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Element zasobów
Opisuje zasoby używane przez tę usługę, które można zadeklarować bez modyfikowania skompilowanego kodu i zmienione podczas wdrażania usługi. Dostęp do tych zasobów jest kontrolowany za pomocą sekcji podmioty zabezpieczeń i zasady manifestu aplikacji. Aby uzyskać więcej informacji, zobacz [element Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element Endpoints
Definiuje punkty końcowe dla usługi. Aby uzyskać więcej informacji, zobacz [punkty końcowe elementu](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element Endpoint
Punkt końcowy zadeklarowany w manifeście usługi, który ma zostać przesłonięty. Aby uzyskać więcej informacji, zobacz [element Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>Elementy manifestu usługi VotingData
### <a name="servicemanifest-element"></a>Element servicemanifest
W sposób deklaratywny opisano typ i wersję usługi. Zawiera listę niezależnie do uaktualnienia kod, konfigurację i pakiety danych, które razem tworzą pakiet usługi do obsługi jednego lub kilku typów usług. Określone są również zasoby, ustawienia diagnostyki i metadane usługi, takie jak typ usługi, właściwości kondycji i metryki równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [element Servicemanifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Element ServiceTypes
Definiuje, jakie typy usług są obsługiwane przez CodePackage w tym manifeście. W przypadku wystąpienia usługi dla jednego z tych typów usług wszystkie pakiety kodu zadeklarowane w tym manifeście są uaktywniane przez uruchomienie ich punktów wejścia. Typy usług są deklarowane na poziomie manifestu, a nie na poziomie pakietu kodu. Aby uzyskać więcej informacji, zobacz [ServiceTypes element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>StatefulServiceType, element
Opisuje typ usługi stanowej. Aby uzyskać więcej informacji, zobacz [StatefulServiceType element](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage, element
Opisuje pakiet kodu, który obsługuje zdefiniowany typ usługi. W przypadku wystąpienia usługi dla jednego z tych typów usług wszystkie pakiety kodu zadeklarowane w tym manifeście są uaktywniane przez uruchomienie ich punktów wejścia. Procesy powstające powinny rejestrować obsługiwane typy usług w czasie wykonywania. W przypadku wielu pakietów kodu są one aktywowane, gdy system szuka jednego z zadeklarowanych typów usług. Aby uzyskać więcej informacji, zobacz [CodePackage element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Element EntryPoint
Plik wykonywalny określony przez punkt wejścia jest zwykle długotrwałym hostem usługi. Obecność oddzielnego punktu wejścia Instalatora pozwala uniknąć konieczności uruchamiania hosta usługi z wysokim poziomem uprawnień przez dłuższy czas. Plik wykonywalny określony przez punkt wejścia jest uruchamiany po pomyślnym zamknięciu SetupEntryPoint. Proces wynikający z tego procesu jest monitorowany i uruchamiany ponownie (od SetupEntryPoint), jeśli kiedykolwiek się zakończy lub ulegnie awarii. Aby uzyskać więcej informacji, zobacz [element EntryPoint](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost, element
 Aby uzyskać więcej informacji, zobacz [ExeHost element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Element programu
Nazwa pliku wykonywalnego.  Na przykład "MySetup.bat" lub "MyServiceHost.exe". Aby uzyskać więcej informacji, zobacz [element programu](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Element workingfolder, element
Katalog roboczy procesu w pakiecie kodu w węźle klastra, w którym wdrażana jest aplikacja. Można określić trzy wartości: służbowe (domyślne), CodePackage lub CodeBase. Baza kodu określa, że katalog roboczy jest ustawiony na katalog, w którym plik EXE jest zdefiniowany w pakiecie kodu. CodePackage ustawia katalog roboczy jako katalog główny pakietu kodu bez względu na to, gdzie EXE jest zdefiniowany w katalogu pakietu kodu. Work ustawia unikatowy folder utworzony w węźle.  Ten folder jest taki sam dla całego wystąpienia aplikacji. Domyślnie katalog roboczy wszystkich procesów w aplikacji jest ustawiony na folder roboczy aplikacji. Jest to miejsce, w którym procesy mogą zapisywać dane. Nie zaleca się zapisywania danych w pakiecie kodu lub w bazie kodu, ponieważ te foldery mogą być współużytkowane przez różne wystąpienia aplikacji i mogły zostać usunięte. Aby uzyskać więcej informacji, zobacz [element workingfolder element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage, element
Deklaruje folder o nazwie w atrybucie Name, w obszarze PackageRoot, który zawiera plik Settings.xml. Ten plik zawiera sekcje ustawień zdefiniowanych przez użytkownika, par klucz-wartość, które proces może odczytywać z powrotem w czasie wykonywania. W trakcie uaktualniania, jeśli tylko wersja ConfigPackage została zmieniona, uruchomiony proces nie zostanie ponownie uruchomiony. Zamiast tego wywołanie zwrotne powiadamia proces, że ustawienia konfiguracji zostały zmienione, aby można je było ponownie załadować dynamicznie. Aby uzyskać więcej informacji, zobacz [ConfigPackage element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Element DataPackage
Deklaruje folder o nazwie przez atrybut Name w obszarze PackageRoot, który zawiera pliki danych statycznych, które mają być używane przez proces w czasie wykonywania. Service Fabric odzyskuje wszystkie exe i DLLHOSTs określone na hoście i pakietach pomocy technicznej, gdy zostaną uaktualnione dowolne pakiety danych wymienione w manifeście usługi. Aby uzyskać więcej informacji, zobacz [element DataPackage](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Element zasobów
Opisuje zasoby używane przez tę usługę, które można zadeklarować bez modyfikowania skompilowanego kodu i zmienione podczas wdrażania usługi. Dostęp do tych zasobów jest kontrolowany za pomocą sekcji podmioty zabezpieczeń i zasady manifestu aplikacji. Aby uzyskać więcej informacji, zobacz [element Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element Endpoints
Definiuje punkty końcowe dla usługi. Aby uzyskać więcej informacji, zobacz [punkty końcowe elementu](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element Endpoint
Punkt końcowy zadeklarowany w manifeście usługi, który ma zostać przesłonięty. Aby uzyskać więcej informacji, zobacz [element Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

