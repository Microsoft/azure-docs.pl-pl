---
title: Biblioteki uwierzytelniania platformy tożsamości firmy Microsoft | Azure
description: Lista bibliotek klienta i oprogramowania pośredniczącego zgodnych z platformą tożsamości firmy Microsoft. Te biblioteki służą do dodawania obsługi logowania użytkownika (uwierzytelniania) i dostępu do chronionego internetowego interfejsu API (autoryzacji) do aplikacji.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 9549ebab687400e32bbc68a2c76cf8efc8c106c8
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218292"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Biblioteki uwierzytelniania platformy tożsamości firmy Microsoft

W poniższych tabelach przedstawiono obsługę biblioteki uwierzytelniania firmy Microsoft dla kilku typów aplikacji. Obejmują one linki do kodu źródłowego biblioteki, gdzie można pobrać pakiet dla projektu aplikacji oraz czy Biblioteka obsługuje logowanie użytkownika (uwierzytelnianie), dostęp do chronionych interfejsów API sieci Web (autoryzacja) lub obie te funkcje.

Platforma tożsamości firmy Microsoft została certyfikowana przez OpenID Connect Foundation jako [Certyfikowany dostawca OpenID Connect](https://openid.net/certification/). Jeśli wolisz używać biblioteki innej niż biblioteka uwierzytelniania firmy Microsoft (MSAL) lub innej biblioteki obsługiwanej przez firmę Microsoft, wybierz jedną z [certyfikowaną implementacją programu OpenID Connect Connect](https://openid.net/developers/certified/).

W przypadku wybrania opcji ręcznego tworzenia kodu na poziomie protokołu [OAuth 2,0 lub OpenID Connect Connect 1,0](active-directory-v2-protocols.md)należy zwrócić szczególną uwagę na kwestie dotyczące zabezpieczeń w każdej specyfikacji standardu i postępować zgodnie z metodologią cyklu projektowania oprogramowania (SDL), taką jak [Microsoft SDL][Microsoft-SDL].

## <a name="single-page-application-spa"></a>Aplikacja jednostronicowa (SPA)

Jednostronicowa aplikacja działa wyłącznie na powierzchni przeglądarki i pobiera dane strony (HTML, CSS i JavaScript) dynamicznie lub w czasie ładowania aplikacji. Może wywoływać interfejsy API sieci Web w celu współpracy z źródłami danych zaplecza.

Ponieważ kod SPA działa całkowicie w przeglądarce, jest traktowany jako *Klient publiczny* , który nie może bezpiecznie przechowywać wpisów tajnych.

| Język/struktura | Projekt włączony<br/>GitHub                                                                                                    | Pakiet                                                                      | Otrzymania<br/>uruchomieniu                             | Logowanie użytkowników                                         | Dostęp do interfejsów API sieci Web                                                 | Ogólnie dostępna (GA) *lub*<br/>Publiczna wersja zapoznawcza<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL skośny 2,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Publiczna wersja zapoznawcza                                               |
| Angular              | [MSAL kątowy](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular) | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | [Samouczek](tutorial-v2-angular.md)              | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Publiczna wersja zapoznawcza                                               |
| JavaScript           | [MSAL.js 2,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Samouczek](tutorial-v2-javascript-auth-code.md) | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| React                | [Reagowanie na MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Publiczna wersja zapoznawcza                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatkowe warunki użytkowania dla][preview-tos] wersji zapoznawczych Microsoft Azure mają zastosowanie do bibliotek w *publicznej wersji zapoznawczej*.

## <a name="web-application"></a>Aplikacja internetowa

Aplikacja sieci Web uruchamia kod na serwerze, który generuje i wysyła pliki HTML, CSS i JavaScript do przeglądarki sieci Web użytkownika do renderowania. Tożsamość użytkownika jest utrzymywana jako sesja między przeglądarką użytkownika (frontonem) a serwerem sieci Web (zapleczem).

Ponieważ kod aplikacji sieci Web jest uruchamiany na serwerze sieci Web, jest traktowany jako *poufny klient* , który umożliwia bezpieczne przechowywanie wpisów tajnych.

| Język/struktura | Projekt włączony<br/>GitHub                                                                                     | Pakiet                                                                                                    | Otrzymania<br/>uruchomieniu                               | Logowanie użytkowników                                            | Dostęp do interfejsów API sieci Web                                                    | Ogólnie dostępna (GA) *lub*<br/>Publiczna wersja zapoznawcza<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![Biblioteka nie może żądać tokenów identyfikatora dla logowania użytkownika.][n] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y]    | Ogólna dostępność                                                           |
| ASP.NET Core         | [Zabezpieczenia ASP.NET](/aspnet/core/security/)                                                                | [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y]    | ![Biblioteka nie może zażądać tokenów dostępu dla chronionych interfejsów API sieci Web.][n] | Ogólna dostępność                                                           |
| ASP.NET Core         | [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web)                               | [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y]    | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y]    | Ogólna dostępność                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     | [Szybki start](quickstart-v2-java-webapp.md)        | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y]    | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y]    | Ogólna dostępność                                                           |
| Node.js              | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal — węzeł](https://www.npmjs.com/package/@azure/msal-node)                                                | [Szybki start](quickstart-v2-nodejs-webapp-msal.md) | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y]    | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y]    | Publiczna wersja zapoznawcza                                               |
| Node.js              | [Paszport usługi Azure AD](https://github.com/AzureAD/passport-azure-ad)                                         | [paszport — Azure — AD](https://www.npmjs.com/package/passport-azure-ad)                                       | [Szybki start](quickstart-v2-nodejs-webapp.md)      | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y]    | ![Biblioteka nie może zażądać tokenów dostępu dla chronionych interfejsów API sieci Web.][n] | Ogólna dostępność                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [msal](https://pypi.org/project/msal)                                                                      | [Szybki start](quickstart-v2-python-webapp.md)      | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y]    | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y]    | Ogólna dostępność                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatkowe warunki użytkowania dla][preview-tos] wersji zapoznawczych Microsoft Azure mają zastosowanie do bibliotek w *publicznej wersji zapoznawczej*.

## <a name="desktop-application"></a>Aplikacja klasyczna

Aplikacja klasyczna to zazwyczaj kod binarny (skompilowany), który wyświetla interfejs użytkownika i jest przeznaczony do uruchamiania na pulpicie użytkownika.

Ponieważ aplikacja klasyczna jest uruchamiana na pulpicie użytkownika, jest traktowana jako *Klient publiczny* , który nie może bezpiecznie przechowywać wpisów tajnych.

| Język/struktura | Projekt włączony<br/>GitHub                                                                                     | Pakiet                                                                               | Otrzymania<br/>uruchomieniu                        | Logowanie użytkowników                                         | Dostęp do interfejsów API sieci Web                                                 | Ogólnie dostępna (GA) *lub*<br/>Publiczna wersja zapoznawcza<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Elektron             | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Publiczna wersja zapoznawcza                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| macOS (SWIFT/obj-C)  | [Biblioteka MSAL dla systemów iOS i macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [BIBLIOTEKA MSAL](https://cocoapods.org/pods/MSAL)                                               | [Samouczek](tutorial-v2-ios.md)             | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| Platforma UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Samouczek](tutorial-v2-windows-uwp.md)     | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Samouczek](tutorial-v2-windows-desktop.md) | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatkowe warunki użytkowania dla][preview-tos] wersji zapoznawczych Microsoft Azure mają zastosowanie do bibliotek w *publicznej wersji zapoznawczej*.

## <a name="mobile-application"></a>Aplikacja mobilna

Aplikacja mobilna to zazwyczaj kod binarny (skompilowany), który wyświetla interfejs użytkownika i jest przeznaczony do uruchamiania na urządzeniu przenośnym użytkownika.

Ponieważ aplikacja mobilna jest uruchamiana na urządzeniu przenośnym użytkownika, jest traktowana jako *Klient publiczny* , który nie może bezpiecznie przechowywać wpisów tajnych.

| Platforma          | Projekt włączony<br/>GitHub                                                                          | Pakiet                                                                               | Otrzymania<br/>uruchomieniu                    | Logowanie użytkowników                                         | Dostęp do interfejsów API sieci Web                                                 | Ogólnie dostępna (GA) *lub*<br/>Publiczna wersja zapoznawcza<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [BIBLIOTEKA MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Szybki start](quickstart-v2-android.md) | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [BIBLIOTEKA MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| iOS (SWIFT/obj-C) | [Biblioteka MSAL dla systemów iOS i macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [BIBLIOTEKA MSAL](https://cocoapods.org/pods/MSAL)                                               | [Samouczek](tutorial-v2-ios.md)         | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatkowe warunki użytkowania dla][preview-tos] wersji zapoznawczych Microsoft Azure mają zastosowanie do bibliotek w *publicznej wersji zapoznawczej*.

## <a name="service--daemon"></a>Usługa/demon

Usługi i demony są często używane w przypadku komunikacji między serwerami i nienadzorowanymi (czasami nazywanymi *bezobsługowymi*). Ponieważ na klawiaturze nie ma uprawnień do wprowadzania poświadczeń lub zgody na dostęp do zasobów, te aplikacje są uwierzytelniane jako same, a nie jako użytkownik, podczas żądania autoryzowanego dostępu do zasobów internetowego interfejsu API.

Usługa lub demon działający na serwerze jest traktowany jako *poufny klient* , który umożliwia bezpieczne przechowywanie swoich kluczy tajnych.

| Język/struktura | Projekt włączony<br/>GitHub                                                                 | Pakiet                                                                                | Otrzymania<br/>uruchomieniu                           | Logowanie użytkowników                                            | Dostęp do interfejsów API sieci Web                                                 | Ogólnie dostępna (GA) *lub*<br/>Publiczna wersja zapoznawcza<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Szybki start](quickstart-v2-netcore-daemon.md) | ![Biblioteka nie może żądać tokenów identyfikatora dla logowania użytkownika.][n] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![Biblioteka nie może żądać tokenów identyfikatora dla logowania użytkownika.][n] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal — Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Szybki start](quickstart-v2-python-daemon.md)  | ![Biblioteka nie może żądać tokenów identyfikatora dla logowania użytkownika.][n] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatkowe warunki użytkowania dla][preview-tos] wersji zapoznawczych Microsoft Azure mają zastosowanie do bibliotek w *publicznej wersji zapoznawczej*.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat biblioteki uwierzytelniania firmy Microsoft, zobacz [Omówienie biblioteki uwierzytelniania firmy Microsoft (MSAL)](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
