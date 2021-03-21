---
title: Tworzenie publicznego modułu równoważenia obciążenia przy użyciu protokołu IPv6 — interfejs wiersza polecenia platformy Azure
titleSuffix: Azure Load Balancer
description: Za pomocą tej ścieżki szkoleniowej Rozpocznij tworzenie publicznego modułu równoważenia obciążenia za pomocą protokołu IPv6 przy użyciu interfejsu wiersza polecenia platformy Azure.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, moduł równoważenia obciążenia platformy Azure, podwójny stos, publiczny adres IP, natywny protokół IPv6, urządzenia przenośne, IoT
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: 714fb92e8757fed6e11a09528b60cda3e945175e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218897"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Tworzenie publicznego modułu równoważenia obciążenia przy użyciu protokołu IPv6 za pomocą interfejsu wiersza polecenia platformy Azure

>[!NOTE] 
>W tym artykule opisano wstępną funkcję IPv6 umożliwiającą podstawowym usługom równoważenia obciążenia zapewnienie łączności protokołów IPv4 i IPv6. Kompleksowa łączność z protokołem IPv6 jest teraz dostępna przy użyciu [protokołu IPv6 dla usługi Azure sieci wirtualnych](../virtual-network/ipv6-overview.md) , która integruje łączność IPv6 z sieciami wirtualnymi i zawiera najważniejsze funkcje, takie jak reguły sieciowej grupy zabezpieczeń IPv6, routing zdefiniowany przez użytkownika IPv6, podstawowe i standardowe Równoważenie obciążenia.  Protokół IPv6 dla usługi Azure sieci wirtualnych jest zalecanym standardem dla aplikacji IPv6 na platformie Azure. Zobacz [IPv6 dla wdrożenia programu PowerShell sieci wirtualnej platformy Azure](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 

Usługa Azure Load Balancer to moduł równoważenia obciążenia w warstwie 4 (TCP, UDP). Usługi równoważenia obciążenia zapewniają wysoką dostępność przez dystrybuowanie ruchu przychodzącego między wystąpieniami usług w chmurze i maszyn wirtualnych w zestawie modułu równoważenia obciążenia. Moduły równoważenia obciążenia mogą również przedstawić te usługi na wielu portach lub wielu adresach IP.

## <a name="example-deployment-scenario"></a>Przykładowy scenariusz wdrażania

Na poniższym diagramie przedstawiono rozwiązanie równoważenia obciążenia, które zostało wdrożone przy użyciu przykładowego szablonu opisanego w tym artykule.

![Scenariusz modułu równoważenia obciążenia](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

W tym scenariuszu utworzysz następujące zasoby platformy Azure:

* Dwie maszyny wirtualne
* Interfejs sieci wirtualnej dla każdej maszyny wirtualnej z przypisanymi adresami IPv4 i IPv6
* Publiczny moduł równoważenia obciążenia z publicznym adresem IP IPv4 i IPv6
* Zestaw dostępności zawierający dwie maszyny wirtualne
* Dwie reguły równoważenia obciążenia służące do mapowania publicznych adresów VIP na prywatne punkty końcowe

## <a name="deploy-the-solution-by-using-azure-cli"></a>Wdrażanie rozwiązania przy użyciu interfejsu wiersza polecenia platformy Azure

Poniższe kroki pokazują, jak utworzyć publiczny moduł równoważenia obciążenia przy użyciu interfejsu wiersza polecenia platformy Azure. Za pomocą interfejsu wiersza polecenia tworzysz i konfigurujesz każdy obiekt indywidualnie, a następnie umieścisz je razem w celu utworzenia zasobu.

Aby wdrożyć moduł równoważenia obciążenia, należy utworzyć i skonfigurować następujące obiekty:

* **Konfiguracja adresu IP frontonu**: zawiera publiczne adresy IP dla przychodzącego ruchu sieciowego.
* **Pula adresów zaplecza**: zawiera interfejsy sieciowe (nic), dla których maszyny wirtualne mają odbierać ruch sieciowy z modułu równoważenia obciążenia.
* **Reguły równoważenia obciążenia**: zawiera reguły, które mapują port publiczny modułu równoważenia obciążenia na port w puli adresów zaplecza.
* **Reguły NAT dla ruchu przychodzącego**: zawierają reguły translacji adresów sieciowych (NAT), które mapują port publiczny modułu równoważenia obciążenia na port określonej maszyny wirtualnej w puli adresów zaplecza.
* **Sondy**: zawiera sondy kondycji, które są używane do sprawdzania dostępności wystąpień maszyn wirtualnych w puli adresów zaplecza.

## <a name="set-up-azure-cli"></a>Konfigurowanie interfejsu wiersza polecenia platformy Azure

W tym przykładzie uruchomiono narzędzia interfejsu wiersza polecenia platformy Azure w oknie poleceń programu PowerShell. Aby zwiększyć czytelność i ponowne użycie, należy użyć funkcji obsługi skryptów programu PowerShell, a nie poleceń cmdlet Azure PowerShell.

1. [Zainstaluj i skonfiguruj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) , wykonując czynności opisane w połączonym artykule i zaloguj się na koncie platformy Azure.

2. Skonfiguruj zmienne programu PowerShell do użycia z poleceniami interfejsu CLI platformy Azure:

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Tworzenie grupy zasobów, modułu równoważenia obciążenia, sieci wirtualnej i podsieci

1. Utwórz grupę zasobów:

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. Tworzenie modułu równoważenia obciążenia:

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Utwórz sieć wirtualną:

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. W tej sieci wirtualnej Utwórz dwie podsieci:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Tworzenie publicznych adresów IP dla puli frontonu

1. Skonfiguruj zmienne programu PowerShell:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Utwórz publiczny adres IP dla puli adresów IP frontonu:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > Moduł równoważenia obciążenia używa etykiety domeny publicznego adresu IP jako jego w pełni kwalifikowanej nazwy domeny (FQDN). Ta zmiana z wdrożenia klasycznego, która używa nazwy usługi w chmurze jako nazwy FQDN modułu równoważenia obciążenia.
    >
    > W tym przykładzie nazwa FQDN to *contoso09152016.southcentralus.cloudapp.Azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Tworzenie pul frontonu i zaplecza

W tej sekcji utworzysz następujące pule adresów IP:
* Pula adresów IP frontonu, która odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
* Pula adresów IP zaplecza, w której Pula frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.

1. Skonfiguruj zmienne programu PowerShell:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Utwórz pulę adresów IP frontonu i skojarz ją z publicznym adresem IP utworzonym w poprzednim kroku i module równoważenia obciążenia.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Tworzenie sondy, reguł translatora adresów sieciowych i reguł modułu równoważenia obciążenia

W tym przykładzie opisano tworzenie następujących elementów:

* Reguła sondy służąca do sprawdzania połączenia z portem TCP 80.
* Reguła NAT służąca do translacji całego ruchu przychodzącego na porcie 3389 do portu 3389 dla protokołu RDP.\*
* Reguła NAT służąca do translacji całego ruchu przychodzącego na porcie 3391 do portu 3389 dla protokołu RDP (Remote Desktop Protocol).\*
* Reguła modułu równoważenia obciążenia do równoważenia całego ruchu przychodzącego na porcie 80 do portu 80 adresów w puli zaplecza.

\* Reguły NAT są skojarzone z określonym wystąpieniem maszyny wirtualnej za modułem równoważenia obciążenia. Ruch sieciowy przychodzący na porcie 3389 jest wysyłany do określonej maszyny wirtualnej i portu skojarzonej z regułą NAT. Musisz określić protokół (UDP lub TCP) dla reguły NAT. Nie można przypisać obu protokołów do tego samego portu.

1. Skonfiguruj zmienne programu PowerShell:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Utwórz sondę.

    Poniższy przykład tworzy sondę TCP, która sprawdza łączność z portem TCP zaplecza 80 co 15 sekund. Po dwóch kolejnych awariach oznacza to, że zasób zaplecza jest niedostępny.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Utwórz reguły NAT dla ruchu przychodzącego, które zezwalają na połączenia RDP z zasobami zaplecza:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Utwórz reguły modułu równoważenia obciążenia, które wysyłają ruch do różnych portów zaplecza, w zależności od frontonu, który odebrał żądanie.

    ```azurecli
    $lbruleIPv4 = az network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = az network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Sprawdź ustawienia:

    ```azurecli
    az network lb show --resource-group $rgName --name $lbName
    ```

    Oczekiwane dane wyjściowe:

    ```output
    info:    Executing command network lb show
    info:    Looking up the load balancer "myIPv4IPv6Lb"
    data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
    data:    Name                            : myIPv4IPv6Lb
    data:    Type                            : Microsoft.Network/loadBalancers
    data:    Location                        : southcentralus
    data:    Provisioning state              : Succeeded
    data:
    data:    Frontend IP configurations:
    data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
    data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
    data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
    data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
    data:
    data:    Probes:
    data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
    data:    -------------------  ------------------  --------  ----  ----  --------  -----
    data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
    data:
    data:    Backend Address Pools:
    data:    Name             Provisioning state
    data:    ---------------  ------------------
    data:    BackendPoolIPv4  Succeeded
    data:    BackendPoolIPv6  Succeeded
    data:
    data:    Load Balancing Rules:
    data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
    data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
    data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
    data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
    data:
    data:    Inbound NAT Rules:
    data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
    data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
    data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
    data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
    info:    network lb show
    ```

## <a name="create-nics"></a>Tworzenie kart sieciowych

Tworzenie kart sieciowych i kojarzenie ich z regułami NAT, regułami modułu równoważenia obciążenia i sondami.

1. Skonfiguruj zmienne programu PowerShell:

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. Utwórz kartę sieciową dla każdego zaplecza i Dodaj konfigurację protokołu IPv6:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Tworzenie zasobów maszyn wirtualnych zaplecza i dołączanie każdej karty sieciowej

Aby tworzyć maszyny wirtualne, musisz mieć konto magazynu. W przypadku równoważenia obciążenia maszyny wirtualne muszą być elementami członkowskimi zestawu dostępności. Aby uzyskać więcej informacji na temat tworzenia maszyn wirtualnych, zobacz [Tworzenie maszyny wirtualnej platformy Azure przy użyciu programu PowerShell](../virtual-machines/windows/quick-create-powershell.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Skonfiguruj zmienne programu PowerShell:

    ```powershell
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $imageurn = "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > W tym przykładzie używana jest nazwa użytkownika i hasło do maszyn wirtualnych w postaci zwykłego tekstu. Należy zachować ostrożność w przypadku używania tych poświadczeń w postaci zwykłego tekstu. Aby uzyskać bardziej bezpieczną metodę obsługi poświadczeń w programie PowerShell, zapoznaj się z [`Get-Credential`](/powershell/module/microsoft.powershell.security/get-credential) poleceniem cmdlet.

2. Utwórz zestaw dostępności:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Utwórz maszyny wirtualne przy użyciu skojarzonych kart sieciowych:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```