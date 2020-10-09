---
title: Kopiuj dyski zarządzane do przykładu subskrypcji — interfejsu wiersza polecenia
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — kopiowanie (lub przenoszenie) dysków zarządzanych do tej samej lub innej subskrypcji
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ed593b37de556d5b62cfec6b726322d12f4d8104
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89052270"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Kopiowanie dysków zarządzanych do tej samej lub innej subskrypcji za pomocą interfejsu wiersza polecenia

Ten skrypt kopiuje dysk zarządzany do tej samej lub innej subskrypcji w obrębie jednego regionu. Kopia działa tylko wtedy, gdy subskrypcje są częścią tej samej dzierżawy usługi Azure AD.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia nowego dysku zarządzanego w subskrypcji docelowej przy użyciu identyfikatora źródłowego dysku zarządzanego. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az disk show](/cli/azure/disk) | Pobiera wszystkie właściwości dysku zarządzanego przy użyciu nazwy i właściwości grupy zasobów dysku zarządzanego. Do kopiowania dysku zarządzanego do innej subskrypcji jest używana właściwość Id.  |
| [az disk create](/cli/azure/disk) | Kopiuje dysk zarządzany przez utworzenie nowego dysku zarządzanego w innej subskrypcji przy użyciu identyfikatora i nazwy nadrzędnego dysku zarządzanego.  |

## <a name="next-steps"></a>Następne kroki

[Tworzenie maszyny wirtualnej na podstawie dysku zarządzanego](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dysków zarządzanych i maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
