---
title: 'Szybki Start: Tworzenie i modyfikowanie obwodu usługi ExpressRoute: interfejs wiersza polecenia platformy Azure'
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia, inicjowania obsługi, weryfikowania, aktualizowania, usuwania i anulowania aprowizacji obwodu usługi ExpressRoute przy użyciu interfejsu wiersza polecenia platformy Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: a1d50c3f8f94fbfd7dbcb9b25e051b7f2951c518
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969096"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-cli"></a>Szybki Start: Tworzenie i modyfikowanie obwodu usługi ExpressRoute przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku szybki start opisano sposób tworzenia obwodu usługi Azure ExpressRoute za pomocą interfejsu wiersza polecenia (CLI). W tym artykule pokazano również, jak sprawdzić stan, zaktualizować lub usunąć i anulować obsługę administracyjną obwodu.

## <a name="prerequisites"></a>Wymagania wstępne

* Przed rozpoczęciem konfiguracji Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) .
* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (2,0 lub nowszej). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Tworzenie i Inicjowanie obsługi obwodu ExpressRoute

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wybierz swoją subskrypcję

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

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>Pobierz listę obsługiwanych dostawców, lokalizacji i przepustowości

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

### <a name="create-an-expressroute-circuit"></a>Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)

> [!IMPORTANT]
> Obwód usługi ExpressRoute jest rozliczany od momentu, gdy zostanie wystawiony klucz usług. Wykonaj tę operację, gdy dostawca łączności jest gotowy do aprowizacji obwodu.
>
>

Jeśli nie masz jeszcze grupy zasobów, musisz ją utworzyć przed utworzeniem obwodu ExpressRoute. Grupę zasobów można utworzyć, uruchamiając następujące polecenie:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

Poniższy przykład pokazuje, jak utworzyć obwód ExpressRoute 200 MB/s za pomocą Equinix w Dolina krzemu. Jeśli używasz innego dostawcy i innych ustawień, Zastąp te informacje podczas żądania.

Upewnij się, że określono poprawną warstwę SKU i rodzinę SKU:

* Warstwa SKU określa, czy obwód ExpressRoute jest [lokalny](expressroute-faqs.md#expressroute-local), standardowy, czy [Premium](expressroute-faqs.md#expressroute-premium). Możesz określić *Local*, * Standard lub *Premium*. Nie można zmienić jednostki SKU z warstwy *standardowa/Premium* na *lokalną*.
* Rodzina SKU określa typ rozliczeń. Możesz określić *MeteredData* dla mierzonego planu taryfowego i *UnlimitedData* dla nieograniczonego planu taryfowego. Typ rozliczeń można zmienić z *MeteredData* na *UnlimitedData*, ale nie można zmienić typu z *UnlimitedData* na *MeteredData*. Obwód *lokalny* jest tylko *UnlimitedData* .


Obwód usługi ExpressRoute jest rozliczany od momentu, gdy zostanie wystawiony klucz usług. Poniższy przykład to żądanie nowego klucza usługi:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Odpowiedź zawiera klucz usługi.

### <a name="list-all-expressroute-circuits"></a>Wyświetl wszystkie obwody usługi ExpressRoute

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

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Wyślij klucz usługi do dostawcy połączenia w celu aprowizacji

Element "ServiceProviderProvisioningState" zawiera informacje o bieżącym stanie aprowizacji po stronie dostawcy usług. Stan zapewnia informacje o stanie po stronie firmy Microsoft. Aby uzyskać więcej informacji, zobacz [artykuł przepływy pracy](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Podczas tworzenia nowego obwodu ExpressRoute obwód jest w następującym stanie:

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Obwód zostanie zmieniony na następujący stan, gdy dostawca łączności aktualnie go włączy:

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Aby użyć obwodu ExpressRoute, musi on być w następującym stanie:

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Okresowe sprawdzanie stanu i stanu klucza obwodu

Sprawdzenie stanu i stanu klucza usługi poinformuje Cię, gdy dostawca zaudostępniał obwód. Po skonfigurowaniu obwodu *ServiceProviderProvisioningState* pojawia się zgodnie z *zainicjowaną obsługą*, jak pokazano w następującym przykładzie:

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

### <a name="create-your-routing-configuration"></a>Utwórz konfigurację routingu

Aby uzyskać instrukcje krok po kroku, zobacz artykuł [Konfiguracja routingu obwodów usługi ExpressRoute](howto-routing-cli.md) w celu utworzenia i zmodyfikowania komunikacji równorzędnej obwodu.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwodów, które są tworzone za pomocą dostawców usług oferujących usługi łączności warstwy 2. Jeśli używasz dostawcy usług oferującego zarządzane usługi warstwy 3 (zazwyczaj sieć VPN sieci IP, na przykład MPLS), dostawca łączności konfiguruje i zarządza routingiem.
>
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Łączenie sieci wirtualnej z obwodem usługi ExpressRoute

Następnie połącz sieć wirtualną z obwodem ExpressRoute. Skorzystaj z artykułu [łączenie sieci wirtualnych do obwodów usługi ExpressRoute](howto-linkvnet-cli.md) .

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modyfikowanie obwodu ExpressRoute

Niektóre właściwości obwodu usługi ExpressRoute można modyfikować bez wpływu na łączność. Możesz wprowadzić następujące zmiany bez przestoju:

* Możesz włączyć lub wyłączyć dodatek ExpressRoute Premium dla obwodu usługi ExpressRoute. Zmiana jednostki SKU z warstwy *standardowa/Premium* na *lokalną* nie jest obsługiwana.
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

* Przed obniżeniem wersji Premium do standard należy upewnić się, że liczba sieci wirtualnych, które są połączone z obwodem, jest mniejsza niż 10. Jeśli tego nie zrobisz, żądanie aktualizacji zakończy się niepowodzeniem, a opłaty są naliczane według stawek za usługę Premium.
* Należy najpierw odłączyć wszystkie sieci wirtualne w innych regionach geopolitycznych. Jeśli nie usuniesz linku, żądanie aktualizacji zakończy się niepowodzeniem i będziemy nadal korzystać z stawek za usługę Premium.
* Tabela tras musi być krótsza niż 4 000 tras dla prywatnej komunikacji równorzędnej. Jeśli rozmiar tabeli tras przekracza 4 000 tras, sesja protokołu BGP zostanie porzucana. Sesja BGP nie zostanie włączona jeszcze raz, dopóki liczba anonsowanych prefiksów nie będzie objęta 4 000.

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

Obwód zostanie uaktualniony po stronie firmy Microsoft. Następnie należy skontaktować się z dostawcą połączenia w celu zaktualizowania konfiguracji po stronie, aby dopasować tę zmianę. Po dokonaniu tego powiadomienia zaczynamy naliczać opłaty za zaktualizowaną opcję przepustowości.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Aby przenieść jednostkę SKU z pomiaru do nieograniczonego

Jednostkę SKU obwodu usługi ExpressRoute można zmienić, korzystając z następującego przykładu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Aby kontrolować dostęp do środowisk klasycznych i Menedżer zasobów

Zapoznaj się z instrukcjami w temacie [Move ExpressRoute Circuits z klasycznego modelu wdrażania Menedżer zasobów](expressroute-howto-move-arm.md).

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Anulowanie aprowizacji obwodu ExpressRoute

Aby anulować obsługę administracyjną i usunąć obwód ExpressRoute, upewnij się, że rozumiesz następujące kryteria:

* Wszystkie sieci wirtualne muszą być odłączone od obwodu usługi ExpressRoute. Jeśli ta operacja zakończy się niepowodzeniem, sprawdź, czy sieci wirtualne są połączone z obwodem.
* Jeśli stan aprowizacji dostawcy usługi obwodu ExpressRoute jest inicjowany lub **Zainicjowano obsługę administracyjną** , należy skontaktować się z dostawcą usług w celu **anulowania aprowizacji** obwodu po stronie. Nadal rezerwujemy zasoby i obciążamy Cię, dopóki dostawca usług nie ukończy anulowania aprowizacji obwodu i powiadamia nas.
* Jeśli dostawca usług anulował obsługę administracyjną tego obwodu, oznacza to, że stan aprowizacji dostawcy usług jest ustawiony na **nieinicjowany**, można usunąć obwód. Naliczanie opłat za obwód zostanie zatrzymane.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Obwód ExpressRoute można usunąć, uruchamiając następujące polecenie:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obwodu i zainicjowaniu obsługi administracyjnej z dostawcą przejdź do następnego kroku, aby skonfigurować komunikację równorzędną:

> [!div class="nextstepaction"]
> [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](howto-routing-cli.md)
