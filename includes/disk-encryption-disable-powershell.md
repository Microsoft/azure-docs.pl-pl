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
ms.openlocfilehash: 82318a94a6a095016fe1177ee486f035d101589c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776795"
---
Szyfrowanie można wyłączyć przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub Resource Manager szablonu. Wyłączenie szyfrowania dysku danych na maszynie wirtualnej z systemem Windows, gdy zaszyfrowano zarówno dysk systemu operacyjnego, jak i dysk danych, nie działa zgodnie z oczekiwaniami. Zamiast tego wyłącz szyfrowanie na wszystkich dyskach.

- **Wyłącz szyfrowanie dysków za pomocą Azure PowerShell:** Aby wyłączyć szyfrowanie, użyj polecenia cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Wyłącz szyfrowanie za pomocą interfejsu wiersza polecenia platformy Azure:** Aby wyłączyć szyfrowanie, użyj [polecenia az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Wyłącz szyfrowanie za pomocą Resource Manager szablonu:** 

    1. Kliknij **pozycję Deploy to Azure (Wd** wdrażaj na platformie Azure) w szablonie Disable disk encryption on running Windows VM (Wyłącz szyfrowanie [dysków na uruchomionej maszynie wirtualnej z systemem Windows).](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
    2. Wybierz subskrypcję, grupę zasobów, lokalizację, maszynę wirtualną, typ woluminu, postanowienia prawne i umowę.
    3.  Kliknij **pozycję Kup,** aby wyłączyć szyfrowanie dysków na uruchomionej maszynie wirtualnej z systemem Windows. 
