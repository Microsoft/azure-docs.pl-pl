---
title: Maszyny wirtualne w szablonie Azure Resource Manager | Microsoft Azure
description: Dowiedz się więcej o tym, jak zasób maszyny wirtualnej jest zdefiniowany w szablonie Azure Resource Manager.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: 2726ba3b337dc785de58b06fb5e8372058f71879
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202100"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Maszyny wirtualne w szablonie usługi Azure Resource Manager

W tym artykule opisano aspekty szablonu Azure Resource Manager, który ma zastosowanie do maszyn wirtualnych. Ten artykuł nie zawiera opisu pełnego szablonu do tworzenia maszyny wirtualnej; w przypadku, gdy potrzebne są definicje zasobów dla kont magazynu, interfejsów sieciowych, publicznych adresów IP i sieci wirtualnych. Aby uzyskać więcej informacji na temat sposobu definiowania tych zasobów, zobacz Przewodnik po [szablonach Menedżer zasobów](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

[W galerii](https://azure.microsoft.com/documentation/templates/?term=VM) znajduje się wiele szablonów obejmujących zasób maszyny wirtualnej. Nie wszystkie elementy, które mogą zostać uwzględnione w szablonie, zostały opisane tutaj.

 

W tym przykładzie przedstawiono typową sekcję zasobów szablonu służącą do tworzenia określonej liczby maszyn wirtualnych:

```json
"resources": [
  { 
    "apiVersion": "2016-04-30-preview", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[concat('myVM', copyindex())]", 
    "location": "[resourceGroup().location]",
    "copy": {
      "name": "virtualMachineLoop", 
      "count": "[parameters('numberOfInstances')]"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/myNIC', copyindex())]" 
    ], 
    "properties": { 
      "hardwareProfile": { 
        "vmSize": "Standard_DS1" 
      }, 
      "osProfile": { 
        "computername": "[concat('myVM', copyindex())]", 
        "adminUsername": "[parameters('adminUsername')]", 
        "adminPassword": "[parameters('adminPassword')]" 
      }, 
      "storageProfile": { 
        "imageReference": { 
          "publisher": "MicrosoftWindowsServer", 
          "offer": "WindowsServer", 
          "sku": "2012-R2-Datacenter", 
          "version": "latest" 
        }, 
        "osDisk": { 
          "name": "[concat('myOSDisk', copyindex())]",
          "caching": "ReadWrite", 
          "createOption": "FromImage" 
        },
        "dataDisks": [
          {
            "name": "[concat('myDataDisk', copyindex())]",
            "diskSizeGB": "100",
            "lun": 0,
            "createOption": "Empty"
          }
        ] 
      }, 
      "networkProfile": { 
        "networkInterfaces": [ 
          { 
            "id": "[resourceId('Microsoft.Network/networkInterfaces',
              concat('myNIC', copyindex()))]" 
          } 
        ] 
      },
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('https://', variables('storageName'), '.blob.core.windows.net')]"
        }
      } 
    },
    "resources": [ 
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
      {
        "name": "MyCustomScriptExtension",
        "type": "extensions",
        "apiVersion": "2016-03-30",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
        ],
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.7",
          "autoUpgradeMinorVersion": true,
          "settings": {
            "fileUris": [
              "[concat('https://', variables('storageName'),
                '.blob.core.windows.net/customscripts/start.ps1')]" 
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
          }
        }
      } 
    ]
  } 
]
``` 

> [!NOTE] 
>Ten przykład opiera się na wcześniej utworzonym koncie magazynu. Konto magazynu można utworzyć, wdrażając je na podstawie szablonu. Przykład opiera się również na interfejsie sieciowym i jego zasobach zależnych, które zostałyby zdefiniowane w szablonie. Te zasoby nie są wyświetlane w przykładzie.
>
>

## <a name="api-version"></a>Wersja interfejsu API

Podczas wdrażania zasobów przy użyciu szablonu należy określić wersję interfejsu API do użycia. Przykład przedstawia zasób maszyny wirtualnej za pomocą tego elementu apiVersion:

```json
"apiVersion": "2016-04-30-preview",
```

Wersja interfejsu API określona w szablonie ma wpływ na właściwości, które można zdefiniować w szablonie. Ogólnie rzecz biorąc, należy wybrać najnowszą wersję interfejsu API podczas tworzenia szablonów. W przypadku istniejących szablonów można zdecydować, czy chcesz kontynuować korzystanie ze starszej wersji interfejsu API, czy zaktualizować szablon pod kątem najnowszej wersji, aby korzystać z nowych funkcji.

Te możliwości umożliwiają uzyskanie najnowszych wersji interfejsu API:

- Interfejs API REST — [Lista wszystkich dostawców zasobów](/rest/api/resources/providers)
- PowerShell- [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)
- Interfejs wiersza polecenia platformy Azure — [Pokaż dostawcę](/cli/azure/provider)


## <a name="parameters-and-variables"></a>Parametry i zmienne

[Parametry](../../azure-resource-manager/templates/template-syntax.md) ułatwiają Określanie wartości dla szablonu podczas jego uruchamiania. Ta sekcja parametrów jest używana w przykładzie:

```json
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Podczas wdrażania przykładowego szablonu należy wprowadzić wartości dla nazwy i hasła konta administratora na każdej maszynie wirtualnej oraz liczbę maszyn wirtualnych do utworzenia. Możesz określić wartości parametrów w osobnym pliku, który jest zarządzany przy użyciu szablonu lub podając wartości po wyświetleniu monitu.

[Zmienne](../../azure-resource-manager/templates/template-syntax.md) ułatwiają konfigurowanie wartości w szablonie, które są używane wielokrotnie, lub mogą ulec zmianie w czasie. Ta sekcja zmiennych jest używana w przykładzie:

```json
"variables": { 
  "storageName": "mystore1",
  "accountid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name,
  '/providers/','Microsoft.Storage/storageAccounts/', variables('storageName'))]", 
  "wadlogs": "<WadCfg> 
    <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> 
      <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> 
      <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > 
        <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" />
      </WindowsEventLog>", 
  "wadperfcounters": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\">
      <PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\">
        <annotation displayName=\"Threads\" locale=\"en-us\"/>
      </PerformanceCounterConfiguration>
    </PerformanceCounters>", 
  "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters'), 
    '<Metrics resourceId=\"')]", 
  "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name , 
    '/providers/', 'Microsoft.Compute/virtualMachines/')]", 
  "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/>
    <MetricAggregation scheduledTransferPeriod=\"PT1M\"/>
    </Metrics></DiagnosticMonitorConfiguration>
    </WadCfg>"
}, 
```

Podczas wdrażania przykładowego szablonu wartości zmiennych są używane dla nazwy i identyfikatora wcześniej utworzonego konta magazynu. Zmienne są również używane do podania ustawień dla rozszerzenia diagnostyki. Użyj [najlepszych rozwiązań dotyczących tworzenia szablonów Azure Resource Manager](../../azure-resource-manager/templates/template-best-practices.md) , aby ułatwić podjęcie decyzji o sposobie struktury parametrów i zmiennych w szablonie.

## <a name="resource-loops"></a>Pętle zasobów

Jeśli potrzebujesz więcej niż jednej maszyny wirtualnej dla aplikacji, możesz użyć elementu Copy w szablonie. Ten opcjonalny element pętli poprzez utworzenie liczby maszyn wirtualnych, które zostały określone jako parametr:

```json
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Zwróć również uwagę na to, że indeks pętli jest używany podczas określania niektórych wartości dla zasobu. Na przykład, jeśli wprowadzono liczbę wystąpień z trzema, nazwy dysków systemu operacyjnego to myOSDisk1, myOSDisk2 i myOSDisk3:

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>W tym przykładzie używa się dysków zarządzanych dla maszyn wirtualnych.
>
>

Należy pamiętać, że utworzenie pętli dla jednego zasobu w szablonie może wymagać użycia pętli podczas tworzenia lub uzyskiwania dostępu do innych zasobów. Na przykład wiele maszyn wirtualnych nie może korzystać z tego samego interfejsu sieciowego, więc jeśli szablon pętli przez tworzenie trzech maszyn wirtualnych, musi on również być w pętli przez tworzenie trzech interfejsów sieciowych. Podczas przypisywania interfejsu sieciowego do maszyny wirtualnej indeks pętli jest używany do identyfikowania:

```json
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Zależności

Większość zasobów jest zależna od innych zasobów do poprawnego działania. Maszyny wirtualne muszą być skojarzone z siecią wirtualną, a tym samym wymagają interfejsu sieciowego. Element [dependsOn](../../azure-resource-manager/templates/define-resource-dependency.md) jest używany do upewnienia się, że interfejs sieciowy jest gotowy do użycia przed utworzeniem maszyn wirtualnych:

```json
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Menedżer zasobów wdrażać równolegle wszystkie zasoby, które nie są zależne od innego wdrożonego zasobu. Należy zachować ostrożność podczas ustawiania zależności, ponieważ można przypadkowo spowalniać wdrożenie przez określenie niepotrzebnych zależności. Zależności można łączyć za poorednictwem wielu zasobów. Na przykład interfejs sieciowy zależy od publicznego adresu IP i zasobów sieci wirtualnej.

Jak sprawdzić, czy zależność jest wymagana? Sprawdź wartości ustawione w szablonie. Jeśli element w definicji zasobu maszyny wirtualnej wskazuje na inny zasób wdrożony w tym samym szablonie, musisz mieć zależność. Na przykład Przykładowa maszyna wirtualna definiuje profil sieciowy:

```json
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Aby ustawić tę właściwość, musi istnieć interfejs sieciowy. W związku z tym musisz mieć zależność. Należy również ustawić zależność, gdy jeden zasób (podrzędny) jest zdefiniowany w innym zasobie (element nadrzędny). Na przykład ustawienia diagnostyczne i rozszerzenia niestandardowego skryptu są zdefiniowane jako zasoby podrzędne maszyny wirtualnej. Nie można ich utworzyć, dopóki nie istnieje maszyna wirtualna. W związku z tym oba zasoby są oznaczane jako zależne od maszyny wirtualnej.

## <a name="profiles"></a>Profile

Podczas definiowania zasobu maszyny wirtualnej są używane różne elementy profilu. Niektóre są wymagane, a niektóre są opcjonalne. Na przykład elementy — obiekt hardwareprofile, osProfile, obszarze storageprofile i networkProfile są wymagane, ale diagnosticsProfile jest opcjonalne. Te profile definiują ustawienia, takie jak:
   
- [zmienia](../sizes.md)
- [Nazwa](/azure/architecture/best-practices/resource-naming) i poświadczenia
- Ustawienia dysku i [systemu operacyjnego](cli-ps-findimage.md)
- [Interfejs sieciowy](/previous-versions/azure/virtual-network/virtual-network-deploy-multinic-classic-ps) 
- Diagnostyka rozruchu

## <a name="disks-and-images"></a>Dyski i obrazy
   
Na platformie Azure pliki VHD mogą reprezentować [dyski lub obrazy](../managed-disks-overview.md). Gdy system operacyjny w pliku VHD jest wyspecjalizowany dla konkretnej maszyny wirtualnej, jest określany jako dysk. Gdy system operacyjny w pliku VHD zostanie uogólniony do użycia w celu utworzenia wielu maszyn wirtualnych, jest on nazywany obrazem.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Tworzenie nowych maszyn wirtualnych i nowych dysków na podstawie obrazu platformy

Podczas tworzenia maszyny wirtualnej należy wybrać system operacyjny, który ma być używany. Element elementu imagereference jest używany do definiowania systemu operacyjnego nowej maszyny wirtualnej. W przykładzie przedstawiono definicję dla systemu operacyjnego Windows Server:

```json
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Jeśli chcesz utworzyć system operacyjny Linux, możesz użyć tej definicji:

```json
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Ustawienia konfiguracji dla dysku systemu operacyjnego są przypisane do elementu osDisk. W przykładzie zdefiniowano nowy dysk zarządzany z trybem buforowania ustawionym na **ReadWrite** i że dysk jest tworzony na podstawie [obrazu platformy](cli-ps-findimage.md):

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Tworzenie nowych maszyn wirtualnych z istniejących dysków zarządzanych

Jeśli chcesz utworzyć maszyny wirtualne z istniejących dysków, Usuń elementu imagereference i elementy osProfile i Zdefiniuj następujące ustawienia dysku:

```json
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Tworzenie nowych maszyn wirtualnych na podstawie zarządzanego obrazu

Jeśli chcesz utworzyć maszynę wirtualną na podstawie obrazu zarządzanego, Zmień element elementu imagereference i Zdefiniuj następujące ustawienia dysku:

```json
"storageProfile": { 
  "imageReference": {
    "id": "[resourceId('Microsoft.Compute/images', 'myImage')]"
  },
  "osDisk": { 
    "name": "[concat('myOSDisk', copyindex())]",
    "osType": "Windows",
    "caching": "ReadWrite", 
    "createOption": "FromImage" 
  }
},
```

### <a name="attach-data-disks"></a>Dołączanie dysków danych

Opcjonalnie można dodać dyski danych do maszyn wirtualnych. [Liczba dysków](../sizes.md) zależy od rozmiaru używanego dysku systemu operacyjnego. Po ustawieniu rozmiaru maszyn wirtualnych ustawionych na Standard_DS1_v2 Maksymalna liczba dysków z danymi, które można dodać do nich, to dwie. W tym przykładzie jeden zarządzany dysk danych jest dodawany do każdej maszyny wirtualnej:

```json
"dataDisks": [
  {
    "name": "[concat('myDataDisk', copyindex())]",
    "diskSizeGB": "100",
    "lun": 0, 
    "caching": "ReadWrite",
    "createOption": "Empty"
  }
],
```

## <a name="extensions"></a>Rozszerzenia

Chociaż [rozszerzenia](../extensions/features-windows.md) są osobnym zasobem, są ściśle powiązane z maszynami wirtualnymi. Rozszerzenia mogą być dodawane jako zasób podrzędny maszyny wirtualnej lub jako osobny zasób. W przykładzie przedstawiono [rozszerzenie diagnostyki](../extensions/diagnostics-template.md) dodawane do maszyn wirtualnych:

```json
{ 
  "name": "Microsoft.Insights.VMDiagnosticsSettings", 
  "type": "extensions", 
  "location": "[resourceGroup().location]", 
  "apiVersion": "2016-03-30", 
  "dependsOn": [ 
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
  ], 
  "properties": { 
    "publisher": "Microsoft.Azure.Diagnostics", 
    "type": "IaaSDiagnostics", 
    "typeHandlerVersion": "1.5", 
    "autoUpgradeMinorVersion": true, 
    "settings": { 
      "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
      variables('wadmetricsresourceid'), 
      concat('myVM', copyindex()),
      variables('wadcfgxend')))]", 
      "storageAccount": "[variables('storageName')]" 
    }, 
    "protectedSettings": { 
      "storageAccountName": "[variables('storageName')]", 
      "storageAccountKey": "[listkeys(variables('accountid'), 
        '2015-06-15').key1]", 
      "storageAccountEndPoint": "https://core.windows.net" 
    } 
  } 
},
```

Ten zasób rozszerzenia używa zmiennej storagename i zmiennych diagnostycznych w celu podania wartości. Jeśli chcesz zmienić dane zbierane przez to rozszerzenie, możesz dodać więcej liczników wydajności do zmiennej wadperfcounters. Dane diagnostyczne można również umieścić na innym koncie magazynu niż w przypadku przechowywania dysków maszyn wirtualnych.

Istnieje wiele rozszerzeń, które można zainstalować na maszynie wirtualnej, ale najprawdopodobniej jest to [rozszerzenie niestandardowego skryptu](../extensions/custom-script-windows.md). W tym przykładzie skrypt programu PowerShell o nazwie start.ps1 jest uruchamiany na każdej maszynie wirtualnej podczas pierwszego uruchomienia:

```json
{
  "name": "MyCustomScriptExtension",
  "type": "extensions",
  "apiVersion": "2016-03-30",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
  ],
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "[concat('https://', variables('storageName'),
          '.blob.core.windows.net/customscripts/start.ps1')]" 
      ],
      "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
    }
  }
}
```

Skrypt start.ps1 może wykonywać wiele zadań konfiguracyjnych. Na przykład dyski danych dodawane do maszyn wirtualnych w tym przykładzie nie są inicjowane; Możesz użyć niestandardowego skryptu do ich zainicjowania. Jeśli masz wiele zadań uruchamiania do wykonania, możesz użyć pliku start.ps1 do wywołania innych skryptów programu PowerShell w usłudze Azure Storage. W przykładzie użyto programu PowerShell, ale można użyć dowolnej metody skryptowej dostępnej w używanym systemie operacyjnym.

Stan zainstalowanych rozszerzeń można zobaczyć w ustawieniach rozszerzenia w portalu:

![Pobierz stan rozszerzenia](./media/template-description/virtual-machines-show-extensions.png)

Informacje o rozszerzeniu można również uzyskać za pomocą polecenia programu PowerShell **Get-AzVMExtension** , **rozszerzenia maszyny wirtualnej Pobierz** polecenie interfejsu wiersza polecenia platformy Azure lub interfejsu API REST **uzyskiwania informacji o rozszerzeniu** .

## <a name="deployments"></a>Wdrożenia

Podczas wdrażania szablonu platforma Azure śledzi zasoby wdrożone jako Grupa i automatycznie przypisuje nazwę tej wdrożonej grupie. Nazwa wdrożenia jest taka sama jak nazwa szablonu.

Jeśli chcesz wiedzieć o stanie zasobów we wdrożeniu, Wyświetl grupę zasobów w Azure Portal:

![Pobierz informacje o wdrożeniu](./media/template-description/virtual-machines-deployment-info.png)
    
Nie jest to problem, aby użyć tego samego szablonu do tworzenia zasobów lub aktualizowania istniejących zasobów. Korzystając z poleceń do wdrażania szablonów, możesz powiedzieć, który [tryb](../../azure-resource-manager/templates/deploy-powershell.md) ma być używany. Tryb można ustawić na wartość **kompletna** lub **przyrostowa**. Domyślnie są to aktualizacje przyrostowe. Należy zachować ostrożność w przypadku korzystania z trybu **kompletnego** , ponieważ można przypadkowo usunąć zasoby. Po ustawieniu trybu na **ukończone**, Menedżer zasobów usuwa wszystkie zasoby w grupie zasobów, które nie znajdują się w szablonie.

## <a name="next-steps"></a>Następne kroki

- Utwórz własny szablon przy użyciu [szablonów Azure Resource Manager tworzenia](../../azure-resource-manager/templates/template-syntax.md).
- Wdróż utworzony szablon przy użyciu polecenia [Utwórz maszynę wirtualną z systemem Windows przy użyciu szablonu Menedżer zasobów](ps-template.md).
- Dowiedz się, jak zarządzać maszynami wirtualnymi utworzonymi przez Ciebie, przeglądając informacje [o tworzeniu maszyn wirtualnych z systemem Windows i zarządzanie nimi za pomocą modułu Azure PowerShell](tutorial-manage-vm.md).
- Aby poznać składnię JSON i właściwości typów zasobów w szablonach, zobacz [Azure Resource Manager dokumentacja szablonu](/azure/templates/).