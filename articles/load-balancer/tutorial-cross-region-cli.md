---
title: 'Samouczek: Tworzenie międzyregionowego modułu równoważenia obciążenia przy użyciu interfejsu wiersza polecenia platformy Azure'
titleSuffix: Azure Load Balancer
description: Rozpocznij pracę z tym samouczkiem, wdrażając Azure Load Balancer międzyregionowe za pomocą interfejsu wiersza polecenia platformy Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: c41dc65b920c80d25a81a09f4550e76a8fd1095a
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204548"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>Samouczek: Tworzenie Azure Load Balancer międzyregionowych przy użyciu interfejsu wiersza polecenia platformy Azure

Moduł równoważenia obciążenia między regionami gwarantuje, że usługa jest dostępna globalnie w wielu regionach świadczenia usługi Azure. Jeśli jeden region ulegnie awarii, ruch jest kierowany do następnego najbliższego w dobrej kondycji regionalnego modułu równoważenia obciążenia.  

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz międzyregionowy moduł równoważenia obciążenia.
> * Utwórz regułę modułu równoważenia obciążenia.
> * Utwórz pulę zaplecza zawierającą dwa regionalne moduły równoważenia obciążenia.
> * Przetestuj moduł równoważenia obciążenia.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure.
- Dwie usługi równoważenia obciążenia platformy Azure w **warstwie Standardowa** z pulami zaplecza wdrożonymi w dwóch różnych regionach świadczenia usługi Azure.
    - Aby uzyskać informacje na temat tworzenia regionalnego standardowego modułu równoważenia obciążenia i maszyn wirtualnych dla pul zaplecza, zobacz [Szybki Start: Tworzenie publicznego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure](quickstart-load-balancer-standard-public-cli.md).
        - Dołącz nazwy modułów równoważenia obciążenia i maszyn wirtualnych w każdym regionie z **-R1** i **-R2**. 
- Interfejs wiersza polecenia platformy Azure został zainstalowany lokalnie lub Azure Cloud Shell.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Logowanie do interfejsu wiersza polecenia platformy Azure

Zaloguj się do interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>Tworzenie międzyregionowego modułu równoważenia obciążenia

W tej sekcji utworzysz międzyregionowy moduł równoważenia obciążenia, publiczny adres IP i regułę równoważenia obciążenia.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą [AZ Group Create](/cli/azure/group#az-group-create):

* Nazwana **myResourceGroupLB-CR**.
* W lokalizacji **zachodniej** .

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>Tworzenie zasobu modułu równoważenia obciążenia

Utwórz międzyregionowy moduł równoważenia obciążenia za pomocą [AZ Network Cross-region-lb Create](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create):

* Nazwana **myLoadBalancer-CR**.
* Pula frontonu o nazwie Moja **fronton — CR**.
* Pula zaplecza o nazwie **myBackEndPool-CR**.

```azurecli-interactive
  az network cross-region-lb create \
    --name myLoadBalancer-CR \
    --resource-group myResourceGroupLB-CR \
    --frontend-ip-name myFrontEnd-CR \
    --backend-pool-name myBackEndPool-CR     
```

### <a name="create-the-health-probe"></a>Tworzenie sondy kondycji

Utwórz sondę kondycji modułu równoważenia obciążenia dla wielu regionów przy użyciu [AZ Network Cross-region lb Probe Create](/cli/azure/network/cross-region-lb/probe#az_network_cross_region_lb_probe_create):

* Nazwana **myHealthProbe-CR**.
* Protokół **TCP**.
* Port **80**.

```azurecli-interactive
  az network cross-region lb probe create \
    --lb-name myLoadBalancer-CR \
    --name myHealthProbe-CR \
    --port 80 \
    --protocol Tcp \
    --resource-group myResourceGroupLB-CR
```

### <a name="create-the-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje:

* Konfiguracja adresu IP frontonu dla ruchu przychodzącego.
* Pula adresów IP zaplecza do odbierania ruchu sieciowego.
* Wymagany port źródłowy i docelowy. 

Utwórz regułę modułu równoważenia obciążenia za pomocą elementu [AZ Network Cross-region-lb Rule Create](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create):

* O nazwie **myHTTPRule-CR**
* Nasłuchiwanie na **porcie 80** w puli frontonu **— CR**.
* Wysyłanie ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza **myBackEndPool-CR** przy użyciu **portu 80**. 
* Protokół **TCP**.

```azurecli-interactive
  az network cross-region-lb rule create \
    --backend-port 80 \
    --frontend-port 80 \
    --lb-name myLoadBalancer-CR \
    --name myHTTPRule-CR \
    --protocol tcp \
    --resource-group myResourceGroupLB-CR \
    --backend-pool-name myBackEndPool-CR \
    --frontend-ip-name myFrontEnd-CR \
    --probe-name myHealthProbe-CR
```

## <a name="create-backend-pool"></a>Tworzenie puli zaplecza

W tej sekcji dodasz dwa regionalne standardowe usługi równoważenia obciążenia do puli zaplecza modułu równoważenia obciążenia między regionami.

> [!IMPORTANT]
> Aby wykonać te kroki, należy się upewnić, że w subskrypcji wdrożono dwa regionalne moduły równoważenia obciążenia z pulami zaplecza.  Aby uzyskać więcej informacji, zobacz **[Przewodnik Szybki Start: Tworzenie publicznego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure](quickstart-load-balancer-standard-public-cli.md)**.

### <a name="add-the-regional-frontends-to-load-balancer"></a>Dodawanie regionalnych frontonów do modułu równoważenia obciążenia

W tej sekcji umieścisz identyfikatory zasobów dwóch frontonów modułów równoważenia obciążenia na zmienne.  Następnie użyjesz zmiennych, aby dodać frontony do puli adresów zaplecza modułu równoważenia obciążenia między regionami.

Pobierz identyfikatory zasobów przy użyciu [AZ Network lb frontonu-IP show](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show).

Użyj [AZ Network Cross-inregion-lb Address — adres puli Dodaj](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) , aby dodać frontony umieszczane w zmiennych w puli zaplecza modułu równoważenia obciążenia między regionami:

```azurecli-interactive
  region1id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R1 \
    --name myFrontEnd-R1 \
    --resource-group CreatePubLBQS-rg-r1 \
    --query id \
    --output tsv)

  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region1id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R1 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR

  region2id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R2 \
    --name myFrontEnd-R2 \
    --resource-group CreatePubLBQS-rg-r2 \
    --query id \
    --output tsv)
  
  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region2id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R2 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR
```

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

W tej sekcji przetestujesz moduł równoważenia obciążenia między regionami. Nastąpi połączenie z publicznym adresem IP w przeglądarce internetowej.  Zatrzymasz maszyny wirtualne w jednym z pul zaplecza modułu równoważenia obciążenia i obserwuj przełączenia w tryb failover.

1. Aby uzyskać publiczny adres IP modułu równoważenia obciążenia, użyj [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show):

    ```azurecli-interactive
      az network public-ip show \
        --resource-group myResourceGroupLB-CR \
        --name PublicIPmyLoadBalancer-CR \
        --query ipAddress \
        --output tsv
    ```
2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

3. Zatrzymaj maszyny wirtualne w puli zaplecza jednego z regionalnych modułów równoważenia obciążenia.

4. Odśwież przeglądarkę internetową i zaobserwuj tryb failover połączenia z innym regionalnym modułem równoważenia obciążenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, użyj polecenia [AZ Group Delete](/cli/azure/group#az-group-delete) , aby usunąć grupę zasobów, moduł równoważenia obciążenia i wszystkie powiązane zasoby.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB-CR
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

* Utworzono moduł równoważenia obciążenia między regionami.
* Utworzono sondę kondycji.
* Utworzono regułę równoważenia obciążenia.
* Dodano regionalne moduły równoważenia obciążenia do puli zaplecza międzyregionowego modułu równoważenia obciążenia.
* Przetestowano moduł równoważenia obciążenia.

Przejdź do następnego artykułu, aby dowiedzieć się, jak to zrobić...
> [!div class="nextstepaction"]
> [Równoważenie obciążenia maszyn wirtualnych w różnych strefach dostępności](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
