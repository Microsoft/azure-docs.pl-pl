---
title: Dostosuj funkcję samoobsługowego resetowania haseł — Azure Active Directory
description: Dowiedz się, jak dostosować opcje wyświetlania i używania użytkowników w usłudze Azure AD Samoobsługowe resetowanie hasła
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: e121a6a45ab20055591a3670152114b8d03529cf
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173998"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Dostosuj środowisko użytkownika Azure Active Directory samoobsługowego resetowania hasła

Funkcja samoobsługowego resetowania hasła (SSPR) zapewnia użytkownikom w Azure Active Directory (Azure AD) możliwość zmiany lub resetowania hasła oraz braku administratora lub pomocy technicznej. Jeśli konto użytkownika jest zablokowane lub zapomni swoje hasło, może wykonać monit o odblokowanie siebie i zawracanie do pracy. Ta możliwość zmniejsza liczbę wywołań pomocy technicznej i utratę produktywności, gdy użytkownik nie może zalogować się na urządzeniu ani w aplikacji.

Aby ulepszyć środowisko SSPR dla użytkowników, możesz dostosować wygląd i działanie strony resetowania hasła, powiadomień e-mail lub stron logowania. Te opcje dostosowywania umożliwiają poinformowanie użytkownika, że znajdują się w odpowiednim miejscu i zapewniają im pewność, że uzyskują dostęp do zasobów firmy.
    
W tym artykule opisano sposób dostosowywania linku do poczty e-mail SSPR dla użytkowników, znakowania firmowego i AD FS linku do strony logowania.

## <a name="customize-the-contact-your-administrator-link"></a>Dostosuj link "Skontaktuj się z administratorem"

Aby ułatwić użytkownikom kontaktowanie się z pomocą techniczną w celu uzyskania pomocy w przypadku samoobsługowego resetowania hasła, w portalu resetowania haseł zostanie wyświetlone łącze "Skontaktuj się z administratorem". Jeśli użytkownik wybierze ten link, wykonuje jedną z dwóch czynności:

* Jeśli łącze do kontaktu jest pozostawione w domyślnym stanie, do administratorów jest wysyłana wiadomość e-mail z prośbą o pomoc w zmianie hasła użytkownika. Poniższy Przykładowa wiadomość e-mail przedstawia tę domyślną wiadomość e-mail:

    ![Przykładowe żądanie zresetowania wiadomości e-mail wysłanej do administratora](./media/howto-sspr-customization/sspr-contact-admin.png)

* Jeśli jest to dostosowane, program wysyła użytkownika do strony sieci Web lub adresu e-mail określonego przez administratora w celu uzyskania pomocy.
    * W przypadku dostosowania tego ustawienia zalecamy ustawienie tej opcji dla użytkowników, którzy już znają, aby uzyskać pomoc techniczną.

    > [!WARNING]
    > Jeśli to ustawienie zostanie dostosowane przy użyciu adresu e-mail i konta wymagającego zresetowania hasła, użytkownik może nie być w stanie uzyskać pomocy.

### <a name="default-email-behavior"></a>Domyślne zachowanie poczty e-mail

Domyślny kontaktowy adres e-mail jest wysyłany do adresatów w następującej kolejności:

1. Jeśli przypisano rolę *administratora pomocy technicznej* lub *administratora hasła* , Administratorzy z tymi rolami będą powiadamiani.
1. Jeśli nie przypisano administratora pomocy technicznej ani administratora hasła, Administratorzy z rolą *administratora użytkownika* są powiadamiani.
1. Jeśli żadna z poprzednich ról nie zostanie przypisana, *administratorzy globalni* są powiadamiani.

We wszystkich przypadkach powiadamia się maksymalnie 100 adresatów.

Aby dowiedzieć się więcej o różnych rolach administratorów i sposobach ich przypisywania, zobacz [Przypisywanie ról administratorów w Azure Active Directory](../roles/permissions-reference.md).

### <a name="disable-contact-your-administrator-emails"></a>Wyłącz wiadomości e-mail "Skontaktuj się z administratorem"

Jeśli organizacja nie chce powiadamiać administratorów o żądaniach resetowania haseł, można użyć następujących opcji konfiguracji:

* Dostosuj link do pomocy technicznej, aby podać internetowy adres URL lub mailto: adres, za pomocą którego użytkownicy mogą uzyskać pomoc. Ta opcja jest w obszarze Dostosowywanie **resetowania hasła**  >  **Customization**  >  **adres e-mail lub adres URL**.
* Włącz Samoobsługowe resetowanie haseł dla wszystkich użytkowników. Ta opcja jest w obszarze właściwości **resetowania hasła**  >  **Properties**. Jeśli nie chcesz, aby użytkownicy zresetują swoje hasła, możesz ograniczyć dostęp do pustej grupy. *Nie zalecamy tej opcji.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>Dostosowywanie strony logowania i panelu dostępu

Możesz dostosować stronę logowania, na przykład, aby dodać logo, które jest wyświetlane wraz z obrazem, który pasuje do marki firmy. Aby uzyskać więcej informacji na temat konfigurowania znakowania firmowego, zobacz [Dodawanie znakowania firmowego do strony logowania w usłudze Azure AD](../fundamentals/customize-branding.md).

Wybrane grafiki są wyświetlane w następujących okolicznościach:

* Gdy użytkownik wprowadzi swoją nazwę użytkownika
* Jeśli użytkownik uzyskuje dostęp do dostosowanego adresu URL:
   * Przekazując `whr` parametr do strony resetowania hasła, na przykład `https://login.microsoftonline.com/?whr=contoso.com`
   * Przekazując `username` parametr do strony resetowania hasła, na przykład `https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Nazwa katalogu

Aby elementy wyglądały lepiej przyjazny dla użytkownika, możesz zmienić nazwę organizacji w portalu i w zautomatyzowanym komunikacie. Aby zmienić atrybut nazwy katalogu w Azure Portal, przejdź do **Azure Active Directory**  >  **Właściwości**. Ta przyjazna nazwa organizacji jest najbardziej widoczna w zautomatyzowanych wiadomościach e-mail, jak w następujących przykładach:

* Przyjazna nazwa w wiadomości e-mail, na przykład "*firma Microsoft w imieniu firmy Contoso demonstracyjna*"
* Wiersz tematu w wiadomości e-mail, na przykład "*kod weryfikacyjny e-mail konta demonstracji contoso*"

## <a name="customize-the-ad-fs-sign-in-page"></a>Dostosowywanie strony logowania AD FS

Jeśli używasz Active Directory Federation Services (AD FS) dla zdarzeń logowania użytkowników, możesz dodać link do strony logowania, korzystając ze wskazówek zawartych w artykule, aby [dodać opis strony logowania](/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Podaj użytkownikom link do strony, aby wprowadzić przepływ pracy SSPR, taki jak *https://passwordreset.microsoftonline.com* . Aby dodać łącze do strony logowania AD FS, użyj następującego polecenia na serwerze AD FS:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>Następne kroki

Aby zrozumieć użycie SSPR w środowisku, zobacz temat [Opcje raportowania dotyczące zarządzania hasłami w usłudze Azure AD](howto-sspr-reporting.md).

Jeśli masz problemy z SSPR, zobacz Rozwiązywanie problemów z funkcją samoobsługowego [resetowania hasła](./troubleshoot-sspr.md)