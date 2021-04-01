---
title: Włączanie rozszerzenia maszyny wirtualnej przy użyciu szablonu Azure Resource Manager
description: W tym artykule opisano sposób wdrażania rozszerzeń maszyn wirtualnych na serwerach z obsługą usługi Azure ARC z włączonymi środowiskami chmury hybrydowej przy użyciu szablonu Azure Resource Manager.
ms.date: 03/01/2021
ms.topic: conceptual
ms.openlocfilehash: 88296cd4f410defcaf7db15507ddac42e80cba2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688267"
---
# <a name="enable-azure-vm-extensions-by-using-arm-template"></a>Włączanie rozszerzeń maszyny wirtualnej platformy Azure przy użyciu szablonu ARM

W tym artykule przedstawiono sposób użycia szablonu Azure Resource Manager (szablon ARM) do wdrażania rozszerzeń maszyn wirtualnych platformy Azure obsługiwanych przez serwery z obsługą usługi Azure Arc.

Rozszerzenia maszyny wirtualnej można dodać do szablonu Azure Resource Manager i wykonać przy użyciu wdrożenia szablonu. Rozszerzenia maszyn wirtualnych obsługiwane przez serwery z obsługą Arc można wdrożyć na maszynach z systemem Linux lub Windows przy użyciu Azure PowerShell. Każdy przykład poniżej zawiera plik szablonu i plik parametrów z przykładowymi wartościami do udostępnienia szablonowi.

>[!NOTE]
>Chociaż wiele rozszerzeń można wsadowo i przetwarzać, są one instalowane seryjnie. Po zakończeniu instalacji pierwszego rozszerzenia zostanie podjęta próba instalacji następnego rozszerzenia.

## <a name="deploy-the-log-analytics-vm-extension"></a>Wdrażanie rozszerzenia maszyny wirtualnej Log Analytics

Aby łatwo wdrożyć agenta Log Analytics, podano następujący przykład w celu zainstalowania agenta w systemie Windows lub Linux.

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

Zapisz szablon i pliki parametrów na dysku, a następnie edytuj plik parametrów z odpowiednimi wartościami dla danego wdrożenia. Następnie można zainstalować rozszerzenie na wszystkich połączonych maszynach w grupie zasobów przy użyciu poniższego polecenia. Polecenie używa parametru *TemplateFile* , aby określić szablon i parametr *TemplateParameterFile* , aby określić plik, który zawiera parametry i wartości parametrów.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgent.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentParms.json"
```

## <a name="deploy-the-custom-script-extension"></a>Wdróż rozszerzenie niestandardowego skryptu

Aby użyć niestandardowego rozszerzenia skryptu, do uruchamiania w systemach Windows i Linux jest dostarczany następujący przykład. Jeśli nie znasz rozszerzenia niestandardowego skryptu, zobacz [rozszerzenie niestandardowego skryptu dla systemu Windows](../../virtual-machines/extensions/custom-script-windows.md) lub [rozszerzenie niestandardowego skryptu dla Linux](../../virtual-machines/extensions/custom-script-linux.md). Istnieje kilka różnych cech, które należy zrozumieć w przypadku używania tego rozszerzenia z maszynami hybrydowymi:

* Lista obsługiwanych systemów operacyjnych z rozszerzeniem niestandardowego skryptu maszyny wirtualnej platformy Azure nie ma zastosowania do serwerów z obsługą usługi Azure Arc. Listę obsługiwanych serwerów OSs dla serwera z włączoną funkcją Arc można znaleźć [tutaj](agent-overview.md#supported-operating-systems).

* Szczegóły konfiguracji dotyczące usługi Azure Virtual Machine Scale Sets lub klasycznych maszyn wirtualnych nie mają zastosowania.

* Jeśli maszyny wymagają pobrania skryptu zewnętrznie i mogą komunikować się tylko za pomocą serwera proxy, należy [skonfigurować agenta połączonej maszyny](manage-agent.md#update-or-remove-proxy-settings) w celu ustawienia zmiennej środowiskowej serwera proxy.

Konfiguracja rozszerzenia niestandardowego skryptu określa elementy, takie jak lokalizacja skryptu i polecenie do uruchomienia. Ta konfiguracja jest określona w szablonie Azure Resource Manager, który znajduje się poniżej dla maszyn hybrydowych z systemem Linux i Windows.

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

## <a name="deploy-the-dependency-agent-extension"></a>Wdróż rozszerzenie agenta zależności

Aby użyć rozszerzenia Agent zależności Azure Monitor, do uruchamiania w systemach Windows i Linux jest dostarczany następujący przykład. Jeśli nie znasz agenta zależności, zobacz [Omówienie agentów Azure monitor](../../azure-monitor/agents/agents-overview.md#dependency-agent).

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

Zapisz plik szablonu na dysku. Następnie można wdrożyć rozszerzenie na połączonej maszynie przy użyciu poniższego polecenia.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\DependencyAgent.json"
```

## <a name="deploy-azure-key-vault-vm-extension-preview"></a>Wdróż rozszerzenie maszyny wirtualnej Azure Key Vault (wersja zapoznawcza)

Poniższy kod JSON przedstawia schemat rozszerzenia maszyny wirtualnej Key Vault (wersja zapoznawcza). Rozszerzenie nie wymaga ustawień chronionych — wszystkie jego ustawienia są uznawane za informacje publiczne. Rozszerzenie wymaga listy monitorowanych certyfikatów, częstotliwości sondowania i docelowego magazynu certyfikatów. W szczególności:

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
> Wynika to z faktu, że `/secrets` ścieżka zwraca pełny certyfikat, w tym klucz prywatny, podczas gdy `/certificates` ścieżka nie jest. Więcej informacji o certyfikatach można znaleźć tutaj: [Key Vault Certificates](../../key-vault/general/about-keys-secrets-certificates.md)

### <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Zapisz plik szablonu na dysku. Następnie można wdrożyć rozszerzenie na połączonej maszynie przy użyciu poniższego polecenia.

> [!NOTE]
> Rozszerzenie maszyny wirtualnej wymaga przypisania tożsamości przypisanej do systemu do uwierzytelniania w magazynie kluczy. Zobacz [Jak przeprowadzić uwierzytelnianie, Key Vault przy użyciu tożsamości zarządzanej](managed-identity-authentication.md) dla serwerów z systemem Windows i Linux.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\KeyVaultExtension.json"
```

## <a name="deploy-the-azure-defender-integrated-scanner"></a>Wdrażanie skanera zintegrowanego usługi Azure Defender

Aby można było korzystać z rozszerzenia skanera zintegrowanego z usługą Azure Defender, do uruchamiania w systemach Windows i Linux jest dostarczany następujący przykład. Jeśli nie znasz skanera zintegrowanego, zobacz [Omówienie rozwiązania do oceny luk w zabezpieczeniach usługi Azure Defender](../../security-center/deploy-vulnerability-assessment-vm.md) dla maszyn hybrydowych.

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

Zapisz plik szablonu na dysku. Następnie można wdrożyć rozszerzenie na połączonej maszynie przy użyciu poniższego polecenia.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\AzureDefenderScanner.json"
```

## <a name="next-steps"></a>Następne kroki

* Korzystając z [Azure PowerShell](manage-vm-extensions-powershell.md), z [Azure Portal](manage-vm-extensions-portal.md)lub [interfejsu wiersza polecenia platformy Azure](manage-vm-extensions-cli.md), można wdrażać i usuwać rozszerzenia maszyn wirtualnych oraz zarządzać nimi.

* Informacje dotyczące rozwiązywania problemów można znaleźć w [podręczniku Rozwiązywanie problemów z maszynami](troubleshoot-vm-extensions.md)wirtualnymi.