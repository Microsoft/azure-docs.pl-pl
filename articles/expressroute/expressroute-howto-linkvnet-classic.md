---
title: 'Azure ExpressRoute: łączenie sieci wirtualnej z obwodem: klasyczny'
description: Ten dokument zawiera omówienie sposobu łączenia sieci wirtualnych (sieci wirtualnych) z obwodami usługi ExpressRoute przy użyciu klasycznego modelu wdrażania i programu PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a731962f22985268093c547b09a8cd77c5b92660
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89395812"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Łączenie sieci wirtualnej z obwodem usługi ExpressRoute przy użyciu programu PowerShell (wersja klasyczna)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-linkvnet-cli.md)
> * [Wideo — Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-linkvnet-classic.md)
>

Ten artykuł ułatwi łączenie sieci wirtualnych (sieci wirtualnych) z obwodami usługi Azure ExpressRoute przy użyciu programu PowerShell. Jedna sieć wirtualna może być połączona do maksymalnie czterech obwodów usługi ExpressRoute. Wykonaj kroki opisane w tym artykule, aby utworzyć nowy link do każdego obwodu ExpressRoute, z którym nawiązujesz połączenie. Obwody usługi ExpressRoute mogą znajdować się w tej samej subskrypcji, różnych subskrypcjach lub kombinacji obu tych elementów. Ten artykuł dotyczy sieci wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania.

Do obwodu ExpressRoute można połączyć maksymalnie 10 sieci wirtualnych. Wszystkie sieci wirtualne muszą znajdować się w tym samym regionie geopolitycznym. Możesz połączyć większą liczbę sieci wirtualnych z obwodem ExpressRoute lub połączyć sieci wirtualne, które znajdują się w innych regionach geopolitycznych, jeśli włączysz dodatek ExpressRoute Premium. Zapoznaj się z [często zadawanymi pytaniami](expressroute-faqs.md) , aby uzyskać więcej informacji na temat dodatku Premium.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Modele wdrażania Azure — informacje**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji

* Przed rozpoczęciem konfiguracji zapoznaj się z warunkami [wstępnymi](expressroute-prerequisites.md), [wymaganiami dotyczącymi routingu](expressroute-routing.md)i [przepływami pracy](expressroute-workflows.md) .
* Musisz mieć aktywny obwód usługi ExpressRoute.
   * Postępuj zgodnie z instrukcjami w celu [utworzenia obwodu usługi ExpressRoute](expressroute-howto-circuit-classic.md) i włączenia tego obwodu przez dostawcę połączenia.
   * Upewnij się, że masz skonfigurowaną prywatną komunikację równorzędną Azure dla obwodu. Instrukcje dotyczące routingu można znaleźć w artykule [Konfigurowanie routingu](expressroute-howto-routing-classic.md) .
   * Upewnij się, że skonfigurowano prywatną komunikację równorzędną platformy Azure, a Komunikacja równorzędna protokołu BGP między siecią i firmą Microsoft jest w stanie umożliwiającym kompleksową łączność.
   * Musisz mieć sieć wirtualną i bramę sieci wirtualnej, która została utworzona i w pełni zainicjowana. Postępuj zgodnie z instrukcjami, aby [skonfigurować sieć wirtualną dla usługi ExpressRoute](expressroute-howto-vnet-portal-classic.md).

### <a name="download-the-latest-powershell-cmdlets"></a>Pobierz najnowsze polecenia cmdlet programu PowerShell

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Łączenie sieci wirtualnej w ramach tej samej subskrypcji z obwodem
Można połączyć sieć wirtualną z obwodem usługi ExpressRoute za pomocą następującego polecenia cmdlet. Upewnij się, że Brama sieci wirtualnej została utworzona i jest gotowa do łączenia przed uruchomieniem polecenia cmdlet.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Usuwanie linku sieci wirtualnej do obwodu
Można usunąć łącze sieci wirtualnej do obwodu usługi ExpressRoute przy użyciu następującego polecenia cmdlet. Upewnij się, że wybrano bieżącą subskrypcję dla danej sieci wirtualnej. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Połączenie sieci wirtualnej w innej subskrypcji z obwodem
Obwód ExpressRoute można udostępnić w wielu subskrypcjach. Na poniższej ilustracji przedstawiono prosty schemat działania udostępniania dla obwodów usługi ExpressRoute w wielu subskrypcjach.

Każda z mniejszych chmur w ramach dużej chmury służy do reprezentowania subskrypcji należących do różnych działów w organizacji. Każdy dział w organizacji może używać własnej subskrypcji do wdrażania swoich usług, ale działy mogą współdzielić jeden obwód usługi ExpressRoute, aby połączyć się z siecią lokalną. Pojedynczy dział (w tym przykładzie:) może być własnym obwodem ExpressRoute. Inne subskrypcje w organizacji mogą korzystać z obwodu ExpressRoute.

> [!NOTE]
> Naliczanie opłat za połączenia i przepustowość dla dedykowanego obwodu zostanie zastosowane do właściciela obwodu usługi ExpressRoute. Wszystkie sieci wirtualne mają tę samą przepustowość.
> 
> 

![Łączność między subskrypcjami](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administracja
*Właściciel obwodu* jest administratorem/współadministratorem subskrypcji, w której jest tworzony obwód usługi ExpressRoute. Właściciel obwodu może autoryzować administratorów/współadministratorów innych subskrypcji, nazywanych *użytkownikami obwodowymi*, do korzystania z dedykowanego obwodu, który posiada. Użytkownicy obwodów, którzy mają uprawnienia do korzystania z obwodu usługi ExpressRoute w organizacji, mogą połączyć sieć wirtualną w swojej subskrypcji z obwodem usługi ExpressRoute po ich autoryzacji.

Właściciel obwodu ma uprawnienia do modyfikowania i odwoływania autoryzacji w dowolnym momencie. Odwoływanie autoryzacji spowoduje usunięcie wszystkich linków z subskrypcji, której dostęp został odwołany.

### <a name="circuit-owner-operations"></a>Operacje właściciela obwodu

**Tworzenie autoryzacji**

Właściciel obwodu autoryzuje administratorów innych subskrypcji do używania określonego obwodu. W poniższym przykładzie administrator obwodu (contoso IT) umożliwia administratorowi innej subskrypcji (Tworzenie i testowanie) łączenie się z dwoma sieciami wirtualnymi do obwodu. W tym celu administrator IT firmy Contoso umożliwia określenie identyfikatora Microsoft Dev-Test. Polecenie cmdlet nie wysyła wiadomości e-mail do określonego identyfikatora firmy Microsoft. Właściciel obwodu musi jawnie powiadomić innego właściciela subskrypcji o ukończeniu autoryzacji.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  Zwracają:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Przeglądanie autoryzacji**

Właściciel obwodu może przejrzeć wszystkie autoryzacje, które są wydawane w określonym obwodzie, uruchamiając następujące polecenie cmdlet:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  Zwracają:

  ```powershell
  Description         : EngineeringTeam
  Limit               : 3
  LinkAuthorizationId : ####################################
  MicrosoftIds        : engadmin@contoso.com
  Used                : 1

  Description         : MarketingTeam
  Limit               : 1
  LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
  MicrosoftIds        : marketingadmin@contoso.com
  Used                : 0

  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : salesadmin@contoso.com
  Used                : 2
  ```

**Aktualizowanie autoryzacji**

Właściciel obwodu może modyfikować autoryzacje przy użyciu następującego polecenia cmdlet:

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  Zwracają:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Usuwanie autoryzacji**

Właściciel obwodu może odwoływać/usunąć autoryzacje dla użytkownika, uruchamiając następujące polecenie cmdlet:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Operacje użytkownika obwodowego

**Przeglądanie autoryzacji**

Użytkownik obwodu może przeglądać autoryzacje przy użyciu następującego polecenia cmdlet:

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  Zwracają:

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : ContosoIT
  Location                         : Washington DC
  MaximumAllowedLinks              : 2
  ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  UsedLinks                        : 0
  ```

**Realizowanie autoryzacji łączy**

Użytkownik obwodu może uruchomić następujące polecenie cmdlet, aby zrealizować autoryzację linku:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Zwracają:

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

Uruchom to polecenie w nowo połączonej subskrypcji dla sieci wirtualnej:

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat ExpressRoute, zobacz [często zadawane pytania](expressroute-faqs.md)dotyczące usługi ExpressRoute.
