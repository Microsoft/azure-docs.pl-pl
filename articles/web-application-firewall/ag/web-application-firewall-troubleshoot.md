---
title: Rozwiązywanie problemów — Zapora aplikacji sieci Web platformy Azure
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z zaporą aplikacji sieci Web (WAF) dla platformy Azure Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 483d261a8cc107d01cfb7a405eac43667d7efcc6
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131840"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Rozwiązywanie problemów z zaporą aplikacji sieci Web (WAF) dla platformy Azure Application Gateway

Istnieje kilka rzeczy, które można wykonać, jeśli żądania, które powinny zostać przekazane przez zaporę aplikacji sieci Web (WAF), są blokowane.

Najpierw upewnij się, że zapoznaj się z [omówieniem WAF](ag-overview.md) i dokumentami [konfiguracyjnymi WAF](application-gateway-waf-configuration.md) . Upewnij się również, że włączono [monitorowanie WAF](../../application-gateway/application-gateway-diagnostics.md) w tym artykule wyjaśniono, jak działają funkcje WAF, jak działa zestaw reguł WAF oraz jak uzyskiwać dostęp do dzienników WAF.

Zestaw reguł OWASP został zaprojektowany tak, aby był bardzo rygorystyczny i mógł zostać dostosowany do określonych potrzeb aplikacji lub organizacji przy użyciu WAF. Jest ona całkowicie normalna i faktycznie oczekiwana w wielu przypadkach, aby utworzyć wykluczenia, reguły niestandardowe, a nawet wyłączyć reguły, które mogą powodować problemy lub fałszywie dodatnie. Zasady dla poszczególnych witryn i identyfikatorów URI umożliwiają wprowadzanie tych zmian tylko do określonych witryn/identyfikatorów URI, dlatego wszelkie zmiany nie powinny mieć wpływu na inne lokacje, które mogą nie działać w tych samych problemach. 

## <a name="understanding-waf-logs"></a>Omówienie dzienników WAF

W dziennikach WAF są wyświetlane wszystkie żądania, które są dopasowane lub blokowane przez WAF. Jest to Księga wszystkich ocenionych żądań, które są dopasowane lub blokowane. Jeśli zauważysz, że WAF blokuje żądanie, które nie powinno (fałszywie dodatni), można wykonać kilka czynności. Po pierwsze, Zawęź i Znajdź konkretne żądanie. Przejrzyj dzienniki, aby znaleźć konkretny identyfikator URI, sygnaturę czasową lub identyfikator transakcji żądania. Po znalezieniu skojarzonych wpisów dziennika można zacząć działać na fałszywie dodatnich.

Załóżmy na przykład, że masz legalny ruch zawierający ciąg *1 = 1* , który chcesz przekazać przez WAF. Jeśli spróbujesz ponowić żądanie, WAF blokuje ruch, który zawiera *1 = 1* ciąg w dowolnym parametrze lub polu. Jest to ciąg często skojarzony z atakiem iniekcji SQL. Możesz przejrzeć dzienniki i sprawdzić sygnaturę czasową żądania oraz reguły, które zostały zablokowane/dopasowane.

W poniższym przykładzie można zobaczyć, że w tym samym żądaniu wyzwalane są cztery reguły (przy użyciu pola TransactionId). Pierwszy z nich jest zgodny ze względu na to, że użytkownik użył adresu URL liczbowego/IP dla żądania, co zwiększa wynik anomalii o trzy, ponieważ jest to ostrzeżenie. Kolejną zgodną regułą jest 942130, która jest tą, której szukasz. W polu można zobaczyć *1 = 1* `details.data` . Spowoduje to dalsze zwiększenie wyników anomalii o trzy ponownie, ponieważ jest to ostrzeżenie. Ogólnie rzecz biorąc, Każda reguła, która ma **dopasowaną** akcję, zwiększa wynik anomalii, a w tym momencie wynik anomalii będzie 6. Aby uzyskać więcej informacji, zobacz [tryb oceniania anomalii](ag-overview.md#anomaly-scoring-mode).

Ostatnie dwa wpisy dziennika pokazują, że żądanie zostało zablokowane, ponieważ wynik anomalii był wystarczająco duży. Te wpisy mają inną akcję niż pozostałe dwa. Pokazują one, że rzeczywiście *zablokowały* żądanie. Te reguły są obowiązkowe i nie można ich wyłączyć. Nie należy traktować ich jako reguł, ale jest to infrastruktura podstawowa WAF wewnętrznych.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>Naprawianie fałszywych dodatnich

Korzystając z tych informacji, a wiedzą, że reguła 942130 jest taka, która pasowała do *1 = 1* ciągu, możesz wykonać kilka rzeczy, aby zatrzymać ten ruch od blokowania ruchu:

- Korzystanie z listy wykluczeń

   Aby uzyskać więcej informacji na temat list wykluczeń, zobacz [Konfiguracja WAF](application-gateway-waf-configuration.md#waf-exclusion-lists) .
- Wyłącz regułę.

### <a name="using-an-exclusion-list"></a>Korzystanie z listy wykluczeń

Aby podjąć świadomą decyzję dotyczącą obsługi fałszywych wartości dodatnich, ważne jest zapoznanie się z technologiami używanymi przez aplikację. Załóżmy na przykład, że nie ma programu SQL Server w stosie technologii i otrzymujesz fałszywe dodatnie powiązane z tymi regułami. Wyłączenie tych reguł nie musi osłabiać zabezpieczeń.

Jedną z zalet korzystania z listy wykluczeń jest wyłączenie tylko określonej części żądania. Oznacza to jednak, że określone wykluczenie ma zastosowanie do całego ruchu przechodzącego przez WAF, ponieważ jest to ustawienie globalne. Na przykład może to prowadzić do problemu, jeśli *1 = 1* to prawidłowe żądanie w treści dla określonej aplikacji, ale nie dla innych. Kolejną zaletą jest to, że można wybrać między treścią, nagłówkami i plikami cookie, które mają być wykluczone w przypadku spełnienia określonego warunku, w przeciwieństwie do wykluczenia całego żądania.

Sporadycznie istnieją przypadki, w których określone parametry są przenoszone do WAF w sposób, który może nie być intuicyjny. Na przykład istnieje token, który jest przesyłany podczas uwierzytelniania przy użyciu Azure Active Directory. Ten token, *__RequestVerificationToken*, zazwyczaj otrzymuje się jako plik cookie żądania. Jednak w niektórych przypadkach, gdy pliki cookie są wyłączone, ten token jest również przenoszona jako atrybut żądania lub "ARG". Jeśli tak się stanie, należy się upewnić, że *__RequestVerificationToken* zostanie dodany do listy wykluczeń jako **nazwa atrybutu żądania** .

![Wykluczenia](../media/web-application-firewall-troubleshoot/exclusion-list.png)

W tym przykładzie chcesz wykluczyć **nazwę atrybutu żądania** , która jest równa *Tekst1*. Jest to widoczne, ponieważ w dziennikach zapory można zobaczyć nazwę atrybutu: **dane: dopasowane dane: 1 = 1 znaleziono w args: Tekst1:1 = 1**. Atrybut jest **Tekst1**. Możesz również znaleźć nazwę tego atrybutu kilka innych sposobów, zobacz [Znajdowanie nazw atrybutów żądania](#finding-request-attribute-names).

![Listy wykluczeń WAF](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Wyłączanie reguł

Innym sposobem na odliczanie fałszywych wyników jest wyłączenie reguły dopasowanej do danych wejściowych WAF uważany za złośliwy. Po przeanalizowaniu dzienników WAF i zawężaniu reguły do 942130 można wyłączyć ją w Azure Portal. Zobacz [Dostosowywanie reguł zapory aplikacji internetowych za pomocą Azure Portal](application-gateway-customize-waf-rules-portal.md).

Jedną z zalet wyłączenia reguły jest to, że jeśli wiesz, że cały ruch, który zawiera określony warunek, który normalnie będzie zablokowany, jest prawidłowym ruchem, możesz wyłączyć tę regułę dla całego WAF. Jeśli jednak tylko prawidłowy ruch w konkretnym przypadku użycia, należy otworzyć lukę, wyłączając tę regułę dla całego WAF, ponieważ jest to ustawienie globalne.

Jeśli chcesz używać Azure PowerShell, zobacz [Dostosowywanie reguł zapory aplikacji sieci Web za pomocą programu PowerShell](application-gateway-customize-waf-rules-powershell.md). Jeśli chcesz korzystać z interfejsu wiersza polecenia platformy Azure, zobacz [Dostosowywanie reguł zapory aplikacji internetowych za pomocą interfejsu wiersza polecenia platformy Azure](application-gateway-customize-waf-rules-cli.md).

![Reguły WAF](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Znajdowanie nazw atrybutów żądania

Za pomocą [programu Fiddler](https://www.telerik.com/fiddler)można sprawdzić poszczególne żądania i określić, które określone pola strony sieci Web są wywoływane. Może to pomóc wykluczyć niektóre pola z inspekcji przy użyciu list wykluczeń.

W tym przykładzie widać, że pole, w którym wprowadzono ciąg *1 = 1* , nosi nazwę **Tekst1**.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-1.png" alt-text="Zrzut ekranu przedstawiający debuger sieci Web w programie Progress Telerik programu Fiddler. Na karcie RAW 1 = 1 jest widoczny po nazwie Tekst1." border="false":::

Jest to pole, które można wykluczyć. Aby dowiedzieć się więcej na temat list wykluczeń, zobacz [limity rozmiaru żądań zapory aplikacji sieci Web i listy wykluczeń](application-gateway-waf-configuration.md#waf-exclusion-lists). Ocenę można wyłączyć w tym przypadku, konfigurując następujące wykluczenie:

![WAF wykluczeń](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

Możesz również sprawdzić dzienniki zapory, aby uzyskać informacje, które należy dodać do listy wykluczeń. Aby włączyć rejestrowanie, zobacz temat [kondycja zaplecza, dzienniki zasobów i metryki dla Application Gateway](../../application-gateway/application-gateway-diagnostics.md).

Sprawdź dziennik zapory i Wyświetl PT1H.jsw pliku przez godzinę, o której wystąpiło żądanie, które chcesz sprawdzić.

W tym przykładzie można zobaczyć, że masz cztery reguły z tym samym TransactionID i że wszystkie wystąpiły w tym samym czasie:

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

Wiedząc, jak działa zestaw reguł aplikacji KSR i czy zestaw reguł programu KSR 3,0 współpracuje z systemem oceny anomalii (zobacz [Zapora aplikacji sieci Web dla platformy Azure Application Gateway](ag-overview.md)), że wiesz, że dwie dolne reguły z **akcją: zablokowane** są blokowane na podstawie łącznego wyniku anomalii. Zasady, na których należy się skoncentrować, to dwie pierwsze.

Pierwszy wpis jest rejestrowany, ponieważ użytkownik użył numerycznego adresu IP do przejścia do Application Gateway, który można zignorować w tym przypadku.

Druga z nich (reguła 942130) jest interesująca. Można zobaczyć szczegóły, które pasują do wzorca (1 = 1), a pole nosi nazwę **Tekst1**. Wykonaj te same poprzednie kroki, aby wykluczyć **nazwę atrybutu żądania** **równą** **1 = 1**.

## <a name="finding-request-header-names"></a>Znajdowanie nazw nagłówków żądań

Programu Fiddler to przydatne narzędzie ponownie, aby znaleźć nazwy nagłówka żądania. Na poniższym zrzucie ekranu widoczne są nagłówki tego żądania GET, w tym *Typ zawartości*, *agent użytkownika*i tak dalej.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-2.png" alt-text="Zrzut ekranu przedstawiający debuger sieci Web w programie Progress Telerik programu Fiddler. Karta RAW zawiera listę szczegółów nagłówka żądania, takich jak połączenie, typ zawartości i agent użytkownika." border="false":::

Innym sposobem wyświetlania nagłówków żądań i odpowiedzi jest zaszukiwanie w narzędziach deweloperskich programu Chrome. Możesz nacisnąć klawisz F12 lub kliknąć prawym przyciskiem myszy > **sprawdzić**  ->  **Narzędzia deweloperskie**i wybrać kartę **Sieć** . Załaduj stronę sieci Web, a następnie kliknij żądanie, które chcesz sprawdzić.

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Znajdowanie nazw plików cookie żądania

Jeśli żądanie zawiera pliki cookie, można wybrać kartę **pliki cookie** , aby wyświetlić je w programu Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Ogranicz parametry globalne, aby wyeliminować fałszywe pozytywy

- Wyłącz inspekcję treści żądania

   Ustawienie opcji **Przeprowadź inspekcję treści żądania** jest wyłączone, a treść żądania całego ruchu nie będzie oceniana przez WAF. Może to być przydatne, Jeśli wiesz, że treści żądania nie są złośliwe dla Twojej aplikacji.

   Wyłączenie tej opcji spowoduje, że tylko treść żądania nie jest sprawdzana. Nagłówki i pliki cookie nadal są sprawdzane, chyba że poszczególne z nich są wykluczone przy użyciu funkcji listy wykluczeń.

- Limity rozmiaru plików

   Ograniczając rozmiar pliku WAF, ograniczasz możliwość ataku na serwery sieci Web. Dzięki umożliwieniu przekazywania dużych plików ryzyko przeciążenia zaplecza jest zwiększane. Aby zapobiec atakom, można ograniczyć rozmiar pliku do normalnego przypadku użycia aplikacji.

   > [!NOTE]
   > Jeśli wiesz, że aplikacja nigdy nie będzie potrzebować przekazywania pliku powyżej danego rozmiaru, możesz ograniczyć tę wartość, ustawiając limit.

## <a name="firewall-metrics-waf_v1-only"></a>Metryki zapory (tylko WAF_v1)

W przypadku zapór aplikacji sieci Web w wersji 1 następujące metryki są teraz dostępne w portalu: 

1. Liczba zablokowanych żądań zapory aplikacji sieci Web
2. Blokada reguły zapory aplikacji sieci Web zlicza wszystkie reguły, które zostały dopasowane **, a** żądanie zostało zablokowane.
3. Całkowita dystrybucja reguł przez zaporę aplikacji sieci Web wszystkie reguły, które zostały dopasowane podczas oceny
     
Aby włączyć metryki, wybierz kartę **metryki** w portalu, a następnie wybierz jedną z trzech metryk.

## <a name="next-steps"></a>Następne kroki

Zobacz [jak skonfigurować zaporę aplikacji sieci Web na Application Gateway](tutorial-restrict-web-traffic-powershell.md).
