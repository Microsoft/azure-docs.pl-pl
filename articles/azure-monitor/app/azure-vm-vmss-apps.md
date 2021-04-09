---
title: Monitorowanie wydajności na maszynach wirtualnych platformy Azure — Application Insights platformy Azure
description: Monitorowanie wydajności aplikacji dla maszyn wirtualnych platformy Azure i zestawów skalowania maszyn wirtualnych platformy Azure. Załaduj wykres i czas odpowiedzi, informacje o zależnościach i ustaw alerty wydajności.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 0951d1d622f59de4780735fad78ac73649ea2369
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711485"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Wdrażanie agenta Application Insights Azure Monitor na maszynach wirtualnych platformy Azure i w zestawach skalowania maszyn wirtualnych platformy Azure

Włączenie monitorowania dla aplikacji sieci Web platformy .NET lub języka Java działających na [maszynach wirtualnych platformy Azure](https://azure.microsoft.com/services/virtual-machines/) i [zestawów skalowania maszyn wirtualnych platformy Azure](../../virtual-machine-scale-sets/index.yml) jest teraz łatwiejsze niż kiedykolwiek wcześniej. Uzyskaj wszystkie korzyści wynikające z używania Application Insights bez modyfikowania kodu.

W tym artykule omówiono Włączanie monitorowania Application Insights przy użyciu agenta Application Insights i przedstawiono wstępne wskazówki dotyczące automatyzowania procesu wdrożeń na dużą skalę.
> [!IMPORTANT]
> Aplikacje **języka Java** działające na maszynach wirtualnych i VMSSach platformy Azure są monitorowane przy użyciu programu **[Application Insights Java 3,0 Agent](./java-in-process-agent.md)**, który jest ogólnie dostępny.

> [!IMPORTANT]
> Usługa Azure Application Insights Agent dla aplikacji ASP.NET działających na **maszynach wirtualnych platformy Azure i VMSS** jest obecnie w publicznej wersji zapoznawczej. Aby monitorować aplikacje ASP.Net uruchomione **lokalnie**, użyj [agenta Application Insights platformy Azure dla serwerów lokalnych](./status-monitor-v2-overview.md), które są ogólnie dostępne i w pełni obsługiwane.
> Wersja zapoznawcza dla maszyn wirtualnych platformy Azure i VMSS jest udostępniana bez umowy dotyczącej poziomu usług i nie jest ona zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, a niektóre z nich mogą mieć ograniczone możliwości.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

Istnieją dwa sposoby włączania monitorowania aplikacji dla maszyn wirtualnych platformy Azure i aplikacji hostowanych w ramach zestawów skalowania maszyn wirtualnych platformy Azure:

### <a name="auto-instrumentation-via-application-insights-agent"></a>Funkcja autoinstrumentacja za pośrednictwem agenta Application Insights

* Ta metoda jest najłatwiejsza do włączenia i nie jest wymagana żadna Konfiguracja zaawansowana. Jest on często określany jako "środowisko uruchomieniowe".

* W przypadku maszyn wirtualnych platformy Azure i zestawów skalowania maszyn wirtualnych platformy Azure zalecamy co najmniej włączenie tego poziomu monitorowania. Po tym, w zależności od konkretnego scenariusza, można sprawdzić, czy jest wymagana Instrumentacja ręczna.

> [!NOTE]
> Funkcja autoinstrumentacja jest obecnie dostępna tylko dla aplikacji hostowanych przez program .NET IIS i środowisko Java. Użyj zestawu SDK do Instrumentacji aplikacji ASP.NET Core, Node.js i Python hostowanych na maszynach wirtualnych platformy Azure i w zestawach skalowania maszyn wirtualnych.


#### <a name="net"></a>.NET

  * Agent Application Insights wykonuje autozbieranie informacji o tej samej zależności, co oznacza, że jest to zestaw .NET SDK. Zobacz [autozbieranie zależności](./auto-collect-dependencies.md#net) , aby dowiedzieć się więcej.
        
#### <a name="java"></a>Java
  * W przypadku języka Java zalecane podejście **[Application Insights java 3,0 Agent](./java-in-process-agent.md)** . Najpopularniejsze biblioteki i struktury, jak również dzienniki i zależności, są [zbierane](./java-in-process-agent.md#auto-collected-requests-dependencies-logs-and-metrics)z wieloma [dodatkowymi konfiguracjami](./java-standalone-config.md)

### <a name="code-based-via-sdk"></a>Oparte na kodzie za pośrednictwem zestawu SDK
    
#### <a name="net"></a>.NET
  * W przypadku aplikacji .NET takie podejście jest znacznie bardziej dostosowywalne, ale wymaga [dodania zależności do pakietów NuGet zestawu Application Insights SDK](./asp-net.md). Ta metoda oznacza również, że konieczne jest zarządzanie aktualizacjami do najnowszej wersji pakietów.

  * Jeśli konieczne jest wykonywanie niestandardowych wywołań interfejsu API w celu śledzenia zdarzeń/zależności, które nie są przechwytywane domyślnie przy użyciu monitorowania opartego na agentach, należy użyć tej metody. Zapoznaj się z [interfejsem API dla niestandardowych zdarzeń i metryk](./api-custom-events-metrics.md) , aby dowiedzieć się więcej.

    > [!NOTE]
    > Tylko dla aplikacji platformy .NET — Jeśli Instrumentacja oparta na agentach i ręczna z zestawem SDK zostanie wykryta, zostaną uznane tylko ręczne ustawienia Instrumentacji. Ma to na celu uniemożliwienie wysyłania zduplikowanych danych. Aby dowiedzieć się więcej na ten temat, zapoznaj się z [sekcją rozwiązywania problemów](#troubleshooting) poniżej.

#### <a name="net-core"></a>.NET Core
Aby monitorować aplikacje platformy .NET Core, użyj [zestawu SDK](./asp-net-core.md) 

#### <a name="java"></a>Java 

Jeśli potrzebujesz dodatkowej telemetrii dla aplikacji Java, zobacz co [to jest dostępne](./java-in-process-agent.md#send-custom-telemetry-from-your-application), Dodaj [niestandardowe wymiary](./java-standalone-config.md#custom-dimensions)lub Użyj [procesorów telemetrii](./java-standalone-telemetry-processors.md). 

#### <a name="nodejs"></a>Node.js

Aby instrumentować Node.js aplikację, użyj [zestawu SDK](./nodejs.md).

#### <a name="python"></a>Python

Aby monitorować aplikacje Python, użyj [zestawu SDK](./opencensus-python.md).

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
> Aplikacje .NET Core, Node.js i Python są obsługiwane tylko w maszynach wirtualnych platformy Azure i w zestawach skalowania maszyn wirtualnych platformy Azure za pośrednictwem usługi Instrumentacja ręcznego zestawu SDK, w związku z czym poniższe kroki nie mają zastosowania do tych scenariuszy.

Dane wyjściowe wykonania rozszerzenia są rejestrowane w plikach znalezionych w następujących katalogach:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [wdrożyć aplikację w zestawie skalowania maszyn wirtualnych platformy Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Skonfiguruj testy sieci Web dostępności](monitor-web-app-availability.md) , aby otrzymywać alerty, jeśli punkt końcowy nie działa.