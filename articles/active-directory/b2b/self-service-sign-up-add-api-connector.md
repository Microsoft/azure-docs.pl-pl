---
title: Dodawanie łącznika interfejsu API do przepływu użytkownika
description: Skonfiguruj interfejs API sieci Web, który ma być używany w przepływie użytkownika.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 738e25a0cd9778f3ee767fc3f9b6944c9c2e6473
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84680056"
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
 
    ![Ustaw oświadczenia łącznika interfejsu API](./media/self-service-sign-up-add-api-connector/api-connector-claims.png)

10. Wybierz pozycję **Zapisz**.
 
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
 "email_address": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "postalCode": "33971",
 "extension_<aad-extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<aad-extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

**Lokalne żądanie interfejsu użytkownika ("ui_locales")** jest domyślnie wysyłane we wszystkich żądaniach. Zapewnia on ustawienia regionalne użytkownika i może być używany przez interfejs API do zwracania odpowiedzi międzynarodowych. Nie jest on wyświetlany w okienku konfiguracji interfejsu API.

Jeśli nie ma wartości w momencie wywołania punktu końcowego interfejsu API, nie zostanie ono wysłane do interfejsu API.

Atrybuty niestandardowe można utworzyć dla użytkownika przy użyciu formatu **extension_ \<aad-extensions-app-id> _ \<AttributeName> ** . Interfejs API powinien oczekiwać otrzymywania oświadczeń w tym samym zserializowanym formacie. Interfejs API może zwracać oświadczenia w tym samym formacie, ale bez `<aad-extensions-app-id>` . <!--TODO: update once fix is rolled out--> Aby uzyskać więcej informacji o atrybutach niestandardowych, zobacz [Definiowanie atrybutów niestandardowych dla przepływów rejestracji samoobsługowej](user-flow-add-custom-attributes.md).

> [!TIP]
> [**Tożsamości ("tożsamości")**](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0) oraz oświadczenia **adresu e-mail ("email_address")** mogą służyć do identyfikowania użytkownika przed utworzeniem konta w dzierżawie. W przypadku, gdy użytkownik uwierzytelnia się za pomocą usługi Google lub Facebook, a element "email_address" jest zawsze wysyłany, jest wysyłane zgłoszenie "tożsamości".


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
    "extension_CustomAttribute": "value" // return claim 
}
```

| Parametr  | Typ  | Wymagane | Opis |
|---|---|---|---|
| version | String | Tak | Wersja interfejsu API. |
| action  | String | Tak | Wartość musi być `Continue` . |
| \<builtInUserAttribute> | \<attribute-type> | Nie  | Wartości mogą być przechowywane w katalogu, jeśli zostały wybrane jako takie, **które mają zostać odebrane** w ramach konfiguracji łącznika interfejsu API i **atrybutów użytkownika** dla przepływu użytkownika. Wartości mogą być zwracane w tokenie, jeśli są wybrane jako **wnioski aplikacji**. |
| \<extension_CustomAttribute> | \<attribute-type> | Nie  | Zwracane zgłoszenie *nie* ma `_<aad-extensions-app-id>_` . Wartości są przechowywane w katalogu, jeśli zostały wybrane jako jako "jako" jako "jako" jako "jako" jako "jako" jako "jako" **jako jako rolę w** **atrybucie User** Connector dla przepływu użytkownika. Nie można ponownie wysłać atrybutów niestandardowych do tokenu. |

### <a name="blocking-response"></a>Zablokuj odpowiedź

Zablokowanie odpowiedzi kończy przepływ użytkownika. Może być celowo wystawiony przez interfejs API, aby zatrzymać kontynuację przepływu użytkownika przez wyświetlenie strony blokowej dla użytkownika. Na stronie blok zostanie wyświetlona wartość `userMessage` dostarczone przez interfejs API. 

Poniżej przedstawiono przykład blokady odpowiedzi:

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

| Parametr  | Typ  | Wymagane | Opis |
|---|---|---|---|
| version         | String           | Tak      | Wersja interfejsu API.    |
| action          | String           | Tak      | Wartość musi być równa`ShowBlockPage`  |
| userMessage     | String           | Tak      | Komunikat wyświetlany użytkownikowi.    |
| kod            | String           | Nie       | Kod błędu. Może służyć do celów debugowania. Niewidoczne dla użytkownika.    |

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

| Parametr  | Typ  | Wymagane | Opis |
|---|---|---|---|
| version         | String           | Tak      | Wersja interfejsu API.   |
| action          | String           | Tak      | Wartość musi być `ValidationError` .   |
| status          | Integer          | Tak      | Musi być wartością `400` dla odpowiedzi ValidationError.  |
| userMessage     | String           | Tak      | Komunikat wyświetlany użytkownikowi.   |
| kod            | String           | Nie       | Kod błędu. Może służyć do celów debugowania. Niewidoczne dla użytkownika.    |

#### <a name="end-user-experience-with-a-validation-error-response"></a>Środowisko użytkownika końcowego ze sprawdzaniem poprawności — odpowiedź z błędami

![Przykładowa strona walidacji](./media/api-connectors-overview/validation-error-postal-code.png)


## <a name="next-steps"></a>Następne kroki
- Dowiedz się, w [jaki sposób interfejs API powinien odpowiadać](self-service-sign-up-add-api-connector.md#expected-response-types-from-the-web-api)
- Dowiedz się [, gdzie można włączyć łącznik interfejsu API](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow)
- Dowiedz się, jak [dodać niestandardowy przepływ pracy zatwierdzania do rejestracji](self-service-sign-up-add-approvals.md) samoobsługowej
<!-- - Learn how to [use API connectors for identity proofing](code-samples-self-service-sign-up.md#identity-proofing) -->