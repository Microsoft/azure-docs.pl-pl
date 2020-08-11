---
title: Kopiowanie migawki dysku zarządzanego do przykładu subskrypcji — interfejs wiersza polecenia, maszyna wirtualna z systemem Linux
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — kopiowanie (lub przenoszenie) migawek dysku zarządzanego do tej samej lub innej subskrypcji przy użyciu interfejsu wiersza polecenia na maszynie wirtualnej z systemem Linux
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
ms.openlocfilehash: 637c8c3a2f6ba90a7a16fa375d99a7463be71270
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056095"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli-on-a-linux-vm"></a>Kopiowanie migawki dysku zarządzanego do tej samej lub innej subskrypcji przy użyciu interfejsu wiersza polecenia na maszynie wirtualnej z systemem Linux

Ten skrypt kopiuje migawkę dysku zarządzanego do tej samej lub innej subskrypcji. Użyj tego skryptu w następujących scenariuszach:

1. Przeprowadź migrację migawki w usłudze Premium Storage (Premium_LRS) do magazynu w warstwie Standardowa (Standard_LRS lub Standard_ZRS), aby zmniejszyć koszty.
1. Przeprowadź migrację migawki z magazynu lokalnie nadmiarowego (Premium_LRS, Standard_LRS) do magazynu Strefowo nadmiarowego (Standard_ZRS), aby korzystać z wyższej niezawodności magazynu ZRS.
1. Przenieś migawkę do innej subskrypcji w tym samym regionie w celu dłuższego przechowywania.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia migawki w subskrypcji docelowej przy użyciu identyfikatora migawki źródła. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az snapshot show](/cli/azure/snapshot) | Pobiera wszystkie właściwości migawki przy użyciu nazwy i właściwości grupy zasobów migawki. Do kopiowania migawki do innej subskrypcji jest używana właściwość Id.  |
| [az snapshot create](/cli/azure/snapshot) | Kopiuje migawkę przez utworzenie migawki w innej subskrypcji przy użyciu identyfikatora i nazwy migawki nadrzędnej.  |

## <a name="next-steps"></a>Następne kroki

[Tworzenie maszyny wirtualnej na podstawie migawki](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dysków zarządzanych i maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
