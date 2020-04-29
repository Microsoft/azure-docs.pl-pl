---
title: Tworzenie maszyny wirtualnej na podstawie przykładowej migawki — interfejs wiersza polecenia
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie maszyny wirtualnej na podstawie migawki
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ffc1a735f743796b73c2b58353638dba7ed60985
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75458454"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-cli"></a>Tworzenie maszyny wirtualnej na podstawie migawki za pomocą interfejsu wiersza polecenia

Ten skrypt umożliwia utworzenie maszyny wirtualnej na podstawie migawki dysku systemu operacyjnego.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.sh "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia dysku zarządzanego, maszyny wirtualnej i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Pobiera migawkę przy użyciu nazwy migawki i nazwy grupy zasobów. Właściwość Id zwróconego obiektu jest używana do utworzenia dysku zarządzanego.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Tworzy dyski zarządzane na podstawie migawki przy użyciu identyfikatora migawki, nazwy dysku, typ magazynu i rozmiaru.  |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Tworzy maszynę wirtualną przy użyciu zarządzanego dysku systemu operacyjnego. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
