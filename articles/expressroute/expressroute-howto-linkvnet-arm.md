---
title: 'Samouczek: łączenie sieci wirtualnej z obwodem ExpressRoute — Azure PowerShell'
description: Ten samouczek zawiera omówienie sposobu łączenia sieci wirtualnych (sieci wirtualnych) z obwodami usługi ExpressRoute przy użyciu modelu wdrażania Menedżer zasobów i Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 69067ca34b231f1b14f8cc854288c3ed4c4ac82a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91855994"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit"></a>Samouczek: łączenie sieci wirtualnej z obwodem ExpressRoute
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-linkvnet-cli.md)
> * [Wideo — Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-linkvnet-classic.md)
>

W tym artykule opisano łączenie sieci wirtualnych (sieci wirtualnych) z obwodymi usługi Azure ExpressRoute przy użyciu modelu wdrażania Menedżer zasobów i programu PowerShell. Sieci wirtualne mogą znajdować się w tej samej subskrypcji lub części innej subskrypcji. W tym artykule pokazano również, jak zaktualizować łącze sieci wirtualnej.

* Do obwodu standardowego ExpressRoute można połączyć maksymalnie 10 sieci wirtualnych. Wszystkie sieci wirtualne muszą znajdować się w tym samym regionie geopolitycznym w przypadku korzystania ze standardowego obwodu usługi ExpressRoute. 

* Jedna sieć wirtualna może być połączona do maksymalnie czterech obwodów usługi ExpressRoute. Wykonaj kroki opisane w tym artykule, aby utworzyć nowy obiekt połączenia dla każdego obwodu usługi ExpressRoute, z którym nawiązujesz połączenie. Obwody usługi ExpressRoute mogą znajdować się w tej samej subskrypcji, różnych subskrypcjach lub kombinacji obu tych elementów.

* Po włączeniu dodatku ExpressRoute Premium można połączyć sieci wirtualne poza regionem geopolitycznym obwodu ExpressRoute. Dodatek Premium umożliwia również łączenie więcej niż 10 sieci wirtualnych do obwodu ExpressRoute, w zależności od wybranej przepustowości. Zapoznaj się z [często zadawanymi pytaniami](expressroute-faqs.md) , aby uzyskać więcej informacji na temat dodatku Premium.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
> - Łączenie sieci wirtualnej w ramach tej samej subskrypcji z obwodem
> - Połączenie sieci wirtualnej w innej subskrypcji z obwodem
> - Modyfikowanie połączenia sieci wirtualnej
> - Konfigurowanie ExpressRoute FastPath

## <a name="prerequisites"></a>Wymagania wstępne

* Przed rozpoczęciem konfiguracji zapoznaj się z warunkami [wstępnymi](expressroute-prerequisites.md), [wymaganiami dotyczącymi routingu](expressroute-routing.md)i [przepływami pracy](expressroute-workflows.md) .

* Musisz mieć aktywny obwód usługi ExpressRoute. 
  * Postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md) i mieć obwód włączony przez dostawcę połączenia. 
  * Upewnij się, że masz skonfigurowaną prywatną komunikację równorzędną Azure dla obwodu. Instrukcje dotyczące routingu można znaleźć w artykule [Konfigurowanie routingu](expressroute-howto-routing-arm.md) . 
  * Upewnij się, że usługa prywatnej komunikacji równorzędnej Azure jest skonfigurowana i ustanawia komunikację równorzędną BGP między siecią a firmą Microsoft w celu kompleksowej łączności.
  * Upewnij się, że masz utworzoną sieć wirtualną i bramę sieci wirtualnej i została ona w pełni zainicjowana. Postępuj zgodnie z instrukcjami, aby [utworzyć bramę sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brama sieci wirtualnej dla usługi ExpressRoute używa elementu Gatewaytype "ExpressRoute", a nie sieci VPN.

### <a name="working-with-azure-powershell"></a>Praca z Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Łączenie sieci wirtualnej w ramach tej samej subskrypcji z obwodem
Bramę sieci wirtualnej można połączyć z obwodem usługi ExpressRoute za pomocą następującego polecenia cmdlet. Upewnij się, że Brama sieci wirtualnej została utworzona i jest gotowa do łączenia przed uruchomieniem polecenia cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Połączenie sieci wirtualnej w innej subskrypcji z obwodem
Obwód ExpressRoute można udostępnić w wielu subskrypcjach. Na poniższej ilustracji przedstawiono prosty schemat działania udostępniania dla obwodów usługi ExpressRoute w wielu subskrypcjach.

Każda z mniejszych chmur w ramach dużej chmury służy do reprezentowania subskrypcji należących do różnych działów w organizacji. Każda z działów w organizacji korzysta z własnej subskrypcji na potrzeby wdrażania ich usług — ale może współdzielić jeden obwód usługi ExpressRoute, aby połączyć się z siecią lokalną. Pojedynczy dział (w tym przykładzie:) może być własnym obwodem ExpressRoute. Inne subskrypcje w organizacji mogą korzystać z obwodu ExpressRoute.

> [!NOTE]
> Do właściciela subskrypcji zostaną zastosowane opłaty za połączenia i przepustowość dla obwodu usługi ExpressRoute. Wszystkie sieci wirtualne mają tę samą przepustowość.
> 

:::image type="content" source="./media/expressroute-howto-linkvnet-classic/cross-subscription.png" alt-text="Łączność między subskrypcjami":::

### <a name="administration---circuit-owners-and-circuit-users"></a>Właściciele obwodów administracyjnych i użytkownicy obwodów

"Właściciel obwodu" jest autoryzowanym użytkownikiem zaawansowanym zasobu obwodu ExpressRoute. Właściciel obwodu może tworzyć autoryzacje, które mogą być zrealizowane przez "użytkowników obwodów". Użytkownicy obwodu to właściciele bram sieci wirtualnej, którzy nie należą do tej samej subskrypcji co obwód usługi ExpressRoute. Użytkownicy obwodów mogą wykorzystać autoryzacje (jedna autoryzacja na sieć wirtualną).

Właściciel obwodu ma uprawnienia do modyfikowania i odwoływania autoryzacji w dowolnym momencie. Odwoływanie autoryzacji powoduje usunięcie wszystkich połączeń linków z subskrypcji, której dostęp został odwołany.

### <a name="circuit-owner-operations"></a>Operacje właściciela obwodu

**Aby utworzyć autoryzację**

Właściciel obwodu tworzy autoryzację, która tworzy klucz autoryzacji, który ma być używany przez użytkownika obwodu do łączenia ich bram sieci wirtualnych z obwodem ExpressRoute. Autoryzacja jest prawidłowa tylko dla jednego połączenia.

Poniższy fragment kodu polecenia cmdlet przedstawia sposób tworzenia autoryzacji:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```

Odpowiedź na poprzednie polecenia będzie zawierać klucz autoryzacji i stan:

```azurepowershell
Name                   : MyAuthorization1
Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
AuthorizationKey       : ####################################
AuthorizationUseStatus : Available
ProvisioningState      : Succeeded
```

**Aby przejrzeć autoryzacji**

Właściciel obwodu może przejrzeć wszystkie autoryzacje, które są wydawane w określonym obwodzie, uruchamiając następujące polecenie cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Aby dodać autoryzacje**

Właściciel obwodu może dodawać autoryzacje przy użyciu następującego polecenia cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Aby usunąć autoryzacje**

Właściciel obwodu może odwoływać/usunąć autoryzacje dla użytkownika, uruchamiając następujące polecenie cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Operacje użytkownika obwodowego

Użytkownik obwodu wymaga identyfikatora elementu równorzędnego i klucza autoryzacji od właściciela obwodu. Klucz autoryzacji jest identyfikatorem GUID.

Identyfikator elementu równorzędnego można sprawdzić za pomocą następującego polecenia:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Aby zrealizować autoryzację połączenia**

Użytkownik obwodu może uruchomić następujące polecenie cmdlet, aby zrealizować autoryzację linku:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Aby zwolnić autoryzację połączenia**

Można zwolnić autoryzację, usuwając połączenie łączące obwód usługi ExpressRoute z siecią wirtualną.

## <a name="modify-a-virtual-network-connection"></a>Modyfikowanie połączenia sieci wirtualnej
Można zaktualizować pewne właściwości połączenia sieci wirtualnej. 

**Aby zaktualizować wagę połączenia**

Sieć wirtualna może być połączona z wieloma obwodami usługi ExpressRoute. Może zostać wyświetlony ten sam prefiks z więcej niż jednego obwodu usługi ExpressRoute. Aby wybrać połączenie do wysyłania ruchu przeznaczonego dla tego prefiksu, możesz zmienić *RoutingWeight* połączenia. Ruch zostanie wysłany w połączeniu z największą *RoutingWeight*.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Zakres *RoutingWeight* ma wartość od 0 do 32000. Wartość domyślna to 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurowanie ExpressRoute FastPath 
Jeśli Brama sieci wirtualnej jest niezwykle wydajna lub ErGw3AZ, można włączyć [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) . FastPath poprawia wydajność ścieżki danych, na przykład pakiety na sekundę i połączenia na sekundę między siecią lokalną i siecią wirtualną. 

**Skonfiguruj FastPath na nowym połączeniu**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**Aktualizowanie istniejącego połączenia w celu włączenia FastPath**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już połączenia usługi ExpressRoute, w ramach subskrypcji, w której znajduje się brama, użyj `Remove-AzVirtualNetworkGatewayConnection` polecenia, aby usunąć łącze między bramą a obwodem.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection "MyConnection" -ResourceGroupName "MyRG"
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat ExpressRoute, zobacz często zadawane pytania dotyczące usługi ExpressRoute.

> [!div class="nextstepaction"]
> [Usługa ExpressRoute — często zadawane pytania](expressroute-faqs.md)
