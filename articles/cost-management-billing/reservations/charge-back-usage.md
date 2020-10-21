---
title: Koszty rezerwacji platformy Azure — obciążenie zwrotne
description: Dowiedz się, jak wyświetlać koszty rezerwacji platformy Azure na potrzeby obciążenia zwrotnego.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: aba6ea467788c51d179ef9377243efb6035b6f98
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148355"
---
# <a name="charge-back-azure-reservation-costs"></a>Koszty rezerwacji platformy Azure — obciążenie zwrotne

Osoby pełniące rolę czytelnika rozliczeń dla umów Enterprise i umów z Klientem Microsoft mogą przeglądać dane o zamortyzowanych kosztach rezerwacji. Na podstawie tych danych o kosztach mogą wykonać obciążenie zwrotne wartości pieniężnej za subskrypcję, grupę zasobów, zasób lub tag na rzecz partnerów. W danych zamortyzowanych rzeczywista cena to obliczony proporcjonalnie godzinowy koszt rezerwacji. Jest to całkowity koszt użycia rezerwacji przez zasób w danym dniu.

Użytkownicy mający subskrypcję indywidualną mogą znaleźć dane o kosztach zamortyzowanych w pliku użycia. Jeśli zasób jest objęty rabatem za rezerwację, szczegóły rezerwacji będą widoczne w sekcji *AdditionalInfo* (Informacje dodatkowe) w pliku użycia. Aby uzyskać więcej informacji, zobacz [Pobieranie danych użycia z witryny Azure Portal](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv).

## <a name="get-reservation-charge-back-data-for-chargeback"></a>Pobieranie danych dotyczących obciążeń zwrotnych za rezerwacje

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do obszaru **Zarządzanie kosztami i rozliczenia**.
1. W obszarze **Koszt rzeczywisty** wybierz metrykę **Koszt zamortyzowany**.
1. Aby sprawdzić, które zasoby były używane z rezerwacją, zastosuj filtr **Rezerwacja** i wybierz rezerwacje.
1. Ustaw **Poziom szczegółowości** na **Miesięczny** lub **Dzienny**.
1. Ustaw typ wykresu **Tabela**.
1. Ustaw opcję **Grupuj według** na **Zasób**.

[![Przykład przedstawiający koszty zasobów rezerwacji, których można użyć do wykonania obciążenia zwrotnego](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

W tym wideo pokazano, jak wyświetlać koszty użycia rezerwacji w witrynie Azure Portal.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak zarządzać wystąpieniem zarezerwowanym, zobacz temat [Manage Azure Reservations](manage-reserved-vm-instance.md) (Zarządzanie wystąpieniami zarezerwowanymi na platformie Azure).
- Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:
  - [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
  - [Zarządzanie rejestracjami platformy Azure](manage-reserved-vm-instance.md)