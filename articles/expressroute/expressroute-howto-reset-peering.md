---
title: 'Azure ExpressRoute: Resetowanie komunikacji równorzędnej obwodu'
description: Dowiedz się, jak włączać i wyłączać komunikację równorzędną dla obwodu usługi Azure ExpressRoute za pomocą Azure PowerShell.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: 0bde96ae5f4a9aff6f4a16a4f1544d9b39e5cb66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97559577"
---
# <a name="reset-expressroute-circuit-peerings"></a>Resetowanie komunikacji równorzędnej obwodu ExpressRoute

W tym artykule opisano sposób włączania i wyłączania komunikacji równorzędnej obwodu usługi ExpressRoute przy użyciu programu PowerShell. Komunikacja równorzędna jest domyślnie włączona podczas ich tworzenia. Po wyłączeniu komunikacji równorzędnej sesja protokołu BGP na podstawowym i pomocniczym połączeniu obwodu usługi ExpressRoute zostanie wyłączona. Utracisz łączność dla tej komunikacji równorzędnej z firmą Microsoft. Po włączeniu komunikacji równorzędnej zostanie nawiązane sesja protokołu BGP na podstawowym i pomocniczym połączeniu z obwodem usługi ExpressRoute. Połączenie z firmą Microsoft zostanie przywrócone dla tej komunikacji równorzędnej. Możesz włączyć i wyłączyć komunikację równorzędną dla komunikacji równorzędnej firmy Microsoft i prywatnej komunikacji równorzędnej Azure niezależnie od obwodu usługi ExpressRoute.

Istnieją dwa scenariusze, w których może być pomocne zresetowanie komunikacji równorzędnej ExpressRoute.
* Jeśli chcesz przetestować projekt i implementację odzyskiwania po awarii. Na przykład masz dwa obwody usługi ExpressRoute. Można wyłączyć komunikację równorzędną w jednym obwodzie i wymusić przełączenie ruchu sieciowego do trybu failover w innym obwodzie.
* Włącz wykrywanie dwukierunkowego przekazywania (BFD) na prywatnej komunikacji równorzędnej Azure lub komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute. BFD domyślnie włączone w prywatnej komunikacji równorzędnej platformy Azure, jeśli został utworzony obwód usługi ExpressRoute po 1 sierpnia 2018 i w przypadku komunikacji równorzędnej firmy Microsoft po 10 stycznia 2020. Jeśli obwód został utworzony przed datą na liście, należy zresetować komunikację równorzędną, aby włączyć BFD. 

### <a name="working-with-azure-powershell"></a>Praca z Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Resetowanie komunikacji równorzędnej

1. Jeśli używasz programu PowerShell lokalnie, Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i Połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

   ```azurepowershell
   Connect-AzAccount
   ```
2. Jeśli masz wiele subskrypcji platformy Azure, wyświetl subskrypcje dla konta.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. Wskaż subskrypcję, której chcesz użyć.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. Uruchom następujące polecenia, aby pobrać obwód ExpressRoute.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Określ komunikację równorzędną, którą chcesz wyłączyć lub włączyć. *Komunikacja równorzędna* jest tablicą. W poniższym przykładzie Komunikacja równorzędna [0] polega na prywatnej komunikacji równorzędnej Azure i komunikacji równorzędnej [1] komunikacji równorzędnej firmy Microsoft.

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
   ProvisioningState                : Succeeded
   Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             :
   ServiceProviderProperties        : {
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. Uruchom następujące polecenia, aby zmienić stan komunikacji równorzędnej.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   Komunikacja równorzędna powinna znajdować się w stanie ustawionym przez użytkownika. 

## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz pomocy dotyczącej rozwiązywania problemów z programem ExpressRoute, zobacz następujące artykuły:
* [Weryfikowanie połączenia usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Rozwiązywanie problemów z wydajnością sieci](expressroute-troubleshooting-network-performance.md)
