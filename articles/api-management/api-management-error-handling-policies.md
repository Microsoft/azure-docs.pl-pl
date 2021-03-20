---
title: Obsługa błędów w zasadach usługi Azure API Management | Microsoft Docs
description: Dowiedz się, jak odpowiedzieć na warunki błędów, które mogą wystąpić podczas przetwarzania żądań w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: a3b6f90d0aa26b478c0f2fcefac55dcd509da437
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92070949"
---
# <a name="error-handling-in-api-management-policies"></a>Obsługa błędów w zasadach usługi API Management

Dostarczając `ProxyError` obiekt, usługa Azure API Management umożliwia wydawcom reagowanie na warunki błędów, które mogą wystąpić podczas przetwarzania żądań. `ProxyError`Dostęp do obiektu odbywa się za pomocą [kontekstu. Właściwość LastError](api-management-policy-expressions.md#ContextVariables) i może być używana przez zasady w `on-error` sekcji Policy. Ten artykuł zawiera informacje dotyczące możliwości obsługi błędów w usłudze Azure API Management.

## <a name="error-handling-in-api-management"></a>Obsługa błędów w API Management

Zasady w usłudze Azure API Management są podzielone `inbound` na `backend` sekcje,, i, `outbound` `on-error` jak pokazano w poniższym przykładzie.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is
         forwarded to the backend service go here -->
    </backend>
    <outbound>
      <!-- statements to be applied to the response go here -->
    </outbound>
    <on-error>
        <!-- statements to be applied if there is an error
             condition go here -->
  </on-error>
</policies>
```

Podczas przetwarzania żądania wbudowane kroki są wykonywane wraz z wszelkimi zasadami, które znajdują się w zakresie żądania. Jeśli wystąpi błąd, przetwarzanie natychmiast przeskakuje do `on-error` sekcji zasad.
`on-error`Sekcji zasad można używać w dowolnym zakresie. Wydawcy interfejsu API mogą konfigurować zachowanie niestandardowe, takie jak rejestrowanie błędu w centrach zdarzeń lub tworzenie nowej odpowiedzi w celu powrotu do obiektu wywołującego.

> [!NOTE]
> `on-error`Sekcja domyślnie nie istnieje w zasadach. Aby dodać `on-error` sekcję do zasad, przejdź do odpowiedniej zasady w edytorze zasad i Dodaj ją. Aby uzyskać więcej informacji o konfigurowaniu zasad, zobacz [zasady w API Management](./api-management-howto-policies.md).
>
> Jeśli nie ma `on-error` sekcji, obiekty wywołujące otrzymają komunikaty odpowiedzi HTTP 400 lub 500 w przypadku wystąpienia błędu.

### <a name="policies-allowed-in-on-error"></a>Zasady dozwolone w przypadku błędu

W sekcji Policy można używać następujących zasad `on-error` .

-   [następnie](api-management-advanced-policies.md#choose)
-   [Set-Variable](api-management-advanced-policies.md#set-variable)
-   [Znajdź i Zamień](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [odpowiedź zwrotna](api-management-advanced-policies.md#ReturnResponse)
-   [Set-header](api-management-transformation-policies.md#SetHTTPheader)
-   [Set-Method](api-management-advanced-policies.md#SetRequestMethod)
-   [Set-status](api-management-advanced-policies.md#SetStatus)
-   [Wyślij żądanie](api-management-advanced-policies.md#SendRequest)
-   [Wyślij-jednokierunkowe-żądanie](api-management-advanced-policies.md#SendOneWayRequest)
-   [Logowanie do centrum eventhub](api-management-advanced-policies.md#log-to-eventhub)
-   [JSON-to-XML](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [XML-to-JSON](api-management-transformation-policies.md#ConvertXMLtoJSON)
-   [Ograniczanie współbieżności](api-management-advanced-policies.md#LimitConcurrency)
-   [makieta — odpowiedź](api-management-advanced-policies.md#mock-response)
-   [Spróbuj ponownie wykonać](api-management-advanced-policies.md#Retry)
-   [szuka](api-management-advanced-policies.md#Trace)

## <a name="lasterror"></a>LastError

Gdy wystąpi błąd i formant przechodzi do `on-error` sekcji zasad, ten błąd jest przechowywany w [kontekście. Właściwość LastError](api-management-policy-expressions.md#ContextVariables) , do której można uzyskać dostęp za pomocą zasad w `on-error` sekcji. LastError ma następujące właściwości.

| Nazwa       | Typ   | Opis                                                                                               | Wymagane |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | ciąg | Nazwa elementu, w którym wystąpił błąd. Może to być zasada lub wbudowana Nazwa kroku potoku.      | Tak      |
| `Reason`   | ciąg | Przyjazny dla maszyn kod błędu, który może być używany w obsłudze błędów.                                       | Nie       |
| `Message`  | ciąg | Opis błędu z możliwością odczytu przez człowieka.                                                                         | Tak      |
| `Scope`    | ciąg | Nazwa zakresu, w którym wystąpił błąd i może być jednym z "globalna", "Product", "API" lub "Operation" | Nie       |
| `Section`  | ciąg | Nazwa sekcji, w której wystąpił błąd. Możliwe wartości: "przychodzące", "zaplecze", "wychodzące" lub "On-Error".      | Nie       |
| `Path`     | ciąg | Określa zasady zagnieżdżone, na przykład "Wybierz [3]/when [2]".                                                 | Nie       |
| `PolicyId` | ciąg | Wartość `id` atrybutu (jeśli została określona przez klienta) dla zasad, w których wystąpił błąd             | Nie       |

> [!TIP]
> Dostęp do kodu stanu można uzyskać za pomocą kontekstu. Response. StatusCode.

> [!NOTE]
> Wszystkie zasady mają opcjonalny `id` atrybut, który można dodać do elementu głównego zasad. Jeśli ten atrybut jest obecny w zasadach w przypadku wystąpienia błędu, wartość atrybutu można pobrać przy użyciu `context.LastError.PolicyId` właściwości.

## <a name="predefined-errors-for-built-in-steps"></a>Wstępnie zdefiniowane błędy dla wbudowanych kroków

Następujące błędy są wstępnie zdefiniowane dla warunków błędów, które mogą wystąpić podczas oceny wbudowanych kroków przetwarzania.

| Element źródłowy        | Warunek                                 | Przyczyna                  | Komunikat                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| konfiguracja | Identyfikator URI nie jest zgodny z żadnym interfejsem API ani operacją | OperationNotFound       | Nie można dopasować przychodzącego żądania do operacji.                                                                      |
| autoryzacja | Nie podano klucza subskrypcji             | SubscriptionKeyNotFound | Odmowa dostępu z powodu braku klucza subskrypcji. Pamiętaj o dołączeniu klucza subskrypcji podczas wykonywania żądań do tego interfejsu API. |
| autoryzacja | Wartość klucza subskrypcji jest nieprawidłowa         | SubscriptionKeyInvalid  | Odmowa dostępu z powodu nieprawidłowego klucza subskrypcji. Upewnij się, że podano prawidłowy klucz dla aktywnej subskrypcji.            |
| wielokrotność | Połączenie podrzędne (od klienta do bramy API Management) zostało przerwane przez klienta podczas oczekiwania na żądanie | ClientConnectionFailure | wielokrotność |
| wielokrotność | Połączenie nadrzędne (z bramy API Management do usługi wewnętrznej bazy danych) nie zostało nawiązane lub zostało przerwane przez zaplecze | BackendConnectionFailure | wielokrotność |
| wielokrotność | Wystąpił wyjątek czasu wykonywania podczas obliczania określonego wyrażenia | ExpressionValueEvaluationFailure | wielokrotność |

## <a name="predefined-errors-for-policies"></a>Wstępnie zdefiniowane błędy zasad

Następujące błędy są wstępnie zdefiniowane dla warunków błędów, które mogą wystąpić podczas oceny zasad.

| Element źródłowy       | Warunek                                                       | Przyczyna                    | Komunikat                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| stawka — limit   | Przekroczono limit szybkości                                             | RateLimitExceeded         | Przekroczono limit szybkości                                                                                                               |
| limit przydziału        | Przekroczono limit przydziału                                                  | QuotaExceeded             | Poza limitem woluminu wywołania. Przydział zostanie uzupełniony w XX: XX: XX. -lub do limitu przydziału przepustowości. Przydział zostanie uzupełniony w XX: XX: XX. |
| JSONP        | Wartość parametru wywołania zwrotnego jest nieprawidłowa (zawiera nieprawidłowe znaki) | CallbackParameterInvalid  | Wartość parametru wywołania zwrotnego {callback-Parameter-Name} nie jest prawidłowym identyfikatorem języka JavaScript.                                          |
| filtr IP    | Nie można przeanalizować adresu IP wywołującego z żądania                          | FailedToParseCallerIP     | Nie można ustanowić adresu IP dla obiektu wywołującego. Odmowa dostępu.                                                                        |
| filtr IP    | Adres IP obiektu wywołującego nie znajduje się na liście dozwolonych                                | CallerIpNotAllowed        | Adres IP obiektu wywołującego {IP-Address} jest niedozwolony. Odmowa dostępu.                                                                        |
| filtr IP    | Adres IP obiektu wywołującego znajduje się na liście zablokowanych                                    | CallerIpBlocked           | Adres IP wywołującego jest zablokowany. Odmowa dostępu.                                                                                         |
| znacznik wyboru | Wymagany nagłówek nie został przedstawiony lub brak wartości               | HeaderNotFound            | W żądaniu nie znaleziono nagłówka {header-Name}. Odmowa dostępu.                                                                    |
| znacznik wyboru | Wymagany nagłówek nie został przedstawiony lub brak wartości               | HeaderValueNotAllowed     | Wartość nagłówka {header-name} elementu {header-Value} jest niedozwolona. Odmowa dostępu.                                                          |
| Walidacja — JWT | Brak tokenu JWT w żądaniu                                 | TokenNotFound             | Nie znaleziono tokenu JWT w żądaniu. Odmowa dostępu.                                                                                         |
| Walidacja — JWT | Weryfikacja podpisu nie powiodła się                                     | TokenSignatureInvalid     | <komunikat z biblioteki JWT \> . Odmowa dostępu.                                                                                          |
| Walidacja — JWT | Nieprawidłowi odbiorcy                                                | TokenAudienceNotAllowed   | <komunikat z biblioteki JWT \> . Odmowa dostępu.                                                                                          |
| Walidacja — JWT | Nieprawidłowy wystawca                                                  | TokenIssuerNotAllowed     | <komunikat z biblioteki JWT \> . Odmowa dostępu.                                                                                          |
| Walidacja — JWT | Token wygasł                                                   | TokenExpired              | <komunikat z biblioteki JWT \> . Odmowa dostępu.                                                                                          |
| Walidacja — JWT | Klucz podpisu nie został rozpoznany przez identyfikator                            | TokenSignatureKeyNotFound | <komunikat z biblioteki JWT \> . Odmowa dostępu.                                                                                          |
| Walidacja — JWT | Brak wymaganych oświadczeń w tokenie                          | TokenClaimNotFound        | W tokenie JWT brakuje następujących oświadczeń: <C1 \> , <C2 \> ,... Odmowa dostępu.                                                            |
| Walidacja — JWT | Niezgodność wartości zgłoszeń                                           | TokenClaimValueNotAllowed | Wartość {Claim-name} elementu {Claim-Value} jest niedozwolona. Odmowa dostępu.                                                             |
| Walidacja — JWT | Inne błędy weryfikacji                                       | JwtInvalid                | <komunikat z biblioteki JWT\>                                                                                                          |
| żądanie przesyłania dalej lub wysyłanie żądania | Kod stanu odpowiedzi HTTP i nagłówki nie zostały odebrane z zaplecza w skonfigurowanym limicie czasu | Limit czasu | wielokrotność |

## <a name="example"></a>Przykład

Ustawianie zasad interfejsu API na:

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <set-header name="ErrorSource" exists-action="override">
            <value>@(context.LastError.Source)</value>
        </set-header>
        <set-header name="ErrorReason" exists-action="override">
            <value>@(context.LastError.Reason)</value>
        </set-header>
        <set-header name="ErrorMessage" exists-action="override">
            <value>@(context.LastError.Message)</value>
        </set-header>
        <set-header name="ErrorScope" exists-action="override">
            <value>@(context.LastError.Scope)</value>
        </set-header>
        <set-header name="ErrorSection" exists-action="override">
            <value>@(context.LastError.Section)</value>
        </set-header>
        <set-header name="ErrorPath" exists-action="override">
            <value>@(context.LastError.Path)</value>
        </set-header>
        <set-header name="ErrorPolicyId" exists-action="override">
            <value>@(context.LastError.PolicyId)</value>
        </set-header>
        <set-header name="ErrorStatusCode" exists-action="override">
            <value>@(context.Response.StatusCode.ToString())</value>
        </set-header>
        <base />
    </on-error>
</policies>
```

a wysłanie nieautoryzowanego żądania spowoduje powstanie następującej odpowiedzi:

![Nieautoryzowana odpowiedź na błąd](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

-   [Zasady w API Management](api-management-howto-policies.md)
-   [Przekształć interfejsy API](transform-api.md)
-   [Dokumentacja zasad](./api-management-policies.md) pełna lista instrukcji zasad i ich ustawień
-   [Przykłady zasad](./policy-reference.md)