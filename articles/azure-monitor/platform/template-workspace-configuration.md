---
title: Szablon usługi Azure Resource Manager dla obszaru roboczego usługi Log Analytics
description: Za pomocą szablonów usługi Azure Resource Manager można tworzyć i konfigurować obszary robocze usługi Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2020
ms.openlocfilehash: 81e46f53c0afc69c927918daa0488c4835d60805
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605016"
---
# <a name="manage-log-analytics-workspace-using-azure-resource-manager-templates"></a>Zarządzanie obszarem roboczym usługi Log Analytics przy użyciu szablonów usługi Azure Resource Manager

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Za pomocą [szablonów usługi Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) można tworzyć i konfigurować obszary robocze usługi Log Analytics w usłudze Azure Monitor. Przykłady zadań, które można wykonywać za pomocą szablonów, obejmują:

* Tworzenie obszaru roboczego, w tym ustawianie warstwy cenowej i rezerwacji zdolności produkcyjnych
* Dodawanie rozwiązania
* Tworzenie zapisanych wyszukiwań
* Tworzenie grupy komputerów
* Włączanie zbierania dzienników usług IIS z komputerów z zainstalowanym agentem systemu Windows
* Zbieranie liczników wydajności z komputerów z systemem Linux i Windows
* Zbieranie zdarzeń z syslog na komputerach z systemem Linux 
* Zbieranie zdarzeń z dzienników zdarzeń systemu Windows
* Zbieranie dzienników niestandardowych z komputera z systemem Windows
* Dodawanie agenta analizy dzienników do maszyny wirtualnej platformy Azure
* Konfigurowanie analizy dzienników do indeksowania danych zebranych przy użyciu diagnostyki platformy Azure

Ten artykuł zawiera przykłady szablonów, które ilustrują niektóre konfiguracje, które można wykonać za pomocą szablonów.

## <a name="api-versions"></a>Wersje interfejsu API

W poniższej tabeli wymieniono wersję interfejsu API dla zasobów używanych w tym przykładzie.

| Zasób | Typ zasobu | Wersja API |
|:---|:---|:---|
| Workspace   | obszary robocze    | 2017-03-15-podgląd |
| Wyszukiwanie      | savedSearches (wyszukiwanie) | 2015-03-20 |
| Źródło danych | Datasources   | 2015-11-01-podgląd |
| Rozwiązanie    | rozwiązania     | 2015-11-01-podgląd |

## <a name="create-a-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi Log Analytics

Poniższy przykład tworzy obszar roboczy przy użyciu szablonu z komputera lokalnego. Szablon JSON jest skonfigurowany tak, aby wymagał tylko nazwy i lokalizacji nowego obszaru roboczego. Używa wartości określonych dla innych parametrów obszaru roboczego, takich jak [tryb kontroli dostępu](design-logs-deployment.md#access-control-mode), warstwa cenowa, retencja i poziom rezerwacji pojemności.

> [!WARNING]
> Poniższy szablon tworzy obszar roboczy usługi Log Analytics i konfiguruje zbieranie danych. Może to zmienić ustawienia rozliczeń. [Przejrzyj zarządzanie użyciem i kosztami za pomocą dzienników usługi Azure Monitor,](manage-cost-storage.md) aby zrozumieć rozliczenia za dane zebrane w obszarze roboczym usługi Log Analytics przed zastosowaniem ich w środowisku platformy Azure.

W przypadku rezerwacji pojemności definiuje się wybraną rezerwację pojemności `CapacityReservation` dla pozyskiwania danych, określając `capacityReservationLevel`jednostkę SKU i wartość w GB właściwości . Poniższa lista zawiera szczegółowe informacje o obsługiwanych wartościach i zachowaniu podczas konfigurowania.

- Po ustawieniu limitu rezerwacji nie można zmienić na inną jednostkę SKU w ciągu 31 dni.

- Po ustawieniu wartości rezerwacji można ją zwiększyć tylko w ciągu 31 dni.

- Można ustawić wartość tylko `capacityReservationLevel` w wielokrotności 100, z maksymalną wartością 50000.

- Jeśli zwiększysz poziom rezerwacji, czasomierz zostanie zresetowany i nie można go zmienić przez kolejne 31 dni od tej aktualizacji.  

- Jeśli zmodyfikujesz inną właściwość dla obszaru roboczego, ale zachowasz limit rezerwacji na tym samym poziomie, czasomierz nie zostanie zresetowany. 

### <a name="create-and-deploy-template"></a>Szablon tworzenia i wdrażania

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
      "sku": {
        "type": "string",
        "allowedValues": [
          "pergb2018",
          "Free",
          "Standalone",
          "PerNode",
          "Standard",
          "Premium"
          ],
        "defaultValue": "pergb2018",
        "metadata": {
        "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
        }
      },
      "location": {
        "type": "String",
        "allowedValues": [
        "australiacentral", 
        "australiaeast", 
        "australiasoutheast", 
        "brazilsouth",
        "canadacentral", 
        "centralindia", 
        "centralus", 
        "eastasia", 
        "eastus", 
        "eastus2", 
        "francecentral", 
        "japaneast", 
        "koreacentral", 
        "northcentralus", 
        "northeurope", 
        "southafricanorth", 
        "southcentralus", 
        "southeastasia", 
        "uksouth", 
        "ukwest", 
        "westcentralus", 
        "westeurope", 
        "westus", 
        "westus2" 
        ],
      "metadata": {
        "description": "Specifies the location in which to create the workspace."
        }
      }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]"
                },
                "retentionInDays": 120,
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            }
          }
       ]
    }
    ```

   >[!NOTE]
   >W przypadku ustawień rezerwacji pojemności należy użyć tych właściwości w obszarze "sku":
   >* "name": "CapacityReservation",
   >* "capacityReservationLevel": 100

2. Edytuj szablon, aby spełnić wymagania. Należy rozważyć utworzenie [pliku parametrów Menedżera zasobów](../../azure-resource-manager/templates/parameter-files.md) zamiast przekazywania parametrów jako wartości wbudowanych. Przejrzyj odwołanie do [szablonu Microsoft.OperationalInsights/workspaces,](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) aby dowiedzieć się, jakie właściwości i wartości są obsługiwane. 

3. Zapisz ten plik jako **deploylaworkspacetemplate.json** w folderze lokalnym.

4. Wszystko jest teraz gotowe do wdrożenia tego szablonu. Do utworzenia obszaru roboczego użyj programu PowerShell lub wiersza polecenia, określając nazwę obszaru roboczego i lokalizację jako część polecenia. Nazwa obszaru roboczego musi być unikatowa globalnie we wszystkich subskrypcjach platformy Azure.

   * W przypadku programu PowerShell użyj następujących poleceń z folderu zawierającego szablon:
   
        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json -workspaceName <workspace-name> -location <location>
        ```

   * W wierszu polecenia użyj następujących poleceń z folderu zawierającego szablon:

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json --workspaceName <workspace-name> --location <location>
        ```

Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do następującego, który zawiera wynik:<br><br> ![Przykładowy wynik po zakończeniu wdrażania](./media/template-workspace-configuration/template-output-01.png)

## <a name="configure-a-log-analytics-workspace"></a>Konfigurowanie obszaru roboczego usługi Log Analytics

Poniższy przykład szablonu ilustruje, jak:

1. Dodawanie rozwiązań do obszaru roboczego
2. Utwórz zapisane wyszukiwania. Aby upewnić się, że wdrożenia nie zastępują zapisanych wyszukiwań przypadkowo, właściwość eTag powinna zostać dodana w zasobie "savedSearches", aby zastąpić i utrzymać idempotency zapisanych wyszukiwań.
3. Tworzenie grupy komputerów
4. Włączanie zbierania dzienników usług IIS z komputerów z zainstalowanym agentem systemu Windows
5. Zbieranie liczników perf dysku logicznego z komputerów z systemem Linux (% używanych iod; Darmowe megabajty; % używanej przestrzeni; Transfery dysków/s; Odczyty dysku/s; Zapisy na dysku/s)
6. Zbieranie zdarzeń syslog z komputerów z systemem Linux
7. Zbieranie zdarzeń błędów i ostrzeżeń z dziennika zdarzeń aplikacji z komputerów z systemem Windows
8. Licznik wydajności Zbieranie pamięci dostępnych bajtów z komputerów z systemem Windows
9. Zbieranie dzienników usług IIS i dzienników zdarzeń systemu Windows napisanych przez diagnostykę platformy Azure na koncie magazynu
10. Zbieranie dzienników niestandardowych z komputera z systemem Windows

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Workspace name"
      }
    },
    "sku": {
      "type": "string",
      "allowedValues": [
        "PerGB2018",
        "Free",
        "Standalone",
        "PerNode",
        "Standard",
        "Premium"
      ],
      "defaultValue": "pergb2018",
      "metadata": {
        "description": "Pricing tier: pergb2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
      }
    },
    "dataRetention": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 7,
      "maxValue": 730,
      "metadata": {
        "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
      }
    },
    "immediatePurgeDataOn30Days": {
      "type": "bool",
      "defaultValue": "[bool('false')]",
      "metadata": {
        "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "australiacentral", 
        "australiaeast", 
        "australiasoutheast", 
        "brazilsouth",
        "canadacentral", 
        "centralindia", 
        "centralus", 
        "eastasia", 
        "eastus", 
        "eastus2", 
        "francecentral", 
        "japaneast", 
        "koreacentral", 
        "northcentralus", 
        "northeurope", 
        "southafricanorth", 
        "southcentralus", 
        "southeastasia", 
        "uksouth", 
        "ukwest", 
        "westcentralus", 
        "westeurope", 
        "westus", 
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location in which to create the workspace."
      }
    },
    "applicationDiagnosticsStorageAccountName": {
        "type": "string",
        "metadata": {
          "description": "Name of the storage account with Azure diagnostics output"
        }
    },
    "applicationDiagnosticsStorageAccountResourceGroup": {
        "type": "string",
        "metadata": {
          "description": "The resource group name containing the storage account with Azure diagnostics output"
        }
    },
    "customLogName": {
    "type": "string",
    "metadata": {
      "description": "The custom log name"
      }
     }
    },
    "variables": {
      "Updates": {
        "Name": "[Concat('Updates', '(', parameters('workspaceName'), ')')]",
        "GalleryName": "Updates"
      },
      "AntiMalware": {
        "Name": "[concat('AntiMalware', '(', parameters('workspaceName'), ')')]",
        "GalleryName": "AntiMalware"
      },
      "SQLAssessment": {
        "Name": "[Concat('SQLAssessment', '(', parameters('workspaceName'), ')')]",
        "GalleryName": "SQLAssessment"
      },
      "diagnosticsStorageAccount": "[resourceId(parameters('applicationDiagnosticsStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName'))]"
  },
  "resources": [
    {
      "apiVersion": "2017-03-15-preview",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "retentionInDays": "[parameters('dataRetention')]",
        "features": {
          "immediatePurgeDataOn30Days": "[parameters('immediatePurgeDataOn30Days')]"
        },
        "sku": {
          "name": "[parameters('sku')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-03-20",
          "name": "VMSS Queries2",
          "type": "savedSearches",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "category": "VMSS",
            "eTag": "*",
            "displayName": "VMSS Instance Count",
            "query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "version": 1
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsEvent1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "Application",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsPerfCounter1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsPerformanceCounter",
          "properties": {
            "objectName": "Memory",
            "instanceName": "*",
            "intervalSeconds": 10,
            "counterName": "Available MBytes"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleIISLog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "IISLogs",
          "properties": {
            "state": "OnPremiseEnabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslog",
          "properties": {
            "syslogName": "kern",
            "syslogSeverities": [
              {
                "severity": "emerg"
              },
              {
                "severity": "alert"
              },
              {
                "severity": "crit"
              },
              {
                "severity": "err"
              },
              {
                "severity": "warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslogCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslogCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerf1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceObject",
          "properties": {
            "performanceCounters": [
              {
                "counterName": "% Used Inodes"
              },
              {
                "counterName": "Free Megabytes"
              },
              {
                "counterName": "% Used Space"
              },
              {
                "counterName": "Disk Transfers/sec"
              },
              {
                "counterName": "Disk Reads/sec"
              },
              {
                "counterName": "Disk Writes/sec"
              }
            ],
            "objectName": "Logical Disk",
            "instanceName": "*",
            "intervalSeconds": 10
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "dataSources",
          "name": "[concat(parameters('workspaceName'), parameters('customLogName'))]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', '/', parameters('workspaceName'))]"
          ],
          "kind": "CustomLog",
          "properties": {
            "customLogName": "[parameters('customLogName')]",
            "description": "this is a description",
            "extractions": [
              {
                "extractionName": "TimeGenerated",
                "extractionProperties": {
                  "dateTimeExtraction": {
                    "regex": [
                      {
                        "matchIndex": 0,
                        "numberdGroup": null,
                        "pattern": "((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]"
                      }
                    ]
                  }
                },
                "extractionType": "DateTime"
              }
            ],
            "inputs": [
              {
                "location": {
                  "fileSystemLocations": {
                    "linuxFileTypeLogPaths": null,
                    "windowsFileTypeLogPaths": [
                      "[concat('c:\\Windows\\Logs\\',parameters('customLogName'))]"
                    ]
                  }
                },
                "recordDelimiter": {
                  "regexDelimiter": {
                    "matchIndex": 0,
                    "numberdGroup": null,
                    "pattern": "(^.*((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9].*$)"
                  }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerfCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-03-20",
          "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('workspaceName'))]",
          "type": "storageinsightconfigs",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "containers": [ 
              "wad-iis-logfiles" 
            ],
            "tables": [
              "WADWindowsEventLogsTable"
            ],
            "storageAccount": {
              "id": "[variables('diagnosticsStorageAccount')]",
              "key": "[listKeys(variables('diagnosticsStorageAccount'),'2015-06-15').key1]"
            }
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('Updates').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('Updates').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('Updates').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('AntiMalware').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('AntiMalware').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('AntiMalware').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('AntiMalware').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('SQLAssessment').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('SQLAssessment').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('SQLAssessment').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('SQLAssessment').GalleryName)]",
            "promotionCode": ""
          }
        }
      ]
    }
  ],
  "outputs": {
    "workspaceName": {
      "type": "string",
      "value": "[parameters('workspaceName')]"
    },
    "provisioningState": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').provisioningState]"
    },
    "source": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').source]"
    },
    "customerId": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').customerId]"
    },
    "sku": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').sku.name]"
    },
    "retentionInDays": {
      "type": "int",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').retentionInDays]"
    },
    "immediatePurgeDataOn30Days": {  
      "type": "bool",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').features.immediatePurgeDataOn30Days]"
    },
    "portalUrl": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').portalUrl]"
    }
  }
}
```

### <a name="deploying-the-sample-template"></a>Wdrażanie przykładowego szablonu

Aby wdrożyć przykładowy szablon:

1. Zapisywanie dołączonej próbki w pliku, na przykład`azuredeploy.json` 
2. Edytuj szablon, aby mieć żądaną konfigurację
3. Wdrażanie szablonu za pomocą programu PowerShell lub wiersza polecenia

#### <a name="powershell"></a>PowerShell

```powershell
New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile azuredeploy.json
```

#### <a name="command-line"></a>Wiersz polecenia

```cmd
azure config mode arm
azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile azuredeploy.json
```

## <a name="example-resource-manager-templates"></a>Przykładowe szablony Menedżera zasobów

Galeria szablonów przewodnika Szybki start platformy Azure zawiera kilka szablonów dla usługi Log Analytics, w tym:

* [Wdrażanie maszyny wirtualnej z systemem Windows z rozszerzeniem maszyny Wirtualnej usługi Log Analytics](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Wdrażanie maszyny wirtualnej z systemem Linux z rozszerzeniem maszyny Wirtualnej usługi Log Analytics](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [Monitorowanie usługi Azure Site Recovery przy użyciu istniejącego obszaru roboczego usługi Log Analytics](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [Monitorowanie aplikacji Azure Web Apps przy użyciu istniejącego obszaru roboczego usługi Log Analytics](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [Dodawanie istniejącego konta magazynu do usługi Log Analytics](https://azure.microsoft.com/resources/templates/oms-existing-storage-account/)

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie agenta systemu Windows na maszynach wirtualnych platformy Azure przy użyciu szablonu Menedżera zasobów](../../virtual-machines/extensions/oms-windows.md).

* [Wdrażanie agenta systemu Linux na maszynach wirtualnych platformy Azure przy użyciu szablonu Menedżera zasobów](../../virtual-machines/extensions/oms-linux.md).
