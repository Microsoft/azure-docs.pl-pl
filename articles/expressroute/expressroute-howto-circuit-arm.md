---
title: 'Szybki Start: Tworzenie i modyfikowanie obwodu za pomocą ExpressRoute Azure PowerShell'
description: Tworzenie, Inicjowanie obsługi, weryfikowanie, aktualizowanie, usuwanie i cofanie aprowizacji obwodu ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: b5ac53c44429e23e2d22a934a9dc71bd485ec4cd
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761910"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-powershell"></a>Szybki Start: Tworzenie i modyfikowanie obwodu usługi ExpressRoute przy użyciu Azure PowerShell

Ten przewodnik Szybki Start przedstawia sposób tworzenia obwodu usługi ExpressRoute przy użyciu poleceń cmdlet programu PowerShell i modelu wdrażania Azure Resource Manager. Możesz również sprawdzić stan, zaktualizować, usunąć lub anulować obsługę administracyjną obwodu.

## <a name="prerequisites"></a>Wymagania wstępne

* Przed rozpoczęciem konfiguracji Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) .
* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure PowerShell zainstalowane lokalnie lub Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Tworzenie i Inicjowanie obsługi obwodu ExpressRoute
### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wybierz swoją subskrypcję

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>Pobierz listę obsługiwanych dostawców, lokalizacji i przepustowości
Przed utworzeniem obwodu usługi ExpressRoute należy uzyskać listę obsługiwanych dostawców połączeń, lokalizacji i opcji przepustowości.

Polecenie cmdlet **Get-AzExpressRouteServiceProvider** programu PowerShell zwraca te informacje, które będą używane w kolejnych krokach:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Sprawdź, czy na liście znajduje się Twój dostawca połączenia. Zanotuj poniższe informacje, które są potrzebne później podczas tworzenia obwodu:

* Nazwa
* PeeringLocations
* BandwidthsOffered

Teraz można przystąpić do tworzenia obwodu ExpressRoute.

### <a name="create-an-expressroute-circuit"></a>Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)
Jeśli nie masz jeszcze grupy zasobów, musisz ją utworzyć przed utworzeniem obwodu ExpressRoute. Możesz to zrobić, uruchamiając następujące polecenie:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

Poniższy przykład pokazuje, jak utworzyć obwód ExpressRoute 200 MB/s za pomocą Equinix w Dolina krzemu. Jeśli używasz innego dostawcy i innych ustawień, Zastąp te informacje podczas żądania. Użyj poniższego przykładu, aby zażądać nowego klucza usługi:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Upewnij się, że określono poprawną warstwę SKU i rodzinę SKU:

* Warstwa SKU określa, czy obwód ExpressRoute jest [lokalny](expressroute-faqs.md#expressroute-local), standardowy, czy [Premium](expressroute-faqs.md#expressroute-premium). Możesz określić *Local*, * Standard lub *Premium*. Nie można zmienić jednostki SKU z warstwy *standardowa/Premium* na *lokalną*.
* Rodzina SKU określa typ rozliczeń. Możesz określić *MeteredData* dla mierzonego planu taryfowego i *UnlimitedData* dla nieograniczonego planu taryfowego. Typ rozliczeń można zmienić z *MeteredData* na *UnlimitedData*, ale nie można zmienić typu z *UnlimitedData* na *MeteredData*. Obwód *lokalny* jest zawsze *UnlimitedData*.

> [!IMPORTANT]
> Obwód usługi ExpressRoute jest rozliczany od momentu, gdy zostanie wystawiony klucz usług. Upewnij się, że ta operacja jest wykonywana, gdy dostawca łączności jest gotowy do aprowizacji obwodu.
>

Odpowiedź zawiera klucz usługi. Aby uzyskać szczegółowe opisy wszystkich parametrów, należy uruchomić następujące polecenie:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="list-all-expressroute-circuits"></a>Wyświetl wszystkie obwody usługi ExpressRoute
Aby uzyskać listę wszystkich utworzonych obwodów usługi ExpressRoute, uruchom polecenie **Get-AzExpressRouteCircuit** :

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Odpowiedź wygląda podobnie do poniższego przykładu:

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

Te informacje można pobrać w dowolnym momencie przy użyciu `Get-AzExpressRouteCircuit` polecenia cmdlet. Wywołanie bez parametrów wyświetla wszystkie obwody. Twój klucz usługi jest wymieniony w polu *ServiceKey* :

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```

Odpowiedź wygląda podobnie do poniższego przykładu:

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

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Wyślij klucz usługi do dostawcy połączenia w celu aprowizacji
*ServiceProviderProvisioningState* zawiera informacje o bieżącym stanie aprowizacji po stronie Dostawca usług. Stan umożliwia wyświetlenie stanu po stronie firmy Microsoft. Aby uzyskać więcej informacji o Stanach aprowizacji obwodów, zobacz [przepływy pracy](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Podczas tworzenia nowego obwodu ExpressRoute obwód jest w następującym stanie:

```azurepowershell
ServiceProviderProvisioningState : NotProvisioned
CircuitProvisioningState         : Enabled
```

Obwód zostanie zmieniony na następujący stan, gdy dostawca łączności aktualnie go włączy:

```azurepowershell
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

Aby użyć obwodu ExpressRoute, musi on być w następującym stanie:

```azurepowershell
ServiceProviderProvisioningState : Provisioned
CircuitProvisioningState         : Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Okresowe sprawdzanie stanu i stanu klucza obwodu
Sprawdzenie stanu i stanu klucza usługi poinformuje Cię, gdy dostawca zaudostępniał obwód. Po skonfigurowaniu obwodu *ServiceProviderProvisioningState* pojawia się zgodnie z *zainicjowaną obsługą*, jak pokazano w następującym przykładzie:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Odpowiedź wygląda podobnie do poniższego przykładu:

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

### <a name="create-your-routing-configuration"></a>Utwórz konfigurację routingu
Aby uzyskać instrukcje krok po kroku, zobacz artykuł [Konfiguracja routingu obwodów usługi ExpressRoute](expressroute-howto-routing-arm.md) w celu utworzenia i zmodyfikowania komunikacji równorzędnej obwodu.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwodów, które są tworzone za pomocą dostawców usług oferujących usługi łączności warstwy 2. Jeśli używasz dostawcy usług oferującego zarządzane usługi warstwy 3 (zazwyczaj sieć VPN sieci IP, na przykład MPLS), dostawca łączności konfiguruje i zarządza routingiem.
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Łączenie sieci wirtualnej z obwodem usługi ExpressRoute
Następnie połącz sieć wirtualną z obwodem ExpressRoute. Podczas pracy z modelem wdrażania Menedżer zasobów należy użyć artykułu [łączenie sieci wirtualnych do obwodów usługi ExpressRoute](expressroute-howto-linkvnet-arm.md) .

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Pobieranie stanu obwodu ExpressRoute
Te informacje można pobrać w dowolnym momencie za pomocą polecenia cmdlet **Get-AzExpressRouteCircuit** . Wywołanie bez parametrów wyświetla wszystkie obwody.

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

Możesz uzyskać informacje dotyczące określonego obwodu ExpressRoute, przekazując nazwę grupy zasobów i nazwę obwodu jako parametr do wywołania:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Odpowiedź wygląda podobnie do poniższego przykładu:

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

Aby uzyskać szczegółowe opisy wszystkich parametrów, należy uruchomić następujące polecenie:

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modyfikowanie obwodu ExpressRoute
Niektóre właściwości obwodu usługi ExpressRoute można modyfikować bez wpływu na łączność.

W przypadku braku przestojów można wykonać następujące zadania:

* Włącz lub Wyłącz dodatek ExpressRoute Premium dla obwodu usługi ExpressRoute. Zmiana jednostki SKU z warstwy *standardowa/Premium* na *lokalną* nie jest obsługiwana.
* Zwiększ przepustowość obwodu ExpressRoute pod warunkiem, że na porcie jest dostępna pojemność. Obniżenie przepustowości obwodu nie jest obsługiwane.
* Zmień plan pomiaru z danych mierzonych na dane nieograniczone. Zmiana planu pomiaru z nieograniczonej ilości danych na dane taryfowe nie jest obsługiwana.
* Można włączać i wyłączać *Zezwalanie na klasyczne operacje*.

Aby uzyskać więcej informacji o limitach i ograniczeniach, zobacz [często zadawane pytania](expressroute-faqs.md)dotyczące usługi ExpressRoute.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Aby włączyć dodatek ExpressRoute Premium
Dodatek ExpressRoute Premium dla istniejącego obwodu można włączyć za pomocą następującego fragmentu kodu programu PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Obwód ma teraz włączone funkcje dodatku ExpressRoute Premium. Zaczynamy naliczać opłaty za dodatek Premium, gdy tylko polecenie zostało pomyślnie uruchomione.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Aby wyłączyć dodatek ExpressRoute Premium
> [!IMPORTANT]
> Jeśli używasz zasobów, które są większe niż dozwolone dla obwodu standardowego, ta operacja może zakończyć się niepowodzeniem.
>

Zanotuj następujące informacje:

* Przed obniżeniem wersji Premium do standard należy upewnić się, że liczba sieci wirtualnych, które są połączone z obwodem, jest mniejsza niż 10. Jeśli tego nie zrobisz, żądanie aktualizacji zakończy się niepowodzeniem, a opłaty są naliczane według stawek za usługę Premium.
* Należy najpierw odłączyć wszystkie sieci wirtualne w innych regionach geopolitycznych. Jeśli nie usuniesz linku, żądanie aktualizacji zakończy się niepowodzeniem i będziemy nadal korzystać z stawek za usługę Premium.
* Tabela tras musi być krótsza niż 4 000 tras dla prywatnej komunikacji równorzędnej. Jeśli rozmiar tabeli tras przekracza 4 000 tras, sesja protokołu BGP zostanie porzucana. Sesja BGP nie zostanie włączona jeszcze raz, dopóki liczba anonsowanych prefiksów nie będzie objęta 4 000.

Dodatek ExpressRoute Premium dla istniejącego obwodu można wyłączyć za pomocą następującego polecenia cmdlet programu PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Aby zaktualizować przepustowość obwodu ExpressRoute
W przypadku opcji obsługiwanych przepustowości dla dostawcy zapoznaj się z tematem [często zadawanych pytań](expressroute-faqs.md)dotyczących usługi ExpressRoute. Można wybrać rozmiar większy niż rozmiar istniejącego obwodu.

> [!IMPORTANT]
> Może być konieczne ponowne utworzenie obwodu ExpressRoute, jeśli nie ma odpowiedniej pojemności na istniejącym porcie. Nie można uaktualnić obwodu, jeśli w tej lokalizacji nie ma dodatkowej pojemności.
>
> Nie można zmniejszyć przepustowości obwodu ExpressRoute bez zakłóceń. Obniżenie przepustowości wymaga anulowania aprowizacji obwodu ExpressRoute, a następnie ponownego aprowizacji nowego obwodu ExpressRoute.
>

Po podjęciu decyzji o żądanym rozmiarze Użyj następującego polecenia, aby zmienić rozmiar obwodu:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Obwód zostanie uaktualniony po stronie firmy Microsoft. Następnie należy skontaktować się z dostawcą połączenia w celu zaktualizowania konfiguracji po stronie, aby dopasować tę zmianę. Po wprowadzeniu tego powiadomienia zaczniemy rozliczać opłaty za zaktualizowaną opcję przepustowości.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Aby przenieść jednostkę SKU z pomiaru do nieograniczonego
Można zmienić jednostkę SKU obwodu usługi ExpressRoute przy użyciu następującego fragmentu kodu programu PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Aby kontrolować dostęp do środowisk klasycznych i Menedżer zasobów
Zapoznaj się z instrukcjami w temacie [Move ExpressRoute Circuits z klasycznego modelu wdrażania Menedżer zasobów](expressroute-howto-move-arm.md).

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Anulowanie aprowizacji obwodu ExpressRoute
Zanotuj następujące informacje:

* Wszystkie sieci wirtualne muszą być odłączone od obwodu usługi ExpressRoute. Jeśli ta operacja zakończy się niepowodzeniem, sprawdź, czy sieci wirtualne są połączone z obwodem.
* Jeśli stan aprowizacji dostawcy usługi obwodu ExpressRoute jest inicjowany lub **Zainicjowano obsługę administracyjną** , należy skontaktować się z dostawcą usług w celu **anulowania aprowizacji** obwodu po stronie. Nadal rezerwujemy zasoby i obciążamy Cię, dopóki dostawca usług nie ukończy anulowania aprowizacji obwodu i powiadamia nas.
* Jeśli dostawca usług anulował obsługę administracyjną tego obwodu, oznacza to, że stan aprowizacji dostawcy usług jest ustawiony na **nieinicjowany**, można usunąć obwód. Naliczanie opłat za obwód zostanie zatrzymane.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Czyszczenie zasobów

Obwód ExpressRoute można usunąć, uruchamiając następujące polecenie:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obwodu i zainicjowaniu obsługi administracyjnej z dostawcą przejdź do następnego kroku, aby skonfigurować komunikację równorzędną:

> [!div class="nextstepaction"]
> [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-arm.md)
