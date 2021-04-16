---
title: Włączanie rozszerzenia maszyny wirtualnej przy użyciu Azure Resource Manager szablonu
description: W tym artykule opisano sposób wdrażania rozszerzeń maszyn wirtualnych na serwerach z Azure Arc działających w środowiskach chmury hybrydowej przy użyciu Azure Resource Manager szablonu.
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: 095f95192a2054d34e438d8683ac9c2e20a824f1
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389642"
---
# <a name="enable-azure-vm-extensions-by-using-arm-template"></a>Włączanie rozszerzeń maszyn wirtualnych platformy Azure przy użyciu szablonu usługi ARM

W tym artykule pokazano, jak używać szablonu usługi Azure Resource Manager usługi Arm do wdrażania rozszerzeń maszyn wirtualnych platformy Azure obsługiwanych Azure Arc serwerach z włączoną obsługą usługi .

Rozszerzenia maszyn wirtualnych można dodać do Azure Resource Manager szablonu i wykonać wraz z wdrożeniem szablonu. Rozszerzenia maszyn wirtualnych obsługiwane przez serwery z usługą Arc umożliwia wdrożenie obsługiwanego rozszerzenia maszyny wirtualnej na maszynach z systemem Linux lub Windows przy użyciu Azure PowerShell. Każdy z poniższych przykładów zawiera plik szablonu i plik parametrów z przykładami wartości do podania w szablonie.

>[!NOTE]
>Chociaż wiele rozszerzeń może być tworzona wsadowo i przetwarzana, są one instalowane szeregowo. Po zakończeniu pierwszej instalacji rozszerzenia podejmowana jest próba zainstalowania następnego rozszerzenia.

> [!NOTE]
> Azure Arc z włączoną obsługą usługi nie obsługują wdrażania rozszerzeń maszyn wirtualnych i zarządzania nimi na maszynach wirtualnych platformy Azure. W przypadku maszyn wirtualnych platformy Azure zobacz następujący artykuł [z omówieniem rozszerzenia maszyny wirtualnej.](../../virtual-machines/extensions/overview.md)

## <a name="deploy-the-log-analytics-vm-extension"></a>Wdrażanie rozszerzenia maszyny wirtualnej usługi Log Analytics

Aby łatwo wdrożyć agenta usługi Log Analytics, dostępny jest następujący przykład instalacji agenta w systemie Windows lub Linux.

### <a name="template-file-for-linux"></a>Plik szablonu dla systemu Linux

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

### <a name="template-file-for-windows"></a>Plik szablonu dla systemu Windows

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

### <a name="parameter-file"></a>Plik parametrów

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

Zapisz pliki szablonu i parametrów na dysku, a następnie edytuj plik parametrów z odpowiednimi wartościami dla wdrożenia. Następnie możesz zainstalować rozszerzenie na wszystkich połączonych maszynach w grupie zasobów za pomocą następującego polecenia. Polecenie używa *parametru TemplateFile* do określenia szablonu i *parametru TemplateParameterFile w* celu określenia pliku zawierającego parametry i wartości parametrów.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgent.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentParms.json"
```

## <a name="deploy-the-custom-script-extension"></a>Wdrażanie rozszerzenia niestandardowego skryptu

Aby użyć rozszerzenia niestandardowego skryptu, przedstawiono następujący przykład do uruchomienia w systemach Windows i Linux. Jeśli nie masz informacji o rozszerzeniu niestandardowego skryptu, zobacz Rozszerzenie niestandardowego skryptu dla systemu [Windows](../../virtual-machines/extensions/custom-script-windows.md) lub Rozszerzenie niestandardowego skryptu [dla systemu Linux.](../../virtual-machines/extensions/custom-script-linux.md) Istnieje kilka różnych cech, które należy zrozumieć podczas korzystania z tego rozszerzenia na maszynach hybrydowych:

* Lista obsługiwanych systemów operacyjnych z rozszerzeniem niestandardowego skryptu maszyny wirtualnej platformy Azure nie ma zastosowania Azure Arc serwerach z włączoną obsługą. Listę obsługiwanych systemu operacyjnego dla serwerów z usługą Arc można [znaleźć tutaj.](agent-overview.md#supported-operating-systems)

* Szczegóły konfiguracji dotyczące usługi Azure Virtual Machine Scale Sets lub klasycznych maszyn wirtualnych nie mają zastosowania.

* Jeśli maszyny muszą pobrać skrypt zewnętrznie i mogą komunikować się tylko za pośrednictwem serwera proxy, należy skonfigurować agenta Connected [Machine,](manage-agent.md#update-or-remove-proxy-settings) aby ustawić zmienną środowiskową serwera proxy.

Konfiguracja rozszerzenia niestandardowego skryptu określa takie rzeczy jak lokalizacja skryptu i polecenie do uruchomienia. Ta konfiguracja jest określona w szablonie Azure Resource Manager, który został podany poniżej dla maszyn hybrydowych z systemami Linux i Windows.

### <a name="template-file-for-linux"></a>Plik szablonu dla systemu Linux

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

### <a name="template-file-for-windows"></a>Plik szablonu dla systemu Windows

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

### <a name="parameter-file"></a>Plik parametrów

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

## <a name="deploy-the-dependency-agent-extension"></a>Wdrażanie rozszerzenia agenta zależności

Aby użyć Azure Monitor agenta zależności, przedstawiono następujący przykład do uruchomienia w systemach Windows i Linux. Jeśli nie masz informacji o agencie Zależności, zobacz [Overview of Azure Monitor agents (Omówienie agentów zależności).](../../azure-monitor/agents/agents-overview.md#dependency-agent)

### <a name="template-file-for-linux"></a>Plik szablonu dla systemu Linux

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

### <a name="template-file-for-windows"></a>Plik szablonu dla systemu Windows

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

### <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Zapisz plik szablonu na dysku. Następnie możesz wdrożyć rozszerzenie na połączonej maszynie za pomocą następującego polecenia.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\DependencyAgent.json"
```

## <a name="deploy-azure-key-vault-vm-extension-preview"></a>Wdrażanie Azure Key Vault maszyny wirtualnej (wersja zapoznawcza)

Poniższy kod JSON przedstawia schemat rozszerzenia maszyny Key Vault wirtualnej (wersja zapoznawcza). Rozszerzenie nie wymaga ustawień chronionych — wszystkie jego ustawienia są traktowane jako informacje publiczne. Rozszerzenie wymaga listy monitorowanych certyfikatów, częstotliwości sondowania i docelowego magazynu certyfikatów. W szczególności:

### <a name="template-file-for-linux"></a>Plik szablonu dla systemu Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "autoUpgradeMinorVersion":{
            "type": "bool"
        },
        "pollingIntervalInS":{
          "type": "int"
        },
        "certificateStoreName":{
          "type": "string"
        },
        "certificateStoreLocation":{
          "type": "string"
        },
        "observedCertificates":{
          "type": "string"
        },
        "msiEndpoint":{
          "type": "string"
        },
        "msiClientId":{
          "type": "string"
        }
},
"resources": [
   {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/KVVMExtensionForLinux')]",
      "apiVersion": "2019-12-12",
      "location": "[parameters('location')]",
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ignored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
          },
          "authenticationSettings": {
                "msiEndpoint":  <MSI endpoint e.g.: "http://localhost:40342/metadata/identity">,
                "msiClientId":  <MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
      }
    }
  }
 ]
}
```

### <a name="template-file-for-windows"></a>Plik szablonu dla systemu Windows

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "autoUpgradeMinorVersion":{
            "type": "bool"
        },
        "pollingIntervalInS":{
          "type": "int"
        },
        "certificateStoreName":{
          "type": "string"
        },
        "linkOnRenewal":{
          "type": "bool"
        },
        "certificateStoreLocation":{
          "type": "string"
        },
        "requireInitialSync":{
          "type": "bool"
        },
        "observedCertificates":{
          "type": "string"
        },
        "msiEndpoint":{
          "type": "string"
        },
        "msiClientId":{
          "type": "string"
        }
},
"resources": [
   {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/KVVMExtensionForWindows')]",
      "apiVersion": "2019-12-12",
      "location": "[parameters('location')]",
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": "3600",
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net"
        },
        "authenticationSettings": {
                "msiEndpoint": <MSI endpoint e.g.: "http://localhost:40342/metadata/identity">,
                "msiClientId": <MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
      }
    }
  }
 ]
}
```

> [!NOTE]
> Adresy URL obserwowanych certyfikatów powinny mieć postać `https://myVaultName.vault.azure.net/secrets/myCertName` .
>
> Wynika to z `/secrets` tego, że ścieżka zwraca pełny certyfikat, w tym klucz prywatny, podczas gdy ścieżka `/certificates` nie. Więcej informacji na temat certyfikatów można znaleźć tutaj: [Key Vault Certyfikatów](../../key-vault/general/about-keys-secrets-certificates.md)

### <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Zapisz plik szablonu na dysku. Następnie możesz wdrożyć rozszerzenie na połączonej maszynie za pomocą następującego polecenia.

> [!NOTE]
> Rozszerzenie maszyny wirtualnej wymaga przypisania tożsamości przypisanej przez system w celu uwierzytelnienia w magazynie kluczy. Zobacz [Jak uwierzytelniać się w Key Vault przy użyciu tożsamości zarządzanej](managed-identity-authentication.md) dla serwerów z systemami Windows i Linux Arc.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\KeyVaultExtension.json"
```

## <a name="deploy-the-azure-defender-integrated-scanner"></a>Wdrażanie zintegrowanego Azure Defender skanera

Aby użyć zintegrowanego Azure Defender skanera, można uruchomić następujący przykład w systemach Windows i Linux. Jeśli nie masz informacji o zintegrowanym skanerze, zobacz Overview of Azure Defender of [the vulnerability assessment solution](../../security-center/deploy-vulnerability-assessment-vm.md) for hybrid machines (Omówienie rozwiązania do oceny luk w zabezpieczeniach dla maszyn hybrydowych).

### <a name="template-file-for-windows"></a>Plik szablonu dla systemu Windows

```json
{
  "properties": {
    "mode": "Incremental",
    "template": {
      "contentVersion": "1.0.0.0",
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "parameters": {
        "vmName": {
          "type": "string"
        },
        "apiVersionByEnv": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "resourceType/providers/WindowsAgent.AzureSecurityCenter",
          "name": "[concat(parameters('vmName'), '/Microsoft.Security/default')]",
          "apiVersion": "[parameters('apiVersionByEnv')]"
        }
      ]
    },
    "parameters": {
      "vmName": {
        "value": "resourceName"
      },
      "apiVersionByEnv": {
        "value": "2015-06-01-preview"
      }
    }
  }
}
```

### <a name="template-file-for-linux"></a>Plik szablonu dla systemu Linux

```json
{
  "properties": {
    "mode": "Incremental",
    "template": {
      "contentVersion": "1.0.0.0",
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "parameters": {
        "vmName": {
          "type": "string"
        },
        "apiVersionByEnv": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "resourceType/providers/LinuxAgent.AzureSecurityCenter",
          "name": "[concat(parameters('vmName'), '/Microsoft.Security/default')]",
          "apiVersion": "[parameters('apiVersionByEnv')]"
        }
      ]
    },
    "parameters": {
      "vmName": {
        "value": "resourceName"
      },
      "apiVersionByEnv": {
        "value": "2015-06-01-preview"
      }
    }
  }
}
```

### <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Zapisz plik szablonu na dysku. Następnie możesz wdrożyć rozszerzenie na połączonej maszynie za pomocą następującego polecenia.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\AzureDefenderScanner.json"
```

## <a name="next-steps"></a>Następne kroki

* Rozszerzenia maszyn wirtualnych można wdrażać i usuwać oraz zarządzać nimi przy użyciu interfejsu [Azure PowerShell](manage-vm-extensions-powershell.md), z witryny [Azure Portal](manage-vm-extensions-portal.md)lub interfejsu wiersza polecenia [platformy Azure.](manage-vm-extensions-cli.md)

* Informacje dotyczące rozwiązywania problemów można znaleźć w [przewodniku Rozwiązywanie problemów z rozszerzeniami maszyn wirtualnych.](troubleshoot-vm-extensions.md)