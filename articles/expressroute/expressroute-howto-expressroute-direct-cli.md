---
title: 'Azure ExpressRoute: Konfigurowanie ExpressRoute Direct: interfejs wiersza polecenia'
description: Dowiedz się, jak skonfigurować usługę Azure ExpressRoute Direct przy użyciu interfejsu wiersza polecenia platformy Azure, aby połączyć się bezpośrednio z siecią globalną firmy Microsoft.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/14/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: aea51e56f2d96fa634b1ece2029c9ea5bf3f60fc
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011309"
---
# <a name="configure-expressroute-direct-by-using-the-azure-cli"></a>Konfigurowanie usługi ExpressRoute Direct przy użyciu interfejsu wiersza polecenia platformy Azure

Funkcja ExpressRoute Direct umożliwia bezpośrednie łączenie się z siecią globalną firmy Microsoft przy użyciu lokalizacji komunikacji równorzędnej strategicznie rozmieszczonych na całym świecie. Aby uzyskać więcej informacji, zobacz [Informacje o programie ExpressRoute Direct Connect](expressroute-erdirect-about.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed użyciem usługi ExpressRoute Direct należy najpierw zarejestrować swoją subskrypcję. Aby zarejestrować się, Wyślij wiadomość E-mail <ExpressRouteDirect@microsoft.com> z identyfikatorem subskrypcji, w tym następujące szczegóły:

* Scenariusze, które chcesz zrealizować za pomocą **ExpressRoute Direct**
* Preferencje lokalizacji — zobacz [partnerzy i lokalizacje komunikacji równorzędnej](expressroute-locations-providers.md) , aby uzyskać pełną listę wszystkich lokalizacji
* Oś czasu dla wdrożenia
* Inne pytania

## <a name="create-the-resource"></a><a name="resources"></a>Tworzenie zasobu

1. Zaloguj się do platformy Azure i wybierz subskrypcję zawierającą ExpressRoute. Zasób ExpressRoute Direct i obwody usługi ExpressRoute muszą znajdować się w tej samej subskrypcji. W interfejsie wiersza polecenia platformy Azure Uruchom następujące polecenia:

   ```azurecli
   az login
   ```

   Sprawdź subskrypcje konta: 

   ```azurecli
   az account list 
   ```

   Wybierz subskrypcję, dla której chcesz utworzyć obwód usługi ExpressRoute:

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```

2. Zarejestruj ponownie swoją subskrypcję w usłudze Microsoft. Network, aby uzyskać dostęp do interfejsów API expressrouteportslocation i expressrouteport

   ```azurecli
   az provider register --namespace Microsoft.Network
   ```
3. Wyświetl listę wszystkich lokalizacji, w których obsługiwana jest ExpressRoute Direct:
    
   ```azurecli
   az network express-route port location list
   ```

   **Przykładowe dane wyjściowe**
  
   ```output
   [
   {
    "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
    "location": null,
    "name": "Equinix-Ashburn-DC2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA3",
    "location": null,
    "name": "Equinix-Dallas-DA3",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "111 8th Avenue, New York, NY 10011",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-New-York-NY5",
    "location": null,
    "name": "Equinix-New-York-NY5",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "11 Great Oaks Blvd, SV1, San Jose, CA 95119",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-SV1",
    "location": null,
    "name": "Equinix-San-Jose-SV1",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "2001 Sixth Ave., Suite 350, SE2, Seattle, WA 98121",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Seattle-SE2",
    "location": null,
    "name": "Equinix-Seattle-SE2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ]
   ```
4. Ustal, czy jedna z lokalizacji wymienionych w poprzednim kroku ma dostępną przepustowość:

   ```azurecli
   az network express-route port location show -l "Equinix-Ashburn-DC2"
   ```

   **Przykładowe dane wyjściowe**

   ```output
   {
   "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
   "availableBandwidths": [
    {
      "offerName": "100 Gbps",
      "valueInGbps": 100
    }
   ],
   "contact": "support@equinix.com",
   "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
   "location": null,
   "name": "Equinix-Ashburn-DC2",
   "provisioningState": "Succeeded",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ```
5. Utwórz zasób ExpressRoute Direct, który jest oparty na lokalizacji wybranej w poprzednich krokach.

   ExpressRoute Direct obsługuje hermetyzację QinQ i Dot1Q. W przypadku wybrania opcji QinQ każdy obwód ExpressRoute jest dynamicznie przypisywany do znacznika S-i jest unikatowy przez cały zasób ExpressRoute Direct. Każdy tag C w obwodzie musi być unikatowy w obwodzie, ale nie w ramach zasobu bezpośredniego ExpressRoute.  

   W przypadku wybrania hermetyzacji Dot1Q należy zarządzać unikatowością tagów języka C (VLAN) w całym zasobie ExpressRoute Direct.  

   > [!IMPORTANT]
   > ExpressRoute Direct może być tylko jednym typem hermetyzacji. Nie można zmienić typu hermetyzacji po utworzeniu zasobu ExpressRoute Direct.
   > 
 
   ```azurecli
   az network express-route port create -n $name -g $RGName --bandwidth 100 gbps  --encapsulation QinQ | Dot1Q --peering-location $PeeringLocationName -l $AzureRegion 
   ```

   > [!NOTE]
   > Można również ustawić atrybut **hermetyzacji** na **Dot1Q**. 
   >

   **Przykładowe dane wyjściowe**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "02ee21fe-4223-4942-a6bc-8d81daabc94f",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }  
   ```

## <a name="change-adminstate-for-links"></a><a name="state"></a>Zmień AdminState dla linków

Użyj tego procesu, aby przeprowadzić test warstwy 1. Upewnij się, że każde połączenie krzyżowe jest poprawnie poprawione na każdy router w portach podstawowych i dodatkowych.

1. Ustaw linki do **włączenia**. Powtórz ten krok, aby ustawić wszystkie łącza do **włączone**.

   Linki [0] są portem podstawowym, a linki [1] są portem pomocniczym.

   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[0].adminState="Enabled"
   ```
   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[1].adminState="Enabled"
   ```
   **Przykładowe dane wyjściowe**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "<resourceGUID>",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }
   ```

   Użyj tej samej procedury, aby obsłużyć porty przy użyciu programu `AdminState = "Disabled"` .

## <a name="create-a-circuit"></a><a name="circuit"></a>Tworzenie obwodu

Domyślnie można utworzyć 10 obwodów w ramach subskrypcji zawierającej zasób ExpressRoute Direct. Pomoc techniczna firmy Microsoft może zwiększyć domyślny limit. Użytkownik jest odpowiedzialny za śledzenie przydziałów i wykorzystania przepustowości. Przystosowana przepustowość to suma przepustowości wszystkich obwodów w zasobie ExpressRoute Direct. Wykorzystanie przepustowości jest fizycznym użyciem podstawowych interfejsów fizycznych.

Dodatkowe przepustowości obwodów w usłudze ExpressRoute Direct można używać tylko w celu obsługi scenariuszy opisanych w tym miejscu. Przepustowość wynosi 40 GB/s i 100 GB/s.

**SkuTier** może być lokalna, standardowa lub Premium.

**SkuFamily** może być tylko MeteredData. Nieograniczona nie jest obsługiwana w przypadku ExpressRoute Direct.

Utwórz obwód w zasobie ExpressRoute Direct:

  ```azurecli
  az network express-route create --express-route-port "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct" -n "Contoso-Direct-ckt" -g "Contoso-Direct-rg" --sku-family MeteredData --sku-tier Standard --bandwidth 100 Gbps
  ```

  Inne przepustowości obejmują 5 GB/s, 10 GB/s i 40 GB/s.

  **Przykładowe dane wyjściowe**

  ```output
  {
  "allowClassicOperations": false,
  "allowGlobalReach": false,
  "authorizations": [],
  "bandwidthInGbps": 100.0,
  "circuitProvisioningState": "Enabled",
  "etag": "W/\"<etagnumber>\"",
  "expressRoutePort": {
    "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
    "resourceGroup": "Contoso-Direct-rg"
  },
  "gatewayManagerEtag": "",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRouteCircuits/ERDirect-ckt-cli",
  "location": "westus",
  "name": "ERDirect-ckt-cli",
  "peerings": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "Contoso-Direct-rg",
  "serviceKey": "<serviceKey>",
  "serviceProviderNotes": null,
  "serviceProviderProperties": null,
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "MeteredData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "stag": null,
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits"
  }  
  ```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi ExpressRoute Direct, zobacz [Omówienie](expressroute-erdirect-about.md).
