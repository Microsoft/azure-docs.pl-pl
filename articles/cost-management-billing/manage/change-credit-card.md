---
title: Zmiana karty kredytowej dla platformy Azure
description: Opisuje sposób zmiany karty kredytowej używanej do płacenia za subskrypcję platformy Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 99b6d86a65ec05a1299abfffae779113e573310c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834972"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Dodawanie, aktualizowanie lub usuwanie karty kredytowej dla platformy Azure

Ten dokument dotyczy klientów, którzy zarejestrowali się na platformie Azure w trybie online przy użyciu karty kredytowej.

W witrynie Azure Portal możesz zmienić domyślną formę płatności na nową kartę kredytową, zaktualizować informacje o karcie kredytowej i usunąć karty kredytowe, których nie używasz. Musisz być [administratorem konta](billing-subscription-transfer.md#whoisaa), aby wprowadzić te zmiany.

Obsługiwane formy płatności dla platformy Microsoft Azure to karty kredytowe i czek/przelew. Aby uzyskać zgodę na płatność za pomocą czeku/przelewu, zobacz [Płatność za subskrypcje platformy Azure przy użyciu faktury](pay-by-invoice.md).

W przypadku Umowy z Klientem Microsoft formy płatności są skojarzone z profilami rozliczeniowymi. Dowiedz się, jak [sprawdzić dostęp do Umowy z Klientem Microsoft](#check-the-type-of-your-account). Jeśli masz Umowę z Klientem Microsoft, przejdź do [zarządzania kartami kredytowymi dla Umowy z Klientem Microsoft](#manage-credit-cards-for-a-microsoft-customer-agreement).

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>Zarządzanie kartami kredytowymi dla subskrypcji platformy Azure

Poniższe sekcje dotyczą klientów, którzy mają konto rozliczeniowe programu Microsoft Online Services. Dowiedz się, jak [sprawdzić typ konta rozliczeniowego](#check-the-type-of-your-account). Jeśli typ konta rozliczeniowego to Program Microsoft Online Services, formy płatności są skojarzone z poszczególnymi subskrypcjami platformy Azure. Jeśli po dodaniu karty kredytowej wystąpi błąd, zobacz [Karta kredytowa została odrzucona podczas tworzenia konta platformy Azure](../../billing/billing-credit-card-fails-during-azure-sign-up.md).

### <a name="change-credit-card-for-a-subscription-by-adding-a-new-credit-card"></a>Zmienianie karty kredytowej dla subskrypcji przez dodanie nowej karty kredytowej

Możesz zmienić domyślną kartę kredytową platformy Azure na nową lub wcześniej zapisaną w witrynie Azure Portal. Aby zmienić kartę kredytową, musisz być administratorem konta. Jeśli więcej niż jedna subskrypcja ma aktywną tę samą formę płatności, zmiana aktywnej formy płatności dowolnej z tych subskrypcji spowoduje także zaktualizowanie aktywnej formy płatności pozostałych subskrypcji.


Możesz zmienić domyślną kartę kredytową subskrypcji na nową, wykonując następujące kroki:

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) jako administrator konta.
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie](./media/change-credit-card/search.png)

1. Wybierz subskrypcję, do której chcesz dodać kartę kredytową.
1. Wybierz pozycję **Formy płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcję Zarządzaj metodami płatności.](./media/change-credit-card/payment-methods-blade-x.png)

1. W lewym górnym rogu wybierz pozycję „+”, aby dodać kartę. Po prawej stronie zostanie wyświetlony formularz karty kredytowej.
1. Podaj szczegółowe informacje dotyczące karty kredytowej.

    ![Zrzut ekranu pokazujący dodawanie nowej karty.](./media/change-credit-card/sub-add-new-x.png)

1. Aby ustawić tę kartę jako aktywną formę płatności, zaznacz pole wyboru obok pola **Ustaw jako moją aktywną formę płatności** znajdującego się powyżej formularza. Ta karta stanie się aktywnym instrumentem płatniczym dla wszystkich subskrypcji używających tej karty jako wybranej subskrypcji.

1. Wybierz opcję **Dalej**.

### <a name="change-credit-card-for-a-subscription-to-a-previously-saved-credit-card"></a>Zmienianie karty kredytowej dla subskrypcji na wcześniej zapisaną kartę kredytową

Możesz również zmienić domyślną kartę kredytową subskrypcji na taką, która jest już zapisana na koncie, wykonując następujące kroki:

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) jako administrator konta.
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie](./media/change-credit-card/search.png)

1. Wybierz subskrypcję, do której chcesz dodać kartę kredytową.
1. Wybierz pozycję **Formy płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcję Zarządzaj metodami płatności.](./media/change-credit-card/payment-methods-blade-x.png)

1. Zaznacz pole obok karty, którą chcesz ustawić jako aktywną formę płatności.
1. Kliknij pozycję **Ustaw aktywną**.
    ![Zrzut ekranu przedstawiający wybraną i aktywowaną kartę kredytową.](./media/change-credit-card/sub-change-active-x.png)

### <a name="edit-credit-card-details"></a>Edytowanie szczegółowych informacji o karcie kredytowej

Jeśli karta kredytowa jest odnawiana, a numer pozostaje taki sam, zaktualizuj istniejące szczegóły karty kredytowej, takie jak data wygaśnięcia. Jeśli numer karty kredytowej zmienia się, ponieważ karta została utracona, skradziona lub wygasła, wykonaj kroki opisane w sekcji [Dodawanie karty kredytowej jako formy płatności](#addcard). Nie musisz aktualizować kodu CVV.

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) jako administrator konta.
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie](./media/change-credit-card/search.png)

1. Wybierz pozycję **Formy płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcję Zarządzaj metodami płatności.](./media/change-credit-card/payment-methods-blade-x.png)

1. Kliknij kartę kredytową, którą chcesz edytować. Po prawej stronie zostanie wyświetlony formularz karty kredytowej.

    ![Zrzut ekranu przedstawiający wybraną kartę kredytową.](./media/change-credit-card/edit-card-x.png)

1. Zaktualizuj szczegółowe informacje o karcie kredytowej.
1. Wybierz pozycję **Zapisz**.

### <a name="delete-a-credit-card-from-the-account"></a>Usuwanie karty kredytowej z konta

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) jako administrator konta.
1. Wybierz pozycję **Zarządzanie kosztami i rozliczenia** w lewej części strony.

    ![Zrzut ekranu przedstawiający wyszukiwanie](./media/change-credit-card/search.png)

1. W obszarze **Rozliczenia** wybierz pozycję **Formy płatności**.

    ![Zrzut ekranu przedstawiający wybraną opcję Zarządzaj metodami płatności.](./media/change-credit-card/payment-methods-blade-x.png)

1. Zaznacz pole wyboru obok karty, którą chcesz usunąć.
1. Kliknij polecenie **Usuń**.

Jeśli karta kredytowa jest aktywną formą płatności dla którejkolwiek z subskrypcji firmy Microsoft, nie możesz usunąć jej z konta platformy Azure. Zmień aktywną formę płatności dla wszystkich subskrypcji połączonych z tą kartą kredytową i spróbuj ponownie.

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>Zarządzanie kartami kredytowymi dla Umowy z Klientem Microsoft

Poniższe sekcje dotyczą klientów, którzy mają Umowę z Klientem Microsoft i zarejestrowali się w usłudze Azure w trybie online przy użyciu karty kredytowej. [Dowiedz się, jak sprawdzić, czy masz dostęp do Umowy z Klientem Microsoft](#check-the-type-of-your-account).

### <a name="change-default-credit-card"></a>Zmienianie domyślnej karty kredytowej

Jeśli masz Umowę z Klientem Microsoft, Twoja karta kredytowa jest skojarzona z profilem rozliczeniowym. Aby zmienić formę płatności dla profilu rozliczeniowego, musisz być osobą, która zarejestrowała się na platformie Azure i utworzyła konto rozliczeniowe.

Jeśli chcesz zmienić domyślną formę płatności profilu rozliczeniowego na czek/przelew, zobacz [Płacenie za subskrypcje platformy Azure za pomocą faktury](pay-by-invoice.md).

Aby zmienić kartę kredytową, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.
1. W menu po lewej stronie kliknij pozycję **Profile rozliczeniowe**.
1. Wybierz profil rozliczeniowy.
1. W menu po lewej stronie wybierz pozycję **Formy płatności**.

   ![Zrzut ekranu przedstawiający formy płatności w menu](./media/change-credit-card/payment-methods-tab-mca.png)

1. W sekcji **Domyślna forma płatności** kliknij pozycję **Zmień**.

    ![Zrzut ekranu pokazujący przycisk zmiany](./media/change-credit-card/change-payment-method-mca.png)

1. W nowym bloku po prawej stronie wybierz istniejącą kartę z listy rozwijanej lub dodaj nową, klikając niebieski link „Dodaj nową formę płatności”.

### <a name="edit-or-delete-a-credit-card"></a>Edytowanie lub usuwanie karty kredytowej

Możesz edytować szczegóły kart kredytowych (na przykład zaktualizować datę wygaśnięcia) i usuwać karty kredytowe ze swojego konta w witrynie Azure Portal. Kartę kredytową możesz usunąć tylko wtedy, gdy nie jest skojarzona z żadną subskrypcją platformy Azure ani profilem rozliczeniowym. Jeśli jest ona skojarzona z wyłączoną subskrypcją platformy Azure, z usunięciem karty kredytowej musisz zaczekać, aż subskrypcja zostanie usunięta (od 30 do 90 dni po anulowaniu).

Aby edytować lub usunąć kartę kredytową, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.
1. W menu po lewej stronie kliknij pozycję **Profile rozliczeniowe**.
1. Wybierz profil rozliczeniowy.
1. W menu po lewej stronie wybierz pozycję **Formy płatności**.

   ![Zrzut ekranu przedstawiający formy płatności w menu](./media/change-credit-card/payment-methods-tab-mca.png)

1. W sekcji **Twoje karty kredytowe** znajdź kartę kredytową, którą chcesz edytować lub usunąć.
1. Kliknij wielokropek (`...`) na końcu wiersza.

    ![Zrzut ekranu przedstawiający wielokropek](./media/change-credit-card/edit-delete-credit-card-mca.png)

1. Aby edytować szczegóły karty kredytowej, wybierz pozycję **Edytuj** z menu kontekstowego.
1. Aby usunąć kartę kredytową, wybierz pozycję **Usuń** z menu kontekstowego.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Nie obsługujemy kart wirtualnych i przedpłaconych. Jeśli podczas dodawania lub aktualizowania ważnej karty kredytowej pojawiają się błędy, spróbuj otworzyć przeglądarkę w trybie prywatnym.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

W poniższych sekcjach zamieszczono odpowiedzi na często zadawane pytania dotyczące zmiany danych karty kredytowej.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Moja subskrypcja jest wyłączona. Dlaczego nie mogę teraz usunąć karty kredytowej?

Po wyłączeniu lub anulowaniu subskrypcji czekamy 90 dni przed trwałym usunięciem subskrypcji. W okresie przechowywania zachowujemy informację o formie płatności na wypadek ponownego aktywowania subskrypcji. Po tym okresie subskrypcja zostanie trwale usunięta.

Jeśli musisz usunąć kartę kredytową przed upływem 90-dniowego okresu przechowywania, [aktywuj ponownie swoją subskrypcję](subscription-disabled.md). Jeśli nie możesz przeprowadzić ponownej aktywacji, [ skontaktuj się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Dlaczego ciągle otrzymuję komunikat „Twoja sesja wygasła. Kliknij tutaj, aby ponownie się zalogować.”?

Jeśli ten komunikat o błędzie pojawia się nawet po wylogowaniu i ponownym zalogowaniu, spróbuj ponownie za pomocą prywatnej sesji przeglądania.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Jak mogę użyć innej karty dla każdej mojej subskrypcji?

Niestety, jeśli subskrypcje już korzystają z tej samej karty, nie jest możliwe rozdzielenie ich, tak aby korzystały z różnych kart. Jednak po zarejestrowaniu nowej subskrypcji możesz wybrać dla niej nową formę płatności.

### <a name="how-do-i-make-payments"></a>Jak mogę płacić?

Jeśli skonfigurujesz kartę kredytową jako formę płatności, automatycznie obciążamy kartę po każdym okresie rozliczeniowym. Nie musisz wykonywać żadnych czynności.

W przypadku [płatności przy użyciu faktury](pay-by-invoice.md) wyślij płatność do lokalizacji wskazanej na dole faktury.

### <a name="how-do-i-change-the-tax-id"></a>Jak mogę zmienić numer NIP?

Aby dodać lub zaktualizować numer NIP, zaktualizuj swój profil w [Centrum konta platformy Azure](https://account.azure.com/Profile), a następnie wybierz pozycję **Rejestr podatkowy**. Numer NIP jest używany do obliczenia kwoty zwolnienia z podatku i pojawia się na fakturze.

## <a name="check-the-type-of-your-account"></a>Sprawdzanie typu konta

[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [rezerwacjami platformy Azure](../reservations/save-compute-costs-reservations.md), aby dowiedzieć się, czy możesz oszczędzić pieniądze.
