---
title: Przepływy uwierzytelniania MSAL | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej na temat przepływów uwierzytelniania i uprawnień używanych przez bibliotekę uwierzytelniania firmy Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/20/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 9a0315fadefac0269a6d9670844ef8c3886eef12
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84975626"
---
# <a name="authentication-flows"></a>Przepływy uwierzytelniania

Biblioteka Microsoft Authentication Library (MSAL) obsługuje kilka przepływów uwierzytelniania do użycia w różnych scenariuszach aplikacji.

| Ruch | Opis | Używany w|
| ---- | ----------- | ------- |
| [Interaktywne](#interactive) | Pobiera token przez proces interaktywny, który wyświetla użytkownikowi komunikat o poświadczeniach za pomocą przeglądarki lub okna podręcznego. | [Aplikacje klasyczne](scenario-desktop-overview.md), [aplikacje mobilne](scenario-mobile-overview.md) |
| [Niejawne przyznanie](#implicit-grant) | Zezwala aplikacji na pobieranie tokenów bez przeprowadzania wymiany poświadczeń serwera zaplecza. Umożliwia aplikacji zalogowanie się użytkownika, zachowanie sesji oraz uzyskanie tokenów do innych interfejsów API sieci Web, a wszystko to w kodzie JavaScript klienta.| [Aplikacje jednostronicowe (SPA)](scenario-spa-overview.md) |
| [Kod autoryzacji](#authorization-code) | Używany w aplikacjach zainstalowanych na urządzeniu w celu uzyskania dostępu do chronionych zasobów, takich jak interfejsy API sieci Web. Umożliwia dodawanie funkcji logowania i dostępu do interfejsu API do aplikacji mobilnych i klasycznych. | [Aplikacje klasyczne](scenario-desktop-overview.md), [aplikacje mobilne](scenario-mobile-overview.md), [aplikacje sieci Web](scenario-web-app-call-api-overview.md) |
| [W imieniu](#on-behalf-of) | Aplikacja wywołuje usługę lub internetowy interfejs API, który z kolei musi wywołać inną usługę lub internetowy interfejs API. Pomysłem jest propagowanie tożsamości i uprawnień delegowanych użytkowników za pomocą łańcucha żądań. | [Interfejsy API sieci Web](scenario-web-api-call-api-overview.md) |
| [Poświadczenia klienta](#client-credentials) | Umożliwia dostęp do zasobów hostowanych przez sieć Web przy użyciu tożsamości aplikacji. Często używane do interakcji między serwerami, które muszą działać w tle bez natychmiastowej interakcji z użytkownikiem. | [Aplikacje demona](scenario-daemon-overview.md) |
| [Kod urządzenia](#device-code) | Umożliwia użytkownikom logowanie się na urządzeniach z ograniczeniami, takich jak inteligentna telewizja, urządzenie IoT lub drukarka. | [Aplikacje klasyczne i mobilne](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Zintegrowane uwierzytelnianie systemu Windows](#integrated-windows-authentication) | Zezwala aplikacjom w domenie lub Azure Active Directory (Azure AD) przyłączonym do uzyskiwania tokenu w trybie dyskretnym (bez interakcji z użytkownikiem).| [Aplikacje klasyczne i mobilne](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nazwa użytkownika/hasło](#usernamepassword) | Zezwala aplikacji na logowanie użytkownika przez bezpośrednią obsługę hasła. Ten przepływ nie jest zalecany. | [Aplikacje klasyczne i mobilne](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Jak każdy przepływ emituje tokeny i kody

W zależności od sposobu skompilowania klienta można użyć co najmniej jednego przepływu uwierzytelniania obsługiwanego przez platformę tożsamości firmy Microsoft. Te przepływy mogą generować kilka typów tokenów, a także kody autoryzacji i wymagać innych tokenów, aby działały.

| Ruch                                                                               | KONIECZN            | id_token | token dostępu | Odśwież token | kod autoryzacji |
|------------------------------------------------------------------------------------|:-------------------:|:--------:|:------------:|:-------------:|:------------------:|
| [Przepływ kodu autoryzacji](v2-oauth2-auth-code-flow.md)                             |                     | x        | x            | x             | x                  |
| [Niejawny przepływ](v2-oauth2-implicit-grant-flow.md)                                  |                     | x        | x            |               |                    |
| [Hybrydowy przepływ OIDC](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition) |                     | x        |              |               | x                  |
| [Odświeżanie umorzenia tokenu](v2-oauth2-auth-code-flow.md#refresh-the-access-token)   | Odśwież token       | x        | x            | x             |                    |
| [Przepływ „w imieniu”](v2-oauth2-on-behalf-of-flow.md)                                | token dostępu        | x        | x            | x             |                    |
| [Przepływ kodu urządzenia](v2-oauth2-device-code.md)                                       |                     | x        | x            | x             |                    |
| [Poświadczenia klienta](v2-oauth2-client-creds-grant-flow.md)                         |                     |          | x (tylko aplikacja) |               |                    |
| [Nazwa użytkownika/hasło](v2-oauth-ropc.md) (ROPC)                                       | hasło & nazwy użytkownika | x        | x            | x             |                    |

## <a name="interactive"></a>Interaktywne

MSAL może interaktywnie monitować użytkownika o podanie poświadczeń w celu zalogowania się i uzyskania tokenu przy użyciu tych poświadczeń.

![Diagram interaktywnego przepływu](media/msal-authentication-flows/interactive.png)

Aby uzyskać więcej informacji na temat interakcyjnego pozyskiwania tokenów na określonych platformach, zobacz:

- [MSAL.js żądania interaktywne](msal-js-prompt-behavior.md)
- [Platforma uniwersalna systemu Windows](msal-net-uwp-considerations.md)
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)

## <a name="implicit-grant"></a>Niejawne przyznanie

MSAL obsługuje [niejawny przepływ uwierzytelniania OAuth 2](v2-oauth2-implicit-grant-flow.md) . Ten przepływ umożliwia aplikacji uzyskanie tokenów z platformy tożsamości firmy Microsoft bez konieczności wymiany poświadczeń serwera zaplecza. Ten przepływ umożliwia aplikacji Logowanie użytkownika, obsługę sesji oraz uzyskiwanie tokenów dla innych interfejsów API sieci Web, a wszystko to w kodzie JavaScript klienta.

![Diagram niejawnego przepływu dotacji](media/msal-authentication-flows/implicit-grant.svg)

Wiele nowoczesnych aplikacji sieci Web jest zbudowanych jako po stronie klienta, jednostronicowych aplikacji (SPA), które są zapisywane w języku JavaScript lub w strukturze SPA, takiej jak kątowy, Vue.js i React.js. Aplikacje te działają w przeglądarce internetowej i mają inne cechy uwierzytelniania niż tradycyjne aplikacje sieci Web po stronie serwera. Platforma tożsamości firmy Microsoft umożliwia aplikacjom jednostronicowym Logowanie użytkowników i uzyskiwanie tokenów umożliwiających dostęp do usług zaplecza lub interfejsów API sieci Web przy użyciu niejawnego przepływu dotacji. Niejawny przepływ umożliwia aplikacji uzyskanie tokenów identyfikatora reprezentujących uwierzytelnionego użytkownika, a także dostęp do tokenów wymaganych do wywołania chronionych interfejsów API.

Ten przepływ uwierzytelniania nie obejmuje scenariuszy aplikacji, które wykorzystują Międzyplatformowe platformy JavaScript, takie jak elektron lub reagowanie na nie, ponieważ wymagają dalszych możliwości interakcji z natywnymi platformami.

Tokeny wystawione za pośrednictwem trybu niejawnego przepływu mają **ograniczenie długości** , ponieważ są zwracane do przeglądarki za pomocą adresu URL (gdzie `response_mode` is `query` lub `fragment` ). Niektóre przeglądarki ograniczają długość adresu URL na pasku przeglądarki i kończą się niepowodzeniem, gdy jest zbyt długi. W ten sposób niejawne tokeny przepływu nie zawierają `groups` ani `wids` oświadczeń.

## <a name="authorization-code"></a>Kod autoryzacji

MSAL obsługuje przepływ [przyznania kodu autoryzacji OAuth 2](v2-oauth2-auth-code-flow.md) . Tego przepływu można używać w aplikacjach zainstalowanych na urządzeniu w celu uzyskania dostępu do chronionych zasobów, takich jak interfejsy API sieci Web. Pozwala to na dodawanie funkcji logowania i dostępu do interfejsu API do aplikacji mobilnych i klasycznych.

Gdy użytkownicy logują się do aplikacji sieci Web (websites), aplikacja sieci Web otrzymuje kod autoryzacji. Kod autoryzacji jest realizowany w celu uzyskania tokenu do wywoływania interfejsów API sieci Web.

![Diagram przepływu kodu autoryzacji](media/msal-authentication-flows/authorization-code.png)

Na powyższym diagramie aplikacja:

1. Żąda kodu autoryzacji, który jest zrealizowany dla tokenu dostępu.
2. Używa tokenu dostępu do wywoływania internetowego interfejsu API.

### <a name="considerations"></a>Zagadnienia do rozważenia

- Aby zrealizować token, można użyć kodu autoryzacji tylko raz. Nie próbuj uzyskać tokenu wielokrotnie przy użyciu tego samego kodu autoryzacji, ponieważ jest on jawnie zabroniony przez standardową specyfikację protokołu. W przypadku zrealizowania kodu kilka razy, celowo lub, ponieważ nie masz pewności, że struktura robi to również, zostanie wyświetlony następujący błąd:

    `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

## <a name="on-behalf-of"></a>W imieniu

MSAL obsługuje [przepływ uwierzytelniania OAuth 2 w imieniu użytkownika](v2-oauth2-on-behalf-of-flow.md). Ten przepływ jest używany, gdy aplikacja wywołuje usługę lub internetowy interfejs API, który z kolei musi wywołać inną usługę lub internetowy interfejs API. Pomysłem jest propagowanie tożsamości i uprawnień delegowanych użytkowników za pomocą łańcucha żądań. W przypadku usługi warstwy środkowej, aby umożliwić uwierzytelnione żądania do usługi podrzędnej, musi on zabezpieczyć token dostępu z platformy tożsamości firmy Microsoft *w imieniu* użytkownika.

![Diagram przepływu w imieniu](media/msal-authentication-flows/on-behalf-of.png)

Na powyższym diagramie:

1. Aplikacja uzyskuje token dostępu dla internetowego interfejsu API.
2. Klient (aplikacja sieci Web, komputer stacjonarny, aplikacje mobilne lub jednostronicowe) wywołuje chroniony internetowy interfejs API, dodając token dostępu jako token okaziciela w nagłówku uwierzytelniania żądania HTTP. Internetowy interfejs API uwierzytelnia użytkownika.
3. Gdy klient wywołuje internetowy interfejs API, internetowy interfejs API żąda innego tokenu w imieniu użytkownika.
4. Chroniony internetowy interfejs API używa tego tokenu do wywoływania internetowego interfejsu API w imieniu użytkownika. Internetowy interfejs API może również później żądać tokenów dla innych podrzędnych interfejsów API (ale nadal w imieniu tego samego użytkownika).

## <a name="client-credentials"></a>Poświadczenia klienta

MSAL obsługuje [przepływ poświadczeń klienta OAuth 2](v2-oauth2-client-creds-grant-flow.md). Ten przepływ umożliwia dostęp do zasobów hostowanych w sieci Web przy użyciu tożsamości aplikacji. Ten typ dotacji jest często używany w przypadku interakcji między serwerami, które muszą działać w tle bez natychmiastowej interakcji z użytkownikiem. Te typy aplikacji są często określane jako demony lub konta usług.

Przepływ przyznania poświadczeń klienta umożliwia usłudze sieci Web (poufnemu klienta) użycie własnych poświadczeń zamiast personifikowania użytkownika w celu uwierzytelniania podczas wywoływania innej usługi sieci Web. W tym scenariuszu klient jest zwykle usługą sieci Web warstwy środkowej, usługą demona lub witryną sieci Web. W przypadku wyższego poziomu gwarancji platforma tożsamości firmy Microsoft umożliwia usłudze wywołującej używanie certyfikatu (zamiast wspólnego klucza tajnego) jako poświadczenia.

> [!NOTE]
> Poufny przepływ klienta nie jest dostępny na platformach mobilnych, takich jak platformy UWP, Xamarin. iOS i Xamarin. Android, ponieważ obsługują one tylko publiczne aplikacje klienckie. Publiczne aplikacje klienckie nie wiedzą, jak udowodnić tożsamość aplikacji dostawcy tożsamości. Bezpieczne połączenie można uzyskać w aplikacji sieci Web lub zapleczu interfejsu API sieci Web, wdrażając certyfikat.

### <a name="application-secrets"></a>Wpisy tajne aplikacji

![Diagram poufnego klienta z hasłem](media/msal-authentication-flows/confidential-client-password.png)

Na powyższym diagramie aplikacja:

1. Uzyskuje token przy użyciu klucza tajnego aplikacji lub poświadczeń hasła.
2. Używa tokenu do żądania zasobu.

### <a name="certificates"></a>Certyfikaty

![Diagram poufnego klienta z certyfikatem](media/msal-authentication-flows/confidential-client-certificate.png)

Na powyższym diagramie aplikacja:

1. Uzyskuje token przy użyciu poświadczeń certyfikatu.
2. Używa tokenu do żądania zasobu.

Te poświadczenia klienta muszą być następujące:

- Zarejestrowano w usłudze Azure AD.
- Przeszedł podczas konstruowania poufnego obiektu aplikacji klienta w kodzie.

## <a name="device-code"></a>Kod urządzenia

MSAL obsługuje [przepływ kodu urządzenia OAuth 2](v2-oauth2-device-code.md) , który umożliwia użytkownikom logowanie się na urządzeniach z ograniczeniami, takich jak inteligentne telewizory, urządzenia IoT i drukarki. Interaktywne uwierzytelnianie w usłudze Azure AD wymaga przeglądarki sieci Web. W przypadku, gdy urządzenie lub system operacyjny nie udostępnia przeglądarki sieci Web, przepływ kodu urządzenia umożliwia użytkownikowi korzystanie z innego urządzenia, takiego jak komputer lub telefon komórkowy, aby zalogować się interaktywnie.

Za pomocą przepływu kodu urządzenia aplikacja uzyskuje tokeny w ramach dwuetapowego procesu zaprojektowanego dla tych urządzeń i systemów operacyjnych. Przykładami takich aplikacji są aplikacje działające na urządzeniach IoT i narzędziach interfejsu wiersza polecenia (CLI).

![Diagram przepływu kodu urządzenia](media/msal-authentication-flows/device-code.png)

Na powyższym diagramie:

1. Za każdym razem, gdy wymagane jest uwierzytelnienie użytkownika, aplikacja udostępnia kod i prosi użytkownika o użycie innego urządzenia, takiego jak smartfon połączony z Internetem, aby odwiedzać adres URL (na przykład `https://microsoft.com/devicelogin` ). Użytkownik otrzymuje monit o wprowadzenie kodu i przechodzenie przez normalne środowisko uwierzytelniania, w tym monity o zgodę i [uwierzytelnianie wieloskładnikowe](../authentication/concept-mfa-howitworks.md), w razie potrzeby.
1. Po pomyślnym uwierzytelnieniu aplikacja wiersza polecenia otrzymuje wymagane tokeny za pośrednictwem kanału zaplecza i używa ich do wykonywania wywołań interfejsu API sieci Web.

### <a name="constraints"></a>Ograniczenia

- Przepływ kodu urządzenia jest dostępny tylko w publicznych aplikacjach klienckich.
- Urząd przeszedł podczas konstruowania publicznej aplikacji klienckiej, musi mieć jedną z następujących czynności:
  - Dzierżawca w formularzu, `https://login.microsoftonline.com/{tenant}/,` gdzie `{tenant}` jest identyfikator GUID reprezentujący identyfikator dzierżawy lub nazwę domeny skojarzoną z dzierżawcą.
  - Dla kont służbowych w formularzu `https://login.microsoftonline.com/organizations/` .

## <a name="integrated-windows-authentication"></a>Zintegrowane uwierzytelnianie systemu Windows

Usługa MSAL obsługuje zintegrowane uwierzytelnianie systemu Windows (IWA) dla aplikacji dla komputerów stacjonarnych i mobilnych działających na komputerze z systemem Windows przyłączonym do domeny lub w usłudze Azure AD. Przy użyciu IWA te aplikacje mogą uzyskać token dyskretnie, bez konieczności interakcji interfejsu użytkownika przez użytkownika.

![Diagram zintegrowanego uwierzytelniania systemu Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

Na powyższym diagramie aplikacja:

1. Uzyskuje token przy użyciu zintegrowanego uwierzytelniania systemu Windows.
2. Używa tokenu do żądania zasobu.

### <a name="constraints"></a>Ograniczenia

Zintegrowane uwierzytelnianie systemu Windows (IWA) obsługuje *tylko* użytkowników federacyjnych — użytkownicy utworzeni w Active Directory i za pomocą usługi Azure AD. Użytkownicy utworzeni bezpośrednio w usłudze Azure AD bez Active Directory kopii zapasowej (zarządzani użytkownicy) nie mogą używać tego przepływu uwierzytelniania. To ograniczenie nie ma wpływu na [przepływ nazwy użytkownika/hasła](#usernamepassword).

IWA jest przeznaczony dla aplikacji .NET Framework, .NET Core i platforma uniwersalna systemu Windows.

IWA nie pomija uwierzytelniania wieloskładnikowego. Jeśli skonfigurowano uwierzytelnianie wieloskładnikowe, IWA może się nie powieść, jeśli wymagane jest wyzwanie usługi uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe wymaga interakcji z użytkownikiem.

Nie kontrolujesz, kiedy dostawca tożsamości żąda wykonania uwierzytelniania dwuskładnikowego. Administrator dzierżawy. Zazwyczaj uwierzytelnianie dwuskładnikowe jest wymagane, gdy logujesz się z innego kraju/regionu, gdy nie masz połączenia za pośrednictwem sieci VPN z siecią firmową, a czasami nawet po nawiązaniu połączenia za pośrednictwem sieci VPN. Usługa Azure AD używa systemu AI do ciągłego uczenia się, czy wymagane jest uwierzytelnianie dwuskładnikowe. Jeśli IWA nie powiedzie się, należy wrócić do [interakcyjnego monitu użytkownika](#interactive).

Urząd przeszedł podczas konstruowania publicznej aplikacji klienckiej musi mieć jedną z:

- Dzierżawca w formularzu, `https://login.microsoftonline.com/{tenant}/,` gdzie `{tenant}` jest identyfikator GUID reprezentujący identyfikator dzierżawy lub nazwę domeny skojarzoną z dzierżawcą.
- Dla dowolnego konta służbowego ( `https://login.microsoftonline.com/organizations/` ). Konta osobiste firmy Microsoft (MSA) nie są obsługiwane; nie można używać `/common` ani `/consumers` dzierżawców.

Ponieważ IWA jest przepływem dyskretnym, musi być spełniony jeden z następujących warunków:

- Użytkownik Twojej aplikacji musi być wcześniej wyraził zgodę na korzystanie z aplikacji.
- Administrator dzierżawy musi zostać wcześniej wysłany do wszystkich użytkowników w dzierżawie, aby mógł korzystać z aplikacji.

Oznacza to, że jest spełniony jeden z następujących warunków:

- Jesteś deweloperem **w Azure Portal** dla siebie.
- Administrator dzierżawy zaznaczył **zgodę na przyznanie/odwołanie administratora dla {domena dzierżawy}** na karcie **uprawnienia interfejsu API** rejestracji aplikacji w Azure Portal (zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- Udostępniono sposób, aby użytkownicy mogli wyrazić zgodę na aplikację; Zobacz [żądanie zgody poszczególnych użytkowników](v2-permissions-and-consent.md#requesting-individual-user-consent).
- Podano sposób, w jaki Administrator dzierżawy wyrazi zgodę na aplikację; Zobacz [zgoda administratora](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

Przepływ IWA jest włączony dla aplikacji platformy .NET dla komputerów stacjonarnych, platformy .NET Core i systemu Windows. W przypadku platformy .NET Core należy podać nazwę użytkownika IWA, ponieważ .NET Core nie może uzyskać nazw użytkowników z systemu operacyjnego.

Aby uzyskać więcej informacji na temat zgody, zobacz temat [uprawnienia i zgoda dotyczące programu v 2.0](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Nazwa użytkownika/hasło

MSAL obsługuje uwierzytelnianie przy użyciu [poświadczeń hasła właściciela zasobu OAuth 2](v2-oauth-ropc.md) (ROPC), które umożliwia aplikacji Logowanie użytkownika przez bezpośrednią obsługę hasła. W aplikacji komputerowej można użyć przepływu nazwy użytkownika/hasła w celu odzyskania tokenu w trybie dyskretnym. W przypadku korzystania z aplikacji nie jest wymagany żaden interfejs użytkownika.

![Diagram przepływu nazwy użytkownika/hasła](media/msal-authentication-flows/username-password.png)

Na powyższym diagramie aplikacja:

1. Uzyskuje token, wysyłając nazwę użytkownika i hasło do dostawcy tożsamości.
2. Wywołuje interfejs API sieci Web przy użyciu tokenu.

> [!WARNING]
> Ten przepływ nie jest zalecany. Wymaga wysokiego stopnia zaufania i ujawnienia poświadczeń. Tego przepływu należy używać *tylko* wtedy, gdy nie można używać bardziej bezpiecznych przepływów. Aby uzyskać więcej informacji, zobacz [co to jest rozwiązanie w przypadku rosnącego problemu z hasłami?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

Preferowany przepływ do uzyskiwania tokenu w trybie dyskretnym na komputerach przyłączonych do domeny systemu Windows jest [zintegrowanym uwierzytelnianiem systemu Windows](#integrated-windows-authentication). W innych przypadkach Użyj [przepływu kodu urządzenia](#device-code).

Chociaż przepływ nazwy użytkownika/hasła może być przydatny w niektórych scenariuszach, takich jak DevOps, unikaj go, jeśli chcesz używać nazwy użytkownika/hasła w scenariuszach interaktywnych, w których udostępniasz własny interfejs użytkownika.

Za pomocą nazwy użytkownika/hasła:

- Użytkownicy, którzy nie muszą wykonać uwierzytelniania wieloskładnikowego, nie będą mogli się zalogować, ponieważ nie ma żadnej interakcji.
- Użytkownicy nie będą mogli korzystać z logowania jednokrotnego.

### <a name="constraints"></a>Ograniczenia

Poza [zintegrowanymi ograniczeniami uwierzytelniania systemu Windows](#integrated-windows-authentication)obowiązują również następujące ograniczenia:

- Przepływ nazwy użytkownika/hasła nie jest zgodny z dostępem warunkowym i uwierzytelnianiem wieloskładnikowym. W związku z tym, jeśli aplikacja działa w dzierżawie usługi Azure AD, w której Administrator dzierżawy wymaga uwierzytelniania wieloskładnikowego, nie można użyć tego przepływu. Wiele organizacji to.
- Działa tylko w przypadku kont służbowych (nie kont Microsoft).
- Przepływ jest dostępny w programie .NET Desktop i .NET Core, ale nie na platforma uniwersalna systemu Windows.

### <a name="azure-ad-b2c-and-ropc"></a>Azure AD B2C i ROPC

Aby uzyskać więcej informacji na temat używania ROPC w MSAL.NET i Azure AD B2C, zobacz [using ROPC with Azure AD B2C](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy przejrzałeś przepływy uwierzytelniania obsługiwane przez bibliotekę uwierzytelniania firmy Microsoft (MSAL), Dowiedz się więcej o uzyskiwaniu i buforowaniu tokenów używanych w tych przepływach:

[Uzyskiwanie i buforowanie tokenów przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL)](msal-acquire-cache-tokens.md)
