---
title: Zasady API Management azure | Microsoft Docs
description: Dowiedz się więcej na temat zasad między domenami dostępnych do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/01/2021
ms.author: apimpm
ms.openlocfilehash: 6f074ff389971fa56da7838a9a46ec5c4d42dc5a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739102"
---
# <a name="api-management-cross-domain-policies"></a>API Management cross domain policies (Zasady usługi API Management obejmujące różne domeny)
Ten temat zawiera informacje dotyczące następujących zasad API Management tematu. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [Zasady w API Management](./api-management-policies.md).

## <a name="cross-domain-policies"></a><a name="CrossDomainPolicies"></a> Zasady między domenami

- [Zezwalaj na wywołania między domenami](api-management-cross-domain-policies.md#AllowCrossDomainCalls) — udostępnia interfejs API z poziomu programu Adobe Flash i klientów programu Microsoft Silverlight opartych na przeglądarce.
- [CORS](api-management-cross-domain-policies.md#CORS) — dodaje obsługę współużytkowania zasobów między źródłami (CORS) do operacji lub interfejsu API w celu zezwalania na wywołania między domenami z klientów opartych na przeglądarce.
- [JSONP](api-management-cross-domain-policies.md#JSONP) — dodaje kod JSON z obsługą dopełnienia (JSONP) do operacji lub interfejsu API w celu zezwalania na wywołania między domenami z klientów opartych na przeglądarce języka JavaScript.

## <a name="allow-cross-domain-calls"></a><a name="AllowCrossDomainCalls"></a> Zezwalaj na wywołania między domenami
Użyj zasad, aby udostępnić interfejs API z klientów opartych na przeglądarce Adobe Flash i `cross-domain` Microsoft Silverlight.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>Przykład

```xml
<cross-domain>
        <allow-http-request-headers-from domain='*' headers='*' />
</cross-domain>
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|między domenami|Element główny. Elementy podrzędne muszą być zgodne ze specyfikacją pliku zasad między [domenami firmy Adobe.](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)|Tak|

### <a name="usage"></a>Użycie
Tych zasad można używać w następujących sekcjach [zasad](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes).

- **Sekcje zasad:** ruch przychodzący
- **Zakresy zasad:** wszystkie zakresy

## <a name="cors"></a><a name="CORS"></a> Cors
Zasady dodają obsługę współużytkowania zasobów między źródłami (CORS) do operacji lub interfejsu API w celu zezwalania na wywołania między domenami `cors` z klientów opartych na przeglądarce. 

> [!NOTE]
> Jeśli żądanie pasuje do operacji z metodą OPTIONS zdefiniowaną w interfejsie API, logika przetwarzania żądań wstępnych skojarzona z zasadami CORS nie zostanie wykonana. W związku z tym takie operacje mogą służyć do implementowania niestandardowej logiki przetwarzania przed lotem.

Dzięki funkcji CORS przeglądarka i serwer mogą wchodzić w interakcje i określać, czy zezwalać na określone żądania między źródłami (tj. WYWOŁANIA XMLHttpRequests wykonane z języka JavaScript na stronie internetowej do innych domen). Zapewnia to większą elastyczność niż zezwalanie tylko na żądania tego samego źródła, ale jest bezpieczniejsze niż zezwalanie na wszystkie żądania między źródłami.

Aby włączyć interaktywną konsolę w portalu dla deweloperów, należy zastosować zasady CORS. Aby uzyskać szczegółowe informacje, [zapoznaj się z dokumentacją](./developer-portal-faq.md#cors) portalu dla deweloperów.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<cors allow-credentials="false|true" terminate-unmatched-request="true|false">
    <allowed-origins>
        <origin>origin uri</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="number of seconds">
        <method>http verb</method>
    </allowed-methods>
    <allowed-headers>
        <header>header name</header>
    </allowed-headers>
    <expose-headers>
        <header>header name</header>
    </expose-headers>
</cors>
```

### <a name="example"></a>Przykład
W tym przykładzie pokazano, jak obsługiwać żądania przed lotem, takie jak żądania z niestandardowymi nagłówkami lub metodami innymi niż GET i POST. Aby obsługiwać nagłówki niestandardowe i dodatkowe czasowniki HTTP, użyj sekcji i , `allowed-methods` `allowed-headers` jak pokazano w poniższym przykładzie.

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <!-- Localhost useful for development -->
        <origin>http://localhost:8080/</origin>
        <origin>http://example.com/</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="300">
        <method>GET</method>
        <method>POST</method>
        <method>PATCH</method>
        <method>DELETE</method>
    </allowed-methods>
    <allowed-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
        <header>x-zumo-version</header>
        <header>x-zumo-auth</header>
        <header>content-type</header>
        <header>accept</header>
    </allowed-headers>
    <expose-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
    </expose-headers>
</cors>
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|Cors|Element główny.|Tak|Nie dotyczy|
|dozwolone źródła|Zawiera `origin` elementy, które opisują dozwolone źródła dla żądań między domenami. `allowed-origins` może zawierać pojedynczy element określający zezwalanie na dowolne źródło lub co najmniej jeden `origin` `*` `origin` element, który zawiera wartość URI.|Tak|Nie dotyczy|
|połączenie pierwotne|Wartością może być `*` zezwalanie na wszystkie źródła lub URI określający pojedyncze źródło. Ten numer URI musi zawierać schemat, hosta i port.|Tak|Jeśli port zostanie pominięty w adresie URI, port 80 będzie używany dla protokołu HTTP, a port 443 dla protokołu HTTPS.|
|allowed-methods|Ten element jest wymagany, jeśli dozwolone są metody inne niż GET lub POST. Zawiera `method` elementy, które określają obsługiwane czasowniki HTTP. Wartość wskazuje `*` wszystkie metody.|Nie|Jeśli ta sekcja nie jest obecna, obsługiwane są get i POST.|
|method|Określa czasownik HTTP.|Jeśli sekcja `method` jest obecna, wymagany jest co najmniej jeden `allowed-methods` element.|Nie dotyczy|
|dozwolone nagłówki|Ten element `header` zawiera elementy określające nazwy nagłówków, które mogą zostać uwzględnione w żądaniu.|Nie|Nie dotyczy|
|expose-headers|Ten element `header` zawiera elementy określające nazwy nagłówków, które będą dostępne dla klienta.|Nie|Nie dotyczy|
|header|Określa nazwę nagłówka.|Co najmniej jeden `header` element jest wymagany w `allowed-headers` elemencie lub , jeśli sekcja jest `expose-headers` obecna.|Nie dotyczy|

### <a name="attributes"></a>Atrybuty

|Nazwa|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|allow-credentials|Nagłówek w odpowiedzi wstępnej zostanie ustawiony na wartość tego atrybutu i będzie miał wpływ na możliwość przesyłania poświadczeń przez klienta w żądaniach `Access-Control-Allow-Credentials` między domenami.|Nie|fałsz|
|terminate-unmatched-request|Ten atrybut kontroluje przetwarzanie żądań między źródłami, które nie są zgodne z ustawieniami zasad CORS. Gdy żądanie OPTIONS jest przetwarzane jako żądanie wstępne i nie jest zgodne z ustawieniami zasad CORS: jeśli atrybut jest ustawiony na wartość , natychmiast zakończ żądanie z pustą odpowiedzią `true` 200 OK. Jeśli atrybut jest ustawiony na wartość , sprawdź ruch przychodzący dla innych zasad CORS w zakresie, które są bezpośrednimi elementami kluczowymi elementu `false` przychodzącego, i zastosuj je.  Jeśli żadne zasady CORS nie zostaną znalezione, zakończ żądanie pustą odpowiedzią 200 OK. Gdy żądanie GET lub HEAD zawiera nagłówek Origin (i dlatego jest przetwarzane jako żądanie między źródłami) i nie jest zgodne z ustawieniami zasad CORS: jeśli atrybut jest ustawiony na wartość , natychmiast zakończ żądanie z pustą odpowiedzią `true` 200 OK; Jeśli atrybut jest ustawiony na wartość , zezwoń na normalne kontynuowanie żądania i nie dodawaj nagłówków `false` CORS do odpowiedzi.|Nie|true|
|preflight-result-max-age|Nagłówek w odpowiedzi wstępnej zostanie ustawiony na wartość tego atrybutu i będzie miał wpływ na możliwość buforowania odpowiedzi przed `Access-Control-Max-Age` lotem przez agenta użytkownika.|Nie|0|

### <a name="usage"></a>Użycie
Tych zasad można używać w następujących sekcjach [zasad](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes).

- **Sekcje zasad:** ruch przychodzący
- **Zakresy zasad:** wszystkie zakresy

## <a name="jsonp"></a><a name="JSONP"></a> Jsonp
Zasady dodają kod JSON z obsługą dopełnienia (JSONP) do operacji lub interfejsu API w celu zezwalania na wywołania między domenami z klientów opartych na przeglądarce `jsonp` języka JavaScript. JSONP to metoda używana w programach JavaScript do żądania danych z serwera w innej domenie. JSONP pomija ograniczenie wymuszane przez większość przeglądarek internetowych, w których dostęp do stron internetowych musi znajdować się w tej samej domenie.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Przykład

```xml
<jsonp callback-parameter-name="cb" />
```

Wywołanie metody bez parametru wywołania zwrotnego ?cb=XXX spowoduje zwrócenie zwykłego danych JSON (bez otoki wywołania funkcji).

Jeśli dodasz parametr wywołania zwrotnego, zwróci on wynik JSONP, opakowując oryginalne wyniki `?cb=XXX` JSON wokół funkcji wywołania zwrotnego, na przykład `XYZ('<json result goes here>');`

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|Jsonp|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

|Nazwa|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|nazwa parametru wywołania zwrotnego|Wywołanie funkcji Języka JavaScript między domenami poprzedzone w pełni kwalifikowaną nazwą domeny, w której znajduje się funkcja.|Tak|Nie dotyczy|

### <a name="usage"></a>Użycie
Tych zasad można używać w następujących sekcjach [zasad](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes).

- **Sekcje zasad:** ruch wychodzący
- **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

+ [Zasady w API Management](api-management-howto-policies.md)
+ [Przekształcanie interfejsów API](transform-api.md)
+ [Informacje o](./api-management-policies.md) zasadach dotyczące pełnej listy instrukcji zasad i ich ustawień
+ [Przykłady zasad](./policy-reference.md)
