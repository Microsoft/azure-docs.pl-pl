---
title: Zasady ograniczeń dostępu API Management platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat zasad ograniczeń dostępu dostępnych do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.topic: article
ms.date: 02/26/2021
ms.author: apimpm
ms.openlocfilehash: 882d96271b6976db1ffc0dde181d5699c5cc27de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688250"
---
# <a name="api-management-access-restriction-policies"></a>Zasady ograniczeń dostępu usługi API Management

Ten temat zawiera informacje dotyczące następujących zasad API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasady w API Management](./api-management-policies.md).

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a> Zasady ograniczeń dostępu

-   [Sprawdź nagłówek HTTP](#CheckHTTPHeader) — wymusza istnienie i/lub wartość nagłówka HTTP.
-   [Ogranicz częstotliwość wywołań przez subskrypcję](#LimitCallRate) — uniemożliwia użycie interfejsu API przez ograniczenie liczby wywołań dla każdej subskrypcji.
-   [Ogranicz częstotliwość wywołań według klucza](#LimitCallRateByKey) — uniemożliwia użycie interfejsu API przez ograniczenie liczby wywołań na podstawie poszczególnych kluczy.
-   [Ogranicz adresy IP wywołującego](#RestrictCallerIPs) — filtry (dozwolone/odmawiające) wywołania z określonych adresów i/lub zakresów adresów.
-   [Ustawianie limitu przydziału użycia według subskrypcji](#SetUsageQuota) — umożliwia wymuszenie naliczania i/lub przydziału przepustowości dla każdej subskrypcji.
-   [Ustawianie przydziału użycia według klucza](#SetUsageQuotaByKey) — umożliwia wymuszenie naliczania i/lub przydziału przepustowości dla każdego klucza.
-   [Sprawdzanie poprawności tokenu JWT](#ValidateJWT) — wymusza istnienie i ważność tokenu JWT wyodrębnionego z albo określonego nagłówka HTTP lub określonego parametru zapytania.

> [!TIP]
> Zasad ograniczeń dostępu można używać w różnych zakresach w różnych celach. Na przykład można zabezpieczyć cały interfejs API z uwierzytelnianiem w usłudze AAD, stosując `validate-jwt` zasady na poziomie interfejsu API lub można zastosować go na poziomie operacji interfejsu API i użyć `claims` do dokładniejszego sterowania.

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a> Sprawdź nagłówek HTTP

Użyj `check-header` zasad, aby wymusić, że żądanie ma określony nagłówek HTTP. Opcjonalnie możesz sprawdzić, czy nagłówek ma określoną wartość, czy też sprawdzić zakres dozwolonych wartości. Jeśli sprawdzenie zakończy się niepowodzeniem, zasady przerywają przetwarzanie żądań i zwracają kod stanu HTTP i komunikat o błędzie określony przez zasady.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>Przykład

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>Elementy

| Nazwa         | Opis                                                                                                                                   | Wymagane |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| znacznik wyboru | Element główny.                                                                                                                                 | Tak      |
| wartość        | Dozwolona wartość nagłówka HTTP. Gdy określono wiele elementów wartości, sprawdzanie jest uznawane za sukces, jeśli jedna z wartości jest zgodna. | Nie       |

### <a name="attributes"></a>Atrybuty

| Nazwa                       | Opis                                                                                                                                                            | Wymagane | Domyślne |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| Niepowodzenie — check-error-message | Komunikat o błędzie do zwrócenia w treści odpowiedzi HTTP, jeśli nagłówek nie istnieje lub ma nieprawidłową wartość. Ten komunikat musi mieć poprawnie zmienione znaki specjalne. | Tak      | Nie dotyczy     |
| Niepowodzenie — Check-httpcode      | Kod stanu HTTP do zwrócenia, jeśli nagłówek nie istnieje lub ma nieprawidłową wartość.                                                                                        | Tak      | Nie dotyczy     |
| Nazwa nagłówka                | Nazwa nagłówka HTTP do sprawdzenia.                                                                                                                                  | Tak      | Nie dotyczy     |
| Ignoruj wielkość liter                | Można ustawić na wartość true lub false. Jeśli jest ustawiona na wartość true Case, jest ignorowana, gdy wartość nagłówka jest porównywana z zestawem akceptowalnych wartości.                                    | Tak      | Nie dotyczy     |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące

-   **Zakresy zasad:** wszystkie zakresy

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a> Ogranicz częstotliwość wywołań według subskrypcji

`rate-limit`Zasady uniemożliwiają użycie interfejsu API dla każdej subskrypcji, ograniczając częstotliwość wywołań do określonej liczby w określonym przedziale czasu. Po przekroczeniu liczby wywołań wywołujący otrzymuje `429 Too Many Requests` kod stanu odpowiedzi.

> [!IMPORTANT]
> Tych zasad można używać tylko raz dla każdego dokumentu zasad.
>
> [Wyrażeń zasad](api-management-policy-expressions.md) nie można używać w żadnym z atrybutów zasad dla tych zasad.

> [!CAUTION]
> Ze względu na rozproszoną naturę architektury ograniczanie szybkości nie jest nigdy całkowicie dokładne. Różnica między skonfigurowaną i rzeczywistą liczbą dozwolonych żądań różni się w zależności od ilości żądania, liczby opóźnień zaplecza i innych czynników.

> [!NOTE]
> Aby zrozumieć różnicę między limitami szybkości i przydziałów, [Zobacz limity szybkości i przydziały.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" 
        retry-after-header-name="header name" 
        retry-after-variable-name="policy expression variable name"
        remaining-calls-header-name="header name"  
        remaining-calls-variable-name="policy expression variable name"
        total-calls-header-name="header name"/>
    </api>
</rate-limit>
```

### <a name="example"></a>Przykład

W poniższym przykładzie limit stawki na subskrypcję wynosi 20 wywołań na 90 sekund. Po każdym wykonaniu zasad pozostałe wywołania dozwolone w danym okresie są przechowywane w zmiennej `remainingCallsPerSubscription` .

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" remaining-calls-variable-name="remainingCallsPerSubscription"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementy

| Nazwa       | Opis                                                                                                                                                                                                                                                                                              | Wymagane |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| stawka — limit | Element główny.                                                                                                                                                                                                                                                                                            | Tak      |
| api        | Dodaj co najmniej jeden z tych elementów, aby nałożyć limit liczby wywołań na interfejsy API w ramach produktu. Limity szybkości wywołań produktu i interfejsu API są stosowane niezależnie. Do interfejsu API można odwoływać się za pośrednictwem `name` lub `id` . Jeśli podano oba atrybuty, `id` zostaną one użyte i `name` zostaną zignorowane.                    | Nie       |
| operation  | Dodaj jeden lub więcej z tych elementów, aby nałożyć limit liczby wywołań na operacje w interfejsie API. Limity szybkości wywołań produktu, interfejsu API i operacji są stosowane niezależnie. Operacji można przywoływać za pośrednictwem `name` lub `id` . Jeśli podano oba atrybuty, `id` zostaną one użyte i `name` zostaną zignorowane. | Nie       |

### <a name="attributes"></a>Atrybuty

| Nazwa           | Opis                                                                                           | Wymagane | Domyślne |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | Nazwa interfejsu API, dla którego ma zostać zastosowany limit szybkości.                                                | Tak      | Nie dotyczy     |
| Rozmowa          | Maksymalna całkowita liczba wywołań dozwolona w przedziale czasu określonym w `renewal-period` . | Tak      | Nie dotyczy     |
| Okres odnawiania | Długość przedziału czasu (w sekundach), w którym liczba dozwolonych żądań powinna być większa niż wartość określona w `calls` .                                              | Tak      | Nie dotyczy     |
| Ponów-po-nazwa nagłówka    | Nazwa nagłówka odpowiedzi, którego wartość jest zalecanym interwałem ponawiania (w sekundach), po przekroczeniu określonej liczby wywołań. |  Nie | Nie dotyczy  |
| Ponów-po-Zmienna-name    | Nazwa zmiennej wyrażenia zasad, która przechowuje Zalecany interwał ponawiania (w sekundach) po przekroczeniu określonego współczynnika wywołań. |  Nie | Nie dotyczy  |
| Pozostałe-wywołania — nazwa nagłówka    | Nazwa nagłówka odpowiedzi, którego wartość po każdym wykonaniu zasad to liczba pozostałych wywołań dozwolonych dla interwału czasu określonego w `renewal-period` . |  Nie | Nie dotyczy  |
| Pozostałe-wywołania-Zmienna-name    | Nazwa zmiennej wyrażenia zasad, która po każdym wykonaniu zasad przechowuje liczbę pozostałych wywołań dozwolonych dla interwału czasu określonego w `renewal-period` . |  Nie | Nie dotyczy  |
| łącznie-Calls-header-Name    | Nazwa nagłówka odpowiedzi, którego wartością jest wartość określona w `calls` . |  Nie | Nie dotyczy  |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące

-   **Zakresy zasad:** produkt, interfejs API, operacja

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a> Ogranicz częstotliwość wywołań według klucza

> [!IMPORTANT]
> Ta funkcja jest niedostępna w warstwie **zużycia** API Management.

`rate-limit-by-key`Zasady uniemożliwiają użycie interfejsu API na podstawie poszczególnych kluczy, ograniczając częstotliwość wywołań do określonej liczby w określonym przedziale czasu. Klucz może mieć dowolną wartość ciągu i jest zwykle dostarczany przy użyciu wyrażenia zasad. Opcjonalny warunek przyrostu można dodać, aby określić, które żądania powinny być wliczane do limitu. Po przekroczeniu tej liczby wywołań obiekt wywołujący otrzymuje `429 Too Many Requests` kod stanu odpowiedzi.

Aby uzyskać więcej informacji i przykłady tych zasad, zobacz [zaawansowane Ograniczanie żądań za pomocą usługi Azure API Management](./api-management-sample-flexible-throttling.md).

> [!CAUTION]
> Ze względu na rozproszoną naturę architektury ograniczanie szybkości nie jest nigdy całkowicie dokładne. Różnica między skonfigurowaną i rzeczywistą liczbą dozwolonych żądań różni się w zależności od ilości żądania, liczby opóźnień zaplecza i innych czynników.

> [!NOTE]
> Aby zrozumieć różnicę między limitami szybkości i przydziałów, [Zobacz limity szybkości i przydziały.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" 
                   retry-after-header-name="header name" retry-after-variable-name="policy expression variable name"
                   remaining-calls-header-name="header name"  remaining-calls-variable-name="policy expression variable name"
                   total-calls-header-name="header name"/> 

```

### <a name="example"></a>Przykład

W poniższym przykładzie Limit szybkości 10 wywołań na 60 sekund jest wybierany przez adres IP obiektu wywołującego. Po każdym wykonaniu zasad pozostałe wywołania dozwolone w danym okresie są przechowywane w zmiennej `remainingCallsPerIP` .

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"
              remaining-calls-variable-name="remainingCallsPerIP"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementy

| Nazwa              | Opis   | Wymagane |
| ----------------- | ------------- | -------- |
| stawka za klucz | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Nazwa                | Opis                                                                                           | Wymagane | Domyślne |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| Rozmowa               | Maksymalna całkowita liczba wywołań dozwolona w przedziale czasu określonym w `renewal-period` . | Tak      | Nie dotyczy     |
| klucz licznika         | Klucz, który ma być używany na potrzeby zasad limitu szybkości.                                                             | Tak      | Nie dotyczy     |
| Zwiększ warunek | Wyrażenie logiczne określające, czy żądanie powinno być zliczane względem współczynnika ( `true` ).        | Nie       | Nie dotyczy     |
| Okres odnawiania      | Długość przedziału czasu (w sekundach), w którym liczba dozwolonych żądań powinna być większa niż wartość określona w `calls` .                                           | Tak      | Nie dotyczy     |
| Ponów-po-nazwa nagłówka    | Nazwa nagłówka odpowiedzi, którego wartość jest zalecanym interwałem ponawiania (w sekundach), po przekroczeniu określonej liczby wywołań. |  Nie | Nie dotyczy  |
| Ponów-po-Zmienna-name    | Nazwa zmiennej wyrażenia zasad, która przechowuje Zalecany interwał ponawiania (w sekundach) po przekroczeniu określonego współczynnika wywołań. |  Nie | Nie dotyczy  |
| Pozostałe-wywołania — nazwa nagłówka    | Nazwa nagłówka odpowiedzi, którego wartość po każdym wykonaniu zasad to liczba pozostałych wywołań dozwolonych dla interwału czasu określonego w `renewal-period` . |  Nie | Nie dotyczy  |
| Pozostałe-wywołania-Zmienna-name    | Nazwa zmiennej wyrażenia zasad, która po każdym wykonaniu zasad przechowuje liczbę pozostałych wywołań dozwolonych dla interwału czasu określonego w `renewal-period` . |  Nie | Nie dotyczy  |
| łącznie-Calls-header-Name    | Nazwa nagłówka odpowiedzi, którego wartością jest wartość określona w `calls` . |  Nie | Nie dotyczy  |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące

-   **Zakresy zasad:** wszystkie zakresy

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a> Ogranicz adresy IP wywołującego

`ip-filter`Filtry zasad (dopuszcza/odrzuca) wywołania z określonych adresów IP i/lub zakresów adresów.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Przykład

W poniższym przykładzie zasada zezwala tylko na żądania pochodzące z pojedynczego adresu IP lub zakresu adresów IP.

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Elementy

| Nazwa                                      | Opis                                         | Wymagane                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| filtr IP                                 | Element główny.                                       | Tak                                                            |
| adres                                   | Określa pojedynczy adres IP, na który ma zostać przefiltrowany.   | Co najmniej jeden `address` `address-range` element jest wymagany. |
| zakres adresów od = "Address" do = "Address" | Określa zakres adresów IP, z których ma zostać przefiltrowany. | Co najmniej jeden `address` `address-range` element jest wymagany. |

### <a name="attributes"></a>Atrybuty

| Nazwa                                      | Opis                                                                                 | Wymagane                                           | Domyślne |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| zakres adresów od = "Address" do = "Address" | Zakres adresów IP, dla których ma być dozwolony lub zablokowany dostęp.                                        | Wymagane, gdy `address-range` element jest używany. | Nie dotyczy     |
| IP-Filter Action = "Zezwalaj na &#124; Zabroń"    | Określa, czy wywołania powinny być dozwolone czy nie dla określonych adresów IP i zakresów. | Tak                                                | Nie dotyczy     |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące
-   **Zakresy zasad:** wszystkie zakresy

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a> Ustawianie przydziału użycia według subskrypcji

`quota`Zasady wymuszają naliczanie i/lub przydział przepustowości przez okres istnienia dla każdej subskrypcji.

> [!IMPORTANT]
> Tych zasad można używać tylko raz dla każdego dokumentu zasad.
>
> [Wyrażeń zasad](api-management-policy-expressions.md) nie można używać w żadnym z atrybutów zasad dla tych zasad.

> [!NOTE]
> Aby zrozumieć różnicę między limitami szybkości i przydziałów, [Zobacz limity szybkości i przydziały.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>Przykład

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementy

| Nazwa      | Opis                                                                                                                                                                                                                                                                                  | Wymagane |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| limit przydziału     | Element główny.                                                                                                                                                                                                                                                                                | Tak      |
| api       | Dodaj jeden lub więcej z tych elementów, aby nałożyć przydział wywołań na interfejsy API w ramach produktu. Przydziały wywołań produktu i interfejsu API są stosowane niezależnie. Do interfejsu API można odwoływać się za pośrednictwem `name` lub `id` . Jeśli podano oba atrybuty, `id` zostaną one użyte i `name` zostaną zignorowane.                    | Nie       |
| operation | Dodaj jeden lub więcej z tych elementów, aby nałożyć przydział wywołań na operacje w interfejsie API. Przydziały produktu, interfejsu API i operacji wywołań są stosowane niezależnie. Operacji można przywoływać za pośrednictwem `name` lub `id` . Jeśli podano oba atrybuty, `id` zostaną one użyte i `name` zostaną zignorowane. | Nie      |

### <a name="attributes"></a>Atrybuty

| Nazwa           | Opis                                                                                               | Wymagane                                                         | Domyślne |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | Nazwa interfejsu API lub operacji, dla której ma zastosowanie przydział.                                             | Tak                                                              | Nie dotyczy     |
| zużywa      | Maksymalna łączna liczba kilobajtów dozwolona w przedziale czasu określonym w `renewal-period` . | `calls` `bandwidth` Należy określić oba jednocześnie. | Nie dotyczy     |
| Rozmowa          | Maksymalna całkowita liczba wywołań dozwolona w przedziale czasu określonym w `renewal-period` .     | `calls` `bandwidth` Należy określić oba jednocześnie. | Nie dotyczy     |
| Okres odnawiania | Czas (w sekundach), po upływie którego zostanie zresetowany przydział. Gdy jest ustawiona na `0` czas, jest ustawiona na nieskończoność. | Tak                                                              | Nie dotyczy     |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące
-   **Zakresy zasad:** produkt

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a> Ustawianie przydziału użycia według klucza

> [!IMPORTANT]
> Ta funkcja jest niedostępna w warstwie **zużycia** API Management.

`quota-by-key`Zasady wymuszają odnowienie i/lub przydział przepustowości dla każdego klucza. Klucz może mieć dowolną wartość ciągu i jest zwykle dostarczany przy użyciu wyrażenia zasad. Opcjonalny warunek przyrostu można dodać, aby określić, które żądania powinny być wliczane do limitu przydziału. Jeśli wiele zasad zwiększy tę samą wartość klucza, jest zwiększana tylko raz dla każdego żądania. Po przekroczeniu liczby wywołań wywołujący otrzymuje `403 Forbidden` kod stanu odpowiedzi.

Aby uzyskać więcej informacji i przykłady tych zasad, zobacz [zaawansowane Ograniczanie żądań za pomocą usługi Azure API Management](./api-management-sample-flexible-throttling.md).

> [!NOTE]
> Aby zrozumieć różnicę między limitami szybkości i przydziałów, [Zobacz limity szybkości i przydziały.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Przykład

W poniższym przykładzie limit przydziału jest poprzedzony przez adres IP obiektu wywołującego.

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementy

| Nazwa  | Opis   | Wymagane |
| ----- | ------------- | -------- |
| limit przydziału | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Nazwa                | Opis                                                                                               | Wymagane                                                         | Domyślne |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| zużywa           | Maksymalna łączna liczba kilobajtów dozwolona w przedziale czasu określonym w `renewal-period` . | `calls` `bandwidth` Należy określić oba jednocześnie. | Nie dotyczy     |
| Rozmowa               | Maksymalna całkowita liczba wywołań dozwolona w przedziale czasu określonym w `renewal-period` .     | `calls` `bandwidth` Należy określić oba jednocześnie. | Nie dotyczy     |
| klucz licznika         | Klucz, który ma być używany dla zasad przydziału.                                                                      | Tak                                                              | Nie dotyczy     |
| Zwiększ warunek | Wyrażenie logiczne określające, czy żądanie powinno być wliczane do limitu przydziału ( `true` )             | Nie                                                               | Nie dotyczy     |
| Okres odnawiania      | Czas (w sekundach), po upływie którego zostanie zresetowany przydział. Gdy jest ustawiona na `0` czas, jest ustawiona na nieskończoność.                                                   | Tak                                                              | Nie dotyczy     |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące
-   **Zakresy zasad:** wszystkie zakresy

## <a name="validate-jwt"></a><a name="ValidateJWT"></a> Weryfikuj token JWT

`validate-jwt`Zasady wymuszają istnienie i ważność tokenu sieci Web JSON (JWT) wyodrębnionego z albo określonego nagłówka HTTP, albo określonego parametru zapytania.

> [!IMPORTANT]
> `validate-jwt`Zasady wymagają, aby `exp` zarejestrowane zastrzeżenie zostało uwzględnione w tokenie JWT, chyba że `require-expiration-time` atrybut jest określony i ma ustawioną wartość `false` .
> `validate-jwt`Zasady obsługują algorytmy podpisywania HS256 i RS256. W przypadku HS256 klucz musi być podany w postaci wbudowanej w ramach zasad w postaci kodowanej algorytmem Base64. W przypadku RS256 klucz można dostarczyć za pośrednictwem punktu końcowego konfiguracji otwartego identyfikatora lub podając identyfikator przekazanego certyfikatu, który zawiera klucz publiczny lub parę wykładników modulo klucza publicznego.
> `validate-jwt`Zasady obsługują tokeny szyfrowane za pomocą kluczy symetrycznych przy użyciu następujących algorytmów szyfrowania: A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
</validate-jwt>

```

### <a name="examples"></a>Przykłady

#### <a name="simple-token-validation"></a>Prosta weryfikacja tokenu

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="token-validation-with-rsa-certificate"></a>Sprawdzanie poprawności tokenu przy użyciu certyfikatu RSA

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key certificate-id="my-rsa-cert" />  <!-- signing key specified as certificate ID, enclosed in double-quotes -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Sprawdzanie poprawności tokenu Azure Active Directory

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Sprawdzanie poprawności tokenu Azure Active Directory B2C

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Autoryzuj dostęp do operacji na podstawie oświadczeń tokenów

Ten przykład pokazuje, jak używać zasad [weryfikacji tokenu JWT](api-management-access-restriction-policies.md#ValidateJWT) do autoryzacji dostępu do operacji na podstawie wartości oświadczenia tokenu.

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

### <a name="elements"></a>Elementy

| Element             | Opis                                                                                                                                                                                                                                                                                                                                           | Wymagane |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Walidacja — JWT        | Element główny.                                                                                                                                                                                                                                                                                                                                         | Tak      |
| odbiorców           | Zawiera listę dopuszczalnych oświadczeń odbiorców, które mogą znajdować się na tokenie. Jeśli są obecne wiele wartości odbiorców, każda wartość zostanie ponowiona, dopóki wszystkie nie zostaną wyczerpane (w której przypadku niepowodzenie walidacji nie powiedzie się) lub dopóki jeden z nich się nie powiedzie. Należy określić co najmniej jedną grupę odbiorców.                                                                     | Nie       |
| wystawca — klucze podpisywania | Lista kluczy zabezpieczeń zakodowanych algorytmem Base64 służących do weryfikowania podpisanych tokenów. Jeśli istnieją wiele kluczy zabezpieczeń, każdy klucz zostanie ponowiony, dopóki wszystkie nie zostaną wyczerpane (w tym przypadku sprawdzanie poprawności nie powiedzie się) lub jeden z nich powiedzie się (przydatne w przypadku przerzucania tokenów). Elementy klucza mają opcjonalny `id` atrybut używany do dopasowywania do `kid` roszczeń. <br/><br/>Alternatywnie Podaj klucz podpisywania wystawcy przy użyciu:<br/><br/> - `certificate-id` w polu Format `<key certificate-id="mycertificate" />` , aby określić identyfikator jednostki certyfikatu [przekazanej](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add) do API Management<br/>-Para modułu RSA `n` i wykładnik `e` w formacie `<key n="<modulus>" e="<exponent>" />` , aby określić parametry RSA w formacie base64url-Encoded               | Nie       |
| Odszyfrowywanie — klucze     | Lista kluczy zakodowanych algorytmem Base64 używana do odszyfrowywania tokenów. Jeśli są obecne wiele kluczy zabezpieczeń, każdy klucz zostanie ponowiony, dopóki wszystkie klucze nie zostaną wyczerpane (w przypadku niepowodzenia walidacji nie powiedzie się) lub klucz powiedzie się. Elementy klucza mają opcjonalny `id` atrybut używany do dopasowywania do `kid` roszczeń.<br/><br/>Alternatywnie Podaj klucz odszyfrowujący przy użyciu:<br/><br/> - `certificate-id` w polu Format `<key certificate-id="mycertificate" />` , aby określić identyfikator jednostki certyfikatu [przekazanej](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add) do API Management                                                 | Nie       |
| wystawców             | Lista akceptowalnych podmiotów, które wystawiły token. Jeśli istnieją wiele wartości wystawcy, każda wartość zostanie ponowiona, dopóki wszystkie nie zostaną wyczerpane (w której przypadku sprawdzanie poprawności nie powiedzie się) lub dopóki jeden z nich się nie powiedzie.                                                                                                                                         | Nie       |
| OpenID Connect-config       | Element służący do określania zgodnego punktu końcowego konfiguracji otwartego identyfikatora, z którego można uzyskać klucze podpisywania i wystawca.                                                                                                                                                                                                                        | Nie       |
| wymagane — oświadczenia     | Zawiera listę oświadczeń, które powinny być obecne w tokenie, aby mógł być uznawany za ważny. Gdy `match` atrybut jest ustawiony na `all` wszystkie wartości w zasadach, muszą być obecne w tokenie, aby Walidacja zakończyła się pomyślnie. Jeśli `match` atrybut jest ustawiony na `any` co najmniej jedno zastrzeżenie, musi być obecne w tokenie, aby Walidacja zakończyła się pomyślnie. | Nie       |

### <a name="attributes"></a>Atrybuty

| Nazwa                            | Opis                                                                                                                                                                                                                                                                                                                                                                                                                                            | Wymagane                                                                         | Domyślne                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| przechylenie zegara                      | Czasu. Użyj, aby określić maksymalną oczekiwaną różnicę czasu między zegarami systemowymi wystawcy tokenów a wystąpieniem API Management.                                                                                                                                                                                                                                                                                                               | Nie                                                                               | 0 sekund                                                                         |
| Niepowodzenie — Walidacja-komunikat o błędzie | Komunikat o błędzie do zwrócenia w treści odpowiedzi HTTP, jeśli token JWT nie przeszedł walidacji. Ten komunikat musi mieć poprawnie zmienione znaki specjalne.                                                                                                                                                                                                                                                                                                 | Nie                                                                               | Domyślny komunikat o błędzie zależy od problemu ze sprawdzaniem poprawności, na przykład "nieobecność tokenu JWT". |
| Niepowodzenie — Walidacja-httpcode      | Kod stanu HTTP do zwrócenia, jeśli JWT nie przeszedł walidacji.                                                                                                                                                                                                                                                                                                                                                                                         | Nie                                                                               | 401                                                                               |
| Nazwa nagłówka                     | Nazwa nagłówka HTTP przechowującego token.                                                                                                                                                                                                                                                                                                                                                                                                         | Jeden z `header-name` `query-parameter-name` lub `token-value` musi być określony. | Nie dotyczy                                                                               |
| Query-Parameter-Name            | Nazwa parametru zapytania przechowującego token.                                                                                                                                                                                                                                                                                                                                                                                                     | Jeden z `header-name` `query-parameter-name` lub `token-value` musi być określony. | Nie dotyczy                                                                               |
| Token-wartość                     | Wyrażenie zwracające ciąg zawierający token JWT                                                                                                                                                                                                                                                                                                                                                                                                     | Jeden z `header-name` `query-parameter-name` lub `token-value` musi być określony. | Nie dotyczy                                                                               |
| identyfikator                              | `id`Atrybut w `key` elemencie pozwala określić ciąg, który zostanie dopasowany do `kid` roszczeń w tokenie (jeśli istnieje), aby dowiedzieć się, jaki klucz jest używany do weryfikacji podpisu.                                                                                                                                                                                                                                           | Nie                                                                               | Nie dotyczy                                                                               |
| match                           | `match`Atrybut `claim` elementu określa, czy każda wartość w zasadach musi być obecna w tokenie, aby Walidacja zakończyła się pomyślnie. Możliwe wartości:<br /><br /> - `all` — Każda wartość w zasadach musi być obecna w tokenie, aby Walidacja zakończyła się pomyślnie.<br /><br /> - `any` -co najmniej jedna wartość elementu Claim musi być obecna w tokenie, aby Walidacja zakończyła się pomyślnie.                                                       | Nie                                                                               | all                                                                               |
| Wymagaj — czas wygaśnięcia         | Typu. Określa, czy w tokenie jest wymagane żądanie wygaśnięcia.                                                                                                                                                                                                                                                                                                                                                                               | Nie                                                                               | true                                                                              |
| Wymagaj-schemat                  | Nazwa schematu tokenów, np. "Bearer". Gdy ten atrybut jest ustawiony, zasady zapewnią, że określony schemat jest obecny w wartości nagłówka autoryzacji.                                                                                                                                                                                                                                                                                    | Nie                                                                               | Nie dotyczy                                                                               |
| Wymagaj-podpisane-tokeny           | Typu. Określa, czy token musi być podpisany.                                                                                                                                                                                                                                                                                                                                                                                           | Nie                                                                               | true                                                                              |
| rozdzielając                       | Ciąg. Określa separator (np. ","), który będzie używany do wyodrębniania zestawu wartości z roszczeń wielowartościowych.                                                                                                                                                                                                                                                                                                                                          | Nie                                                                               | Nie dotyczy                                                                               |
| url                             | Otwórz adres URL punktu końcowego konfiguracji identyfikatora, z którego można uzyskać metadane konfiguracji otwartego identyfikatora. Odpowiedź powinna być zgodna ze specyfikacją zdefiniowaną w adresie URL: `https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata` . W przypadku Azure Active Directory użyj następującego adresu URL: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` podstawianie nazwy dzierżawy katalogu, np. `contoso.onmicrosoft.com` . | Tak                                                                              | Nie dotyczy                                                                               |
| Output-token-Variable-Name      | Ciąg. Nazwa zmiennej kontekstowej, która będzie otrzymywać wartość tokenu jako obiekt typu [`Jwt`](api-management-policy-expressions.md) po pomyślnym sprawdzeniu tokenu                                                                                                                                                                                                                                                                                     | Nie                                                                               | Nie dotyczy                                                                               |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące
-   **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

-   [Zasady w API Management](api-management-howto-policies.md)
-   [Przekształć interfejsy API](transform-api.md)
-   [Dokumentacja zasad](./api-management-policies.md) pełna lista instrukcji zasad i ich ustawień
-   [Przykłady zasad](./policy-reference.md)
