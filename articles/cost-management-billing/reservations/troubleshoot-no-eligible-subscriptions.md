---
title: Rozwiązywanie problemów z brakiem kwalifikujących się subskrypcji w witrynie Azure Portal
description: Ten artykuł pomaga w rozwiązaniu problemu polegającego na wyświetlaniu komunikatu o błędzie Brak kwalifikujących się subskrypcji w witrynie Azure Portal przy próbie zakupu rezerwacji.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 12/15/2020
ms.openlocfilehash: ad85bd278b5dff1532f218acc0b8e88515d96070
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561209"
---
# <a name="troubleshoot-no-eligible-subscriptions"></a>Rozwiązywanie problemów z brakiem kwalifikujących się subskrypcji

Ten artykuł pomaga w rozwiązaniu problemu polegającego na wyświetlaniu komunikatu o błędzie *Brak kwalifikujących się subskrypcji* w witrynie Azure Portal przy próbie zakupu rezerwacji.

## <a name="symptoms"></a>Objawy

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i przejdź do obszaru **Rezerwacje**.
1. Wybierz pozycję **Dodaj**, a następnie wybierz usługę.
1. Zostanie wyświetlony następujący komunikat o błędzie:
   ```
    No eligible subscriptions
    
    You do not have any eligible subscriptions to purchase reservations. To purchase a reservation, you should have owner or reservation purchaser permission on at least one subscription of the following type: Pay-as-you-go, CSP, Microsoft Enterprise or Microsoft Customer Agreement.
    ```
1. W obszarze **Wybór produktu, który chcesz kupić** rozwiń listę **Subskrypcja rozliczeniowa**, aby zobaczyć, z jakiej przyczyny dana subskrypcja nie kwalifikuje się do zakupu wystąpienia zarezerwowanego. Na poniższym obrazie pokazano przykłady przyczyn uniemożliwiających zakup rezerwacji.  
    :::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" alt-text="Przykład pokazujący, dlaczego nie można kupić rezerwacji" lightbox="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" :::

## <a name="cause"></a>Przyczyna

Aby kupić wystąpienie zarezerwowane na platformie Azure, musisz mieć co najmniej jedną subskrypcję spełniającą następujące wymagania:

- Subskrypcja musi być obsługiwanym typem oferty. Obsługiwane typy ofert to: Płatność zgodnie z rzeczywistym użyciem, Cloud Solution Provider (CSP), Microsoft Azure Enterprise i Umowa z Klientem Microsoft.
- Musisz być nabywcą rezerwacji lub właścicielem subskrypcji.

Jeśli nie masz subskrypcji spełniającej wymagania, zostanie wyświetlony komunikat o błędzie `No eligible subscriptions` (Brak kwalifikujących się subskrypcji).

### <a name="cause-1"></a>Przyczyna 1

Subskrypcja musi być obsługiwanym typem oferty. Obsługiwane typy ofert to: Płatność zgodnie z rzeczywistym użyciem, CSP, Microsoft Azure Enterprise i Umowa z Klientem Microsoft. Typ subskrypcji nie jest jednym z obsługiwanych typów. W przypadku wybrania subskrypcji, której typ oferty nie obsługuje rezerwacji, zostanie wyświetlony następujący błąd.

```
Subscription not eligible for purchase

This subscription is not eligible for reservation benefit an cannot be used to purchase a reservation.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/subscription-not-eligible.png" alt-text="Przykład przedstawiający komunikat o błędzie Subskrypcja nie kwalifikuje się do zakupu" :::

### <a name="cause-2"></a>Przyczyna 2

Musisz być nabywcą rezerwacji lub właścicielem subskrypcji. W przypadku braku wystarczających uprawnień, zobaczysz następujący błąd.

```
You do not have owner or reservation purchaser access on the subscription

You can only purchase reservations using subscriptions on which you have owner or reservation purchaser access.
```

## <a name="solution"></a>Rozwiązanie

- Jeśli Twoja bieżąca oferta nie obsługuje rezerwacji, musisz utworzyć nową subskrypcję platformy Azure.
- Jeśli nie masz dostępu do istniejącej rezerwacji, możesz go uzyskać od bieżącego właściciela.

### <a name="solution-1"></a>Rozwiązanie 1

Aby kupić rezerwację, musisz utworzyć nową subskrypcję platformy Azure, która obsługuje rezerwacje.

- Jeśli korzystasz z bezpłatnej wersji próbnej platformy Azure, możesz [podwyższyć poziom planu subskrypcji](../manage/upgrade-azure-subscription.md).
- Możesz utworzyć nową subskrypcję platformy Azure korzystającą ze [stawek przy płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).
- Zarejestruj się, zawierając [Umowę z Klientem Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/), i utwórz nową subskrypcję.
- Kup nową subskrypcję w ramach programu [CSP](https://www.microsoft.com/solution-providers/home) i utwórz nową subskrypcję.

### <a name="solution-2"></a>Rozwiązanie 2

Aby uzyskać dostęp właściciela do rezerwacji, musisz mieć dostęp do jednego z następujących elementów:

- Zamówienie rezerwacji, którego użyto do zakupu rezerwacji
- Sama rezerwacja

Obecny właściciel zamówienia rezerwacji lub właściciel rezerwacji może delegować Ci dostęp, wykonując poniższe kroki.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Wszystkie usługi** > **Rezerwacja** w celu wyświetlenia listy rezerwacji, do których masz dostęp.
1. Wybierz rezerwację, dla której chcesz delegować dostęp do innych użytkowników.
1. Wybierz pozycję **Kontrola dostępu (IAM)** .
1. Wybierz pozycję **Dodaj przypisanie roli** > **Rola** > **Właściciel**. Ewentualnie, jeśli chcesz nadać ograniczony dostęp, wybierz inną rolę.
1. Wpisz adres e-mail użytkownika, którego chcesz dodać jako właściciela.
1. Wybierz użytkownika, a następnie wybierz polecenie **Zapisz**.

Aby uzyskać więcej informacji, zobacz [Dodawanie lub zmienianie użytkowników, którzy mogą zarządzać rezerwacją](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).

## <a name="next-steps"></a>Następne kroki

- Zobacz [Dodawanie lub zmienianie użytkowników, którzy mogą zarządzać rezerwacją](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default), jeśli właściciel rezerwacji musi przyznać Ci dostęp.
