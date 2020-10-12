---
title: Monitorowanie dzienników dla zapory aplikacji sieci Web platformy Azure
description: Dowiedz się, jak włączyć dzienniki i zarządzać nimi oraz zaporę aplikacji sieci Web platformy Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: a84e48c7fbb6d63a4bf8946b66bd35f354643ccb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84753602"
---
# <a name="resource-logs-for-azure-web-application-firewall"></a>Dzienniki zasobów dla zapory aplikacji sieci Web platformy Azure

Zasoby zapory aplikacji sieci Web można monitorować przy użyciu dzienników. Możesz zaoszczędzić wydajność, dostęp i inne dane lub wykorzystać je z zasobu do monitorowania.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Za pomocą różnych typów dzienników na platformie Azure można zarządzać bramami aplikacji i rozwiązywać problemy. Niektóre z tych dzienników są dostępne za pośrednictwem portalu. Wszystkie dzienniki można wyodrębnić z usługi Azure Blob Storage i przeglądać je w różnych narzędziach, takich jak [dzienniki Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md), Excel i Power BI. Więcej informacji o różnych typach dzienników można znaleźć na poniższej liście:

* **Dziennik aktywności**: możesz użyć [dzienników aktywności platformy Azure](../../azure-resource-manager/management/view-activity-logs.md) , aby wyświetlić wszystkie operacje przesyłane do subskrypcji platformy Azure oraz ich stan. Wpisy dziennika aktywności są zbierane domyślnie i można je wyświetlać w witrynie Azure Portal.
* **Dostęp do dziennika zasobów**: można użyć tego dziennika do wyświetlania wzorców dostępu Application Gateway i analizowania ważnych informacji. Dotyczy to adresu IP wywołującego, żądanego adresu URL, opóźnienia odpowiedzi, kodu powrotu i liczby bajtów. Dziennik dostępu jest zbierany co 300 sekund. Ten dziennik zawiera jeden rekord na wystąpienie Application Gateway. Wystąpienie Application Gateway jest identyfikowane przez właściwość instanceId.
* **Dziennik zasobów wydajności**: ten dziennik służy do wyświetlania sposobu wykonywania wystąpień Application Gateway. Ten dziennik przechwytuje informacje o wydajności dla każdego wystąpienia, w tym łączne obsłużone żądania, przepływność w bajtach, Łączna Liczba obsłużonych żądań, liczbę żądań zakończonych niepowodzeniem oraz liczbę wystąpień zaplecza w złej kondycji i złej jakości. Dziennik wydajności jest zbierany co 60 sekund. Dziennik wydajności jest dostępny tylko dla jednostki SKU w wersji 1. W przypadku jednostki SKU v2 Użyj [metryk](../../application-gateway/application-gateway-metrics.md) dla danych wydajności.
* **Dziennik zasobów zapory**: ten dziennik służy do wyświetlania żądań, które są rejestrowane w trybie wykrywania lub zapobiegania bramie aplikacji skonfigurowanej za pomocą zapory aplikacji sieci Web.

> [!NOTE]
> Dzienniki są dostępne tylko dla zasobów wdrożonych w modelu wdrażania Azure Resource Manager. Nie można używać dzienników dla zasobów w klasycznym modelu wdrażania. Aby lepiej zrozumieć dwa modele, zapoznaj się z artykułem dotyczącym [wdrażania i wdrażania klasycznego Menedżer zasobów](../../azure-resource-manager/management/deployment-models.md) .

Masz trzy opcje przechowywania dzienników:

* **Konto magazynu**: konta magazynu najlepiej sprawdzają się w przypadku dzienników przechowywanych przez dłuższy czas i przeglądanych w razie potrzeby.
* **Centra zdarzeń**: Centra zdarzeń to doskonałe rozwiązanie umożliwiające integrację z innymi narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń (Siem) w celu uzyskania alertów dotyczących zasobów.
* **Azure monitor Logs**: dzienniki Azure monitor najlepiej sprawdzają się w czasie rzeczywistym monitorowania aplikacji lub patrząc na trendy.

### <a name="enable-logging-through-powershell"></a>Włączanie rejestrowania za poorednictwem programu PowerShell

Rejestrowanie aktywności jest automatycznie włączone dla wszystkich zasobów usługi Resource Manager. Aby rozpocząć zbieranie danych dostępnych w tych dziennikach, należy włączyć funkcję rejestrowania dostępu i wydajności. Aby włączyć rejestrowanie, wykonaj następujące czynności:

1. Zanotuj identyfikator zasobu konta magazynu, w ramach którego są przechowywane dane dzienników. Ta wartość ma postać:/subscriptions/ \<subscriptionId\> /ResourceGroups/ \<resource group name\> /providers/Microsoft.Storage/storageAccounts/ \<storage account name\> . Użyć możesz dowolnego konta magazynu w ramach subskrypcji. Te informacje możesz znaleźć w witrynie Azure Portal.

    ![Portal: Identyfikator zasobu dla konta magazynu](../media/web-application-firewall-logs/diagnostics1.png)

2. Zanotuj identyfikator zasobu bramy aplikacji, dla którego jest włączone rejestrowanie. Ta wartość ma postać:/subscriptions/ \<subscriptionId\> /ResourceGroups/ \<resource group name\> /providers/Microsoft.Network/applicationGateways/ \<application gateway name\> . Te informacje możesz znaleźć w portalu.

    ![Portal: Identyfikator zasobu dla usługi Application Gateway](../media/web-application-firewall-logs/diagnostics2.png)

3. Włącz rejestrowanie zasobów przy użyciu następującego polecenia cmdlet programu PowerShell:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>Dzienniki aktywności nie wymagają oddzielnego konta magazynu. Użycie magazynu na potrzeby rejestrowania danych o dostępie i wydajności powoduje naliczenie opłat za usługę.

### <a name="enable-logging-through-the-azure-portal"></a>Włączanie rejestrowania za pośrednictwem witryny Azure Portal

1. W Azure Portal Znajdź zasób i wybierz pozycję **Ustawienia diagnostyczne**.

   W przypadku Application Gateway dostępne są trzy dzienniki:

   * Dziennik dostępu
   * Dziennik wydajności
   * Dziennik zapory

2. Aby rozpocząć zbieranie danych, wybierz pozycję **Włącz diagnostykę**.

   ![Włączanie diagnostyki][1]

3. Strona **Ustawienia diagnostyki** zawiera ustawienia dzienników zasobów. W tym przykładzie Log Analytics przechowuje dzienniki. W celu zapisania dzienników zasobów można również użyć centrów zdarzeń i konta magazynu.

   ![Rozpoczynanie procesu konfiguracji][2]

5. Wpisz nazwę ustawień, Potwierdź ustawienia, a następnie wybierz pozycję **Zapisz**.

### <a name="activity-log"></a>Dziennik aktywności

Platforma Azure domyślnie generuje dziennik aktywności. Dzienniki są przechowywane przez 90 dni w magazynie dzienników zdarzeń platformy Azure. Dowiedz się więcej o tych dziennikach, odczytując artykuł [Wyświetlanie zdarzeń i dziennika aktywności](../../azure-resource-manager/management/view-activity-logs.md) .

### <a name="access-log"></a>Dziennik dostępu

Dziennik dostępu jest generowany tylko wtedy, gdy włączono go na każdym wystąpieniu Application Gateway, zgodnie z opisem w poprzednich krokach. Dane są przechowywane na koncie magazynu określonym podczas włączania rejestrowania. Każdy dostęp do Application Gateway jest rejestrowany w formacie JSON, jak pokazano w następującym przykładzie dla wersji 1:

|Wartość  |Opis  |
|---------|---------|
|instanceId     | Application Gateway wystąpienie, które obsłużyło żądanie.        |
|clientIP     | Adres IP pochodzący od żądania.        |
|clientPort     | Port źródłowy żądania.       |
|httpMethod     | Metoda HTTP używana przez żądanie.       |
|requestUri     | Identyfikator URI odebranego żądania.        |
|RequestQuery     | **Serwer — rozesłane**: wystąpienie puli zaplecza, które wysłało żądanie.</br>**X-AzureApplicationGateway-log-ID**: identyfikator korelacji używany dla żądania. Może służyć do rozwiązywania problemów z ruchem na serwerach zaplecza. </br>**Serwer-stan**: kod odpowiedzi HTTP otrzymany Application Gateway od zaplecza.       |
|UserAgent     | Agent użytkownika z nagłówka żądania HTTP.        |
|Wartości httpStatus     | Kod stanu HTTP zwrócony do klienta z Application Gateway.       |
|httpVersion     | Wersja protokołu HTTP żądania.        |
|Hmaster     | Rozmiar odebranego pakietu w bajtach.        |
|Hmaster| Rozmiar wysłanego pakietu, w bajtach.|
|timeTaken| Czas (w milisekundach), przez jaki trwa przetwarzanie żądania i jego odpowiedź do wysłania. Ta wartość jest obliczana jako interwał od momentu, gdy Application Gateway otrzymuje pierwszy bajt żądania HTTP do momentu zakończenia operacji wysyłania odpowiedzi. Należy pamiętać, że pole Time-Taken zwykle obejmuje czas, w którym pakiety żądań i odpowiedzi są przesyłane przez sieć. |
|sslEnabled| Czy komunikacja z pulami zaplecza korzysta z protokołu TLS/SSL. Prawidłowe wartości są włączone i wyłączone.|
|host| Nazwa hosta, za pomocą którego żądanie zostało wysłane do serwera wewnętrznej bazy danych. Jeśli nazwa hosta zaplecza jest zastępowana, będzie to miało odzwierciedlenie.|
|originalHost| Nazwa hosta, za pomocą którego żądanie zostało odebrane przez Application Gateway od klienta.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "timestamp": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
W przypadku Application Gateway i WAF v2 dzienniki zawierają nieco więcej informacji:

|Wartość  |Opis  |
|---------|---------|
|instanceId     | Application Gateway wystąpienie, które obsłużyło żądanie.        |
|clientIP     | Adres IP pochodzący od żądania.        |
|clientPort     | Port źródłowy żądania.       |
|httpMethod     | Metoda HTTP używana przez żądanie.       |
|requestUri     | Identyfikator URI odebranego żądania.        |
|UserAgent     | Agent użytkownika z nagłówka żądania HTTP.        |
|Wartości httpStatus     | Kod stanu HTTP zwrócony do klienta z Application Gateway.       |
|httpVersion     | Wersja protokołu HTTP żądania.        |
|Hmaster     | Rozmiar odebranego pakietu w bajtach.        |
|Hmaster| Rozmiar wysłanego pakietu, w bajtach.|
|timeTaken| Czas (w milisekundach), przez jaki trwa przetwarzanie żądania i jego odpowiedź do wysłania. Ta wartość jest obliczana jako interwał od momentu, gdy Application Gateway otrzymuje pierwszy bajt żądania HTTP do momentu zakończenia operacji wysyłania odpowiedzi. Należy pamiętać, że pole Time-Taken zwykle obejmuje czas, w którym pakiety żądań i odpowiedzi są przesyłane przez sieć. |
|sslEnabled| Czy komunikacja z pulami zaplecza była używana w protokole TLS. Prawidłowe wartości są włączone i wyłączone.|
|sslCipher| Mechanizm szyfrowania używany do komunikacji TLS (Jeśli protokół TLS jest włączony).|
|sslProtocol| Używany protokół TLS (Jeśli protokół TLS jest włączony).|
|serverRouted| Serwer zaplecza, do którego Brama aplikacji kieruje żądanie.|
|serverStatus| Kod stanu HTTP serwera wewnętrznej bazy danych.|
|serverResponseLatency| Opóźnienie odpowiedzi z serwera wewnętrznej bazy danych.|
|host| Adres podany w nagłówku hosta żądania.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>Dziennik wydajności

Dziennik wydajności jest generowany tylko wtedy, gdy włączono go na każdym wystąpieniu Application Gateway, zgodnie z opisem w poprzednich krokach. Dane są przechowywane na koncie magazynu określonym podczas włączania rejestrowania. Dane dziennika wydajności są generowane w 1-minutowych interwałach. Jest on dostępny tylko dla jednostki SKU w wersji 1. W przypadku jednostki SKU v2 Użyj [metryk](../../application-gateway/application-gateway-metrics.md) dla danych wydajności. Rejestrowane są następujące dane:


|Wartość  |Opis  |
|---------|---------|
|instanceId     |  Application Gateway wystąpienie, dla którego są generowane dane wydajności. W przypadku bramy aplikacji o wielu wystąpieniach istnieje jeden wiersz dla każdego wystąpienia.        |
|healthyHostCount     | Liczba hostów w dobrej kondycji w puli zaplecza.        |
|unHealthyHostCount     | Liczba hostów w złej kondycji w puli zaplecza.        |
|requestCount     | Liczba obsłużonych żądań.        |
|opóźnienie | Średnie opóźnienie (w milisekundach) żądań z wystąpienia do zaplecza, który obsługuje żądania. |
|failedRequestCount| Liczba żądań zakończonych niepowodzeniem.|
|danych| Średnia przepływność od ostatniego dziennika, mierzona w bajtach na sekundę.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> Opóźnienie jest obliczane od momentu otrzymania pierwszego bajtu żądania HTTP do momentu wysłania ostatniego bajtu odpowiedzi HTTP. Jest to suma Application Gateway czasu przetwarzania oraz koszt sieci do zaplecza oraz czas przetwarzania żądania przez zaplecze.

### <a name="firewall-log"></a>Dziennik zapory

Dziennik zapory jest generowany tylko wtedy, gdy włączono go dla każdej bramy aplikacji, zgodnie z opisem w poprzednich krokach. Ten dziennik wymaga również, aby zapora aplikacji sieci Web została skonfigurowana na bramie aplikacji. Dane są przechowywane na koncie magazynu określonym podczas włączania rejestrowania. Rejestrowane są następujące dane:


|Wartość  |Opis  |
|---------|---------|
|instanceId     | Wystąpienie Application Gateway, dla którego są generowane dane zapory. W przypadku bramy aplikacji o wielu wystąpieniach istnieje jeden wiersz dla każdego wystąpienia.         |
|clientIp     |   Adres IP pochodzący od żądania.      |
|clientPort     |  Port źródłowy żądania.       |
|requestUri     | Adres URL otrzymanego żądania.       |
|Element rulesettype     | Typ zestawu reguł. Dostępna wartość to OWASP.        |
|Element rulesetversion     | Używana wersja zestawu reguł. Dostępne wartości to 2.2.9 i 3,0.     |
|ruleId     | Identyfikator reguły zdarzenia wyzwalającego.        |
|message     | Przyjazny dla użytkownika komunikat dla zdarzenia wyzwalającego. Więcej szczegółowych informacji znajduje się w sekcji Szczegóły.        |
|akcja     |  Akcja podjęta na żądaniu. Dostępne wartości są blokowane i dozwolone.      |
|lokacja     | Lokacja, dla której został wygenerowany dziennik. Obecnie tylko globalne są wyświetlane, ponieważ reguły są globalne.|
|uzyskać     | Szczegóły zdarzenia wyzwalającego.        |
|details. Message     | Opis reguły.        |
|Szczegóły. dane     | Określone dane Znalezione w żądaniu, które pasują do reguły.         |
|details. File     | Plik konfiguracji, który zawiera regułę.        |
|Szczegóły. wiersz     | Numer wiersza w pliku konfiguracji, który wyzwolił zdarzenie.       |
|nazw   | Nazwa hosta lub adres IP Application Gateway.    |
|transactionId  | Unikatowy identyfikator danej transakcji, który pomaga grupować wiele naruszeń reguł, które wystąpiły w ramach tego samego żądania.   |
|policyId   | Unikatowy identyfikator zasad zapory skojarzonych z Application Gateway, odbiornikiem lub ścieżką.   |
|policyScope    | Lokalizacjami wartości zasad mogą być "globalne", "odbiornik" lub "lokalizacja".   |
|policyScopeName   | Nazwa obiektu, w którym zasady są stosowane.    |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.147",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343",
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/perListener",
      "policyScope": "Listener",
      "policyScopeName": "httpListener1"
    }
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>Wyświetlanie i analizowanie dziennika aktywności

Dane dziennika aktywności można wyświetlać i analizować przy użyciu dowolnej z następujących metod:

* **Narzędzia platformy Azure**: pobierz informacje z dziennika aktywności przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST platformy Azure lub witryny Azure Portal. Instrukcje krok po kroku dla każdej metody są szczegółowo opisane w artykule [Activity operations with Resource Manager (Operacje działań przy użyciu usługi Resource Manager)](../../azure-resource-manager/management/view-activity-logs.md).
* **Usługa Power BI**: jeśli nie masz jeszcze konta usługi [Power BI](https://powerbi.microsoft.com/pricing), możesz ją wypróbować bezpłatnie. Za pomocą [Power BI szablonów aplikacji](https://docs.microsoft.com/power-bi/service-template-apps-overview)można analizować dane.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Wyświetlanie i analizowanie dzienników dostępu, wydajności i zapory

[Dzienniki Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md) mogą zbierać pliki liczników i dzienników zdarzeń z konta usługi BLOB Storage. Obejmuje ona wizualizacje oraz zaawansowane możliwości wyszukiwania na potrzeby analizowania dzienników.

Ponadto możesz połączyć się z kontem magazynu i pobrać wpisy dziennika JSON dotyczące dostępu i wydajności. Po pobraniu plików JSON możesz je przekonwertować do formatu CSV i wyświetlać w programie Excel, usłudze Power BI lub innym narzędziu do wizualizacji danych.

> [!TIP]
> Jeśli znasz program Visual Studio oraz podstawowe pojęcia dotyczące zmiany wartości stałych i zmiennych w języku C#, możesz skorzystać z [konwerterów dzienników](https://github.com/Azure-Samples/networking-dotnet-log-converter) dostępnych w witrynie GitHub.
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>Analizowanie dzienników dostępu za poorednictwem GoAccess

Opublikowano szablon Menedżer zasobów, który służy do instalowania i uruchamiania popularnych analizatora dzienników [GoAccess](https://goaccess.io/) na potrzeby Application Gateway dzienników dostępu. GoAccess zapewnia cenne dane statystyczne dotyczące ruchu HTTP, takie jak unikatowych odwiedzających, żądanych plików, hostów, systemów operacyjnych, przeglądarek, kodów stanu HTTP i nie tylko. Aby uzyskać więcej informacji, zobacz [plik Readme w folderze szablonów Menedżer zasobów w witrynie GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="next-steps"></a>Następne kroki

* Wizualizuj dzienniki liczników i zdarzeń przy użyciu [dzienników Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md).
* [Wizualizuj dziennik aktywności platformy Azure przy użyciu wpisu w blogu Power BI](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) .
* [Wyświetlanie i analizowanie dzienników aktywności platformy Azure w Power BI i więcej wpisów w](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogu.

[1]: ../media/web-application-firewall-logs/figure1.png
[2]: ../media/web-application-firewall-logs/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
