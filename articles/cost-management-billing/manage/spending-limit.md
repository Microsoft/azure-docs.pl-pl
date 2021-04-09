---
title: Azure — limit wydatków
description: W tym artykule pokazano, jak działa limit wydatków platformy Azure oraz jak go usunąć.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/29/2021
ms.author: banders
ms.openlocfilehash: 47f1a1ed182420852682e908c7cd8b5e094bc085
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932560"
---
# <a name="azure-spending-limit"></a>Azure — limit wydatków

Dzięki limitowi wydatków na platformie Azure nie można wydawać więcej niż wynosi kwota środków. Wszyscy nowi klienci, którzy rejestrują się w celu założenia bezpłatnego konta platformy Azure lub skorzystania z typów subskrypcji obejmujących środki na wiele miesięcy, mają domyślnie włączony limit wydatków. Limit wydatków jest równy kwocie kredytu. Nie można zmienić wartości limitu wydatków. Jeśli na przykład zarejestrujesz się w celu założenia bezpłatnego konta platformy Azure, Twój limit wydatków będzie wynosić 200 USD i nie będzie można go zmienić na 500 USD. Limit wydatków można jednak usunąć. Więc albo nie masz limitu, albo masz limit równy kwocie środków. Pozwala to uniknąć większości rodzajów wydatków. Limit wydatków nie jest dostępny dla subskrypcji z planami zobowiązań lub z cenami po stawkach płatności zgodnie z rzeczywistym użyciem. Zobacz [pełną listę typów subskrypcji platformy Azure i dostępność limitu wydatków](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Osiąganie limitu wydatków

Gdy użycie spowoduje naliczenie opłat wyczerpujących limit wydatków, wdrożone usługi zostaną wyłączone na pozostałą część danego okresu rozliczeniowego.

Jeśli na przykład wydasz wszystkie środki zawarte na bezpłatnym koncie platformy Azure, wdrożone zasoby platformy Azure zostaną usunięte ze środowiska produkcyjnego, a maszyny wirtualne platformy Azure zostaną zatrzymane i ich przydział zostanie cofnięty. Dane na kontach magazynu są dostępne tylko do odczytu.

Jeśli Twój typ subskrypcji obejmuje środki na wiele miesięcy, na początku kolejnego okresu rozliczeniowego subskrypcja zostanie ponownie włączona automatycznie. Następnie możesz ponownie wdrożyć zasoby platformy Azure i mieć pełny dostęp do kont magazynu oraz baz danych.

Platforma Azure wysyła powiadomienia e-mail po osiągnięciu limit wydatków. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), aby zobaczyć powiadomienia dotyczące subskrypcji, w przypadku których osiągnięto limit wydatków.

Jeśli masz bezpłatne konto platformy Azure i osiągniesz limit wydatków, możesz przejść na ceny [płatności zgodnie z rzeczywistym użyciem](upgrade-azure-subscription.md), aby usunąć limit wydatków i automatycznie włączyć ponownie subskrypcję.

## <a name="remove-the-spending-limit-in-azure-portal"></a>Usuwanie limitu wydatków w witrynie Azure Portal

Możesz usunąć limit wydatków w dowolnym momencie, o ile z subskrypcją platformy Azure została skojarzona prawidłowa forma płatności. W przypadku typów subskrypcji ze środkami na wiele miesięcy, takich jak Visual Studio Enterprise i Visual Studio Professional, możesz usunąć limit wydatków na czas nieokreślony lub tylko dla bieżącego okresu rozliczeniowego. W przypadku wybrania tylko bieżącego okresu rozliczeniowego limit wydatków zostanie automatycznie włączony na początku kolejnego okresu rozliczeniowego.

Jeśli masz bezpłatne konto platformy Azure, zapoznaj się z artykułem [Podwyższanie poziomu planu subskrypcji platformy Azure](upgrade-azure-subscription.md), aby usunąć limit wydatków. W przeciwnym razie wykonaj następujące czynności, aby usunąć limit wydatków:

<a id="remove"></a>

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) jako administrator konta.
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeniami ](./media/spending-limit/search-bar.png)

1. Na liście **Moje subskrypcje** wybierz subskrypcję. Na przykład *Visual Studio Enterprise*.

   ![Zrzut ekranu przedstawiający przegląd siatki Moje subskrypcje](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Jeśli niektóre subskrypcje Visual Studio nie są tutaj widoczne, może to oznaczać, że w pewnym momencie zmieniono katalog subskrypcji. Dla tych subskrypcji należy przełączyć katalog na katalog oryginalny (katalog, w którym początkowo utworzono konto). Następnie powtórz krok 2.

1. W przeglądzie subskrypcji kliknij pomarańczowy baner, aby usunąć limit wydatków.

    ![Zrzut ekranu przedstawiający baner umożliwiający usunięcie limitu wydatków](./media/spending-limit/msdn-remove-spending-limit-banner-x.png)

1. Wybierz, czy chcesz usunąć limit wydatków na czas nieokreślony, czy tylko w bieżącym okresie rozliczeniowym.

      ![Zrzut ekranu przedstawiający blok usuwania limitu wydatków](./media/spending-limit/remove-spending-limit-blade-x.png)

      | Opcja | Efekt |
      | --- | --- |
      | Usuń trwale limit wydatków | Limit wydatków nie zostanie automatycznie ponownie włączony na początku następnego okresu rozliczeniowego. W dowolnym momencie możesz go jednak ponownie włączyć samodzielnie. |
      | Usuń limit wydatków w bieżącym okresie rozliczeniowym | Limit wydatków zostanie automatycznie ponownie włączony na początku następnego okresu rozliczeniowego. |


1. Kliknij pozycję **Wybierz formę płatności**, aby wybrać formę płatności dla subskrypcji. Stanie się ona aktywną formą płatności dla danej subskrypcji.

1. Kliknij przycisk **Finish** (Zakończ).


## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Dlaczego warto usunąć limit wydatków

Limit wydatków może uniemożliwić wdrażanie lub używanie niektórych usług innych firm i firmy Microsoft. Poniżej przedstawiono sytuacje, w których należy usunąć limit wydatków dla subskrypcji.

-  Planujesz wdrożyć obrazy innych firm, np. Oracle, lub usługi, np. Azure DevOps Services. Ta sytuacja powoduje niemal natychmiastowe osiągnięcie limitu wydatków i wyłączenie subskrypcji.
- Masz usługi, których działania nie chcesz zakłócać. Po osiągnięciu limitu wydatków wdrożone zasoby platformy Azure zostaną usunięte ze środowiska produkcyjnego, a maszyny wirtualne platformy Azure zostaną zatrzymane i zostanie cofnięty ich przydział. Jeśli masz usługi, których działania nie chcesz zakłócać, musisz usunąć limit wydatków.
- Masz usługi i zasoby z ustawieniami, takimi jak wirtualne adresy IP, których nie chcesz utracić. Te ustawienia zostaną utracone, gdy osiągniesz limit wydatków, a przydział usługi i zasobów zostanie cofnięty.

## <a name="turn-on-the-spending-limit-after-removing"></a>Włączanie limitu wydatków po usunięciu

Ta funkcja jest dostępna tylko wtedy, gdy limit wydatków dla typów subskrypcji obejmujących środki na wiele miesięcy został usunięty na czas nieokreślony. Za pomocą tej funkcji można automatycznie włączyć limit wydatków na początku kolejnego okresu rozliczeniowego.


1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) jako administrator konta.
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeniami ](./media/spending-limit/search-bar.png)

1. Na liście **Moje subskrypcje** wybierz subskrypcję. Na przykład *Visual Studio Enterprise*.

   ![Zrzut ekranu przedstawiający przegląd siatki Moje subskrypcje](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Jeśli niektóre subskrypcje Visual Studio nie są tutaj widoczne, może to oznaczać, że w pewnym momencie zmieniono katalog subskrypcji. Dla tych subskrypcji należy przełączyć katalog na katalog oryginalny (katalog, w którym początkowo utworzono konto). Następnie powtórz krok 2.

1. W przeglądzie subskrypcji kliknij baner u góry strony, aby ponownie włączyć limit wydatków.

## <a name="custom-spending-limit"></a>Niestandardowy limit wydatków

Niestandardowe limity wydatków są niedostępne.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Limit wydatków nie uniemożliwia naliczania wszystkich opłat

[Niektórych usług zewnętrznych opublikowanych w witrynie Azure Marketplace](../understand/understand-azure-marketplace-charges.md) nie można używać razem ze środkami powiązanymi z subskrypcją — oddzielne opłaty mogą być naliczane nawet po ustawieniu limitu wydatków. Należą do nich licencje programu Visual Studio, usługa Azure Active Directory w warstwie Premium, plany pomocy technicznej i większość usług innych firm. W przypadku aprowizacji nowej usługi zewnętrznej zostanie wyświetlone ostrzeżenie informujące o tym, że usługi są rozliczane osobno:

![Ostrzeżenie dotyczące zakupu w witrynie Marketplace](./media/spending-limit/marketplace-warning01.png)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- Przeprowadź uaktualnienie do planu z cennikiem typu [płatność zgodnie z rzeczywistym użyciem](upgrade-azure-subscription.md).
