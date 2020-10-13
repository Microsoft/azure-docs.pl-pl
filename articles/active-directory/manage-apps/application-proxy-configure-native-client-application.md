---
title: Publikowanie natywnych aplikacji klienckich — Azure AD | Microsoft Docs
description: W tym artykule opisano, jak włączyć natywne aplikacje klienckie do komunikowania się z łącznikiem usługi Azure serwer proxy aplikacji usługi Azure AD, aby zapewnić bezpieczny dostęp zdalny do aplikacji lokalnych.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f8494852bcff49602645c940470b529302f119f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88165077"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Jak włączyć natywne aplikacje klienckie do współdziałania z aplikacjami proxy

Za pomocą serwera proxy aplikacji usługi Azure Active Directory (Azure AD) można opublikować aplikacje sieci Web, ale można go również użyć do opublikowania natywnych aplikacji klienckich, które są skonfigurowane za pomocą biblioteki uwierzytelniania firmy Microsoft (MSAL). Natywne aplikacje klienckie różnią się od aplikacji sieci Web, ponieważ są one instalowane na urządzeniu, podczas gdy aplikacje sieci Web są dostępne za pośrednictwem przeglądarki.

Aby zapewnić obsługę natywnych aplikacji klienckich, serwer proxy aplikacji akceptuje tokeny wystawione przez usługę Azure AD, które są wysyłane w nagłówku. Usługa serwera proxy aplikacji wykonuje uwierzytelnianie dla użytkowników. To rozwiązanie nie korzysta z tokenów aplikacji na potrzeby uwierzytelniania.

![Relacja między użytkownikami końcowymi, usługą Azure AD i opublikowanymi aplikacjami](./media/application-proxy-configure-native-client-application/richclientflow.png)

Aby opublikować aplikacje natywne, należy użyć biblioteki uwierzytelniania firmy Microsoft, która jest obsługiwana przez uwierzytelnianie i obsługę wielu środowisk klienckich. Serwer proxy aplikacji jest [przypisywany do aplikacji klasycznej, która wywołuje interfejs API sieci Web w imieniu scenariusza zalogowanego użytkownika](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios#desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user) .

W tym artykule omówiono cztery kroki umożliwiające opublikowanie aplikacji natywnej przy użyciu serwera proxy aplikacji i biblioteki uwierzytelniania usługi Azure AD.

## <a name="step-1-publish-your-proxy-application"></a>Krok 1. publikowanie aplikacji serwera proxy

Opublikuj swoją aplikację proxy tak jak każda inna aplikacja i przypisz użytkownikom dostęp do aplikacji. Aby uzyskać więcej informacji, zobacz [publikowanie aplikacji przy użyciu serwera proxy aplikacji](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Krok 2. rejestrowanie aplikacji natywnej

Teraz musisz zarejestrować aplikację w usłudze Azure AD w następujący sposób:

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/). Zostanie wyświetlony **pulpit nawigacyjny** **Centrum administracyjnego Azure Active Directory** .
1. Na pasku bocznym wybierz pozycję **Azure Active Directory**. Zostanie wyświetlona strona przegląd **Azure Active Directory** .
1. Na pasku bocznym przegląd usługi Azure AD wybierz pozycję **rejestracje aplikacji**. Zostanie wyświetlona lista wszystkich rejestracji aplikacji.
1. Wybierz pozycję **Nowa rejestracja**. Zostanie wyświetlona strona **zarejestruj aplikację** .

   ![Utwórz nową rejestrację aplikacji w Azure Portal](./media/application-proxy-configure-native-client-application/create.png)

1. W nagłówku **Nazwa** Określ nazwę wyświetlaną użytkownika dla aplikacji.
1. W obszarze **obsługiwane typy kont** wybierz poziom dostępu, korzystając z następujących wskazówek:

   - Aby kierować tylko konta, które są wewnętrzne dla Twojej organizacji, wybierz opcję **konta tylko w tym katalogu organizacji**.
   - Aby kierować tylko do klientów branżowych lub edukacyjnych, wybierz pozycję **konta w dowolnym katalogu organizacyjnym**.
   - Aby określić szeroki zbiór tożsamości firmy Microsoft, wybierz pozycję **konta w dowolnym katalogu organizacyjnym i osobiste konta Microsoft**.
1. W obszarze **Identyfikator URI przekierowania**wybierz pozycję **Klient publiczny (Mobile & Desktop)**, a następnie wpisz identyfikator URI przekierowania `https://login.microsoftonline.com/common/oauth2/nativeclient` dla aplikacji.
1. Wybierz i przeczytaj **Zasady platformy Microsoft**, a następnie wybierz pozycję **zarejestruj**. Zostanie utworzona i wyświetlona strona przeglądu nowej rejestracji aplikacji.

Aby uzyskać szczegółowe informacje na temat tworzenia nowej rejestracji aplikacji, zobacz [Integrowanie aplikacji z Azure Active Directory](../develop/quickstart-register-app.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Krok 3. udzielanie dostępu do aplikacji serwera proxy

Po zarejestrowaniu natywnej aplikacji można udzielić jej dostępu do innych aplikacji w katalogu w tym przypadku w celu uzyskania dostępu do aplikacji serwera proxy. Aby umożliwić uwidocznienie aplikacji natywnej aplikacji serwera proxy:

1. Na pasku bocznym strony rejestracja nowej aplikacji wybierz pozycję **uprawnienia interfejsu API**. Zostanie wyświetlona strona **uprawnienia interfejsu API** dla nowej rejestracji aplikacji.
1. Wybierz pozycję **Dodaj uprawnienie**. Zostanie wyświetlona strona **uprawnienia do interfejsu API żądania** .
1. W obszarze **Wybierz ustawienie interfejsu API** wybierz opcję **interfejsy API, które są wykorzystywane przez moją organizację**. Zostanie wyświetlona lista zawierająca aplikacje w katalogu, które uwidaczniają interfejsy API.
1. Wpisz w polu wyszukiwania lub przewiń, aby znaleźć aplikację serwera proxy opublikowaną w [kroku 1: publikowanie aplikacji serwera proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-native-client-application#step-1-publish-your-proxy-application), a następnie wybierz aplikację serwera proxy.
1. W obszarze **jakiego typu uprawnienia aplikacja jest wymagana?** wybierz pozycję Typ uprawnienia. Jeśli aplikacja natywna musi uzyskać dostęp do interfejsu API aplikacji serwera proxy jako zalogowanego użytkownika, wybierz pozycję **uprawnienia delegowane**.
1. W nagłówku **Wybierz uprawnienia** wybierz odpowiednie uprawnienia, a następnie wybierz pozycję **Dodaj uprawnienia**. Strona **uprawnienia interfejsu API** dla aplikacji natywnej zawiera teraz dodaną aplikację serwera proxy i interfejs API uprawnień.

## <a name="step-4-add-the-microsoft-authentication-library-to-your-code-net-c-sample"></a>Krok 4. Dodawanie biblioteki uwierzytelniania firmy Microsoft do kodu (przykładowego programu .NET C#)

Edytuj natywny kod aplikacji w kontekście uwierzytelniania biblioteki uwierzytelniania firmy Microsoft (MSAL) w celu uwzględnienia następującego tekstu: 

```         
// Acquire Access Token from AAD for Proxy Application
IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }

if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application

  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

Informacje wymagane w przykładowym kodzie można znaleźć w portalu usługi Azure AD w następujący sposób:

| Wymagane informacje | Jak znaleźć je w portalu usługi Azure AD |
| --- | --- |
| \<Tenant ID> | **Azure Active Directory**  >  **Właściwości**  >  **Identyfikator katalogu** |
| \<App ID of the Native app> | **Rejestracja aplikacji**  >  *aplikacja*  >  natywna **Przegląd**  >  **Identyfikator aplikacji** |
| \<Scope> | **Rejestracja aplikacji**  >  *aplikacja*  >  natywna **Uprawnienia interfejsu api** > kliknij pozycję Interfejs API uprawnień (user_impersonation) > panel z napisem **user_impersonation** pojawia się po prawej stronie. > zakresem jest adres URL w polu edycji.
| \<Proxy App Url> | Zewnętrzny adres URL i ścieżka do interfejsu API

Po edycji kodu MSAL za pomocą tych parametrów użytkownicy mogą uwierzytelniać się w natywnych aplikacjach klienckich nawet wtedy, gdy znajdują się poza siecią firmową.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat natywnego przepływu aplikacji, zobacz [aplikacje natywne w Azure Active Directory](../azuread-dev/native-app.md).

Dowiedz się więcej na temat konfigurowania [logowania jednokrotnego do aplikacji w Azure Active Directory](sso-options.md#choosing-a-single-sign-on-method).