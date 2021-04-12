---
title: Konfigurowanie akcji zestawu reguł Standard/Premium platformy Azure
description: Ten artykuł zawiera listę różnych akcji, które można wykonać przy użyciu zestawu reguł dla drzwi platformy Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: e4698a1c1576d15042dd050e0123b83dba39a3e3
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064795"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-actions"></a>Akcje zestawu reguł w warstwie Standardowa/Premium platformy Azure (wersja zapoznawcza)

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

[Zestaw reguł](concept-rule-set.md) Standard/Premium na platformie Azure obejmuje reguły z kombinacją warunków i akcji dopasowywania. Ten artykuł zawiera szczegółowy opis akcji, których można użyć w zestawie reguł Standard/Premium na platformie Azure. Akcja definiuje zachowanie, które jest stosowane do typu żądania identyfikowanego przez warunek zgodności. W zestawie reguł dla drzwi frontonu platformy Azure (standard/Premium) reguła może zawierać maksymalnie pięć akcji.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Następujące akcje są dostępne do użycia w zestawie reguł dla drzwi platformy Azure.  

## <a name="cache-expiration"></a><a name="CacheExpiration"></a> Wygaśnięcie pamięci podręcznej

Użyj akcji **wygaśnięcie pamięci podręcznej** , aby zastąpić wartość czasu wygaśnięcia (TTL) punktu końcowego dla żądań, które są zgodne z warunkami określonymi przez reguły.

> [!NOTE]
> Źródła mogą określać, aby nie buforować określonych odpowiedzi przy użyciu `Cache-Control` nagłówka z wartością `no-cache` , `private` lub `no-store` . W takich przypadkach drzwi z przodu nigdy nie będą w pamięci podręcznej zawartości, a ta akcja nie będzie miała żadnego efektu.

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|-------|------------------|
| Zachowanie pamięci podręcznej | <ul><li>**Pamięć podręczna obejścia:** Zawartość nie powinna być buforowana. W szablonach ARM Ustaw `cacheBehavior` Właściwość na `BypassCache` .</li><li>**Zastąpienie:** Wartość czasu wygaśnięcia zwracana z źródła jest zastępowana wartością określoną w akcji. Takie zachowanie będzie stosowane tylko w przypadku, gdy odpowiedź jest w pamięci podręcznej. W szablonach ARM Ustaw `cacheBehavior` Właściwość na `Override` .</li><li>**Ustaw, jeśli brak:** Jeśli wartość czasu wygaśnięcia nie zostanie zwrócona ze źródła, reguła ustawia czas wygaśnięcia na wartość określoną w akcji. Takie zachowanie będzie stosowane tylko w przypadku, gdy odpowiedź jest w pamięci podręcznej. W szablonach ARM Ustaw `cacheBehavior` Właściwość na `SetIfMissing` .</li></ul> |
| Czas trwania pamięci podręcznej | Gdy _zachowanie pamięci podręcznej_ jest ustawione na `Override` lub `Set if missing` , te pola muszą określać czas trwania pamięci podręcznej do użycia. Maksymalny czas trwania to 366 dni.<ul><li>W Azure Portal: Określ dni, godziny, minuty i sekundy.</li><li>W szablonach ARM: Określ czas trwania w formacie `d.hh:mm:ss` . |

### <a name="example"></a>Przykład

W tym przykładzie przesłonimy wygaśnięcie pamięci podręcznej do 6 godzin, w przypadku pasujących żądań, które nie określają już czasu trwania pamięci podręcznej.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-expiration.png" alt-text="Zrzut ekranu portalu przedstawiający akcję wygaśnięcia pamięci podręcznej.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheExpiration",
  "parameters": {
    "cacheBehavior": "SetIfMissing",
    "cacheType": "All",
    "cacheDuration": "0.06:00:00",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheExpiration'
  parameters: {
    cacheBehavior: 'SetIfMissing'
    cacheType: All
    cacheDuration: '0.06:00:00'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters'
  }
}
```

---

## <a name="cache-key-query-string"></a><a name="CacheKeyQueryString"></a> Ciąg zapytania klucza pamięci podręcznej

Użyj akcji **ciągu zapytania klucza pamięci podręcznej** , aby zmodyfikować klucz pamięci podręcznej na podstawie ciągów zapytań. Klucz pamięci podręcznej jest sposobem, w jaki drzwi frontonu identyfikują unikatowe żądania w pamięci podręcznej.

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|-------|------------------|
| Zachowanie | <ul><li>**Uwzględnij:** Ciągi zapytania określone w parametrach Get są uwzględniane podczas generowania klucza pamięci podręcznej. W szablonach ARM Ustaw `queryStringBehavior` Właściwość na `Include` .</li><li>**Buforuj każdy unikatowy adres URL:** Każdy unikatowy adres URL ma swój własny klucz pamięci podręcznej. W szablonach ARM Użyj `queryStringBehavior` programu `IncludeAll` .</li><li>**Wyklucz:** Ciągi zapytania określone w parametrach zostaną wykluczone, gdy zostanie wygenerowany klucz pamięci podręcznej. W szablonach ARM Ustaw `queryStringBehavior` Właściwość na `Exclude` .</li><li>**Ignoruj ciągi zapytań:** Ciągi zapytań nie są brane pod uwagę podczas generowania klucza pamięci podręcznej. W szablonach ARM Ustaw `queryStringBehavior` Właściwość na `ExcludeAll` .</li></ul>  |
| Parametry | Lista nazw parametrów ciągu zapytania, rozdzielonych przecinkami. |

### <a name="example"></a>Przykład

W tym przykładzie zmodyfikujemy klucz pamięci podręcznej w celu uwzględnienia parametru ciągu zapytania o nazwie `customerId` .

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-key-query-string.png" alt-text="Zrzut ekranu portalu przedstawiający akcję ciągu zapytania klucza pamięci podręcznej.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheKeyQueryString",
  "parameters": {
    "queryStringBehavior": "Include",
    "queryParameters": "customerId",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheKeyQueryString'
  parameters: {
    queryStringBehavior: 'Include'
    queryParameters: 'customerId'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters'
  }
}
```

---

## <a name="modify-request-header"></a><a name="ModifyRequestHeader"></a> Modyfikuj nagłówek żądania

Akcja **Modyfikuj nagłówek żądania** służy do modyfikowania nagłówków w żądaniu wysyłanych do źródła.

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|-------|------------------|
| Operator | <ul><li>**Dołącz:** Określony nagłówek zostanie dodany do żądania o określonej wartości. Jeśli nagłówek już istnieje, wartość jest dołączana do istniejącej wartości nagłówka przy użyciu łączenia ciągów. Nie dodano ograniczników. W szablonach ARM Użyj `headerAction` programu `Append` .</li><li>**Zastąp:** Określony nagłówek zostanie dodany do żądania o określonej wartości. Jeśli nagłówek już istnieje, określona wartość zastępuje istniejącą wartość. W szablonach ARM Użyj `headerAction` programu `Overwrite` .</li><li>**Usuń:** Jeśli nagłówek określony w regule jest obecny, nagłówek zostanie usunięty z żądania. W szablonach ARM Użyj `headerAction` programu `Delete` .</li></ul> |
| Nazwa nagłówka | Nazwa nagłówka do zmodyfikowania. |
| Wartość nagłówka | Wartość do dołączenia lub zastępowania. |

### <a name="example"></a>Przykład

W tym przykładzie dołączymy wartość `AdditionalValue` do `MyRequestHeader` nagłówka żądania. Jeśli początek ustawił nagłówek odpowiedzi na wartość `ValueSetByClient` , po zastosowaniu tej akcji nagłówek żądania będzie miał wartość `ValueSetByClientAdditionalValue` .

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-request-header.png" alt-text="Zrzut ekranu portalu przedstawiający akcję Modyfikuj nagłówek żądania.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyRequestHeader",
  "parameters": {
    "headerAction": "Append",
    "headerName": "MyRequestHeader",
    "value": "AdditionalValue",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyRequestHeader'
  parameters: {
    headerAction: 'Append'
    headerName: 'MyRequestHeader'
    value: 'AdditionalValue'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="modify-response-header"></a><a name="ModifyResponseHeader"></a> Modyfikuj nagłówek odpowiedzi

Akcja **Modyfikuj nagłówek odpowiedzi** służy do modyfikowania nagłówków, które są obecne w odpowiedziach przed zwróceniem ich do klientów.

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|-------|------------------|
| Operator | <ul><li>**Dołącz:** Określony nagłówek zostanie dodany do odpowiedzi o określonej wartości. Jeśli nagłówek już istnieje, wartość jest dołączana do istniejącej wartości nagłówka przy użyciu łączenia ciągów. Nie dodano ograniczników. W szablonach ARM Użyj `headerAction` programu `Append` .</li><li>**Zastąp:** Określony nagłówek zostanie dodany do odpowiedzi o określonej wartości. Jeśli nagłówek już istnieje, określona wartość zastępuje istniejącą wartość. W szablonach ARM Użyj `headerAction` programu `Overwrite` .</li><li>**Usuń:** Jeśli nagłówek określony w regule jest obecny, nagłówek zostanie usunięty z odpowiedzi.  W szablonach ARM Użyj `headerAction` programu `Delete` .</li></ul> |
| Nazwa nagłówka | Nazwa nagłówka do zmodyfikowania. |
| Wartość nagłówka | Wartość do dołączenia lub zastępowania. |

### <a name="example"></a>Przykład

W tym przykładzie usuniemy nagłówek z nazwą `X-Powered-By` z odpowiedzi, zanim zostaną one zwrócone do klienta.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-response-header.png" alt-text="Zrzut ekranu portalu przedstawiający akcję Modyfikuj nagłówek odpowiedzi.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyResponseHeader",
  "parameters": {
    "headerAction": "Delete",
    "headerName": "X-Powered-By",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyResponseHeader'
  parameters: {
    headerAction: 'Delete'
    headerName: 'X-Powered-By'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="url-redirect"></a><a name="UrlRedirect"></a> Przekierowanie adresu URL

Użyj akcji **przekierowanie adresu URL** , aby przekierować klientów do nowego adresu URL. Klienci otrzymują odpowiedź przekierowania od drzwi przednich.

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|----------|------------------|
| Typ przekierowania | Typ odpowiedzi, który ma zostać zwrócony do obiektu żądającego. <ul><li>W Azure Portal: znaleziono (302), przeniesione (301), tymczasowe przekierowanie (307), trwałe przekierowanie (308).</li><li>W szablonach ARM: `Found` , `Moved` , `TemporaryRedirect` , `PermanentRedirect`</li></ul> |
| Protokół przekierowania | <ul><li>W Azure Portal: `Match Request` , `HTTP` , `HTTPS`</li><li>W szablonach ARM: `MatchRequest` , `Http` , `Https`</li></ul> |
| Host docelowy | Nazwa hosta, do którego ma zostać przekierowane żądanie. Pozostaw to pole puste, aby zachować hosta przychodzącego. |
| Ścieżka docelowa | Ścieżka do użycia w przekierowaniu. Uwzględnij wiodące `/` . Pozostaw to pole puste, aby zachować ścieżkę przychodzącą. |
| Ciąg zapytania | Ciąg zapytania używany w przekierowaniu. Nie dołączaj wiodącej `?` . Pozostaw to pole puste, aby zachować ciąg zapytania przychodzącego. |
| Fragment docelowy | Fragment do użycia w przekierowaniu. Pozostaw to pole puste, aby zachować fragment przychodzący. |

### <a name="example"></a>Przykład

W tym przykładzie przekierowujemy żądanie do `https://contoso.com/exampleredirection?clientIp={client_ip}` , jednocześnie zachowując fragment. Używane jest tymczasowe Przekierowanie HTTP (307). Adres IP klienta jest używany zamiast `{client_ip}` tokenu w adresie URL przy użyciu `client_ip` [zmiennej serwerowej](#server-variables).

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-redirect.png" alt-text="Zrzut ekranu portalu pokazujący akcję przekierowania adresu URL.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRedirect",
  "parameters": {
    "redirectType": "TemporaryRedirect",
    "destinationProtocol": "Https",
    "customHostname": "contoso.com",
    "customPath": "/exampleredirection",
    "customQueryString": "clientIp={client_ip}",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRedirect'
  parameters: {
    redirectType: 'TemporaryRedirect'
    destinationProtocol: 'Https'
    customHostname: 'contoso.com'
    customPath: '/exampleredirection'
    customQueryString: 'clientIp={client_ip}'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters'
  }
}
```

---

## <a name="url-rewrite"></a><a name="UrlRewrite"></a> Ponowne zapisywanie adresów URL

Użyj akcji **ponownego zapisywania adresu URL** , aby ponownie napisać ścieżkę żądania, które jest trasy do źródła.

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|----------|------------------|
| Wzorzec źródła | Zdefiniuj wzorzec źródłowy w ścieżce URL, który ma zostać zamieniony. Obecnie wzorzec źródłowy używa dopasowania opartego na prefiksie. Aby dopasować wszystkie ścieżki URL, użyj ukośnika ( `/` ) jako wartości wzorca źródła. |
| Element docelowy | Zdefiniuj ścieżkę docelową do użycia podczas ponownego zapisywania. Ścieżka docelowa zastępuje wzorzec źródłowy. |
| Zachowaj niedopasowaną ścieżkę | W przypadku ustawienia wartości _tak_ pozostała ścieżka po wzorcu źródłowym zostanie dołączona do nowej ścieżki docelowej. |

### <a name="example"></a>Przykład

W tym przykładzie ponownie zapisujemy wszystkie żądania do ścieżki `/redirection` i nie zachowuje reszty ścieżki.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-rewrite.png" alt-text="Zrzut ekranu portalu przedstawiający akcję ponownego zapisu adresu URL.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRewrite",
  "parameters": {
    "sourcePattern": "/",
    "destination": "/redirection",
    "preserveUnmatchedPath": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRewrite'
  parameters: {
    sourcePattern: '/'
    destination: '/redirection'
    preserveUnmatchedPath: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters'
  }
}
```

---

## <a name="server-variables"></a>Zmienne serwera

Zmienne serwera zestawu reguł zapewniają dostęp do strukturalnych informacji o żądaniu. Zmienne serwera umożliwiają dynamiczne Zmienianie nagłówków żądania/odpowiedzi lub ścieżki ponownego zapisywania adresu URL/ciągów zapytań, na przykład po załadowaniu nowej strony lub po opublikowaniu formularza.

### <a name="supported-variables"></a>Obsługiwane zmienne

| Nazwa zmiennej    | Opis                                                                                                                                                                                                                                                                               |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `socket_ip`      | Adres IP bezpośredniego połączenia z krawędzią frontu drzwi platformy Azure. Jeśli Klient użył serwera proxy HTTP lub modułu równoważenia obciążenia do wysłania żądania, wartość `socket_ip` jest adresem IP serwera proxy lub modułu równoważenia obciążenia.                                                                      |
| `client_ip`      | Adres IP klienta, który dokonał oryginalnego żądania. Jeśli `X-Forwarded-For` w żądaniu wystąpił nagłówek, adres IP klienta zostanie pobrany z nagłówka.                                                                                                               |
| `client_port`    | Port IP klienta, który wykonał żądanie.                                                                                                                                                                                                                                          |
| `hostname`       | Nazwa hosta w żądaniu klienta.                                                                                                                                                                                                                                             |
| `geo_country`    | Wskazuje kraj/region osoby żądającej za pomocą kodu kraju/regionu.                                                                                                                                                                                                       |
| `http_method`    | Metoda używana do żądania adresu URL, na przykład `GET` lub `POST` .                                                                                                                                                                                                                         |
| `http_version`   | Protokół żądania. Zwykle `HTTP/1.0` , `HTTP/1.1` , lub `HTTP/2.0` .                                                                                                                                                                                                                      |
| `query_string`   | Lista par zmienna/wartość, które następuje po "?" w żądanym adresie URL.<br />Na przykład w żądaniu `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` wartość będzie równa `query_string` `id=123&title=fabrikam` .                                                      |
| `request_scheme` | Schemat żądania: `http` lub `https` .                                                                                                                                                                                                                                                    |
| `request_uri`    | Pełny identyfikator URI żądania (z argumentami).<br />Na przykład w żądaniu `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` wartość będzie równa `request_uri` `/article.aspx?id=123&title=fabrikam` .                                                                     |
| `ssl_protocol`   | Protokół ustanowionego połączenia TLS.                                                                                                                                                                                                                                            |
| `server_port`    | Port serwera, który zaakceptował żądanie.                                                                                                                                                                                                                                           |
| `url_path`       | Identyfikuje określony zasób na hoście, do którego klient sieci Web chce uzyskać dostęp. Jest to część identyfikatora URI żądania bez argumentów.<br />Na przykład w żądaniu `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` wartość będzie równa `uri_path` `/article.aspx` . |

### <a name="server-variable-format"></a>Format zmiennej serwera    

Zmienne serwera można określić przy użyciu następujących formatów:

* `{variable}`: Uwzględnij całą zmienną serwerową. Na przykład, jeśli adres IP klienta to `111.222.333.444` następnie, będzie `{client_ip}` obliczany token `111.222.333.444` .
* `{variable:offset}`: Uwzględnij zmienną serwerową po określonym przesunięciu aż do końca zmiennej. Przesunięcie jest zależne od zera. Na przykład, jeśli adres IP klienta to `111.222.333.444` następnie, będzie `{client_ip:3}` obliczany token `.222.333.444` .
* `{variable:offset:length}`: Uwzględnij zmienną serwerową po określonym przesunięciu do określonej długości. Przesunięcie jest zależne od zera. Na przykład, jeśli adres IP klienta to `111.222.333.444` następnie, będzie `{client_ip:4:3}` obliczany token `222` .

### <a name="supported-actions"></a>Obsługiwane akcje

Zmienne serwera są obsługiwane w następujących akcjach:

* Ciąg zapytania klucza pamięci podręcznej
* Modyfikuj nagłówek żądania
* Modyfikuj nagłówek odpowiedzi
* Przekierowywanie adresów URL
* Regenerowanie adresów URL

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zestawie reguł Standard/Premium platformy Azure](concept-rule-set.md).
* Dowiedz się więcej o [warunkach dopasowania zestawu reguł](concept-rule-set-match-conditions.md).
