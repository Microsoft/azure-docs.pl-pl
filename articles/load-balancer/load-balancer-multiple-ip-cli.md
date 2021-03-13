---
title: Równoważenie obciążenia dla wielu konfiguracji adresów IP przy użyciu interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak przypisać wiele adresów IP do maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: asudbring
ms.custom: seodec18, devx-track-azurecli
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: 67f3ffa15b46eec9f352e08b0fe7c8b1bb15ac7e
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103417794"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-azure-cli"></a>Równoważenie obciążenia dla wielu konfiguracji adresów IP przy użyciu interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [Interfejs wiersza polecenia](load-balancer-multiple-ip-cli.md)
> * [Program PowerShell](load-balancer-multiple-ip-powershell.md)

W tym artykule opisano, jak używać Azure Load Balancer z wieloma adresami IP w dodatkowym interfejsie sieciowym. W tym scenariuszu istnieją dwie maszyny wirtualne z systemem Windows, z których każdy ma podstawową i pomocniczą kartę sieciową. Każda z pomocniczych kart sieciowych ma dwie konfiguracje IP. Każda maszyna wirtualna hostuje zarówno witryny sieci Web contoso.com, jak i fabrikam.com. Każda witryna sieci Web jest powiązana z jedną z konfiguracji protokołu IP na pomocniczej karcie sieciowej. Używamy Azure Load Balancer, aby uwidocznić dwa adresy IP frontonu, jeden dla każdej witryny sieci Web, aby dystrybuować ruch do odpowiedniej konfiguracji protokołu IP dla witryny sieci Web. W tym scenariuszu jest stosowany ten sam numer portu dla obu frontonów, a także adresy IP puli zaplecza.

![Obraz scenariusza LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Procedura równoważenia obciążenia dla wielu konfiguracji adresów IP

Aby osiągnąć scenariusz opisany w tym artykule, wykonaj następujące czynności:

1. [Zainstaluj i skonfiguruj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) , postępując zgodnie z instrukcjami w połączonym artykule i zaloguj się na koncie platformy Azure.
2. [Utwórz grupę zasobów](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-group) o nazwie *contosofabrikam* w następujący sposób:

    ```azurecli
    az group create contosofabrikam westcentralus
    ```

3. [Utwórz zestaw dostępności](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) dla dwóch maszyn wirtualnych. W tym scenariuszu Użyj następującego polecenia:

    ```azurecli
    az vm availability-set create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Utwórz sieć wirtualną](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) o nazwie *myVNet* oraz podsieć o nazwie Moja *podsieć*:

    ```azurecli
    az network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus --subnet-name MySubnet --subnet-prefix 10.0.0.0/24

    ```

5. [Utwórz moduł równoważenia obciążenia](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o nazwie *mylb*:

    ```azurecli
    az network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Utwórz dwa dynamiczne publiczne adresy IP dla konfiguracji adresu IP frontonu dla modułu równoważenia obciążenia:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. Utwórz odpowiednio dwie konfiguracje IP frontonu, *contosofe* i *fabrikamfe* :

    ```azurecli
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Utwórz pule adresów zaplecza — *contosopool* i *fabrikampool*, [sondy](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  -  *http* i reguły równoważenia obciążenia — *HTTPc* i *HTTPf*:

    ```azurecli
    az network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    az network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Sprawdź dane wyjściowe, aby [sprawdzić, czy moduł równoważenia obciążenia](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) został utworzony prawidłowo, uruchamiając następujące polecenie:

    ```azurecli
    az network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Utwórz publiczny adres IP](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address), *myPublicIp* i [konto magazynu](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json), *mystorageaccont1* dla pierwszej maszyny wirtualnej VM1 w następujący sposób:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [Utwórz interfejsy sieciowe](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-nic) dla VM1 i Dodaj drugą konfigurację adresu IP, *VM1-ipconfig2* i [Utwórz maszynę wirtualną](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-vm) w następujący sposób:

    ```azurecli
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic1 --ip-config-name NIC1-ipconfig1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic2 --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM1Nic2 --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-names VM1Nic1,VM1Nic2  --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. Powtórz kroki 10-11 dla drugiej maszyny wirtualnej:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount2
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM2Nic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-names VM2Nic1,VM2Nic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. Na koniec należy skonfigurować rekordy zasobów DNS w taki sposób, aby wskazywały odpowiedni adres IP frontonu Load Balancer. Domeny mogą być hostowane w Azure DNS. Aby uzyskać więcej informacji o korzystaniu z Azure DNS z Load Balancer, zobacz [używanie Azure DNS z innymi usługami platformy Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat sposobu łączenia usług równoważenia obciążenia na platformie Azure [przy użyciu usług równoważenia obciążenia na platformie Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Dowiedz się, jak za pomocą różnych typów dzienników na platformie Azure zarządzać usługą równoważenia obciążenia i rozwiązywać problemy z usługą [log Analytics dla Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).