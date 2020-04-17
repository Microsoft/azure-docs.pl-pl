---
title: Przepływy uwierzytelniania platformy tożsamości firmy Microsoft & scenariuszach aplikacji | Azure
description: Dowiedz się więcej o scenariuszach aplikacji dla platformy tożsamości firmy Microsoft, w tym o uwierzytelnieniu tożsamości, pobieraniu tokenów i wywoływaniu chronionych interfejsów API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: bf47a352542be0ad4467ed420cc38f62da0d47dc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534886"
---
# <a name="authentication-flows-and-application-scenarios"></a>Przepływy uwierzytelniania i scenariusze aplikacji

Punkt końcowy platformy tożsamości firmy Microsoft (w wersji 2.0) obsługuje uwierzytelnianie dla różnych rodzajów nowoczesnych architektur aplikacji. Wszystkie architektury są oparte na standardowych protokołach branżowych [OAuth 2.0 i OpenID Connect](active-directory-v2-protocols.md).  Korzystając z [bibliotek uwierzytelniania platformy tożsamości firmy Microsoft,](reference-v2-libraries.md)aplikacje uwierzytelniają tożsamości i uzyskują tokeny w celu uzyskania dostępu do chronionych interfejsów API.

W tym artykule opisano przepływy uwierzytelniania i scenariusze aplikacji, w których są używane:

- [Scenariusze aplikacji i obsługiwane przepływy uwierzytelniania](#scenarios-and-supported-authentication-flows).
- [Scenariusze aplikacji oraz obsługiwane platformy i języki](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Kategorie aplikacji

Tokeny można uzyskać z kilku typów aplikacji, w tym:

- Aplikacje internetowe
- Aplikacji mobilnych
- Aplikacje klasyczne
- Interfejsy API sieci Web

Tokeny można również uzyskać z aplikacji działających na urządzeniach, które nie mają przeglądarki lub są uruchomione na IoT.

Aplikacje można podzielić na następujące kategorie:

- [Chronione zasoby a aplikacje klienckie:](#protected-resources-vs-client-applications)Niektóre scenariusze dotyczą ochrony zasobów, takich jak aplikacje sieci web lub internetowe interfejsy API. Inne scenariusze dotyczą nabycia tokenu zabezpieczającego w celu wywołania chronionego interfejsu API sieci web.
- [Z użytkownikami lub bez użytkowników:](#with-users-or-without-users)Niektóre scenariusze obejmują zalogowanego użytkownika, ale inne, takie jak scenariusze demonów, nie dotyczą użytkownika.
- [Jednostronicowe, publiczne i poufne aplikacje klienckie:](#single-page-public-client-and-confidential-client-applications)Te typy są trzy duże kategorie aplikacji. Każdy z nich jest używany z różnymi bibliotekami i obiektami.
- [Grupa odbiorców logowania:](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types)dostępne przepływy uwierzytelniania różnią się w zależności od grupy odbiorców logowania. Niektóre przepływy są dostępne tylko dla kont służbowych lub szkolnych. Niektóre z nich są dostępne zarówno dla kont służbowych lub szkolnych, jak i osobistych kont Microsoft. Dozwolona grupa odbiorców zależy od przepływów uwierzytelniania.
- [Obsługiwane przepływy OAuth 2.0:](#scenarios-and-supported-authentication-flows)Przepływy uwierzytelniania są używane do implementowania scenariuszy aplikacji, które żądają tokenów. Nie ma mapowania jeden do jednego między scenariuszami aplikacji i przepływów uwierzytelniania.
- [Obsługiwane platformy:](#scenarios-and-supported-platforms-and-languages)Nie wszystkie scenariusze aplikacji są dostępne dla każdej platformy.

### <a name="protected-resources-vs-client-applications"></a>Chronione zasoby a aplikacje klienckie

Scenariusze uwierzytelniania obejmują dwa działania:

- **Pobieranie tokenów zabezpieczających dla chronionego interfejsu API sieci Web:** Zaleca się używanie [bibliotek klienckich obsługiwanych przez](reference-v2-libraries.md#microsoft-supported-client-libraries) firmę Microsoft do uzyskiwania tokenów, w szczególności z rodziny biblioteki uwierzytelniania firmy Microsoft (MSAL).
- **Ochrona interfejsu API sieci Web lub aplikacji sieci web:** Jednym z wyzwań związanych z ochroną interfejsu API sieci Web lub zasobu aplikacji sieci Web jest sprawdzanie poprawności tokenu zabezpieczającego. Na niektórych platformach firma Microsoft oferuje [biblioteki oprogramowania pośredniczącego.](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)

### <a name="with-users-or-without-users"></a>Z użytkownikami lub bez użytkowników

Większość scenariuszy uwierzytelniania uzyskuje tokeny w imieniu zalogowanych użytkowników.

![Scenariusze z użytkownikami](media/scenarios/scenarios-with-users.svg)

Istnieją jednak również scenariusze demona-aplikacji, w których aplikacje pozyskują tokeny w imieniu siebie bez użytkownika.

![Scenariusze z aplikacjami demonów](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Jednostronicowe, publiczne i poufne aplikacje klienckie

Tokeny zabezpieczające można uzyskać z wielu typów aplikacji. Aplikacje te są zazwyczaj podzielone na trzy kategorie:

- **Aplikacje jednostronicowe**: Te aplikacje, znane również jako OSO, to aplikacje internetowe, w których tokeny są pobierane z aplikacji JavaScript lub TypeScript działającej w przeglądarce. Wiele nowoczesnych aplikacji ma jednostronicowy fronton aplikacji, który jest napisany głównie w javascript. Aplikacja często używa struktury, takich jak Angular, React lub Vue. MSAL.js jest jedyną biblioteką uwierzytelniania firmy Microsoft, która obsługuje aplikacje jednostronicowe.

- **Publiczne aplikacje klienckie**: Te aplikacje zawsze logują się do użytkowników:
  - Aplikacje klasyczne wywołujące internetowe interfejsy API w imieniu zalogowanego użytkownika
  - Aplikacji mobilnych
  - Aplikacje działające na urządzeniach, które nie mają przeglądarki, takich jak te uruchomione na iOT

  Te aplikacje są reprezentowane przez MSAL [PublicClientApplication](/dotnet/api/microsoft.identity.client.publicclientapplication) klasy. Aby uzyskać więcej informacji, zobacz [Publiczne aplikacje klienckie i poufne](msal-client-applications.md).

- **Poufne aplikacje klienckie:**
  - Aplikacje sieci Web wywołujące internetowy interfejs API
  - Interfejsy API sieci Web wywołujące internetowy interfejs API
  - Aplikacje demonów, nawet jeśli są realizowane jako usługa konsoli, taka jak demon Linuksa lub usługa Windows

  Tego typu aplikacje używają [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplication) klasy. Aby uzyskać więcej informacji, zobacz [Publiczne aplikacje klienckie i poufne](msal-client-applications.md).

## <a name="application-scenarios"></a>Scenariusze aplikacji

Punkt końcowy platformy tożsamości firmy Microsoft obsługuje uwierzytelnianie dla różnych architektur aplikacji:

- Aplikacje jednostronicowe
- Aplikacje internetowe
- Interfejsy API sieci Web
- Aplikacji mobilnych
- Aplikacje natywne
- Aplikacje demona
- Aplikacje po stronie serwera

Aplikacje używają różnych przepływów uwierzytelniania do logowania użytkowników i uzyskania tokenów do wywoływania chronionych interfejsów API.

### <a name="a-single-page-application"></a>Aplikacja jednostronicowa

Wiele nowoczesnych aplikacji internetowych są tworzone jako aplikacje po stronie klienta jednostronicowe. Te aplikacje korzystające z języka JavaScript lub jednostronicowej struktury aplikacji, takich jak Angular, Vue.js i React.js. Te aplikacje działają w przeglądarce internetowej.

Ich cechy uwierzytelniania różnią się od tradycyjnych aplikacji sieci Web po stronie serwera. Korzystając z platformy tożsamości firmy Microsoft, aplikacje jednostronicowe mogą logować się do użytkowników i uzyskiwać tokeny, aby uzyskać dostęp do usług zaplecza lub internetowych interfejsów API.

![Aplikacja jednostronicowa](media/scenarios/spa-app.svg)

Aby uzyskać więcej informacji, zobacz [Aplikacje jednostronicowe](scenario-spa-overview.md).

### <a name="a-web-app-that-is-signing-in-a-user"></a>Aplikacja internetowa logująca się do użytkownika

![Aplikacja internetowa, która loguje się do użytkownika](media/scenarios/scenario-webapp-signs-in-users.svg)

Aby chronić aplikację sieci web, która loguje się do użytkownika:

- Jeśli program jest rozwijany w sieci .NET, używasz ASP.NET lub ASP.NET Core z oprogramowaniem pośredniczącym ASP.NET Open ID Connect. Ochrona zasobu obejmuje sprawdzanie poprawności tokenu zabezpieczającego, który jest wykonywany przez [rozszerzenia IdentityModel dla biblioteki .NET,](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) a nie bibliotek MSAL.

- Jeśli rozwijasz się w node.js, użyj Passport.js.

Aby uzyskać więcej informacji, zobacz [Aplikacja sieci Web, która loguje się do użytkowników](scenario-web-app-sign-user-overview.md).

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Aplikacja internetowa, która loguje się do użytkownika i wywołuje internetowy interfejs API w imieniu użytkownika

![Aplikacje sieci Web wywołujące internetowe interfejsy API](media/scenarios/web-app.svg)

Aby wywołać internetowy interfejs API z aplikacji sieci web w imieniu użytkownika, należy użyć klasy MSAL **ConfidentialClientApplication.** Użyj przepływu kodu autoryzacji i przechowywania nabytych tokenów w pamięci podręcznej tokenu. W razie potrzeby msal odświeża tokeny i kontroler dyskretnie uzyskuje tokeny z pamięci podręcznej.

Aby uzyskać więcej informacji, zobacz [Aplikacja internetowa wywołująca internetowe interfejsy API](scenario-web-app-call-api-overview.md).

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>Aplikacja klasyczna wywołująca internetowy interfejs API w imieniu zalogowanego użytkownika

Aby aplikacja klasyczna do wywołania internetowego interfejsu API, który loguje się do użytkowników, użyj interaktywnych metod pozyskiwania tokenów klasy MSAL **PublicClientApplication.** Za pomocą tych interaktywnych metod można kontrolować środowisko interfejsu użytkownika logowania. Msal używa przeglądarki internetowej do tej interakcji.

![Aplikacja klasyczna wywołująca internetowy interfejs API](media/scenarios/desktop-app.svg)

Istnieje inna możliwość dla aplikacji hostowanych w systemie Windows na komputerach przyłączonych do domeny systemu Windows lub usługi Azure Active Directory (Azure AD). Te aplikacje mogą po cichu uzyskać token przy użyciu [zintegrowanego uwierzytelniania systemu Windows](https://aka.ms/msal-net-iwa).

Aplikacje działające na urządzeniu bez przeglądarki mogą nadal wywoływać interfejs API w imieniu użytkownika. Aby uwierzytelnić, użytkownik musi zalogować się na innym urządzeniu z przeglądarką sieci Web. W tym scenariuszu wymaga użycia [przepływu kodu urządzenia](https://aka.ms/msal-net-device-code-flow).

![Przepływ kodu urządzenia](media/scenarios/device-code-flow-app.svg)

Chociaż nie zalecamy korzystania z niego, [przepływ nazwy użytkownika/hasła](https://aka.ms/msal-net-up) jest dostępny w publicznych aplikacjach klienckich. Ten przepływ jest nadal potrzebne w niektórych scenariuszach, takich jak DevOps.

Ale za pomocą tego przepływu ogranicza aplikacje. Na przykład aplikacje nie można zalogować użytkownika, który musi używać uwierzytelniania wieloskładnikowego lub dostępu warunkowego. Aplikacje również nie korzystają z logowania jednokrotnego.

Uwierzytelnianie za pomocą przepływu nazwy użytkownika/hasła jest sprzeczne z zasadami nowoczesnego uwierzytelniania i jest dostarczane tylko ze starszych powodów.

W aplikacjach klasycznych, jeśli chcesz, aby pamięć podręczna tokenów się utrwaliła, dostosuj [serializację pamięci podręcznej tokenu](https://aka.ms/msal-net-token-cache-serialization). Implementując [serializację pamięci podręcznej dwóch tokenów,](https://aka.ms/msal-net-dual-cache-serialization)można użyć pamięci podręcznych tokenów zgodnych z do tyłu i zgodnych z przyszłością. Tokeny te obsługują poprzednie generacje bibliotek uwierzytelniania. Określone biblioteki obejmują bibliotekę uwierzytelniania usługi Azure AD dla platformy .NET (ADAL.NET) w wersji 3 i 4.

Aby uzyskać więcej informacji, zobacz [Aplikacja klasyczna, która wywołuje internetowe interfejsy API](scenario-desktop-overview.md).

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>Aplikacja mobilna wywołująca internetowy interfejs API w imieniu interaktywnego użytkownika

Podobnie jak w przypadku aplikacji klasycznej, aplikacja mobilna wywołuje interaktywne metody pozyskiwania tokenów klasy MSAL **PublicClientApplication,** aby uzyskać token do wywoływania internetowego interfejsu API.

![Aplikacja mobilna wywołująca internetowy interfejs API](media/scenarios/mobile-app.svg)

MSAL iOS i MSAL Android domyślnie korzystają z systemowej przeglądarki internetowej. Można jednak skierować je do korzystania z osadzonego widoku sieci Web. Istnieją specyfiki, które zależą od platformy mobilnej: platforma uniwersalna systemu Windows (PLATFORMY UNIWERSALNEJ SYSTEMU WINDOWS), iOS lub Android.

Niektóre scenariusze, takie jak te, które obejmują dostęp warunkowy związane z identyfikatorem urządzenia lub rejestracji urządzenia, wymagają brokera, który ma być zainstalowany na urządzeniu. Przykładami brokerów są Microsoft Company Portal w systemie Android i Microsoft Authenticator w systemach Android i iOS. MSAL może teraz wchodzić w interakcje z brokerami. Aby uzyskać więcej informacji, zobacz [Wykorzystywanie brokerów w systemach Android i iOS](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS).

Aby uzyskać więcej informacji, zobacz [Aplikacja mobilna, która wywołuje internetowe interfejsy API](scenario-mobile-overview.md).

> [!NOTE]
> Aplikacja mobilna korzystająca z pliku MSAL.iOS, MSAL. Android lub MSAL.NET na xamarin może mieć zasady ochrony aplikacji stosowane do niego. Na przykład zasady mogą uniemożliwić użytkownikowi kopiowanie chronionego tekstu. Aplikacja mobilna jest zarządzana przez usługę Intune i rozpoznawana przez usługę Intune jako aplikacja zarządzana. Aby uzyskać więcej informacji, zobacz [Omówienie zestawie SDK aplikacji usługi Microsoft Intune](https://docs.microsoft.com/intune/app-sdk).
>
> Zestaw [SDK aplikacji usługi Intune](https://docs.microsoft.com/intune/app-sdk-get-started) jest oddzielony od bibliotek MSAL i samodzielnie współdziała z usługą Azure AD.

### <a name="a-protected-web-api"></a>Chroniony internetowy interfejs API

Punktu końcowego platformy tożsamości firmy Microsoft można używać do zabezpieczania usług sieci web, takich jak interfejs API sieci Web restful aplikacji. Chroniony interfejs API sieci web jest wywoływany przy użyciu tokenu dostępu. Token zabezpiecza dane interfejsu API i uwierzytelnia przychodzące żądania. Wywołujący internetowy interfejs API dołącza token dostępu w nagłówku autoryzacji żądania HTTP.

Jeśli chcesz chronić ASP.NET lub ASP.NET core interfejsu API sieci web, należy sprawdzić poprawność tokenu dostępu. W tym przypadku należy użyć ASP.NET oprogramowania pośredniczącego JWT. Sprawdzanie poprawności odbywa się przez [rozszerzenia IdentityModel dla](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) biblioteki .NET, a nie przez MSAL.NET.

Aby uzyskać więcej informacji, zobacz [Chroniony interfejs API sieci Web](scenario-protected-web-api-overview.md).

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>Internetowy interfejs API wywołujący inny internetowy interfejs API w imieniu użytkownika

Aby interfejs API sieci web chroniony ASP.NET lub ASP.NET Core wywoływał inny internetowy interfejs API w imieniu użytkownika, aplikacja musi uzyskać token dla podrzędnego interfejsu API sieci web. Aby uzyskać token, aplikacja wywołuje **ConfidentialClientApplication** klasy [AcquireTokenOnBehalfOf](https://aka.ms/msal-net-on-behalf-of) metody. Takie wywołania są również nazywane *wywołania usługi do usług.* Interfejsy API sieci Web, które wywołują inne internetowe interfejsy API, muszą zapewniać niestandardową serializację pamięci podręcznej.

  ![Internetowy interfejs API wywołujący inny internetowy interfejs API](media/scenarios/web-api.svg)

Aby uzyskać więcej informacji, zobacz [Internetowy interfejs API, który wywołuje internetowe interfejsy API](scenario-web-api-call-api-overview.md).

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>Aplikacja demona wywołująca internetowy interfejs API w nazwie demona

Aplikacje, które mają długotrwałe procesy lub działają bez interakcji z użytkownikiem, również potrzebują sposobu dostępu do bezpiecznych interfejsów API sieci Web. Taka aplikacja może uwierzytelniać i uzyskać tokeny przy użyciu tożsamości aplikacji. Aplikacja potwierdza swoją tożsamość przy użyciu klucza tajnego klienta lub certyfikatu.

Można napisać takie aplikacje demona, które nabywają token dla aplikacji wywołującej przy użyciu MSAL **ConfidentialClientApplication** klasy klientów metody pozyskiwania [poświadczeń.](https://aka.ms/msal-net-client-credentials) Te metody wymagają, aby aplikacja wywołująca zarejestrowała klucz tajny w usłudze Azure AD. Następnie aplikacja udostępnia klucz tajny z wywoływanym demonem. Przykłady takich wpisów tajnych obejmują hasła aplikacji, potwierdzenia certyfikatu lub potwierdzenia klienta.

![Aplikacja demona wywoływana przez inne aplikacje i interfejsy API](media/scenarios/daemon-app.svg)

Aby uzyskać więcej informacji, zobacz [Aplikacja demona, która wywołuje internetowe interfejsy API](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scenariusze i obsługiwane przepływy uwierzytelniania

Scenariusze, które obejmują pobieranie tokenów również mapować do przepływów uwierzytelniania OAuth 2.0. Aby uzyskać więcej informacji, zobacz [protokoły OAuth 2.0 i OpenID Connect na platformie tożsamości firmy Microsoft](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Scenariusz</th> <th>Szczegółowy scenariusz</th> <th>OAuth 2.0 przepływ i dotacja</th> <th>Grupy odbiorców</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Aplikacja jednostronicowa</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Niejawne</a></td>
   <td>Konta służbowe, konta osobiste i usługa Microsoft Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Aplikacja internetowa, która loguje się do użytkowników</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Kod autoryzacji</a></td>
   <td>Konta służbowe, konta osobiste i usługa Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Aplikacja internetowa, która wywołuje internetowe interfejsy API</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Kod autoryzacji</a></td>
   <td>Konta służbowe, konta osobiste i usługa Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Aplikacja klasyczna, która wywołuje internetowe interfejsy API</a></td>
   <td>Interaktywna za pomocą <a href="v2-oauth2-auth-code-flow.md">kodu autoryzacji</a> z PKCE</td>
   <td>Konta służbowe, konta osobiste i usługa Azure AD B2C</td>
 </tr>

  <tr>
   <td>Zintegrowany system Windows Auth</td>
   <td>Konta służbowe</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Hasło właściciela zasobu</a></td>
   <td>Konta służbowe i usługa Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Kod urządzenia</a></td>
   <td>Konta służbowe</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Aplikacja mobilna, która wywołuje internetowe interfejsy API</a></td>
   <td>Interaktywna za pomocą <a href="v2-oauth2-auth-code-flow.md">kodu autoryzacji</a> z PKCE</td>
   <td>Konta służbowe, konta osobiste i usługa Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Hasło właściciela zasobu</a></td>
   <td>Konta służbowe i usługa Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">Aplikacja demona, która wywołuje internetowe interfejsy API</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Poświadczenia klienta</a></td>
   <td>Uprawnienia tylko do aplikacji bez użytkownika i używane tylko w organizacjach usługi Azure AD</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Internetowy interfejs API, który wywołuje internetowe interfejsy API</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">Zastępczy</a></td>
   <td>Konta służbowe i osobiste</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scenariusze i obsługiwane platformy i języki

Biblioteki uwierzytelniania firmy Microsoft obsługują wiele platform:

- JavaScript
- .NET Framework
- .NET Core
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android
- Natywny iOS
- macOS
- Natywny system Android
- Java
- Python

Do tworzenia aplikacji można również używać różnych języków.

> [!NOTE]
> Niektóre typy aplikacji nie są dostępne na każdej platformie.

W kolumnie systemu Windows w poniższej tabeli za każdym razem .NET Core jest również możliwe. Ten ostatni jest pomijany, aby uniknąć zaśmiecania tabeli.

|Scenariusz  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Aplikacja jednostronicowa](scenario-spa-overview.md) <br/>[![Aplikacja jednostronicowa](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Aplikacja internetowa z możliwością logowania użytkowników](scenario-web-app-sign-user-overview.md) <br/>[![Aplikacja sieci Web, która loguje się do użytkowników](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Aplikacja internetowa wywołująca internetowe interfejsy API](scenario-web-app-call-api-overview.md) <br/> <br/>[![Aplikacja internetowa wywołująca internetowe interfejsy API](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET rdzeń + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MĘTÓW MSAL](media/sample-v2-code/small_logo_python.png)<br/>Kolba + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET rdzeń + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MĘTÓW MSAL](media/sample-v2-code/small_logo_python.png)<br/>Kolba + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET rdzeń + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MĘTÓW MSAL](media/sample-v2-code/small_logo_python.png)<br/>Kolba + MSAL Python
| [Aplikacja klasyczna wywołująca internetowe interfejsy API](scenario-desktop-overview.md) <br/> <br/>Aplikacja klasyczna, która wywołuje przepływ kodu urządzenia ![interfejsów API sieci [ ![Web](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md)](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MĘTÓW MSAL](media/sample-v2-code/small_logo_python.png)<br/>MĘTÓW MSAL| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MĘTÓW MSAL](media/sample-v2-code/small_logo_python.png)<br/>MĘTÓW MSAL| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MĘTÓW MSAL](media/sample-v2-code/small_logo_python.png)<br/>MĘTÓW MSAL <br/> ![iOS / Cel C lub swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc |
| [Aplikacja mobilna wywołująca internetowe interfejsy API](scenario-mobile-overview.md) <br/> [![Aplikacja mobilna wywołująca internetowe interfejsy API](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![Platforma UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS / Cel C lub swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Aplikacja demona](scenario-daemon-overview.md) <br/> [![Aplikacja demona](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MĘTÓW MSAL](media/sample-v2-code/small_logo_python.png)<br/>MĘTÓW MSAL| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MĘTÓW MSAL](media/sample-v2-code/small_logo_python.png)<br/>MĘTÓW MSAL| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MĘTÓW MSAL](media/sample-v2-code/small_logo_python.png)<br/>MĘTÓW MSAL
| [Internetowy interfejs API wywołujący internetowe interfejsy API](scenario-web-api-call-api-overview.md) <br/><br/> [![Internetowy interfejs API wywołujący internetowe interfejsy API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET rdzeń + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MĘTÓW MSAL](media/sample-v2-code/small_logo_python.png)<br/>MĘTÓW MSAL| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET rdzeń + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MĘTÓW MSAL](media/sample-v2-code/small_logo_python.png)<br/>MĘTÓW MSAL| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET rdzeń + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MĘTÓW MSAL](media/sample-v2-code/small_logo_python.png)<br/>MĘTÓW MSAL

Aby uzyskać więcej informacji, zobacz [Biblioteki obsługiwane przez firmę Microsoft według systemu operacyjnego / języka](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [podstawach uwierzytelniania](authentication-scenarios.md) i [tokenach dostępu do platformy tożsamości firmy Microsoft.](access-tokens.md)
* Dowiedz się więcej o [zabezpieczaniu dostępu do aplikacji IoT](/azure/architecture/example-scenario/iot-aad/iot-aad).
