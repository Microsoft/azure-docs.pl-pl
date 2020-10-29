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
ms.openlocfilehash: ff8912794169cf61f394a097248a8476b2e0c0f3
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926239"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Dodaj firmę Google jako dostawcę tożsamości dla użytkowników gościa B2B

Konfigurując Federacji w usłudze Google, możesz zezwolić zaproszonym użytkownikom na logowanie się do udostępnionych aplikacji i zasobów przy użyciu własnych kont usługi Gmail, bez konieczności tworzenia kont Microsoft. 

> [!NOTE]
> Firma Google Federation została zaprojektowana specjalnie dla użytkowników usługi Gmail. Aby sfederować z domenami usługi G Suite, należy użyć [Federacji bezpośredniej](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Co to jest środowisko użytkownika Google?
Po wysłaniu zaproszenia do użytkowników usługi Google Gmail użytkownicy-Goście powinni uzyskiwać dostęp do udostępnionych aplikacji lub zasobów przy użyciu linku zawierającego kontekst dzierżawy. Ich środowisko pracy różni się w zależności od tego, czy są już zalogowane w usłudze Google:
  - Użytkownicy-Goście, którzy nie są zalogowani do usługi Google, będą monitowani o to.
  - Użytkownicy-Goście, którzy zostali już zalogowani w usłudze Google, będą monitowani o wybranie konta, którego chcą użyć. Muszą wybrać konto użyte do zaproszenia.

Użytkownicy-Goście, którzy zobaczą "nagłówek zbyt długi", mogą wyczyścić swoje pliki cookie lub otworzyć okno prywatne lub incognito, a następnie spróbować zalogować się ponownie.

![Zrzut ekranu przedstawiający stronę logowania Google.](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Ograniczenia

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
3. Utwórz nowy projekt: na pulpicie nawigacyjnym wybierz pozycję **Utwórz projekt** , Nadaj projektowi nazwę (np. **Azure AD B2B** ), a następnie wybierz pozycję **Utwórz** : 
   
   ![Zrzut ekranu pokazujący nową stronę projektu.](media/google-federation/google-new-project.png)

4. Na stronie **& usług API Services** wybierz pozycję **Widok** pod nowym projektem.

5. Wybierz pozycję **Przejdź do interfejsów API przegląd** na karcie interfejsy API. Wybierz **ekran wyrażania zgody na uwierzytelnianie OAuth** .

6. Wybierz pozycję **zewnętrzny** , a następnie wybierz pozycję **Utwórz** . 

7. Na **ekranie wyrażanie zgody na uwierzytelnianie OAuth** wprowadź **nazwę aplikacji** :

   ![Zrzut ekranu przedstawiający ekran wyrażania zgody na usługi Google OAuth.](media/google-federation/google-oauth-consent-screen.png)

8. Przewiń do sekcji **autoryzowane domeny** i wprowadź **microsoftonline.com** :

   ![Zrzut ekranu przedstawiający sekcję autoryzowane domeny.](media/google-federation/google-oauth-authorized-domains.PNG)

9. Wybierz pozycję **Zapisz** .

10. Wybierz pozycję **Poświadczenia** . W menu **Tworzenie poświadczeń** wybierz pozycję **Identyfikator klienta OAuth** :

    ![Zrzut ekranu przedstawiający menu Tworzenie poświadczeń usługi Google API.](media/google-federation/google-api-credentials.png)

11. W obszarze **Typ aplikacji** wybierz pozycję **aplikacja sieci Web** . Nadaj aplikacji odpowiednią nazwę, taką jak **Azure AD B2B** . W obszarze **autoryzowane identyfikatory URI przekierowania** wprowadź następujące identyfikatory URI:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(gdzie `<tenant ID>` jest identyfikator dzierżawy)
   
    > [!NOTE]
    > Aby znaleźć swój identyfikator dzierżawy, przejdź do [Azure Portal](https://portal.azure.com). W obszarze **Azure Active Directory** wybierz pozycję **Właściwości** i skopiuj **Identyfikator dzierżawy** .

    ![Zrzut ekranu przedstawiający sekcję autoryzowane identyfikatory URI przekierowania.](media/google-federation/google-create-oauth-client-id.png)

12. Wybierz pozycję **Utwórz** . Skopiuj identyfikator klienta i klucz tajny klienta. Będziesz ich używać podczas dodawania dostawcy tożsamości w Azure Portal.

    ![Zrzut ekranu pokazujący identyfikator klienta OAuth i klucz tajny klienta.](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Krok 2. Konfigurowanie usługi Google Federation w usłudze Azure AD 
Ustawisz teraz identyfikator klienta Google i klucz tajny klienta. Do tego celu można użyć Azure Portal lub programu PowerShell. Pamiętaj, aby przetestować konfigurację Federacji Google, zapraszając siebie. Użyj adresu Gmail i spróbuj zrealizować zaproszenie przy użyciu zaproszonego konta Google. 

**Aby skonfigurować usługi Google Federation w Azure Portal** 
1. Przejdź do witryny [Azure Portal](https://portal.azure.com). W okienku po lewej stronie wybierz pozycję **Azure Active Directory** . 
2. Wybierz **tożsamości zewnętrzne** .
3. Wybierz pozycję **Wszyscy dostawcy tożsamości** , a następnie wybierz przycisk **Google** .
4. Wprowadź wcześniej otrzymany identyfikator klienta i klucz tajny klienta. Wybierz pozycję **Zapisz** : 

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
Możesz usunąć konfigurację usługi Google Federation. W takim przypadku użytkownicy-Goście usługi Google, którzy już skorzystali z zaproszenia, nie będą mogli się zalogować. Można jednak ponownie uzyskać dostęp do swoich zasobów, usuwając je z katalogu i ponownie zapraszając. 
 
**Aby usunąć usługę Google Federation w portalu usługi Azure AD**
1. Przejdź do witryny [Azure Portal](https://portal.azure.com). W okienku po lewej stronie wybierz pozycję **Azure Active Directory** . 
2. Wybierz **tożsamości zewnętrzne** .
3. Wybierz pozycję **Wszyscy dostawcy tożsamości** .
4. W wierszu **Google** wybierz przycisk wielokropka ( **...** ), a następnie wybierz pozycję **Usuń** . 
   
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
