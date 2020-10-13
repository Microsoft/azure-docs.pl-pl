---
title: Przesłonięcie podsieci Traffic Manager platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Ten artykuł pomoże zrozumieć, w jaki sposób przesłonięcie podsieci Traffic Manager może służyć do przesłaniania metody routingu profilu Traffic Manager, aby skierować ruch do punktu końcowego na podstawie adresu IP użytkownika końcowego za pośrednictwem wstępnie zdefiniowanego zakresu adresów IP na potrzeby mapowania punktów końcowych.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: c43b85f3c4b8d1b750e9f97e0be812bee683c702
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90600810"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Traffic Manager przesłonięcia podsieci przy użyciu interfejsu wiersza polecenia platformy Azure

Przesłonięcie podsieci Traffic Manager umożliwia zmianę metody routingu profilu.  Dodanie przesłonięcia spowoduje przekierowanie ruchu na podstawie adresu IP użytkownika końcowego ze wstępnie zdefiniowanym zakresem adresów IP do mapowania punktu końcowego. 

## <a name="how-subnet-override-works"></a>Jak działa przesłonięcie podsieci

Gdy przesłonięcia podsieci są dodawane do profilu usługi Traffic Manager, Traffic Manager najpierw sprawdza, czy istnieje przesłonięcie podsieci dla adresu IP użytkownika końcowego. Jeśli zostanie znaleziony, zapytanie DNS użytkownika zostanie skierowane do odpowiadającego mu punktu końcowego.  Jeśli mapowanie nie zostanie znalezione, Traffic Manager powróci do oryginalnej metody routingu profilu. 

Zakresy adresów IP można określić jako zakresy CIDR (na przykład 1.2.3.0/24) lub jako zakresy adresów (na przykład 1.2.3.4-5.6.7.8). Zakresy adresów IP skojarzone z każdym punktem końcowym muszą być unikatowe dla tego punktu końcowego. Wszelkie nakładanie się zakresów adresów IP między różnymi punktami końcowymi spowoduje odrzucenie profilu przez Traffic Manager.

Istnieją dwa typy profilów routingu, które obsługują przesłonięcia podsieci:

* **Geograficzna** — Jeśli Traffic Manager odnajdzie przesłonięcie podsieci dla adresu IP zapytania DNS, zostanie on rozesłany do punktu końcowego, niezależnie od kondycji punktu końcowego.
* **Wydajność** — Jeśli Traffic Manager odnajdzie przesłonięcie podsieci dla adresu IP zapytania DNS, tylko będzie kierować ruch do punktu końcowego, jeśli jest w dobrej kondycji.  Traffic Manager powróci do algorytmu heurystycznego routingu wydajności, jeśli punkt końcowy przesłonięcia podsieci nie jest w dobrej kondycji.

## <a name="create-a-traffic-manager-subnet-override"></a>Tworzenie przesłonięcia podsieci Traffic Manager

Aby utworzyć przesłonięcie podsieci Traffic Manager, możesz użyć interfejsu wiersza polecenia platformy Azure, aby dodać podsieci do przesłonięcia do Traffic Manager punktu końcowego.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Zaktualizuj punkt końcowy Traffic Manager przy użyciu przesłonięcia podsieci.
Użyj interfejsu wiersza polecenia platformy Azure, aby zaktualizować punkt końcowy za pomocą poleceń [AZ Network Traffic-Manager Endpoint Update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

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

Zakresy adresów IP można usunąć, uruchamiając polecenie [AZ Network Traffic-Manager Endpoint Update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) with **--Remove** .

```azurecli-interactive
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md)Traffic Manager.

Informacje o [metodzie routingu ruchu podsieci](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)
