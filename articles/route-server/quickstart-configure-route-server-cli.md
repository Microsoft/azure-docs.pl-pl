---
title: 'Szybki Start: Tworzenie i Konfigurowanie serwera tras przy użyciu interfejsu wiersza polecenia platformy Azure'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć i skonfigurować serwer tras przy użyciu interfejsu wiersza polecenia platformy Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: ef41c52fa1b63094d952dc34f81db36f7aeaac95
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521292"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>Szybki Start: Tworzenie i Konfigurowanie serwera tras przy użyciu interfejsu wiersza polecenia platformy Azure 

W tym artykule opisano sposób konfigurowania usługi Azure Route Server do komunikacji równorzędnej z wirtualnymi urządzeniami sieciowymi (urządzenie WUS) w sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure. Usługa Azure Route Server będzie uczyć się tras z urządzenie WUS i programu dla maszyn wirtualnych w sieci wirtualnej. Anonsuje również trasy sieci wirtualnej do urządzenie WUS. Aby uzyskać więcej informacji, przeczytaj temat [serwer usługi Azure Route](overview.md).

> [!IMPORTANT]
> Usługa Azure Route Server (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

##  <a name="prerequisites"></a>Wymagania wstępne 

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Upewnij się, że masz najnowszy interfejs wiersza polecenia platformy Azure lub możesz użyć Azure Cloud Shell w portalu. 
* Przejrzyj [limity usługi dla serwera usługi Azure Route](route-server-faq.md#limitations). 

##  <a name="create-a-route-server"></a>Tworzenie serwera tras 

###  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wybierz subskrypcję. 

Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure. Jeśli używasz Cloud Shell "Wypróbuj", nastąpi automatyczne logowanie. Poniższe przykłady ułatwiają nawiązanie połączenia:

```azurecli-interactive
az login
```

Sprawdź subskrypcje dostępne na koncie.

```azurecli-interactive
az account list
```

Wybierz subskrypcję, dla której chcesz utworzyć obwód usługi ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="create-a-resource-group-and-virtual-network"></a>Tworzenie grupy zasobów i sieci wirtualnej 

Aby można było utworzyć serwer tras platformy Azure, musisz mieć sieć wirtualną do hostowania wdrożenia. Użyj poniższego polecenia, aby utworzyć grupę zasobów i sieć wirtualną. Jeśli masz już sieć wirtualną, możesz przejść do następnej sekcji.

```azurecli-interactive
az group create -n "RouteServerRG" -l "westus" 
az network vnet create -g "RouteServerRG" -n "myVirtualNetwork" --address-prefix "10.0.0.0/16" 
``` 

### <a name="add-a-subnet"></a>Dodawanie podsieci 

1. Dodaj podsieć o nazwie *RouteServerSubnet* , aby wdrożyć serwer tras platformy Azure w programie. Ta podsieć jest dedykowaną podsiecią tylko dla serwera tras platformy Azure. RouteServerSubnet musi mieć wartość/27 lub krótszy prefiks (na przykład/26,/25) lub podczas dodawania serwera usługi Azure Route zostanie wyświetlony komunikat o błędzie.

    ```azurecli-interactive 
    az network vnet subnet create -g "RouteServerRG" --vnet-name "myVirtualNetwork" --name "RouteServerSubnet" --address-prefix "10.0.0.0/24"  
    ``` 

1. Uzyskaj identyfikator RouteServerSubnet. Aby wyświetlić identyfikator zasobu wszystkich podsieci w sieci wirtualnej, użyj tego polecenia: 

    ```azurecli-interactive 
    subnet_id = $(az network vnet subnet show -n "RouteServerSubnet" --vnet-name "myVirtualNetwork" -g "RouteServerRG" --query id -o tsv) 
    ``` 

Identyfikator RouteServerSubnet wygląda następująco: 

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Tworzenie serwera tras 

Utwórz serwer tras za pomocą tego polecenia: 

```azurecli-interactive
az network routeserver create -n "myRouteServer" -g "RouteServerRG" --hosted-subnet $subnet_id  
``` 

Lokalizacja musi być zgodna z lokalizacją sieci wirtualnej. HostedSubnet jest IDENTYFIKATORem RouteServerSubnet uzyskanym w poprzedniej sekcji. 

## <a name="create-peering-with-an-nva"></a>Tworzenie komunikacji równorzędnej za pomocą urządzenie WUS 

Użyj następującego polecenia, aby ustanowić komunikację równorzędną z serwera tras do urządzenie WUS: 

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA1_name" 

``` 

"nva_ip" to adres IP sieci wirtualnej przypisany do urządzenie WUS. "nva_asn" jest numerem systemu autonomicznego (ASN) skonfigurowanym w urządzenie WUS. Numer ASN może być dowolną liczbą 16-bitową inną niż te w zakresie 65515-65520. Ten zakres numerów ASN jest zarezerwowany przez firmę Microsoft. 

Aby skonfigurować komunikację równorzędną z różnymi urządzenie WUS lub innym wystąpieniem tego samego urządzenie WUS w celu zapewnienia nadmiarowości, użyj tego polecenia:

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA2_name" 
``` 

## <a name="complete-the-configuration-on-the-nva"></a>Ukończ konfigurację na urządzenie WUS 

Aby ukończyć konfigurację urządzenie WUS i włączyć sesje protokołu BGP, wymagany jest adres IP i ASN serwera usługi Azure Route. Te informacje można uzyskać za pomocą tego polecenia: 

```azurecli-interactive 
az network routeserver show -g "RouteServerRG" -n "myRouteServer" 
``` 

Dane wyjściowe zawierają następujące informacje. 

```azurecli-interactive 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>Konfigurowanie wymiany tras 

Jeśli masz bramę ExpressRoute i bramę sieci VPN platformy Azure w tej samej sieci wirtualnej i chcesz, aby były one dostępne dla tras wymiany, możesz włączyć wymianę trasy na serwerze tras platformy Azure.

> [!IMPORTANT]
> W przypadku wdrożeń Greenfield należy utworzyć bramę sieci VPN platformy Azure przed utworzeniem serwera usługi Azure Route; w przeciwnym razie wdrożenie usługi Azure VPN Gateway zakończy się niepowodzeniem.
> 

1. Aby włączyć wymianę tras między serwerem tras platformy Azure i bramami, użyj tego polecenia:

```azurecli-interactive 
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic true 

``` 

2. Aby wyłączyć wymianę tras między serwerem tras platformy Azure i bramami, użyj tego polecenia:

```azurecli-interactive
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic false 
``` 

## <a name="troubleshooting"></a>Rozwiązywanie problemów 

Za pomocą tego polecenia można wyświetlić trasy anonsowane i odebrane przez serwer usługi Azure Route:

```azurecli-interactive 
az network routeserver peering list-advertised-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
az network routeserver peering list-learned-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
``` 

## <a name="clean-up"></a>Czyszczenie 

Jeśli serwer tras platformy Azure nie jest już potrzebny, Użyj tych poleceń, aby usunąć komunikację równorzędną BGP, a następnie usunąć serwer tras. 

1. Usuń komunikację równorzędną BGP między serwerem tras platformy Azure i urządzenie WUS za pomocą tego polecenia:

```azurecli-interactive
az network routeserver peering delete --routeserver-name "myRouteServer" -g "RouteServerRG" -n "NVA2_name" 
``` 

2. Usuń serwer tras platformy Azure za pomocą tego polecenia: 

```azurecli-interactive 
az network routeserver delete -n "myRouteServer" -g "RouteServerRG" 
``` 

## <a name="next-steps"></a>Następne kroki

Po utworzeniu serwera usługi Azure Route należy dalej dowiedzieć się, jak usługa Azure Route Server współdziała z ExpressRoute i bramami sieci VPN: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute i obsługa sieci VPN platformy Azure](expressroute-vpn-support.md)
 
