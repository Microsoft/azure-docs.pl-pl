---
title: Stosowanie rabatów na rezerwacje usługi Azure Synapse Analytics | Microsoft Docs
description: Dowiedz się, w jaki sposób rabaty na rezerwacje stosowane względem usługi Azure Synapse Analytics pomagają zaoszczędzić pieniądze.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: banders
ms.openlocfilehash: f0bd57befc0ec30473065ac69026a77e21e30e5c
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682607"
---
# <a name="how-reservation-discounts-apply-to-azure-synapse-analytics"></a>Stosowanie rabatów na rezerwacje usługi Azure Synapse Analytics

Po zakupie pojemności zarezerwowanej usługi Azure Synapse Analytics rabat dotyczący rezerwacji jest automatycznie stosowany do aprowizowanych wystąpień istniejących w danym regionie. Rabat dotyczący rezerwacji ma zastosowanie do użycia emitowanego przez miernik cDWU usługi Azure Synapse Analytics. Opłaty za magazyn i sieć są naliczane według stawek płatności zgodnie z rzeczywistym użyciem.

## <a name="reservation-discount-application"></a>Stosowanie rabatu za wystąpienia zarezerwowane

Rabat na pojemność zarezerwowaną usługi Azure Synapse Analytics jest stosowany względem działających magazynów z rozliczeniem godzinowym. Jeśli magazyn nie będzie wdrożony przez godzinę, pojemność zarezerwowana zostanie utracona dla tej godziny. Niewykorzystane wartości nie są przenoszone na następne okresy.

Po zakupie rezerwacja jest dopasowywana do użycia usługi Azure Synapse Analytics emitowanego przez działające magazyny w dowolnym momencie. Jeśli wyłączysz niektóre magazyny, rabaty na rezerwacje będą automatycznie stosowane do pozostałych zgodnych magazynów.

W przypadku magazynów, które pozostają wyłączone przez pełną godzinę, rezerwacja jest automatycznie stosowana do innych zgodnych wystąpień podczas tej godziny.

## <a name="discount-examples"></a>Przykłady rabatów

W poniższych przykładach pokazano, jak jest stosowany rabat za pojemność zarezerwowaną usługi Azure Synapse Analytics w zależności od wdrożeń.

- **Przykład 1**: Zakupiono 5 jednostek po 100 obliczeniowych jednostek magazynu danych (cDWU) pojemności zarezerwowanej. Uruchomiono na jedną godzinę wystąpienie usługi Azure Synapse Analytics DW1500c. W takim przypadku użycie jest emitowane dla 15 jednostek po 100 jednostek cDWU użycia każda. Rabat związany z rezerwacją dotyczy 5 wykorzystywanych jednostek. Opłata jest naliczana według stawek płatności zgodnie z rzeczywistym użyciem dla pozostałych 10 jednostek po 100 jednostek cDWU użycia każda. Innymi słowy, w przypadku wielu rezerwacji jest możliwe częściowe pokrycie.

- **Przykład 2**: Zakupiono 5 jednostek po 100 obliczeniowych jednostek magazynu danych (cDWU) pojemności zarezerwowanej. Uruchomiono na jedną godzinę dwa wystąpienia usługi Azure Synapse Analytics DW100c. W takim przypadku dwa zdarzenia użycia są emitowane dla 1 jednostki obejmującej 100 jednostek cDWU użycia. Oba zdarzenia użycia mają rabaty dotyczące pojemności zarezerwowanej. Pozostałe 3 jednostki po 100 jednostek cDWU pojemności zarezerwowanej każda są tracone i nie są przenoszone do użytku w przyszłości. Innymi słowy, pojedyncze rezerwacje można dopasować do wielu wystąpień usługi Azure Synapse Analytics.

- **Przykład 3**: Zakupiono 1 jednostkę obejmującą 100 obliczeniowych jednostek magazynu danych (cDWU) pojemności zarezerwowanej. Uruchomiono dwa wystąpienia usługi Azure Synapse Analytics DW100c. Każdy przebieg trwał 30 minut. W takim przypadku oba zdarzenia użycia mają rabaty dotyczące pojemności zarezerwowanej. Nie jest naliczana opłata za użycie według stawek płatności zgodnie z rzeczywistym użyciem.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

- Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
- [Wyświetlanie transakcji rezerwacji](view-reservations.md)
- [Pobieranie transakcji rezerwacji i użycia za pośrednictwem interfejsu API](reservation-apis.md)
- [Zarządzanie rezerwacjami](manage-reserved-vm-instance.md)
