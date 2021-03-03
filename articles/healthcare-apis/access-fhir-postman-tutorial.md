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
ms.date: 02/01/2021
ms.openlocfilehash: 6c4d63862f4e02f3fd525021921b02cde6d10f61
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699338"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Dostęp do interfejsu API platformy Azure dla usługi FHIR za pomocą programu Poster

Aplikacja kliencka uzyskuje dostęp do interfejsu API FHIR za pomocą [interfejsu API REST](https://www.hl7.org/fhir/http.html). Możesz również powoływać się bezpośrednio z serwerem FHIR podczas kompilowania aplikacji, na przykład do celów debugowania. W tym samouczku przedstawiono kroki niezbędne do uzyskania dostępu do serwera FHIR przy użyciu programu [Poster](https://www.getpostman.com/) . Poster to narzędzie często używane do debugowania podczas kompilowania aplikacji, które uzyskują dostęp do interfejsów API.

## <a name="prerequisites"></a>Wymagania wstępne

- Punkt końcowy FHIR na platformie Azure. Można skonfigurować, aby korzystać z zarządzanego interfejsu API platformy Azure dla FHIR lub serwera Open Source FHIR dla platformy Azure. Skonfiguruj zarządzany interfejs API platformy Azure dla usługi FHIR przy użyciu [Azure Portal](fhir-paas-portal-quickstart.md), [programu PowerShell](fhir-paas-powershell-quickstart.md)lub [interfejsu wiersza polecenia platformy Azure](fhir-paas-cli-quickstart.md).
- [Aplikacja kliencka](register-confidential-azure-ad-client-app.md) , która będzie używana w celu uzyskania dostępu do usługi FHIR.
- Przyznano uprawnienia, na przykład "Współautor danych FHIR", do aplikacji klienckiej w celu uzyskania dostępu do usługi FHIR. Więcej informacji na temat [konfigurowania usługi Azure RBAC dla FHIR](./configure-azure-rbac.md)
- Zainstalowano notkę. Możesz uzyskać od [https://www.getpostman.com](https://www.getpostman.com)

## <a name="fhir-server-and-authentication-details"></a>FHIR serwer i szczegóły uwierzytelniania

Aby można było użyć programu Poster, konieczne są następujące informacje:

- Adres URL serwera FHIR, na przykład `https://MYACCOUNT.azurehealthcareapis.com`
- Dostawca tożsamości `Authority` dla serwera FHIR, na przykład `https://login.microsoftonline.com/{TENANT-ID}`
- Skonfigurowane `audience` . Jest to zazwyczaj adres URL serwera FHIR, np. `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` lub tylko `https://azurehealthcareapis.com` .
- `client_id`(Lub identyfikator aplikacji) [aplikacji klienckiej](register-confidential-azure-ad-client-app.md) , która będzie używana do uzyskiwania dostępu do usługi FHIR.
- `client_secret`(Lub klucz tajny aplikacji) aplikacji klienckiej.

Na koniec należy sprawdzić, czy `https://www.getpostman.com/oauth2/callback` jest zarejestrowanego adresu URL odpowiedzi dla aplikacji klienckiej.

## <a name="connect-to-fhir-server"></a>Łączenie z serwerem FHIR

Za pomocą programu Poster wykonaj `GET` żądanie, aby `https://fhir-server-url/metadata` :

![Instrukcja obsługi metadanych w programie Poster](media/tutorial-postman/postman-metadata.png)

Adresem URL metadanych dla usługi Azure API for FHIR jest `https://MYACCOUNT.azurehealthcareapis.com/metadata` . W tym przykładzie adres URL serwera FHIR to `https://fhirdocsmsft.azurewebsites.net` i instrukcja możliwości serwera jest dostępna pod adresem `https://fhirdocsmsft.azurewebsites.net/metadata` . Ten punkt końcowy powinien być dostępny bez uwierzytelniania.

W przypadku próby uzyskania dostępu do zasobów ograniczonych należy uzyskać odpowiedź "uwierzytelnianie nie powiodło się":

![Uwierzytelnianie nie powiodło się](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Uzyskiwanie tokenu dostępu

Aby uzyskać prawidłowy token dostępu, wybierz pozycję "Autoryzacja" i wybierz typ "OAuth 2,0":

![Ustawianie protokołu OAuth 2,0](media/tutorial-postman/postman-select-oauth2.png)

Trafij przycisk "Uzyskaj nowy token dostępu" i pojawi się okno dialogowe:

![Żądaj nowego tokenu dostępu](media/tutorial-postman/postman-request-token.png)

Konieczna będzie pewna część szczegółów:

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
| Stan                 | `1234`                                                                                                            |                            |
| Uwierzytelnianie klienta | Wyślij poświadczenia klienta w treści                                                                                 |                 

Trafij "token żądania" i przejdziesz przez przepływ uwierzytelniania Azure Active Directory, a token zostanie zwrócony do programu Poster. Jeśli wystąpią problemy, Otwórz konsolę programu Poster (z elementu menu "widok->Pokaż konsolę wpisów").

Przewiń w dół na ekranie zwróconego tokenu i naciśnij pozycję "Użyj tokenu":

![Użyj tokenu](media/tutorial-postman/postman-use-token.png)

Token powinien być teraz wypełniany w polu "token dostępu" i można wybrać tokeny z "dostępnych tokenów". Jeśli ponownie "Wyślij", aby powtórzyć `Patient` Wyszukiwanie zasobów, należy uzyskać stan `200 OK` :

![200 OK](media/tutorial-postman/postman-200-OK.png)

W takim przypadku w bazie danych nie ma pacjentów i wyszukiwanie jest puste.

Jeśli sprawdzisz token dostępu przy użyciu narzędzia, takiego jak [https://jwt.ms](https://jwt.ms) , powinna zostać wyświetlona zawartość, taka jak:

```jsonc
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

W sytuacjach związanych z rozwiązywaniem problemów sprawdzenie, czy masz odpowiednich odbiorców ( `aud` roszczeń), jest dobrym miejscem do rozpoczęcia. Jeśli token jest z poprawnego wystawcy ( `iss` roszczeń) i ma prawidłowych odbiorców ( `aud` roszczeń), ale nadal nie możesz uzyskać dostępu do interfejsu API FHIR, prawdopodobnie użytkownik lub główna usługa ( `oid` Claim) nie ma dostępu do płaszczyzny danych FHIR. Zalecamy [Używanie kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure](configure-azure-rbac.md) do przypisywania ról płaszczyzny danych do użytkowników. Jeśli używasz zewnętrznej, pomocniczej dzierżawy usługi Azure Active Directory dla danej płaszczyzny danych, musisz [skonfigurować lokalne przypisania RBAC](configure-local-rbac.md).

Można również [uzyskać token dla interfejsu API platformy Azure dla usługi FHIR przy użyciu interfejsu wiersza polecenia platformy Azure](get-healthcare-apis-access-token-cli.md). Jeśli używasz tokenu uzyskanego w interfejsie wiersza polecenia platformy Azure, należy użyć typu autoryzacji "token okaziciela" i bezpośrednio wkleić token.

## <a name="inserting-a-patient"></a>Wstawianie pacjenta

Teraz masz prawidłowy token dostępu. Możesz wstawić nowego pacjenta. Przejdź do metody "POST" i Dodaj następujący dokument JSON w treści żądania:

[!code-json[](samples/sample-patient.json)]

Trafij "Send" i zobaczysz, że pacjent został pomyślnie utworzony:

![Zrzut ekranu pokazujący, że pacjent został pomyślnie utworzony.](media/tutorial-postman/postman-patient-created.png)

W przypadku powtórzenia wyszukiwania pacjenta należy teraz zobaczyć rekord pacjenta:

![Utworzono pacjenta](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku uzyskano dostęp do interfejsu API FHIR przy użyciu programu Poster. Przeczytaj o obsługiwanych funkcjach interfejsu API w sekcji obsługiwane funkcje.
 
>[!div class="nextstepaction"]
>[Obsługiwane funkcje](fhir-features-supported.md)