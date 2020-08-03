---
title: 'Tworzenie i modyfikowanie obwodu usługi ExpressRoute: interfejs wiersza polecenia platformy Azure'
description: W tym artykule przedstawiono sposób tworzenia, inicjowania obsługi, sprawdzania, aktualizowania, usuwania i anulowania aprowizacji obwodu usługi ExpressRoute przy użyciu interfejsu wiersza polecenia.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 11/13/2019
ms.author: cherylmc
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2d8ad6b90f533161835ab12a43865b177c24c14e
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503586"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Tworzenie i modyfikowanie obwodu usługi ExpressRoute za pomocą interfejsu wiersza polecenia


W tym artykule opisano sposób tworzenia obwodu usługi Azure ExpressRoute za pomocą interfejsu wiersza polecenia (CLI). W tym artykule pokazano również, jak sprawdzić stan, zaktualizować lub usunąć i anulować obsługę administracyjną obwodu. Jeśli chcesz użyć innej metody do pracy z obwodami usługi ExpressRoute, możesz wybrać artykuł z poniższej listy:

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Szablon Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Wideo — Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Przed rozpoczęciem zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (wersję 2.0 lub nowszą). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli).
* Przed rozpoczęciem konfiguracji Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) .

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Tworzenie i Inicjowanie obsługi obwodu ExpressRoute

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Zaloguj się do konta platformy Azure i wybierz swoją subskrypcję

Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure. Jeśli używasz CloudShell "Wypróbuj", nastąpi automatyczne logowanie. Poniższe przykłady ułatwiają nawiązanie połączenia:

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

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Pobierz listę obsługiwanych dostawców, lokalizacji i przepustowości

Przed utworzeniem obwodu usługi ExpressRoute należy uzyskać listę obsługiwanych dostawców połączeń, lokalizacji i opcji przepustowości. Polecenie interfejsu wiersza polecenia `az network express-route list-service-providers` zwraca te informacje, które będą używane w kolejnych krokach:

```azurecli-interactive
az network express-route list-service-providers
```

Odpowiedź jest podobna do poniższego przykładu:

```output
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

Sprawdź odpowiedź, aby sprawdzić, czy dostawca połączenia jest wymieniony. Zanotuj poniższe informacje, które będą potrzebne podczas tworzenia obwodu:

* Nazwa
* PeeringLocations
* BandwidthsOffered

Teraz można przystąpić do tworzenia obwodu ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Tworzenie obwodu ExpressRoute

> [!IMPORTANT]
> Obwód usługi ExpressRoute jest rozliczany od momentu, gdy zostanie wystawiony klucz usług. Wykonaj tę operację, gdy dostawca łączności jest gotowy do aprowizacji obwodu.
>
>

Jeśli nie masz jeszcze grupy zasobów, musisz ją utworzyć przed utworzeniem obwodu ExpressRoute. Grupę zasobów można utworzyć, uruchamiając następujące polecenie:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

Poniższy przykład pokazuje, jak utworzyć obwód ExpressRoute 200 MB/s za pomocą Equinix w Dolina krzemu. Jeśli używasz innego dostawcy i innych ustawień, podstaw te informacje podczas żądania.

Upewnij się, że określono poprawną warstwę SKU i rodzinę SKU:

* Warstwa SKU określa, czy obwód ExpressRoute jest [lokalny](expressroute-faqs.md#expressroute-local), standardowy czy [Premium](expressroute-faqs.md#expressroute-premium). Możesz określić *Local*, *Standard* lub *Premium*.
* Rodzina SKU określa typ rozliczeń. Możesz określić *Metereddata* dla mierzonego planu taryfowego i *Unlimiteddata* dla nieograniczonego planu taryfowego. Typ rozliczeń można zmienić z *Metereddata* na *Unlimiteddata*, ale nie można zmienić typu z *Unlimiteddata* na *Metereddata*. Obwód *lokalny* jest tylko *Unlimiteddata* .


Obwód usługi ExpressRoute jest rozliczany od momentu, gdy zostanie wystawiony klucz usług. Poniższy przykład to żądanie nowego klucza usługi:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Odpowiedź zawiera klucz usługi.

### <a name="4-list-all-expressroute-circuits"></a>4. Wyświetl wszystkie obwody usługi ExpressRoute

Aby uzyskać listę wszystkich utworzonych obwodów usługi ExpressRoute, uruchom `az network express-route list` polecenie. Te informacje można pobrać w dowolnym momencie za pomocą tego polecenia. Aby wyświetlić listę wszystkich obwodów, należy wywołać wywołanie bez parametrów.

```azurecli-interactive
az network express-route list
```

Twój klucz usługi jest wymieniony w polu *ServiceKey* odpowiedzi.

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
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

Aby uzyskać szczegółowe opisy wszystkich parametrów, należy uruchomić polecenie przy użyciu parametru "-h".

```azurecli-interactive
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Wyślij klucz usługi do dostawcy połączenia w celu aprowizacji

Element "ServiceProviderProvisioningState" zawiera informacje o bieżącym stanie aprowizacji po stronie dostawcy usług. Stan zapewnia informacje o stanie po stronie firmy Microsoft. Aby uzyskać więcej informacji, zobacz [artykuł przepływy pracy](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Podczas tworzenia nowego obwodu ExpressRoute obwód jest w następującym stanie:

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Obwód zostanie zmieniony na następujący stan, gdy dostawca łączności jest w trakcie jego włączania:

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Aby móc korzystać z obwodu usługi ExpressRoute, musi on być w następującym stanie:

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. okresowo sprawdza stan i stan klucza obwodu.

Sprawdzenie stanu i stanu klucza obwodu pozwala sprawdzić, kiedy dostawca włączył obwód. Po skonfigurowaniu obwodu "ServiceProviderProvisioningState" jest wyświetlany jako "zainicjowany", jak pokazano w następującym przykładzie:

```azurecli-interactive
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
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="7-create-your-routing-configuration"></a>7. Utwórz konfigurację routingu

Aby uzyskać instrukcje krok po kroku, zobacz artykuł [Konfiguracja routingu obwodów usługi ExpressRoute](howto-routing-cli.md) w celu utworzenia i zmodyfikowania komunikacji równorzędnej obwodu.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwodów, które są tworzone za pomocą dostawców usług oferujących usługi łączności warstwy 2. Jeśli używasz dostawcy usług oferującego zarządzane usługi warstwy 3 (zazwyczaj sieć VPN sieci IP, na przykład MPLS), dostawca łączności konfiguruje i zarządza routingiem.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Łączenie sieci wirtualnej z obwodem ExpressRoute

Następnie połącz sieć wirtualną z obwodem ExpressRoute. Skorzystaj z artykułu [łączenie sieci wirtualnych do obwodów usługi ExpressRoute](howto-linkvnet-cli.md) .

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modyfikowanie obwodu ExpressRoute

Niektóre właściwości obwodu usługi ExpressRoute można modyfikować bez wpływu na łączność. Możesz wprowadzić następujące zmiany bez przestoju:

* Możesz włączyć lub wyłączyć dodatek ExpressRoute Premium dla obwodu usługi ExpressRoute.
* Można zwiększyć przepustowość obwodu ExpressRoute pod warunkiem, że na porcie jest dostępna pojemność. Jednak obniżenie przepustowości obwodu nie jest obsługiwane.
* Plan pomiaru można zmienić z danych mierzonych na dane nieograniczone. Jednak zmiana planu pomiaru z nieograniczonej ilości danych na dane taryfowe nie jest obsługiwana.
* Można włączać i wyłączać *Zezwalanie na klasyczne operacje*.

Aby uzyskać więcej informacji o limitach i ograniczeniach, zobacz [często zadawane pytania](expressroute-faqs.md)dotyczące usługi ExpressRoute.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Aby włączyć dodatek ExpressRoute Premium

Dodatek ExpressRoute Premium dla istniejącego obwodu można włączyć za pomocą następującego polecenia:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Obwód ma teraz włączone funkcje dodatku ExpressRoute Premium. Zaczynamy naliczać opłaty za dodatek Premium, gdy tylko polecenie zostało pomyślnie uruchomione.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Aby wyłączyć dodatek ExpressRoute Premium

> [!IMPORTANT]
> Ta operacja może zakończyć się niepowodzeniem, jeśli używasz zasobów, które są większe niż dozwolone dla obwodu standardowego.
>
>

Przed wyłączeniem dodatku ExpressRoute Premium zapoznaj się z następującymi kryteriami:

* Przed obniżeniem wersji Premium do standard należy upewnić się, że masz mniej niż 10 sieci wirtualnych podłączonych do obwodu. Jeśli masz więcej niż 10, żądanie aktualizacji zakończy się niepowodzeniem, a opłaty są naliczane według stawek za usługę Premium.
* Należy odłączyć wszystkie sieci wirtualne w innych regionach geopolitycznych. Jeśli nie odłączysz wszystkich sieci wirtualnych, żądanie aktualizacji zakończy się niepowodzeniem, a opłaty są naliczane zgodnie z stawką za usługę Premium.
* Tabela tras musi być krótsza niż 4 000 tras dla prywatnej komunikacji równorzędnej. Jeśli rozmiar tabeli tras przekracza 4 000 tras, sesja BGP spadnie. Sesja nie zostanie ponownie włączona, dopóki liczba anonsowanych prefiksów nie będzie mniejsza niż 4 000.

Dodatek ExpressRoute Premium dla istniejącego obwodu można wyłączyć, korzystając z następującego przykładu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Aby zaktualizować przepustowość obwodu ExpressRoute

W przypadku opcji obsługiwanej przepustowości dla dostawcy zapoznaj się z tematem [często zadawanych pytań](expressroute-faqs.md)dotyczących usługi ExpressRoute. Można wybrać rozmiar większy niż rozmiar istniejącego obwodu.

> [!IMPORTANT]
> Jeśli istniejący port nie ma odpowiedniej pojemności, może być konieczne ponowne utworzenie obwodu ExpressRoute. Nie można uaktualnić obwodu, jeśli w tej lokalizacji nie ma dodatkowej pojemności.
>
> Nie można zmniejszyć przepustowości obwodu ExpressRoute bez zakłóceń. Obniżenie przepustowości wymaga anulowania aprowizacji obwodu ExpressRoute, a następnie ponownego aprowizacji nowego obwodu ExpressRoute.
>

Po określeniu wymaganego rozmiaru Użyj następującego polecenia, aby zmienić rozmiar obwodu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Rozmiar obwodu jest ustawiany po stronie firmy Microsoft. Następnie należy skontaktować się z dostawcą połączenia w celu zaktualizowania konfiguracji po stronie, aby dopasować tę zmianę. Po dokonaniu tego powiadomienia zaczynamy naliczać opłaty za zaktualizowaną opcję przepustowości.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Aby przenieść jednostkę SKU z pomiaru do nieograniczonego

Jednostkę SKU obwodu usługi ExpressRoute można zmienić, korzystając z następującego przykładu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Aby kontrolować dostęp do środowisk klasycznych i Menedżer zasobów

Zapoznaj się z instrukcjami w temacie [Move ExpressRoute Circuits z klasycznego modelu wdrażania Menedżer zasobów](expressroute-howto-move-arm.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Anulowanie aprowizacji i usuwanie obwodu usługi ExpressRoute

Aby anulować obsługę administracyjną i usunąć obwód ExpressRoute, upewnij się, że rozumiesz następujące kryteria:

* Musisz odłączyć wszystkie sieci wirtualne od obwodu usługi ExpressRoute. Jeśli ta operacja zakończy się niepowodzeniem, sprawdź, czy sieci wirtualne są połączone z obwodem.
* Jeśli stan aprowizacji dostawcy usługi obwodu ExpressRoute **jest inicjowany lub** **zainicjowano**obsługę administracyjną, należy skontaktować się z dostawcą usług w celu anulowania aprowizacji obwodu po stronie. Nadal rezerwujemy zasoby i obciążamy Cię, dopóki dostawca usług nie ukończy anulowania aprowizacji obwodu i powiadamia nas.
* Obwód można usunąć, jeśli dostawca usług anulował obsługę administracyjną obwodu. W przypadku anulowania **aprowizacji**obwodu stan aprowizacji dostawcy usług jest ustawiony na nieudostępniane. Spowoduje to zatrzymanie naliczania opłat za obwód.

Obwód ExpressRoute można usunąć, uruchamiając następujące polecenie:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obwodu upewnij się, że wykonano następujące zadania:

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](howto-routing-cli.md)
* [Łączenie sieci wirtualnej z obwodem ExpressRoute](howto-linkvnet-cli.md)
