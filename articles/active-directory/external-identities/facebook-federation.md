---
title: Dodawanie usługi Facebook jako dostawcy tożsamości — Azure AD
description: Sfederować z usługą Facebook, aby umożliwić użytkownikom zewnętrznym logowanie się do aplikacji usługi Azure AD przy użyciu własnych kont w serwisie Facebook.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 547b455dc776b7304e413b3b6f1330e7cedcf2a2
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442001"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>Dodawanie usługi Facebook jako dostawcy tożsamości dla tożsamości zewnętrznych

Możesz dodać usługę Facebook do swoich przepływów użytkownika samoobsługowego rejestrowania (wersja zapoznawcza), aby użytkownicy mogli logować się do aplikacji przy użyciu własnych kont w serwisie Facebook. Aby umożliwić użytkownikom logowanie się przy użyciu usługi Facebook, należy najpierw włączyć samoobsługowe [Rejestrowanie](self-service-sign-up-user-flow.md) w dzierżawie. Po dodaniu usługi Facebook jako dostawcy tożsamości Skonfiguruj przepływ użytkownika dla aplikacji i wybierz pozycję Facebook jako jedną z opcji logowania.

> [!NOTE]
> Użytkownicy mogą korzystać tylko z kont w serwisie Facebook, aby zarejestrować się w aplikacjach za pomocą rejestracji samoobsługowej i przepływów użytkowników. Użytkownicy nie mogą zostać zaproszeni i korzystać z zaproszenia przy użyciu konta w serwisie Facebook.

## <a name="create-an-app-in-the-facebook-developers-console"></a>Tworzenie aplikacji w konsoli deweloperów usługi Facebook

Aby użyć konta w serwisie Facebook jako [dostawcy tożsamości](identity-providers.md), musisz utworzyć aplikację w konsoli deweloperów serwisu Facebook. Jeśli nie masz jeszcze konta w serwisie Facebook, możesz zarejestrować się w usłudze [https://www.facebook.com/](https://www.facebook.com) .

> [!NOTE]  
> Użyj następujących adresów URL w krokach 9 i 16 poniżej.
> - W polu **adres URL witryny** wprowadź adres aplikacji, na przykład `https://contoso.com` .
> - W przypadku **prawidłowych identyfikatorów URI przekierowania OAuth**wprowadź `https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp` . Możesz znaleźć `<tenant-ID>` w bloku przegląd Azure Active Directory.


1. Zaloguj się do usługi [Facebook dla deweloperów](https://developers.facebook.com/) przy użyciu poświadczeń konta w serwisie Facebook.
2. Jeśli jeszcze tego nie zrobiono, należy zarejestrować się jako deweloper w serwisie Facebook. W tym celu wybierz pozycję **Rozpocznij** w prawym górnym rogu strony, zaakceptuj zasady serwisu Facebook i wykonaj kroki rejestracji.
3. Wybierz pozycję **Moje aplikacje** , a następnie **Utwórz aplikację**.
4. Wprowadź **nazwę wyświetlaną** i prawidłowy **kontaktowy adres e-mail**.
5. Wybierz pozycję **Utwórz identyfikator aplikacji**. Może to wymagać zaakceptowania zasad platformy Facebook i ukończenia kontroli zabezpieczeń w trybie online.
6. Wybierz pozycję **Ustawienia**  >  **podstawowe**.
7. Wybierz **kategorię**, na przykład firmowe i strony. Ta wartość jest wymagana przez serwis Facebook, ale nie jest używana w usłudze Azure AD.
8. W dolnej części strony wybierz pozycję **Dodaj platformę**, a następnie wybierz pozycję **Witryna sieci Web**.
9. W polu **adres URL witryny**wprowadź odpowiedni adres URL (wymieniony powyżej).
10. W polu **adres URL zasad ochrony prywatności**wprowadź adres URL strony, na której są przechowywane informacje o prywatności aplikacji, na przykład `http://www.contoso.com` .
11. Wybierz pozycję **Zapisz zmiany**.
12. W górnej części strony skopiuj wartość **Identyfikator aplikacji**.
13. Wybierz pozycję **Pokaż** i skopiuj wartość **wpisu tajnego aplikacji**. Oba te elementy umożliwiają skonfigurowanie usługi Facebook jako dostawcy tożsamości w dzierżawie. **Wpis tajny aplikacji** jest ważnym poświadczeniem zabezpieczeń.
14. Wybierz znak plus obok pozycji **produkty**, a następnie wybierz pozycję **Konfiguruj** w obszarze **Logowanie do serwisu Facebook**.
15. W obszarze **Logowanie do serwisu Facebook**wybierz pozycję **Ustawienia**.
16. W **prawidłowych identyfikatorach URI przekierowania OAuth**wprowadź odpowiedni adres URL (wymieniony powyżej).
17. Wybierz pozycję **Zapisz zmiany** w dolnej części strony.
18. Aby udostępnić aplikację Facebook do usługi Azure AD, wybierz selektor stanu w prawym górnym rogu strony i włącz go, aby udostępnić **aplikację jako** publiczną, a następnie wybierz pozycję **Przełącz tryb**. W tym momencie stan powinien ulec zmianie z **opracowywania** na na **żywo**.
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurowanie konta w serwisie Facebook jako dostawcy tożsamości
Teraz ustawisz identyfikator klienta i klucz tajny klienta w serwisie Facebook, wprowadzając go w portalu usługi Azure AD lub przy użyciu programu PowerShell. Konfigurację w serwisie Facebook można przetestować, rejestrując się w ramach przepływu użytkownika w aplikacji z włączoną obsługą rejestracji samoobsługowej.

### <a name="to-configure-facebook-federation-in-the-azure-ad-portal"></a>Aby skonfigurować Federacji w serwisie Facebook w portalu usługi Azure AD
1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny dzierżawy usługi Azure AD.
2. W obszarze **usługi platformy Azure**wybierz pozycję **Azure Active Directory**.
3. W menu po lewej stronie wybierz pozycję **tożsamości zewnętrzne**.
4. Wybierz pozycję **Wszyscy dostawcy tożsamości**, a następnie wybierz pozycję **Facebook**.
5. W polu **Identyfikator klienta**wprowadź **Identyfikator** aplikacji w usłudze Facebook, który został utworzony wcześniej.
6. W polu **klucz tajny klienta**Wprowadź zarejestrowany **wpis tajny aplikacji** .

   ![Zrzut ekranu przedstawiający stronę Dodawanie dostawcy tożsamości społecznościowej](media/facebook-federation/add-social-identity-provider-page.png)

7. Wybierz pozycję **Zapisz**.
### <a name="to-configure-facebook-federation-by-using-powershell"></a>Aby skonfigurować Federacji Facebook przy użyciu programu PowerShell
1. Zainstaluj najnowszą wersję modułu PowerShell usługi Azure AD dla programu Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Uruchom następujące polecenie: `Connect-AzureAD` .
3. W wierszu logowania zaloguj się przy użyciu zarządzanego konta administratora globalnego.  
4. Uruchom następujące polecenie: 
   
   `New-AzureADMSIdentityProvider -Type Facebook -Name Facebook -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Użyj identyfikatora klienta i klucza tajnego klienta z utworzonej wcześniej aplikacji w konsoli dewelopera serwisu Facebook. Aby uzyskać więcej informacji, zobacz artykuł [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) . 

## <a name="how-do-i-remove-facebook-federation"></a>Jak mogę usunąć Federacji Facebook?
Możesz usunąć konfigurację Federacji w serwisie Facebook. Jeśli to zrobisz, wszyscy użytkownicy, którzy zarejestrowali się w przepływach użytkowników przy użyciu ich kont w serwisie Facebook, nie będą już mogli się zalogować. 

### <a name="to-delete-facebook-federation-in-the-azure-ad-portal"></a>Aby usunąć Federację w serwisie Facebook w portalu usługi Azure AD: 
1. Przejdź do witryny [Azure Portal](https://portal.azure.com). W lewym okienku wybierz pozycję **Azure Active Directory**. 
2. Wybierz **tożsamości zewnętrzne**.
3. Wybierz pozycję **Wszyscy dostawcy tożsamości**.
4. W wierszu w **serwisie Facebook** wybierz menu kontekstowe (**...**), a następnie wybierz pozycję **Usuń**. 
5. Wybierz pozycję **tak** , aby potwierdzić usunięcie.

### <a name="to-delete-facebook-federation-by-using-powershell"></a>Aby usunąć Federacji Facebook przy użyciu programu PowerShell: 
1. Zainstaluj najnowszą wersję modułu PowerShell usługi Azure AD dla programu Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Należy uruchomić polecenie `Connect-AzureAD`.  
4. W wierszu logowania zaloguj się przy użyciu zarządzanego konta administratora globalnego.  
5. Wprowadź następujące polecenie:

    `Remove-AzureADMSIdentityProvider -Id Facebook-OAUTH`

   > [!NOTE]
   > Aby uzyskać więcej informacji, zobacz [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 

## <a name="next-steps"></a>Następne kroki

- [Dodawanie funkcji samoobsługowego rejestrowania do aplikacji](self-service-sign-up-user-flow.md)