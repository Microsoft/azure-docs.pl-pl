---
title: Listy wykluczeń zapory aplikacji internetowej w Azure Front Door — Azure Portal
description: Ten artykuł zawiera informacje na temat konfiguracji list wykluczeń na platformie Azure Front przy użyciu Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 83baf03c414d9b0f7acb6a93db03794a539a3c58
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107860837"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Web Application Firewall (WAF) z listami wykluczeń Front Door Service 

Czasami Web Application Firewall (WAF) może zablokować żądanie, na które chcesz zezwolić dla aplikacji. Na przykład usługa Active Directory wstawia tokeny używane do uwierzytelniania. Tokeny te mogą zawierać znaki specjalne, które mogą wyzwolić wynik fałszywie dodatni z reguł WAF. Listy wykluczeń WAF umożliwiają pominięcie niektórych atrybutów żądań w ocenie WAF.  Listę wykluczeń można skonfigurować przy użyciu programu  [PowerShell,](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject)interfejsu wiersza polecenia platformy [Azure,](/cli/azure/network/front-door/waf-policy/managed-rules/exclusion#az_network_front_door_waf_policy_managed_rules_exclusion_add)interfejsu [API REST](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)lub Azure Portal. W poniższym przykładzie pokazano Azure Portal konfiguracji. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Konfigurowanie list wykluczeń przy użyciu Azure Portal
**Zarządzanie wykluczeniami jest** dostępne w portalu WAF w obszarze **Reguły zarządzane**

![Zarządzanie wykluczeniami ](../media/waf-front-door-exclusion/exclusion1.png)
 ![ Zarządzanie exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 Przykładowa lista wykluczeń: ![ Zarządzanie exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

W tym przykładzie nie jest wykluczana wartość w *polu nagłówka* użytkownika. Prawidłowe żądanie może zawierać *pole użytkownika,* które zawiera ciąg, który wyzwala regułę iniekcji SQL. W takim przypadku *możesz wykluczyć* parametr użytkownika, aby reguła WAF nie oceniała niczego w polu.

Następujące atrybuty można dodać do list wykluczeń według nazwy. Wartości pól, których używasz, nie są oceniane względem reguł WAF, ale ich nazwy są oceniane. Listy wykluczeń usuwają inspekcję wartości pola.

* Nazwa nagłówka żądania
* Nazwa pliku cookie żądania
* Nazwa args ciągu zapytania
* Nazwa wpisów treści żądania

Można określić dokładne dopasowanie atrybutu nagłówka żądania, treści, pliku cookie lub ciągu zapytania.  Możesz też opcjonalnie określić częściowe dopasowania. Obsługiwane kryteria dopasowania to następujące operatory:

- **Równa się:** ten operator służy do dokładnego dopasowania. Aby na przykład wybrać nagłówek o nazwie **bearerToken,** użyj operatora równości z selektorem ustawionym na **bearerToken**.
- **Rozpoczyna się od**: ten operator dopasowuje wszystkie pola, które zaczynają się od określonej wartości selektora.
- **Kończy się na**: ten operator dopasowuje wszystkie pola żądania, które kończą się określoną wartością selektora.
- **Zawiera**: ten operator dopasowuje wszystkie pola żądania, które zawierają określoną wartość selektora.
- **Równa się dowolnemu:** ten operator pasuje do wszystkich pól żądania. * to wartość selektora.

W nazwach nagłówków i plików cookie nie jest uwzględniania ich litera.

Jeśli wartość nagłówka, wartość pliku cookie, wartość argumentu post lub wartość argumentu zapytania generuje wyniki fałszywie dodatnie dla niektórych reguł, możesz wykluczyć część żądania z uwzględnienia przez regułę:


|MatchVariableName z dzienników aplikacji sieci Szkieletowej  |Wykluczanie reguł w portalu  |
|---------|---------|
|CookieValue:SOME_NAME        |Żądanie nazwy pliku cookie jest równe SOME_NAME|
|HeaderValue:SOME_NAME        |Nazwa nagłówka żądania jest równa SOME_NAME|
|PostParamValue:SOME_NAME     |Treść żądania po nazwie args jest równa SOME_NAME|
|QueryParamValue:SOME_NAME    |Nazwa args ciągu zapytania jest równa SOME_NAME|


Obecnie obsługujemy tylko wykluczenia reguł dla powyższych nazw matchVariableName w ich dziennikach aplikacji sieci WAF. W przypadku innych nazw matchVariableName należy wyłączyć reguły, które dają wyniki fałszywie dodatnie, lub utworzyć regułę niestandardową, która jawnie zezwala na te żądania. W szczególności, gdy matchVariableName to CookieName, HeaderName, PostParamName lub QueryParamName, oznacza to, że sama nazwa wyzwala regułę. Wykluczenie reguły nie obsługuje obecnie tych nazw matchVariableName.


Jeśli wykluczysz wpis treści żądania o nazwie *FOO,* żadna reguła nie powinna pokazywać parametru PostParamValue:FOO jako parametru matchVariableName w dziennikach aplikacji sieci Szkieletowej. Jednak nadal może być widać regułę z parametrem matchVariableName InitialBodyContents, która odpowiada wartości parametru POST FOO, ponieważ wartości parametru post są częścią właściwości InitialBodyContents.

Listy wykluczeń można stosować do wszystkich reguł w zarządzanym zestawie reguł, do reguł dla określonej grupy reguł lub do jednej reguły, jak pokazano w poprzednim przykładzie.

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>Definiowanie wykluczeń na podstawie Web Application Firewall dzienników
 [Azure Web Application Firewall monitorowania i rejestrowania](waf-front-door-monitor.md) pokazuje dopasowane szczegóły zablokowanego żądania. Jeśli wartość nagłówka, wartość pliku cookie, wartość argumentu post lub wartość argumentu zapytania generuje wyniki fałszywie dodatnie dla niektórych reguł, możesz wykluczyć część żądania z rozważania przez regułę. W poniższej tabeli przedstawiono przykładowe wartości z dzienników WAF i odpowiednie warunki wykluczenia.

|matchVariableName z dzienników aplikacji sieci Szkieletowej    |Wykluczanie reguł w portalu|
|--------|------|
|CookieValue:SOME_NAME  |Nazwa pliku cookie żądania jest równa SOME_NAME|
|HeaderValue:SOME_NAME  |Nazwa nagłówka żądania jest równa SOME_NAME|
|PostParamValue:SOME_NAME|  Treść żądania po nazwie args jest równa SOME_NAME|
|QueryParamValue:SOME_NAME| Nazwa args ciągu zapytania jest równa SOME_NAME|


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu ustawień aplikacji internetowej dowiedz się, jak wyświetlać dzienniki aplikacji internetowej. Aby uzyskać więcej informacji, zobacz [Front Door diagnostyki](../afds/waf-front-door-monitor.md).