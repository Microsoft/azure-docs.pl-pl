---
title: Monitorowanie i rejestrowanie zapory aplikacji sieci Web platformy Azure
description: Informacje o zaporze aplikacji sieci Web (WAF) z usługa frontdoor monitorowaniem i rejestrowaniem
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 596374d4f3f188e08a10bd25b36b178cc79a6e57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84808951"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitorowanie i rejestrowanie zapory aplikacji sieci Web platformy Azure

Monitorowanie i rejestrowanie w usłudze Azure Web Application Firewall (WAF) odbywa się za pomocą rejestrowania i integracji z dziennikami Azure Monitor i Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

WAF z dziennikiem usługa frontdoor jest zintegrowany z [Azure monitor](../../azure-monitor/overview.md). Azure Monitor umożliwia śledzenie informacji diagnostycznych, w tym alertów i dzienników WAF. Monitorowanie WAF można skonfigurować w ramach zasobu front-drzwi w portalu na karcie **Diagnostyka** lub bezpośrednio za pomocą usługi Azure monitor.

W obszarze Azure Portal przejdź do pozycji Typ zasobu front-drzwi.  / Na karcie **metryki** monitorowania po lewej stronie można dodać **WebApplicationFirewallRequestCount** do śledzenia liczby żądań zgodnych z regułami WAF. Filtry niestandardowe można tworzyć w oparciu o typy akcji i nazwy reguł.

:::image type="content" source="../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png" alt-text="WAFMetrics ":::

## <a name="logs-and-diagnostics"></a>Dzienniki i Diagnostyka

WAF z przednimi drzwiami udostępnia szczegółowe raporty dotyczące każdego wykrytego zagrożenia. Rejestrowanie jest zintegrowane z dziennikami diagnostycznymi platformy Azure, a alerty są zapisywane w formacie json. Te dzienniki można zintegrować z [dziennikami Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

[FrontdoorAccessLog](../../frontdoor/front-door-diagnostics.md) rejestruje wszystkie żądania. FrontdoorWebApplicationFirewallLog rejestruje wszystkie żądania zgodne z regułą WAF o poniższym schemacie:

| Właściwość  | Opis |
| ------------- | ------------- |
|Akcja|Akcja podjęta na żądanie|
| ClientIp | Adres IP klienta, który wykonał żądanie. Jeśli w żądaniu było nagłówka X-Forwarded-For, adres IP klienta jest wybierany z pola nagłówka. |
| ClientPort | Port IP klienta, który wykonał żądanie. |
| Szczegóły|Dodatkowe szczegóły dotyczące dopasowanego żądania |
|| matchVariableName: Nazwa parametru http pasującego do żądania, na przykład nazwy nagłówków|
|| matchVariableValue: wartości, które wywołały dopasowanie|
| Host | Nagłówek hosta dopasowanego żądania |
| Zasady | Nazwa zasad WAF, które pasują do żądania. |
| Policymode | Tryb operacji zasad WAFymi. Możliwe wartości to "Zapobieganie" i "wykrywanie" |
| RequestUri | Pełny identyfikator URI dopasowanego żądania. |
| RuleName | Nazwa reguły WAF, która pasuje do żądania. |
| SocketIp | Źródłowy adres IP widziany przez WAF. Ten adres IP jest oparty na sesji protokołu TCP niezależnie od nagłówka żądania.|
| TrackingReference | Unikatowy ciąg odwołania, który identyfikuje żądanie obsługiwane przez tylne drzwi, również wysyłany jako nagłówek X-Azure-ref do klienta. Wymagane do wyszukiwania szczegółowych informacji w dziennikach dostępu dla określonego żądania. |

Poniższe przykładowe zapytanie zwraca WAF dzienników dla zablokowanych żądań:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Oto przykład zarejestrowanego żądania w dzienniku WAF:

``` WAFlogQuerySample
{
    "time":  "2020-06-09T22:32:17.8376810Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoorWebApplicationFirewallLog/Write",
    "properties":
    {
        "clientIP":"xxx.xxx.xxx.xxx",
        "clientPort":"52097",
        "socketIP":"xxx.xxx.xxx.xxx",
        "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
        "ruleName":"Microsoft_DefaultRuleSet-1.1-SQLI-942100",
        "policy":"WafDemoCustomPolicy",
        "action":"Block",
        "host":"wafdemofrontdoorwebapp.azurefd.net",
        "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode":"prevention",
        "details":
            {
            "matches":
                [{
                "matchVariableName":"QueryParamValue:q",
                "matchVariableValue":"' or 1=1"
                }]
            }
     }
}
```

Poniższe przykładowe zapytanie zwraca wpisy AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Oto przykład zarejestrowanego żądania w dzienniku dostępu:

``` AccessLogSample
{
"time": "2020-06-09T22:32:17.8383427Z",
"category": "FrontdoorAccessLog",
"operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
 "properties":
    {
    "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
    "httpMethod":"GET",
    "httpVersion":"2.0",
    "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
    "requestBytes":"715",
    "responseBytes":"380",
    "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4157.0 Safari/537.36 Edg/85.0.531.1",
    "clientIp":"xxx.xxx.xxx.xxx",
    "socketIp":"xxx.xxx.xxx.xxx",
    "clientPort":"52097",
    "timeTaken":"0.003",
    "securityProtocol":"TLS 1.2",
    "routingRuleName":"WAFdemoWebAppRouting",
    "rulesEngineMatchNames":[],
    "backendHostname":"wafdemowebappuscentral.azurewebsites.net:443",
    "sentToOriginShield":false,
    "httpStatusCode":"403",
    "httpStatusDetails":"403",
    "pop":"SJC",
    "cacheStatus":"CONFIG_NOCACHE"
    }
}

```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [drzwiach zewnętrznych](../../frontdoor/front-door-overview.md).
