---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — Równoważenie obciążenia wielu witryn sieci Web za pomocą interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — Równoważenie obciążenia wielu witryn sieci Web do tej samej maszyny wirtualnej
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: allensu
ms.openlocfilehash: 7a3bd4cab8123814fe360fe4ab724650c785e9f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98234118"
---
# <a name="load-balance-multiple-websites"></a>Równoważenie obciążenia wielu witryn internetowych

Ten przykładowy skrypt tworzy sieć wirtualną z dwiema maszynami wirtualnymi, które są elementami członkowskimi zestawu dostępności. Moduł równoważenia obciążenia kieruje ruch dla 2 osobnych adresów IP do 2 maszyn wirtualnych. Po uruchomieniu skryptu możesz wdrożyć oprogramowanie serwera internetowego na maszynach wirtualnych i hostować wiele witryn internetowych, z których każda będzie miała własny adres IP.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt


[!code-azurecli-interactive[main](../../../cli_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.sh  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, sieci wirtualnej, modułu równoważenia obciążenia i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az network vnet create](/cli/azure/network/vnet) | Tworzy sieć wirtualną i podsieć platformy Azure. |
| [az network public-ip create](/cli/azure/network/public-ip) | Tworzy publiczny adres IP przy użyciu statycznego adresu IP i skojarzonej nazwy DNS. |
| [az network lb create](/cli/azure/network/lb) | Tworzy moduł równoważenia obciążenia platformy Azure. |
| [az network lb probe create](/cli/azure/network/lb/probe) | Tworzy sondę modułu równoważenia obciążenia. Sonda modułu równoważenia obciążenia służy do monitorowania każdej maszyny wirtualnej w zestawie modułu równoważenia obciążenia. Jeśli maszyna wirtualna stanie się niedostępna, ruch nie będzie do niej kierowany. |
| [az network lb rule create](/cli/azure/network/lb/rule) | Tworzy regułę modułu równoważenia obciążenia. W tym przykładzie tworzona jest reguła dla portu 80. Ruch HTTP docierający do modułu równoważenia obciążenia jest kierowany do portu 80 jednej z maszyn wirtualnych w zestawie modułu równoważenia obciążenia. |
| [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) | Tworzy adres IP frontonu dla modułu równoważenia obciążenia. |
| [az network lb address-pool create](/cli/azure/network/lb/address-pool) | Tworzy pulę adresów zaplecza. |
| [az network nic create](/cli/azure/network/nic) | Tworzy wirtualną kartę sieciową i dołącza ją do sieci wirtualnej oraz podsieci. |
| [az vm availability-set create](/cli/azure/network/lb/rule) | Tworzy zestaw dostępności. Zestawy dostępności zapewniają działanie aplikacji bez przestojów dzięki rozmieszczeniu maszyn wirtualnych w ramach zasobów fizycznych tak, aby niepowodzenie nie wpływało na cały zestaw. |
| [az network nic ip-config create](/cli/azure/network/nic/ip-config) | Tworzy konfigurację adresu IP. Funkcja Microsoft.Network/AllowMultipleIpConfigurationsPerNic musi być włączona dla subskrypcji. Jako podstawową konfigurację adresu IP można wyznaczyć tylko jedną konfigurację na kartę sieciową (przy użyciu flagi --make-primary). |
| [az vm create](/cli/azure/vm/availability-set) | Tworzy maszynę wirtualną i łączy ją z kartą sieciową, siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne.  |
| [az group delete](/cli/azure/vm/extension) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia dla sieci można znaleźć w [dokumentacji i omówieniu sieci platformy Azure](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).