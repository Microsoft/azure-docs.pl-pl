---
title: Azure Traffic Manager podsieci przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: W tym artykule opisano sposób, w jaki można użyć przesłonięcia podsieci Traffic Manager w celu zastąpienia metody routingu profilu usługi Traffic Manager w celu kierowania ruchu do punktu końcowego na podstawie adresu IP użytkownika końcowego za pośrednictwem wstępnie zdefiniowanego zakresu adresów IP na mapowania punktów końcowych.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: fab167884d9060edc4f626d3ee05fa0b23389d92
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767803"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Traffic Manager podsieci przy użyciu interfejsu wiersza polecenia platformy Azure

Traffic Manager podsieci umożliwia zmianę metody routingu profilu.  Dodanie przesłonięcia spowoduje kierowanie ruchu na podstawie adresu IP użytkownika końcowego ze wstępnie zdefiniowanym zakresem adresów IP na mapowanie punktu końcowego. 

## <a name="how-subnet-override-works"></a>Jak działa przesłonięcie podsieci

Po dodaniu zastąpień podsieci do profilu usługi Traffic Manager Traffic Manager najpierw sprawdzi, czy istnieje przesłonięcie podsieci dla adresu IP użytkownika końcowego. Jeśli zostanie znaleziony, zapytanie DNS użytkownika zostanie skierowane do odpowiedniego punktu końcowego.  Jeśli mapowanie nie zostanie znalezione, Traffic Manager powróci do oryginalnej metody routingu profilu. 

Zakresy adresów IP można określić jako zakresy CIDR (na przykład 1.2.3.0/24) lub jako zakresy adresów (na przykład 1.2.3.4-5.6.7.8). Zakresy adresów IP skojarzone z każdym punktem końcowym muszą być unikatowe dla tego punktu końcowego. Wszelkie nakładanie się zakresów adresów IP między różnymi punktami końcowymi spowoduje odrzucenie profilu przez Traffic Manager.

Istnieją dwa typy profilów routingu, które obsługują zastąpienia podsieci:

* **Geograficzne** — Traffic Manager znajdzie przesłonięcie podsieci dla adresu IP zapytania DNS, spowoduje to skierowanie zapytania do punktu końcowego niezależnie od kondycji punktu końcowego.
* **Wydajność** — Traffic Manager znajdzie przesłonięcie podsieci dla adresu IP zapytania DNS, będzie on przekierowyowywywać ruch do punktu końcowego tylko wtedy, gdy jest w dobrej kondycji.  Traffic Manager powróci do heurystycznego routingu wydajności, jeśli punkt końcowy zastąpienia podsieci nie jest w dobrej kondycji.

## <a name="create-a-traffic-manager-subnet-override"></a>Tworzenie przesłonięcia Traffic Manager podsieci

Aby utworzyć Traffic Manager podsieci, możesz użyć interfejsu wiersza polecenia platformy Azure, aby dodać podsieci dla przesłonięcia do Traffic Manager końcowego.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Zaktualizuj punkt końcowy Traffic Manager za pomocą przesłonięcia podsieci.
Użyj interfejsu wiersza polecenia platformy Azure, aby zaktualizować punkt końcowy za pomocą [polecenia az network traffic-manager endpoint update](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_update).

```azurecli-interactive
### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints
```

Zakresy adresów IP można usunąć, uruchamiając aktualizację punktu końcowego [az network traffic-manager](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_update) z **opcją --remove.**

```azurecli-interactive
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Traffic Manager [routingu ruchu sieciowego.](traffic-manager-routing-methods.md)

Dowiedz się więcej [o metodzie routingu ruchu w podsieci](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)