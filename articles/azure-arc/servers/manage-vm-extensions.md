---
title: Zarządzanie rozszerzeniami maszyny wirtualnej za pomocą serwerów z obsługą usługi Azure Arc
description: Serwery z obsługą usługi Azure Arc mogą zarządzać wdrożeniem rozszerzeń maszyn wirtualnych, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji z maszynami wirtualnymi spoza platformy Azure.
ms.date: 09/23/2020
ms.topic: conceptual
ms.openlocfilehash: 59772022672781e926e439e9740f8e8a02c8b4d3
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108120"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Zarządzanie rozszerzeniami maszyn wirtualnych za pomocą serwerów z obsługą usługi Azure Arc

Rozszerzenia maszyn wirtualnych to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure. Na przykład jeśli maszyna wirtualna wymaga instalacji oprogramowania, ochrony antywirusowej lub uruchomienia skryptu w swoim środowisku, można użyć rozszerzenia maszyny wirtualnej.

Serwery z obsługą usługi Azure ARC umożliwiają Wdrażanie rozszerzeń maszyn wirtualnych platformy Azure na maszynach wirtualnych z systemami Windows i Linux, które upraszczają zarządzanie maszynami w chmurze w środowisku lokalnym, w granicach i innych środowiskach chmurowych.

## <a name="key-benefits"></a>Najważniejsze korzyści

Obsługa rozszerzenia maszyny wirtualnej z obsługą usługi Azure Arc zapewnia następujące korzyści:

* Użyj [konfiguracji stanu Azure Automation](../../automation/automation-dsc-overview.md) , aby centralnie przechowywać konfiguracje i zachować żądany stan maszyn połączonych hybrydowo włączonych przy użyciu rozszerzenia maszyny wirtualnej DSC.

* Zbieraj dane dziennika do analizy z [dziennikami w Azure monitor](../../azure-monitor/platform/data-platform-logs.md) włączone za pomocą rozszerzenia maszyny wirtualnej log Analytics Agent. Jest to przydatne w przypadku wykonywania złożonej analizy danych z różnych źródeł.

* Dzięki [Azure monitor dla maszyn wirtualnych](../../azure-monitor/insights/vminsights-overview.md)analizuje wydajność maszyn wirtualnych z systemami Windows i Linux oraz monitorują procesy i zależności od innych zasobów i procesów zewnętrznych. Jest to realizowane przez włączenie zarówno rozszerzenia agenta Log Analytics, jak i agenta zależności maszyny wirtualnej.

* Pobieranie i wykonywanie skryptów na maszynach połączonych hybrydowo przy użyciu niestandardowego rozszerzenia skryptu. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalacji oprogramowania lub innych zadań związanych z konfiguracją lub zarządzaniem.

## <a name="availability"></a>Dostępność

Funkcjonalność rozszerzenia maszyny wirtualnej jest dostępna tylko na liście [obsługiwanych regionów](overview.md#supported-regions). Upewnij się, że Twoja maszyna została dołączona do jednego z tych regionów.

## <a name="extensions"></a>Rozszerzenia

W tej wersji obsługiwane są następujące rozszerzenia maszyn wirtualnych na komputerach z systemem Windows i Linux.

|Rozszerzenie |System operacyjny |Publisher |Dodatkowe informacje |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Rozszerzenie niestandardowego skryptu systemu Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Rozszerzenie DSC środowiska Windows PowerShell](../../virtual-machines/extensions/dsc-windows.md)|
|Agent usługi Log Analytics |Windows |Microsoft. EnterpriseCloud. Monitoring |[Log Analytics rozszerzenie maszyny wirtualnej dla systemu Windows](../../virtual-machines/extensions/oms-windows.md)|
|Agent zależności firmy Microsoft | Windows |Microsoft.Compute | [Rozszerzenie maszyny wirtualnej agenta zależności dla systemu Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft. Azure. Extension |[Rozszerzenie niestandardowego skryptu systemu Linux w wersji 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft. OSTCExtensions |[Rozszerzenie DSC programu PowerShell dla systemu Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Agent usługi Log Analytics |Linux |Microsoft. EnterpriseCloud. Monitoring |[Log Analytics rozszerzenie maszyny wirtualnej dla systemu Linux](../../virtual-machines/extensions/oms-linux.md) |
|Agent zależności firmy Microsoft | Linux |Microsoft.Compute | [Rozszerzenie maszyny wirtualnej agenta zależności dla systemu Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |

Rozszerzenia maszyn wirtualnych można uruchamiać z szablonami Azure Resource Manager, z Azure Portal lub Azure PowerShell na serwerach hybrydowych zarządzanych przez serwery z obsługą łuku.

Aby dowiedzieć się więcej na temat pakietu agenta połączonego z platformą Azure i szczegółowych informacji o składniku agenta rozszerzeń, zobacz [Omówienie agenta](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Wymagania wstępne

Ta funkcja jest zależna od następujących dostawców zasobów platformy Azure w ramach subskrypcji:

* **Microsoft. HybridCompute**
* **Microsoft. GuestConfiguration**

Jeśli nie zostały one jeszcze zarejestrowane, wykonaj kroki opisane w sekcji [Rejestrowanie dostawców zasobów platformy Azure](agent-overview.md#register-azure-resource-providers).

Rozszerzenie maszyny wirtualnej agenta Log Analytics dla systemu Linux wymaga zainstalowania języka Python 2. x na maszynie docelowej.

### <a name="connected-machine-agent"></a>Agent połączonej maszyny

Sprawdź, czy maszyna jest zgodna z [obsługiwanymi wersjami](agent-overview.md#supported-operating-systems) systemu operacyjnego Windows i Linux dla agenta połączonego z platformą Azure.

Minimalna wersja agenta połączonej maszyny obsługiwana w przypadku tej funkcji w systemie Windows i Linux to wersja 1,0.

Aby uaktualnić maszynę do wymaganej wersji agenta, zobacz [upgrade Agent](manage-agent.md#upgrading-agent).

## <a name="enable-extensions-from-the-portal"></a>Włącz rozszerzenia portalu

Rozszerzenia maszyny wirtualnej mogą być stosowane do Twojego łuku dla maszyny zarządzanej przez serwer za pomocą Azure Portal.

1. W przeglądarce przejdź do [Azure Portal](https://portal.azure.com).

2. W portalu przejdź do opcji **serwery — Azure Arc** i wybierz maszynę hybrydową z listy.

3. Wybierz pozycję **rozszerzenia**, a następnie wybierz pozycję **Dodaj**. Wybierz odpowiednie rozszerzenie z listy dostępnych rozszerzeń i postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze. W tym przykładzie zostanie wdrożone rozszerzenie maszyny wirtualnej Log Analytics.

    ![Wybierz rozszerzenie maszyny wirtualnej dla wybranej maszyny](./media/manage-vm-extensions/add-vm-extensions.png)

    W poniższym przykładzie przedstawiono instalację rozszerzenia maszyny wirtualnej Log Analytics z Azure Portal:

    ![Zainstaluj rozszerzenie maszyny wirtualnej Log Analytics](./media/manage-vm-extensions/mma-extension-config.png)

    Aby ukończyć instalację, należy podać identyfikator obszaru roboczego i klucz podstawowy. Jeśli nie wiesz, jak znaleźć te informacje, zobacz temat [Uzyskiwanie identyfikatora i klucza obszaru roboczego](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

4. Po potwierdzeniu wymaganych informacji wybierz pozycję **Utwórz**. Zostanie wyświetlone podsumowanie wdrożenia i można sprawdzić stan wdrożenia.

>[!NOTE]
>Chociaż wiele rozszerzeń można wsadowo i przetwarzać, są one instalowane seryjnie. Po zakończeniu instalacji pierwszego rozszerzenia zostanie podjęta próba instalacji następnego rozszerzenia.

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Rozszerzenia maszyny wirtualnej można dodać do szablonu Azure Resource Manager i wykonać przy użyciu wdrożenia szablonu. Rozszerzenia maszyn wirtualnych obsługiwane przez serwery z obsługą Arc można wdrożyć na maszynach z systemem Linux lub Windows przy użyciu Azure PowerShell. Każdy przykład poniżej zawiera plik szablonu i plik parametrów z przykładowymi wartościami do udostępnienia szablonowi.

>[!NOTE]
>Chociaż wiele rozszerzeń można wsadowo i przetwarzać, są one instalowane seryjnie. Po zakończeniu instalacji pierwszego rozszerzenia zostanie podjęta próba instalacji następnego rozszerzenia.

### <a name="deploy-the-log-analytics-vm-extension"></a>Wdrażanie rozszerzenia maszyny wirtualnej Log Analytics

Aby łatwo wdrożyć agenta Log Analytics, podano następujący przykład w celu zainstalowania agenta w systemie Windows lub Linux.

#### <a name="template-file-for-linux"></a>Plik szablonu dla systemu Linux

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Plik szablonu dla systemu Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Plik parametrów

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

Zapisz szablon i pliki parametrów na dysku, a następnie edytuj plik parametrów z odpowiednimi wartościami dla danego wdrożenia. Następnie można zainstalować rozszerzenie na wszystkich połączonych maszynach w grupie zasobów przy użyciu poniższego polecenia. Polecenie używa parametru *TemplateFile* , aby określić szablon i parametr *TemplateParameterFile* , aby określić plik, który zawiera parametry i wartości parametrów.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgentWin.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentWinParms.json"
```

### <a name="deploy-the-custom-script-extension"></a>Wdróż rozszerzenie niestandardowego skryptu

Aby użyć niestandardowego rozszerzenia skryptu, do uruchamiania w systemach Windows i Linux jest dostarczany następujący przykład. Jeśli nie znasz rozszerzenia niestandardowego skryptu, zobacz [rozszerzenie niestandardowego skryptu dla systemu Windows](../../virtual-machines/extensions/custom-script-windows.md) lub [rozszerzenie niestandardowego skryptu dla Linux](../../virtual-machines/extensions/custom-script-linux.md). Istnieje kilka różnych cech, które należy zrozumieć w przypadku używania tego rozszerzenia z maszynami hybrydowymi:

* Lista obsługiwanych systemów operacyjnych z rozszerzeniem niestandardowego skryptu maszyny wirtualnej platformy Azure nie ma zastosowania do serwerów z obsługą usługi Azure Arc. Listę obsługiwanych serwerów OSs dla serwera z włączoną funkcją Arc można znaleźć [tutaj](agent-overview.md#supported-operating-systems).

* Szczegóły konfiguracji dotyczące usługi Azure Virtual Machine Scale Sets lub klasycznych maszyn wirtualnych nie mają zastosowania.

* Jeśli maszyny wymagają pobrania skryptu zewnętrznie i mogą komunikować się tylko za pomocą serwera proxy, należy [skonfigurować agenta połączonej maszyny](manage-agent.md#update-or-remove-proxy-settings) w celu ustawienia zmiennej środowiskowej serwera proxy.

Konfiguracja rozszerzenia niestandardowego skryptu określa elementy, takie jak lokalizacja skryptu i polecenie do uruchomienia. Ta konfiguracja jest określona w szablonie Azure Resource Manager, który znajduje się poniżej dla maszyn hybrydowych z systemem Linux i Windows.

#### <a name="template-file-for-linux"></a>Plik szablonu dla systemu Linux

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

#### <a name="template-file-for-windows"></a>Plik szablonu dla systemu Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Plik parametrów

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

### <a name="deploy-the-powershell-dsc-extension"></a>Wdróż rozszerzenie DSC programu PowerShell

Aby można było użyć rozszerzenia DSC programu PowerShell, do uruchamiania w systemach Windows i Linux jest dostarczany następujący przykład. Jeśli nie znasz rozszerzenia DSC programu PowerShell, zobacz [Omówienie obsługi rozszerzenia DSC](../../virtual-machines/extensions/dsc-overview.md). Istnieje kilka różnych cech, które należy zrozumieć w przypadku używania tego rozszerzenia z maszynami hybrydowymi:

* Lista obsługiwanych systemów operacyjnych z rozszerzeniem DSC programu PowerShell dla maszyny wirtualnej platformy Azure nie ma zastosowania do serwerów z obsługą usługi Azure Arc. Listę obsługiwanych serwerów OSs dla serwera z włączoną funkcją Arc można znaleźć [tutaj](agent-overview.md#supported-operating-systems).

* Jeśli maszyny wymagają pobrania skryptu zewnętrznie i mogą komunikować się tylko za pomocą serwera proxy, należy [skonfigurować agenta połączonej maszyny](manage-agent.md#update-or-remove-proxy-settings) w celu ustawienia zmiennej środowiskowej serwera proxy.

#### <a name="template-file-for-linux"></a>Plik szablonu dla systemu Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vm, will be used as DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "mode": {
            "type": "string",
            "defaultValue": "Push",
            "metadata": {
                "description": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)"
            },
            "allowedValues": [
                "Push",
                "Pull",
                "Install",
                "Register"
            ]
        },
        "fileUri": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The uri of the MOF file/Meta MOF file/resource ZIP file"
            }
        },
        "registrationUrl": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The URL of the Azure Automation account"
            }
        },
        "registrationKey": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The access key of the Azure Automation account"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/DSCForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.OSTCExtensions",
                "type": "DSCForLinux",
                "settings": {
                    "Mode": "[parameters('mode')]",
                    "FileUri": "[parameters('fileUri')]"
                },
                "protectedSettings": {
                    "RegistrationUrl": "[parameters('registrationUrl')]",
                    "RegistrationKey": "[parameters('registrationKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Plik szablonu dla systemu Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "modulesUrl": {
            "type": "string"
        },
        "configurationFunction": {
            "type": "string"
        },
        "properties": {
            "type": "string",
            "defaultValue": ""
        },
        "dataBlobUri": {
            "type": "string",
            "defaultValue": ""
        },
        "wmfVersion": {
            "type": "string",
            "defaultValue": "latest",
            "allowedValues": [
                "4.0",
                "5.0",
                "5.1",
                "latest"
            ]
        },
        "privacy": {
            "type": "string",
            "defaultValue": ""
        },
        "autoUpdate": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/Microsoft.Powershell.DSC')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Powershell",
                "type": "DSC",
                "autoUpgradeMinorVersion": "[parameters('autoUpdate')]",
                "settings": {
                    "ModulesUrl": "[parameters('modulesUrl')]",
                    "ConfigurationFunction": "[parameters('configurationFunction')]",
                    "Properties": "[parameters('properties')]",
                    "WmfVersion": "[parameters('wmfVersion')]",
                    "Privacy": {
                        "DataCollection": "[parameters('privacy')]"
                    }
                },
                "protectedSettings": {
                    "DataBlobUri": "[parameters('dataBlobUri')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Plik parametrów

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "dscExtension",
        "label": "Add DSC Extension",
        "elements": [
          {
            "name": "Mode",
            "type": "Microsoft.Common.OptionsGroup",
            "label": "Mode",
            "defaultValue": 0,
            "toolTip": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Push",
                  "value": "Push"
                },
                {
                  "label": "Pull",
                  "value": "Pull"
                },
                {
                  "label": "Install",
                  "value": "Install"
                },
                {
                  "label": "Register",
                  "value": "Register"
                }
              ]
            },
            "visible": true
          },
          {
            "name": "FileUri",
            "type": "Microsoft.Common.FileUpload",
            "label": "File URI",
            "toolTip": "The uri of the MOF file/Meta MOF file/resource ZIP file",
            "constraints": {
              "required": false,
              "accept": ".psd1"
            },
            "options": {
              "multiple": false,
              "uploadMode": "url",
              "openMode": "binary",
              "encoding": "UTF-8"
            }
          },
          {
            "name": "RegistrationUrl",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration URL",
            "toolTip": "The URL of the Azure Automation account",
            "constraints": {
              "required": false
            }
          },
          {
            "name": "RegistrationKey",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration key",
            "toolTip": "The access key of the Azure Automation account",
            "constraints": {
              "required": false
            }
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "mode": "[steps('dscExtension').Mode]",
      "fileUri": "[steps('dscExtension').FileUri]",
      "registrationUrl": "[steps('dscExtension').RegistrationUrl]",
      "registrationKey": "[steps('dscExtension').RegistrationKey]"
    }
  }
}
```

### <a name="dependency-agent"></a>Agent zależności

Aby użyć rozszerzenia Agent zależności Azure Monitor, do uruchamiania w systemach Windows i Linux jest dostarczany następujący przykład. Jeśli nie znasz agenta zależności, zobacz [Omówienie agentów Azure monitor](../../azure-monitor/platform/agents-overview.md#dependency-agent).

#### <a name="template-file-for-linux"></a>Plik szablonu dla systemu Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

#### <a name="template-file-for-windows"></a>Plik szablonu dla systemu Windows

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

## <a name="uninstall-extension"></a>Odinstaluj rozszerzenie

Usunięcie jednego lub większej liczby rozszerzeń z serwera z włączonym Łukiemm łuku można wykonać tylko z Azure Portal. Wykonaj następujące kroki, aby usunąć rozszerzenie.

1. W przeglądarce przejdź do [Azure Portal](https://portal.azure.com).

2. W portalu przejdź do opcji **serwery — Azure Arc** i wybierz maszynę hybrydową z listy.

3. Wybierz **rozszerzenia**, a następnie wybierz rozszerzenie z listy zainstalowanych rozszerzeń.

4. Wybierz pozycję **Odinstaluj** , a po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak** , aby wykonać operację.

## <a name="next-steps"></a>Następne kroki

* Informacje dotyczące rozwiązywania problemów można znaleźć w [podręczniku Rozwiązywanie problemów z maszynami](troubleshoot-vm-extensions.md)wirtualnymi.

* Dowiedz się, jak zarządzać maszyną za pomocą [Azure Policy](../../governance/policy/overview.md), na przykład w [konfiguracji gościa](../../governance/policy/concepts/guest-configuration.md)maszyny wirtualnej, sprawdzając, czy komputer jest raportowany do oczekiwanego log Analytics obszaru roboczego, włącz monitorowanie za pomocą [Azure monitor z maszynami wirtualnymi](../../azure-monitor/insights/vminsights-enable-policy.md)i wiele więcej.

* Dowiedz się więcej o [agencie log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics dla systemów Windows i Linux jest wymagany, gdy chcesz zbierać dane monitorowania systemu operacyjnego i obciążenia, zarządzać nimi za pomocą elementów Runbook lub funkcji usługi Automation, takich jak Update Management, lub korzystać z innych usług platformy Azure, takich jak [Azure Security Center](../../security-center/security-center-introduction.md).