---
title: Tworzenie monitora połączeń — PowerShell
titleSuffix: Azure Network Watcher
description: Dowiedz się, jak utworzyć monitor połączeń przy użyciu programu PowerShell.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 7f175d82b650871437a506ea4513f0ae28360f68
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833035"
---
# <a name="create-a-connection-monitor-by-using-powershell"></a>Tworzenie monitora połączeń przy użyciu programu PowerShell

> [!IMPORTANT]
> Od 1 lipca 2021 nie będzie można dodawać nowych testów w istniejącym obszarze roboczym ani włączać nowego obszaru roboczego w Network Performance Monitor. Nie będzie również można dodawać nowych monitorów połączeń w monitorze połączeń (klasyczny). Można nadal korzystać z testów i monitorów połączeń utworzonych przed 1 lipca 2021. Aby zminimalizować przerwy w działaniu usługi z bieżącymi obciążeniami, należy [migrować testy z Network Performance Monitor ](migrate-to-connection-monitor-from-network-performance-monitor.md) lub  [przeprowadzić migrację z monitora połączeń (klasycznego)](migrate-to-connection-monitor-from-connection-monitor-classic.md) do nowego monitora połączeń na platformie Azure Network Watcher przed 29 lutego 2024.


Dowiedz się, jak monitorować komunikację między zasobami przy użyciu funkcji Monitor połączeń w usłudze Azure Network Watcher.


## <a name="before-you-begin"></a>Zanim rozpoczniesz

W monitorach połączeń utworzonych za pomocą monitora połączeń można dodawać zarówno maszyny lokalne, jak i maszyny wirtualne platformy Azure jako źródła. Te monitory połączeń mogą również monitorować łączność z punktami końcowymi. Punkty końcowe mogą znajdować się na platformie Azure lub dowolnym innym adresem URL lub adresie IP.

Monitor połączenia obejmuje następujące jednostki:

* **Zasób monitora połączeń**: zasób platformy Azure specyficzny dla regionu. Wszystkie poniższe jednostki są właściwościami zasobu monitora połączeń.
* **Punkt końcowy**: Źródło lub miejsce docelowe, które uczestniczy w sprawdzaniu łączności. Przykładowe punkty końcowe obejmują maszyny wirtualne platformy Azure, agentów lokalnych, adresy URL i adresy IP.
* **Konfiguracja testu**: Konfiguracja specyficzna dla protokołu dla testu. Na podstawie wybranego protokołu można zdefiniować port, progi, częstotliwość testów i inne parametry.
* **Grupa testowa**: grupa zawierająca źródłowe punkty końcowe, docelowe punkty końcowe i konfiguracje testów. Monitor połączeń może zawierać więcej niż jedną grupę testową.
* **Test**: kombinacja źródłowego punktu końcowego, docelowego punktu końcowego i konfiguracji testu. Test to najbardziej szczegółowy poziom, na którym dane monitorowania są dostępne. Dane monitorowania obejmują procent testów zakończonych niepowodzeniem i czas błądzenia (RTT).

    ![Diagram przedstawiający monitor połączeń, który definiuje relację między grupami testów i testami.](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-a-connection-monitor"></a>Procedura tworzenia monitora połączeń

Użyj następujących poleceń, aby utworzyć monitor połączeń przy użyciu programu PowerShell.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>Opis właściwości

* **ConnectionMonitorName**: nazwa zasobu monitora połączeń.

* **Sub**: Identyfikator subskrypcji subskrypcji, w której chcesz utworzyć monitor połączeń.

* **NW**: Network Watcher identyfikator zasobu, w którym jest tworzony monitor połączeń.

* **Lokalizacja**: region, w którym jest tworzony monitor połączeń.

* **Punkty końcowe**
    * **Nazwa**: unikatowa nazwa dla każdego punktu końcowego.
    * **Identyfikator zasobu**: dla punktów końcowych platformy Azure identyfikator zasobu odnosi się do Azure Resource Manager identyfikatorem zasobu dla maszyn wirtualnych. W przypadku punktów końcowych spoza platformy Azure identyfikator zasobu odnosi się do Azure Resource Manager identyfikator zasobu dla obszaru roboczego Log Analytics połączonego z agentami nienależącymi do platformy Azure.
    * **Adres**: dotyczy tylko sytuacji, gdy nie określono identyfikatora zasobu lub jeśli identyfikator zasobu znajduje się w obszarze roboczym log Analytics. Jeśli jest używany bez identyfikatora zasobu, może to być adres URL lub IP dowolnego publicznego punktu końcowego. Jeśli jest używany z IDENTYFIKATORem zasobu Log Analytics, to odnosi się do nazwy FQDN agenta monitorowania.
    * **Filtr**: w przypadku punktów końcowych spoza platformy Azure Użyj filtrów, aby wybrać agentów monitorowania z obszaru roboczego log Analytics w ramach zasobu monitora połączeń. Jeśli filtry nie są ustawione, Wszyscy agenci należący do obszaru roboczego Log Analytics mogą być używani do monitorowania.
        * **Typ**: Ustaw jako **adres agenta**.
        * **Address**: Ustaw jako nazwę FQDN agenta lokalnego.

* **Grupy testowe**
    * **Nazwa**: nadaj nazwę grupie testowej.
    * **Źródła**: Wybierz spośród utworzonych wcześniej punktów końcowych. Punkty końcowe źródłowe oparte na platformie Azure muszą mieć zainstalowane rozszerzenie usługi Azure Network Watcher. w źródłowych punktach końcowych nie korzystających z platformy Azure należy zainstalować agenta Log Analytics platformy Azure. Aby zainstalować agenta dla źródła, zobacz [Instalowanie agentów monitorowania](./connection-monitor-overview.md#install-monitoring-agents).
    * **Miejsca docelowe**: Wybierz spośród utworzonych wcześniej punktów końcowych. Można monitorować łączność z maszynami wirtualnymi platformy Azure lub dowolnym punktem końcowym (publicznym adresem IP, URL lub nazwą FQDN) przez określenie ich jako miejsc docelowych. W jednej grupie testowej można dodać maszyny wirtualne platformy Azure, adresy URL pakietu Office 365, adresy URL Dynamics 365 i niestandardowe punkty końcowe.
    * **Wyłącz**: Wyłącz monitorowanie dla wszystkich źródeł i miejsc docelowych, które określa Grupa testowa.

* **Konfiguracje testowe**
    * **Nazwa**: nadaj nazwę konfiguracji testowej.
    * **TestFrequencySec**: Określ, jak często źródła mają być poddane ping w określonym protokole i porcie. Możesz wybrać 30 sekund, 1 minutę, 5 minut, 15 minut lub 30 minut. Źródła testują łączność z lokalizacjami docelowymi na podstawie wybranej wartości. Na przykład w przypadku wybrania 30 sekund źródła sprawdzają łączność z miejscem docelowym co najmniej raz w okresie 30 sekund.
    * **Protokół**: wybierz TCP, ICMP, http lub https. W zależności od protokołu można również wybrać następujące konfiguracje specyficzne dla protokołu:
        * **preferHTTPS**: Określ, czy używać protokołu HTTPS za pośrednictwem protokołu HTTP.
        * **port**: Określ wybrany port docelowy.
        * **disableTraceRoute**: zatrzymuje źródła odkrywania topologii i RTT przeskoków przez przeskok. Dotyczy to grup testów z protokołem TCP lub ICMP.
        * **Metoda**: Wybierz metodę żądania HTTP (get lub post). Dotyczy to konfiguracji testów przy użyciu protokołu HTTP.
        * **ścieżka**: Określ parametry ścieżki, które mają zostać dołączone do adresu URL.
        * **validStatusCodes**: wybierz odpowiednie kody stanu. Jeśli kod odpowiedzi nie jest zgodny, zostanie wyświetlony komunikat diagnostyczny.
        * **requestHeaders**: Określ niestandardowe ciągi nagłówka żądania, które przechodzą do miejsca docelowego.
    * **Próg sukcesu**: Ustaw wartości progowe dla następujących parametrów sieci:
        * **checksFailedPercent**: Ustaw procent kontroli, które mogą zakończyć się niepowodzeniem, gdy źródła sprawdzają łączność z lokalizacjami docelowymi przy użyciu określonych kryteriów. W przypadku protokołu TCP lub ICMP wartość procentowa niezakończonych testów może być równa wartości procentowej utraty pakietów. W przypadku protokołu HTTP to pole reprezentuje procent żądań HTTP, które nie otrzymały odpowiedzi.
        * **roundTripTimeMs**: Ustaw, jak długie źródła mogą posłużyć do łączenia się z miejscem docelowym przez konfigurację testu w milisekundach.

## <a name="scale-limits"></a>Limity skalowania

Monitory połączeń mają następujące limity skali:

* Maksymalna liczba monitorów połączeń na subskrypcję na region: 100
* Maksymalna liczba grup testów na monitor połączeń: 20
* Maksymalna liczba źródeł i miejsc docelowych na monitor połączeń: 100
* Maksymalna liczba konfiguracji testu na monitor połączeń: 20

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak analizować dane monitorowania i ustawiać alerty](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts).
* Dowiedz się [, jak zdiagnozować problemy w sieci](./connection-monitor-overview.md#diagnose-issues-in-your-network).
