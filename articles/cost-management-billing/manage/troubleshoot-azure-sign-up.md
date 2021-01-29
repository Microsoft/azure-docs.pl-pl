---
title: Rozwiązywanie problemów podczas tworzenia nowego konta w witrynie Azure Portal lub Centrum konta platformy Azure
description: Rozwiązywanie problemu, który może wystąpić przy próbie utworzenia nowego konta w Centrum konta w witrynie Microsoft Azure Portal.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 01/28/2021
ms.author: v-miegge
ms.openlocfilehash: 015e6058236190a04df152573621f0e57030d23b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054165"
---
# <a name="troubleshoot-issues-when-you-sign-up-for-a-new-account-in-azure-portal-or-azure-account-center"></a>Rozwiązywanie problemów podczas tworzenia nowego konta w witrynie Azure Portal lub Centrum konta platformy Azure

Podczas próby utworzenia nowego konta w witrynie Microsoft Azure Portal lub w Centrum konta platformy Azure może wystąpić problem. Ten krótki przewodnik przeprowadzi Cię przez proces rejestracji i omawia kilka typowych problemów na każdym kroku.

> [!NOTE]
> Jeśli masz już konto i szukasz wskazówek dotyczących rozwiązywania problemów z logowaniem, zobacz [Rozwiązywanie problemów z logowaniem w przypadku subskrypcji platformy Azure](./troubleshoot-sign-in-issue.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem rejestracji sprawdź, czy:

- Informacje w Twoim profilu konta platformy Azure (w tym kontaktowy adres e-mail, adres i numer telefonu) są poprawne.
- Informacje o karcie kredytowej są poprawne.
- Nie masz jeszcze konta Microsoft używającego tych samych informacji.

## <a name="guided-walkthrough-of-azure-sign-up"></a>Przewodnik po rejestracji na platformie Azure

Środowisko rejestracji na platformie Azure składa się z czterech sekcji:

- Informacje o Tobie
- Weryfikacja tożsamości przy użyciu telefonu
- Weryfikacja tożsamości przy użyciu karty
- Umowa

W tym przewodniku przedstawiono przykłady poprawnych informacji na potrzeby zakładania konta na platformie Azure. W każdej sekcji przedstawiono również niektóre typowe problemy i sposoby ich rozwiązywania.

## <a name="about-you"></a>Informacje o Tobie

![Informacje o Tobie](./media/troubleshoot-azure-sign-up/1.png)
 
### <a name="common-issues-and-solutions"></a>Typowe problemy i rozwiązania

#### <a name="you-see-the-message-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Widzisz komunikat „Nie możemy kontynuować rejestracji z powodu problemu z Twoim kontem. Skontaktuj się z działem pomocy technicznej ds. rozliczeń”.

Aby rozwiązać ten problem, wykonaj te kroki:

1.  Zaloguj się do [Centrum konta platformy Azure](https://account.azure.com/Profile) przy użyciu poświadczeń administratora konta.
1.  Wybierz pozycję **Edytuj szczegóły**.
1.  Sprawdź, czy wszystkie pola adresu zostały wypełnione prawidłowymi wartościami.
1.  Po zarejestrowaniu się w celu uzyskania subskrypcji platformy Azure sprawdź, czy adres rozliczeniowy rejestracji karty kredytowej jest zgodny z danymi w banku.

Jeśli komunikat będzie nadal wyświetlany, spróbuj zarejestrować się przy użyciu innej przeglądarki.

Może uda się to zrobić w trybie InPrivate?

#### <a name="free-trial-is-not-available"></a>Bezpłatna wersja próbna jest niedostępna

Czy w przeszłości używano subskrypcji platformy Azure? Umowa dotycząca warunków użytkowania platformy Azure ogranicza aktywację bezpłatnej wersji próbnej tylko w przypadku nowych użytkowników platformy Azure. Jeśli masz dowolny inny typ subskrypcji platformy Azure, nie możesz aktywować bezpłatnej wersji próbnej. Rozważ utworzenie konta w ramach [subskrypcji z płatnością zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/).

#### <a name="you-see-the-message-you-are-not-eligible-for-an-azure-subscription"></a>Widzisz komunikat „Nie masz uprawnień do subskrypcji platformy Azure”

Aby rozwiązać ten problem, sprawdź ponownie, czy są poniższe kwestie są prawdziwe:

- Informacje podane dla Twojego profilu konta platformy Azure (w tym kontaktowy adres e-mail, adres i numer telefonu) są poprawne.
- Informacje o karcie kredytowej są poprawne.
- Nie masz jeszcze konta Microsoft używającego tych samych informacji.

#### <a name="you-see-the-message-your-current-account-type-is-not-supported"></a>Widzisz komunikat „Bieżący typ konta nie jest obsługiwany”

Ten problem może wystąpić, jeśli konto zostało zarejestrowane w [niezarządzanym katalogu usługi Azure AD](../../active-directory/enterprise-users/directory-self-service-signup.md) i nie znajduje się w katalogu usługi Azure AD organizacji.
Aby rozwiązać ten problem, utwórz konto platformy Azure przy użyciu innego konta lub przejmij niezarządzany katalog usługi AD. Aby uzyskać więcej informacji, zobacz temat [Take over an unmanaged directory as administrator in Azure Active Directory](../../active-directory/enterprise-users/domains-admin-takeover.md) (Przejmowanie niezarządzanego katalogu jako administrator w usłudze Azure Active Directory).

## <a name="identity-verification-by-phone"></a>Weryfikacja tożsamości przy użyciu telefonu

![Weryfikacja tożsamości przy użyciu telefonu](./media/troubleshoot-azure-sign-up/2.png)
 
Po odebraniu wiadomości SMS lub połączenia telefonicznego wprowadź otrzymany kod w polu tekstowym.

### <a name="common-issues-and-solutions"></a>Typowe problemy i rozwiązania

#### <a name="no-verification-text-message-or-phone-call"></a>Brak wiadomości SMS lub połączenia telefonicznego do weryfikacji

Chociaż proces weryfikacji rejestracji jest zwykle szybki, dostarczenie kodu weryfikacyjnego może potrwać do czterech minut.

Oto kilka dodatkowych porad:

- Na potrzeby weryfikacji możesz użyć dowolnego numeru telefonu, o ile spełnia on wymagania. Numer telefonu wprowadzony na potrzeby weryfikacji nie jest przechowywany jako numer kontakty dla konta.
  - W procesie weryfikacji telefonu nie można używać numeru telefonu VoiP (Voice-over-IP).
  - Sprawdź, czy telefon może odbierać połączenia lub wiadomości SMS z numeru telefonu w Stanach Zjednoczonych.
- Sprawdź ponownie wprowadzony numer telefonu, w tym kod kraju wybrany w menu rozwijanym.
- Jeśli telefon nie odbiera wiadomości tekstowych (SMS), spróbuj użyć opcji **Zadzwoń do mnie**.

## <a name="identity-verification-by-card"></a>Weryfikacja tożsamości przy użyciu karty

![Weryfikacja tożsamości przy użyciu karty](./media/troubleshoot-azure-sign-up/3.png)
 
### <a name="common-issues-and-solutions"></a>Typowe problemy i rozwiązania

#### <a name="credit-card-declined-or-not-accepted"></a>Karta kredytowa została odrzucona lub nie została zaakceptowana

Wirtualne bądź przedpłacone karty kredytowe lub debetowe nie są akceptowane jako płatność za subskrypcje platformy Azure. Aby zobaczyć, co jeszcze może powodować odrzucenie Twojej karty, zobacz [Rozwiązywanie problemów z kartą odrzuconą podczas tworzenia konta platformy Azure](./troubleshoot-declined-card.md).

#### <a name="credit-card-form-doesnt-support-my-billing-address"></a>Formularz karty kredytowej nie obsługuje adresu rozliczeniowego

Adres rozliczeniowy musi znajdować się w kraju wybranym w sekcji **Informacje o Tobie**. Sprawdź, czy masz wybrany prawidłowy kraj.

#### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>Pasek postępu zawiesza się w sekcji weryfikacji tożsamości przy użyciu karty

Aby ukończyć weryfikację tożsamości przy użyciu karty, pliki cookie innych firm muszą być dozwolone w przeglądarce.

Wykonaj następujące kroki, aby zaktualizować ustawienia plików cookie przeglądarki.

1. Zaktualizuj ustawienia plików cookie.
   - Jeśli używasz przeglądarki **Chrome**:
     - Wybierz pozycję **Ustawienia** > **Pokaż ustawienia zaawansowane** > **Prywatność** > **Ustawienia treści**. Wyczyść pole **Blokuj pliki cookie i dane z witryn innych firm**.

   - Jeśli używasz przeglądarki **Microsoft Edge**:
     - Wybierz pozycje **Ustawienia** > **Wyświetl ustawienia zaawansowane** > **Pliki cookie** > **Nie blokuj plików cookie**.

1. Odśwież stronę rejestracji na platformie Azure, a następnie sprawdź, czy problem został rozwiązany.
1. Jeśli odświeżenie nie rozwiązało problemu, zamknij i uruchom ponownie przeglądarkę, a następnie spróbuj ponownie.

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Widzę opłatę za konto w bezpłatnej wersji próbnej

Gdy utworzysz konto, możesz zauważyć tymczasową weryfikacyjną blokadę niewielkiej kwoty na koncie karty kredytowej. Zostanie ona usunięta w ciągu od trzech do pięciu dni. Jeśli martwisz się o zarządzanie kosztami, przeczytaj więcej na temat [analizy nieoczekiwanych opłat](../understand/analyze-unexpected-charges.md).

## <a name="agreement"></a>Umowa

Uzupełnij umowę.

## <a name="other-issues"></a>Inne problemy

### <a name="cant-activate-azure-benefit-plan-like-visual-studio-bizspark-bizsparkplus-or-mpn"></a>Nie można aktywować planu korzyści platformy Azure, takiego jak Visual Studio, BizSpark, BizSparkPlus lub MPN

Sprawdź, czy używasz prawidłowych poświadczeń logowania. Następnie sprawdź program korzyści i upewnij się, że masz odpowiednie uprawnienia.
- Visual Studio
  - Sprawdź swój status uprawnień na [stronie konta programu Visual Studio](https://my.visualstudio.com/Benefits).
  - Jeśli nie możesz zweryfikować swojego stanu, skontaktuj się z [pomocą techniczną Visual Studio Subscription](https://visualstudio.microsoft.com/subscriptions/support/).
- Microsoft for Startups
  - Zaloguj się do portalu [Microsoft for Startups](https://startups.microsoft.com/#start-two), aby zweryfikować stan uprawnień do uczestniczenia w programie Microsoft for Startups.
  - Jeśli nie możesz zweryfikować stanu, możesz uzyskać pomoc [na forach programu Microsoft for Startups](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN
  - Zaloguj się do portalu [MPN](https://mspartner.microsoft.com/Pages/Locale.aspx), aby zweryfikować stan uprawnień. Jeśli masz odpowiednie [kompetencje w zakresie platformy w chmurze](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx), możesz kwalifikować się do dodatkowych korzyści.
  - Jeśli nie możesz zweryfikować stanu, skontaktuj się z [działem pomocy technicznej MPN](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Nie można aktywować nowej subskrypcji platformy Azure w ramach programu licencjonowania Open

Aby utworzyć subskrypcję platformy Azure w ramach programu licencjonowania Open, musisz dysponować prawidłowym kluczem aktywacji usług online (OSA, Online Service Activation), który ma skojarzony co najmniej jeden token platformy Azure w ramach programu licencjonowania Open. Jeśli nie masz klucza OSA, skontaktuj się z jednym z partnerów firmy Microsoft wymienionym w witrynie [Microsoft Pinpoint](https://pinpoint.microsoft.com/).

## <a name="additional-help-resources"></a>Dodatkowe zasoby pomocy

Inne artykuły dotyczące rozwiązywania problemów z rozliczeniami i subskrypcjami platformy Azure

- [Odrzucona karta](./troubleshoot-declined-card.md)
- [Subscription sign-in issues (Problemy z logowaniem do subskrypcji)](./troubleshoot-sign-in-issue.md)
- [No subscriptions found (Nie odnaleziono żadnych subskrypcji)](./no-subscriptions-found.md)
- [Wyłączony widok kosztów przedsiębiorstwa](./enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Skontaktuj się z nami, aby uzyskać pomoc

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="find-out-more-about-azure-cost-management"></a>Dowiedz się więcej o usłudze Azure Cost Management

- [Dokumentacja usługi Azure Cost Management i rozliczeń](../index.yml)