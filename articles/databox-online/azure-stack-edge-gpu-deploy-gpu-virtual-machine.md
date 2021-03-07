---
title: Omówienie i wdrażanie maszyn wirtualnych GPU na urządzeniu z systemem Azure Stack Edge
description: Opisuje sposób tworzenia maszyn wirtualnych procesora GPU i zarządzania nimi na urządzeniu Azure Stack EDGE Pro przy użyciu szablonów.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: ff805b758dce05a66764ab1ff08e53378c946362
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438186"
---
# <a name="gpu-vms-for-your-azure-stack-edge-pro-device"></a>Maszyny wirtualne procesora GPU dla urządzenia z Azure Stack EDGE Pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Ten artykuł zawiera omówienie maszyn wirtualnych procesora GPU na urządzeniu, na którym znajduje się Azure Stack Edge. W tym artykule opisano, jak utworzyć maszynę wirtualną procesora GPU, a następnie zainstalować rozszerzenie sterownika procesora GPU w celu zainstalowania odpowiednich sterowników firmy NVIDIA. Użyj szablonów Azure Resource Manager, aby utworzyć maszynę wirtualną procesora GPU i zainstalować rozszerzenie sterownika procesora GPU. 

Ten artykuł ma zastosowanie do Azure Stack brzegowych urządzeń GPU i Azure Stack EDGE Pro.

## <a name="about-gpu-vms"></a>Informacje o maszynach wirtualnych GPU

Twoje urządzenia Azure Stack EDGE Pro są wyposażone w 1 lub 2 jednostki GPU Tesla T4. W celu wdrożenia na tych urządzeniach obciążeń maszyn wirtualnych z procesorem GPU należy używać zoptymalizowanych od procesora GPU rozmiarów maszyn wirtualnych. Na przykład w celu wdrożenia obciążeń wnioskowania, które są wyposażone w procesory T4, należy użyć serii v3. 

Aby uzyskać więcej informacji, zobacz [maszyny wirtualne z serii v3](../virtual-machines/nct4-v3-series.md).

## <a name="supported-os-and-gpu-drivers"></a>Obsługiwane sterowniki systemu operacyjnego i procesora GPU 

Aby skorzystać z możliwości procesora GPU maszyn wirtualnych z serii N, należy zainstalować sterowniki graficznego procesora NVIDIA. 

Rozszerzenie sterownika NVIDIA GPU instaluje odpowiednie sterowniki NVIDIA CUDA lub GRID. Możesz zainstalować rozszerzenie i zarządzać nim za pomocą szablonów Azure Resource Manager.

### <a name="supported-os-for-gpu-extension-for-windows"></a>Obsługiwane systemy operacyjne dla rozszerzenia GPU dla systemu Windows

To rozszerzenie obsługuje następujące systemy operacyjne (OSs). Inne wersje mogą działać, ale nie zostały przetestowane wewnętrznie na maszynach wirtualnych GPU działających na urządzeniach Azure Stack EDGE Pro.

| Dystrybucja | Wersja |
|---|---|
| Windows Server 2019 | Core |
| Windows Server 2016 | Core |

### <a name="supported-os-for-gpu-extension-for-linux"></a>Obsługiwane systemy operacyjne dla rozszerzenia procesora GPU dla systemu Linux

To rozszerzenie obsługuje następujący dystrybucje systemu operacyjnego, w zależności od obsługi sterowników dla konkretnej wersji systemu operacyjnego. Inne wersje mogą działać, ale nie zostały przetestowane wewnętrznie na maszynach wirtualnych GPU działających na urządzeniach Azure Stack EDGE Pro.


| Dystrybucja | Wersja |
|---|---|
| Ubuntu | 18,04 LTS |
| Red Hat Enterprise Linux | 7,4 |


## <a name="gpu-vms-and-kubernetes"></a>Maszyny wirtualne GPU i Kubernetes

Przed wdrożeniem maszyn wirtualnych GPU na urządzeniu zapoznaj się z poniższymi uwagami, jeśli na urządzeniu skonfigurowano Kubernetes.

#### <a name="for-1-gpu-device"></a>Dla urządzenia 1-GPU: 

- **Utwórz maszynę wirtualną procesora GPU, a następnie Kubernetes konfigurację na urządzeniu**: w tym scenariuszu konfiguracja tworzenia maszyn wirtualnych i Kubernetes procesora GPU powiedzie się. W takim przypadku Kubernetes nie będzie miał dostępu do procesora GPU.

- **Skonfiguruj Kubernetes na urządzeniu, a następnie utwórz maszynę wirtualną procesora GPU**: w tym scenariuszu Kubernetes będzie przejąć procesor GPU na urządzeniu i Tworzenie maszyny wirtualnej nie powiedzie się, ponieważ nie ma dostępnych zasobów procesora GPU.

#### <a name="for-2-gpu-device"></a>Dla urządzenia 2-GPU

- **Utwórz maszynę wirtualną procesora GPU, a następnie Kubernetes konfigurację na urządzeniu**: w tym scenariuszu utworzona maszyna wirtualna procesora GPU będzie odrościć jeden procesor GPU na urządzeniu, a konfiguracja Kubernetes również powiodła się i zostanie zastosowana pozostały jeden procesor GPU. 

- **Utwórz dwie maszyny wirtualne procesora GPU, a następnie Kubernetes konfigurację na urządzeniu**: w tym scenariuszu dwie maszyny wirtualne procesora GPU zawiążą dwa procesory GPU na urządzeniu, a Kubernetes jest prawidłowo skonfigurowany bez procesorów GPU. 

- **Skonfiguruj Kubernetes na urządzeniu, a następnie utwórz maszynę wirtualną procesora GPU**: w tym scenariuszu Kubernetes będzie dotyczyć zarówno procesorów GPU na urządzeniu, jak i Tworzenie maszyny wirtualnej nie powiedzie się, ponieważ nie będzie dostępnych zasobów procesora GPU.

Jeśli na urządzeniu są uruchomione maszyny wirtualne GPU, a Kubernetes jest również skonfigurowany, w każdej chwili zostanie cofnięta alokacja maszyny wirtualnej (po zatrzymaniu lub usunięciu maszyny wirtualnej przy użyciu Stop-AzureRmVM lub polecenia Remove-AzureRmVM), istnieje ryzyko, że klaster Kubernetes będzie odrościł wszystkie procesory GPU dostępne na urządzeniu. W takim przypadku nie będzie można ponownie uruchomić maszyn wirtualnych procesora GPU wdrożonych na urządzeniu ani tworzyć maszyn wirtualnych GPU.


## <a name="create-gpu-vms"></a>Tworzenie maszyn wirtualnych GPU

Wykonaj następujące kroki, aby wdrożyć maszyny wirtualne GPU na urządzeniu:

1. Ustal, czy urządzenie będzie również działać w Kubernetes. Jeśli na urządzeniu zostanie uruchomione Kubernetes, najpierw musisz utworzyć maszynę wirtualną procesora GPU, a następnie skonfigurować Kubernetes. Jeśli Kubernetes jest skonfigurowany jako pierwszy, zostanie on zaliczony do wszystkich dostępnych zasobów procesora GPU, a Tworzenie maszyny wirtualnej procesora GPU zakończy się niepowodzeniem.

1. [Pobierz szablony maszyn wirtualnych i pliki parametrów](https://aka.ms/ase-vm-templates) na komputer kliencki. Rozpakuj go do katalogu, który będzie używany jako katalog roboczy.

1. Aby utworzyć maszyny wirtualne procesora GPU, wykonaj wszystkie kroki opisane w sekcji [Wdróż maszynę wirtualną na swojej Azure Stack EDGE Pro przy użyciu szablonów](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) z wyjątkiem następujących różnic: 

    1. Podczas konfigurowania sieci obliczeniowej Włącz port, który jest połączony z Internetem, w celu obliczenia. Pozwala to na pobranie sterowników procesora GPU wymaganych dla rozszerzeń procesora GPU dla maszyn wirtualnych GPU.

        Oto przykład, w którym port 2 został połączony z Internetem i został użyty do włączenia sieci obliczeniowej. Jeśli zidentyfikowano, że Kubernetes nie jest wymagany w poprzednim kroku, można pominąć przypisanie adresu IP i usług zewnętrznych węzła Kubernetes.

        ![Włącz ustawienia obliczeń na porcie połączonym z Internetem](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

            
    1. Utwórz maszynę wirtualną przy użyciu szablonów. Podczas określania rozmiarów maszyn wirtualnych procesora, upewnij się, że w obszarze maszyny `CreateVM.parameters.json` wirtualne procesora GPU jest używana seria NCasT4-v3. Aby uzyskać więcej informacji, zobacz [obsługiwane rozmiary maszyn wirtualnych dla maszyn wirtualnych GPU](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview).

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```

    1. Po pomyślnym utworzeniu maszyny wirtualnej procesora GPU można wyświetlić tę MASZYNę wirtualną na liście maszyn wirtualnych w Azure Stack zasobów brzegowych w Azure Portal.

        ![Maszyna wirtualna procesora GPU na liście maszyn wirtualnych w Azure Portal](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. Wybierz maszynę wirtualną i przejdź do szczegółów. Skopiuj adres IP przydzielony do maszyny wirtualnej.

    ![Adres IP przydzielony do maszyny wirtualnej procesora GPU w Azure Portal](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. Po utworzeniu maszyny wirtualnej Wdróż rozszerzenie GPU przy użyciu szablonu rozszerzenia. W przypadku maszyn wirtualnych z systemem Linux zobacz [Instalowanie rozszerzenia procesora GPU dla](#gpu-extension-for-linux) maszyn wirtualnych z systemem Linux i dla systemu Windows, zobacz [Instalowanie rozszerzenia procesora GPU dla systemu Windows](#gpu-extension-for-windows).

1. Aby zweryfikować instalację rozszerzenia procesora GPU, Połącz się z maszyną wirtualną procesora GPU:
    1. W przypadku korzystania z maszyny wirtualnej z systemem Windows wykonaj kroki opisane w sekcji [łączenie z maszyną wirtualną z systemem Windows](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-windows-vm). [Zweryfikuj instalację](#verify-windows-driver-installation).
    1. W przypadku korzystania z maszyny wirtualnej z systemem Linux wykonaj kroki opisane w sekcji [nawiązywanie połączenia z maszyną wirtualną z systemem Linux](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm). [Zweryfikuj instalację](#verify-linux-driver-installation).

1. W razie potrzeby można przełączyć sieć obliczeniową z powrotem do dowolnego, co jest potrzebne. 


> [!NOTE]
> W przypadku aktualizowania oprogramowania urządzenia z wersji 2012 do nowszej trzeba ręcznie zatrzymać maszyny wirtualne GPU.


## <a name="install-gpu-extension"></a>Zainstaluj rozszerzenie procesora GPU

W zależności od systemu operacyjnego dla maszyny wirtualnej można zainstalować rozszerzenie procesora GPU dla systemu Windows lub Linux.

> [!NOTE]
> Przed zainstalowaniem rozszerzenia procesora GPU upewnij się, że port włączony dla sieci obliczeniowej na urządzeniu jest połączony z Internetem i ma dostęp. Sterowniki procesora GPU są pobierane przez dostęp do Internetu.

### <a name="gpu-extension-for-windows"></a>Rozszerzenie procesora GPU dla systemu Windows

Aby wdrożyć sterowniki procesora GPU NVIDIA dla istniejącej maszyny wirtualnej, należy edytować `addGPUExtWindowsVM.parameters.json` plik parametrów, a następnie wdrożyć szablon `addGPUextensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Edytuj plik parametrów

Plik `addGPUExtWindowsVM.parameters.json` przyjmuje następujące parametry:

```json
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: windowsGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverWindows" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DriverURL" : "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
    "DriverCertificateUrl" : "https://go.microsoft.com/fwlink/?linkid=871664",
    "DriverType":"CUDA"
    }
    }
    }
```

Oto przykładowy plik parametrów, który został użyty w tym artykule:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment2"

DeploymentName          : deployment2
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:02:56 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          adminUsername    String                     Administrator
                          password         String                     Password1
                          imageName        String                     myasewindowsimg
                          vmSize           String                     Standard_NC4as_T4_v3
                          vnetName         String                     ASEVNET
                          vnetRG           String                     aserg
                          subnetName       String                     ASEVNETsubNet
                          nicName          String                     nic6
                          ipConfigName     String                     ipconfig6
                          privateIPAddress  String

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="deploy-template"></a>Wdrażanie szablonu

Wdróż szablon `addGPUextensiontoVM.json` . Ten szablon wdraża rozszerzenie dla istniejącej maszyny wirtualnej. Uruchom następujące polecenie:

```powershell
$templateFile = "<Path to addGPUextensiontoVM.json>" 
$templateParameterFile = "<Path to addGPUExtWindowsVM.parameters.json>" 
$RGName = "<Name of your resource group>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
```
> [!NOTE]
> Wdrożenie rozszerzenia to długotrwałe zadanie i trwa około 10 minut.

Oto przykładowe dane wyjściowe:

```powershell
PS C:\WINDOWS\system32> "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment3"

DeploymentName          : deployment3
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:18:50 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          extensionName    String                     windowsgpuext
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverWindows
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="track-deployment"></a>Śledzenie wdrożenia

Aby sprawdzić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
Oto przykładowe dane wyjściowe:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM2 -Name windowsgpuext

ResourceGroupName       : myasegpuvm1
VMName                  : VM2
Name                    : windowsgpuext
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverWindows
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM2/extensions/windowsgpuext
PublicSettings          : {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w następującym pliku. Zapoznaj się z tym plikiem `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status` , aby śledzić stan instalacji. 


Pomyślna instalacja jest wskazywana `message` przez `Enable Extension` i `status` jako `success` .

```powershell
"status":  {
                       "formattedMessage":  {
                                                "message":  "Enable Extension",
                                                "lang":  "en"
                                            },
                       "name":  "NvidiaGpuDriverWindows",
                       "status":  "success",
```

#### <a name="verify-windows-driver-installation"></a>Weryfikowanie instalacji sterowników systemu Windows

Zaloguj się do maszyny wirtualnej i uruchom narzędzie wiersza polecenia NVIDIA-SMI zainstalowane wraz z sterownikiem. Znajduje się `nvidia-smi.exe` w lokalizacji  `C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe` . Jeśli plik nie jest wyświetlany, istnieje możliwość, że instalacja sterownika nadal działa w tle. Poczekaj 10 minut i sprawdź ponownie.

Jeśli sterownik jest zainstalowany, zobaczysz dane wyjściowe podobne do poniższego przykładu: 

```powershell
PS C:\Users\Administrator> cd "C:\Program Files\NVIDIA Corporation\NVSMI"
PS C:\Program Files\NVIDIA Corporation\NVSMI> ls

    Directory: C:\Program Files\NVIDIA Corporation\NVSMI

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/26/2020  12:00 PM         849640 MCU.exe
-a----        2/26/2020  12:00 PM         443104 nvdebugdump.exe
-a----        2/25/2020   2:06 AM          81823 nvidia-smi.1.pdf
-a----        2/26/2020  12:01 PM         566880 nvidia-smi.exe
-a----        2/26/2020  12:01 PM         991344 nvml.dll

PS C:\Program Files\NVIDIA Corporation\NVSMI> .\nvidia-smi.exe
Wed Dec 16 00:35:51 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 442.50       Driver Version: 442.50       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name            TCC/WDDM | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            TCC  | 0000503C:00:00.0 Off |                    0 |
| N/A   35C    P8    11W /  70W |      8MiB / 15205MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
PS C:\Program Files\NVIDIA Corporation\NVSMI>
```

Aby uzyskać więcej informacji, zobacz [rozszerzenie sterownika GPU NVIDIA dla systemu Windows](../virtual-machines/extensions/hpccompute-gpu-windows.md).

### <a name="gpu-extension-for-linux"></a>Rozszerzenie procesora GPU dla systemu Linux

Aby wdrożyć sterowniki procesora GPU NVIDIA dla istniejącej maszyny wirtualnej, należy edytować plik parametrów, a następnie wdrożyć szablon `addGPUextensiontoVM.json` . Istnieją określone pliki parametrów dla Ubuntu i Red Hat Enterprise Linux (RHEL), jak opisano w poniższych sekcjach.

#### <a name="edit-parameters-file"></a>Edytuj plik parametrów

W przypadku korzystania z Ubuntu `addGPUExtLinuxVM.parameters.json` plik przyjmuje następujące parametry:

```powershell
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: linuxGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverLinux" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
    "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
    "CUDA_ver": "10.0.130",
    "InstallCUDA": "true"
    }
    }
    }
```
W przypadku używania Red Hat Enterprise Linux (RHEL) `addGPUExtensionRHELVM.parameters.json` plik przyjmuje następujące parametry:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<name of the VM>" 
        },
        "extensionName": {
            "value": "<name for the extension. Example: linuxGpu>" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
                    "isCustomInstall":true,
                    "DRIVER_URL":"https://go.microsoft.com/fwlink/?linkid=874273",
                    "CUDA_ver":"10.0.130",
                    "PUBKEY_URL":"http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                    "DKMS_URL":"https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm",
                    "LIS_URL":"https://aka.ms/lis",
                    "LIS_RHEL_ver":"3.10.0-1062.9.1.el7"
            }
        }
    }
}
```


Oto przykładowy plik parametrów Ubuntu, który został użyty w tym artykule:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM1" 
        },
        "extensionName": {
            "value": "gpuLinux" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
            "CUDA_ver": "10.0.130",
            "InstallCUDA": "true"
            }
        }
    }
}
```


#### <a name="deploy-template"></a>Wdrażanie szablonu

Wdróż szablon `addGPUextensiontoVM.json` . Ten szablon wdraża rozszerzenie dla istniejącej maszyny wirtualnej. Uruchom następujące polecenie:

```powershell
$templateFile = "Path to addGPUextensiontoVM.json" 
$templateParameterFile = "Path to addGPUExtLinuxVM.parameters.json" 
$RGName = "<Name of your resource group>" 
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
``` 

> [!NOTE]
> Wdrożenie rozszerzenia to długotrwałe zadanie i trwa około 10 minut.

Oto przykładowe dane wyjściowe:

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "rg2"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "delpoyment7"

DeploymentName          : delpoyment7
ResourceGroupName       : rg2
ProvisioningState       : Succeeded
Timestamp               : 12/10/2020 10:43:23 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM1
                          extensionName    String                     gpuLinux
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverLinux
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL":
                          "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```

#### <a name="track-deployment-status"></a>Śledzenie stanu wdrożenia    
    
Template deployment to długotrwałe zadanie. Aby sprawdzić stan wdrożenia dla danej maszyny wirtualnej, Otwórz inną sesję programu PowerShell (Uruchom jako administrator). Uruchom następujące polecenie: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Oto przykładowe dane wyjściowe: 

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName rg2 -VMName VM1 -Name gpulinux

ResourceGroupName       : rg2
VMName                  : VM1
Name                    : gpuLinux
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverLinux
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg2/providers/Microsoft.Compute/virtualMachines/VM1/extensions/gpuLinux
PublicSettings          : {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> Po zakończeniu wdrożenia `ProvisioningState` zmiany zostaną wprowadzone do `Succeeded` .

Dane wyjściowe wykonania rozszerzenia są rejestrowane w następującym pliku: `/var/log/azure/nvidia-vmext-status` .

#### <a name="verify-linux-driver-installation"></a>Weryfikowanie instalacji sterowników systemu Linux

Wykonaj następujące kroki, aby zweryfikować instalację sterownika:

1. Nawiąż połączenie z maszyną wirtualną procesora GPU. Postępuj zgodnie z instrukcjami podanymi w temacie [łączenie z maszyną wirtualną z systemem Linux](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm). 

    Oto przykładowe dane wyjściowe:

    ```powershell
    PS C:\WINDOWS\system32> ssh -l Administrator 10.57.50.60
    Administrator@10.57.50.60's password:
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
      System information as of Thu Dec 10 22:57:01 UTC 2020
    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
    
    249 packages can be updated.
    140 updates are security updates.
    
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage    
      System information as of Thu Dec 10 22:57:01 UTC 2020    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
        
    249 packages can be updated.
    140 updates are security updates.
    
    New release '20.04.1 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
        
    *** System restart required ***
    Last login: Thu Dec 10 21:49:29 2020 from 10.90.24.23
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    Administrator@VM1:~$
    ```
2. Uruchom narzędzie wiersza polecenia NVIDIA-SMI zainstalowane wraz z sterownikiem. Jeśli sterownik został pomyślnie zainstalowany, będzie można uruchomić narzędzie i zobaczyć następujące dane wyjściowe:

    ```powershell
    Administrator@VM1:~$ nvidia-smi
    Thu Dec 10 22:58:46 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 455.45.01    Driver Version: 455.45.01    CUDA Version: 11.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            Off  | 0000941F:00:00.0 Off |                    0 |
    | N/A   48C    P0    27W /  70W |      0MiB / 15109MiB |      5%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    Administrator@VM1:~$
    ```

Aby uzyskać więcej informacji, zobacz [rozszerzenie sterownika GPU NVIDIA dla systemu Linux](../virtual-machines/extensions/hpccompute-gpu-linux.md).

## <a name="remove-gpu-extension"></a>Usuń rozszerzenie procesora GPU

Aby usunąć rozszerzenie procesora GPU, użyj następującego polecenia:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Oto przykładowe dane wyjściowe:

```powershell
PS C:\azure-stack-edge-deploy-vms> Remove-AzureRmVMExtension -ResourceGroupName rgl -VMName WindowsVM -Name windowsgpuext
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue? [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
Requestld IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------    
          True                OK         OK
```




## <a name="next-steps"></a>Następne kroki

[Polecenia cmdlet Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)