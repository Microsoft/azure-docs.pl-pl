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
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: 532f045233f26a9a2933a19ae7a0a893195ad33f
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94384130"
---
# <a name="create-a-connection-monitor-using-powershell"></a>Tworzenie monitora połączeń przy użyciu programu PowerShell

Dowiedz się, jak utworzyć monitor połączeń do monitorowania komunikacji między zasobami przy użyciu programu PowerShell.

## <a name="before-you-begin"></a>Przed rozpoczęciem 

W monitorach połączeń utworzonych w monitorze połączeń można dodawać zarówno maszyny lokalne, jak i maszyny wirtualne platformy Azure jako źródła. Te monitory połączeń mogą również monitorować łączność z punktami końcowymi. Punkty końcowe mogą znajdować się na platformie Azure lub dowolnym innym adresem URL lub adresie IP.

Monitor połączeń obejmuje następujące jednostki:

* **Zasób monitora połączeń** — zasób platformy Azure specyficzny dla regionu. Wszystkie poniższe jednostki są właściwościami zasobu monitora połączeń.
* **Endpoint** — Źródło lub miejsce docelowe, które uczestniczy w sprawdzaniu łączności. Przykładowe punkty końcowe obejmują maszyny wirtualne platformy Azure, agentów lokalnych, adresy URL i adresy IP.
* **Konfiguracja testu** — Konfiguracja specyficzna dla protokołu dla testu. Na podstawie wybranego protokołu można zdefiniować port, progi, częstotliwość testów i inne parametry.
* **Grupa testowa** — grupa zawierająca źródłowe punkty końcowe, docelowe punkty końcowe i konfiguracje testów. Monitor połączeń może zawierać więcej niż jedną grupę testową.
* **Test** — połączenie źródłowego punktu końcowego, docelowego punktu końcowego i konfiguracji testu. Test to najbardziej szczegółowy poziom, na którym dane monitorowania są dostępne. Dane monitorowania obejmują procent testów zakończonych niepowodzeniem i czas błądzenia (RTT).

    ![Diagram przedstawiający monitor połączeń, który definiuje relację między grupami testów i testami](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-powershell"></a>Procedura tworzenia przy użyciu programu PowerShell

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

* connectionMonitorName — nazwa zasobu monitora połączeń

* Identyfikator subskrypcji PODRZĘDNEj subskrypcji, w której chcesz utworzyć monitor połączeń

* Identyfikator zasobu NW Network Watcher, w którym zostanie utworzony Menedżer CM 

* Lokalizacja — region, w którym zostanie utworzony monitor połączeń

* Punkty końcowe
    * Nazwa — unikatowa nazwa dla każdego punktu końcowego
    * resourceId — dla punktów końcowych platformy Azure identyfikator zasobu odnosi się do Azure Resource Manager IDENTYFIKATORem zasobu dla maszyn wirtualnych. W przypadku punktów końcowych spoza platformy Azure identyfikator zasobu odwołuje się do identyfikatora zasobu usługi Azure Resource Manager dla obszaru roboczego Log Analytics połączonego z agentami spoza platformy Azure.
    * adres — dotyczy tylko sytuacji, gdy nie określono identyfikatora zasobu lub jeśli identyfikator zasobu jest Log Analytics obszarze roboczym. Jeśli jest używany z IDENTYFIKATORem zasobu Log Analytics, to odnosi się do nazwy FQDN agenta, która może być używana do monitorowania. Jeśli jest używany bez identyfikatora zasobu, może to być adres URL lub IP dowolnego publicznego punktu końcowego.
    * Filtr — w przypadku punktów końcowych spoza platformy Azure Użyj opcji Filtruj, aby wybrać agentów z Log Analytics obszaru roboczego, które będą używane do monitorowania w ramach zasobu monitora połączeń. Jeśli filtry nie są ustawione, Wszyscy agenci należący do obszaru roboczego Log Analytics mogą być używani do monitorowania
        * Typ — Ustaw typ jako "adres agenta"
        * Address — Ustaw adres jako nazwę FQDN lokalnego agenta

* Grupy testowe
    * Nazwa — Nadaj nazwę grupie testowej.
    * testConfigurations-test Configurations, na podstawie których źródłowe punkty końcowe nawiązują połączenie z docelowym punktami końcowymi
    * źródła — wybierz spośród utworzonych powyżej punktów końcowych. Punkty końcowe źródłowej platformy Azure muszą mieć zainstalowane rozszerzenie usługi Azure Network Watcher i punkty końcowe źródłowe na platformie Azure muszą być haveAzure Log Analytics zainstalowanym agentem. Aby zainstalować agenta dla źródła, zobacz [Instalowanie agentów monitorowania](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents).
    * miejsca docelowe — wybierz spośród utworzonych powyżej punktów końcowych. Można monitorować łączność z maszynami wirtualnymi platformy Azure lub dowolnym punktem końcowym (publicznym adresem IP, URL lub nazwą FQDN) przez określenie ich jako miejsc docelowych. W jednej grupie testowej można dodać maszyny wirtualne platformy Azure, adresy URL pakietu Office 365, adresy URL Dynamics 365 i niestandardowe punkty końcowe.
    * Wyłącz — to pole służy do wyłączania monitorowania dla wszystkich źródeł i miejsc docelowych określanych przez grupę testową.

* Konfiguracje testowe
    * Nazwa — nazwa konfiguracji testu.
    * testFrequencySec — Określ, jak często źródła będą wysyłać polecenia ping do określonych protokołów i portów. Możesz wybrać 30 sekund, 1 minutę, 5 minut, 15 minut lub 30 minut. Źródła przetestują łączność do miejsc docelowych na podstawie wybranej wartości. Jeśli na przykład wybierzesz 30 sekund, źródła będą sprawdzać łączność z miejscem docelowym co najmniej raz w okresie 30 sekund.
    * Protokół — można wybrać TCP, ICMP, HTTP lub HTTPS. W zależności od protokołu można wykonać kilka konfiguracji specyficznych dla protokołu
        * preferHTTPS — Określ, czy używać protokołu HTTPS przez HTTP
        * Port — określ wybrany port docelowy.
        * disableTraceRoute — dotyczy grup testów, których protokół to TCP lub ICMP. Uniemożliwia ona źródłem odnajdywania topologii i RTT przeskoków przez przeskok.
    * successThreshold — można ustawić progi dla następujących parametrów sieci:
        * checksFailedPercent — Ustaw procent kontroli, które mogą zakończyć się niepowodzeniem, gdy źródła sprawdzają łączność z lokalizacjami docelowymi przy użyciu określonych kryteriów. W przypadku protokołu TCP lub ICMP wartość procentowa nieudanych testów może być równa wartości procentowej utraty pakietów. W przypadku protokołu HTTP to pole reprezentuje procent żądań HTTP, które nie otrzymały odpowiedzi.
        * roundTripTimeMs — Ustaw czas RTT w milisekundach, przez jaki źródła mogą być wykonywane w celu nawiązania połączenia z miejscem docelowym za pośrednictwem konfiguracji testu.

## <a name="scale-limits"></a>Limity skalowania

Monitory połączeń mają następujące limity skali:

* Maksymalna liczba monitorów połączeń na subskrypcję na region: 100
* Maksymalna liczba grup testów na monitor połączeń: 20
* Maksymalna liczba źródeł i miejsc docelowych na monitor połączeń: 100
* Maksymalna liczba konfiguracji testu na monitor połączeń: 20

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak analizować dane monitorowania i ustawiać alerty](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* Dowiedz się [, jak zdiagnozować problemy w sieci](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)
