---
title: Niestandardowa reguła zapory aplikacji sieci Web dla drzwi frontonu platformy Azure
description: Dowiedz się, jak używać niestandardowych reguł zapory aplikacji sieci Web (WAF), chroniąc aplikacje sieci Web przed złośliwymi atakami.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "79475828"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Reguły niestandardowe dla zapory aplikacji sieci Web z usługami frontonu platformy Azure

Zapora aplikacji sieci Web (WAF) platformy Azure z przednimi drzwiami umożliwia kontrolowanie dostępu do aplikacji sieci Web na podstawie zdefiniowanych warunków. Niestandardowa reguła WAF składa się z numeru priorytetu, typu reguły, warunków dopasowania i akcji. Istnieją dwa typy reguł niestandardowych: reguły dopasowania i reguły limitów szybkości. Reguła dopasowania kontroluje dostęp na podstawie zestawu zgodnych warunków, natomiast reguła limitu szybkości kontroluje dostęp na podstawie pasujących warunków i stawek żądań przychodzących. Można wyłączyć regułę niestandardową, aby zapobiec jej ocenie, ale nadal zachować konfigurację. 

## <a name="priority-match-conditions-and-action-types"></a>Priorytet, warunki dopasowania i typy akcji

Można kontrolować dostęp za pomocą niestandardowej reguły WAf, która definiuje numer priorytetu, typ reguły, tablicę warunków dopasowania i akcję. 

- **Priorytet:** jest unikatową liczbą całkowitą opisującą kolejność oceny reguł WAF. Reguły o niższych wartościach priorytetów są oceniane przed regułami o wyższych wartościach. Numery priorytetów muszą być unikatowe wśród wszystkich reguł niestandardowych.

- **Akcja:** definiuje sposób kierowania żądania w przypadku dopasowania reguły WAF. Można wybrać jedną z poniższych akcji do zastosowania, gdy żądanie jest zgodne z regułą niestandardową.

    - *Allow* -WAF przekazuje żądanie do zaplecza, rejestruje wpis w dziennikach WAF i kończy pracę.
    - Żądanie *blokowania* jest blokowane, WAF wysyła odpowiedź do klienta bez przesyłania dalej żądania do zaplecza. WAF rejestruje wpis w dziennikach WAF.
    - *Log* -WAF rejestruje wpis w dziennikach WAF i kontynuuje ocenę następnej reguły.
    - *Redirect* -WAF przekierowuje żądanie do określonego identyfikatora URI, rejestruje wpis w dziennikach WAF i kończy pracę.

- **Warunek dopasowania:** definiuje zmienną dopasowania, operatora i wartość Match. Każda reguła może zawierać wiele warunków dopasowywania. Warunek dopasowania może opierać się na lokalizacji geograficznej, adresie IP klienta (CIDR), rozmiarze lub dopasowaniu ciągu. Dopasowanie ciągu może odnosić się do listy zmiennych dopasowywania.
  - **Zmienna dopasowania:**
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - Elemencie requestbody
    - Pliki cookie
  - **Zakład**
    - Any: jest często używany do definiowania akcji domyślnej, jeśli nie są spełnione żadne reguły. Any jest operatorem Match ALL.
    - Równe
    - Contains
    - LessThan: ograniczenie rozmiaru
    - GreaterThan: ograniczenie rozmiaru
    - LessThanOrEqual: ograniczenie rozmiaru
    - GreaterThanOrEqual: ograniczenie rozmiaru
    - BeginsWith
    - EndsWith
    - Wyrażeń
  
  - **Wyrażenie regularne** nie obsługuje następujących operacji: 
    - Odwołania wsteczne i przechwytywanie podwyrażeń
    - Dowolne potwierdzenia o zerowej szerokości
    - Odwołania podprocedury i wzorce cykliczne
    - Wzorce warunkowe
    - Zlecenia kontroli wycofywania
    - \C jednobajtowa dyrektywa
    - Dyrektywa dopasowania nowego wiersza
    - \K początek dyrektywy resetowania dopasowania
    - Objaśnienia i kod osadzony
    - Grupowanie niepodzielne i Kwantyfikatory Possessive

  - **Negate [opcjonalnie]:** Warunek *Negate* można ustawić na wartość true, jeśli wynik warunku powinien być negacji.
      
  - **Przekształć [opcjonalnie]:** Lista ciągów z nazwami transformacji, które należy wykonać przed próbą dopasowania. Mogą to być następujące przekształcenia:
     - Wielkie litery 
     - Małe litery
     - Trim
     - RemoveNulls
     - UrlDecode
     - UrlEncode
     
   - **Wartość dopasowania:** Obsługiwane wartości metod żądania HTTP to:
     - GET
     - POST
     - PUT
     - HEAD
     - DELETE
     - SKRĘT
     - ODBLOKOWANIA
     - PROFIL
     - Opcje
     - PROPFIND
     - PROPPATCH
     - MKCOL
     - KOPIOWANE
     - Przenieś

## <a name="examples"></a>Przykłady

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Przykład WAF reguł niestandardowych opartych na parametrach http

Oto przykład, który pokazuje konfigurację niestandardowej reguły z dwoma warunkami dopasowania. Żądania pochodzą z określonej lokacji zdefiniowanej przez obiekt odwołujący, a ciąg zapytania nie zawiera "hasła".

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
Przykładowa konfiguracja służąca do blokowania metody "PUT" jest pokazana poniżej:

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>Ograniczenie rozmiaru

Można utworzyć regułę niestandardową, która określa ograniczenie rozmiaru w części żądania przychodzącego. Na przykład niższa reguła blokuje adres URL dłuższy niż 100 znaków.

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie zasad zapory aplikacji sieci Web przy użyciu Azure PowerShell](waf-front-door-custom-rules-powershell.md) 
- Dowiedz się więcej o [zaporze aplikacji sieci Web z przednimi drzwiami](afds-overview.md)
- Dowiedz się, jak [utworzyć usługę Front Door](../../frontdoor/quickstart-create-front-door.md).

