---
title: Wyświetlanie i pobieranie cennika platformy Azure organizacji
description: Dowiedz się, jak wyświetlać i pobierać cennik oraz szacować koszty za pomocą cennika organizacji.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 63e648681a63daafe1eaeb1d2e8e5a6cd607aa70
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91446337"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Wyświetlanie i pobieranie cennika platformy Azure organizacji

Klienci platformy Azure z umową Azure Enterprise Agreement (EA), umową klienta firmy Microsoft (MCA) lub umową partnerską firmy Microsoft (MPA) mogą wyświetlać i pobierać cennik za pośrednictwem witryny Azure Portal. [Dowiedz się, jak sprawdzić typ konta rozliczeniowego](#check-your-billing-account-type).

## <a name="download-pricing-for-an-enterprise-agreement"></a>Pobieranie cennika dla umowy Enterprise Agreement

W zależności od zasad ustawionych dla organizacji przez administratora przedsiębiorstwa tylko niektóre role administracyjne zapewniają dostęp do informacji o cenniku EA organizacji. Aby uzyskać więcej informacji, zobacz [Omówienie ról administracyjnych dla umowy Azure Enterprise Agreement na platformie Azure](understand-ea-roles.md).

1. Jako administrator przedsiębiorstwa zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
1. Wyszukaj pozycję *Zarządzanie kosztami i rozliczenia*.

   ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal.](./media/ea-pricing/portal-cm-billing-search.png)

1. W obszarze konta rozliczeniowego wybierz pozycję **Użycie i opłaty**.

   ![Zrzut ekranu przedstawiający użycie i opłaty w obszarze Rozliczenia](./media/ea-pricing/ea-pricing-usage-charges-nav.png)

1. Wybierz ![ikonę pobierania.](./media/ea-pricing/download-icon.png) **Pobierz** dla miesiąca.

1. W obszarze **Arkusz cen** wybierz opcję **Pobierz plik CSV**.

   ![Zrzut ekranu przedstawia opcje Pobierz użycie i opłaty.](./media/ea-pricing/download-ea-price-sheet.png)

## <a name="download-pricing-for-an-mca-or-mpa-account"></a>Pobieranie cennika dla konta z umową MCA lub MPA

Jeśli masz umowę MCA, to aby móc wyświetlać i pobierać cennik, musisz być właścicielem, współautorem, czytelnikiem lub menedżerem faktur w profilu rozliczeniowym. Jeśli masz umowę MPA, to aby móc wyświetlać i pobierać cennik, musisz mieć rolę Administrator globalny lub Agent administratora w organizacji partnera.

### <a name="download-price-sheets-for-billed-charges"></a>Pobieranie arkuszy cen dla naliczanych opłat

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję *Zarządzanie kosztami i rozliczenia*.
1. Wybierz profil rozliczeniowy. W zależności od praw dostępu może być konieczne wybranie najpierw konta rozliczeniowego.
1. Wybierz pozycję **Faktury**.
1. W siatce faktury znajdź wiersz faktury odpowiadający arkuszowi cen, który chcesz pobrać.
1. Kliknij symbol wielokropka (`...`) na końcu wiersza.
![Zrzut ekranu przedstawiający wybrany symbol wielokropka](./media/ea-pricing/billingprofile-invoicegrid-new.png)

1. Jeśli chcesz zobaczyć ceny usług w ramach wybranej faktury, wybierz pozycję **Arkusz cen faktury**.
1. Jeśli chcesz zobaczyć ceny dla wszystkich usług platformy Azure w danym okresie rozliczeniowym, wybierz pozycję **Arkusz cen platformy Azure**.

![Zrzut ekranu przedstawiający menu kontekstowe z arkuszami cen](./media/ea-pricing/contextmenu-pricesheet01.png)

### <a name="download-price-sheets-for-the-current-billing-period"></a>Pobieranie arkuszy cen dla bieżącego okresu rozliczeniowego

Jeśli masz umowę MCA, możesz pobrać cennik dla bieżącego okresu rozliczeniowego.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję *Zarządzanie kosztami i rozliczenia*.
1. Wybierz profil rozliczeniowy. W zależności od praw dostępu może być konieczne wybranie najpierw konta rozliczeniowego.
1. W obszarze **Przegląd** znajdź linki pobierania pod opłatami za bieżący miesiąc.
1. Wybierz pozycję **Arkusz cen platformy Azure**.
![Zrzut ekranu przedstawiający pobieranie z obszaru Przegląd](./media/ea-pricing/open-pricing01.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Szacowanie kosztów przy użyciu kalkulatora cen platformy Azure

Możesz również użyć cennika organizacji, aby oszacować koszty za pomocą kalkulatora cen platformy Azure.

1. Przejdź do [Kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator).
1. W prawym górnym rogu wybierz pozycję **Zaloguj**.
1. W obszarze **Programy i oferty** > **Program licencjonowania** wybierz opcję **Enterprise Agreement (EA)** .
1. W obszarze **Programy i oferty** > **Wybrana umowa** wybierz opcję **Niczego nie wybrano**.

    ![Zrzut ekranu przedstawia dostępne programy i oferty.](./media/ea-pricing/ea-pricing-calculator-estimate.png)

1. Wybierz organizację.
1. Wybierz przycisk **Zastosuj**.
1. Wyszukaj, a następnie dodaj produkty do oszacowania.
1. Wyświetlane szacowane ceny są oparte na cenach dla wybranej organizacji.

## <a name="check-your-billing-account-type"></a>Sprawdzanie typu konta rozliczeniowego
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś klientem z umową EA, zobacz:

- [Zarządzanie dostępem do informacji rozliczeniowych umów EA dla platformy Azure](manage-billing-access.md)
- [Wyświetlanie i pobieranie faktury platformy Microsoft Azure](../understand/download-azure-invoice.md)
- [Wyświetlanie i pobieranie danych na temat użycia i opłat na platformie Microsoft Azure](../understand/download-azure-daily-usage.md)
- [Omówienie zawartości rachunku dla klientów z umową Enterprise Agreement](../understand/review-enterprise-agreement-bill.md)

Jeśli masz umowę klienta firmy Microsoft, zobacz:

- [Omówienie warunków w arkuszu cen dla umowy klienta firmy Microsoft](mca-understand-pricesheet.md)
- [Wyświetlanie i pobieranie faktury platformy Microsoft Azure](../understand/download-azure-invoice.md)
- [Wyświetlanie i pobieranie danych na temat użycia i opłat na platformie Microsoft Azure](../understand/download-azure-daily-usage.md)
- [Wyświetlanie i pobieranie dokumentów podatkowych dla profilu rozliczeniowego](../understand/mca-download-tax-document.md)
- [Informacje o opłatach na fakturze w profilu rozliczeniowym](../understand/review-customer-agreement-bill.md)
