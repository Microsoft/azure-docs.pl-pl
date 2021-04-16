---
title: 'Samouczek: tworzenie bramy nat — interfejs wiersza polecenia platformy Azure'
titlesuffix: Azure Virtual Network NAT
description: Wprowadzenie do tworzenia bramy nat przy użyciu interfejsu wiersza polecenia platformy Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: d312702f441cfe2ad94e347cadcdfc88d4cc2a72
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479325"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-cli"></a>Samouczek: tworzenie bramy nat przy użyciu interfejsu wiersza polecenia platformy Azure

W tym samouczku pokazano, jak używać Translator adresów sieciowych usługi Azure Virtual Network service. Utworzysz bramę nat w celu zapewnienia łączności wychodzącej dla maszyny wirtualnej na platformie Azure. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz sieć wirtualną.
> * Tworzy maszynę wirtualną.
> * Utwórz bramę nat i skojarz ją z siecią wirtualną.
> * Połącz się z maszyną wirtualną i sprawdź adres IP nat.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o **nazwie myResourceGroupNAT** w **lokalizacji eastus2:**

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Tworzenie bramy nat

W tej sekcji utworzymy bramę nat i zasoby obsługi.

### <a name="create-public-ip-address"></a>Tworzenie publicznego adresu IP

Aby uzyskać dostęp do Internetu, potrzebujesz co najmniej jednego publicznego adresu IP dla bramy nat. Użyj [az network public-ip create,](/cli/azure/network/public-ip#az_network_public_ip_create) aby utworzyć zasób publicznego adresu IP o nazwie **myPublicIP** w **grupie zasobów myResourceGroupNAT.** 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard \
    --allocation static
```

### <a name="create-nat-gateway-resource"></a>Tworzenie zasobu bramy nat

Utwórz globalną bramę azure NAT za pomocą [az network nat gateway create](/cli/azure/network/nat#az_network_nat_gateway_create). Wynik tego polecenia spowoduje utworzenie zasobu bramy o nazwie **myNATgateway,** który używa publicznego adresu IP **myPublicIP.** Limit czasu bezczynności jest ustawiony na 10 minut.  

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --idle-timeout 10       
  ```

### <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną o nazwie **myVnet z** podsiecią o nazwie **mySubnet** [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) w grupie zasobów **myResourceGroup.** Przestrzeń adresowa IP sieci wirtualnej to **10.1.0.0/16.** Podsieć w sieci wirtualnej to **10.1.0.0/24.**

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.1.0.0/24
```

### <a name="create-bastion-host"></a>Tworzenie hosta bastionu

Utwórz hosta Azure Bastion o nazwie **myBastionHost,** aby uzyskać dostęp do maszyny wirtualnej. 

Użyj [az network vnet subnet create,](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) aby utworzyć Azure Bastion podsieci.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroupNAT \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

Utwórz publiczny adres IP hosta bastionu za pomocą az [network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). 

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myBastionIP \
    --sku Standard
```

Użyj [az network bastion create,](/cli/azure/network/bastion#az-network-bastion-create) aby utworzyć hosta bastionu. 

```azurecli-interactive
az network bastion create \
    --resource-group myResourceGroupNAT \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus2
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurowanie usługi NAT dla podsieci źródłowej

Skonfigurujemy źródłową **podsieć mySubnet** w sieci wirtualnej **myVnet** do używania określonego zasobu bramy nat **myNATgateway** za pomocą az [network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). To polecenie aktywuje usługę NAT w określonej podsieci.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Cały ruch wychodzący do miejsc docelowych w Internecie korzysta teraz z bramy nat.  Nie trzeba konfigurować sieci UDR.


## <a name="virtual-machine"></a>Maszyna wirtualna

W tej sekcji utworzysz maszynę wirtualną, aby przetestować bramę nat w celu zweryfikowania publicznego adresu IP połączenia wychodzącego.

Utwórz maszynę wirtualną za pomocą [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
    --name myVM \
    --resource-group myResourceGroupNAT  \
    --admin-username azureuser \
    --image win2019datacenter \
    --public-ip-address "" \
    --subnet mySubnet \
    --vnet-name myVNet
```

Przed przejściem do następnej sekcji poczekaj na ukończenie tworzenia maszyny wirtualnej.

## <a name="test-nat-gateway"></a>Testowanie bramy nat

W tej sekcji przetestujemy bramę nat. Najpierw odkryjemy publiczny adres IP bramy nat. Następnie połączymy się z testową maszyną wirtualną i zweryfikujemy połączenie wychodzące za pośrednictwem bramy nat.
    
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Znajdź publiczny adres IP bramy nat na **ekranie** Przegląd. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby,** a następnie wybierz **pozycję myPublicIP.**

2. Zanotuj publiczny adres IP:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Odnajdywanie publicznego adresu IP bramy nat" border="true":::

3. Wybierz **pozycję** Wszystkie usługi w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby,** a następnie z listy zasobów wybierz pozycję **myVM** znajdującą się w grupie zasobów **myResourceGroupNAT.**

4. Na stronie **Przegląd** wybierz pozycję **Połącz,** a następnie **pozycję Bastion.**

5. Wybierz niebieski przycisk **Użyj bastionu.**

6. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

7. Otwórz **Internet Explorer** na **myTestVM.**

8. Wprowadź **https://whatsmyip.com** na pasku adresu.

9. Sprawdź, czy wyświetlony adres IP odpowiada adresowi bramy adresów sieciowych zanotonym w poprzednim kroku:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer wyświetlanie zewnętrznego wychodzącego adresu IP" border="true":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń sieć wirtualną, maszynę wirtualną i bramę nat. W tym celu należy wykonać następujące czynności:

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na Translator adresów sieciowych usługi Azure Virtual Network, zobacz:
> [!div class="nextstepaction"]
> [Translator adresów sieciowych usługi Virtual Network omówienie](nat-overview.md)
