---
title: Aktualizowanie lub usuwanie istniejącej usługi Azure Load Balancer używanej przez zestaw skalowania maszyn wirtualnych
titleSuffix: Update or delete existing Azure Load Balancer used by Virtual Machine Scale Set
description: Korzystając z tego artykułu, Rozpocznij pracę z usługą Azure usługa Load Balancer w warstwie Standardowa i Virtual Machine Scale Sets.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/30/2020
ms.author: irenehua
ms.openlocfilehash: d5614490bfd2cfb67b6b7afd7b7b8643bbf754bd
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790093"
---
# <a name="how-to-updatedelete-azure-load-balancer-used-by-virtual-machine-scale-sets"></a>Jak aktualizować/usuwać Azure Load Balancer używane przez Virtual Machine Scale Sets

## <a name="how-to-set-up-azure-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Jak skonfigurować Azure Load Balancer skalowania w górę Virtual Machine Scale Sets
  * Upewnij się, że Load Balancer ma skonfigurowaną [pulę NAT dla ruchu przychodzącego](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) i że zestaw skalowania maszyn wirtualnych jest umieszczony w puli zaplecza Load Balancer. Azure Load Balancer automatycznie utworzy nowe reguły NAT dla ruchu przychodzącego w puli NAT dla ruchu przychodzącego, gdy nowe wystąpienia maszyn wirtualnych zostaną dodane do zestawu skalowania maszyn wirtualnych. 
  * Aby sprawdzić, czy pula NAT dla ruchu przychodzącego jest prawidłowo skonfigurowana, 
  1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
  
  1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie, a następnie wybierz pozycję **MyLoadBalancer** na liście zasobów.
  
  1. W obszarze **Ustawienia** wybierz pozycję **reguły NAT dla ruchu przychodzącego**.
Jeśli widzisz w okienku po prawej stronie, zostanie wyświetlona lista reguł utworzonych dla każdego pojedynczego wystąpienia w zestawie skalowania maszyn wirtualnych, a w każdym momencie zostanie ustawiona wartość "gotowe do skalowania w górę".

## <a name="how-to-add-inbound-nat-rules"></a>Jak dodać reguły NAT dla ruchu przychodzącego? 
  * Nie można dodać indywidualnej reguły NAT dla ruchu przychodzącego. Można jednak dodać zestaw reguł NAT dla ruchu przychodzącego ze zdefiniowanym zakresem portów frontonu i portem zaplecza dla wszystkich wystąpień w zestawie skalowania maszyn wirtualnych.
  * Aby dodać do Virtual Machine Scale Sets cały zestaw reguł NAT dla ruchu przychodzącego, należy najpierw utworzyć pulę NAT dla ruchu przychodzącego w Load Balancer, a następnie odwołać się do puli NAT dla ruchu przychodzącego z profilu sieciowego zestawu skalowania maszyn wirtualnych. Poniżej przedstawiono pełny przykład użycia interfejsu wiersza polecenia.
  * Nowa pula NAT dla ruchu przychodzącego nie powinna mieć nakładających się zakresów portów frontonu z istniejącymi przychodzącymi pulami NAT. Aby wyświetlić istniejące skonfigurowane pule NAT dla ruchu przychodzącego, możesz użyć tego [polecenia interfejsu CLI](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list)
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
## <a name="how-to-update-inbound-nat-rules"></a>Jak zaktualizować reguły NAT dla ruchu przychodzącego? 
  * Nie można zaktualizować indywidualnej reguły NAT dla ruchu przychodzącego. Można jednak zaktualizować zestaw reguł NAT dla ruchu przychodzącego ze zdefiniowanym zakresem portów frontonu i portem zaplecza dla wszystkich wystąpień w zestawie skalowania maszyn wirtualnych.
  * Aby zaktualizować dla Virtual Machine Scale Sets cały zestaw reguł NAT dla ruchu przychodzącego, należy zaktualizować pulę NAT dla ruchu przychodzącego w Load Balancer. 
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="how-to-delete-inbound-nat-rules"></a>Jak usunąć reguły NAT dla ruchu przychodzącego? 
* Nie można usunąć indywidualnej reguły NAT dla ruchu przychodzącego. Można jednak usunąć cały zestaw reguł NAT dla ruchu przychodzącego.
* Aby usunąć cały zestaw reguł NAT dla ruchu przychodzącego używany przez zestaw skalowania, należy najpierw usunąć pulę NAT z zestawu skalowania. Poniżej przedstawiono pełny przykład użycia interfejsu wiersza polecenia:
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
   az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```

## <a name="how-to-add-multiple-ip-configurations"></a>Jak dodać wiele konfiguracji adresów IP:
1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie, a następnie wybierz pozycję **MyLoadBalancer** na liście zasobów.
   
1. W obszarze **Ustawienia** wybierz pozycję **konfiguracje adresów IP frontonu**, a następnie wybierz pozycję **Dodaj**.
   
1. Na stronie **Dodawanie adresu IP frontonu** wpisz wartości, a następnie wybierz **przycisk OK** .

1. Postępuj zgodnie z [krokami 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) i [6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) w tym samouczku, jeśli są potrzeby nowe reguły równoważenia obciążenia

1. Utwórz nowy zestaw reguł NAT dla ruchu przychodzącego przy użyciu nowo utworzonych konfiguracji adresu IP frontonu w razie konieczności. Przykład można znaleźć w tym miejscu w [poprzedniej sekcji].

## <a name="how-to-delete-frontend-ip-configuration-used-by-virtual-machine-scale-set"></a>Jak usunąć konfigurację adresu IP frontonu używaną przez zestaw skalowania maszyn wirtualnych: 
 1. Aby usunąć konfigurację adresu IP frontonu używaną przez zestaw skalowania, należy najpierw usunąć pulę NAT dla ruchu przychodzącego (zestaw reguł NAT dla ruchu przychodzącego) odwołującą się do konfiguracji adresu IP frontonu. Instrukcje dotyczące sposobu usuwania reguł ruchu przychodzącego można znaleźć w poprzedniej sekcji.
 1. Usuń regułę równoważenia obciążenia odwołującą się do konfiguracji adresu IP frontonu. 
 1. Usuń konfigurację adresu IP frontonu.
 

## <a name="how-to-delete-azure-load-balancer-used-by-virtual-machine-scale-set"></a>Jak usunąć Azure Load Balancer używane przez zestaw skalowania maszyn wirtualnych: 
 1. Aby usunąć konfigurację adresu IP frontonu używaną przez zestaw skalowania, należy najpierw usunąć pulę NAT dla ruchu przychodzącego (zestaw reguł NAT dla ruchu przychodzącego) odwołującą się do konfiguracji adresu IP frontonu. Instrukcje dotyczące sposobu usuwania reguł ruchu przychodzącego można znaleźć w poprzedniej sekcji.
 
 1. Usuń regułę równoważenia obciążenia odwołującą się do puli zaplecza zawierającej zestaw skalowania maszyn wirtualnych.
 
 1. Usuń odwołanie loadBalancerBackendAddressPool z profilu sieciowego zestawu skalowania maszyn wirtualnych. Poniżej przedstawiono pełny przykład użycia interfejsu wiersza polecenia:
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
Na koniec Usuń zasób Load Balancer.
 
## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Load Balancer i zestawu skalowania maszyn wirtualnych, Przeczytaj więcej na temat koncepcji.

> [Azure Load Balancer z zestawami skalowania maszyn wirtualnych platformy Azure](load-balancer-standard-virtual-machine-scale-sets.md)