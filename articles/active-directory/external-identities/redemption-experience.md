---
title: Realizacja zaproszeń w współpracy B2B — Azure AD
description: Opisuje środowisko realizacji zaproszeń do współpracy B2B usługi Azure AD dla użytkowników końcowych, w tym umowę na warunki zachowania poufności informacji.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6bf5d40262c5991504d3dc62490fb50f6a20592
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87909151"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory realizacji zaproszeń do współpracy B2B

W tym artykule opisano sposób, w jaki użytkownicy-Goście mogą uzyskać dostęp do Twoich zasobów i proces wyrażania zgody. Jeśli wyślesz wiadomość e-mail z zaproszeniem do gościa, zaproszenie zawiera link, który może zostać zrealizowany przez Gościa w celu uzyskania dostępu do aplikacji lub portalu. Wiadomość e-mail z zaproszeniem jest tylko jeden z sposobów, w którym Goście mogą uzyskać dostęp do zasobów. Alternatywnie możesz dodać Gości do katalogu i udostępnić im bezpośredni link do portalu lub aplikacji, którą chcesz udostępnić. Niezależnie od używanej metody Goście są przeprowadzani przez proces wyrażania zgody po raz pierwszy. Ten proces zapewnia, że Goście zgadzają się na warunki zachowania poufności i akceptują wszelkie skonfigurowane [warunki użytkowania](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) .

Po dodaniu użytkownika-gościa do katalogu konto użytkownika-gościa ma stan zgody (widoczny w programie PowerShell) początkowo ustawiony na **PendingAcceptance**. To ustawienie pozostanie do momentu zaakceptowania zaproszenia przez gościa i zgody na zasady ochrony prywatności oraz warunki użytkowania. Następnie stan zgody zmieni się na **zaakceptowane**, a strony zgody nie będą już widoczne dla gościa.

   > [!IMPORTANT]
   > **Od 31 marca 2021**firma Microsoft nie będzie już obsługiwać wykupu zaproszeń przez tworzenie niezarządzanych kont usługi Azure AD i dzierżawców na potrzeby scenariuszy współpracy B2B. W przygotowaniu Zachęcamy klientów do samodzielnego [uwierzytelniania kodu dostępu za pośrednictwem poczty e-mail](one-time-passcode.md). Prosimy o opinię na temat tej publicznej funkcji w wersji zapoznawczej i przyjemnością, aby utworzyć jeszcze więcej sposobów współpracy.

## <a name="redemption-through-the-invitation-email"></a>Wykup za pośrednictwem wiadomości e-mail z zaproszeniem

Po dodaniu użytkownika-gościa do katalogu przy [użyciu Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)do gościa w procesie jest wysyłana wiadomość e-mail z zaproszeniem. Możesz również wysyłać wiadomości e-mail z zaproszeniem, gdy [korzystasz z programu PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) , aby dodać użytkowników-Gości do katalogu. Poniżej znajduje się opis środowiska gościa, gdy korzystają z linku w wiadomości e-mail.

1. Gość otrzymuje [wiadomość e-mail z zaproszeniem](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) , która jest wysyłana z **zaproszeń firmy Microsoft**.
2. Gość wybierze opcję **Akceptuj zaproszenie** w wiadomości e-mail.
3. Gość będzie używać swoich własnych poświadczeń do logowania się do katalogu. Jeśli gość nie ma konta, które może być federacyjne dla katalogu i nie jest włączona funkcja [jednorazowego kodu dostępu (OTP) wiadomości e-mail](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) . Gość jest monitowany o utworzenie osobistego elementu [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) lub [konta samoobsługi usługi Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup). Szczegóły można znaleźć w [przepływie wykupu zaproszenia](#invitation-redemption-flow) .
4. Gościa jest przeprowadzana [w opisany poniżej](#consent-experience-for-the-guest) sposób.

## <a name="redemption-through-a-direct-link"></a>Umorzenie za pośrednictwem bezpośredniego linku

Alternatywą dla wiadomości e-mail z zaproszeniem jest nadanie gościa bezpośredniego linku do aplikacji lub portalu. Najpierw musisz dodać użytkownika-gościa do katalogu za pośrednictwem [Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) lub [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Następnie możesz użyć dowolnie [dostosowywalnych metod wdrażania aplikacji dla użytkowników](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), w tym bezpośrednich linków logowania. Gdy gość korzysta ze bezpośredniego linku zamiast wiadomości e-mail z zaproszeniem, nadal będzie przeprowadzany po raz pierwszy.

> [!IMPORTANT]
> Link bezpośredni musi być specyficzny dla dzierżawy. Innymi słowy, musi zawierać identyfikator dzierżawy lub zweryfikowaną domenę, aby można było uwierzytelnić gościa w dzierżawie, gdzie znajduje się aplikacja udostępniona. Wspólny adres URL, taki jak https://myapps.microsoft.com nie będzie działał dla gościa, ponieważ zostanie przekierowany do dzierżawy domowej w celu uwierzytelnienia. Oto kilka przykładów bezpośrednich linków z kontekstem dzierżawy:
 > - Panel dostępu do aplikacji: `https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - Panel dostępu aplikacje dla zweryfikowanej domeny: `https://myapps.microsoft.com/<;verified domain>`
 > - Witryna Azure Portal: `https://portal.azure.com/<tenant id>`
 > - Poszczególna aplikacja: Zobacz, jak używać [linku bezpośredniego logowania](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Istnieją sytuacje, w których wiadomość e-mail z zaproszeniem zaleca się za pośrednictwem linku bezpośredniego. Jeśli te specjalne przypadki są ważne dla organizacji, zalecamy Zapraszanie użytkowników przy użyciu metod, które nadal wysyłają wiadomość e-mail z zaproszeniem:
 - Użytkownik nie ma konta usługi Azure AD, elementu MSA ani konta e-mail w organizacji federacyjnej. Jeśli nie korzystasz z funkcji jednorazowego kodu dostępu, gość musi zrealizować wiadomość e-mail z zaproszeniem, aby zapoznać się z instrukcjami tworzenia konta MSA.
 - Czasami obiekt zaproszony użytkownik może nie mieć adresu e-mail z powodu konfliktu z obiektem Contact (na przykład obiektem Contact programu Outlook). W takim przypadku użytkownik musi kliknąć adres URL wykupu w wiadomości e-mail z zaproszeniem.
 - Użytkownik może zalogować się przy użyciu aliasu zaproszonego adresu e-mail. (Alias to dodatkowy adres e-mail skojarzony z kontem e-mail). W takim przypadku użytkownik musi kliknąć adres URL wykupu w wiadomości e-mail z zaproszeniem.

## <a name="invitation-redemption-flow"></a>Przepływ wykupu zaproszeń

Gdy użytkownik kliknie link **Zaakceptuj zaproszenie** w [wiadomości e-mail z zaproszeniem](invitation-email-elements.md), usługa Azure AD automatycznie zrealizuje zaproszenie na podstawie przepływu wykupu, jak pokazano poniżej:

![Zrzut ekranu przedstawiający Diagram przepływu realizacji](media/redemption-experience/invitation-redemption-flow.png)

**Jeśli główna nazwa użytkownika (UPN) jest zgodna z istniejącym kontem usługi Azure AD i osobistym współdziałaniem MSA, użytkownik zostanie poproszony o wybranie konta, za pomocą którego chcesz zrealizować.*

1. Usługa Azure AD wykonuje odnajdywanie na podstawie użytkownika w celu ustalenia, czy użytkownik istnieje w [istniejącej dzierżawie usługi Azure AD](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b#easily-add-guest-users-in-the-azure-ad-portal).

2. Jeśli administrator włączył [bezpośrednie Federacji](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation), usługa Azure AD sprawdza, czy sufiks domeny użytkownika jest zgodny z domeną skonfigurowanego dostawcy tożsamości protokołu SAML/WS-karmionego i przekierowuje użytkownika do wstępnie skonfigurowanego dostawcy tożsamości.

3. Jeśli administrator włączył usługę [Google Federation](https://docs.microsoft.com/azure/active-directory/b2b/google-federation), usługa Azure AD sprawdza, czy sufiks domeny użytkownika jest gmail.com lub googlemail.com i przekierowuje użytkownika do usługi Google.

4. Proces wykupu sprawdza, czy użytkownik ma istniejące konto Microsoft osobiste [(MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create).

5. Po zidentyfikowaniu **katalogu macierzystego** użytkownika użytkownik jest wysyłany do odpowiedniego dostawcy tożsamości w celu zalogowania się.  

6. Jeśli kroki od 1 do 4 nie powiodło się znalezienie katalogu macierzystego dla zaproszonego użytkownika, usługa Azure AD określi, czy zapraszana dzierżawa włączyła funkcję [dostępu jednorazowego (OTP) wiadomości e-mail](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) dla Gości.

7. Jeśli [dla Gości zostanie włączona jednorazowy kod dostępu do wiadomości e-mail](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode), kod dostępu zostanie wysłany do użytkownika za pośrednictwem zaproszonej wiadomości e-mail. Użytkownik będzie pobierać i wprowadzać ten kod dostępu na stronie logowania usługi Azure AD.

8. Jeżeli jednorazowy kod dostępu wiadomości e-mail dla Gości jest wyłączony, usługa Azure AD sprawdza sufiks domeny w celu ustalenia, czy należy on do konta odbiorcy. Jeśli tak, użytkownik jest monitowany o utworzenie osobistego [konto Microsoft](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create). Jeśli nie, użytkownik jest monitowany o utworzenie [konta samoobsługi usługi Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup).

9. Usługa Azure AD próbuje utworzyć [konto samoobsługi usługi Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup) , sprawdzając dostęp do wiadomości e-mail. Sprawdzanie, czy konto jest wykonywane przez wysłanie kodu do wiadomości e-mail i pobranie i przesłanie go do usługi Azure AD. Jeśli jednak dzierżawca zaproszonego użytkownika jest federacyjny lub jeśli pole AllowEmailVerifiedUsers jest ustawione na wartość false w dzierżawie zaproszonego użytkownika, użytkownik nie może zakończyć realizacji, a przepływ spowoduje wystąpienie błędu. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów Azure Active Directory współpracy B2B](https://docs.microsoft.com/azure/active-directory/b2b/troubleshoot#the-user-that-i-invited-is-receiving-an-error-during-redemption).

10. Użytkownik jest monitowany o utworzenie osobistego [konto Microsoft (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create).

11. Po uwierzytelnieniu w prawidłowym dostawcy tożsamości użytkownik zostanie przekierowany do usługi Azure AD w celu ukończenia [tego działania](https://docs.microsoft.com/azure/active-directory/b2b/redemption-experience#consent-experience-for-the-guest).  

W przypadku wykupu just-in-Time (JIT), gdy wykup odbywa się za pomocą linku aplikacji dzierżawców, kroki od 8 do 10 nie są dostępne. Jeśli użytkownik przejdzie do kroku 6, a funkcja jednorazowego kodu dostępu wiadomości E-mail nie jest włączona, użytkownik otrzyma komunikat o błędzie i nie będzie mógł zrealizować zaproszenia. Aby uniknąć tego błędu, Administratorzy powinni [włączyć jednorazowy kod dostępu wiadomości e-mail](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode) lub upewnić się, że użytkownik kliknie link zaproszenia.

## <a name="consent-experience-for-the-guest"></a>Środowisko zgody gościa

Gdy gość loguje się w celu uzyskania dostępu do zasobów w organizacji partnerskiej po raz pierwszy, są one przekierowane przez następujące strony. 

1. Gość przegląda stronę **przeglądanie uprawnień** z opisem zasad zachowania poufności informacji w organizacji. Użytkownik musi **zaakceptować** użycie informacji zgodnie z zasadami zachowania poufności w organizacji zapraszania, aby kontynuować.

   ![Zrzut ekranu przedstawiający stronę przegląd uprawnień](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Aby uzyskać informacje o tym, jak Administrator dzierżawy może połączyć się z zasadami zachowania poufności informacji w organizacji, zobacz [How to: Dodawanie informacji o ochronie prywatności organizacji w Azure Active Directory](https://aka.ms/adprivacystatement).

2. W przypadku skonfigurowania warunków użytkowania gość otwiera i przegląda warunki użytkowania, a następnie wybiera pozycję **Zaakceptuj**. 

   ![Zrzut ekranu przedstawiający nowe warunki użytkowania](media/redemption-experience/terms-of-use-accept.png) 

   [Warunki użytkowania](../governance/active-directory-tou.md) można skonfigurować w warunki użytkowania **tożsamości zewnętrznych**  >  **Terms of use**.

3. O ile nie określono inaczej, Gość zostanie przekierowany do panelu dostępu do aplikacji, który zawiera listę aplikacji, do których gość może uzyskać dostęp.

   ![Zrzut ekranu przedstawiający panel dostępu aplikacje](media/redemption-experience/myapps.png) 

W katalogu wartość **zaakceptowana przez zaproszenie** gościa zmieni się na **tak**. Jeśli element MSA został utworzony, **Źródło** gościa pokazuje **konto Microsoft**. Aby uzyskać więcej informacji na temat właściwości konta użytkownika-gościa, zobacz [właściwości użytkownika współpracy B2B usługi Azure AD](user-properties.md). 

## <a name="next-steps"></a>Następne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Dodawanie Azure Active Directory użytkowników współpracy B2B w Azure Portal](add-users-administrator.md)
- [Jak Pracownicy przetwarzający informacje mogą dodawać do Azure Active Directory użytkowników współpracy B2B?](add-users-information-worker.md)
- [Dodawanie Azure Active Directory użytkowników współpracy B2B przy użyciu programu PowerShell](customize-invitation-api.md#powershell)
- [Opuszczanie organizacji jako użytkownik-gość](leave-the-organization.md)
