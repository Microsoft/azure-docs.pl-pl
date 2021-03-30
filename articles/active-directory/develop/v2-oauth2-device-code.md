---
title: Przepływ kodu urządzenia OAuth 2,0 | Azure
titleSuffix: Microsoft identity platform
description: Użytkownicy logują się bez przeglądarki. Tworzenie wbudowanych i niezwiązanych z przeglądarką przepływów uwierzytelniania przy użyciu uprawnień do autoryzacji urządzeń.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 8c757f3e067aeac5d8145ca47b2eac145daba574
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88272454"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft Identity platform i autoryzacja autoryzacji urządzeń z uwierzytelnianiem OAuth 2,0

Platforma tożsamości firmy Microsoft obsługuje [przyznawanie autoryzacji urządzeń](https://tools.ietf.org/html/rfc8628), co pozwala użytkownikom na logowanie się na urządzeniach z ograniczeniami, takich jak inteligentna telewizja, urządzenie IoT lub drukarka.  Aby włączyć ten przepływ, na urządzeniu zostanie wyświetlona strona sieci Web w przeglądarce na innym urządzeniu w celu zalogowania się.  Po zalogowaniu się użytkownika urządzenie będzie w stanie uzyskać tokeny dostępu i odświeżać tokeny zgodnie z wymaganiami.

W tym artykule opisano, jak programować bezpośrednio w odniesieniu do protokołu w aplikacji.  Jeśli to możliwe, zalecamy korzystanie z obsługiwanych bibliotek uwierzytelniania firmy Microsoft (MSAL) zamiast [uzyskiwać tokeny i wywoływać zabezpieczone interfejsy API sieci Web](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Zapoznaj się również z [przykładowymi aplikacjami korzystającymi z MSAL](sample-v2-code.md).

## <a name="protocol-diagram"></a>Diagram protokołu

Cały przepływ kodu urządzenia wygląda podobnie do następnego diagramu. Opisujemy każdy z kroków opisanych w dalszej części tego artykułu.

![Przepływ kodu urządzenia](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Żądanie autoryzacji urządzenia

Klient musi najpierw sprawdzić, czy na serwerze uwierzytelniania znajduje się kod urządzenia i użytkownika używany do inicjowania uwierzytelniania. Klient zbiera żądanie z `/devicecode` punktu końcowego. W tym żądaniu klient powinien również zawierać uprawnienia wymagane do uzyskania od użytkownika. Od momentu wysłania tego żądania użytkownik ma tylko 15 minut na zalogowanie (zazwyczaj wartość dla `expires_in` ), więc to żądanie należy wykonać tylko wtedy, gdy użytkownik wykazał, że jest gotowy do zalogowania się.

> [!TIP]
> Spróbuj wykonać to żądanie w programie Poster!
> [![Spróbuj uruchomić to żądanie w programie Poster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagane | Może być/typowe,/consumers lub/Organizations.  Może to być również dzierżawca katalogu, do którego chcesz zażądać uprawnień w formacie GUID lub przyjaznych nazw.  |
| `client_id` | Wymagane | **Identyfikator aplikacji (klienta)** , który [Azure Portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko przypisane do aplikacji. |
| `scope` | Wymagane | Rozdzielana spacjami lista [zakresów](v2-permissions-and-consent.md) , do których użytkownik ma wyrazić zgodę.  |

### <a name="device-authorization-response"></a>Odpowiedź na autoryzację urządzenia

Pomyślna odpowiedź będzie obiektem JSON zawierającym wymagane informacje umożliwiające użytkownikowi zalogowanie się.

| Parametr | Format | Opis |
| ---              | --- | --- |
|`device_code`     | Ciąg | Długi ciąg używany do weryfikowania sesji między klientem a serwerem autoryzacji. Klient używa tego parametru, aby zażądać tokenu dostępu z serwera autoryzacji. |
|`user_code`       | Ciąg | Krótki ciąg pokazywany użytkownikowi, który służy do identyfikowania sesji na urządzeniu pomocniczym.|
|`verification_uri`| URI | Identyfikator URI, do którego użytkownik powinien przejść, aby `user_code` móc się zalogować. |
|`expires_in`      | int | Liczba sekund przed `device_code` `user_code` wygaśnięciem i. |
|`interval`        | int | Liczba sekund, przez jaką klient powinien czekać między żądaniami sondowania. |
| `message`        | Ciąg | Czytelny dla człowieka ciąg zawierający instrukcje dla użytkownika. Może to być lokalizowane przez dołączenie **parametru zapytania** w żądaniu formularza `?mkt=xx-XX` , wypełniając odpowiedni kod kultury języka. |

> [!NOTE]
> `verification_uri_complete`Pole odpowiedzi nie jest w tej chwili uwzględniane ani obsługiwane.  Wspominamy, że jeśli odczytasz [Standard](https://tools.ietf.org/html/rfc8628) , zobaczysz, że `verification_uri_complete` jest on wymieniony jako opcjonalny element standardowego przepływu kodu urządzenia.

## <a name="authenticating-the-user"></a>Uwierzytelnianie użytkownika

Po odebraniu usługi `user_code` i `verification_uri` Klient wyświetli te elementy użytkownikowi, co powoduje ich zalogowanie się przy użyciu telefonu komórkowego lub przeglądarki komputerowej.

Jeśli użytkownik jest uwierzytelniany przy użyciu konta osobistego (na/typowe lub/consumers), zostanie poproszony o ponowne zalogowanie się w celu przetransferowania stanu uwierzytelniania na urządzenie.  Zostaną również poproszeni o udzielenie zgody, aby upewnić się, że są świadome udzielanych uprawnień.  Nie dotyczy to kont służbowych używanych do uwierzytelniania.

Podczas uwierzytelniania użytkownika w usłudze `verification_uri` należy wykonać sondowanie `/token` punktu końcowego dla żądanego tokenu przy użyciu `device_code` .

```HTTP
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parametr | Wymagane | Opis|
| -------- | -------- | ---------- |
| `tenant`  | Wymagane | Ten sam alias dzierżawcy lub dzierżawy używany w żądaniu początkowym. |
| `grant_type` | Wymagane | Musi być `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Wymagane | Musi być zgodna z `client_id` użytym w początkowym żądaniu. |
| `device_code`| Wymagane | `device_code`Zwrócone w żądaniu autoryzacji urządzenia.  |

### <a name="expected-errors"></a>Oczekiwane błędy

Przepływ kodu urządzenia to protokół sondowania, aby klient oczekiwał na otrzymywanie błędów, zanim użytkownik zakończy uwierzytelnianie.

| Błąd | Opis | Akcja klienta |
| ------ | ----------- | -------------|
| `authorization_pending` | Użytkownik nie zakończył uwierzytelniania, ale nie anulował przepływu. | Powtórz żądanie po co najmniej `interval` sekundach. |
| `authorization_declined` | Użytkownik końcowy odrzucił żądanie autoryzacji.| Zatrzymaj sondowanie i Przywróć stan nieuwierzytelniony.  |
| `bad_verification_code`| `device_code` `/token` Nie rozpoznano wysłanego do punktu końcowego. | Sprawdź, czy klient wysyła poprawny `device_code` w żądaniu. |
| `expired_token` | Co najmniej `expires_in` kilka sekund zostało zakończone, a uwierzytelnianie nie jest już możliwe `device_code` . | Zatrzymaj sondowanie i Przywróć stan nieuwierzytelniony. |

### <a name="successful-authentication-response"></a>Pomyślna odpowiedź uwierzytelniania

Pomyślna odpowiedź dotycząca tokenu będzie wyglądać następująco:

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
| `token_type` | Ciąg| Zawsze "Bearer". |
| `scope` | Ciągi rozdzielone spacją | Jeśli został zwrócony token dostępu, spowoduje to wyświetlenie listy zakresów, dla których token dostępu jest prawidłowy. |
| `expires_in`| int | Liczba sekund, po upływie których uwzględniony token dostępu jest prawidłowy dla elementu. |
| `access_token`| Ciąg nieprzezroczysty | Wystawiony dla żądanych [zakresów](v2-permissions-and-consent.md) .  |
| `id_token`   | JWT | Wystawiony, jeśli oryginalny `scope` parametr zawiera `openid` zakres.  |
| `refresh_token` | Ciąg nieprzezroczysty | Wystawiony, jeśli zostanie `scope` uwzględniony oryginalny parametr `offline_access` .  |

Możesz użyć tokenu odświeżania, aby uzyskać nowe tokeny dostępu i odświeżać tokeny przy użyciu tego samego przepływu udokumentowanego w [dokumentacji przepływu kodu OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).
