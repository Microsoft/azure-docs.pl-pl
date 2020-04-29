---
title: Przewodnik publikowania szablonów rozwiązań aplikacji platformy Azure | Portal Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania szablonu rozwiązania w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084859"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Aplikacje platformy Azure: szablon rozwiązania oferuje wymagania dotyczące publikowania

W tym artykule wyjaśniono wymagania dotyczące typu oferty szablonu rozwiązania, który jest jednym ze sposobów opublikowania oferty aplikacji platformy Azure w portalu Azure Marketplace. Typ oferty szablonu rozwiązania wymaga [szablonu Azure Resource Manager (szablon ARM)](../azure-resource-manager/templates/overview.md) , aby automatycznie wdrażać infrastrukturę rozwiązania.

Użyj typu oferty szablonu rozwiązania aplikacji platformy Azure, jeśli są wymagane następujące warunki:

- Twoje rozwiązanie wymaga dodatkowego wdrożenia i automatyzacji konfiguracji poza jedną maszyną wirtualną, na przykład z użyciem kombinacji maszyn wirtualnych, sieci i zasobów magazynu.
- Klient będzie zarządzać samym rozwiązaniem.

Wywołanie akcji, którą użytkownik widzi dla tego typu oferty, to "Pobierz teraz".

## <a name="requirements-for-solution-template-offers"></a>Wymagania dotyczące ofert szablonów rozwiązań

| **Wymagania** | **Szczegóły**  |
| ---------------  | -----------  |
|Rozliczenia i pomiary    |  Oferty szablonów rozwiązań nie są ofertami Transact, ale mogą być używane do wdrażania płatnych maszyn wirtualnych w rozliczeniach za pomocą komercyjnej witryny Marketplace firmy Microsoft. Zasoby, które wdraża szablon ARM rozwiązania, zostaną zainicjowane w subskrypcji platformy Azure klienta. W przypadku maszyn wirtualnych z płatnością zgodnie z rzeczywistym użyciem (PAYGO) firma Microsoft będzie rozliczana z klientem za pośrednictwem subskrypcji platformy Azure klienta.<br/> W przypadku dołączenia do własnej licencji (BYOL), podczas gdy firma Microsoft będzie rozliczać koszty infrastruktury związane z subskrypcją klienta, należy bezpośrednio wprowadzić opłaty za licencję na oprogramowanie klienta.   |
|Wirtualny dysk twardy (VHD) zgodny z platformą Azure  |   Maszyny wirtualne muszą być wbudowane w system Windows lub Linux. Aby uzyskać więcej informacji, zobacz: <ul> <li>[Tworzenie oferty aplikacji platformy Azure](./partner-center-portal/create-new-azure-apps-offer.md)(dla dysków VHD z systemem Windows)</li><li>[Dystrybucje systemu Linux zatwierdzone na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (dla dysków VHD z systemem Linux).</li></ul> |
| Przypisanie użycia klienta | Włączenie przypisywania użycia klienta jest wymagane we wszystkich szablonach rozwiązań opublikowanych w portalu Azure Marketplace. Aby uzyskać więcej informacji na temat przypisywania użycia klientów i sposobu ich włączania, zobacz temat przypisanie [użycia klienta przez partnera platformy Azure](./azure-partner-customer-usage-attribution.md).  |
| Korzystanie z funkcji Dyski zarządzane | [Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) jest opcją domyślną dla utrwalonych dysków maszyn wirtualnych IaaS na platformie Azure. W szablonach rozwiązań należy używać Managed Disks. <br> <br> 1. Postępuj zgodnie ze [wskazówkami](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) i [przykładami](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) dotyczącymi używania Managed disks w SZABLONach usługi Azure ARM w celu zaktualizowania szablonów rozwiązań. <br> <br> 2. Postępuj zgodnie z poniższymi instrukcjami, aby zaimportować źródłowy wirtualny dysk twardy Managed Disks na konto magazynu w celu opublikowania dysku VHD jako obrazu w portalu Marketplace: <br> <ul> <li> [Narzędzia](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [Interfejs wiersza polecenia](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Następne kroki

- Jeśli jeszcze tego nie zrobiono, [Dowiedz się więcej](https://azuremarketplace.microsoft.com/sell) o witrynie Azure Marketplace.
- [Zaloguj się do Centrum partnerskiego](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , aby utworzyć lub zakończyć swoją ofertę.
- [Utwórz ofertę aplikacji platformy Azure,](./partner-center-portal/create-new-azure-apps-offer.md) Aby uzyskać więcej informacji.
