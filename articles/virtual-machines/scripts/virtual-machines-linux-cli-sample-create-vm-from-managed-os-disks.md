---
title: Tworzenie maszyny wirtualnej przez dołączenie dysku zarządzanego jako dysku systemu operacyjnego — przykład interfejsu wiersza polecenia
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie maszyny wirtualnej przez dołączenie dysku zarządzanego jako dysk systemu operacyjnego
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 83f0ea094c26a1ff664ef27729731b77d987e7ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86501504"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-cli"></a>Tworzenie maszyny wirtualnej przy użyciu istniejącego zarządzanego dysku systemu operacyjnego za pomocą interfejsu wiersza polecenia

Ten skrypt tworzy maszynę wirtualną przez dołączenie istniejącego dysku zarządzanego jako dysku systemu operacyjnego. Użyj tego skryptu w poprzednich scenariuszach:
* Tworzenie maszyny wirtualnej na podstawie istniejącego zarządzanego dysku systemu operacyjnego, który został skopiowany z dysku zarządzanego z innej subskrypcji
* Tworzenie maszyny wirtualnej na podstawie istniejącego dysku zarządzanego utworzonego z wyspecjalizowanego pliku VHD 
* Tworzenie maszyny wirtualnej na podstawie istniejącego zarządzanego dysku systemu operacyjnego utworzonego z migawki 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-attach-existing-managed-os-disk/create-vm-attach-existing-managed-os-disk.sh "Create VM from a managed disk")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu uzyskania właściwości dysku zarządzanego, dołączenia dysku zarządzanego do nowej maszyny wirtualnej i utworzenia maszyny wirtualnej. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az disk show](/cli/azure/disk) | Pobiera właściwości dysku zarządzanego przy użyciu nazwy dysku i nazwy grupy zasobów. W celu dołączenia dysku zarządzanego do nowej maszyny wirtualnej jest używana właściwość Id |
| [az vm create](/cli/azure/vm) | Tworzy maszynę wirtualną przy użyciu zarządzanego dysku systemu operacyjnego. |
## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
