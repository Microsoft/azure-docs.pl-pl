---
title: Kopiowanie migawki dysku zarządzanego do subskrypcji — przykładowy wiersza polecenia
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — kopiowanie (przenoszenie) migawki dysków zarządzanych do tej samej lub innej subskrypcji przy użyciu interfejsu wiersza polecenia
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 00c06790a146efb089b65d16b93b6dc624a11e85
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459597"
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

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia maszyny wirtualnej i dysków zarządzanych można znaleźć w [dokumentacji maszyny wirtualnej systemu Azure systemu Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
