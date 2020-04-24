---
title: Rozwiązywanie problemów z logowaniem w przypadku subskrypcji platformy Azure
description: Pomaga w rozwiązywaniu problemów, gdy nie można zalogować się do witryny Azure Portal lub centrum konta platformy Azure.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: v-miegge
ms.openlocfilehash: 08f278d56e742659ebe197695fae8dfa35383b17
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639202"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Rozwiązywanie problemów z logowaniem w przypadku subskrypcji platformy Azure

Ten przewodnik pomaga w rozwiązywaniu problemów, gdy nie można zalogować się do witryny Azure Portal lub Centrum konta platformy Azure.

## <a name="issues"></a>Problemy

### <a name="page-hangs-in-the-loading-status"></a>Strona zawiesza się w stanie ładowania

Jeśli strona przeglądarki internetowej się zawiesza, spróbuj wykonać każdą z następujących czynności, aż uda się przejść do witryny Azure Portal.

- Odśwież stronę.
- Użyj innej przeglądarki internetowej.
- Użyj prywatnego trybu przeglądania w przeglądarce. W programie Internet Explorer: Kliknij kolejno pozycje **Narzędzia** > **Bezpieczeństwo** > **Przeglądanie InPrivate**, a następnie przejdź do witryny [Azure Portal](https://portal.azure.com/) lub [Centrum konta platformy Azure](https://account.azure.com/Subscriptions) i zaloguj się.

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Użytkownik jest automatycznie logowany jako inny użytkownik

Ten problem może wystąpić, jeśli w przeglądarce internetowej używasz więcej niż jednego konta użytkownika.

Aby rozwiązać ten problem, wypróbuj jedną z poniższych metod:

- Wyczyść pamięć podręczną i usuń internetowe pliki cookie. W programie Internet Explorer kliknij kolejno pozycje **Narzędzia** > **Opcje internetowe** > **Usuń**. Upewnij się, że zaznaczone są pola wyboru plików tymczasowych, plików cookie, hasła i historii przeglądania, a następnie kliknij przycisk Usuń.
- Zresetuj ustawienia programu Internet Explorer, aby przywrócić wszystkie ustawienia osobiste, które zostały wprowadzone. Kliknij kolejno pozycje **Narzędzia** > **Opcje internetowe** > **Zaawansowane** > zaznacz pole **Usuń ustawienia osobiste** > **Resetuj**.
- Użyj prywatnego trybu przeglądania w przeglądarce. W programie Internet Explorer:  Kliknij kolejno pozycje **Narzędzia** > **Bezpieczeństwo** > **Przeglądanie InPrivate**, a następnie przejdź do witryny [Azure Portal](https://portal.azure.com/) lub [Centrum konta platformy Azure](https://account.azure.com/Subscriptions) i zaloguj się.

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Mogę się zalogować, ale widzę komunikat *Nie odnaleziono żadnych subskrypcji*

Ten problem występuje, jeśli wybrano niewłaściwy katalog lub jeśli konto nie ma wystarczających uprawnień.

**Scenariusz 1:** Komunikat o błędzie zostaje wyświetlony w witrynie [Azure Portal](https://portal.azure.com/)

Aby rozwiązać ten problem:

- Upewnij się, że wybrano prawidłowy katalog platformy Azure, klikając swoje konto w prawym górnym rogu.
- Jeśli wybrano właściwy katalog platformy Azure, ale nadal pojawia się komunikat o błędzie, poproś [o dodanie konta jako właściciel](add-change-subscription-administrator.md).

**Scenariusz 2:** Komunikat o błędzie zostaje wyświetlony w [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions)

Sprawdź, czy użyte konto jest administratorem konta. Aby sprawdzić, kto jest administratorem konta, wykonaj następujące czynności:

1. Zaloguj się do [widoku Subskrypcje w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Wybierz subskrypcję, którą chcesz sprawdzić, a następnie przejrzyj **Ustawienia**.

3. Wybierz pozycję **Właściwości**. Administrator konta subskrypcji jest wyświetlany w polu **Administrator konta**.

## <a name="additional-help-resources"></a>Dodatkowe zasoby pomocy

Inne artykuły dotyczące rozwiązywania problemów z rozliczeniami i subskrypcjami platformy Azure

- [Odrzucona karta](troubleshoot-declined-card.md)
- [Subscription sign-up issues (Problemy z rejestrowaniem subskrypcji)](troubleshoot-azure-sign-up.md)
- [No subscriptions found (Nie odnaleziono żadnych subskrypcji)](no-subscriptions-found.md)
- [Wyłączony widok kosztów przedsiębiorstwa](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Skontaktuj się z nami, aby uzyskać pomoc

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja dotycząca rozliczeń platformy Azure](../../billing/index.md)
