---
title: Tworzenie i szyfrowanie zestawu skalowania maszyn wirtualnych za pomocą szablonów Azure Resource Manager
description: W tym przewodniku szybki start dowiesz się, jak za pomocą szablonów Azure Resource Manager utworzyć i zaszyfrować zestaw skalowania maszyn wirtualnych
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: c2b49a7e7e14bfbefcca64133ff23fdfabe53e7b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83198420"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Szyfruj zestawy skalowania maszyn wirtualnych za pomocą Azure Resource Manager

Zestawy skalowania maszyn wirtualnych z systemem Linux można szyfrować lub odszyfrowywać przy użyciu szablonów Azure Resource Manager.

## <a name="deploying-templates"></a>Wdrażanie szablonów

Najpierw wybierz szablon, który odpowiada Twojemu scenariuszowi.

- [Włącz szyfrowanie dysków w uruchomionym zestawie skalowania maszyn wirtualnych z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Włącz szyfrowanie dysków w uruchomionym zestawie skalowania maszyn wirtualnych z systemem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Wdrażanie zestawu skalowania maszyn wirtualnych z systemem Linux przy użyciu programu serwera przesiadkowego i Włączanie szyfrowania w ramach zestawów skalowania maszyn wirtualnych z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Wdrażanie zestawu skalowania maszyn wirtualnych z systemem Windows za pomocą serwera przesiadkowego i Włączanie szyfrowania w zestawach skalowania maszyn wirtualnych z systemem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Wyłącz szyfrowanie dysków w uruchomionym zestawie skalowania maszyn wirtualnych z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Wyłączanie szyfrowania dysków w uruchomionym zestawie skalowania maszyn wirtualnych z systemem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Następnie wykonaj poniższe czynności:

     1. Click **Deploy to Azure**.
     2. Fill in the required fields then agree to the terms and conditions.
     3. Click **Purchase** to deploy the template.

## <a name="next-steps"></a>Następne kroki

- [Azure Disk Encryption dla zestawów skalowania maszyn wirtualnych](disk-encryption-overview.md)
- [Szyfrowanie zestawów skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli.md)
- [Szyfrowanie zestawów skalowania maszyn wirtualnych przy użyciu Azure PowerShell](disk-encryption-powershell.md)
- [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md)
- [Użyj Azure Disk Encryption z sekwencjonowaniem rozszerzenia zestawu skalowania maszyn wirtualnych](disk-encryption-extension-sequencing.md)
