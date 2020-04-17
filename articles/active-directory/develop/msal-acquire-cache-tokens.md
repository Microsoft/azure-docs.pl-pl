---
title: Nabywanie tokenów & pamięci podręcznej za pomocą msal | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o pobieraniu i buforowaniu tokenów przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 647dff9e6401322371ef795a25ca5ced2b517e9c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534588"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Pozyskiwanie i buforowanie tokenów przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL)

[Tokeny dostępu](access-tokens.md) umożliwiają klientom bezpieczne wywoływanie internetowych interfejsów API chronionych przez platformę Azure. Istnieje wiele sposobów uzyskania tokenu przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL). Niektóre sposoby wymagają interakcji użytkownika za pośrednictwem przeglądarki internetowej. Niektóre z nich nie wymagają żadnych interakcji z użytkownikiem. Ogólnie rzecz biorąc sposób uzyskania tokenu zależy od tego, czy aplikacja jest publiczna aplikacja kliencka (aplikacja kliencka lub przenośna) lub poufne aplikacji klienckiej (aplikacji sieci web, interfejsu API sieci web lub aplikacji demona, takich jak usługa systemu Windows).

MSAL buforuje token po jego nabyciu.  Kod aplikacji należy spróbować uzyskać token dyskretnie (z pamięci podręcznej), najpierw przed uzyskaniem tokenu za pomocą innych środków.

Można również wyczyścić pamięć podręczną tokenu, który jest osiągany przez usunięcie kont z pamięci podręcznej. Nie powoduje to jednak usunięcia pliku cookie sesji, który znajduje się w przeglądarce.

## <a name="scopes-when-acquiring-tokens"></a>Zakresy podczas pozyskiwania tokenów

[Zakresy](v2-permissions-and-consent.md) są uprawnienia, które udostępnia internetowy interfejs API dla aplikacji klienckich, aby zażądać dostępu do. Aplikacje klienckie żądają zgody użytkownika dla tych zakresów podczas składania żądań uwierzytelniania, aby uzyskać tokeny dostępu do internetowych interfejsów API. Narzędzie MSAL umożliwia uzyskanie tokenów dostępu do usługi Azure AD dla deweloperów (w wersji 1.0) i interfejsów API platformy tożsamości firmy Microsoft (w wersji 2.0). Protokół w wersji 2.0 używa zakresów zamiast zasobów w żądaniach. Aby uzyskać więcej informacji, przeczytaj [porównanie wersji 1.0 i v2.0](active-directory-v2-compare.md). Na podstawie konfiguracji interfejsu API sieci web wersji tokenu akceptuje, punkt końcowy w wersji 2.0 zwraca token dostępu do msal.

Wiele metod msal nabycia tokenu wymagają parametru *zakresów.* Ten parametr jest prostą listą ciągów, które deklarują żądane uprawnienia i zasoby, które są wymagane. Dobrze znane zakresy to [uprawnienia programu Microsoft Graph](/graph/permissions-reference).

Jest również możliwe w msal dostęp do zasobów w wersji 1.0. Aby uzyskać więcej informacji, zobacz [zakresy aplikacji w wersji 1.0](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>Żądanie określonych zakresów dla internetowego interfejsu API

Gdy aplikacja musi zażądać tokenów z określonymi uprawnieniami dla interfejsu API zasobów, należy przekazać zakresy zawierające identyfikator URI identyfikatora aplikacji interfejsu API w poniższym formacie: * &lt;zakres identyfikatora&gt;/&lt;aplikacji URI&gt;*

Na przykład zakresy interfejsu API programu Microsoft Graph:`https://graph.microsoft.com/User.Read`

Lub, na przykład, zakresy niestandardowego interfejsu API sieci web:`api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

Tylko w przypadku interfejsu API programu `user.read` Microsoft `https://graph.microsoft.com/User.Read` Graph wartość zakresu jest mapowana do formatowania i może być używana zamiennie.

> [!NOTE]
> Niektóre interfejsy API sieci Web,https://management.core.windows.net/) takie jak interfejs API usługi Azure Resource Manager ( spodziewaj się końcowego "/" w oświadczeniu odbiorców (aud) tokenu dostępu. W takim przypadku ważne jest, aby https://management.core.windows.net//user_impersonation przekazać zakres jako (uwaga podwójne ukośnik), aby token był prawidłowy w interfejsie API.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Żądanie zakresów dynamicznych dla zgody przyrostowej

Podczas tworzenia aplikacji przy użyciu wersji 1.0, trzeba było zarejestrować pełny zestaw uprawnień (zakresy statyczne) wymagane przez aplikację dla użytkownika do zgody w momencie logowania. W wersji 2.0 można zażądać dodatkowych uprawnień, zgodnie z potrzebami przy użyciu parametru scope. Są one nazywane zakresami dynamicznymi i umożliwiają użytkownikowi udzielanie przyrostowej zgody na zakresy.

Na przykład można początkowo zalogować się do użytkownika i odmówić mu wszelkiego rodzaju dostępu. Później można dać im możliwość odczytu kalendarza użytkownika, żądając zakresu kalendarza w metody tokenu nabycia i uzyskać zgodę użytkownika.

Na przykład: `https://graph.microsoft.com/User.Read``https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Pobieranie tokenów po cichu (z pamięci podręcznej)

MSAL przechowuje pamięć podręczną tokenu (lub dwie pamięci podręczne dla poufnych aplikacji klienckich) i buforuje token po jego nabyciu.  W wielu przypadkach próba cichego uzyskania tokenu uzyska inny token z większą licznymi zakresami opartymi na tokenie w pamięci podręcznej. Jest również w stanie odświeżyć token, gdy zbliża się do wygaśnięcia (ponieważ pamięć podręczna tokenu zawiera również token odświeżania).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Zalecany wzorzec połączeń dla publicznych aplikacji klienckich

Kod aplikacji należy najpierw spróbować uzyskać token (z pamięci podręcznej).  Jeśli wywołanie metody zwraca błąd lub wyjątek "Wymagane interfejsu użytkownika", spróbuj pozyskać token za pomocą innych środków.

Istnieją jednak dwa przepływy, przed którymi **nie należy** podejmować prób dyskretnego nabycia tokenu:

- [przepływ poświadczeń klienta](msal-authentication-flows.md#client-credentials), który nie używa pamięci podręcznej tokenu użytkownika, ale pamięci podręcznej tokenu aplikacji. Ta metoda zajmuje się sprawdzanie tej pamięci podręcznej tokenu aplikacji przed wysłaniem żądania do STS.
- [przepływ kodu autoryzacji](msal-authentication-flows.md#authorization-code) w aplikacjach sieci web, ponieważ realizuje kod, który aplikacja uzyskała, logując się do użytkownika i wyrażając zgodę na więcej zakresów. Ponieważ kod jest przekazywany jako parametr, a nie konto, metoda nie może wyglądać w pamięci podręcznej przed realizacją kodu, który wymaga, mimo to, wywołania usługi.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Zalecany wzorzec połączeń w aplikacjach sieci Web przy użyciu przepływu kodu autoryzacji

W przypadku aplikacji sieci Web korzystających z [przepływu kodu autoryzacji OpenID Connect](v2-protocols-oidc.md)zalecany wzorzec w kontrolerach ma na celu:

- Tworzenie wystąpienia poufnej aplikacji klienckiej z pamięcią podręczną tokenu z niestandardową serializacją.
- Uzyskaj token przy użyciu przepływu kodu autoryzacji

## <a name="acquiring-tokens"></a>Pobieranie tokenów

Ogólnie rzecz biorąc metoda pozyskiwania tokenu zależy od tego, czy jest to klient publiczny lub poufne aplikacji klienta.

### <a name="public-client-applications"></a>Publiczne aplikacje klienckie

W przypadku publicznych aplikacji klienckich (na komputery lub aplikacje mobilne):
- Często nabywaj tokeny interaktywnie, mając użytkownika zalogować się za pośrednictwem interfejsu użytkownika lub okna podręcznego.
- Można [uzyskać token dyskretnie dla zalogowanego użytkownika](msal-authentication-flows.md#integrated-windows-authentication) przy użyciu zintegrowanego uwierzytelniania systemu Windows (IWA/Kerberos), jeśli aplikacja klasyczna jest uruchomiona na komputerze z systemem Windows przyłączony do domeny lub platformy Azure.
- Można [uzyskać token z nazwą użytkownika i hasłem](msal-authentication-flows.md#usernamepassword) w aplikacjach klienckich platformy .NET framework, ale nie jest to zalecane. Nie używaj nazwy użytkownika/hasła w poufnych aplikacjach klienckich.
- Można uzyskać token za pośrednictwem [przepływu kodu urządzenia](msal-authentication-flows.md#device-code) w aplikacjach uruchomionych na urządzeniach, które nie mają przeglądarki sieci web. Użytkownik otrzymuje adres URL i kod, który następnie przechodzi do przeglądarki internetowej na innym urządzeniu i wprowadza kod i loguje się.  Usługa Azure AD następnie wysyła token z powrotem do urządzenia bez przeglądarki.

### <a name="confidential-client-applications"></a>Poufne aplikacje klienckie

W przypadku poufnych aplikacji klienckich (aplikacji sieci Web, interfejsu API sieci Web lub aplikacji demona, takich jak usługa windowsowa), użytkownik:
- Uzyskaj tokeny **dla samej aplikacji,** a nie dla użytkownika, przy użyciu [przepływu poświadczeń klienta](msal-authentication-flows.md#client-credentials). Może to służyć do synchronizacji narzędzi lub narzędzi, które przetwarzają użytkowników w ogóle, a nie określonego użytkownika.
- Użyj [przepływu w imieniu](msal-authentication-flows.md#on-behalf-of) dla internetowego interfejsu API do wywoływania interfejsu API w imieniu użytkownika. Aplikacja jest identyfikowana za pomocą poświadczeń klienta w celu uzyskania tokenu na podstawie potwierdzenia użytkownika (saml na przykład lub token JWT). Ten przepływ jest używany przez aplikacje, które muszą uzyskać dostęp do zasobów określonego użytkownika w wywołaniach usługi do usługi.
- Uzyskaj tokeny przy użyciu [przepływu kodu autoryzacji](msal-authentication-flows.md#authorization-code) w aplikacjach sieci web po zalogowaniu się użytkownika za pośrednictwem adresu URL żądania autoryzacji. Aplikacja OpenID Connect zazwyczaj używa tego mechanizmu, który umożliwia użytkownikowi logowanie się przy użyciu open id connect, a następnie dostęp do internetowych interfejsów API w imieniu użytkownika.

## <a name="authentication-results"></a>Wyniki uwierzytelniania

Gdy klient żąda tokenu dostępu, usługa Azure AD zwraca również wynik uwierzytelniania, który zawiera niektóre metadane dotyczące tokenu dostępu. Informacje te obejmują czas wygaśnięcia tokenu dostępu i zakresy, dla których jest prawidłowy. Te dane umożliwia aplikacji do inteligentnego buforowania tokenów dostępu bez konieczności analizowania tokenu dostępu.  Wynik uwierzytelniania udostępnia:

- [Token dostępu](access-tokens.md) dla internetowego interfejsu API, aby uzyskać dostęp do zasobów. Jest to ciąg, zwykle base64 zakodowane JWT, ale klient nigdy nie należy szukać wewnątrz tokenu dostępu. Format nie jest gwarantowane, aby pozostać stabilny i może być zaszyfrowany dla zasobu. Osoby piszące kod w zależności od zawartości tokenu dostępu na kliencie jest jednym z największych źródeł błędów i podziałów logiki klienta.
- [Token identyfikatora](id-tokens.md) dla użytkownika (jest to JWT).
- Wygaśnięcie tokenu, który informuje o dacie/godzinie wygaśnięcia tokenu.
- Identyfikator dzierżawy zawiera dzierżawę, w której znaleziono użytkownika. Dla użytkowników-gości (scenariusze usługi Azure AD B2B) identyfikator dzierżawy jest dzierżawą gościa, a nie unikatową dzierżawą. Gdy token jest dostarczany w imieniu użytkownika, wynik uwierzytelniania zawiera również informacje o tym użytkowniku. W przypadku poufnych przepływów klienta, w których tokeny są wymagane bez użytkownika (dla aplikacji), te informacje o użytkowniku mają wartość null.
- Zakresy, dla których token został wystawiony.
- Unikatowy identyfikator użytkownika.

## <a name="next-steps"></a>Następne kroki

Jeśli używasz msal dla języka Java, dowiedz się więcej o [serializacji niestandardowej pamięci podręcznej tokenów w msal dla języka Java](msal-java-token-cache-serialization.md).

Dowiedz się więcej o [obchodzeniu się z błędami i wyjątkami](msal-handling-exceptions.md).
