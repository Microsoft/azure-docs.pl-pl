---
title: Włącz kompleksowe szyfrowanie przy użyciu szyfrowania na dyskach zarządzanych hosta Azure Portal
description: Użyj szyfrowania na hoście, aby umożliwić kompleksowe szyfrowanie na dyskach zarządzanych platformy Azure — Azure Portal.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/23/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e969fe34e7e7723218586f24807ad70944ba5716
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171368"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-portal"></a>Włącz kompleksowe szyfrowanie przy użyciu szyfrowania na hoście-Azure Portal

Po włączeniu szyfrowania na hoście dane przechowywane na hoście maszyny wirtualnej są szyfrowane w stanie spoczynku i są zaszyfrowane w usłudze Storage. Aby uzyskać informacje koncepcyjne na temat szyfrowania na hoście, a także inne typy szyfrowania dysków zarządzanych, zobacz [szyfrowanie na hoście-end-to-end dla danych maszyny wirtualnej](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

[!INCLUDE [virtual-machines-disks-encryption-at-host-portal](../../../includes/virtual-machines-disks-encryption-at-host-portal.md)]

## <a name="next-steps"></a>Następne kroki

[Przykłady szablonów usługi Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)