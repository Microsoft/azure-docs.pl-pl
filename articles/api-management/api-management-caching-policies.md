---
title: Zasady buforowania API Management platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat zasad buforowania dostępnych do użycia w usłudze Azure API Management. Zobacz przykłady i wyświetlanie dodatkowych dostępnych zasobów.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: e939e3e6eb686894094c77f62ed815d0ebb5a24f
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358443"
---
# <a name="api-management-caching-policies"></a>Zasady buforowania usługi API Management
Ten temat zawiera informacje dotyczące następujących zasad API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasady w API Management](./api-management-policies.md).

## <a name="caching-policies"></a><a name="CachingPolicies"></a> Zasady buforowania

- Zasady buforowania odpowiedzi
    - [Pobierz z pamięci podręcznej](api-management-caching-policies.md#GetFromCache) — wykonaj wyszukiwanie w pamięci podręcznej i zwróć poprawne buforowane odpowiedzi, jeśli są dostępne.
    - [Przechowywanie w pamięci](api-management-caching-policies.md#StoreToCache) podręcznej w pamięci podręcznej odpowiedzi zgodnie z określoną konfiguracją kontroli pamięci podręcznej.
- Zasady buforowania wartości
    - [Pobierz wartość z pamięci podręcznej](#GetFromCacheByKey) — Pobierz zbuforowany element według klucza.
    - [Wartość magazynu w pamięci podręcznej](#StoreToCacheByKey) — przechowywanie elementu w pamięci podręcznej według klucza.
    - [Usuń wartość z pamięci podręcznej](#RemoveCacheByKey) — Usuń element z pamięci podręcznej według klucza.

## <a name="get-from-cache"></a><a name="GetFromCache"></a> Pobierz z pamięci podręcznej
Użyj `cache-lookup` zasad w celu przeprowadzenia wyszukiwania w pamięci podręcznej i zwrócenia prawidłowej pamięci podręcznej, jeśli jest dostępna. Te zasady mogą być stosowane w przypadkach, w których zawartość odpowiedzi pozostaje statyczna w danym okresie czasu. Buforowanie odpowiedzi zmniejsza wymagania dotyczące przepustowości i przetwarzania narzucone na serwerze sieci Web zaplecza i obniża opóźnienia postrzegane przez klientów interfejsu API.

> [!NOTE]
> Te zasady muszą mieć odpowiedni [Magazyn do zasad pamięci podręcznej](api-management-caching-policies.md#StoreToCache) .

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Przykład użycia wyrażeń zasad
Ten przykład pokazuje, jak API Management skonfigurować czas trwania buforowania odpowiedzi, który jest zgodny z buforowaniem odpowiedzi usługi wewnętrznej bazy danych określonej przez dyrektywę usługi w tej usłudze `Cache-Control` . Aby zapoznać się z prezentacją konfigurowania i korzystania z tych zasad, zobacz temat [Cloud okładki epizod 177: więcej API Management funkcji z Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkie przekazanie do 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Aby uzyskać więcej informacji, zobacz [wyrażenia zasad](api-management-policy-expressions.md) i [zmienna kontekstowa](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|pamięć podręczna — wyszukiwanie|Element główny.|Tak|
|Zróżnicuj według nagłówka|Rozpocznij buforowanie odpowiedzi na wartość określonego nagłówka, na przykład Accept, Accept-Charset, Accept-Encoding, Accept-Language, Authorization, from, host, if-Match.|Nie|
|Zróżnicuj według zapytania — parametr|Rozpocznij buforowanie odpowiedzi na wartość określonych parametrów zapytania. Wprowadź jeden lub wiele parametrów. Użyj średnika jako separatora. Jeśli żaden nie zostanie określony, używane są wszystkie parametry zapytania.|Nie|

### <a name="attributes"></a>Atrybuty

| Nazwa                           | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| Zezwalaj na buforowanie z odpowiedziami prywatnymi | Gdy ustawiona na `true` , umożliwia buforowanie żądań zawierających nagłówek autoryzacji.                                                                                                                                                                                                                                                                        | Nie       | fałsz             |
| Typ buforowania               | Wybierz między następującymi wartościami atrybutu:<br />- `internal` Aby użyć wbudowanej pamięci podręcznej API Management,<br />- `external` Aby użyć zewnętrznej pamięci podręcznej, zgodnie z opisem w temacie [Korzystanie z zewnętrznej pamięci podręcznej platformy Azure dla Redis w usłudze Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` użycie zewnętrznej pamięci podręcznej, jeśli jest skonfigurowana lub wewnętrzna pamięć podręczna w przeciwnym razie. | Nie       | `prefer-external` |
| buforowanie podrzędne-Type        | Ten atrybut musi być ustawiony na jedną z następujących wartości.<br /><br /> -None — buforowanie podrzędne nie jest dozwolone.<br />— prywatne buforowanie w trybie podrzędnym jest dozwolone.<br />-publiczne i udostępnione buforowanie podrzędne jest dozwolone.                                                                                                          | Nie       | brak              |
| musi ponownie sprawdzić poprawność                | Gdy buforowanie podrzędne jest włączone, ten atrybut włącza lub wyłącza `must-revalidate` dyrektywę kontroli pamięci podręcznej w odpowiedziach bramy.                                                                                                                                                                                                                      | Nie       | true              |
| Zróżnicuj według deweloperów              | Ustaw na `true` buforowanie odpowiedzi na konto dewelopera, do którego należy [klucz subskrypcji](./api-management-subscriptions.md) uwzględniony w żądaniu.                                                                                                                                                                                                                                                                                                  | Tak      |         Fałsz          |
| różne grupy deweloperów       | Ustaw na wartość `true` w celu buforowania odpowiedzi dla każdej [grupy użytkowników](./api-management-howto-create-groups.md).                                                                                                                                                                                                                                                                                                             | Tak      |       Fałsz            |

### <a name="usage"></a>Użycie
Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

- **Sekcje zasad:** przychodzące
- **Zakresy zasad:** wszystkie zakresy

## <a name="store-to-cache"></a><a name="StoreToCache"></a> Przechowywanie w pamięci podręcznej
`cache-store`Zasady buforują odpowiedzi zgodnie z określonymi ustawieniami pamięci podręcznej. Te zasady mogą być stosowane w przypadkach, w których zawartość odpowiedzi pozostaje statyczna w danym okresie czasu. Buforowanie odpowiedzi zmniejsza wymagania dotyczące przepustowości i przetwarzania narzucone na serwerze sieci Web zaplecza i obniża opóźnienia postrzegane przez klientów interfejsu API.

> [!NOTE]
> Te zasady muszą mieć odpowiednie zasady [Get z pamięci podręcznej](api-management-caching-policies.md#GetFromCache) .

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<cache-store duration="seconds" />
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Przykład użycia wyrażeń zasad
Ten przykład pokazuje, jak API Management skonfigurować czas trwania buforowania odpowiedzi, który jest zgodny z buforowaniem odpowiedzi usługi wewnętrznej bazy danych określonej przez dyrektywę usługi w tej usłudze `Cache-Control` . Aby zapoznać się z prezentacją konfigurowania i korzystania z tych zasad, zobacz temat [Cloud okładki epizod 177: więcej API Management funkcji z Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkie przekazanie do 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Aby uzyskać więcej informacji, zobacz [wyrażenia zasad](api-management-policy-expressions.md) i [zmienna kontekstowa](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|Magazyn pamięci podręcznej|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

| Nazwa             | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| czas trwania         | Czas wygaśnięcia wpisów w pamięci podręcznej (w sekundach).                                                                                                                                                                                                                                                                                                   | Tak      | Brak               |

### <a name="usage"></a>Użycie
Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

- **Sekcje zasad:** wychodzące
- **Zakresy zasad:** wszystkie zakresy

## <a name="get-value-from-cache"></a><a name="GetFromCacheByKey"></a> Pobierz wartość z pamięci podręcznej
Użyj `cache-lookup-value` zasad do przeszukiwania pamięci podręcznej według klucza i zwrócenia wartości w pamięci podręcznej. Klucz może mieć dowolną wartość ciągu i jest zwykle dostarczany przy użyciu wyrażenia zasad.

> [!NOTE]
> Te zasady muszą mieć odpowiadającą [wartość magazynu w zasadach pamięci podręcznej](#StoreToCacheByKey) .

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Przykład
Aby uzyskać więcej informacji i przykłady tych zasad, zobacz [niestandardowe buforowanie na platformie Azure API Management](./api-management-sample-cache-by-key.md).

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|pamięć podręczna — wyszukiwanie-wartość|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

| Nazwa             | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| Typ buforowania | Wybierz między następującymi wartościami atrybutu:<br />- `internal` Aby użyć wbudowanej pamięci podręcznej API Management,<br />- `external` Aby użyć zewnętrznej pamięci podręcznej, zgodnie z opisem w temacie [Korzystanie z zewnętrznej pamięci podręcznej platformy Azure dla Redis w usłudze Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` użycie zewnętrznej pamięci podręcznej, jeśli jest skonfigurowana lub wewnętrzna pamięć podręczna w przeciwnym razie. | Nie       | `prefer-external` |
| wartość domyślna    | Wartość, która zostanie przypisana do zmiennej, jeśli wyszukiwanie klucza pamięci podręcznej spowodowało brak. Jeśli ten atrybut nie jest określony, `null` jest przypisany.                                                                                                                                                                                                           | Nie       | `null`            |
| key              | Wartość klucza pamięci podręcznej do użycia w wyszukiwaniu.                                                                                                                                                                                                                                                                                                                       | Tak      | Brak               |
| Nazwa zmiennej    | Nazwa [zmiennej kontekstowej](api-management-policy-expressions.md#ContextVariables) , do której zostanie przypisana wartość, jeśli wyszukiwanie zakończyło się pomyślnie. Jeśli wyszukiwanie spowoduje odrzucenie, zmienna zostanie przypisana do wartości `default-value` atrybutu lub `null` , jeśli `default-value` atrybut zostanie pominięty.                                       | Tak      | Brak               |

### <a name="usage"></a>Użycie
Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

- **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu
- **Zakresy zasad:** wszystkie zakresy

## <a name="store-value-in-cache"></a><a name="StoreToCacheByKey"></a> Wartość magazynu w pamięci podręcznej
`cache-store-value`Magazyn pamięci podręcznej przez klucz. Klucz może mieć dowolną wartość ciągu i jest zwykle dostarczany przy użyciu wyrażenia zasad.

> [!NOTE]
> Te zasady muszą mieć odpowiednie [wartości Get z zasad pamięci podręcznej](#GetFromCacheByKey) .

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Przykład
Aby uzyskać więcej informacji i przykłady tych zasad, zobacz [niestandardowe buforowanie na platformie Azure API Management](./api-management-sample-cache-by-key.md).

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|pamięć podręczna-magazyn-wartość|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

| Nazwa             | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| Typ buforowania | Wybierz między następującymi wartościami atrybutu:<br />- `internal` Aby użyć wbudowanej pamięci podręcznej API Management,<br />- `external` Aby użyć zewnętrznej pamięci podręcznej, zgodnie z opisem w temacie [Korzystanie z zewnętrznej pamięci podręcznej platformy Azure dla Redis w usłudze Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` użycie zewnętrznej pamięci podręcznej, jeśli jest skonfigurowana lub wewnętrzna pamięć podręczna w przeciwnym razie. | Nie       | `prefer-external` |
| czas trwania         | Wartość zostanie zbuforowana dla podanej wartości czasu trwania określonej w sekundach.                                                                                                                                                                                                                                                                                 | Tak      | Brak               |
| key              | Klucz pamięci podręcznej, w której będzie przechowywana wartość.                                                                                                                                                                                                                                                                                                                   | Tak      | Brak               |
| value            | Wartość do buforowania.                                                                                                                                                                                                                                                                                                                                     | Tak      | Brak               |
### <a name="usage"></a>Użycie
Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

- **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu
- **Zakresy zasad:** wszystkie zakresy

## <a name="remove-value-from-cache"></a><a name="RemoveCacheByKey"></a> Usuń wartość z pamięci podręcznej
`cache-remove-value`Usuwa buforowany element identyfikowany przez jego klucz. Klucz może mieć dowolną wartość ciągu i jest zwykle dostarczany przy użyciu wyrażenia zasad.

#### <a name="policy-statement"></a>Instrukcja zasad

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Przykład

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|pamięć podręczna — usuwanie wartości|Element główny.|Tak|

#### <a name="attributes"></a>Atrybuty

| Nazwa             | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| Typ buforowania | Wybierz między następującymi wartościami atrybutu:<br />- `internal` Aby użyć wbudowanej pamięci podręcznej API Management,<br />- `external` Aby użyć zewnętrznej pamięci podręcznej, zgodnie z opisem w temacie [Korzystanie z zewnętrznej pamięci podręcznej platformy Azure dla Redis w usłudze Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` użycie zewnętrznej pamięci podręcznej, jeśli jest skonfigurowana lub wewnętrzna pamięć podręczna w przeciwnym razie. | Nie       | `prefer-external` |
| key              | Klucz poprzednio buforowanej wartości, który ma zostać usunięty z pamięci podręcznej.                                                                                                                                                                                                                                                                                        | Tak      | Brak               |

#### <a name="usage"></a>Użycie
Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes) zasad.

- **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu
- **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

+ [Zasady w API Management](api-management-howto-policies.md)
+ [Przekształć interfejsy API](transform-api.md)
+ [Dokumentacja zasad](./api-management-policies.md) pełna lista instrukcji zasad i ich ustawień
+ [Przykłady zasad](./policy-reference.md)