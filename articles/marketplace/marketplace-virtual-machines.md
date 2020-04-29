---
title: Przewodnik publikowania oferty maszyny wirtualnej dla witryny Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania maszyny wirtualnej i bezpłatnej wersji próbnej oprogramowania do wdrożenia z portalu Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 2fa67d81546db86535c179a9c59d0602c1175cba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687497"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Przewodnik publikowania oferty maszyny wirtualnej

Obrazy maszyn wirtualnych to jeden z głównych sposobów publikowania rozwiązania w portalu Azure Marketplace. Skorzystaj z tego przewodnika, aby poznać wymagania dotyczące tej oferty. 

Są to oferty transakcji, które są wdrażane i rozliczane za pomocą witryny Marketplace. Wywołanie akcji, którą widzi użytkownik, to "Pobierz teraz".

## <a name="free-trial"></a>Bezpłatna wersja próbna 

W celu przetestowania oferty przez użytkowników z ograniczonymi licencjami na oprogramowanie należy skorzystać w przypadku korzystania z modelu rozliczeń własnych licencji (BYOL). 

## <a name="test-drive"></a>Wersja testowa

Wdrażasz co najmniej jedną maszynę wirtualną za pomocą aplikacji typu "infrastruktura jako usługa" (IaaS) lub "oprogramowanie jako usługa" (SaaS). Zaletą opcji publikowania wersji testowej jest automatyczne Inicjowanie obsługi maszyny wirtualnej lub całego rozwiązania z przewodnikiem obsługiwanym przez partnera. Na dysku testowym jest dostępna Ocena bez dodatkowych kosztów dla klienta. Klient nie musi być istniejącym klientem platformy Azure, aby móc współpracować z wersją próbną. 

Skontaktuj się z nami pod adresem [amp-testdrive](mailto:amp-testdrive@microsoft.com) , aby rozpocząć pracę. 

|Wymagania  |Szczegóły |
|---------|---------|
| Masz aplikację Marketplace   |    Co najmniej jedna maszyna wirtualna za poorednictwem IaaS lub SaaS.      |

## <a name="interactive-demo"></a>Pokaz interaktywny

W przypadku korzystania z rozwiązania z przewodnikiem dla klientów możesz skorzystać z interaktywnej prezentacji. Zaletą opcji publikowania interaktywnej demonstracyjnej jest udostępnienie wersji próbnej bez skomplikowanej aprowizacji złożonego rozwiązania. 

## <a name="virtual-machine-offer"></a>Oferta maszyny wirtualnej

Podczas wdrażania urządzenia wirtualnego w ramach subskrypcji skojarzonej z klientem Użyj typu oferty maszyny wirtualnej. Maszyny wirtualne są w pełni włączane, korzystając z modeli licencjonowania płatność zgodnie z rzeczywistym użyciem lub BYOL. Firma Microsoft hostuje transakcję handlową i rozlicza klienta w Twoim imieniu. Korzyści wynikające z korzystania z preferowanej relacji płatności między klientem i firmą Microsoft, w tym dowolnymi umowami Enterprise Agreement.

> [!NOTE]
> W tym momencie zobowiązania pieniężne związane z Umowa Enterprise mogą być używane w odniesieniu do użycia platformy Azure przez maszynę wirtualną, ale nie do opłat za Licencjonowanie oprogramowania.  
> 
> [!NOTE]
> Możesz ograniczyć odnajdywanie i wdrażanie maszyny wirtualnej do określonego zestawu klientów, publikując obraz i ceny jako ofertę prywatną. W ofertach prywatnych można odblokować możliwość tworzenia ofert wyłącznych dla najbliższych klientów i oferowania niestandardowych programów oraz warunków. Dostosowane warunki umożliwiają wyróżnianie różnorodnych scenariuszy, w tym w przypadku, gdy użytkownik korzysta z wyspecjalizowanych cen i warunków oraz wczesnego dostępu do ograniczonej wersji oprogramowania. Oferty prywatne umożliwiają podanie określonych cen lub produktów do ograniczonego zestawu klientów przez utworzenie nowej jednostki SKU z tymi szczegółami.  
> *   Aby uzyskać więcej informacji o ofertach prywatnych, odwiedź stronę oferty prywatne w witrynie Azure Marketplace w witrynie [Azure.Microsoft.com/blog/Private-offers-on-Azure-Marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Wymaganie | Szczegóły |  
|:--- |:--- | 
| Rozliczenia i pomiary | Maszyna wirtualna musi obsługiwać miesięczne rozliczenie BYOL lub płatność zgodnie z rzeczywistym użyciem. |  
| Wirtualny dysk twardy (VHD) zgodny z platformą Azure | Maszyny wirtualne muszą być wbudowane w system Windows lub Linux. <ul> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego z systemem Linux, zobacz [dystrybucje systemu Linux zatwierdzone na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego systemu Windows, zobacz [Tworzenie dysku VHD zgodnego z platformą Azure](./partner-center-portal/azure-vm-create-offer.md).</li> </ul> |  

>[!Note]
>Niedostępność kanału partnera dostawcy rozwiązań w chmurze (CSP) jest teraz dostępna.  Zobacz [dostawcy rozwiązań w chmurze](./cloud-solution-providers.md) , aby uzyskać więcej informacji na temat marketingu oferty przez kanały partnerskie programu Microsoft CSP.

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, 

- [Dowiedz się więcej](https://azuremarketplace.microsoft.com/sell) o portalu Marketplace.

Jeśli jesteś zarejestrowanym użytkownikiem i tworzysz nową ofertę lub pracujesz nad istniejącym,

- [Zaloguj się do Centrum partnerskiego](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , aby utworzyć lub zakończyć swoją ofertę.
- Aby uzyskać więcej informacji, zobacz temat [Tworzenie oferty maszyny wirtualnej](./partner-center-portal/azure-vm-create-offer.md) .
