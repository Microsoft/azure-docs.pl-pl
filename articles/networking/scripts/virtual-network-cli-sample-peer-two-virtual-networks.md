---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — równorzędne dwie sieci wirtualne | Microsoft Docs
description: Użyj przykładowego skryptu interfejsu wiersza polecenia platformy Azure, aby utworzyć i połączyć dwie sieci wirtualne w tym samym regionie za pomocą sieci platformy Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 507e17b2edb0db2d21265f3e945745d97395e7c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87503858"
---
# <a name="use-an-azure-cli-sample-script-to-connect-two-virtual-networks"></a>Użyj przykładowego skryptu interfejsu wiersza polecenia platformy Azure, aby połączyć dwie sieci wirtualne

Ten skrypt tworzy i łączy dwie sieci wirtualne w tym samym regionie za pomocą sieci platformy Azure. Po uruchomieniu skryptu utworzysz komunikację równorzędną pomiędzy dwiema sieciami wirtualnymi.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Tworzy sieć wirtualną i podsieć platformy Azure. |
| [az network vnet peering create](https://docs.microsoft.com/cli/azure/network/vnet/peering) | Tworzy komunikację równorzędną między dwiema sieciami wirtualnymi.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia dla sieci można znaleźć w [dokumentacji i omówieniu sieci platformy Azure](../cli-samples.md).
