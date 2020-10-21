---
title: Zniżki w ramach planu rezerwacji Red Hat — Azure
description: Dowiedz się, jak są stosowane rabaty za plany oprogramowania Red Hat na maszynach wirtualnych.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: 311ce7edc617b02e62f4e4fefc7393318f99a1a9
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148272"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Dowiedz się, jak na platformie Azure jest stosowany rabat za plany rezerwacji oprogramowania Red Hat Linux Enterprise

Po zakupie planu oprogramowania Red Hat Linux rabat jest automatycznie stosowany do wdrożonych maszyn wirtualnych z oprogramowaniem Red Hat pasujących do rezerwacji. Plan oprogramowania Red Hat Linux pokrywa koszt korzystania z oprogramowania Red Hat na maszynie wirtualnej platformy Azure.

Aby kupić właściwy plan oprogramowania Red Hat Linux, musisz wiedzieć, jakie maszyny wirtualne z oprogramowaniem Red Hat masz uruchomione i ile procesorów wirtualnych mają te maszyny wirtualne. Informacje w poniższych sekcjach pomogą Ci określić, jaki plan należy kupić, na podstawie pliku CSV z danymi o użyciu.

## <a name="discount-applies-to-different-vm-sizes"></a>Rabat ma zastosowanie do różnych rozmiarów maszyn wirtualnych

Podobnie jak wystąpienia zarezerwowane maszyn wirtualnych zakupione plany oprogramowania Red Hat oferują elastyczne rozmiary wystąpień. To oznacza, że rabat zostanie zastosowany nawet wtedy, gdy wdrożysz maszynę wirtualną z inną liczbą procesorów wirtualnych. Rabat jest stosowany do maszyn wirtualnych o różnych rozmiarach w ramach planu oprogramowania.

Kwota rabatu zależy od współczynnika określonego w poniższych tabelach. Ten współczynnik porównuje względne rozmiary dla każdego rozmiaru maszyny wirtualnej w tej grupie. Współczynnik zależy od liczby procesorów wirtualnych maszyny wirtualnej. Używając wartości współczynnika, możesz obliczyć, ile wystąpień maszyn wirtualnych może otrzymać rabat za plan oprogramowania Red Hat Linux.

Na przykład w przypadku zakupu planu oprogramowania Red Hat Linux Enterprise Server dla maszyny wirtualnej z nie więcej niż czterema procesorami wirtualnymi współczynnik dla tej rezerwacji wynosi 1. Rabat obejmuje koszt oprogramowania Red Hat dla:

- 1 wdrożone maszyny wirtualne z 1 do 4 procesorów wirtualnych,
- lub 0,46 (około 46%) kosztów oprogramowania Red Hat Enterprise Linux dla maszyny wirtualnej z 5 lub większą liczbą procesorów wirtualnych.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Nazwy na platformie Marketplace i w witrynie Azure Portal:
- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 8.2

[Sprawdzanie mierników oprogramowania Red Hat Enterprise Linux, których dotyczy plan](https://isfratio.blob.core.windows.net/isfratio/RHELRatios.csv)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rezerwacji, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure](save-compute-costs-reservations.md)
- [Opłacanie planów oprogramowania Red Hat z góry dzięki rezerwacjom platformy Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zarządzanie rezerwacjami platformy Azure](manage-reserved-vm-instance.md)
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).