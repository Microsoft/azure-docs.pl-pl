---
title: Zbieranie metryk zestawu skalowania systemu Windows w Azure Monitor z szablonem
description: Wysyłanie metryk systemu operacyjnego gościa do Azure Monitor magazynu metryk przy użyciu szablonu Menedżer zasobów dla zestawu skalowania maszyn wirtualnych z systemem Windows
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: db410133bab530db731eee58048d1f9d26a1a3fb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613342"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Wysyłanie metryk systemu operacyjnego gościa do Azure Monitor magazynu metryk przy użyciu szablonu Azure Resource Manager dla zestawu skalowania maszyn wirtualnych z systemem Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Korzystając z rozszerzenia Azure Monitor [Windows Diagnostyka Azure (funkcji wad)](../agents/diagnostics-extension-overview.md), można zbierać metryki i dzienniki z systemu operacyjnego gościa (systemu operacyjnego gościa) uruchamianego w ramach maszyny wirtualnej, usługi w chmurze lub klastra Service Fabric platformy Azure. Rozszerzenie może wysyłać dane telemetryczne do wielu różnych lokalizacji wymienionych w wcześniej połączonym artykule.  

W tym artykule opisano proces wysyłania metryk wydajności systemu operacyjnego gościa dla zestawu skalowania maszyn wirtualnych z systemem Windows do magazynu danych Azure Monitor. Począwszy od systemu Windows Diagnostyka Azure w wersji 1,11, można pisać metryki bezpośrednio do magazynu metryk Azure Monitor, gdzie standardowe metryki platformy są już zbierane. Przechowywanie ich w tej lokalizacji pozwala uzyskać dostęp do tych samych akcji, które są dostępne dla metryk platformy. Akcje obejmują generowanie alertów niemal w czasie rzeczywistym, tworzenie wykresów, routing, dostęp z interfejsu API REST i nie tylko. W przeszłości rozszerzenie systemu Windows Diagnostyka Azure zostało napisane do usługi Azure Storage, ale nie do Azure Monitor magazynu danych.  

Jeśli jesteś nowym szablonem Menedżer zasobów, Dowiedz się więcej na temat [wdrożeń szablonów](../../azure-resource-manager/management/overview.md) oraz ich struktury i składni.  

## <a name="prerequisites"></a>Wymagania wstępne

- Twoja subskrypcja musi być zarejestrowana w usłudze [Microsoft. Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- Musisz mieć zainstalowaną [Azure PowerShell](/powershell/azure) lub można użyć [Azure Cloud Shell](../../cloud-shell/overview.md). 

- Zasób maszyny wirtualnej musi znajdować się w [regionie, który obsługuje metryki niestandardowe](../platform/metrics-custom-overview.md#supported-regions).

## <a name="set-up-azure-monitor-as-a-data-sink"></a>Konfigurowanie Azure Monitor jako ujścia danych 
Rozszerzenie Diagnostyka Azure używa funkcji o nazwie **ujścia danych** do kierowania metryk i dzienników do różnych lokalizacji. Poniższe kroki pokazują, jak za pomocą szablonu Menedżer zasobów i programu PowerShell wdrożyć maszynę wirtualną przy użyciu nowego ujścia danych Azure Monitor. 

## <a name="author-a-resource-manager-template"></a>Tworzenie szablonu Menedżer zasobów 
Na potrzeby tego przykładu można użyć dostępnego publicznie [przykładowego szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale):  

- **Azuredeploy.json** jest wstępnie skonfigurowanym szablonem Menedżer zasobów do wdrożenia zestawu skalowania maszyn wirtualnych.

- **Azuredeploy.parameters.json** to plik parametrów, który przechowuje informacje takie jak nazwa użytkownika i hasło, które chcesz ustawić dla maszyny wirtualnej. Podczas wdrażania szablon Menedżer zasobów używa parametrów ustawionych w tym pliku. 

Pobierz i Zapisz oba pliki lokalnie. 

###  <a name="modify-azuredeployparametersjson"></a>Modyfikuj azuredeploy.parameters.jsna
Otwórz **azuredeploy.parameters.jsw** pliku:  
 
- Podaj **vmSKU** do wdrożenia. Zalecamy Standard_D2_v3. 
- Określ **windowsOSVersion** dla zestawu skalowania maszyn wirtualnych. Zalecamy 2016 — centrum danych. 
- Nazwij zasób zestawu skalowania maszyn wirtualnych, który ma zostać wdrożony za pomocą właściwości **vmssName** . Przykładem jest **VMSS-funkcji wad-test**.    
- Określ liczbę maszyn wirtualnych, które mają być uruchamiane w zestawie skalowania maszyn wirtualnych za pomocą właściwości **instanceCount** .
- Wprowadź wartości dla **adminUsername** i **adminPassword** dla zestawu skalowania maszyn wirtualnych. Te parametry są używane na potrzeby dostępu zdalnego do maszyn wirtualnych w zestawie skalowania. Aby uniknąć przejęcia maszyny **wirtualnej, nie należy używać** tych elementów w tym szablonie. Botów Skanuj Internet pod kątem nazw użytkowników i haseł w publicznych repozytoriach usługi GitHub. Te wartości domyślne mogą testować maszyny wirtualne. 


###  <a name="modify-azuredeployjson"></a>Modyfikuj azuredeploy.jsna
Otwórz **azuredeploy.js** pliku. 

Dodaj zmienną do przechowywania informacji o koncie magazynu w szablonie Menedżer zasobów. Wszystkie dzienniki i liczniki wydajności określone w pliku konfiguracyjnym diagnostyki są zapisywane w magazynie metryk Azure Monitor i na koncie magazynu określonym w tym miejscu: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Znajdź definicję zestawu skalowania maszyn wirtualnych w sekcji zasoby i Dodaj sekcję **tożsamość** do konfiguracji. To dodanie zapewnia, że platforma Azure przypisze mu tożsamość systemową. Ten krok gwarantuje również, że maszyny wirtualne w zestawie skalowania mogą emitować metryki gościa dotyczące Azure Monitor:  

```json
    { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
```

W ramach zasobu zestawu skalowania maszyn wirtualnych Znajdź sekcję **virtualMachineProfile** . Dodaj nowy profil o nazwie **extensionsProfile** , aby zarządzać rozszerzeniami.  


W **extensionProfile** Dodaj nowe rozszerzenie do szablonu, jak pokazano w sekcji **VMSS-funkcji wad-Extension** .  Ta sekcja to zarządzane tożsamości dla rozszerzenia zasobów platformy Azure, które zapewniają, że metryki są akceptowane przez Azure Monitor. Pole **nazwy** może zawierać dowolną nazwę. 

Poniższy kod z rozszerzenia MSI dodaje również rozszerzenie i konfigurację diagnostyki jako zasób rozszerzenia do zasobu zestawu skalowania maszyn wirtualnych. Możesz dodać lub usunąć liczniki wydajności w miarę potrzeby: 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Dodaj element **dependsOn** dla konta magazynu, aby upewnić się, że został on utworzony w odpowiedniej kolejności: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Utwórz konto magazynu, jeśli nie zostało ono jeszcze utworzone w szablonie: 

```json
"resources": [
// add this code    
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
    "accountType": "Standard_LRS"
    }
},
// end added code
{ 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Zapisz i zamknij oba pliki. 

## <a name="deploy-the-resource-manager-template"></a>Wdrażanie szablonu Menedżer zasobów 

> [!NOTE]  
> Musisz mieć uruchomione rozszerzenie Diagnostyka Azure w wersji 1,5 lub nowszej **i** mieć właściwość **włączoną flagą autoupgrademinorversion:** o **wartości true** w szablonie Menedżer zasobów. Platforma Azure następnie ładuje odpowiednie rozszerzenie podczas uruchamiania maszyny wirtualnej. Jeśli nie masz tych ustawień w szablonie, zmień je i ponownie Wdróż szablon. 


Aby wdrożyć szablon Menedżer zasobów, użyj Azure PowerShell:  

1. Uruchom program PowerShell. 
1. Zaloguj się do platformy Azure przy użyciu `Login-AzAccount` .
1. Zapoznaj się z listą subskrypcji, korzystając z programu `Get-AzSubscription` .
1. Ustaw subskrypcję, którą utworzysz, lub zaktualizuj maszynę wirtualną: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Utwórz nową grupę zasobów dla wdrażanej maszyny wirtualnej. Uruchom następujące polecenie: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Pamiętaj, aby użyć regionu platformy Azure, który jest włączony dla metryk niestandardowych. Pamiętaj, aby użyć [regionu platformy Azure, który jest włączony dla metryk niestandardowych](../platform/metrics-custom-overview.md#supported-regions).
 
1. Uruchom następujące polecenia, aby wdrożyć maszynę wirtualną:  

   > [!NOTE]  
   > Jeśli chcesz zaktualizować istniejący zestaw skalowania, Dodaj **tryb przyrostowy** do końca polecenia. 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Po pomyślnym wdrożeniu należy znaleźć zestaw skalowania maszyn wirtualnych w Azure Portal. Powinna emitować metryki do Azure Monitor. 

   > [!NOTE]  
   > Można napotkać błędy dotyczące wybranych **vmSkuSize**. W takim przypadku Wróć do **azuredeploy.jsw** pliku i zaktualizuj wartość domyślną parametru **vmSkuSize** . Zalecamy wypróbowanie **Standard_DS1_v2**. 


## <a name="chart-your-metrics"></a>Tworzenie wykresów metryk 

1. Zaloguj się do witryny Azure Portal. 

1. W menu po lewej stronie wybierz pozycję **monitorowanie**. 

1. Na stronie **monitorowanie** wybierz pozycję **metryki**. 

   ![Monitor — Strona metryk](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Zmień okres agregacji na **ostatni 30 minut**.  

1. Z menu rozwijanego zasób wybierz utworzony zestaw skalowania maszyn wirtualnych.  

1. W menu rozwijanym obszary nazw wybierz pozycję **Azure. VM. Windows. gość**. 

1. Z menu rozwijanego metryk wybierz pozycję **pamięć \% zadeklarowane bajty w użyciu**.  

Następnie można również użyć wymiarów tej metryki do wykresu dla określonej maszyny wirtualnej lub wykreślić każdą maszynę wirtualną w zestawie skalowania. 



## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [metrykach niestandardowych](../platform/metrics-custom-overview.md).
