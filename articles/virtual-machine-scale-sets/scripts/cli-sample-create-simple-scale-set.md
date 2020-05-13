---
title: Przykłady interfejsu wiersza polecenia platformy Azure — Tworzenie zestawu skalowania maszyn wirtualnych
description: Ten skrypt umożliwia utworzenie zestawu skalowania maszyn wirtualnych platformy Azure z systemem operacyjnym Ubuntu i powiązanymi zasobami sieciowymi łącznie z modułem równoważenia obciążenia.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 03/27/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: ad21a816aba5bd8f8de1c541cc4bc34243956d35
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83200990"
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli"></a>Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure
Ten skrypt umożliwia utworzenie zestawu skalowania maszyn wirtualnych platformy Azure z systemem operacyjnym Ubuntu i powiązanymi zasobami sieciowymi łącznie z modułem równoważenia obciążenia. Po uruchomieniu skryptu dostęp do wystąpień maszyn wirtualnych możesz uzyskać za pomocą protokołu SSH.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/simple-scale-set/simple-scale-set.sh "Create a simple virtual machine scale set")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Uruchom następujące polecenie, aby usunąć grupę zasobów, zestaw skalowania i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, zestawu skalowania maszyn wirtualnych i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/ad/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az vmss create](/cli/azure/vmss) | Tworzy zestaw skalowania maszyn wirtualnych i łączy go z siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. Zostanie też utworzony moduł równoważenia obciążenia w celu dystrybucji ruchu do wielu wystąpień maszyn wirtualnych. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne.  |
| [az group delete](/cli/azure/ad/group) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/overview).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia platformy Azure dotyczących zestawu skalowania maszyn wirtualnych można znaleźć w [dokumentacji zestawu skalowania maszyn wirtualnych platformy Azure](../cli-samples.md).
