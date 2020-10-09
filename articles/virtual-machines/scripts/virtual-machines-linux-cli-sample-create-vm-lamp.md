---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — wdrażanie stosu LAMP w Load-Balanced zestawie skalowania maszyn wirtualnych
description: Użyj rozszerzenia skryptu niestandardowego w celu wdrożenia stosu LAMP w zestawie skalowania maszyn wirtualnych ze zrównoważonym obciążeniem na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
ms.author: cynthn
tags: azure-service-management
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.custom: mvc, devx-track-azurecli
ms.date: 04/05/2017
ms.openlocfilehash: e71b2f940c637a8b1375be71f9b6ca95fd76628e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501869"
---
# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>Wdrażanie stosu LAMP w zestawie skalowania maszyn wirtualnych ze zrównoważonym obciążeniem

Ten przykład tworzy zestaw skalowania maszyn wirtualnych i stosuje rozszerzenie, które uruchamia niestandardowy skrypt w celu wdrożenia stosu LAMP na każdej maszynie wirtualnej w zestawie skalowania.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Create virtual machine scale set with LAMP stack")]

## <a name="connect"></a>Connect

Ten kod umożliwia sprawdzenie sposobu połączenia maszyn wirtualnych i zestawu skalowania.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Access the virtual machine scale set")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, zestaw skalowania i maszyny wirtualne oraz wszystkie powiązane zasoby.

```azurecli-interactive 
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej, zestawu dostępności, modułu równoważenia obciążenia i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az vmss create](/cli/azure/vmss) | Tworzy zestaw skalowania maszyn wirtualnych. |
| [az network lb rule create](/cli/azure/network/lb/rule) | Dodaje zestaw punktów końcowych ze zrównoważonym obciążeniem. |
| [az vmss extension set](/cli/azure/vmss/extension) | Tworzy rozszerzenie, które uruchamia skrypt niestandardowy w obrębie wdrożenia maszyny wirtualnej. |
| [az vmss update-instances](/cli/azure/vmss) | Uruchamia skrypt niestandardowy w obrębie wdrożonych wystąpień maszyny wirtualnej przed zastosowaniem rozszerzenia w zestawie skalowania. |
| [az vmss scale](/cli/azure/vmss) | Skaluje zestaw skalowania w górę, dodając kolejne wystąpienia maszyn wirtualnych. Podczas ich wdrażania w ich obrębie jest uruchamiany skrypt niestandardowy. |
| [az network public-ip list](/cli/azure/network/public-ip) | Pobiera adresy IP maszyn wirtualnych utworzonych w ramach przykładu. |
| [az network lb show](/cli/azure/network/lb) | Pobiera porty frontonu i zaplecza używane przez moduł równoważenia obciążenia. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
