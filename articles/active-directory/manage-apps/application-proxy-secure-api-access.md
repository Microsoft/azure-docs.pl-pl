---
title: Dostęp do lokalnych interfejsów API za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD
description: Serwer proxy aplikacji Azure Active Directory pozwala natywnym aplikacjom bezpiecznie uzyskać dostęp do interfejsów API i logiki biznesowej, które są hostowane lokalnie lub na maszynach wirtualnych w chmurze.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 9341646f32f6a2e05397b072d3f63186964fbd88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258986"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Bezpieczny dostęp do lokalnych interfejsów API przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD

Mogą być dostępne interfejsy API logiki biznesowej działające lokalnie lub hostowane na maszynach wirtualnych w chmurze. Natywne aplikacje dla systemów Android, iOS, Mac lub Windows muszą współdziałać z punktami końcowymi interfejsu API w celu korzystania z danych lub zapewnienia interakcji z użytkownikiem. Usługa Azure serwer proxy aplikacji usługi Azure AD i [Biblioteka Microsoft Authentication Library (MSAL)](../azuread-dev/active-directory-authentication-libraries.md) umożliwiają bezpieczne dostęp do lokalnych interfejsów API przez natywne aplikacje. Serwer proxy aplikacji usługi Azure Active Directory to szybsze i bardziej bezpieczne rozwiązanie niż otwieranie portów zapory i kontrolowanie uwierzytelniania i autoryzacji w warstwie aplikacji.

W tym artykule przedstawiono sposób konfigurowania rozwiązania serwer proxy aplikacji usługi Azure AD platformy Azure do hostowania usługi internetowego interfejsu API, do której aplikacje natywne mogą uzyskać dostęp.

## <a name="overview"></a>Omówienie

Na poniższym diagramie przedstawiono tradycyjny sposób publikowania lokalnych interfejsów API. To podejście wymaga otwarcia portów przychodzących 80 i 443.

![Tradycyjny dostęp do interfejsu API](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Na poniższym diagramie przedstawiono, jak za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD bezpiecznie publikować interfejsy API bez otwierania żadnych portów przychodzących:

![Dostęp do interfejsu API serwer proxy aplikacji usługi Azure AD platformy Azure](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Usługa Azure serwer proxy aplikacji usługi Azure AD stanowi szkielet rozwiązania, działającego jako publiczny punkt końcowy do uzyskiwania dostępu do interfejsu API i zapewniający uwierzytelnianie i autoryzację. Możesz uzyskać dostęp do interfejsów API z rozległej macierzy platform przy użyciu bibliotek [Microsoft Authentication Library (MSAL)](../azuread-dev/active-directory-authentication-libraries.md) .

Ponieważ uwierzytelnianie i autoryzacja w usłudze Azure serwer proxy aplikacji usługi Azure AD są oparte na usłudze Azure AD, można użyć dostępu warunkowego usługi Azure AD w celu zapewnienia, że tylko zaufane urządzenia mają dostęp do interfejsów API opublikowanych za pomocą serwera proxy aplikacji. Użyj funkcji Dołącz do usługi Azure AD lub hybrydowej usługi Azure AD dla komputerów stacjonarnych, a usługa Intune zarządzana na urządzeniach. Możesz również korzystać z funkcji Azure Active Directory — wersja Premium, takich jak usługa Azure AD Multi-Factor Authentication, oraz zabezpieczeń usługi Machine Learning w [usłudze Azure Identity Protection](../identity-protection/overview-identity-protection.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać czynności opisane w tym instruktażu, potrzebne są:

- Dostęp administratora do katalogu platformy Azure przy użyciu konta, które może tworzyć i rejestrować aplikacje
- Przykładowy internetowy interfejs API i natywne aplikacje klienckie z [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp)

## <a name="publish-the-api-through-application-proxy"></a>Publikowanie interfejsu API za pomocą serwera proxy aplikacji

Aby opublikować interfejs API poza intranetem za pośrednictwem serwera proxy aplikacji, należy postępować zgodnie z tym samym wzorcem, co w przypadku publikowania aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [Samouczek: Dodawanie aplikacji lokalnej dla dostępu zdalnego za poorednictwem serwera proxy aplikacji w Azure Active Directory](application-proxy-add-on-premises-application.md).

Aby opublikować interfejs API sieci Web SecretAPI za pomocą serwera proxy aplikacji:

1. Kompilowanie i publikowanie przykładowego projektu SecretAPI jako aplikacji sieci Web ASP.NET na komputerze lokalnym lub w intranecie. Upewnij się, że masz dostęp do aplikacji sieci Web lokalnie.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Azure Active Directory**. Następnie wybierz pozycję **aplikacje dla przedsiębiorstw**.

1. W górnej części strony **aplikacje dla przedsiębiorstw — wszystkie aplikacje** wybierz pozycję **Nowa aplikacja**.

1. Na stronie **Dodawanie aplikacji** wybierz pozycję **aplikacje lokalne**. Zostanie wyświetlona strona **Dodawanie własnej aplikacji lokalnej** .

1. Jeśli nie masz zainstalowanego łącznika serwera proxy aplikacji, zostanie wyświetlony monit o jego zainstalowanie. Wybierz pozycję **Pobierz łącznik serwera proxy aplikacji** , aby pobrać i zainstalować łącznik.

1. Po zainstalowaniu łącznika serwera proxy aplikacji na stronie **Dodawanie własnej aplikacji lokalnej** :

   1. Obok pozycji **Nazwa** wprowadź *SecretAPI*.

   1. W polu **wewnętrzny adres URL** wprowadź adres URL używany do uzyskiwania dostępu do interfejsu API z intranetu.

   1. Upewnij się, że **wstępne uwierzytelnianie** jest ustawione na **Azure Active Directory**.

   1. Wybierz pozycję **Dodaj** w górnej części strony i poczekaj na utworzenie aplikacji.

   ![Dodawanie aplikacji interfejsu API](./media/application-proxy-secure-api-access/3-add-api-app.png)

1. Na stronie **aplikacje dla przedsiębiorstw — wszystkie aplikacje** wybierz aplikację **SecretAPI** .

1. Na stronie **SecretAPI — przegląd** wybierz pozycję **Właściwości** w okienku nawigacji po lewej stronie.

1. Nie chcesz, aby interfejsy API były dostępne dla użytkowników końcowych w panelu Moje **aplikacje** , więc ustawione **dla użytkowników jako widoczne** w dolnej części strony **Właściwości** , a następnie wybierz pozycję **Zapisz**. 

   ![Niewidoczne dla użytkowników](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)

Interfejs API sieci Web został opublikowany za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD. Teraz Dodaj użytkowników, którzy mają dostęp do aplikacji.

1. Na stronie **SecretAPI — przegląd** wybierz pozycję **Użytkownicy i grupy** w okienku nawigacji po lewej stronie.

1. Na stronie **Użytkownicy i grupy** wybierz pozycję **Dodaj użytkownika**.

1. Na stronie **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

1. Na stronie **Użytkownicy i grupy** Wyszukaj i wybierz użytkowników, którzy mają dostęp do aplikacji, w tym co najmniej siebie. Po wybraniu opcji Wszyscy użytkownicy wybierz pozycję **Wybierz**.

   ![Wybieranie i przypisywanie użytkownika](./media/application-proxy-secure-api-access/7-select-admin-user.png)

1. Na stronie **Dodawanie przypisania** wybierz pozycję **Przypisz**.

> [!NOTE]
> Interfejsy API korzystające ze zintegrowanego uwierzytelniania systemu Windows mogą wymagać [dodatkowych czynności](./application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Zarejestruj aplikację natywną i Udziel dostępu do interfejsu API

Aplikacje natywne to programy opracowane do użycia na określonej platformie lub urządzeniu. Zanim aplikacja natywna będzie mogła łączyć się z interfejsem API i uzyskiwać do niego dostęp, musisz zarejestrować ją w usłudze Azure AD. Poniższe kroki pokazują, jak zarejestrować aplikację natywną i zapewnić dostęp do internetowego interfejsu API opublikowanego za pomocą serwera proxy aplikacji.

Aby zarejestrować aplikację natywną AppProxyNativeAppSample:

1. Na stronie **przegląd** Azure Active Directory wybierz pozycję **rejestracje aplikacji**, a następnie w górnej części okienka **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja**.

1. Na stronie **zarejestruj aplikację** :

   1. W polu **Nazwa** wprowadź *AppProxyNativeAppSample*.

   1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.

   1. W obszarze **adres URL przekierowania** wybierz pozycję **Klient publiczny (Mobile & Desktop)**, a następnie wprowadź *https://login.microsoftonline.com/common/oauth2/nativeclient* .

   1. Wybierz pozycję **zarejestruj** i poczekaj na pomyślne zarejestrowanie aplikacji.

      ![Rejestrowanie nowej aplikacji](./media/application-proxy-secure-api-access/8-create-reg-ga.png)

Aplikacja AppProxyNativeAppSample została już zarejestrowana w Azure Active Directory. Aby zapewnić aplikacji natywnej dostęp do interfejsu API sieci Web SecretAPI:

1. Na stronie Azure Active Directory **Przegląd**  >  **rejestracji aplikacji** wybierz aplikację **AppProxyNativeAppSample** .

1. Na stronie **AppProxyNativeAppSample** wybierz pozycję **uprawnienia interfejsu API** na lewym pasku nawigacyjnym.

1. Na stronie **uprawnienia interfejsu API** wybierz pozycję **Dodaj uprawnienie**.

1. Na stronie pierwsze **uprawnienia interfejsu API żądania** wybierz pozycję **interfejsy API Moja organizacja używa** karty, a następnie wyszukaj i wybierz pozycję **SecretAPI**.

1. Na stronie następne **uprawnienia interfejsu API żądania** zaznacz pole wyboru obok pozycji **user_impersonation**, a następnie wybierz pozycję **Dodaj uprawnienia**.

    ![Wybieranie interfejsu API](./media/application-proxy-secure-api-access/10-secretapi-added.png)

1. Na stronie **uprawnienia interfejsu API** można wybrać opcję **Przyznaj zgodę administratora firmie Contoso** , aby uniemożliwić innym użytkownikom osobną zgodę na aplikację.

## <a name="configure-the-native-app-code"></a>Konfigurowanie natywnego kodu aplikacji

Ostatnim krokiem jest skonfigurowanie aplikacji natywnej. Poniższy fragment kodu z pliku *Form1. cs* w przykładowej aplikacji NativeClient powoduje, że biblioteka MSAL umożliwia uzyskanie tokenu żądającego wywołania interfejsu API i dołączenie go jako okaziciela do nagłówka aplikacji.

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

Aby skonfigurować aplikację natywną do nawiązywania połączenia z Azure Active Directory i wywoływać serwer proxy aplikacji interfejsu API, zaktualizuj wartości symboli zastępczych w pliku *App.config* przykładowej aplikacji NativeClient przy użyciu wartości z usługi Azure AD:

- Wklej w polu **Identyfikator katalogu (dzierżawcy)** `<add key="ida:Tenant" value="" />` . Tę wartość (identyfikator GUID) można znaleźć i skopiować na stronie **Przegląd** dowolnej aplikacji.

- Wklej w polu **Identyfikator aplikacji AppProxyNativeAppSample (klienta)** `<add key="ida:ClientId" value="" />` . Tę wartość (identyfikator GUID) można znaleźć i skopiować na stronie **Przegląd** AppProxyNativeAppSample.

- Wklej **Identyfikator URI przekierowania** AppProxyNativeAppSample w `<add key="ida:RedirectUri" value="" />` polu. Tę wartość (identyfikator URI) można znaleźć i skopiować na stronie **uwierzytelnianie** AppProxyNativeAppSample.

- Wklej **Identyfikator URI aplikacji** SecretAPI w `<add key="todo:TodoListResourceId" value="" />` polu. Możesz znaleźć i skopiować tę wartość (identyfikator URI) ze strony SecretAPI **uwidaczniaj interfejs API** .

- Wklej **adres URL strony głównej** SecretAPI w `<add key="todo:TodoListBaseAddress" value="" />` polu. Możesz znaleźć i skopiować tę wartość (adres URL) ze strony **znakowania** SecretAPI.

Po skonfigurowaniu parametrów Skompiluj i uruchom aplikację natywną. Po wybraniu przycisku **Zaloguj** aplikacja umożliwi zalogowanie się, a następnie wyświetli ekran sukcesu, aby potwierdzić pomyślne połączenie z SecretAPI.

![Zrzut ekranu przedstawia komunikat o powodzeniu i przycisk OK.](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Dodawanie aplikacji lokalnej dla dostępu zdalnego przy użyciu serwera proxy aplikacji w Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Szybki Start: Konfigurowanie aplikacji klienckiej w celu uzyskiwania dostępu do interfejsów API sieci Web](../develop/quickstart-configure-app-access-web-apis.md)
- [Jak włączyć natywne aplikacje klienckie do współdziałania z aplikacjami proxy](application-proxy-configure-native-client-application.md)