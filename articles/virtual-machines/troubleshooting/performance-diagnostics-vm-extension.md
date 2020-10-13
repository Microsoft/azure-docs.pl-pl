---
title: Rozszerzenie maszyny wirtualnej diagnostyki wydajności platformy Azure dla systemu Windows | Microsoft Docs
description: Wprowadza rozszerzenie maszyny wirtualnej diagnostyki wydajności Azure dla systemu Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 16af8b8c1258ef7945e88a7af42e86a7bba2003b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963265"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Rozszerzenie maszyny wirtualnej diagnostyki wydajności platformy Azure dla systemu Windows

Rozszerzenie maszyny wirtualnej diagnostyki wydajności Azure ułatwia zbieranie danych diagnostycznych wydajności z maszyn wirtualnych z systemem Windows. Rozszerzenie wykonuje analizę i zawiera raport dotyczący wyników i zaleceń dotyczących identyfikowania i rozwiązywania problemów z wydajnością na maszynie wirtualnej. To rozszerzenie powoduje zainstalowanie narzędzia do rozwiązywania problemów o nazwie [że program perfinsights](./how-to-use-perfinsights.md).

> [!NOTE]
> Jeśli chcesz uruchomić diagnostykę na maszynie wirtualnej z Azure Portal dla nieklasycznych maszyn wirtualnych, zaleca się korzystanie z nowego środowiska. Aby uzyskać więcej informacji, zobacz [Diagnostyka wydajności dla maszyn wirtualnych platformy Azure](performance-diagnostics.md) 

## <a name="prerequisites"></a>Wymagania wstępne

To rozszerzenie można zainstalować na
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 z dodatkiem R2
* Windows Server 2012
* Windows Server 2008 z dodatkiem R2
* Windows 10
* Windows 8.1
* Windows 8

## <a name="extension-schema"></a>Schemat rozszerzenia
Poniższy kod JSON przedstawia schemat rozszerzenia maszyny wirtualnej diagnostyki wydajności Azure. To rozszerzenie wymaga nazwy i klucza konta magazynu do przechowywania danych wyjściowych i raportów diagnostycznych. Te wartości są poufne. Klucz konta magazynu powinien być przechowywany w konfiguracji chronionego ustawienia. Dane ustawienia chronionego rozszerzenia maszyny wirtualnej platformy Azure są szyfrowane i są odszyfrowywane tylko na docelowej maszynie wirtualnej. Należy pamiętać, że w **storageAccountName** i **storageAccountKey** jest rozróżniana wielkość liter. Inne wymagane parametry są wymienione w poniższej sekcji.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>Wartości właściwości

|   **Nazwa**   |**Wartość/przykład**|       **Opis**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Wersja interfejsu API.
|publisher|Microsoft. Azure. Performance. Diagnostics|Przestrzeń nazw wydawcy dla rozszerzenia.
|typ|AzurePerformanceDiagnostics|Typ rozszerzenia maszyny wirtualnej.
|typeHandlerVersion|1.0|Wersja procedury obsługi rozszerzenia.
|performanceScenario|prosty|Scenariusz wydajności, dla którego mają zostać przechwycone dane. Prawidłowe wartości to: **Basic**, **vmslow**, **migracji pamięci**i **Custom**.
|traceDurationInSeconds|300|Czas trwania śledzenia, jeśli wybrano dowolną z opcji śledzenia.
|perfCounterTrace|p|Opcja włączenia śledzenia licznika wydajności. Prawidłowe wartości to **p** lub wartość pusta. Jeśli nie chcesz przechwytywać tego śledzenia, pozostaw wartość pustą.
|networkTrace|n|Opcja włączania funkcji śledzenia sieci. Prawidłowe wartości to **n** lub wartość pusta. Jeśli nie chcesz przechwytywać tego śledzenia, pozostaw wartość pustą.
|xperfTrace|x|Opcja włączania śledzenia XPerf. Prawidłowe wartości to **x** lub wartość pusta. Jeśli nie chcesz przechwytywać tego śledzenia, pozostaw wartość pustą.
|storPortTrace|s|Opcja włączenia śledzenia StorPort. Prawidłowe wartości to **s** lub Empty. Jeśli nie chcesz przechwytywać tego śledzenia, pozostaw wartość pustą.
|srNumber|123452016365929|Numer biletu pomocy technicznej, jeśli jest dostępny. Pozostaw wartość pustą, jeśli jej nie masz.
|requestTimeUtc|2017-09-28T22:08:53.736 Z|Bieżąca data i godzina w formacie UTC. Jeśli używasz portalu do zainstalowania tego rozszerzenia, nie musisz podawać tej wartości.
|resourceId|/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}|Unikatowy identyfikator maszyny wirtualnej.
|storageAccountName|mystorageaccount|Nazwa konta magazynu do przechowywania dzienników i wyników diagnostyki.
|storageAccountKey|lDuVvxuZB28NNP... hAiRF3voADxLBTcc = =|Klucz dla konta magazynu.

## <a name="install-the-extension"></a>Instalowanie rozszerzenia

Postępuj zgodnie z tymi instrukcjami, aby zainstalować rozszerzenie na maszynach wirtualnych z systemem Windows:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz maszynę wirtualną, w której chcesz zainstalować to rozszerzenie.

    ![Zrzut ekranu przedstawiający Azure Portal z wyróżnionymi maszynami wirtualnymi](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Wybierz blok **rozszerzenia** , a następnie wybierz pozycję **Dodaj**.

    ![Zrzut ekranu przedstawiający blok rozszerzeń z wyróżnioną pozycją Dodaj](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Wybierz pozycję **Diagnostyka wydajności Azure**, zapoznaj się z warunkami i postanowieniami, a następnie wybierz pozycję **Utwórz**.

    ![Zrzut ekranu przedstawiający nowy ekran zasobów z wyróżnioną usługą Azure Performance Diagnostics](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Podaj wartości parametrów instalacji, a następnie wybierz **przycisk OK** , aby zainstalować rozszerzenie. Aby uzyskać więcej informacji o obsługiwanych scenariuszach, zobacz [How to use że program perfinsights](how-to-use-perfinsights.md#supported-troubleshooting-scenarios). 

    ![Zrzut ekranu przedstawiający okno dialogowe rozszerzenia instalacji](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Po pomyślnym zakończeniu instalacji zostanie wyświetlony komunikat z informacją o tym stanie.

    ![Zrzut ekranu przedstawiający komunikat o pomyślnym aprowizacji](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > Rozszerzenie jest uruchamiane po pomyślnym zainicjowaniu obsługi administracyjnej. Ukończenie scenariusza podstawowego trwa dwa minuty lub mniej. W przypadku innych scenariuszy działa on przez czas trwania określony podczas instalacji.

## <a name="remove-the-extension"></a>Usuwanie rozszerzenia
Aby usunąć rozszerzenie z maszyny wirtualnej, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz maszynę wirtualną, z której chcesz usunąć to rozszerzenie, a następnie wybierz blok **rozszerzenia** . 
2. Wybierz pozycję (**...**) dla wpisu rozszerzenia Diagnostyka wydajności z listy, a następnie wybierz pozycję **Odinstaluj**.

    ![Zrzut ekranu przedstawiający blok rozszerzeń z wyróżnioną opcją odinstalowania](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Możesz również wybrać wpis rozszerzenia i wybrać opcję **Odinstaluj** .

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu
Rozszerzenia maszyny wirtualnej platformy Azure można wdrażać za pomocą szablonów Azure Resource Manager. Schemat JSON opisany w poprzedniej sekcji można użyć w szablonie Azure Resource Manager. Spowoduje to uruchomienie rozszerzenia maszyny wirtualnej usługi Azure Performance Diagnostics podczas wdrażania szablonu Azure Resource Manager. Oto przykładowy szablon:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

## <a name="powershell-deployment"></a>Wdrażanie przy użyciu programu PowerShell
Za pomocą `Set-AzVMExtension` polecenia można wdrożyć rozszerzenie maszyny wirtualnej diagnostyki wydajności platformy Azure dla istniejącej maszyny wirtualnej.

Program PowerShell

```
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z";"resourceId"="VMResourceId" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
```

## <a name="information-on-the-data-captured"></a>Informacje na temat przechwyconych danych
Narzędzie że program perfinsights zbiera różne dzienniki, konfigurację i dane diagnostyczne w zależności od wybranego scenariusza. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją że program perfinsights](./how-to-use-perfinsights.md).

## <a name="view-and-share-the-results"></a>Wyświetlanie i udostępnianie wyników

Dane wyjściowe z rozszerzenia można znaleźć w pliku zip, który został przekazany do konta magazynu określonego podczas instalacji i jest udostępniany przez 30 dni przy użyciu [sygnatur dostępu współdzielonego (SAS)](../../storage/common/storage-sas-overview.md). Ten plik zip zawiera dzienniki diagnostyczne i Raport z wynikami i zaleceniami. Link sygnatury dostępu współdzielonego do wyjściowego pliku zip można znaleźć w pliku tekstowym o nazwie *zipfilename*_saslink.txt w **folderze \\ \<version> C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics**. Każdy, kto ma ten link, może pobrać plik zip.

Aby pomóc inżynierowi pomocy technicznej w pracy nad biletem pomocy technicznej, firma Microsoft może używać tego linku SAS do pobierania danych diagnostycznych.

Aby wyświetlić raport, Wyodrębnij plik zip i Otwórz plik **że program perfinsights Report.html** .

Należy również pobrać plik zip bezpośrednio z portalu, wybierając rozszerzenie.

![Zrzut ekranu przedstawiający szczegółowy stan diagnostyki wydajności](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> Link sygnatury dostępu współdzielonego wyświetlany w portalu może nie funkcjonować czasami. Przyczyną może być nieprawidłowo sformułowany adres URL podczas operacji kodowania i dekodowania. Zamiast tego możesz uzyskać link bezpośrednio z pliku * _saslink.txt z maszyny wirtualnej.

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

- Stan wdrożenia rozszerzenia (w obszarze powiadomień) może wskazywać na "wdrożenie w toku", mimo że rozszerzenie zostało pomyślnie zainicjowane.

    Ten problem można bezpiecznie zignorować, o ile stan rozszerzenia wskazuje, że rozszerzenie zostało pomyślnie zainicjowane.
- Niektóre problemy można rozwiązać podczas instalacji przy użyciu dzienników rozszerzeń. Dane wyjściowe wykonania rozszerzenia są rejestrowane w plikach znalezionych w następującym katalogu:

    `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>`

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję **Uzyskaj pomoc techniczną**. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).