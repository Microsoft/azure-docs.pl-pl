---
title: Omówienie dedykowanych hostów platformy Azure dla maszyn wirtualnych
description: Dowiedz się więcej o tym, jak hosty dedykowane platformy Azure mogą być używane do wdrażania maszyn wirtualnych.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 9709408f006cbcc5786a4e5a854abcd20affd693
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082836"
---
# <a name="azure-dedicated-hosts"></a>Dedykowane hosty platformy Azure

Azure Dedicated Host to usługa, która zapewnia serwery fizyczne — mogą obsługiwać jedną lub więcej maszyn wirtualnych — dedykowane jednej subskrypcji platformy Azure. Dedykowane hosty to te same serwery fizyczne używane w naszych centrach danych, dostarczane jako zasób. Można aprowizować dedykowane hosty w obrębie regionu, strefy dostępności i domeny błędów. Następnie można umieścić maszyny wirtualne bezpośrednio do hostów aprowizowanych, niezależnie od konfiguracji najlepiej spełnia twoje potrzeby.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Następne kroki

- Dedykowanego hosta można wdrożyć przy użyciu [programu Azure PowerShell](dedicated-hosts-powershell.md), [portalu](dedicated-hosts-portal.md)i [interfejsu wiersza polecenia platformy Azure.](../linux/dedicated-hosts-cli.md)

- Istnieje przykładowy szablon, znaleziono [tutaj,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)który używa zarówno stref i domen błędów dla maksymalnej odporności w regionie.

- Można również zaoszczędzić na kosztach dzięki [zarezerwowane wystąpienie hostów dedykowanych platformy Azure.](../prepay-dedicated-hosts-reserved-instances.md)
