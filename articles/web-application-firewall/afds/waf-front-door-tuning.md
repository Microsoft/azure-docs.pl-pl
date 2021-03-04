---
title: Dostrajanie zapory aplikacji sieci Web (WAF) dla drzwi platformy Azure
description: W tym artykule dowiesz się, jak dostosować WAF do przodu.
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: 8752886bc5304de420083212d29ccd3e1cb14084
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043698"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Dostrajanie zapory aplikacji sieci Web (WAF) dla drzwi platformy Azure
 
Domyślny zestaw reguł zarządzany przez platformę Azure jest oparty na [zestawie reguł OWASP Core (KSR)](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) i został zaprojektowany z myślą o tym, aby był bardziej rygorystyczny. Często oczekuje się, że reguły WAF muszą być dostosowane do konkretnych potrzeb aplikacji lub organizacji przy użyciu WAF. Jest to często osiągane przez zdefiniowanie wykluczeń reguł, utworzenie reguł niestandardowych, a nawet wyłączenie reguł, które mogą powodować problemy lub fałszywie dodatnich. Istnieje kilka rzeczy, które można wykonać, jeśli żądania, które powinny zostać przekazane przez zaporę aplikacji sieci Web (WAF), są blokowane.

Najpierw upewnij się, że zapoznaj się z [omówieniem WAF drzwi z góry](afds-overview.md) i [zasad WAF dla dokumentów z drzwiami](waf-front-door-create-portal.md) . Upewnij się również, że włączono [monitorowanie WAF i rejestrowanie](waf-front-door-monitor.md). W tych artykułach wyjaśniono, jak działają funkcje WAF, jak działa zestaw reguł WAF oraz jak uzyskiwać dostęp do dzienników WAF.
 
## <a name="understanding-waf-logs"></a>Omówienie dzienników WAF
 
W dziennikach WAF są wyświetlane wszystkie żądania, które są dopasowane lub blokowane przez WAF. Jest to kolekcja wszystkich ocenionych żądań, które są dopasowane lub blokowane. Jeśli zauważysz, że WAF blokuje żądanie, które nie powinno (fałszywie dodatni), można wykonać kilka czynności. Po pierwsze, Zawęź i Znajdź konkretne żądanie. W razie potrzeby można [skonfigurować niestandardowy komunikat odpowiedzi](./waf-front-door-configure-custom-response-code.md) , aby uwzględnić `trackingReference` pole w celu łatwego identyfikowania zdarzenia i wykonać zapytanie dziennika dla tej konkretnej wartości. Zapoznaj się z dziennikami, aby znaleźć konkretny identyfikator URI, sygnaturę czasową lub adres IP klienta żądania. Po znalezieniu powiązanych wpisów dziennika można zacząć działać na fałszywie dodatnich. 
 
Załóżmy na przykład, że masz legalny ruch zawierający ciąg `1=1` , który chcesz przekazać przez WAF. Oto jak wygląda żądanie:

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

Jeśli spróbujesz ponowić żądanie, WAF blokuje ruch, który zawiera *1 = 1* ciąg w dowolnym parametrze lub polu. Jest to ciąg często skojarzony z atakiem iniekcji SQL. Możesz przejrzeć dzienniki i sprawdzić sygnaturę czasową żądania oraz reguły, które zostały zablokowane/dopasowane.
 
W poniższym przykładzie eksplorujemy `FrontdoorWebApplicationFirewallLog` Dziennik wygenerowany ze względu na zgodność z regułą. Następujące zapytanie Log Analytics może służyć do znajdowania żądań zablokowanych w ciągu ostatnich 24 godzin:

```kusto
AzureDiagnostics
| where Category == 'FrontdoorWebApplicationFirewallLog'
| where TimeGenerated > ago(1d)
| where action_s == 'Block'

```
 
W tym `requestUri` polu można zobaczyć, że żądanie zostało wykonane w specjalny sposób `/api/Feedbacks/` . Dodatkowo znajdziesz Identyfikator reguły `942110` w `ruleName` polu. Wiedząc, że identyfikator reguły, możesz przejść do [OWASPego zestawu reguł zapory ModSecurity Core](https://github.com/coreruleset/coreruleset) [, aby przejrzeć](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) jego kod i zrozumieć dokładnie, co ta reguła pasuje. 
 
Następnie sprawdzając `action` pole, że ta reguła jest ustawiona do blokowania żądań po dopasowaniu, a my potwierdzamy, że żądanie zostało zablokowane przez WAF, ponieważ `policyMode` jest ustawione na `prevention` . 
 
Teraz Sprawdźmy informacje w `details` polu. Jest to miejsce, w którym można zobaczyć `matchVariableName` i `matchVariableValue` informacje. Dowiesz się, że ta reguła została wyzwolona, ponieważ w polu aplikacji sieci Web wprowadzono *1 = 1* `comment` .
 
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
 
Jest również dostępna wartość w obszarze Sprawdzanie dzienników dostępu w celu zwiększenia wiedzy o danym zdarzeniu WAF. Poniżej znajdziesz `FrontdoorAccessLog` Dziennik, który został wygenerowany jako odpowiedź na powyższe zdarzenie.
 
Te dzienniki są powiązane z `trackingReference` tą samą wartością. Spośród różnych pól, które zapewniają ogólne informacje, takie jak `userAgent` i `clientIP` , należy zwrócić uwagę na `httpStatusCode` pola i `httpStatusDetails` . W tym miejscu możemy potwierdzić, że klient otrzymał odpowiedź HTTP 403, która absolutnie potwierdza, że to żądanie zostało odrzucone i zablokowane. 
 
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

## <a name="resolving-false-positives"></a>Rozpoznawanie fałszywych dodatnich
 
Aby podjąć świadomą decyzję dotyczącą obsługi fałszywych wartości dodatnich, ważne jest zapoznanie się z technologiami używanymi przez aplikację. Załóżmy na przykład, że nie ma programu SQL Server w stosie technologii i otrzymujesz fałszywe dodatnie powiązane z tymi regułami. Wyłączenie tych reguł nie musi osłabiać zabezpieczeń. 

Korzystając z tych informacji, a wiedzą, że reguła 942110 jest zgodna z `1=1` ciągiem w naszym przykładzie, możemy zrobić kilka rzeczy, aby zatrzymać to wiarygodne żądanie przed zablokowaniem:
 
* Użyj list wykluczeń
  * Aby uzyskać więcej informacji na temat list wykluczeń, zobacz [Zapora aplikacji sieci Web (WAF) z listami wykluczeń usługi front-drzwi](waf-front-door-exclusion.md) . 
* Zmień akcje WAF
  * Zobacz [WAF akcji](afds-overview.md#waf-actions) , aby uzyskać więcej informacji na temat akcji, które można wykonać, gdy żądanie pasuje do warunków reguły.
* Korzystanie z reguł niestandardowych
  * Aby uzyskać więcej informacji na temat reguł niestandardowych, zobacz [reguły niestandardowe zapory aplikacji sieci Web z usługami Azure Front drzwiczks](waf-front-door-custom-rules.md) .
* Wyłączanie reguł 

> [!TIP]
> W przypadku wybrania podejścia do zezwalania na legalne żądania przez WAF spróbuj to zrobić tak, jak to możliwe. Na przykład lepiej jest użyć listy wykluczeń, aby całkowicie wyłączyć regułę.

### <a name="using-exclusion-lists"></a>Korzystanie z list wykluczeń

Jedną z zalet korzystania z listy wykluczeń jest to, że tylko zmienna dopasowania wybrana do wykluczenia nie będzie już sprawdzana dla danego żądania. Oznacza to, że można wybrać między określonymi nagłówkami żądań, plików cookie żądania, argumentów ciągu zapytania lub argumentów post treści żądania, które mają zostać wykluczone w przypadku spełnienia określonego warunku, w przeciwieństwie do wykluczania całego żądania z inspekcji. Inne nieokreślone zmienne żądania będą nadal sprawdzane w normalny sposób.
 
Należy wziąć pod uwagę, że wykluczenia są ustawieniem globalnym. Oznacza to, że skonfigurowane wykluczenie ma zastosowanie do całego ruchu przechodzącego przez WAF, a nie tylko dla konkretnej aplikacji sieci Web lub identyfikatora URI. Na przykład może to być problem, jeśli *1 = 1* to prawidłowe żądanie w treści dla określonej aplikacji sieci Web, ale nie dla innych osób w ramach tych samych zasad WAFymi. Jeśli warto używać różnych list wykluczeń dla różnych aplikacji, należy rozważyć użycie różnych zasad WAFymi dla każdej aplikacji i zastosowanie ich do frontonu poszczególnych aplikacji.
 
Podczas konfigurowania list wykluczeń dla reguł zarządzanych można wykluczyć wszystkie reguły w ramach zestawu reguł, wszystkie reguły w grupie reguł lub pojedynczą regułę. Listę wykluczeń można skonfigurować przy użyciu [programu PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-4.7.0&viewFallbackFrom=azps-3.5.0), interfejsu [wiersza polecenia platformy Azure lub usługi](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext_front_door_az_network_front_door_waf_policy_managed_rules_exclusion_add) [API REST](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)lub Azure Portal.

* Wykluczenia na poziomie reguły
  * Zastosowanie wykluczeń na poziomie reguły oznacza, że określone wykluczenia nie będą analizowane tylko względem tej pojedynczej reguły, podczas gdy nadal będą analizowane przez wszystkie inne reguły w zestawie reguł. Jest to najbardziej szczegółowy poziom wykluczeń i można go użyć do dostosowania zestawu reguł zarządzanych na podstawie informacji znajdujących się w dziennikach WAF podczas rozwiązywania problemów dotyczących zdarzenia.
* Wykluczenia na poziomie grupy reguł
  * Zastosowanie wykluczeń na poziomie grupy reguł oznacza, że określone wykluczenia nie będą analizowane względem tego określonego zestawu typów reguł. Na przykład wybranie *SQLi* jako wykluczonej grupy reguł wskazuje, że zdefiniowane wykluczenia żądań nie będą kontrolowane przez żadną z reguł SQLi, ale nadal będzie ona sprawdzana przez reguły w innych grupach, takich jak *php*, *RFI* lub *XSS*. Ten typ wykluczenia może być przydatny, gdy mamy pewność, że aplikacja nie jest podatna na określone typy ataków. Na przykład aplikacja, która nie ma żadnych baz danych SQL, może mieć wykluczone wszystkie reguły *SQLi* bez naruszania jej poziomu zabezpieczeń.
* Wykluczenia na poziomie zestawu reguł 
  * Zastosowanie wykluczeń na poziomie zestawu reguł oznacza, że określone wykluczenia nie będą analizowane względem żadnych reguł zabezpieczeń dostępnych w tym zestawie reguł. Jest to kompleksowe wykluczenie, dlatego powinno być używane uważnie.

W tym przykładzie zostanie wykluczane na poziomie największej szczegółowości (zastosowanie wykluczenia do jednej reguły) i chcemy wykluczyć zmienną dopasowania zmienna **żądania nazwa** , która zawiera `comment` . Jest to oczywiste, ponieważ w dzienniku zapory można zobaczyć szczegóły zmiennej dopasowania: `"matchVariableName": "PostParamValue:comment"` . Ten atrybut jest `comment` . Możesz również znaleźć nazwę tego atrybutu kilka innych sposobów, zobacz [Znajdowanie nazw atrybutów żądania](#finding-request-attribute-names).

![Reguły wykluczania](../media/waf-front-door-tuning/exclusion-rules.png)

![Wykluczanie reguły dla określonej reguły](../media/waf-front-door-tuning/exclusion-rule.png)

Sporadycznie istnieją przypadki, w których określone parametry są przenoszone do WAF w sposób, który może nie być intuicyjny. Na przykład istnieje token, który jest przesyłany podczas uwierzytelniania przy użyciu Azure Active Directory. Ten token, `__RequestVerificationToken` , zazwyczaj jest przenoszona jako plik cookie żądania. Jednak w niektórych przypadkach, gdy pliki cookie są wyłączone, ten token jest również przesyłany jako argument post żądania. Z tego powodu, aby rozwiązać wynik fałszywie dodatnich dla tokenu usługi Azure AD, należy upewnić się, że `__RequestVerificationToken` zostanie on dodany do listy wykluczeń dla obu `RequestCookieNames` i `RequestBodyPostArgsNames` .

Wykluczenia w nazwie pola (*Selektor*) oznacza, że wartość nie będzie już oceniana przez WAF. Jednak sama nazwa pola nadal jest szacowana i w rzadkich przypadkach może być zgodna z regułą WAF i wyzwalać akcję.

![Wykluczanie reguły dla zestawu reguł](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>Zmiana akcji WAF

Innym sposobem obsługi zachowania reguł WAF jest wybranie akcji, która zostanie podjęta, gdy żądanie pasuje do warunków reguły. Dostępne akcje to: [Zezwalaj, Blokuj, Rejestruj i Przekieruj](afds-overview.md#waf-actions).

W tym przykładzie został zmieniony domyślny *blok* akcji na akcję *dziennika* na zasadzie 942110. To spowoduje, że WAF rejestruje żądanie i kontynuuje ocenę tego samego żądania względem pozostałych reguł o niższym priorytecie.

![Akcje WAF](../media/waf-front-door-tuning/actions.png)

Po przeprowadzeniu tego samego żądania możemy odwoływać się do dzienników, a zobaczymy, że to żądanie było zgodne z IDENTYFIKATORem reguły 942110 i że `action_s` pole wskazuje teraz jako *Dziennik* zamiast *bloku*. Następnie rozwinięcie zapytania dziennika w celu uwzględnienia `trackingReference_s` informacji i zobacz, co jeszcze się stało z tym żądaniem.

![Dziennik pokazujący wiele dopasowania reguł](../media/waf-front-door-tuning/actions-log.png)

W interesującej sekcji zobaczymy inne dopasowanie reguły SQLI w milisekundach po przetworzeniu identyfikatora reguły 942110. To samo żądanie dopasowane do identyfikatora reguły 942310 i ten czas jest wyzwalany przez domyślny *blok* akcji.

Inną zaletą korzystania z akcji *rejestrowania* podczas dostrajania WAF lub rozwiązywania problemów jest możliwość zidentyfikowania, czy wiele reguł w danej grupie reguł jest zgodnych i blokuje dane żądanie. Następnie można utworzyć wykluczenia na odpowiednim poziomie, na przykład na poziomie reguły lub grupy reguł. 

### <a name="using-custom-rules"></a>Używanie reguł niestandardowych

Po zidentyfikowaniu, co powoduje dopasowanie reguły WAF, możesz użyć niestandardowych reguł, aby dostosować sposób, w jaki WAF reaguje na zdarzenie. Reguły niestandardowe są przetwarzane przed regułami zarządzanymi, ale mogą zawierać więcej niż jeden warunek, a ich akcje mogą być [dozwolone, odrzucanie, rejestrowanie lub przekierowywanie](afds-overview.md#waf-actions). W przypadku dopasowania reguły aparat WAF zatrzyma przetwarzanie. Oznacza to, że inne niestandardowe reguły o niższym priorytecie i reguły zarządzane nie są już wykonywane.

W poniższym przykładzie została utworzona reguła niestandardowa o dwóch warunkach. Pierwszy warunek poszukuje `comment` wartości w treści żądania. Drugi warunek poszukuje `/api/Feedbacks/` wartości w identyfikatorze URI żądania.

Korzystanie z reguły niestandardowej pozwala być najbardziej szczegółowym warunkiem dostrajania reguł WAF i w celu poradzenia z fałszywie dodatnimi. W tym przypadku firma Microsoft nie podejmuje działania wyłącznie na podstawie `comment` wartości treści żądania, która może istnieć w wielu witrynach lub aplikacjach objętych tymi samymi zasadami WAFymi. Dzięki dołączeniu innego warunku do dopasowania do określonego identyfikatora URI żądania `/api/Feedbacks/` upewnij się, że ta reguła niestandardowa dotyczy tego jawnego przypadku użycia, który zbadane. Gwarantuje to, że w przypadku przeprowadzenia tego samego ataku w odniesieniu do różnych warunków nadal będzie on sprawdzany i uniemożliwiany przez aparat WAF.

![Dziennik](../media/waf-front-door-tuning/custom-rule.png)

Podczas eksplorowania dziennika można zobaczyć, że `ruleName_s` pole zawiera nazwę nadaną dla utworzonej reguły niestandardowej: `redirectcomment` . W `action_s` polu można zobaczyć, że wykonano akcję *przekierowania* dla tego zdarzenia. W `details_matches_s` polu można zobaczyć szczegóły obu warunków.

### <a name="disabling-rules"></a>Wyłączanie reguł

Innym sposobem na odliczanie fałszywych wyników jest wyłączenie reguły dopasowanej do danych wejściowych WAF uważany za złośliwy. Po przeanalizowaniu dzienników WAF i zawężaniu reguły do 942110 można wyłączyć ją w Azure Portal. Zobacz [Dostosowywanie reguł zapory aplikacji sieci Web przy użyciu Azure Portal](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules).
 
Wyłączenie reguły jest korzystne, gdy masz pewność, że wszystkie żądania, które spełniają określony warunek, są w rzeczywistości legalnymi żądaniami lub jeśli masz pewność, że reguła po prostu nie ma zastosowania do Twojego środowiska (na przykład wyłączenie reguły iniekcji SQL z powodu braku instrukcji SQL). 
 
Jednak wyłączenie reguły jest ustawieniem globalnym, które ma zastosowanie do wszystkich hostów frontonu skojarzonych z zasadami WAFymi. Po wybraniu opcji wyłączenia reguły mogą występować luki w zabezpieczeniach, które nie zostały ujawnione lub wykryją inne hosty frontonu skojarzone z zasadami WAFymi.
 
Jeśli chcesz użyć Azure PowerShell, aby wyłączyć regułę zarządzaną, zobacz [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject?preserve-view=true&view=azps-4.7.0) dokumentację obiektu. Jeśli chcesz użyć interfejsu wiersza polecenia platformy Azure, zapoznaj się z [`az network front-door waf-policy managed-rules override`](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/override?preserve-view=true&view=azure-cli-latest) dokumentacją.

![Reguły WAF](../media/waf-front-door-tuning/waf-rules.png)

> [!TIP]
> Dobrym pomysłem jest udokumentowanie wszelkich zmian wprowadzonych w zasadach WAF. Dołącz przykładowe żądania do zilustrowania fałszywego wykrywania pozytywnego i jasno Wyjaśnij, dlaczego dodano regułę niestandardową, wyłączono regułę lub zestaw reguł lub dodaliśmy wyjątek. Ta dokumentacja może być przydatna w przypadku ponownego projektowania aplikacji w przyszłości i sprawdzenia, czy zmiany są nadal ważne. Może również pomóc w ewentualnym inspekcji lub zawieszeniu, dlaczego zasady WAF zostały ponownie skonfigurowane przy użyciu ustawień domyślnych.

## <a name="finding-request-fields"></a>Znajdowanie pól żądania

Korzystając z serwera proxy przeglądarki, takiego jak [programu Fiddler](https://www.telerik.com/fiddler), można sprawdzić poszczególne żądania i określić, które określone pola strony sieci Web są wywoływane. Jest to przydatne, gdy konieczne jest wykluczenie niektórych pól z inspekcji przy użyciu list wykluczeń w WAF.

### <a name="finding-request-attribute-names"></a>Znajdowanie nazw atrybutów żądania
 
W tym przykładzie można zobaczyć pole, w którym `1=1` wprowadzono ciąg `comment` . Te dane zostały przesłane w treści żądania POST.

![Żądanie programu Fiddler pokazujące treść](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

Jest to pole, które można wykluczyć. Aby dowiedzieć się więcej na temat list wykluczeń, zobacz [listy wykluczeń zapory aplikacji sieci Web](./waf-front-door-exclusion.md). Ocenę można wyłączyć w tym przypadku, konfigurując następujące wykluczenie:

![Reguła wykluczania](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

Możesz również sprawdzić dzienniki zapory, aby uzyskać informacje, które należy dodać do listy wykluczeń. Aby włączyć rejestrowanie, zobacz [monitorowanie metryk i dzienników w usłudze Azure front-drzwi](./waf-front-door-monitor.md).

Sprawdź, czy w pliku dziennika zapory jest `PT1H.json` godzina, o której wystąpiło żądanie, które chcesz sprawdzić. `PT1H.json` pliki są dostępne w kontenerach konta magazynu, w których `FrontDoorWebApplicationFirewallLog` `FrontDoorAccessLog` są przechowywane dzienniki diagnostyczne i.

W tym przykładzie można zobaczyć regułę, która zablokowała żądanie (z tym samym odwołaniem do transakcji) i wystąpiła w dokładnie tym samym czasie:

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

Mając wiedzę o sposobie działania zestawów reguł zarządzanych przez platformę Azure (zobacz [Zapora aplikacji sieci Web na platformie Azure](afds-overview.md)), wiadomo, że reguła z *akcją: Block* blokuje się na podstawie danych dopasowanych w treści żądania. Można zobaczyć szczegóły, które pasują do wzorca ( `1=1` ), a pole ma nazwę `comment` . Postępuj zgodnie z tymi samymi poprzednimi krokami, aby wykluczyć treść żądania, która zawiera `comment` .

### <a name="finding-request-header-names"></a>Znajdowanie nazw nagłówków żądań

Programu Fiddler to przydatne narzędzie ponownie, aby znaleźć nazwy nagłówka żądania. Na poniższym zrzucie ekranu widoczne są nagłówki tego żądania GET, w tym typ zawartości, agent użytkownika i tak dalej. Można również użyć nagłówków żądań do tworzenia wykluczeń i reguł niestandardowych w WAF.

![Żądanie programu Fiddler z nagłówkiem](../media/waf-front-door-tuning/fiddler-request-header-name.png)

Innym sposobem wyświetlania nagłówków żądań i odpowiedzi jest zaszukiwanie w narzędziach deweloperskich przeglądarki, takich jak Edge lub Chrome. Możesz nacisnąć klawisz F12 lub kliknąć prawym przyciskiem myszy > **sprawdzić**  ->  **Narzędzia deweloperskie** i wybrać kartę **Sieć** . Załaduj stronę sieci Web, a następnie kliknij żądanie, które chcesz sprawdzić.

![Żądanie inspektora sieci](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>Znajdowanie nazw plików cookie żądania

Jeśli żądanie zawiera pliki cookie, można wybrać kartę pliki cookie, aby wyświetlić je w programu Fiddler. Informacje o pliku cookie mogą również służyć do tworzenia wykluczeń lub reguł niestandardowych w programie WAF.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zaporze aplikacji sieci Web platformy Azure](../overview.md).
- Dowiedz się, jak [utworzyć usługę Front Door](../../frontdoor/quickstart-create-front-door.md).
