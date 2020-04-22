---
title: Przedpłata za plany oprogramowania — Rezerwacje platformy Azure
description: Dowiedz się, jak możesz płacić z góry za plany oprogramowania, aby zaoszczędzić pieniądze na kosztach płatności zgodnie z rzeczywistym ratą.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: banders
ms.openlocfilehash: a527b75d376f05ab6190187b7a03d6da775055ab
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759289"
---
# <a name="prepay-for-azure-software-plans"></a>Opłacanie planów oprogramowania platformy Azure z góry

Gdy płacisz z góry za użycie oprogramowania SUSE i RedHat na platformie Azure, możesz zaoszczędzić pieniądze na kosztach płatności zgodnie z rzeczywistym użyciem. Rabaty dotyczą tylko mierników SUSE i RedHat, a nie użycia maszyny wirtualnej. Rezerwacji maszyn wirtualnych można kupić osobno, aby uzyskać dodatkowe oszczędności.

Plany oprogramowania SUSE i RedHat można kupić w witrynie Azure portal. Aby kupić plan:

- Musisz mieć rolę właściciela dla co najmniej jednej subskrypcji Enterprise lub indywidualnej z ceną płatności zgodnie z rzeczywistym użyciem.
- W przypadku subskrypcji Enterprise w witrynie [EA portal](https://ea.azure.com/) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Jeśli ustawienie jest wyłączone, musisz być administratorem EA dla subskrypcji.
- W przypadku programu Dostawcy rozwiązań w chmurze (CSP) agenci administracyjni lub agenci sprzedaży mogą kupować plany oprogramowania.

## <a name="buy-a-software-plan"></a>Kup plan oprogramowania

1. Zaloguj się do witryny Azure portal i przejdź do [rezerwacji](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Kliknij **pozycję Dodaj,** a następnie wybierz plan oprogramowania, który chcesz kupić.
Wypełnij wymagane pola. Każda maszyna wirtualna SUSE Linux lub Maszyna wirtualna RedHat, która pasuje do atrybutów tego, co kupujesz, otrzymuje rabat. Rzeczywista liczba wdrożeń, które otrzymują rabat zależy od wybranego zakresu i ilości.
3. Wybierz subskrypcję. Jest używany do płacenia za plan.
W przypadku rezerwacji pobierana jest opłata za opłatę z góry za rezerwację. Typ subskrypcji musi być umową Enterprise Agreement (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) lub indywidualną umową z cenami płatności zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P).
    - W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka.
    - W przypadku pojedynczej subskrypcji z ceną płatności zgodnie z rzeczywistym użyciem opłaty są naliczane przy płatnościach karty kredytowej lub faktury w ramach subskrypcji.
4. Wybierz zakres. Zakres może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony).
    - Pojedyncza subskrypcja — rabat planu jest stosowany do pasującego użycia w subskrypcji.
    - Udostępnione — rabat planu jest stosowany do pasujących wystąpień w dowolnej subskrypcji w kontekście rozliczeń. Dla klientów korporacyjnych kontekst rozliczeń jest rejestracją i obejmuje wszystkie subskrypcje w rejestracji. W przypadku planu indywidualnego z klientami cenowymi płatności zgodnie z rzeczywistym użyciem kontekst rozliczeń to wszystkie indywidualne plany z subskrypcjami cenowymi płatności zgodnie z rzeczywistym użyciem utworzonymi przez administratora konta.
    - Pojedyncza grupa zasobów — stosuje rabat rezerwacji do pasujących zasobów tylko w wybranej grupie zasobów.
5. Wybierz produkt, aby wybrać rozmiar maszyny Wirtualnej i typ obrazu. Rabat dotyczy tylko wybranego rozmiaru maszyny Wirtualnej.
6. Wybierz okres jednego roku lub trzech lat.
7. Wybierz ilość, która jest liczbą przedpłaconych wystąpień maszyn wirtualnych, które mogą uzyskać rabat rozliczeniowy.
8. Dodaj produkt do koszyka, przejrzyj i kup.

Zniżka na rezerwację jest automatycznie stosowana do licznika oprogramowania, za który płacisz z góry. Opłaty obliczeniowe maszyny Wirtualnej nie są objęte planem. Rezerwacje maszyn wirtualnych można kupić oddzielnie.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Zniżka dotyczy różnych rozmiarów maszyn wirtualnych SUSE

Podobnie jak zarezerwowane wystąpienia maszyn wirtualnych, plany SUSE Linux oferują elastyczność rozmiaru wystąpienia. Rabat ma zastosowanie nawet wtedy, gdy wdrażasz maszynę wirtualną o innym rozmiarze niż zakupiony plan SUSE. Aby uzyskać więcej informacji, zobacz [Opis sposobu stosowania rabatu planu oprogramowania](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>Rabat planu RedHat

Plany są dostępne tylko dla maszyn wirtualnych Red Hat Enterprise Linux. Rabat nie ma zastosowania do maszyn wirtualnych RedHat Enterprise SAP HANA lub RedHat Enterprise Linux SAP Business Apps.

Rabaty planu RedHat dotyczą tylko rozmiaru maszyny Wirtualnej wybranego w momencie zakupu. Planów RHEL nie można zwrócić ani wymienić po zakupie.


## <a name="cancellation-and-exchanges-not-allowed"></a>Anulowanie i wymiana nie jest dozwolona

Nie można anulować ani wymienić zakupionego planu SUSE lub RedHat. Sprawdź użycie, aby upewnić się, że zakupiony plan jest właściwy. Aby uzyskać pomoc w określeniu, co kupić, zobacz [Opis sposobu stosowania rabatu planu oprogramowania](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zarządzać rezerwacją, zobacz [Zarządzanie rezerwacjami platformy Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Aby dowiedzieć się więcej, zobacz następujące artykuły:

- [Co to jest Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Zarządzanie rezerwacjami na platformie Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Dowiedz się, jak stosowany jest rabat rezerwacjowy SUSE](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
