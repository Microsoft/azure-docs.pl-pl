---
title: Logowanie się użytkownika opartego na programie SMS do usługi Azure Active Directory
description: Dowiedz się, jak skonfigurować i umożliwić użytkownikom logowanie się do usługi Azure Active Directory za pomocą programu SMS (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: 845948d9aec28ee79a11fb11aaef4cfbf1b263fa
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770548"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>Konfigurowanie i włączanie użytkowników do uwierzytelniania opartego na programie SMS przy użyciu usługi Azure Active Directory (wersja zapoznawcza)

Aby zmniejszyć złożoność i zagrożenia bezpieczeństwa dla użytkowników, aby zalogować się do aplikacji i usług, usługa Azure Active Directory (Azure AD) udostępnia wiele opcji uwierzytelniania. Uwierzytelnianie oparte na sms-ie, obecnie w wersji zapoznawczej, umożliwia użytkownikom logowanie się bez konieczności podawania, a nawet znać swoją nazwę użytkownika i hasło. Po utworzeniu konta przez administratora tożsamości mogą wprowadzić swój numer telefonu w wierszu logowania i podać kod uwierzytelniający, który jest do nich wysyłany za pośrednictwem wiadomości TEKSTOWEJ. Ta metoda uwierzytelniania upraszcza dostęp do aplikacji i usług, szczególnie dla pracowników pierwszej linii.

W tym artykule pokazano, jak włączyć uwierzytelnianie oparte na programie SMS dla wybranych użytkowników lub grup w usłudze Azure AD.

|     |
| --- |
| Uwierzytelnianie oparte na programie SMS dla użytkowników jest publiczną funkcją w wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawców, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Aby włączyć uwierzytelnianie oparte na usłudze SMS, potrzebujesz uprawnień *administratora globalnego* w dzierżawie usługi Azure AD.
* Każdy użytkownik, który jest włączony w zasadach metody uwierzytelniania wiadomości tekstowych musi mieć licencję, nawet jeśli nie używa go. Każdy włączony użytkownik musi mieć jedną z następujących licencji usługi Azure AD lub Microsoft 365:
    * [Usługa Azure AD Premium P1 lub P2][azuread-licensing]
    * [Microsoft 365 (M365) F1 lub F3][m365-firstline-workers-licensing]
    * [Mobilność w przedsiębiorstwie + zabezpieczenia (EMS) E3 lub E5][ems-licensing] lub [Microsoft 365 (M365) E3 lub E5][m365-licensing]

## <a name="limitations"></a>Ograniczenia

Podczas publicznej wersji zapoznawczej uwierzytelniania opartego na programie SMS obowiązują następujące ograniczenia:

* Uwierzytelnianie oparte na programie SMS nie jest obecnie zgodne z uwierzytelnianiem wieloskładnikowym platformy Azure.
* Z wyjątkiem aplikacji Teams uwierzytelnianie oparte na programie SMS nie jest obecnie zgodne z natywnymi aplikacjami pakietu Office.
* Uwierzytelnianie oparte na programie SMS nie jest zalecane dla kont B2B.
* Użytkownicy federowani nie będą uwierzytelniać się w dzierżawie domowej. Uwierzytelniają się tylko w chmurze.

## <a name="enable-the-sms-based-authentication-method"></a>Włączanie metody uwierzytelniania opartego na programie SMS

Istnieją trzy główne kroki, aby włączyć i używać uwierzytelniania opartego na programie SMS w organizacji:

* Włącz zasady metody uwierzytelniania.
* Wybierz użytkowników lub grupy, które mogą korzystać z metody uwierzytelniania opartego na programie SMS.
* Przypisz numer telefonu dla każdego konta użytkownika.
    * Ten numer telefonu można przypisać w witrynie Azure portal (która jest pokazana w tym artykule) oraz w *obszarze Mój personel* lub Mój *profil*.

Najpierw włączmy uwierzytelnianie oparte na programie SMS dla dzierżawy usługi Azure AD.

1. Zaloguj się do [witryny Azure portal][azure-portal] jako *administrator globalny*.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. Z menu nawigacyjnego po lewej stronie okna usługi Azure Active Directory wybierz pozycję **Metody uwierzytelniania zabezpieczeń > > zasady metody uwierzytelniania (wersja zapoznawcza).**

    [![](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png "Browse to and select the Authentication method policy (preview) window in the Azure portal")](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. Z listy dostępnych metod uwierzytelniania wybierz pozycję **Wiadomość tekstowa**.
1. Ustaw **Enable** włącz *tak*.

    ![Włączanie uwierzytelniania tekstowego w oknie zasad metody uwierzytelniania](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    Można włączyć uwierzytelnianie oparte na programie SMS dla *wszystkich użytkowników* lub *Wybierz użytkowników* i grupy. W następnej sekcji włączysz uwierzytelnianie oparte na programie SMS dla użytkownika testowego.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>Przypisywanie metody uwierzytelniania do użytkowników i grup

Po włączeniu uwierzytelniania opartego na programie SMS w dzierżawie usługi Azure AD wybierz teraz niektórych użytkowników lub grupy, które mają mieć możliwość korzystania z tej metody uwierzytelniania.

1. W oknie zasad uwierzytelniania wiadomości tekstowych ustaw **pozycję Wybierz** *użytkowników*.
1. Wybierz opcję **Dodaj użytkowników lub grupy**, a następnie wybierz użytkownika testowego lub grupę, na przykład Użytkownika *Contoso* lub *Użytkowników SMS Contoso*.

    [![](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png "Choose users or groups to enable for SMS-based authentication in the Azure portal")](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Po wybraniu użytkowników lub grup wybierz pozycję **Wybierz,** a następnie **zapisz** zaktualizowane zasady metody uwierzytelniania.

Każdy użytkownik, który jest włączony w zasadach metody uwierzytelniania wiadomości tekstowych musi mieć licencję, nawet jeśli nie używa go. Upewnij się, że masz odpowiednie licencje dla użytkowników, które są włączane w zasadach metody uwierzytelniania, szczególnie po włączeniu tej funkcji dla dużych grup użytkowników.

## <a name="set-a-phone-number-for-user-accounts"></a>Ustawianie numeru telefonu dla kont użytkowników

Użytkownicy są teraz włączeni do uwierzytelniania opartego na programie SMS, ale ich numer telefonu musi być skojarzony z profilem użytkownika w usłudze Azure AD, zanim będą mogli się zalogować. Użytkownik może [samodzielnie ustawić ten numer telefonu](../user-help/sms-sign-in-explainer.md) w obszarze Mój *profil*lub przypisać numer telefonu za pomocą witryny Azure portal. Numery telefonów mogą być ustawiane przez *administratorów globalnych,* *administratorów uwierzytelniania*lub *administratorów uwierzytelniania uprzywilejowanego.*

Gdy numer telefonu jest ustawiony dla znaku SMS, jest również dostępny do użytku z [uwierzytelnianiem wieloskładnikowym platformy Azure][tutorial-azure-mfa] i [samoobsługowym resetowaniem hasła.][tutorial-sspr]

1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. Z menu nawigacyjnego po lewej stronie okna usługi Azure Active Directory wybierz pozycję **Użytkownicy**.
1. Wybierz użytkownika włączonego do uwierzytelniania opartego na programie SMS w poprzedniej sekcji, na przykład *Użytkownik Contoso,* a następnie wybierz pozycję **Metody uwierzytelniania**.
1. Wprowadź numer telefonu użytkownika, w tym kod kraju, taki jak *+1 xxxxxxxxx*. Portal Azure sprawdza poprawność numer telefonu jest w poprawnym formacie.

    ![Ustawianie numeru telefonu dla użytkownika w witrynie Azure portal do używania z uwierzytelnianiem opartym na programie SMS](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    Numer telefonu musi być unikatowy w dzierżawie. Jeśli spróbujesz użyć tego samego numeru telefonu dla wielu użytkowników, zostanie wyświetlony komunikat o błędzie.

1. Aby zastosować numer telefonu do konta użytkownika, wybierz pozycję **Zapisz**.

Po pomyślnym zainicjowaniu obsługi administracyjnej dla *włączonego logowania SMS*pojawi się znacznik wyboru .

## <a name="test-sms-based-sign-in"></a>Testowanie logowania opartego na usłudze SMS

Aby przetestować konto użytkownika, które jest teraz włączone dla logowania opartego na programie SMS, wykonaj następujące czynności:

1. Otwórz nowe okno przeglądarki inprivate lub incognito, aby[https://www.office.com][office]
1. W prawym górnym rogu wybierz pozycję **Zaloguj**się .
1. W wierszu logowania wprowadź numer telefonu skojarzony z użytkownikiem w poprzedniej sekcji, a następnie wybierz przycisk **Dalej**.

    ![Wprowadź numer telefonu w wierszu logowania dla użytkownika testowego](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Na podany numer telefonu zostanie wysłana wiadomość TEKSTOWA. Aby zakończyć proces logowania, wprowadź 6-cyfrowy kod podany w wiadomości tekstowej w wierszu logowania.

    ![Wprowadź kod potwierdzenia wysłany sms-em na numer telefonu użytkownika](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. Użytkownik jest teraz zalogowany bez konieczności podawania nazwy użytkownika lub hasła.

## <a name="troubleshoot-sms-based-sign-in"></a>Rozwiązywanie problemów z logowaniem oparte na usłudze SMS

W przypadku problemów z włączaniem i używaniem logowania opartego na programie SMS można użyć następujących scenariuszy i kroków rozwiązywania problemów.

### <a name="phone-number-already-set-for-a-user-account"></a>Numer telefonu już ustawiony dla konta użytkownika

Jeśli użytkownik zarejestrował się już w usłudze Azure Multi-Factor Authentication i/ lub samoobsługowym resetowaniem hasła (SSPR), ma już numer telefonu skojarzony z ich kontem. Ten numer telefonu nie jest automatycznie dostępny do użycia z logiem opartym na programie SMS.

Użytkownik, który ma numer telefonu już ustawiony dla swojego konta, wyświetli przycisk *Włącz dla logowania SMS na* swojej stronie Mój **profil.** Wybierz ten przycisk, a konto jest włączone do użycia z logowania opartego na programie SMS i poprzedniej usługi Azure Multi-Factor Authentication lub rejestracji samowzłochu.

Aby uzyskać więcej informacji na temat środowiska użytkownika końcowego, zobacz [Środowisko logowania sms dla numeru telefonu (wersja zapoznawcza)](../user-help/sms-sign-in-explainer.md).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Błąd podczas próby ustawienia numeru telefonu na koncie użytkownika

Jeśli podczas próby ustawienia numeru telefonu dla konta użytkownika w witrynie Azure Portal pojawia się błąd, zapoznaj się z następującymi krokami rozwiązywania problemów:

1. Upewnij się, że włączono podgląd logowania oparty na programie SMS.
1. Upewnij się, że konto użytkownika jest włączone w zasadach metody uwierzytelniania *wiadomości tekstowych.*
1. Upewnij się, że numer telefonu został ustawiony z odpowiednim formatowaniem, zweryfikowanym w witrynie Azure portal (np. *+1 4251234567*).
1. Upewnij się, że numer telefonu nie jest używany w innym miejscu w dzierżawie.
1. Sprawdź, czy na koncie nie ustawiono numeru głosowego. Jeśli numer głosowy jest ustawiony, usuń i spróbuj ponownie użyć numeru telefonu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe sposoby logowania się do usługi Azure AD bez hasła, takie jak aplikacja Microsoft Authenticator lub klucze zabezpieczeń FIDO2, zobacz [Opcje uwierzytelniania bez użycia hasła dla usługi Azure AD][concepts-passwordless].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
