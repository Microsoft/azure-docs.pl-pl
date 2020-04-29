---
title: Zbieranie metryk maszyn wirtualnych z systemem Windows w Azure Monitor z szablonem
description: Wysyłanie metryk systemu operacyjnego gościa do Azure Monitor magazynu metryk przy użyciu szablonu Menedżer zasobów dla maszyny wirtualnej z systemem Windows
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: e747ca89912c36538bfb9d02986629fe57c5adcb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77657371"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Wysyłanie metryk systemu operacyjnego gościa do Azure Monitor magazynu metryk przy użyciu szablonu Menedżer zasobów dla maszyny wirtualnej z systemem Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Za pomocą [rozszerzenia diagnostyki](diagnostics-extension-overview.md)Azure monitor można zbierać metryki i dzienniki z systemu operacyjnego gościa (systemu operacyjnego gościa) działającego w ramach maszyny wirtualnej, usługi w chmurze lub klastra Service Fabric. Rozszerzenie może wysyłać dane telemetryczne do [wielu różnych lokalizacji.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

W tym artykule opisano proces wysyłania metryk wydajności systemu operacyjnego gościa dla maszyny wirtualnej z systemem Windows do magazynu danych Azure Monitor. Począwszy od wersji Diagnostics 1,11, można pisać metryki bezpośrednio do magazynu metryk Azure Monitor, w którym są już zbierane metryki platformy standardowej.

Przechowywanie ich w tej lokalizacji pozwala uzyskać dostęp do tych samych akcji dla metryk platformy. Akcje obejmują alerty w czasie niemal rzeczywistym, wykresy, Routing i dostęp z interfejsu API REST i nie tylko. W przeszłości rozszerzenie diagnostyki Zapisano do usługi Azure Storage, ale nie do Azure Monitor magazynu danych.

Jeśli jesteś nowym szablonem Menedżer zasobów, Dowiedz się więcej na temat [wdrożeń szablonów](../../azure-resource-manager/management/overview.md) oraz ich struktury i składni.

## <a name="prerequisites"></a>Wymagania wstępne

- Twoja subskrypcja musi być zarejestrowana w usłudze [Microsoft. Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

- Musisz mieć zainstalowaną [Azure PowerShell](/powershell/azure) lub [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) .

- Zasób maszyny wirtualnej musi znajdować się w [regionie, który obsługuje metryki niestandardowe](metrics-custom-overview.md#supported-regions). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Konfigurowanie Azure Monitor jako ujścia danych
Rozszerzenie Diagnostyka Azure używa funkcji o nazwie "ujścia danych" do kierowania metryk i dzienników do różnych lokalizacji. Poniższe kroki pokazują, jak za pomocą szablonu Menedżer zasobów i programu PowerShell wdrożyć maszynę wirtualną przy użyciu nowego ujścia danych "Azure Monitor".

## <a name="author-resource-manager-template"></a>Tworzenie szablonu Menedżer zasobów
Na potrzeby tego przykładu można użyć publicznie dostępnego przykładowego szablonu. Szablony uruchamiania znajdują się https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windowspod adresem.

- **Azuredeploy. JSON** jest wstępnie skonfigurowanym szablonem Menedżer zasobów na potrzeby wdrożenia maszyny wirtualnej.

- **Azuredeploy. Parameters. JSON** to plik parametrów, który przechowuje informacje takie jak nazwa użytkownika i hasło, które chcesz ustawić dla maszyny wirtualnej. Podczas wdrażania szablon Menedżer zasobów używa parametrów ustawionych w tym pliku.

Pobierz i Zapisz oba pliki lokalnie.

### <a name="modify-azuredeployparametersjson"></a>Modyfikuj azuredeploy. Parameters. JSON
Otwórz plik *azuredeploy. Parameters. JSON*

1. Wprowadź wartości dla **adminUsername** i **adminPassword** dla maszyny wirtualnej. Te parametry są używane na potrzeby dostępu zdalnego do maszyny wirtualnej. Aby uniknąć przejęcia maszyny wirtualnej, nie używaj wartości z tego szablonu. Botów Skanuj Internet pod kątem nazw użytkowników i haseł w publicznych repozytoriach usługi GitHub. Te wartości domyślne mogą testować maszyny wirtualne.

1. Utwórz unikatowy dnsname dla maszyny wirtualnej.

### <a name="modify-azuredeployjson"></a>Modyfikuj plik azuredeploy. JSON

Otwórz plik *azuredeploy. JSON*

Dodaj identyfikator konta magazynu do sekcji **zmienne** szablonu po wpisie dla **storageAccountName.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Dodaj to rozszerzenie tożsamość usługi zarządzanej (MSI) do szablonu w górnej części sekcji **resources** . Rozszerzenie gwarantuje, że Azure Monitor akceptuje metryki, które są emitowane.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'), '/', 'WADExtensionSetup')]",
        "apiVersion": "2017-12-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    },
```

Dodaj konfigurację **tożsamości** do zasobu maszyny wirtualnej, aby upewnić się, że platforma Azure przypisze tożsamość systemową do rozszerzenia MSI. Ten krok zapewnia, że maszyna wirtualna może emitować metryki gościa dotyczące Azure Monitor.

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    // add these 3 lines below
    "identity": {
    "type": "SystemAssigned"
    },
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {
    ...
```

Dodaj następującą konfigurację, aby włączyć rozszerzenie diagnostyki na maszynie wirtualnej z systemem Windows. W przypadku prostej maszyny wirtualnej opartej na Menedżer zasobów można dodać konfigurację rozszerzenia do tablicy zasobów maszyny wirtualnej. Wiersz "ujścia"&mdash; "AzMonSink" i odpowiadający mu "SinksConfig" w dalszej części sekcji&mdash;umożliwiają rozłączenie metryk bezpośrednio do Azure monitor. W razie potrzeby możesz dodawać lub usuwać liczniki wydajności.


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]"
    }
}
},
//Start of section to add
"resources": [
{
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'), '/', 'Microsoft.Insights.VMDiagnosticsSettings')]",
            "apiVersion": "2017-12-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.12",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "WadCfg": {
                "DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
        },
                    "Directories": {
                    "scheduledTransferPeriod": "PT1M",
    "IISLogs": {
                        "containerName": "wad-iis-logfiles"
                    },
                    "FailedRequestLogs": {
                        "containerName": "wad-failedrequestlogs"
                    }
                    },
                    "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "sinks": "AzMonSink",
                    "PerformanceCounterConfiguration": [
                        {
                        "counterSpecifier": "\\Memory\\Available Bytes",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\Committed Bytes",
                        "sampleRate": "PT15S"
                        }
                    ]
                    },
                    "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                        {
                        "name": "Application!*"
                        }
                    ]
                    },
                    "Logs": {
                    "scheduledTransferPeriod": "PT1M",
                    "scheduledTransferLogLevelFilter": "Error"
                    }
                },
                "SinksConfig": {
                    "Sink": [
                    {
                        "name" : "AzMonSink",
                        "AzureMonitor" : {}
                    }
                    ]
                }
                },
                "StorageAccount": "[variables('storageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[variables('storageAccountName')]",
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]",
                "storageAccountEndPoint": "https://core.windows.net/"
            }
            }
        }
        ]
//End of section to add
```


Zapisz i zamknij oba pliki.


## <a name="deploy-the-resource-manager-template"></a>Wdrażanie szablonu Menedżer zasobów

> [!NOTE]
> Musisz mieć uruchomione rozszerzenie Diagnostyka Azure w wersji 1,5 lub nowszej i mieć właściwość **włączoną flagą autoupgrademinorversion**: o wartości "true" w szablonie Menedżer zasobów. Platforma Azure następnie ładuje odpowiednie rozszerzenie podczas uruchamiania maszyny wirtualnej. Jeśli nie masz tych ustawień w szablonie, zmień je i ponownie Wdróż szablon.


Aby wdrożyć szablon Menedżer zasobów, wykorzystujemy Azure PowerShell.

1. Uruchom program PowerShell.
1. Zaloguj się do platformy Azure `Login-AzAccount`przy użyciu.
1. Zapoznaj się z listą subskrypcji `Get-AzSubscription`, korzystając z programu.
1. Ustaw subskrypcję używaną do tworzenia/aktualizowania maszyny wirtualnej w programie:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Aby utworzyć nową grupę zasobów dla wdrażanej maszyny wirtualnej, uruchom następujące polecenie:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Pamiętaj [, aby korzystać z regionu świadczenia usługi Azure, w którym włączono obsługę metryk niestandardowych](metrics-custom-overview.md).

1. Uruchom następujące polecenia, aby wdrożyć maszynę wirtualną przy użyciu szablonu Menedżer zasobów.
   > [!NOTE]
   > Jeśli chcesz zaktualizować istniejącą maszynę wirtualną, po prostu Dodaj *tryb przyrostowy* do końca poniższego polecenia.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Po pomyślnym wdrożeniu maszyna wirtualna powinna znajdować się w Azure Portal, emitując metryki Azure Monitor.

   > [!NOTE]
   > Można napotkać błędy dotyczące wybranych vmSkuSize. W takim przypadku Wróć do pliku azuredeploy. JSON i zaktualizuj wartość domyślną parametru vmSkuSize. W tym przypadku zalecamy podjęcie próby "Standard_DS1_v2").

## <a name="chart-your-metrics"></a>Tworzenie wykresów metryk

1. Zaloguj się w witrynie Azure Portal.

2. W menu po lewej stronie wybierz pozycję **Monitoruj**.

3. Na stronie monitorowanie wybierz pozycję **metryki**.

   ![Strona metryk](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Zmień okres agregacji na **ostatni 30 minut**.

5. Z menu rozwijanego zasób Wybierz utworzoną maszynę wirtualną. Jeśli nazwa nie została zmieniona w szablonie, powinna być *SimpleWinVM2*.

6. W menu rozwijanym obszary nazw wybierz pozycję **Azure. VM. Windows. Guest**

7. W menu rozwijanym metryk wybierz pozycję **pamięć\%zadeklarowane bajty w użyciu**.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [metrykach niestandardowych](metrics-custom-overview.md).

