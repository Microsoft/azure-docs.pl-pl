---
title: Omówienie dedykowanych hostów platformy Azure dla maszyn wirtualnych
description: Dowiedz się więcej o tym, jak można używać dedykowanych hostów platformy Azure do wdrażania maszyn wirtualnych.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 7e19f74c40bbeb83c9230e620c4488778823ef99
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86510807"
---
# <a name="azure-dedicated-hosts-for-virtual-machines"></a>Dedykowane hosty platformy Azure dla maszyn wirtualnych

Dedykowany Host platformy Azure to usługa, która zapewnia serwerom fizycznym możliwość hostowania co najmniej jednej maszyny wirtualnej — dedykowanej dla jednej subskrypcji platformy Azure. Dedykowane hosty to te same serwery fizyczne, które są używane w centrach danych, udostępniane jako zasób. Można udostępnić dedykowane hosty w obrębie regionu, strefy dostępności i domeny błędów. Następnie można umieścić maszyny wirtualne bezpośrednio na hostach, w których konfiguracja najlepiej odpowiada Twoim potrzebom.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Następne kroki

- Można wdrożyć dedykowanego hosta za pomocą [interfejsu wiersza polecenia platformy Azure](dedicated-hosts-cli.md), [portalu](dedicated-hosts-portal.md)i [programu PowerShell](../windows/dedicated-hosts-powershell.md).

- Aby uzyskać więcej informacji, zobacz Omówienie [dedykowanych hostów](dedicated-hosts.md) .

- Istnieje przykładowy szablon, który znajduje się w [tym miejscu](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), który używa stref i domen błędów w celu uzyskania maksymalnej odporności w regionie.

- Można także zaoszczędzić na kosztach przy użyciu [zarezerwowanego wystąpienia dedykowanych hostów platformy Azure](../prepay-dedicated-hosts-reserved-instances.md).
