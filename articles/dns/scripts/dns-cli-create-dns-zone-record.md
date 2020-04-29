---
title: Tworzenie strefy i rekordu DNS dla nazwy domeny — interfejs wiersza polecenia platformy Azure — Azure DNS
description: Ten przykładowy skrypt wiersza polecenia platformy Azure przedstawia sposób tworzenia strefy DNS i rekordu dla nazwy domeny
services: dns
author: rohinkoul
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: rohink
ms.openlocfilehash: b1db9ffb895470dd9224dc020eb82b0bbf6560c1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76939308"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Przykładowy skrypt interfejsu wiersza polecenia platformy Azure: tworzenie rekordu i strefy DNS

Ten przykładowy skrypt wiersza polecenia platformy Azure umożliwia utworzenie strefy DNS i rekordu dla nazwy domeny. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/dns/create-dns-zone-and-record/create-dns-zone-and-record.sh "Create DNS zone and record")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, strefę DNS i wszystkie powiązane zasoby.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej, zestawu dostępności, modułu równoważenia obciążenia i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Tworzy strefę DNS na platformie Azure. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | Dodaje rekord *A* do strefy DNS. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | Zwraca listę wszystkich zestawów rekordów *A* w strefie DNS. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

