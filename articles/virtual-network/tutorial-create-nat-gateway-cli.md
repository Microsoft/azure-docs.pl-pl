---
title: 'Samouczek: Tworzenie bramy translatora adresów sieciowych — interfejs wiersza polecenia platformy Azure'
titlesuffix: Azure Virtual Network NAT
description: Rozpocznij tworzenie bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 5dd431a5a7377c409be0794511c5f402d1c5a3a9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102663329"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-cli"></a>Samouczek: Tworzenie bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure

W tym samouczku przedstawiono sposób korzystania z usługi Azure Virtual Network translatora adresów sieciowych. Utworzysz bramę translatora adresów sieciowych, aby zapewnić łączność wychodzącą dla maszyny wirtualnej na platformie Azure. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz sieć wirtualną.
> * Tworzy maszynę wirtualną.
> * Utwórz bramę NAT i skojarz ją z siecią wirtualną.
> * Połącz się z maszyną wirtualną i Sprawdź adres IP translatora adresów sieciowych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten przewodnik Szybki Start wymaga wersji 2.0.28 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie **myResourceGroupNAT** w lokalizacji **eastus2** :

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Tworzenie bramy translatora adresów sieciowych

W tej sekcji utworzysz bramę translatora adresów sieciowych i pomocnicze zasoby.

### <a name="create-public-ip-address"></a>Tworzenie publicznego adresu IP

Aby uzyskać dostęp do Internetu, wymagany jest co najmniej jeden publiczny adres IP dla bramy translatora adresów sieciowych. Użyj [AZ Network Public-IP Create](/cli/azure/network/public-ip#az_network_public_ip_create) , aby utworzyć zasób publicznego adresu IP o nazwie **myPublicIP** w **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard \
    --allocation static
```

### <a name="create-nat-gateway-resource"></a>Utwórz zasób bramy translatora adresów sieciowych

Utwórz globalną bramę usługi Azure NAT za pomocą [AZ Network NAT Gateway Create](/cli/azure/network/nat#az_network_nat_gateway_create). Wynik tego polecenia spowoduje utworzenie zasobu bramy o nazwie **myNATgateway** , który korzysta z publicznego adresu IP **myPublicIP**. Limit czasu bezczynności jest ustawiony na 10 minut.  

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --idle-timeout 10       
  ```

### <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną o nazwie **myVnet** z podsiecią o nazwie Moja **Subnet** [AZ Network VNET Create](/cli/azure/network/vnet#az_network_vnet_create) w **grupie zasobów zasobu** . Przestrzeń adresów IP dla sieci wirtualnej to **10.1.0.0/16**. Podsieć w sieci wirtualnej to **10.1.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.1.0.0/24
```

### <a name="create-bastion-host"></a>Utwórz hosta bastionu

Utwórz hosta usługi Azure bastionu o nazwie **myBastionHost** w celu uzyskania dostępu do maszyny wirtualnej. 

Użyj [AZ Network VNET Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) , aby utworzyć podsieć usługi Azure bastionu.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroupNAT \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

Utwórz publiczny adres IP dla hosta bastionu za pomocą [AZ Network Public-IP Create](/cli/azure/network/public-ip#az_network_public_ip_create). 

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myBastionIP \
    --sku Standard
```

Użyj [AZ Network bastionu Create](/cli/azure/network/bastion#az-network-bastion-create) , aby utworzyć hosta bastionu. 

```azurecli-interactive
az network bastion create \
    --resource-group myResourceGroupNAT \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus2
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurowanie usługi translatora adresów sieciowych dla podsieci źródłowej

Skonfigurujemy dla podsieci źródłowej wartość **Websubnet** **myVnet** w sieci wirtualnej, aby użyć konkretnego zasobu bramy translatora adresów sieciowych **myNATgateway** przy użyciu elementu [AZ Network VNET Subnet Update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). To polecenie spowoduje Aktywowanie usługi translatora adresów sieciowych w określonej podsieci.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Cały ruch wychodzący do miejsc docelowych jest teraz używany przez bramę translatora adresów sieciowych.  Nie trzeba konfigurować elementu UDR.


## <a name="virtual-machine"></a>Maszyna wirtualna

W tej sekcji utworzysz maszynę wirtualną w celu przetestowania bramy translatora adresów sieciowych, aby zweryfikować publiczny adres IP połączenia wychodzącego.

Utwórz maszynę wirtualną za pomocą [AZ VM Create](/cli/azure/vm#az-vm-create).

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

Poczekaj na zakończenie tworzenia maszyny wirtualnej przed przejściem do następnej sekcji.

## <a name="test-nat-gateway"></a>Testowanie bramy translatora adresów sieciowych

W tej sekcji przetestujemy bramę translatora adresów sieciowych. Najpierw odnajdziemy publiczny adres IP bramy translatora adresów sieciowych. Następnie nastąpi połączenie z testową maszyną wirtualną i zweryfikowanie połączenia wychodzącego za pomocą bramy translatora adresów sieciowych.
    
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Znajdź publiczny adres IP dla bramy translatora adresów sieciowych na ekranie **Przegląd** . Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myPublicIP**.

2. Zanotuj publiczny adres IP:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Odnajdź publiczny adres IP bramy translatora adresów sieciowych" border="true":::

3. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów wybierz pozycję **myVM** , która znajduje się w grupie zasobów **myResourceGroupNAT** .

4. Na stronie **Przegląd** wybierz opcję **Połącz**, a następnie **bastionu**.

5. Wybierz przycisk **bastionu Użyj** niebieska.

6. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

7. Otwórz program **Internet Explorer** w systemie **myTestVM**.

8. Wprowadź **https://whatsmyip.com** na pasku adresu.

9. Sprawdź, czy adres IP wyświetlany jest zgodny z adresem bramy NAT zanotowanym w poprzednim kroku:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer pokazujący zewnętrzny adres IP dla ruchu wychodzącego" border="true":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń sieć wirtualną, maszynę wirtualną i bramę translatora adresów sieciowych, wykonując następujące czynności:

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Virtual Network translator adresów sieciowych, zobacz:
> [!div class="nextstepaction"]
> [Omówienie Virtual Network NAT](nat-overview.md)
