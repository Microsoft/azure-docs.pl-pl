---
title: Dodawanie łączników interfejsu API do samoobsługowego przepływu rejestracji — Azure AD
description: Skonfiguruj interfejs API sieci Web, który ma być używany w przepływie użytkownika.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88270d51bf50b2b175d9d8761685a8a2a8ae19b1
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87428273"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Dodawanie łącznika interfejsu API do przepływu użytkownika

Aby użyć [łącznika interfejsu API](api-connectors-overview.md), należy najpierw utworzyć łącznik interfejsu API, a następnie włączyć go w przepływie użytkownika.

## <a name="create-an-api-connector"></a>Tworzenie łącznika interfejsu API

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako administrator usługi Azure AD.
2. W obszarze **usługi platformy Azure**wybierz pozycję **Azure Active Directory**.
3. W menu po lewej stronie wybierz pozycję **tożsamości zewnętrzne**.
4. Wybierz pozycję **Wszystkie łączniki interfejsu API (wersja zapoznawcza)**, a następnie wybierz pozycję **Nowy łącznik interfejsu API**.

   ![Dodawanie nowego łącznika interfejsu API](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. Podaj nazwę wyświetlaną dla wywołania. Na przykład **Sprawdź stan zatwierdzenia**.
6. Podaj **adres URL punktu końcowego** dla wywołania interfejsu API.
7. Podaj informacje o uwierzytelnianiu dla interfejsu API.

   - Obecnie jest obsługiwane tylko uwierzytelnianie podstawowe. Jeśli chcesz użyć interfejsu API bez uwierzytelniania podstawowego do celów deweloperskich, po prostu wprowadź fikcyjną **nazwę użytkownika** i **hasło** , które mogą być ignorowane przez interfejs API. Do użycia z funkcją platformy Azure z kluczem interfejsu API można uwzględnić kod jako parametr zapytania w **adresie URL punktu końcowego** (na przykład https []() ://contoso.azurewebsites.NET/API/Endpoint<b>? Code = 0123456789</b>).

   ![Dodawanie nowego łącznika interfejsu API](./media/self-service-sign-up-add-api-connector/api-connector-config.png)

8. Wybierz oświadczenia, które chcesz wysłać do interfejsu API.
9. Wybierz wszelkie oświadczenia, które planujesz odebrać z interfejsu API.

   <!-- ![Set API connector claims](./media/self-service-sign-up-add-api-connector/api-connector-claims.png) -->

10. Wybierz pozycję **Zapisz**.

### <a name="selection-of-claims-to-send-and-claims-to-receive"></a>Wybór "oświadczeń do wysłania" i "oświadczeń do odebrania"
> [!IMPORTANT]
> Można zobaczyć wszystkie wybrane oświadczenia domyślnie, jak pokazano poniżej. Wszystkie łączniki interfejsu API zostaną zaktualizowane w taki sposób. Interfejs API otrzyma wszystkie dostępne oświadczenia i będzie mógł wysłać wszelkie obsługiwane oświadczenia bez konfigurowania ich w definicji łącznika interfejsu API. 

![Ustaw oświadczenia łącznika interfejsu API](./media/self-service-sign-up-add-api-connector/api-connector-claims-new.png)


## <a name="enable-the-api-connector-in-a-user-flow"></a>Włączanie łącznika interfejsu API w przepływie użytkownika

Wykonaj następujące kroki, aby dodać łącznik interfejsu API do samoobsługowego przepływu użytkowników rejestracji.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako administrator usługi Azure AD.
2. W obszarze **usługi platformy Azure**wybierz pozycję **Azure Active Directory**.
3. W menu po lewej stronie wybierz pozycję **tożsamości zewnętrzne**.
4. Wybierz pozycję **przepływy użytkownika (wersja zapoznawcza)**, a następnie wybierz przepływ użytkownika, do którego chcesz dodać łącznik interfejsu API.
5. Wybierz pozycję **Łączniki interfejsu API**, a następnie wybierz punkty końcowe interfejsu API, które mają być wywoływane, w następujących krokach przepływu użytkownika:

   - **Po zalogowaniu się za pomocą dostawcy tożsamości**
   - **Przed utworzeniem użytkownika**

   ![Dodawanie interfejsów API do przepływu użytkownika](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. Wybierz pozycję **Zapisz**.

Dowiedz się [, gdzie można włączyć łącznik interfejsu API w przepływie użytkownika](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow).

## <a name="request-sent-to-the-api"></a>Żądanie wysłane do interfejsu API

Łącznik interfejsu API materializuje jako żądanie HTTP POST, wysyłając wybrane oświadczenia jako pary klucz-wartość w treści JSON. Odpowiedź powinna również mieć nagłówek HTTP `Content-Type: application/json` . Atrybuty są serializowane w sposób analogiczny do atrybutów użytkownika Microsoft Graph. <!--# TODO: Add link to MS Graph or create separate reference.-->

### <a name="example-request"></a>Przykładowe żądanie

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
 "postalCode": "33971",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

**Lokalne żądanie interfejsu użytkownika ("ui_locales")** jest domyślnie wysyłane we wszystkich żądaniach. Zapewnia on ustawienia regionalne użytkownika i może być używany przez interfejs API do zwracania odpowiedzi międzynarodowych. Nie jest on wyświetlany w okienku konfiguracji interfejsu API.

Jeśli nie ma wartości w momencie wywołania punktu końcowego interfejsu API, nie zostanie ono wysłane do interfejsu API.

Atrybuty niestandardowe można utworzyć dla użytkownika przy użyciu formatu ** \<extensions-app-id> _AttributeName extension_** . Interfejs API powinien oczekiwać otrzymywania oświadczeń w tym samym zserializowanym formacie. Interfejs API może zwracać oświadczenia z lub bez `<extensions-app-id>` . Aby uzyskać więcej informacji o atrybutach niestandardowych, zobacz [Definiowanie atrybutów niestandardowych dla przepływów rejestracji samoobsługowej](user-flow-add-custom-attributes.md).

> [!TIP] 
> [**tożsamości ("tożsamości")**](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0) oraz oświadczenia **adresu e-mail ("e-mail")** mogą służyć do identyfikowania użytkownika przed utworzeniem konta w dzierżawie. W przypadku, gdy użytkownik uwierzytelnia się za pomocą usługi Google lub Facebook, a adres e-mail jest zawsze wysyłany, jest wysyłane zgłoszenie tożsamości.

## <a name="expected-response-types-from-the-web-api"></a>Oczekiwane typy odpowiedzi z internetowego interfejsu API

Gdy internetowy interfejs API odbiera żądanie HTTP z usługi Azure AD podczas przepływu użytkownika, może zwrócić następujące odpowiedzi:

- [Dalsza odpowiedź](#continuation-response)
- [Zablokuj odpowiedź](#blocking-response)
- [Walidacja — odpowiedź na błąd](#validation-error-response)

### <a name="continuation-response"></a>Dalsza odpowiedź

Odpowiedź kontynuacji wskazuje, że przepływ użytkownika powinien przejść do następnego kroku. W odpowiedzi na kontynuację interfejs API może zwracać oświadczenia.

W przypadku zwrócenia przez interfejs API żądania i wybrania jako **żądania odebrania**, w ramach tego żądania następuje:

- Wstępnie wypełnia pola wejściowe na stronie kolekcji atrybutów, jeśli łącznik interfejsu API jest wywoływany przed wyświetleniem strony.
- Zastępuje wszystkie wartości, które zostały już przypisane do żądania.
- Przypisuje wartość do żądania, jeśli wcześniej była równa null.

#### <a name="example-of-a-continuation-response"></a>Przykład odpowiedzi kontynuacji

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
| version                                            | String            | Tak      | Wersja interfejsu API.                                                                                                                                                                                                                                                                |
| akcja                                             | String            | Tak      | Wartość musi być `Continue` .                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Nie       | Wartości mogą być przechowywane w katalogu, jeśli zostały wybrane jako takie, **które mają zostać odebrane** w ramach konfiguracji łącznika interfejsu API i **atrybutów użytkownika** dla przepływu użytkownika. Wartości mogą być zwracane w tokenie, jeśli są wybrane jako **wnioski aplikacji**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Nie       | Zwróconego żądania nie musi zawierać `_<extensions-app-id>_` . Wartości są przechowywane w katalogu, jeśli zostały wybrane jako jako "jako" jako "jako" jako "jako" jako "jako" jako "jako" **jako jako rolę w** **atrybucie User** Connector dla przepływu użytkownika. Nie można ponownie wysłać atrybutów niestandardowych do tokenu. |

### <a name="blocking-response"></a>Zablokuj odpowiedź

Zablokowanie odpowiedzi kończy przepływ użytkownika. Może być celowo wystawiony przez interfejs API, aby zatrzymać kontynuację przepływu użytkownika przez wyświetlenie strony blokowej dla użytkownika. Na stronie blok zostanie wyświetlona wartość `userMessage` dostarczone przez interfejs API.

Przykład odpowiedzi blokującej:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| Parametr   | Typ   | Wymagane | Opis                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | String | Tak      | Wersja interfejsu API.                                                    |
| akcja      | String | Tak      | Wartość musi być równa`ShowBlockPage`                                              |
| userMessage | String | Tak      | Komunikat wyświetlany użytkownikowi.                                            |
| kod        | String | Nie       | Kod błędu. Może służyć do celów debugowania. Niewidoczne dla użytkownika. |

#### <a name="end-user-experience-with-a-blocking-response"></a>Środowisko użytkownika końcowego z odpowiedzią blokującą

![Przykładowa strona bloku](./media/api-connectors-overview/blocking-page-response.png)

### <a name="validation-error-response"></a>Walidacja — odpowiedź na błąd

Wywołanie interfejsu API wywoływane po stronie kolekcji atrybutów może zwracać błąd walidacji. W takim przypadku przepływ użytkownika pozostaje na stronie kolekcji atrybutów i `userMessage` jest wyświetlany użytkownikowi. Użytkownik może następnie edytować i ponownie przesłać formularz. Ten typ odpowiedzi może służyć do sprawdzania poprawności danych wejściowych.

#### <a name="example-of-a-validation-error-response"></a>Przykład weryfikacji — odpowiedź na błąd

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| Parametr   | Typ    | Wymagane | Opis                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | String  | Tak      | Wersja interfejsu API.                                                    |
| akcja      | String  | Tak      | Wartość musi być `ValidationError` .                                           |
| status      | Integer | Tak      | Musi być wartością `400` dla odpowiedzi ValidationError.                        |
| userMessage | String  | Tak      | Komunikat wyświetlany użytkownikowi.                                            |
| kod        | String  | Nie       | Kod błędu. Może służyć do celów debugowania. Niewidoczne dla użytkownika. |

#### <a name="end-user-experience-with-a-validation-error-response"></a>Środowisko użytkownika końcowego z odpowiedzią na błędy weryfikacji

![Przykładowa strona walidacji](./media/api-connectors-overview/validation-error-postal-code.png)

### <a name="integration-with-azure-functions"></a>Integracja z usługą Azure Functions
Wyzwalacza protokołu HTTP można używać w Azure Functions jako prosty sposób tworzenia interfejsu API do użycia z łącznikiem interfejsu API. Funkcja platformy Azure służy do, [na przykład](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts), wykonywania logiki walidacji i ograniczania logowania do określonych domen. Możesz również wywołać i wywołać inne interfejsy API sieci Web, sklepy użytkowników i inne usługi w chmurze.

## <a name="next-steps"></a>Następne kroki

<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- Dowiedz się, jak [dodać niestandardowy przepływ pracy zatwierdzania do rejestracji](self-service-sign-up-add-approvals.md) samoobsługowej
- Rozpocznij pracę z naszymi [przykładami szybkiego startu usługi Azure Functions](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
