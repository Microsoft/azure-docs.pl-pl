---
title: Kopiuj dyski zarządzane do przykładu subskrypcji — interfejsu wiersza polecenia
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — kopiowanie (lub przenoszenie) dysków zarządzanych do tej samej lub innej subskrypcji
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
ms.openlocfilehash: 540f11e2089d6aeac1b6c664695d1dafbf31d65a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81460889"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Kopiowanie dysków zarządzanych do tej samej lub innej subskrypcji za pomocą interfejsu wiersza polecenia

Ten skrypt kopiuje dysk zarządzany do tej samej lub innej subskrypcji w obrębie jednego regionu. Kopiowanie działa tylko wtedy, gdy subskrypcje należą do tej samej dzierżawy usługi AAD.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia nowego dysku zarządzanego w subskrypcji docelowej przy użyciu identyfikatora źródłowego dysku zarządzanego. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk) | Pobiera wszystkie właściwości dysku zarządzanego przy użyciu nazwy i właściwości grupy zasobów dysku zarządzanego. Do kopiowania dysku zarządzanego do innej subskrypcji jest używana właściwość Id.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Kopiuje dysk zarządzany przez utworzenie nowego dysku zarządzanego w innej subskrypcji przy użyciu identyfikatora i nazwy nadrzędnego dysku zarządzanego.  |

## <a name="next-steps"></a>Następne kroki

[Tworzenie maszyny wirtualnej na podstawie dysku zarządzanego](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dysków zarządzanych i maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
