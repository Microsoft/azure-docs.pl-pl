---
title: Dostrajanie Web Application Firewall (WAF) dla Azure Front Door
description: Z tego artykułu dowiesz się, jak dostroić WAF do Front Door.
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: c0879edc0e3fbd6cf6bcadc26dd862f95ecf4fd4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872357"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Dostrajanie Web Application Firewall (WAF) dla Azure Front Door
 
Domyślny zestaw reguł zarządzany przez platformę Azure jest oparty na podstawowym zestawie reguł [OWASP (CRS)](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) i został zaprojektowany tak, aby był ściśle zaprojektowany. Często oczekuje się, że reguły WAF muszą być dostosowane do konkretnych potrzeb aplikacji lub organizacji korzystających z tej aplikacji. Jest to często osiągane przez zdefiniowanie wykluczeń reguł, utworzenie reguł niestandardowych, a nawet wyłączenie reguł, które mogą powodować problemy lub wyniki fałszywie dodatnie. Istnieje kilka czynności, które można wykonać, jeśli żądania, które powinny przechodzić przez Web Application Firewall (WAF), są zablokowane.

Najpierw upewnij się, że przeczytasz omówienie Front Door [WAF](waf-front-door-create-portal.md) i zasady aplikacji sieci Front Door aplikacji sieci Szkieletowej dla dokumentów. [](afds-overview.md) Upewnij się również, że włączono monitorowanie i rejestrowanie w funkcji [WAF.](waf-front-door-monitor.md) W tych artykułach wyjaśniono, jak działa ta funkcja, jak działają zestawy reguł aplikacji sieci Szkieletowej oraz jak uzyskać dostęp do dzienników aplikacji sieci Szkieletowej.
 
## <a name="understanding-waf-logs"></a>Understanding WAF logs (Informacje o dziennikach aplikacji sieci Szkieletowej)
 
Dzienniki WAF mają na celu pokazanie każdego żądania, które jest dopasowane lub zablokowane przez usługę WAF. Jest to kolekcja wszystkich ocenianych żądań, które są dopasowane lub zablokowane. Jeśli zauważysz, żeafaf blokuje żądanie, które nie powinno (wynik fałszywie dodatni), możesz wykonać kilka czynności. Najpierw zawęzij i znajdź określone żądanie. W razie potrzeby można skonfigurować [niestandardowy komunikat](./waf-front-door-configure-custom-response-code.md) odpowiedzi, aby uwzględnić pole, aby łatwo zidentyfikować zdarzenie i wykonać zapytanie dziennika dla `trackingReference` tej określonej wartości. Sprawdź dzienniki, aby znaleźć określony adres URI, sygnaturę czasową lub adres IP klienta żądania. Po odnalezieniu powiązanych wpisów dziennika można rozpocząć działanie na podstawie wyników fałszywie dodatnich. 
 
Załóżmy na przykład, że masz legalny ruch zawierający ciąg, który chcesz `1=1` przekazać za pośrednictwem twojej aplikacji sieci WAF. Oto jak wygląda żądanie:

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

Jeśli spróbujesz wykonać żądanie, taafaf blokuje ruch zawierający ciąg *1=1* w dowolnym parametrze lub polu. Jest to ciąg często skojarzony z atakiem przez wstrzyknięcie kodu SQL. Możesz sprawdzić dzienniki i wyświetlić sygnaturę czasową żądania oraz reguły, które zablokowały/pasują.
 
W poniższym przykładzie eksplorujesz `FrontdoorWebApplicationFirewallLog` dziennik wygenerowany z powodu dopasowania reguły. Następujące zapytanie usługi Log Analytics może służyć do wyszukiwania żądań, które zostały zablokowane w ciągu ostatnich 24 godzin:

```kusto
AzureDiagnostics
| where Category == 'FrontdoorWebApplicationFirewallLog'
| where TimeGenerated > ago(1d)
| where action_s == 'Block'

```
 
W polu `requestUri` widać, że żądanie zostało wykonane w szczególności `/api/Feedbacks/` do tego celu. Przechodząc dalej, w polu znajdujemy `942110` identyfikator `ruleName` reguły. Znając identyfikator reguły, możesz przejść do oficjalnego repozytorium zestawu reguł [](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) [OWASP ModSecurity Core i](https://github.com/coreruleset/coreruleset) wyszukać go według tego identyfikatora reguły, aby przejrzeć jego kod i dokładnie zrozumieć, na czym dokładnie odpowiada ta reguła. 
 
Następnie, sprawdzając pole, widzimy, że ta reguła jest ustawiona na blokowanie żądań po dopasowaniu i potwierdzamy, że żądanie zostało faktycznie zablokowane przez tę regułę, ponieważ ustawiono wartość `action` `policyMode` `prevention` . 
 
Teraz sprawdźmy informacje w `details` polu . W tym miejscu można zobaczyć `matchVariableName` informacje i `matchVariableValue` . Wiemy, że ta reguła została wyzwolona, ponieważ ktoś w polu aplikacji internetowej wprowadź *wartość 1=1.* `comment`
 
```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```
 
Istnieje również wartość podczas sprawdzania dzienników dostępu w celu rozwinięcia wiedzy o danym zdarzeniu WAF. Poniżej znajduje się `FrontdoorAccessLog` dziennik, który został wygenerowany jako odpowiedź na powyższe zdarzenie.
 
Możesz zobaczyć, że są to powiązane dzienniki na podstawie `trackingReference` wartości, która jest taka sama. Między różnymi polami, które zapewniają ogólne informacje szczegółowe, takie jak `userAgent` i `clientIP` , zwracamy uwagę na pola `httpStatusCode` i `httpStatusDetails` . W tym miejscu możemy potwierdzić, że klient otrzymał odpowiedź HTTP 403, co zdecydowanie potwierdza, że to żądanie zostało odrzucone i zablokowane. 
 
```json
{
    "time": "2020-09-24T16:43:04.5430764Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "httpMethod": "POST",
        "httpVersion": "1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "requestBytes": "2160",
        "responseBytes": "324",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36",
        "clientIp": "1.1.1.1",
        "socketIp": "1.1.1.1",
        "clientPort": "53566",
        "timeToFirstByte": "0.01",
        "timeTaken": "0.011",
        "securityProtocol": "",
        "routingRuleName": "DemoBERoutingRule",
        "rulesEngineMatchNames": [],
        "backendHostname": "13.88.65.130:3000",
        "isReceivedFromClient": true,
        "httpStatusCode": "403",
        "httpStatusDetails": "403",
        "pop": "WST",
        "cacheStatus": "CONFIG_NOCACHE"
    }
}
```

## <a name="resolving-false-positives"></a>Rozwiązywanie wyników fałszywie dodatnich
 
Aby podjąć świadomą decyzję o obsłudze wyników fałszywie dodatnich, należy zapoznać się z technologiami używanymi przez aplikację. Załóżmy na przykład, że w stosie technologii nie ma serwera SQL, a z tymi regułami są związane wyniki fałszywie dodatnie. Wyłączenie tych reguł niekoniecznie zniechęca bezpieczeństwo. 

Dzięki tym informacjom i wiedzy, że reguła 942110 jest taka, która pasuje do ciągu w naszym przykładzie, możemy wykonać kilka czynności, aby zatrzymać blokowanie tego uzasadnionego `1=1` żądania:
 
* Korzystanie z list wykluczeń
  * Zobacz [Web Application Firewall (WAF) with Front Door Service exclusion lists](waf-front-door-exclusion.md) (Lista wykluczeń usługi Front Door Service), aby uzyskać więcej informacji na temat list wykluczeń. 
* Zmienianie akcji WAF
  * Zobacz [Akcje WAF,](afds-overview.md#waf-actions) aby uzyskać więcej informacji na temat akcji, które można podjąć, gdy żądanie jest spełnione warunki reguły.
* Używanie reguł niestandardowych
  * Aby uzyskać więcej informacji na [temat reguł niestandardowych,](waf-front-door-custom-rules.md) zobacz Reguły niestandardowe Web Application Firewall z Azure Front Door niestandardowych.
* Wyłączanie reguł 

> [!TIP]
> Wybierając podejście umożliwiające zezwalanie na uzasadnione żądania za pośrednictwem aplikacji sieci Szkieletowej aplikacji sieci Szkieletowej, staraj się, aby było to jak najbardziej wąskie. Na przykład lepiej jest użyć listy wykluczeń niż całkowite wyłączenie reguły.

### <a name="using-exclusion-lists"></a>Korzystanie z list wykluczeń

Jedną z zalet korzystania z listy wykluczeń jest to, że tylko zmienna dopasowania, która ma zostać wykluczona, nie będzie już sprawdzana dla danego żądania. Oznacza to, że można wybrać między określonymi nagłówkami żądania, plikami cookie żądania, argumentami ciągu zapytania lub argumentami wpisów treści żądania do wykluczenia, jeśli zostanie spełniony określony warunek, zamiast wykluczać całe żądanie z inspekcji. Inne nieo określone zmienne żądania będą nadal sprawdzane normalnie.
 
Należy wziąć pod uwagę, że wykluczenia są ustawieniem globalnym. Oznacza to, że skonfigurowane wykluczenie będzie stosowane do całego ruchu przechodzącego przez twoją aplikację internetową, a nie tylko do określonej aplikacji internetowej lub URI. Może to być na przykład problemem, jeśli *wartość 1=1* jest prawidłowym żądaniem w treści dla określonej aplikacji internetowej, ale nie dla innych użytkowników w ramach tych samych zasad WAF. Jeśli użycie różnych list wykluczeń dla różnych aplikacji ma sens, rozważ użycie różnych zasad WAF dla każdej aplikacji i zastosowanie ich do frontendu każdej aplikacji.
 
Podczas konfigurowania list wykluczeń dla reguł zarządzanych można wykluczyć wszystkie reguły w zestawie reguł, wszystkie reguły w grupie reguł lub pojedynczą regułę. Listę wykluczeń można skonfigurować przy użyciu programu [PowerShell,](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject)interfejsu wiersza polecenia platformy [Azure,](/cli/azure/network/front-door/waf-policy/managed-rules/exclusion#az_network_front_door_waf_policy_managed_rules_exclusion_add)interfejsu [API REST](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)lub Azure Portal.

* Wykluczenia na poziomie reguły
  * Zastosowanie wykluczeń na poziomie reguły oznacza, że określone wykluczenia nie będą analizowane tylko względem tej indywidualnej reguły, podczas gdy będą analizowane przez wszystkie inne reguły w zestawie reguł. Jest to najbardziej szczegółowy poziom w przypadku wykluczeń i może służyć do dostrojenia zarządzanego zestawu reguł na podstawie informacji w dziennikach aplikacji sieci Szkieletowej podczas rozwiązywania problemów ze zdarzeniem.
* Wykluczenia na poziomie grupy reguł
  * Zastosowanie wykluczeń na poziomie grupy reguł oznacza, że określone wykluczenia nie będą analizowane względem tego określonego zestawu typów reguł. Na przykład wybranie *sqli* jako wykluczonej grupy reguł wskazuje, że zdefiniowane wykluczenia żądań nie będą sprawdzane przez żadne reguły specyficzne dla języka SQLI, ale nadal będą sprawdzane przez reguły w innych grupach, takich jak *PHP,* *RFI* lub *XSS.* Ten typ wykluczenia może być przydatny, gdy mamy pewność, że aplikacja nie jest podatna na określone typy ataków. Na przykład aplikacja, która nie ma żadnych baz danych SQL, może mieć wszystkie reguły *SQLI* wykluczone bez zagrożenia dla poziomu zabezpieczeń.
* Wykluczenia na poziomie zestawu reguł 
  * Zastosowanie wykluczeń na poziomie zestawu reguł oznacza, że określone wykluczenia nie będą analizowane względem żadnych reguł zabezpieczeń dostępnych w tym zestawie reguł. Jest to kompleksowe wykluczenie, dlatego należy z niego korzystać ostrożnie.

W tym przykładzie będziemy wykonywać wykluczenia na najbardziej szczegółowym poziomie (stosując wykluczenie do pojedynczej reguły) i chcemy wykluczyć zmienną dopasowania Treść żądania po nazwie grupy **args,** która zawiera element `comment` . Jest to oczywiste, ponieważ szczegóły zmiennej dopasowania są widoczne w dzienniku zapory: `"matchVariableName": "PostParamValue:comment"` . Atrybut to `comment` . Możesz również znaleźć tę nazwę atrybutu na kilka innych sposobów. Zobacz [Znajdowanie nazw atrybutów żądania](#finding-request-attribute-names).

![Reguły wykluczeń](../media/waf-front-door-tuning/exclusion-rules.png)

![Wykluczenie reguły dla określonej reguły](../media/waf-front-door-tuning/exclusion-rule.png)

Czasami zdarzają się przypadki, w których określone parametry są przekazywane do aplikacji sieci WAF w sposób, który może nie być intuicyjny. Na przykład istnieje token, który jest przekazywany podczas uwierzytelniania przy użyciu Azure Active Directory. Ten token, `__RequestVerificationToken` , zwykle jest przekazywany jako plik cookie żądania. Jednak w niektórych przypadkach, gdy pliki cookie są wyłączone, ten token jest również przekazywany jako argument post żądania. Z tego powodu w celu rozwiązania fałszywych alarmów tokenu usługi Azure AD należy upewnić się, że jest on dodawany do listy wykluczeń `__RequestVerificationToken` zarówno dla elementów , jak i `RequestCookieNames` `RequestBodyPostArgsNames` .

Wykluczenia nazwy pola *(selektora)* oznaczają, że wartość nie będzie już oceniana przezafaf. Jednak sama nazwa pola jest nadal oceniana i w rzadkich przypadkach może być ona dopasowana do reguły WAF i wyzwalać akcję.

![Wykluczanie reguł dla zestawu reguł](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>Zmienianie akcji WAF

Innym sposobem obsługi zachowania reguł WAF jest wybranie akcji, która będzie akcję, która będzie akcję, gdy żądanie pasuje do warunków reguły. Dostępne akcje to: [Zezwalaj, Blokuj, Dziennik i Przekieruj.](afds-overview.md#waf-actions)

W tym przykładzie zmieniliśmy domyślną akcję *Blokuj* na akcję *Dziennik* w regułę 942110. Spowoduje to rejestrowanie żądania przez WAF i kontynuowanie oceny tego samego żądania względem pozostałych reguł o niższym priorytecie.

![Akcje WAF](../media/waf-front-door-tuning/actions.png)

Po wykonaniu tego samego żądania możemy odwołać się do dzienników i zobaczymy, że to żądanie było zgodne z regułą o identyfikatorze 942110 i że pole zawiera teraz pole Log zamiast `action_s` *Blokuj*  . Następnie rozszerzyliśmy zapytanie dziennika, aby uwzględnić informacje i zobaczyć, co jeszcze `trackingReference_s` się stało z tym żądaniem.

![Dziennik przedstawiający wiele dopasowania reguł](../media/waf-front-door-tuning/actions-log.png)

Co ciekawe, widzimy, że dopasowanie innej reguły SQLI występuje w milisekundach po przetworzeniu reguły o identyfikatorze 942110. To samo żądanie zostało dopasowane do reguły o identyfikatorze 942310 i tym razem została wyzwolona domyślna akcja Blokuj. 

Inną zaletą  korzystania z akcji Dziennika podczas dostrajania lub rozwiązywania problemów z WAF jest możliwość określenia, czy wiele reguł w określonej grupie reguł jest zgodnych i blokuje dane żądanie. Następnie można utworzyć wykluczenia na odpowiednim poziomie, tj. na poziomie reguły lub grupy reguł. 

### <a name="using-custom-rules"></a>Używanie reguł niestandardowych

Po zidentyfikowaniu, co powoduje dopasowanie reguły aplikacji sieci Szkieletowej, możesz użyć reguł niestandardowych, aby dostosować sposób, w jaki taafaf odpowiada na zdarzenie. Reguły niestandardowe są przetwarzane przed regułami zarządzanymi, mogą zawierać więcej niż jeden warunek, a ich akcje mogą być [zezwalania, odmowy, dziennika lub przekierowania.](afds-overview.md#waf-actions) W przypadku dopasowania reguły aparat WAF przestaje przetwarzać dane. Oznacza to, że inne reguły niestandardowe o niższym priorytecie i reguły zarządzane nie są już wykonywane.

W poniższym przykładzie utworzono regułę niestandardową z dwoma warunkami. Pierwszy warunek szuka wartości `comment` w treści żądania. Drugi warunek szuka wartości `/api/Feedbacks/` w żądaniu URI.

Użycie reguły niestandardowej pozwala uzyskać najbardziej szczegółowy poziom podczas dostrajania reguł WAF i radzenia sobie z fałszywymi alarmami. W tym przypadku nie robimy akcji tylko na podstawie wartości treści żądania, która może istnieć w wielu witrynach lub aplikacjach w ramach tych `comment` samych zasad aplikacji internetowych. Uwzględniając inny warunek, który będzie również odpowiadać konkretnej uri żądania, upewniamy się, że ta reguła niestandardowa naprawdę ma zastosowanie do tego jawnego przypadku użycia, który został `/api/Feedbacks/` zweryfikowany. Dzięki temu ten sam atak, jeśli zostanie przeprowadzony w różnych warunkach, będzie nadal sprawdzany i blokowany przez aparat WAF.

![Dziennik](../media/waf-front-door-tuning/custom-rule.png)

Podczas eksplorowania dziennika można zobaczyć, że pole zawiera nazwę nadaną utworzonej przez nas `ruleName_s` niestandardowych regułom: `redirectcomment` . W polu widać, że dla tego zdarzenia została `action_s` podjęta akcja  Przekierowanie. W polu widać, że zostały dopasowane szczegóły dla `details_matches_s` obu warunków.

### <a name="disabling-rules"></a>Wyłączanie reguł

Innym sposobem na ominiecie wyników fałszywie dodatnich jest wyłączenie reguły dopasowanej do danych wejściowych, które zdaniem aplikacji WAF były złośliwe. Ponieważ dzienniki WAF zostały przejmowane i reguła jest zawężona do 942110, można ją wyłączyć w Azure Portal. Zobacz [Dostosowywanie Web Application Firewall reguł przy użyciu Azure Portal](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules).
 
Wyłączenie reguły jest korzystne, gdy masz pewność, że wszystkie żądania, które spełnia określony warunek, są w rzeczywistości uprawnionymi żądaniami lub gdy masz pewność, że reguła po prostu nie ma zastosowania do Twojego środowiska (na przykład wyłączenie reguły iniekcji SQL, ponieważ masz zaplecza inne niż SQL). 
 
Wyłączenie reguły jest jednak ustawieniem globalnym, które ma zastosowanie do wszystkich hostów frontonu skojarzonych z zasadami WAF. Jeśli zdecydujesz się wyłączyć regułę, możesz pozostawić luki w zabezpieczeniach bez ochrony lub wykrywania innych hostów frontonu skojarzonych z zasadami WAF.
 
Jeśli chcesz użyć usługi Azure PowerShell, aby wyłączyć regułę zarządzaną, zapoznaj się z [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject) dokumentacją obiektu. Jeśli chcesz używać interfejsu wiersza polecenia platformy Azure, zapoznaj się z [`az network front-door waf-policy managed-rules override`](/cli/azure/network/front-door/waf-policy/managed-rules/override) dokumentacją.

![Regułyaf (WAF)](../media/waf-front-door-tuning/waf-rules.png)

> [!TIP]
> Dobrym pomysłem jest udokumentowanie wszelkich zmian wprowadzonych w zasadach aplikacji sieci WAF. Dołącz przykładowe żądania, aby zilustrować wykrywanie wyników fałszywie dodatnich, i jasno wyjaśnić, dlaczego dodano regułę niestandardową, wyłączono regułę lub zestaw reguł albo dodano wyjątek. Ta dokumentacja może być przydatna, jeśli w przyszłości przeprojektujemy aplikację i będzie trzeba sprawdzić, czy zmiany są nadal prawidłowe. Może to również pomóc, jeśli kiedykolwiek będziesz podlegać inspekcji lub musisz uzasadnić, dlaczego zasady aplikacji internetowej zostały ponownie skonfigurowane z ich ustawień domyślnych.

## <a name="finding-request-fields"></a>Znajdowanie pól żądania

Za pomocą serwera proxy przeglądarki, takiego jak [Fiddler,](https://www.telerik.com/fiddler)można sprawdzić poszczególne żądania i określić, jakie określone pola strony internetowej są wywoływane. Jest to przydatne, gdy musimy wykluczyć niektóre pola z inspekcji przy użyciu list wykluczeń w funkcji WAF.

### <a name="finding-request-attribute-names"></a>Znajdowanie nazw atrybutów żądania
 
W tym przykładzie widać pole, w którym wprowadzono `1=1` ciąg, ma nazwę `comment` . Te dane przekazano w treści żądania POST.

![Żądanie fiddler z wyświetloną treścią](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

Jest to pole, które można wykluczyć. Aby dowiedzieć się więcej na temat list wykluczeń, zobacz [Listy wykluczeń zapory aplikacji internetowej](./waf-front-door-exclusion.md). W takim przypadku można wykluczyć ocenę, konfigurując następujące wykluczenie:

![Reguła wykluczania](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

Możesz również zbadać dzienniki zapory, aby uzyskać informacje, aby zobaczyć, co należy dodać do listy wykluczeń. Aby włączyć rejestrowanie, zobacz Monitorowanie metryk i [dzienników w Azure Front Door](./waf-front-door-monitor.md).

Sprawdź dziennik zapory w pliku dla godziny, przez którą wystąpiło żądanie, `PT1H.json` które chcesz sprawdzić. `PT1H.json` Pliki są dostępne w kontenerach konta magazynu, w których są przechowywane dzienniki `FrontDoorWebApplicationFirewallLog` `FrontDoorAccessLog` diagnostyczne i .

W tym przykładzie można zobaczyć regułę, która zablokowała żądanie (z tym samym odwołaniem do transakcji) i wystąpiła dokładnie w tym samym czasie:

```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```

Mając wiedzę na temat działania zestawów reguł zarządzanych przez platformę Azure (zobacz Web Application Firewall w witrynie [Azure Front Door),](afds-overview.md)wiesz, że reguła z akcją: Blokuj właściwość blokuje się na podstawie danych dopasowanych w treści żądania.  W szczegółach widać, że pasuje ono do wzorca ( ), a `1=1` pole ma nazwę `comment` . Wykonaj te same poprzednie kroki, aby wykluczyć nazwę wpisów treści żądania, która `comment` zawiera .

### <a name="finding-request-header-names"></a>Znajdowanie nazw nagłówków żądań

Fiddler to ponownie przydatne narzędzie do znalezienia nazw nagłówków żądań. Na poniższym zrzucie ekranu widać nagłówki tego żądania GET, w tym Content-Type, User-Agent i tak dalej. Nagłówków żądań można również używać do tworzenia wykluczeń i reguł niestandardowych w funkcji WAF.

![Żądanie narzędzia Fiddler z nagłówkiem](../media/waf-front-door-tuning/fiddler-request-header-name.png)

Innym sposobem wyświetlania nagłówków żądań i odpowiedzi jest przejrzenie w narzędziach programistów przeglądarki, takich jak Edge lub Chrome. Możesz nacisnąć klawisz F12 lub kliknąć prawym przyciskiem myszy pozycję -> **Inspect** Narzędzia deweloperskie i wybrać kartę Sieć. Załaduj stronę internetową, a następnie kliknij żądanie, które  ->  chcesz sprawdzić. 

![Żądanie inspektora sieci](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>Znajdowanie nazw plików cookie żądań

Jeśli żądanie zawiera pliki cookie, można wybrać kartę Pliki cookie, aby wyświetlić je w programie Fiddler. Informacje o plikach cookie mogą również służyć do tworzenia wykluczeń lub reguł niestandardowych w aplikacji internetowej.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [o zaporze aplikacji internetowej platformy Azure.](../overview.md)
- Dowiedz się, jak [utworzyć usługę Front Door](../../frontdoor/quickstart-create-front-door.md).
