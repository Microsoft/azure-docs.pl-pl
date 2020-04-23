---
title: Przewodnik po szablonach rozwiązań aplikacji platformy Azure — przewodnik po publikowaniu | Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania szablonu rozwiązania w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084859"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Aplikacje platformy Azure: wymagania dotyczące publikowania szablonów rozwiązań

W tym artykule opisano wymagania dotyczące typu oferty szablonu rozwiązania, który jest jednym ze sposobów publikowania oferty aplikacji platformy Azure w portalu Azure Marketplace. Typ oferty szablonu rozwiązania wymaga [szablonu usługi Azure Resource Manager (szablon ARM)](../azure-resource-manager/templates/overview.md) do automatycznego wdrażania infrastruktury rozwiązania.

Użyj typu oferty szablonu rozwiązania aplikacji platformy Azure, gdy wymagane są następujące warunki:

- Rozwiązanie wymaga dodatkowego wdrożenia i automatyzacji konfiguracji poza jedną maszyną wirtualną, taką jak kombinacja maszyn wirtualnych, sieci i zasobów magazynu.
- Twój klient sam będzie zarządzał rozwiązaniem.

Wywołanie akcji, które użytkownik widzi dla tego typu oferty jest "Pobierz teraz."

## <a name="requirements-for-solution-template-offers"></a>Wymagania dotyczące ofert szablonów rozwiązań

| **Wymagania** | **Szczegóły**  |
| ---------------  | -----------  |
|Rozliczenia i pomiary    |  Oferty szablonów rozwiązań nie są ofertami transakcji, ale mogą być używane do wdrażania płatnych ofert maszyn wirtualnych rozliczanych za pośrednictwem komercyjnego portalu Microsoft Marketplace. Zasoby wdrażane przez szablon ARM rozwiązania zostaną zainicjowane w ramach subskrypcji platformy Azure klienta. Maszyny wirtualne pay-as-you-go (PAYGO) będą transagowane z klientem za pośrednictwem firmy Microsoft, rozliczane za pośrednictwem subskrypcji platformy Azure klienta.<br/> W przypadku licencji "bring-your-own-license" (BYOL), podczas gdy firma Microsoft rozlicza koszty infrastruktury poniesione w związku z subskrypcją klienta, użytkownik bezpośrednio dokonuje płatności za licencjonowanie oprogramowania na rzecz klienta.   |
|Wirtualny dysk twardy zgodny z platformą Azure (VHD)  |   Maszyny wirtualne muszą być zbudowane w systemie Windows lub Linux. Aby uzyskać więcej informacji, zobacz: <ul> <li>[Tworzenie oferty aplikacji platformy Azure](./partner-center-portal/create-new-azure-apps-offer.md)(dla vhds systemu Windows)</li><li>[Dystrybucje linuksa zatwierdzone na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (dla vhds Linux).</li></ul> |
| Atrybucja użycia klienta | Włączenie atrybucji użycia klienta jest wymagane we wszystkich szablonach rozwiązań opublikowanych w portalu Azure Marketplace. Aby uzyskać więcej informacji na temat atrybucji użycia klienta i sposobu jej włączania, zobacz [Atrybucja użycia klienta partnera platformy Azure](./azure-partner-customer-usage-attribution.md).  |
| Korzystanie z funkcji Dyski zarządzane | [Dyski zarządzane](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) to domyślna opcja dla utrwalonych dysków maszyn wirtualnych IaaS na platformie Azure. Dysków zarządzanych należy używać w szablonach rozwiązań. <br> <br> 1. Postępuj zgodnie [ze wskazówkami](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) i [przykładami](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) dotyczącymi używania dysków zarządzanych w szablonach arm platformy Azure do aktualizowania szablonów rozwiązań. <br> <br> 2. Postępuj zgodnie z poniższymi instrukcjami, aby zaimportować bazowy dysk wirtualny dysków zarządzanych na konto magazynu, aby opublikować dysk VHD jako obraz w portalu Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [Interfejs wiersza polecenia](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Następne kroki

- Jeśli jeszcze tego nie zrobiono, [dowiedz się więcej](https://azuremarketplace.microsoft.com/sell) o portalu Azure Marketplace.
- [Zaloguj się do Centrum partnerów,](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) aby utworzyć lub uzupełnić ofertę.
- [Utwórz ofertę aplikacji platformy Azure, aby](./partner-center-portal/create-new-azure-apps-offer.md) uzyskać więcej informacji.
