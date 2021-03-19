---
title: Włącz Azure Disk Encryption dla Virtual Machine Scale Sets
description: Ten artykuł zawiera instrukcje dotyczące włączania Microsoft Azure szyfrowania dysków dla Virtual Machine Scale Sets
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 6b9805d66149a18216a200bc89a79b3e06106c9d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83195106"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Azure Disk Encryption Virtual Machine Scale Sets

Azure Disk Encryption zapewnia szyfrowanie woluminów systemu operacyjnego i dysków danych maszyn wirtualnych, co pomaga chronić dane i zabezpieczać je w celu spełnienia wymagań w zakresie zabezpieczeń i bezpieczeństwa organizacji. Aby dowiedzieć się więcej, zobacz [Azure Disk Encryption: maszyny wirtualne systemu Linux](../virtual-machines/linux/disk-encryption-overview.md) i [Azure Disk Encryption: maszyny wirtualne z systemem Windows](../virtual-machines/windows/disk-encryption-overview.md)  

Azure Disk Encryption można również zastosować do zestawów skalowania maszyn wirtualnych z systemami Windows i Linux, w następujących przypadkach:
- Zestawy skalowania utworzone przy użyciu dysków zarządzanych. Usługa Azure Disk Encryption nie jest obsługiwana w przypadku zestawów skalowania dysków natywnych (lub niezarządzanych).
- System operacyjny i woluminy danych w zestawach skalowania systemu Windows.
- Woluminy danych w zestawach skalowania systemu Linux. Szyfrowanie dysków systemu operacyjnego nie jest obsługiwane w przypadku zestawów skalowania w systemie Linux.

Podstawy Azure Disk Encryption dla zestawów skalowania maszyn wirtualnych można znaleźć w zaledwie kilka minut za pomocą [szyfrowania zestawów skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli.md) lub [szyfrowania zestawów skalowania maszyn wirtualnych przy użyciu](disk-encryption-powershell.md) samouczków Azure PowerShell.

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie zestawów skalowania maszyn wirtualnych przy użyciu Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md)
- [Użyj Azure Disk Encryption z sekwencjonowaniem rozszerzenia zestawu skalowania maszyn wirtualnych](disk-encryption-extension-sequencing.md)
