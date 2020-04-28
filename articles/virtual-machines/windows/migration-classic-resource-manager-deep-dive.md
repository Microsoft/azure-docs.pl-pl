---
title: Głębokie głębokie nurkowanie techniczne związane z migracją klasyczną do usługi Azure Resource Manager
description: Głębokie głębokie zagłębienie techniczne w zakresie migracji zasobów obsługiwanych przez platformę z klasycznego modelu wdrażania do usługi Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: ebd67bdf34bce1d90057ca402b4e3be243b7ec6c
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866202"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager

> [!IMPORTANT]
> Obecnie około 90% maszyn wirtualnych IaaS korzysta z [usługi Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) Od 28 lutego 2020 r. klasyczne maszyny wirtualne zostały przestarzałe i zostaną całkowicie wycofane 1 marca 2023 r. [Dowiedz się więcej]( https://aka.ms/classicvmretirement) o tym uszczukaniu i [o tym, jak wpływa na Ciebie](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Przejdźmy do głębokiego zagłębienia w migrację z klasycznego modelu wdrażania platformy Azure do modelu wdrażania usługi Azure Resource Manager. Przyjrzyjmy się zasobom na poziomie zasobów i funkcji, aby ułatwić zrozumienie, jak platforma Azure migruje zasoby między dwoma modelami wdrażania. Aby uzyskać więcej informacji, przeczytaj artykuł anonsu usługi: [Migracja zasobów IaaS obsługiwana przez platformę z klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>Następne kroki

* [Omówienie migracji zasobów usługi IaaS obsługiwanej przez platformę z klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrowanie zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager za pomocą programu PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrowanie zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager za pomocą interfejsu wiersza polecenia](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migracja klasycznej bramy sieci VPN do Menedżera zasobów](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [Migrowanie obwodów usługi ExpressRoute i skojarzonych sieci wirtualnych z klasycznego do modelu wdrażania Menedżera zasobów](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [Narzędzia społecznościowe pomagające w migracji zasobów IaaS z klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przejrzyj najczęściej zadawane pytania dotyczące migracji zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
