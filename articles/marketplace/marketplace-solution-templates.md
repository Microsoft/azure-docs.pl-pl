---
title: Przewodnik po publikacji szablonów rozwiązań platformy Azure | Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania szablonu rozwiązania w portalu Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: faff12adbf6c0f88f3161e1e4f2760da3b7966ca
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687509"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Usługi Azure Applications: Przewodnik publikowania oferty szablonów rozwiązań

Szablony rozwiązań są jednym z głównych sposobów publikowania rozwiązania w portalu Marketplace. Skorzystaj z tego przewodnika, aby zrozumieć wymagania dotyczące tej oferty. 

Użyj aplikacji platformy Azure: typ oferty szablonu rozwiązania, gdy rozwiązanie wymaga dodatkowego wdrożenia i automatyzacji konfiguracji poza jedną maszyną wirtualną. Można zautomatyzować inicjowanie obsługi administracyjnej co najmniej jednej maszyny wirtualnej przy użyciu aplikacji platformy Azure: szablony rozwiązań. Można również aprowizować zasoby sieciowe i magazynowe. Aplikacje platformy Azure: szablony rozwiązań oferują typ zapewnia korzyści automatyzacji dla pojedynczych maszyn wirtualnych i całych rozwiązań opartych na IaaS.

Te szablony rozwiązań nie są ofertami transakcji, ale mogą służyć do wdrażania płatnych ofert maszyn wirtualnych rozliczanych za pośrednictwem komercyjnego portalu Microsoft. Wywołanie działania, które użytkownik widzi jest "Get It Now".


## <a name="requirements-for-solution-templates"></a>Wymagania dotyczące szablonów rozwiązań

| **Wymagania** | **Szczegóły**  |
| ---------------  | -----------  |
|Rozliczenia i pomiary    |  Zasoby zostaną aprowied w subskrypcji platformy Azure klienta. Maszyny wirtualne pay-as-you-go (PAYGO) będą transagowane z klientem za pośrednictwem firmy Microsoft, rozliczane za pośrednictwem subskrypcji platformy Azure klienta (PAYGO).  <br/> W przypadku licencji "bring-your-own-license" (BYOL), podczas gdy firma Microsoft rozlicza koszty infrastruktury poniesione w związku z subskrypcją klienta, użytkownik bezpośrednio dokonuje płatności za licencjonowanie oprogramowania na rzecz klienta.   |
|Wirtualny dysk twardy zgodny z platformą Azure (VHD)  |   Maszyny wirtualne muszą być zbudowane w systemie Windows lub Linux.  Aby uzyskać więcej informacji, zobacz [tworzenie oferty aplikacji platformy Azure](./partner-center-portal/create-new-azure-apps-offer.md). |
| Atrybucja użycia klienta | Włączenie atrybucji użycia klienta jest wymagane we wszystkich szablonach rozwiązań opublikowanych w portalu Azure Marketplace. Aby uzyskać więcej informacji na temat atrybucji użycia klienta i sposobu jej włączania, zobacz [Atrybucja użycia klienta partnera platformy Azure](./azure-partner-customer-usage-attribution.md).  |
| Korzystanie z funkcji Dyski zarządzane | [Dyski zarządzane](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) to domyślna opcja dla utrwalonych dysków maszyn wirtualnych IaaS na platformie Azure. Dysków zarządzanych należy używać w szablonach rozwiązań. <br> <br> 1. Postępuj zgodnie [ze wskazówkami](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) i [przykładami](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) dotyczącymi używania dysków zarządzanych w szablonach arm platformy Azure do aktualizowania szablonów rozwiązań. <br> <br> 2. Postępuj zgodnie z poniższymi instrukcjami, aby zaimportować bazowy dysk wirtualny dysków zarządzanych na konto Magazynu, aby opublikować dysk VHD jako obraz w portalu Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [Interfejs wiersza polecenia](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiłeś, 

- [Dowiedz się więcej](https://azuremarketplace.microsoft.com/sell) o rynku.

Aby zarejestrować się w Centrum partnerów, rozpocznij tworzenie nowej oferty lub pracę nad istniejącą:

- [Zaloguj się do Centrum partnerów,](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) aby utworzyć lub uzupełnić ofertę.
- Aby uzyskać więcej [informacji, zobacz tworzenie oferty aplikacji platformy Azure.](./partner-center-portal/create-new-azure-apps-offer.md)

