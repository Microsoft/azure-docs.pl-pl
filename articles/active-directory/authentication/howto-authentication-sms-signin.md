---
title: Logowanie użytkownika oparte na wiadomościach SMS na Azure Active Directory
description: Dowiedz się, jak skonfigurować i umożliwić użytkownikom logowanie się do aplikacji Azure Active Directory przy użyciu wiadomości SMS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: b84d55e2d3a2f49a870c1e57eeed3c5c0caeba4a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530416"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory"></a>Konfigurowanie i umożliwianie użytkownikom uwierzytelniania opartego na wiadomościach SMS przy użyciu Azure Active Directory 

Aby uprościć i zabezpieczyć logowanie do aplikacji i usług, usługa Azure Active Directory (Azure AD) oferuje wiele opcji uwierzytelniania. Uwierzytelnianie oparte na wiadomościach SMS umożliwia użytkownikom logowanie się bez konieczności poniania nazwy użytkownika i hasła. Po utworzeniu konta przez administratora tożsamości może on wprowadzić swój numer telefonu w wierszu polecenia logowania. Otrzymują oni kod uwierzytelniania za pośrednictwem wiadomości SMS, który mogą podać w celu ukończenia logowania. Ta metoda uwierzytelniania upraszcza dostęp do aplikacji i usług, szczególnie w przypadku pracowników frontline.

W tym artykule pokazano, jak włączyć uwierzytelnianie oparte na wiadomościach SMS dla wybranych użytkowników lub grup w usłudze Azure AD.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Do ukończenia tego artykułu potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa Azure Active Directory skojarzona z Twoją subskrypcją.
    * W razie potrzeby [utwórz dzierżawę Azure Active Directory lub][create-azure-ad-tenant] [skojarz subskrypcję platformy Azure ze swoim kontem.][associate-azure-ad-tenant]
* Aby włączyć *uwierzytelnianie oparte na wiadomościach* SMS, musisz mieć uprawnienia administratora globalnego w dzierżawie usługi Azure AD.
* Każdy użytkownik włączony w zasadach metody uwierzytelniania wiadomości SMS musi mieć licencję, nawet jeśli jej nie używa. Każdy włączony użytkownik musi mieć jedną z następujących licencji usług Azure AD, EMS Microsoft 365 licencji:
    * [Microsoft 365 (M365) F1 lub F3][m365-firstline-workers-licensing]
    * [Enterprise Mobility + Security (EMS) E3 lub E5][ems-licensing] [albo Microsoft 365 (M365) E3 lub E5][m365-licensing]
    * [Office 365 F3][o365-f3]

## <a name="limitations"></a>Ograniczenia

W przypadku uwierzytelniania opartego na wiadomościach SMS obowiązują następujące ograniczenia:

* Uwierzytelnianie oparte na wiadomościach SMS nie jest obecnie zgodne z usługą Azure AD Multi-Factor Authentication.
* Z wyjątkiem aplikacji Teams uwierzytelnianie oparte na wiadomościach SMS nie jest zgodne z natywnymi aplikacjami pakietu Office.
* Uwierzytelnianie oparte na wiadomościach SMS nie jest zalecane w przypadku kont B2B.
* Użytkownicy federacyjni nie będą uwierzytelniani w dzierżawie głównej. Uwierzytelniają się tylko w chmurze.

## <a name="enable-the-sms-based-authentication-method"></a>Włączanie metody uwierzytelniania opartej na wiadomościach SMS

Istnieją trzy główne kroki włączania i używania uwierzytelniania opartego na wiadomościach SMS w organizacji:

* Włącz zasady metody uwierzytelniania.
* Wybierz użytkowników lub grupy, które mogą używać metody uwierzytelniania opartej na wiadomościach SMS.
* Przypisz numer telefonu dla każdego konta użytkownika.
    * Ten numer telefonu można przypisać w Azure Portal (co pokazano w tym  artykule) oraz w Mój personel *lub Moje konto.*

Najpierw włączmy uwierzytelnianie oparte na wiadomościach SMS dla dzierżawy usługi Azure AD.

1. Zaloguj się do [witryny Azure Portal][azure-portal] jako *administrator globalny.*
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W menu nawigacji po lewej stronie okna usługi Azure Active Directory wybierz pozycję Metody uwierzytelniania usługi Security > > **Metodę uwierzytelniania.**

    [![Przejdź do strony i wybierz okno Zasady metody uwierzytelniania w Azure Portal.](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png)](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. Z listy dostępnych metod uwierzytelniania wybierz pozycję **Wiadomość SMS.**
1. Dla **ustawienia Włącz** ustaw wartość *Tak.*

    ![Włączanie uwierzytelniania tekstowego w oknie zasad metody uwierzytelniania](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    Możesz włączyć uwierzytelnianie oparte na wiadomościach SMS dla *opcji Wszyscy użytkownicy* lub Wybierz *użytkowników* i grupy. W następnej sekcji włączysz uwierzytelnianie oparte na wiadomościach SMS dla użytkownika testowego.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>Przypisywanie metody uwierzytelniania do użytkowników i grup

Po włączeniu uwierzytelniania opartego na wiadomościach SMS w dzierżawie usługi Azure AD wybierz niektórych użytkowników lub grupy, którzy mają mieć możliwość korzystania z tej metody uwierzytelniania.

1. W oknie zasad uwierzytelniania wiadomości SMS ustaw opcję **Cel** na *Wybierz użytkowników.*
1. Wybierz pozycję **Dodaj użytkowników lub grupy,** a następnie wybierz testowego użytkownika lub grupę, na przykład Użytkownik contoso *lub* Użytkownicy programu SMS *firmy Contoso.*

    [![Wybierz użytkowników lub grupy do włączenia uwierzytelniania opartego na wiadomościach SMS w Azure Portal.](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png)](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Po wybraniu użytkowników lub grup wybierz pozycję **Wybierz,** a następnie **zapisz** zaktualizowane zasady metody uwierzytelniania.

Każdy użytkownik włączony w zasadach metody uwierzytelniania wiadomości SMS musi mieć licencję, nawet jeśli jej nie używa. Upewnij się, że masz odpowiednie licencje dla użytkowników włączanych w zasadach metody uwierzytelniania, szczególnie w przypadku włączenia tej funkcji dla dużych grup użytkowników.

## <a name="set-a-phone-number-for-user-accounts"></a>Ustawianie numeru telefonu dla kont użytkowników

Użytkownicy są teraz włączoną obsługą uwierzytelniania opartego na wiadomościach SMS, ale ich numer telefonu musi być skojarzony z profilem użytkownika w usłudze Azure AD, aby można było się zalogować. Użytkownik może [samodzielnie ustawić ten numer telefonu](../user-help/sms-sign-in-explainer.md) na stronie Moje konto lub przypisać go przy użyciu Azure Portal.  Numery telefonów mogą być ustawiane przez *administratorów* globalnych, *administratorów uwierzytelniania* lub *administratorów uwierzytelniania uprzywilejowanego.*

Po skonfigurowaniu numeru telefonu dla funkcji podpisywania wiadomości SMS jest on również dostępny do użycia z uwierzytelnianiem wieloskładnikowym usługi [Azure AD][tutorial-azure-mfa] i [samoobsługowym resetowaniem hasła.][tutorial-sspr]

1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W menu nawigacji po lewej stronie okna Azure Active Directory wybierz pozycję **Użytkownicy.**
1. Wybierz użytkownika, dla których w poprzedniej sekcji włączono uwierzytelnianie oparte na wiadomościach SMS, na przykład Użytkownik *contoso,* a następnie wybierz pozycję **Metody uwierzytelniania.**
1. Wybierz **pozycję + Dodaj metodę uwierzytelniania,** a następnie z menu rozwijanego *Wybierz* metodę wybierz pozycję **Numer telefonu**.

    Wprowadź numer telefonu użytkownika, w tym kod kraju, na przykład *+1 xxxxxxxxx.* Numer Azure Portal sprawdza, czy numer telefonu jest w poprawnym formacie.

    Następnie z menu *rozwijanego Typ* telefonu wybierz pozycję *Mobilne,* *Alternatywne urządzenie* przenośne lub Inne *zgodnie z* potrzebami.

    :::image type="content" source="media/howto-authentication-sms-signin/set-user-phone-number.png" alt-text="Ustawianie numeru telefonu dla użytkownika w aplikacji Azure Portal do użycia z uwierzytelnianiem za pomocą wiadomości SMS":::

    Numer telefonu musi być unikatowy w dzierżawie. Jeśli spróbujesz użyć tego samego numeru telefonu dla wielu użytkowników, zostanie wyświetlony komunikat o błędzie.

1. Aby zastosować numer telefonu do konta użytkownika, wybierz pozycję **Dodaj**.

Po pomyślnej aprowizce zostanie wyświetlony znacznik wyboru dla opcji *Logowanie za pomocą wiadomości SMS z włączoną obsługą*.

## <a name="test-sms-based-sign-in"></a>Testowanie logowania opartego na wiadomościACH SMS

Aby przetestować konto użytkownika, dla których włączono logowanie za pomocą wiadomości SMS, wykonaj następujące kroki:

1. Otwórz nowe okno przeglądarki internetowej InPrivate lub Incognito, aby [https://www.office.com][office]
1. W prawym górnym rogu wybierz pozycję **Zaloguj się.**
1. W wierszu polecenia logowania wprowadź numer telefonu skojarzony z użytkownikiem w poprzedniej sekcji, a następnie wybierz pozycję **Dalej.**

    ![Wprowadź numer telefonu w wierszu polecenia logowania dla użytkownika testowego](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Wiadomość SMS jest wysyłana na podany numer telefonu. Aby ukończyć proces logowania, wprowadź 6-cyfrowy kod podany w wiadomości SMS w wierszu polecenia logowania.

    ![Wprowadź kod potwierdzenia wysłany za pośrednictwem wiadomości SMS na numer telefonu użytkownika](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. Użytkownik jest teraz zalogowany bez konieczności podania nazwy użytkownika lub hasła.

## <a name="troubleshoot-sms-based-sign-in"></a>Rozwiązywanie problemów z logowaniem za pomocą wiadomości SMS

Poniższe scenariusze i kroki rozwiązywania problemów mogą być używane, jeśli masz problemy z włączaniem i używaniem logowania opartego na wiadomościach SMS.

### <a name="phone-number-already-set-for-a-user-account"></a>Numer telefonu już ustawiony dla konta użytkownika

Jeśli użytkownik zarejestrował się już w usłudze Azure AD Multi-Factor Authentication i/lub samoobsługowego resetowania hasła (SSPR), ma już numer telefonu skojarzony z kontem. Ten numer telefonu nie jest automatycznie dostępny do użycia z logowaniem opartym na wiadomościach SMS.

Użytkownik, który ma już ustawiony numer telefonu dla swojego konta, zostanie wyświetlony przycisk Włącz dla logowania za pomocą wiadomości *SMS* na **stronie Mój profil.** Wybierz ten przycisk, aby konto było włączone do użycia z logowaniem opartym na wiadomościach SMS i poprzednią rejestracją usługi Azure AD Multi-Factor Authentication lub funkcji SSPR.

Aby uzyskać więcej informacji na temat obsługi użytkownika końcowego, zobacz Środowisko użytkownika logowania za pomocą wiadomości [SMS dla numeru telefonu](../user-help/sms-sign-in-explainer.md).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Błąd podczas próby ustawienia numeru telefonu na koncie użytkownika

Jeśli podczas próby ustawienia numeru telefonu dla konta użytkownika w portalu Azure Portal, zapoznaj się z następującymi krokami rozwiązywania problemów:

1. Upewnij się, że logowanie oparte na wiadomościach SMS jest włączone.
1. Upewnij się, że konto użytkownika jest włączone w zasadach *metody uwierzytelniania* wiadomości SMS.
1. Upewnij się, że ustawiono numer telefonu z odpowiednim formatowaniem, zgodnie z weryfikacją w Azure Portal (na przykład *+1 4251234567).*
1. Upewnij się, że numer telefonu nie jest używany w innym miejscu w dzierżawie.
1. Sprawdź, czy na koncie nie ustawiono numeru głosu. Jeśli ustawiono numer telefonu, usuń go i spróbuj ponownie użyć numeru telefonu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe sposoby logowania się do usługi Azure AD bez hasła, takie jak klucze zabezpieczeń aplikacji Microsoft Authenticator lub FIDO2, zobacz Opcje uwierzytelniania bez hasła dla usługi [Azure AD.][concepts-passwordless]

Możesz również użyć interfejsu API REST Microsoft Graph, aby włączyć [lub][rest-enable] [wyłączyć][rest-disable] logowanie oparte na wiadomościach SMS.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md
[rest-enable]: /graph/api/phoneauthenticationmethod-enablesmssignin?tabs=http
[rest-disable]: /graph/api/phoneauthenticationmethod-disablesmssignin?tabs=http

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab
