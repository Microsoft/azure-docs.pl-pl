---
title: Korzystanie z rozszerzeń niestandardowych skryptów dla maszyn wirtualnych na urządzeniu z systemem Azure Stack Edge
description: Opisuje sposób instalowania rozszerzeń skryptów niestandardowych na maszynach wirtualnych uruchomionych na urządzeniu Azure Stack EDGE Pro przy użyciu szablonów.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 2d2e7d403ab3e9cc7e8e17de53b6e821ec24caa1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438016"
---
# <a name="deploy-custom-script-extension-on-vms-running-on-your-azure-stack-edge-pro-device"></a>Wdróż niestandardowe rozszerzenie skryptu na maszynach wirtualnych działających na urządzeniu z systemem Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Rozszerzenie skryptu niestandardowego pobiera i uruchamia skrypty lub polecenia na maszynach wirtualnych uruchomionych na urządzeniach Azure Stack EDGE Pro. W tym artykule opisano sposób instalowania i uruchamiania niestandardowego rozszerzenia skryptu przy użyciu szablonu Azure Resource Manager. 

Ten artykuł ma zastosowanie do Azure Stack brzegowych procesorów GPU, Azure Stack EDGE Pro R i Azure Stack Edge.

## <a name="about-custom-script-extension"></a>Informacje o rozszerzeniu niestandardowego skryptu

Rozszerzenie niestandardowego skryptu jest przydatne w przypadku konfiguracji po wdrożeniu, instalacji oprogramowania lub innych zadań związanych z konfiguracją/zarządzaniem. Skrypty można pobrać z usługi Azure Storage lub innej dostępnej lokalizacji w Internecie albo udostępnić skrypty lub polecenia do środowiska uruchomieniowego rozszerzenia.

Rozszerzenie niestandardowego skryptu integruje się z szablonami Azure Resource Manager. Można go również uruchomić przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub usługi Azure Virtual Machines REST.

## <a name="os-for-custom-script-extension"></a>System operacyjny dla niestandardowego rozszerzenia skryptu

#### <a name="supported-os-for-custom-script-extension-on-windows"></a>Obsługiwane systemy operacyjne dla niestandardowego rozszerzenia skryptu w systemie Windows

Rozszerzenie niestandardowego skryptu dla systemu Windows zostanie uruchomione na następującym serwerze OSs. Inne wersje mogą działać, ale nie zostały przetestowane na maszynach wirtualnych działających na Azure Stack na urządzeniach brzegowych Pro.

| Dystrybucja | Wersja |
|---|---|
| Windows Server 2019 | Core |
| Windows Server 2016 | Core |

#### <a name="supported-os-for-custom-script-extension-on-linux"></a>Obsługiwane systemy operacyjne dla niestandardowego rozszerzenia skryptu w systemie Linux

Rozszerzenie niestandardowego skryptu dla systemu Linux zostanie uruchomione na następującym serwerze OSs. Inne wersje mogą działać, ale nie zostały przetestowane na maszynach wirtualnych działających na Azure Stack na urządzeniach brzegowych Pro.

| Dystrybucja | Wersja |
|---|---|
| Linux: Ubuntu | 18,04 LTS |
| Linux: Red Hat Enterprise Linux | 7,4, 7,5, 7,7 |

<!--### Script location

Instead of the scripts, in this article, we pass a command to execute via the Custom Script Extension. 

### Internet Connectivity

To download a script externally such as from GitHub or Azure Storage, make sure that the port on which you enable compute network, is connected to the internet. 

If your script is on a local server, then you may still need additional firewall and Network Security Group ports need to be opened.

> [!NOTE]
> Before you install the Custom Script extension, make sure that the port enabled for compute network on your device is connected to Internet and has access. -->

## <a name="prerequisites"></a>Wymagania wstępne

1. [Pobierz szablony maszyn wirtualnych i pliki parametrów](https://aka.ms/ase-vm-templates) na komputer kliencki. Rozpakuj pobieranie do katalogu, który będzie używany jako katalog roboczy.

1. Na urządzeniu powinna być utworzona i wdrożona maszyna wirtualna. Aby utworzyć maszyny wirtualne, wykonaj wszystkie kroki opisane w temacie [Wdrażanie maszyny wirtualnej na Azure Stack EDGE Pro przy użyciu szablonów](azure-stack-edge-gpu-deploy-virtual-machine-templates.md).

    Jeśli musisz pobrać skrypt, taki jak z witryny GitHub lub z usługi Azure Storage zewnętrznie, podczas konfigurowania sieci obliczeniowej Włącz port połączony z Internetem w celu obliczenia. Pozwala to na pobranie skryptu.

    W poniższym przykładzie port 2 został połączony z Internetem i został użyty do włączenia sieci obliczeniowej. Jeśli zidentyfikowano, że Kubernetes nie jest wymagany w poprzednim kroku, można pominąć przypisanie adresu IP węzła Kubernetes i usługi zewnętrznej.

    ![Włącz ustawienia obliczeń na porcie połączonym z Internetem](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

## <a name="install-custom-script-extension"></a>Zainstaluj rozszerzenie niestandardowego skryptu

W zależności od systemu operacyjnego dla maszyny wirtualnej można zainstalować rozszerzenie skryptu niestandardowego dla systemu Windows lub Linux.
 

### <a name="custom-script-extension-for-windows"></a>Rozszerzenie niestandardowego skryptu dla systemu Windows

Aby wdrożyć rozszerzenie niestandardowego skryptu dla systemu Windows dla maszyny wirtualnej działającej na urządzeniu, należy edytować `addCSExtWindowsVM.parameters.json` plik parametrów, a następnie wdrożyć szablon `addCSextensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Edytuj plik parametrów

Plik `addCSExtWindowsVM.parameters.json` przyjmuje następujące parametry:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of VM>" 
        },
        "extensionName": {
            "value": "<Name of extension>" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Podaj nazwę maszyny wirtualnej, nazwę rozszerzenia i polecenie, które chcesz wykonać.

Oto przykładowy plik parametrów, który został użyty w tym artykule.

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM5" 
        },
        "extensionName": {
            "value": "CustomScriptExtension" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "md C:\\Users\\Public\\Documents\\test"
            }
        }
    }
}
```
#### <a name="deploy-template"></a>Wdrażanie szablonu

Wdróż szablon `addCSextensiontoVM.json` . Ten szablon wdraża rozszerzenie dla istniejącej maszyny wirtualnej. Uruchom następujące polecenie:

```powershell
$templateFile = "<Path to addCSExtensiontoVM.json file>"
$templateParameterFile = "<Path to addCSExtWindowsVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
```
> [!NOTE]
> Wdrożenie rozszerzenia to długotrwałe zadanie i trwa około 10 minut.

Oto przykładowe dane wyjściowe:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment7"

DeploymentName          : deployment7
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/17/2020 10:07:44 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM5
                          extensionName    String                     CustomScriptExtension
                          publisher        String                     Microsoft.Compute
                          type             String                     CustomScriptExtension
                          typeHandlerVersion  String                     1.10
                          settings         Object                     {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
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
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
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

Dane wyjściowe rozszerzenia są rejestrowane w plikach znalezionych w następującym folderze na docelowej maszynie wirtualnej. 

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Określone pliki zostaną pobrane do następującego folderu na docelowej maszynie wirtualnej.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
gdzie <n> jest dziesiętną liczbą całkowitą, która może ulec zmianie między wykonaniami rozszerzenia. Wartość 1. * jest zgodna z rzeczywistą, bieżącą `typeHandlerVersion` wartością rozszerzenia. Na przykład faktyczny katalog w tym wystąpieniu był `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.9\Downloads\0` . 


W tym wystąpieniu polecenie, które ma zostać wykonane dla niestandardowego rozszerzenia: `md C:\\Users\\Public\\Documents\\test` . Po pomyślnym zainstalowaniu rozszerzenia można sprawdzić, czy katalog został utworzony na maszynie wirtualnej w określonej ścieżce w poleceniu. 


### <a name="custom-script-extension-for-linux"></a>Niestandardowe rozszerzenie skryptu dla systemu Linux

Aby wdrożyć rozszerzenie niestandardowego skryptu dla systemu Windows dla maszyny wirtualnej działającej na urządzeniu, należy edytować `addCSExtLinuxVM.parameters.json` plik parametrów, a następnie wdrożyć szablon `addCSExtensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Edytuj plik parametrów

Plik `addCSExtLinuxVM.parameters.json` przyjmuje następujące parametry:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of your VM>" 
        },
        "extensionName": {
            "value": "<Name of your extension>" 
        },
        "publisher": {
            "value": "Microsoft.Azure.Extensions" 
        },
        "type": {
            "value": "CustomScript" 
        },
        "typeHandlerVersion": {
            "value": "2.0" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Podaj nazwę maszyny wirtualnej, nazwę rozszerzenia i polecenie, które chcesz wykonać.

Oto przykładowy plik parametrów, który został użyty w tym artykule:

```powershell
$templateFile = "<Path to addCSExtensionToVM.json file>"
$templateParameterFile = "<Path to addCSExtLinuxVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
``` 

> [!NOTE]
> Wdrożenie rozszerzenia to długotrwałe zadanie i trwa około 10 minut.

Oto przykładowe dane wyjściowe:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensionToVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment99"

DeploymentName          : deployment99
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/18/2020 1:55:23 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM6
                          extensionName    String                     LinuxCustomScriptExtension
                          publisher        String                     Microsoft.Azure.Extensions
                          type             String                     CustomScript
                          typeHandlerVersion  String                     2.0
                          settings         Object                     {
                            "commandToExecute": "sudo echo 'some text' >> /home/Administrator/file2.txt"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```

`commandToExecute`Został ustawiony tak, aby utworzył plik `file2.txt` w `/home/Administrator` katalogu, a zawartość pliku to `some text` . W takim przypadku można sprawdzić, czy plik został utworzony w określonej ścieżce.

```powershell
Administrator@VM6:~$ dir
file2.txt
Administrator@VM6:~$ cat file2.txt
some text
Administrator@VM6:
```

#### <a name="track-deployment-status"></a>Śledzenie stanu wdrożenia    
    
Template deployment to długotrwałe zadanie. Aby sprawdzić stan wdrożenia dla danej maszyny wirtualnej, Otwórz inną sesję programu PowerShell (Uruchom jako administrator). Uruchom następujące polecenie: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Oto przykładowe dane wyjściowe: 

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
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

Dane wyjściowe wykonania rozszerzenia są rejestrowane w następującym pliku: `/var/lib/waagent/custom-script/download/0/` .


## <a name="remove-custom-script-extension"></a>Usuń rozszerzenie niestandardowego skryptu

Aby usunąć rozszerzenie niestandardowego skryptu, użyj następującego polecenia:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Oto przykładowe dane wyjściowe:

```powershell
PS C:\WINDOWS\system32> Remove-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM6 -Name LinuxCustomScriptExtension
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Yes
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


## <a name="next-steps"></a>Następne kroki

[Polecenia cmdlet Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
