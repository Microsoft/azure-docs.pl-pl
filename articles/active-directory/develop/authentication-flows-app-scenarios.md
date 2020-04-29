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
ms.openlocfilehash: bf47a352542be0ad4467ed420cc38f62da0d47dc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534886"
---
# <a name="authentication-flows-and-application-scenarios"></a>Przepływy uwierzytelniania i scenariusze aplikacji

Punkt końcowy Microsoft Identity platform (v 2.0) obsługuje uwierzytelnianie dla różnych rodzajów nowoczesnych architektur aplikacji. Wszystkie architektury są oparte na standardach branżowych protokołu [OAuth 2,0 i OpenID Connect Connect](active-directory-v2-protocols.md).  Przy użyciu [bibliotek uwierzytelniania platformy tożsamości firmy Microsoft](reference-v2-libraries.md)aplikacje uwierzytelniają tożsamości i uzyskują tokeny umożliwiające dostęp do chronionych interfejsów API.

W tym artykule opisano przepływy uwierzytelniania i scenariusze aplikacji, które są używane w programie:

- [Scenariusze aplikacji i obsługiwane przepływy uwierzytelniania](#scenarios-and-supported-authentication-flows).
- [Scenariusze aplikacji i obsługiwane platformy i języki](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Kategorie aplikacji

Tokeny mogą być uzyskiwane z kilku typów aplikacji, w tym:

- Aplikacje internetowe
- Aplikacji mobilnych
- Aplikacje klasyczne
- Interfejsy API sieci Web

Tokeny mogą być również uzyskiwane z aplikacji działających na urządzeniach, które nie mają przeglądarki lub działają na IoT.

Aplikacje można klasyfikować jak na poniższej liście:

- [Chronione zasoby a aplikacje klienckie](#protected-resources-vs-client-applications): Niektóre scenariusze dotyczą ochrony zasobów, takich jak aplikacje sieci Web lub interfejsy API sieci Web. Inne scenariusze mają na celu uzyskanie tokenu zabezpieczającego w celu wywołania chronionego internetowego interfejsu API.
- [Użytkownicy lub bez użytkowników](#with-users-or-without-users): Niektóre scenariusze obejmują zalogowanego użytkownika, ale inne, takie jak scenariusze demona, nie obejmują użytkownika.
- [Jedna strona, klient publiczny i poufne aplikacje klienckie](#single-page-public-client-and-confidential-client-applications): te typy to trzy duże kategorie aplikacji. Każdy z nich jest używany z różnymi bibliotekami i obiektami.
- [Odbiorcy logowania](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types): dostępne przepływy uwierzytelniania różnią się w zależności od odbiorców logowania. Niektóre przepływy są dostępne tylko dla kont służbowych. Niektóre z nich są dostępne zarówno w przypadku kont służbowych, jak i dla osobistych kont Microsoft. Odbiorcy mogą zależeć od przepływów uwierzytelniania.
- [Obsługiwane przepływy protokołu OAuth 2,0](#scenarios-and-supported-authentication-flows): przepływy uwierzytelniania służą do implementowania scenariuszy aplikacji, które żądają tokenów. Nie istnieje mapowanie jeden do jednego między scenariuszami aplikacji i przepływami uwierzytelniania.
- [Obsługiwane platformy](#scenarios-and-supported-platforms-and-languages): nie wszystkie scenariusze aplikacji są dostępne dla każdej platformy.

### <a name="protected-resources-vs-client-applications"></a>Chronione zasoby a aplikacje klienckie

Scenariusze uwierzytelniania obejmują dwa działania:

- **Uzyskiwanie tokenów zabezpieczających dla chronionego internetowego interfejsu API**: zalecamy używanie [bibliotek klienckich obsługiwanych przez firmę Microsoft](reference-v2-libraries.md#microsoft-supported-client-libraries) do uzyskiwania tokenów, w szczególności rodziny Microsoft Authentication Library (MSAL).
- **Ochrona internetowego interfejsu API lub aplikacji sieci Web**: jedno wyzwanie chroniące internetowy interfejs API lub zasób aplikacji sieci Web sprawdza token zabezpieczający. Na niektórych platformach firma Microsoft oferuje [biblioteki oprogramowania pośredniczącego](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>Z użytkownikami lub bez użytkowników

Większość scenariuszy uwierzytelniania uzyskuje tokeny w imieniu zalogowanych użytkowników.

![Scenariusze z użytkownikami](media/scenarios/scenarios-with-users.svg)

Istnieją jednak również scenariusze aplikacji demona, w których aplikacje uzyskują tokeny w imieniu użytkownika bez żadnych użytkowników.

![Scenariusze z aplikacjami demona](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Pojedyncze strony, klient publiczny i poufne aplikacje klienckie

Tokeny zabezpieczające można nabyć z wielu typów aplikacji. Te aplikacje są podzielone na trzy kategorie:

- **Aplikacje jednostronicowe**: znane również jako aplikacji jednostronicowych, te aplikacje to aplikacje sieci Web, w których tokeny są uzyskiwane z aplikacji JavaScript lub TypeScript działającej w przeglądarce. Wiele nowoczesnych aplikacji ma fronton aplikacji jednostronicowej, który jest głównie pisany w języku JavaScript. Aplikacja często używa struktury, takiej jak kątowy, reaguje lub Vue. MSAL. js jest jedyną biblioteką uwierzytelniania firmy Microsoft, która obsługuje aplikacje jednostronicowe.

- **Publiczne aplikacje klienckie**: aplikacje są zawsze zalogowani użytkownicy:
  - Aplikacje klasyczne wywołujące interfejsy API sieci Web w imieniu zalogowanego użytkownika
  - Aplikacji mobilnych
  - Aplikacje działające na urządzeniach, które nie mają przeglądarki, takie jak procesy działające na iOT

  Te aplikacje są reprezentowane przez klasę MSAL [PublicClientApplication](/dotnet/api/microsoft.identity.client.publicclientapplication) . Aby uzyskać więcej informacji, zobacz [publiczny klient i poufne aplikacje klienckie](msal-client-applications.md).

- **Poufne aplikacje klienckie**:
  - Aplikacje sieci Web wywołujące internetowy interfejs API
  - Interfejsy API sieci Web wywołujące internetowy interfejs API
  - Aplikacje demona, nawet w przypadku zaimplementowania jako usługa konsolowa, taka jak demon systemu Linux lub usługa Windows

  Te typy aplikacji używają klasy [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplication) . Aby uzyskać więcej informacji, zobacz [publiczny klient i poufne aplikacje klienckie](msal-client-applications.md).

## <a name="application-scenarios"></a>Scenariusze aplikacji

Punkt końcowy platformy tożsamości firmy Microsoft obsługuje uwierzytelnianie dla różnych architektur aplikacji:

- Aplikacje jednostronicowe
- Aplikacje internetowe
- Interfejsy API sieci Web
- Aplikacji mobilnych
- Aplikacje natywne
- Aplikacje demona
- Aplikacje po stronie serwera

Aplikacje używają różnych przepływów uwierzytelniania do logowania użytkowników i uzyskiwania tokenów do wywoływania chronionych interfejsów API.

### <a name="a-single-page-application"></a>Aplikacja jednostronicowa

Wiele nowoczesnych aplikacji sieci Web jest zbudowanych jako aplikacje jednostronicowe po stronie klienta. Te aplikacje używają języka JavaScript lub jednostronicowej struktury aplikacji, takiej jak kątowy, Vue. js i replika. js. Aplikacje te działają w przeglądarce internetowej.

Ich cechy uwierzytelniania różnią się od tradycyjnych aplikacji sieci Web po stronie serwera. Korzystając z platformy tożsamości firmy Microsoft, aplikacje jednostronicowe mogą logować użytkowników i uzyskiwać tokeny umożliwiające dostęp do usług zaplecza lub interfejsów API sieci Web.

![Aplikacja jednostronicowa](media/scenarios/spa-app.svg)

Aby uzyskać więcej informacji, zobacz [aplikacje jednostronicowe](scenario-spa-overview.md).

### <a name="a-web-app-that-is-signing-in-a-user"></a>Aplikacja sieci Web, która loguje użytkownika

![Aplikacja sieci Web, która loguje się do użytkownika](media/scenarios/scenario-webapp-signs-in-users.svg)

Aby chronić aplikację sieci Web, która loguje użytkownika:

- W przypadku tworzenia oprogramowania w programie .NET należy użyć ASP.NET lub ASP.NET Core z programem ASP.NET Open ID Connect. Ochrona zasobu polega na zweryfikowaniu tokenu zabezpieczającego, który jest wykonywany przez [rozszerzenia IdentityModel dla biblioteki platformy .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) , a nie biblioteki MSAL.

- W przypadku tworzenia oprogramowania w programie Node. js należy użyć usługi Passport. js.

Aby uzyskać więcej informacji, zobacz [aplikacja sieci Web, która oznacza użytkowników](scenario-web-app-sign-user-overview.md).

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Aplikacja sieci Web, która loguje się do użytkownika i wywołuje internetowy interfejs API w imieniu użytkownika

![Aplikacja sieci Web wywołująca interfejsy API sieci Web](media/scenarios/web-app.svg)

Aby wywołać interfejs API sieci Web z aplikacji sieci Web w imieniu użytkownika, należy użyć klasy MSAL **ConfidentialClientApplication** . Używasz przepływu kodu autoryzacji i przechowujesz uzyskane tokeny w pamięci podręcznej tokenów. W razie konieczności MSAL odświeża tokeny i kontroler dyskretnie uzyskuje tokeny z pamięci podręcznej.

Aby uzyskać więcej informacji, zobacz [aplikacje sieci Web wywołujące interfejsy API sieci Web](scenario-web-app-call-api-overview.md).

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>Aplikacja klasyczna wywołująca internetowy interfejs API w imieniu zalogowanego użytkownika

Aby aplikacja klasyczna wywoływała interfejs API sieci Web, który loguje użytkowników, użyj interaktywnych metod pozyskiwania tokenów klasy MSAL **PublicClientApplication** . Za pomocą tych interaktywnych metod można kontrolować środowisko logowania użytkownika. MSAL używa przeglądarki sieci Web dla tej interakcji.

![Aplikacja klasyczna wywołująca internetowy interfejs API](media/scenarios/desktop-app.svg)

Istnieje inna możliwość dla aplikacji hostowanych w systemie Windows na komputerach przyłączonych do domeny systemu Windows lub przez Azure Active Directory (Azure AD). Te aplikacje mogą w trybie dyskretnym uzyskać token przy użyciu [zintegrowanego uwierzytelniania systemu Windows](https://aka.ms/msal-net-iwa).

Aplikacje działające na urządzeniu bez przeglądarki nadal mogą wywołać interfejs API w imieniu użytkownika. W celu uwierzytelnienia użytkownik musi zalogować się na innym urządzeniu z przeglądarką sieci Web. Ten scenariusz wymaga użycia [przepływu kodu urządzenia](https://aka.ms/msal-net-device-code-flow).

![Przepływ kodu urządzenia](media/scenarios/device-code-flow-app.svg)

Mimo że nie zalecamy korzystania z tego programu, [przepływ nazwy użytkownika/hasła](https://aka.ms/msal-net-up) jest dostępny w publicznych aplikacjach klienckich. Ten przepływ jest nadal wymagany w niektórych scenariuszach, takich jak DevOps.

Jednak użycie tego przepływu ogranicza swoje aplikacje. Na przykład aplikacje nie mogą logować się do użytkownika, który musi korzystać z uwierzytelniania wieloskładnikowego lub dostępu warunkowego. Aplikacje nie korzystają również z logowania jednokrotnego.

Uwierzytelnianie za pomocą przepływu nazwy użytkownika/hasła odbywa się w oparciu o zasady nowoczesnego uwierzytelniania i jest dostarczane tylko ze względu na starsze przyczyny.

Jeśli chcesz utrwalać pamięć podręczną tokenów w aplikacjach klasycznych, Dostosuj [serializację pamięci podręcznej tokenu](https://aka.ms/msal-net-token-cache-serialization). Przez implementację [serializacji pamięci podręcznej podwójnego tokenu](https://aka.ms/msal-net-dual-cache-serialization)można używać pamięci podręcznej tokenów zgodnych z poprzednimi wersjami i przesyłania dalej. Te tokeny obsługują poprzednie generacji bibliotek uwierzytelniania. Do określonych bibliotek należą Biblioteka Azure AD Authentication Library for .NET (ADAL.NET) w wersji 3 i 4.

Aby uzyskać więcej informacji, zobacz [aplikacji klasycznej, która wywołuje interfejsy API sieci Web](scenario-desktop-overview.md).

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>Aplikacja mobilna wywołująca internetowy interfejs API w imieniu użytkownika interaktywnego

Podobnie jak w przypadku aplikacji klasycznej, aplikacja mobilna wywołuje interaktywne metody pozyskiwania tokenów klasy MSAL **PublicClientApplication** w celu uzyskania tokenu do wywoływania interfejsu API sieci Web.

![Aplikacja mobilna wywołująca internetowy interfejs API](media/scenarios/mobile-app.svg)

MSAL iOS i MSAL w systemie Android domyślnie korzystają z przeglądarki sieci Web. Można jednak skierować je do korzystania z osadzonego widoku sieci Web. Istnieją pewne informacje, które są zależne od platformy mobilnej: platforma uniwersalna systemu Windows (platformy UWP), iOS lub Android.

Niektóre scenariusze, takie jak te, które obejmują dostęp warunkowy związany z IDENTYFIKATORem urządzenia lub rejestracją urządzenia, wymagają, aby Broker został zainstalowany na urządzeniu. Przykłady brokerów to Microsoft Portal firmy w systemach Android i Microsoft Authenticator w systemach Android i iOS. MSAL może teraz współdziałać z brokerami. Aby uzyskać więcej informacji, zobacz Korzystanie [z brokerów w systemach Android i iOS](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS).

Aby uzyskać więcej informacji, zobacz [aplikacji mobilnej, która wywołuje interfejsy API sieci Web](scenario-mobile-overview.md).

> [!NOTE]
> Aplikacja mobilna korzystająca z MSAL. iOS, MSAL. Dla systemu Android lub MSAL.NET na platformie Xamarin mogą być stosowane zasady ochrony aplikacji. Na przykład zasady mogą uniemożliwić użytkownikowi kopiowanie chronionego tekstu. Aplikacja mobilna jest zarządzana przez usługę Intune i rozpoznawana przez usługę Intune jako zarządzaną aplikację. Aby uzyskać więcej informacji, zobacz [Omówienie zestawu SDK aplikacji Microsoft Intune](https://docs.microsoft.com/intune/app-sdk).
>
> [Zestaw SDK aplikacji usługi Intune](https://docs.microsoft.com/intune/app-sdk-get-started) jest oddzielony od bibliotek MSAL i współdziała z usługą Azure AD.

### <a name="a-protected-web-api"></a>Chroniony internetowy interfejs API

Z punktu końcowego platformy tożsamości firmy Microsoft można korzystać w celu zabezpieczania usług sieci Web, takich jak RESTful internetowy interfejs API aplikacji. Chroniony internetowy interfejs API jest wywoływany przy użyciu tokenu dostępu. Token zabezpiecza dane interfejsu API i uwierzytelnia żądania przychodzące. Obiekt wywołujący internetowy interfejs API dołącza token dostępu w nagłówku autoryzacji żądania HTTP.

Jeśli chcesz chronić internetowy interfejs API usługi ASP.NET lub ASP.NET Core, musisz sprawdzić poprawność tokenu dostępu. W przypadku tej weryfikacji należy użyć oprogramowania pośredniczącego JWT ASP.NET. Walidacja jest wykonywana przez [rozszerzenia IdentityModel dla biblioteki .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) , a nie przez MSAL.NET.

Aby uzyskać więcej informacji, zobacz [chroniony internetowy interfejs API](scenario-protected-web-api-overview.md).

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>Interfejs API sieci Web wywołujący inny internetowy interfejs API w imieniu użytkownika

Aby program ASP.NET lub ASP.NET Core chroniony interfejs API sieci Web w imieniu użytkownika wywoływał inny internetowy interfejs API, aplikacja musi uzyskać token dla podrzędnego interfejsu API sieci Web. Aby uzyskać token, aplikacja wywołuje metodę [AcquireTokenOnBehalfOf](https://aka.ms/msal-net-on-behalf-of) klasy **ConfidentialClientApplication** . Takie wywołania są również nazywane wywołaniami między *usługami* . Interfejsy API sieci Web wywołujące inne interfejsy API sieci Web muszą zapewnić niestandardową serializację pamięci podręcznej.

  ![Internetowy interfejs API wywołujący inny internetowy interfejs API](media/scenarios/web-api.svg)

Aby uzyskać więcej informacji, zobacz [internetowy interfejs API, który wywołuje interfejsy API sieci Web](scenario-web-api-call-api-overview.md).

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>Aplikacja demona wywołująca internetowy interfejs API w nazwie demona

Aplikacje, które mają długotrwałe procesy lub działają bez interakcji z użytkownikiem, muszą również mieć możliwość uzyskania dostępu do bezpiecznych interfejsów API sieci Web. Takie aplikacje mogą uwierzytelniać tokeny i uzyskiwać do nich dostęp przy użyciu tożsamości aplikacji. Aplikacja udowadnia swoją tożsamość przy użyciu klucza tajnego klienta lub certyfikatu.

Można napisać takie aplikacje demona, które uzyskują token dla aplikacji wywołującej przy użyciu metod pozyskiwania [poświadczeń klienta](https://aka.ms/msal-net-client-credentials) klasy MSAL **ConfidentialClientApplication** . Te metody wymagają, aby aplikacja wywołująca zarejestrowała wpis tajny w usłudze Azure AD. Aplikacja następnie udostępnia klucz tajny wywołanym demonem. Przykłady takich wpisów tajnych obejmują hasła aplikacji, potwierdzenie certyfikatu lub potwierdzenie klienta.

![Aplikacja demona wywołana przez inne aplikacje i interfejsy API](media/scenarios/daemon-app.svg)

Aby uzyskać więcej informacji, zobacz [aplikacji demona, która wywołuje interfejsy API sieci Web](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scenariusze i obsługiwane przepływy uwierzytelniania

Scenariusze, które obejmują pobieranie tokenów, również są mapowane na przepływy uwierzytelniania OAuth 2,0. Aby uzyskać więcej informacji, zobacz [Protokoły OAuth 2,0 i OpenID Connect Connect na platformie tożsamości firmy Microsoft](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Scenariusz</th> <th>Szczegółowy opis scenariusza</th> <th>Przepływ OAuth 2,0 i przyznawanie</th> <th>Grupy odbiorców</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Aplikacja jednostronicowa</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Konwersja</a></td>
   <td>Konta służbowe, konta osobiste i Microsoft Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Aplikacja internetowa, która loguje użytkowników</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Kod autoryzacji</a></td>
   <td>Konta służbowe, konta osobiste i Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Aplikacja sieci Web, która wywołuje interfejsy API sieci Web</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Kod autoryzacji</a></td>
   <td>Konta służbowe, konta osobiste i Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Aplikacja klasyczna, która wywołuje interfejsy API sieci Web</a></td>
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
   <td>Konta służbowe</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Aplikacja mobilna, która wywołuje interfejsy API sieci Web</a></td>
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
   <td>Uprawnienia tylko do aplikacji bez użytkownika i używane tylko w organizacjach usługi Azure AD</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Internetowy interfejs API, który wywołuje interfejsy API sieci Web</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">Zastępczy</a></td>
   <td>Konta służbowe i konta osobiste</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scenariusze i obsługiwane platformy i Języki

Biblioteki uwierzytelniania firmy Microsoft obsługują wiele platform:

- JavaScript
- .NET Framework
- .NET Core
- Windows 10/platformy UWP
- Xamarin.iOS
- Xamarin.Android
- Natywny system iOS
- macOS
- Natywny system Android
- Java
- Python

Możesz również użyć różnych języków do kompilowania aplikacji.

> [!NOTE]
> Niektóre typy aplikacji nie są dostępne na każdej platformie.

W kolumnie Windows w poniższej tabeli, za każdym razem, gdy jest wymieniony program .NET Core, możliwe jest również .NET Framework. Ten ostatni zostanie pominięty, aby uniknąć bałaganu w tabeli.

|Scenariusz  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Aplikacja jednostronicowa](scenario-spa-overview.md) <br/>[![Aplikacja jednostronicowa](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Aplikacja internetowa, która loguje użytkowników](scenario-web-app-sign-user-overview.md) <br/>[![Aplikacja internetowa, która loguje użytkowników](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Aplikacja internetowa, która wywołuje internetowe interfejsy API](scenario-web-app-call-api-overview.md) <br/> <br/>[![Aplikacja internetowa, która wywołuje internetowe interfejsy API](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core i MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Kolba + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core i MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Kolba + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core i MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Kolba + MSAL Python
| [Aplikacja klasyczna wywołująca internetowe interfejsy API](scenario-desktop-overview.md) <br/> <br/>[Aplikacja klasyczna, która wywołuje przepływ kodu urządzenia interfejsów API sieci Web ![](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![iOS/cel C lub SWIFT](media/sample-v2-code/small_logo_iOS.png) MSAL. objc |
| [Aplikacja mobilna wywołująca internetowe interfejsy API](scenario-mobile-overview.md) <br/> [![Aplikacja mobilna wywołująca internetowe interfejsy API](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![Platforma UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/cel C lub SWIFT](media/sample-v2-code/small_logo_iOS.png) MSAL. objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Systemów
| [Aplikacja demona](scenario-daemon-overview.md) <br/> [![Aplikacja demona](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [Internetowy interfejs API wywołujący internetowe interfejsy API](scenario-web-api-call-api-overview.md) <br/><br/> [![Internetowy interfejs API wywołujący internetowe interfejsy API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core i MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core i MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core i MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

Aby uzyskać więcej informacji, zobacz [biblioteki obsługiwane przez firmę Microsoft według systemu operacyjnego/języka](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat podstawowych informacji dotyczących [uwierzytelniania](authentication-scenarios.md) i [tokenów dostępu platformy tożsamości firmy Microsoft](access-tokens.md).
* Dowiedz się więcej o [zabezpieczaniu dostępu do aplikacji IoT](/azure/architecture/example-scenario/iot-aad/iot-aad).
