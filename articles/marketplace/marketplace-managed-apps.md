---
title: Przewodnik publikowania oferty zarządzanych aplikacji usługi Azure Applications
description: W tym artykule opisano wymagania dotyczące publikowania aplikacji zarządzanej w portalu Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 56281ff8faba6b4b950334e2b0018d48c8e7aeb3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687563"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Usługi Azure Applications: Przewodnik publikowania oferty zarządzanych aplikacji

Aplikacja zarządzana jest jednym z głównych sposobów publikowania rozwiązania w portalu Marketplace. Skorzystaj z tego przewodnika, aby zrozumieć wymagania dotyczące tej oferty. 

Są to oferty transakcji, które są wdrażane i rozliczane za pośrednictwem portalu Marketplace. Wywołanie działania, które użytkownik widzi jest "Get It Now".

Użyj aplikacji platformy Azure: typ oferty aplikacji zarządzanej, gdy wymagane są następujące warunki:
- Można wdrożyć rozwiązanie oparte na subskrypcji dla klienta przy użyciu maszyny Wirtualnej lub całego rozwiązania opartego na IaaS.
- Ty lub Twój klient wymagasz, aby rozwiązanie było zarządzane przez partnera.

>[!NOTE]
>Na przykład partnerem może być SI lub zarządzanego dostawcy usług (MSP).  

## <a name="managed-application-offer"></a>Oferta aplikacji zarządzanych

|Wymagania |Szczegóły  |
|---------|---------|
|Wdrożony w ramach subskrypcji platformy Azure klienta | Aplikacje zarządzane muszą być wdrażane w ramach subskrypcji klienta i mogą być zarządzane przez stronę trzecią. | 
|Rozliczenia i pomiary    |  Zasoby zostaną aprowied w subskrypcji platformy Azure klienta. Maszyny wirtualne pay-as-you-go (PAYGO) będą transagowane z klientem za pośrednictwem firmy Microsoft, rozliczane za pośrednictwem subskrypcji platformy Azure klienta (PAYGO). <br> W przypadku licencji bring-your-own, podczas gdy firma Microsoft rozlicza koszty infrastruktury poniesione w związku z subskrypcją klienta, użytkownik bezpośrednio dokonuje płatności za licencjonowanie oprogramowania na rzecz klienta.        |
|Wirtualny dysk twardy zgodny z platformą Azure (VHD)    |   Maszyny wirtualne muszą być zbudowane w systemie Windows lub Linux.<ul> <ul> <li>Aby uzyskać więcej informacji na temat tworzenia dysku VHD systemu Linux, zobacz [Dystrybucje systemu Linux zatwierdzone na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Aby uzyskać więcej informacji na temat tworzenia dysku VHD systemu Windows, zobacz [tworzenie oferty aplikacji platformy Azure](./partner-center-portal/create-new-azure-apps-offer.md).</li> </ul> |

>[!NOTE]
> Aplikacje zarządzane muszą być wdrażane za pośrednictwem portalu Marketplace. Jeśli komunikacja z klientem jest problemem, powinieneś skontaktować się z zainteresowanymi klientami po włączeniu udostępniania potencjalnych klientów.  

>[!Note]
>Usługa wyboru kanału partnerskiego dostawców rozwiązań w chmurze (CSP) jest już dostępna.  Zobacz [dostawców rozwiązań w chmurze, aby](./cloud-solution-providers.md) uzyskać więcej informacji na temat marketingu oferty za pośrednictwem kanałów partnerów CSP firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiłeś, 

- [Dowiedz się więcej](https://azuremarketplace.microsoft.com/sell) o rynku.

Aby zarejestrować się w Centrum partnerów, rozpocznij tworzenie nowej oferty lub pracę nad istniejącą:

- [Zaloguj się do Centrum partnerów,](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) aby utworzyć lub uzupełnić ofertę.
- Aby uzyskać więcej [informacji, zobacz tworzenie oferty aplikacji platformy Azure.](./partner-center-portal/create-new-azure-apps-offer.md)
