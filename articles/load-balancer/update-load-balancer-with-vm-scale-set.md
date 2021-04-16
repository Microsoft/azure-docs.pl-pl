---
title: Aktualizowanie lub usuwanie istniejącego równoważenia obciążenia używanego przez zestawy skalowania maszyn wirtualnych
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: W tym artykule z how-to-to-started with Azure usługa Load Balancer w warstwie Standardowa and virtual machine scale sets (Rozpoczynanie pracy z usługą Azure usługa Load Balancer w warstwie Standardowa zestawami skalowania maszyn wirtualnych).
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18, devx-track-azurecli
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: 2079eeb97ac935ba24c6ff0616a58fbb28a962f4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480090"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>Aktualizowanie lub usuwanie usługi równoważenia obciążenia używanej przez zestawy skalowania maszyn wirtualnych

Podczas pracy z zestawami skalowania maszyn wirtualnych i wystąpieniem Azure Load Balancer można:

- Dodawanie, aktualizowanie i usuwanie reguł.
- Dodaj konfiguracje.
- Usuń równoważenie obciążenia.

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Konfigurowanie usługi równoważenia obciążenia na celu skalowanie zestawów skalowania maszyn wirtualnych w górę

Upewnij się, że wystąpienie usługi Azure Load Balancer ma ustawioną pulę [nat](/cli/azure/network/lb/inbound-nat-pool) dla ruchu przychodzącego i że zestaw skalowania maszyn wirtualnych jest umieszczany w puli zaplecza usługi równoważenia obciążenia. Load Balancer automatycznie utworzy nowe reguły NAT dla ruchu przychodzącego w puli nat dla ruchu przychodzącego po dodaniu nowych wystąpień maszyn wirtualnych do zestawu skalowania maszyn wirtualnych.

Aby sprawdzić, czy pula nat dla ruchu przychodzącego jest prawidłowo ustawiona:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W menu po lewej stronie wybierz **pozycję Wszystkie zasoby.** Następnie wybierz **pozycję MyLoadBalancer** z listy zasobów.
1. W **obszarze Ustawienia** wybierz pozycję Reguły **NAT dla ruchu przychodzącego.** Jeśli w okienku po prawej stronie zostanie wyświetlona lista reguł utworzonych dla poszczególnych wystąpień w zestawie skalowania maszyn wirtualnych, wszystko będzie w stanie przejść do skalowania w górę w dowolnym momencie.

## <a name="add-inbound-nat-rules"></a>Dodawanie reguł NAT dla ruchu przychodzącego

Nie można dodać poszczególnych reguł NAT dla ruchu przychodzącego. Można jednak dodać zestaw reguł NAT dla ruchu przychodzącego ze zdefiniowanym zakresem portów frontu i portem frontony dla wszystkich wystąpień w zestawie skalowania maszyn wirtualnych.

Aby dodać cały zestaw reguł NAT dla ruchu przychodzącego dla zestawów skalowania maszyn wirtualnych, należy najpierw utworzyć pulę nat dla ruchu przychodzącego w równoważeniu obciążenia. Następnie odwołaj się do puli nat dla ruchu przychodzącego z profilu sieciowego zestawu skalowania maszyn wirtualnych. Zostanie przedstawiony pełny przykład użycia interfejsu wiersza polecenia.

Nowa pula nat dla ruchu przychodzącego nie powinna mieć nakładających się zakresów portów frontu z istniejącymi pulami nat dla ruchu przychodzącego. Aby wyświetlić istniejące pule nat dla ruchu przychodzącego, które są ustawione, użyj tego polecenia [interfejsu wiersza polecenia:](/cli/azure/network/lb/inbound-nat-pool#az_network_lb_inbound_nat_pool_list)
  
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
          -â€“instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>Aktualizowanie reguł NAT dla ruchu przychodzącego

Nie można zaktualizować poszczególnych reguł NAT dla ruchu przychodzącego. Można jednak zaktualizować zestaw reguł NAT dla ruchu przychodzącego przy użyciu zdefiniowanego zakresu portów frontony i portu serwera końcowego dla wszystkich wystąpień w zestawie skalowania maszyn wirtualnych.

Aby zaktualizować cały zestaw reguł NAT dla ruchu przychodzącego dla zestawów skalowania maszyn wirtualnych, zaktualizuj pulę nat dla ruchu przychodzącego w równoważeniu obciążenia.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>Usuwanie reguł NAT dla ruchu przychodzącego

Nie można usunąć poszczególnych reguł NAT dla ruchu przychodzącego, ale można usunąć cały zestaw reguł NAT dla ruchu przychodzącego, usuwając pulę NAT dla ruchu przychodzącego.

Aby usunąć pulę nat, najpierw usuń ją z zestawu skalowania. Pełny przykład użycia interfejsu wiersza polecenia pokazano tutaj:

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
       -â€“lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>Dodawanie wielu konfiguracji adresów IP

Aby dodać wiele konfiguracji adresów IP:

1. W menu po lewej stronie wybierz **pozycję Wszystkie zasoby.** Następnie wybierz **pozycję MyLoadBalancer** z listy zasobów.
1. W **obszarze Ustawienia** wybierz pozycję Konfiguracja adresu IP **frontony.** Następnie wybierz pozycję **Dodaj**.
1. Na stronie **Dodawanie adresu IP frontonia** wprowadź wartości i wybierz przycisk **OK.**
1. Wykonaj [kroki 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) [i 6 w](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) tym samouczku, jeśli są potrzebne nowe reguły równoważenia obciążenia.
1. W razie potrzeby utwórz nowy zestaw reguł NAT dla ruchu przychodzącego przy użyciu nowo utworzonych konfiguracji adresów IP frontonia. Przykład znajduje się w poprzedniej sekcji.

## <a name="multiple-virtual-machine-scale-sets-behind-a-single-load-balancer"></a>Wiele Virtual Machine Scale Sets za pojedynczym Load Balancer

Utwórz pulę nat dla ruchu przychodzącego na platformie Load Balancer, odwołaj się do puli nat dla ruchu przychodzącego w profilu sieciowym zestawu skalowania maszyn wirtualnych, a na koniec zaktualizuj wystąpienia, aby zmiany weszły w życie. Powtórz kroki dla wszystkich Virtual Machine Scale Sets.

Pamiętaj, aby utworzyć oddzielne pule nat dla ruchu przychodzącego z nienakładającymi się zakresami portów frontendu.
  
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
          -â€“instance-ids *
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
          -â€“instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS2
```

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>Usuń konfigurację adresu IP frontonia używaną przez zestaw skalowania maszyn wirtualnych

Aby usunąć konfigurację adresu IP frontonia używaną przez zestaw skalowania:

 1. Najpierw usuń pulę nat dla ruchu przychodzącego (zestaw reguł NAT dla ruchu przychodzącego), która odwołuje się do konfiguracji adresów IP frontonia. Instrukcje dotyczące usuwania reguł ruchu przychodzącego znajdują się w poprzedniej sekcji.
 1. Usuń regułę równoważenia obciążenia, która odwołuje się do konfiguracji adresu IP frontonia.
 1. Usuń konfigurację adresu IP frontonia.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>Usuwanie usługi równoważenia obciążenia używanej przez zestaw skalowania maszyn wirtualnych

Aby usunąć konfigurację adresu IP frontonia używaną przez zestaw skalowania:

 1. Najpierw usuń pulę nat dla ruchu przychodzącego (zestaw reguł NAT dla ruchu przychodzącego), która odwołuje się do konfiguracji adresów IP frontou. Instrukcje dotyczące usuwania reguł ruchu przychodzącego znajdują się w poprzedniej sekcji.
 1. Usuń regułę równoważenia obciążenia, która odwołuje się do puli back-end zawierającej zestaw skalowania maszyn wirtualnych.
 1. Usuń `loadBalancerBackendAddressPool` odwołanie z profilu sieciowego zestawu skalowania maszyn wirtualnych.
 
 Pełny przykład użycia interfejsu wiersza polecenia pokazano tutaj:

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
Na koniec usuń zasób równoważenia obciążenia.
 
## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej Azure Load Balancer i zestawów skalowania maszyn wirtualnych, przeczytaj więcej o pojęciach.

> [Azure Load Balancer z zestawami skalowania maszyn wirtualnych](load-balancer-standard-virtual-machine-scale-sets.md)
