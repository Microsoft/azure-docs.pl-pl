---
title: Zadania administratora konta w witrynie Azure Portal
description: Opisuje sposób wykonywania operacji płatności w witrynie Azure Portal
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/26/2020
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: bd46e7b2f0713da67842def47dfeadc837027d8f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027972"
---
# <a name="account-administrator-tasks-in-the-azure-portal"></a>Zadania administratora konta w witrynie Azure Portal

W tym artykule opisano sposób wykonywania następujących zadań w witrynie Azure Portal:
- Zarządzanie formami płatności dla subskrypcji
- Usuwanie limitu wydatków z subskrypcji
- Dodawanie środków do subskrypcji platformy Azure w ramach programu licencjonowania Open

Aby wykonać dowolne z tych zadań, musisz być administratorem konta.

## <a name="navigate-to-your-subscriptions-payment-methods"></a>Przechodzenie do form płatności dla subskrypcji

1. Zaloguj się w witrynie Azure Portal jako administrator konta.

1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeniami ](./media/account-admin-tasks/search-bar.png)

1. Na liście **Moje subskrypcje** wybierz subskrypcję, do której chcesz dodać kartę kredytową.

   ![Zrzut ekranu przedstawia stronę narzędzia Cost Management + Billing, na której można wybrać subskrypcję.](./media/account-admin-tasks/cost-management-billing-overview-x.png)

   > [!NOTE]
   > Jeśli niektóre subskrypcje nie są tutaj widoczne, może to oznaczać, że w pewnym momencie zmieniono katalog subskrypcji. Dla tych subskrypcji należy przełączyć katalog na katalog oryginalny (katalog, w którym początkowo utworzono konto). Następnie powtórz krok 2.

1. Wybierz pozycję **Formy płatności**.

    ![Zrzut ekranu przedstawia stronę Formy płatności, na której można dodać metodę płatności.](./media/account-admin-tasks/subscription-payment-methods-blade.png)

W tym miejscu możesz dodać nową kartę kredytową, zmienić aktywną formę płatności, edytować szczegóły karty kredytowej i usunąć karty kredytowe.

### <a name="change-active-payment-method"></a>Zmiana aktywnej formy płatności

Aktywną formę płatności możesz zmienić, dodając nową kartę kredytową lub wybierając kartę, która została już zapisana. Aby zmienić aktywną formę płatności na nową kartę kredytową:

1. W lewym górnym rogu wybierz pozycję „+”, aby dodać kartę kredytową.

    ![Zrzut ekranu przedstawiający symbol plusa](./media/account-admin-tasks/subscription-payment-methods-plus.png)

1. Wprowadź szczegóły karty kredytowej w formularzu po prawej stronie.

    ![Zrzut ekranu przedstawiający formularz dodawania karty kredytowej.](./media/account-admin-tasks/subscription-add-payment-method-x.png)

1. Aby ustawić tę kartę jako aktywną formę płatności, zaznacz pole wyboru obok pola **Ustaw jako moją aktywną formę płatności** znajdującego się powyżej formularza. Ta karta stanie się aktywnym instrumentem płatniczym dla wszystkich subskrypcji używających tej karty jako wybranej subskrypcji.

    ![Zrzut ekranu przedstawiający pole wyboru służące do ustawiania karty jako aktywnej formy płatności.](./media/account-admin-tasks/subscription-make-active-payment-method-x.png)

1. Wybierz opcję **Dalej**.

Aby zmienić aktywną formę płatności na kartę kredytową, która została już zapisana:

1. Zaznacz pole obok karty, którą chcesz ustawić jako aktywną formę płatności.

    ![Zrzut ekranu przedstawiający zaznaczone pole obok karty kredytowej](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Na pasku poleceń kliknij przycisk **Ustaw aktywną**.

    ![Zrzut ekranu przedstawiający przycisk Ustaw aktywną](./media/account-admin-tasks/subscription-checked-payment-method-set-active.png)

### <a name="edit-credit-card-details"></a>Edytowanie szczegółowych informacji o karcie kredytowej

Aby edytować szczegółowe informacje o karcie kredytowej, takie jak data wygaśnięcia lub adres, kliknij kartę kredytową, którą chcesz edytować. Po prawej stronie zostanie wyświetlony formularz karty kredytowej.

![Zrzut ekranu przedstawiający wybraną kartę kredytową](./media/account-admin-tasks/subscription-edit-payment-method-x.png)

Zaktualizuj szczegółowe informacje o karcie kredytowej i kliknij przycisk **Zapisz**.

### <a name="remove-a-credit-card-from-the-account"></a>Usuwanie karty kredytowej z konta

1. Zaznacz pole obok karty, którą chcesz usunąć.

    ![Zrzut ekranu przedstawiający zaznaczone pole obok karty kredytowej](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Na pasku poleceń kliknij przycisk **Usuń**.

    ![Zrzut ekranu pokazujący przycisk Usuń](./media/account-admin-tasks/subscription-checked-payment-method-delete.png)

Jeśli karta kredytowa jest aktywną formą płatności dla którejkolwiek z subskrypcji firmy Microsoft, nie możesz usunąć jej z konta platformy Azure. Zmień aktywną formę płatności dla wszystkich subskrypcji połączonych z tą kartą kredytową i spróbuj ponownie.

### <a name="switch-to-invoice-payment"></a>Przełączanie się na płatność za pomocą faktury

Jeśli kwalifikujesz się do płatności za pomocą faktury (czek/przelew), możesz przełączyć subskrypcję na płatność za pomocą faktury (czek/przelew) w witrynie Azure Portal.

1. Wybierz pozycję **Płatność za pomocą faktury** na pasku poleceń.

    ![Zrzut ekranu przedstawia stronę Formy płatności z wybraną opcją Płatność przy użyciu faktury.](./media/account-admin-tasks/subscription-payment-methods-pay-by-invoice.png)

1. Wprowadź adres dla formy płatności za pomocą faktury.
1. Kliknij przycisk **Dalej**.

Jeśli chcesz uzyskać zgodę na płatności za pomocą faktury, zobacz artykuł [Dowiedz się, jak płacić za pomocą faktury](pay-by-invoice.md).

### <a name="edit-invoice-payment-address"></a>Edytowanie adresu płatności za pomocą faktury

Aby edytować adres formy płatności za pomocą faktury, kliknij pozycję **Faktura** na liście form płatności dla subskrypcji. Formularz adresu zostanie otwarty po prawej stronie.

## <a name="remove-spending-limit"></a>Usuwanie limitu wydatków

Dzięki limitowi wydatków na platformie Azure nie można wydawać więcej niż wynosi kwota środków. Możesz usunąć limit wydatków w dowolnym momencie, o ile z subskrypcją platformy Azure została skojarzona prawidłowa forma płatności. W przypadku typów subskrypcji ze środkami na wiele miesięcy, takich jak Visual Studio Enterprise i Visual Studio Professional, możesz ponownie włączyć limit wydatków na początku następnego okresu rozliczeniowego.

Limit wydatków nie jest dostępny dla subskrypcji z planami zobowiązań lub z cenami po stawkach płatności zgodnie z rzeczywistym użyciem.

1. Zaloguj się w witrynie Azure Portal jako administrator konta.
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeniami ](./media/account-admin-tasks/search-bar.png)

1. Na liście **Moje subskrypcje** wybierz subskrypcję Visual Studio Enterprise.

   ![Zrzut ekranu przedstawia obszar Moje subskrypcje, w którym można wybrać subskrypcję programu Visual Studio Enterprise.](./media/account-admin-tasks/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Jeśli niektóre subskrypcje Visual Studio nie są tutaj widoczne, może to oznaczać, że w pewnym momencie zmieniono katalog subskrypcji. Dla tych subskrypcji należy przełączyć katalog na katalog oryginalny (katalog, w którym początkowo utworzono konto). Następnie powtórz krok 2.

1. W przeglądzie subskrypcji kliknij pomarańczowy baner, aby usunąć limit wydatków.

    ![Zrzut ekranu przedstawiający baner umożliwiający usunięcie limitu wydatków](./media/account-admin-tasks/msdn-remove-spending-limit-banner-x.png)

1. Wybierz, czy chcesz usunąć limit wydatków na czas nieokreślony, czy tylko w bieżącym okresie rozliczeniowym.

   ![Zrzut ekranu przedstawiający blok usuwania limitu wydatków](./media/account-admin-tasks/remove-spending-limit-blade-x.png)

1. Kliknij pozycję **Wybierz formę płatności**, aby wybrać formę płatności dla subskrypcji. Stanie się ona aktywną formą płatności dla danej subskrypcji.

1. Kliknij przycisk **Finish** (Zakończ).

## <a name="add-credits-to-azure-in-open-subscription"></a>Dodawanie środków do subskrypcji platformy Azure w ramach programu licencjonowania Open

Jeśli masz subskrypcję platformy Azure w ramach programu licencjonowania Open, możesz dodać środki do subskrypcji w witrynie Azure Portal przez zrealizowanie klucza produktu lub wykupienie środków za pomocą karty kredytowej.

1. Zaloguj się w witrynie Azure Portal jako administrator konta.
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeniami ](./media/account-admin-tasks/search-bar.png)

1. Na liście **Moje subskrypcje** wybierz subskrypcję platformy Azure w ramach programu licencjonowania Open.

    ![Zrzut ekranu przedstawia obszar Moje subskrypcje, w którym można wybrać subskrypcję platformy Azure w ramach programu licencjonowania Open.](./media/account-admin-tasks/cost-management-overview-aio-x.png)

   > [!NOTE]
   > Jeśli Twoja subskrypcja nie jest tutaj widoczna, może to oznaczać, że w pewnym momencie zmieniono jej katalog. Należy przełączyć katalog subskrypcji na katalog oryginalny (katalog, w którym początkowo utworzono konto). Następnie powtórz krok 2.

1. Wybierz pozycję **Historia środków**.

    ![Zrzut ekranu przedstawiający historię środków](./media/account-admin-tasks/aio-credit-history-blade.png)

1. W lewym górnym rogu wybierz pozycję „+”, aby dodać więcej środków.

    ![Zrzut ekranu przedstawiający przycisk Dodaj środki](./media/account-admin-tasks/aio-credit-history-plus.png)

1. Wybierz typ formy płatności z listy rozwijanej. Możesz dodać klucz produktu lub zakupić środki przy użyciu karty kredytowej.

    ![Zrzut ekranu przedstawiający listę rozwijaną formy płatności w bloku dodawania środków](./media/account-admin-tasks/add-credits-select-payment-method.png)

1. W przypadku użycia klucza produktu:
    - Wprowadź klucz produktu
    - Kliknij pozycję **Sprawdź poprawność**

1. W przypadku użycia karty kredytowej:
    - Kliknij pozycję **Wybierz formę płatności**, aby dodać kartę kredytową lub wybrać już istniejącą.
    - Określ ilość środków, które chcesz dodać.

1. Kliknij przycisk **Zastosuj**

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Nie obsługujemy kart wirtualnych i przedpłaconych. Jeśli podczas dodawania lub aktualizowania ważnej karty kredytowej pojawiają się błędy, spróbuj otworzyć przeglądarkę w trybie prywatnym.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat [analizy nieoczekiwanych opłat](../understand/analyze-unexpected-charges.md)
