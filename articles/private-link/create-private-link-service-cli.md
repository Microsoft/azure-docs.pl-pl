---
title: Szybki start — tworzenie usługi Azure Private Link przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć usługę Azure Private Link przy użyciu interfejsu wiersza polecenia platformy Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: allensu
ms.openlocfilehash: c8e32a56148326104c3514b8a2fdb5d6bbd3f00a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778485"
---
# <a name="quickstart-create-a-private-link-service-using-azure-cli"></a>Szybki start: tworzenie usługi Private Link przy użyciu interfejsu wiersza polecenia platformy Azure

Wprowadzenie do tworzenia usługi Private Link, która odnosi się do Twojej usługi.  Zapewnianie Private Link do usługi lub zasobu wdrożonego za usługą Azure usługa Load Balancer w warstwie Standardowa.  Użytkownicy usługi mają dostęp prywatny z sieci wirtualnej.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą [az group create](/cli/azure/group#az_group_create):

* O **nazwie CreatePrivLinkService-rg.** 
* W lokalizacji **eastus.**

```azurecli-interactive
  az group create \
    --name CreatePrivLinkService-rg \
    --location eastus2

```

## <a name="create-an-internal-load-balancer"></a>Utwórz wewnętrzny moduł równoważenia obciążenia.

W tej sekcji utworzysz sieć wirtualną i wewnętrzną sieć Azure Load Balancer.

### <a name="virtual-network"></a>Sieć wirtualna

W tej sekcji utworzysz sieć wirtualną i podsieć do hostowania usługi równoważenia obciążenia, która uzyskuje dostęp do Private Link usługi.

Utwórz sieć wirtualną za pomocą [narzędzia az network vnet create](/cli/azure/network/vnet#az_network_vnet_create):

* Nosi **nazwę myVNet.**
* Prefiks adresu **10.1.0.0/16.**
* Podsieć **o nazwie mySubnet**.
* Prefiks podsieci **10.1.0.0/24.**
* W grupie **zasobów CreatePrivLinkService-rg.**
* Lokalizacja **regionu eastus2**.
* Wyłącz zasady sieciowe dla usługi łącza prywatnego w podsieci.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

Aby zaktualizować podsieć w celu wyłączenia zasad sieci usługi łącza prywatnego, użyj [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update):

```azurecli-interactive
az network vnet subnet update \
    --name mySubnet \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNet \
    --disable-private-link-service-network-policies true
```

### <a name="create-standard-load-balancer"></a>Tworzenie standardowego równoważenia obciążenia

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

  * Pula adresów IP frontonia, która odbiera przychodzący ruch sieciowy w równoważeniu obciążenia.
  * Pula adresów IP zaplecza, w której pula frontona wysyła ruch sieciowy ze zrównoważonym obciążeniem.
  * Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  * Reguła równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-the-load-balancer-resource"></a>Tworzenie zasobu równoważenia obciążenia

Utwórz publiczny równoważenie obciążenia za pomocą [az network lb create](/cli/azure/network/lb#az_network_lb_create):

* Nazwane **myLoadBalancer**.
* Pula frontendu o **nazwie myFrontEnd.**
* Pula zaplecza o nazwie **myBackEndPool.**
* Skojarzona z siecią **wirtualną myVNet.**
* Skojarzona z podsiecią zaplecza **mySubnet.**

```azurecli-interactive
  az network lb create \
    --resource-group CreatePrivLinkService-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet mySubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji sprawdza wszystkie wystąpienia maszyn wirtualnych, aby upewnić się, że mogą wysyłać ruch sieciowy. 

Maszyna wirtualna z nieudanym sprawdzaniem sondy jest usuwana z usługi równoważenia obciążenia. Maszyna wirtualna jest dodawana z powrotem do usługi równoważenia obciążenia, gdy awaria zostanie rozwiązana.

Utwórz sondę kondycji za pomocą [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create):

* Monitoruje kondycję maszyn wirtualnych.
* Nosi **nazwę myHealthProbe.**
* Protokół **TCP**.
* Port **monitorowania 80.**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła równoważenia obciążenia definiuje:

* Konfiguracja adresu IP frontendu dla ruchu przychodzącego.
* Pula adresów IP zaplecza do odbierania ruchu.
* Wymagany port źródłowy i docelowy. 

Utwórz regułę usługi równoważenia obciążenia za pomocą [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create):

* O **nazwie myHTTPRule**
* Nasłuchiwanie **na porcie 80** w puli frontonie **myFrontEnd.**
* Wysyłanie ruchu sieciowego ze zrównoważonym obciążeniem do puli adresów zaplecza **myBackEndPool** przy **użyciu portu 80.** 
* Za pomocą sondy **kondycji myHealthProbe**.
* Protokół **TCP**.
* Limit czasu bezczynności: **15 minut.**
* Włącz resetowanie protokołu TCP.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

## <a name="create-a-private-link-service"></a>Tworzenie usługi łącza prywatnego

W tej sekcji utworzysz usługę łącza prywatnego, która używa Azure Load Balancer utworzonej w poprzednim kroku.

Utwórz usługę łącza prywatnego przy użyciu standardowej konfiguracji adresu IP frontonia usługi równoważenia obciążenia za pomocą [narzędzia az network private-link-service create:](/cli/azure/network/private-link-service#az_network_private_link_service_create)

* O **nazwie myPrivateLinkService.**
* W sieci **wirtualnej myVNet**.
* Skojarzone ze standardowymi usługami **równoważenia obciążenia myLoadBalancer** i konfiguracją frontonu **myFrontEnd.**
* W lokalizacji **eastus2.**
 
```azurecli-interactive
az network private-link-service create \
    --resource-group CreatePrivLinkService-rg \
    --name myPrivateLinkService \
    --vnet-name myVNet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-frontend-ip-configs myFrontEnd \
    --location eastus2
```

Usługa łącza prywatnego została utworzona i może odbierać ruch. Jeśli chcesz wyświetlić przepływy ruchu, skonfiguruj aplikację za standardowym równoważeniem obciążenia.


## <a name="create-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

W tej sekcji zamapujesz usługę łącza prywatnego na prywatny punkt końcowy. Sieć wirtualna zawiera prywatny punkt końcowy dla usługi łącza prywatnego. Ta sieć wirtualna zawiera zasoby, które będą mieć dostęp do usługi łącza prywatnego.

### <a name="create-private-endpoint-virtual-network"></a>Tworzenie sieci wirtualnej prywatnego punktu końcowego

Utwórz sieć wirtualną za pomocą [narzędzia az network vnet create](/cli/azure/network/vnet#az_network_vnet_create):

* Nosi **nazwę myVNetPE.**
* Prefiks adresu **11.1.0.0/16.**
* Podsieć **o nazwie mySubnetPE**.
* Prefiks podsieci **11.1.0.0/24.**
* W grupie **zasobów CreatePrivLinkService-rg.**
* Lokalizacja **regionu eastus2**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

Aby zaktualizować podsieć, aby wyłączyć zasady sieci prywatnego punktu końcowego, użyj [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update):

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>Tworzenie punktu końcowego i połączenia

* Użyj [az network private-link-service show,](/cli/azure/network/private-link-service#az_network_private_link_service_show) aby uzyskać identyfikator zasobu usługi łącza prywatnego. Polecenie umieszcza identyfikator zasobu w zmiennej do późniejszego użycia.

* Użyj [az network private-endpoint create,](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) aby utworzyć prywatny punkt końcowy w utworzonej wcześniej sieci wirtualnej.

* O **nazwie MyPrivateEndpoint.**
* W grupie **zasobów CreatePrivLinkService-rg.**
* Nazwa połączenia **myPEconnectiontoPLS.**
* Lokalizacja **regionu eastus2**.
* W sieci **wirtualnej myVNetPE** i podsieci **mySubnetPE.**

```azurecli-interactive
  export resourceid=$(az network private-link-service show \
    --name myPrivateLinkService \
    --resource-group CreatePrivLinkService-rg \
    --query id \
    --output tsv)

  az network private-endpoint create \
    --connection-name myPEconnectiontoPLS \
    --name myPrivateEndpoint \
    --private-connection-resource-id $resourceid \
    --resource-group CreatePrivLinkService-rg \
    --subnet mySubnetPE \
    --manual-request false \
    --vnet-name myVNetPE 

```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, usługa łącza prywatnego, usługa równoważenia obciążenia i wszystkie pokrewne zasoby nie będą już potrzebne, użyj polecenia [az group delete.](/cli/azure/group#az_group_delete)

```azurecli-interactive
  az group delete \
    --name CreatePrivLinkService-rg 
```

## <a name="next-steps"></a>Następne kroki

W ramach tego przewodnika Szybki start wykonasz następujące czynności:

* Utworzono sieć wirtualną i sieć Azure Load Balancer.
* Utworzono usługę łącza prywatnego

Aby dowiedzieć się więcej na temat prywatnego punktu końcowego platformy Azure, przejdź do:
> [!div class="nextstepaction"]
> [Szybki start: tworzenie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure](create-private-endpoint-cli.md)
