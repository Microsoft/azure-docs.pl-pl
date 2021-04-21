---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 37571a82ca73342b8edfc4702686ccd9091887c4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771512"
---
Szyfrowanie można wyłączyć przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub Resource Manager szablonu. 

>[!IMPORTANT]
>Wyłączanie szyfrowania za pomocą Azure Disk Encryption na komputerach wirtualnych z systemem Linux jest obsługiwane tylko w przypadku woluminów danych. Nie jest obsługiwana na woluminach danych lub systemu operacyjnego, jeśli wolumin systemu operacyjnego został zaszyfrowany.  

- **Wyłącz szyfrowanie dysków za pomocą Azure PowerShell:** Aby wyłączyć szyfrowanie, użyj polecenia cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType DATA]
     ```

- **Wyłącz szyfrowanie za pomocą interfejsu wiersza polecenia platformy Azure:** Aby wyłączyć szyfrowanie, użyj [polecenia az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```
- **Wyłącz szyfrowanie za pomocą Resource Manager szablonu:** Użyj szablonu [Wyłącz szyfrowanie na uruchomionej maszynie wirtualnej](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) z systemem Linux, aby wyłączyć szyfrowanie.
     1. Kliknij pozycję **Wdrażanie na platformie Azure**.
     2. Wybierz subskrypcję, grupę zasobów, lokalizację, maszynę wirtualną, postanowienia prawne i umowę.
