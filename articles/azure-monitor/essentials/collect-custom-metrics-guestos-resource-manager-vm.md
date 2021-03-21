---
title: Zbieranie metryk maszyn wirtualnych z systemem Windows w Azure Monitor z szablonem
description: Wysyłanie metryk systemu operacyjnego gościa do Azure Monitor magazynu bazy danych metryk przy użyciu szablonu Menedżer zasobów dla maszyny wirtualnej z systemem Windows
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: bwren
ms.openlocfilehash: 8e510cf2e6fed9f9ffdec1dcc4dacf16a866d66b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049019"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine"></a>Wysyłanie metryk systemu operacyjnego gościa do Azure Monitor magazynu metryk przy użyciu szablonu Azure Resource Manager dla maszyny wirtualnej z systemem Windows
Dane dotyczące wydajności z systemu operacyjnego gościa maszyn wirtualnych platformy Azure nie są zbierane automatycznie, podobnie jak inne [metryki platformy](./monitor-azure-resource.md#monitoring-data). Zainstaluj [rozszerzenie diagnostyka](../agents/diagnostics-extension-overview.md) Azure monitor, aby zbierać METRYKI systemu operacyjnego gościa do bazy danych metryk, aby można było ich używać ze wszystkimi funkcjami metryk Azure monitor, w tym alertami niemal w czasie rzeczywistym, wykresami, routingiem i dostępem z interfejsu API REST. W tym artykule opisano proces wysyłania metryk wydajności systemu operacyjnego gościa dla maszyny wirtualnej z systemem Windows do bazy danych metryk przy użyciu szablonu Menedżer zasobów. 

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat konfigurowania rozszerzenia diagnostyki do zbierania metryk systemu operacyjnego gościa przy użyciu Azure Portal, zobacz [Instalowanie i Konfigurowanie rozszerzenia diagnostyki systemu Windows Azure (funkcji wad)](../agents/diagnostics-extension-windows-install.md).


Jeśli jesteś nowym szablonem Menedżer zasobów, Dowiedz się więcej na temat [wdrożeń szablonów](../../azure-resource-manager/management/overview.md) oraz ich struktury i składni.

## <a name="prerequisites"></a>Wymagania wstępne

- Twoja subskrypcja musi być zarejestrowana w usłudze [Microsoft. Insights](../../azure-resource-manager/management/resource-providers-and-types.md).

- Musisz mieć zainstalowaną [Azure PowerShell](/powershell/azure) lub [Azure Cloud Shell](../../cloud-shell/overview.md) .

- Zasób maszyny wirtualnej musi znajdować się w [regionie, który obsługuje metryki niestandardowe](./metrics-custom-overview.md#supported-regions). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Konfigurowanie Azure Monitor jako ujścia danych
Rozszerzenie Diagnostyka Azure używa funkcji o nazwie "ujścia danych" do kierowania metryk i dzienników do różnych lokalizacji. Poniższe kroki pokazują, jak za pomocą szablonu Menedżer zasobów i programu PowerShell wdrożyć maszynę wirtualną przy użyciu nowego ujścia danych "Azure Monitor".

## <a name="author-resource-manager-template"></a>Tworzenie szablonu Menedżer zasobów
Na potrzeby tego przykładu można użyć publicznie dostępnego przykładowego szablonu. Szablony uruchamiania znajdują się pod adresem https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows .

- **Azuredeploy.json** jest wstępnie skonfigurowanym szablonem Menedżer zasobów do wdrożenia maszyny wirtualnej.

- **Azuredeploy.parameters.json** to plik parametrów, który przechowuje informacje takie jak nazwa użytkownika i hasło, które chcesz ustawić dla maszyny wirtualnej. Podczas wdrażania szablon Menedżer zasobów używa parametrów ustawionych w tym pliku.

Pobierz i Zapisz oba pliki lokalnie.

### <a name="modify-azuredeployparametersjson"></a>Modyfikuj azuredeploy.parameters.jsna
Otwórz *azuredeploy.parameters.jsw* pliku

1. Wprowadź wartości dla **adminUsername** i **adminPassword** dla maszyny wirtualnej. Te parametry są używane na potrzeby dostępu zdalnego do maszyny wirtualnej. Aby uniknąć przejęcia maszyny wirtualnej, nie używaj wartości z tego szablonu. Botów Skanuj Internet pod kątem nazw użytkowników i haseł w publicznych repozytoriach usługi GitHub. Te wartości domyślne mogą testować maszyny wirtualne.

1. Utwórz unikatowy dnsname dla maszyny wirtualnej.

### <a name="modify-azuredeployjson"></a>Modyfikuj azuredeploy.jsna

Otwórz *azuredeploy.jsw* pliku

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

Dodaj następującą konfigurację, aby włączyć rozszerzenie diagnostyki na maszynie wirtualnej z systemem Windows. W przypadku prostej maszyny wirtualnej opartej na Menedżer zasobów można dodać konfigurację rozszerzenia do tablicy zasobów maszyny wirtualnej. Wiersz "ujścia" &mdash; "AzMonSink" i odpowiadający mu "SinksConfig" w dalszej części sekcji umożliwiają rozłączenie &mdash; metryk bezpośrednio do Azure monitor. W razie potrzeby możesz dodawać lub usuwać liczniki wydajności.


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
1. Zaloguj się do platformy Azure przy użyciu `Login-AzAccount` .
1. Zapoznaj się z listą subskrypcji, korzystając z programu `Get-AzSubscription` .
1. Ustaw subskrypcję używaną do tworzenia/aktualizowania maszyny wirtualnej w programie:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Aby utworzyć nową grupę zasobów dla wdrażanej maszyny wirtualnej, uruchom następujące polecenie:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Pamiętaj [, aby korzystać z regionu świadczenia usługi Azure, w którym włączono obsługę metryk niestandardowych](./metrics-custom-overview.md).

1. Uruchom następujące polecenia, aby wdrożyć maszynę wirtualną przy użyciu szablonu Menedżer zasobów.
   > [!NOTE]
   > Jeśli chcesz zaktualizować istniejącą maszynę wirtualną, po prostu Dodaj *tryb przyrostowy* do końca poniższego polecenia.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Po pomyślnym wdrożeniu maszyna wirtualna powinna znajdować się w Azure Portal, emitując metryki Azure Monitor.

   > [!NOTE]
   > Można napotkać błędy dotyczące wybranych vmSkuSize. W takim przypadku Wróć do azuredeploy.jsw pliku i zaktualizuj wartość domyślną parametru vmSkuSize. W tym przypadku zalecamy podjęcie próby "Standard_DS1_v2").

## <a name="chart-your-metrics"></a>Tworzenie wykresów metryk

1. Zaloguj się w witrynie Azure Portal.

2. W menu po lewej stronie wybierz pozycję **Monitoruj**.

3. Na stronie monitorowanie wybierz pozycję **metryki**.

   ![Strona metryk](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Zmień okres agregacji na **ostatni 30 minut**.

5. Z menu rozwijanego zasób Wybierz utworzoną maszynę wirtualną. Jeśli nazwa nie została zmieniona w szablonie, powinna być *SimpleWinVM2*.

6. W menu rozwijanym obszary nazw wybierz pozycję **Azure. VM. Windows. Guest**

7. W menu rozwijanym metryk wybierz pozycję **pamięć \% zadeklarowane bajty w użyciu**.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [metrykach niestandardowych](./metrics-custom-overview.md).