---
title: Logowanie użytkownika opartego na programie SMS dla Azure Active Directory
description: Dowiedz się, jak skonfigurować i umożliwić użytkownikom logowanie się do Azure Active Directory przy użyciu programu SMS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd2a38e3d59af18477a1e5bb11e8b58dbbe81e0b
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471668"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory"></a>Konfigurowanie i umożliwienie użytkownikom uwierzytelniania opartego na programie SMS przy użyciu Azure Active Directory 

Aby uprościć i bezpiecznie zalogować się do aplikacji i usług, Azure Active Directory (Azure AD) udostępnia wiele opcji uwierzytelniania. Uwierzytelnianie oparte na programie SMS umożliwia użytkownikom logowanie się bez podawania nazwy użytkownika i hasła, a nawet ich znajomości. Po utworzeniu konta przez administratora tożsamości można wprowadzić numer telefonu w wierszu logowania. Otrzymują oni kod uwierzytelniania za pośrednictwem wiadomości SMS, którą mogą zapewnić, aby zakończyć logowanie. Ta metoda uwierzytelniania upraszcza dostęp do aplikacji i usług, szczególnie w przypadku procesów roboczych teraźniejszości.

W tym artykule opisano sposób włączania uwierzytelniania opartego na programie SMS dla wybranych użytkowników lub grup w usłudze Azure AD.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby wykonać ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z Twoją subskrypcją.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Musisz mieć uprawnienia *administratora globalnego* w dzierżawie usługi Azure AD, aby włączyć uwierzytelnianie oparte na programie SMS.
* Każdy użytkownik, który jest włączony w zasadach metody uwierzytelniania wiadomości tekstowych musi być licencjonowany, nawet jeśli nie używa tego programu. Każdy włączony użytkownik musi mieć jedną z następujących licencji usługi Azure AD, usług EMS i Microsoft 365:
    * [Microsoft 365 (M365) F1 lub F3][m365-firstline-workers-licensing]
    * [Enterprise Mobility + Security (EMS) E3 lub E5][ems-licensing] lub [Microsoft 365 (M365) E3 lub E5][m365-licensing]

## <a name="limitations"></a>Ograniczenia

Do uwierzytelniania opartego na programie SMS mają zastosowanie następujące ograniczenia:

* Uwierzytelnianie oparte na programie SMS nie jest obecnie zgodne z usługą Azure AD Multi-Factor Authentication.
* Uwierzytelnianie oparte na programie SMS nie jest jednak zgodne z natywnymi aplikacjami pakietu Office.
* Uwierzytelnianie oparte na programie SMS nie jest zalecane w przypadku kont B2B.
* Użytkownicy federacyjny nie będą uwierzytelniani w dzierżawie głównej. Są one uwierzytelniane tylko w chmurze.

## <a name="enable-the-sms-based-authentication-method"></a>Włącz metodę uwierzytelniania opartą na programie SMS

Istnieją trzy główne kroki umożliwiające włączenie uwierzytelniania opartego na programie SMS i korzystanie z niego w organizacji:

* Włącz zasady metody uwierzytelniania.
* Wybierz użytkowników lub grupy, które mogą korzystać z metody uwierzytelniania opartej na programie SMS.
* Przypisz numer telefonu do każdego konta użytkownika.
    * Ten numer telefonu można przypisać do Azure Portal (który jest przedstawiony w tym artykule) i w obszarze *mój personel* lub *Moje konto*.

Najpierw Włącz uwierzytelnianie SMS dla dzierżawy usługi Azure AD.

1. Zaloguj się do [Azure Portal][azure-portal] jako *administrator globalny*.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W menu nawigacji po lewej stronie okna Azure Active Directory wybierz pozycję **zabezpieczenia > metody uwierzytelniania > zasady metody uwierzytelniania**.

    [![Przejdź do okna zasady metody uwierzytelniania i wybierz je w Azure Portal.](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png)](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. Z listy dostępnych metod uwierzytelniania wybierz pozycję **wiadomość tekstowa**.
1. Ustaw opcję **Włącz** na *wartość tak*.

    ![Włącz uwierzytelnianie tekstu w oknie Zasady metody uwierzytelniania](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    Możesz włączyć uwierzytelnianie oparte na programie SMS dla *wszystkich użytkowników* lub *wybrać użytkowników* i grupy. W następnej sekcji zostanie włączone uwierzytelnianie SMS dla użytkownika testowego.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>Przypisywanie metody uwierzytelniania do użytkowników i grup

Korzystając z uwierzytelniania opartego na programie SMS w dzierżawie usługi Azure AD, wybierz niektórych użytkowników lub grupy, aby zezwolić na korzystanie z tej metody uwierzytelniania.

1. W oknie zasady uwierzytelniania wiadomości tekstowych ustaw opcję **cel** na *Wybierz użytkowników*.
1. Wybierz opcję **dodania użytkowników lub grup**, a następnie wybierz użytkownika lub grupę testową, na przykład użytkowników *contoso* lub *contoso SMS*.

    [![Wybierz pozycję Użytkownicy lub grupy, aby włączyć uwierzytelnianie oparte na programie SMS w Azure Portal.](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png)](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Po wybraniu użytkowników lub grup wybierz pozycję **Wybierz**, a następnie **Zapisz** zaktualizowane zasady metody uwierzytelniania.

Każdy użytkownik, który jest włączony w zasadach metody uwierzytelniania wiadomości tekstowych musi być licencjonowany, nawet jeśli nie używa tego programu. Upewnij się, że masz odpowiednie licencje dla użytkowników, których można włączyć w zasadach metody uwierzytelniania, szczególnie w przypadku włączenia funkcji dla dużych grup użytkowników.

## <a name="set-a-phone-number-for-user-accounts"></a>Ustawianie numeru telefonu dla kont użytkowników

Użytkownicy są teraz włączeni do uwierzytelniania za pomocą programu SMS, ale ich numer telefonu musi być skojarzony z profilem użytkownika w usłudze Azure AD przed zalogowaniem się. Użytkownik może [ustawić ten numer telefonu](../user-help/sms-sign-in-explainer.md) w ramach *mojego konta* lub przypisać numer telefonu przy użyciu Azure Portal. Numery telefonów mogą być ustawiane przez *administratorów globalnych*, *administratorów uwierzytelniania* lub *administratorów uwierzytelniania uprzywilejowanego*.

Po ustawieniu numeru telefonu dla wiadomości SMS jest on również dostępny do użycia z [usługą Azure AD Multi-Factor Authentication][tutorial-azure-mfa] i samoobsługowego [resetowania hasła][tutorial-sspr].

1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W menu nawigacji po lewej stronie okna Azure Active Directory wybierz pozycję **Użytkownicy**.
1. Wybierz użytkownika, który został włączony dla uwierzytelniania opartego na programie SMS w poprzedniej sekcji, na przykład *użytkownik firmy Contoso*, a następnie wybierz pozycję **metody uwierzytelniania**.
1. Wybierz pozycję **+ Dodaj metodę uwierzytelniania**, a następnie z menu rozwijanego *Wybierz metodę* wybierz **numer telefonu**.

    Wprowadź numer telefonu użytkownika, w tym kod kraju, taki jak *+ 1 xxxxxxxxx*. Azure Portal sprawdza poprawność numeru telefonu.

    Następnie z menu rozwijanego *Typ telefonu* wybierz pozycję *Mobile*, *alternatywny Mobile* lub *inne* w zależności od wymagań.

    :::image type="content" source="media/howto-authentication-sms-signin/set-user-phone-number.png" alt-text="Ustaw numer telefonu dla użytkownika w Azure Portal, który ma być używany z uwierzytelnianiem na podstawie programu SMS":::

    Numer telefonu musi być unikatowy w dzierżawie. Jeśli spróbujesz użyć tego samego numeru telefonu dla wielu użytkowników, zostanie wyświetlony komunikat o błędzie.

1. Aby zastosować numer telefonu do konta użytkownika, wybierz pozycję **Dodaj**.

Po pomyślnym zainicjowaniu obsługi zostanie wyświetlony znacznik wyboru dla opcji *Logowanie za pomocą programu SMS*.

## <a name="test-sms-based-sign-in"></a>Testuj logowanie oparte na programie SMS

Aby przetestować konto użytkownika, które jest teraz włączone na potrzeby logowania opartego na programie SMS, wykonaj następujące czynności:

1. Otwórz nowe, InPrivate lub incognito okno przeglądarki sieci Web, aby [https://www.office.com][office]
1. W prawym górnym rogu wybierz pozycję **Zaloguj**.
1. W wierszu logowania wprowadź numer telefonu skojarzony z użytkownikiem w poprzedniej sekcji, a następnie wybierz przycisk **dalej**.

    ![Wprowadź numer telefonu w monicie logowania dla użytkownika testowego](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Wiadomość tekstowa jest wysyłana na podany numer telefonu. Aby ukończyć proces logowania, wprowadź 6-cyfrowy kod podany w wiadomości tekstowej w monicie logowania.

    ![Wprowadź kod potwierdzenia wysłany za pośrednictwem wiadomości tekstowej do numeru telefonu użytkownika](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. Użytkownik jest obecnie zalogowany bez konieczności podania nazwy użytkownika lub hasła.

## <a name="troubleshoot-sms-based-sign-in"></a>Rozwiązywanie problemów z logowaniem opartego na programie SMS

W przypadku problemów z włączaniem i używaniem logowania opartego na programie SMS można użyć następujących scenariuszy i kroków rozwiązywania problemów.

### <a name="phone-number-already-set-for-a-user-account"></a>Numer telefonu został już ustawiony dla konta użytkownika

Jeśli użytkownik zarejestrował się już w usłudze Azure AD Multi-Factor Authentication i/lub funkcji samoobsługowego resetowania hasła (SSPR), ma już przypisany numer telefonu do konta. Ten numer telefonu nie jest automatycznie dostępny do użycia z logowaniem opartym na programie SMS.

Użytkownik, który ma już numer telefonu ustawiony dla swojego konta, jest wyświetlany przycisk umożliwiający *Logowanie za pomocą programu SMS* na stronie **mój profil** . Wybierz ten przycisk, a konto jest włączone na potrzeby logowania opartego na programie SMS i poprzedniej Multi-Factor Authentication rejestracji w usłudze Azure AD.

Aby uzyskać więcej informacji na temat środowiska użytkownika końcowego, zobacz [środowisko użytkownika logowania do programu SMS dla numeru telefonu](../user-help/sms-sign-in-explainer.md).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Wystąpił błąd podczas próby ustawienia numeru telefonu na koncie użytkownika

Jeśli podczas próby ustawienia numeru telefonu dla konta użytkownika w Azure Portal wystąpi błąd, zapoznaj się z następującymi krokami rozwiązywania problemów:

1. Upewnij się, że włączono obsługę logowania przy użyciu programu SMS.
1. Upewnij się, że konto użytkownika jest włączone w zasadach metody uwierzytelniania *wiadomości tekstowych* .
1. Upewnij się, że ustawiono numer telefonu z właściwym formatowaniem, zgodnie z opisem w Azure Portal (na przykład *+ 1 4251234567*).
1. Upewnij się, że numer telefonu nie jest używany w innym miejscu dzierżawy.
1. Sprawdź, czy na koncie nie ustawiono numeru głosu. Jeśli ustawiono numer telefonu, Usuń i spróbuj ponownie.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe możliwości logowania się do usługi Azure AD bez hasła, takiego jak Microsoft Authenticator App lub FIDO2 Security, zobacz [Opcje uwierzytelniania bez hasła dla usługi Azure AD][concepts-passwordless].

Możesz również użyć interfejsu API REST w wersji beta Microsoft Graph, aby [włączyć][rest-enable] lub [wyłączyć][rest-disable] logowanie oparte na programie SMS.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md
[rest-enable]: /graph/api/phoneauthenticationmethod-enablesmssignin?view=graph-rest-beta&tabs=http
[rest-disable]: /graph/api/phoneauthenticationmethod-disablesmssignin?view=graph-rest-beta&tabs=http

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab
