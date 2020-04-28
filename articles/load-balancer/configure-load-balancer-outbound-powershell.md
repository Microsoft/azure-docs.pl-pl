---
title: Konfigurowanie równoważenia obciążenia i reguł ruchu wychodzącego za pomocą Azure PowerShell
titleSuffix: Azure Load Balancer
description: W tym artykule pokazano, jak skonfigurować równoważenie obciążenia i reguły ruchu wychodzącego w usługa Load Balancer w warstwie Standardowa przy użyciu Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74225446"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Konfigurowanie równoważenia obciążenia i reguł ruchu wychodzącego w usługa Load Balancer w warstwie Standardowa przy użyciu Azure PowerShell

W tym artykule pokazano, jak skonfigurować reguły ruchu wychodzącego w usługa Load Balancer w warstwie Standardowa przy użyciu Azure PowerShell.  

Po zakończeniu scenariusza tego artykułu moduł równoważenia obciążenia zawiera dwa Frontony i skojarzone z nimi reguły. Masz jeden fronton dla ruchu przychodzącego i innego frontonu dla ruchu wychodzącego.  

Każdy fronton odwołuje się do publicznego adresu IP. W tym scenariuszu publiczny adres IP dla ruchu przychodzącego różni się od adresu ruchu wychodzącego.   Reguła równoważenia obciążenia umożliwia tylko Równoważenie obciążenia przychodzącego. Reguła ruchu wychodzącego steruje wychodzącym translatorem adresów sieciowych (NAT) dla maszyny wirtualnej.  

W scenariuszu są stosowane dwie pule zaplecza: jeden dla ruchu przychodzącego i jeden dla ruchu wychodzącego. Te pule ilustrują możliwości i zapewniają elastyczność dla scenariusza.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure
Zaloguj się do subskrypcji platformy Azure przy użyciu polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0). Następnie postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Grupa zasobów platformy Azure to logiczny kontener, w którym są wdrażane zasoby platformy Azure. Następnie zasoby są zarządzane przez grupę.

Poniższy przykład tworzy grupę zasobów o nazwie *myresourcegroupoutbound* w lokalizacji *eastus2* :

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną o nazwie *myvnetoutbound*. Nazwij jego podsieć *mysubnetoutbound*. Umieść go w *myresourcegroupoutbound* za pomocą polecenia [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) i [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0).

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Tworzenie publicznego adresu IP dla ruchu przychodzącego 

Aby uzyskać dostęp do aplikacji sieci Web w Internecie, potrzebny jest publiczny adres IP dla modułu równoważenia obciążenia. Usługa Load Balancer w warstwie Standardowa obsługuje tylko standardowe publiczne adresy IP. 

Użyj [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) , aby utworzyć standardowy publiczny adres IP o nazwie *mypublicipinbound* w *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Tworzenie wychodzącego publicznego adresu IP 

Utwórz standardowy adres IP dla konfiguracji wychodzącej frontonu modułu równoważenia obciążenia przy użyciu polecenia [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Tworzenie modułu równoważenia obciążenia na platformie Azure

W tej sekcji opisano sposób tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:
  - Adres IP frontonu, który odbiera przychodzący ruch sieciowy w module równoważenia obciążenia
  - Pula zaplecza, w której adres IP frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu
  - Pula zaplecza dla łączności wychodzącej
  - Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza
  - Reguła ruchu przychodzącego modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych
  - Reguła ruchu wychodzącego modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu z maszyn wirtualnych

### <a name="create-an-inbound-front-end-ip"></a>Tworzenie przychodzącego adresu IP frontonu
Utwórz przychodzącą konfigurację adresu IP frontonu dla modułu równoważenia obciążenia za pomocą polecenia [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Moduł równoważenia obciążenia powinien zawierać konfigurację przychodzącego adresu IP frontonu o nazwie *myfrontendinbound*. Skojarz tę konfigurację z publicznym adresem IP *mypublicipinbound*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Tworzenie wychodzącego adresu IP frontonu
Utwórz wychodzącą konfigurację adresu IP frontonu dla modułu równoważenia obciążenia za pomocą polecenia [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Ten moduł równoważenia obciążenia powinien obejmować wychodzącą konfigurację adresu IP frontonu o nazwie *myfrontendoutbound*. Skojarz tę konfigurację z publicznym adresem IP *mypublicipoutbound*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Tworzenie przychodzącej puli zaplecza
Utwórz pulę przychodzącą zaplecza dla modułu równoważenia obciążenia za pomocą polecenia [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Nazwij pulę *bepoolinbound*.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Tworzenie wychodzącej puli zaplecza
Użyj następującego polecenia, aby utworzyć kolejną pulę adresów zaplecza w celu zdefiniowania łączności wychodzącej dla puli maszyn wirtualnych przy użyciu polecenia [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Nazwij tę pulę *bepooloutbound*. 

Utworzenie oddzielnej puli wychodzącej zapewnia maksymalną elastyczność. Można jednak pominąć ten krok i użyć tylko *bepoolinbound* ruchu przychodzącego, jeśli wolisz.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji sprawdza wszystkie wystąpienia maszyn wirtualnych, aby upewnić się, że mogą wysyłać ruch sieciowy. Wystąpienie maszyny wirtualnej, które nie może sprawdzić sondy, zostanie usunięte z modułu równoważenia obciążenia do momentu powrotu do trybu online, a sprawdzenie sondy określa, że jest w dobrej kondycji. 

Aby monitorować kondycję maszyn wirtualnych, należy utworzyć sondę kondycji za pomocą polecenia [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0). 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje konfigurację adresów IP frontonu dla ruchu przychodzącego i pulę zaplecza do odbierania ruchu. Definiuje również wymagany port źródłowy i docelowy. 

Utwórz regułę modułu równoważenia obciążenia o nazwie *myinboundlbrule* przy użyciu polecenia [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0). Ta reguła nasłuchuje na porcie 80 w puli frontonu *myfrontendinbound*. Będzie również używany port 80 do wysyłania ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza *bepoolinbound*. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Ta zasada równoważenia obciążenia wyłącza automatyczne wychodzące bezpieczne NAT z powodu parametru **-DisableOutboundSNAT** . Ruch wychodzący NAT jest udostępniany tylko przez regułę wychodzącą.

### <a name="create-an-outbound-rule"></a>Tworzenie reguły dla ruchu wychodzącego

Reguła wychodząca definiuje publiczny adres IP frontonu, który jest reprezentowany przez *myfrontendoutbound*frontonu. Ten fronton zostanie użyty dla całego ruchu wychodzącego NAT, a także do puli zaplecza, do której zostanie zastosowana reguła.  

Użyj następującego polecenia, aby utworzyć regułę wychodzącą *myoutboundrule* na potrzeby translacji sieci wychodzącej wszystkich maszyn wirtualnych (w konfiguracjach protokołu IP kart sieciowych) w puli zaplecza *bepool* .  Polecenie zmienia limit czasu bezczynności wychodzącego z 4 na 15 minut. Przypisuje porty podrzędnego 10 000 zamiast 1 024. Aby uzyskać więcej informacji, zobacz polecenie [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0).

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Jeśli nie chcesz używać oddzielnej puli wychodzącej, możesz zmienić argument puli adresów w poprzednim poleceniu, aby określić *$bepoolin* .  Zalecamy użycie oddzielnych pul, aby zapewnić elastyczność i czytelność powstającej konfiguracji.

### <a name="create-a-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Użyj następującego polecenia, aby utworzyć moduł równoważenia obciążenia dla przychodzącego adresu IP za pomocą polecenia [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0). Nazwij moduł równoważenia obciążenia *lb*. Powinna zawierać przychodzącą konfigurację adresu IP frontonu. Jej Pula zaplecza *bepoolinbound* powinna być skojarzona z publicznym adresem IP *mypublicipinbound* utworzonym w poprzednim kroku.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

W tym momencie można kontynuować dodawanie maszyn wirtualnych do pul zaplecza *bepoolinbound* i *bepooloutbound* przez zaktualizowanie konfiguracji protokołu IP odpowiednich zasobów kart sieciowych. Zaktualizuj konfigurację zasobu za pomocą polecenia [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, moduł równoważenia obciążenia i powiązane zasoby nie są już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0).

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Następne kroki
W tym artykule opisano tworzenie standardowego modułu równoważenia obciążenia, skonfigurowanie reguł ruchu przychodzącego i wychodzącego równoważenia obciążenia oraz skonfigurowanie sondy kondycji dla maszyn wirtualnych w puli zaplecza. 

Aby dowiedzieć się więcej, przejdź do [samouczków dotyczących Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
