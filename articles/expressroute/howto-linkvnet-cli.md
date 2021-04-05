---
title: 'Samouczek: łączenie sieci wirtualnej z obwodem usługi ExpressRoute — interfejs wiersza polecenia platformy Azure'
description: W tym samouczku pokazano, jak połączyć sieci wirtualne (sieci wirtualnych) ze obwodami usługi ExpressRoute przy użyciu modelu wdrażania Menedżer zasobów i interfejsu wiersza polecenia platformy Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: b9dda384e2ef30808559d10012dea2909b2af0fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206938"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Samouczek: łączenie sieci wirtualnej z obwodem usługi ExpressRoute przy użyciu interfejsu wiersza polecenia

W tym samouczku pokazano, jak połączyć sieci wirtualne (sieci wirtualnych) z obwodymi usługi Azure ExpressRoute przy użyciu interfejsu wiersza polecenia platformy Azure. Aby utworzyć łącze przy użyciu interfejsu wiersza polecenia platformy Azure, należy utworzyć sieci wirtualne przy użyciu Menedżer zasobów model wdrażania. Mogą one znajdować się w tej samej subskrypcji lub części innej subskrypcji. Jeśli chcesz użyć innej metody do łączenia sieci wirtualnej z obwodem usługi ExpressRoute, możesz wybrać artykuł z poniższej listy:

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-linkvnet-cli.md)
> * [Wideo — Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-linkvnet-classic.md)
> 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Łączenie sieci wirtualnej w ramach tej samej subskrypcji z obwodem
> - Połączenie sieci wirtualnej w innej subskrypcji z obwodem
> - Modyfikowanie połączenia sieci wirtualnej
> - Konfigurowanie ExpressRoute FastPath

## <a name="prerequisites"></a>Wymagania wstępne

* Potrzebna jest Najnowsza wersja interfejsu wiersza polecenia (CLI). Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Przed rozpoczęciem konfiguracji zapoznaj się z warunkami [wstępnymi](expressroute-prerequisites.md), [wymaganiami dotyczącymi routingu](expressroute-routing.md)i [przepływami pracy](expressroute-workflows.md) .
* Musisz mieć aktywny obwód usługi ExpressRoute. 
  * Postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](howto-circuit-cli.md) i mieć obwód włączony przez dostawcę połączenia. 
  * Upewnij się, że masz skonfigurowaną prywatną komunikację równorzędną Azure dla obwodu. Instrukcje dotyczące routingu można znaleźć w artykule [Konfigurowanie routingu](howto-routing-cli.md) . 
  * Upewnij się, że skonfigurowano prywatną komunikację równorzędną Azure. Należy ustanowić komunikację równorzędną BGP między siecią i firmą Microsoft, aby umożliwić kompleksowe połączenie.
  * Upewnij się, że masz utworzoną sieć wirtualną i bramę sieci wirtualnej i została ona w pełni zainicjowana. Postępuj zgodnie z instrukcjami, aby [skonfigurować bramę sieci wirtualnej dla usługi ExpressRoute](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md). Upewnij się, że używasz `--gateway-type ExpressRoute` .
* Do obwodu standardowego ExpressRoute można połączyć maksymalnie 10 sieci wirtualnych. Wszystkie sieci wirtualne muszą znajdować się w tym samym regionie geopolitycznym w przypadku korzystania ze standardowego obwodu usługi ExpressRoute. 
* Jedna sieć wirtualna może być połączona do maksymalnie czterech obwodów usługi ExpressRoute. Użyj poniższego procesu, aby utworzyć nowy obiekt połączenia dla każdego obwodu usługi ExpressRoute, z którym nawiązujesz połączenie. Obwody usługi ExpressRoute mogą znajdować się w tej samej subskrypcji, różnych subskrypcjach lub kombinacji obu tych elementów.
* Po włączeniu dodatku ExpressRoute Premium można połączyć sieci wirtualne poza regionem geopolitycznym obwodu ExpressRoute. Dodatek Premium umożliwia również łączenie więcej niż 10 sieci wirtualnych do obwodu ExpressRoute, w zależności od wybranej przepustowości. Zapoznaj się z [często zadawanymi pytaniami](expressroute-faqs.md) , aby uzyskać więcej informacji na temat dodatku Premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Łączenie sieci wirtualnej w ramach tej samej subskrypcji z obwodem

Bramę sieci wirtualnej można podłączyć do obwodu usługi ExpressRoute przy użyciu przykładu. Upewnij się, że Brama sieci wirtualnej została utworzona i jest gotowa do łączenia przed uruchomieniem polecenia.

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Połączenie sieci wirtualnej w innej subskrypcji z obwodem

Obwód ExpressRoute można udostępnić w wielu subskrypcjach. Na poniższej ilustracji przedstawiono prosty schemat działania udostępniania dla obwodów usługi ExpressRoute w wielu subskrypcjach.

Każda z mniejszych chmur w ramach dużej chmury służy do reprezentowania subskrypcji należących do różnych działów w organizacji. Każda z działów w organizacji korzysta z własnej subskrypcji na potrzeby wdrażania ich usług — ale może współdzielić jeden obwód usługi ExpressRoute, aby połączyć się z siecią lokalną. Pojedynczy dział (w tym przykładzie:) może być własnym obwodem ExpressRoute. Inne subskrypcje w organizacji mogą korzystać z obwodu ExpressRoute.

> [!NOTE]
> Naliczanie opłat za połączenia i przepustowość dla dedykowanego obwodu zostanie zastosowane do właściciela obwodu usługi ExpressRoute. Wszystkie sieci wirtualne mają tę samą przepustowość.
> 
> 

![Łączność między subskrypcjami](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Właściciele obwodów administracyjnych i użytkownicy obwodów

"Właściciel obwodu" jest autoryzowanym użytkownikiem zaawansowanym zasobu obwodu ExpressRoute. Właściciel obwodu może tworzyć autoryzacje, które mogą być zrealizowane przez "użytkowników obwodów". Użytkownicy obwodu to właściciele bram sieci wirtualnej, którzy nie należą do tej samej subskrypcji co obwód usługi ExpressRoute. Użytkownicy obwodów mogą wykorzystać autoryzacje (jedna autoryzacja na sieć wirtualną).

Właściciel obwodu ma uprawnienia do modyfikowania i odwoływania autoryzacji w dowolnym momencie. Po odwołaniu autoryzacji wszystkie połączenia łączy są usuwane z subskrypcji, której dostęp został odwołany.

### <a name="circuit-owner-operations"></a>Operacje właściciela obwodu

**Aby utworzyć autoryzację**

Właściciel obwodu tworzy autoryzację, która tworzy klucz autoryzacji, który ma być używany przez użytkownika obwodu do łączenia ich bram sieci wirtualnych z obwodem ExpressRoute. Autoryzacja jest prawidłowa tylko dla jednego połączenia.

Poniższy przykład pokazuje, jak utworzyć autoryzację:

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Odpowiedź zawiera klucz autoryzacji i stan:

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Aby przejrzeć autoryzacji**

Właściciel obwodu może przejrzeć wszystkie autoryzacje, które są wydawane w określonym obwodzie, uruchamiając Poniższy przykład:

```azurecli-interactive
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Aby dodać autoryzacje**

Właściciel obwodu może dodawać autoryzacje, korzystając z następującego przykładu:

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Aby usunąć autoryzacje**

Właściciel obwodu może odwoływać/usunąć autoryzacje dla użytkownika, uruchamiając Poniższy przykład:

```azurecli-interactive
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Operacje użytkownika obwodowego

Użytkownik obwodu wymaga identyfikatora elementu równorzędnego i klucza autoryzacji od właściciela obwodu. Klucz autoryzacji jest identyfikatorem GUID.

```azurecli-interactive
az network express-route show -n MyCircuit -g ExpressRouteResourceGroup
```

**Aby zrealizować autoryzację połączenia**

Użytkownik obwodu może uruchomić następujący przykład, aby zrealizować autoryzację linku:

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Aby zwolnić autoryzację połączenia**

Można zwolnić autoryzację, usuwając połączenie łączące obwód usługi ExpressRoute z siecią wirtualną.

## <a name="modify-a-virtual-network-connection"></a>Modyfikowanie połączenia sieci wirtualnej
Można zaktualizować pewne właściwości połączenia sieci wirtualnej. 

**Aby zaktualizować wagę połączenia**

Sieć wirtualna może być połączona z wieloma obwodami usługi ExpressRoute. Może zostać wyświetlony ten sam prefiks z więcej niż jednego obwodu usługi ExpressRoute. Aby wybrać połączenie do wysyłania ruchu przeznaczonego dla tego prefiksu, możesz zmienić *RoutingWeight* połączenia. Ruch zostanie wysłany w połączeniu z największą *RoutingWeight*.

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

Zakres *RoutingWeight* ma wartość od 0 do 32000. Wartość domyślna to 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurowanie ExpressRoute FastPath 
Jeśli Brama sieci wirtualnej jest niezwykle wydajna lub ErGw3AZ, można włączyć [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) . FastPath poprawia wydajność ścieżki danych, na przykład pakiety na sekundę i połączenia na sekundę między siecią lokalną i siecią wirtualną. 

**Skonfiguruj FastPath na nowym połączeniu**

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Aktualizowanie istniejącego połączenia w celu włączenia FastPath**

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już połączenia usługi ExpressRoute, w ramach subskrypcji, w której znajduje się brama, użyj `az network vpn-connection delete` polecenia, aby usunąć łącze między bramą a obwodem.

```azurecli-interactive
az network vpn-connection delete --name ERConnection --resource-group ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się połączyć sieć wirtualną z obwodem w ramach tej samej subskrypcji i innej subskrypcji. Aby uzyskać więcej informacji na temat bramy ExpressRoute, zobacz: 

> [!div class="nextstepaction"]
> [Informacje o bramach sieci wirtualnej ExpressRoute](expressroute-about-virtual-network-gateways.md)