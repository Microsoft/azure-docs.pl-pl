---
title: 'Szybki start: tworzenie i modyfikowanie obwodu za pomocą expressRoute — Azure PowerShell'
description: W tym przewodniku Szybki start pokazano, jak utworzyć, aprowizować, weryfikować, aktualizować, usuwać i coprowizować obwód usługi ExpressRoute.
services: expressroute
author: duongau
ms.author: duau
ms.date: 10/12/2020
ms.topic: quickstart
ms.service: expressroute
ms.custom:
- mode-api
ms.openlocfilehash: 50307e40526f5cce4d1349180b29fac00fb70943
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534961"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-powershell"></a>Szybki start: tworzenie i modyfikowanie obwodu usługi ExpressRoute przy użyciu Azure PowerShell

W tym przewodniku Szybki start pokazano, jak utworzyć obwód usługi ExpressRoute przy użyciu poleceń cmdlet programu PowerShell Azure Resource Manager modelu wdrażania. Możesz również sprawdzić stan, zaktualizować, usunąć lub coprowizować obwód.

## <a name="prerequisites"></a>Wymagania wstępne

* Przed rozpoczęciem [konfiguracji przejrzyj wymagania wstępne](expressroute-prerequisites.md) [i](expressroute-workflows.md) przepływy pracy.
* Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure PowerShell zainstalowane lokalnie lub Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Tworzenie i aprowizować obwód usługi ExpressRoute
### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wybierz swoją subskrypcję

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>Pobierz listę obsługiwanych dostawców, lokalizacji i przepustowości
Przed utworzeniem obwodu usługi ExpressRoute potrzebna jest lista obsługiwanych dostawców łączności, lokalizacji i opcji przepustowości.

Polecenie cmdlet programu PowerShell **Get-AzExpressRouteServiceProvider** zwraca te informacje, których użyjesz w kolejnych krokach:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Sprawdź, czy twój dostawca połączenia znajduje się na liście. Zanotuj następujące informacje, które będą potrzebne później podczas tworzenia obwodu:

* Nazwa
* Lokalizacja komunikacji równorzędnej
* BandwidthsOffered

Teraz możesz utworzyć obwód expressroute.

### <a name="create-an-expressroute-circuit"></a>Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)
Jeśli nie masz jeszcze grupy zasobów, musisz utworzyć nową przed utworzeniem obwodu expressRoute. Możesz to zrobić, uruchamiając następujące polecenie:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

W poniższym przykładzie pokazano, jak utworzyć obwód expressRoute o rozmiarze 200 Mb/s za pośrednictwem platformy Equinix w Doli Krzemowej. Jeśli używasz innego dostawcy i różnych ustawień, zastąp te informacje podczas żądania. Użyj następującego przykładu, aby zażądać nowego klucza usługi:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Upewnij się, że określono prawidłową warstwę SKU i rodzinę SKU:

* Warstwa SKU określa, czy obwód usługi ExpressRoute ma warstwie [Lokalna,](expressroute-faqs.md#expressroute-local)Standardowa, [czy Premium.](expressroute-faqs.md#expressroute-premium) Możesz określić wartość *Lokalna,**Standardowa lub *Premium.* Nie można zmienić wersji *standardowej/Premium* na *lokalną.*
* Rodzina SKU określa typ rozliczeń. Możesz określić wartość *MeteredData dla* taryfowego planu danych i *wartość UnlimitedData* dla nieograniczonego planu danych. Typ rozliczeń można zmienić z *MeteredData* na *UnlimitedData,* ale nie można zmienić typu z *UnlimitedData* na *MeteredData.* Obwód *lokalny to* zawsze *UnlimitedData.*

> [!IMPORTANT]
> Obwód usługi ExpressRoute jest rozliczany od momentu, gdy zostanie wystawiony klucz usługi. Upewnij się, że tę operację wykonujesz, gdy dostawca połączenia jest gotowy do aprowizowania obwodu.
>

Odpowiedź zawiera klucz usługi. Szczegółowe opisy wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="list-all-expressroute-circuits"></a>Lista wszystkich obwodów expressRoute
Aby uzyskać listę wszystkich utworzonych obwodów expressRoute, uruchom polecenie **Get-AzExpressRouteCircuit:**

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Odpowiedź wygląda podobnie do następującego przykładu:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState          : Enabled
ServiceProviderProvisioningState  : NotProvisioned
ServiceProviderNotes              :
ServiceProviderProperties         : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                        : **************************************
Peerings                          : []
```

Te informacje można pobrać w dowolnym momencie za pomocą `Get-AzExpressRouteCircuit` polecenia cmdlet . Wywołanie bez parametrów wyświetla listę wszystkich obwodów. Klucz usługi jest wymieniony w *polu ServiceKey:*

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```

Odpowiedź wygląda podobnie do następującego przykładu:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Wyślij klucz usługi do dostawcy łączności w celu aprowizowania
*Stan ServiceProviderProvisioningState* zawiera informacje o bieżącym stanie aprowowania po stronie dostawcy usług. Stan zawiera stan po stronie firmy Microsoft. Aby uzyskać więcej informacji o stanach aprowowania obwodu, zobacz [Workflows ( Przepływy pracy).](expressroute-workflows.md#expressroute-circuit-provisioning-states)

Podczas tworzenia nowego obwodu expressRoute obwód jest w następującym stanie:

```azurepowershell
ServiceProviderProvisioningState : NotProvisioned
CircuitProvisioningState         : Enabled
```

Obwód zmieni się na następujący, gdy dostawca połączenia aktualnie go dla Ciebie wł.

```azurepowershell
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

Aby można było korzystać z obwodu expressRoute, musi on mieć następujący stan:

```azurepowershell
ServiceProviderProvisioningState : Provisioned
CircuitProvisioningState         : Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Okresowe sprawdzanie stanu i stanu klucza obwodu
Sprawdzenie stanu i stanu klucza usługi spowoduje, że dostawca zaaprowizuje obwód. Po skonfigurowaniu obwodu stan *ServiceProviderProvisioningState* będzie wyświetlany jako Aprowizowany, jak pokazano w poniższym przykładzie: 

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Odpowiedź wygląda podobnie do następującego przykładu:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
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
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

### <a name="create-your-routing-configuration"></a>Tworzenie konfiguracji routingu
Aby uzyskać instrukcje krok po kroku, zobacz artykuł [ExpressRoute circuit routing configuration (Konfiguracja routingu](expressroute-howto-routing-arm.md) obwodu usługi ExpressRoute), aby tworzyć i modyfikować komunikacji równorzędne obwodów.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwodów utworzonych za pomocą dostawców usług, którzy oferują usługi łączności warstwy 2. Jeśli używasz dostawcy usług, który oferuje usługi zarządzane w warstwie 3 (zazwyczaj sieć VPN ip, na przykład MPLS), dostawca połączenia konfiguruje routing i zarządza tym routingiem.
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Łączenie sieci wirtualnej z obwodem usługi ExpressRoute
Następnie połącz sieć wirtualną z obwodem usługi ExpressRoute. Podczas pracy z modelem wdrażania usługi Resource Manager artykuł Linking virtual [networks to ExpressRoute circuits](expressroute-howto-linkvnet-arm.md) (Łączenie sieci wirtualnych z obwodami usługi ExpressRoute).

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Pobieranie stanu obwodu expressRoute
Te informacje można pobrać w dowolnym momencie za pomocą polecenia cmdlet **Get-AzExpressRouteCircuit.** Wywołanie bez parametrów wyświetla listę wszystkich obwodów.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```

Odpowiedź jest podobna do poniższego przykładu:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
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
                                        "ServiceProviderName": "Equinix",
                                        "PeeringLocation": "Silicon Valley",
                                        "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

Informacje dotyczące określonego obwodu expressRoute można uzyskać, przekazując nazwę grupy zasobów i nazwę obwodu jako parametr do wywołania:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Odpowiedź wygląda podobnie do następującego przykładu:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
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
                                        "ServiceProviderName": "Equinix",
                                        "PeeringLocation": "Silicon Valley",
                                        "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

Szczegółowe opisy wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modyfikowanie obwodu expressRoute
Niektóre właściwości obwodu usługi ExpressRoute można modyfikować bez wpływu na łączność.

Można wykonać następujące zadania bez przestoju:

* Włącz lub wyłącz dodatek ExpressRoute Premium dla obwodu usługi ExpressRoute. Zmiana wersji SKU z *Standardowa/Premium* na *Lokalna* nie jest obsługiwana.
* Zwiększ przepustowość obwodu usługi ExpressRoute, o ile na porcie jest dostępna pojemność. Nie jest obsługiwana downgrading przepustowości obwodu.
* Zmień plan pomiarów z Dane taryfowe na Nieograniczone dane. Zmiana planu pomiarów z Nieograniczone dane na Dane taryfowe nie jest obsługiwana.
* Można włączyć i wyłączyć opcję *Zezwalaj na operacje klasyczne.*

Aby uzyskać więcej informacji na temat limitów i ograniczeń, zobacz [ExpressRoute FAQ (ExpressRoute — często zadawane pytania).](expressroute-faqs.md)

### <a name="to-enable-the-expressroute-premium-add-on"></a>Aby włączyć dodatek ExpressRoute Premium
Możesz włączyć dodatek ExpressRoute Premium dla istniejącego obwodu, korzystając z następującego fragmentu kodu programu PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Obwód ma teraz włączone funkcje dodatku ExpressRoute Premium. Rozpoczynamy rozliczanie za możliwość dodatku Premium natychmiast po pomyślnym uruchomieniu polecenia.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Aby wyłączyć dodatek ExpressRoute Premium
> [!IMPORTANT]
> Jeśli używasz zasobów, które są większe niż dozwolone dla obwodu standardowego, ta operacja może się nie powieść.
>

Zanotuj następujące informacje:

* Przed obniżeniem wersji Premium na standardową należy upewnić się, że liczba sieci wirtualnych połączonych z obwodem jest mniejsza niż 10. Jeśli tego nie zimkniesz, żądanie aktualizacji zakończy się niepowodzeniem, a opłaty będą naliczane po stawkach Premium.
* Wszystkie sieci wirtualne w innych regionach geopolitycznych muszą być najpierw odłączone. Jeśli nie usuniesz tego linku, żądanie aktualizacji nie powiedzie się, a opłaty będą nadal naliczane po stawkach Premium.
* W przypadku prywatnej komunikacji równorzędnej tabela tras musi być mniejsza niż 4000 tras. Jeśli rozmiar tabeli tras jest większy niż 4000 tras, sesja protokołu BGP zostanie upuszczana. Sesja protokołu BGP nie zostanie ponownie włączona, dopóki liczba anonsowanych prefiksów nie będzie poniżej 4000.

Dodatek ExpressRoute Premium dla istniejącego obwodu można wyłączyć za pomocą następującego polecenia cmdlet programu PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Aby zaktualizować przepustowość obwodu usługi ExpressRoute
Aby uzyskać informacje o obsługiwanych opcjach przepustowości dla dostawcy, zobacz [ExpressRoute FAQ (ExpressRoute — często zadawane pytania).](expressroute-faqs.md) Możesz wybrać dowolny rozmiar większy niż rozmiar istniejącego obwodu.

> [!IMPORTANT]
> W przypadku niewystarczającej pojemności istniejącego portu może być trzeba ponownie utworzyć obwód usługi ExpressRoute. Nie można uaktualnić obwodu, jeśli w tej lokalizacji nie ma dostępnej dodatkowej pojemności.
>
> Nie można zmniejszyć przepustowości obwodu usługi ExpressRoute bez zakłóceń. Ograniczenie przepustowości wymaga coprowizowania obwodu expressRoute, a następnie ponownego aprowizowania nowego obwodu expressRoute.
>

Po podjęciu decyzji o potrzebnym rozmiarze użyj następującego polecenia, aby zmienić rozmiar obwodu:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Obwód zostanie uaktualniony po stronie firmy Microsoft. Następnie należy skontaktować się z dostawcą połączenia, aby zaktualizować konfiguracje po jego stronie w celu dopasowania do tej zmiany. Po otrzymaniu tego powiadomienia rozpoczniemy rozliczanie za zaktualizowaną opcję przepustowości.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Aby przenieść z mierzonej do nieograniczonej liczby sku
Możesz zmienić sku obwodu usługi ExpressRoute przy użyciu następującego fragmentu kodu programu PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Aby kontrolować dostęp do klasycznych i Resource Manager lokalnych
Zapoznaj się z instrukcjami [w te tematu Move ExpressRoute circuits from the classic to the Resource Manager deployment model (Przenoszenie](expressroute-howto-move-arm.md)obwodów usługi ExpressRoute z klasycznego do Resource Manager wdrożenia).

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Coprowizowanie obwodu expressRoute
Zanotuj następujące informacje:

* Wszystkie sieci wirtualne muszą być odłączone od obwodu usługi ExpressRoute. Jeśli ta operacja nie powiedzie się, sprawdź, czy jakiekolwiek sieci wirtualne są połączone z obwodem.
* Jeśli stan aprowizowania dostawcy usług  obwodowych  usługi ExpressRoute to Aprowizowanie lub Aprowizowanie, musisz współpracować z dostawcą usług, aby coprowizować obwód po jego stronie. Nadal rezerwuj zasoby i naliczamy rachunek, dopóki dostawca usług nie zakończy aprowizowania obwodu i powiadomi nas.
* Jeśli dostawca usług coprowizował obwód, co oznacza, że stan inicjowania obsługi dostawcy usług zostanie ustawiony na Wartość Nie **aprowizowana,** możesz usunąć obwód. Następnie opłaty za obwód zostaną zatrzymane.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Obwód expressRoute można usunąć, uruchamiając następujące polecenie:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obwodu i aprowizowania go u dostawcy przejdź do następnego kroku, aby skonfigurować komunikacji równorzędnej:

> [!div class="nextstepaction"]
> [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-arm.md)
