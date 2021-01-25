---
title: Uzyskiwanie tokenów pamięci podręcznej & przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o uzyskiwaniu i buforowaniu tokenów przy użyciu MSAL.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 98ae81626db637f5b0bd6bfe9e294c32293d09e5
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755069"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Uzyskiwanie i buforowanie tokenów przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL)

[Tokeny dostępu](access-tokens.md) umożliwiają klientom bezpieczne wywoływanie interfejsów API sieci Web chronionych przez platformę Azure. Istnieje kilka sposobów uzyskania tokenu przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL). Niektóre wymagają interakcji z użytkownikiem za pomocą przeglądarki sieci Web, podczas gdy inne nie wymagają interakcji z użytkownikiem. Ogólnie rzecz biorąc, Metoda używana do pozyskiwania tokenu zależy od tego, czy aplikacja jest publiczną aplikacją kliencką, taką jak aplikacja klasyczna lub mobilna, czy poufną aplikacją kliencką, taką jak aplikacja sieci Web, internetowy interfejs API lub aplikacja demona.

MSAL buforuje token po jego uzyskaniu. Kod aplikacji powinien najpierw próbować uzyskać token dyskretnie z pamięci podręcznej przed podjęciem próby uzyskania tokenu przy użyciu innych metod.

Można również wyczyścić pamięć podręczną tokenów, która jest osiągana przez usunięcie kont z pamięci podręcznej. Nie spowoduje to jednak usunięcia pliku cookie sesji znajdującego się w przeglądarce.

## <a name="scopes-when-acquiring-tokens"></a>Zakresy podczas uzyskiwania tokenów

[Zakresy](v2-permissions-and-consent.md) są uprawnieniami, które udostępnia interfejs API sieci Web, że aplikacje klienckie mogą żądać dostępu do programu. Aplikacje klienckie żądają zgody użytkownika na te zakresy podczas wykonywania żądań uwierzytelniania w celu uzyskania dostępu do interfejsów API sieci Web. MSAL umożliwia uzyskanie tokenów dostępu do usługi Azure AD dla deweloperów (wersja 1.0) i interfejsów API platformy tożsamości firmy Microsoft. Protokół v 2.0 używa zakresów zamiast zasobów w żądaniach. Więcej informacji można znaleźć w artykule [porównanie wersji 1.0 i 2.0](../azuread-dev/azure-ad-endpoint-comparison.md). W oparciu o konfigurację internetowego interfejsu API akceptowaną przez tę wersję tokenu punkt końcowy v 2.0 zwraca token dostępu do MSAL.

Kilka metod pozyskiwania tokenów MSAL wymaga `scopes` parametru. `scopes`Parametr jest listą ciągów, które deklarują żądane uprawnienia i wymagane zasoby. Dobrze znane zakresy to [uprawnienia Microsoft Graph](/graph/permissions-reference).

Możliwe jest również, że w MSAL dostęp do zasobów w wersji 1.0. Aby uzyskać więcej informacji, zobacz [zakresy dla aplikacji w wersji 1.0](msal-v1-app-scopes.md).

### <a name="request-scopes-for-a-web-api"></a>Zakresy żądań dla internetowego interfejsu API

Gdy aplikacja musi zażądać tokenu dostępu z określonymi uprawnieniami dla interfejsu API zasobów, należy przekazać zakresy zawierające identyfikator URI aplikacji interfejsu API w formacie `<app ID URI>/<scope>` .

Niektóre przykładowe wartości zakresu dla różnych zasobów:

- Interfejs API Microsoft Graph: `https://graph.microsoft.com/User.Read`
- Niestandardowy interfejs API sieci Web: `api://11111111-1111-1111-1111-111111111111/api.read`

Format wartości zakresu różni się w zależności od zasobu (interfejsu API), który odbiera token dostępu i `aud` akceptowane przez niego wartości.

Tylko w przypadku Microsoft Graph `user.read` zakres jest mapowany do `https://graph.microsoft.com/User.Read` , a oba formaty zakresu mogą być używane zamiennie.

Niektóre interfejsy API sieci Web, takie jak Azure Resource Manager API ( https://management.core.windows.net/) oczekiwano końcowego ukośnika ("/") w ramach żądania odbiorców ( `aud` ) tokenu dostępu. W takim przypadku należy przekazać zakres jako `https://management.core.windows.net//user_impersonation` , łącznie z podwójnym ukośnikiem w przód ("//").

Inne interfejsy API mogą wymagać *braku schematu lub hosta* w wartości zakresu i oczekiwać tylko identyfikatora aplikacji (GUID) i nazwy zakresu, na przykład:

`11111111-1111-1111-1111-111111111111/api.read`

> [!TIP]
> Jeśli zasób podrzędny nie jest objęty kontrolą, może być konieczne wypróbowanie różnych formatów wartości zakresu (na przykład z schematem/bez użycia schematu i hosta) w przypadku otrzymania `401` lub innych błędów podczas przekazywania tokenu dostępu do zasobu.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Żądaj zakresów dynamicznych na potrzeby przyrostowej zgody

W miarę zmiany funkcji udostępnianych przez aplikację lub jej wymagania można zażądać dodatkowych uprawnień zgodnie z potrzebami, używając parametru scope. Takie *zakresy dynamiczne* umożliwiają użytkownikom zapewnienie przyrostowej zgody na zakresy.

Na przykład użytkownik może zalogować się, ale początkowo odmówić dostępu do żadnych zasobów. Później można dać im możliwość wyświetlania swoich kalendarzy, żądając zakresu kalendarza w metodzie pozyskiwania tokenu i uzyskiwania zgody użytkownika na to. Na przykład żądając `https://graph.microsoft.com/User.Read` `https://graph.microsoft.com/Calendar.Read` zakresów i.

## <a name="acquiring-tokens-silently-from-the-cache"></a>Uzyskiwanie tokenów dyskretnie (z pamięci podręcznej)

MSAL przechowuje pamięć podręczną tokenów (lub dwie pamięci podręczne dla poufnych aplikacji klienckich) i buforuje token po jego uzyskaniu. W wielu przypadkach próba dyskretnego pobrania tokenu spowoduje uzyskanie innego tokenu z większą liczbą zakresów na podstawie tokenu w pamięci podręcznej. Istnieje również możliwość odświeżenia tokenu, gdy zbliża się do wygaśnięcia (ponieważ pamięć podręczna tokenów zawiera również token odświeżania).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Zalecany wzorzec wywołań dla publicznych aplikacji klienckich

Kod aplikacji powinien najpierw próbować uzyskać token dyskretnie z pamięci podręcznej. Jeśli wywołanie metody zwraca błąd lub wyjątek "wymagany interfejs użytkownika", spróbuj uzyskać token w inny sposób.

Istnieją jednak dwa przepływy, w których **nie należy** próbować dyskretnie uzyskać tokenu:

- [Przepływ poświadczeń klienta](msal-authentication-flows.md#client-credentials), który nie korzysta z pamięci podręcznej tokenów użytkownika, ale pamięci podręcznej tokenów aplikacji. Ta metoda wymaga weryfikacji pamięci podręcznej tokenów aplikacji przed wysłaniem żądania do usługi tokenu zabezpieczającego (STS).
- [Przepływ kodu autoryzacji](msal-authentication-flows.md#authorization-code) w aplikacjach sieci Web, ponieważ realizuje kod, który aplikacja uzyskała podczas podpisywania użytkownika i wyraża zgodę na więcej zakresów. Ponieważ kod i niekonto są przesyłane jako parametr, metoda nie może wyszukać w pamięci podręcznej przed zrealizowaniem kodu, co wywołuje wywołanie usługi.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Zalecany wzorzec wywołań w aplikacjach sieci Web przy użyciu przepływu kodu autoryzacji

W przypadku aplikacji sieci Web, które używają [przepływu kodu autoryzacji OpenID Connect Connect](v2-protocols-oidc.md), zalecanym wzorcem w kontrolerach jest:

- Utwórz wystąpienie poufnej aplikacji klienckiej z pamięcią podręczną tokenów z dostosowaną serializacji.
- Uzyskiwanie tokenu przy użyciu przepływu kodu autoryzacji

## <a name="acquiring-tokens"></a>Uzyskiwanie tokenów

Ogólnie rzecz biorąc, metoda uzyskiwania tokenu zależy od tego, czy jest to klient publiczny, czy poufna aplikacja kliencka.

### <a name="public-client-applications"></a>Publiczne aplikacje klienckie

W przypadku publicznych aplikacji klienckich (aplikacji klasycznej lub mobilnej):

- Często uzyskuje tokeny interaktywnie, gdy użytkownik loguje się za pomocą interfejsu użytkownika lub okna podręcznego.
- Program może [uzyskać token dyskretny dla zalogowanego użytkownika](msal-authentication-flows.md#integrated-windows-authentication) przy użyciu zintegrowanego uwierzytelniania systemu Windows (IWA/Kerberos), jeśli aplikacja klasyczna jest uruchomiona na komputerze z systemem Windows przyłączonym do domeny lub na platformie Azure.
- [Program może uzyskać token z nazwą użytkownika i hasłem](msal-authentication-flows.md#usernamepassword) w aplikacjach klienckich programu .NET Framework Desktop (niezalecane). Nie używaj nazwy użytkownika/hasła w poufnych aplikacjach klienckich.
- Może uzyskać token za pomocą [przepływu kodu urządzenia](msal-authentication-flows.md#device-code) w aplikacjach działających na urządzeniach, które nie mają przeglądarki sieci Web. Użytkownik otrzymuje adres URL i kod, który następnie przechodzi do przeglądarki sieci Web na innym urządzeniu i wprowadza kod i loguje się. Usługa Azure AD następnie wysyła token z powrotem do urządzenia bez przeglądarki.

### <a name="confidential-client-applications"></a>Poufne aplikacje klienckie

W przypadku poufnych aplikacji klienckich (aplikacji sieci Web, internetowego interfejsu API lub aplikacji demona, takich jak usługa systemu Windows):

- Uzyskaj tokeny **dla samej aplikacji** , a nie dla użytkownika, przy użyciu [przepływu poświadczeń klienta](msal-authentication-flows.md#client-credentials). Ta technika może służyć do synchronizowania narzędzi lub narzędzi, które przetwarzają użytkowników ogólnie, a nie do określonego użytkownika.
- Użyj [przepływu w imieniu](msal-authentication-flows.md#on-behalf-of) interfejsu API sieci Web, aby wywołać interfejs API w imieniu użytkownika. Aplikacja jest identyfikowana przy użyciu poświadczeń klienta w celu uzyskania tokenu na podstawie potwierdzenia użytkownika (na przykład lub tokenu JWT). Ten przepływ jest używany przez aplikacje, które muszą uzyskać dostęp do zasobów określonego użytkownika w ramach wywołań między usługami.
- Uzyskuj tokeny przy użyciu [przepływu kodu autoryzacji](msal-authentication-flows.md#authorization-code) w usłudze Web Apps po zalogowaniu się użytkownika za pośrednictwem adresu URL żądania autoryzacji. Aplikacja OpenID Connect Connect zazwyczaj używa tego mechanizmu, który umożliwia użytkownikowi Logowanie przy użyciu funkcji Open ID Connect, a następnie dostęp do internetowych interfejsów API w imieniu użytkownika.

## <a name="authentication-results"></a>Wyniki uwierzytelniania

Gdy klient zażąda tokenu dostępu, usługa Azure AD zwraca również wynik uwierzytelniania obejmujący metadane dotyczące tokenu dostępu. Te informacje obejmują czas wygaśnięcia tokenu dostępu i zakresy, dla których jest on prawidłowy. Te dane umożliwiają aplikacji inteligentne buforowanie tokenów dostępu bez konieczności analizowania samego tokenu dostępu. Wynik uwierzytelniania ujawnia:

- [Token dostępu](access-tokens.md) dla internetowego interfejsu API do uzyskiwania dostępu do zasobów. Ten ciąg jest zazwyczaj szyfrowanym algorytmem Base64, ale klient nigdy nie powinien szukać w tokenie dostępu. Format nie gwarantuje, że jest stabilny i może być zaszyfrowany dla zasobu. Osoby piszące kod w zależności od zawartości tokenu dostępu na kliencie są jednym z najczęstszych źródeł błędów i zerwania logiki klienta.
- [Token identyfikatora](id-tokens.md) dla użytkownika (JWT).
- Wygaśnięcie tokenu, które informuje datę i godzinę wygaśnięcia tokenu.
- Identyfikator dzierżawy zawiera dzierżawcę, w którym znaleziono użytkownika. Dla użytkowników-Gości (scenariusze B2B usługi Azure AD) identyfikator dzierżawy jest dzierżawcą gościa, a nie z unikatowym dzierżawcą. Po dostarczeniu tokenu w nazwie użytkownika wynik uwierzytelniania zawiera również informacje o tym użytkowniku. W przypadku poufnych przepływów klientów, w których tokeny są żądane bez użytkownika (dla aplikacji), informacje o użytkowniku mają wartość null.
- Zakresy, dla których token został wystawiony.
- Unikatowy identyfikator użytkownika.

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>Doświadczonych Uzyskiwanie dostępu do buforowanych tokenów użytkownika w aplikacjach i usługach w tle

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>Następne kroki

Niektóre platformy obsługiwane przez MSAL mają dodatkowe informacje związane z pamięcią podręczną tokenu w dokumentacji biblioteki platformy. Na przykład:
- [Pobieranie tokenu z pamięci podręcznej tokenów przy użyciu MSAL.NET](msal-net-acquire-token-silently.md)
- [Logowanie jednokrotne za pomocą biblioteki MSAL.js](msal-js-sso.md)
- [Serializacja niestandardowego buforu tokenów w MSAL dla języka Python](msal-python-token-cache-serialization.md)
- [Serializacja niestandardowego buforu tokenów w MSAL dla języka Java](msal-java-token-cache-serialization.md)
