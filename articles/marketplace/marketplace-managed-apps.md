---
title: Przewodnik publikowania oferty aplikacji zarządzanych przez aplikacje platformy Azure | Portal Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania aplikacji zarządzanej w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084876"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Aplikacje platformy Azure: wymagania dotyczące publikowania ofert aplikacji zarządzanych

W tym artykule wyjaśniono wymagania dotyczące typu oferty aplikacji zarządzanej, który jest jednym ze sposobów opublikowania oferty aplikacji platformy Azure w portalu Azure Marketplace. Aplikacje zarządzane to oferty Transact wdrożone i rozliczane za pomocą witryny Azure Marketplace. Wywołanie akcji, którą widzi użytkownik, to "Pobierz teraz".

Użyj typu oferty aplikacji zarządzanej, jeśli są wymagane następujące warunki:

- Rozwiązanie oparte na subskrypcji można wdrożyć dla klienta przy użyciu maszyny wirtualnej lub całego rozwiązania opartego na IaaS.
- Ty lub Twój klient wymaga, aby rozwiązanie było zarządzane przez partnera.

>[!NOTE]
>Na przykład partner może być dostawcą usług (MSP) lub usługą zarządzaną.  

## <a name="managed-application-offer"></a>Oferta aplikacji zarządzanej

|Wymagania |Szczegóły  |
|---------|---------|
|Wdrożone w subskrypcji platformy Azure klienta | Zarządzane aplikacje muszą być wdrożone w subskrypcji klienta i mogą być zarządzane przez inną firmę. |
|Rozliczenia i pomiary    |  Zasoby będą obsługiwane w ramach subskrypcji platformy Azure klienta. Maszyny wirtualne z płatnością zgodnie z rzeczywistym użyciem (PAYGO) będą w firmie Microsoft rozliczane za pośrednictwem subskrypcji platformy Azure (PAYGO) klienta. <br> W przypadku dołączenia do własnej licencji, podczas gdy firma Microsoft będzie rozliczać koszty infrastruktury związane z subskrypcją klienta, należy bezpośrednio wprowadzić opłaty za licencję na oprogramowanie klienta.        |
|Wirtualny dysk twardy (VHD) zgodny z platformą Azure    |   Maszyny wirtualne muszą być wbudowane w system Windows lub Linux.<ul> <ul> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego z systemem Linux, zobacz [dystrybucje systemu Linux zatwierdzone na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego systemu Windows, zobacz [Tworzenie oferty aplikacji platformy Azure](./partner-center-portal/create-new-azure-apps-offer.md).</li> </ul> |

>[!NOTE]
> Zarządzane aplikacje muszą być wdrażane za pomocą portalu Marketplace. Jeśli komunikacja z klientem jest istotna, należy skontaktować się z zainteresowanymi klientami po włączeniu udostępniania potencjalnego klienta.  

>[!Note]
>Niedostępność kanału partnera dostawcy rozwiązań w chmurze (CSP) jest teraz dostępna. Aby uzyskać więcej informacji na temat marketingu oferty za pomocą kanałów partnerów CSP firmy Microsoft, zobacz [dostawcy rozwiązań w chmurze](./cloud-solution-providers.md).

## <a name="next-steps"></a>Następne kroki

- Jeśli jeszcze tego nie zrobiono, [Dowiedz się więcej](https://azuremarketplace.microsoft.com/sell) o witrynie Azure Marketplace.
- [Zaloguj się do Centrum partnerskiego](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , aby utworzyć lub zakończyć swoją ofertę.
- [Utwórz ofertę aplikacji platformy Azure,](./partner-center-portal/create-new-azure-apps-offer.md) Aby uzyskać więcej informacji.
