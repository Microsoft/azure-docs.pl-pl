---
title: Punkt końcowy informacji o firmie Microsoft Identity platform Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o punkcie końcowym UserInfo na platformie tożsamości firmy Microsoft.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3bd0621d607b121f19bc47c717343b8e2e39b04f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98752736"
---
# <a name="microsoft-identity-platform-userinfo-endpoint"></a>Punkt końcowy informacji o firmie Microsoft Identity platform

Punkt końcowy UserInfo jest częścią [OpenID Connect Connect Standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC), która została zaprojektowana w celu zwracania oświadczeń dotyczących uwierzytelnionego użytkownika.  W przypadku platformy tożsamości firmy Microsoft punkt końcowy UserInfo jest hostowany na Microsoft Graph ( https://graph.microsoft.com/oidc/userinfo) . 

## <a name="find-the-well-known-configuration-endpoint"></a>Znajdź dobrze znaną nazwę punktu końcowego konfiguracji

Można programowo wykryć punkt końcowy UserInfo przy użyciu narzędzia OpenID Connect Connect Discovery w witrynie `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` . Jest on wymieniony w `userinfo_endpoint` polu, a ten wzorzec może być używany w różnych chmurach, aby wskazywał prawy punkt końcowy.  Nie zalecamy wstępnego kodowania punktu końcowego UserInfo w aplikacji — użyj dokumentu odnajdywania OIDC, aby znaleźć ten punkt końcowy w środowisku uruchomieniowym.

W ramach specyfikacji OpenID Connect Connect punkt końcowy UserInfo jest często automatycznie wywoływany przez [biblioteki zgodne z OIDC](https://openid.net/developers/certified/)  , aby uzyskać informacje o użytkowniku.  Bez obsługi takiego punktu końcowego platforma tożsamości firmy Microsoft nie będzie zgodna ze standardami, a niektóre biblioteki będą kończyć się niepowodzeniem.  Z [listy oświadczeń określonych w standardzie OIDC](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims) firma Microsoft tworzy oświadczenia nazw, oświadczenia podmiotu i wiadomości e-mail, jeśli są dostępne i są zgodne z.  

## <a name="consider-use-an-id-token-instead"></a>Rozważmy: zamiast tego użyj tokenu identyfikatora

Informacje dostępne w tokenie identyfikatora, które mogą zostać odebrane przez aplikację, to nadzbiór informacji, które może pobrać z punktu końcowego UserInfo.  Ponieważ można uzyskać token identyfikatora w tym samym czasie, uzyskując token do wywołania punktu końcowego UserInfo, sugerujemy użycie tego tokenu identyfikatora, aby uzyskać informacje o użytkowniku zamiast wywoływania punktu końcowego UserInfo.  Użycie tokenu identyfikatora spowoduje usunięcie jednego do dwóch żądań sieci z uruchamiania aplikacji, co zmniejsza opóźnienie w aplikacji.

Jeśli potrzebujesz więcej szczegółowych informacji o użytkowniku, należy wywołać [ `/user` interfejs API Microsoft Graph](/graph/api/user-get) , aby uzyskać informacje takie jak numer biura lub stanowisko.   Możesz również użyć [opcjonalnych oświadczeń](active-directory-optional-claims.md) , aby dołączyć dodatkowe informacje o użytkowniku do identyfikatorów i tokenów dostępu.

## <a name="calling-the-userinfo-endpoint"></a>Wywoływanie punktu końcowego UserInfo

UserInfo jest standardowym interfejsem API tokenu okaziciela OAuth nazywanym innym interfejsem API Microsoft Graph przy użyciu tokenu dostępu otrzymanego podczas uzyskiwania tokenu dla Microsoft Graph. Zwraca odpowiedź JSON zawierającą oświadczenia dotyczące użytkownika.

### <a name="permissions"></a>Uprawnienia

Aby wywołać interfejs API UserInfo, użyj następujących [uprawnień OIDC](v2-permissions-and-consent.md#openid-connect-scopes) . `openid` jest wymagana, a `profile` zakresy i `email` zapewniają, że dodatkowe informacje są podane w odpowiedzi.

|Typ uprawnienia      | Uprawnienia    |
|:--------------------|:---------------------------------------------------------|
|Delegowane (konto służbowe) | OpenID Connect (wymagane), profil, adres e-mail |
|Delegowane (osobiste konto Microsoft) | OpenID Connect (wymagane), profil, adres e-mail |
|Aplikacja | Nie dotyczy |

> [!TIP]
> Skopiuj ten adres URL w przeglądarce, aby uzyskać token dla punktu końcowego UserInfo, a także [token ID](id-tokens.md) i zastąpić identyfikator klienta oraz identyfikator URI przekierowania własnymi. Należy zauważyć, że tylko żąda zakresów dla zakresów OpenID Connect lub Graph i nic innego.  Jest to wymagane, ponieważ nie można zażądać uprawnień do dwóch różnych zasobów w ramach tego samego żądania tokenu.
>
> `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=<yourClientID>&response_type=token+id_token&redirect_uri=<YourRedirectUri>&scope=user.read+openid+profile+email&response_mode=fragment&state=12345&nonce=678910`
>
> Tego tokenu dostępu można użyć w następnej sekcji.

Podobnie jak w przypadku dowolnego innego tokenu Microsoft Graph, token, który otrzymujesz, nie może być JWT. Jeśli zalogowano Cię konto Microsoft użytkownika, będzie to format szyfrowanego tokenu. Dzieje się tak, ponieważ Microsoft Graph ma specjalny wzorzec wystawiania tokenów. Nie ma to wpływu na możliwość używania tokenu dostępu do wywoływania punktu końcowego UserInfo.

### <a name="calling-the-api"></a>Wywoływanie interfejsu API

Interfejs API UserInfo obsługuje zarówno funkcję GET, jak i POST dla specyfikacji OIDC.

```http
GET or POST /oidc/userinfo HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6Il…
```

### <a name="userinfo-response"></a>Odpowiedź na UserInfo

```jsonc
{
    "sub": "OLu859SGc2Sr9ZsqbkG-QbeLgJlb41KcdiPoLYNpSFA",
    "name": "Mikah Ollenburg", // names all require the “profile” scope.
    "family_name": " Ollenburg",
    "given_name": "Mikah",
    "email": "mikoll@contoso.com" //requires the “email” scope.
}
```

Oświadczenia wymienione w tym miejscu to wszystkie oświadczenia, które mogą zostać zwrócone przez punkt końcowy UserInfo.  Są to te same wartości, które aplikacja zobaczy w [tokenie ID](id-tokens.md) wystawionym dla aplikacji.  

## <a name="notes-and-caveats-on-the-userinfo-endpoint"></a>Uwagi i zastrzeżenia w punkcie końcowym UserInfo

* Jeśli chcesz wywołać ten punkt końcowy UserInfo, musisz użyć punktu końcowego v 2.0.  W przypadku korzystania z punktu końcowego programu v 1.0 otrzymasz token dla punktu końcowego informacji o użytkowniku w wersji 1.0, który jest hostowany w witrynie login.microsoftonline.com.  Zaleca się, aby wszystkie aplikacje i biblioteki zgodne z OIDCą korzystały z punktu końcowego v 2.0 w celu zapewnienia zgodności.
* Nie można dostosować odpowiedzi z punktu końcowego UserInfo.  Jeśli chcesz dostosować oświadczenia, użyj [mapowania oświadczeń]( active-directory-claims-mapping.md) , aby edytować informacje zwracane w tokenach.
* Nie można dodać odpowiedzi z punktu końcowego UserInfo do.  Jeśli chcesz uzyskać dodatkowe oświadczenia dotyczące użytkownika, użyj [opcjonalnych oświadczeń]( active-directory-optional-claims.md) , aby dodać nowe oświadczenia do tokenów.

## <a name="next-steps"></a>Następne kroki

* [Przejrzyj zawartość tokenów identyfikatora](id-tokens.md)
* [Dostosuj zawartość tokenu identyfikatora przy użyciu opcjonalnych oświadczeń](active-directory-optional-claims.md)
* [Żądanie tokenu dostępu i tokenu identyfikatora przy użyciu protokołu OAuth2](v2-protocols-oidc.md)
