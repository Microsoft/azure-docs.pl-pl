---
title: Kopiowanie migawki dysku zarządzanego do subskrypcji — przykładowy wiersza polecenia
description: Przykład skryptu interfejsu wiersza polecenia platformy Azure — kopiowanie (lub przenoszenie) migawki dysku zarządzanego do tej samej lub innej subskrypcji za pomocą interfejsu wiersza polecenia
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
ms.openlocfilehash: 707fc2f805e19487f93affd2c58943090233967f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459971"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Kopiowanie migawki dysku zarządzanego do tej samej lub innej subskrypcji przy użyciu interfejsu wiersza polecenia

Ten skrypt kopiuje migawkę dysku zarządzanego do tej samej lub innej subskrypcji. Użyj tego skryptu dla następujących scenariuszy:

1. Migrowanie migawki w magazynie w wersji Premium (Premium_LRS) do magazynu standardowego (Standard_LRS lub Standard_ZRS), aby zmniejszyć koszty.
1. Migrowanie migawki z magazynu lokalnie nadmiarowego (Premium_LRS, Standard_LRS) do magazynu nadmiarowego strefy (Standard_ZRS), aby korzystać z większej niezawodności magazynu ZRS.
1. Przenieś migawkę do innej subskrypcji w tym samym regionie, aby uzyskać dłuższe przechowywanie.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia migawki w subskrypcji docelowej przy użyciu identyfikatora migawki źródła. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Pobiera wszystkie właściwości migawki przy użyciu nazwy i właściwości grupy zasobów migawki. Do kopiowania migawki do innej subskrypcji jest używana właściwość Id.  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot) | Kopiuje migawkę przez utworzenie migawki w innej subskrypcji przy użyciu identyfikatora i nazwy migawki nadrzędnej.  |

## <a name="next-steps"></a>Następne kroki

[Tworzenie maszyny wirtualnej na podstawie migawki](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dysków zarządzanych i maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
