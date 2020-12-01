---
title: Podręcznik publikowania dla aplikacji platformy Azure oferty szablonów rozwiązań — Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania szablonów rozwiązań w witrynie Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 04/22/2020
ms.openlocfilehash: 475a8615e9233def091edc03fe91c82ea63cdc27
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433537"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Przewodnik publikowania dla oferty szablonów rozwiązań dla aplikacji platformy Azure

W tym artykule wyjaśniono wymagania dotyczące publikowania ofert szablonów rozwiązań, które są jednym ze sposobów publikowania ofert aplikacji platformy Azure w portalu Azure Marketplace. Typ oferty szablonu rozwiązania wymaga [szablonu Azure Resource Manager (szablon ARM)](../azure-resource-manager/templates/overview.md) , aby automatycznie wdrażać infrastrukturę rozwiązania.

Użyj typu oferty *szablonu rozwiązania* aplikacji platformy Azure w następujących warunkach:

- Twoje rozwiązanie wymaga dodatkowego wdrożenia i automatyzacji konfiguracji poza pojedynczą maszyną wirtualną (VM), taką jak połączenie maszyn wirtualnych, sieci i zasobów magazynu.
- Klienci będą zarządzać samym rozwiązaniem.

Opcja wystaw, którą klient widzi dla tego typu oferty, otrzymuje *teraz*.

## <a name="requirements-for-solution-template-offers"></a>Wymagania dotyczące ofert szablonów rozwiązań

| **Wymagania** | **Szczegóły**  |
| ---------------  | -----------  |
|Rozliczenia i pomiary    |  Oferty szablonów rozwiązań nie są ofertami transakcji, ale mogą służyć do wdrażania płatnych ofert maszyn wirtualnych, które są rozliczane za pomocą komercyjnej witryny Microsoft Marketplace. Zasoby, które wdraża szablon ARM rozwiązania, są konfigurowane w subskrypcji platformy Azure klienta. Maszyny wirtualne z płatnością zgodnie z rzeczywistym użyciem są transakcyjne dla klienta przez firmę Microsoft i rozliczane za pośrednictwem subskrypcji platformy Azure klienta.<br/> W przypadku rozliczeń związanych z licencją własną (BYOL), chociaż koszty infrastruktury firmy Microsoft, które są naliczane w ramach subskrypcji klienta, są naliczane bezpośrednio przez klienta.   |
|Wirtualny dysk twardy (VHD) zgodny z platformą Azure  |   Maszyny wirtualne muszą być wbudowane w system Windows lub Linux. Aby uzyskać więcej informacji, zobacz: <ul> <li>[Utwórz ofertę aplikacji platformy Azure](./create-new-azure-apps-offer.md) (dla dysków VHD systemu Windows).</li><li>[Dystrybucje systemu Linux zatwierdzone na platformie Azure](../virtual-machines/linux/endorsed-distros.md) (dla dysków VHD z systemem Linux).</li></ul> |
| Udział w zakresie użycia przez klienta | Włączenie przypisywania użycia klienta jest wymagane we wszystkich szablonach rozwiązań opublikowanych w witrynie Azure Marketplace. Aby uzyskać więcej informacji o przypisywaniu użycia klienta i sposobach ich włączania, zobacz temat przypisanie [użycia klienta przez partnera platformy Azure](./azure-partner-customer-usage-attribution.md).  |
| Korzystanie z dysków zarządzanych | Usługa [Managed disks](../virtual-machines/managed-disks-overview.md) jest opcją domyślną dla utrwalonych dysków maszyn wirtualnych infrastruktury jako usługi (IaaS) na platformie Azure. W szablonach rozwiązań należy używać dysków zarządzanych. <ul><li>Aby zaktualizować szablony rozwiązań, postępuj zgodnie ze wskazówkami w temacie [używanie dysków zarządzanych w Azure Resource Manager szablonach](../virtual-machines/using-managed-disks-template-deployments.md)i Użyj dostarczonych [przykładów](https://github.com/Azure/azure-quickstart-templates).<br><br> </li><li>Aby opublikować dysk VHD jako obraz w portalu Azure Marketplace, zaimportuj podstawowy dysk VHD z dysków zarządzanych do konta magazynu przy użyciu jednej z następujących metod:<ul><li>[Azure PowerShell](../virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md) </li> <li> [Interfejs wiersza polecenia platformy Azure](../virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd.md) </li> </ul></ul> |

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, Dowiedz się, jak [rozwijać swoją firmę w chmurze za pomocą witryny Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Aby zarejestrować się i rozpocząć pracę w centrum partnerskim:

- [Zaloguj się do Centrum partnerskiego](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , aby utworzyć lub zakończyć swoją ofertę.
- Aby uzyskać więcej informacji, zobacz [Tworzenie oferty aplikacji platformy Azure](./create-new-azure-apps-offer.md) .