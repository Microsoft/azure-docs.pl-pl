---
title: Typowe polecenia programu PowerShell dla usługi Azure Virtual Networks
description: Typowe polecenia programu PowerShell umożliwiające rozpoczęcie tworzenia sieci wirtualnej i skojarzonych z nią zasobów maszyn wirtualnych.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: b4d6b20e63c42616aad0f8776fae159a0f2aa455
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87088380"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Typowe polecenia programu PowerShell dla usługi Azure Virtual Networks

Jeśli chcesz utworzyć maszynę wirtualną, musisz utworzyć [sieć wirtualną](../../virtual-network/virtual-networks-overview.md) lub zapoznać się z istniejącą siecią wirtualną, w której można dodać maszynę wirtualną. Zazwyczaj podczas tworzenia maszyny wirtualnej należy rozważyć utworzenie zasobów opisanych w tym artykule.

Zobacz [How to install and configure Azure PowerShell](/powershell/azure/) (Jak zainstalować i skonfigurować program Azure PowerShell), aby uzyskać informacje na temat instalowania najnowszej wersji programu Azure PowerShell, wybierania subskrypcji i logowania się do konta.

Niektóre zmienne mogą być przydatne w przypadku uruchamiania więcej niż jednego polecenia w tym artykule:

- $location — lokalizacja zasobów sieciowych. Możesz użyć [Get-AzLocation](/powershell/module/az.resources/get-azlocation) , aby znaleźć [region geograficzny](https://azure.microsoft.com/regions/) , który działa dla Ciebie.
- $myResourceGroup — nazwa grupy zasobów, w której znajdują się zasoby sieciowe.

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

| Zadanie | Polecenie |
| ---- | ------- |
| Tworzenie konfiguracji podsieci |$subnet 1 = [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1"-AddressPrefix XX. X. X. x/XX<BR>$subnet 2 = New-AzVirtualNetworkSubnetConfig-Name "mySubnet2"-AddressPrefix XX. X. x. X/XX<BR><BR>Typowa sieć może mieć podsieć dla [modułu równoważenia obciążenia](../../load-balancer/load-balancer-overview.md) połączonego z Internetem oraz oddzielną podsieć dla [wewnętrznego modułu równoważenia obciążenia](../../load-balancer/load-balancer-overview.md). |
| Tworzenie sieci wirtualnej |$vnet = [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet"-ResourceGroupName $myResourceGroup lokalizacji $Location-AddressPrefix XX. X. X. x/XX-Subnet $Subnet 1, $Subnet 2 |
| Testowanie dla unikatowej nazwy domeny |[Test-AzDnsAvailability](/powershell/module/az.network/test-azdnsavailability) -wartość domainnamelabel "myDNS" — Lokalizacja $Location<BR><BR>Możesz określić nazwę domeny DNS dla [zasobu publicznego adresu IP](../../virtual-network/public-ip-addresses.md), co spowoduje utworzenie mapowania domainname.Location.cloudapp.Azure.com na publiczny adres IP na serwerach DNS zarządzanych przez platformę Azure. Nazwa może zawierać tylko litery, cyfry i łączniki. Pierwszy i ostatni znak musi być literą lub cyfrą, a nazwa domeny musi być unikatowa w ramach swojej lokalizacji platformy Azure. Jeśli zwracana jest **wartość true** , proponowana nazwa jest globalnie unikatowa. |
| Tworzenie publicznego adresu IP |$pip = [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp"-ResourceGroupName $MyResourceGroup-wartość domainnamelabel "myDNS"-Location $Location-metodę AllocationMethod jako Dynamic<BR><BR>Publiczny adres IP używa wcześniej przetestowanej nazwy domeny i jest używany przez konfigurację frontonu modułu równoważenia obciążenia. |
| Tworzenie konfiguracji adresu IP frontonu |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP"-PublicIpAddress $PIP<BR><BR>Konfiguracja frontonu obejmuje publiczny adres IP, który został wcześniej utworzony dla przychodzącego ruchu sieciowego. |
| Tworzenie puli adresów zaplecza |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Zapewnia wewnętrzne adresy wewnętrznej bazy danych modułu równoważenia obciążenia, do których dostęp uzyskuje się za pomocą interfejsu sieciowego. |
| Tworzenie sondy |$healthProbe = [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "RequestPath" healthProbe. aspx "-Protocol HTTP-port 80-IntervalInSeconds 15-ProbeCount 2<BR><BR>Zawiera sondy kondycji używane do sprawdzania dostępności wystąpień maszyn wirtualnych w puli adresów zaplecza. |
| Tworzenie reguły równoważenia obciążenia |$lbRule = [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) -Name http-elementu frontendipconfiguration $FrontendIP-BackendAddressPool $BeAddressPool-prob $HealthProbe-Protocol TCP-FrontendPort 80-BackendPort 80<BR><BR>Zawiera reguły przypisujące port publiczny modułu równoważenia obciążenia do portu w puli adresów zaplecza. |
| Tworzenie reguły NAT dla ruchu przychodzącego |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1"-elementu frontendipconfiguration $FrontendIP-Protocol TCP-FrontendPort 3441-BackendPort 3389<BR><BR>Zawiera reguły mapowania portu publicznego modułu równoważenia obciążenia na port określonej maszyny wirtualnej w puli adresów zaplecza. |
| Tworzenie modułu równoważenia obciążenia |$loadBalancer = [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $MyResourceGroup-Name "myLoadBalancer" $location-elementu frontendipconfiguration $FrontendIP-InboundNatRule $InboundNATRule-LoadBalancingRule $LbRule-BackendAddressPool $BeAddressPool-probe $healthProbe |
| Tworzenie interfejsu sieciowego |$nic 1 = [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $MyResourceGroup-Name "myNIC" — Lokalizacja $Location-PrivateIpAddress XX. x. X. x-Subnet $Subnet 2-LoadBalancerBackendAddressPool $LoadBalancer. BackendAddressPools [0]-LoadBalancerInboundNatRule $LoadBalancer. InboundNatRules [0]<BR><BR>Utwórz interfejs sieciowy przy użyciu publicznego adresu IP i podsieci sieci wirtualnej, która została wcześniej utworzona. |

## <a name="get-information-about-network-resources"></a>Uzyskaj informacje o zasobach sieciowych

| Zadanie | Polecenie |
| ---- | ------- |
| Wyświetl listę sieci wirtualnych |[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla listę wszystkich sieci wirtualnych w grupie zasobów. |
| Uzyskaj informacje o sieci wirtualnej |Get-AzVirtualNetwork-Name "myVNet" — ResourceGroupName $myResourceGroup |
| Wyświetlanie listy podsieci w sieci wirtualnej |Get-AzVirtualNetwork-Name "myVNet"-ResourceGroupName $myResourceGroup &#124; Wybieranie podsieci |
| Pobierz informacje o podsieci |[Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1"-VirtualNetwork $VNET<BR><BR>Pobiera informacje o podsieci w określonej sieci wirtualnej. Wartość $vnet reprezentuje obiekt zwrócony przez Get-AzVirtualNetwork. |
| Wyświetlanie listy adresów IP |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla listę publicznych adresów IP w grupie zasobów. |
| Wyświetlanie listy modułów równoważenia obciążenia |[Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla listę wszystkich modułów równoważenia obciążenia w grupie zasobów. |
| Wyświetl listę interfejsów sieciowych |[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla wszystkie interfejsy sieciowe w grupie zasobów. |
| Uzyskaj informacje o interfejsie sieciowym |Get-AzNetworkInterface-Name "myNIC" — ResourceGroupName $myResourceGroup<BR><BR>Pobiera informacje o konkretnym interfejsie sieciowym. |
| Pobieranie konfiguracji adresu IP interfejsu sieciowego |[Get-AzNetworkInterfaceIPConfig](/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP"-interfejsu sieciowego $nic<BR><BR>Pobiera informacje o konfiguracji protokołu IP określonego interfejsu sieciowego. Wartość $nic reprezentuje obiekt zwrócony przez Get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Zarządzanie zasobami sieciowymi

| Zadanie | Polecenie |
| ---- | ------- |
| Dodawanie podsieci do sieci wirtualnej |[Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX. X. X. x/XX-Name "mySubnet1"-VirtualNetwork $VNET<BR><BR>Dodaje podsieć do istniejącej sieci wirtualnej. Wartość $vnet reprezentuje obiekt zwrócony przez Get-AzVirtualNetwork. |
| Usuwanie sieci wirtualnej |[Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet"-ResourceGroupName $myResourceGroup<BR><BR>Usuwa określoną sieć wirtualną z grupy zasobów. |
| Usuwanie interfejsu sieciowego |[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC"-ResourceGroupName $myResourceGroup<BR><BR>Usuwa określony interfejs sieciowy z grupy zasobów. |
| Usuwanie modułu równoważenia obciążenia |[Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer"-ResourceGroupName $myResourceGroup<BR><BR>Usuwa określony moduł równoważenia obciążenia z grupy zasobów. |
| Usuwanie publicznego adresu IP |[Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress)-Name "ResourceGroupName $myResourceGroup<BR><BR>Usuwa określony publiczny adres IP z grupy zasobów. |

## <a name="next-steps"></a>Następne kroki
Użyj interfejsu sieciowego, który został właśnie utworzony podczas [tworzenia maszyny wirtualnej](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json).
