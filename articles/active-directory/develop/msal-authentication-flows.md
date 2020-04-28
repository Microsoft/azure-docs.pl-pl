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
ms.date: 01/30/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 25c219bedbbbec9fbc0c5617c7bd9fc482faf49a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80050508"
---
# <a name="authentication-flows"></a>Przepływy uwierzytelniania

W tym artykule opisano różne przepływy uwierzytelniania udostępniane przez bibliotekę uwierzytelniania firmy Microsoft (MSAL).  Te przepływy mogą być używane w różnych scenariuszach aplikacji.

| Ruch | Opis | Używany w|  
| ---- | ----------- | ------- | 
| [Interaktywne](#interactive) | Pobiera token przez proces interaktywny, który wyświetla użytkownikowi komunikat o poświadczeniach za pomocą przeglądarki lub okna podręcznego. | [Aplikacje klasyczne](scenario-desktop-overview.md), [aplikacje mobilne](scenario-mobile-overview.md) |
| [Niejawne przyznanie](#implicit-grant) | Zezwala aplikacji na pobieranie tokenów bez przeprowadzania wymiany poświadczeń serwera zaplecza. Dzięki temu aplikacja może logować się do użytkownika, obsługiwać sesję i uzyskiwać tokeny do innych interfejsów API sieci Web, a wszystko to w kodzie JavaScript klienta.| [Aplikacje jednostronicowe (SPA)](scenario-spa-overview.md) |
| [Kod autoryzacji](#authorization-code) | Używany w aplikacjach zainstalowanych na urządzeniu w celu uzyskania dostępu do chronionych zasobów, takich jak interfejsy API sieci Web. Pozwala to na dodawanie funkcji logowania i dostępu do interfejsu API do aplikacji mobilnych i klasycznych. | [Aplikacje klasyczne](scenario-desktop-overview.md), [aplikacje mobilne](scenario-mobile-overview.md), [aplikacje sieci Web](scenario-web-app-call-api-overview.md) | 
| [W imieniu](#on-behalf-of) | Aplikacja wywołuje usługę lub internetowy interfejs API, który z kolei musi wywołać inną usługę lub internetowy interfejs API. Pomysłem jest propagowanie tożsamości i uprawnień delegowanych użytkowników za pomocą łańcucha żądań. | [Interfejsy API sieci Web](scenario-web-api-call-api-overview.md) |
| [Poświadczenia klienta](#client-credentials) | Umożliwia dostęp do zasobów hostowanych przez sieć Web przy użyciu tożsamości aplikacji. Często używane do interakcji między serwerami, które muszą działać w tle bez natychmiastowej interakcji z użytkownikiem. | [Aplikacje demona](scenario-daemon-overview.md) |
| [Kod urządzenia](#device-code) | Umożliwia użytkownikom logowanie się na urządzeniach z ograniczeniami, takich jak inteligentna telewizja, urządzenie IoT lub drukarka. | [Aplikacje klasyczne i mobilne](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Zintegrowane uwierzytelnianie systemu Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Zezwala aplikacjom w domenie lub Azure Active Directory (Azure AD) przyłączonym do uzyskiwania tokenu w trybie dyskretnym (bez interakcji z użytkownikiem).| [Aplikacje klasyczne i mobilne](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nazwa użytkownika/hasło](scenario-desktop-acquire-token.md#username-and-password) | Zezwala aplikacji na logowanie użytkownika przez bezpośrednią obsługę hasła. Ten przepływ nie jest zalecany. | [Aplikacje klasyczne i mobilne](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Jak każdy przepływ emituje tokeny i kody
 
W zależności od sposobu skompilowania klienta można użyć jednego (lub kilku) przepływów uwierzytelniania obsługiwanych przez platformę tożsamości firmy Microsoft.  Te przepływy mogą generować różne tokeny (id_tokens, tokeny odświeżenia, tokeny dostępu), a także kody autoryzacji i wymagać innych tokenów, aby działały. Ten wykres zawiera przegląd:
 
|Ruch | KONIECZN | id_token | token dostępu | Odśwież token | kod autoryzacji | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Przepływ kodu autoryzacji](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Niejawny przepływ](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybrydowy przepływ OIDC](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Odświeżanie umorzenia tokenu](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | Odśwież token | x | x | x| |
|[Przepływ „w imieniu”](v2-oauth2-on-behalf-of-flow.md) | token dostępu| x| x| x| |
|[Przepływ kodu urządzenia](v2-oauth2-device-code.md) | | x| x| x| |
|[Poświadczenia klienta](v2-oauth2-client-creds-grant-flow.md) | | | x (tylko aplikacja)| | |
 
Tokeny wystawione za pośrednictwem trybu niejawnego mają ograniczenie długości z powodu przekazanie z powrotem do przeglądarki `response_mode` za `query` pośrednictwem adresu URL (gdzie is lub `fragment`).  Niektóre przeglądarki mają limit rozmiaru adresu URL, który może być umieszczony na pasku przeglądarki i niepowodzenie, gdy jest zbyt długi.  W rezultacie te tokeny nie mają `groups` ani `wids` oświadczenia.

## <a name="interactive"></a>Interaktywne

Usługa MSAL obsługuje możliwość interaktywnego monitowania użytkownika o podanie poświadczeń w celu zalogowania się i uzyskania tokenu przy użyciu tych poświadczeń.

![Diagram interaktywnego przepływu](media/msal-authentication-flows/interactive.png)

Aby uzyskać więcej informacji na temat korzystania z usługi MSAL.NET do interaktywnego pozyskiwania tokenów na określonych platformach, zobacz:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Platforma uniwersalna systemu Windows](msal-net-uwp-considerations.md)

Aby uzyskać więcej informacji na temat interakcyjnych wywołań w MSAL. js, zobacz temat [zachowanie monitu w programie MSAL. js Interactive Requests](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Niejawne przyznanie

Usługa MSAL obsługuje [niejawny przepływ uwierzytelniania OAuth 2](v2-oauth2-implicit-grant-flow.md), który umożliwia aplikacji pobieranie tokenów z platformy tożsamości firmy Microsoft bez konieczności wymiany poświadczeń serwera zaplecza. Dzięki temu aplikacja może logować się do użytkownika, obsługiwać sesję i uzyskiwać tokeny do innych interfejsów API sieci Web, a wszystko to w kodzie JavaScript klienta.

![Diagram niejawnego przepływu dotacji](media/msal-authentication-flows/implicit-grant.svg)

Wiele nowoczesnych aplikacji sieci Web jest zbudowanych jako aplikacje jednostronicowe, które są zapisywane przy użyciu języka JavaScript lub środowiska SPA, takiego jak kątowy, Vue. js i reaguje. js. Aplikacje te działają w przeglądarce internetowej i mają inne cechy uwierzytelniania niż tradycyjne aplikacje sieci Web po stronie serwera. Platforma tożsamości firmy Microsoft umożliwia aplikacjom jednostronicowym Logowanie użytkowników i uzyskiwanie tokenów umożliwiających dostęp do usług zaplecza lub interfejsów API sieci Web przy użyciu niejawnego przepływu dotacji. Niejawny przepływ umożliwia aplikacji uzyskanie tokenów identyfikatora reprezentujących uwierzytelnionego użytkownika, a także dostęp do tokenów wymaganych do wywołania chronionych interfejsów API.

Ten przepływ uwierzytelniania nie obejmuje scenariuszy aplikacji, które wykorzystują Międzyplatformowe platformy JavaScript, takie jak elektron i reagowanie na aplikacje natywne, ponieważ wymagają one dalszych możliwości interakcji z platformami macierzystymi.

## <a name="authorization-code"></a>Kod autoryzacji

MSAL obsługuje [przyznawanie kodu autoryzacji OAuth 2](v2-oauth2-auth-code-flow.md). Tego uprawnienia można używać w aplikacjach zainstalowanych na urządzeniu w celu uzyskania dostępu do chronionych zasobów, takich jak interfejsy API sieci Web. Pozwala to na dodawanie funkcji logowania i dostępu do interfejsu API do aplikacji mobilnych i klasycznych. 

Gdy użytkownicy logują się do aplikacji sieci Web (websites), aplikacja sieci Web otrzymuje kod autoryzacji.  Kod autoryzacji jest realizowany w celu uzyskania tokenu do wywoływania interfejsów API sieci Web. W ASP.NET i ASP.NET Core Web Apps jedynym celem `AcquireTokenByAuthorizationCode` jest dodanie tokenu do pamięci podręcznej tokenów. Token może być następnie używany przez aplikację (zazwyczaj w kontrolerach, które po prostu otrzymują token dla interfejsu API za pomocą programu `AcquireTokenSilent`).

![Diagram przepływu kodu autoryzacji](media/msal-authentication-flows/authorization-code.png)

Na powyższym diagramie aplikacja:

1. Żąda kodu autoryzacji, który jest zrealizowany dla tokenu dostępu.
2. Używa tokenu dostępu do wywoływania internetowego interfejsu API.

### <a name="considerations"></a>Zagadnienia do rozważenia

- Aby zrealizować token, można użyć kodu autoryzacji tylko raz. Nie próbuj uzyskać tokenu wiele razy przy użyciu tego samego kodu autoryzacji (jest to jawnie zabronione przez standardową specyfikację protokołu). W przypadku zrealizowania kodu kilkakrotnie lub z tego powodu nie masz świadomego, że struktura również go wykonuje, zostanie wyświetlony następujący błąd:`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Jeśli piszesz aplikację ASP.NET lub ASP.NET Core, może się tak zdarzyć, jeśli nie poinformujesz platformy o tym, że kod autoryzacji został już zrealizowany. W tym celu należy wywołać `context.HandleCodeRedemption()` metodę programu obsługi `AuthorizationCodeReceived` zdarzeń.

- Unikaj udostępniania tokenu dostępu za pomocą ASP.NET, co może uniemożliwić prawidłowe poprawność. Aby uzyskać więcej informacji, zobacz temat [#693 problemu](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>W imieniu

MSAL obsługuje [przepływ uwierzytelniania OAuth 2 w imieniu użytkownika](v2-oauth2-on-behalf-of-flow.md).  Ten przepływ jest używany, gdy aplikacja wywołuje usługę lub internetowy interfejs API, co z kolei musi wywołać inną usługę lub internetowy interfejs API. Pomysłem jest propagowanie tożsamości i uprawnień delegowanych użytkowników za pomocą łańcucha żądań. W przypadku usługi warstwy środkowej aby żądania były uwierzytelniane w usłudze podrzędnej, należy zabezpieczyć token dostępu z platformy tożsamości firmy Microsoft w imieniu użytkownika.

![Diagram przepływu w imieniu](media/msal-authentication-flows/on-behalf-of.png)

Na powyższym diagramie:

1. Aplikacja uzyskuje token dostępu dla internetowego interfejsu API.
2. Klient (aplikacja sieci Web, komputer stacjonarny, aplikacje mobilne lub jednostronicowe) wywołuje chroniony internetowy interfejs API, dodając token dostępu jako token okaziciela w nagłówku uwierzytelniania żądania HTTP. Internetowy interfejs API uwierzytelnia użytkownika.
3. Gdy klient wywołuje internetowy interfejs API, internetowy interfejs API żąda innego tokenu w imieniu użytkownika.  
4. Chroniony internetowy interfejs API używa tego tokenu do wywoływania internetowego interfejsu API w imieniu użytkownika.  Internetowy interfejs API może również później żądać tokenów dla innych podrzędnych interfejsów API (ale nadal w imieniu tego samego użytkownika).

## <a name="client-credentials"></a>Poświadczenia klienta

MSAL obsługuje [przepływ poświadczeń klienta OAuth 2](v2-oauth2-client-creds-grant-flow.md). Ten przepływ umożliwia dostęp do zasobów hostowanych w sieci Web przy użyciu tożsamości aplikacji. Ten typ dotacji jest często używany w przypadku interakcji między serwerami, które muszą działać w tle bez natychmiastowej interakcji z użytkownikiem. Te typy aplikacji są często określane jako demony lub konta usług. 

Przepływ przyznania poświadczeń klienta umożliwia usłudze sieci Web (poufnemu klienta) użycie własnych poświadczeń zamiast personifikowania użytkownika w celu uwierzytelniania podczas wywoływania innej usługi sieci Web. W tym scenariuszu klient jest zwykle usługą sieci Web warstwy środkowej, usługą demona lub witryną sieci Web. W przypadku wyższego poziomu gwarancji platforma tożsamości firmy Microsoft umożliwia usłudze wywołującej używanie certyfikatu (zamiast wspólnego klucza tajnego) jako poświadczenia.

> [!NOTE]
> Poufny przepływ klienta nie jest dostępny na platformach mobilnych (platformy UWP, Xamarin. iOS i Xamarin. Android), ponieważ obsługują one tylko publiczne aplikacje klienckie. Publiczne aplikacje klienckie nie wiedzą, jak udowodnić tożsamość aplikacji dostawcy tożsamości. Bezpieczne połączenie można uzyskać na zapleczu aplikacji sieci Web lub interfejsu API sieci Web, wdrażając certyfikat.

MSAL.NET obsługuje dwa typy poświadczeń klienta. Te poświadczenia klienta muszą być zarejestrowane w usłudze Azure AD. Poświadczenia są przesyłane do konstruktorów poufnej aplikacji klienckiej w kodzie.

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
- Przeszedł do konstruowania poufnej aplikacji klienckiej w kodzie.

## <a name="device-code"></a>Kod urządzenia

MSAL obsługuje [przepływ kodu urządzenia OAuth 2](v2-oauth2-device-code.md), który umożliwia użytkownikom logowanie się na urządzeniach z ograniczeniami, takich jak inteligentna telewizja, urządzenie IoT lub drukarka. Interaktywne uwierzytelnianie w usłudze Azure AD wymaga przeglądarki sieci Web. Przepływ kodu urządzenia umożliwia użytkownikowi Używanie innego urządzenia (na przykład innego komputera lub telefonu komórkowego) do interaktywnego logowania, w którym urządzenie lub system operacyjny nie udostępnia przeglądarki sieci Web.

Za pomocą przepływu kodu urządzenia aplikacja uzyskuje tokeny przez proces dwuetapowy przeznaczony specjalnie dla tych urządzeń lub systemów operacyjnych. Przykładami takich aplikacji są procesy działające na urządzeniach IoT lub w narzędziach wiersza polecenia (CLI). 

![Diagram przepływu kodu urządzenia](media/msal-authentication-flows/device-code.png)

Na powyższym diagramie:

1. Za każdym razem, gdy wymagane jest uwierzytelnienie użytkownika, aplikacja udostępnia kod i prosi użytkownika o użycie innego urządzenia (takiego jak telefon smartphone połączone z Internetem), aby przejść do adresu URL (na `https://microsoft.com/devicelogin`przykład). Następnie użytkownik jest monitowany o wprowadzenie kodu i przechodzi przez normalne środowisko uwierzytelniania, w tym monity o zgodę i uwierzytelnianie wieloskładnikowe, w razie potrzeby.

2. Po pomyślnym uwierzytelnieniu aplikacja wiersza polecenia otrzymuje wymagane tokeny za pośrednictwem kanału zaplecza i używa ich do wykonywania wywołań interfejsu API sieci Web.

### <a name="constraints"></a>Ograniczenia

- Przepływ kodu urządzenia jest dostępny tylko w publicznych aplikacjach klienckich.
- Urząd przeszedł podczas konstruowania publicznej aplikacji klienckiej, musi mieć jedną z następujących czynności:
  - Dzierżawca (w postaci `https://login.microsoftonline.com/{tenant}/` , gdzie `{tenant}` jest identyfikator GUID reprezentujący identyfikator dzierżawy lub domenę skojarzoną z dzierżawcą).
  - Dla dowolnego konta służbowego (`https://login.microsoftonline.com/organizations/`).
- Konta osobiste firmy Microsoft nie są jeszcze obsługiwane przez punkt końcowy usługi Azure AD v 2.0 (nie `/common` można `/consumers` używać dzierżawców).

## <a name="integrated-windows-authentication"></a>Zintegrowane uwierzytelnianie systemu Windows

Usługa MSAL obsługuje zintegrowane uwierzytelnianie systemu Windows (IWA) dla aplikacji dla komputerów stacjonarnych lub mobilnych, które działają na przyłączonych do domeny lub komputerze z systemem Windows AD Korzystając z IWA, te aplikacje mogą uzyskać token dyskretnie (bez interakcji z INTERFEJSem użytkownika). 

![Diagram zintegrowanego uwierzytelniania systemu Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

Na powyższym diagramie aplikacja:

1. Uzyskuje token przy użyciu zintegrowanego uwierzytelniania systemu Windows.
2. Używa tokenu do żądania zasobu.

### <a name="constraints"></a>Ograniczenia

IWA obsługuje tylko użytkowników federacyjnych, co oznacza, że użytkownicy utworzeni w Active Directory i obsługujący usługę Azure AD. Użytkownicy utworzeni bezpośrednio w usłudze Azure AD, bez Active Directory kopii zapasowej (zarządzani użytkownicy) nie mogą używać tego przepływu uwierzytelniania. To ograniczenie nie ma wpływu na [przepływ nazwy użytkownika/hasła](#usernamepassword).

IWA jest przeznaczony dla aplikacji przeznaczonych dla platform .NET Framework, .NET Core i platforma uniwersalna systemu Windows.

IWA nie pomija uwierzytelniania wieloskładnikowego. Jeśli skonfigurowano uwierzytelnianie wieloskładnikowe, IWA może się nie powieść, jeśli wymagane jest wyzwanie usługi uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe wymaga interakcji z użytkownikiem.

Nie kontrolujesz, kiedy dostawca tożsamości żąda wykonania uwierzytelniania dwuskładnikowego. Administrator dzierżawy. Zazwyczaj uwierzytelnianie dwuskładnikowe jest wymagane, gdy użytkownik loguje się z innego kraju, gdy nie jest połączony za pośrednictwem sieci VPN z siecią firmową, a czasami nawet wtedy, gdy użytkownik nawiązuje połączenie za pośrednictwem sieci VPN. Usługa Azure AD używa systemu AI do ciągłego uczenia się, czy wymagane jest uwierzytelnianie dwuskładnikowe. Jeśli IWA nie powiedzie się, należy wrócić do [monitu użytkownika interaktywnego] (#interactive).

Urząd przeszedł podczas konstruowania publicznej aplikacji klienckiej, musi mieć jedną z następujących czynności:
- Dzierżawca (w postaci `https://login.microsoftonline.com/{tenant}/` , gdzie `tenant` jest identyfikator GUID reprezentujący identyfikator dzierżawy lub domenę skojarzoną z dzierżawcą).
- Dla dowolnego konta służbowego (`https://login.microsoftonline.com/organizations/`). Konta osobiste firmy Microsoft nie są obsługiwane (nie można `/common` używać `/consumers` ani dzierżawców).

Ponieważ IWA jest przepływem dyskretnym, musi być spełniony jeden z następujących warunków:
- Użytkownik Twojej aplikacji musi być wcześniej wyraził zgodę na korzystanie z aplikacji. 
- Administrator dzierżawy musi zostać wcześniej wysłany do wszystkich użytkowników w dzierżawie, aby mógł korzystać z aplikacji.

Oznacza to, że jest spełniony jeden z następujących warunków:
- Jesteś deweloperem, który **zaznaczył** Azure Portal dla siebie.
- Administrator dzierżawy zaznaczył **zgodę na przyznanie/odwołanie administratora dla {domena dzierżawy}** na karcie **uprawnienia interfejsu API** rejestracji aplikacji (zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- Użytkownik podał sposób, aby użytkownicy wyrażali zgodę na aplikację (patrz [żądanie zgody poszczególnych użytkowników](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- Podano sposób, w jaki Administrator dzierżawy wyrazi zgodę na aplikację (zobacz [zgoda administratora](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

Przepływ IWA jest włączony dla aplikacji platformy .NET dla komputerów stacjonarnych, platformy .NET Core i systemu Windows. W przypadku platformy .NET Core należy podać nazwę użytkownika IWA, ponieważ .NET Core nie może uzyskać nazw użytkowników z systemu operacyjnego.
  
Aby uzyskać więcej informacji na temat zgody, zobacz temat [uprawnienia i zgoda dotyczące programu v 2.0](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Nazwa użytkownika/hasło

MSAL obsługuje [uprawnienie do uwierzytelniania poświadczeń hasła właściciela zasobu OAuth 2](v2-oauth-ropc.md), które umożliwia aplikacji Logowanie użytkownika przez bezpośrednią obsługę hasła. W aplikacji komputerowej można użyć przepływu nazwy użytkownika/hasła w celu odzyskania tokenu w trybie dyskretnym. W przypadku korzystania z aplikacji nie jest wymagany żaden interfejs użytkownika.

![Diagram przepływu nazwy użytkownika/hasła](media/msal-authentication-flows/username-password.png)

Na powyższym diagramie aplikacja:

1. Uzyskuje token, wysyłając nazwę użytkownika i hasło do dostawcy tożsamości.
2. Wywołuje interfejs API sieci Web przy użyciu tokenu.

> [!WARNING]
> Ten przepływ nie jest zalecany. Wymaga wysokiego stopnia zaufania i narażenia użytkownika.  Tego przepływu należy używać tylko wtedy, gdy inne, bezpieczniejsze, nie można używać przepływów. Aby uzyskać więcej informacji, zobacz [co to jest rozwiązanie w przypadku rosnącego problemu z hasłami?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Preferowany przepływ do uzyskiwania tokenu w trybie dyskretnym na komputerach przyłączonych do domeny systemu Windows jest [zintegrowanym uwierzytelnianiem systemu Windows](#integrated-windows-authentication). W przeciwnym razie można również użyć [przepływu kodu urządzenia](#device-code).

Chociaż jest to przydatne w niektórych przypadkach (scenariusze DevOps), jeśli chcesz używać nazwy użytkownika/hasła w scenariuszach interaktywnych, w których udostępniasz własny interfejs użytkownika, spróbuj go uniknąć. Za pomocą nazwy użytkownika/hasła:
- Użytkownicy wymagający uwierzytelniania wieloskładnikowego nie będą mogli się zalogować (w przypadku braku interakcji).
- Użytkownicy nie będą mogli korzystać z logowania jednokrotnego.

### <a name="constraints"></a>Ograniczenia

Poza [zintegrowanymi ograniczeniami uwierzytelniania systemu Windows](#integrated-windows-authentication)obowiązują również następujące ograniczenia:

- Przepływ nazwy użytkownika/hasła nie jest zgodny z dostępem warunkowym i uwierzytelnianiem wieloskładnikowym. W związku z tym, jeśli aplikacja działa w dzierżawie usługi Azure AD, w której Administrator dzierżawy wymaga uwierzytelniania wieloskładnikowego, nie można użyć tego przepływu. Wiele organizacji to.
- Działa tylko w przypadku kont służbowych (nie kont Microsoft).
- Przepływ jest dostępny w programie .NET Desktop i .NET Core, ale nie na platforma uniwersalna systemu Windows.

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C szczegóły

Aby uzyskać więcej informacji na temat korzystania z MSAL.NET i Azure AD B2C, zobacz [using ROPC with Azure AD B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
