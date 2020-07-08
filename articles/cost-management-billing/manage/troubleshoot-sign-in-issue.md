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
ms.openlocfilehash: e424a3d0e46f0dc154809eba501d2339bc94b1f5
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85806707"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Rozwiązywanie problemów z logowaniem w przypadku subskrypcji platformy Azure

Ten przewodnik pomaga w rozwiązywaniu problemów, gdy nie można zalogować się do witryny Azure Portal lub Centrum konta platformy Azure.

> [!NOTE]
> Jeśli masz problemy z zarejestrowaniem się w celu utworzenia nowego konta platformy Azure, zobacz [Rozwiązywanie problemów dotyczących tworzenia konta na potrzeby subskrypcji platformy Azure](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-azure-sign-up).

## <a name="page-hangs-in-the-loading-status"></a>Strona zawiesza się podczas ładowania

Jeśli strona przeglądarki internetowej się zawiesza, spróbuj wykonać każdą z następujących czynności, aż uda się przejść do witryny Azure Portal.

- Odśwież stronę.
- Użyj innej przeglądarki internetowej.
- Użyj prywatnego trybu przeglądania w przeglądarce:

   - **Edge:** Otwórz okienko **Ustawienia** (trzy kropki przy zdjęciu profilu), wybierz pozycję **Nowe okno InPrivate**, a następnie zaloguj się do witryny [Azure Portal](https://portal.azure.com/) lub [Centrum konta platformy Azure](https://account.azure.com/Subscriptions). 
   - **Chrome:** Wybierz tryb **Incognito**.
   - **Safari:** Wybierz kolejno pozycje **Plik**, **Nowe okno prywatne**.

- Wyczyść pamięć podręczną i usuń internetowe pliki cookie:

   - **Edge:** Otwórz okienko **Ustawienia** i wybierz pozycję **Prywatność i usługi**. Postępuj zgodnie z instrukcjami w obszarze **Czyszczenie danych przeglądania**. Upewnij się, że zaznaczono pola wyboru **Historia przeglądania**, **Historia pobierania** i **Buforowane obrazy i pliki**, a następnie wybierz pozycję **Usuń**.
   - **Chrome:** Wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **Wyczyść dane przeglądania** w obszarze **Prywatność i bezpieczeństwo**.

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Następuje automatyczne zalogowanie Ciebie jako innego użytkownika

Ten problem może wystąpić, jeśli w przeglądarce internetowej używasz więcej niż jednego konta użytkownika.

Aby rozwiązać ten problem, wypróbuj jedną z poniższych metod:

- Wyczyść pamięć podręczną i usuń internetowe pliki cookie.

   - **Edge:** Otwórz okienko **Ustawienia** i wybierz pozycję **Prywatność i usługi**. Postępuj zgodnie z instrukcjami w obszarze **Czyszczenie danych przeglądania**. Upewnij się, że zaznaczono pola wyboru **Historia przeglądania**, **Historia pobierania**, **Pliki cookie** i **Buforowane obrazy i pliki**, a następnie wybierz pozycję **Usuń**.
   - **Chrome:** Wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **Wyczyść dane przeglądania** w obszarze **Prywatność i bezpieczeństwo**.
- Zresetuj ustawienia przeglądarki do wartości domyślnych.
- Użyj prywatnego trybu przeglądania w przeglądarce. 
   - **Edge:** Otwórz okienko **Ustawienia** (trzy kropki przy zdjęciu profilu), wybierz pozycję **Nowe okno InPrivate**, a następnie zaloguj się do witryny [Azure Portal](https://portal.azure.com/) lub [Centrum konta platformy Azure](https://account.azure.com/Subscriptions). 
   - **Chrome:** Wybierz tryb **Incognito**.
   - **Safari:** Wybierz kolejno pozycje **Plik**, **Nowe okno prywatne**.

## <a name="i-can-sign-in-but-i-see-the-error-no-subscriptions-found"></a>Mogę się zalogować, ale widzę komunikat o błędzie Nie odnaleziono żadnych subskrypcji

Ten problem występuje, jeśli wybrano niewłaściwy katalog lub jeśli konto nie ma wystarczających uprawnień.

**Scenariusz 1:** Podczas logowania się do witryny [Azure Portal](https://portal.azure.com/) zostaje wyświetlony komunikat o błędzie

Aby rozwiązać ten problem:

- Upewnij się, że wybrano prawidłowy katalog platformy Azure. W tym celu wybierz swoje konto w prawym górnym rogu.
- Jeśli wybrano właściwy katalog platformy Azure, ale nadal pojawia się komunikat o błędzie, poproś [o dodanie konta jako właściciel](https://docs.microsoft.com/azure/cost-management-billing/manage/add-change-subscription-administrator).

**Scenariusz 2:** Podczas logowania się do [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions) zostaje wyświetlony komunikat o błędzie

Sprawdź, czy używane konto ma uprawnienia administratora konta. Aby sprawdzić, kto jest administratorem konta, wykonaj następujące czynności:

1.  Zaloguj się do [widoku Subskrypcje w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1.  Wybierz subskrypcję, którą chcesz sprawdzić, a następnie wybierz pozycję **Ustawienia**.
1.  Wybierz pozycję **Właściwości**. Administrator konta subskrypcji jest wyświetlany w polu **Administrator konta**.

## <a name="additional-help-resources"></a>Dodatkowe zasoby pomocy

Inne artykuły dotyczące rozwiązywania problemów z rozliczeniami i subskrypcjami platformy Azure

- [Odrzucona karta](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [Subscription sign-up issues (Problemy z rejestrowaniem subskrypcji)](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-azure-sign-up)
- [No subscriptions found (Nie odnaleziono żadnych subskrypcji)](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [Wyłączony widok kosztów przedsiębiorstwa](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)
- [Dokumentacja dotycząca rozliczeń platformy Azure](https://docs.microsoft.com/azure/cost-management-billing/)

## <a name="contact-us-for-help"></a>Skontaktuj się z nami, aby uzyskać pomoc

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
