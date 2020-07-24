---
title: Filtrowanie ruchu sieciowego maszyny wirtualnej — przykład skryptu interfejsu wiersza polecenia platformy Azure
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — filtrowanie przychodzącego i wychodzącego ruchu sieciowego maszyny wirtualnej.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.openlocfilehash: d47678767bd73ab993cfd0e8d0bc9b532b2bfaaa
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046112"
---
# <a name="filter-inbound-and-outbound-vm-network-traffic-using-an-azure-cli-script-sample"></a>Filtrowanie przychodzącego i wychodzącego ruchu sieciowego maszyny wirtualnej za pomocą przykładowego skryptu interfejsu wiersza polecenia platformy Azure

Ten przykładowy skrypt tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch sieciowy przychodzący do podsieci frontonu jest ograniczony do protokołów HTTP, HTTPS i SSH, a ruch wychodzący do Internetu z podsieci zaplecza jest niedozwolony. Po uruchomieniu skryptu będziesz mieć jedną maszynę wirtualną z dwiema kartami sieciowymi. Każda karta sieciowa jest połączona z inną podsiecią.

Skrypt można wykonać z poziomu usługi Azure [Cloud Shell](https://shell.azure.com/bash) lub lokalnej instalacji wiersza polecenia platformy Azure. Jeśli używasz interfejsu wiersza polecenia lokalnie, ten skrypt wymaga uruchomienia wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia lokalnie, musisz też uruchomić polecenie `az login`, aby utworzyć połączenie z platformą Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, sieci wirtualnej i sieciowych grup zabezpieczeń. Każde polecenie w poniższej tabeli stanowi link do dokumentacji polecenia:

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az network vnet create](/cli/azure/network/vnet) | Tworzy sieć wirtualną i podsieć frontonu platformy Azure. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Tworzy podsieć zaplecza. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet) | Tworzy skojarzenia sieciowych grup zabezpieczeń z podsieciami. |
| [az network public-ip create](/cli/azure/network/public-ip) | Tworzy publiczny adres IP umożliwiający uzyskiwanie dostępu do maszyny wirtualnej z Internetu. |
| [az network nic create](/cli/azure/network/nic) | Tworzy interfejsy sieci wirtualnej i dołącza je do podsieci frontonu i zaplecza sieci wirtualnej. |
| [az network nsg create](/cli/azure/network/nsg) | Tworzy sieciowe grupy zabezpieczeń, które zostały skojarzone z podsieciami frontonu i zaplecza. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |Tworzy reguły sieciowych grup zabezpieczeń, które zezwalają na użycie określonych portów w wybranych sieciach lub blokują te porty. |
| [az vm create](/cli/azure/vm) | Tworzy maszyny wirtualne i dołącza kartę sieciową do każdej maszyny wirtualnej. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne. |
| [az group delete](/cli/azure/group) | Usuwa grupę zasobów i wszystkie zasoby, które zawiera. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla sieci wirtualnej można znaleźć w temacie [Virtual network CLI samples (Przykładowe skrypty interfejsu wiersza polecenia dla sieci wirtualnej)](../cli-samples.md).
