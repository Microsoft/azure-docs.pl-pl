---
title: Przewodnik po publikowaniu aplikacji zarządzanych przez aplikacje platformy Azure | Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania aplikacji zarządzanej w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084876"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Aplikacje platformy Azure: wymagania dotyczące publikowania ofert aplikacji zarządzanych

W tym artykule opisano wymagania dotyczące typu oferty aplikacji zarządzanej, który jest jednym ze sposobów publikowania oferty aplikacji platformy Azure w portalu Azure Marketplace. Aplikacje zarządzane są oferty transakcji, które są wdrażane i rozliczane za pośrednictwem portalu Azure Marketplace. Wywołanie działania, które użytkownik widzi jest "Get It Now".

Użyj typu oferty aplikacji zarządzanej, gdy wymagane są następujące warunki:

- Wdrażasz rozwiązanie oparte na subskrypcji dla klienta przy użyciu maszyny Wirtualnej lub całego rozwiązania opartego na IaaS.
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
>Usługa wyboru kanału partnerskiego dostawców rozwiązań w chmurze (CSP) jest już dostępna. Aby uzyskać więcej informacji na temat marketingu oferty za pośrednictwem kanałów partnerskich dostawcy usług kryptograficznych firmy Microsoft, zobacz [Dostawcy rozwiązań w chmurze](./cloud-solution-providers.md).

## <a name="next-steps"></a>Następne kroki

- Jeśli jeszcze tego nie zrobiono, [dowiedz się więcej](https://azuremarketplace.microsoft.com/sell) o portalu Azure Marketplace.
- [Zaloguj się do Centrum partnerów,](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) aby utworzyć lub uzupełnić ofertę.
- [Utwórz ofertę aplikacji platformy Azure, aby](./partner-center-portal/create-new-azure-apps-offer.md) uzyskać więcej informacji.
