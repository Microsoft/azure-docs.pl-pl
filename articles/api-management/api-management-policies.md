---
title: Zasady usługi Azure API Management | Microsoft Docs
description: Dowiedz się więcej o zasadach dostępnych do użycia w usłudze Azure API Management. Zasady umożliwiają wydawcy zmianę zachowania interfejsu API za pomocą konfiguracji.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: fe5c13b8c34d29be9a62717215271c10a48e9ac6
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076899"
---
# <a name="api-management-policies"></a>API Management policies
Ta sekcja zawiera informacje dotyczące następujących zasad API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasady w API Management](api-management-howto-policies.md).

 Zasady są zaawansowaną możliwością systemu umożliwiającą wydawcy zmianę zachowania interfejsu API za pomocą konfiguracji. Zasady są zbiorem instrukcji, które są wykonywane sekwencyjnie na żądanie lub odpowiedź interfejsu API. Popularne instrukcje obejmują konwersję formatu z XML do JSON i ograniczanie szybkości wywołań, aby ograniczyć liczbę wywołań przychodzących od dewelopera. Dostępnych jest wiele innych zasad.

 Wyrażenia zasad mogą służyć jako wartości atrybutów lub wartości tekstowe w dowolnej z zasad usługi API Management, o ile w zasadach nie określono inaczej. Niektóre zasady, np. [Przepływ sterowania](api-management-advanced-policies.md#choose) i [Ustawianie zmiennej](api-management-advanced-policies.md#set-variable), są oparte na wyrażeniach zasad. Aby uzyskać więcej informacji, zobacz tematy [Advanced policies](api-management-advanced-policies.md#AdvancedPolicies) (Zaawansowane zasady) i [Policy expressions](api-management-policy-expressions.md) (Wyrażenia zasad).

##  <a name="policies"></a><a name="ProxyPolicies"></a> Jazd

-   [Zasady ograniczeń dostępu](api-management-access-restriction-policies.md#AccessRestrictionPolicies)
    -   [Sprawdź nagłówek HTTP](api-management-access-restriction-policies.md#CheckHTTPHeader) — wymusza istnienie i/lub wartość nagłówka HTTP.
    -   [Ogranicz częstotliwość wywołań przez subskrypcję](api-management-access-restriction-policies.md#LimitCallRate) — uniemożliwia użycie interfejsu API przez ograniczenie liczby wywołań dla każdej subskrypcji.
    -   [Ogranicz częstotliwość wywołań według klucza](api-management-access-restriction-policies.md#LimitCallRateByKey) — uniemożliwia użycie interfejsu API przez ograniczenie liczby wywołań na podstawie poszczególnych kluczy.
    -   [Ogranicz adresy IP wywołującego](api-management-access-restriction-policies.md#RestrictCallerIPs) — filtry (dozwolone/odmawiające) wywołania z określonych adresów i/lub zakresów adresów.
    -   [Ustawianie limitu przydziału użycia według subskrypcji](api-management-access-restriction-policies.md#SetUsageQuota) — umożliwia wymuszenie naliczania i/lub przydziału przepustowości dla każdej subskrypcji.
    -   [Ustawianie przydziału użycia według klucza](api-management-access-restriction-policies.md#SetUsageQuotaByKey) — umożliwia wymuszenie naliczania i/lub przydziału przepustowości dla każdego klucza.
    -   [Sprawdzanie poprawności tokenu JWT](api-management-access-restriction-policies.md#ValidateJWT) — wymusza istnienie i ważność tokenu JWT wyodrębnionego z albo określonego nagłówka HTTP lub określonego parametru zapytania.
-   [Zasady zaawansowane](api-management-advanced-policies.md#AdvancedPolicies)
    -   [Przepływ sterowania](api-management-advanced-policies.md#choose) — warunkowo stosuje instrukcje zasad na podstawie oceny wyrażeń logicznych.
    -   [Żądanie przesyłania dalej](api-management-advanced-policies.md#ForwardRequest) przesyła żądanie do usługi zaplecza.
    -   [Ograniczenie współbieżności](api-management-advanced-policies.md#LimitConcurrency) uniemożliwia wykonywanie załączonych zasad przez więcej niż określoną liczbę żądań w danym momencie.
    -   [Logowanie do centrum zdarzeń](api-management-advanced-policies.md#log-to-eventhub) — wysyła komunikaty w określonym formacie do obiektu docelowego komunikatu zdefiniowanego przez jednostkę rejestratora.
    -   [Makieta odpowiedzi](api-management-advanced-policies.md#mock-response) — przerywa wykonywanie potoku i zwraca zamakietę odpowiedzi bezpośrednio do obiektu wywołującego.
    -   [Ponawianie](api-management-advanced-policies.md#Retry) próbuje wykonać załączone instrukcje zasad, jeśli i do momentu spełnienia warunku. Wykonanie będzie powtarzane w określonych odstępach czasu i do określonej liczby ponownych prób.
    -   [Odpowiedź zwrotna](api-management-advanced-policies.md#ReturnResponse) — przerywa wykonywanie potoku i zwraca określoną odpowiedź bezpośrednio do obiektu wywołującego.
    -   [Wyślij jednokierunkowe żądanie](api-management-advanced-policies.md#SendOneWayRequest) — wysyła żądanie do określonego adresu URL bez oczekiwania na odpowiedź.
    -   [Wyślij żądanie](api-management-advanced-policies.md#SendRequest) — wysyła żądanie do określonego adresu URL.
    -   [Ustaw serwer proxy HTTP](api-management-advanced-policies.md#SetHttpProxy) — umożliwia kierowanie żądań przesyłanych dalej za pośrednictwem serwera proxy HTTP.
    -   [Ustaw zmienną](api-management-advanced-policies.md#set-variable) — utrwalanie wartości w nazwanej zmiennej kontekstowej w celu późniejszego dostępu.
    -   [Ustaw metodę żądania](api-management-advanced-policies.md#SetRequestMethod) — pozwala zmienić metodę http dla żądania.
    -   [Ustawianie kodu stanu](api-management-advanced-policies.md#SetStatus) — zmienia kod stanu HTTP na określoną wartość.
    -   [Trace](api-management-advanced-policies.md#Trace) — dodaje niestandardowe ślady do danych wyjściowych [inspektora interfejsu API](./api-management-howto-api-inspector.md) , Application Insights telemetrii i dzienników zasobów.
    -   [Zaczekaj](api-management-advanced-policies.md#Wait) — oczekiwanie na załączone [żądanie wysłania](api-management-advanced-policies.md#SendRequest), [Pobierz wartość z pamięci podręcznej](api-management-caching-policies.md#GetFromCacheByKey)lub zasady [przepływu sterowania](api-management-advanced-policies.md#choose) do ukończenia przed kontynuowaniem.
-   [Zasady uwierzytelniania](api-management-authentication-policies.md#AuthenticationPolicies)
    -   [Uwierzytelnianie z](api-management-authentication-policies.md#Basic) uwierzytelnianiem Basic z użyciem usługi wewnętrznej bazy danych przy użyciu uwierzytelniania podstawowego.
    -   [Uwierzytelnianie przy użyciu certyfikatu klienta](api-management-authentication-policies.md#ClientCertificate) — uwierzytelnianie za pomocą usługi zaplecza przy użyciu certyfikatów klienta.
    -   [Uwierzytelnianie przy użyciu tożsamości zarządzanej](api-management-authentication-policies.md#ManagedIdentity) — uwierzytelnianie za pomocą usługi zaplecza przy użyciu [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md).
-   [Caching policies](api-management-caching-policies.md#CachingPolicies)
    -   [Pobierz z pamięci podręcznej](api-management-caching-policies.md#GetFromCache) — wykonaj wyszukiwanie w pamięci podręcznej i zwróć poprawną buforowaną odpowiedź, jeśli jest dostępna.
    -   [Przechowywanie w pamięci](api-management-caching-policies.md#StoreToCache) podręcznej w pamięci podręcznej zgodnie z określoną konfiguracją kontroli pamięci podręcznej.
    -   [Pobierz wartość z pamięci podręcznej](api-management-caching-policies.md#GetFromCacheByKey) — Pobierz zbuforowany element według klucza.
    -   [Wartość magazynu w pamięci podręcznej](api-management-caching-policies.md#StoreToCacheByKey) — przechowywanie elementu w pamięci podręcznej według klucza.
    -   [Usuń wartość z pamięci podręcznej](api-management-caching-policies.md#RemoveCacheByKey) — Usuń element z pamięci podręcznej według klucza.
-   [Zasady międzydomenowe](api-management-cross-domain-policies.md#CrossDomainPolicies)
    -   [Zezwalaj na wywołania międzydomenowe](api-management-cross-domain-policies.md#AllowCrossDomainCalls) — umożliwia dostęp do interfejsu API z poziomu klientów opartych na przeglądarce Adobe Flash i Microsoft Silverlight.
    -   [CORS](api-management-cross-domain-policies.md#CORS) — dodaje obsługę funkcji udostępniania zasobów między źródłami (CORS) do operacji lub interfejsu API w celu zezwalania na wywołania międzydomenowe z klientów opartych na przeglądarce.
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) — dodaje kod JSON z obsługą dopełnienia (JSONP) do operacji lub interfejsu API w celu zezwalania na wywołania międzydomenowe z klientów opartych na przeglądarce JavaScript.
-   [Zasady transformacji](api-management-transformation-policies.md#TransformationPolicies)
    -   [Convert JSON do formatu XML](api-management-transformation-policies.md#ConvertJSONtoXML) — konwertuje treść żądania lub odpowiedzi z formatu JSON na XML.
    -   [Konwertuj kod XML na format JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) — konwertuje treść żądania lub odpowiedzi z formatu XML na notację JSON.
    -   [Znajdź i Zamień ciąg w treści](api-management-transformation-policies.md#Findandreplacestringinbody) — umożliwia znalezienie żądania lub podciągu odpowiedzi i zastąpienie go innym podciągiem.
    -   [Maskowanie adresów URL w](api-management-transformation-policies.md#MaskURLSContent) treści odpowiedzi, aby wskazywały na równoważne łącze za pośrednictwem bramy.
    -   [Ustawianie usługi zaplecza](api-management-transformation-policies.md#SetBackendService) — zmienia usługę zaplecza dla żądania przychodzącego.
    -   [Ustaw treść](api-management-transformation-policies.md#SetBody) — ustawia treść wiadomości dla żądań przychodzących i wychodzących.
    -   [Ustawianie nagłówka HTTP](api-management-transformation-policies.md#SetHTTPheader) — przypisuje wartość do istniejącej odpowiedzi i/lub nagłówka żądania lub dodaje nową odpowiedź i/lub nagłówek żądania.
    -   [Ustaw parametr ciągu zapytania](api-management-transformation-policies.md#SetQueryStringParameter) — dodaje, zastępuje wartość lub usuwa parametr ciągu zapytania żądania.
    -   [Ponownie Napisz adres URL](api-management-transformation-policies.md#RewriteURL) — konwertuje adres URL żądania z jego formularza publicznego na formularz oczekiwany przez usługę sieci Web.
    -   [Przekształcanie kodu XML przy użyciu XSLT](api-management-transformation-policies.md#XSLTransform) — stosuje transformację XSL do pliku XML w treści żądania lub odpowiedzi.
- [Zasady integracji Dapr](api-management-dapr-policies.md)
    - [Wyślij żądanie do usługi](api-management-dapr-policies.md#invoke) — program używa środowiska uruchomieniowego Dapr do lokalizowania i niezawodnego komunikowania się z mikrousługą Dapr.
    -  [Wyślij wiadomość do publikowania/podtematu](api-management-dapr-policies.md#pubsub) — używa środowiska uruchomieniowego Dapr, aby opublikować komunikat w temacie publikowanie/subskrybowanie. publikowanie/subskrybowanie wiadomości/Readme. MD), aby dowiedzieć się więcej na temat publikowania/subskrybowania wiadomości w Dapr.
    -  [Wyzwalanie powiązania danych wyjściowych](api-management-dapr-policies.md#bind) — używa środowiska uruchomieniowego Dapr do wywoływania systemu zewnętrznego za pośrednictwem powiązania danych wyjściowych.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

+ [Zasady w API Management](api-management-howto-policies.md)
+ [Przekształć interfejsy API](transform-api.md)
+ [Przykłady zasad](./policy-reference.md)