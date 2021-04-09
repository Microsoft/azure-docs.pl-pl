---
title: Microsoft Identity platform Authentication — przepływy & scenariusze aplikacji | Azure
description: Poznaj scenariusze aplikacji dla platformy tożsamości firmy Microsoft, w tym uwierzytelnianie tożsamości, uzyskiwanie tokenów i wywoływanie chronionych interfejsów API.
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
ms.openlocfilehash: ffa52805a5e2680d534b2b24a210465cb3fc7cac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100557874"
---
# <a name="authentication-flows-and-application-scenarios"></a>Przepływy uwierzytelniania i scenariusze aplikacji

Platforma tożsamości firmy Microsoft obsługuje uwierzytelnianie różnych rodzajów nowoczesnych architektur aplikacji. Wszystkie architektury są oparte na standardach branżowych protokołu [OAuth 2,0 i OpenID Connect Connect](active-directory-v2-protocols.md). Korzystając z [bibliotek uwierzytelniania platformy tożsamości firmy Microsoft](reference-v2-libraries.md), aplikacje uwierzytelniają tożsamości i uzyskują tokeny dostępu do chronionych interfejsów API.

W tym artykule opisano przepływy uwierzytelniania i scenariusze aplikacji, które są używane w programie.

## <a name="application-categories"></a>Kategorie aplikacji

Tokeny mogą być uzyskiwane z kilku typów aplikacji, w tym:

- Aplikacje internetowe
- Aplikacji mobilnych
- Aplikacje klasyczne
- Interfejsy API sieci Web

Tokeny mogą być również uzyskiwane przez aplikacje działające na urządzeniach, które nie mają przeglądarki lub działają na Internet rzeczy (IoT).

W poniższych sekcjach opisano kategorie aplikacji.

### <a name="protected-resources-vs-client-applications"></a>Chronione zasoby a aplikacje klienckie

Scenariusze uwierzytelniania obejmują dwa działania:

- **Uzyskiwanie tokenów zabezpieczających dla chronionego internetowego interfejsu API**: zalecamy używanie [biblioteki uwierzytelniania firmy Microsoft (MSAL)](reference-v2-libraries.md)opracowanej i obsługiwanej przez firmę Microsoft.
- **Ochrona internetowego interfejsu API lub aplikacji sieci Web**: jednym wyzwaniem chroniącym te zasoby jest Weryfikowanie tokenu zabezpieczającego. Na niektórych platformach firma Microsoft oferuje [biblioteki oprogramowania pośredniczącego](reference-v2-libraries.md).

### <a name="with-users-or-without-users"></a>Z użytkownikami lub bez użytkowników

Większość scenariuszy uwierzytelniania uzyskuje tokeny w imieniu zalogowanych użytkowników.

![Scenariusze z użytkownikami](media/scenarios/scenarios-with-users.svg)

Istnieją jednak również aplikacje demona. W tych scenariuszach aplikacje uzyskują tokeny tylko w imieniu użytkownika.

![Scenariusze z aplikacjami demona](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Pojedyncze strony, klient publiczny i poufne aplikacje klienckie

Tokeny zabezpieczające mogą być uzyskiwane przez wiele typów aplikacji. Te aplikacje są podzielone na następujące trzy kategorie. Każdy z nich jest używany z różnymi bibliotekami i obiektami.

- **Aplikacje jednostronicowe**: znane również jako aplikacji jednostronicowych, są to aplikacje sieci Web, w których tokeny są uzyskiwane przez aplikację JavaScript lub TypeScript działającą w przeglądarce. Wiele nowoczesnych aplikacji ma aplikację jednostronicową, która jest przede wszystkim zapisywana w języku JavaScript. Aplikacja często używa struktury, takiej jak kątowy, reaguje lub Vue. MSAL.js jest jedyną biblioteką uwierzytelniania firmy Microsoft, która obsługuje aplikacje jednostronicowe.

- **Publiczne aplikacje klienckie**: aplikacje w tej kategorii, takie jak następujące typy, zawsze logują się użytkowników:
  - Aplikacje klasyczne, które wywołują interfejsy API sieci Web w imieniu zalogowanych użytkowników
  - Aplikacji mobilnych
  - Aplikacje działające na urządzeniach, które nie mają przeglądarki, takie jak procesy działające na IoT

- **Poufne aplikacje klienckie**: aplikacje w tej kategorii obejmują:
  - Aplikacje sieci Web wywołujące interfejs API sieci Web
  - Interfejsy API sieci Web wywołujące interfejs API sieci Web
  - Aplikacje demona, nawet w przypadku zaimplementowania jako usługa konsolowa, taka jak demon systemu Linux lub usługa Windows

### <a name="sign-in-audience"></a>Logowanie odbiorców

Dostępne przepływy uwierzytelniania różnią się w zależności od odbiorców logowania. Niektóre przepływy są dostępne tylko dla kont służbowych. Inne są dostępne zarówno dla kont służbowych, jak i dla osobistych kont Microsoft.

Aby uzyskać więcej informacji, zobacz [obsługiwane typy kont](v2-supported-account-types.md#account-type-support-in-authentication-flows).

## <a name="application-scenarios"></a>Scenariusze aplikacji

Platforma tożsamości firmy Microsoft obsługuje uwierzytelnianie dla następujących architektur aplikacji:

- Aplikacje jednostronicowe
- Aplikacje internetowe
- Interfejsy API sieci Web
- Aplikacji mobilnych
- Aplikacje natywne
- Aplikacje demona
- Aplikacje po stronie serwera

Aplikacje używają różnych przepływów uwierzytelniania do logowania użytkowników i uzyskiwania tokenów do wywoływania chronionych interfejsów API.

### <a name="single-page-application"></a>Aplikacja jednostronicowa

Wiele nowoczesnych aplikacji sieci Web jest zbudowanych jako aplikacje jednostronicowe po stronie klienta. Aplikacje te używają języka JavaScript lub struktury, takich jak kątowy, Vue i reagują. Aplikacje te działają w przeglądarce internetowej.

Aplikacje jednostronicowe różnią się od tradycyjnych aplikacji sieci Web po stronie serwera pod kątem cech uwierzytelniania. Korzystając z platformy tożsamości firmy Microsoft, aplikacje jednostronicowe mogą logować użytkowników i uzyskiwać tokeny umożliwiające dostęp do usług zaplecza lub interfejsów API sieci Web. Platforma tożsamości firmy Microsoft oferuje dwa typy dotacji dla aplikacji JavaScript:

| MSAL.js (2. x) | MSAL.js (1. x) |
|---|---|
| ![Uwierzytelnianie aplikacji jednostronicowej](media/scenarios/spa-app-auth.svg) | ![Niejawna aplikacja jednostronicowa](media/scenarios/spa-app.svg) |

### <a name="web-app-that-signs-in-a-user"></a>Aplikacja sieci Web, która loguje się do użytkownika

![Aplikacja sieci Web, która loguje się do użytkownika](media/scenarios/scenario-webapp-signs-in-users.svg)

Aby pomóc w ochronie aplikacji sieci Web, która jest oznaką użytkownika:

- W przypadku tworzenia oprogramowania w programie .NET należy używać ASP.NET lub ASP.NET Core z programem ASP.NET OpenID Connect Connect. Ochrona zasobu polega na weryfikacji tokenu zabezpieczającego, który jest wykonywany przez [rozszerzenia IdentityModel dla platformy .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) , a nie MSAL.

- W przypadku tworzenia w Node.js należy użyć [węzła MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) lub [Passport.js](https://github.com/AzureAD/passport-azure-ad).

Aby uzyskać więcej informacji, zobacz [aplikacja sieci Web, która oznacza użytkowników](scenario-web-app-sign-user-overview.md).

### <a name="web-app-that-signs-in-a-user-and-calls-a-web-api-on-behalf-of-the-user"></a>Aplikacja sieci Web, która loguje się do użytkownika i wywołuje internetowy interfejs API w imieniu użytkownika

![Aplikacja sieci Web wywołująca interfejsy API sieci Web](media/scenarios/web-app.svg)

Aby wywołać interfejs API sieci Web z aplikacji sieci Web w imieniu użytkownika, użyj przepływu kodu autoryzacji i Przechowaj uzyskane tokeny w pamięci podręcznej tokenów. W razie konieczności MSAL odświeża tokeny i kontroler dyskretnie uzyskuje tokeny z pamięci podręcznej.

Aby uzyskać więcej informacji, zobacz [aplikacja sieci Web, która wywołuje interfejsy API sieci Web](scenario-web-app-call-api-overview.md).

### <a name="desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user"></a>Aplikacja klasyczna, która wywołuje internetowy interfejs API w imieniu zalogowanego użytkownika

Aby aplikacja klasyczna wywoływała interfejs API sieci Web, który loguje użytkowników, użyj interaktywnych metod pozyskiwania tokenów MSAL. Za pomocą tych interaktywnych metod można kontrolować środowisko logowania użytkownika. MSAL używa przeglądarki sieci Web dla tej interakcji.

![Aplikacja klasyczna wywołująca internetowy interfejs API](media/scenarios/desktop-app.svg)

Istnieje inna możliwość dla aplikacji hostowanych w systemie Windows na komputerach przyłączonych do domeny systemu Windows lub przez Azure Active Directory (Azure AD). Te aplikacje mogą w trybie dyskretnym uzyskać token przy użyciu [zintegrowanego uwierzytelniania systemu Windows](https://aka.ms/msal-net-iwa).

Aplikacje działające na urządzeniu bez przeglądarki nadal mogą wywołać interfejs API w imieniu użytkownika. W celu uwierzytelnienia użytkownik musi zalogować się na innym urządzeniu z przeglądarką sieci Web. Ten scenariusz wymaga użycia [przepływu kodu urządzenia](https://aka.ms/msal-net-device-code-flow).

![Przepływ kodu urządzenia](media/scenarios/device-code-flow-app.svg)

Chociaż nie zalecamy ich używania, [przepływ nazwy użytkownika/hasła](scenario-desktop-acquire-token.md#username-and-password) jest dostępny w publicznych aplikacjach klienckich. Ten przepływ jest nadal wymagany w niektórych scenariuszach, takich jak DevOps.

Użycie przepływu nazwy użytkownika/hasła ogranicza Twoje aplikacje. Na przykład aplikacje nie mogą logować się do użytkownika, który musi korzystać z uwierzytelniania wieloskładnikowego lub narzędzia dostępu warunkowego w usłudze Azure AD. Aplikacje nie korzystają również z logowania jednokrotnego. Uwierzytelnianie za pomocą przepływu nazwy użytkownika/hasła odbywa się w oparciu o zasady nowoczesnego uwierzytelniania i jest dostarczane tylko ze względu na starsze przyczyny.

Jeśli chcesz, aby pamięć podręczna tokenów była trwała w aplikacjach klasycznych, możesz dostosować [serializację pamięci podręcznej tokenu](scenario-desktop-acquire-token.md#file-based-token-cache). Przez implementację [serializacji pamięci podręcznej podwójnego tokenu](scenario-desktop-acquire-token.md#dual-token-cache-serialization-msal-unified-cache--adal-v3)można używać pamięci podręcznej tokenów zgodnych z poprzednimi wersjami i przesyłania dalej. Te tokeny obsługują poprzednie generacji bibliotek uwierzytelniania. Do określonych bibliotek należą Biblioteka Azure AD Authentication Library for .NET (ADAL.NET) w wersji 3 i 4.

Aby uzyskać więcej informacji, zobacz [aplikacji klasycznej, która wywołuje interfejsy API sieci Web](scenario-desktop-overview.md).

### <a name="mobile-app-that-calls-a-web-api-on-behalf-of-an-interactive-user"></a>Aplikacja mobilna, która wywołuje internetowy interfejs API w imieniu użytkownika interaktywnego

Podobnie jak w przypadku aplikacji klasycznej, aplikacja mobilna wywołuje interaktywne metody pozyskiwania tokenów MSAL, aby uzyskać token do wywoływania interfejsu API sieci Web.

![Aplikacja mobilna wywołująca internetowy interfejs API](media/scenarios/mobile-app.svg)

MSAL iOS i MSAL w systemie Android domyślnie korzystają z przeglądarki sieci Web. Można jednak skierować je do korzystania z osadzonego widoku sieci Web. Istnieją pewne informacje, które są zależne od platformy mobilnej: platforma uniwersalna systemu Windows (platformy UWP), iOS lub Android.

Niektóre scenariusze, takie jak te, które obejmują dostęp warunkowy związany z IDENTYFIKATORem urządzenia lub rejestracją urządzenia, wymagają, aby Broker został zainstalowany na urządzeniu. Przykłady brokerów to Microsoft Portal firmy w systemach Android i Microsoft Authenticator w systemach Android i iOS. MSAL może teraz współdziałać z brokerami. Aby uzyskać więcej informacji na temat brokerów, zobacz Korzystanie [z brokerów w systemach Android i iOS](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS).

Aby uzyskać więcej informacji, zobacz [aplikacji mobilnej, która wywołuje interfejsy API sieci Web](scenario-mobile-overview.md).

> [!NOTE]
> Aplikacja mobilna korzystająca z MSAL. iOS, MSAL. Dla systemu Android lub MSAL.NET na platformie Xamarin mogą być stosowane zasady ochrony aplikacji. Na przykład zasady mogą uniemożliwić użytkownikowi kopiowanie chronionego tekstu. Aplikacja mobilna jest zarządzana przez usługę Intune i jest rozpoznawana przez usługę Intune jako zarządzaną aplikację. Aby uzyskać więcej informacji, zobacz [Omówienie zestawu SDK aplikacji Microsoft Intune](/intune/app-sdk).
>
> [Zestaw SDK aplikacji usługi Intune](/intune/app-sdk-get-started) jest oddzielony od bibliotek MSAL i współdziała z usługą Azure AD.

### <a name="protected-web-api"></a>Chroniony internetowy interfejs API

Z punktu końcowego platformy tożsamości firmy Microsoft można korzystać w celu zabezpieczania usług sieci Web, takich jak RESTful internetowy interfejs API aplikacji. Chroniony internetowy interfejs API jest wywoływany za pomocą tokenu dostępu. Token pomaga zabezpieczyć dane interfejsu API i uwierzytelniać żądania przychodzące. Obiekt wywołujący internetowy interfejs API dołącza token dostępu w nagłówku autoryzacji żądania HTTP.

Jeśli chcesz chronić internetowy interfejs API ASP.NET lub ASP.NET Core, sprawdź poprawność tokenu dostępu. W przypadku tej weryfikacji należy użyć oprogramowania pośredniczącego JWT ASP.NET. Walidacja jest wykonywana przez [rozszerzenia IdentityModel dla biblioteki .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) , a nie przez MSAL.NET.

Aby uzyskać więcej informacji, zobacz [chroniony internetowy interfejs API](scenario-protected-web-api-overview.md).

### <a name="web-api-that-calls-another-web-api-on-behalf-of-a-user"></a>Internetowy interfejs API, który wywołuje inny internetowy interfejs API w imieniu użytkownika

Aby chroniony internetowy interfejs API mógł wywołać inny internetowy interfejs API w imieniu użytkownika, aplikacja musi uzyskać token dla podrzędnego interfejsu API sieci Web. Takie wywołania są czasami *określane jako wywołania między usługami.* Interfejsy API sieci Web wywołujące inne interfejsy API sieci Web muszą zapewnić niestandardową serializację pamięci podręcznej.

![Internetowy interfejs API wywołujący inny internetowy interfejs API](media/scenarios/web-api.svg)

Aby uzyskać więcej informacji, zobacz [internetowy interfejs API, który wywołuje interfejsy API sieci Web](scenario-web-api-call-api-overview.md).

### <a name="daemon-app-that-calls-a-web-api-in-the-daemons-name"></a>Aplikacja demona, która wywołuje interfejs API sieci Web w nazwie demona

Aplikacje, które mają długotrwałe procesy lub działają bez interakcji z użytkownikiem, muszą również mieć możliwość uzyskania dostępu do bezpiecznych interfejsów API sieci Web. Takie aplikacje mogą uwierzytelniać tokeny i uzyskiwać do nich dostęp przy użyciu tożsamości aplikacji. Aplikacja udowadnia swoją tożsamość przy użyciu klucza tajnego klienta lub certyfikatu.

Można napisać takie aplikacje demona, które uzyskują token dla aplikacji wywołującej przy użyciu metod pozyskiwania [poświadczeń klienta](scenario-daemon-acquire-token.md#acquiretokenforclient-api) w MSAL. Te metody wymagają wpisu tajnego klienta, który jest dodawany do rejestracji aplikacji w usłudze Azure AD. Aplikacja następnie udostępnia klucz tajny wywołanym demonem. Przykłady takich wpisów tajnych obejmują hasła aplikacji, potwierdzenie certyfikatu i potwierdzenie klienta.

![Aplikacja demona wywołana przez inne aplikacje i interfejsy API](media/scenarios/daemon-app.svg)

Aby uzyskać więcej informacji, zobacz [aplikacji demona, która wywołuje interfejsy API sieci Web](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scenariusze i obsługiwane przepływy uwierzytelniania

Przepływy uwierzytelniania służą do implementowania scenariuszy aplikacji, które żądają tokenów. Nie istnieje mapowanie jeden do jednego między scenariuszami aplikacji i przepływami uwierzytelniania.

Scenariusze, które obejmują pobieranie tokenów, również są mapowane na przepływy uwierzytelniania OAuth 2,0. Aby uzyskać więcej informacji, zobacz [Protokoły OAuth 2,0 i OpenID Connect Connect na platformie tożsamości firmy Microsoft](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Scenariusz</th> <th>Szczegółowy opis scenariusza</th> <th>Przepływ OAuth 2,0 i przyznawanie</th> <th>Grupy odbiorców</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Auth code" src="media/scenarios/spa-app-auth.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Aplikacja jednostronicowa</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Kod autoryzacji</a> z PKCE</td>
   <td>Konta służbowe, konta osobiste i Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Implicit" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Aplikacja jednostronicowa</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Niejawna</a></td>
   <td>Konta służbowe, konta osobiste i Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Aplikacja internetowa, która loguje użytkowników</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Kod autoryzacji</a></td>
   <td>Konta służbowe, konta osobiste i Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that calls web APIs" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Aplikacja internetowa, która wywołuje internetowe interfejsy API</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Kod autoryzacji</a></td>
   <td>Konta służbowe, konta osobiste i Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Aplikacja klasyczna wywołująca internetowe interfejsy API</a></td>
   <td>Interaktywny przy użyciu <a href="v2-oauth2-auth-code-flow.md">kodu autoryzacji</a> z PKCE</td>
   <td>Konta służbowe, konta osobiste i Azure AD B2C</td>
 </tr>

  <tr>
   <td>Zintegrowane uwierzytelnianie systemu Windows</td>
   <td>Konta służbowe</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Hasło właściciela zasobu</a></td>
   <td>Konta służbowe i Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Kod urządzenia</a></td>
   <td>Konta służbowe, konta osobiste i Azure AD B2C</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Aplikacja mobilna wywołująca internetowe interfejsy API</a></td>
   <td>Interaktywny przy użyciu <a href="v2-oauth2-auth-code-flow.md">kodu autoryzacji</a> z PKCE</td>
   <td>Konta służbowe, konta osobiste i Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Hasło właściciela zasobu</a></td>
   <td>Konta służbowe i Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">Aplikacja demona, która wywołuje interfejsy API sieci Web</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Poświadczenia klienta</a></td>
   <td>Uprawnienia tylko do aplikacji, które nie mają użytkownika i są używane tylko w organizacjach usługi Azure AD</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Internetowy interfejs API wywołujący internetowe interfejsy API</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">W imieniu</a></td>
   <td>Konta służbowe i konta osobiste</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scenariusze i obsługiwane platformy i Języki

Biblioteki uwierzytelniania firmy Microsoft obsługują wiele platform:

- .NET Core
- .NET Framework
- Java
- JavaScript
- macOS
- Natywny system Android
- Natywny system iOS
- Node.js
- Python
- Windows 10/platformy UWP
- Xamarin.iOS
- Xamarin.Android

Możesz również użyć różnych języków do kompilowania aplikacji.

> [!NOTE]
> Niektóre typy aplikacji nie są dostępne na każdej platformie.

W kolumnie Windows w poniższej tabeli, za każdym razem, gdy jest wymieniony program .NET Core, możliwe jest również .NET Framework. Ten ostatni zostanie pominięty, aby uniknąć bałaganu w tabeli.

|Scenariusz  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Aplikacja jednostronicowa](scenario-spa-overview.md) <br/>[![Uwierzytelnianie aplikacji jednostronicowej](media/scenarios/spa-app-auth.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Aplikacja jednostronicowa](scenario-spa-overview.md) <br/>[![Niejawna aplikacja jednostronicowa](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Aplikacja internetowa, która loguje użytkowników](scenario-web-app-sign-user-overview.md) <br/>[![Aplikacja internetowa, która loguje użytkowników](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![Węzeł MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Węzeł MSAL<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![Węzeł MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Węzeł MSAL<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![Węzeł MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Węzeł MSAL<br/>
| [Aplikacja internetowa, która wywołuje internetowe interfejsy API](scenario-web-app-call-api-overview.md) <br/> <br/>[![Aplikacja internetowa, która wywołuje internetowe interfejsy API](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core i MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Kolba + MSAL Python ![Węzeł MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Węzeł MSAL<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core i MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Kolba + MSAL Python ![Węzeł MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Węzeł MSAL<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core i MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Kolba + MSAL Python ![Węzeł MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Węzeł MSAL<br/>
| [Aplikacja klasyczna wywołująca internetowe interfejsy API](scenario-desktop-overview.md) <br/> <br/>[ ![ Aplikacja klasyczna, która wywołuje interfejsy API](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![ sieci Web Przepływ kodu urządzenia](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Węzeł MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Węzeł MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Węzeł MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Węzeł MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![Węzeł MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Węzeł MSAL<br/> ![iOS/cel C lub SWIFT](media/sample-v2-code/small_logo_iOS.png) MSAL. objc |
| [Aplikacja mobilna wywołująca internetowe interfejsy API](scenario-mobile-overview.md) <br/> [![Aplikacja mobilna wywołująca internetowe interfejsy API](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![Platforma UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/cel C lub SWIFT](media/sample-v2-code/small_logo_iOS.png) MSAL. objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Systemów
| [Aplikacja demona](scenario-daemon-overview.md) <br/> [![Aplikacja demona](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Węzeł MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Węzeł MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Węzeł MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Węzeł MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Węzeł MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Węzeł MSAL<br/>
| [Internetowy interfejs API wywołujący internetowe interfejsy API](scenario-web-api-call-api-overview.md) <br/><br/> [![Internetowy interfejs API wywołujący internetowe interfejsy API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core i MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Węzeł MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Węzeł MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core i MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Węzeł MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Węzeł MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core i MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Węzeł MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Węzeł MSAL<br/>

Aby uzyskać więcej informacji, zobacz [biblioteki uwierzytelniania platformy tożsamości firmy Microsoft](reference-v2-libraries.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat podstawowych informacji dotyczących [uwierzytelniania](./authentication-vs-authorization.md) i [tokenów dostępu na platformie tożsamości firmy Microsoft](access-tokens.md).
* Dowiedz się więcej o [zabezpieczaniu dostępu do aplikacji IoT](/azure/architecture/example-scenario/iot-aad/iot-aad).
