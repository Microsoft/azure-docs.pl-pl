---
title: Serwer FHIR programu Poster na platformie Azure — interfejs API platformy Azure dla FHIR
description: W tym samouczku przedstawiono kroki niezbędne do uzyskania dostępu do serwera FHIR przy użyciu programu Poster. Program pocztowy jest przydatny do debugowania aplikacji, które mają dostęp do interfejsów API.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 03/16/2021
ms.openlocfilehash: e9031dc77054a2bbac8015bbbdd7b9ed2a35e84f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043346"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Dostęp do interfejsu API platformy Azure dla usługi FHIR za pomocą programu Poster

Aplikacja kliencka może uzyskać dostęp do interfejsu API platformy Azure dla usługi FHIR za pomocą [interfejsu API REST](https://www.hl7.org/fhir/http.html). Aby wysyłać żądania, wyświetlać odpowiedzi i debugować aplikację podczas jej kompilowania, Użyj wybranego narzędzia do testowania interfejsu API. W tym samouczku przeprowadzimy Cię przez kroki uzyskiwania dostępu do serwera FHIR przy użyciu programu [Poster](https://www.getpostman.com/). 

## <a name="prerequisites"></a>Wymagania wstępne

- Punkt końcowy FHIR na platformie Azure. 

   Aby wdrożyć interfejs API platformy Azure dla FHIR (usługi zarządzanej), możesz użyć [Azure Portal](fhir-paas-portal-quickstart.md), [programu PowerShell](fhir-paas-powershell-quickstart.md)lub [interfejsu wiersza polecenia platformy Azure](fhir-paas-cli-quickstart.md).
- Zarejestrowana [poufna aplikacja kliencka](register-confidential-azure-ad-client-app.md) do uzyskiwania dostępu do usługi FHIR.
- Przyznano uprawnienia do poufnej aplikacji klienckiej, na przykład "Współautor danych FHIR", aby uzyskać dostęp do usługi FHIR. Aby uzyskać więcej informacji, zobacz [Konfigurowanie usługi Azure RBAC dla FHIR](./configure-azure-rbac.md).
- Zainstalowano notkę. 
    
    Aby uzyskać więcej informacji na temat programu Poster, zobacz [Rozpoczynanie pracy z programem Poster](https://www.getpostman.com).

## <a name="fhir-server-and-authentication-details"></a>FHIR serwer i szczegóły uwierzytelniania

Aby użyć programu Poster, wymagane są następujące parametry uwierzytelniania:

- Adres URL serwera FHIR, na przykład `https://MYACCOUNT.azurehealthcareapis.com`

- Dostawca tożsamości `Authority` dla serwera FHIR, na przykład `https://login.microsoftonline.com/{TENANT-ID}`

- Skonfigurowany, `audience` który jest zwykle adresem URL serwera FHIR, na przykład `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` lub `https://azurehealthcareapis.com` .

- `client_id`Identyfikator aplikacji [poufnej klienta](register-confidential-azure-ad-client-app.md) używany do uzyskiwania dostępu do usługi FHIR.

- `client_secret`Wpis tajny aplikacji lub poufnej aplikacji klienta.

Na koniec należy sprawdzić, czy `https://www.getpostman.com/oauth2/callback` jest zarejestrowanego adresu URL odpowiedzi dla aplikacji klienckiej.

## <a name="connect-to-fhir-server"></a>Łączenie z serwerem FHIR

Otwórz program Poster, a następnie wybierz pozycję **Get** , aby wykonać żądanie `https://fhir-server-url/metadata` .

![Instrukcja obsługi metadanych w programie Poster](media/tutorial-postman/postman-metadata.png)

Adresem URL metadanych dla usługi Azure API for FHIR jest `https://MYACCOUNT.azurehealthcareapis.com/metadata` . 

W tym przykładzie adres URL serwera FHIR to `https://fhirdocsmsft.azurewebsites.net` , a instrukcja możliwości serwera jest dostępna pod adresem `https://fhirdocsmsft.azurewebsites.net/metadata` . Ten punkt końcowy jest dostępny bez uwierzytelniania.

W przypadku próby uzyskania dostępu do zasobów z ograniczeniami wystąpi odpowiedź "uwierzytelnianie nie powiodło się".

![Uwierzytelnianie nie powiodło się](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Uzyskiwanie tokenu dostępu
Aby uzyskać prawidłowy token dostępu, wybierz pozycję **autoryzacja** i wybierz pozycję **OAuth 2,0** z menu rozwijanego **Typ** .

![Ustawianie protokołu OAuth 2,0](media/tutorial-postman/postman-select-oauth2.png)

Wybierz pozycję **Get New Access Token** (Uzyskaj nowy token dostępu).

![Żądaj nowego tokenu dostępu](media/tutorial-postman/postman-request-token.png)

W oknie dialogowym **pobieranie nowego tokenu dostępu** wprowadź następujące informacje:

| Pole                 | Przykładowa wartość                                                                                                   | Komentarz                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Nazwa tokenu            | Mój TOKEN                                                                                                         | Wybrana nazwa          |
| Typ udzielenia            | Kod autoryzacji                                                                                              |                            |
| Adres URL wywołania zwrotnego          | `https://www.getpostman.com/oauth2/callback`                                                                      |                            |
| Adres URL uwierzytelniania              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` jest `https://MYACCOUNT.azurehealthcareapis.com` przeznaczony dla interfejsu API platformy Azure dla FHIR |
| Adres URL tokenu dostępu      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                      |                            |
| Identyfikator klienta             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                            | Identyfikator aplikacji             |
| Klucz tajny klienta         | `XXXXXXXX`                                                                                                        | Klucz klienta tajnego          |
| Zakres | `<Leave Blank>` |
| Stan                |  `1234`                                                                                                           |                            |
| Uwierzytelnianie klienta | Wyślij poświadczenia klienta w treści                                                                                 |                 

Wybierz **token żądania** , który ma być kierowany przez przepływ uwierzytelniania Azure Active Directory, a token zostanie zwrócony do programu Poster. W przypadku wystąpienia błędu uwierzytelniania zapoznaj się z konsolą programu Poster, aby uzyskać więcej informacji. **Uwaga**: na wstążce wybierz pozycję **Widok**, a następnie wybierz pozycję **Pokaż konsolę programu post**. Skrót klawiaturowy do konsoli programu Poster jest **Alt-Ctrl + C**.

Przewiń w dół, aby wyświetlić ekran zwróconego tokenu, a następnie wybierz pozycję **Użyj tokenu**.

![Użyj tokenu](media/tutorial-postman/postman-use-token.png)

Zapoznaj się z informacjami w polu **token dostępu** , aby wyświetlić nowo wypełniony token. Jeśli wybierzesz pozycję **Wyślij** , aby powtórzyć `Patient` Wyszukiwanie zasobów, zostanie zwrócony **stan** `200 OK` . Zwrócony stan `200 OK` wskazuje pomyślne żądanie HTTP.

![200 OK](media/tutorial-postman/postman-200-OK.png)

W przykładzie *wyszukiwania pacjenta* nie ma żadnych pacjentów w bazie danych, takich jak wynik wyszukiwania jest pusty.

Token dostępu można sprawdzić za pomocą narzędzia, takiego jak [JWT.MS](https://jwt.ms). Poniżej przedstawiono przykład zawartości.

```json
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

W sytuacjach związanych z rozwiązywaniem problemów sprawdzenie, czy masz odpowiednich odbiorców ( `aud` roszczeń), jest dobrym miejscem do rozpoczęcia. Jeśli token jest z poprawnego wystawcy ( `iss` roszczeń) i ma odpowiednich odbiorców ( `aud` roszczeń), ale nadal nie możesz uzyskać dostępu do interfejsu API FHIR, prawdopodobnie nie `oid` ma dostępu do płaszczyzny danych FHIR. Zalecamy używanie [kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure](configure-azure-rbac.md) do przypisywania ról płaszczyzny danych do użytkowników. Jeśli używasz zewnętrznej, pomocniczej dzierżawy usługi Azure Active Directory dla danej płaszczyzny danych, musisz [skonfigurować lokalną kontrolę RBAC dla](configure-local-rbac.md) przypisań FHIR.

Można również uzyskać token dla [interfejsu API platformy Azure dla usługi FHIR przy użyciu interfejsu wiersza polecenia platformy Azure](get-healthcare-apis-access-token-cli.md). Jeśli używasz tokenu uzyskanego w interfejsie wiersza polecenia platformy Azure, musisz użyć *tokenu okaziciela* typu autoryzacji. Wklej token bezpośrednio.

## <a name="inserting-a-patient"></a>Wstawianie pacjenta

Przy użyciu prawidłowego tokenu dostępu można teraz wstawić nowego pacjenta. W programie Poster Zmień metodę, wybierając pozycję **post**, a następnie Dodaj następujący dokument JSON w treści żądania.

[!code-json[](../samples/sample-patient.json)]

Wybierz pozycję **Wyślij** , aby określić, że pacjent został pomyślnie utworzony.

![Zrzut ekranu pokazujący, że pacjent został pomyślnie utworzony.](media/tutorial-postman/postman-patient-created.png)

W przypadku powtórzenia wyszukiwania pacjenta należy teraz zobaczyć rekord pacjenta:

![Utworzono pacjenta](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku uzyskano dostęp do usługi Azure API for FHIR za pomocą programu Poster. Więcej informacji o interfejsie API platformy Azure dla funkcji FHIR można znaleźć w temacie.
 
>[!div class="nextstepaction"]
>[Obsługiwane funkcje](fhir-features-supported.md)
