---
title: Rozwiązywanie problemów podczas tworzenia nowego konta w witrynie Azure Portal lub Centrum konta platformy Azure
description: Rozwiązywanie problemu, który może wystąpić przy próbie utworzenia nowego konta w Centrum konta w witrynie Microsoft Azure Portal.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: v-miegge
ms.openlocfilehash: 7bcf3197ae218c469983200814cecf551dc42a6b
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133181"
---
# <a name="troubleshoot-issues-when-you-sign-up-for-a-new-account-in-azure-portal-or-azure-account-center"></a>Rozwiązywanie problemów podczas tworzenia nowego konta w witrynie Azure Portal lub Centrum konta platformy Azure

Podczas próby utworzenia nowego konta w witrynie Microsoft Azure Portal lub w Centrum konta platformy Azure może wystąpić problem. Przed rozwiązaniem tego problemu należy najpierw sprawdzić następujące kwestie:

- Informacje podane dla Twojego profilu konta platformy Azure (w tym kontaktowy adres e-mail, adres i numer telefonu) są poprawne.
- Informacje o karcie kredytowej są poprawne.
- Nie masz jeszcze konta Microsoft używającego tych samych informacji.

> [!NOTE]
> Jeśli masz już konto i szukasz wskazówek dotyczących rozwiązywania problemów z logowaniem, zobacz [Rozwiązywanie problemów z logowaniem w przypadku subskrypcji platformy Azure](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue).

## <a name="resolutions"></a>Rozwiązania

Aby usunąć wszelkie błędy, wybierz problem, który występuje podczas próby utworzenia konta na platformie Azure.

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Błąd: *Nie można kontynuować tworzenia konta z powodu problemu z Twoim kontem. Skontaktuj się z działem pomocy technicznej dotyczącej rozliczeń.*

Aby rozwiązać problem, wykonaj następujące kroki:

1. Zaloguj się do [Centrum konta platformy Azure](https://account.azure.com/Profile) przy użyciu poświadczeń administratora konta.

2. Wybierz pozycję **Edytuj szczegóły**.

3. Upewnij się, że wszystkie pola adresu zostały wypełnione przy użyciu prawidłowych wartości.

4. Po utworzeniu konta subskrypcji platformy Azure upewnij się, że adres rozliczeniowy rejestracji karty kredytowej jest zgodny z rekordami banku.

Jeśli komunikat o błędzie będzie nadal wyświetlany, spróbuj utworzyć konto przy użyciu innej przeglądarki.

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>Pasek postępu zawiesza się w sekcji *Weryfikacja tożsamości przy użyciu karty*.

Aby ukończyć weryfikację tożsamości przy użyciu karty, pliki cookie innych firm muszą być dozwolone w przeglądarce.

![Weryfikacja tożsamości przy użyciu karty](./media/troubleshoot-azure-sign-up/identify-verification-by-card.png)

Wykonaj następujące kroki, aby zaktualizować ustawienia plików cookie przeglądarki.

1. Jeśli używasz programu Chrome, wybierz pozycję **Ustawienia** > **Pokaż ustawienia zaawansowane** > **Prywatność** > **Ustawienia treści**. Wyczyść pole **Blokuj pliki cookie i dane z witryn innych firm**.

2. Jeśli używasz przeglądarki Microsoft Edge, wybierz pozycję **Ustawienia** > **Wyświetl ustawienia zaawansowane** > **Pliki cookie** > **Nie blokuj plików cookie**.

3. Odśwież stronę tworzenia konta platformy Azure, a następnie sprawdź, czy problem został rozwiązany.

4. Jeśli odświeżenie nie rozwiązało problemu, zamknij i uruchom ponownie przeglądarkę, a następnie spróbuj ponownie.

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>Formularz karty kredytowej nie obsługuje adresu rozliczeniowego

Adres rozliczeniowy musi znajdować się w kraju wybranym w sekcji **Informacje o użytkowniku**. Upewnij się, że wybrano prawidłowy kraj.

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Brak komunikatów tekstowych lub połączeń podczas weryfikacji konta rejestracji

Chociaż proces jest zwykle szybki, dostarczenie kodu weryfikacyjnego może potrwać do czterech minut. Numer telefonu wprowadzony na potrzeby weryfikacji nie jest przechowywany jako numer kontakty dla konta.

Oto kilka dodatkowych porad:

- W procesie weryfikacji telefonu nie można używać numeru telefonu VoiP (Voice-over-IP).
- Sprawdź ponownie wprowadzony numer telefonu, w tym kod kraju wybrany w menu rozwijanym.
- Jeśli telefon nie odbiera wiadomości tekstowych (SMS), spróbuj użyć opcji **Zadzwoń do mnie**.
- Upewnij się, że telefon może odbierać połączenia lub wiadomości SMS z numeru telefonu w Stanach Zjednoczonych.

Po odebraniu wiadomości SMS lub połączenia telefonicznego wprowadź otrzymany kod w polu tekstowym.

### <a name="credit-card-declined-or-not-accepted"></a>Karta kredytowa została odrzucona lub nie została zaakceptowana

Wirtualne bądź przedpłacone karty kredytowe lub debetowe nie są akceptowane jako płatność za subskrypcje platformy Azure. Aby sprawdzić, co jeszcze może spowodować odrzucenie karty, zobacz temat dotyczący sytuacji, w której karta debetowa lub kredytowa została [odrzucona podczas tworzenia konta platformy Azure](https://support.microsoft.com/help/4042960).

### <a name="free-trial-is-not-available"></a>Bezpłatna wersja próbna jest niedostępna

Czy w przeszłości używano subskrypcji platformy Azure? Umowa dotycząca warunków użytkowania platformy Azure ogranicza aktywację bezpłatnej wersji próbnej tylko w przypadku nowych użytkowników platformy Azure. Jeśli masz dowolny inny typ subskrypcji platformy Azure, nie możesz aktywować bezpłatnej wersji próbnej. Rozważ utworzenie konta w ramach [subskrypcji z płatnością zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/).

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Widzę opłatę za konto bezpłatnej wersji próbnej

Gdy utworzysz konto, możesz zauważyć transakcję weryfikacyjną, która blokuje niewielką kwotę na koncie karty kredytowej. Zostanie ono usunięte w ciągu trzech do pięciu dni. Jeśli martwisz się o zarządzanie kosztami, przeczytaj więcej na temat [zapobiegania nieoczekiwanym kosztom](getting-started.md).

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Nie można aktywować planu korzyści platformy Azure, takiego jak MSDN, BizSpark, BizSparkPlus lub MPN

Upewnij się, że używasz prawidłowych poświadczeń logowania. Następnie sprawdź program korzyści, aby upewnić się, że masz odpowiednie uprawnienia.

- MSDN
  - Sprawdź stan uprawnień na [stronie konta MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  - Jeśli nie możesz zweryfikować swojego stanu, skontaktuj się z [centrum obsługi klienta subskrypcji MSDN](https://msdn.microsoft.com/library/aa493452.aspx).
- Microsoft for Startups
  - Zaloguj się do portalu [Microsoft for Startups](https://startups.microsoft.com/#start-two), aby zweryfikować stan uprawnień do uczestniczenia w programie Microsoft for Startups.
  - Jeśli nie możesz zweryfikować stanu, możesz uzyskać pomoc [na forach programu Microsoft for Startups](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN
  - Zaloguj się do portalu [MPN](https://mspartner.microsoft.com/Pages/Locale.aspx), aby zweryfikować stan uprawnień. Jeśli masz odpowiednie [kompetencje w zakresie platformy w chmurze](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx), możesz kwalifikować się do dodatkowych korzyści.
  - Jeśli nie możesz zweryfikować stanu, skontaktuj się z [działem pomocy technicznej MPN](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Nie można aktywować nowej subskrypcji platformy Azure w ramach programu licencjonowania Open

Aby utworzyć subskrypcję platformy Azure w ramach programu licencjonowania Open, musisz dysponować prawidłowym kluczem aktywacji usług online (OSA, Online Service Activation), który ma skojarzony co najmniej jeden token platformy Azure w ramach programu licencjonowania Open. Jeśli nie masz klucza OSA, skontaktuj się z jednym z partnerów firmy Microsoft wymienionym w witrynie [Microsoft Pinpoint](https://pinpoint.microsoft.com/).

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>Błąd: *Nie masz uprawnień do subskrypcji platformy Azure*

Aby rozwiązać ten problem, sprawdź ponownie, czy są poniższe kwestie są prawdziwe:

- Informacje podane dla Twojego profilu konta platformy Azure (w tym kontaktowy adres e-mail, adres i numer telefonu) są poprawne.
- Informacje o karcie kredytowej są poprawne.
- Nie masz jeszcze konta Microsoft używającego tych samych informacji.

### <a name="error-your-current-account-type-is-not-supported"></a>Błąd: *Bieżący typ konta nie jest obsługiwany*

Ten problem może wystąpić, jeśli konto zostało zarejestrowane w [niezarządzanym katalogu usługi Azure AD](../../active-directory/users-groups-roles/directory-self-service-signup.md) i nie znajduje się w katalogu usługi Azure AD organizacji.

Aby rozwiązać ten problem, utwórz konto platformy Azure przy użyciu innego konta lub przejmij niezarządzany katalog usługi AD. Aby uzyskać więcej informacji, zobacz temat [Take over an unmanaged directory as administrator in Azure Active Directory](../../active-directory/users-groups-roles/domains-admin-takeover.md) (Przejmowanie niezarządzanego katalogu jako administrator w usłudze Azure Active Directory).

## <a name="additional-help-resources"></a>Dodatkowe zasoby pomocy

Inne artykuły dotyczące rozwiązywania problemów z rozliczeniami i subskrypcjami platformy Azure

- [Odrzucona karta](troubleshoot-declined-card.md)
- [Subscription sign-in issues (Problemy z logowaniem do subskrypcji)](troubleshoot-sign-in-issue.md)
- [No subscriptions found (Nie odnaleziono żadnych subskrypcji)](no-subscriptions-found.md)
- [Wyłączony widok kosztów przedsiębiorstwa](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Skontaktuj się z nami, aby uzyskać pomoc

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja dotycząca rozliczeń platformy Azure](../../billing/index.md)
