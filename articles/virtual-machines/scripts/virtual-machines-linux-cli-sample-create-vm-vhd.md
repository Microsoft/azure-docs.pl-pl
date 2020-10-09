---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — Tworzenie maszyny wirtualnej przy użyciu wirtualnego dysku twardego
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie maszyny wirtualnej przy użyciu wirtualnego dysku twardego.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6f5e30b0d6a072f9a40ae57ebe325461cdfe5bb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87479633"
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Tworzenie maszyny wirtualnej przy użyciu wirtualnego dysku twardego

W tym przykładzie maszyna wirtualna jest tworzona przy użyciu dysku VHD.
Tworzona jest grupa zasobów, konto magazynu i kontener, a następnie maszyna wirtualna przez przekazanie dysku VHD do kontenera.
Zastępuje klucz publiczny ssh Twoim kluczem publicznym, aby udzielić Ci dostępu do maszyny wirtualnej.

Będziesz potrzebować rozruchowego dysku VHD. Skrypt szuka elementu `~/sample.vhd`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej, zestawu dostępności, modułu równoważenia obciążenia i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account list](/cli/azure/storage/account) | Wyświetla listę kont magazynu |
| [az storage account check-name](/cli/azure/storage/account) | Sprawdza, czy nazwa konta magazynu jest prawidłowa i czy już nie istnieje |
| [az storage account keys list](/cli/azure/storage/account/keys) | Wyświetla listę kluczy kont magazynu |
| [az storage blob exists](/cli/azure/storage/blob) | Sprawdza, czy obiekt blob istnieje |
| [az storage container create](/cli/azure/storage/container) | Tworzy kontener na koncie magazynu. |
| [az storage blob upload](/cli/azure/storage/blob) | Tworzy obiekt blob w kontenerze, przekazując dysk VHD. |
| [az vm list](/cli/azure/vm) | Używane z opcją `--query` w celu sprawdzenia, czy nazwa maszyny wirtualnej jest już w użyciu. | 
| [az vm create](/cli/azure/vm/availability-set) | Tworzy maszyny wirtualne. |
| [az vm list-ip-addresses](/cli/azure/vm#az-vm-list-ip-addresses) | Pobiera adres IP utworzonej maszyny wirtualnej. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
