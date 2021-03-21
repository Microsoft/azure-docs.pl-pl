---
title: Realizacja zaproszeń w współpracy B2B — Azure AD
description: Opisuje środowisko realizacji zaproszeń do współpracy B2B usługi Azure AD dla użytkowników końcowych, w tym umowę na warunki zachowania poufności informacji.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cd0febe5ffbc1b17718043d5fc97b804f87cc46
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199728"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Realizacja zaproszenia do współpracy B2B w usłudze Azure Active Directory

W tym artykule opisano sposób, w jaki użytkownicy-Goście mogą uzyskać dostęp do Twoich zasobów i proces wyrażania zgody. Jeśli wyślesz wiadomość e-mail z zaproszeniem do gościa, zaproszenie zawiera link, który może zostać zrealizowany przez Gościa w celu uzyskania dostępu do aplikacji lub portalu. Wiadomość e-mail z zaproszeniem jest tylko jeden z sposobów, w którym Goście mogą uzyskać dostęp do zasobów. Alternatywnie możesz dodać Gości do katalogu i udostępnić im bezpośredni link do portalu lub aplikacji, którą chcesz udostępnić. Niezależnie od używanej metody Goście są przeprowadzani przez proces wyrażania zgody po raz pierwszy. Ten proces zapewnia, że Goście zgadzają się na warunki zachowania poufności i akceptują wszelkie skonfigurowane [warunki użytkowania](../conditional-access/terms-of-use.md) .

Po dodaniu użytkownika-gościa do katalogu konto użytkownika-gościa ma stan zgody (widoczny w programie PowerShell) początkowo ustawiony na **PendingAcceptance**. To ustawienie pozostanie do momentu zaakceptowania zaproszenia przez gościa i zgody na zasady ochrony prywatności oraz warunki użytkowania. Następnie stan zgody zmieni się na **zaakceptowane**, a strony zgody nie będą już widoczne dla gościa.

   > [!IMPORTANT]
   > - **Od 4 stycznia 2021** firma Google jest [przestarzałą obsługą logowania do usługi WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). W przypadku korzystania z usługi Google Federation lub samoobsługowego rejestrowania się w usłudze Gmail należy [przetestować natywne aplikacje biznesowe pod kątem zgodności](google-federation.md#deprecation-of-webview-sign-in-support).
   > - **Od października 2021** firma Microsoft nie będzie już obsługiwać wykupu zaproszeń przez tworzenie niezarządzanych kont usługi Azure AD i dzierżawców na potrzeby scenariuszy współpracy B2B. W przygotowaniu Zachęcamy klientów do samodzielnego [uwierzytelniania kodu dostępu za pośrednictwem poczty e-mail](one-time-passcode.md). Prosimy o opinię na temat tej publicznej funkcji w wersji zapoznawczej i przyjemnością, aby utworzyć jeszcze więcej sposobów współpracy.

## <a name="redemption-and-sign-in-through-a-common-endpoint"></a>Wypełnianie i logowanie za pomocą wspólnego punktu końcowego

Użytkownicy-Goście mogą teraz zalogować się do aplikacji firmowych z wieloma dzierżawcami lub aplikacjami firmy Microsoft za pomocą wspólnego punktu końcowego (na przykład adresu URL) `https://myapps.microsoft.com` . Wcześniej wspólny adres URL przekieruje użytkownika-gościa do swojej dzierżawy domowej zamiast dzierżawy zasobów w celu uwierzytelnienia, dlatego wymagane jest łącze specyficzne dla dzierżawy (na przykład `https://myapps.microsoft.com/?tenantid=<tenant id>` ). Teraz użytkownik-Gość może przejść do wspólnego adresu URL aplikacji, wybrać **Opcje logowania**, a następnie wybrać opcję Zaloguj się **do organizacji**. Następnie użytkownik wpisze nazwę organizacji.

![Logowanie wspólnego punktu końcowego](media/redemption-experience/common-endpoint-flow-small.png)

Użytkownik zostanie następnie przekierowany do dzierżawcy punktu końcowego, gdzie może zalogować się przy użyciu adresu e-mail lub wybrać skonfigurowany dostawcę tożsamości.

## <a name="redemption-through-a-direct-link"></a>Umorzenie za pośrednictwem bezpośredniego linku

Jako alternatywę dla wiadomości e-mail z zaproszeniem lub wspólnego adresu URL aplikacji można udzielić gościa bezpośredniego linku do aplikacji lub portalu. Najpierw musisz dodać użytkownika-gościa do katalogu za pośrednictwem [Azure Portal](./b2b-quickstart-add-guest-users-portal.md) lub [PowerShell](./b2b-quickstart-invite-powershell.md). Następnie możesz użyć dowolnie [dostosowywalnych metod wdrażania aplikacji dla użytkowników](../manage-apps/end-user-experiences.md), w tym bezpośrednich linków logowania. Gdy gość korzysta ze bezpośredniego linku zamiast wiadomości e-mail z zaproszeniem, nadal będzie przeprowadzany po raz pierwszy.

> [!NOTE]
> Link bezpośredni jest specyficzny dla dzierżawy. Innymi słowy zawiera identyfikator dzierżawy lub zweryfikowaną domenę, aby można było uwierzytelnić gościa w dzierżawie, gdzie znajduje się aplikacja udostępniona. Oto kilka przykładów bezpośrednich linków z kontekstem dzierżawy:
 > - Panel dostępu do aplikacji: `https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - Panel dostępu aplikacje dla zweryfikowanej domeny: `https://myapps.microsoft.com/<;verified domain>`
 > - Witryna Azure Portal: `https://portal.azure.com/<tenant id>`
 > - Poszczególna aplikacja: Zobacz, jak używać [linku bezpośredniego logowania](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Istnieją sytuacje, w których wiadomość e-mail z zaproszeniem zaleca się za pośrednictwem linku bezpośredniego. Jeśli te specjalne przypadki są ważne dla organizacji, zalecamy Zapraszanie użytkowników przy użyciu metod, które nadal wysyłają wiadomość e-mail z zaproszeniem:
 - Użytkownik nie ma konta usługi Azure AD, elementu MSA ani konta e-mail w organizacji federacyjnej. Jeśli nie korzystasz z funkcji jednorazowego kodu dostępu, gość musi zrealizować wiadomość e-mail z zaproszeniem, aby zapoznać się z instrukcjami tworzenia konta MSA.
 - Czasami obiekt zaproszony użytkownik może nie mieć adresu e-mail z powodu konfliktu z obiektem Contact (na przykład obiektem Contact programu Outlook). W takim przypadku użytkownik musi kliknąć adres URL wykupu w wiadomości e-mail z zaproszeniem.
 - Użytkownik może zalogować się przy użyciu aliasu zaproszonego adresu e-mail. (Alias to dodatkowy adres e-mail skojarzony z kontem e-mail). W takim przypadku użytkownik musi kliknąć adres URL wykupu w wiadomości e-mail z zaproszeniem.

## <a name="redemption-through-the-invitation-email"></a>Wykup za pośrednictwem wiadomości e-mail z zaproszeniem

Po dodaniu użytkownika-gościa do katalogu przy [użyciu Azure Portal](./b2b-quickstart-add-guest-users-portal.md)do gościa w procesie jest wysyłana wiadomość e-mail z zaproszeniem. Możesz również wysyłać wiadomości e-mail z zaproszeniem, gdy [korzystasz z programu PowerShell](./b2b-quickstart-invite-powershell.md) , aby dodać użytkowników-Gości do katalogu. Poniżej znajduje się opis środowiska gościa, gdy korzystają z linku w wiadomości e-mail.

1. Gość otrzymuje [wiadomość e-mail z zaproszeniem](./invitation-email-elements.md) , która jest wysyłana z **zaproszeń firmy Microsoft**.
2. Gość wybierze opcję **Akceptuj zaproszenie** w wiadomości e-mail.
3. Gość będzie używać swoich własnych poświadczeń do logowania się do katalogu. Jeśli gość nie ma konta, które może być federacyjne dla katalogu i nie jest włączona funkcja [jednorazowego kodu dostępu (OTP) wiadomości e-mail](./one-time-passcode.md) . Gość jest monitowany o utworzenie osobistego elementu [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) lub [konta samoobsługi usługi Azure AD](../enterprise-users/directory-self-service-signup.md). Szczegóły można znaleźć w [przepływie wykupu zaproszenia](#invitation-redemption-flow) .
4. Gościa jest przeprowadzana [w opisany poniżej](#consent-experience-for-the-guest) sposób.
## <a name="invitation-redemption-flow"></a>Przepływ wykupu zaproszeń

Gdy użytkownik kliknie link **Zaakceptuj zaproszenie** w [wiadomości e-mail z zaproszeniem](invitation-email-elements.md), usługa Azure AD automatycznie zrealizuje zaproszenie na podstawie przepływu wykupu, jak pokazano poniżej:

![Zrzut ekranu przedstawiający Diagram przepływu realizacji](media/redemption-experience/invitation-redemption-flow.png)

**Jeśli główna nazwa użytkownika (UPN) jest zgodna z istniejącym kontem usługi Azure AD i osobistym współdziałaniem MSA, użytkownik zostanie poproszony o wybranie konta, za pomocą którego chcesz zrealizować.*

1. Usługa Azure AD wykonuje odnajdywanie na podstawie użytkownika w celu ustalenia, czy użytkownik istnieje w [istniejącej dzierżawie usługi Azure AD](./what-is-b2b.md#easily-invite-guest-users-from-the-azure-ad-portal).

2. Jeśli administrator włączył [bezpośrednie Federacji](./direct-federation.md), usługa Azure AD sprawdza, czy sufiks domeny użytkownika jest zgodny z domeną skonfigurowanego dostawcy tożsamości protokołu SAML/WS-karmionego i przekierowuje użytkownika do wstępnie skonfigurowanego dostawcy tożsamości.

3. Jeśli administrator włączył usługę [Google Federation](./google-federation.md), usługa Azure AD sprawdza, czy sufiks domeny użytkownika jest gmail.com lub googlemail.com i przekierowuje użytkownika do usługi Google.

4. Proces wykupu sprawdza, czy użytkownik ma istniejące konto Microsoft osobiste [(MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) dla wykupu just-in-Time (JIT), ale nie na potrzeby tworzenia linków do wiadomości e-mail z zaproszeniem. Jeśli użytkownik ma już istniejący element MSA, zaloguje się przy użyciu istniejącego konta MSA.

5. Po zidentyfikowaniu **katalogu macierzystego** użytkownika użytkownik jest wysyłany do odpowiedniego dostawcy tożsamości w celu zalogowania się.  

6. Jeśli kroki od 1 do 4 nie powiodło się znalezienie katalogu macierzystego dla zaproszonego użytkownika, usługa Azure AD określi, czy zapraszana dzierżawa włączyła funkcję [dostępu jednorazowego (OTP) wiadomości e-mail](./one-time-passcode.md) dla Gości.

7. Jeśli [dla Gości zostanie włączona jednorazowy kod dostępu do wiadomości e-mail](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode), kod dostępu zostanie wysłany do użytkownika za pośrednictwem zaproszonej wiadomości e-mail. Użytkownik będzie pobierać i wprowadzać ten kod dostępu na stronie logowania usługi Azure AD.

8. Jeżeli jednorazowy kod dostępu wiadomości e-mail dla Gości jest wyłączony, usługa Azure AD sprawdza sufiks domeny w celu ustalenia, czy należy on do konta odbiorcy. Jeśli tak, użytkownik jest monitowany o utworzenie osobistego [konto Microsoft](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create). Jeśli nie, użytkownik jest monitowany o utworzenie [konta samoobsługi usługi Azure AD](../enterprise-users/directory-self-service-signup.md).

9. Usługa Azure AD próbuje utworzyć [konto samoobsługi usługi Azure AD](../enterprise-users/directory-self-service-signup.md) , sprawdzając dostęp do wiadomości e-mail. Sprawdzanie, czy konto jest wykonywane przez wysłanie kodu do wiadomości e-mail i pobranie i przesłanie go do usługi Azure AD. Jeśli jednak dzierżawca zaproszonego użytkownika jest federacyjny lub jeśli pole AllowEmailVerifiedUsers jest ustawione na wartość false w dzierżawie zaproszonego użytkownika, użytkownik nie może zakończyć realizacji, a przepływ spowoduje wystąpienie błędu. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów Azure Active Directory współpracy B2B](./troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption).

10. Użytkownik jest monitowany o utworzenie osobistego [konto Microsoft (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create).

11. Po uwierzytelnieniu w prawidłowym dostawcy tożsamości użytkownik zostanie przekierowany do usługi Azure AD w celu ukończenia [tego działania](#consent-experience-for-the-guest).  

W przypadku wykupu just-in-Time (JIT), gdy wykup odbywa się za pomocą linku aplikacji dzierżawców, kroki od 8 do 10 nie są dostępne. Jeśli użytkownik przejdzie do kroku 6, a funkcja jednorazowego kodu dostępu wiadomości E-mail nie jest włączona, użytkownik otrzyma komunikat o błędzie i nie będzie mógł zrealizować zaproszenia. Aby uniknąć tego błędu, Administratorzy powinni [włączyć jednorazowy kod dostępu wiadomości e-mail](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) lub upewnić się, że użytkownik kliknie link zaproszenia.

## <a name="consent-experience-for-the-guest"></a>Środowisko zgody gościa

Gdy gość loguje się w celu uzyskania dostępu do zasobów w organizacji partnerskiej po raz pierwszy, są one przekierowane przez następujące strony. 

1. Gość przegląda stronę **przeglądanie uprawnień** z opisem zasad zachowania poufności informacji w organizacji. Użytkownik musi **zaakceptować** użycie informacji zgodnie z zasadami zachowania poufności w organizacji zapraszania, aby kontynuować.

   ![Zrzut ekranu przedstawiający stronę przegląd uprawnień](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Aby uzyskać informacje o tym, jak Administrator dzierżawy może połączyć się z zasadami zachowania poufności informacji w organizacji, zobacz [How to: Dodawanie informacji o ochronie prywatności organizacji w Azure Active Directory](../fundamentals/active-directory-properties-area.md).

2. W przypadku skonfigurowania warunków użytkowania gość otwiera i przegląda warunki użytkowania, a następnie wybiera pozycję **Zaakceptuj**. 

   ![Zrzut ekranu przedstawiający nowe warunki użytkowania](media/redemption-experience/terms-of-use-accept.png) 

   [Warunki użytkowania](../conditional-access/terms-of-use.md) można skonfigurować w warunki użytkowania **tożsamości zewnętrznych**  >  .

3. O ile nie określono inaczej, Gość zostanie przekierowany do panelu dostępu do aplikacji, który zawiera listę aplikacji, do których gość może uzyskać dostęp.

   ![Zrzut ekranu przedstawiający panel dostępu aplikacje](media/redemption-experience/myapps.png) 

W katalogu wartość **zaakceptowana przez zaproszenie** gościa zmieni się na **tak**. Jeśli element MSA został utworzony, **Źródło** gościa pokazuje **konto Microsoft**. Aby uzyskać więcej informacji na temat właściwości konta użytkownika-gościa, zobacz [właściwości użytkownika współpracy B2B usługi Azure AD](user-properties.md). 

## <a name="next-steps"></a>Następne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Dodawanie Azure Active Directory użytkowników współpracy B2B w Azure Portal](add-users-administrator.md)
- [Jak Pracownicy przetwarzający informacje mogą dodawać do Azure Active Directory użytkowników współpracy B2B?](add-users-information-worker.md)
- [Dodawanie Azure Active Directory użytkowników współpracy B2B przy użyciu programu PowerShell](customize-invitation-api.md#powershell)
- [Opuszczanie organizacji jako użytkownik-gość](leave-the-organization.md)
