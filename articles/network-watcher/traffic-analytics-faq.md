---
title: Często zadawane pytania dotyczące analizy ruchu na platformie Azure | Microsoft Docs
description: Uzyskaj odpowiedzi na niektóre z najczęściej zadawanych pytań dotyczących analizy ruchu.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: 98c0a6f88da717256e78a748902317a90a369a9c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533622"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Analiza ruchu często zadawanych pytań

W tym artykule zebrano w jednym miejscu wiele często zadawanych pytań dotyczących analizy ruchu w usłudze Azure Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Jakie są wymagania wstępne dotyczące korzystania z analizy ruchu?

Analiza ruchu wymaga następujących wymagań wstępnych:

- Subskrypcja Network Watcher włączona.
- Dzienniki przepływu sieciowej grupy zabezpieczeń są włączone dla sieciowych grup zabezpieczeń, które chcesz monitorować.
- Konto usługi Azure Storage do przechowywania nieprzetworzonych dzienników przepływu.
- Obszar roboczy usługi Azure Log Analytics z dostępem do odczytu i zapisu.

Aby włączyć analizę ruchu, Twoje konto musi spełniać jedną z następujących wymagań:

- Twoje konto musi mieć jedną z następujących ról platformy Azure w zakresie subskrypcji: właściciel, współautor, czytelnik lub współautor sieci.
- Jeśli Konto nie jest przypisane do jednej z wcześniej wymienionych ról, musi być przypisane do roli niestandardowej, która ma przypisane następujące akcje na poziomie subskrypcji.
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
Aby sprawdzić role przypisane do użytkownika dla subskrypcji:

1. Zaloguj się do platformy Azure przy użyciu **konta Login-AzAccount.** 

2. Wybierz wymaganą subskrypcję przy użyciu **opcji Select-AzSubscription.** 

3. Aby wyświetlić listę wszystkich ról przypisanych do określonego użytkownika, użyj **get-AzRoleAssignment -SignInName [adres e-mail użytkownika] -IncludeClassicAdministrators.** 

Jeśli nie widzisz żadnych danych wyjściowych, skontaktuj się z odpowiednim administratorem subskrypcji, aby uzyskać dostęp do uruchamiania poleceń. Aby uzyskać więcej informacji, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Czy dzienniki przepływów mogą być włączane przez sieciowe gs w innych regionach niż mój obszar roboczy?

Tak, te sieciowe sieci mogą być w różnych regionach niż obszar roboczy usługi Log Analytics.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Czy w jednym obszarze roboczym można skonfigurować wiele sieciowych grupy zasad?

Tak.

## <a name="can-i-use-an-existing-workspace"></a>Czy mogę użyć istniejącego obszaru roboczego?

Tak. Jeśli wybierzesz istniejący obszar roboczy, upewnij się, że został on zmigrowany do nowego języka zapytań. Jeśli nie chcesz uaktualniać obszaru roboczego, musisz utworzyć nowy. Aby uzyskać więcej informacji na temat nowego języka zapytań, zobacz [Azure Monitor uaktualnianie dzienników do nowego przeszukiwania dzienników.](../azure-monitor/logs/log-query-overview.md)

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Czy moje konto usługi Azure Storage może być w jednej subskrypcji, a mój obszar roboczy usługi Log Analytics może być w innej subskrypcji?

Tak, Twoje konto usługi Azure Storage może być w jednej subskrypcji, a obszar roboczy usługi Log Analytics może być w innej subskrypcji.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Czy mogę przechowywać nieprzetworzone dzienniki w innej subskrypcji?

Tak. Możesz skonfigurować dzienniki przepływu sieciowej organizacji zabezpieczeń tak, aby były wysyłane do konta magazynu znajdującego się w innej subskrypcji, pod warunkiem, że masz odpowiednie uprawnienia i że konto magazynu znajduje się w tym samym regionie co sieciowa żadna z tych sieci. Sieciowa i docelowa konto magazynu muszą również współużytkowały tę samą Azure Active Directory Dzierżawa.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Co zrobić, jeśli nie mogę skonfigurować sieciowej organizacji sieciowej do analizy ruchu z powodu błędu "Nie znaleziono"?

Wybierz obsługiwany region. W przypadku wybrania regionu, który nie jest obsługiwany, zostanie wyświetlony błąd "Nie znaleziono". Obsługiwane regiony są wymienione wcześniej w tym artykule.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Co zrobić, jeśli widzę stan "Nie można załadować" na stronie dzienników przepływu sieciowej organizacji zabezpieczeń?

Aby rejestrowanie przepływu działało prawidłowo, należy zarejestrować dostawcę Microsoft.Insights. Jeśli nie masz pewności, czy dostawca Microsoft.Insights jest zarejestrowany dla Twojej subskrypcji, zastąp *ciąg xxxxx-xxxxx-xxxxxx-xxxx* w poniższym poleceniu i uruchom następujące polecenia w programie PowerShell:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Skonfigurowano rozwiązanie. Dlaczego nie widzę niczego na pulpicie nawigacyjnym?

Pierwszy raz może upłynieć do 30 minut. Rozwiązanie musi najpierw agregować wystarczającą ilość danych, aby uzyskać istotne szczegółowe informacje. Następnie generuje raporty. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Co zrobić, jeśli zostanie wyświetlony ten komunikat: "Nie można odnaleźć żadnych danych w tym obszarze roboczym dla wybranego przedziału czasu. Spróbuj zmienić przedział czasu lub wybrać inny obszar roboczy."?

Wypróbuj następujące opcje:
- Zmień przedział czasu na górnym pasku.
- Wybierz inny obszar roboczy usługi Log Analytics na górnym pasku.
- Spróbuj uzyskać dostęp do analizy ruchu po 30 minutach, jeśli została ona ostatnio włączona.
    
Jeśli problemy będą się powtarzać, zgłaszaj problemy na [forum user voice](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Co zrobić, jeśli zostanie wyświetlony ten komunikat: "Analizowanie dzienników przepływu sieciowej organizacji zabezpieczeń po raz pierwszy. Ten proces może potrwać 20–30 minut. Wróć ponownie po pewnym czasie. 2) Jeśli powyższy krok nie działa i obszar roboczy znajduje się w bezpłatnej wersji SKU, sprawdź użycie obszaru roboczego tutaj, aby zweryfikować limit przydziału. W innym przypadku zapoznaj się z często zadawanych pytaniami, aby uzyskać więcej informacji".

Może zostać wyświetlony ten komunikat, ponieważ:
- Analiza ruchu została niedawno włączona i może jeszcze nie mieć zagregowanej ilości danych wystarczającej do uzyskania istotnych szczegółowych informacji.
- Używasz bezpłatnej wersji obszaru roboczego usługi Log Analytics i przekroczono limity przydziału. Może być konieczne użycie obszaru roboczego o większej pojemności.
    
Jeśli problemy będą się powtarzać, zgłaszaj problemy na [forum user voice](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Co zrobić, jeśli zostanie wyświetlony ten komunikat: "Wygląda na to, że mamy dane zasobów (topologię) i nie mamy informacji o przepływach. W międzyczasie kliknij tutaj, aby wyświetlić dane zasobów i zapoznać się z często zadawanych pytaniami, aby uzyskać więcej informacji".

Na pulpicie nawigacyjnym są dostępne informacje o zasobach. Nie ma jednak żadnych statystyk związanych z przepływem. Dane mogą nie być obecne z powodu brak przepływu komunikacji między zasobami. Poczekaj 60 minut i sprawdź ponownie stan. Jeśli problem będzie się powtarzać i będziesz mieć pewność, że komunikacja między zasobami istnieje, zgłaszaj obawy na [forum User Voice](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Czy mogę skonfigurować analizę ruchu przy użyciu programu PowerShell, Azure Resource Manager szablonu lub klienta?

Analizę ruchu można skonfigurować przy użyciu Windows PowerShell od wersji 6.2.1. Aby skonfigurować rejestrowanie przepływu i analizę ruchu dla określonej sieciowej sieciowej sieci przy użyciu polecenia cmdlet Set, zobacz [Set-AzNetworkWatcherConfigFlowLog.](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) Aby uzyskać informacje o rejestrowaniu przepływu i stanie analizy ruchu dla określonej sieciowej sieci, zobacz [Get-AzNetworkWatcherFlowLogStatus](/powershell/module/az.network/get-aznetworkwatcherflowlogstatus).

Obecnie nie można używać szablonu usługi Azure Resource Manager do konfigurowania analizy ruchu.

Aby skonfigurować analizę ruchu przy użyciu klienta Azure Resource Manager, zobacz poniższe przykłady.

**Przykładowe ustawianie polecenia cmdlet:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Pobierz przykładowe polecenie cmdlet:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```


## <a name="how-is-traffic-analytics-priced"></a>Jak są Analiza ruchu wyceny?

Analiza ruchu jest mierzona. Pomiar jest oparty na przetwarzaniu danych dziennika przepływu przez usługę i przechowywaniu wynikowych rozszerzonych dzienników w obszarze roboczym usługi Log Analytics. 

Na przykład zgodnie [](https://azure.microsoft.com/pricing/details/network-watcher/)z planem cenowym, biorąc pod uwagę region Zachodnio-środkowe stany USA, jeśli dane dzienników przepływu przechowywane na koncie magazynu przetworzonym przez usługę Analiza ruchu wynosi 10 GB, a rozszerzone dzienniki pozyskane w obszarze roboczym usługi Log Analytics to 1 GB, odpowiednie opłaty to: 10 x 2,3$ + 1 x 2,76$ = 25,76$

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Jak często Analiza ruchu przetwarzać dane?

Zapoznaj się z [sekcją agregacji danych w](./traffic-analytics-schema.md#data-aggregation) Analiza ruchu schemacie i agregacji danych

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Jak Analiza ruchu, że adres IP jest złośliwy? 

Analiza ruchu polega na wewnętrznych systemach analizy zagrożeń firmy Microsoft, aby adres IP został przez firmę Microsoft jako złośliwy. Systemy te wykorzystują różnorodne źródła danych telemetrycznych, takie jak produkty i usługi firmy Microsoft, Jednostka ds. Cyfrowych Działań Firmy Microsoft (DCU), Centrum zabezpieczeń firmy Microsoft (MSRC) i zewnętrzne źródła danych, a na ich podstawie tworzy się wiele inteligencji. Niektóre z tych danych to dane wewnętrzne firmy Microsoft. Jeśli znany adres IP jest oflagowany jako złośliwy, zdaj bilet pomocy technicznej, aby poznać szczegóły.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Jak ustawić alerty dotyczące Analiza ruchu danych?

Analiza ruchu nie ma wbudowanej obsługi alertów. Jednak ponieważ dane Analiza ruchu są przechowywane w u usługi Log Analytics, można pisać zapytania niestandardowe i ustawiać na nich alerty. Kroki:
- Możesz użyć krótkiego linku dla usługi Log Analytics w Analiza ruchu. 
- Używanie [udokumentowanego tutaj schematu do](traffic-analytics-schema.md) pisania zapytań 
- Kliknij pozycję "Nowa reguła alertu", aby utworzyć alert
- Zapoznaj się z [dokumentacją alertów dzienników,](../azure-monitor/alerts/alerts-log.md) aby utworzyć alert

## <a name="how-do-i-check-which-vms-are-receiving-most-on-premises-traffic"></a>Jak mogę, które maszyny wirtualne odbierają najwięcej ruchu lokalnego?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
| where <Scoping condition>
| mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
| where isnotempty(vm) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
| make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by vm
| render timechart
```

  W przypadku ip:

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
| where isnotempty(IP) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
| make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by IP
| render timechart
```

Dla godziny użyj formatu: yyyy-mm-dd 00:00:00

## <a name="how-do-i-check-standard-deviation-in-traffic-received-by-my-vms-from-on-premises-machines"></a>Jak mogę sprawdzić odchylenie standardowe ruchu odebranego przez moje maszyny wirtualne z maszyn lokalnych?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
| where isnotempty(vm) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + utboundBytes_d
| summarize deviation = stdev(traffic)  by vm
```

W przypadku ip:

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
| where isnotempty(IP) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
| summarize deviation = stdev(traffic)  by IP
```

## <a name="how-do-i-check-which-ports-are-reachable-or-blocked-between-ip-pairs-with-nsg-rules"></a>Jak mogę, które porty są dostępne (lub zablokowane) między parami adresów IP i regułami sieciowej sieciowej sieci

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and TimeGenerated between (startTime .. endTime)
| extend sourceIPs = iif(isempty(SrcIP_s), split(SrcPublicIPs_s, " ") , pack_array(SrcIP_s)),
destIPs = iif(isempty(DestIP_s), split(DestPublicIPs_s," ") , pack_array(DestIP_s))
| mvexpand SourceIp = sourceIPs to typeof(string)
| mvexpand DestIp = destIPs to typeof(string)
| project SourceIp = tostring(split(SourceIp, "|")[0]), DestIp = tostring(split(DestIp, "|")[0]), NSGList_s, NSGRule_s, DestPort_d, L4Protocol_s, FlowStatus_s 
| summarize DestPorts= makeset(DestPort_d) by SourceIp, DestIp, NSGList_s, NSGRule_s, L4Protocol_s, FlowStatus_s
```

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Jak nawigować za pomocą klawiatury w widoku mapy geograficznej?

Strona mapy geograficznej zawiera dwie główne sekcje:
    
- **Transparent:** baner w górnej części mapy geograficznej zawiera przyciski służące do wybierania filtrów dystrybucji ruchu (na przykład Wdrożenie, Ruch z krajów/regionów i Złośliwe). Po wybraniu przycisku odpowiedni filtr zostanie zastosowany na mapie. Jeśli na przykład wybierzesz przycisk Aktywne, mapa wyróżni aktywne centra danych we wdrożeniu.
- **Mapa:** pod transparentem sekcja mapy pokazuje rozkład ruchu między centrami danych platformy Azure i krajami/regionami.
    
### <a name="keyboard-navigation-on-the-banner"></a>Nawigacja za pomocą klawiatury na banerze
    
- Domyślnie na stronie mapy geograficznej dla baneru jest filtr "Azure DCs".
- Aby przejść do innego filtru, użyj `Tab` klucza lub `Right arrow` . Aby cofnąć się, użyj `Shift+Tab` klawisza lub `Left arrow` . Nawigacja do przodu jest od lewej do prawej, a następnie od góry do dołu.
- Naciśnij `Enter` lub klawisz `Down` strzałki, aby zastosować wybrany filtr. W oparciu o wybór i wdrożenie filtru wyróżniony jest jeden lub wiele węzłów w sekcji mapy.
- Aby przełączyć się między transparentem i mapą, naciśnij klawisz `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-map"></a>Nawigacja za pomocą klawiatury na mapie
    
- Po wybraniu dowolnego filtru na banerze i naciśnięciu klawisza fokus zostanie przeniesiony do jednego z wyróżnionych węzłów (centrum danych platformy Azure lub `Ctrl+F6` **kraj/region)** w widoku mapy.
- Aby przejść do innych wyróżnienia węzłów na mapie, użyj klawisza `Tab` lub `Right arrow` do przenoszenia do przodu. Użyj `Shift+Tab` lub `Left arrow` klawisza do ruchu wstecz.
- Aby wybrać dowolny wyróżniony węzeł na mapie, użyj `Enter` klawisza lub `Down arrow` .
- Po zaznaczeniu takich węzłów fokus jest przenoszyny do okna **narzędzi informacji** dla węzła. Domyślnie fokus jest przesuowany na zamknięty przycisk w **przyborowym polu informacji.** Aby przejść dalej wewnątrz widoku **Box,** użyj klawiszy i , aby przejść odpowiednio do przodu `Right arrow` i do `Left arrow` tyłu. Naciśnięcie `Enter` klawisza ma taki sam efekt jak wybranie przycisku ukierunkowanego w **przyborni informacji**.
- Po naciśnięciu klawisza , gdy fokus znajduje się na polu narzędzi informacji, fokus jest przenoszyny do punktów końcowych na tym samym kontynentze co `Tab` wybrany węzeł.  Użyj kluczy `Right arrow` i , aby przejść przez te punkty `Left arrow` końcowe.
- Aby przejść do innych punktów końcowych przepływu lub klastrów kontynentów, użyj funkcji do przesuwania do przodu `Tab` i `Shift+Tab` do tyłu.
- Gdy fokus znajduje się w klastrach **kontynentu**, użyj klawiszy strzałek lub , aby wyróżnić punkty `Enter` końcowe wewnątrz klastra `Down` kontynentu. Aby przechodzić między punktami końcowymi i przyciskiem zamykania w polu informacji klastra kontynentu, użyj odpowiednio klawisza lub dla ruchu do przodu `Right arrow` `Left arrow` i do tyłu. W dowolnym punkcie końcowym możesz użyć polecenia , aby przełączyć się do linii połączenia z `Shift+L` wybranego węzła do punktu końcowego. Możesz ponownie nacisnąć `Shift+L` klawisz , aby przejść do wybranego punktu końcowego.
        
### <a name="keyboard-navigation-at-any-stage"></a>Nawigacja za pomocą klawiatury na dowolnym etapie
    
- `Esc` Rozwijany wybór.
- Klucz `Up-arrow` wykonuje tę samą akcję co `Esc` . Klucz `Down arrow` wykonuje tę samą akcję co `Enter` .
- Użyj `Shift+Plus` funkcji , aby powiększyć i `Shift+Minus` pomniejszyć widok.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Jak nawigować przy użyciu klawiatury w widoku topologii sieci wirtualnej?

Strona topologii sieci wirtualnych zawiera dwie główne sekcje:
    
- **Transparent:** transparent w górnej części topologii sieci wirtualnych zawiera przyciski służące do wybierania filtrów dystrybucji ruchu (na przykład Połączone sieci wirtualne, Odłączone sieci wirtualne i Publiczne ip). Po wybraniu przycisku odpowiedni filtr jest stosowany do topologii. Jeśli na przykład wybierzesz przycisk Aktywne, topologia wyróżnia aktywne sieci wirtualne we wdrożeniu.
- **Topologia:** pod transparentem sekcja topologii pokazuje dystrybucję ruchu między sieciami wirtualnymi.
    
### <a name="keyboard-navigation-on-the-banner"></a>Nawigacja za pomocą klawiatury na banerze
    
- Domyślnie na stronie topologii sieci wirtualnych transparentu jest filtr "Połączone sieci wirtualne".
- Aby przejść do innego filtru, użyj `Tab` klawisza , aby przejść do przodu. Aby cofnąć się, użyj `Shift+Tab` klawisza . Nawigacja do przodu jest od lewej do prawej, a następnie od góry do dołu.
- Naciśnij `Enter` klawisz , aby zastosować wybrany filtr. W oparciu o wybór i wdrożenie filtru wyróżniony jest jeden lub wiele węzłów (sieć wirtualna) w sekcji topologii.
- Aby przełączyć się między transparentem a topologią, naciśnij klawisz `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-topology"></a>Nawigacja za pomocą klawiatury w topologii
    
- Po wybraniu dowolnego filtru na banerze i naciśnięciu klawisza fokus zostanie przeniesiony do jednego z wyróżnionych węzłów `Ctrl+F6` **(sieć** wirtualna) w widoku topologii.
- Aby przejść do innych wyróżnienia węzłów w widoku topologii, użyj `Shift+Right arrow` klucza do przenoszenia do przodu. 
- W wyróżnione węzły fokus jest **przenoszony** do okna narzędzi informacji dla węzła. Domyślnie fokus jest zmieniany na przycisk **Więcej szczegółów** na polu **narzędzi informacji.** Aby dalej poruszać się wewnątrz **widoku Box,** użyj klawiszy i , aby przejść odpowiednio do przodu i `Right arrow` do `Left arrow` tyłu. Naciśnięcie `Enter` klawisza ma taki sam efekt jak wybranie przycisku ukierunkowanego w **przyborni informacji**.
- Po zaznaczeniu dowolnego z takich węzłów możesz odwiedzać wszystkie jego połączenia, jeden po drugiej, naciskając `Shift+Left arrow` klawisz . Fokus jest **przesunyny do pola narzędzia informacyjnego** tego połączenia. W dowolnym momencie fokus można przesunąć z powrotem do węzła, naciskając `Shift+Right arrow` ponownie.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Jak mogę nawigować przy użyciu klawiatury w widoku topologii podsieci?

Strona topologii podsieci wirtualnych zawiera dwie główne sekcje:
    
- **Transparent:** transparent w górnej części topologii podsieci wirtualnych zawiera przyciski służące do wybierania filtrów dystrybucji ruchu (na przykład podsieci aktywnych, średnich i bram). Po wybraniu przycisku odpowiedni filtr jest stosowany do topologii. Jeśli na przykład wybierzesz przycisk Aktywne, topologia wyróżnia aktywną podsieć wirtualną we wdrożeniu.
- **Topologia:** pod transparentem sekcja topologii pokazuje dystrybucję ruchu między podsieciami wirtualnymi.
    
### <a name="keyboard-navigation-on-the-banner"></a>Nawigacja za pomocą klawiatury na banerze
    
- Domyślnie na stronie topologii podsieci wirtualnych dla transparentu jest wybór filtru "Podsieci".
- Aby przejść do innego filtru, użyj `Tab` klawisza , aby przejść do przodu. Aby cofnąć się, użyj `Shift+Tab` klawisza . Nawigacja do przodu jest od lewej do prawej, a następnie od góry do dołu.
- Naciśnij `Enter` klawisz , aby zastosować wybrany filtr. W oparciu o wybór i wdrożenie filtru wyróżniony jest jeden lub wiele węzłów (podsieć) w sekcji topologii.
- Aby przełączyć się między transparentem a topologią, naciśnij klawisz `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-topology"></a>Nawigacja za pomocą klawiatury w topologii
    
- Po wybraniu dowolnego filtru na banerze i naciśnięciu klawisza fokus zostanie przeniesiony do jednego z wyróżnionych węzłów `Ctrl+F6` (podsieć) w widoku topologii.
- Aby przejść do innych wyróżnienia węzłów w widoku topologii, użyj `Shift+Right arrow` klucza do przenoszenia do przodu. 
- W wyróżnione węzły fokus jest **przenoszony** do okna narzędzi informacji dla węzła. Domyślnie fokus jest zmieniany na przycisk **Więcej szczegółów** na polu **narzędzi informacji.** Aby przejść dalej wewnątrz widoku **Box,** użyj klawiszy i , aby przejść odpowiednio do przodu `Right arrow` i do `Left arrow` tyłu. Naciśnięcie `Enter` klawisza ma taki sam efekt jak wybranie przycisku ukierunkowanego w **przyborni informacji**.
- Po zaznaczeniu dowolnego z takich węzłów możesz odwiedzać wszystkie jego połączenia, jeden po jednym, naciskając `Shift+Left arrow` klawisz. Fokus jest **przesunyny do pola narzędzia informacyjnego** tego połączenia. W dowolnym momencie fokus można przesunąć z powrotem do węzła, naciskając `Shift+Right arrow` ponownie.

## <a name="are-classic-nsgs-supported"></a>Czy klasyczne sieciowe sieciowe sieci są obsługiwane?
Nie, Analiza ruchu nie obsługuje klasycznej sieciowej sieciowej sieciowej sieci. Zaleca się migrację zasobów IaaS z wersji klasycznej do wersji Azure Resource Manager ponieważ zasoby klasyczne będą [przestarzałe.](../virtual-machines/classic-vm-deprecation.md) Zapoznaj się z tym artykułem, aby [dowiedzieć się, jak przeprowadzić migrację](../virtual-machines/migration-classic-resource-manager-overview.md).