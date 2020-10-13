---
title: Azure Files sterownik woluminu dla Service Fabric
description: Service Fabric obsługuje używanie Azure Files do tworzenia kopii zapasowych woluminów z kontenera.
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: a5125dbd88a2fe236196c427244f1311d9b73b9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86247697"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>Azure Files sterownik woluminu dla Service Fabric

Sterownik woluminu Azure Files to [wtyczka woluminu platformy Docker](https://docs.docker.com/engine/extend/plugins_volume/) , która dostarcza woluminy oparte na [Azure Files](../storage/files/storage-files-introduction.md) dla kontenerów platformy Docker. Jest ona spakowana jako aplikacja Service Fabric, którą można wdrożyć w klastrze Service Fabric w celu udostępnienia woluminów dla innych Service Fabric aplikacji kontenera w klastrze.

> [!NOTE]
> Wersja 6.5.661.9590 wtyczki woluminu Azure Files została udostępniona w celu uzyskania ogólnej dostępności.
>

## <a name="prerequisites"></a>Wymagania wstępne
* Wersja systemu Windows wtyczki woluminu Azure Files działa tylko w systemach operacyjnych [Windows Server w wersji 1709](/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 w wersji 1709](/windows/whats-new/whats-new-windows-10-version-1709) lub nowszej.

* Wersja systemu Linux dla wtyczki woluminu Azure Files działa we wszystkich wersjach systemów operacyjnych obsługiwanych przez Service Fabric.

* Wtyczka woluminu Azure Files działa tylko na Service Fabric wersji 6,2 i nowszych.

* Postępuj zgodnie z instrukcjami w [dokumentacji Azure Files](../storage/files/storage-how-to-create-file-share.md) , aby utworzyć udział plików dla aplikacji kontenera Service Fabric do użycia jako wolumin.

* Wymagany jest program [PowerShell z zainstalowanym modułem Service Fabric](./service-fabric-get-started.md) lub [SFCTL](./service-fabric-cli.md) .

* W przypadku korzystania z kontenerów funkcji Hyper-V następujące fragmenty kodu muszą zostać dodane w sekcji ClusterManifest (klaster lokalny) lub fabricSettings w szablonie Azure Resource Manager (klaster platformy Azure) lub ClusterConfig.jsw programie (autonomiczny klaster).

W ClusterManifest w sekcji hosting należy dodać następujące elementy. W tym przykładzie nazwa woluminu to **sfazurefile** , a port, do którego nasłuchuje w klastrze, to **19100**. Zastąp je wartościami prawidłowymi dla klastra.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

W sekcji fabricSettings szablonu Azure Resource Manager (dla wdrożeń platformy Azure) lub ClusterConfig.js(w przypadku wdrożeń autonomicznych) należy dodać poniższy fragment kodu. Ponownie Zastąp wartości Nazwa woluminu i port własnymi.

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Wdrażanie przykładowej aplikacji przy użyciu sterownika woluminu Azure Files Service Fabric

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Używanie Azure Resource Manager za pośrednictwem podanego skryptu programu PowerShell (zalecane)

Jeśli klaster jest oparty na platformie Azure, zalecamy wdrożenie w niej aplikacji przy użyciu modelu zasobów aplikacji Azure Resource Manager, aby ułatwić korzystanie z usługi i poruszanie się w modelu obsługi infrastruktury jako kodu. Takie podejście eliminuje konieczność śledzenia wersji aplikacji dla Azure Files sterownika woluminu. Umożliwia również obsługę oddzielnych szablonów Azure Resource Manager dla każdego obsługiwanego systemu operacyjnego. Skrypt założono, że wdrażasz najnowszą wersję aplikacji Azure Files i pobieramy parametry dla typu systemu operacyjnego, identyfikatora subskrypcji klastra i grupy zasobów. Skrypt można pobrać z [witryny pobierania Service Fabric](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip). Należy zauważyć, że to automatycznie ustawia wartość ListenPort, czyli port, na którym wtyczka woluminu Azure Files nasłuchuje żądań z demona Docker do 19100. Można to zmienić, dodając parametr o nazwie "listenPort". Upewnij się, że port nie jest w konflikcie z żadnym innym portem używanym przez klaster lub aplikacje.
 

Azure Resource Manager polecenia wdrażania dla systemu Windows:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Azure Resource Manager polecenia wdrażania dla systemu Linux:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

Po pomyślnym uruchomieniu skryptu możesz przejść do [sekcji Konfigurowanie aplikacji.](#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Ręczne wdrażanie klastrów autonomicznych

Aplikację Service Fabric, która dostarcza woluminy dla kontenerów, można pobrać z [witryny pobierania Service Fabric](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip). Aplikację można wdrożyć w klastrze za pomocą [interfejsów API](./service-fabric-deploy-remove-applications-fabricclient.md) [programu PowerShell](./service-fabric-deploy-remove-applications.md), [interfejsu wiersza polecenia](./service-fabric-application-lifecycle-sfctl.md) lub FabricClient.

1. Przy użyciu wiersza polecenia Zmień katalog na katalog główny pobranego pakietu aplikacji.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Następnie skopiuj pakiet aplikacji do magazynu obrazów z odpowiednimi wartościami dla [ApplicationPackagePath] i [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Rejestrowanie typu aplikacji

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Utwórz aplikację, zwracając bliską uwagę na wartość parametru **ListenPort** aplikacji. Ta wartość jest portem, na którym wtyczka woluminu Azure Files nasłuchuje żądań z demona platformy Docker. Upewnij się, że port podany dla aplikacji jest zgodny z VolumePluginPorts w ClusterManifest i nie powoduje konfliktu z żadnym innym portem używanym przez klaster lub aplikacje.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> System Windows Server 2016 Datacenter nie obsługuje mapowania instalacji SMB do kontenerów ([które są obsługiwane tylko w systemie Windows Server w wersji 1709](/virtualization/windowscontainers/manage-containers/container-storage)). To ograniczenie uniemożliwia mapowanie woluminu sieciowego i Azure Files sterowniki woluminów w wersjach starszych niż 1709.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>Wdrażanie aplikacji w lokalnym klastrze projektowym
Wykonaj kroki 1-3 z [powyższego.](#manual-deployment-for-standalone-clusters)

 Domyślna liczba wystąpień usługi dla aplikacji wtyczki woluminu Azure Files to-1, co oznacza, że istnieje wystąpienie usługi wdrożone w każdym węźle w klastrze. Jednak podczas wdrażania aplikacji wtyczki woluminu Azure Files w lokalnym klastrze projektowym liczba wystąpień usługi powinna być określona jako 1. Można to zrobić za pomocą parametru aplikacji **InstanceCount** . W związku z tym, polecenie do tworzenia aplikacji wtyczki woluminu Azure Files w lokalnym klastrze projektowym jest następujące:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Konfigurowanie aplikacji do korzystania z woluminu
Poniższy fragment kodu przedstawia, w jaki sposób wolumin oparty na Azure Files może być określony w pliku manifestu aplikacji aplikacji. Określony element zainteresowania jest tagiem **woluminu** :

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
                <DriverOption Name="storageAccountFQDN" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

Nazwa sterownika wtyczki woluminu Azure Files to **sfazurefile**. Ta wartość jest ustawiana dla atrybutu **sterownika** elementu tag **woluminu** w manifeście aplikacji.

W poniższym fragmencie kodu **wtyczka woluminu Azure Files** wymaga następujących atrybutów:
- **Źródło** — to jest nazwa woluminu. Użytkownik może wybrać dowolną nazwę dla swojego woluminu.
- **Miejsce docelowe** — ten atrybut jest lokalizacją, w której jest mapowany wolumin w działającym kontenerze. W rezultacie miejsce docelowe nie może być lokalizacją, która już istnieje w kontenerze

Jak pokazano w elementach **DriverOption** w powyższym fragmencie kodu, wtyczka woluminu Azure Files obsługuje następujące opcje sterownika:
- Nazwa **udziałuname** Azure Files udziału plików, który udostępnia wolumin dla kontenera.
- **storageAccountName** — nazwa konta usługi Azure Storage, która zawiera Azure Files udział plików.
- **storageAccountKey** — klucz dostępu dla konta usługi Azure Storage, który zawiera Azure Files udział plików.
- **storageAccountFQDN** — nazwa domeny skojarzona z kontem magazynu. Jeśli storageAccountFQDN nie zostanie określony, nazwa domeny zostanie utworzona przy użyciu domyślnego sufiksu (. plik. Core. Windows. NET) z storageAccountName.  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>Używanie własnego woluminu lub sterownika rejestrowania
Service Fabric również umożliwia użycie własnych niestandardowych sterowników [woluminów](https://docs.docker.com/engine/extend/plugins_volume/) lub [dzienników](https://docs.docker.com/engine/admin/logging/overview/) . Jeśli w klastrze nie zainstalowano sterownika rejestrowania woluminów/sterowników platformy Docker, można zainstalować go ręcznie przy użyciu protokołów RDP/SSH. Instalację można przeprowadzić za pomocą tych protokołów za pomocą [skryptu uruchamiania zestawu skalowania maszyn wirtualnych](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) lub [skryptu SetupEntryPoint](./service-fabric-application-model.md).

Przykładowy skrypt [służący do instalacji sterownika woluminu platformy Docker dla platformy Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) jest następujący:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

W aplikacjach, aby użyć zainstalowanego sterownika woluminu lub rejestrowania, należy określić odpowiednie wartości w **woluminie** i **LogConfig** elementy w obszarze **ContainerHostPolicies** w manifeście aplikacji.

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

Podczas określania wtyczki woluminu Service Fabric automatycznie tworzy wolumin przy użyciu określonych parametrów. Tag **Source** dla elementu **Volume** jest nazwą woluminu, a tag **sterownika** określa wtyczkę sterownika woluminu. Znacznik **docelowy** jest lokalizacją, w której jest mapowane **Źródło** w uruchomionym kontenerze. W rezultacie miejsce docelowe nie może być lokalizacją, która już istnieje w kontenerze. Opcje można określić za pomocą tagu **DriverOption** w następujący sposób:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Parametry aplikacji są obsługiwane w przypadku woluminów, jak pokazano w powyższym fragmencie kodu manifestu (wyszukiwanie w `MyStorageVar` celu użycia przykładu).

W przypadku określenia sterownika dziennika platformy Docker należy wdrożyć agentów (lub kontenery) w celu obsługi dzienników w klastrze. Tag **DriverOption** może służyć do określania opcji dla sterownika dziennika.

## <a name="next-steps"></a>Następne kroki
* Aby zobaczyć przykłady kontenerów, w tym sterownik woluminu, odwiedź [przykłady kontenerów Service Fabric](https://github.com/Azure-Samples/service-fabric-containers)
* Aby wdrożyć kontenery w klastrze Service Fabric, zapoznaj się z artykułem [wdrażanie kontenera na Service Fabric](./service-fabric-get-started-containers.md)
