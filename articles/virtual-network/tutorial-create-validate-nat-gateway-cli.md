---
title: Tworzenie i testowanie bramy translatora adresów sieciowych — interfejs wiersza polecenia platformy Azure
titlesuffix: Azure Virtual Network NAT
description: W tym samouczku pokazano, jak utworzyć bramę NAT przy użyciu interfejsu wiersza polecenia platformy Azure i przetestować usługę translatora adresów sieciowych
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7d4467e557105100fc32940c05fa349722689867
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88054378"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Samouczek: Tworzenie bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure i testowanie usługi translatora adresów sieciowych

W tym samouczku utworzysz bramę translatora adresów sieciowych, aby zapewnić łączność wychodzącą dla maszyn wirtualnych na platformie Azure. Aby przetestować bramę NAT, należy wdrożyć źródłową i docelową maszynę wirtualną. Przetestujesz bramę translatora adresów sieciowych, wykonując połączenia wychodzące z publicznym adresem IP. Te połączenia będą pochodzić z lokalizacji źródłowej dla docelowej maszyny wirtualnej. W tym samouczku przedstawiono lokalizację źródłową i docelową dwóch różnych sieci wirtualnych w tej samej grupie zasobów tylko dla uproszczenia.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ten samouczek można wykonać przy użyciu Azure Cloud Shell lub uruchomić odpowiednie polecenia lokalnie.  Jeśli Azure Cloud Shell nie zostały użyte, należy [zalogować się teraz](https://shell.azure.com).

Jeśli zdecydujesz się uruchomić te polecenia lokalnie, musisz zainstalować interfejs wiersza polecenia.  Ten samouczek wymaga korzystania z wersji interfejsu wiersza polecenia platformy Azure w wersji 2.0.71 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie **myResourceGroupNAT** w lokalizacji **eastus2** :

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
    
```

## <a name="create-the-nat-gateway"></a>Tworzenie bramy translatora adresów sieciowych

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Aby uzyskać dostęp do publicznej sieci Internet, wymagany jest co najmniej jeden publiczny adres IP dla bramy translatora adresów sieciowych. Użyj [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) , aby utworzyć zasób publicznego adresu IP o nazwie **myPublicIPsource** w **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
  
```

### <a name="create-a-public-ip-prefix"></a>Tworzenie publicznego prefiksu adresu IP

Można użyć co najmniej jednego publicznego zasobu adresów IP, publicznych prefiksów IP lub obu z bramą translatora adresów sieciowych. Do tego scenariusza zostanie dodany zasób prefiksu publicznego adresu IP.   Użyj [AZ Network Public-IP prefix Create](/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) , aby utworzyć zasób publicznego PREFIKSU adresu IP o nazwie **myPublicIPprefixsource** w **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
  
```

### <a name="create-a-nat-gateway-resource"></a>Tworzenie zasobu bramy NAT

W tej sekcji szczegółowo opisano, jak utworzyć i skonfigurować następujące składniki usługi NAT przy użyciu zasobu bramy translatora adresów sieciowych:
  - Publiczna Pula adresów IP i publiczny prefiks IP do użycia dla przepływów wychodzących przetłumaczonych przez zasób bramy translatora adresów sieciowych.
  - Zmień limit czasu bezczynności z wartości domyślnej wynoszącej 4 minuty na 10 minut.

Utwórz globalną bramę usługi Azure NAT za pomocą [AZ Network translator Gateway Create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) o nazwie **myNATgateway**. Polecenie używa publicznego adresu IP **myPublicIP** i publicznego prefiksu IP **myPublicIPprefix**. Polecenie zmienia również limit czasu bezczynności na 10 minut.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
    
  ```

W tym momencie Brama translatora adresów sieciowych jest funkcjonalna i nie ma potrzeby konfigurowania podsieci sieci wirtualnej.

## <a name="prepare-the-source-for-outbound-traffic"></a>Przygotuj Źródło dla ruchu wychodzącego

Przeprowadzimy Cię przez proces instalacji pełnego środowiska testowego. Należy skonfigurować test przy użyciu narzędzi typu "open source", aby zweryfikować bramę translatora adresów sieciowych. Zaczniemy od źródła, które będzie używać wcześniej utworzonej bramy translatora adresów sieciowych.

### <a name="configure-virtual-network-for-source"></a>Skonfiguruj sieć wirtualną dla źródła

Przed wdrożeniem maszyny wirtualnej i przetestowanie bramy translatora adresów sieciowych musimy utworzyć sieć wirtualną.

Utwórz sieć wirtualną o nazwie **myVnetsource** z podsiecią o nazwie **mySubnetsource** w **myResourceGroupNAT** za pomocą polecenia [AZ Network Microsoft Azure Virtual Network Create](https://docs.microsoft.com/cli/azure/network/vnet).  Przestrzeń adresów IP dla sieci wirtualnej to **192.168.0.0/16**. Podsieć w sieci wirtualnej to **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurowanie usługi translatora adresów sieciowych dla podsieci źródłowej

Skonfiguruj **mySubnetsource** źródłową podsieci w sieci wirtualnej **myVnetsource** , aby użyć określonego zasobu bramy translatora adresów sieciowych **myNATgateway** za pomocą elementu [AZ Network Microsoft Azure Virtual Network Subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet). To polecenie spowoduje Aktywowanie usługi translatora adresów sieciowych w określonej podsieci.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
    
```

Cały ruch wychodzący do miejsc docelowych w Internecie używa teraz usługi translatora adresów sieciowych.  Nie trzeba konfigurować elementu UDR.

Aby można było przetestować bramę NAT, musimy utworzyć źródłową maszynę wirtualną.  Przypiszemy zasób publicznego adresu IP jako publiczny adres IP na poziomie wystąpienia, aby uzyskać dostęp do tej maszyny wirtualnej z zewnątrz. Ten adres jest używany tylko w celu uzyskania dostępu do niego dla testu.  Pokazujemy, jak usługa translatora adresów sieciowych ma pierwszeństwo przed innymi opcjami wychodzącymi.

Możesz również utworzyć tę maszynę wirtualną bez publicznego adresu IP i utworzyć kolejną maszynę wirtualną do użycia jako serwera przesiadkowego bez publicznego adresu IP jako ćwiczenia.

### <a name="create-public-ip-for-source-vm"></a>Utwórz publiczny adres IP dla źródłowej maszyny wirtualnej

Tworzymy publiczny adres IP, który będzie używany do uzyskiwania dostępu do źródłowej maszyny wirtualnej. Użyj [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) , aby utworzyć zasób publicznego adresu IP o nazwie **myPublicIPsourceVM** w **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
    
```

### <a name="create-an-nsg-for-source-vm"></a>Tworzenie sieciowej grupy zabezpieczeń dla źródłowej maszyny wirtualnej

Ze względu na to, że standardowe publiczne adresy IP są "zabezpieczone domyślnie", musimy utworzyć sieciowej grupy zabezpieczeń, aby zezwolić na dostęp przychodzący do protokołu SSH.  Usługa translatora adresów sieciowych platformy Azure ma świadomość kierunku przepływu. Ten sieciowej grupy zabezpieczeń nie będzie używany do wychodzącego, gdy Brama translatora adresów sieciowych zostanie skonfigurowana w tej samej podsieci. Użyj [AZ Network sieciowej grupy zabezpieczeń Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) , aby utworzyć zasób sieciowej grupy zabezpieczeń o nazwie **myNSGsource** w **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
    
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Uwidacznianie punktu końcowego SSH na źródłowej maszynie wirtualnej

Utworzymy regułę w sieciowej grupy zabezpieczeń na potrzeby dostępu SSH do źródłowej maszyny wirtualnej. Użyj [AZ Network sieciowej grupy zabezpieczeń Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) , aby utworzyć regułę sieciowej grupy zabezpieczeń o nazwie **SSH**. Ta reguła zostanie utworzona w sieciowej grupy zabezpieczeń o nazwie **myNSGsource** w grupie zasobów **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="create-nic-for-source-vm"></a>Utwórz kartę sieciową dla źródłowej maszyny wirtualnej

Utwórz interfejs sieciowy za pomocą [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create) i skojarz z publicznym adresem IP i grupą zabezpieczeń sieci. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
    
```

### <a name="create-a-source-vm"></a>Tworzenie źródłowej maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą [AZ VM Create](/cli/azure/vm#az-vm-create).  Wygenerujemy klucze SSH dla tej maszyny wirtualnej i przechowują klucz prywatny do użycia później.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```

Gdy polecenie zostanie zwrócone natychmiast, wdrożenie maszyny wirtualnej może potrwać kilka minut.

## <a name="prepare-destination-for-outbound-traffic"></a>Przygotuj miejsce docelowe dla ruchu wychodzącego

Teraz utworzysz miejsce docelowe dla ruchu wychodzącego przetłumaczonego przez usługę NAT, aby umożliwić jego przetestowanie.

### <a name="configure-virtual-network-for-destination"></a>Skonfiguruj sieć wirtualną dla miejsca docelowego

 Musimy utworzyć sieć wirtualną, w której będzie docelowa maszyna wirtualna.  Te polecenia są takie same jak dla źródłowej maszyny wirtualnej z małymi zmianami w celu udostępnienia docelowego punktu końcowego.

Utwórz sieć wirtualną o nazwie **myVnetdestination** z podsiecią o nazwie **mySubnetdestination** w **myResourceGroupNAT** za pomocą polecenia [AZ Network Microsoft Azure Virtual Network Create](https://docs.microsoft.com/cli/azure/network/vnet).  Przestrzeń adresów IP dla sieci wirtualnej to **192.168.0.0/16**. Podsieć w sieci wirtualnej to **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="create-public-ip-for-destination-vm"></a>Utwórz publiczny adres IP dla docelowej maszyny wirtualnej

Tworzymy publiczny adres IP, który będzie używany do uzyskiwania dostępu do źródłowej maszyny wirtualnej. Użyj [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) , aby utworzyć zasób publicznego adresu IP o nazwie **myPublicIPdestinationVM** w **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard
  
```

### <a name="create-an-nsg-for-destination-vm"></a>Utwórz sieciowej grupy zabezpieczeń dla docelowej maszyny wirtualnej

Standardowe publiczne adresy IP są "zabezpieczone domyślnie", musisz utworzyć sieciowej grupy zabezpieczeń, aby zezwolić na dostęp przychodzący do protokołu SSH. Usługa Azure NAT jest ukierunkowana na kierunek przepływu. Ten sieciowej grupy zabezpieczeń nie będzie używany do wychodzącego, gdy Brama translatora adresów sieciowych zostanie skonfigurowana w tej samej podsieci. Użyj [AZ Network sieciowej grupy zabezpieczeń Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) , aby utworzyć zasób sieciowej grupy zabezpieczeń o nazwie **myNSGdestination** w **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination
    
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>Uwidacznianie punktu końcowego SSH na docelowej maszynie wirtualnej

Utworzymy regułę w sieciowej grupy zabezpieczeń na potrzeby dostępu SSH do docelowej maszyny wirtualnej. Użyj [AZ Network sieciowej grupy zabezpieczeń Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) , aby utworzyć regułę sieciowej grupy zabezpieczeń o nazwie **SSH**. Ta reguła zostanie utworzona w sieciowej grupy zabezpieczeń o nazwie **myNSGdestination** w grupie zasobów **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="expose-http-endpoint-on-destination-vm"></a>Uwidacznianie punktu końcowego HTTP na docelowej maszynie wirtualnej

Utworzymy regułę w sieciowej grupy zabezpieczeń na potrzeby dostępu HTTP do docelowej maszyny wirtualnej. Użyj [AZ Network sieciowej grupy zabezpieczeń Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) , aby utworzyć regułę sieciowej grupy zabezpieczeń o nazwie **http** w sieciowej grupy zabezpieczeń o nazwie **myNSGdestination** in **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
    
```

### <a name="create-nic-for-destination-vm"></a>Utwórz kartę sieciową dla docelowej maszyny wirtualnej

Utwórz interfejs sieciowy za pomocą [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create) i skojarz z publicznym adresem IP **myPublicIPdestinationVM** i sieciową grupą zabezpieczeń **myNSGdestination**. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination
    
```

### <a name="create-a-destination-vm"></a>Utwórz docelową maszynę wirtualną

Utwórz maszynę wirtualną za pomocą [AZ VM Create](/cli/azure/vm#az-vm-create).  Wygenerujemy klucze SSH dla tej maszyny wirtualnej i przechowują klucz prywatny do użycia później.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```
Gdy polecenie zostanie zwrócone natychmiast, wdrożenie maszyny wirtualnej może potrwać kilka minut.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Przygotowywanie serwera sieci Web i ładunku testowego na docelowej maszynie wirtualnej

Najpierw musimy odnaleźć adres IP docelowej maszyny wirtualnej.  Aby uzyskać publiczny adres IP docelowej maszyny wirtualnej, użyj polecenie [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do Notatnika, aby można było go użyć w kolejnych krokach. Wskaż, że jest to docelowa maszyna wirtualna.

### <a name="sign-in-to-destination-vm"></a>Zaloguj się na docelowej maszynie wirtualnej

Poświadczenia SSH powinny być przechowywane w Cloud Shell z poprzedniej operacji.  Otwórz [Azure Cloud Shell](https://shell.azure.com) w przeglądarce. Użyj adresu IP pobranego w poprzednim kroku do połączenia SSH z maszyną wirtualną. 

```bash
ssh <ip-address-destination>
```

Skopiuj i wklej następujące polecenia po zalogowaniu się.  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

Te polecenia zaktualizują maszynę wirtualną, instalują Nginx i tworzą plik 100-kilobajtów. Ten plik zostanie pobrany ze źródłowej maszyny wirtualnej przy użyciu usługi translatora adresów sieciowych.

Zamknij sesję SSH z docelową maszyną wirtualną.

## <a name="prepare-test-on-source-vm"></a>Przygotuj test na źródłowej maszynie wirtualnej

Najpierw musimy odnaleźć adres IP źródłowej maszyny wirtualnej.  Aby uzyskać publiczny adres IP źródłowej maszyny wirtualnej, użyj polecenie [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do Notatnika, aby można było go użyć w kolejnych krokach. Wskaż, że jest to źródłowa maszyna wirtualna.

### <a name="sign-in-to-source-vm"></a>Zaloguj się do źródłowej maszyny wirtualnej

Ponownie poświadczenia SSH są przechowywane w Cloud Shell. Otwórz nową kartę [Azure Cloud Shell](https://shell.azure.com) w przeglądarce.  Użyj adresu IP pobranego w poprzednim kroku do połączenia SSH z maszyną wirtualną. 

```bash
ssh <ip-address-source>
```

Skopiuj i wklej następujące polecenia, aby przygotować się do testowania usługi translatora adresów sieciowych.

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

To polecenie zaktualizuje maszynę wirtualną [, zainstaluje](https://github.com/rakyll/hey) go, zainstaluje z usługi GitHub i zaktualizuje środowisko powłoki.

Teraz można przystąpić do testowania usługi translatora adresów sieciowych.

## <a name="validate-nat-service"></a>Weryfikowanie usługi translatora adresów sieciowych

Po zalogowaniu się do źródłowej maszyny wirtualnej można użyć jej **zwinięcie** i **Hej** do generowania żądań na docelowy adres IP.

Użyj zwinięciea, aby pobrać plik 100-kilobajtów.  **\<ip-address-destination>** W poniższym przykładzie Zastąp wartość docelowym wcześniej skopiowanym adresem IP.  Parametr **--Output** wskazuje, że pobrany plik zostanie odrzucony.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Możesz również generować serię żądań za pomocą polecenia **Hej**. Ponownie Zastąp **\<ip-address-destination>** wartość docelowym adresem IP, który został wcześniej skopiowany.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

To polecenie spowoduje wygenerowanie 100 żądań, 10 współbieżnie, z limitem czasu wynoszącym 30 sekund. Połączenie TCP nie zostanie ponownie użyte.  Każde żądanie spowoduje pobranie 100 kilobajtów.  Na końcu przebiegu **zostanie** zaraportowana Statystyka dotycząca tego, jak dobrze była usługa translatora adresów sieciowych.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
  
```

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono bramę translatora adresów sieciowych, utworzono źródłową i docelową maszynę wirtualną, a następnie przetestowano bramę translatora adresów sieciowych.

Przejrzyj metryki w Azure Monitor, aby zobaczyć, jak działa usługa translatora adresów sieciowych. Diagnozuj problemy, takie jak wyczerpanie zasobów dostępnych portów.  Wyczerpanie zasobów portów protokołu IPSec jest łatwo rozwiązywane przez dodanie dodatkowych zasobów publicznego adresu IP lub publicznych zasobów prefiksu IP lub obu tych elementów.

- Dowiedz się więcej o [Virtual Network translatora adresów sieciowych](./nat-overview.md)
- Dowiedz się więcej o [zasobach bramy translatora adresów sieciowych](./nat-gateway-resource.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure](./quickstart-create-nat-gateway-cli.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu Azure Portal](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

