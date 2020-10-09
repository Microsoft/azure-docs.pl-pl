---
title: Tworzenie dysku zarządzanego na podstawie migawki (Linux) — przykład interfejsu wiersza polecenia
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie dysku zarządzanego na podstawie migawki
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 7c5e9e16b81c0befb6524a284d63fb2e106bff7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89323178"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-cli-linux"></a>Tworzenie dysku zarządzanego na podstawie migawki przy użyciu interfejsu wiersza polecenia (Linux)

Ten skrypt tworzy dysk zarządzany na podstawie migawki. Umożliwia on przywrócenie maszyny wirtualnej z migawek systemu operacyjnego i dysków danych. Twórz dyski zarządzane systemu operacyjnego i danych na podstawie odpowiednich migawek, a następnie utwórz nową maszynę wirtualną przez dołączenie dysków zarządzanych. Możesz również przywracać dyski z danymi istniejącej maszyny wirtualnej, dołączając dyski z danymi utworzone na podstawie migawek.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Create managed disk from snapshot")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń w celu utworzenia dysku zarządzanego na podstawie migawki. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az snapshot show](/cli/azure/snapshot) | Pobiera wszystkie właściwości migawki przy użyciu nazwy i właściwości grupy zasobów migawki. Właściwość Id służy do tworzenia dysku zarządzanego.  |
| [az disk create](/cli/azure/disk) | Tworzy dysk zarządzany przy użyciu identyfikatora migawki zarządzanej |

## <a name="next-steps"></a>Następne kroki

[Tworzenie maszyny wirtualnej przez dołączenie dysku zarządzanego jako dysku systemu operacyjnego](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dysków zarządzanych i maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
