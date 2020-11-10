---
title: Listy wykluczeń zapory aplikacji sieci Web na platformie Azure — Azure Portal
description: Ten artykuł zawiera informacje na temat konfiguracji list wykluczeń na platformie Azure z systemem Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 943124982fe1f2ccf142bb9161ec8ada07e63df5
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444983"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Zapora aplikacji sieci Web (WAF) z listami wykluczeń usługi front-drzwi 

Czasami zapora aplikacji sieci Web (WAF) może blokować żądanie, które ma być dozwolone dla aplikacji. Na przykład Active Directory wstawia tokeny, które są używane do uwierzytelniania. Te tokeny mogą zawierać znaki specjalne, które mogą wyzwolić wynik fałszywie dodatni z reguł WAF. Listy wykluczeń WAF umożliwiają pominięcie niektórych atrybutów żądania z oceny WAF.  Listę wykluczeń można skonfigurować przy użyciu  [programu PowerShell](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0), interfejsu [wiersza polecenia platformy Azure lub usługi](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add) [API REST](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)lub Azure Portal. W poniższym przykładzie przedstawiono konfigurację Azure Portal. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Konfigurowanie list wykluczeń przy użyciu Azure Portal
**Zarządzanie wykluczeniami** jest dostępne z portalu WAF w ramach **zarządzanych reguł**

![Zarządzaj ](../media/waf-front-door-exclusion/exclusion1.png)
 ![ exclusion_add zarządzanie wykluczeniami](../media/waf-front-door-exclusion/exclusion2.png)

 Przykładowa lista wykluczeń: ![ zarządzanie exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

Ten przykład wyklucza wartość w polu nagłówka *użytkownika* . Prawidłowe żądanie może zawierać pole *użytkownika* zawierające ciąg, który wyzwala regułę iniekcji SQL. W tym przypadku można wykluczyć parametr *użytkownika* , aby reguła WAF nie była szacowana w tym polu.

Następujące atrybuty można dodać do listy wykluczeń według nazwy. Wartości pól, których używasz, nie są oceniane względem reguł WAF, ale ich nazwy są oceniane. Wykluczenie zawiera listę usuwania inspekcji wartości pola.

* Nazwa nagłówka żądania
* Nazwa pliku cookie żądania
* Nazwa argumentów ciągu zapytania
* Nazwa argumentów post treści żądania

Można określić dokładny nagłówek żądania, treść, plik cookie lub dopasowanie atrybutu ciągu zapytania.  Alternatywnie możesz określić częściowe dopasowania. Następujące operatory są obsługiwanymi kryteriami dopasowania:

- **Equals** : Ten operator jest używany do dokładnego dopasowania. Na przykład, aby wybrać nagłówek o nazwie **bearerToken** , użyj operatora Equals z selektorem ustawionym jako **bearerToken**.
- **Rozpoczyna się od** : Ten operator dopasowuje wszystkie pola, które zaczynają się od określonej wartości selektora.
- **Kończy się na** : Ten operator dopasowuje wszystkie pola żądań, które kończą się określoną wartością selektora.
- **Zawiera** : Ten operator dopasowuje wszystkie pola żądania, które zawierają określoną wartość selektora.
- **Równa się any** : Ten operator dopasowuje wszystkie pola żądania. * jest wartością selektora.

Nazwy nagłówków i plików cookie nie uwzględniają wielkości liter.

Jeśli wartość nagłówka, wartość cookie, wartość argumentu post lub wartość argumentu zapytania będą zwracać wynik fałszywie dodatnie dla niektórych reguł, można wykluczyć tę część żądania przez regułę:


|matchVariableName z dzienników WAF  |Wykluczanie reguły w portalu  |
|---------|---------|
|CookieValue: SOME_NAME        |Nazwa pliku cookie żądania jest równa SOME_NAME|
|HeaderValue: SOME_NAME        |Nazwa nagłówka żądania jest równa SOME_NAME|
|PostParamValue: SOME_NAME     |Nazwa argumentów post treści żądania jest równa SOME_NAME|
|QueryParamValue: SOME_NAME    |Nazwa argumentów ciągu zapytania jest równa SOME_NAME|


Obecnie obsługujemy wykluczenia z reguł dla powyższych matchVariableNames w swoich dziennikach WAF. W przypadku innych matchVariableNames należy wyłączyć reguły, które dają fałszywie dodatnie lub utworzyć regułę niestandardową, która jawnie zezwala na te żądania. W szczególności, gdy matchVariableName ma wartość CookieName, headerName, PostParamName lub QueryParamName, oznacza to, że sama nazwa wyzwala regułę. Wyłączenie reguły nie ma w tej chwili żadnego wsparcia dla tych matchVariableNames.


Jeśli wykluczasz argumenty post treści żądania o nazwie *foo* , żadna reguła nie powinna wyświetlać POSTPARAMVALUE: foo jako matchVariableName w dziennikach WAF. Jednak nadal może zostać wyświetlona reguła z matchVariableName InitialBodyContents, która pasuje do wartości parametru post FOO, ponieważ wartości parametru post są częścią InitialBodyContents.

Listy wykluczeń można stosować do wszystkich reguł w ramach zarządzanego zestawu reguł, do reguł dla określonej grupy reguł lub do jednej reguły, jak pokazano w poprzednim przykładzie.

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>Definiowanie wykluczeń na podstawie dzienników zapory aplikacji sieci Web
 [Monitorowanie i rejestrowanie zapory aplikacji sieci Web platformy Azure](waf-front-door-monitor.md) przedstawia dopasowane szczegóły zablokowanego żądania. Jeśli wartość nagłówka, wartość pliku cookie, wartość argumentu post lub wartość argumentu zapytania są wynikami fałszywie dodatnich dla niektórych reguł, można wykluczać tę część żądania z zasady. W poniższej tabeli przedstawiono przykładowe wartości z dzienników WAF i odpowiadające im warunki wykluczenia.

|matchVariableName z dzienników WAF    |Wykluczanie reguły w portalu|
|--------|------|
|CookieValue: SOME_NAME  |Nazwa pliku cookie żądania jest równa SOME_NAME|
|HeaderValue: SOME_NAME  |Nazwa nagłówka żądania jest równa SOME_NAME|
|PostParamValue: SOME_NAME|  Nazwa argumentów post treści żądania jest równa SOME_NAME|
|QueryParamValue: SOME_NAME| Nazwa argumentów ciągu zapytania jest równa SOME_NAME|


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu ustawień WAF, Dowiedz się, jak wyświetlać dzienniki WAF. Aby uzyskać więcej informacji, zobacz [Diagnostyka z przodu](../afds/waf-front-door-monitor.md).
