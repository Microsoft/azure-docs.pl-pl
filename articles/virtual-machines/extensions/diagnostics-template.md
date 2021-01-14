---
title: Dodaj diagnostykę & monitorowania do maszyny wirtualnej platformy Azure
description: Użyj szablonu Azure Resource Manager, aby utworzyć nową maszynę wirtualną z systemem Windows przy użyciu rozszerzenia usługi Azure Diagnostics.
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2017
ms.author: mimckitt
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a91e21994dda126e14c100bcf1d2a69c36b13e1e
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202168"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Używanie monitorowania i diagnostyki z MASZYNami wirtualnymi z systemem Windows i szablonami Azure Resource Manager
Rozszerzenie Diagnostyka Azure zapewnia możliwości monitorowania i diagnostyki na maszynie wirtualnej platformy Azure opartej na systemie Windows. Te możliwości można włączyć na maszynie wirtualnej, dołączając rozszerzenie jako część szablonu Azure Resource Manager. Zobacz [Tworzenie szablonów Azure Resource Manager z rozszerzeniami maszyn wirtualnych](../windows/template-description.md#extensions) , aby uzyskać więcej informacji na temat dołączania dowolnego rozszerzenia w ramach szablonu maszyny wirtualnej. W tym artykule opisano sposób dodawania rozszerzenia Diagnostyka Azure do szablonu maszyny wirtualnej z systemem Windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Dodawanie rozszerzenia Diagnostyka Azure do definicji zasobu maszyny wirtualnej
Aby włączyć rozszerzenie diagnostyki na maszynie wirtualnej z systemem Windows, należy dodać rozszerzenie jako zasób maszyny wirtualnej w szablonie Menedżer zasobów.

W przypadku prostej maszyny wirtualnej opartej na Menedżer zasobów Dodaj konfigurację rozszerzenia do tablicy *zasobów* maszyny wirtualnej: 

```json
"resources": [
    {
        "name": "Microsoft.Insights.VMDiagnosticsSettings",
        "type": "extensions",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-06-15",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "tags": {
            "displayName": "AzureDiagnostics"
        },
        "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                "storageAccountEndPoint": "https://core.windows.net"
            }
        }
    }
]
```

Inną wspólną Konwencją jest dodanie konfiguracji rozszerzenia w węźle zasobów głównych szablonu zamiast definiowania go w węźle zasoby maszyny wirtualnej. W tym podejściu należy jawnie określić hierarchiczną relację między rozszerzeniem i maszyną wirtualną z wartościami *nazw* i *typów* . Na przykład: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

Rozszerzenie jest zawsze skojarzone z maszyną wirtualną, można je bezpośrednio zdefiniować w węźle zasobów maszyny wirtualnej lub zdefiniować na poziomie podstawowym, a następnie użyć hierarchicznej konwencji nazewnictwa, aby skojarzyć ją z maszyną wirtualną.

W przypadku Virtual Machine Scale Sets konfiguracja rozszerzeń jest określona we właściwości *ExtensionProfile* *VirtualMachineProfile*.

Właściwość *wydawcy* z wartością **Microsoft. Azure. Diagnostics** oraz Właściwość *Type* z wartością **IaaSDiagnostics** jednoznacznie identyfikuje rozszerzenie Diagnostyka Azure.

Wartość właściwości *Nazwa* może służyć do odwoływania się do rozszerzenia w grupie zasobów. Ustawienie go dla usługi **Microsoft. Insights. VMDiagnosticsSettings** umożliwia łatwą identyfikację przy użyciu Azure Portal, dzięki czemu wykresy monitorowania są prawidłowo wyświetlane w Azure Portal.

*TypeHandlerVersion* określa wersję rozszerzenia, którego chcesz użyć. Ustawienie wersji pomocniczej *włączoną flagą autoupgrademinorversion* na **true** gwarantuje, że otrzymasz najnowszą wersję pomocniczą rozszerzenia, które jest dostępne. Zdecydowanie zaleca się, aby zawsze ustawić *włączoną flagą autoupgrademinorversion* na **wartość true** , aby zawsze używać najnowszego rozszerzenia diagnostyki ze wszystkimi nowymi funkcjami i poprawkami błędów. 

Element *Settings* zawiera właściwości konfiguracji dla rozszerzenia, które można ustawić i odczytywać z rozszerzenia (czasami nazywanego konfiguracją publiczną). Właściwość *xmlcfg* zawiera konfigurację opartą na języku XML dla dzienników diagnostycznych, liczników wydajności itp., które są zbierane przez agenta diagnostyki. Aby uzyskać więcej informacji na temat schematu XML, zobacz [Schemat konfiguracji diagnostyki](../../azure-monitor/platform/diagnostics-extension-schema-windows.md) . Typowym celem jest przechowywanie rzeczywistej konfiguracji XML jako zmiennej w szablonie Azure Resource Manager, a następnie łączenie i kodowanie Base64 w celu ustawienia wartości dla *xmlcfg*. Zapoznaj się z sekcją dotyczącą [zmiennych konfiguracyjnych diagnostyki](#diagnostics-configuration-variables) , aby dowiedzieć się więcej o sposobie przechowywania kodu XML w zmiennych. Właściwość *storageAccount* określa nazwę konta magazynu, do którego są przesyłane dane diagnostyczne. 

Można ustawić właściwości w *protectedSettings* (czasami określane jako Konfiguracja prywatna), ale nie można ich odczytywać po ustawieniu. Charakter tylko do zapisu *protectedSettings* ułatwia przechowywanie wpisów tajnych, takich jak klucz konta magazynu, w którym zapisywane są dane diagnostyczne.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Określanie konta magazynu diagnostyki jako parametrów
W powyższym fragmencie kodu JSON rozszerzenia diagnostyki przyjęto założenie, że dwa parametry *existingdiagnosticsStorageAccountName* i *existingdiagnosticsStorageResourceGroup* określają konto magazynu diagnostyki, w którym są przechowywane dane diagnostyczne. Określenie konta magazynu diagnostyki jako parametru ułatwia zmianę konta magazynu diagnostyki w różnych środowiskach, na przykład możesz chcieć użyć innego konta magazynu diagnostyki do testowania i innego dla wdrożenia produkcyjnego.  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
    }
}
```

Najlepszym rozwiązaniem jest określenie konta magazynu diagnostyki w innej grupie zasobów niż grupa zasobów maszyny wirtualnej. Grupa zasobów może być traktowana jako jednostka wdrożenia z własnym okresem istnienia, dlatego można wdrożyć i ponownie wdrożyć maszynę wirtualną w miarę aktualizowania nowych konfiguracji, ale warto kontynuować przechowywanie danych diagnostycznych na tym samym koncie magazynu w ramach tych wdrożeń maszyn wirtualnych. Konto magazynu w innym zasobie umożliwia konto magazynu akceptujące dane z różnych wdrożeń maszyn wirtualnych, co ułatwia rozwiązywanie problemów w różnych wersjach.

> [!NOTE]
> W przypadku tworzenia szablonu maszyny wirtualnej z systemem Windows z poziomu programu Visual Studio domyślne konto magazynu może być ustawione tak, aby korzystało z tego samego konta magazynu, do którego zostanie przekazany wirtualny dysk twardy maszyny wirtualnej. Jest to uproszczenie początkowej konfiguracji maszyny wirtualnej. Aby użyć innego konta magazynu, które można przekazywać jako parametr, należy go przywrócić. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Zmienne konfiguracji diagnostyki
Poprzedni fragment kodu JSON rozszerzenia diagnostyki definiuje zmienną *AccountId* , aby uprościć Pobieranie klucza konta magazynu dla magazynu diagnostyki:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

Właściwość *xmlcfg* rozszerzenia diagnostyki jest definiowana przy użyciu wielu zmiennych połączonych ze sobą. Wartości tych zmiennych są w formacie XML, aby podczas ustawiania zmiennych JSON musiały zostać prawidłowo zmienione.

W poniższym przykładzie opisano kod XML konfiguracji diagnostyki, który zbiera standardowe liczniki wydajności na poziomie systemu wraz z niektórymi dziennikami zdarzeń systemu Windows i dzienników infrastruktury diagnostyki. Został on zmieniony i poprawnie sformatowany, aby można było bezpośrednio wkleić konfigurację do sekcji zmiennych szablonu. Zobacz [Schemat konfiguracji diagnostyki](../../azure-monitor/platform/diagnostics-extension-schema-windows.md) , aby uzyskać bardziej czytelny przykład pliku XML konfiguracji.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

Węzeł XML definicji metryk w powyższej konfiguracji jest ważnym elementem konfiguracji, ponieważ definiuje sposób agregowania i przechowywania liczników wydajności zdefiniowanych wcześniej w kodzie XML w węźle *PerformanceCounter* . 

> [!IMPORTANT]
> Te metryki umożliwiają monitorowanie wykresów i alertów w Azure Portal.  Węzeł **metryk** z identyfikatorem *ResourceID* i **MetricAggregation** musi być uwzględniony w konfiguracji diagnostyki dla maszyny wirtualnej, jeśli chcesz zobaczyć dane monitorowania maszyny wirtualnej w Azure Portal. 
> 
> 

W poniższym przykładzie przedstawiono XML dla definicji metryk: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

Atrybut *ResourceID* jednoznacznie identyfikuje maszynę wirtualną w subskrypcji. Upewnij się, że korzystasz z funkcji subskrypcja () i Grupa zasobów (), dzięki czemu szablon automatycznie zaktualizuje te wartości na podstawie subskrypcji i grupy zasobu, do których jest wdrażana.

Jeśli tworzysz wiele Virtual Machines w pętli, musisz wypełnić wartość *ResourceID* przy użyciu funkcji funkcji copyindex (), aby prawidłowo odróżnić poszczególne maszyny wirtualne. Wartość *xmlCfg* można zaktualizować, aby obsługiwać ją w następujący sposób:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

Wartość MetricAggregation *PT1M* i *PT1H* oznacza agregację na minutę i agregację odpowiednio w ciągu godziny.

## <a name="wadmetrics-tables-in-storage"></a>WADMetrics tabel w magazynie
Powyższa konfiguracja metryk generuje tabele na koncie magazynu diagnostyki przy użyciu następujących konwencji nazewnictwa:

* **WADMetrics**: prefiks standardowy dla wszystkich tabel WADMetrics
* **PT1H** lub **PT1M**: oznacza, że tabela zawiera zagregowane dane przez 1 godzinę lub 1 minutę
* **P10D**: oznacza, że tabela będzie zawierać dane przez 10 dni od momentu rozpoczęcia zbierania danych przez tabelę
* **V2S**: ciąg stałej
* **RRRRMMDD**: Data rozpoczęcia zbierania danych przez tabelę

Przykład: *WADMetricsPT1HP10DV2S20151108* zawiera dane metryk zagregowane w ciągu godziny przez 10 dni, począwszy od 11-lis-2015    

Każda tabela WADMetrics zawiera następujące kolumny:

* **PartitionKey**: klucz partycji jest zbudowany na podstawie wartości *ResourceID* w celu jednoznacznego zidentyfikowania zasobu maszyny wirtualnej. Na przykład: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: Format `<Descending time tick>:<Performance Counter Name>` . Malejące obliczenie cykl czasu to maksymalne cykle czasu minus czas rozpoczęcia okresu agregacji. Na przykład jeśli okres próbny rozpoczęty 10-lis-2015 i 00:00Hrs UTC, obliczenia byłyby następujące: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)` . W przypadku licznika wydajności liczba bajtów dostępnej pamięci zostanie wyświetlona wartość klucza wiersza: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: jest nazwą licznika wydajności. Pasuje do *counterSpecifier* zdefiniowanego w konfiguracji XML.
* **Maksimum**: maksymalna wartość licznika wydajności w okresie agregacji.
* **Minimum**: wartość minimalna licznika wydajności w okresie agregacji.
* **Łącznie**: suma wszystkich wartości licznika wydajności raportowanego w okresie agregowania.
* **Liczba**: całkowita liczba wartości zgłoszonych dla licznika wydajności.
* **Średnia**: wartość średnia (całkowita/liczba) licznika wydajności w okresie agregacji.

## <a name="next-steps"></a>Następne kroki
* Aby zapoznać się z kompletnym przykładowym szablonem maszyny wirtualnej z systemem Windows z rozszerzeniem Diagnostics, zobacz [201-VM-monitoring-Diagnostics-Extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Wdrażanie szablonu Azure Resource Manager przy użyciu [Azure PowerShell](../windows/ps-template.md) lub [wiersza polecenia platformy Azure](../linux/create-ssh-secured-vm-from-template.md)
* Dowiedz się więcej na temat [tworzenia szablonów Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md)