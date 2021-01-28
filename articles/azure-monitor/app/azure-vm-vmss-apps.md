---
title: Monitorowanie wydajności na maszynach wirtualnych platformy Azure — Application Insights platformy Azure
description: Monitorowanie wydajności aplikacji dla maszyn wirtualnych platformy Azure i zestawów skalowania maszyn wirtualnych platformy Azure. Załaduj wykres i czas odpowiedzi, informacje o zależnościach i ustaw alerty wydajności.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 0ea005427348e5265867a9e7ee805b0e6aa202f2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933905"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Wdrażanie agenta Application Insights Azure Monitor na maszynach wirtualnych platformy Azure i w zestawach skalowania maszyn wirtualnych platformy Azure

Włączenie monitorowania w aplikacjach sieci Web opartych na architekturze .NET działających na [maszynach wirtualnych platformy Azure](https://azure.microsoft.com/services/virtual-machines/) i w [usłudze Azure Virtual Machine Sets](../../virtual-machine-scale-sets/index.yml) jest teraz łatwiejsze niż kiedykolwiek wcześniej. Uzyskaj wszystkie korzyści wynikające z używania Application Insights bez modyfikowania kodu.

W tym artykule omówiono Włączanie monitorowania Application Insights przy użyciu agenta Application Insights i przedstawiono wstępne wskazówki dotyczące automatyzowania procesu wdrożeń na dużą skalę.

> [!IMPORTANT]
> Usługa Azure Application Insights Agent dla aplikacji ASP.NET działających na **maszynach wirtualnych platformy Azure i VMSS** jest obecnie w publicznej wersji zapoznawczej. Aby monitorować aplikacje ASP.Net uruchomione **lokalnie**, użyj [agenta Application Insights platformy Azure dla serwerów lokalnych](./status-monitor-v2-overview.md), które są ogólnie dostępne i w pełni obsługiwane.
> Wersja zapoznawcza dla maszyn wirtualnych platformy Azure i VMSS jest udostępniana bez umowy dotyczącej poziomu usług i nie jest ona zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, a niektóre z nich mogą mieć ograniczone możliwości.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

Istnieją dwa sposoby włączania monitorowania aplikacji dla maszyn wirtualnych platformy Azure i aplikacji hostowanych w ramach zestawów skalowania maszyn wirtualnych platformy Azure:

* Bez **kodu** za pośrednictwem agenta Application Insights
    * Ta metoda jest najłatwiejsza do włączenia i nie jest wymagana żadna Konfiguracja zaawansowana. Jest on często określany jako "środowisko uruchomieniowe".

    * W przypadku maszyn wirtualnych platformy Azure i zestawów skalowania maszyn wirtualnych platformy Azure zalecamy co najmniej włączenie tego poziomu monitorowania. Po tym, w zależności od konkretnego scenariusza, można sprawdzić, czy jest wymagana Instrumentacja ręczna.

    * Agent Application Insights wykonuje autozbieranie informacji o tej samej zależności, co oznacza, że jest to zestaw .NET SDK. Zobacz [autozbieranie zależności](./auto-collect-dependencies.md#net) , aby dowiedzieć się więcej.
        > [!NOTE]
        > Obecnie obsługiwane są tylko aplikacje obsługiwane przez program .NET IIS. Użyj zestawu SDK do Instrumentacji aplikacji ASP.NET Core, Java i Node.js hostowanych na maszynach wirtualnych platformy Azure i w zestawach skalowania maszyn wirtualnych.

* **Oparte na kodzie** za pośrednictwem zestawu SDK

    * Takie podejście jest znacznie bardziej dostosowywalne, ale wymaga [dodania zależności do pakietów NuGet zestawu Application Insights SDK](./asp-net.md). Ta metoda oznacza również, że konieczne jest zarządzanie aktualizacjami do najnowszej wersji pakietów.

    * Jeśli konieczne jest wykonywanie niestandardowych wywołań interfejsu API w celu śledzenia zdarzeń/zależności, które nie są przechwytywane domyślnie przy użyciu monitorowania opartego na agentach, należy użyć tej metody. Zapoznaj się z [interfejsem API dla niestandardowych zdarzeń i metryk](./api-custom-events-metrics.md) , aby dowiedzieć się więcej.

> [!NOTE]
> W przypadku wykrycia zarówno Instrumentacji opartej na agencie, jak i ręcznego zestawu SDK można wykryć tylko ręczne ustawienia Instrumentacji. Ma to na celu uniemożliwienie wysyłania zduplikowanych danych. Aby dowiedzieć się więcej na ten temat, zapoznaj się z [sekcją rozwiązywania problemów](#troubleshooting) poniżej.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Zarządzanie agentem Application Insights dla aplikacji .NET na maszynach wirtualnych platformy Azure przy użyciu programu PowerShell

> [!NOTE]
> Przed zainstalowaniem agenta Application Insights potrzebne są parametry połączenia. [Utwórz nowy zasób Application Insights](./create-new-resource.md) lub skopiuj parametry połączenia z istniejącego zasobu usługi Application Insights.

> [!NOTE]
> Jesteś nowym w programie PowerShell? Zapoznaj się z [przewodnikiem wprowadzenie](/powershell/azure/get-started-azureps).

Zainstaluj lub zaktualizuj agenta Application Insights jako rozszerzenie dla usługi Azure Virtual Machines
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> Agenta Application Insights można zainstalować lub zaktualizować w ramach wielu Virtual Machines na skalę przy użyciu pętli programu PowerShell.

Odinstaluj rozszerzenie agenta Application Insights z maszyny wirtualnej platformy Azure
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Przeszukiwanie stanu rozszerzenia agenta Application Insights dla maszyny wirtualnej platformy Azure
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Pobierz listę zainstalowanych rozszerzeń dla maszyny wirtualnej platformy Azure
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
Możliwe jest również wyświetlenie zainstalowanych rozszerzeń w [bloku maszyny wirtualnej platformy Azure](../../virtual-machines/extensions/overview.md) w portalu.

> [!NOTE]
> Sprawdź instalację, klikając Live Metrics Stream w ramach zasobu Application Insights skojarzonego z parametrami połączenia użytymi do wdrożenia rozszerzenia agenta Application Insights. W przypadku wysyłania danych z wielu Virtual Machines wybierz docelowe maszyny wirtualne platformy Azure w polu Nazwa serwera. Rozpoczęcie przepływu danych może potrwać do minuty.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Zarządzanie agentem Application Insights dla aplikacji .NET w zestawach skalowania maszyn wirtualnych platformy Azure przy użyciu programu PowerShell

Instalowanie lub aktualizowanie agenta Application Insights jako rozszerzenia zestawu skalowania maszyn wirtualnych platformy Azure
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Odinstaluj rozszerzenie monitorowania aplikacji z zestawów skalowania maszyn wirtualnych platformy Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Zbadaj stan rozszerzenia monitorowania aplikacji dla zestawów skalowania maszyn wirtualnych platformy Azure
```powershell
# Not supported by extensions framework
```

Pobierz listę zainstalowanych rozszerzeń dla zestawów skalowania maszyn wirtualnych platformy Azure
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Znajdź wskazówki dotyczące rozwiązywania problemów Application Insights rozszerzenia agenta monitorowania dla aplikacji platformy .NET działających na maszynach wirtualnych platformy Azure i w zestawach skalowania maszyn wirtualnych.

> [!NOTE]
> Aplikacje .NET Core, Java i Node.js są obsługiwane tylko na maszynach wirtualnych platformy Azure i w zestawach skalowania maszyn wirtualnych platformy Azure za pośrednictwem usługi Instrumentacja ręcznego zestawu SDK, w związku z czym poniższe kroki nie mają zastosowania do tych scenariuszy.

Dane wyjściowe wykonania rozszerzenia są rejestrowane w plikach znalezionych w następujących katalogach:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [wdrożyć aplikację w zestawie skalowania maszyn wirtualnych platformy Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Skonfiguruj testy sieci Web dostępności](monitor-web-app-availability.md) , aby otrzymywać alerty, jeśli punkt końcowy nie działa.
