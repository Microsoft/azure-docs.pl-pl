---
title: 'Azure ExpressRoute: Konfigurowanie komunikacji równorzędnej: interfejs wiersza polecenia'
description: Ten artykuł pomaga utworzyć i zainicjować obsługę komunikacji równorzędnej prywatnej, publicznej i firmy Microsoft obwodu ExpressRoute. W tym artykule opisano również, jak aktualizować i usuwać komunikację równoległą dla obwodu oraz sprawdzać jej stan.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/24/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: c20b0741f12ee2cab40d8f5b53c51ad537801bf2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89393177"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute przy użyciu interfejsu wiersza polecenia

Ten artykuł pomaga utworzyć konfigurację routingu/komunikację równorzędną dla obwodu usługi ExpressRoute w Menedżer zasobów modelu wdrażania przy użyciu interfejsu wiersza polecenia i zarządzać nią. Możesz również sprawdzić stan, aktualizowanie i usuwanie komunikacji równorzędnej dla obwodu usługi ExpressRoute. Jeśli chcesz użyć innej metody do pracy z obwodem, wybierz artykuł z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-routing-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md)
> * [Publiczna Komunikacja równorzędna](about-public-peering.md)
> * [Wideo — prywatna Komunikacja równorzędna](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Wideo — Komunikacja równorzędna firmy Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji

* Przed rozpoczęciem zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (wersję 2.0 lub nowszą). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Przed rozpoczęciem konfiguracji upewnij się, że zostały sprawdzone wymagania [wstępne](expressroute-prerequisites.md) [dotyczące wymagań routingu](expressroute-routing.md)i stron [przepływu pracy](expressroute-workflows.md) .
* Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](howto-circuit-cli.md), który powinien zostać włączony przez dostawcę połączenia. Obwód usługi ExpressRoute musi być w stanie aprowizacji i włączony, aby można było uruchamiać polecenia z tego artykułu.

Te instrukcje dotyczą tylko obwodów utworzonych przy pomocy dostawców oferujących usługi łączności warstwy 2. Jeśli używasz dostawcy usług oferującego zarządzane usługi warstwy 3 (zwykle IPVPN, takie jak MPLS), dostawca łączności skonfiguruje dla Ciebie Routing i zarządza nim.

Można skonfigurować prywatną komunikację równorzędną i komunikację równorzędną firmy Microsoft dla obwodu usługi ExpressRoute (publiczna Komunikacja równorzędna Azure jest przestarzała dla nowych obwodów). Komunikację równorzędną można skonfigurować w dowolnej wybranej kolejności. Musisz jednak pamiętać, aby kończyć konfiguracje poszczególnych komunikacji równorzędnych pojedynczo. Aby uzyskać więcej informacji o domenach routingu i komunikacji równorzędnej, zobacz [ExpressRoute Routing domen](expressroute-circuit-peerings.md). Aby uzyskać informacje na temat publicznej komunikacji równorzędnej, zobacz [ExpressRoute publicznej komunikacji równorzędnej](about-public-peering.md).

## <a name="microsoft-peering"></a><a name="msft"></a>Komunikacja równorzędna firmy Microsoft

Ta sekcja ułatwia tworzenie, pobieranie, aktualizowanie i usuwanie konfiguracji komunikacji równorzędnej firmy Microsoft dla obwodu usługi ExpressRoute.

> [!IMPORTANT]
> Komunikacja równorzędna firmy Microsoft ze obwodów usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017, będzie miała wszystkie prefiksy usług anonsowane za pomocą komunikacji równorzędnej firmy Microsoft, nawet jeśli filtry tras nie są zdefiniowane. Komunikacja równorzędna firmy Microsoft obwodów usługi ExpressRoute, które są skonfigurowane w dniu lub po 1 sierpnia 2017, nie będzie miała żadnych prefiksów anonsowanych do momentu dołączenia do obwodu filtru tras. Aby uzyskać więcej informacji, zobacz [Konfigurowanie filtru tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Aby utworzyć komunikację równorzędną Microsoft

1. Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure. Użyj najnowszej wersji interfejsu wiersza polecenia platformy Azure. Przed rozpoczęciem konfiguracji Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) .

   ```azurecli
   az login
   ```

   Wybierz subskrypcję, dla której chcesz utworzyć obwód usługi ExpressRoute.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Utwórz obwód usługi ExpressRoute. Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](howto-circuit-cli.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz polecić dostawcę połączenia, aby włączyć komunikację równorzędną firmy Microsoft. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jeśli jednak dostawca łączności nie zarządza usługą dla Ciebie, po utworzeniu obwodu kontynuuj konfigurację przy użyciu następnych kroków. 

3. Sprawdź obwód ExpressRoute, aby upewnić się, że jest on zainicjowany i włączony. Skorzystaj z następującego przykładu:

   ```azurecli
   az network express-route list
   ```

   Odpowiedź jest podobna do poniższego przykładu:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Skonfiguruj komunikację równorzędną Microsoft dla obwodu. Zanim przejdziesz dalej, upewnij się, że masz poniższe informacje.

   * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR.
   * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Anonsowane prefiksy: musisz podać listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli planujesz wysłanie zestawu prefiksów, możesz wysłać listę rozdzieloną przecinkami. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR.
   * **Opcjonalne-** Numer ASN klienta: w przypadku anonsowania prefiksów, które nie są zarejestrowane w komunikacji równorzędnej jako liczba, można określić liczbę AS, do której są zarejestrowani.
   * Nazwa rejestru routingu: możesz określić RIR/IRR, względem którego rejestrowany jest numer AS i prefiksy.
   * **Opcjonalne-** Skrót MD5, jeśli zdecydujesz się na użycie jednego z nich.

   Uruchom Poniższy przykład, aby skonfigurować komunikację równorzędną firmy Microsoft dla obwodu:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Aby wyświetlić szczegóły dotyczące komunikacji równorzędnej firmy Microsoft

Szczegóły konfiguracji można uzyskać, korzystając z następującego przykładu:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> Firma Microsoft sprawdza, czy określone "anonsowane publiczne prefiksy" i "peer ASN" (lub "ASN klienta") są przypisane do Ciebie w rejestrze routingu internetowego. Jeśli otrzymujesz publiczne prefiksy z innej jednostki i jeśli przypisanie nie zostanie zarejestrowane w rejestrze routingu, automatyczne sprawdzanie poprawności nie zostanie ukończone i będzie wymagać ręcznej walidacji. Jeśli automatyczne sprawdzanie poprawności nie powiedzie się, zobaczysz "AdvertisedPublicPrefixesState" w danych wyjściowych powyższego polecenia. 
> 
> Jeśli zobaczysz komunikat "wymagana Walidacja", Zbierz dokumenty, które pokazują, że publiczne prefiksy są przypisane do organizacji przez jednostkę wymienioną jako właściciel prefiksów w rejestrze routingu i prześlij te dokumenty w celu ręcznej weryfikacji, otwierając bilet pomocy technicznej, jak pokazano poniżej. 
> 
>

Dane wyjściowe są podobne do poniższego przykładu:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzureMicrosoftPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": {
    "advertisedPublicPrefixes": [
        ""
      ],
     "advertisedPublicPrefixesState": "",
     "customerASN": ,
     "routingRegistryName": ""
  }
  "name": "AzureMicrosoftPeering",
  "peerAsn": ,
  "peeringType": "AzureMicrosoftPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Aby zaktualizować konfigurację komunikacji równorzędnej firmy Microsoft

Możesz zaktualizować dowolną część konfiguracji. Anonsowane prefiksy obwodu są aktualizowane z 123.1.0.0/24 do 124.1.0.0/24 w następującym przykładzie:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="to-add-ipv6-microsoft-peering-settings-to-an-existing-ipv4-configuration"></a><a name="addIPv6msft"></a>Aby dodać ustawienia komunikacji równorzędnej Microsoft IPv6 do istniejącej konfiguracji IPv4

```azurecli
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Aby usunąć komunikację równorzędną firmy Microsoft

Konfigurację komunikacji równorzędnej można usunąć, uruchamiając Poniższy przykład:

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

## <a name="azure-private-peering"></a><a name="private"></a>Prywatna komunikacja równorzędna platformy Azure

Ta sekcja ułatwia tworzenie, pobieranie, aktualizowanie i usuwanie konfiguracji prywatnej komunikacji równorzędnej Azure dla obwodu usługi ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Aby utworzyć prywatną komunikację równorzędną

1. Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure. Musisz użyć najnowszej wersji interfejsu wiersza polecenia platformy Azure (CLI). * Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.

   ```azurecli
   az login
   ```

   Wybierz subskrypcję, w ramach której chcesz utworzyć obwód usługi ExpressRoute.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Utwórz obwód usługi ExpressRoute. Wypełnij instrukcje, aby utworzyć [obwód usługi ExpressRoute](howto-circuit-cli.md), który zostanie zainicjowany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz polecić dostawcę połączenia, aby włączyć prywatną komunikację równorzędną Azure. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jeśli jednak dostawca łączności nie zarządza usługą dla Ciebie, po utworzeniu obwodu kontynuuj konfigurację przy użyciu następnych kroków.

3. Sprawdź obwód ExpressRoute, aby upewnić się, że jest on zainicjowany i włączony. Skorzystaj z następującego przykładu:

   ```azurecli
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
   ```

   Odpowiedź jest podobna do poniższego przykładu:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
   "bandwidthInMbps": 200,
   "peeringLocation": "Silicon Valley",
   "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Skonfiguruj prywatną komunikację równorzędną Azure dla obwodu. Zanim przejdziesz do następnych kroków, upewnij się, czy masz następujące elementy:

   * Podsieć /30 dla połączenia podstawowego. Podsieć nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych.
   * Podsieć /30 dla połączenia dodatkowego. Podsieć nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS. Możesz użyć prywatnego numeru AS dla tej komunikacji równorzędnej. Pamiętaj, aby nie używać numeru 65515.
   * **Opcjonalne-** Skrót MD5, jeśli zdecydujesz się na użycie jednego z nich.

   Skorzystaj z następującego przykładu, aby skonfigurować prywatną komunikację równorzędną Azure dla obwodu:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   Jeśli zdecydujesz się używać skrótu MD5, użyj poniższego przykładu:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Pamiętaj, aby określić numer AS jako ASN komunikacji równorzędnej, a nie ASN klienta.
   > 
   > 

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Aby wyświetlić szczegóły dotyczące prywatnej komunikacji równorzędnej Azure

Szczegóły konfiguracji można uzyskać, korzystając z następującego przykładu:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

Dane wyjściowe są podobne do poniższego przykładu:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePrivatePeering",
  "ipv6PeeringConfig": null,
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePrivatePeering",
  "peerAsn": 7671,
  "peeringType": "AzurePrivatePeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Aby zaktualizować konfigurację prywatnej komunikacji równorzędnej Azure

Możesz zaktualizować dowolną część konfiguracji, korzystając z poniższego przykładu. W tym przykładzie identyfikator sieci VLAN obwodu jest aktualizowany z 100 do 500.

```azurecli
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Aby usunąć prywatną komunikację równorzędną Azure

Konfigurację komunikacji równorzędnej można usunąć, uruchamiając Poniższy przykład:

> [!WARNING]
> Przed uruchomieniem tego przykładu należy upewnić się, że wszystkie sieci wirtualne i ExpressRoute Global Reach są usuwane. 
> 
> 

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```


## <a name="next-steps"></a>Następne kroki

Następny krok: [Link a VNet to an ExpressRoute circuit](howto-linkvnet-cli.md) (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute).

* Więcej informacji na temat przepływów pracy usługi ExpressRoute znajduje się w artykule [ExpressRoute workflows](expressroute-workflows.md) (Przepływy pracy usługi ExpressRoute).
* Aby uzyskać więcej informacji o komunikacji równorzędnej obwodu, zobacz artykuł [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute).
* Więcej informacji na temat pracy z sieciami wirtualnymi znajduje się w artykule [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Omówienie sieci wirtualnych).
