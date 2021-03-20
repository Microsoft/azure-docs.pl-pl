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
ms.openlocfilehash: 1e3cdfccc36549d2aa127636b15b698a4e19ccf0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85610572"
---
Szyfrowanie można wyłączyć przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonu Menedżer zasobów. Wyłączenie szyfrowania dysku danych na maszynie wirtualnej z systemem Windows, gdy zaszyfrowano zarówno dysk systemu operacyjnego, jak i dysk danych, nie działa zgodnie z oczekiwaniami. Zamiast tego należy wyłączyć szyfrowanie na wszystkich dyskach.

- **Wyłącz szyfrowanie dysków przy użyciu Azure PowerShell:** Aby wyłączyć szyfrowanie, należy użyć polecenia cmdlet [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Wyłącz szyfrowanie za pomocą interfejsu wiersza polecenia platformy Azure:** Aby wyłączyć szyfrowanie, użyj polecenia [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Wyłącz szyfrowanie przy użyciu szablonu Menedżer zasobów:** 

    1. Kliknij pozycję **Wdróż na platformie Azure** , korzystając z szablonu [wyłącz szyfrowanie dysków na URUCHOMIONYM szablonie maszyny wirtualnej systemu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) .
    2. Wybierz subskrypcję, grupę zasobów, lokalizację, maszynę wirtualną, typ woluminu, warunki prawne i umowę.
    3.  Kliknij przycisk **Kup** , aby wyłączyć szyfrowanie dysków na działającej maszynie wirtualnej z systemem Windows. 
