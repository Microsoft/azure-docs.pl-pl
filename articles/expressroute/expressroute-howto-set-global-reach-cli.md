---
title: 'Azure ExpressRoute: Konfigurowanie ExpressRoute Global Reach: interfejs wiersza polecenia'
description: Dowiedz się, jak połączyć obwody usługi ExpressRoute w celu zapewnienia sieci prywatnej między sieciami lokalnymi i włączania Global Reach przy użyciu interfejsu wiersza polecenia platformy Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 55f1e4c030dc0e7c8d0011dcff0cc1c9f8fec06e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99260235"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Konfigurowanie Global Reach ExpressRoute przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób konfigurowania Global Reach Azure ExpressRoute za pomocą interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [ExpressRoute Global Reach](expressroute-global-reach.md).
 
Przed rozpoczęciem konfiguracji należy spełnić następujące wymagania:

* Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure. Zobacz artykuły [Install the Azure CLI (Instalowanie interfejsu wiersza polecenia platformy Azure) ](/cli/azure/install-azure-cli) i [Get started with Azure CLI (Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure)](/cli/azure/get-started-with-azure-cli).
* Poznaj [przepływy pracy](expressroute-workflows.md)inicjowania obsługi obwodu ExpressRoute.
* Upewnij się, że obwody usługi ExpressRoute są w stanie aprowizacji.
* Upewnij się, że prywatna Komunikacja równorzędna Azure została skonfigurowana na obwodach usługi ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Zaloguj się do swojego konta platformy Azure

Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure. Następujące polecenie otwiera domyślną przeglądarkę i wyświetla prośbę o poświadczenia logowania do konta platformy Azure:  

```azurecli
az login
```

Jeśli masz wiele subskrypcji platformy Azure, sprawdź subskrypcje konta:

```azurecli
az account list
```

Określ subskrypcję, której chcesz użyć:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Zidentyfikuj obwody usługi ExpressRoute na potrzeby konfiguracji

Można włączyć ExpressRoute Global Reach między dowolnymi dwoma obwodami ExpressRoute. Obwody muszą znajdować się w obsługiwanych krajach/regionach i zostały utworzone w różnych lokalizacjach komunikacji równorzędnej. Jeśli Twoja subskrypcja jest właścicielem obu obwodów, możesz wybrać jeden z obwodów, aby uruchomić konfigurację. Jeśli jednak dwa obwody znajdują się w różnych subskrypcjach platformy Azure, należy utworzyć klucz autoryzacji z jednego obwodu. Przy użyciu klucza autoryzacji wygenerowanego na podstawie pierwszego obwodu można włączyć Global Reach w drugim obwodzie.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Włącz łączność między sieciami lokalnymi

Po uruchomieniu polecenia, aby włączyć łączność, należy zwrócić uwagę na następujące wymagania dotyczące wartości parametrów:

* *obwód równorzędny* powinien być pełnym identyfikatorem zasobu. Na przykład:

  > /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering

* *prefiks adresu* musi być podsiecią IPv4 "/29" (na przykład "10.0.0.0/29"). Adresy IP w tej podsieci są używane do nawiązywania łączności między dwoma obwodami usługi ExpressRoute. Adresów w tej podsieci nie można używać w sieciach wirtualnych platformy Azure ani w sieciach lokalnych.

Uruchom następujące polecenie interfejsu wiersza polecenia, aby połączyć dwa obwody usługi ExpressRoute:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

Dane wyjściowe interfejsu wiersza polecenia wyglądają następująco:

```output
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

Po zakończeniu tej operacji będziesz mieć łączność między sieciami lokalnymi po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Włączanie łączności między obwodami usługi ExpressRoute w różnych subskrypcjach platformy Azure

Jeśli dwa obwody nie znajdują się w tej samej subskrypcji platformy Azure, musisz mieć autoryzację. W poniższej konfiguracji wygenerujesz autoryzację w ramach subskrypcji obwodu 2. Następnie Przekaż klucz autoryzacji do obwodu 1.

1. Generuj klucz autoryzacji:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   Dane wyjściowe interfejsu wiersza polecenia wyglądają następująco:

   ```output
   {
     "authorizationKey": "<authorizationKey>",
     "authorizationUseStatus": "Available",
     "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
     "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
     "name": "<AuthorizationName>",
     "provisioningState": "Succeeded",
     "resourceGroup": "<Circuit2ResourceGroupName>",
     "type": "Microsoft.Network/expressRouteCircuits/authorizations"
   }
   ```

1. Zanotuj zarówno identyfikator zasobu, jak i klucz autoryzacji obwodu 2.

1. Uruchom następujące polecenie w stosunku do obwodu 1, przekazując identyfikator zasobu obwodu 2 i klucz autoryzacji:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Po zakończeniu tej operacji będziesz mieć łączność między sieciami lokalnymi po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Pobierz i sprawdź konfigurację

Użyj poniższego polecenia, aby zweryfikować konfigurację obwodu, w którym została wprowadzona konfiguracja (obwód 1 w poprzednim przykładzie):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

W danych wyjściowych interfejsu wiersza polecenia zobaczysz *CircuitConnectionStatus*. Informuje o tym, czy połączenie między dwoma obwodami zostało nawiązane ("połączone"), czy nie ("Rozłączono"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Wyłączanie łączności między sieciami lokalnymi

Aby wyłączyć łączność, uruchom następujące polecenie w stosunku do obwodu, w którym została wprowadzona konfiguracja (obwód 1 w poprzednim przykładzie).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Użyj ```show``` polecenia, aby sprawdzić stan.

Po zakończeniu tej operacji nie będziesz już mieć łączności między sieciami lokalnymi za pośrednictwem obwody usługi ExpressRoute.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o ExpressRoute Global Reach](expressroute-global-reach.md)
* [Weryfikowanie łączności ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Łączenie obwodu ExpressRoute z siecią wirtualną](expressroute-howto-linkvnet-arm.md)
