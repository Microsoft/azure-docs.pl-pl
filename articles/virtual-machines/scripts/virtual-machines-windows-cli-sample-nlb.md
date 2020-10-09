---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie maszyny wirtualnej z systemem Windows Server 2016 przy użyciu modułu równoważenia obciążenia sieciowego
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie maszyny wirtualnej z systemem Windows Server 2016 przy użyciu modułu równoważenia obciążenia sieciowego
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1ef4136e65c2d5c58eff5b63bacd8e3cffe802e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500848"
---
# <a name="use-an-azure-cli-sample-script-to-load-balance-traffic-between-highly-available-virtual-machines"></a>Używanie przykładowego skryptu interfejsu wiersza polecenia platformy Azure w celu równoważenia obciążenia ruchu między maszynami wirtualnymi o wysokiej dostępności

Ten przykładowy skrypt umożliwia utworzenie wszystkich elementów potrzebnych do uruchomienia kilku maszyn wirtualnych z systemem Ubuntu skonfigurowanych w ramach konfiguracji o wysokiej dostępności i zrównoważonym obciążeniu. Po uruchomieniu skryptu będziesz mieć trzy maszyny wirtualne dołączone do zestawu dostępności platformy Azure i dostępne za pośrednictwem usługi Azure Load Balancer.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-windows-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej, zestawu dostępności, modułu równoważenia obciążenia i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az network vnet create](/cli/azure/network/vnet) | Tworzy sieć wirtualną i podsieć platformy Azure. |
| [az network public-ip create](/cli/azure/network/public-ip) | Tworzy publiczny adres IP przy użyciu statycznego adresu IP i skojarzonej nazwy DNS. |
| [az network lb create](/cli/azure/network/lb) | Tworzy moduł Azure Network Load Balancer (NLB). |
| [az network lb probe create](/cli/azure/network/lb/probe) | Tworzy sondę równoważenia obciążenia sieciowego. Sonda równoważenia obciążenia sieciowego jest używana do monitorowania poszczególnych maszyn wirtualnych w zestawie równoważenia obciążenia sieciowego. Jeśli maszyna wirtualna stanie się niedostępna, ruch nie będzie do niej kierowany. |
| [az network lb rule create](/cli/azure/network/lb/rule) | Tworzy regułę równoważenia obciążenia sieciowego. W tym przykładzie tworzona jest reguła dla portu 80. Ruch HTTP docierający do modułu równoważenia obciążenia sieciowego jest kierowany do portu 80 jednej z maszyn wirtualnych w module równoważenia obciążenia sieciowego. |
| [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule) | Tworzy regułę translatora adresów sieciowych modułu równoważenia obciążenia sieciowego.  Reguły translatora adresów sieciowych mapują port modułu równoważenia obciążenia sieciowego na port na maszynie wirtualnej. W tym przykładzie tworzona jest reguła translatora adresów sieciowych dla ruchu SSH do każdej maszyny wirtualnej w zestawie równoważenia obciążenia sieciowego.  |
| [az network nsg create](/cli/azure/network/nsg) | Tworzy sieciową grupę zabezpieczeń, czyli granicę zabezpieczeń między Internetem i maszyną wirtualną. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) | Tworzy regułę sieciowej grupy zabezpieczeń zezwalającą na ruch przychodzący. W tym przykładzie port 22 jest otwierany dla ruchu protokołu SSH. |
| [az network nic create](/cli/azure/network/nic) | Tworzy wirtualną kartę sieciową i dołącza ją do sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń. |
| [az vm availability-set create](/cli/azure/network/lb/rule) | Tworzy zestaw dostępności. Zestawy dostępności zapewniają działanie aplikacji bez przestojów dzięki rozmieszczeniu maszyn wirtualnych w ramach zasobów fizycznych tak, aby niepowodzenie nie wpływało na cały zestaw. |
| [az vm create](/cli/azure/vm/availability-set) | Tworzy maszynę wirtualną i łączy ją z kartą sieciową, siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne.  |
| [az group delete](/cli/azure/vm/extension) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
