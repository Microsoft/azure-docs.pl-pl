---
title: Narzędzia społeczności — przenoszenie klasycznych zasobów do Azure Resource Manager
description: W tym artykule opisano narzędzia dostarczone przez społeczność w celu ułatwienia migracji zasobów IaaS z klasycznej do modelu wdrażania Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: f165acb72fdf881a0828c38db577be1f8741384e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674748"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Narzędzia społeczności do migrowania zasobów IaaS z klasycznego do Azure Resource Manager

> [!IMPORTANT]
> Dzisiaj około 90% maszyn wirtualnych IaaS korzysta z [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Od 28 lutego 2020, klasyczne maszyny wirtualne są przestarzałe i zostaną w pełni wycofane z 1 marca 2023. [Dowiedz się więcej]( https://aka.ms/classicvmretirement) o tym zaniechaniu i [sposobach jego działania](classic-vm-deprecation.md#how-does-this-affect-me).

W tym artykule opisano narzędzia dostarczone przez społeczność, które ułatwiają migrację zasobów IaaS z klasycznej do modelu wdrażania Azure Resource Manager.

> [!NOTE]
> Te narzędzia nie są oficjalnie obsługiwane przez pomoc techniczna firmy Microsoft. W związku z tym są one otwarte w serwisie GitHub, a firma Microsoft chętnie zaakceptuje żądań ściągnięcia w zakresie poprawek lub dodatkowych scenariuszy. Aby zgłosić problem, użyj funkcji problemy w witrynie GitHub.
> 
> Migrowanie za pomocą tych narzędzi spowoduje przestoje dla klasycznej maszyny wirtualnej. Jeśli szukasz migracji obsługiwanej przez platformę, odwiedź stronę 
> 
>   * [Migracja IaaS zasobów z platformy klasycznej do Azure Resource Manager stosu](migration-classic-resource-manager-overview.md)
>   * [Szczegółowa szczegółowe na platformie obsługiwana migracja z klasycznej do Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migrowanie zasobów IaaS z klasycznego do Azure Resource Manager przy użyciu Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Jest to kolekcja narzędzi pomocniczych utworzonych w ramach migracji przedsiębiorstwa z usługi Azure Service Management do Azure Resource Manager. To narzędzie pozwala replikować infrastrukturę do innej subskrypcji, która może służyć do testowania migracji i wypróbować wszelkie problemy przed uruchomieniem migracji w ramach subskrypcji produkcyjnej.

[Link do dokumentacji narzędziowej](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz to dodatkowa opcja migracji kompletnego zestawu klasycznych zasobów IaaS do Azure Resource Manager zasobów IaaS. Migracja może odbywać się w ramach tej samej subskrypcji lub między różnymi subskrypcjami i typami subskrypcji (np. subskrypcjami CSP).

[Link do dokumentacji narzędziowej](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Następne kroki

* [Przegląd obsługiwanej przez platformę migracji zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Migrowanie zasobów IaaS z klasycznego do Azure Resource Manager za pomocą programu PowerShell](migration-classic-resource-manager-ps.md)
* [Użyj interfejsu wiersza polecenia do migrowania zasobów IaaS z klasycznego do Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md)
* [Zapoznaj się z najczęściej zadawanymi pytaniami dotyczącymi migrowania zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-faq.md)
