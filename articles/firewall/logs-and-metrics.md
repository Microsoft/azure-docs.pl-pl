---
title: Omówienie dzienników i metryk zapory platformy Azure
description: Usługę Azure Firewall możesz monitorować przy użyciu dzienników zapory. Ponadto dzienniki aktywności umożliwiają inspekcję operacji wykonywanych względem zasobów usługi Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 09/10/2020
ms.author: victorh
ms.openlocfilehash: a0333f9afa69b533ac28dc302987e6d057bfeeb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090163"
---
# <a name="azure-firewall-logs-and-metrics"></a>Dzienniki i metryki usługi Azure Firewall

Usługę Azure Firewall możesz monitorować przy użyciu dzienników zapory. Ponadto dzienniki aktywności umożliwiają inspekcję operacji wykonywanych względem zasobów usługi Azure Firewall.

Niektóre z tych dzienników są dostępne za pośrednictwem portalu. Dzienniki mogą być wysyłane do [Azure monitor dzienników](../azure-monitor/insights/azure-networking-analytics.md), magazynu i Event Hubs i analizowane w dziennikach Azure monitor lub przy użyciu różnych narzędzi, takich jak Excel i Power BI.

Metryki są lekkie i obsługują scenariusze niemal w czasie rzeczywistym, dzięki czemu mogą być używane do tworzenia alertów i szybkiego wykrywania problemów.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

 Dla usługi Azure Firewall są dostępne następujące dzienniki diagnostyczne:

* **Dziennik reguł aplikacji**

   Dziennik reguł aplikacji jest zapisywany na koncie magazynu przesyłanym strumieniowo do centrów zdarzeń i/lub wysyłany do dzienników Azure Monitor tylko wtedy, gdy włączono go dla każdej zapory platformy Azure. W wyniku każdego nowego połączenia, które jest zgodne z jedną ze skonfigurowanych reguł aplikacji, jest tworzony dziennik dla zaakceptowanego/odrzuconego połączenia. Dane są rejestrowane w formacie JSON, jak pokazano w poniższym przykładzie:

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Dziennik reguł sieci**

   Dziennik reguł sieci jest zapisywany na koncie magazynu przesyłanym strumieniowo do centrów zdarzeń i/lub wysyłanych do Azure Monitor dzienników tylko wtedy, gdy włączono go dla każdej zapory platformy Azure. W wyniku każdego nowego połączenia, które jest zgodne z jedną ze skonfigurowanych reguł sieci, jest tworzony dziennik dla zaakceptowanego/odrzuconego połączenia. Dane są rejestrowane w formacie JSON, jak pokazano w poniższym przykładzie:

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

* **Dziennik proxy DNS**

   Dziennik proxy DNS jest zapisywany na koncie magazynu przesyłanym strumieniowo do centrów zdarzeń i/lub wysyłany do dzienników Azure Monitor tylko wtedy, gdy włączono go dla każdej zapory platformy Azure. Ten dziennik śledzi komunikaty DNS na serwerze DNS skonfigurowanym przy użyciu serwera proxy DNS. Dane są rejestrowane w formacie JSON, jak pokazano w następujących przykładach:


   ```
   Category: DNS proxy logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   Prawnego
   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": "DNS Request: 11.5.0.7:48197 – 15676 AAA IN md-l1l1pg5lcmkq.blob.core.windows.net. udp 55 false 512 NOERROR - 0 2.000301956s"
     }
   }
   ```

   Awarii

   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": " Error: 2 time.windows.com.reddog.microsoft.com. A: read udp 10.0.1.5:49126->168.63.129.160:53: i/o timeout”
     }
   }
   ```

   format komunikatu:

   `[client’s IP address]:[client’s port] – [query ID] [type of the request] [class of the request] [name of the request] [protocol used] [request size in bytes] [EDNS0 DO (DNSSEC OK) bit set in the query] [EDNS0 buffer size advertised in the query] [response CODE] [response flags] [response size] [response duration]`

Masz trzy opcje przechowywania dzienników:

* **Konto magazynu**: konta magazynu najlepiej sprawdzają się w przypadku dzienników przechowywanych przez dłuższy czas i przeglądanych w razie potrzeby.
* **Event Hubs**: usługa Event Hubs to doskonałe rozwiązanie umożliwiające integrację z innymi narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM, Security Information and Event Management), aby otrzymywać alerty dotyczące zasobów.
* **Azure monitor Logs**: dzienniki Azure monitor najlepiej sprawdzają się w czasie rzeczywistym monitorowania aplikacji lub patrząc na trendy.

## <a name="activity-logs"></a>Dzienniki aktywności

   Wpisy dziennika aktywności są zbierane domyślnie i można je wyświetlać w witrynie Azure Portal.

   Aby wyświetlić wszystkie operacje przesłane do subskrypcji platformy Azure, można użyć [dzienników aktywności platformy Azure](../azure-resource-manager/management/view-activity-logs.md) (znanych wcześniej jako dzienników operacyjnych i dzienników inspekcji).

## <a name="metrics"></a>Metryki

Metryki w Azure Monitor to wartości liczbowe, które opisują niektóre aspekty systemu w określonym czasie. Metryki są zbierane co minutę i są przydatne do zgłaszania alertów, ponieważ mogą być często próbkowane. Alert może być uruchamiany szybko z stosunkowo prostą logiką.

Następujące metryki są dostępne dla zapory platformy Azure:

- **Liczba trafień reguł aplikacji** — liczba trafień reguły aplikacji.

    Jednostka: liczba

- **Liczba trafień reguł sieci** — liczba trafień reguły sieci.

    Jednostka: liczba

- **Przetworzone dane** — suma danych przechodzących przez zaporę w danym przedziale czasu.

    Jednostka: bajty

- **Przepływność** — szybkość danych przechodzących przez zaporę na sekundę.

    Jednostka: bity na sekundę

- **Stan kondycji zapory** — wskazuje na kondycję zapory na podstawie dostępności portu.

    Jednostka: procent

   Ta metryka ma dwa wymiary:
  - Stan: możliwe wartości są *zdrowe*, *w*złej *kondycji*.
  - Przyczyna: wskazuje przyczynę odpowiedniego stanu zapory. 

     W przypadku używania portów podłączania adresów sieciowych > 95%, są one uznawane za wyczerpane, a kondycja wynosi 50% ze stanem o**obniżonym obniżyć** i Przyczyna =**port**. Zapora nadal przetwarza ruch i nie ma to wpływu na istniejące połączenia. Jednak sporadycznie mogą wystąpić problemy z ustanawianiem nowych połączeń.

     Jeśli porty źródłowego translatora adresów sieciowych są używane < 95%, Zapora jest uznawana za w dobrej kondycji, a kondycja jest wyświetlana jako 100%.

     Jeśli nie zgłoszono żadnego użycia portów SNAT, dla kondycji jest wyświetlana wartość 0%. 

- **Wykorzystanie portów** przez przystawkę adresów sieciowych — wartość procentowa portów, które zostały wykorzystane przez zaporę.

    Jednostka: procent

   Gdy dodasz więcej publicznych adresów IP do zapory, więcej portów SNAT będzie dostępnych i zmniejszy się ich wykorzystanie. Ponadto, gdy zapora jest z różnych powodów skalowana w poziomie (na przykład ze względu na procesor CPU lub przepływność), zwalniane są dodatkowe porty SNAT. W związku z tym, procent wykorzystania portów przydziałów adresów sieciowych może zostać wyłączony bez dodawania jakichkolwiek publicznych adresów IP, po prostu ze względu na skalowanie usługi. Możesz bezpośrednio kontrolować liczbę dostępnych publicznych adresów IP w celu zwiększenia liczby portów dostępnych w zaporze. Nie można jednak bezpośrednio kontrolować skalowania zapory.


## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak monitorować dzienniki i metryki zapory platformy Azure, zobacz [Samouczek: monitorowanie dzienników zapory platformy Azure](tutorial-diagnostics.md).

- Aby dowiedzieć się więcej o metrykach w Azure Monitor, zobacz [metryki w Azure monitor](../azure-monitor/platform/data-platform-metrics.md).
