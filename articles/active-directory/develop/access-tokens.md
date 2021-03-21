---
title: Tokeny dostępu platformy tożsamości firmy Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o tokenach dostępu emitowanych przez punkty końcowe usługi Azure AD v 1.0 i Microsoft Identity platform (v 2.0).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 2/18/2021
ms.author: hirsin
ms.reviewer: mmacy, hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 8630dd2fb1157fbeba99f2a06d73712ab46a63f4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035071"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Tokeny dostępu platformy tożsamości firmy Microsoft

Tokeny dostępu umożliwiają klientom bezpieczne wywoływanie chronionych interfejsów API sieci Web, które są używane przez interfejsy API sieci Web do uwierzytelniania i autoryzacji. W przypadku specyfikacji OAuth tokeny dostępu są nieprzezroczystymi ciągami bez formatu zestawu — niektórzy dostawcy tożsamości (dostawców tożsamości) używają identyfikatorów GUID, a inne używają szyfrowanych obiektów BLOB. Platforma tożsamości firmy Microsoft korzysta z różnych formatów tokenów dostępu w zależności od konfiguracji interfejsu API, który akceptuje token. [Niestandardowe interfejsy API zarejestrowane przez deweloperów](quickstart-configure-app-expose-web-apis.md) na platformie tożsamości firmy Microsoft mogą wybierać spośród dwóch różnych formatów tokenów sieci Web JSON (JWTs) o nazwie "v1" i "v2", a interfejsy API opracowane przez firmę Microsoft, takie jak Microsoft Graph lub interfejsy API na platformie Azure, mają dodatkowe formaty tokenów własnych. Te formaty zastrzeżone mogą być zaszyfrowane tokeny, JWTs lub specjalne tokeny podobne do JWT, które nie będą weryfikowane.

Klienci muszą traktować tokeny dostępu jako ciągi nieprzezroczyste, ponieważ zawartość tokenu jest przeznaczona tylko dla zasobu (API). *Tylko* do celów związanych z walidacją i debugowaniem deweloperzy mogą zdekodować JWTs za pomocą witryny, takiej jak [JWT.MS](https://jwt.ms). Należy jednak pamiętać, że tokeny otrzymane dla interfejsu API firmy Microsoft mogą nie zawsze być JWT i nie można ich dekodować.

Aby uzyskać szczegółowe informacje o tym, co znajduje się w tokenie dostępu, klienci powinni używać danych odpowiedzi o tokenach, które są zwracane z tokenem dostępu do klienta. Gdy klient zażąda tokenu dostępu, platforma tożsamości firmy Microsoft zwróci również metadane dotyczące tokenu dostępu dla użycia aplikacji. Te informacje obejmują czas wygaśnięcia tokenu dostępu i zakresy, dla których jest on prawidłowy. Te dane umożliwiają aplikacji inteligentne buforowanie tokenów dostępu bez konieczności analizowania samego tokenu dostępu.

Zapoznaj się z następującymi sekcjami, aby dowiedzieć się, jak interfejs API może sprawdzać poprawność oświadczeń i używać ich w ramach tokenu dostępu  

> [!NOTE]
> Wszystkie dokumenty na tej stronie, z wyjątkiem sytuacji, gdy zaznaczono inaczej, mają zastosowanie tylko do tokenów wystawionych dla zarejestrowanych interfejsów API.  Nie ma zastosowania do tokenów wystawionych dla interfejsów API firmy Microsoft, a tokeny te nie mogą być używane do sprawdzania, jak platforma tożsamości firmy Microsoft będzie wystawiała tokeny dla tworzonego interfejsu API.  

## <a name="token-formats-and-ownership"></a>Formaty i własności tokenu

### <a name="v10-and-v20"></a>Wersja 1.0 i wersja 2.0 

Istnieją dwie wersje tokenów dostępu dostępne na platformie tożsamości firmy Microsoft: v 1.0 i 2.0.  Te wersje określają, jakie oświadczenia znajdują się w tokenie, dzięki czemu interfejs API sieci Web może kontrolować, jak wyglądają ich tokeny. Interfejsy API sieci Web mają jedną z wybranych jako domyślne podczas rejestracji w wersji 1.0 dla aplikacji obsługujących usługę Azure AD i 2.0 dla aplikacji, które obsługują konta klientów.  Jest to kontrolowane przez aplikacje korzystające z `accessTokenAcceptedVersion` Ustawienia w [manifeście aplikacji](reference-app-manifest.md#manifest-reference), gdzie `null` i `1` wynik w tokenach v 1.0, a `2` wyniki w tokenach v 2.0.

### <a name="what-app-is-a-token-for"></a>Jakiej aplikacji jest token "for"?

Żądanie tokenu dostępu obejmuje dwie strony: klient, który żąda tokenu oraz zasób (interfejs API) akceptujący token, gdy wywoływany jest interfejs API. `aud`W tokenie wskazuje zasób, dla którego jest przeznaczony token (jego *odbiorcy*). Klienci używają tokenu, ale nie powinni zrozumieć ani próbować go przeanalizować. Zasoby akceptują token.  

Platforma tożsamości firmy Microsoft obsługuje wystawianie dowolnej wersji tokenu z poziomu punktu końcowego wersji — nie są one powiązane. Dlatego ustawienie zasobu `accessTokenAcceptedVersion` `2` oznacza, że klient wywołujący punkt końcowy v 1.0 do uzyskania tokenu dla tego interfejsu API otrzyma token dostępu w wersji 2.0.  Zasoby zawsze są właścicielami swoich tokenów (z ich `aud` wnioskiem) i są jedynymi aplikacjami, które mogą zmieniać swoje szczegóły tokenu. Z tego powodu zmiana [opcjonalnych oświadczeń](active-directory-optional-claims.md) tokenu dostępu dla *klienta* nie powoduje zmiany tokenu dostępu otrzymanego, gdy wymagany jest token dla `user.read` , który należy do zasobu Microsoft Graph.

### <a name="sample-tokens"></a>Przykładowe tokeny

tokeny 1.0 i v 2.0 wyglądają podobnie i zawierają wiele z tych samych oświadczeń. Przykładem każdego z nich jest tutaj. Te przykładowe tokeny nie będą [zweryfikowane](#validating-tokens), jednak ponieważ klucze zostały obrócone przed publikacją i dane osobowe zostały z nich usunięte.

#### <a name="v10"></a>Wersja 1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Wyświetl ten token v 1.0 w [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd).

#### <a name="v20"></a>Wersja 2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Wyświetl ten token v 2.0 w [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Oświadczenia w tokenach dostępu

JWTs (tokeny sieci Web JSON) są podzielone na trzy sztuki:

* **Nagłówek** — zawiera informacje na temat [weryfikowania tokenu, w](#validating-tokens) tym informacje o typie tokenu i sposobie jego podpisywania.
* **Ładunek** — zawiera wszystkie ważne dane dotyczące użytkownika lub aplikacji próbujących wywołać usługę.
* **Signature** — jest surowcem używanym do sprawdzania poprawności tokenu.

Każdy element jest oddzielony kropką ( `.` ) i osobno zakodowany w formacie base64.

Oświadczenia są obecne tylko wtedy, gdy istnieje wartość do wypełnienia. Twoja aplikacja nie powinna podejmować zależności od obecnego żądania. Przykładowo `pwd_exp` (nie każdy dzierżawca wymaga hasła) i `family_name` (przepływy[poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md) znajdują się w imieniu aplikacji, które nie mają nazw). Oświadczenia używane do sprawdzania poprawności tokenu dostępu będą zawsze obecne.

Niektóre oświadczenia są używane do zabezpieczania tokenów usługi Azure AD w przypadku ponownego użycia. Są one oznaczane jako nieprzeznaczone do użycia publicznego w opisie jako "nieprzezroczyste". Te oświadczenia mogą lub nie mogą występować w tokenie, a nowe mogą zostać dodane bez powiadomienia.

### <a name="header-claims"></a>Oświadczenia nagłówka

|Claim | Format | Opis |
|--------|--------|-------------|
| `typ` | Ciąg — zawsze "JWT" | Wskazuje, że token jest JWT.|
| `nonce` | Ciąg | Unikatowy identyfikator używany do ochrony przed atakami polegającymi na powtarzaniu tokenu. Zasób może zapisać tę wartość, aby chronić przed odtwarzaniem. |
| `alg` | Ciąg | Wskazuje algorytm, który został użyty do podpisania tokenu, na przykład "RS256" |
| `kid` | Ciąg | Określa odcisk palca klucza publicznego, który jest używany do podpisywania tego tokenu. Emitowane w tokenach dostępu zarówno w wersji 1.0, jak i 2.0. |
| `x5t` | Ciąg | Działa tak samo (w użyciu i wartości) jak `kid` . `x5t` czy starsze zgłoszenie jest emitowane tylko w tokenach dostępu w wersji 1.0 dla celów zgodności. |

### <a name="payload-claims"></a>Oświadczenia ładunku

| Claim | Format | Opis |
|-----|--------|-------------|
| `aud` | Ciąg, identyfikator URI identyfikatora aplikacji lub identyfikator GUID | Identyfikuje zamierzony odbiorcę tokenu — jej odbiorcy.  Interfejs API powinien sprawdzić poprawność tej wartości i odrzucić token, jeśli wartość nie jest zgodna. W tokenach v 2.0 jest to zawsze identyfikator klienta interfejsu API, a w tokenach w wersji 1.0 może to być identyfikator klienta lub identyfikator URI zasobu używany w żądaniu, w zależności od tego, jak klient zażądał tokenu.|
| `iss` | Ciąg, identyfikator URI usługi STS | Identyfikuje usługę tokenu zabezpieczającego (STS), która konstruuje i zwraca token oraz dzierżawę usługi Azure AD, w której użytkownik został uwierzytelniony. Jeśli token wystawiony jest tokenem v 2.0 (patrz " `ver` Claim"), identyfikator URI zakończy się `/v2.0` . Identyfikator GUID, który wskazuje, że użytkownik jest użytkownikiem konsumenta z konto Microsoft to `9188040d-6c67-4c5b-b112-36a304b66dad` . Twoja aplikacja może użyć części identyfikatora GUID żądania, aby ograniczyć zbiór dzierżawców, którzy mogą logować się do aplikacji, jeśli ma to zastosowanie. |
|`idp`| Ciąg, zazwyczaj identyfikator URI usługi STS | Rejestruje dostawcę tożsamości, który uwierzytelnił podmiot tokenu. Ta wartość jest taka sama jak wartość odszkodowania wystawcy, chyba że konto użytkownika nie znajduje się w tej samej dzierżawie co wystawcy, na przykład. Jeśli oświadczenia nie jest obecny, oznacza to, że `iss` można użyć zamiast niego wartości.  W przypadku kont osobistych używanych w kontekście organizacyjnym (np. konta osobistego zaproszonego do dzierżawy usługi Azure AD) `idp` może istnieć wartość "Live.com" lub identyfikator URI usługi STS zawierający dzierżawcę konto Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad` . |
| `iat` | int, sygnatura czasowa systemu UNIX | "Wystawiony w" wskazuje, kiedy wystąpiło uwierzytelnianie dla tego tokenu. |
| `nbf` | int, sygnatura czasowa systemu UNIX | Wartość "NBF" (nie wcześniej) określa czas, po którym nie można zatwierdzić tokenu JWT do przetwarzania. |
| `exp` | int, sygnatura czasowa systemu UNIX | Wartość "EXP" (czas wygaśnięcia) określa czas wygaśnięcia w dniu lub, po którym nie można zaakceptować tokenu JWT do przetworzenia. Należy pamiętać, że zasób może odrzucić token przed tym terminem, na przykład jeśli wymagana jest zmiana uwierzytelniania lub wykryto odwołanie do tokenu. |
| `aio` | Ciąg nieprzezroczysty | Deklaracja wewnętrzna używana przez usługę Azure AD do rejestrowania danych do ponownego użycia tokenu. Zasoby nie powinny używać tego żądania. |
| `acr` | Ciąg, "0" lub "1" | Występuje tylko w tokenach v 1.0. Wartość żądania "Klasa kontekstu uwierzytelniania". Wartość "0" wskazuje, że uwierzytelnianie użytkownika końcowego nie spełnia wymagań ISO/IEC 29115. |
| `amr` | Tablica JSON ciągów | Występuje tylko w tokenach v 1.0. Określa sposób uwierzytelniania podmiotu tokenu. Aby uzyskać więcej informacji [, zobacz sekcję "AMR](#the-amr-claim) ". |
| `appid` | Ciąg, identyfikator GUID | Występuje tylko w tokenach v 1.0. Identyfikator aplikacji klienta korzystającej z tokenu. Aplikacja może działać jako sama lub w imieniu użytkownika. Identyfikator aplikacji zazwyczaj reprezentuje obiekt aplikacji, ale może również reprezentować obiekt główny usługi w usłudze Azure AD. |
| `azp` | Ciąg, identyfikator GUID | Występuje tylko w przypadku tokenów v 2.0, Zamiennik dla `appid` . Identyfikator aplikacji klienta korzystającej z tokenu. Aplikacja może działać jako sama lub w imieniu użytkownika. Identyfikator aplikacji zazwyczaj reprezentuje obiekt aplikacji, ale może również reprezentować obiekt główny usługi w usłudze Azure AD. |
| `appidacr` | "0", "1" lub "2" | Występuje tylko w tokenach v 1.0. Wskazuje, w jaki sposób klient został uwierzytelniony. W przypadku klienta publicznego wartością jest "0". Jeśli używasz identyfikatora klienta i klucza tajnego klienta, wartość jest równa "1". Jeśli certyfikat klienta został użyty do uwierzytelnienia, wartość jest równa "2". |
| `azpacr` | "0", "1" lub "2" | Występuje tylko w przypadku tokenów v 2.0, Zamiennik dla `appidacr` . Wskazuje, w jaki sposób klient został uwierzytelniony. W przypadku klienta publicznego wartością jest "0". Jeśli używasz identyfikatora klienta i klucza tajnego klienta, wartość jest równa "1". Jeśli certyfikat klienta został użyty do uwierzytelnienia, wartość jest równa "2". |
| `preferred_username` | Ciąg | Podstawowa nazwa użytkownika, która reprezentuje użytkownika. Może to być adres e-mail, numer telefonu lub ogólna nazwa użytkownika bez określonego formatu. Jego wartość jest modyfikowalna i może ulec zmianie w czasie. Ponieważ jest modyfikowalny, ta wartość nie może być używana do podejmowania decyzji dotyczących autoryzacji.  Można go używać na potrzeby wskazówek dotyczących nazw użytkowników, a także w interfejsie użytkownika z możliwością czytania przez człowieka jako nazwę użytkownika. `profile`Zakres jest wymagany w celu otrzymania tego żądania. Obecne tylko w tokenach v 2.0. |
| `name` | Ciąg | Zapewnia czytelną dla człowieka wartość, która identyfikuje podmiot tokenu. Wartość nie może być unikatowa, jest modyfikowalna i przeznaczona do użycia tylko do celów wyświetlania. `profile`Zakres jest wymagany w celu otrzymania tego żądania. |
| `scp` | Ciąg, rozdzielana spacjami lista zakresów | Zestaw zakresów uwidocznionych przez aplikację, dla których aplikacja kliencka zażądała (i została odebrana). Aplikacja powinna sprawdzić, czy te zakresy są prawidłowe dla danej aplikacji, i podejmować decyzje dotyczące autoryzacji na podstawie wartości tych zakresów. Uwzględnione tylko w przypadku [tokenów użytkowników](#user-and-application-tokens). |
| `roles` | Tablica ciągów, lista uprawnień | Zestaw uprawnień uwidocznionych przez aplikację, dla których aplikacja żądająca lub użytkownik przyznał uprawnienia do wywoływania. W przypadku [tokenów aplikacji](#user-and-application-tokens)jest używany w ramach przepływu poświadczeń klienta ([v 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v 2.0](v2-oauth2-client-creds-grant-flow.md)) zamiast zakresów użytkowników.  W przypadku [tokenów użytkowników](#user-and-application-tokens) jest to wypełniane rolami przypisanymi do użytkownika w aplikacji docelowej. |
| `wids` | Tablica identyfikatorów GUID [RoleTemplateID](../roles/permissions-reference.md#all-roles) | Wskazuje role w całej dzierżawie przypisane do tego użytkownika, z sekcji ról znajdujących się w [wbudowanych rolach usługi Azure AD](../roles/permissions-reference.md#all-roles).  To zgłoszenie jest konfigurowane dla poszczególnych aplikacji, przez `groupMembershipClaims` Właściwość [manifestu aplikacji](reference-app-manifest.md).  Ustawienie go na "All" lub "DirectoryRole" jest wymagane.  Może nie być obecny w tokenach uzyskanych za pomocą niejawnego przepływu ze względu na długość tokenu. |
| `groups` | Tablica JSON identyfikatorów GUID | Dostarcza identyfikatory obiektów, które reprezentują członkostwo w grupach podmiotu. Te wartości są unikatowe (zobacz identyfikator obiektu) i mogą być bezpiecznie używane do zarządzania dostępem, takie jak wymuszanie autoryzacji dostępu do zasobu. Grupy zawarte w ramach roszczeń grup są konfigurowane dla poszczególnych aplikacji, przez `groupMembershipClaims` Właściwość [manifestu aplikacji](reference-app-manifest.md). Wartość null spowoduje wykluczenie wszystkich grup, a wartość "Security Group" będzie zawierać tylko Active Directory przynależności do grupy zabezpieczeń, a wartość "All" będzie obejmować zarówno grupy zabezpieczeń, jak i listy dystrybucyjne Microsoft 365. <br><br>Zapoznaj się z poniższym zgłoszeniem, `hasgroups` Aby uzyskać szczegółowe informacje na temat używania `groups` roszczeń z niejawnym udzieleniem. <br>W przypadku innych przepływów, jeśli liczba grup, do których należy użytkownik, przekracza limit (150 dla protokołu SAML, 200 dla tokenu JWT), do źródeł roszczeń zostanie dodana wartość nadwyżkowa, która wskazuje na punkt końcowy Microsoft Graph zawierający listę grup dla użytkownika. |
| `hasgroups` | Wartość logiczna | Jeśli jest obecny, zawsze `true` oznacza to, że użytkownik należy do co najmniej jednej grupy. Używane zamiast `groups` roszczeń dla JWTs w niejawnej postaci, jeśli w ramach żądania Full Groups zostanie rozszerzona wartość fragmentu identyfikatora URI poza limitami długości adresów URL (obecnie 6 lub więcej grup). Wskazuje, że klient powinien używać interfejsu API Microsoft Graph do określenia grup użytkownika ( `https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects` ). |
| `groups:src1` | Obiekt JSON | W przypadku żądań tokenów, które nie mają ograniczonej długości (patrz `hasgroups` powyżej), ale wciąż za duże dla tokenu, zostanie uwzględniony link do listy pełnych grup dla użytkownika. W przypadku JWTs jako roszczeń rozproszonych, w przypadku protokołu SAML jako nowego odszkodowania zamiast `groups` zgłoszenia. <br><br>**Przykładowa wartość JWT**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | Ciąg | Podmiot zabezpieczeń, dla którego token potwierdza informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani ponownie użyć. Może służyć do bezpiecznego sprawdzania autoryzacji, na przykład gdy token jest używany w celu uzyskania dostępu do zasobu i może być używany jako klucz w tabelach bazy danych. Ponieważ podmiot jest zawsze obecny w tokenach, które są problemy z usługą Azure AD, zalecamy użycie tej wartości w systemie autoryzacji ogólnego przeznaczenia. Podmiot jest jednak identyfikatorem parowania — jest unikatowy dla określonego identyfikatora aplikacji. W związku z tym, jeśli pojedynczy użytkownik zaloguje się do dwóch różnych aplikacji przy użyciu dwóch różnych identyfikatorów klienta, te aplikacje otrzymają dwie różne wartości dla zgłoszenia podmiotu. Może to być niepotrzebne, w zależności od wymagań dotyczących architektury i ochrony prywatności. Zobacz też, jak również to jest `oid` takie samo, jak w przypadku wszystkich aplikacji w ramach dzierżawy. |
| `oid` | Ciąg, identyfikator GUID | Niezmienny identyfikator dla "podmiotu" żądania — nazwy głównej użytkownika lub usługi, którego tożsamość została zweryfikowana.  W tokenach identyfikatorów i tokenach użytkownika i aplikacji, jest to identyfikator obiektu użytkownika.  W tokenach tylko do aplikacji jest to identyfikator obiektu jednostki usługi wywołującej. Może również służyć do bezpiecznego sprawdzania autoryzacji i jako klucz w tabelach bazy danych. Ten identyfikator jednoznacznie identyfikuje podmiot zabezpieczeń w aplikacjach — dwie różne aplikacje, które logują się w tym samym użytkowniku, otrzymają taką samą wartość w ramach `oid` roszczeń. W związku z tym, `oid` mogą być używane podczas wykonywania zapytań do usługi online firmy Microsoft, takich jak Microsoft Graph. Microsoft Graph zwróci ten identyfikator jako `id` Właściwość dla danego [konta użytkownika](/graph/api/resources/user). Ponieważ `oid` umożliwia wielu aplikacjom skorelowanie podmiotów zabezpieczeń, `profile` zakres jest wymagany w celu uzyskania tego żądania dla użytkowników. Należy pamiętać, że jeśli pojedynczy użytkownik istnieje w wielu dzierżawach, użytkownik będzie zawierać inny identyfikator obiektu w każdej dzierżawie — jest uznawany za różne konta, nawet jeśli użytkownik loguje się do każdego konta z tymi samymi poświadczeniami. |
| `tid` | Ciąg, identyfikator GUID | Reprezentuje dzierżawę usługi Azure AD, z której korzysta użytkownik. W przypadku kont służbowych identyfikator GUID jest niezmiennym IDENTYFIKATORem dzierżawy organizacji, do której należy użytkownik. W przypadku kont osobistych wartość to `9188040d-6c67-4c5b-b112-36a304b66dad` . `profile`Zakres jest wymagany w celu otrzymania tego żądania. |
| `unique_name` | Ciąg | Występuje tylko w tokenach v 1.0. Udostępnia zrozumiałą wartość identyfikującą podmiot tokenu. Ta wartość nie powinna być unikatowa w ramach dzierżawy i powinna być używana tylko do wyświetlania. |
| `uti` | Ciąg nieprzezroczysty | Wyjątek wewnętrzny używany przez platformę Azure do weryfikacji tokenów. Zasoby nie powinny korzystać z tego żądania. |
| `rh` | Ciąg nieprzezroczysty | Wyjątek wewnętrzny używany przez platformę Azure do weryfikacji tokenów. Zasoby nie powinny używać tego żądania. |
| `ver` | Ciąg znaków `1.0` lub `2.0` | Wskazuje wersję tokenu dostępu. |

**Zgłoszenie nadwyżkowe grup**

Aby mieć pewność, że rozmiar tokenu nie przekracza limitów rozmiaru nagłówka HTTP, usługa Azure AD ogranicza liczbę identyfikatorów obiektów uwzględnionych w ramach żądania grup. Jeśli użytkownik jest członkiem większej liczby grup niż limit nadwyżkowy (150 dla tokenów SAML, 200 dla tokenów JWT i tylko 6, jeśli został wystawiony za pośrednictwem niejawnego przepływu), usługa Azure AD nie emituje roszczeń grupowych w tokenie. Zamiast tego zawiera w tokenie wystąpienie nadwyżkowe, które wskazuje aplikacji, w której ma być wysyłana kwerenda Microsoft Graph interfejsu API w celu pobrania członkostwa w grupie użytkownika.

```JSON
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
}
```

Możesz użyć `BulkCreateGroups.ps1` podanego w folderze [Skrypty tworzenia aplikacji](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts) , aby pomóc w testowaniu scenariuszy.

#### <a name="v10-basic-claims"></a>podstawowe oświadczenia 1.0

Następujące oświadczenia zostaną uwzględnione w tokenach w wersji 1.0, jeśli ma zastosowanie, ale nie są domyślnie uwzględniane w tokenach programu v 2.0. Jeśli używasz programu v 2.0 i potrzebujesz jednego z tych oświadczeń, zażądaj ich przy użyciu [opcjonalnych oświadczeń](active-directory-optional-claims.md).

| Claim | Format | Opis |
|-----|--------|-------------|
| `ipaddr`| Ciąg | Adres IP, z którego użytkownik został uwierzytelniony. |
| `onprem_sid`| Ciąg w [formacie identyfikatora SID](/windows/desktop/SecAuthZ/sid-components) | W przypadkach, w których użytkownik ma uwierzytelnianie lokalne, to zgłoszenie zapewnia swój identyfikator SID. Programu można użyć `onprem_sid` do autoryzacji w starszych aplikacjach.|
| `pwd_exp`| int, sygnatura czasowa systemu UNIX | Wskazuje, kiedy wygasa hasło użytkownika. |
| `pwd_url`| Ciąg | Adres URL, pod którym można wysyłać użytkowników w celu zresetowania hasła. |
| `in_corp`| boolean | Sygnalizuje, czy klient loguje się z sieci firmowej. Jeśli nie, oświadczenia nie są uwzględniane. |
| `nickname`| Ciąg | Dodatkowa nazwa użytkownika, oddzielona od imię lub nazwisko.|
| `family_name` | Ciąg | Zawiera nazwisko, nazwisko lub nazwę rodziny użytkownika, zgodnie z definicją w obiekcie użytkownika. |
| `given_name` | Ciąg | Udostępnia imię i nazwisko użytkownika, zgodnie z ustawieniem obiektu użytkownika. |
| `upn` | Ciąg | Nazwa użytkownika. Może to być numer telefonu, adres e-mail lub niesformatowany ciąg. Powinna być używana tylko do celów wyświetlania i dostarczająca wskazówki dotyczące nazwy użytkownika w scenariuszach ponownego uwierzytelniania. |

#### <a name="the-amr-claim"></a>Zgłoszenie `amr`

Tożsamości firmy Microsoft mogą być uwierzytelniane na różne sposoby, które mogą być odpowiednie dla Twojej aplikacji. To `amr` jest tablica, która może zawierać wiele elementów, takich jak `["mfa", "rsa", "pwd"]` , do uwierzytelniania, które używały zarówno hasła, jak i aplikacji uwierzytelniania.

| Wartość | Opis |
|-----|-------------|
| `pwd` | Uwierzytelnianie hasła albo hasło użytkownika firmy Microsoft lub klucz tajny klienta aplikacji. |
| `rsa` | Uwierzytelnianie było oparte na weryfikacji klucza RSA, na przykład z [aplikacją Microsoft Authenticator](https://aka.ms/AA2kvvu). Obejmuje to, czy uwierzytelnianie zostało wykonane przy użyciu certyfikatu JWT z podpisem własnym z certyfikatem x509 usługi. |
| `otp` | Jednorazowy kod dostępu przy użyciu wiadomości e-mail lub wiadomości tekstowej. |
| `fed` | Zostało użyte potwierdzenie uwierzytelniania federacyjnego (takie jak JWT lub SAML). |
| `wia` | Zintegrowane uwierzytelnianie systemu Windows |
| `mfa` | Użyto [uwierzytelniania wieloskładnikowego](../authentication/concept-mfa-howitworks.md) . Gdy jest obecny, zostaną uwzględnione również inne metody uwierzytelniania. |
| `ngcmfa` | Równoważne z `mfa` , używane do inicjowania obsługi niektórych zaawansowanych typów poświadczeń. |
| `wiaormfa`| Użytkownik użył systemu Windows lub poświadczenia usługi MFA do uwierzytelnienia. |
| `none` | Nie zostało wykonane żadne uwierzytelnianie. |

## <a name="validating-tokens"></a>Sprawdzanie poprawności tokenów

Nie wszystkie aplikacje powinny weryfikować tokeny. Tylko w przypadku określonych scenariuszy aplikacje weryfikują token:

* [Interfejsy API sieci Web](quickstart-configure-app-expose-web-apis.md) muszą weryfikować tokeny dostępu wysyłane do nich przez klienta.  Muszą akceptować tylko tokeny zawierające ich `aud` wnioski.
* Poufne aplikacje sieci Web, takie jak ASP.NET Core, muszą sprawdzać poprawność identyfikatorów wysyłanych do nich za pośrednictwem przeglądarki użytkownika w przepływie hybrydowym, przed zezwoleniem na dostęp do danych użytkownika lub ustanawianie sesji.

Jeśli żaden z powyższych scenariuszy nie zostanie zastosowany, aplikacja nie będzie korzystać z weryfikacji tokenu i może stanowić zagrożenie bezpieczeństwa i niezawodności, jeśli decyzje są podejmowane na podstawie ważności tokenu.  Klienci publiczni, takie jak aplikacje natywne lub aplikacji jednostronicowych, nie korzystają z walidacji tokenów — aplikacja komunikuje się bezpośrednio z dostawcy tożsamości, więc ochrona SSL gwarantuje, że tokeny są prawidłowe.

Interfejsy API i aplikacje sieci Web muszą sprawdzać tylko tokeny, które są `aud` zgodne z ich aplikacjami. inne zasoby mogą mieć niestandardowe reguły walidacji tokenów. Na przykład tokeny dla Microsoft Graph nie będą sprawdzane zgodnie z tymi regułami z powodu ich zastrzeżonego formatu. Sprawdzanie poprawności i akceptowanie tokenów przeznaczonych dla innego zasobu to przykład nieznanego [problemu.](https://cwe.mitre.org/data/definitions/441.html)

Jeśli aplikacja wymaga weryfikacji id_token lub access_token zgodnie z powyższym, aplikacja powinna najpierw zweryfikować sygnaturę i wystawcę tokenu względem wartości w dokumencie odnajdywania OpenID Connect. Na przykład wersja dokumentu niezależna od dzierżawy znajduje się w lokalizacji [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration) .

Następujące informacje są dostarczane dla osób, które chcą zrozumieć proces podstawowy. Oprogramowanie pośredniczące usługi Azure AD ma wbudowane funkcje do sprawdzania poprawności tokenów dostępu. możesz przeglądać nasze [przykłady](sample-v2-code.md) , aby znaleźć je w wybranym języku. Istnieje również kilka bibliotek Open-Source innych firm dostępnych do sprawdzania poprawności tokenu JWT — istnieje co najmniej jedna opcja dla niemal każdej platformy i języka. Aby uzyskać więcej informacji na temat bibliotek uwierzytelniania i przykładów kodu usługi Azure AD, zobacz [biblioteki uwierzytelniania](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Weryfikowanie podpisu

Token JWT zawiera trzy segmenty, które są oddzielane `.` znakiem. Pierwszy segment jest znany jako **nagłówek**, drugi jako **treść**, a trzeci jako **sygnatura**. Segment podpisu może służyć do weryfikowania autentyczności tokenu, aby mógł być zaufany przez aplikację.

Tokeny wystawione przez usługę Azure AD są podpisywane przy użyciu standardowych algorytmów szyfrowania asymetrycznego, takich jak RS256. Nagłówek JWT zawiera informacje na temat metody klucza i szyfrowania użytej do podpisania tokenu:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

To `alg` zastrzeżenie wskazuje algorytm, który został użyty do podpisania tokenu, podczas gdy to zgłoszenie `kid` wskazuje określony klucz publiczny, który został użyty do zweryfikowania tokenu.

W dowolnym momencie usługa Azure AD może podpisać id_token przy użyciu dowolnego zestawu par kluczy publiczny-prywatny. Usługa Azure AD umożliwia okresowe obracanie możliwego zestawu kluczy, dlatego należy napisać aplikację w celu automatycznego obsłużenia tych zmian. Rozsądna częstotliwość sprawdzania dostępności aktualizacji kluczy publicznych używanych przez usługę Azure AD wynosi co 24 godziny.

Możesz uzyskać dane klucza podpisywania niezbędne do zweryfikowania podpisu za pomocą [dokumentu OpenID Connect Connect Metadata](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) w lokalizacji:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Wypróbuj ten [adres URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) w przeglądarce.

Ten dokument metadanych:

* Jest obiektem JSON zawierającym kilka przydatnych informacji, takich jak lokalizacja różnych punktów końcowych wymaganych do wykonania uwierzytelniania OpenID Connect Connect.
* Zawiera element `jwks_uri` , który zawiera lokalizację zestawu kluczy publicznych używanych do podpisywania tokenów. Klucz internetowy JSON (JWK) znajdujący się na `jwks_uri` liście zawiera wszystkie informacje o kluczu publicznym używany w tym konkretnym momencie.  Format JWK został opisany w [dokumencie RFC 7517](https://tools.ietf.org/html/rfc7517).  Twoja aplikacja może użyć `kid` w nagłówku JWT żądania, aby wybrać klucz publiczny w tym dokumencie, który został użyty do podpisania określonego tokenu. Następnie można sprawdzić poprawność podpisu przy użyciu prawidłowego klucza publicznego i wskazanego algorytmu.

> [!NOTE]
> Zalecamy użycie tego `kid` żądania do zweryfikowania tokenu. Chociaż tokeny w wersji 1.0 zawierają `x5t` zarówno `kid` oświadczenia, jak i, tokeny v 2.0 zawierają tylko `kid` oświadczenie.

Sprawdzanie poprawności podpisu jest poza zakresem tego dokumentu — istnieje wiele bibliotek typu "open source", które ułatwiają wykonywanie takich czynności.  Jednak platforma tożsamości firmy Microsoft ma jedno rozszerzenie podpisywania tokenu do standardów — niestandardowe klucze podpisywania.

Jeśli aplikacja ma niestandardowe klucze podpisywania w wyniku użycia funkcji [mapowania oświadczeń](active-directory-claims-mapping.md) , należy dołączyć `appid` parametr zapytania zawierający identyfikator aplikacji, aby uzyskać `jwks_uri` wskazanie informacji o kluczu podpisywania aplikacji, które powinny być używane do walidacji. Na przykład: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` zawiera `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` .

### <a name="claims-based-authorization"></a>Autoryzacja oparta na oświadczeniach

W ramach logiki biznesowej aplikacji zostanie wyznaczony ten krok, niektóre typowe metody autoryzacji są opisane poniżej.

* Sprawdź `scp` lub `roles` Przejmij, aby sprawdzić, czy wszystkie obecne zakresy są zgodne z tymi, które są udostępniane przez interfejs API, i zezwól klientowi na wykonywanie żądanych akcji.
* Upewnij się, że klient wywołujący może wywołać interfejs API przy użyciu tego `appid` żądania.
* Sprawdź poprawność stanu uwierzytelniania klienta wywołującego, `appidacr` ale nie powinna być równa 0, jeśli klienci publiczni nie mogą wywoływać interfejsu API.
* Sprawdź listę wcześniejszych `nonce` oświadczeń, aby sprawdzić, czy token nie jest odtwarzany.
* Sprawdź, czy jest `tid` zgodny z dzierżawcą, który może wywołać interfejs API.
* Użyj tego `amr` żądania, aby sprawdzić, czy użytkownik wykonał uwierzytelnianie wieloskładnikowe. Należy to wymusić przy użyciu [dostępu warunkowego](../conditional-access/overview.md).
* Jeśli zażądano `roles` oświadczeń lub `groups` oświadczenia w tokenie dostępu, należy sprawdzić, czy użytkownik znajduje się w grupie, która może wykonać tę akcję.
  * W przypadku tokenów pobranych przy użyciu niejawnego przepływu prawdopodobnie trzeba będzie wykonać zapytanie dotyczące [Microsoft Graph](https://developer.microsoft.com/graph/) dla tych danych, ponieważ jest ono często zbyt duże, aby zmieścić je w tokenie.

## <a name="user-and-application-tokens"></a>Tokeny użytkownika i aplikacji

Aplikacja może otrzymywać tokeny dla użytkownika (zazwyczaj omówionego przez przepływ) lub bezpośrednio z aplikacji (za pośrednictwem [przepływu poświadczeń klienta](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)). Te tokeny obsługujące tylko aplikacje wskazują, że to wywołanie pochodzi z aplikacji i nie ma do niego kopii zapasowej. Te tokeny są obsługiwane w dużym stopniu:

* Użyj, `roles` Aby wyświetlić uprawnienia, które zostały przyznane podmiotowi tokenu.
* Użyj `oid` lub `sub` , aby sprawdzić, czy nazwa główna usługi wywołującej jest oczekiwana.

Jeśli aplikacja musi rozróżnić tokeny dostępu tylko do aplikacji i tokeny dostępu dla użytkowników, należy użyć `idtyp` [opcjonalnego żądania](active-directory-optional-claims.md).  Dodając `idtyp` do `accessToken` pola i sprawdzając wartość `app` , można wykryć tokeny dostępu tylko do aplikacji.  Tokeny identyfikatorów i tokeny dostępu dla użytkowników nie mają `idtyp` uwzględnionych roszczeń.

## <a name="token-revocation"></a>Odwołanie do tokenu

Tokeny odświeżania można unieważniać lub odwołać w dowolnym momencie, z różnych powodów. Są one podzielone na dwie główne kategorie: przekroczenia limitu czasu i odwołania.

### <a name="token-timeouts"></a>Limity czasu tokenu

Przy użyciu [konfiguracji okresu istnienia tokenu](active-directory-configurable-token-lifetimes.md)można zmienić okres istnienia tokenów odświeżania.  Jest to normalne i oczekiwane w przypadku niektórych tokenów bez użycia (np. użytkownik nie otwiera aplikacji przez 3 miesiące) i w związku z tym wygasa.  Aplikacje będą napotykać scenariusze, w których serwer logowania odrzuca token odświeżania z powodu jego wieku.

* MaxInactiveTime: Jeśli token odświeżania nie został użyty w czasie określonym przez MaxInactiveTime, token odświeżania nie będzie już prawidłowy.
* MaxSessionAge: Jeśli MaxAgeSessionMultiFactor lub MaxAgeSessionSingleFactor została ustawiona na inną niż domyślna (do odwołania), wówczas uwierzytelnianie będzie wymagane po upływie czasu określonego w MaxAgeSession *.
* Przykłady:
  * Dzierżawa ma MaxInactiveTime przez pięć dni, a użytkownik wyszedł urlop przez tydzień, a więc usługa Azure AD nie widziała nowego żądania tokenu od użytkownika w ciągu 7 dni. Gdy następnym razem użytkownik zażąda nowego tokenu, zobaczy token odświeżenia, który został odwołany, i musi wprowadzić ponownie swoje poświadczenia.
  * Poufne aplikacje mają MaxAgeSessionSingleFactor jeden dzień. Jeśli użytkownik zaloguje się w poniedziałek i we wtorek (po 25 godzinach upłynął), będzie wymagane do ponownego uwierzytelnienia.

### <a name="revocation"></a>Unieważni

Tokeny odświeżania mogą zostać odwołane przez serwer z powodu zmiany poświadczeń lub akcji administratora.  Tokeny odświeżania dzielą się na dwie klasy — te wystawione dla klientów poufnych (kolumna z prawej stronie) i wystawione dla klientów publicznych (wszystkie inne kolumny).

| Zmiana | Plik cookie oparty na hasłach | Token oparty na hasłach | Plik cookie bez hasła | Token nieoparty na haśle | Poufny token klienta |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Hasło wygasa | Pozostaje aktywna | Pozostaje aktywna | Pozostaje aktywna | Pozostaje aktywna | Pozostaje aktywna |
| Hasło zostało zmienione przez użytkownika | Revoked | Revoked | Pozostaje aktywna | Pozostaje aktywna | Pozostaje aktywna |
| Użytkownik wykonuje SSPR | Revoked | Revoked | Pozostaje aktywna | Pozostaje aktywna | Pozostaje aktywna |
| Administrator resetuje hasło | Revoked | Revoked | Pozostaje aktywna | Pozostaje aktywna | Pozostaje aktywna |
| Użytkownik odwołuje tokeny odświeżania [za pośrednictwem programu PowerShell](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Revoked | Revoked | Revoked | Revoked | Revoked |
| Administrator odwołuje wszystkie tokeny odświeżania dla użytkownika [za pośrednictwem programu PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Revoked | Revoked |Revoked | Revoked | Revoked |
| Logowanie jednokrotne ([v 1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out), [v 2.0](v2-protocols-oidc.md#single-sign-out) ) w sieci Web | Revoked | Pozostaje aktywna | Revoked | Pozostaje aktywna | Pozostaje aktywna |

#### <a name="non-password-based"></a>Bez hasła

Logowanie *nieoparte na hasłach* to takie, w przypadku których użytkownik nie mógł wpisać hasła, aby go pobrać. Przykłady logowania nieoparte na hasłach obejmują:

- Korzystanie z funkcji Windows Hello na platformie
- Klucz FIDO2
- SMS
- Połączenia głosowe
- Kod PIN

Sprawdź [podstawowe tokeny odświeżania](../devices/concept-primary-refresh-token.md) , aby uzyskać więcej informacji na temat podstawowych tokenów odświeżania.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [ `id_tokens` usłudze Azure AD](id-tokens.md).
* Dowiedz się więcej o uprawnieniach i zgodzie ( [v 1.0](../azuread-dev/v1-permissions-consent.md), [v 2.0](v2-permissions-and-consent.md)).
