---
title: Dodawanie łączników interfejsu API do przepływów użytkownika
description: Skonfiguruj łącznik interfejsu API, który ma być używany w przepływie użytkownika.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 09/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: 824b8f386e6bf822444450305e603e6068a34c5e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91854362"
---
# <a name="add-an-api-connector-to-a-sign-up-user-flow"></a>Dodawanie łącznika interfejsu API do przepływu użytkownika w celu rejestracji

Aby użyć [łącznika interfejsu API](api-connectors-overview.md), należy najpierw utworzyć łącznik interfejsu API, a następnie włączyć go w przepływie użytkownika.

## <a name="create-an-api-connector"></a>Tworzenie łącznika interfejsu API

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W obszarze **usługi platformy Azure**wybierz pozycję **Azure AD B2C**.
4. Wybierz pozycję **Łączniki interfejsu API (wersja zapoznawcza)**, a następnie wybierz pozycję **Nowy łącznik interfejsu API**.

   ![Dodawanie nowego łącznika interfejsu API](./media/add-api-connector/api-connector-new.png)

5. Podaj nazwę wyświetlaną dla wywołania. Na przykład **Sprawdź poprawność informacji o użytkowniku**.
6. Podaj **adres URL punktu końcowego** dla wywołania interfejsu API.
7. Podaj informacje o uwierzytelnianiu dla interfejsu API.

   - Obecnie jest obsługiwane tylko uwierzytelnianie podstawowe. Jeśli chcesz użyć interfejsu API bez uwierzytelniania podstawowego do celów deweloperskich, po prostu wprowadź **nazwę użytkownika** "fikcyjnej" i **hasło** , które może ignorować interfejs API. Do użycia z funkcją platformy Azure z kluczem interfejsu API można uwzględnić kod jako parametr zapytania w **adresie URL punktu końcowego** (na przykład https []() ://contoso.azurewebsites.NET/API/Endpoint<b>? Code = 0123456789</b>).

   ![Konfigurowanie nowego łącznika interfejsu API](./media/add-api-connector/api-connector-config.png)
8. Wybierz pozycję **Zapisz**.

## <a name="the-request-sent-to-your-api"></a>Żądanie wysłane do interfejsu API
Łącznik interfejsu API materializuje jako żądanie **http post** , wysyłając atrybuty użytkownika ("oświadczenia") jako pary klucz-wartość w treści JSON. Atrybuty są serializowane w sposób podobny do [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/user#properties) właściwości użytkownika. 

**Przykładowe żądanie**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

Tylko właściwości użytkownika i atrybuty niestandardowe wymienione w **Azure AD B2C**  >  środowisku**atrybutów użytkownika** są dostępne do wysłania w żądaniu.

Atrybuty niestandardowe istnieją w formacie **extension_ \<extensions-app-id> _CustomAttribute**  w katalogu. Interfejs API powinien oczekiwać otrzymywania oświadczeń w tym samym zserializowanym formacie. Aby uzyskać więcej informacji na temat atrybutów niestandardowych, zobacz [Definiowanie atrybutów niestandardowych w Azure Active Directory B2C](user-flow-custom-attributes.md).

Ponadto w przypadku wszystkich żądań jest domyślnie wysyłane żądanie **ustawień regionalnych interfejsu użytkownika ("ui_locales")** . Udostępnia ustawienia regionalne użytkownika skonfigurowane na urządzeniu, które mogą być używane przez interfejs API do zwracania międzynarodowych odpowiedzi.

> [!IMPORTANT]
> Jeśli w momencie wywołania punktu końcowego interfejsu API nie ma wartości, to nie zostanie ono wysłane do interfejsu API. Interfejs API powinien zostać zaprojektowany, aby jawnie sprawdzić i obsłużyć przypadek, w którym w żądaniu nie występuje żądanie.

> [!TIP] 
> [**tożsamości ("tożsamości")**](https://docs.microsoft.com/graph/api/resources/objectidentity) oraz oświadczenia **adresu e-mail ("e-mail")** mogą być używane przez interfejs API do identyfikowania użytkownika przed utworzeniem konta w dzierżawie. To zdarzenie jest wysyłane, gdy użytkownik uwierzytelnia się za pomocą dostawcy tożsamości, takiego jak Google lub Facebook. wiadomość e-mail jest zawsze wysyłana.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Włączanie łącznika interfejsu API w przepływie użytkownika

Wykonaj następujące kroki, aby dodać łącznik interfejsu API do przepływu użytkownika w celu rejestracji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W obszarze **usługi platformy Azure**wybierz pozycję **Azure AD B2C**.
4. Wybierz pozycję **przepływy użytkownika**, a następnie wybierz przepływ użytkownika, do którego chcesz dodać łącznik interfejsu API.
5. Wybierz pozycję **Łączniki interfejsu API**, a następnie wybierz punkty końcowe interfejsu API, które mają być wywoływane, w następujących krokach przepływu użytkownika:

   - **Po zalogowaniu się za pomocą dostawcy tożsamości**
   - **Przed utworzeniem użytkownika**

   ![Dodawanie interfejsów API do przepływu użytkownika](./media/add-api-connector/api-connectors-user-flow-select.png)

6. Wybierz pozycję **Zapisz**.

## <a name="after-signing-in-with-an-identity-provider"></a>Po zalogowaniu się za pomocą dostawcy tożsamości

Łącznik interfejsu API w tym kroku w procesie tworzenia konta jest wywoływany natychmiast po uwierzytelnieniu użytkownika przy użyciu dostawcy tożsamości (np. Google, Facebook, & usługi Azure AD). Ten krok poprzedza ***stronę kolekcji atrybutów***, która jest formularzem prezentowanym użytkownikowi w celu zbierania atrybutów użytkownika. Ten krok nie jest wywoływany, jeśli użytkownik jest rejestrowany przy użyciu konta lokalnego.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Przykładowe żądanie wysłane do interfejsu API w tym kroku
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

Dokładne oświadczenia wysyłane do interfejsu API są zależne od tego, które informacje są dostarczane przez dostawcę tożsamości. wiadomość e-mail jest zawsze wysyłana.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Oczekiwane typy odpowiedzi z internetowego interfejsu API w tym kroku

Gdy internetowy interfejs API odbiera żądanie HTTP z usługi Azure AD podczas przepływu użytkownika, może zwrócić następujące odpowiedzi:

- Dalsza odpowiedź
- Zablokuj odpowiedź

#### <a name="continuation-response"></a>Dalsza odpowiedź

Odpowiedź kontynuacji wskazuje, że przepływ użytkownika powinien przejść do następnego kroku: Strona kolekcji atrybutów.

W odpowiedzi na kontynuację interfejs API może zwracać oświadczenia. Jeśli w interfejsie API zostanie zwrócone zgłoszenie, jest ono następujące:

- Wstępnie wypełnia pole wejściowe na stronie kolekcji atrybutów.

Zobacz przykład [odpowiedzi kontynuacji](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Zablokuj odpowiedź

Zablokowanie odpowiedzi kończy przepływ użytkownika. Może być celowo wystawiony przez interfejs API, aby zatrzymać kontynuację przepływu użytkownika przez wyświetlenie strony blokowej dla użytkownika. Na stronie blok zostanie wyświetlona wartość `userMessage` dostarczone przez interfejs API.

Zapoznaj się z przykładem [odpowiedzi blokującej](#example-of-a-blocking-response).

## <a name="before-creating-the-user"></a>Przed utworzeniem użytkownika

Łącznik interfejsu API w tym kroku w procesie tworzenia konta jest wywoływany po stronie kolekcji atrybutów, jeśli jest dołączony. Ten krok jest zawsze wywoływany przed utworzeniem konta użytkownika.

<!-- The following are examples of scenarios you might enable at this point during sign-up: -->
<!-- 
- Validate user input data and ask a user to resubmit data.
- Block a user sign-up based on data entered by the user.
- Perform identity verification.
- Query external systems for existing data about the user and overwrite the user-provided value. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>Przykładowe żądanie wysłane do interfejsu API w tym kroku

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```
Dokładne oświadczenia wysyłane do interfejsu API są zależne od tego, które informacje są zbierane od użytkownika lub dostarczane przez dostawcę tożsamości.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Oczekiwane typy odpowiedzi z internetowego interfejsu API w tym kroku

Gdy internetowy interfejs API odbiera żądanie HTTP z usługi Azure AD podczas przepływu użytkownika, może zwrócić następujące odpowiedzi:

- Dalsza odpowiedź
- Zablokuj odpowiedź
- Odpowiedź dotycząca walidacji

#### <a name="continuation-response"></a>Dalsza odpowiedź

Odpowiedź kontynuacji wskazuje, że przepływ użytkownika powinien przejść do następnego kroku: Utwórz użytkownika w katalogu.

W odpowiedzi na kontynuację interfejs API może zwracać oświadczenia. Jeśli w interfejsie API zostanie zwrócone zgłoszenie, jest ono następujące:

- Zastępuje wszystkie wartości, które zostały już przypisane do żądania ze strony kolekcji atrybutów.

Zobacz przykład [odpowiedzi kontynuacji](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Zablokuj odpowiedź
Zablokowanie odpowiedzi kończy przepływ użytkownika. Może być celowo wystawiony przez interfejs API, aby zatrzymać kontynuację przepływu użytkownika przez wyświetlenie strony blokowej dla użytkownika. Na stronie blok zostanie wyświetlona wartość `userMessage` dostarczone przez interfejs API.

Zapoznaj się z przykładem [odpowiedzi blokującej](#example-of-a-blocking-response).

### <a name="validation-error-response"></a>Walidacja — odpowiedź na błąd
 Gdy interfejs API reaguje na odpowiedź z błędami walidacji, przepływ użytkownika pozostaje na stronie kolekcji atrybutów i `userMessage` zostanie wyświetlony użytkownikowi. Użytkownik może następnie edytować i ponownie przesłać formularz. Ten typ odpowiedzi może służyć do sprawdzania poprawności danych wejściowych.

Zapoznaj się z przykładem [odpowiedzi na błąd walidacji](#example-of-a-validation-error-response).

## <a name="example-responses"></a>Przykładowe odpowiedzi

### <a name="example-of-a-continuation-response"></a>Przykład odpowiedzi kontynuacji

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Parametr                                          | Typ              | Wymagane | Opis                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Wersja                                            | Ciąg            | Tak      | Wersja interfejsu API.                                                                                                                                                                                                                                                                |
| akcja                                             | Ciąg            | Tak      | Wartość musi być `Continue` .                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Nie       | Wartości mogą być przechowywane w katalogu, jeśli zostały wybrane jako takie, **które mają zostać odebrane** w ramach konfiguracji łącznika interfejsu API i **atrybutów użytkownika** dla przepływu użytkownika. Wartości mogą być zwracane w tokenie, jeśli są wybrane jako **wnioski aplikacji**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Nie       | Zwróconego żądania nie musi zawierać `_<extensions-app-id>_` . Wartości są przechowywane w katalogu, jeśli zostały wybrane jako jako "jako" jako "jako" jako "jako" jako "jako" jako "jako" **jako jako rolę w** **atrybucie User** Connector dla przepływu użytkownika. Nie można ponownie wysłać atrybutów niestandardowych do tokenu. |

### <a name="example-of-a-blocking-response"></a>Przykład odpowiedzi blokującej

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| Parametr   | Typ   | Wymagane | Opis                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| Wersja     | Ciąg | Tak      | Wersja interfejsu API.                                                    |
| akcja      | Ciąg | Tak      | Wartość musi być równa `ShowBlockPage`                                              |
| userMessage | Ciąg | Tak      | Komunikat wyświetlany użytkownikowi.                                            |

**Środowisko użytkownika końcowego z odpowiedzią blokującą**

![Przykładowa strona bloku](./media/add-api-connector/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Przykład weryfikacji — odpowiedź na błąd

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code."
}
```

| Parametr   | Typ    | Wymagane | Opis                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| Wersja     | Ciąg  | Tak      | Wersja interfejsu API.                                                    |
| akcja      | Ciąg  | Tak      | Wartość musi być `ValidationError` .                                           |
| status      | Liczba całkowita | Tak      | Musi być wartością `400` dla odpowiedzi ValidationError.                        |
| userMessage | Ciąg  | Tak      | Komunikat wyświetlany użytkownikowi.                                            |

**Środowisko użytkownika końcowego z odpowiedzią na błędy weryfikacji**

![Przykładowa strona walidacji](./media/add-api-connector/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>Najlepsze rozwiązania i sposoby rozwiązywania problemów

### <a name="using-serverless-cloud-functions"></a>Korzystanie z funkcji w chmurze bezserwerowej
Funkcje bezserwerowe, takie jak wyzwalacze HTTP w Azure Functions, zapewniają prostą metodę tworzenia punktów końcowych interfejsu API do użycia z łącznikiem interfejsu API. Za pomocą funkcji chmury bezserwerowej można na [przykład](code-samples.md#api-connectors)wykonać logikę walidacji i ograniczyć logowanie do określonych domen poczty e-mail. Funkcja chmury bezserwerowej może również wywołać i wywołać inne interfejsy API sieci Web, sklepy użytkowników i inne usługi w chmurze w celu uzyskania bardziej złożonych scenariuszy.

### <a name="best-practices"></a>Najlepsze rozwiązania
Upewnij się, że:
* Interfejs API jest następujący po żądaniu interfejsu API i kontraktów odpowiedzi, jak opisano powyżej. 
* **Adres URL punktu końcowego** łącznika interfejsu API wskazuje na prawidłowy punkt końcowy interfejsu API.
* Interfejs API jawnie sprawdza wartości null odebranych oświadczeń.
* Interfejs API reaguje tak szybko, jak to możliwe, aby zapewnić środowisko użytkownika systemu.
    * Jeśli używana jest funkcja bezserwerowa lub skalowalna usługa sieci Web, należy użyć planu hostingu, który utrzymuje interfejs API "w stanie" lub "grzane". w środowisku produkcyjnym. Aby uzyskać Azure Functions, zaleca się korzystanie z [planu Premium](../azure-functions/functions-scale.md)


### <a name="use-logging"></a>Korzystanie z rejestrowania
Ogólnie rzecz biorąc, warto użyć narzędzi rejestrowania włączonych przez usługę internetowego interfejsu API, takich jak [Application Insights](../azure-functions/functions-monitoring.md), aby monitorować interfejs API pod kątem nieoczekiwanych kodów błędów, wyjątków i niskiej wydajności.
* Monitoruj kody stanu HTTP, które nie są HTTP 200 lub 400.
* Kod stanu HTTP 401 lub 403 zazwyczaj wskazuje, że występuje problem z uwierzytelnianiem. Sprawdź dwukrotnie warstwę uwierzytelniania interfejsu API i odpowiednią konfigurację w łączniku interfejsu API.
* W razie konieczności używaj bardziej agresywnych poziomów rejestrowania (np. "Trace" lub "debug").
* Monitoruj interfejs API przez długi czas odpowiedzi.

## <a name="next-steps"></a>Następne kroki
<!-- - Learn how to [add a custom approval workflow to sign-up](add-approvals.md) -->
- Rozpocznij pracę z naszymi [przykładami szybkiego startu usługi Azure Functions](code-samples.md#api-connectors).
