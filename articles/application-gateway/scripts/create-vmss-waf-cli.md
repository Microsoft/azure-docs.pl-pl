---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — ograniczanie ruchu internetowego | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie bramy aplikacji przy użyciu zapory aplikacji internetowych i zestawu skalowania maszyn wirtualnych, który używa reguł OWASP do ograniczania ruchu.
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6e20a4324f46925b8f83d7519c481a1d5bfc06a9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789307"
---
# <a name="restrict-web-traffic-using-the-azure-cli"></a>Ograniczanie ruchu internetowego przy użyciu interfejsu wiersza polecenia platformy Azure

Ten skrypt tworzy bramę aplikacji z zaporą aplikacji internetowych, która używa zestawu skalowania maszyn wirtualnych dla serwerów zaplecza. Zapora aplikacji internetowych ogranicza ruch internetowy na podstawie reguł OWASP. Po uruchomieniu skryptu można przetestować bramę aplikacji, korzystając z jej publicznego adresu IP.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss-waf.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, bramę aplikacji i wszystkie powiązane zasoby.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia wdrożenia. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Tworzy sieć wirtualną. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Tworzy podsieć w sieci wirtualnej. |
| [az network public-ip create](/cli/azure/network/public-ip) | Tworzy publiczny adres IP dla bramy aplikacji. |
| [az network application-gateway create](/cli/azure/network/application-gateway) | Tworzy bramę aplikacji. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Tworzy zestaw skalowania maszyn wirtualnych. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Tworzy konto magazynu. |
| [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) | Tworzy konto magazynu. |
| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) | Pobiera publiczny adres IP bramy aplikacji. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure/overview).

Dodatkowe przykładowe skrypty interfejsu wiersza polecenia dotyczące bramy aplikacji można znaleźć w [dokumentacji usługi Azure Application Gateway](../cli-samples.md).
