---
title: Omówienie magazynu dyskowego
description: Omówienie usługi Azure Managed disks, która obsługuje konta magazynu podczas korzystania z maszyn wirtualnych.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d3932047422571399ad0562b2f9f2d19c3e6799b
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145299"
---
# <a name="introduction-to-azure-managed-disks"></a>Wprowadzenie do dysków zarządzanych na platformie Azure

Azure Managed disks to woluminy magazynu na poziomie bloku, które są zarządzane przez platformę Azure i używane z usługą Azure Virtual Machines. Dyski zarządzane są podobne do dysku fizycznego na serwerze lokalnym, ale zwirtualizowane. W przypadku dysków zarządzanych należy określić rozmiar dysku, typ dysku i zainicjować obsługę administracyjną dysku. Po udostępnieniu dysku platforma Azure obsługuje pozostałe.

Dostępne typy dysków to Ultra disks, dyski półprzewodnikowe w warstwie Premium (SSD), standardowe dysków SSD i standardowe dyski twarde (dysk twardy). Aby uzyskać informacje o poszczególnych typach dysków, zobacz [Wybieranie typu dysku dla maszyn wirtualnych IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Wybieranie typu dysku dla maszyn wirtualnych IaaS](disks-types.md)