---
title: Zasady integracji usługi Azure API Management Dapr | Microsoft Docs
description: Dowiedz się więcej o zasadach API Management platformy Azure na potrzeby współpracy z rozszerzeniami mikrousług Dapr.
author: vladvino
ms.author: vlvinogr
ms.date: 02/18/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 051bf4398555f318f613c66d58ec65be1d30e215
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646813"
---
# <a name="api-management-dapr-integration-policies"></a>Zasady integracji API Management Dapr

Ten temat zawiera informacje dotyczące Dapr Integration API Management zasad. Dapr to przenośne środowisko uruchomieniowe służące do tworzenia bezstanowych i bezstanowych aplikacji opartych na mikrousługach przy użyciu dowolnego języka lub struktury. Ta metoda skodyfikowa typowe wzorce mikrousług, takie jak odnajdowanie usług i wywoływanie przy użyciu logiki ponawiania prób, publikowania i subskrybowania z użyciem co najmniej jednokrotnej semantyki dostarczania lub podłączanych zasobów powiązań w celu ułatwienia kompozycji przy użyciu usług zewnętrznych. Przejdź do [dapr.IO](https://dapr.io) , aby uzyskać szczegółowe informacje i instrukcje dotyczące rozpoczynania pracy z usługą dapr. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasady w API Management](api-management-howto-policies.md).

> [!CAUTION]
> Zasady, do których odwołuje się ten temat, znajdują się w publicznej wersji zapoznawczej i podlegają [dodatkowym warunkom użytkowania dla Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)wersji zapoznawczych.

> [!IMPORTANT]
> Zasady, do których odwołuje się ten temat, działają tylko w [autonomicznej wersji bramy API Management](self-hosted-gateway-overview.md) z włączoną obsługą Dapr.

## <a name="enable-dapr-support-in-the-self-hosted-gateway"></a>Włącz obsługę Dapr w samoobsługowej bramie

Aby włączyć obsługę Dapr w ramach bramy samoobsługowej, Dodaj [Adnotacje Dapr](https://github.com/dapr/docs/blob/master/README.md) poniżej do [szablonu wdrożenia Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md) , zastępując ciąg "App-Name" pożądaną nazwą. Pełny przewodnik konfigurowania i używania API Management z programem Dapr jest dostępny [tutaj](https://aka.ms/apim/dapr/walkthru).
```yml
template:
    metadata:
      labels:
        app: app-name
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "app-name"
```


## <a name="distributed-application-runtime-dapr-integration-policies"></a>Zasady integracji środowiska Dapr (Distributed Application Runtime)

-  [Wyślij żądanie do usługi](api-management-dapr-policies.md#invoke): używa środowiska uruchomieniowego Dapr do lokalizowania i niezawodnego komunikowania się z mikrousługą Dapr. Aby dowiedzieć się więcej na temat wywołania usługi w programie Dapr, zobacz opis w tym pliku [README](https://github.com/dapr/docs/blob/master/README.md#service-invocation) .
-  [Wyślij komunikat do publikowania/podtematu](api-management-dapr-policies.md#pubsub): używa środowiska uruchomieniowego Dapr, aby opublikować komunikat w temacie publikowanie/subskrybowanie. Aby dowiedzieć się więcej na temat publikowania/subskrybowania wiadomości w programie Dapr, zobacz opis w tym pliku [README](https://github.com/dapr/docs/blob/master/README.md) .
-  [Wyzwalanie powiązania danych wyjściowych](api-management-dapr-policies.md#bind): używa środowiska uruchomieniowego Dapr do wywoływania systemu zewnętrznego za pośrednictwem powiązania danych wyjściowych. Aby dowiedzieć się więcej na temat powiązań w programie Dapr, zobacz opis w tym pliku [README](https://github.com/dapr/docs/blob/master/README.md) .

## <a name="send-request-to-a-service"></a><a name="invoke"></a> Wyślij żądanie do usługi

Te zasady określają docelowy adres URL dla bieżącego żądania `http://localhost:3500/v1.0/invoke/{app-id}[.{ns-name}]/method/{method-name}` zastąpienia parametrów szablonu wartościami określonymi w instrukcji Policy.

Zasady założono, że Dapr działa w kontenerze przyczepki w tym samym obszarze, w którym znajduje się brama. Po odebraniu żądania środowisko uruchomieniowe Dapr wykonuje odnajdowanie usług i rzeczywiste wywołanie, w tym możliwe tłumaczenie protokołów między HTTP i gRPC, ponowne próby, śledzenie rozproszone i obsługa błędów.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<set-backend-service backend-id="dapr" dapr-app-id="app-id" dapr-method="method-name" dapr-namespace="ns-name" />
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

Poniższy przykład ilustruje wywoływanie metody o nazwie "Back" w mikrousłudze o nazwie "Echo". `set-backend-service`Zasady ustawiają docelowy adres URL na `http://localhost:3500/v1.0/invoke/echo.echo-app/method/back` . `forward-request`Zasady wysyłają żądanie do środowiska uruchomieniowego Dapr, który dostarcza go do mikrousługi.

`forward-request`Zasady są wyświetlane w tym miejscu do przejrzystości. Zasady są zwykle "dziedziczone" z zakresu globalnego za pomocą `base` słowa kluczowego.

```xml
<policies>
    <inbound>
        <base />
        <set-backend-service backend-id="dapr" dapr-app-id="echo" dapr-method="back" dapr-namespace="echo-app" />
    </inbound>
    <backend>
        <forward-request />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementy

| Element             | Opis  | Wymagane |
|---------------------|--------------|----------|
| Konfiguracja zaplecza — usługa | Element główny | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut        | Opis                     | Wymagane | Domyślne |
|------------------|---------------------------------|----------|---------|
| Identyfikator zaplecza       | Musi być ustawiona na wartość "dapr"           | Tak      | Nie dotyczy     |
| dapr-App-ID      | Nazwa docelowej mikrousługi. Służy do tworzenia parametru [AppID](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) w Dapr.| Tak | Nie dotyczy |
| dapr — Metoda      | Nazwa metody lub adresu URL, który ma zostać wywołany w przypadku mikrousługi docelowej. Mapuje do parametru [Method-Name](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) w Dapr.| Tak | Nie dotyczy |
| dapr — przestrzeń nazw   | Nazwa przestrzeni nazw, w której znajduje się docelowa mikrousługa. Służy do tworzenia parametru [AppID](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) w Dapr.| Nie | Nie dotyczy |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

- **Sekcje zasad:** przychodzące
- **Zakresy zasad:** wszystkie zakresy

## <a name="send-message-to-pubsub-topic"></a><a name="pubsub"></a> Wyślij komunikat do publikowania/tematu podrzędnego

Te zasady instruują API Management Gateway, aby wysyłali komunikat do tematu Dapr Publish/subskrybowania. Zasady te osiągają, dzięki czemu żądanie HTTP POST `http://localhost:3500/v1.0/publish/{{pubsub-name}}/{{topic}}` zastępujące parametry szablonu i dodanie zawartości określonej w instrukcji zasad.

Zasady założono, że środowisko uruchomieniowe Dapr jest uruchomione w kontenerze przyczepki w tym samym obszarze co brama. Środowisko uruchomieniowe Dapr implementuje semantykę pub/sub.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<publish-to-dapr pubsub-name="pubsub-name" topic="topic-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <!-- message content -->
</publish-to-dapr>
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

Poniższy przykład ilustruje wysyłanie treści bieżącego żądania do [tematu](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters) "nowy" składnika publikowania/ [podskładnika](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters)"Orders". Odpowiedź odebrana z środowiska uruchomieniowego Dapr jest przechowywana w wpisie "Dapr-Response" kolekcji zmiennych w obiekcie [Context](api-management-policy-expressions.md#ContextVariables) .

Jeśli środowisko uruchomieniowe Dapr nie może zlokalizować tematu docelowego, na przykład i reaguje na błąd, sekcja "On-Error" jest wyzwalana. Odpowiedź odebrana z środowiska uruchomieniowego Dapr jest zwracana do obiektu wywołującego Verbatim. W przeciwnym razie `200 OK` zwracana jest odpowiedź domyślna.

Sekcja "zaplecze" jest pusta, a żądanie nie jest przekazywane do zaplecza.

```xml
<policies>
     <inbound>
        <base />
        <publish-to-dapr
           pubsub-name="orders"
               topic="new"
               response-variable-name="dapr-response">
            @(context.Request.Body.As<string>())
        </publish-to-dapr>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="pubsub-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementy

| Element             | Opis  | Wymagane |
|---------------------|--------------|----------|
| Publikuj do dapr     | Element główny | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut        | Opis                     | Wymagane | Domyślne |
|------------------|---------------------------------|----------|---------|
| pubsub — nazwa      | Nazwa docelowego składnika PubSub. Mapuje do parametru [pubsubname](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) w Dapr. Jeśli nie istnieje, wartość atrybutu __tematu__ musi mieć postać `pubsub-name/topic-name` .    | Nie       | Brak    |
| temat            | Nazwa tematu. Mapuje do parametru [tematu](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) w Dapr.               | Tak      | Nie dotyczy     |
| Ignoruj-błąd     | Jeśli ustawiono `true` , aby nie wyzwalał instrukcji ["On-Error"](api-management-error-handling-policies.md) podczas uzyskiwania błędu z środowiska uruchomieniowego Dapr | Nie | `false` |
| odpowiedź-zmienna-nazwa | Nazwa wpisu kolekcji [zmiennych](api-management-policy-expressions.md#ContextVariables) , który ma być używany do przechowywania odpowiedzi z środowiska uruchomieniowego Dapr | Nie | Brak |
| timeout | Czas (w sekundach) oczekiwania na odpowiedź środowiska uruchomieniowego Dapr. Może mieć zakres od 1 do 240 sekund. | Nie | 5 |
| szablon | Aparat tworzenia szablonów używany do przekształcania zawartości komunikatu. Jedyną obsługiwaną wartością jest "Liquid". | Nie | Brak |
| Typ zawartości | Typ zawartości komunikatu. jedyną obsługiwaną wartością jest "Application/JSON". | Nie | Brak |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

- **Sekcje zasad:** przychodzące, wychodzące, w przypadku błędu
- **Zakresy zasad:** wszystkie zakresy

## <a name="trigger-output-binding"></a><a name="bind"></a> Wyzwalanie powiązania danych wyjściowych

Te zasady instruują API Management Gateway, aby wyzwolić wychodzące [powiązanie](https://github.com/dapr/docs/blob/master/README.md)Dapr. Zasady te osiągają, dzięki czemu żądanie HTTP POST `http://localhost:3500/v1.0/bindings/{{bind-name}}` zastępujące parametr szablonu i dodanie zawartości określonej w instrukcji zasad.

Zasady założono, że środowisko uruchomieniowe Dapr jest uruchomione w kontenerze przyczepki w tym samym obszarze co brama. Środowisko uruchomieniowe Dapr jest odpowiedzialne za wywoływanie zasobu zewnętrznego reprezentowanego przez powiązanie.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<invoke-dapr-binding name="bind-name" operation="op-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <metadata>
        <item key="item-name"><!-- item-value --></item>
    </metadata>
    <data>
        <!-- message content -->
    </data>
</invoke-dapr-binding>
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

Poniższy przykład pokazuje wyzwalanie powiązania wychodzącego o nazwie "zewnętrzne systemy" z nazwą operacji "Create", metadanymi zawierającym dwa elementy klucz/wartość o nazwie "source" i "Client-IP" oraz treść pochodzącą z oryginalnego żądania. Odpowiedź odebrana z środowiska uruchomieniowego Dapr jest przechwytywana w wpisie "bind-Response" kolekcji zmiennych w obiekcie [Context](api-management-policy-expressions.md#ContextVariables) .

Jeśli środowisko uruchomieniowe Dapr nie powiedzie się z jakiegoś powodu i odpowie na błąd, sekcja "On-Error" jest wyzwalana, a odpowiedź odebrana przez środowisko uruchomieniowe Dapr jest zwracana do Verbatim obiektu wywołującego. W przeciwnym razie `200 OK` zwracana jest odpowiedź domyślna.

Sekcja "zaplecze" jest pusta, a żądanie nie jest przekazywane do zaplecza.

```xml
<policies>
     <inbound>
        <base />
        <invoke-dapr-binding
                      name="external-system"
                      operation="create"
                      response-variable-name="bind-response">
            <metadata>
                <item key="source">api-management</item>
                <item key="client-ip">@( context.Request.IpAddress )</item>
            </metadata>
            <data>
                @( context.Request.Body.As<string>() )
            </data>
        </invoke-dapr-binding>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="bind-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementy

| Element             | Opis  | Wymagane |
|---------------------|--------------|----------|
| Invoke-dapr-Binding | Element główny | Tak      |
| metadane            | Powiązanie określonych metadanych w formie par klucz/wartość. Mapuje do właściwości [Metadata](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) w Dapr. | Nie |
| dane            | Zawartość wiadomości. Mapuje do właściwości [Data](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) w Dapr. | Nie |


### <a name="attributes"></a>Atrybuty

| Atrybut        | Opis                     | Wymagane | Domyślne |
|------------------|---------------------------------|----------|---------|
| name            | Nazwa powiązania docelowego. Musi być zgodna z nazwą powiązań [zdefiniowaną](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#bindings-structure) w Dapr.           | Tak      | Nie dotyczy     |
| operation       | Nazwa operacji docelowej (specyficzne dla powiązania). Mapuje do właściwości [operacji](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) w Dapr. | Nie | Brak |
| Ignoruj-błąd     | Jeśli ustawiono `true` , aby nie wyzwalał instrukcji ["On-Error"](api-management-error-handling-policies.md) podczas uzyskiwania błędu z środowiska uruchomieniowego Dapr | Nie | `false` |
| odpowiedź-zmienna-nazwa | Nazwa wpisu kolekcji [zmiennych](api-management-policy-expressions.md#ContextVariables) , który ma być używany do przechowywania odpowiedzi z środowiska uruchomieniowego Dapr | Nie | Brak |
| timeout | Czas (w sekundach) oczekiwania na odpowiedź środowiska uruchomieniowego Dapr. Może mieć zakres od 1 do 240 sekund. | Nie | 5 |
| szablon | Aparat tworzenia szablonów używany do przekształcania zawartości komunikatu. Jedyną obsługiwaną wartością jest "Liquid". | Nie | Brak |
| Typ zawartości | Typ zawartości komunikatu. jedyną obsługiwaną wartością jest "Application/JSON". | Nie | Brak |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

- **Sekcje zasad:** przychodzące, wychodzące, w przypadku błędu
- **Zakresy zasad:** wszystkie zakresy
