---
title: Rozszerzenie maszyny wirtualnej usługi Log Analytics dla systemu Windows
description: Wdróż agenta Log Analytics na maszynie wirtualnej z systemem Windows przy użyciu rozszerzenia maszyny wirtualnej.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 06/26/2020
ms.openlocfilehash: 7757bd765bcb02782b6199f71c4a6e460b7b8143
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559022"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Rozszerzenie maszyny wirtualnej usługi Log Analytics dla systemu Windows

Dzienniki Azure Monitor udostępniają możliwości monitorowania między zasobami w chmurze i lokalnymi. Rozszerzenie maszyny wirtualnej Log Analytics agenta dla systemu Windows jest publikowane i obsługiwane przez firmę Microsoft. Rozszerzenie instaluje agenta Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyny wirtualne w istniejącym Log Analytics obszarze roboczym. Ten dokument zawiera szczegółowe informacje o obsługiwanych platformach, konfiguracjach i opcjach wdrażania dla rozszerzenia maszyny wirtualnej Log Analytics dla systemu Windows.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Szczegółowe informacje o obsługiwanych systemach operacyjnych Windows można znaleźć w artykule [omówienie Azure Monitor agentów](../../azure-monitor/agents/agents-overview.md#supported-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Wersja rozszerzenia agenta i maszyny wirtualnej
Poniższa tabela zawiera mapowanie wersji rozszerzenia maszyny wirtualnej z systemem Windows Log Analytics i pakietu agenta Log Analytics dla każdej wersji. 

| Wersja pakietu Log Analytics Windows Agent | Log Analytics wersja rozszerzenia maszyny wirtualnej z systemem Windows | Data wydania | Informacje o wersji |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18053| 1.0.18053.0 | Październik 2020 r.   | <ul><li>Nowe narzędzie do rozwiązywania problemów z agentem</li><li>Aktualizacje dotyczące sposobu, w jaki Agent obsługuje zmiany certyfikatów w usługach platformy Azure</li></ul> |
| 10.20.18040 | 1.0.18040.2 | Sierpień 2020 r.   | <ul><li>Rozwiązuje problem w usłudze Azure Arc</li></ul> |
| 10.20.18038 | 1.0.18038 | Kwiecień 2020 r.   | <ul><li>Umożliwia łączność za pośrednictwem prywatnego linku przy użyciu Azure Monitor zakresy linków prywatnych</li><li>Dodaje ograniczenie pozyskiwania, aby uniknąć nagłego i przypadkowego napływu w obszarze roboczym</li><li>Dodaje obsługę dodatkowych chmur Azure Government i regionów</li><li>Rozwiązuje usterkę, w której HealthService.exe awaria</li></ul> |
| 10.20.18029 | 1.0.18029 | Marzec 2020 r.   | <ul><li>Dodaje obsługę podpisywania kodu SHA-2</li><li>Udoskonalenie instalacji i zarządzania rozszerzeniami maszyny wirtualnej</li><li>Rozwiązuje usterkę w usłudze Azure Arc na potrzeby integracji serwerów</li><li>Dodaje wbudowane narzędzie do rozwiązywania problemów z obsługą klienta</li><li>Dodaje obsługę dodatkowych regionów Azure Government</li> |
| 10.20.18018 | 1.0.18018 | Październik 2019 r. | <ul><li> Drobne poprawki błędów i ulepszenia stabilizacji </li></ul> |
| 10.20.18011 | 1.0.18011 | Lipiec 2019 r. | <ul><li> Drobne poprawki błędów i ulepszenia stabilizacji </li><li> Zwiększono MaxExpressionDepth do 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Czerwiec 2019 r. | <ul><li> Drobne poprawki błędów i ulepszenia stabilizacji </li><li> Dodano możliwość wyłączania poświadczeń domyślnych podczas nawiązywania połączenia z serwerem proxy (obsługa WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | Marzec 2019 r. | <ul><li>Niewielkie poprawki stabilizacji </li></ul> |
| 10.19.10006 | n/d | Dec 2018 | <ul><li> Niewielkie poprawki stabilizacji </li></ul> | 
| 8.0.11136 | n/d | Września 2018 |  <ul><li> Dodano obsługę wykrywania zmiany identyfikatora zasobu podczas przenoszenia maszyny wirtualnej </li><li> Dodano obsługę identyfikatora zasobu raportowania podczas korzystania z instalacji niezwiązanej z rozszerzeniem </li></ul>| 
| 8.0.11103 | n/d |  Kwiecień 2018 r. | |
| 8.0.11081 | 1.0.11081 | Lis 2017 | | 
| 8.0.11072 | 1.0.11072 | Września 2017 | |
| 8.0.11049 | 1.0.11049 | 2017 lutego | |


### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center automatycznie inicjuje agenta Log Analytics i łączy go z domyślnym obszarem roboczym Log Analytics subskrypcji platformy Azure. Jeśli używasz Azure Security Center, nie wykonuj kroków opisanych w tym dokumencie. Spowoduje to zastąpienie skonfigurowanego obszaru roboczego i przerwanie połączenia z Azure Security Center.

### <a name="internet-connectivity"></a>Łączność z Internetem
Rozszerzenie agenta Log Analytics dla systemu Windows wymaga, aby docelowa maszyna wirtualna była połączona z Internetem. 

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON przedstawia schemat rozszerzenia agenta Log Analytics. Rozszerzenie wymaga identyfikatora obszaru roboczego i klucza obszaru roboczego z docelowego obszaru roboczego Log Analytics. Można je znaleźć w ustawieniach obszaru roboczego w Azure Portal. Ponieważ klucz obszaru roboczego powinien być traktowany jako poufne dane, powinien on być przechowywany w konfiguracji chronionego ustawienia. Dane ustawienia chronionego rozszerzenia maszyny wirtualnej platformy Azure są szyfrowane i odszyfrowywane tylko na docelowej maszynie wirtualnej. Należy pamiętać, że w **Identyfikator obszaru roboczego** i **workspaceKey** jest rozróżniana wielkość liter.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość/przykład |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft. EnterpriseCloud. Monitoring |
| typ | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| Identyfikator obszaru roboczego (np.) * | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (np.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI + rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ = = |

\* Identyfikator obszaru roboczego jest nazywana consumerId w interfejsie API Log Analytics.

> [!NOTE]
> Aby uzyskać dodatkowe właściwości, zobacz Azure [Connect komputery z systemem Windows do Azure monitor](../../azure-monitor/agents/agent-windows.md).

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny wirtualnej platformy Azure można wdrażać za pomocą szablonów Azure Resource Manager. Schemat JSON opisany w poprzedniej sekcji można użyć w szablonie Azure Resource Manager, aby uruchomić rozszerzenie agenta Log Analytics podczas wdrażania szablonu Azure Resource Manager. Przykładowy szablon zawierający rozszerzenie maszyny wirtualnej agenta Log Analytics można znaleźć w [galerii szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>Szablon nie obsługuje określania więcej niż jednego identyfikatora obszaru roboczego i klucza obszaru roboczego, jeśli chcesz skonfigurować agenta do raportowania do wielu obszarów roboczych. Aby skonfigurować agenta do raportowania do wielu obszarów roboczych, zobacz [Dodawanie lub usuwanie obszaru roboczego](../../azure-monitor/agents/agent-manage.md#adding-or-removing-a-workspace).  

KOD JSON rozszerzenia maszyny wirtualnej może być zagnieżdżony w obrębie zasobu maszyny wirtualnej lub umieszczony na głównym lub najwyższym poziomie szablonu JSON Menedżer zasobów. Położenie pliku JSON wpływa na wartość nazwy zasobu i typu. Aby uzyskać więcej informacji, zobacz [Ustawianie nazwy i typu dla zasobów podrzędnych](../../azure-resource-manager/templates/child-resource-name-type.md). 

W poniższym przykładzie założono, że rozszerzenie Log Analytics jest zagnieżdżone w ramach zasobu maszyny wirtualnej. Podczas zagnieżdżania zasobu rozszerzenia kod JSON jest umieszczany w `"resources": []` obiekcie maszyny wirtualnej.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Podczas umieszczania kodu JSON rozszerzenia w katalogu głównym szablonu nazwa zasobu zawiera odwołanie do nadrzędnej maszyny wirtualnej, a typ odzwierciedla konfigurację zagnieżdżoną. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>Wdrażanie przy użyciu programu PowerShell

Za pomocą `Set-AzVMExtension` polecenia można wdrożyć rozszerzenie maszyny wirtualnej agenta log Analytics na istniejącej maszynie wirtualnej. Przed uruchomieniem polecenia należy zapisać konfigurację publiczną i prywatną w tabeli skrótów programu PowerShell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal i przy użyciu modułu Azure PowerShell. Aby wyświetlić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie przy użyciu modułu Azure PowerShell.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w plikach znalezionych w następującym katalogu:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).
