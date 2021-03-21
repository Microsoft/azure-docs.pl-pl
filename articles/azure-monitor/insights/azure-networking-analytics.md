---
title: Rozwiązanie do analizy sieci platformy Azure w Azure Monitor | Microsoft Docs
description: Korzystając z rozwiązania Azure Networking Analytics w Azure Monitor, można przejrzeć dzienniki sieciowych grup zabezpieczeń platformy Azure i dzienniki usługi Azure Application Gateway.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2018
ms.openlocfilehash: b8b03378e82810bc2b9680805bacf8360f322a94
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708139"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Rozwiązania do monitorowania sieci platformy Azure w Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor oferuje następujące rozwiązania do monitorowania sieci:
* Network Performance Monitor (NPM) na
    * Monitorowanie kondycji sieci
* Analiza usługi Azure Application Gateway do przeglądu
    * Dzienniki usługi Azure Application Gateway
    * Metryki usługi Azure Application Gateway
* Rozwiązania do monitorowania i inspekcji aktywności sieciowej w sieci w chmurze
    * [Analiza ruchu](../../networking/network-monitoring-overview.md#traffic-analytics) 
    * Azure Network Security Group Analytics

## <a name="network-performance-monitor-npm"></a>Network Performance Monitor (NPM)

Rozwiązanie do zarządzania [Network Performance Monitor](../../networking/network-monitoring-overview.md) to rozwiązanie do monitorowania sieci, które monitoruje kondycję, dostępność i osiągalność sieci.  Służy do monitorowania łączności między:

* Chmura publiczna i lokalna
* Centra danych i lokalizacje użytkowników (biura oddziałów)
* Podsieci obsługujące różne warstwy wielowarstwowej aplikacji.

Aby uzyskać więcej informacji, zobacz [Network Performance Monitor](../../networking/network-monitoring-overview.md).

## <a name="network-security-group-analytics"></a>Analiza sieciowych grup zabezpieczeń

1. Dodaj rozwiązanie do zarządzania do Azure Monitor i
2. Włącz diagnostykę, aby skierować diagnostykę do obszaru roboczego Log Analytics w Azure Monitor. Nie jest konieczne zapisywanie dzienników w usłudze Azure Blob Storage.

Jeśli dzienniki diagnostyczne nie są włączone, bloki pulpitu nawigacyjnego dla tego zasobu są puste i wyświetlają komunikat o błędzie.

## <a name="azure-application-gateway-analytics"></a>Analiza Application Gateway platformy Azure

1. Włącz diagnostykę, aby skierować diagnostykę do obszaru roboczego Log Analytics w Azure Monitor.
2. Skorzystaj z szczegółowego podsumowania dla zasobu przy użyciu szablonu skoroszytu dla Application Gateway.

Jeśli dzienniki diagnostyczne nie są włączone dla Application Gateway, w skoroszycie zostaną wypełnione tylko domyślne dane metryki.


> [!NOTE]
> W styczniu 2017 obsługiwane sposoby wysyłania dzienników z bram aplikacji i sieciowych grup zabezpieczeń do obszaru roboczego Log Analytics zmienione. Jeśli widzisz rozwiązanie **Azure Networking Analytics (przestarzałe)** , zapoznaj się z tematem [Migrowanie ze starego rozwiązania do analizy sieci](#migrating-from-the-old-networking-analytics-solution) , aby poznać kroki, które należy wykonać.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Przejrzyj szczegóły zbierania danych w sieci platformy Azure
Rozwiązania Azure Application Gateway Analytics i Network Security Group Analytics są zbierane dzienników diagnostycznych bezpośrednio z usługi Azure Application Gateway i sieciowych grup zabezpieczeń. Nie jest konieczne zapisywanie dzienników w usłudze Azure Blob Storage, a agent nie jest wymagany do zbierania danych.

W poniższej tabeli przedstawiono metody zbierania danych oraz inne szczegóły dotyczące sposobu zbierania danych dla usługi Azure Application Gateway Analytics i analizy sieciowych grup zabezpieczeń.

| Platforma | Agent bezpośredni | Agent programu System Center Operations Manager | Azure | Operations Manager jest wymagane? | Dane agenta Operations Manager wysyłane przez grupę zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |zarejestrowane |


### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Włączanie diagnostyki usługi Azure Application Gateway w portalu

1. W Azure Portal przejdź do zasobu Application Gateway do monitorowania.
2. Wybierz pozycję *Ustawienia diagnostyczne* , aby otworzyć następującą stronę.

   ![Zrzut ekranu przedstawiający konfigurację ustawień diagnostycznych dla zasobu Application Gateway.](media/azure-networking-analytics/diagnostic-settings-1.png)

   [![Zrzut ekranu przedstawiający stronę konfigurowania ustawień diagnostycznych.](media/azure-networking-analytics/diagnostic-settings-2.png)](media/azure-networking-analytics/application-gateway-diagnostics-2.png#lightbox)

5. Kliknij pole wyboru *Wyślij do log Analytics*.
6. Wybierz istniejący obszar roboczy Log Analytics lub Utwórz obszar roboczy.
7. Kliknij pole wyboru w obszarze **Dziennik** dla każdego typu dziennika do zebrania.
8. Kliknij przycisk *Zapisz* , aby włączyć rejestrowanie diagnostyki do Azure monitor.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Włączanie diagnostyki sieci platformy Azure przy użyciu programu PowerShell

Poniższy skrypt programu PowerShell zawiera przykład sposobu włączania rejestrowania zasobów dla bram aplikacji.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

#### <a name="accessing-azure-application-gateway-analytics-via-azure-monitor-network-insights"></a>Uzyskiwanie dostępu do usługi Azure Application Gateway Analytics za pośrednictwem Azure Monitor szczegółowych informacji o sieci

Dostęp do usługi Application Insights można uzyskać za pośrednictwem karty Insights w ramach zasobu Application Gateway.

![Zrzut ekranu przedstawiający Application Gateway szczegółowych informacji ](media/azure-networking-analytics/azure-appgw-insights.png
)

Na karcie "Wyświetl szczegółowe metryki" zostanie otwarty wstępnie wypełniony skoroszyt podsumowujący dane z Application Gateway.

[![Zrzut ekranu przedstawiający skoroszyt Application Gateway](media/azure-networking-analytics/azure-appgw-workbook.png)](media/azure-networking-analytics/application-gateway-workbook.png#lightbox)

### <a name="new-capabilities-with-azure-monitor-network-insights-workbook"></a>Nowe możliwości z skoroszytem usługi Azure Monitor Network Insights

> [!NOTE]
> Nie ma dodatkowych kosztów skojarzonych z skoroszytem usługi Azure Monitor Insights. W obszarze roboczym Log Analytics będzie nadal naliczana stawka za użycie.

Skoroszyt usługi Network Insights umożliwia korzystanie z najnowszych możliwości Azure Monitor i Log Analytics, takich jak:

* Centralna konsola do monitorowania i rozwiązywania problemów z danymi [metryk](../insights/network-insights-overview.md#resource-health-and-metrics) i dzienników.

* Elastyczna Kanwa do obsługi tworzenia niestandardowych bogatych [wizualizacji](../visualize/workbooks-overview.md#visualizations).

* Możliwość używania i [udostępniania szablonów skoroszytów](../visualize/workbooks-overview.md#workbooks-versus-workbook-templates) z szerszym społecznością.

Aby uzyskać więcej informacji na temat możliwości nowego skoroszytu, sprawdź [skoroszyty — Omówienie](../visualize/workbooks-overview.md)

## <a name="migrating-from-azure-gateway-analytics-solution-to-azure-monitor-workbooks"></a>Migrowanie z rozwiązania Azure Gateway Analytics do skoroszytów Azure Monitor

> [!NOTE]
> Azure Monitor skoroszycie usługi Network Insights jest zalecanym rozwiązaniem do uzyskiwania dostępu do metryk i analizy dzienników dla zasobów Application Gateway.

1. Upewnij się, że [Ustawienia diagnostyki](#enable-azure-application-gateway-diagnostics-in-the-portal) umożliwiają przechowywanie dzienników w obszarze roboczym log Analytics. Jeśli jest już skonfigurowany, Azure Monitor skoroszyt usługi Network Insights będzie mógł korzystać z danych z tej samej lokalizacji i nie są wymagane żadne dodatkowe zmiany.

> [!NOTE]
> Wszystkie przeszłe dane są już dostępne w skoroszycie z poziomu ustawień diagnostycznych. Nie jest wymagany transfer danych.

2. Uzyskaj dostęp do [domyślnego skoroszytu usługi Insights](#accessing-azure-application-gateway-analytics-via-azure-monitor-network-insights) dla zasobu Application Gateway. Wszystkie istniejące szczegółowe informacje obsługiwane przez rozwiązanie Application Gateway Analytics będą już obecne w skoroszycie. Można to zwiększyć przez dodanie [wizualizacji](../visualize/workbooks-overview.md#visualizations) niestandardowych opartych na danych dzienników & metryk.

3. Po wyświetleniu wszystkich metryk i szczegółowych informacji dzienników aby wyczyścić rozwiązanie Azure Gateway Analytics z obszaru roboczego, możesz usunąć rozwiązanie ze strony zasobów rozwiązania.

[![Zrzut ekranu opcji Usuń dla rozwiązania Azure Application Gateway Analytics.](media/azure-networking-analytics/azure-appgw-analytics-delete.png)](media/azure-networking-analytics/application-gateway-analytics-delete.png#lightbox)

## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Rozwiązanie analizy grup zabezpieczeń sieci platformy Azure w Azure Monitor

![Symbol Azure Network Security Group Analytics](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> Rozwiązanie Network Security Group Analytics jest przenoszone do pomocy technicznej społeczności, ponieważ jej funkcjonalność została zastąpiona przez [Analiza ruchu](../../network-watcher/traffic-analytics.md).
> - Rozwiązanie jest teraz dostępne w [szablonach szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) i wkrótce nie będzie już dostępne w witrynie Azure Marketplace.
> - W przypadku istniejących klientów, którzy już dodaliśmy rozwiązanie do swojego obszaru roboczego, będzie ona nadal działać bez zmian.
> - Firma Microsoft będzie nadal obsługiwać wysyłanie dzienników zasobów sieciowej grupy zabezpieczeń do obszaru roboczego przy użyciu ustawień diagnostycznych.

Następujące dzienniki są obsługiwane dla sieciowych grup zabezpieczeń:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Instalowanie i Konfigurowanie rozwiązania
Skorzystaj z poniższych instrukcji, aby zainstalować i skonfigurować rozwiązanie do analizy sieci Azure:

1. Aby włączyć rozwiązanie do analizy grup zabezpieczeń sieci platformy Azure, Użyj procesu opisanego w temacie [Dodawanie rozwiązań Azure monitor z Galeria rozwiązań](./solutions.md).
2. Włącz rejestrowanie diagnostyczne dla zasobów [sieciowej grupy zabezpieczeń](../../virtual-network/virtual-network-nsg-manage-log.md) , które chcesz monitorować.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Włączanie diagnostyki grup zabezpieczeń sieci platformy Azure w portalu

1. W Azure Portal przejdź do zasobu sieciowej grupy zabezpieczeń do monitorowania
2. Wybierz pozycję *dzienniki diagnostyczne* , aby otworzyć następującą stronę

   ![Zrzut ekranu przedstawiający stronę dzienników diagnostycznych zasobu sieciowej grupy zabezpieczeń, w którym jest wyświetlana opcja włączenia diagnostyki.](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. Kliknij pozycję *Włącz diagnostykę* , aby otworzyć następującą stronę

   ![Zrzut ekranu przedstawiający stronę konfigurowania ustawień diagnostycznych. Stan jest ustawiony na włączone, wybrano opcję Wyślij do Log Analytics, a wybrano dwa typy dzienników.](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Aby włączyć diagnostykę *, kliknij pozycję w obszarze* *stan*
5. Kliknij pole wyboru *Wyślij do log Analytics*
6. Wybierz istniejący obszar roboczy Log Analytics lub Utwórz obszar roboczy
7. Kliknij pole wyboru w obszarze **Dziennik** dla każdego typu dziennika do zebrania
8. Kliknij przycisk *Zapisz* , aby włączyć rejestrowanie diagnostyki do log Analytics

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Włączanie diagnostyki sieci platformy Azure przy użyciu programu PowerShell

Poniższy skrypt programu PowerShell zawiera przykład sposobu włączania rejestrowania zasobów dla sieciowych grup zabezpieczeń
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Korzystanie z usługi Azure Network Security Group Analytics
Po kliknięciu kafelka **Analiza grup zabezpieczeń sieci platformy Azure** na stronie Przegląd można wyświetlić podsumowania dzienników, a następnie przejść do szczegółów dla następujących kategorii:

* Zablokowane przepływy sieciowej grupy zabezpieczeń
  * Reguły sieciowej grupy zabezpieczeń z zablokowanymi przepływami
  * Adresy MAC z zablokowanymi przepływami
* Dozwolone przepływy sieciowej grupy zabezpieczeń
  * Reguły sieciowej grupy zabezpieczeń z dozwolonymi przepływami
  * Adresy MAC z dozwolonymi przepływami

![Zrzut ekranu kafelków z danymi dla zablokowanych przepływów sieciowych grup zabezpieczeń, w tym reguł z zablokowanymi przepływami i adresami MAC z zablokowanymi przepływami.](media/azure-networking-analytics/log-analytics-nsg01.png)

![Zrzut ekranu kafelków z danymi na potrzeby przepływów sieciowych grup zabezpieczeń, w tym reguł z dozwolonymi przepływami i adresami MAC z dozwolonymi przepływami.](media/azure-networking-analytics/log-analytics-nsg02.png)

Na pulpicie nawigacyjnym **analizy grup zabezpieczeń sieci Azure** Przejrzyj informacje podsumowujące w jednej z bloków, a następnie kliknij jeden, aby wyświetlić szczegółowe informacje na stronie przeszukiwanie dzienników.

Na dowolnej stronie przeszukiwania dzienników można wyświetlać wyniki według czasu, szczegółowych wyników i historii przeszukiwania dzienników. Możesz również filtrować według aspektów, aby zawęzić wyniki.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrowanie ze starego rozwiązania do analizy sieci
W styczniu 2017 obsługiwane sposoby wysyłania dzienników z usługi Azure Application Gateway i sieciowych grup zabezpieczeń platformy Azure do obszaru roboczego Log Analytics zmienione. Te zmiany zapewniają następujące korzyści:
+ Dzienniki są zapisywane bezpośrednio do Azure Monitor bez konieczności korzystania z konta magazynu
+ Mniejsze opóźnienie od momentu, w którym dzienniki są generowane w celu udostępnienia w Azure Monitor
+ Mniej czynności konfiguracyjnych
+ Typowy format dla wszystkich typów diagnostyki platformy Azure

Aby korzystać z zaktualizowanych rozwiązań:

1. [Skonfiguruj diagnostykę do wysłania bezpośrednio do Azure Monitor z usługi Azure Application Gateways](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Skonfiguruj diagnostykę do wysłania bezpośrednio do Azure Monitor z sieciowych grup zabezpieczeń platformy Azure](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Włącz *Azure Application Gateway Analytics* i rozwiązanie *Azure Network Security Group Analytics* przy użyciu procesu opisanego w temacie [Dodawanie rozwiązań Azure monitor z Galeria rozwiązań](solutions.md)
3. Zaktualizuj wszystkie zapisane zapytania, pulpity nawigacyjne lub alerty, aby użyć nowego typu danych
   + Typ to AzureDiagnostics. Do filtrowania dzienników sieciowych platformy Azure można użyć elementu ResourceType.

     | Zamiast: | Używanych |
     | --- | --- |
     | NetworkApplicationgateways &#124; gdzie OperationName = = "ApplicationGatewayAccess" | AzureDiagnostics &#124;, gdzie ResourceType = = "APPLICATIONGATEWAYS" i OperationName = = "ApplicationGatewayAccess" |
     | NetworkApplicationgateways &#124; gdzie OperationName = = "ApplicationGatewayPerformance" | AzureDiagnostics &#124;, gdzie ResourceType = = "APPLICATIONGATEWAYS" i OperationName = = "ApplicationGatewayPerformance" |
     | NetworkSecuritygroups | AzureDiagnostics &#124;, gdzie ResourceType = = "NETWORKSECURITYGROUPS" |

   + Dla każdego pola, które ma sufiks \_ s, \_ d lub \_ g w nazwie, Zmień pierwszy znak na małe litery
   + Dla każdego pola, które ma sufiks \_ o wartości w nazwie, dane są dzielone na poszczególne pola na podstawie zagnieżdżonych nazw pól.
4. Usuń rozwiązanie *Azure Networking Analytics (przestarzałe)* .
   + Jeśli używasz programu PowerShell, użyj polecenia `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Dane zbierane przed zmianą nie będą widoczne w nowym rozwiązaniu. Można kontynuować zapytania o te dane przy użyciu starych typów i nazw pól.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Następne kroki
* Użyj [zapytań dzienników w Azure monitor](../logs/log-query-overview.md) , aby wyświetlić szczegółowe dane diagnostyczne platformy Azure.

