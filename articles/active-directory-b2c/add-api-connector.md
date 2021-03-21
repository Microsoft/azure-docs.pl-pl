---
title: Dodawanie łączników interfejsu API do przepływów użytkowników (wersja zapoznawcza)
description: Skonfiguruj łącznik interfejsu API, który ma być używany w przepływie użytkownika.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: 59246c3739ad4de27e65641cc9d2154b33a6ee5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008437"
---
# <a name="add-an-api-connector-to-a-sign-up-user-flow-preview"></a>Dodawanie łącznika interfejsu API do przepływu użytkownika rejestracji (wersja zapoznawcza)

> [!IMPORTANT]
> Łączniki interfejsu API do rejestracji jest publiczną funkcją w wersji zapoznawczej Azure AD B2C. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

Aby użyć [łącznika interfejsu API](api-connectors-overview.md), należy najpierw utworzyć łącznik interfejsu API, a następnie włączyć go w przepływie użytkownika.

## <a name="create-an-api-connector"></a>Tworzenie łącznika interfejsu API

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W obszarze **usługi platformy Azure** wybierz pozycję **Azure AD B2C**.
4. Wybierz pozycję **Łączniki interfejsu API (wersja zapoznawcza)**, a następnie wybierz pozycję **Nowy łącznik interfejsu API**.

   ![Dodawanie nowego łącznika interfejsu API](./media/add-api-connector/api-connector-new.png)

5. Podaj nazwę wyświetlaną dla wywołania. Na przykład **Sprawdź poprawność informacji o użytkowniku**.
6. Podaj **adres URL punktu końcowego** dla wywołania interfejsu API.
7. Wybierz **Typ uwierzytelniania** i skonfiguruj informacje uwierzytelniania na potrzeby wywoływania interfejsu API. Zapoznaj się z sekcją poniżej, aby poznać opcje zabezpieczania interfejsu API.

    ![Konfigurowanie łącznika interfejsu API](./media/add-api-connector/api-connector-config.png)

8. Wybierz pozycję **Zapisz**.

## <a name="securing-the-api-endpoint"></a>Zabezpieczanie punktu końcowego interfejsu API
Punkt końcowy interfejsu API można chronić za pomocą uwierzytelniania podstawowego HTTP lub uwierzytelniania certyfikatu klienta przy użyciu protokołu HTTPS (wersja zapoznawcza). W obu przypadkach podaj poświadczenia, które będą używane przez Azure AD B2C podczas wywoływania punktu końcowego interfejsu API. Punkt końcowy interfejsu API sprawdza poświadczenia i wykonuje decyzje dotyczące autoryzacji.

### <a name="http-basic-authentication"></a>Uwierzytelnianie podstawowe HTTP
Uwierzytelnianie podstawowe protokołu HTTP jest zdefiniowane w [dokumencie RFC 2617](https://tools.ietf.org/html/rfc2617). Azure AD B2C wysyła żądanie HTTP przy użyciu poświadczeń klienta ( `username` i `password` ) w `Authorization` nagłówku. Poświadczenia są sformatowane jako ciąg szyfrowany algorytmem Base64 `username:password` . Interfejs API sprawdza następnie te wartości, aby określić, czy odrzucić wywołanie interfejsu API, czy nie.

### <a name="https-client-certificate-authentication-preview"></a>Uwierzytelnianie certyfikatu klienta HTTPS (wersja zapoznawcza)

> [!IMPORTANT]
> Ta funkcja jest dostępna w wersji zapoznawczej i jest oferowana bez umowy dotyczącej poziomu usług. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Uwierzytelnianie za pomocą certyfikatu klienta jest wzajemnym uwierzytelnianiem opartym na certyfikatach, w którym klient dostarcza do serwera certyfikat klienta w celu potwierdzenia jego tożsamości. W takim przypadku Azure AD B2C będzie używać certyfikatu przekazanego w ramach konfiguracji łącznika interfejsu API. Dzieje się tak w ramach uzgadniania protokołu SSL. Tylko usługi, które mają odpowiednie certyfikaty, mogą uzyskać dostęp do usługi interfejsu API REST. Certyfikat klienta jest certyfikatem cyfrowym X. 509. W środowiskach produkcyjnych powinna być podpisana przez urząd certyfikacji. 


Aby utworzyć certyfikat, można użyć [Azure Key Vault](../key-vault/certificates/create-certificate.md), który zawiera opcje dla certyfikatów z podpisem własnym i integracji z dostawcami wystawcy certyfikatów dla podpisanych certyfikatów. Następnie można [wyeksportować certyfikat](../key-vault/certificates/how-to-export-certificate.md) i przekazać go do użytku w konfiguracji łączników interfejsu API. Należy pamiętać, że hasło jest wymagane tylko w przypadku plików certyfikatów chronionych hasłem. Możesz również użyć [polecenia cmdlet New-SelfSignedCertificate](./secure-rest-api.md#prepare-a-self-signed-certificate-optional) programu PowerShell, aby wygenerować certyfikat z podpisem własnym.

Aby uzyskać Azure App Service i Azure Functions, zobacz [Konfigurowanie wzajemnego uwierzytelniania TLS](../app-service/app-service-web-configure-tls-mutual-auth.md) , aby dowiedzieć się, jak włączyć i zweryfikować certyfikat z punktu końcowego interfejsu API.

Zalecane jest ustawienie alertów przypomnień dla momentu wygaśnięcia certyfikatu. Aby przekazać nowy certyfikat do istniejącego łącznika interfejsu API, wybierz łącznik interfejsu API w obszarze **Łączniki interfejsu API (wersja zapoznawcza)** i kliknij przycisk **Przekaż nowy certyfikat**. Ostatnio przekazany certyfikat, który nie wygasł i jest późniejsza niż data rozpoczęcia, zostanie użyty automatycznie przez Azure AD B2C.

### <a name="api-key"></a>Klucz interfejsu API
Niektóre usługi używają mechanizmu "klucz interfejsu API", aby zasłaniać dostęp do punktów końcowych HTTP podczas opracowywania. Aby uzyskać [Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys), można to zrobić, dołączając `code` jako parametr zapytania w **adresie URL punktu końcowego**. Na przykład, `https://contoso.azurewebsites.net/api/endpoint` <b>`?code=0123456789`</b> ). 

Nie jest to mechanizm, który powinien być używany tylko w środowisku produkcyjnym. W związku z tym konfiguracja uwierzytelniania podstawowego lub certyfikatu jest zawsze wymagana. Jeśli nie chcesz zaimplementować żadnej metody uwierzytelniania (niezalecanej) do celów programistycznych, możesz wybrać uwierzytelnianie podstawowe i użyć wartości tymczasowych dla programu `username` oraz `password` , że interfejs API może być pomijany podczas implementowania autoryzacji w interfejsie API.

## <a name="the-request-sent-to-your-api"></a>Żądanie wysłane do interfejsu API
Łącznik interfejsu API materializuje jako żądanie **http post** , wysyłając atrybuty użytkownika ("oświadczenia") jako pary klucz-wartość w treści JSON. Atrybuty są serializowane w sposób podobny do [Microsoft Graph](/graph/api/resources/user#properties) właściwości użytkownika. 

**Przykładowe żądanie**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
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

Tylko właściwości użytkownika i atrybuty niestandardowe wymienione w **Azure AD B2C**  >  środowisku **atrybutów użytkownika** są dostępne do wysłania w żądaniu.

Atrybuty niestandardowe istnieją w formacie **extension_ \<extensions-app-id> _CustomAttribute**  w katalogu. Interfejs API powinien oczekiwać otrzymywania oświadczeń w tym samym zserializowanym formacie. Aby uzyskać więcej informacji na temat atrybutów niestandardowych, zobacz [Definiowanie atrybutów niestandardowych w Azure AD B2C](user-flow-custom-attributes.md).

Ponadto w przypadku wszystkich żądań jest domyślnie wysyłane żądanie **ustawień regionalnych interfejsu użytkownika ("ui_locales")** . Udostępnia ustawienia regionalne użytkownika skonfigurowane na urządzeniu, które mogą być używane przez interfejs API do zwracania międzynarodowych odpowiedzi.

> [!IMPORTANT]
> Jeśli w momencie wywołania punktu końcowego interfejsu API nie ma wartości, to nie zostanie ono wysłane do interfejsu API. Interfejs API powinien zostać zaprojektowany, aby jawnie sprawdzić i obsłużyć przypadek, w którym w żądaniu nie występuje żądanie.

> [!TIP] 
> [**tożsamości ("tożsamości")**](/graph/api/resources/objectidentity) oraz oświadczenia **adresu e-mail ("e-mail")** mogą być używane przez interfejs API do identyfikowania użytkownika przed utworzeniem konta w dzierżawie. 

## <a name="enable-the-api-connector-in-a-user-flow"></a>Włączanie łącznika interfejsu API w przepływie użytkownika

Wykonaj następujące kroki, aby dodać łącznik interfejsu API do przepływu użytkownika w celu rejestracji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W obszarze **usługi platformy Azure** wybierz pozycję **Azure AD B2C**.
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
 "identities": [ 
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

### <a name="example-request-sent-to-the-api-at-this-step"></a>Przykładowe żądanie wysłane do interfejsu API w tym kroku

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
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
| akcja                                             | Ciąg            | Tak      | Wartość musi być `Continue` .                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Nie       | Zwracane wartości mogą zastąpić wartości zebrane przez użytkownika. Mogą być również zwracane w tokenie, jeśli wybrano jako rolę **wniosku**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Nie       | Nie musi zawierać tego żądania `_<extensions-app-id>_` . Zwracane wartości mogą zastąpić wartości zebrane przez użytkownika. Mogą być również zwracane w tokenie, jeśli wybrano jako rolę **wniosku**.  |

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

> [!NOTE]
> Kod stanu HTTP musi mieć wartość "400" Oprócz wartości "status" w treści odpowiedzi.

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
- Rozpocznij pracę z naszymi [przykładami](code-samples.md#api-connectors).
