---
title: Tworzenie strefy i rekordu DNS dla nazwy domeny — interfejs wiersza polecenia platformy Azure — Azure DNS
description: Ten przykładowy skrypt wiersza polecenia platformy Azure przedstawia sposób tworzenia strefy DNS i rekordu dla nazwy domeny
services: dns
author: rohinkoul
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 348b7911930711a25c88595b6360341ef6e00468
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954430"
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
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).