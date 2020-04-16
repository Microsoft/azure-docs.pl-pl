---
title: Dostosowywanie samoobsługowego resetowania hasła — usługa Azure Active Directory
description: Dowiedz się, jak dostosować opcje wyświetlania i środowiska użytkownika do samoobsługowego resetowania hasła usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a45602b80db965e3cc79d188dd40034a320b79
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394256"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Dostosowywanie środowiska użytkownika do samodzielnego resetowania hasła usługi Azure Active Directory

Samoobsługowe resetowanie hasła (SSPR) umożliwia użytkownikom usługi Azure Active Directory (Azure AD) zmianę lub zresetowanie hasła bez udziału administratora lub działu pomocy technicznej. Jeśli konto użytkownika jest zablokowane lub zapomni hasła, może wykonać monity, aby odblokować się i wrócić do pracy. Ta funkcja zmniejsza liczbę połączeń pomocy technicznej i utratę produktywności, gdy użytkownik nie może zalogować się do urządzenia lub aplikacji.

Aby poprawić środowisko samooglądu samospr. Te opcje dostosowywania pozwalają wyjaśnić użytkownikowi, że są we właściwym miejscu, i dać mu pewność, że uzyskuje dostęp do zasobów firmy.
    
W tym artykule pokazano, jak dostosować łącze e-mail samookapła dla użytkowników, znakowanie firmowe i łącze strony logowania usług AD FS.

## <a name="customize-the-contact-your-administrator-link"></a>Dostosowywanie linku "Skontaktuj się z administratorem"

Aby pomóc użytkownikom uzyskać pomoc w samodzielnym resetowania hasła, w portalu resetowania hasła pojawi się link "Skontaktuj się z administratorem". Jeśli użytkownik wybierze to łącze, wykonuje jedną z dwóch czynności:

* Jeśli to łącze kontaktowe pozostaje w stanie domyślnym, do administratorów zostanie wysłana wiadomość e-mail z prośbą o pomoc w zmianie hasła użytkownika. Następująca przykładowa wiadomość e-mail zawiera tę domyślną wiadomość e-mail:

    ![Przykładowe żądanie zresetowania wiadomości e-mail wysłanej do administratora](./media/howto-sspr-customization/sspr-contact-admin.png)

* Jeśli jest dostosowany, wysyła użytkownika na stronę sieci Web lub adres e-mail określony przez administratora w celu uzyskania pomocy.
    * Jeśli dostosujesz to, zalecamy ustawienie tego na coś, co użytkownicy są już zaznajomieni z pomocą techniczną.

    > [!WARNING]
    > Jeśli dostosujesz to ustawienie za pomocą adresu e-mail i konta, które wymaga zresetowania hasła, użytkownik może nie być w stanie poprosić o pomoc.

### <a name="default-email-behavior"></a>Domyślne zachowanie poczty e-mail

Domyślna wiadomość e-mail kontaktu jest wysyłana do adresatów w następującej kolejności:

1. Jeśli przypisana jest rola *administratora pomocy technicznej* lub rola *administratora haseł,* administratorzy z tymi rolami zostaną powiadomieni.
1. Jeśli nie przypisano administratora pomocy technicznej ani administratora haseł, administratorzy z rolą *administratora użytkownika* zostaną powiadomieni.
1. Jeśli żadna z poprzednich ról nie zostanie przypisana, *administratorzy globalni* zostaną powiadomieni.

We wszystkich przypadkach zgłasza się maksymalnie 100 odbiorców.

Aby dowiedzieć się więcej o różnych rolach administratora i sposobie ich [przypisywania, zobacz Przypisywanie ról administratora w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Wyłączanie wiadomości e-mail "Skontaktuj się z administratorem"

Jeśli organizacja nie chce powiadamiać administratorów o żądaniach resetowania hasła, można użyć następujących opcji konfiguracji:

* Dostosuj łącze pomocy technicznej, aby podać internetowy adres URL lub adres mailto: używany przez użytkowników do uzyskania pomocy. Ta opcja znajduje się w obszarze Adres**e-mail lub adres URL niestandardowego pomocy technicznej** **do dostosowywania** > **hasła** > .
* Włącz samoobsługowe resetowanie hasła dla wszystkich użytkowników. Ta opcja znajduje się w obszarze Właściwości **resetowania** > **hasła**. Jeśli nie chcesz, aby użytkownicy resetowali własne hasła, możesz mieć dostęp do pustej grupy. *Nie zalecamy tej opcji.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>Dostosowywanie strony logowania i panelu dostępu

Możesz dostosować stronę logowania, na przykład dodać logo wyświetlane wraz z obrazem, który pasuje do marki twojej firmy. Aby uzyskać więcej informacji na temat konfigurowania znakowania firmy, zobacz [Dodawanie znakowania firmy do strony logowania w usłudze Azure AD](../fundamentals/customize-branding.md).

Wybrana grafika jest wyświetlana w następujących okolicznościach:

* Po wprowadzeniu nazwy użytkownika
* Jeśli użytkownik uzyskuje dostęp do dostosowanego adresu URL:
   * Przekazując `whr` parametr do strony resetowania hasła, np.`https://login.microsoftonline.com/?whr=contoso.com`
   * Przekazując `username` parametr do strony resetowania hasła, np.`https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Nazwa katalogu

Aby uczynić rzeczy bardziej przyjaznymi dla użytkownika, możesz zmienić nazwę organizacji w portalu i w komunikacji zautomatyzowanej. Aby zmienić atrybut nazwy katalogu w witrynie Azure portal, przejdź do **usługi Azure Active Directory** > **Properties**. Ta przyjazna opcja nazwy organizacji jest najbardziej widoczna w automatycznych wiadomościach e-mail, jak w poniższych przykładach:

* Przyjazna nazwa w wiadomości e-mail, na przykład "*Microsoft w imieniu wersji demonstracyjnej CONTOSO*"
* Wiersz tematu w wiadomości e-mail, na przykład "*kod weryfikacyjny konta demo CONTOSO*"

## <a name="customize-the-ad-fs-sign-in-page"></a>Dostosowywanie strony logowania usług AD FS

Jeśli używasz Usług federacyjnych Active Directory (AD FS) dla zdarzeń logowania użytkownika, możesz dodać łącze do strony logowania, korzystając ze wskazówek zawartych w artykule, aby [dodać opis strony logowania](/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Udostępnij użytkownikom łącze do strony, aby mogli wejść do *https://passwordreset.microsoftonline.com*przepływu pracy samooczyszeńcowi, na przykład . Aby dodać łącze do strony logowania usług AD FS, użyj następującego polecenia na serwerze usług AD FS:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>Następne kroki

Aby zrozumieć użycie spr w twoim środowisku, zobacz [Opcje raportowania zarządzania hasłami usługi Azure AD](howto-sspr-reporting.md).

Jeśli ty lub użytkownicy mają problemy z samoobsługowym łazem samoobsługowym, zobacz Rozwiązywanie problemów z [samoobsługowym resetowaniem hasła](active-directory-passwords-troubleshoot.md)
