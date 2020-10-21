---
title: Informacje dotyczące rabatu na wystąpienia zarezerwowane usługi Azure Dedicated Host
description: Dowiedz się, jak rabat na wystąpienie zarezerwowane maszyny wirtualnej platformy Azure jest stosowany względem hostów usługi Azure Dedicated Host.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 8d273aae3588a006f7b0a55d2798b7a43c040c9b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148381"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-azure-dedicated-hosts"></a>Jak rabat na rezerwację platformy Azure jest stosowany względem hostów usługi Azure Dedicated Host

Po zakupie wystąpienia zarezerwowanego usługi Azure Dedicated Host rabat na rezerwację jest automatycznie stosowany do dedykowanych hostów pasujących do atrybutów i ilości rezerwacji. Rezerwacja obejmuje koszty zasobów obliczeniowych dedykowanych hostów.

## <a name="how-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację

Rabat na rezerwację jest dostępny na zasadzie „*wykorzystaj lub strać*”. Zatem jeśli w ciągu jakiejś godziny nie będziesz mieć pasujących zasobów, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.

Po usunięciu dedykowanego hosta rabat za rezerwację jest automatycznie stosowany do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną *utracone*.

## <a name="reservation-discount-for-dedicated-hosts"></a>Rabat na rezerwację dla hostów usługi Dedicated Host

Wystąpienie zarezerwowane usługi Azure Dedicated Host zapewnia rabat na koszt infrastruktury obliczeniowej używanej z dedykowanymi hostami. Rabat ma zastosowanie do dedykowanych hostów bez względu na to, czy są one używane przez maszyny wirtualne, czy nie. Rezerwacja nie obejmuje dodatkowych kosztów, takich jak licencjonowanie, użycie sieci lub wykorzystanie magazynu przez maszynę wirtualną wdrożoną na dedykowanym hoście.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy,  [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](./save-compute-costs-reservations.md)

- [Using Azure Dedicated Hosts (Używanie hostów usługi Azure Dedicated Host)](../../virtual-machines/dedicated-hosts.md)

- [Dedicated Hosts Pricing (Ceny hostów usługi Azure Dedicated Host)](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Zarządzanie rezerwacjami platformy Azure](./manage-reserved-vm-instance.md)

- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](./understand-reserved-instance-usage.md)

- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](./understand-reserved-instance-usage-ea.md)

- [Understand reservation usage for CSP subscriptions (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji CSP)](/partner-center/azure-reservations)