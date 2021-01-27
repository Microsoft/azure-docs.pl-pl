---
title: Informacje o rabatach rezerwacji dla przepływów danych Azure Data Factory | Microsoft Docs
description: Dowiedz się, w jaki sposób rabat rezerwacji jest stosowany do uruchamiania przepływów danych ADF. Rabat jest stosowany do tych przepływów danych co godzinę.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: makromer
ms.openlocfilehash: 6936b9344196f8071a6c1859869c62e5bee22924
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811712"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>Sposób zastosowania rabatu rezerwacji do Azure Data Factory przepływów danych

Po zakupie zarezerwowanej pojemności przepływu danych ADF rabat jest automatycznie stosowany do przepływów danych przy użyciu środowiska Azure Integration Runtime zgodnego z typem obliczeń i podstawową liczbą rezerwacji.

## <a name="how-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację

Rabat na rezerwację jest dostępny na zasadzie „*wykorzystaj lub strać*”. Jeśli więc nie masz pasujących zasobów integracji platformy Azure używanych przez żadną godzinę, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.

Po zatrzymaniu korzystania z środowiska Integration Runtime na potrzeby przepływów danych rabat rezerwacji automatycznie stosuje się do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną *utracone*.

## <a name="discount-applied-to-adf-data-flows"></a>Rabat dotyczący przepływów danych ADF

Rabat zarezerwowany dla przepływu danych w ramach modułu ADF jest stosowany do wykonywania w godzinach wykonywania Integration Runtime. Rezerwacja, którą kupujesz, jest dopasowywana do użycia obliczeń emitowanych przez używany środowisko Integration Runtime. W przypadku przepływów danych, które nie mają pełnej godziny, rezerwacja jest automatycznie stosowana do innych przepływów danych pasujących do atrybutów rezerwacji. Rabat może dotyczyć przepływów danych, które są uruchamiane współbieżnie. Jeśli nie masz przepływów danych, które są uruchamiane przez pełną godzinę, która jest zgodna z atrybutami rezerwacji, nie będziesz w pełni korzystać z rabatu rezerwacji dla tej godziny.

W poniższych przykładach pokazano, w jaki sposób rabat zdolności produkcyjnych przepływu danych ADF jest stosowany w zależności od liczby zakupionych rdzeni i czasu ich działania.

- Scenariusz 1: zakup zarezerwowanej pojemności przepływu danych ADF dla 80 rdzeni obliczeń zoptymalizowanych pod kątem pamięci. Przepływ danych jest uruchamiany z zestawem Azure Integration Runtime ustawionym na 144 rdzenie pamięci zoptymalizowanej przez jedną godzinę. Opłata jest naliczana według ceny zgodnie z rzeczywistym użyciem dla 64 rdzeni przepływu danych przez jedną godzinę. Rabat rezerwacji jest naliczany przez jedną godzinę z 80 rdzeni zoptymalizowanych pod kątem pamięci.
- Scenariusz 2: zakup zarezerwowanej pojemności przepływu danych ADF na 32 rdzeni obliczeń ogólnego przeznaczenia. Można debugować przepływy danych przez 1 godzinę przy użyciu 32 rdzeni obliczeń ogólnego środowiska Azure Integration Runtime. Rabat związany z rezerwacją jest naliczany w całej godzinie użycia.

Aby poznać zastosowanie swoich rezerwacji platformy Azure w raportach rozliczeń użycia i przejrzeć je, zobacz [Omówienie użycia rezerwacji platformy Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Reservations, zobacz następujący artykuł:

- [Co to są rezerwacje platformy Azure?](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)
