---
title: Informacje o rabatach rezerwacji dla przepływów danych Azure Data Factory | Microsoft Docs
description: Dowiedz się, w jaki sposób rabat rezerwacji jest stosowany do uruchamiania przepływów danych ADF. Rabat jest stosowany do tych przepływów danych co godzinę.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: makromer
ms.openlocfilehash: 12b640fd97f48e293320593b33ab2fdc54980c0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101716299"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>Sposób zastosowania rabatu rezerwacji do Azure Data Factory przepływów danych

Po zakupie zarezerwowanej pojemności przepływu danych ADF rabat jest automatycznie stosowany do przepływów danych przy użyciu środowiska Azure Integration Runtime zgodnego z typem obliczeń i podstawową liczbą rezerwacji.

## <a name="how-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację

Rabat na rezerwację jest dostępny na zasadzie „*wykorzystaj lub strać*”. Jeśli więc nie masz pasujących zasobów integracji platformy Azure używanych przez żadną godzinę, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.

Po zatrzymaniu korzystania z środowiska Integration Runtime na potrzeby przepływów danych rabat rezerwacji automatycznie stosuje się do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną *utracone*.

## <a name="discount-applied-to-adf-data-flows"></a>Rabat dotyczący przepływów danych ADF

Rabat zarezerwowany dla przepływu danych w ramach modułu ADF jest stosowany do wykonywania w godzinach wykonywania Integration Runtime. Rezerwacja, którą kupujesz, jest zgodna z typem obliczeń używanym przez środowisko Integration Runtime dla przepływów danych. W przypadku przepływów danych, które nie mają pełnej godziny, rezerwacja jest automatycznie stosowana do innych przepływów danych pasujących do atrybutów rezerwacji. Rabat może również dotyczyć przepływów danych, które są uruchamiane współbieżnie. Jeśli nie masz przepływów danych, które są uruchamiane przez pełną godzinę, która jest zgodna z atrybutami rezerwacji, nie będziesz w pełni korzystać z rabatu rezerwacji dla tej godziny.

W poniższych przykładach pokazano, w jaki sposób rabat zdolności produkcyjnych przepływu danych ADF jest stosowany w zależności od liczby zakupionych rdzeni i czasu ich działania.

- Scenariusz 1: zakup rezerwacji przepływu danych ADF dla 1 godziny 80 rdzeni obliczeń zoptymalizowanych pod kątem pamięci, wprowadzając 80 jako ilość dla typu obliczenia zoptymalizowanego pod kątem pamięci. Przepływ danych jest uruchamiany z zestawem Azure Integration Runtime ustawionym na 144 rdzenie pamięci zoptymalizowanej przez jedną godzinę. Opłata jest naliczana według ceny zgodnie z rzeczywistym użyciem dla 64 rdzeni przepływu danych przez jedną godzinę. Rabat rezerwacji jest naliczany przez jedną godzinę z 80 rdzeni zoptymalizowanych pod kątem pamięci.
- Scenariusz 2: zakup rezerwacji przepływu danych ADF dla 1 godziny z 32 rdzeni obliczeń ogólnego przeznaczenia przez wprowadzenie 32 jako ilości dla typu obliczeń ogólnego przeznaczenia. Można debugować przepływy danych przez 1 godzinę przy użyciu 32 rdzeni obliczeń ogólnego środowiska Azure Integration Runtime. Rabat związany z rezerwacją jest naliczany w całej godzinie użycia.

Aby poznać zastosowanie swoich rezerwacji platformy Azure w raportach rozliczeń użycia i przejrzeć je, zobacz [Omówienie użycia rezerwacji platformy Azure](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Reservations, zobacz następujący artykuł:

- [Co to są rezerwacje platformy Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)