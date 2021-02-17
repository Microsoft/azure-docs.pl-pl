---
title: Analiza ruchu na platformie Azure — często zadawane pytania | Microsoft Docs
description: Uzyskaj odpowiedzi na kilka często zadawanych pytań dotyczących analizy ruchu.
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
ms.openlocfilehash: f4d97b81bf8f11b8380f04dcbfdb72bd658805ab
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579238"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Analiza ruchu często zadawane pytania

W tym artykule zebrano wiele często zadawanych pytań dotyczących analizy ruchu na platformie Azure Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Jakie są wymagania wstępne dotyczące korzystania z analizy ruchu?

Analiza ruchu wymaga następujących wstępnie wymaganych elementów:

- Network Watcher z włączoną subskrypcją.
- Włączono obsługę dzienników przepływu sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) dla sieciowych grup zabezpieczeń, które mają być monitorowane.
- Konto usługi Azure Storage do przechowywania nieprzetworzonych dzienników przepływów.
- Obszar roboczy Log Analytics platformy Azure z dostępem do odczytu i zapisu.

Aby możliwe było włączenie analizy ruchu, Twoje konto musi spełniać następujące warunki:

- Twoje konto musi mieć jedną z następujących ról platformy Azure w zakresie subskrypcji: właściciel, współautor, czytelnik lub współautor sieci.
- Jeśli Twoje konto nie jest przypisane do jednej z wcześniej wymienionych ról, musi być przypisane do roli niestandardowej, do której przypisano następujące akcje na poziomie subskrypcji.
            
    - Microsoft. Network/applicationGateways/odczyt
    - Microsoft. Network/Connections/Read
    - Microsoft. Network/loadBalancers/odczyt 
    - Microsoft. Network/localNetworkGateways/odczyt 
    - Microsoft. Network/networkInterfaces/odczyt 
    - Microsoft. Network/networkSecurityGroups/odczyt 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft. Network/routeTables/odczyt
    - Microsoft. Network/virtualNetworkGateways/odczyt 
    - Microsoft. Network/virtualNetworks/odczyt
        
Aby sprawdzić role przypisane do użytkownika w ramach subskrypcji:

1. Zaloguj się do platformy Azure przy użyciu polecenia **login-AzAccount**. 

2. Wybierz wymaganą subskrypcję za pomocą polecenia **SELECT-AzSubscription**. 

3. Aby wyświetlić listę wszystkich ról przypisanych do określonego użytkownika, użyj polecenie  **Get-AzRoleAssignment-SignInName [User email]-IncludeClassicAdministrators**. 

Jeśli nie widzisz żadnych danych wyjściowych, skontaktuj się z administratorem subskrypcji, aby uzyskać dostęp do uruchamiania poleceń. Aby uzyskać więcej informacji, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>W jakich regionach świadczenia usługi Azure Analiza ruchu dostępne?

Analizy ruchu można użyć dla sieciowych grup zabezpieczeń w jednym z następujących obsługiwanych regionów:
- Kanada Środkowa
- Zachodnio-środkowe stany USA
- East US
- Wschodnie stany USA 2
- Północno-środkowe stany USA
- South Central US
- Central US
- Zachodnie stany USA
- Zachodnie stany USA 2
- Francja Środkowa
- West Europe
- Europa Północna
- Brazylia Południowa
- Zachodnie Zjednoczone Królestwo
- Południowe Zjednoczone Królestwo
- Australia Wschodnia
- Australia Południowo-Wschodnia 
- Azja Wschodnia
- Southeast Asia
- Korea Środkowa
- Indie Środkowe
- Indie Południowe
- Japonia Wschodnia
- Japonia Zachodnia
- US Gov Wirginia
- Chiny Wschodnie 2

Obszar roboczy Log Analytics musi istnieć w następujących regionach:
- Kanada Środkowa
- Zachodnio-środkowe stany USA
- East US
- Wschodnie stany USA 2
- Północno-środkowe stany USA
- South Central US
- Central US
- Zachodnie stany USA
- Zachodnie stany USA 2
- Francja Środkowa
- West Europe
- Europa Północna
- Zachodnie Zjednoczone Królestwo
- Południowe Zjednoczone Królestwo
- Australia Wschodnia
- Australia Południowo-Wschodnia
- Azja Wschodnia
- Southeast Asia 
- Korea Środkowa
- Indie Środkowe
- Japonia Wschodnia
- US Gov Wirginia
- Chiny Wschodnie 2

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Czy sieciowych grup zabezpieczeń mogę włączyć dzienniki przepływów w różnych regionach niż mój obszar roboczy?

Tak, te sieciowych grup zabezpieczeń mogą znajdować się w różnych regionach niż obszar roboczy Log Analytics.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Czy można skonfigurować wiele sieciowych grup zabezpieczeń w ramach jednego obszaru roboczego?

Tak.

## <a name="can-i-use-an-existing-workspace"></a>Czy mogę użyć istniejącego obszaru roboczego?

Tak. W przypadku wybrania istniejącego obszaru roboczego upewnij się, że został on zmigrowany do nowego języka zapytań. Jeśli nie chcesz uaktualnić obszaru roboczego, musisz utworzyć nowy. Aby uzyskać więcej informacji o nowym języku zapytań, zobacz artykuł [Azure Monitoring Logs Upgrade to New Search log](../azure-monitor/logs/log-query-overview.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Czy moje konto usługi Azure Storage znajduje się w jednej subskrypcji, a mój obszar roboczy Log Analytics należy do innej subskrypcji?

Tak, Twoje konto usługi Azure Storage może znajdować się w jednej subskrypcji, a obszar roboczy Log Analytics może znajdować się w innej subskrypcji.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Czy mogę przechowywać dzienniki RAW w innej subskrypcji?

Tak. Dzienniki przepływu sieciowej grupy zabezpieczeń można skonfigurować do wysyłania do konta magazynu znajdującego się w innej subskrypcji, pod warunkiem że masz odpowiednie uprawnienia i że konto magazynu znajduje się w tym samym regionie co sieciowej grupy zabezpieczeń. SIECIOWEJ grupy zabezpieczeń i docelowe konto magazynu muszą również współużytkować tę samą dzierżawę Azure Active Directory.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Co zrobić, jeśli nie można skonfigurować sieciowej grupy zabezpieczeń dla analizy ruchu z powodu błędu "nie znaleziono"?

Wybierz obsługiwany region. W przypadku wybrania nieobsługiwanego regionu zostanie wyświetlony komunikat o błędzie "nie znaleziono". Obsługiwane regiony są wymienione wcześniej w tym artykule.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Co w przypadku otrzymania stanu "nie można załadować," na stronie dzienników przepływów sieciowej grupy zabezpieczeń?

Aby rejestrowanie przepływu działało prawidłowo, dostawca Microsoft. Insights musi być zarejestrowany. Jeśli nie masz pewności, czy dostawca Microsoft. Insights został zarejestrowany dla Twojej subskrypcji, Zastąp *ciąg XXXXX-XXXXX-XXXXXX-xxxx* w poniższym poleceniu i uruchom następujące polecenia w programie PowerShell:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Skonfigurowano rozwiązanie. Dlaczego nie widzę żadnych elementów na pulpicie nawigacyjnym?

Po raz pierwszy pulpit nawigacyjny może potrwać do 30 minut. Rozwiązanie musi najpierw agregować wystarczającą ilość danych, aby uzyskać istotny wgląd w dane. Spowoduje to wygenerowanie raportów. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Co zrobić, jeśli otrzymam następujący komunikat: "nie można odnaleźć żadnych danych w tym obszarze roboczym dla wybranego interwału czasu. Spróbuj zmienić przedział czasu lub wybierz inny obszar roboczy. "?

Wypróbuj następujące opcje:
- Zmień interwał czasu na górnym pasku.
- Wybierz inny obszar roboczy Log Analytics na górnym pasku.
- Spróbuj uzyskać dostęp do analizy ruchu po 30 minutach, jeśli była niedawno włączona.
    
Jeśli problemy będą się powtarzać, zgłoś wątpliwości na forum dotyczącego [głosu użytkownika](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Co zrobić, jeśli otrzymam następujący komunikat: "analizowanie dzienników przepływu usługi sieciowej grupy zabezpieczeń po raz pierwszy. Ten proces może potrwać 20-30 minut. Sprawdź ponownie za jakiś czas. 2) Jeśli powyższy krok nie działa, a obszar roboczy jest objęty bezpłatną jednostką SKU, Sprawdź użycie obszaru roboczego tutaj, aby sprawdzić poprawność przydziału. w przeciwnym razie zapoznaj się z często zadawanymi pytaniami "?

Ten komunikat może zostać wyświetlony, ponieważ:
- Analiza ruchu był niedawno włączony i może nie mieć jeszcze zagregowanej ilości danych, aby uzyskać istotny wgląd w dane.
- Korzystasz z bezpłatnej wersji obszaru roboczego Log Analytics i przekroczono limity przydziałów. Może być konieczne użycie obszaru roboczego o większej pojemności.
    
Jeśli problemy będą się powtarzać, zgłoś wątpliwości na forum dotyczącego [głosu użytkownika](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Co zrobić, jeśli otrzymam następujący komunikat: "Wygląda na to, że mamy dane zasobów (topologia) i nie ma informacji o przepływie. W tym celu kliknij tutaj, aby wyświetlić dane zasobów i zapoznaj się z często zadawanymi pytaniami, aby uzyskać więcej informacji "?

Widzisz informacje o zasobach na pulpicie nawigacyjnym; nie ma jednak żadnych statystyk związanych z przepływem. Dane mogą nie być obecne ze względu na brak przepływów komunikacji między zasobami. Poczekaj 60 minut i ponownie sprawdź stan. Jeśli problem będzie się powtarzał i masz pewność, że przepływy komunikacji między zasobami istnieją, zgłoś problemy na [forum dotyczącego głosu użytkownika](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Czy można skonfigurować analizę ruchu przy użyciu programu PowerShell lub szablonu Azure Resource Manager lub klienta?

Analiza ruchu można skonfigurować przy użyciu programu Windows PowerShell z wersji 6.2.1 lub nowszej. Aby skonfigurować rejestrowanie przepływu i analizę ruchu dla określonego sieciowej grupy zabezpieczeń przy użyciu polecenia cmdlet Set, zobacz [Set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog). Aby uzyskać informacje o rejestrowaniu przepływu i stanie analizy ruchu dla określonego sieciowej grupy zabezpieczeń, zobacz [Get-AzNetworkWatcherFlowLogStatus](/powershell/module/az.network/get-aznetworkwatcherflowlogstatus).

Obecnie nie można użyć szablonu Azure Resource Manager w celu skonfigurowania analizy ruchu.

Aby skonfigurować analizę ruchu przy użyciu klienta Azure Resource Manager, zobacz następujące przykłady.

**Ustaw przykład polecenia cmdlet:**
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
**Pobierz przykład poleceń cmdlet:**
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


## <a name="how-is-traffic-analytics-priced"></a>W jaki sposób Analiza ruchu cena?

Analiza ruchu jest naliczana. Pomiary opierają się na przetwarzaniu danych dzienników przepływów przez usługę i przechowywaniu powstałych ulepszonych dzienników w obszarze roboczym Log Analytics. 

Na przykład zgodnie z [planem cenowym](https://azure.microsoft.com/pricing/details/network-watcher/), uwzględniając Region Zachodni środkowe stany USA, jeśli dane dzienników przepływów przechowywane na koncie magazynu przetworzonym przez Analiza ruchu to 10 GB, a ulepszone dzienniki pozyskane w log Analytics obszarze roboczym to 1 GB, odpowiednie opłaty są następujące: 10 x $2,3 + 1 x $2,76 = $25,76

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Jak często Analiza ruchu są przetwarzane dane?

Zapoznaj się z [sekcją agregacja danych](./traffic-analytics-schema.md#data-aggregation) w artykule Analiza ruchu schematu i dokumentu agregacji danych

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Jak Analiza ruchu decyduje o tym, że adres IP jest złośliwy? 

Analiza ruchu opiera się na wewnętrznych systemach analizy zagrożeń firmy Microsoft, aby uznać za złośliwe adresy IP. Te systemy używają różnorodnych źródeł danych telemetrycznych, takich jak produkty i usługi firmy Microsoft, Microsoft Digital Zbrodnis (DCU), Microsoft Security Response Center (MSRC) oraz zewnętrznych źródeł danych i tworzenia dużej ilości informacji na ten temat. Niektóre z tych danych są wewnętrzne firmy Microsoft. Jeśli znany adres IP jest wyświetlany jako złośliwy, zgłoś bilet pomocy technicznej, aby poznać szczegóły.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Jak ustawić alerty dla Analiza ruchu danych?

Analiza ruchu nie ma wbudowaną obsługę alertów. Jednak ponieważ Analiza ruchu dane są przechowywane w Log Analytics można napisać zapytania niestandardowe i ustawić dla nich alerty. Czynnooci
- Można użyć wypchnięty do Log Analytics w Analiza ruchu. 
- Użyj [schematu udokumentowanego w tym miejscu](traffic-analytics-schema.md) , aby napisać zapytania 
- Kliknij pozycję "Nowa reguła alertu", aby utworzyć alert
- Zapoznaj się z [dokumentacją alertów dziennika](../azure-monitor/alerts/alerts-log.md) , aby utworzyć alert

## <a name="how-do-i-check-which-vms-are-receiving-most-on-premises-traffic"></a>Jak mogę sprawdzić, które maszyny wirtualne odbierają większość ruchu lokalnego?

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

  Dla adresów IP:

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

Na czas Użyj formatu: rrrr-mm-dd 00:00:00

## <a name="how-do-i-check-standard-deviation-in-traffic-received-by-my-vms-from-on-premises-machines"></a>Jak mogę sprawdzić odchylenie standardowe w ruchu odbieranym przez maszyny wirtualne z maszyn lokalnych?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
| where isnotempty(vm) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + utboundBytes_d
| summarize deviation = stdev(traffic)  by vm
```

Dla adresów IP:

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
| where isnotempty(IP) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
| summarize deviation = stdev(traffic)  by IP
```

## <a name="how-do-i-check-which-ports-are-reachable-or-blocked-between-ip-pairs-with-nsg-rules"></a>Jak mogę sprawdzić, które porty są osiągalne (lub zablokowane) między parami adresów IP a regułami sieciowej grupy zabezpieczeń?

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

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Jak nawigować przy użyciu klawiatury w widoku mapy geograficznej?

Strona mapy geograficznej zawiera dwie główne sekcje:
    
- **Transparent**: transparent w górnej części mapy geograficznej zawiera przyciski umożliwiające wybranie filtrów dystrybucji ruchu (na przykład wdrożenie, ruch z krajów/regionów i złośliwe). Po wybraniu przycisku odpowiedni filtr jest stosowany na mapie. Jeśli na przykład wybierzesz aktywny przycisk, Mapa wyróżni aktywne centra danych we wdrożeniu.
- **Mapa**: poniżej transparentu sekcja mapy przedstawia rozkład ruchu między centrami danych platformy Azure a krajami/regionami.
    
### <a name="keyboard-navigation-on-the-banner"></a>Nawigacja przy użyciu klawiatury na banerze
    
- Domyślnie wybór na stronie mapy geograficznej transparentu to filtr "Kontrolery domeny" platformy Azure.
- Aby przejść do innego filtru, użyj `Tab` albo `Right arrow` klucza. Aby przenieść do tyłu, użyj `Shift+Tab` albo `Left arrow` klucza. Nawigacja do przodu jest od lewej do prawej, a następnie od góry do dołu.
- Naciśnij klawisz `Enter` lub `Down` klawisz Strzałka, aby zastosować wybrany filtr. W oparciu o wybór i wdrożenie filtru są wyróżniane co najmniej jeden węzeł w sekcji map.
- Aby przełączać się między transparentem i mapą, naciśnij klawisz `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-map"></a>Nawigacja przy użyciu klawiatury na mapie
    
- Po wybraniu dowolnego filtru na transparencie i naciśnięciu `Ctrl+F6` fokus zostanie przeniesiony do jednego z wyróżnionych węzłów (**centrum danych platformy Azure** lub **kraju/regionu**) w widoku mapy.
- Aby przejść do innych wyróżnionych węzłów na mapie, użyj albo `Tab` `Right arrow` klucza do przesunięcia do przodu. Użyj `Shift+Tab` lub `Left arrow` klawisza do przesuwania wstecznego.
- Aby zaznaczyć dowolny wyróżniony węzeł w mapie, użyj `Enter` `Down arrow` klawisza or.
- Po wybraniu takich węzłów fokus jest przenoszony do **pola narzędzia informacji** dla węzła. Domyślnie fokus jest przenoszony do przycisku zamknięty w **polu narzędzie informacji**. Aby dalej poruszać się wewnątrz widoku **pola** , użyj `Right arrow` klawiszy i, `Left arrow` Aby przenieść odpowiednio do przodu i do tyłu. Naciśnięcie klawisza `Enter` ma ten sam efekt, co wybranie priorytetowego przycisku w **polu narzędzie informacji**.
- Po naciśnięciu klawisza `Tab` , gdy fokus znajduje się w **polu narzędzia informacji**, fokus jest przenoszony do punktów końcowych w tym samym kontynentie co wybrany węzeł. Użyj `Right arrow` klawiszy i, `Left arrow` Aby poruszać się za pomocą tych punktów końcowych.
- Aby przejść do innych punktów końcowych przepływów lub klastrów kontynentu, użyj `Tab` do przenoszenia do przodu i `Shift+Tab` do przenoszenia wstecznego.
- Gdy fokus jest skierowany do **klastrów kontynentu**, użyj `Enter` `Down` klawiszy strzałek lub, aby wyróżnić punkty końcowe w ramach klastra kontynentu. Aby poruszać się za pomocą punktów końcowych i przycisku Zamknij w oknie informacje w klastrze kontynentu, należy odpowiednio użyć albo `Right arrow` `Left arrow` klawisza lub do przenoszenia do przodu i do tyłu. W dowolnym punkcie końcowym można użyć, `Shift+L` Aby przełączyć się do linii połączenia z wybranego węzła do punktu końcowego. Możesz nacisnąć `Shift+L` ponownie, aby przejść do wybranego punktu końcowego.
        
### <a name="keyboard-navigation-at-any-stage"></a>Nawigacja przy użyciu klawiatury na dowolnym etapie
    
- `Esc` zwija rozwinięty wybór.
- `Up-arrow`Klucz wykonuje tę samą akcję co `Esc` . `Down arrow`Klucz wykonuje tę samą akcję co `Enter` .
- Użyj `Shift+Plus` , aby powiększyć i `Shift+Minus` pomniejszyć.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Jak nawigować przy użyciu klawiatury w widoku topologia sieci wirtualnej?

Na stronie topologia sieci wirtualnych znajdują się dwie główne sekcje:
    
- **Transparent**: transparent w górnej części topologii sieci wirtualnych udostępnia przyciski do wybierania filtrów dystrybucji ruchu (na przykład połączonych sieci wirtualnych, odłączonych sieci wirtualnych i publicznych adresów IP). Po wybraniu przycisku odpowiedni filtr jest stosowany w topologii. Jeśli na przykład wybierzesz aktywny przycisk, topologia podświetla aktywne sieci wirtualne w danym wdrożeniu.
- **Topologia**: poniżej transparentu sekcja topologia przedstawia rozkład ruchu między sieciami wirtualnymi.
    
### <a name="keyboard-navigation-on-the-banner"></a>Nawigacja przy użyciu klawiatury na banerze
    
- Domyślnie wybór na stronie topologii sieci wirtualnych dla transparentu to filtr "Connected sieci wirtualnych".
- Aby przejść do innego filtru, użyj `Tab` klawisza, aby przejść do przodu. Aby przejść do tyłu, użyj `Shift+Tab` klucza. Nawigacja do przodu jest od lewej do prawej, a następnie od góry do dołu.
- Naciśnij klawisz, `Enter` Aby zastosować wybrany filtr. W zależności od wyboru filtru i wdrożenia w sekcji topologia zostaną wyróżnione jeden lub wiele węzłów (Sieć wirtualna).
- Aby przełączać się między transparentem a topologią, naciśnij klawisz `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-topology"></a>Nawigacja przy użyciu klawiatury w topologii
    
- Po wybraniu dowolnego filtru na transparencie i naciśnięciu `Ctrl+F6` fokus zostanie przeniesiony do jednego z wyróżnionych węzłów (**VNET**) w widoku topologii.
- Aby przejść do innych wyróżnionych węzłów w widoku topologii, użyj `Shift+Right arrow` klawisza do przesunięcia do przodu. 
- W zaznaczonych węzłach fokus jest przenoszony do **pola narzędzia informacji** dla węzła. Domyślnie fokus jest przenoszony do przycisku **więcej szczegółów** w **polu narzędzie informacji**. Aby dalej poruszać się wewnątrz widoku **pola** , użyj `Right arrow` klawiszy i, `Left arrow` Aby przejść odpowiednio do przodu i do tyłu. Naciśnięcie klawisza `Enter` ma ten sam efekt, co wybranie priorytetowego przycisku w **polu narzędzie informacji**.
- Po wybraniu takich węzłów można odwiedzać wszystkie jego połączenia, po jednym przez naciśnięcie `Shift+Left arrow` klawisza. Fokus jest przenoszony do **pola narzędzia informacji** tego połączenia. W dowolnym momencie fokus można przesunąć z powrotem do węzła, naciskając `Shift+Right arrow` ponownie.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Jak nawigować przy użyciu klawiatury w widoku topologia podsieci?

Na stronie topologia podsieci wirtualnych znajdują się dwie główne sekcje:
    
- **Transparent**: transparent w górnej części topologii podsieci wirtualnych zawiera przyciski umożliwiające wybranie filtrów dystrybucji ruchu (na przykład aktywnych, średnich i podsieci bramy). Po wybraniu przycisku odpowiedni filtr jest stosowany w topologii. Na przykład, jeśli wybierzesz aktywny przycisk, topologia podświetla aktywną podsieć wirtualną we wdrożeniu.
- **Topologia**: poniżej transparentu sekcja topologia przedstawia rozkład ruchu między podsieciami wirtualnymi.
    
### <a name="keyboard-navigation-on-the-banner"></a>Nawigacja przy użyciu klawiatury na banerze
    
- Domyślnie wybór na stronie topologii sieci wirtualnych dla transparentu to filtr "podsieci".
- Aby przejść do innego filtru, użyj `Tab` klawisza, aby przejść do przodu. Aby przejść do tyłu, użyj `Shift+Tab` klucza. Nawigacja do przodu jest od lewej do prawej, a następnie od góry do dołu.
- Naciśnij klawisz, `Enter` Aby zastosować wybrany filtr. W oparciu o wybór i wdrożenie filtru, wyróżniono jeden lub wiele węzłów (podsieć) w sekcji topologia.
- Aby przełączać się między transparentem a topologią, naciśnij klawisz `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-topology"></a>Nawigacja przy użyciu klawiatury w topologii
    
- Po wybraniu dowolnego filtru na transparencie i naciśnięciu `Ctrl+F6` fokus zostanie przeniesiony do jednego z wyróżnionych węzłów (**podsieci**) w widoku topologii.
- Aby przejść do innych wyróżnionych węzłów w widoku topologii, użyj `Shift+Right arrow` klawisza do przesunięcia do przodu. 
- W zaznaczonych węzłach fokus jest przenoszony do **pola narzędzia informacji** dla węzła. Domyślnie fokus jest przenoszony do przycisku **więcej szczegółów** w **polu narzędzie informacji**. Aby dalej poruszać się wewnątrz widoku **pola** , użyj `Right arrow` klawiszy i, `Left arrow` Aby przenieść odpowiednio do przodu i do tyłu. Naciśnięcie klawisza `Enter` ma ten sam efekt, co wybranie priorytetowego przycisku w **polu narzędzie informacji**.
- Po wybraniu takich węzłów można odwiedzać wszystkie jego połączenia, po jednym przez naciśnięcie `Shift+Left arrow` klawisza. Fokus jest przenoszony do **pola narzędzia informacji** tego połączenia. W dowolnym momencie fokus można przesunąć z powrotem do węzła, naciskając `Shift+Right arrow` ponownie.

## <a name="are-classic-nsgs-supported"></a>Czy obsługiwane są klasyczne sieciowych grup zabezpieczeń?
Nie, Analiza ruchu nie obsługuje klasycznej sieciowej grupy zabezpieczeń. Zaleca się migrację zasobów IaaS z klasycznego do Azure Resource Manager, ponieważ zasoby klasyczne będą [przestarzałe](../virtual-machines/classic-vm-deprecation.md). Zapoznaj się z tym artykułem, aby zrozumieć, [Jak przeprowadzić migrację](../virtual-machines/migration-classic-resource-manager-overview.md).