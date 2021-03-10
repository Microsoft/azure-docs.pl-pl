---
title: Szybki Start — Tworzenie usługi Azure Private link przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym przewodniku szybki start dowiesz się, jak utworzyć usługę prywatnego połączenia platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: allensu
ms.openlocfilehash: 76fd959c28203132be4695031d96315f258cf53f
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563119"
---
# <a name="quickstart-create-a-private-link-service-using-azure-cli"></a>Szybki Start: Tworzenie usługi linku prywatnego przy użyciu interfejsu wiersza polecenia platformy Azure

Rozpocznij tworzenie usługi linku prywatnego, która odwołuje się do Twojej usługi.  Udziel prywatnego linku dostępu do usługi lub zasobu wdrożonego za usługa Load Balancer w warstwie Standardowa platformy Azure.  Użytkownicy usługi mają prywatny dostęp z sieci wirtualnej.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Ten przewodnik Szybki Start wymaga wersji 2.0.28 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą [AZ Group Create](/cli/azure/group#az_group_create):

* O nazwie **CreatePrivLinkService-RG**. 
* W lokalizacji **wschodniego** .

```azurecli-interactive
  az group create \
    --name CreatePrivLinkService-rg \
    --location eastus2

```

## <a name="create-an-internal-load-balancer"></a>Utwórz wewnętrzny moduł równoważenia obciążenia.

W tej sekcji utworzysz sieć wirtualną i wewnętrzną Azure Load Balancer.

### <a name="virtual-network"></a>Sieć wirtualna

W tej sekcji utworzysz sieć wirtualną i podsieć służącą do hostowania modułu równoważenia obciążenia, który uzyskuje dostęp do usługi linków prywatnych.

Utwórz sieć wirtualną za pomocą polecenia [AZ Network VNET Create](/cli/azure/network/vnet#az-network-vnet-create):

* O nazwie **myVNet**.
* Prefiks adresu **10.1.0.0/16**.
* Podsieci o nazwie Moja **podsieć**.
* Prefiks podsieci **10.1.0.0/24**.
* W grupie zasobów **CreatePrivLinkService-RG** .
* Lokalizacja elementu **eastus2**.
* Wyłącz zasady sieci dla usługi łącza prywatnego w podsieci.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

Aby zaktualizować podsieć w celu wyłączenia zasad sieciowych usługi linku prywatnego, użyj polecenie [AZ Network VNET Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name mySubnet \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNet \
    --disable-private-link-service-network-policies true
```

### <a name="create-standard-load-balancer"></a>Tworzenie usługi równoważenia obciążenia w warstwie Standardowa

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

  * Pula adresów IP frontonu, która odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
  * Pula adresów IP zaplecza, w której Pula frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
  * Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  * Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-the-load-balancer-resource"></a>Tworzenie zasobu modułu równoważenia obciążenia

Utwórz publiczny moduł równoważenia obciążenia za pomocą [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create):

* O nazwie **myLoadBalancer**.
* Pula frontonu o nazwie Moja **fronton**.
* Pula zaplecza o nazwie **myBackEndPool**.
* Skojarzone z **myVNet** sieci wirtualnej.
* Skojarzona z **podsiecią** podsieci zaplecza.

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

Maszyna wirtualna z niepowodzeniem sprawdzaniem sondy jest usuwana z modułu równoważenia obciążenia. Gdy błąd zostanie rozwiązany, maszyna wirtualna zostanie ponownie dodana do modułu równoważenia obciążenia.

Utwórz sondę kondycji za pomocą [AZ Network lb Probe Create](/cli/azure/network/lb/probe#az-network-lb-probe-create):

* Monitoruje kondycję maszyn wirtualnych.
* O nazwie **myHealthProbe**.
* Protokół **TCP**.
* Monitorowanie **portu 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje:

* Konfiguracja adresu IP frontonu dla ruchu przychodzącego.
* Pula adresów IP zaplecza do odbierania ruchu sieciowego.
* Wymagany port źródłowy i docelowy. 

Utwórz regułę modułu równoważenia obciążenia za pomocą [AZ Network lb Rule Create](/cli/azure/network/lb/rule#az-network-lb-rule-create):

* O nazwie **myHTTPRule**
* Nasłuchiwanie na **porcie 80** w elemencie **webfrontonu** puli frontonów.
* Wysyłanie ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza **myBackEndPool** przy użyciu **portu 80**. 
* Korzystanie z sondy kondycji **myHealthProbe**.
* Protokół **TCP**.
* Limit czasu bezczynności wynoszący **15 minut**.
* Włącz Resetowanie protokołu TCP.

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

## <a name="create-a-private-link-service"></a>Tworzenie usługi linku prywatnego

W tej sekcji utworzysz usługę link prywatny, która używa Azure Load Balancer utworzonej w poprzednim kroku.

Tworzenie prywatnej usługi linkowej przy użyciu konfiguracji adresu IP frontonu usługi równoważenia obciążenia za pomocą polecenia [AZ Network Private-Link-Service Create](/cli/azure/network/private-link-service#az-network-private-link-service-create):

* O nazwie **myPrivateLinkService**.
* W sieci wirtualnej **myVNet**.
* Skojarzone z **myLoadBalancerem** usługi równoważenia obciążenia **i konfiguracją frontonu**.
* W lokalizacji **eastus2** .
 
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

Usługa link prywatny jest tworzona i może odbierać ruch. Jeśli chcesz zobaczyć przepływy ruchu, skonfiguruj aplikację za usługą równoważenia obciążenia w warstwie Standardowa.


## <a name="create-private-endpoint"></a>Utwórz prywatny punkt końcowy

W tej sekcji utworzysz mapowanie prywatnego usługi linku do prywatnego punktu końcowego. Sieć wirtualna zawiera prywatny punkt końcowy usługi link prywatny. Ta sieć wirtualna zawiera zasoby, które będą miały dostęp do prywatnej usługi link.

### <a name="create-private-endpoint-virtual-network"></a>Utwórz prywatną sieć wirtualną punktu końcowego

Utwórz sieć wirtualną za pomocą polecenia [AZ Network VNET Create](/cli/azure/network/vnet#az-network-vnet-create):

* O nazwie **myVNetPE**.
* Prefiks adresu **11.1.0.0/16**.
* Podsieć o nazwie **mySubnetPE**.
* Prefiks podsieci **11.1.0.0/24**.
* W grupie zasobów **CreatePrivLinkService-RG** .
* Lokalizacja elementu **eastus2**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

Aby zaktualizować podsieć w celu wyłączenia zasad sieci prywatnych punktów końcowych, użyj polecenie [AZ Network VNET Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>Tworzenie punktu końcowego i połączenia

* Użyj [AZ Network Private-Link-Service show](/cli/azure/network/private-link-service#az_network_private_link_service_show) , aby uzyskać identyfikator zasobu usługi łącza prywatnego. Polecenie umieszcza identyfikator zasobu w zmiennej do późniejszego użycia.

* Użyj [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) , aby utworzyć prywatny punkt końcowy w utworzonej wcześniej sieci wirtualnej.

* O nazwie **MyPrivateEndpoint**.
* W grupie zasobów **CreatePrivLinkService-RG** .
* Nazwa połączenia **myPEconnectiontoPLS**.
* Lokalizacja elementu **eastus2**.
* W obszarze **myVNetPE** sieci wirtualnej i **mySubnetPE** podsieci.

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

Gdy nie jest już potrzebne, użyj polecenia [AZ Group Delete](/cli/azure/group#az-group-delete) , aby usunąć grupę zasobów, usługę link prywatny, moduł równoważenia obciążenia i wszystkie powiązane zasoby.

```azurecli-interactive
  az group delete \
    --name CreatePrivLinkService-rg 
```

## <a name="next-steps"></a>Następne kroki

W ramach tego przewodnika Szybki start wykonasz następujące czynności:

* Utworzono sieć wirtualną i wewnętrzną Azure Load Balancer.
* Utworzono usługę linku prywatnego

Aby dowiedzieć się więcej o prywatnym punkcie końcowym platformy Azure, przejdź do:
> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure](create-private-endpoint-cli.md)
