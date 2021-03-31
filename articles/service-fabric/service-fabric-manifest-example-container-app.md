---
title: Przykłady manifestu aplikacji kontenera Service Fabric platformy Azure
description: Dowiedz się, jak skonfigurować ustawienia manifestu aplikacji i usługi dla wielokontenerowej aplikacji Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 5efdbd17db20e69ace33b379ddbb99b2c4a20e69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84701170"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Przykłady manifestu wielokontenerowej aplikacji i usługi
Poniżej przedstawiono przykłady manifestów aplikacji i usług dla wielokontenerowej aplikacji Service Fabric. Te przykłady przedstawiają, jakie ustawienia są dostępne i jak z nich korzystać. Te manifesty aplikacji i usług są oparte na [przykładowych manifestach kontenera systemu Windows Server 2016](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows) .

Wyświetlane są następujące funkcje:

|Manifest|Funkcje|
|---|---|
|[Manifest aplikacji](#application-manifest)| [Zastąp zmienne środowiskowe](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [Skonfiguruj mapowanie portów między hostami](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery), [Skonfiguruj uwierzytelnianie rejestru kontenerów](service-fabric-get-started-containers.md#configure-container-repository-authentication), [Zarządzanie zasobami](service-fabric-resource-governance.md), [Ustaw tryb izolacji](service-fabric-get-started-containers.md#configure-isolation-mode), [Określ obrazy kontenerów właściwych dla kompilacji systemu operacyjnego](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[Manifest usługi FrontEndService](#frontendservice-service-manifest)| [Ustaw zmienne środowiskowe](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [Skonfiguruj punkt końcowy](service-fabric-get-started-containers.md#configure-communication), Przekaż polecenia do kontenera, [zaimportuj certyfikat do kontenera](service-fabric-securing-containers.md)| 
|[Manifest usługi BackEndService](#backendservice-service-manifest)|[Ustawianie zmiennych środowiskowych](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [konfigurowanie punktu końcowego](service-fabric-get-started-containers.md#configure-communication), [Konfigurowanie sterownika woluminu](service-fabric-containers-volume-logging-drivers.md)| 

Aby uzyskać więcej informacji na temat określonych elementów XML, zobacz elementy [manifestu aplikacji](#application-manifest-elements), [elementy manifestu usługi FrontEndService](#frontendservice-service-manifest-elements)oraz [elementy manifestu usługi BackEndService](#backendservice-service-manifest-elements) .

## <a name="application-manifest"></a>Manifest aplikacji

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="BackEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="FrontEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="MaximumIOps" DefaultValue="20"/>
    <Parameter Name="MemoryFront" DefaultValue="4084" />
    <Parameter Name="MemoryBack" DefaultValue="2048" />
    <Parameter Name="CertThumbprint" DefaultValue=""/>
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="BackEndServicePkg" ServiceManifestVersion="1.0.0" />    
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryFront]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[MemoryFront]" BlockIOWeight="[BlockIOWeight]" MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" MemorySwapInMB="[MemorySwapInMB]"/>
      
      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
        
        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>
        
        <!-- This binds the port the container is listening on (8905 in this sample) to an endpoint resource named "BackEndServiceTypeEndpoint", which is defined in the service manifest.  -->
        <PortBinding ContainerPort="8905" EndpointRef="BackEndServiceTypeEndpoint"/>
        
        <!-- Configure the Azure Files volume plugin.  Bind the source folder on the host VM or a remote share to the destination folder within the running container. -->
        <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
          <!-- Driver options to be passed to driver. The Azure Files volume plugin supports the following driver options:
            shareName (the Azure Files file share that provides the volume for the container), storageAccountName (the Azure storage account
            that contains the Azure Files file share), storageAccountKey (Access key for the Azure storage account that contains the Azure Files file share).
            These three driver options are required. -->
          <DriverOption Name="shareName" Value="" />
          <DriverOption Name="storageAccountName" Value="MY-STORAGE-ACCOUNT-NAME" />
          <DriverOption Name="storageAccountKey" Value="MY-STORAGE-ACCOUNT-KEY" />
        </Volume>
        
        <!-- Windows Server containers may not be compatible across different versions of the OS.  You can specify multiple OS images per container and tag 
        them with the build versions of the OS. Get the build version of the OS by running "winver" at a Windows command prompt. -->
        <ImageOverrides>
          <!-- If the underlying OS is build version 16299 (Windows Server version 1709), Service Fabric picks the container image tagged with Os="16299". -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_1709" Os="16299" />
          
          <!-- An untagged container image is assumed to work across all versions of the OS and overrides the image specified in the service manifest. -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_default" />          
        </ImageOverrides>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <!-- Policies to be applied to the imported service manifest. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    
    <!-- This enables you to provide different values for environment variables when creating a FrontEndService
         Theses environment variables are declared in the FrontEndServiceType service manifest-->
    <EnvironmentOverrides CodePackageRef="Code">
      <EnvironmentVariable Name="BackendServiceName" Value="Container.Application/BackEndService"/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentOverrides>
    
    <!-- This policy maps the  port of the container (80) to the endpoint declared in the service, 
         FrontEndServiceTypeEndpoint which is exposed as port 80 on the host-->    
    <Policies>

      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryBack]"/>

      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">

        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>

        <!-- Binds an endpoint resource (declared in the service manifest) to the exposed container port. -->
        <PortBinding ContainerPort="80" EndpointRef="FrontEndServiceTypeEndpoint"/>

        <!-- Import a certificate into the container.  The certificate must be installed in the LocalMachine store of all the cluster nodes.
          When the application starts, the runtime reads the certificate and generates a PFX file and password (on Windows) or a PEM file (on Linux).
          The PFX file and password are accessible in the container using the Certificates_ServicePackageName_CodePackageName_CertName_PFX and 
          Certificates_ServicePackageName_CodePackageName_CertName_Password environment variables. The PEM file is accessible in the container using the 
          Certificates_ServicePackageName_CodePackageName_CertName_PEM and Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey environment variables.-->
        <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[CertThumbprint]" />

        <!-- If the certificate is already in PFX or PEM form, you can create a data package inside your application and reference that certificate here. -->
        <CertificateRef Name="MyCert2" DataPackageRef="Data" DataPackageVersion="1.0.0" RelativePath="MyCert2.PFX" Password="ENCRYPTED-PASSWORD" IsPasswordEncrypted="true"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
        
    <Service Name="FrontEndService" >
      <StatelessService ServiceTypeName="FrontEndServiceType" InstanceCount="[FrontEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
        <Service Name="BackEndService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="BackEndServiceType" InstanceCount="[BackEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="frontendservice-service-manifest"></a>Manifest usługi FrontEndService

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="FrontEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricfrontendservice:v1</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container or exe.  These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="IsContainer" Value=""/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Data package is the contents of the Data directory under PackageRoot that contains an 
       independently-updateable and versioned static data that's consumed by the process at runtime. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme and port 80 -->
      <Endpoint Name="FrontEndServiceTypeEndpoint" UriScheme="http" Port="80"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="backendservice-service-manifest"></a>Manifest usługi BackEndService

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="BackEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricbackendservice:v1</ImageName>
        
        <!-- Pass comma delimited commands to your container. -->
        <Commands> dotnet, myproc.dll, 5 </Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container. These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the host port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme. In this case since no port is specified, one is created and assigned dynamically
           to the service. This dynamically assigned host port is mapped to the container port (8905 in this sample),
            which was specified in the application manifest.-->
      <Endpoint Name="BackEndServiceTypeEndpoint" UriScheme="http" />
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

### <a name="policies-element"></a>Element zasad
Opisuje zasady (powiązanie punktu końcowego, udostępnianie pakietów, uruchamianie jako i dostęp zabezpieczeń), które mają zostać zastosowane do zaimportowanego manifestu usługi. Aby uzyskać więcej informacji, zobacz [element zasad](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy, element
Definiuje zasady zarządzania zasobami stosowane na poziomie całego pakietu usługi. Aby uzyskać więcej informacji, zobacz [ServicePackageResourceGovernancePolicy element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy, element
Określa limity zasobów dla pakietu kodu. Aby uzyskać więcej informacji, zobacz [ResourceGovernancePolicy element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>ContainerHostPolicies, element
Określa zasady dotyczące aktywowania hostów kontenerów. Aby uzyskać więcej informacji, zobacz [ContainerHostPolicies element](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>RepositoryCredentials, element
Poświadczenia repozytorium obrazu kontenera do ściągania obrazów z programu. Aby uzyskać więcej informacji, zobacz [RepositoryCredentials element](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>Portbinding — element
Określa zasób punktu końcowego, który ma zostać powiązany z udostępnionym portem kontenera. Aby uzyskać więcej informacji, zobacz [portbinding — element](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Element woluminu
Określa wolumin, który ma zostać powiązany z kontenerem. Aby uzyskać więcej informacji, zobacz [element Volume](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>DriverOption, element
Opcje sterownika, które mają zostać przesłane do sterownika. Aby uzyskać więcej informacji, zobacz [DriverOption element](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>ImageOverrides, element
Kontenery systemu Windows Server mogą być niezgodne z różnymi wersjami systemu operacyjnego.  Można określić wiele obrazów systemu operacyjnego na kontener i oznaczyć je za pomocą wersji kompilacji systemu operacyjnego. Pobierz wersję kompilacji systemu operacyjnego, uruchamiając polecenie "winver" w wierszu polecenia systemu Windows. Jeśli podstawowy system operacyjny jest w wersji 16299 (system Windows Server w wersji 1709), Service Fabric wybiera obraz kontenera oznaczony przy użyciu systemu operacyjnego = "16299". Założono, że obraz kontenera z nieoznakowanym założenia działa we wszystkich wersjach systemu operacyjnego i zastępuje obraz określony w manifeście usługi. Aby uzyskać więcej informacji, zobacz [ImageOverrides element](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Element obrazu
Obraz kontenera odpowiadający numerowi wersji kompilacji systemu operacyjnego do uruchomienia. Jeśli atrybut systemu operacyjnego nie zostanie określony, zakłada się, że obraz kontenera działa we wszystkich wersjach systemu operacyjnego i zastąpi obraz określony w manifeście usługi. Aby uzyskać więcej informacji, zobacz [element obrazu](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)

### <a name="environmentoverrides-element"></a>EnvironmentOverrides, element
 Aby uzyskać więcej informacji, zobacz [EnvironmentOverrides element](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>Zmiennych środowiskowych, element
Zmienna środowiskowa. Aby uzyskać więcej informacji, zobacz [zmiennych środowiskowych element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="certificateref-element"></a>CertificateRef, element
Określa informacje o certyfikacie x509, który ma być narażony na środowisko kontenera. Certyfikat należy zainstalować w magazynie LocalMachine wszystkich węzłów klastra.
Po uruchomieniu aplikacji środowisko uruchomieniowe odczytuje certyfikat i generuje plik PFX oraz hasło (w systemie Windows) lub plik PEM (w systemie Linux).
Plik PFX i hasło są dostępne w kontenerze przy użyciu zmiennych środowiskowych Certificates_ServicePackageName_CodePackageName_CertName_PFX i Certificates_ServicePackageName_CodePackageName_CertName_Password. Plik PEM jest dostępny w kontenerze przy użyciu zmiennych środowiskowych Certificates_ServicePackageName_CodePackageName_CertName_PEM i Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey. Aby uzyskać więcej informacji, zobacz [CertificateRef element](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>DefaultServices, element
Deklaruje wystąpienia usługi, które są tworzone automatycznie za każdym razem, gdy aplikacja zostanie utworzona na podstawie tego typu aplikacji. Aby uzyskać więcej informacji, zobacz [DefaultServices element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Element usługi
Deklaruje usługę, która ma zostać utworzona automatycznie podczas tworzenia wystąpienia aplikacji. Aby uzyskać więcej informacji, zobacz [element usługi](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statelessservice-element"></a>StatelessService, element
Definiuje usługę bezstanową. Aby uzyskać więcej informacji, zobacz [StatelessService element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>Elementy manifestu usługi FrontEndService
### <a name="servicemanifest-element"></a>Element servicemanifest
W sposób deklaratywny opisano typ i wersję usługi. Zawiera listę niezależnie do uaktualnienia kod, konfigurację i pakiety danych, które razem tworzą pakiet usługi do obsługi jednego lub kilku typów usług. Określone są również zasoby, ustawienia diagnostyki i metadane usługi, takie jak typ usługi, właściwości kondycji i metryki równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [element Servicemanifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Element ServiceTypes
Definiuje, jakie typy usług są obsługiwane przez CodePackage w tym manifeście. W przypadku wystąpienia usługi dla jednego z tych typów usług wszystkie pakiety kodu zadeklarowane w tym manifeście są uaktywniane przez uruchomienie ich punktów wejścia. Typy usług są deklarowane na poziomie manifestu, a nie na poziomie pakietu kodu. Aby uzyskać więcej informacji, zobacz [ServiceTypes element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType, element
Opisuje typ usługi bezstanowej. Aby uzyskać więcej informacji, zobacz [StatelessServiceType element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage, element
Opisuje pakiet kodu, który obsługuje zdefiniowany typ usługi. W przypadku wystąpienia usługi dla jednego z tych typów usług wszystkie pakiety kodu zadeklarowane w tym manifeście są uaktywniane przez uruchomienie ich punktów wejścia. Procesy powstające powinny rejestrować obsługiwane typy usług w czasie wykonywania. W przypadku wielu pakietów kodu są one aktywowane, gdy system szuka jednego z zadeklarowanych typów usług. Aby uzyskać więcej informacji, zobacz [CodePackage element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Element EntryPoint
Plik wykonywalny określony przez punkt wejścia jest zwykle długotrwałym hostem usługi. Obecność oddzielnego punktu wejścia Instalatora pozwala uniknąć konieczności uruchamiania hosta usługi z wysokim poziomem uprawnień przez dłuższy czas. Plik wykonywalny określony przez punkt wejścia jest uruchamiany po pomyślnym zamknięciu SetupEntryPoint. Proces wynikający z tego procesu jest monitorowany i uruchamiany ponownie (od SetupEntryPoint), jeśli kiedykolwiek się zakończy lub ulegnie awarii. Aby uzyskać więcej informacji, zobacz [element EntryPoint](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost, element
 Aby uzyskać więcej informacji, zobacz [ContainerHost element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName — element
Repozytorium i obraz w [https://hub.docker.com](https://hub.docker.com) lub Azure Container Registry. Aby uzyskać więcej informacji, zobacz [element ImageName](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables, element
Przekaż zmienne środowiskowe do kontenera lub exe.  Aby uzyskać więcej informacji, zobacz [EnvironmentVariables element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Zmiennych środowiskowych, element
Zmienna środowiskowa. Aby uzyskać więcej informacji, zobacz [zmiennych środowiskowych element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage, element
Deklaruje folder o nazwie, który zawiera plik Settings.xml. Ten plik zawiera sekcje ustawień zdefiniowanych przez użytkownika, par klucz-wartość, które proces może odczytywać z powrotem w czasie wykonywania. W trakcie uaktualniania, jeśli tylko wersja ConfigPackage została zmieniona, uruchomiony proces nie zostanie ponownie uruchomiony. Zamiast tego wywołanie zwrotne powiadamia proces, że ustawienia konfiguracji zostały zmienione, aby można je było ponownie załadować dynamicznie. Aby uzyskać więcej informacji, zobacz [ConfigPackage element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Element DataPackage
Deklaruje folder o nazwie, który zawiera pliki danych statycznych. Service Fabric odzyskuje wszystkie exe i DLLHOSTs określone na hoście i pakietach pomocy technicznej, gdy zostaną uaktualnione dowolne pakiety danych wymienione w manifeście usługi. Aby uzyskać więcej informacji, zobacz [element DataPackage](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Element zasobów
Opisuje zasoby używane przez tę usługę, które można zadeklarować bez modyfikowania skompilowanego kodu i zmienione podczas wdrażania usługi. Dostęp do tych zasobów jest kontrolowany za pomocą sekcji podmioty zabezpieczeń i zasady manifestu aplikacji. Aby uzyskać więcej informacji, zobacz [element Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element Endpoints
Definiuje punkty końcowe dla usługi. Aby uzyskać więcej informacji, zobacz [punkty końcowe elementu](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element Endpoint
Aby uzyskać więcej informacji, zobacz [element Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>Elementy manifestu usługi BackEndService
### <a name="servicemanifest-element"></a>Element servicemanifest
W sposób deklaratywny opisano typ i wersję usługi. Zawiera listę niezależnie do uaktualnienia kod, konfigurację i pakiety danych, które razem tworzą pakiet usługi do obsługi jednego lub kilku typów usług. Określone są również zasoby, ustawienia diagnostyki i metadane usługi, takie jak typ usługi, właściwości kondycji i metryki równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [element Servicemanifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Element ServiceTypes
Definiuje, jakie typy usług są obsługiwane przez CodePackage w tym manifeście. W przypadku wystąpienia usługi dla jednego z tych typów usług wszystkie pakiety kodu zadeklarowane w tym manifeście są uaktywniane przez uruchomienie ich punktów wejścia. Typy usług są deklarowane na poziomie manifestu, a nie na poziomie pakietu kodu. Aby uzyskać więcej informacji, zobacz [ServiceTypes element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType, element
Opisuje typ usługi bezstanowej. Aby uzyskać więcej informacji, zobacz [StatelessServiceType element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage, element
Opisuje pakiet kodu, który obsługuje zdefiniowany typ usługi. W przypadku wystąpienia usługi dla jednego z tych typów usług wszystkie pakiety kodu zadeklarowane w tym manifeście są uaktywniane przez uruchomienie ich punktów wejścia. Procesy powstające powinny rejestrować obsługiwane typy usług w czasie wykonywania. W przypadku wielu pakietów kodu są one aktywowane, gdy system szuka jednego z zadeklarowanych typów usług. Aby uzyskać więcej informacji, zobacz [CodePackage element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Element EntryPoint
Plik wykonywalny określony przez punkt wejścia jest zwykle długotrwałym hostem usługi. Obecność oddzielnego punktu wejścia Instalatora pozwala uniknąć konieczności uruchamiania hosta usługi z wysokim poziomem uprawnień przez dłuższy czas. Plik wykonywalny określony przez punkt wejścia jest uruchamiany po pomyślnym zamknięciu SetupEntryPoint. Proces wynikający z tego procesu jest monitorowany i uruchamiany ponownie (od SetupEntryPoint), jeśli kiedykolwiek się zakończy lub ulegnie awarii. Aby uzyskać więcej informacji, zobacz [element EntryPoint](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost, element
Aby uzyskać więcej informacji, zobacz [ContainerHost element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName — element
Repozytorium i obraz w [https://hub.docker.com](https://hub.docker.com) lub Azure Container Registry. Aby uzyskać więcej informacji, zobacz [element ImageName](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Commands, element
Przekaż listę rozdzielonych przecinkami poleceń do kontenera. Aby uzyskać więcej informacji, zobacz [polecenia element](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables, element
Przekaż zmienne środowiskowe do kontenera lub exe.  Aby uzyskać więcej informacji, zobacz [EnvironmentVariables element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Zmiennych środowiskowych, element
Zmienna środowiskowa. Aby uzyskać więcej informacji, zobacz [zmiennych środowiskowych element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage, element
Deklaruje folder o nazwie, który zawiera plik Settings.xml. Ten plik zawiera sekcje ustawień zdefiniowanych przez użytkownika, par klucz-wartość, które proces może odczytywać z powrotem w czasie wykonywania. W trakcie uaktualniania, jeśli tylko wersja ConfigPackage została zmieniona, uruchomiony proces nie zostanie ponownie uruchomiony. Zamiast tego wywołanie zwrotne powiadamia proces, że ustawienia konfiguracji zostały zmienione, aby można je było ponownie załadować dynamicznie. Aby uzyskać więcej informacji, zobacz [ConfigPackage element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Element zasobów
Opisuje zasoby używane przez tę usługę, które można zadeklarować bez modyfikowania skompilowanego kodu i zmienione podczas wdrażania usługi. Dostęp do tych zasobów jest kontrolowany za pomocą sekcji podmioty zabezpieczeń i zasady manifestu aplikacji. Aby uzyskać więcej informacji, zobacz [element Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element Endpoints
Definiuje punkty końcowe dla usługi. Aby uzyskać więcej informacji, zobacz [punkty końcowe elementu](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Element Endpoint
 Aby uzyskać więcej informacji, zobacz [element Endpoint](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

