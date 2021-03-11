---
title: Włączanie diagnostyki na platformie Azure Cloud Services (klasyczny) przy użyciu programu PowerShell | Microsoft Docs
description: Dowiedz się, jak używać programu PowerShell do włączania zbierania danych diagnostycznych z usługi w chmurze platformy Azure przy użyciu rozszerzenia Diagnostyka Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 23528190d2eb60cc6ea3fe94bb9f7a2374526f6a
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618444"
---
# <a name="enable-diagnostics-in-azure-cloud-services-classic-using-powershell"></a>Włączanie diagnostyki na platformie Azure Cloud Services (klasyczny) przy użyciu programu PowerShell

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Można zbierać dane diagnostyczne, takie jak Dzienniki aplikacji, liczniki wydajności itp., z usługi w chmurze przy użyciu rozszerzenia Diagnostyka Azure. W tym artykule opisano sposób włączania rozszerzenia Diagnostyka Azure dla usługi w chmurze przy użyciu programu PowerShell.  Zapoznaj się z tematem [Instalowanie i konfigurowanie Azure PowerShell](/powershell/azure/) wymagań wstępnych dotyczących tego artykułu.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Włączanie rozszerzenia diagnostyki w ramach wdrażania usługi Cloud Service
Takie podejście ma zastosowanie do typu ciągłej integracji, w którym można włączyć rozszerzenie diagnostyki w ramach wdrażania usługi w chmurze. Podczas tworzenia nowego wdrożenia usługi w chmurze można włączyć rozszerzenie diagnostyki, przekazując parametr *ExtensionConfiguration* do polecenia cmdlet [New-AzureDeployment](/powershell/module/servicemanagement/azure.service/new-azuredeployment) . Parametr *ExtensionConfiguration* pobiera tablicę konfiguracji diagnostycznych, które można utworzyć za pomocą polecenia cmdlet [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure.service/new-azureservicediagnosticsextensionconfig) .

Poniższy przykład pokazuje, jak można włączyć diagnostykę dla usługi w chmurze z rolą webrole i rola procesu roboczego, z których każda ma inną konfigurację diagnostyki.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Jeśli plik konfiguracji diagnostyki określa `StorageAccount` element z nazwą konta magazynu, `New-AzureServiceDiagnosticsExtensionConfig` polecenie cmdlet automatycznie użyje tego konta magazynu. Aby to działało, konto magazynu musi znajdować się w tej samej subskrypcji co wdrożonej usłudze w chmurze.

Z zestawu Azure SDK 2,6 w dalszym ciągu pliki konfiguracji rozszerzenia wygenerowane przez wynikowe dane wyjściowe publikowania programu MSBuild będą obejmować nazwę konta magazynu opartą na parametrze konfiguracji diagnostyki określonym w pliku konfiguracji usługi (cscfg). Poniższy skrypt pokazuje, jak przeanalizować pliki konfiguracji rozszerzenia z docelowego wyjścia publikowania i skonfigurować rozszerzenie diagnostyki dla każdej roli podczas wdrażania usługi w chmurze.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

W usłudze Visual Studio Online jest stosowane podobne podejście do zautomatyzowanych wdrożeń Cloud Services przy użyciu rozszerzenia diagnostyki. Zobacz [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) , aby uzyskać kompletny przykład.

Jeśli wartość nie `StorageAccount` została określona w konfiguracji diagnostyki, należy przekazać parametr *StorageAccountName* do polecenia cmdlet. Jeśli parametr *StorageAccountName* jest określony, polecenie cmdlet zawsze będzie używać konta magazynu, które jest określone w parametrze, a nie tego, który jest określony w pliku konfiguracji diagnostyki.

Jeśli konto magazynu diagnostyki znajduje się w innej subskrypcji usługi w chmurze, należy jawnie przekazać parametry *StorageAccountName* i *StorageAccountKey* do polecenia cmdlet. Parametr *StorageAccountKey* nie jest wymagany, jeśli konto magazynu diagnostyki znajduje się w tej samej subskrypcji, ponieważ polecenie cmdlet może automatycznie wysyłać zapytania i ustawić wartość klucza podczas włączania rozszerzenia diagnostyki. Jeśli jednak konto magazynu diagnostyki znajduje się w innej subskrypcji, polecenie cmdlet może nie być w stanie automatycznie uzyskać klucza i należy jawnie określić klucz za pomocą parametru *StorageAccountKey* .

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Włączanie rozszerzenia diagnostyki w istniejącej usłudze Cloud Service
Aby włączyć lub zaktualizować konfigurację diagnostyki w usłudze w chmurze, która jest już uruchomiona, można użyć polecenia cmdlet [Set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azureservicediagnosticsextension) .

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>Pobieranie bieżącej konfiguracji rozszerzenia diagnostyki
Użyj polecenia cmdlet [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/get-azureservicediagnosticsextension) , aby pobrać bieżącą konfigurację diagnostyki dla usługi w chmurze.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Usuwanie rozszerzenia diagnostyki
Aby wyłączyć diagnostykę w usłudze w chmurze, można użyć polecenia cmdlet [Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/remove-azureservicediagnosticsextension) .

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Jeśli włączono rozszerzenie diagnostyki przy użyciu polecenia *Set-AzureServiceDiagnosticsExtension* lub *New-AzureServiceDiagnosticsExtensionConfig* bez parametru *role* , można usunąć rozszerzenie za pomocą polecenia *Remove-AzureServiceDiagnosticsExtension* bez parametru *role* . Jeśli podczas włączania rozszerzenia użyto parametru *role* , należy go również użyć podczas usuwania rozszerzenia.

Aby usunąć rozszerzenie diagnostyki z pojedynczej roli:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać dodatkowe wskazówki dotyczące korzystania z diagnostyki platformy Azure i innych technik rozwiązywania problemów, zobacz [Włączanie diagnostyki na platformie Azure Cloud Services i Virtual Machines](cloud-services-dotnet-diagnostics.md).
* [Schemat konfiguracji diagnostyki](../azure-monitor/agents/diagnostics-extension-schema-windows.md) objaśnia różne opcje konfiguracji XML dla rozszerzenia diagnostyki.
* Aby dowiedzieć się, jak włączyć rozszerzenie diagnostyki dla Virtual Machines, zobacz [Tworzenie maszyny wirtualnej z systemem Windows z funkcją monitorowania i diagnostyki przy użyciu szablonu Azure Resource Manager](../virtual-machines/extensions/diagnostics-template.md)