---
title: 'Samouczek: tworzenie usługi równoważenia obciążenia między regionami przy użyciu interfejsu wiersza polecenia platformy Azure'
titleSuffix: Azure Load Balancer
description: Rozpoczynanie pracy z tym samouczkiem w celu wdrożenia aplikacji między regionami przy Azure Load Balancer interfejsu wiersza polecenia platformy Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: ca4134ff25dc9915f256b5a7bdd9404021b60a8e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791917"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>Samouczek: tworzenie aplikacji między regionami przy Azure Load Balancer interfejsu wiersza polecenia platformy Azure

Usługa równoważenia obciążenia między regionami zapewnia, że usługa jest dostępna globalnie w wielu regionach świadczenia usługi Azure. Jeśli jeden region ulegnie awarii, ruch jest przekierowyny do następnego regionu równoważenia obciążenia o najbliższej dobrej kondycji.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz między regionami równoważenia obciążenia.
> * Utwórz regułę modułu równoważenia obciążenia.
> * Utwórz pulę zaplecza zawierającą dwa regionalne równoważenia obciążenia.
> * Przetestuj równoważenie obciążenia.

Jeśli nie masz subskrypcji platformy Azure, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem utwórz bezpłatne konto.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure.
- Dwie **standardowe sku** usługi Azure Load Balancer z pulami zaplecza wdrożonych w dwóch różnych regionach świadczenia usługi Azure.
    - Aby uzyskać informacje na temat tworzenia regionalnego standardowego równoważenia obciążenia i maszyn wirtualnych dla pul zaplecza, zobacz Szybki start: tworzenie publicznego równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu interfejsu wiersza [polecenia platformy Azure.](quickstart-load-balancer-standard-public-cli.md)
        - Dołącz nazwę usługi równoważenia obciążenia i maszyn wirtualnych w każdym regionie za pomocą **-R1** i **-R2.** 
- Interfejs wiersza polecenia platformy Azure zainstalowany lokalnie lub Azure Cloud Shell.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i używać go lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Logowanie do interfejsu wiersza polecenia platformy Azure

Zaloguj się do interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>Tworzenie między regionami równoważenia obciążenia

W tej sekcji utworzysz między regionami usługę równoważenia obciążenia, publiczny adres IP i regułę równoważenia obciążenia.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą [az group create](/cli/azure/group#az_group_create):

* O **nazwie myResourceGroupLB-CR.**
* W lokalizacji **westus.**

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>Tworzenie zasobu równoważenia obciążenia

Utwórz usługę równoważenia obciążenia między regionami za pomocą [az network cross-region-lb create:](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create)

* Nosi **nazwę myLoadBalancer-CR.**
* Pula frontendu o **nazwie myFrontEnd-CR.**
* Pula zaplecza o nazwie **myBackEndPool-CR.**

```azurecli-interactive
  az network cross-region-lb create \
    --name myLoadBalancer-CR \
    --resource-group myResourceGroupLB-CR \
    --frontend-ip-name myFrontEnd-CR \
    --backend-pool-name myBackEndPool-CR     
```

### <a name="create-the-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła równoważenia obciążenia definiuje:

* Konfiguracja adresu IP frontonia dla ruchu przychodzącego.
* Pula adresów IP zaplecza do odbierania ruchu.
* Wymagany port źródłowy i docelowy. 

Utwórz regułę usługi równoważenia obciążenia za pomocą [az network cross-region-lb rule create](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create):

* O **nazwie myHTTPRule-CR**
* Nasłuchuje **na porcie 80** w puli frontonie **myFrontEnd-CR.**
* Wysyłanie ruchu sieciowego ze zrównoważonym obciążeniem do puli adresów zaplecza **myBackEndPool-CR** przy **użyciu portu 80.** 
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
    --frontend-ip-name myFrontEnd-CR
```

## <a name="create-backend-pool"></a>Tworzenie puli zaplecza

W tej sekcji dodasz dwa regionalne standardowe równoważenia obciążenia do puli zaplecza między regionami.

> [!IMPORTANT]
> Aby wykonać te kroki, upewnij się, że w twojej subskrypcji zostały wdrożone dwa regionalne usługi równoważenia obciążenia z pulami zaplecza.  Aby uzyskać więcej informacji, zobacz Szybki start: tworzenie publicznego równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu interfejsu **[wiersza polecenia platformy Azure.](quickstart-load-balancer-standard-public-cli.md)**

### <a name="add-the-regional-frontends-to-load-balancer"></a>Dodawanie regionalnych frontendów do równoważenia obciążenia

W tej sekcji umieść identyfikatory zasobów dwóch frontoników regionalnych równoważenia obciążenia w zmiennych.  Następnie użyjesz tych zmiennych, aby dodać frontonie do puli adresów zaplecza między regionami równoważenia obciążenia.

Pobierz identyfikatory zasobów za pomocą [az network lb frontend-ip show](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show).

Użyj [az network cross-region-lb address-pool address add,](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) aby dodać frontony umieszczone w zmiennych w puli zaplecza usługi równoważenia obciążenia między regionami:

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

W tej sekcji przetestujemy między regionami równoważenia obciążenia. Połączysz się z publicznym adresem IP w przeglądarce internetowej.  Zatrzymasz maszyny wirtualne w jednej z regionalnych pul zaplecza usługi równoważenia obciążenia i zobaczysz, jak jest to używane w przypadku trybu failover.

1. Aby uzyskać publiczny adres IP usługi równoważenia obciążenia, użyj [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show):

    ```azurecli-interactive
      az network public-ip show \
        --resource-group myResourceGroupLB-CR \
        --name PublicIPmyLoadBalancer-CR \
        --query ipAddress \
        --output tsv
    ```
2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

3. Zatrzymaj maszyny wirtualne w puli zaplecza jednego z regionalnych równoważenia obciążenia.

4. Odśwież przeglądarkę internetową i obserwuj, jak połączenie jest w stanie failover z innym regionalnym usługą równoważenia obciążenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, równoważenie obciążenia i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je za pomocą polecenia [az group](/cli/azure/group#az_group_delete) delete.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB-CR
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

* Utworzono między regionami równoważenie obciążenia.
* Utworzono regułę równoważenia obciążenia.
* Dodano regionalne równoważenie obciążenia do puli zaplecza między regionami.
* Przetestowano równoważenie obciążenia.

Zapoznaj się z następnym artykułem, aby dowiedzieć się, jak...
> [!div class="nextstepaction"]
> [Równoważenie obciążenia maszyn wirtualnych w różnych strefach dostępności](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
