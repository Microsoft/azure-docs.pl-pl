---
title: Przewodnik publikowania dla ofert maszyn wirtualnych w portalu Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania maszyny wirtualnej i bezpłatnej wersji próbnej oprogramowania do wdrożenia z portalu Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 09/04/2020
ms.openlocfilehash: cc6b040731cbeb7271d7a7c0de1c32fa2d007013
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89484192"
---
# <a name="publishing-guide-for-virtual-machine-offers"></a>Przewodnik publikowania dla ofert maszyn wirtualnych

Publikowanie obrazów maszyn wirtualnych (VM) to jeden z głównych sposobów publikowania rozwiązania w portalu Azure Marketplace. Skorzystaj z tego przewodnika, aby poznać wymagania dotyczące tego typu oferty. 

Oferty maszyn wirtualnych to oferty transakcji wdrożone i rozliczane za pomocą witryny Azure Marketplace. Opcja wystaw, którą widzi użytkownik, otrzymuje *teraz*.

## <a name="free-trial"></a>Bezpłatna wersja próbna 

Aby przygotować się do przetestowania oferty przez użytkowników, uzyskaj dostęp do ograniczonych licencji na oprogramowanie w przypadku korzystania z modelu rozliczeń BYOL. 

## <a name="test-drive"></a>Wersja testowa

Można wdrożyć co najmniej jedną maszynę wirtualną za pomocą aplikacji infrastruktury jako usługi (IaaS) lub oprogramowania jako usługi (SaaS). Zaletą opcji publikowania *dysku testowego* jest zautomatyzowana konfiguracja maszyny wirtualnej lub całego rozwiązania, które zostało obsłużone z przewodnikiem obsługiwanym przez partnera. Stacja testowa umożliwia klientom ocenę maszyn wirtualnych bez dodatkowych kosztów. Klient nie musi być istniejącym klientem platformy Azure, aby mieć możliwość korzystania z wersji próbnej. 

Aby uzyskać więcej informacji na temat dysków testowych, zobacz [co to jest dysk testowy?](what-is-test-drive.md)

|Wymagania  |Szczegóły |
|---------|---------|
| Masz aplikację Azure Marketplace   |  Co najmniej jedna maszyna wirtualna za poorednictwem IaaS lub SaaS.      |

## <a name="interactive-demo"></a>Interaktywny pokaz

Dzięki tej ofercie klienci mogą korzystać z przewodnika po rozwiązaniu z użyciem interaktywnej prezentacji. Zaletą opcji publikowania interaktywnej demonstracyjnej jest możliwość oferowania wersji próbnej bez konieczności zapewnienia skomplikowanej konfiguracji złożonego rozwiązania. 

## <a name="virtual-machine-offer"></a>Oferta maszyny wirtualnej

Typ oferty *maszyny wirtualnej* służy do wdrażania urządzenia wirtualnego w ramach subskrypcji skojarzonej z klientem. Maszyny wirtualne są w pełni obsługiwane w handlu z opcją płatność zgodnie z rzeczywistym użyciem lub BYOL. Firma Microsoft hostuje transakcję handlową i rozlicza klienta w Twoim imieniu. Korzyści wynikające z korzystania z preferowanej relacji płatności między klientem i firmą Microsoft, w tym dowolnymi umowami Enterprise Agreement.

> [!NOTE]
> W tym momencie zobowiązania pieniężne związane z Umowa Enterprise mogą być używane w odniesieniu do użycia platformy Azure przez maszynę wirtualną, ale nie do opłat za Licencjonowanie oprogramowania.  
> 
> [!NOTE]
> Możesz ograniczyć odnajdywanie i wdrażanie maszyny wirtualnej do określonego zestawu klientów, publikując obraz i ceny jako ofertę prywatną. W ofertach prywatnych można odblokować możliwość tworzenia ofert wyłącznych dla najbliższych klientów i oferowania niestandardowych programów oraz warunków. Dostosowane warunki umożliwiają wyróżnianie różnorodnych scenariuszy, w tym w przypadku, gdy użytkownik korzysta z wyspecjalizowanych cen i warunków oraz wczesnego dostępu do ograniczonej wersji oprogramowania. Oferty prywatne umożliwiają podanie określonych cen lub produktów do ograniczonego zestawu klientów przez utworzenie nowego planu z tymi szczegółami.  
>
> Aby uzyskać więcej informacji, zobacz [prywatne oferty w witrynie Azure Marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Wymaganie | Szczegóły |  
|:--- |:--- | 
| Rozliczenia i pomiary | Maszyna wirtualna musi obsługiwać miesięczne rozliczenie BYOL lub płatność zgodnie z rzeczywistym użyciem. |  
| Wirtualny dysk twardy (VHD) zgodny z platformą Azure | Maszyny wirtualne muszą być wbudowane w system Windows lub Linux. Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego, zobacz: <ul> <li>[Dystrybucje systemu Linux zatwierdzone na platformie Azure](../virtual-machines/linux/endorsed-distros.md) (dla dysków VHD z systemem Linux).</li> <li>[Utwórz dysk VHD zgodny z platformą Azure](./partner-center-portal/azure-vm-create-offer.md) (dla dysków VHD systemu Windows).</li> </ul> |  

>[!Note]
>Niedostępność kanału partnera dostawcy rozwiązań w chmurze (CSP) jest teraz dostępna. Aby uzyskać więcej informacji na temat marketingu oferty za poorednictwem kanałów partnerów CSP firmy Microsoft, zobacz [dostawcy rozwiązań w chmurze](./cloud-solution-providers.md).

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, Dowiedz się, jak [rozwijać swoją firmę w chmurze za pomocą witryny Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Aby zarejestrować się i rozpocząć pracę w centrum partnerskim:

- [Zaloguj się do Centrum partnerskiego](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , aby utworzyć lub zakończyć swoją ofertę.
- Aby uzyskać więcej informacji, zobacz temat [Tworzenie oferty maszyny wirtualnej](./partner-center-portal/azure-vm-create-offer.md) .
