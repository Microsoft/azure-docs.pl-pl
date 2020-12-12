---
title: Dodaj firmę Google jako dostawcę tożsamości dla B2B — Azure AD
description: Sfederować z firmą Google, aby umożliwić użytkownikom-Gościom logowanie się do aplikacji usługi Azure AD przy użyciu własnych kont Gmail.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53d2369e93052ef28191dd1862034c1aaa488add
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355600"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Dodaj firmę Google jako dostawcę tożsamości dla użytkowników gościa B2B

Konfigurując Federacji w usłudze Google, możesz zezwolić zaproszonym użytkownikom na logowanie się do udostępnionych aplikacji i zasobów przy użyciu własnych kont usługi Gmail, bez konieczności tworzenia kont Microsoft. 

> [!NOTE]
> Firma Google Federation została zaprojektowana specjalnie dla użytkowników usługi Gmail. Aby sfederować z domenami usługi G Suite, należy użyć [Federacji bezpośredniej](direct-federation.md).

> [!IMPORTANT]
> **Od 4 stycznia 2021** firma Google jest [przestarzałą obsługą logowania do usługi WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). W przypadku korzystania z usługi Google Federation lub samoobsługowego rejestrowania się w usłudze Gmail należy [przetestować natywne aplikacje biznesowe pod kątem zgodności](google-federation.md#deprecation-of-webview-sign-in-support).

## <a name="what-is-the-experience-for-the-google-user"></a>Co to jest środowisko użytkownika Google?
Po wysłaniu zaproszenia do użytkowników usługi Google Gmail użytkownicy-Goście powinni uzyskiwać dostęp do udostępnionych aplikacji lub zasobów przy użyciu linku zawierającego kontekst dzierżawy. Ich środowisko pracy różni się w zależności od tego, czy są już zalogowane w usłudze Google:
  - Użytkownicy-Goście, którzy nie są zalogowani do usługi Google, będą monitowani o to.
  - Użytkownicy-Goście, którzy zostali już zalogowani w usłudze Google, będą monitowani o wybranie konta, którego chcą użyć. Muszą wybrać konto użyte do zaproszenia.

Użytkownicy-Goście, którzy zobaczą "nagłówek zbyt długi", mogą wyczyścić swoje pliki cookie lub otworzyć okno prywatne lub incognito, a następnie spróbować zalogować się ponownie.

![Zrzut ekranu przedstawiający stronę logowania Google.](media/google-federation/google-sign-in.png)

## <a name="deprecation-of-webview-sign-in-support"></a>Wycofanie obsługi logowania w usłudze WebView

Od 4 stycznia 2021, Google jest [przestarzała obsługa logowania w widoku WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). W przypadku korzystania z usługi Google Federation lub samoobsługowego [rejestrowania się w usłudze Gmail](identity-providers.md)należy przetestować natywne aplikacje biznesowe pod kątem zgodności. Jeśli aplikacje obejmują zawartość WebView, która wymaga uwierzytelnienia, użytkownicy usługi Google Gmail nie będą mogli się uwierzytelnić. Poniżej przedstawiono znane scenariusze, które mają wpływ na użytkowników usługi Gmail:

- Aplikacje systemu Windows korzystające z osadzonego widoku WebView lub webaccountmanager (WAM) we wcześniejszych wersjach systemu Windows.
- Inne aplikacje natywne, które zostały opracowane przy użyciu osadzonej struktury przeglądarki na potrzeby uwierzytelniania.

Ta zmiana nie ma wpływu na:

- Aplikacje systemu Windows korzystające z osadzonego widoku WebView lub webaccountmanager (WAM) w najnowszych wersjach systemu Windows
- Aplikacje systemu Microsoft iOS
- Tożsamości usługi G Suite, na przykład w przypadku korzystania z [Federacji bezpośredniej](direct-federation.md) opartej na protokole SAML z pakietem G Suite

Kontynuujemy testowanie różnych platform i scenariuszy, a tym samym aktualizujemy ten artykuł.
### <a name="to-test-your-apps-for-compatibility"></a>Aby przetestować aplikacje pod kątem zgodności

1. Postępuj zgodnie ze [wskazówkami firmy Google](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) , aby określić, czy dotyczy Twoich aplikacji.
2. Korzystając z programu Fiddler lub innego narzędzia do testowania, należy wstrzyknąć nagłówek podczas logowania i użyć tożsamości zewnętrznej firmy Google do przetestowania logowania:

   1. Dodaj konto Google-accounts-Check-OAuth-login: true do nagłówków żądania HTTP, gdy żądania są wysyłane do accounts.google.com.
   1. Spróbuj zalogować się do aplikacji, wprowadzając adres Gmail na stronie logowania accounts.google.com.
   1. Jeśli logowanie nie powiedzie się i zostanie wyświetlony komunikat o błędzie, np. "Ta przeglądarka lub aplikacja może nie być zabezpieczona", logowanie w usłudze Google webtożsamość zostanie zablokowane.

3. Aby rozwiązać ten problem, wykonaj jedną z następujących czynności:

   - Jeśli aplikacja systemu Windows używa osadzonego widoku WebView lub webaccountmanager (WAM) w starszej wersji systemu Windows, należy zaktualizować do najnowszej wersji systemu Windows.
   - Zmodyfikuj swoje aplikacje, aby użyć przeglądarki systemowej do logowania. Aby uzyskać szczegółowe informacje, zobacz [osadzony interfejs użytkownika sieci Web systemu vs](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui) w dokumentacji MSAL.NET.  

## <a name="sign-in-endpoints"></a>Punkty końcowe logowania

Zespoły w pełni obsługują użytkowników gościa Google na wszystkich urządzeniach. Użytkownicy usługi Google mogą logować się do zespołów z wspólnego punktu końcowego, takiego jak `https://teams.microsoft.com` .

Wspólne punkty końcowe aplikacji mogą nie obsługiwać użytkowników usługi Google. Użytkownicy usługi gościa Google muszą się zalogować przy użyciu linku zawierającego informacje o dzierżawie. Poniżej przedstawiono przykłady:
  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://portal.azure.com/<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Jeśli użytkownicy usługi Google Guest próbują użyć linku `https://myapps.microsoft.com` , takiego jak lub `https://portal.azure.com` , wystąpi błąd.

Możesz również udzielić użytkownikom usługi Google gość bezpośredniego linku do aplikacji lub zasobu, o ile link zawiera informacje o dzierżawie. Na przykład `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`. 
## <a name="step-1-configure-a-google-developer-project"></a>Krok 1. Konfigurowanie projektu dla deweloperów Google
Najpierw utwórz nowy projekt w konsoli firmy Google Developers w celu uzyskania identyfikatora klienta i klucza tajnego klienta, który można później dodać do Azure Active Directory (Azure AD). 
1. Przejdź do interfejsów API usługi Google at https://console.developers.google.com i zaloguj się przy użyciu konta Google. Zalecamy używanie udostępnionego konta Google zespołu.
2. Jeśli zostanie wyświetlony monit, zaakceptuj warunki korzystania z usługi.
3. Utwórz nowy projekt: na pulpicie nawigacyjnym wybierz pozycję **Utwórz projekt**, Nadaj projektowi nazwę (np. **Azure AD B2B**), a następnie wybierz pozycję **Utwórz**: 
   
   ![Zrzut ekranu pokazujący nową stronę projektu.](media/google-federation/google-new-project.png)

4. Na stronie **& usług API Services** wybierz pozycję **Widok** pod nowym projektem.

5. Wybierz pozycję **Przejdź do interfejsów API przegląd** na karcie interfejsy API. Wybierz **ekran wyrażania zgody na uwierzytelnianie OAuth**.

6. Wybierz pozycję **zewnętrzny**, a następnie wybierz pozycję **Utwórz**. 

7. Na **ekranie wyrażanie zgody na uwierzytelnianie OAuth** wprowadź **nazwę aplikacji**:

   ![Zrzut ekranu przedstawiający ekran wyrażania zgody na usługi Google OAuth.](media/google-federation/google-oauth-consent-screen.png)

8. Przewiń do sekcji **autoryzowane domeny** i wprowadź **microsoftonline.com**:

   ![Zrzut ekranu przedstawiający sekcję autoryzowane domeny.](media/google-federation/google-oauth-authorized-domains.PNG)

9. Wybierz pozycję **Zapisz**.

10. Wybierz pozycję **Poświadczenia**. W menu **Tworzenie poświadczeń** wybierz pozycję **Identyfikator klienta OAuth**:

    ![Zrzut ekranu przedstawiający menu Tworzenie poświadczeń usługi Google API.](media/google-federation/google-api-credentials.png)

11. W obszarze **Typ aplikacji** wybierz pozycję **aplikacja sieci Web**. Nadaj aplikacji odpowiednią nazwę, taką jak **Azure AD B2B**. W obszarze **autoryzowane identyfikatory URI przekierowania** wprowadź następujące identyfikatory URI:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(gdzie `<tenant ID>` jest identyfikator dzierżawy)
   
    > [!NOTE]
    > Aby znaleźć swój identyfikator dzierżawy, przejdź do [Azure Portal](https://portal.azure.com). W obszarze **Azure Active Directory** wybierz pozycję **Właściwości** i skopiuj **Identyfikator dzierżawy**.

    ![Zrzut ekranu przedstawiający sekcję autoryzowane identyfikatory URI przekierowania.](media/google-federation/google-create-oauth-client-id.png)

12. Wybierz pozycję **Utwórz**. Skopiuj identyfikator klienta i klucz tajny klienta. Będziesz ich używać podczas dodawania dostawcy tożsamości w Azure Portal.

    ![Zrzut ekranu pokazujący identyfikator klienta OAuth i klucz tajny klienta.](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Krok 2. Konfigurowanie usługi Google Federation w usłudze Azure AD 
Ustawisz teraz identyfikator klienta Google i klucz tajny klienta. Do tego celu można użyć Azure Portal lub programu PowerShell. Pamiętaj, aby przetestować konfigurację Federacji Google, zapraszając siebie. Użyj adresu Gmail i spróbuj zrealizować zaproszenie przy użyciu zaproszonego konta Google. 

**Aby skonfigurować usługi Google Federation w Azure Portal** 
1. Przejdź do witryny [Azure Portal](https://portal.azure.com). W okienku po lewej stronie wybierz pozycję **Azure Active Directory**. 
2. Wybierz **tożsamości zewnętrzne**.
3. Wybierz pozycję **Wszyscy dostawcy tożsamości**, a następnie wybierz przycisk **Google** .
4. Wprowadź wcześniej otrzymany identyfikator klienta i klucz tajny klienta. Wybierz pozycję **Zapisz**: 

   ![Zrzut ekranu przedstawiający stronę Dodawanie dostawcy usługi Google Identity.](media/google-federation/google-identity-provider.png)

**Aby skonfigurować usługi Google Federation przy użyciu programu PowerShell**
1. Zainstaluj najnowszą wersję modułu PowerShell usługi Azure AD dla programu Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Uruchom następujące polecenie: `Connect-AzureAD`
3. W wierszu logowania zaloguj się przy użyciu zarządzanego konta administratora globalnego.  
4. Uruchom następujące polecenie: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > Użyj identyfikatora klienta i klucza tajnego klienta z aplikacji utworzonej w sekcji "krok 1: Konfigurowanie projektu usługi Google Developer". Aby uzyskać więcej informacji, zobacz polecenie [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Jak mogę usunąć usługi Google Federation?
Możesz usunąć konfigurację usługi Google Federation. W takim przypadku użytkownicy-Goście usługi Google, którzy już skorzystali z zaproszenia, nie będą mogli się zalogować. Można jednak umożliwić im ponowne uzyskanie dostępu do zasobów, usuwając je z katalogu i ponownie zapraszając. 
 
**Aby usunąć usługę Google Federation w portalu usługi Azure AD**
1. Przejdź do witryny [Azure Portal](https://portal.azure.com). W okienku po lewej stronie wybierz pozycję **Azure Active Directory**. 
2. Wybierz **tożsamości zewnętrzne**.
3. Wybierz pozycję **Wszyscy dostawcy tożsamości**.
4. W wierszu **Google** wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Usuń**. 
   
   ![Zrzut ekranu pokazujący przycisk Usuń dla dostawcy tożsamości społecznościowej.](media/google-federation/google-social-identity-providers.png)

1. Wybierz pozycję **tak** , aby potwierdzić usunięcie. 

**Aby usunąć usługi Google Federation przy użyciu programu PowerShell** 
1. Zainstaluj najnowszą wersję modułu PowerShell usługi Azure AD dla programu Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Uruchom polecenie `Connect-AzureAD`.  
4. W wierszu logowania zaloguj się przy użyciu zarządzanego konta administratora globalnego.  
5. Wprowadź następujące polecenie:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Aby uzyskać więcej informacji, zobacz [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview).
