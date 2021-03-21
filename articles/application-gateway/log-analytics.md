---
title: Sprawdzanie dzienników WAF przy użyciu usługi Azure Log Analytics
titleSuffix: Azure Application Gateway
description: W tym artykule pokazano, jak za pomocą usługi Azure Log Analytics przeanalizować dzienniki Application Gateway zapory aplikacji sieci Web (WAF).
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 6c7acdda3dce3b1338888cdf36f1623597c11fc3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586073"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-waf-logs"></a>Używanie Log Analytics do badania dzienników Application Gateway zapory aplikacji sieci Web (WAF)

Po uruchomieniu Application Gateway WAF można włączyć dzienniki, aby sprawdzić, co dzieje się z każdym żądaniem. Dzienniki zapory dają wgląd w to, co WAF ocenia, dopasowuje i blokuje. Za pomocą Log Analytics można przeanalizować dane znajdujące się w dziennikach zapory, aby dać jeszcze więcej szczegółowych informacji. Aby uzyskać więcej informacji na temat tworzenia obszaru roboczego Log Analytics, zobacz [tworzenie log Analytics obszaru roboczego w Azure Portal](../azure-monitor/logs/quick-create-workspace.md). Aby uzyskać więcej informacji o zapytaniach dziennika, zobacz [Omówienie zapytań dzienników w Azure monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="import-waf-logs"></a>Importowanie dzienników WAF

Aby zaimportować Dzienniki zapory do Log Analytics, zobacz temat [kondycja zaplecza, dzienniki diagnostyczne i metryki dla Application Gateway](application-gateway-diagnostics.md#diagnostic-logging). Gdy masz Dzienniki zapory w obszarze roboczym Log Analytics, możesz wyświetlać dane, zapisywać zapytania, tworzyć wizualizacje i dodawać je do pulpitu nawigacyjnego portalu.

## <a name="explore-data-with-examples"></a>Eksplorowanie danych za pomocą przykładów

Aby wyświetlić dane pierwotne w dzienniku zapory, można uruchomić następujące zapytanie:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Będzie to wyglądać podobnie do poniższego zapytania:

![Zapytanie Log Analytics](media/log-analytics/log-query.png)

Możesz przejść do szczegółów danych i wykreolić wykresy lub utworzyć wizualizacje w tym miejscu. Zobacz następujące zapytania jako punkt początkowy:

### <a name="matchedblocked-requests-by-ip"></a>Dopasowane/zablokowane żądania według adresu IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Dopasowane/zablokowane żądania według identyfikatora URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Najważniejsze dopasowane reguły

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Pięć najważniejszych pasujących grup reguł

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Dodawanie do pulpitu nawigacyjnego

Po utworzeniu zapytania można je dodać do pulpitu nawigacyjnego.  Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** w prawym górnym rogu obszaru roboczego usługi log Analytics. Dzięki powyższym czteremu zapytaniom przypiętym do przykładowego pulpitu nawigacyjnego są to dane, które można wyświetlić w skrócie:

![Zrzut ekranu przedstawia pulpit nawigacyjny platformy Azure, w którym można dodać zapytanie.](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Następne kroki

[Kondycja zaplecza, dzienniki diagnostyczne i metryki dla usługi Application Gateway](application-gateway-diagnostics.md)
