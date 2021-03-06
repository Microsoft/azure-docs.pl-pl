---
title: Aktualizowanie lub usuwanie istniejącego modułu równoważenia obciążenia używanego przez zestawy skalowania maszyn wirtualnych
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: Korzystając z tego artykułu, Rozpocznij pracę z usługą Azure usługa Load Balancer w warstwie Standardowa i zestawami skalowania maszyn wirtualnych.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: 52f2a2ed301bf734ad605a2ee68a0ab672a97014
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218727"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>Aktualizowanie lub usuwanie modułu równoważenia obciążenia używanego przez zestawy skalowania maszyn wirtualnych

Podczas pracy z zestawami skalowania maszyn wirtualnych i wystąpieniem Azure Load Balancer można:

- Dodawanie, aktualizowanie i usuwanie reguł.
- Dodaj konfiguracje.
- Usuń moduł równoważenia obciążenia.

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Konfigurowanie modułu równoważenia obciążenia do skalowania zestawów skalowania maszyn wirtualnych

Upewnij się, że wystąpienie Azure Load Balancer ma skonfigurowaną [pulę NAT dla ruchu przychodzącego](/cli/azure/network/lb/inbound-nat-pool) i że zestaw skalowania maszyn wirtualnych jest umieszczony w puli zaplecza modułu równoważenia obciążenia. Load Balancer automatycznie utworzy nowe reguły NAT dla ruchu przychodzącego w puli NAT dla ruchu przychodzącego, gdy nowe wystąpienia maszyn wirtualnych zostaną dodane do zestawu skalowania maszyn wirtualnych.

Aby sprawdzić, czy pula NAT dla ruchu przychodzącego jest prawidłowo skonfigurowana:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Z menu po lewej stronie wybierz pozycję **wszystkie zasoby**. Następnie wybierz pozycję **MyLoadBalancer** z listy zasobów.
1. W obszarze **Ustawienia** wybierz pozycję **reguły NAT dla ruchu przychodzącego**. W okienku po prawej stronie, jeśli zostanie wyświetlona lista reguł utworzonych dla każdego pojedynczego wystąpienia w zestawie skalowania maszyn wirtualnych, wszystko jest ustawione tak, aby można było skalować w górę w dowolnym momencie.

## <a name="add-inbound-nat-rules"></a>Dodawanie reguł NAT dla ruchu przychodzącego

Nie można dodać pojedynczych reguł NAT dla ruchu przychodzącego. Można jednak dodać zestaw reguł NAT dla ruchu przychodzącego ze zdefiniowanym zakresem portów frontonu i port zaplecza dla wszystkich wystąpień w zestawie skalowania maszyn wirtualnych.

Aby dodać cały zestaw reguł NAT ruchu przychodzącego dla zestawów skalowania maszyn wirtualnych, należy najpierw utworzyć pulę NAT dla ruchu przychodzącego w module równoważenia obciążenia. Następnie odwołują się do puli NAT dla ruchu przychodzącego z profilu sieciowego zestawu skalowania maszyn wirtualnych. Pokazano pełny przykład przy użyciu interfejsu wiersza polecenia.

Nowa pula NAT dla ruchu przychodzącego nie powinna mieć nakładających się zakresów portów frontonu z istniejącymi przychodzącymi pulami NAT. Aby wyświetlić istniejące pule NAT dla ruchu przychodzącego, które są skonfigurowane, użyj tego [polecenia CLI](/cli/azure/network/lb/inbound-nat-pool#az_network_lb_inbound_nat_pool_list):
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIp
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>Aktualizowanie reguł NAT dla ruchu przychodzącego

Nie można zaktualizować indywidualnych reguł NAT dla ruchu przychodzącego. Można jednak zaktualizować zestaw reguł NAT dla ruchu przychodzącego ze zdefiniowanym zakresem portów frontonu i port zaplecza dla wszystkich wystąpień w zestawie skalowania maszyn wirtualnych.

Aby zaktualizować cały zestaw reguł NAT ruchu przychodzącego dla zestawów skalowania maszyn wirtualnych, należy zaktualizować pulę NAT dla ruchu przychodzącego w module równoważenia obciążenia.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>Usuwanie reguł NAT dla ruchu przychodzącego

Nie można usunąć pojedynczych reguł NAT dla ruchu przychodzącego, ale można usunąć cały zestaw reguł NAT dla ruchu przychodzącego, usuwając pulę NAT dla ruchu przychodzącego.

Aby usunąć pulę NAT, najpierw usuń ją z zestawu skalowania. W tym miejscu pokazano pełny przykład użycia interfejsu wiersza polecenia:

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
     az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
    az network lb inbound-nat-pool delete
       --resource-group MyResourceGroup
       -–lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>Dodawanie wielu konfiguracji adresów IP

Aby dodać wiele konfiguracji adresów IP:

1. Z menu po lewej stronie wybierz pozycję **wszystkie zasoby**. Następnie wybierz pozycję **MyLoadBalancer** z listy zasobów.
1. W obszarze **Ustawienia** wybierz pozycję **Konfiguracja adresu IP frontonu**. Następnie wybierz pozycję **Dodaj**.
1. Na stronie **Dodawanie adresu IP frontonu** wprowadź wartości, a następnie wybierz **przycisk OK**.
1. Postępuj zgodnie z [krokami 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) i [6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) w tym samouczku, jeśli są potrzeby nowe reguły równoważenia obciążenia.
1. Utwórz nowy zestaw reguł NAT dla ruchu przychodzącego przy użyciu nowo utworzonych konfiguracji adresu IP frontonu w razie konieczności. Przykład znajduje się w poprzedniej sekcji.

## <a name="multiple-virtual-machine-scale-sets-behind-a-single-load-balancer"></a>Wiele Virtual Machine Scale Sets za pojedynczym Load Balancer

Utwórz pulę NAT dla ruchu przychodzącego w Load Balancer, odwołując się do puli NAT dla ruchu przychodzącego w profilu sieciowym zestawu skalowania maszyn wirtualnych, a wreszcie zaktualizuj wystąpienia zmian, które zaczną obowiązywać. Powtórz kroki dla wszystkich Virtual Machine Scale Sets.

Upewnij się, że utworzono oddzielne pule NAT dla ruchu przychodzącego z nienakładanymi zakresami portów frontonu.
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
          
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool2
          --protocol Tcp 
          --frontend-port-range-start 100 
          --frontend-port-range-end 109 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig2
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS2 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool2'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS2
```

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>Usuwanie konfiguracji adresu IP frontonu używanej przez zestaw skalowania maszyn wirtualnych

Aby usunąć konfigurację adresu IP frontonu używaną przez zestaw skalowania:

 1. Najpierw usuń pulę NAT dla ruchu przychodzącego (zestaw reguł NAT dla ruchu przychodzącego), która odwołuje się do konfiguracji adresu IP frontonu. Instrukcje dotyczące usuwania reguł ruchu przychodzącego znajdują się w poprzedniej sekcji.
 1. Usuń regułę równoważenia obciążenia odwołującą się do konfiguracji adresu IP frontonu.
 1. Usuń konfigurację adresu IP frontonu.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>Usuwanie modułu równoważenia obciążenia używanego przez zestaw skalowania maszyn wirtualnych

Aby usunąć konfigurację adresu IP frontonu używaną przez zestaw skalowania:

 1. Najpierw usuń pulę NAT dla ruchu przychodzącego (zestaw reguł NAT dla ruchu przychodzącego), która odwołuje się do konfiguracji adresu IP frontonu. Instrukcje dotyczące usuwania reguł ruchu przychodzącego znajdują się w poprzedniej sekcji.
 1. Usuń regułę równoważenia obciążenia odwołującą się do puli zaplecza zawierającej zestaw skalowania maszyn wirtualnych.
 1. Usuń `loadBalancerBackendAddressPool` odwołanie z profilu sieciowego zestawu skalowania maszyn wirtualnych.
 
 W tym miejscu pokazano pełny przykład użycia interfejsu wiersza polecenia:

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
    az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
```
Na koniec Usuń zasób usługi równoważenia obciążenia.
 
## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Load Balancer i zestawów skalowania maszyn wirtualnych, Przeczytaj więcej na temat koncepcji.

> [Azure Load Balancer z zestawami skalowania maszyn wirtualnych](load-balancer-standard-virtual-machine-scale-sets.md)
