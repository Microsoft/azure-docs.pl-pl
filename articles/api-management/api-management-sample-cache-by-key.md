---
title: Buforowanie niestandardowe w usłudze Azure API Management
description: Dowiedz się, jak buforować elementy według klucza w usłudze Azure API Management. Klucz można zmodyfikować przy użyciu nagłówków żądania.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: cf9901b4e49460dd2fb91dceaf239571058c5284
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88213319"
---
# <a name="custom-caching-in-azure-api-management"></a>Buforowanie niestandardowe w usłudze Azure API Management
Usługa Azure API Management Service ma wbudowaną obsługę [buforowania odpowiedzi HTTP](api-management-howto-cache.md) przy użyciu adresu URL zasobu jako klucza. Klucz może być modyfikowany przez nagłówki żądań przy użyciu `vary-by` właściwości. Jest to przydatne w przypadku buforowania całych odpowiedzi HTTP (znanych także jako reprezentacje), ale czasami warto tylko buforować część reprezentacji. Nowe zasady [pamięci podręcznej — wyszukiwanie](./api-management-caching-policies.md#GetFromCacheByKey) i [przechowywanie w pamięci podręcznej](./api-management-caching-policies.md#StoreToCacheByKey) zapewniają możliwość przechowywania i pobierania dowolnych danych z definicji zasad. Ta możliwość dodaje również wartość do wcześniej wprowadzonych zasad [wysyłania żądania](./api-management-advanced-policies.md#SendRequest) , ponieważ teraz można buforować odpowiedzi z usług zewnętrznych.

## <a name="architecture"></a>Architektura
Usługa API Management używa udostępnionej pamięci podręcznej danych dla dzierżawy, aby podczas skalowania do wielu jednostek nadal uzyskać dostęp do tych samych danych w pamięci podręcznej. Jednak podczas pracy z wdrożeniem wieloregionowym w każdym z regionów są niezależne pamięci podręczne. Ważne jest, aby nie traktować pamięci podręcznej jako magazynu danych, gdzie jest to jedyne źródło informacji. Jeśli wcześniej zadecydowano o skorzystaniu z wdrożenia obejmującego wiele regionów, klienci z użytkownikami, którzy podróżują, mogą utracić dostęp do tych danych w pamięci podręcznej.

## <a name="fragment-caching"></a>Buforowanie fragmentów
Istnieją pewne przypadki, w których zwrócone odpowiedzi zawierają część danych, które są kosztowne do ustalenia i jeszcze nie pozostały do użycia przez rozsądny czas. Przykładowo Rozważmy usługę utworzoną przez linie lotnicze, która zawiera informacje dotyczące rezerwacji lotów, stanu lotu itd. Jeśli użytkownik jest członkiem programu punktów linii lotniczych, będzie miał również informacje dotyczące ich bieżącego stanu i liczby skumulowanych przebiegów. Te informacje związane z użytkownikiem mogą być przechowywane w innym systemie, ale może być konieczne dołączenie go do odpowiedzi na temat stanu i rezerwacji lotu. Można to zrobić przy użyciu procesu o nazwie buforowanie fragmentów. Reprezentację podstawową można zwrócić z serwera pochodzenia przy użyciu pewnego rodzaju tokenu, aby wskazać, gdzie mają zostać wstawione informacje związane z użytkownikiem. 

Weź pod uwagę następującą odpowiedź JSON z interfejsu API zaplecza.

```json
{
  "airline" : "Air Canada",
  "flightno" : "871",
  "status" : "ontime",
  "gate" : "B40",
  "terminal" : "2A",
  "userprofile" : "$userprofile$"
}  
```

I zasób pomocniczy `/userprofile/{userid}` wygląda jak,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Aby określić odpowiednie informacje o użytkowniku do dołączenia, API Management musi identyfikować użytkownika końcowego. Ten mechanizm jest zależny od implementacji. Na przykład używam `Subject` żądania `JWT` tokenu. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

API Management przechowuje `enduserid` wartość w zmiennej kontekstowej do późniejszego użycia. Następnym krokiem jest określenie, czy poprzednie żądanie pobrało już informacje o użytkowniku i zapisane w pamięci podręcznej. W tym celu API Management używa `cache-lookup-value` zasad.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Jeśli w pamięci podręcznej nie ma wpisu odpowiadającego wartości klucza, nie `userprofile` zostanie utworzona zmienna kontekstowa. API Management sprawdza powodzenie wyszukiwania przy użyciu `choose` zasad przepływu sterowania.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Jeśli `userprofile` zmienna kontekstowa nie istnieje, API Management powinien wykonać żądanie HTTP, aby je pobrać.

```xml
<send-request
  mode="new"
  response-variable-name="userprofileresponse"
  timeout="10"
  ignore-error="true">

  <!-- Build a URL that points to the profile for the current end-user -->
  <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
      (string)context.Variables["enduserid"]).AbsoluteUri)
  </set-url>
  <set-method>GET</set-method>
</send-request>
```

API Management używa `enduserid` do konstruowania adresu URL do zasobu profilu użytkownika. Gdy API Management ma odpowiedź, pobiera tekst treści z odpowiedzi i zapisuje ją z powrotem w zmiennej kontekstowej.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Aby zapobiec ponownemu API Management żądania HTTP, gdy ten sam użytkownik wykonuje inne żądanie, możesz określić, aby profil użytkownika został zapisany w pamięci podręcznej.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

API Management przechowuje wartość w pamięci podręcznej przy użyciu dokładnie tego samego klucza, który API Management pierwotnie podjęto próbę pobrania z programu. Czas, jaki API Management wybrać do przechowywania wartości, powinien opierać się na tym, jak często zmieniają się informacje i jak odporni użytkownicy są na bieżąco z informacjami. 

Należy pamiętać, że pobieranie z pamięci podręcznej jest nadal pozaprocesowe, żądanie sieciowe i może nadal dodawać dziesiątki milisekund do żądania. Korzyści występują podczas określania, czy informacje o profilu użytkownika zajmują więcej czasu niż to, z powodu potrzeby wykonywania zapytań dotyczących bazy danych lub agregowania informacji z wielu zaplecza.

Ostatnim krokiem w procesie jest zaktualizowanie zwróconej odpowiedzi z informacjami o profilu użytkownika.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Można wybrać opcję dołączenia cudzysłowu jako części tokenu, aby nawet wtedy, gdy zastąpienie nie wystąpi, odpowiedź jest nadal prawidłowym kodem JSON.  

Po połączeniu wszystkich tych kroków, wynik końcowy to zasady, które wyglądają następująco.

```xml
<policies>
    <inbound>
        <!-- How you determine user identity is application dependent -->
        <set-variable
          name="enduserid"
          value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

        <!--Look for userprofile for this user in the cache -->
        <cache-lookup-value
          key="@("userprofile-" + context.Variables["enduserid"])"
          variable-name="userprofile" />

        <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                <!-- Make HTTP request to get user profile -->
                <send-request
                  mode="new"
                  response-variable-name="userprofileresponse"
                  timeout="10"
                  ignore-error="true">

                   <!-- Build a URL that points to the profile for the current end-user -->
                    <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>

                <!-- Store response body in context variable -->
                <set-variable
                  name="userprofile"
                  value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                <!-- Store result in cache -->
                <cache-store-value
                  key="@("userprofile-" + context.Variables["enduserid"])"
                  value="@((string)context.Variables["userprofile"])"
                  duration="100000" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <!-- Update response body with user profile-->
        <find-and-replace
              from='"$userprofile$"'
              to="@((string)context.Variables["userprofile"])" />
        <base />
    </outbound>
</policies>
```

To podejście pamięci podręcznej jest używane głównie w witrynach sieci Web, w których kod HTML składa się po stronie serwera, dzięki czemu może być renderowany jako pojedyncza strona. Może być również przydatny w interfejsach API, w których klienci nie mogą przełączać się po stronie klienta lub nie należy jej przyłożyć na klienta.

Ten sam rodzaj buforowania fragmentów można również wykonać na serwerach sieci Web zaplecza przy użyciu serwera buforowania Redis, jednak użycie usługi API Management do wykonania tej pracy jest przydatne, gdy buforowane fragmenty pochodzą z różnych zaplecza niż odpowiedzi podstawowe.

## <a name="transparent-versioning"></a>Przezroczyste przechowywanie wersji
Powszechną zaletą jest wiele różnych wersji implementacji interfejsu API, które mają być obsługiwane w dowolnym momencie. Na przykład w celu obsługi różnych środowisk (deweloperskich, testowych, produkcyjnych itp.) lub do obsługi starszych wersji interfejsu API, aby umożliwić klientom interfejsu API Migrowanie do nowszych wersji. 

Jednym z metod obsługi tego, zamiast wymagać od deweloperów programu klienckiego zmiany adresów URL z `/v1/customers` programu do programu, `/v2/customers` jest przechowywanie w danych profilu użytkownika, których wersji interfejsu API aktualnie chcą używać, i wywoływanie odpowiedniego adresu URL zaplecza. Aby określić prawidłowy adres URL zaplecza do wywołania dla określonego klienta, konieczne jest zbadanie niektórych danych konfiguracyjnych. Dzięki buforowaniu tych danych konfiguracji API Management może zminimalizować spadek wydajności operacji wyszukiwania.

Pierwszym krokiem jest określenie identyfikatora używanego do skonfigurowania odpowiedniej wersji. W tym przykładzie wybrano opcję skojarzenia wersji z kluczem subskrypcji produktu. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

Następnie API Management przeszukiwanie pamięci podręcznej, aby sprawdzić, czy pobrano już żądaną wersję klienta.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Następnie API Management sprawdza, czy nie znalazł w pamięci podręcznej.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Jeśli nie odnaleziono go API Management, API Management pobiera go.

```xml
<send-request
    mode="new"
    response-variable-name="clientconfiguresponse"
    timeout="10"
    ignore-error="true">
            <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
            <set-method>GET</set-method>
</send-request>
```

Wyodrębnij tekst treści odpowiedzi z odpowiedzi.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Zapisz ją z powrotem w pamięci podręcznej do użycia w przyszłości.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

Następnie zaktualizuj adres URL zaplecza, aby wybrać wersję usługi wymaganą przez klienta.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

Pełne zasady są następujące:

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">
            <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
            </send-request>
            <!-- Store response body in context variable -->
            <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
            <!-- Store result in cache -->
            <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
        </when>
    </choose>
    <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
</inbound>
```

Umożliwienie konsumentom interfejsu API nieprzezroczystej kontroli dostępu do wersji zaplecza przez klientów bez konieczności aktualizacji i ponownego wdrażania klientów to elegancki rozwiązanie, które rozwiązuje wiele problemów związanych z wersją interfejsu API.

## <a name="tenant-isolation"></a>Izolacja dzierżawy
W dużych wdrożeniach z wieloma dzierżawcami niektóre firmy tworzą osobne grupy dzierżawców na różnych wdrożeniach sprzętu zaplecza. Pozwala to zminimalizować liczbę klientów, którzy mają wpływ na problem ze sprzętem w zapleczu. Umożliwia również wdrażanie nowych wersji oprogramowania na etapach. Najlepiej, aby ta architektura zaplecza była niewidoczna dla użytkowników interfejsu API. Można to osiągnąć w podobny sposób do przezroczystej wersji, ponieważ jest ona oparta na tej samej metodzie manipulowania adresem URL zaplecza przy użyciu stanu konfiguracji dla klucza interfejsu API.  

Zamiast zwracać preferowaną wersję interfejsu API dla każdego klucza subskrypcji, należy zwrócić identyfikator, który odnosi dzierżawę do przypisanej grupy sprzętu. Ten identyfikator może służyć do konstruowania odpowiedniego adresu URL zaplecza.

## <a name="summary"></a>Podsumowanie
Swoboda używania pamięci podręcznej usługi Azure API Management do przechowywania dowolnego rodzaju danych umożliwia wydajne dostęp do danych konfiguracji, które mogą wpływać na sposób przetwarzania żądania przychodzącego. Można go również użyć do przechowywania fragmentów danych, które mogą rozszerzać odpowiedzi zwrócone przez interfejs API zaplecza.
