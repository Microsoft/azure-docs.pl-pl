---
title: Przepływ kodu urządzenia OAuth 2.0 | Azure
titleSuffix: Microsoft identity platform
description: Zaloguj użytkowników bez przeglądarki. Tworzenie osadzonych i mniej przeglądarki przepływów uwierzytelniania przy użyciu dotacji autoryzacji urządzenia.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 42f3ca233597d0fbc31ce656bd856875e873e3c2
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868486"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Platforma tożsamości firmy Microsoft i przepływ autoryzacji urządzeń OAuth 2.0

Platforma tożsamości firmy Microsoft obsługuje [udzielanie autoryzacji urządzeń,](https://tools.ietf.org/html/rfc8628)które umożliwia użytkownikom logowanie się do urządzeń o ograniczonym dostępie, takich jak telewizor Smart TV, urządzenie IoT lub drukarka.  Aby włączyć ten przepływ, urządzenie ma użytkownika odwiedzić stronę sieci Web w przeglądarce na innym urządzeniu, aby się zalogować.  Gdy użytkownik zaloguje się, urządzenie jest w stanie uzyskać tokeny dostępu i odświeżyć tokeny w razie potrzeby.

W tym artykule opisano sposób programowania bezpośrednio względem protokołu w aplikacji.  Jeśli to możliwe, zaleca się użycie obsługiwanych bibliotek uwierzytelniania firmy Microsoft (MSAL) zamiast tego do [uzyskiwania tokenów i wywoływania zabezpieczonych interfejsów API sieci Web](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Zapoznaj się również z [przykładowymi aplikacjami korzystającymi z programu MSAL](sample-v2-code.md).

## <a name="protocol-diagram"></a>Diagram protokołu

Przepływ kodu całego urządzenia wygląda podobnie do następnego diagramu. Opisujemy każdy z kroków w dalszej części tego artykułu.

![Przepływ kodu urządzenia](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Żądanie autoryzacji urządzenia

Klient musi najpierw sprawdzić na serwerze uwierzytelniania dla urządzenia i kodu użytkownika, który jest używany do inicjowania uwierzytelniania. Klient zbiera to żądanie `/devicecode` z punktu końcowego. W tym żądaniu klient powinien również zawierać uprawnienia, które musi uzyskać od użytkownika. Od momentu wysłania tego żądania użytkownik ma tylko 15 minut na `expires_in`zalogowanie się (zwykle wartość), więc złożyć to żądanie tylko wtedy, gdy użytkownik wskaza, że jest gotowy do logowania.

> [!TIP]
> Spróbuj wykonać to żądanie w Postman!
> [![Spróbuj uruchomić to żądanie w postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagany | Może być /common, /consumers lub /organizations.  Może to być również dzierżawa katalogu, od której chcesz zażądać uprawnień w identyfikatorze GUID lub przyjaznym formacie nazwy.  |
| `client_id` | Wymagany | **Identyfikator aplikacji (klienta),** który usługa Azure portal — środowisko [rejestracji aplikacji przypisane](https://go.microsoft.com/fwlink/?linkid=2083908) do aplikacji. |
| `scope` | Zalecane | Oddzielona spacja lista [zakresów,](v2-permissions-and-consent.md) na które użytkownik ma wyrazić zgodę.  |

### <a name="device-authorization-response"></a>Odpowiedź autoryzacji urządzenia

Pomyślną odpowiedzią będzie obiekt JSON zawierający wymagane informacje, aby umożliwić użytkownikowi zalogowanie się.

| Parametr | Format | Opis |
| ---              | --- | --- |
|`device_code`     | Ciąg | Długi ciąg używany do weryfikacji sesji między klientem a serwerem autoryzacji. Klient używa tego parametru do żądania tokenu dostępu z serwera autoryzacji. |
|`user_code`       | Ciąg | Krótki ciąg wyświetlany użytkownikowi, który jest używany do identyfikowania sesji na urządzeniu pomocniczym.|
|`verification_uri`| Identyfikator URI | Identyfikator URI, do który `user_code` użytkownik powinien przejść z w celu zalogowania się. |
|`expires_in`      | int | Liczba sekund przed `device_code` i `user_code` wygaśnie. |
|`interval`        | int | Liczba sekund, przez które klient powinien czekać między żądaniami sondowania. |
| `message`        | Ciąg | Ciąg czytelny dla człowieka z instrukcjami dla użytkownika. Można to zlokalizować, dołączając **parametr zapytania** w `?mkt=xx-XX`żądaniu formularza, wypełniając odpowiedni kod kultury języka. |

> [!NOTE]
> Pole `verification_uri_complete` odpowiedzi nie jest obecnie uwzględniane ani obsługiwane.  Wspominamy o tym, ponieważ jeśli `verification_uri_complete` czytasz [standard,](https://tools.ietf.org/html/rfc8628) który jest wymieniony jako opcjonalna część standardu przepływu kodu urządzenia.

## <a name="authenticating-the-user"></a>Uwierzytelnianie użytkownika

Po otrzymaniu `user_code` i `verification_uri`, klient wyświetla je do użytkownika, instruując go, aby zalogować się za pomocą telefonu komórkowego lub przeglądarki PC.

Jeśli użytkownik uwierzytelnia się przy użyciu konta osobistego (na /common lub /consumers), zostanie poproszony o ponowne zalogowanie się w celu przeniesienia stanu uwierzytelniania na urządzenie.  Zostaną również poproszeni o wyrażenie zgody, aby upewnić się, że są świadomi przyznanych uprawnień.  Nie dotyczy to kont służbowych używanych do uwierzytelniania.

Podczas gdy użytkownik uwierzytelnia `verification_uri`się w programie `/token` , klient powinien sondować `device_code`punkt końcowy żądanego tokenu przy użyciu .

```HTTP
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parametr | Wymagany | Opis|
| -------- | -------- | ---------- |
| `tenant`  | Wymagany | Ten sam alias dzierżawy lub dzierżawy używany w żądaniu początkowym. |
| `grant_type` | Wymagany | Musi być`urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Wymagany | Musi odpowiadać używane w `client_id` początkowym żądaniu. |
| `device_code`| Wymagany | Zwrócone `device_code` w żądaniu autoryzacji urządzenia.  |

### <a name="expected-errors"></a>Oczekiwane błędy

Przepływ kodu urządzenia jest protokołem sondowania, więc klient musi oczekiwać, aby uzyskać błędy, zanim użytkownik zakończy uwierzytelnianie.

| Błąd | Opis | Akcja klienta |
| ------ | ----------- | -------------|
| `authorization_pending` | Użytkownik nie zakończył uwierzytelniania, ale nie anulował przepływu. | Powtórz żądanie po `interval` co najmniej sekundach. |
| `authorization_declined` | Użytkownik końcowy odmówił żądania autoryzacji.| Zatrzymaj sondowanie i przywróć stan nieuwierzyty.  |
| `bad_verification_code`| Wysłane `device_code` do `/token` punktu końcowego nie został rozpoznany. | Sprawdź, czy klient wysyła `device_code` poprawność w żądaniu. |
| `expired_token` | Minęło `expires_in` co najmniej sekundy, a uwierzytelnianie `device_code`nie jest już możliwe dzięki temu . | Zatrzymaj sondowanie i przywróć stan nieuwierzyty. |

### <a name="successful-authentication-response"></a>Pomyślna odpowiedź na uwierzytelnianie

Pomyślna odpowiedź tokenu będzie wyglądać następująco:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parametr | Format | Opis |
| --------- | ------ | ----------- |
| `token_type` | Ciąg| Zawsze "Okaziciela. |
| `scope` | Ciągi oddzielone przestrzenią | Jeśli token dostępu został zwrócony, to wyświetla zakresy token dostępu jest prawidłowy dla. |
| `expires_in`| int | Liczba sekund przed tokenem dostępu dołączone jest prawidłowa dla. |
| `access_token`| Nieprzezroczysty ciąg | Wydane dla [zakresów,](v2-permissions-and-consent.md) które zostały wymagane.  |
| `id_token`   | Jwt | Wystawiono, jeśli `scope` oryginalny parametr `openid` zawierał zakres.  |
| `refresh_token` | Nieprzezroczysty ciąg | Wydane, jeśli `scope` oryginalny parametr `offline_access`zawiera .  |

Token odświeżania służy do uzyskiwania nowych tokenów dostępu i odświeżania tokenów przy użyciu tego samego przepływu udokumentowanego w [dokumentacji przepływu kodu OAuth.](v2-oauth2-auth-code-flow.md#refresh-the-access-token)
