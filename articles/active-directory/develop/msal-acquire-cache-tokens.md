---
title: Uzyskiwanie tokenów pamięci podręcznej & za pomocą MSAL | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o uzyskiwaniu i buforowaniu tokenów przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL).
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534588"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Uzyskiwanie i buforowanie tokenów przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL)

[Tokeny dostępu](access-tokens.md) umożliwiają klientom bezpieczne wywoływanie interfejsów API sieci Web chronionych przez platformę Azure. Istnieje wiele sposobów uzyskania tokenu przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL). Niektóre sposoby wymagają interakcji z użytkownikami za pomocą przeglądarki sieci Web. Niektóre nie wymagają interakcji z użytkownikiem. Ogólnie rzecz biorąc, sposób uzyskania tokenu zależy od tego, czy aplikacja to publiczna aplikacja kliencka (aplikacja klasyczna lub mobilna), czy poufna aplikacja kliencka (aplikacja sieci Web, internetowy interfejs API lub aplikacja demona, taka jak usługa systemu Windows).

MSAL buforuje token po jego uzyskaniu.  Kod aplikacji powinien próbować uzyskać token dyskretnie (z pamięci podręcznej), najpierw przed uzyskaniem tokenu przy użyciu innych metod.

Można również wyczyścić pamięć podręczną tokenów, która jest osiągana przez usunięcie kont z pamięci podręcznej. Nie spowoduje to usunięcia pliku cookie sesji, który znajduje się w przeglądarce.

## <a name="scopes-when-acquiring-tokens"></a>Zakresy podczas uzyskiwania tokenów

[Zakresy](v2-permissions-and-consent.md) są uprawnieniami udostępnianymi przez interfejs API sieci Web dla aplikacji klienckich w celu żądania dostępu do programu. Aplikacje klienckie żądają zgody użytkownika na te zakresy podczas wykonywania żądań uwierzytelniania w celu uzyskania dostępu do interfejsów API sieci Web. MSAL umożliwia uzyskanie tokenów dostępu do usługi Azure AD dla deweloperów (v 1.0) i Microsoft Identity platform (v 2.0). Protokół v 2.0 używa zakresów zamiast zasobów w żądaniach. Więcej informacji można znaleźć w artykule [porównanie wersji 1.0 i 2.0](active-directory-v2-compare.md). W oparciu o konfigurację internetowego interfejsu API akceptowaną przez tę wersję tokenu punkt końcowy v 2.0 zwraca token dostępu do MSAL.

Szereg metod uzyskiwania tokenów MSAL wymaga parametru *Scopes* . Ten parametr jest prostą listą ciągów, które deklarują żądane uprawnienia i żądane zasoby. Dobrze znane zakresy są [uprawnieniami Microsoft Graph](/graph/permissions-reference).

Możliwe jest również, że w MSAL dostęp do zasobów w wersji 1.0. Aby uzyskać więcej informacji, Przeczytaj [zakresy dla aplikacji w wersji 1.0](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>Żądaj określonych zakresów dla internetowego interfejsu API

Gdy aplikacja musi zażądać tokenów z określonymi uprawnieniami dla interfejsu API zasobów, należy przekazać zakresy zawierające identyfikator URI aplikacji interfejsu API w następującym formacie: * &lt;&gt;/&lt;zakres identyfikatorów URI aplikacji&gt;*

Na przykład zakresy interfejsu API Microsoft Graph:`https://graph.microsoft.com/User.Read`

Lub na przykład zakresy niestandardowego interfejsu API sieci Web:`api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

W przypadku interfejsu API Microsoft Graph tylko wartości `user.read` zakresu są mapowane na `https://graph.microsoft.com/User.Read` format i mogą być używane zamiennie.

> [!NOTE]
> Niektóre interfejsy API sieci Web, takie jakhttps://management.core.windows.net/) Azure Resource Manager API (oczekiwano znaku końcowego "/" w ramach żądania odbiorców (AUD) tokenu dostępu. W takim przypadku ważne jest przekazanie zakresu jako https://management.core.windows.net//user_impersonation (należy pamiętać o podwójnym ukośniku), aby token był prawidłowy w interfejsie API.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Żądaj zakresów dynamicznych na potrzeby przyrostowej zgody

Podczas kompilowania aplikacji przy użyciu wersji 1.0, należy zarejestrować pełen zestaw uprawnień (zakresy statyczne) wymagane przez aplikację, aby użytkownik mógł wyrazić zgodę w czasie logowania. W wersji 2.0 można zażądać dodatkowych uprawnień zgodnie z wymaganiami przy użyciu parametru scope. Są one nazywane zakresami dynamicznymi i umożliwiają użytkownikowi zapewnienie przyrostowej zgody na zakresy.

Na przykład możesz początkowo zalogować użytkownika i odmówić im dowolnego rodzaju dostępu. Później można dać im możliwość odczytywania kalendarza użytkownika przez zażądanie zakresu kalendarza w metodach uzyskiwania tokenu i uzyskanie zgody użytkownika.

Na przykład: `https://graph.microsoft.com/User.Read` i`https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Uzyskiwanie tokenów dyskretnie (z pamięci podręcznej)

MSAL przechowuje pamięć podręczną tokenów (lub dwie pamięci podręczne dla poufnych aplikacji klienckich) i buforuje token po jego uzyskaniu.  W wielu przypadkach próba dyskretnego pobrania tokenu spowoduje uzyskanie innego tokenu z większą liczbą zakresów na podstawie tokenu w pamięci podręcznej. Istnieje również możliwość odświeżenia tokenu, gdy zbliża się do wygaśnięcia (ponieważ pamięć podręczna tokenów zawiera również token odświeżania).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Zalecany wzorzec wywołań dla publicznych aplikacji klienckich

Kod aplikacji powinien próbować uzyskać token dyskretnie (z pamięci podręcznej), najpierw.  Jeśli wywołanie metody zwraca błąd lub wyjątek "wymagany interfejs użytkownika", spróbuj uzyskać token w inny sposób.

Jednak istnieją dwa przepływy, przed którymi **nie należy** próbować dyskretnie uzyskać tokenu:

- [przepływ poświadczeń klienta](msal-authentication-flows.md#client-credentials), który nie korzysta z pamięci podręcznej tokenów użytkownika, ale pamięci podręcznej tokenu aplikacji. Ta metoda polega na sprawdzeniu poprawności tej pamięci podręcznej token aplikacji przed wysłaniem żądania do usługi STS.
- [przepływ kodu autoryzacji](msal-authentication-flows.md#authorization-code) w aplikacjach sieci Web, ponieważ realizuje kod, który aplikacja uzyskała, logując się do użytkownika i wyraża zgodę na więcej zakresów. Ponieważ kod jest przekazaniem jako parametr, a nie na koncie, metoda nie może wyszukać w pamięci podręcznej przed zrealizowaniem kodu, który wymaga, pomimo, wywołania usługi.

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
- Program może [uzyskać token z nazwą użytkownika i hasłem](msal-authentication-flows.md#usernamepassword) w aplikacjach klienckich programu .NET Framework Desktop, ale nie jest to zalecane. Nie używaj nazwy użytkownika/hasła w poufnych aplikacjach klienckich.
- Może uzyskać token za pomocą [przepływu kodu urządzenia](msal-authentication-flows.md#device-code) w aplikacjach działających na urządzeniach, które nie mają przeglądarki sieci Web. Użytkownik otrzymuje adres URL i kod, który następnie przechodzi do przeglądarki sieci Web na innym urządzeniu i wprowadza kod i loguje się.  Usługa Azure AD następnie wysyła token z powrotem do urządzenia bez przeglądarki.

### <a name="confidential-client-applications"></a>Poufne aplikacje klienckie

W przypadku poufnych aplikacji klienckich (aplikacji sieci Web, internetowego interfejsu API lub aplikacji demona, takich jak usługa systemu Windows):
- Uzyskaj tokeny **dla samej aplikacji** , a nie dla użytkownika, przy użyciu [przepływu poświadczeń klienta](msal-authentication-flows.md#client-credentials). Może to służyć do synchronizowania narzędzi lub narzędzi, które przetwarzają użytkowników ogólnie, a nie do określonego użytkownika.
- Użyj [przepływu w imieniu](msal-authentication-flows.md#on-behalf-of) interfejsu API sieci Web, aby wywołać interfejs API w imieniu użytkownika. Aplikacja jest identyfikowana przy użyciu poświadczeń klienta w celu uzyskania tokenu na podstawie potwierdzenia użytkownika (na przykład lub tokenu JWT). Ten przepływ jest używany przez aplikacje, które muszą uzyskać dostęp do zasobów określonego użytkownika w ramach wywołań między usługami.
- Uzyskuj tokeny przy użyciu [przepływu kodu autoryzacji](msal-authentication-flows.md#authorization-code) w usłudze Web Apps po zalogowaniu się użytkownika za pośrednictwem adresu URL żądania autoryzacji. Aplikacja OpenID Connect Connect zazwyczaj używa tego mechanizmu, który umożliwia użytkownikowi Logowanie przy użyciu funkcji Open ID Connect, a następnie dostęp do internetowych interfejsów API w imieniu użytkownika.

## <a name="authentication-results"></a>Wyniki uwierzytelniania

Gdy klient zażąda tokenu dostępu, usługa Azure AD zwraca również wynik uwierzytelniania, który zawiera metadane dotyczące tokenu dostępu. Te informacje obejmują czas wygaśnięcia tokenu dostępu i zakresy, dla których jest on prawidłowy. Te dane umożliwiają aplikacji inteligentne buforowanie tokenów dostępu bez konieczności analizowania samego tokenu dostępu.  Wynik uwierzytelniania ujawnia:

- [Token dostępu](access-tokens.md) dla internetowego interfejsu API do uzyskiwania dostępu do zasobów. Jest to ciąg, zazwyczaj szyfrowany przy użyciu algorytmu Base64, ale klient nigdy nie powinien być widoczny w tokenie dostępu. Format nie jest gwarantowany, aby pozostały stabilny i można go było zaszyfrować dla zasobu. Osoby piszące kod w zależności od zawartości tokenu dostępu na kliencie są jednym z największych źródeł błędów i podziałów logiki klienta.
- [Identyfikator tokenu](id-tokens.md) dla użytkownika (jest to token JWT).
- Wygaśnięcie tokenu, które informuje datę i godzinę wygaśnięcia tokenu.
- Identyfikator dzierżawy zawiera dzierżawcę, w którym znaleziono użytkownika. Dla użytkowników-Gości (scenariusze B2B usługi Azure AD) identyfikator dzierżawy jest dzierżawcą gościa, a nie z unikatowym dzierżawcą. Po dostarczeniu tokenu w nazwie użytkownika wynik uwierzytelniania zawiera również informacje o tym użytkowniku. W przypadku poufnych przepływów klientów, w których tokeny są żądane bez użytkownika (dla aplikacji), informacje o użytkowniku mają wartość null.
- Zakresy, dla których token został wystawiony.
- Unikatowy identyfikator użytkownika.

## <a name="next-steps"></a>Następne kroki

Jeśli używasz programu MSAL for Java, Dowiedz się więcej o [serializacji niestandardowego buforu tokenów w MSAL for Java](msal-java-token-cache-serialization.md).

Dowiedz się więcej na temat [obsługi błędów i wyjątków](msal-handling-exceptions.md).
