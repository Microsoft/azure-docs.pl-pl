---
title: Zasady zaawansowane platformy Azure API Management | Microsoft Docs
description: Dowiedz się więcej na temat zaawansowanych zasad dostępnych do użycia w usłudze Azure API Management. Zobacz przykłady i wyświetlanie dodatkowych dostępnych zasobów.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 01d50f6228d63801f62ae933a8367f842d89ef97
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071374"
---
# <a name="api-management-advanced-policies"></a>Zaawansowane zasady usługi API Management

Ten temat zawiera informacje dotyczące następujących zasad API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasady w API Management](./api-management-policies.md).

## <a name="advanced-policies"></a><a name="AdvancedPolicies"></a> Zasady zaawansowane

-   [Przepływ sterowania](api-management-advanced-policies.md#choose) — warunkowo stosuje instrukcje zasad na podstawie wyników oceny [wyrażeń](api-management-policy-expressions.md)logicznych.
-   [Żądanie przesyłania dalej](#ForwardRequest) przesyła żądanie do usługi zaplecza.
-   [Ograniczenie współbieżności](#LimitConcurrency) uniemożliwia wykonywanie załączonych zasad przez więcej niż określoną liczbę żądań w danym momencie.
-   [Logowanie do centrum zdarzeń](#log-to-eventhub) — wysyła komunikaty w określonym formacie do centrum zdarzeń zdefiniowanego przez jednostkę rejestratora.
-   [Makieta odpowiedzi](#mock-response) — przerywa wykonywanie potoku i zwraca zamakietę odpowiedzi bezpośrednio do obiektu wywołującego.
-   [Ponawianie](#Retry) próbuje wykonać załączone instrukcje zasad, jeśli i do momentu spełnienia warunku. Wykonanie będzie powtarzane w określonych odstępach czasu i do określonej liczby ponownych prób.
-   [Odpowiedź zwrotna](#ReturnResponse) — przerywa wykonywanie potoku i zwraca określoną odpowiedź bezpośrednio do obiektu wywołującego.
-   [Wyślij jednokierunkowe żądanie](#SendOneWayRequest) — wysyła żądanie do określonego adresu URL bez oczekiwania na odpowiedź.
-   [Wyślij żądanie](#SendRequest) — wysyła żądanie do określonego adresu URL.
-   [Ustaw serwer proxy HTTP](#SetHttpProxy) — umożliwia kierowanie żądań przesyłanych dalej za pośrednictwem serwera proxy HTTP.
-   [Ustaw metodę żądania](#SetRequestMethod) — pozwala zmienić metodę http dla żądania.
-   [Ustawianie kodu stanu](#SetStatus) — zmienia kod stanu HTTP na określoną wartość.
-   [Set Variable](api-management-advanced-policies.md#set-variable) — utrwala wartość w nazwanej zmiennej [kontekstowej](api-management-policy-expressions.md#ContextVariables) w celu późniejszego dostępu.
-   [Trace](#Trace) — dodaje niestandardowe ślady do danych wyjściowych [inspektora interfejsu API](./api-management-howto-api-inspector.md) , Application Insights telemetrii i dzienników zasobów.
-   [Zaczekaj](#Wait) — oczekiwanie na załączone [żądanie wysłania](api-management-advanced-policies.md#SendRequest), [Pobierz wartość z pamięci podręcznej](api-management-caching-policies.md#GetFromCacheByKey)lub zasady [przepływu sterowania](api-management-advanced-policies.md#choose) do ukończenia przed kontynuowaniem.

## <a name="control-flow"></a><a name="choose"></a> Przepływ sterowania

`choose`Zasady stosują się do załączonych instrukcji zasad na podstawie wyniku oceny wyrażeń logicznych, podobnie jak konstrukcja if-then-else lub Switch w języku programowania.

### <a name="policy-statement"></a><a name="ChoosePolicyStatement"></a> Instrukcja zasad

```xml
<choose>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <otherwise>
        <!— one or more policy statements to be applied if none of the above conditions are true  -->
</otherwise>
</choose>
```

Zasady przepływu sterowania muszą zawierać co najmniej jeden `<when/>` element. `<otherwise/>`Element jest opcjonalny. Warunki w `<when/>` elementach są oceniane w kolejności ich wyglądu w ramach zasad. Deklaracje zasad ujęte w pierwszym `<when/>` elemencie z atrybutem Condition Equals `true` zostaną zastosowane. Zasady zawarte w `<otherwise/>` elemencie (jeśli istnieją) będą stosowane, jeśli wszystkie `<when/>` atrybuty warunku elementu są `false` .

### <a name="examples"></a>Przykłady

#### <a name="example"></a><a name="ChooseExample"></a> Przyklad

Poniższy przykład ilustruje zasady [zestawu zmiennych](api-management-advanced-policies.md#set-variable) i dwie zasady przepływu sterowania.

Zasada Ustawianie zmiennej znajduje się w sekcji przychodzące i tworzy `isMobile` zmienną [kontekstu](api-management-policy-expressions.md#ContextVariables) logicznego, która ma wartość true, jeśli `User-Agent` nagłówek żądania zawiera tekst `iPad` lub `iPhone` .

Zasady pierwszego przepływu sterowania są również w sekcji przychodzące i warunkowo stosuje jedną z dwóch [ustawionych zasad parametrów ciągu zapytania](api-management-transformation-policies.md#SetQueryStringParameter) w zależności od wartości `isMobile` zmiennej kontekstowej.

Zasada drugiego przepływu sterowania znajduje się w sekcji wychodzące i warunkowo stosuje zasady [Konwertuj XML do JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) , gdy `isMobile` jest ustawiony na `true` .

```xml
<policies>
    <inbound>
        <set-variable name="isMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <set-query-parameter name="mobile" exists-action="override">
                    <value>true</value>
                </set-query-parameter>
            </when>
            <otherwise>
                <set-query-parameter name="mobile" exists-action="override">
                    <value>false</value>
                </set-query-parameter>
            </otherwise>
        </choose>
    </inbound>
    <outbound>
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>
            </when>
        </choose>
    </outbound>
</policies>
```

#### <a name="example"></a>Przykład

Ten przykład pokazuje, jak wykonywać filtrowanie zawartości przez usunięcie elementów danych z odpowiedzi otrzymanej z usługi wewnętrznej bazy wiedzy podczas korzystania z `Starter` produktu. Aby zapoznać się z prezentacją konfigurowania i korzystania z tych zasad, zobacz temat [Cloud okładki epizod 177: więcej API Management funkcji z Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkie przekazanie do 34:30. Zacznij od 31:50, aby zapoznać się z omówieniem [interfejsu API prognozowania ciemnej przestrzeniki](https://developer.forecast.io/) używanej w tej wersji demonstracyjnej.

```xml
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->
<choose>
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">
    <set-body>@{
        var response = context.Response.Body.As<JObject>();
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
          response.Property (key).Remove ();
        }
        return response.ToString();
      }
    </set-body>
  </when>
</choose>
```

### <a name="elements"></a>Elementy

| Element   | Opis                                                                                                                                                                                                                                                               | Wymagane |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| następnie    | Element główny.                                                                                                                                                                                                                                                             | Tak      |
| czasie      | Warunek, który ma być używany dla `if` lub `ifelse` części `choose` zasad. Jeśli `choose` zasady zawierają wiele `when` sekcji, są oceniane sekwencyjnie. Gdy `condition` element elementu when ma wartość `true` , nie są oceniane żadne dalsze `when` warunki. | Tak      |
| przypadku | Zawiera fragment kodu zasad, który ma być używany, jeśli nie `when` ma żadnych warunków do obliczenia `true` .                                                                                                                                                                               | Nie       |

### <a name="attributes"></a>Atrybuty

| Atrybut                                              | Opis                                                                                               | Wymagane |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| warunek = "wyrażenie logiczne &#124; stałej logicznej" | Wyrażenie logiczne lub stała do oceny, gdy `when` zostanie oceniona zawierająca ją instrukcja Policy. | Tak      |

### <a name="usage"></a><a name="ChooseUsage"></a> Wykorzystywani

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="forward-request"></a><a name="ForwardRequest"></a> Prześlij żądanie dalej

`forward-request`Zasady przesyłają żądanie przychodzące do usługi wewnętrznej bazy danych określonej w [kontekście](api-management-policy-expressions.md#ContextVariables)żądania. Adres URL usługi wewnętrznej bazy danych jest określony w [ustawieniach](./import-and-publish.md) interfejsu API i można go zmienić przy użyciu zasad dla [Ustawienia usługi zaplecza](api-management-transformation-policies.md) .

> [!NOTE]
> Usunięcie tych zasad spowoduje, że żądanie nie zostanie przekazane do usługi wewnętrznej bazy danych, a zasady w sekcji wychodzącej są oceniane natychmiast po pomyślnym zakończeniu zasad w sekcji przychodzące.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<forward-request timeout="time in seconds" follow-redirects="false | true" buffer-request-body="false | true" fail-on-error-status-code="false | true"/>
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

Poniższe zasady poziomu interfejsu API przesyłają dalej wszystkie żądania interfejsu API do usługi wewnętrznej bazy danych z interwałem limitu czasu wynoszącym 60 sekund.

```xml
<!-- api level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="60"/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Przykład

Te zasady poziomu operacji używają `base` elementu do dziedziczenia zasad zaplecza z nadrzędnego zakresu poziomu interfejsu API.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <base/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Przykład

Te zasady na poziomie operacji jawnie przekazują wszystkie żądania do usługi wewnętrznej bazy danych o limicie czasu 120 i nie dziedziczą zasad zaplecza nadrzędnego interfejsu API. Jeśli usługa zaplecza odpowie z kodem stanu błędu z 400 do 599 włącznie, zostanie wyzwolona sekcja [w przypadku błędu](api-management-error-handling-policies.md) .

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="120" fail-on-error-status-code="true" />
        <!-- effective policy. note the absence of <base/> -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Przykład

Te zasady poziomu operacji nie przesyłają dalej żądań do usługi wewnętrznej bazy danych.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <!-- no forwarding to backend -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

### <a name="elements"></a>Elementy

| Element         | Opis   | Wymagane |
| --------------- | ------------- | -------- |
| Prześlij dalej żądania | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut                                     | Opis                                                                                                                                                                                                                                                                                                    | Wymagane | Domyślne |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| Timeout = "Integer"                             | Czas (w sekundach) oczekiwania na zwrócenie nagłówków odpowiedzi HTTP przez usługę zaplecza przed podjęciem błędu limitu czasu. Wartość minimalna to 0 s. Wartości większe niż 240 sekund mogą nie być honorowane, ponieważ źródłowa infrastruktura sieciowa może porzucić bezczynne połączenia po tym czasie. | Nie       | Brak    |
| Postępuj zgodnie z przekierowaniami = "false &#124; true"          | Określa, czy przekierowania z usługi wewnętrznej bazy danych następuje przez bramę, czy zwracane do obiektu wywołującego.                                                                                                                                                                                                    | Nie       | fałsz   |
| buffer-Request-Body = "false &#124; true"       | Kiedy wartość "true" żądania jest buforowana i zostanie ponownie użyta podczas [ponawiania](api-management-advanced-policies.md#Retry).                                                                                                                                                                                               | Nie       | fałsz   |
| Niepowodzenie-w-Error-status-Code = "false &#124; true" | Po ustawieniu na wartość true Wyzwalaj [w sekcji Error](api-management-error-handling-policies.md) dla kodów odpowiedzi z zakresu od 400 do 599 włącznie.                                                                                                                                                                      | Nie       | fałsz   |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** zaplecze
-   **Zakresy zasad:** wszystkie zakresy

## <a name="limit-concurrency"></a><a name="LimitConcurrency"></a> Ogranicz współbieżność

`limit-concurrency`Zasady te uniemożliwiają wykonywanie zawartych zasad przez więcej niż określoną liczbę żądań w dowolnym momencie. Po przekroczeniu tej liczby nowe żądania będą natychmiast kończyć się niepowodzeniem z 429 zbyt dużą liczbą żądań w kodzie stanu.

### <a name="policy-statement"></a><a name="LimitConcurrencyStatement"></a> Instrukcja zasad

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

W poniższym przykładzie pokazano, jak ograniczyć liczbę żądań przesyłanych do zaplecza na podstawie wartości zmiennej kontekstowej.

```xml
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3">
      <forward-request timeout="120"/>
    </limit-concurrency>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Elementy

| Element           | Opis   | Wymagane |
| ----------------- | ------------- | -------- |
| Ograniczanie współbieżności | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut | Opis                                                                                        | Wymagane | Domyślne |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| key       | Ciąg. Wyrażenie jest dozwolone. Określa zakres współbieżności. Mogą być współużytkowane przez wiele zasad. | Tak      | Nie dotyczy     |
| Max-Count | Liczba całkowita. Określa maksymalną liczbę żądań, które mogą wejść do zasad.           | Tak      | Nie dotyczy     |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="log-to-event-hub"></a><a name="log-to-eventhub"></a> Logowanie do centrum zdarzeń

`log-to-eventhub`Zasady wysyłają komunikaty w określonym formacie do centrum zdarzeń zdefiniowanego przez jednostkę rejestratora. Zgodnie z jego nazwą, zasady są używane do zapisywania informacji o wybranym żądaniu lub kontekście odpowiedzi dla analizy w trybie online lub offline.

> [!NOTE]
> Przewodnik krok po kroku dotyczący konfigurowania zdarzeń centrum zdarzeń i rejestrowania można znaleźć w temacie [How to log API Management Events with Azure Event Hubs](./api-management-howto-log-event-hubs.md).

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Przykład

Dowolny ciąg może być używany jako wartość, która ma być zalogowana Event Hubs. W tym przykładzie Data i godzina, nazwa usługi wdrożenia, identyfikator żądania, adres IP i nazwa operacji dla wszystkich wywołań przychodzących są rejestrowane w rejestratorze centrum zdarzeń zarejestrowanym z `contoso-logger` identyfikatorem

```xml
<policies>
  <inbound>
    <log-to-eventhub logger-id ='contoso-logger'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name) )
    </log-to-eventhub>
  </inbound>
  <outbound>
  </outbound>
</policies>
```

### <a name="elements"></a>Elementy

| Element         | Opis                                                                     | Wymagane |
| --------------- | ------------------------------------------------------------------------------- | -------- |
| Logowanie do centrum eventhub | Element główny. Wartość tego elementu jest ciągiem, który ma być zalogowany do centrum zdarzeń. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut     | Opis                                                               | Wymagane                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| Identyfikator rejestratora     | Identyfikator rejestratora zarejestrowanego w usłudze API Management.         | Tak                                                                  |
| Identyfikator partycji  | Określa indeks partycji, w której będą wysyłane wiadomości.             | Opcjonalny. Tego atrybutu nie można użyć, jeśli `partition-key` jest używany. |
| klucz partycji | Określa wartość używaną do przypisywania partycji podczas wysyłania wiadomości. | Opcjonalny. Tego atrybutu nie można użyć, jeśli `partition-id` jest używany.  |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="mock-response"></a><a name="mock-response"></a> Makieta odpowiedzi

`mock-response`, Jak nazwa oznacza, jest używany do makietowania interfejsów API i operacji. Przerywa normalne wykonywanie potoku i zwraca zamakietę odpowiedzi do obiektu wywołującego. Zasady zawsze podejmują próbę zwrócenia odpowiedzi o najwyższej wierności. Preferuje przykłady zawartości odpowiedzi, jeśli są dostępne. Generuje przykładowe odpowiedzi ze schematów, gdy schematy są udostępniane i przykłady nie są. Jeśli nie zostaną znalezione żadne przykłady ani schematy, odpowiedzi bez żadnej zawartości są zwracane.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<mock-response status-code="code" content-type="media type"/>

```

### <a name="examples"></a>Przykłady

```xml
<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code. First found content type is used. If no example or schema is found, the content is empty. -->
<mock-response/>

<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code and media type. If no example or schema found, the content is empty. -->
<mock-response status-code='200' content-type='application/json'/>
```

### <a name="elements"></a>Elementy

| Element       | Opis   | Wymagane |
| ------------- | ------------- | -------- |
| makieta — odpowiedź | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut    | Opis                                                                                           | Wymagane | Domyślne |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| stan — kod  | Określa kod stanu odpowiedzi i służy do wybierania odpowiedniego przykładu lub schematu.                 | Nie       | 200     |
| Typ zawartości | Określa `Content-Type` wartość nagłówka odpowiedzi i służy do wybierania odpowiedniego przykładu lub schematu. | Nie       | Brak    |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="retry"></a><a name="Retry"></a> Spróbuj ponownie wykonać

Zasady te wykonują `retry` zasady podrzędne raz, a następnie ponawiają próbę wykonania, dopóki nie zostanie ponowiona `condition` `false` lub nie zostanie ponowiona `count` .

### <a name="policy-statement"></a>Instrukcja zasad

```xml

<retry
    condition="boolean expression or literal"
    count="number of retry attempts"
    interval="retry interval in seconds"
    max-interval="maximum retry interval in seconds"
    delta="retry interval delta in seconds"
    first-fast-retry="boolean expression or literal">
        <!-- One or more child policies. No restrictions -->
</retry>

```

### <a name="example"></a>Przykład

W poniższym przykładzie przekazanie żądania jest ponawiane do dziesięciu razy przy użyciu algorytmu ponowień wykładniczych. Ponieważ `first-fast-retry` jest ustawiona na wartość false, wszystkie próby ponowienia są objęte algorytmem ponawiania próby wykładniczej.

```xml

<retry
    condition="@(context.Response.StatusCode == 500)"
    count="10"
    interval="10"
    max-interval="100"
    delta="10"
    first-fast-retry="false">
        <forward-request buffer-request-body="true" />
</retry>

```

### <a name="elements"></a>Elementy

| Element | Opis                                                         | Wymagane |
| ------- | ------------------------------------------------------------------- | -------- |
| retry   | Element główny. Mogą zawierać inne zasady jako elementy podrzędne. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut        | Opis                                                                                                                                           | Wymagane | Domyślne |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| rozgrzewa        | Literał logiczny lub [wyrażenie](api-management-policy-expressions.md) określające, czy ponawianie próby powinno zostać zatrzymane ( `false` ) lub ciąg ( `true` ).      | Tak      | Nie dotyczy     |
| count            | Liczba dodatnia określająca maksymalną liczbę ponownych prób.                                                                                | Tak      | Nie dotyczy     |
| interval         | Dodatnia liczba sekund określająca interwał oczekiwania między ponownymi próbami.                                                                 | Tak      | Nie dotyczy     |
| Max — interwał     | Dodatnia liczba sekund określająca maksymalny interwał oczekiwania między ponownymi próbami. Służy do implementowania algorytmu ponowień wykładniczych. | Nie       | Nie dotyczy     |
| delta            | Dodatnia liczba sekund określająca przyrost interwału oczekiwania. Służy do implementowania algorytmów ponawiania liniowego i wykładniczego.             | Nie       | Nie dotyczy     |
| pierwszy — szybko ponów próbę | Jeśli jest ustawiona na `true` , pierwsza próba ponowienia zostanie wykonana natychmiast.                                                                                  | Nie       | `false` |

> [!NOTE]
> Gdy `interval` jest określona tylko wartość, zostanie wykonany ponowna próba interwału **stałego** .
> Gdy tylko `interval` i `delta` są określone, jest używany **linear** algorytm ponawiania interwału, w którym czas oczekiwania między ponownymi próbami jest obliczany zgodnie z poniższą formułą `interval + (count - 1)*delta` .
> Gdy `interval` , `max-interval` i `delta` są określone, algorytm ponawiania interwałów **wykładniczych** jest stosowany, gdzie czas oczekiwania między ponownymi próbami rośnie wykładniczo od wartości `interval` do wartości zgodnie z `max-interval` poniższą formułą `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)` .

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes) zasad. Należy zauważyć, że ograniczenia użycia zasad podrzędnych będą dziedziczone przez te zasady.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="return-response"></a><a name="ReturnResponse"></a> Odpowiedź zwrócona

`return-response`Zasady przerywają wykonywanie potoku i zwracają domyślną lub niestandardową odpowiedź do obiektu wywołującego. Odpowiedź domyślna to `200 OK` bez treści. Niestandardową odpowiedź można określić za pomocą zmiennej kontekstowej lub instrukcji zasad. Gdy są podane oba, odpowiedzi zawarte w zmiennej kontekstowej są modyfikowane przez instrukcje zasad przed zwróceniem do obiektu wywołującego.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<return-response response-variable-name="existing context variable">
  <set-header/>
  <set-body/>
  <set-status/>
</return-response>

```

### <a name="example"></a>Przykład

```xml
<return-response>
   <set-status code="401" reason="Unauthorized"/>
   <set-header name="WWW-Authenticate" exists-action="override">
      <value>Bearer error="invalid_token"</value>
   </set-header>
</return-response>

```

### <a name="elements"></a>Elementy

| Element         | Opis                                                                               | Wymagane |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| odpowiedź zwrotna | Element główny.                                                                             | Tak      |
| Set-header      | Instrukcja zasad [Set-header](api-management-transformation-policies.md#SetHTTPheader) . | Nie       |
| Ustaw treść        | Deklaracja zasad dotyczących [zestawu](api-management-transformation-policies.md#SetBody) .         | Nie       |
| Set-status      | Zestawienie zasad dotyczących [stanu](api-management-advanced-policies.md#SetStatus) .           | Nie       |

### <a name="attributes"></a>Atrybuty

| Atrybut              | Opis                                                                                                                                                                          | Wymagane  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| odpowiedź-zmienna-nazwa | Nazwa zmiennej kontekstowej, do której odwołuje się, na przykład nadrzędne zasady [wysyłania](api-management-advanced-policies.md#SendRequest) i zawierający `Response` obiekt | Opcjonalny. |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="send-one-way-request"></a><a name="SendOneWayRequest"></a> Wyślij jednokierunkowe żądanie

`send-one-way-request`Zasady wysyłają podane żądanie do określonego adresu URL bez oczekiwania na odpowiedź.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<send-one-way-request mode="new | copy">
  <url>...</url>
  <method>...</method>
  <header name="" exists-action="override | skip | append | delete">...</header>
  <body>...</body>
  <authentication-certificate thumbprint="thumbprint" />
</send-one-way-request>

```

### <a name="example"></a>Przykład

Te przykładowe zasady przedstawiają przykład użycia `send-one-way-request` zasad do wysyłania komunikatu do pokoju rozmów zapasowych, jeśli kod odpowiedzi HTTP jest większy lub równy 500. Aby uzyskać więcej informacji na temat tego przykładu, zobacz [Korzystanie z usług zewnętrznych z usługi Azure API Management](./api-management-sample-send-request.md).

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>Elementy

| Element                    | Opis                                                                                                 | Wymagane                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| Wyślij-jednokierunkowe-żądanie       | Element główny.                                                                                               | Tak                             |
| url                        | Adres URL żądania.                                                                                     | Nie, jeśli tryb = Copy; w przeciwnym razie. |
| method                     | Metoda HTTP dla żądania.                                                                            | Nie, jeśli tryb = Copy; w przeciwnym razie. |
| header                     | Nagłówek żądania. Użyj wielu elementów nagłówka dla wielu nagłówków żądań.                                  | Nie                              |
| body                       | Treść żądania.                                                                                           | Nie                              |
| Uwierzytelnianie — certyfikat | [Certyfikat do użycia na potrzeby uwierzytelniania klientów](api-management-authentication-policies.md#ClientCertificate) | Nie                              |

### <a name="attributes"></a>Atrybuty

| Atrybut     | Opis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| Mode = "String" | Określa, czy jest to nowe żądanie, czy kopię bieżącego żądania. W trybie wychodzącym tryb = Copy nie inicjuje treści żądania.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nie       | Nowy      |
| name          | Określa nazwę nagłówka, która ma zostać ustawiona.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Tak      | Nie dotyczy      |
| Istnieje — akcja | Określa akcję, która ma zostać podjęta, gdy nagłówek jest już określony. Ten atrybut musi mieć jedną z następujących wartości.<br /><br /> -override — zastępuje wartość istniejącego nagłówka.<br />-Skip — nie zastępuje istniejącej wartości nagłówka.<br />-Append-dołącza wartość do istniejącej wartości nagłówka.<br />-DELETE — usuwa nagłówek z żądania.<br /><br /> Gdy ustawione na `override` Rejestrowanie wielu wpisów o tej samej nazwie powoduje, że nagłówek jest ustawiany zgodnie ze wszystkimi wpisami (które zostaną wyświetlone wiele razy); w wyniku zostaną ustawione tylko wartości wyświetlane. | Nie       | override |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="send-request"></a><a name="SendRequest"></a> Wyślij żądanie

`send-request`Zasady wysyłają podane żądanie do określonego adresu URL, co nie przekracza wartości limitu czasu.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error
="false|true">
  <set-url>...</set-url>
  <set-method>...</set-method>
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>
  <set-body>...</set-body>
  <authentication-certificate thumbprint="thumbprint" />
</send-request>

```

### <a name="example"></a>Przykład

Ten przykład pokazuje jeden ze sposobów na zweryfikowanie tokenu odwołania z serwerem autoryzacji. Aby uzyskać więcej informacji na temat tego przykładu, zobacz [Korzystanie z usług zewnętrznych z usługi Azure API Management](./api-management-sample-send-request.md).

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
        <!-- Check active property in response -->
        <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
            <!-- Return 401 Unauthorized with http-problem payload -->
            <return-response>
                <set-status code="401" reason="Unauthorized" />
                <set-header name="WWW-Authenticate" exists-action="override">
                    <value>Bearer error="invalid_token"</value>
                </set-header>
            </return-response>
        </when>
    </choose>
  <base />
</inbound>

```

### <a name="elements"></a>Elementy

| Element                    | Opis                                                                                                 | Wymagane                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| Wyślij żądanie               | Element główny.                                                                                               | Tak                             |
| url                        | Adres URL żądania.                                                                                     | Nie, jeśli tryb = Copy; w przeciwnym razie. |
| method                     | Metoda HTTP dla żądania.                                                                            | Nie, jeśli tryb = Copy; w przeciwnym razie. |
| header                     | Nagłówek żądania. Użyj wielu elementów nagłówka dla wielu nagłówków żądań.                                  | Nie                              |
| body                       | Treść żądania.                                                                                           | Nie                              |
| Uwierzytelnianie — certyfikat | [Certyfikat do użycia na potrzeby uwierzytelniania klientów](api-management-authentication-policies.md#ClientCertificate) | Nie                              |

### <a name="attributes"></a>Atrybuty

| Atrybut                       | Opis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| Mode = "String"                   | Określa, czy jest to nowe żądanie, czy kopię bieżącego żądania. W trybie wychodzącym tryb = Copy nie inicjuje treści żądania.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nie       | Nowy      |
| odpowiedź-Zmienna-name = "String" | Nazwa zmiennej kontekstowej, która będzie odbierać obiekt odpowiedzi. Jeśli zmienna nie istnieje, zostanie utworzona po pomyślnym wykonaniu zasad i stanie się dostępna za pośrednictwem [`context.Variable`](api-management-policy-expressions.md#ContextVariables) kolekcji.                                                                                                                                                                                                                                                                                                                          | Tak      | Nie dotyczy      |
| Timeout = "Integer"               | Interwał limitu czasu (w sekundach), po którym wywołanie adresu URL nie powiedzie się.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Nie       | 60       |
| Ignoruj-błąd                    | Jeśli wartość jest równa true, a żądanie powoduje błąd:<br /><br /> -Jeśli określono odpowiedź-Variable-Name, będzie zawierać wartość null.<br />-Jeśli odpowiedź-Zmienna-name nie została określona, Context. Żądanie nie zostanie zaktualizowane.                                                                                                                                                                                                                                                                                                                                                                                   | Nie       | fałsz    |
| name                            | Określa nazwę nagłówka, która ma zostać ustawiona.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Tak      | Nie dotyczy      |
| Istnieje — akcja                   | Określa akcję, która ma zostać podjęta, gdy nagłówek jest już określony. Ten atrybut musi mieć jedną z następujących wartości.<br /><br /> -override — zastępuje wartość istniejącego nagłówka.<br />-Skip — nie zastępuje istniejącej wartości nagłówka.<br />-Append-dołącza wartość do istniejącej wartości nagłówka.<br />-DELETE — usuwa nagłówek z żądania.<br /><br /> Gdy ustawione na `override` Rejestrowanie wielu wpisów o tej samej nazwie powoduje, że nagłówek jest ustawiany zgodnie ze wszystkimi wpisami (które zostaną wyświetlone wiele razy); w wyniku zostaną ustawione tylko wartości wyświetlane. | Nie       | override |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="set-http-proxy"></a><a name="SetHttpProxy"></a> Ustaw serwer proxy HTTP

`proxy`Zasady umożliwiają kierowanie żądań przesyłanych do frontonu za pośrednictwem serwera proxy HTTP. Między bramą i serwerem proxy obsługiwane są tylko protokół HTTP (nie HTTPS). Tylko uwierzytelnianie podstawowe i NTLM.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Przykład

Zwróć uwagę na użycie [Właściwości](api-management-howto-properties.md) jako wartości nazwy użytkownika i hasła, aby uniknąć przechowywania poufnych informacji w dokumencie zasad.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Elementy

| Element | Opis  | Wymagane |
| ------- | ------------ | -------- |
| proxy   | Element główny | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut         | Opis                                            | Wymagane | Domyślne |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| URL = "String"      | Adres URL serwera proxy w postaci http://host:port .             | Tak      | Nie dotyczy     |
| username = "String" | Nazwa użytkownika, która ma być używana na potrzeby uwierzytelniania z serwerem proxy. | Nie       | Nie dotyczy     |
| Password = "String" | Hasło, które ma być używane na potrzeby uwierzytelniania z serwerem proxy. | Nie       | Nie dotyczy     |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące

-   **Zakresy zasad:** wszystkie zakresy

## <a name="set-request-method"></a><a name="SetRequestMethod"></a> Ustaw metodę żądania

`set-method`Zasady umożliwiają zmianę metody żądania HTTP dla żądania.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Przykład

Ta przykładowa zasada korzystająca z `set-method` zasad pokazuje przykład wysyłania komunikatu do pokoju rozmów zapasowych, jeśli kod odpowiedzi HTTP jest większy lub równy 500. Aby uzyskać więcej informacji na temat tego przykładu, zobacz [Korzystanie z usług zewnętrznych z usługi Azure API Management](./api-management-sample-send-request.md).

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>Elementy

| Element    | Opis                                                       | Wymagane |
| ---------- | ----------------------------------------------------------------- | -------- |
| Set-Method | Element główny. Wartość elementu określa metodę HTTP. | Tak      |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="set-status-code"></a><a name="SetStatus"></a> Ustaw kod stanu

`set-status`Zasada ustawia kod stanu HTTP na określoną wartość.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Przykład

Ten przykład pokazuje, jak zwrócić odpowiedź 401, jeśli token autoryzacji jest nieprawidłowy. Aby uzyskać więcej informacji, zobacz [Korzystanie z usług zewnętrznych z usługi Azure API Management](./api-management-sample-send-request.md)

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>

```

### <a name="elements"></a>Elementy

| Element    | Opis   | Wymagane |
| ---------- | ------------- | -------- |
| Set-status | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut       | Opis                                                | Wymagane | Domyślne |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| Code = "Integer"  | Kod stanu HTTP do zwrócenia.                            | Tak      | Nie dotyczy     |
| Przyczyna = "ciąg" | Opis przyczyny zwrócenia kodu stanu. | Tak      | Nie dotyczy     |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** wychodzące, zaplecze, w przypadku błędu
-   **Zakresy zasad:** wszystkie zakresy

## <a name="set-variable"></a><a name="set-variable"></a> Ustaw zmienną

`set-variable`Zasady deklarują zmienną [kontekstową](api-management-policy-expressions.md#ContextVariables) i przypisuje jej wartość określoną za pośrednictwem [wyrażenia](api-management-policy-expressions.md) lub literału ciągu. Jeśli wyrażenie zawiera literał, zostanie on przekonwertowany na ciąg, a typ wartości będzie `System.String` .

### <a name="policy-statement"></a><a name="set-variablePolicyStatement"></a> Instrukcja zasad

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="example"></a><a name="set-variableExample"></a> Przyklad

Poniższy przykład ilustruje Ustawianie zmiennych zasad w sekcji przychodzące. Ten zestaw zasad zmiennych tworzy `isMobile` zmienną [kontekstu](api-management-policy-expressions.md#ContextVariables) logicznego, która ma wartość true, jeśli `User-Agent` nagłówek żądania zawiera tekst `iPad` lub `iPhone` .

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elementy

| Element      | Opis   | Wymagane |
| ------------ | ------------- | -------- |
| Set-Variable | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut | Opis                                                              | Wymagane |
| --------- | ------------------------------------------------------------------------ | -------- |
| name      | Nazwa zmiennej.                                                | Tak      |
| wartość     | Wartość zmiennej. Może to być wyrażenie lub wartość literału. | Tak      |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu
-   **Zakresy zasad:** wszystkie zakresy

### <a name="allowed-types"></a><a name="set-variableAllowedTypes"></a> Dozwolone typy

Wyrażenia używane w `set-variable` zasadach muszą zwracać jeden z następujących typów podstawowych.

-   System. Boolean
-   System. nadana
-   System. Byte
-   System. UInt16
-   System. UInt32
-   System. UInt64
-   System. Int16
-   System. Int32
-   System. Int64
-   System. Decimal
-   System. Single
-   System. Double
-   System. GUID
-   System. String
-   System. Char
-   System. DateTime
-   System. TimeSpan
-   System. Byte?
-   System. UInt16?
-   System. UInt32?
-   System. UInt64?
-   System. Int16?
-   System. Int32?
-   System. Int64?
-   System. Decimal?
-   System. Single?
-   System. Double?
-   System. GUID?
-   System. String?
-   System. Char?
-   System. DateTime?

## <a name="trace"></a><a name="Trace"></a> Szuka

`trace`Zasady dodaje niestandardowy ślad do danych wyjściowych inspektora interfejsu API, Application Insights telemetrii i/lub dzienników zasobów.

-   Zasada dodaje niestandardowy ślad do danych wyjściowych [inspektora interfejsu API](./api-management-howto-api-inspector.md) , gdy śledzenie jest wyzwalane, tj. `Ocp-Apim-Trace` nagłówek żądania jest obecny i ma ustawioną wartość true, a `Ocp-Apim-Subscription-Key` nagłówek żądania jest obecny i przechowuje prawidłowy klucz umożliwiający śledzenie.
-   Zasady tworzą dane telemetryczne [śledzenia](../azure-monitor/app/data-model-trace-telemetry.md) w Application Insights, gdy [integracja Application Insights](./api-management-howto-app-insights.md) jest włączona, a `severity` poziom określony w zasadach jest równy lub większy niż `verbosity` określony w ustawieniu diagnostyki.
-   Zasada dodaje właściwość w wpisie dziennika, gdy [dzienniki zasobów](./api-management-howto-use-azure-monitor.md#activity-logs) jest włączony, a poziom ważności określony w zasadach jest równy lub większy niż poziom szczegółowości określony w ustawieniu diagnostyki.

### <a name="policy-statement"></a>Instrukcja zasad

```xml

<trace source="arbitrary string literal" severity="verbose|information|error">
    <message>String literal or expressions</message>
    <metadata name="string literal or expressions" value="string literal or expressions"/>
</trace>

```

### <a name="example"></a><a name="traceExample"></a> Przyklad

```xml
<trace source="PetStore API" severity="verbose">
    <message>@((string)context.Variables["clientConnectionID"])</message>
    <metadata name="Operation Name" value="New-Order"/>
</trace>
```

### <a name="elements"></a>Elementy

| Element  | Opis                                                                                                                                          | Wymagane |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| śledzenie    | Element główny.                                                                                                                                        | Tak      |
| message  | Ciąg lub wyrażenie, które ma zostać zarejestrowane.                                                                                                                 | Tak      |
| metadane | Dodaje właściwość niestandardową do telemetrii [śledzenia](../azure-monitor/app/data-model-trace-telemetry.md) Application Insights. | Nie       |

### <a name="attributes"></a>Atrybuty

| Atrybut | Opis                                                                                                               | Wymagane | Domyślne |
| --------- | ------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| source    | Literał ciągu istotny dla podglądu śledzenia i określający źródło wiadomości.                                   | Tak      | Nie dotyczy     |
| ważność  | Określa poziom ważności śledzenia. Dozwolone wartości to `verbose` , `information` , `error` (od najniższego do najwyższego). | Nie       | Pełny |
| name      | Nazwa właściwości.                                                                                                     | Tak      | Nie dotyczy     |
| wartość     | Wartość właściwości.                                                                                                    | Tak      | Nie dotyczy     |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes) zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="wait"></a><a name="Wait"></a> Trwa

`wait`Zasady domyślnie wykonują bezpośrednie zasady podrzędne i czekają na zakończenie wszystkich lub jednej z jej bezpośrednich zasad podrzędnych przed zakończeniem. Zasady oczekiwania mogą mieć charakter żądania natychmiastowego [wysłania żądań](api-management-advanced-policies.md#SendRequest), [pobrania wartości z pamięci podręcznej](api-management-caching-policies.md#GetFromCacheByKey)i zasad [przepływu sterowania](api-management-advanced-policies.md#choose) .

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Przykład

W poniższym przykładzie istnieją dwie `choose` zasady jako bezpośrednie zasady podrzędne `wait` zasad. Każda z tych `choose` zasad jest wykonywana równolegle. Każda `choose` zasada próbuje pobrać buforowaną wartość. W przypadku braku miejsca w pamięci podręcznej wywoływana jest usługa zaplecza, aby zapewnić wartość. W tym przykładzie `wait` zasady nie zostaną ukończone przed ukończeniem wszystkich bezpośrednich zasad podrzędnych, ponieważ `for` atrybut jest ustawiony na `all` . W tym przykładzie zmienne kontekstowe ( `execute-branch-one` , `value-one` , `execute-branch-two` i `value-two` ) są zadeklarowane poza zakresem tych przykładowych zasad.

```xml
<wait for="all">
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-one="])">
      <cache-lookup-value key="key-one" variable-name="value-one" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-one="))">
          <send-request mode="new" response-variable-name="value-one">
            <set-url>https://backend-one</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-two="])">
      <cache-lookup-value key="key-two" variable-name="value-two" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-two="))">
          <send-request mode="new" response-variable-name="value-two">
            <set-url>https://backend-two</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
</wait>

```

### <a name="elements"></a>Elementy

| Element | Opis                                                                                                   | Wymagane |
| ------- | ------------------------------------------------------------------------------------------------------------- | -------- |
| czas oczekiwania    | Element główny. Może zawierać tylko elementy podrzędne `send-request` , `cache-lookup-value` i `choose` zasady. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut | Opis                                                                                                                                                                                                                                                                                                                                                                                                            | Wymagane | Domyślne |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| dla       | Określa, czy `wait` zasady czekają na ukończenie wszystkich bezpośrednich zasad podrzędnych, czy tylko jeden. Dozwolone wartości to:<br /><br /> - `all` -Poczekaj na zakończenie wszystkich bezpośrednich zasad podrzędnych<br />-dowolny-poczekaj na zakończenie wszelkich natychmiastowych zasad podrzędnych. Po zakończeniu pierwszej bezpośredniej zasad podrzędnej `wait` zasady zakończą działanie i wykonywanie wszelkich innych bezpośrednich zasad podrzędnych zostanie zakończone. | Nie       | all     |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze
-   **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

-   [Zasady w API Management](api-management-howto-policies.md)
-   [Wyrażenia zasad](api-management-policy-expressions.md)
-   [Dokumentacja zasad](./api-management-policies.md) pełna lista instrukcji zasad i ich ustawień
-   [Przykłady zasad](./policy-reference.md)