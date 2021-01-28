---
title: Tworzenie bramy translatora adresów sieciowych — interfejs wiersza polecenia platformy Azure
titlesuffix: Azure Virtual Network NAT
description: W tym przewodniku szybki start pokazano, jak utworzyć bramę NAT przy użyciu interfejsu wiersza polecenia platformy Azure
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8d14b8b83fd784956091e738a38d6851d5edacd9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927142"
---
# <a name="create-a-nat-gateway-using-azure-cli"></a>Tworzenie bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure

W tym samouczku przedstawiono sposób korzystania z usługi Azure Virtual Network translatora adresów sieciowych. Utworzysz bramę translatora adresów sieciowych, aby zapewnić łączność wychodzącą dla maszyny wirtualnej na platformie Azure. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.0.71 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie **myResourceGroupNAT** w lokalizacji **eastus2** :

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Tworzenie bramy translatora adresów sieciowych

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Aby uzyskać dostęp do publicznej sieci Internet, wymagany jest co najmniej jeden publiczny adres IP dla bramy translatora adresów sieciowych. Użyj [AZ Network Public-IP Create](/cli/azure/network/public-ip) , aby utworzyć zasób publicznego adresu IP o nazwie **myPublicIP** w **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Tworzenie publicznego prefiksu adresu IP

Można użyć co najmniej jednego publicznego zasobu adresów IP, publicznych prefiksów IP lub obu z bramą translatora adresów sieciowych. Do tego scenariusza zostanie dodany zasób prefiksu publicznego adresu IP.   Użyj [AZ Network Public-IP prefix Create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) , aby utworzyć zasób publicznego PREFIKSU adresu IP o nazwie **myPublicIPprefix** w **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Tworzenie zasobu bramy NAT

W tej sekcji szczegółowo opisano, jak utworzyć i skonfigurować następujące składniki usługi NAT przy użyciu zasobu bramy translatora adresów sieciowych:
  - Publiczna Pula adresów IP i publiczny prefiks IP do użycia dla przepływów wychodzących przetłumaczonych przez zasób bramy translatora adresów sieciowych.
  - Zmień limit czasu bezczynności z wartości domyślnej wynoszącej 4 minuty na 10 minut.

Utwórz globalną bramę usługi Azure NAT za pomocą [AZ Network translator Gateway Create](/cli/azure/network/nat) o nazwie **myNATgateway**. Polecenie używa publicznego adresu IP **myPublicIP** i publicznego prefiksu IP **myPublicIPprefix**. Polecenie zmienia limit czasu bezczynności na **10** minut.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

W tym momencie Brama translatora adresów sieciowych jest funkcjonalna i nie ma potrzeby konfigurowania podsieci sieci wirtualnej.

## <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej

Przed wdrożeniem maszyny wirtualnej i użyciem bramy translatora adresów sieciowych musimy utworzyć sieć wirtualną.

Utwórz sieć wirtualną o nazwie **myVnet** z podsiecią o nazwie Moja **podsieć** w **myResourceGroupNAT** przy użyciu polecenia [AZ Network VNET Create](/cli/azure/network/vnet).  Przestrzeń adresów IP dla sieci wirtualnej to **192.168.0.0/16**. Podsieć w sieci wirtualnej to **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurowanie usługi translatora adresów sieciowych dla podsieci źródłowej

Skonfigurujemy dla podsieci źródłowej wartość **Websubnet** **myVnet** w sieci wirtualnej, aby użyć konkretnego zasobu bramy translatora adresów sieciowych **myNATgateway** przy użyciu elementu [AZ Network VNET Subnet Update](/cli/azure/network/vnet/subnet).  To polecenie spowoduje Aktywowanie usługi translatora adresów sieciowych w określonej podsieci.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Cały ruch wychodzący do miejsc docelowych jest teraz używany przez bramę translatora adresów sieciowych.  Nie trzeba konfigurować elementu UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Tworzenie maszyny wirtualnej do korzystania z usługi translatora adresów sieciowych

Teraz utworzymy maszynę wirtualną do korzystania z usługi translatora adresów sieciowych.  Ta maszyna wirtualna ma publiczny adres IP do użycia jako publiczny adres IP na poziomie wystąpienia, który umożliwia dostęp do maszyny wirtualnej.  Usługa translatora adresów sieciowych obsługuje kierunek przepływu i zastępuje domyślne miejsce docelowe w podsieci. Publiczny adres IP maszyny wirtualnej nie będzie używany dla połączeń wychodzących.

### <a name="create-public-ip-for-source-vm"></a>Utwórz publiczny adres IP dla źródłowej maszyny wirtualnej

Tworzymy publiczny adres IP, który będzie używany do uzyskiwania dostępu do maszyny wirtualnej.  Użyj [AZ Network Public-IP Create](/cli/azure/network/public-ip) , aby utworzyć zasób publicznego adresu IP o nazwie **myPublicIPVM** w **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>Tworzenie sieciowej grupy zabezpieczeń dla maszyny wirtualnej

Ze względu na to, że standardowe publiczne adresy IP są "zabezpieczone domyślnie", musimy utworzyć sieciowej grupy zabezpieczeń, aby zezwolić na dostęp przychodzący do protokołu SSH. Użyj [AZ Network sieciowej grupy zabezpieczeń Create](/cli/azure/network/nsg#az-network-nsg-create) , aby utworzyć zasób sieciowej grupy zabezpieczeń o nazwie **myNSG** w **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Uwidacznianie punktu końcowego SSH na źródłowej maszynie wirtualnej

Utworzymy regułę w sieciowej grupy zabezpieczeń na potrzeby dostępu SSH do źródłowej maszyny wirtualnej. Użyj [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) , aby utworzyć regułę sieciowej grupy zabezpieczeń o nazwie **SSH** w sieciowej grupy zabezpieczeń o nazwie **myNSG** in **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>Utwórz kartę sieciową dla maszyny wirtualnej

Utwórz interfejs sieciowy za pomocą [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create) i skojarz z publicznym adresem IP i grupą zabezpieczeń sieci. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą [AZ VM Create](/cli/azure/vm#az-vm-create).  Wygenerujemy klucze SSH dla tej maszyny wirtualnej i przechowują klucz prywatny do użycia później.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Zaczekaj na wdrożenie maszyny wirtualnej, a następnie kontynuuj pozostałe kroki.

## <a name="discover-the-ip-address-of-the-vm"></a>Odnajdywanie adresu IP maszyny wirtualnej

Najpierw musimy odnaleźć adres IP utworzonej maszyny wirtualnej. Aby pobrać publiczny adres IP maszyny wirtualnej, użyj [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do Notatnika, aby można było używać go do uzyskiwania dostępu do maszyny wirtualnej.

### <a name="sign-in-to-vm"></a>Logowanie do maszyny wirtualnej

Poświadczenia SSH powinny być przechowywane w Cloud Shell z poprzedniej operacji.  Otwórz [Azure Cloud Shell](https://shell.azure.com) w przeglądarce. Użyj adresu IP pobranego w poprzednim kroku do połączenia SSH z maszyną wirtualną.

```bash
ssh <ip-address-destination>
```

Teraz można przystąpić do korzystania z usługi translatora adresów sieciowych.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono bramę translatora adresów sieciowych oraz maszynę wirtualną w celu jej użycia. 

Przejrzyj metryki w Azure Monitor, aby zobaczyć, jak działa usługa translatora adresów sieciowych. Diagnozuj problemy, takie jak wyczerpanie zasobów dostępnych portów.  Wyczerpanie zasobów portów źródeł adresów sieciowych polega na dodaniu dodatkowych zasobów publicznego adresu IP lub publicznych zasobów prefiksu adresów IP.


- Dowiedz się więcej o [usłudze Azure Virtual Network translator adresów sieciowych](./nat-overview.md)
- Dowiedz się więcej o [zasobach bramy translatora adresów sieciowych](./nat-gateway-resource.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure](./quickstart-create-nat-gateway-cli.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu Azure Portal](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]
