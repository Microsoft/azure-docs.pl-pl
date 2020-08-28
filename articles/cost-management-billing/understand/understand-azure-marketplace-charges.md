---
title: Informacje o opłatach za usługi zewnętrzne na platformie Azure
description: Dowiedz się więcej na temat rozliczeń za usługi zewnętrzne, nazywane dawniej usługami platformy Marketplace, na platformie Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8da85ec5781ff9575cf380092fea9e41d6af8786
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686126"
---
# <a name="understand-your-azure-external-services-charges"></a>Informacje o opłatach za usługi zewnętrzne na platformie Azure
Usługi zewnętrzne są publikowane przez zewnętrznych dostawców oprogramowania na platformie Azure Marketplace. Na przykład SendGrid to usługa zewnętrzna, którą można zakupić na platformie Azure, ale która nie jest publikowana przez firmę Microsoft. Także niektóre produkty firmy Microsoft są sprzedawane na platformie Azure Marketplace.

## <a name="how-external-services-are-billed"></a>Jak są rozliczane usługi zewnętrzne

- Jeśli masz umowę klienta firmy Microsoft (MCA) lub umowę partnerską firmy Microsoft (MPA), usługi innych firm są rozliczane łącznie z pozostałymi usługami platformy Azure. [Sprawdź typ konta rozliczeniowego](#check-billing-account-type), aby sprawdzić, czy masz dostęp do umowy MCA lub MPA.
- Jeśli nie masz umowy MCA ani MPA, usługi zewnętrzne są rozliczane oddzielnie od usług platformy Azure. W każdym okresie rozliczeniowym otrzymasz dwie faktury: jedną fakturę dla usługą platformy Azure, a drugą dla zakupów w witrynie Marketplace.
- Każda usługa zewnętrzna ma inny model rozliczeń. Niektóre usługi są rozliczane na zasadzie płatności zgodnie z rzeczywistym użyciem, a inne mają stałe opłaty miesięczne.
- Za usługi zewnętrzne nie można płacić przy użyciu otrzymywanych co miesiąc bezpłatnych środków. Jeśli korzystasz z subskrypcji platformy Azure, w ramach której otrzymujesz [bezpłatne środki](https://azure.microsoft.com/pricing/spending-limits/), nie możesz użyć tych środków do opłacenia usług zewnętrznych. Podczas aprowizacji nowych usług i zasobów zewnętrznych jest wyświetlane ostrzeżenie:

    ![Ostrzeżenie dotyczące zakupu w witrynie Marketplace](./media/understand-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/understand-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/understand-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/understand-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/understand-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="external-spending-for-ea-customers"></a>Wydatki zewnętrzne dla klientów z umowami EA

Klienci z umowami EA mogą wyświetlać wydatki na usługi zewnętrzne i pobierać raporty w witrynie EA Portal. Aby dowiedzieć się, jak zacząć pracę, zobacz [Azure Marketplace dla klientów z umowami EA](https://ea.azure.com/helpdocs/azureMarketplace).

## <a name="view-and-download-invoices-for-external-services"></a>Wyświetlanie i pobieranie faktur dla usług zewnętrznych

Jeśli masz umowę klienta firmy Microsoft (MCA) lub umowę Microsoft Partner Agreement (MPA), usługi innych firm są rozliczane łącznie z pozostałymi usługami platformy Azure na jednej fakturze. [Sprawdź typ konta rozliczeniowego](#check-billing-account-type), aby sprawdzić, czy masz dostęp do umowy MCA lub MPA. Jeśli to zrobisz, zobacz [Wyświetlanie i pobieranie faktur w witrynie Azure Portal](download-azure-invoice.md), aby zobaczyć opłaty na rzecz innych firm.

Jeśli nie masz umowy MCA lub MPA, dostajesz oddzielne faktury z opłatami na rzecz innych firm. 

Opłaty za zakupy w witrynie Azure Marketplace są pokazywane w lokalnej walucie.

Możesz wyświetlać i pobierać faktury za usługi z platformy Azure Marketplace w witrynie Azure Portal, wykonując następujące czynności:

1. Zaloguj się do [Azure portal](https://portal.azure.com).
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.
1. W menu po lewej stronie wybierz pozycję **Faktury**.
1. Za listy rozwijanej subskrypcji wybierz subskrypcję skojarzoną z usługami witryny Marketplace.
1. Przejrzyj kolumnę **Typ** na liście faktur. Jeśli faktura dotyczy usługi Marketplace, będzie miała typ **Azure Marketplace i rezerwacje**. 

    ![zrzut ekranu przedstawiający typ Azure Marketplace w siatce faktury](./media/understand-azure-marketplace-charges/marketplace-type-twd.png)

1. Aby filtrować według typu, tak aby wyświetlić tylko faktury dla witryny Azure Marketplace i rezerwacji, wybierz filtr **Typ**. Następnie na liście rozwijanej wybierz pozycję **Azure Marketplace i rezerwacje**.

    ![zrzut ekranu przedstawiający wybrany filtr Typ, z pozycją Azure Marketplace i rezerwacje zaznaczoną na liście rozwijanej](./media/understand-azure-marketplace-charges/type-filter.png)

1. Wybierz ikonę pobierania po prawej stronie dla faktury, którą chcesz pobrać.

    ![zrzut ekranu przedstawiający ikonę pobierania wybraną dla faktury](./media/understand-azure-marketplace-charges/download-icon-marketplace.png)

1. W obszarze **Faktura** wybierz niebieski przycisk **Pobierz**.

    ![zrzut ekranu przedstawiający przycisk pobierania dla faktury w okienku kontekstu](./media/understand-azure-marketplace-charges/invoice-download-marketplace.png)

## <a name="pay-for-external-services-in-the-azure-portal"></a>Płatność za usługi zewnętrzne w witrynie Azure Portal

Jeśli masz umowę klienta firmy Microsoft (MCA) lub umowę partnerską firmy Microsoft (MPA), usługi innych firm są rozliczane łącznie z pozostałymi usługami platformy Azure. [Sprawdź typ konta rozliczeniowego](#check-billing-account-type), aby sprawdzić, czy masz dostęp do umowy MCA lub MPA. Jeśli tak, możesz opłacić całą fakturę w witrynie Azure Portal, wykonując kroki opisane w temacie [Opłacanie rachunku za korzystanie z platformy Microsoft Azure](pay-bill.md).

Jeśli nie masz umowy klienta firmy Microsoft ani umowy MPA, możesz opłacić swoje faktury witryny Marketplace w witrynie Azure Portal, wykonując następujące kroki:

1. Postępuj zgodnie z instrukcjami w poprzedniej sekcji, [Wyświetlanie i pobieranie faktur dla usług zewnętrznych](#view-and-download-invoices-for-external-services), aby znaleźć faktury witryny Marketplace.
1. Wybierz niebieski link **Zapłać teraz** dla faktury, którą chcesz opłacić.

    ![zrzut ekranu przedstawiający link Zapłać teraz wybrany w siatce faktur](./media/understand-azure-marketplace-charges/pay-now-twd.png)

    >[!NOTE]
    > Link **Zapłać teraz** jest widoczny tylko wtedy, gdy typ faktury to **Azure Marketplace i rezerwacje**, a termin płatności faktury zbliża się lub upłynął.

1. Na nowej stronie kliknij niebieski link **Wybierz formę płatności**.

    ![zrzut ekranu przedstawiający zaznaczony link wyboru formy płatności](./media/understand-azure-marketplace-charges/select-payment-method-pay-now-twd.png)

1. Po wybraniu formy płatności kliknij niebieski przycisk **Zapłać teraz** w lewym dolnym rogu strony.
    ![zrzut ekranu przedstawiający zaznaczony przycisk Zapłać teraz](./media/understand-azure-marketplace-charges/pay-now-button-twd.png)

## <a name="change-default-payment-for-external-services"></a>Zmiana domyślnej formy płatności dla usług zewnętrznych

Podczas zakupu usługi zewnętrznej należy wybrać subskrypcję platformy Azure, w której zostaną utworzone jej zasoby. Metoda płatności skojarzona z wybraną subskrypcją platformy Azure zostanie użyta do płatności za usługę zewnętrzną. Aby zmienić metodę płatności za usługę zewnętrzną, należy [zmienić metodę płatności za subskrypcję platformy Azure ](../manage/change-credit-card.md) powiązaną z tą usługą zewnętrzną. Aby określić, z którą subskrypcją jest powiązane zamówienie usługi zewnętrznej, wykonaj następujące czynności:

1. Zaloguj się do [Azure portal](https://portal.azure.com).
1. Kliknij pozycję **Wszystkie zasoby** w menu nawigacji po lewej stronie.
     ![Zrzut ekranu przedstawiający element menu Wszystkie zasoby](./media/understand-azure-marketplace-charges/all-resources.png)
1. Wyszukaj usługę zewnętrzną.
1. Wyszukaj nazwę subskrypcji w kolumnie **Subskrypcja**.
    ![Zrzut ekranu przedstawiający nazwę subskrypcji powiązanej z zasobem](./media/understand-azure-marketplace-charges/sub-selected.png)
1. Kliknij nazwę subskrypcji i [zaktualizuj aktywną metodę płatności](../manage/change-credit-card.md).

## <a name="cancel-an-external-service-order"></a>Anulowanie zamówienia usługi zewnętrznej

Jeśli chcesz anulować zamówienie usługi zewnętrznej, usuń odpowiedni zasób w witrynie [Azure Portal](https://portal.azure.com).

1. Zaloguj się do [Azure portal](https://portal.azure.com).
1. Kliknij pozycję **Wszystkie zasoby** w menu nawigacji po lewej stronie.
    ![Zrzut ekranu przedstawiający element menu Wszystkie zasoby](./media/understand-azure-marketplace-charges/all-resources.png)
1. Wyszukaj usługę zewnętrzną.
1. Zaznacz pole wyboru obok zasobu, który chcesz usunąć.
1. Na pasku poleceń wybierz pozycję **Usuń**.
    ![Zrzut ekranu przedstawiający przycisk Usuń](./media/understand-azure-marketplace-charges/delete-button.png)
1. W bloku potwierdzenia wpisz *Yes* (Tak).
    ![Usuwanie zasobu](./media/understand-azure-marketplace-charges/delete-resource.PNG)
1. Kliknij polecenie **Usuń**.

## <a name="check-billing-account-type"></a>Sprawdzanie typu konta rozliczeniowego
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- [Rozpoczęcie analizy kosztów](../costs/quick-acm-cost-analysis.md)