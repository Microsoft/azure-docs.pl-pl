---
title: 'Szybki start: tworzenie profilu wysokiej dostępności aplikacji — Azure PowerShell — Azure Traffic Manager'
description: W tym artykule Szybki start opisano sposób tworzenia profilu Traffic Manager w celu utworzenia aplikacji internetowej o wysokiej dostępie.
services: traffic-manager
author: duongau
ms.author: duau
manager: twooley
ms.date: 10/01/2020
ms.topic: quickstart
ms.service: traffic-manager
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-api
ms.openlocfilehash: 0fd2ae59f62850da75eecd5423ad225e208dca80
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537651"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Szybki start: tworzenie profilu Traffic Manager aplikacji internetowej o wysokiej Azure PowerShell

W tym przewodniku Szybki start opisano tworzenie profilu usługi Traffic Manager, który zapewni wysoką dostępność aplikacji internetowej.

W tym przewodniku Szybki start utworzysz dwa wystąpienia aplikacji internetowej. Każde z nich jest uruchamiane w innym regionie świadczenia usługi Azure. Utworzysz profil usługi Traffic Manager na podstawie [priorytetu punktu końcowego](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profil kieruje ruch użytkowników do lokacji głównej, w której działa aplikacja internetowa. Usługa Traffic Manager stale monitoruje aplikację internetową. Jeśli lokacja główna jest niedostępna, usługa zapewnia automatyczne przejście w tryb failover w lokacji zapasowej.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, utwórz teraz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten artykuł wymaga modułu Azure PowerShell w wersji 5.4.1 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów przy użyciu [narzędzia New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup)

```azurepowershell-interactive

# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Utwórz profil Traffic Manager przy użyciu [narzędzia New-AzTrafficManagerProfile,](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) który kieruje ruchem użytkowników na podstawie priorytetu punktu końcowego.

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>Tworzenie Web Apps

W tym przewodniku Szybki start będą potrzebne dwa wystąpienia aplikacji internetowej wdrożone w dwóch różnych regionach świadczenia usługi Azure *(Zachodnie stany USA* *i Wschodnie stany USA).* Każda będzie służyć jako podstawowy punkt końcowy i punkt końcowy trybu failover dla usługi Traffic Manager.

### <a name="create-web-app-service-plans"></a>Tworzenie planów App Service Sieci Web
Utwórz plany usługi Aplikacji internetowej przy użyciu narzędzia [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) dla dwóch wystąpień aplikacji internetowej, które będą wdrażane w dwóch różnych regionach świadczenia usługi Azure.

```azurepowershell-interactive

# Variables
$App1Name="AppServiceTM1$Random"
$App2Name="AppServiceTM2$Random"
$Location1="WestUS"
$Location2="EastUS"

# Create an App service plan
New-AzAppservicePlan -Name "$App1Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "$App2Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>Tworzenie aplikacji internetowej w planie App Service aplikacji
Utwórz dwa wystąpienia aplikacji internetowej przy użyciu [narzędzia New-AzWebApp](/powershell/module/az.websites/new-azwebapp) w planach usługi App Service w regionach świadczenia usługi Azure *Zachodnie* stany USA i *Wschodnie stany* USA.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager
Dodaj te dwa Web Apps jako Traffic Manager końcowe przy użyciu [new-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) do profilu Traffic Manager w następujący sposób:
- Dodaj aplikację internetową znajdującą się w regionie platformy Azure *Zachodnie* stany USA jako podstawowy punkt końcowy do rozsyłania całego ruchu użytkowników. 
- Dodaj aplikację internetową znajdującą się w regionie świadczenia usługi Azure *Wschodnie* usa jako punkt końcowy trybu failover. Gdy podstawowy punkt końcowy będzie niedostępny, ruch będzie automatycznie kierowany do punktu końcowego trybu failover.

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "$App1Name-$Location1" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "$App2Name-$Location2" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>Testowanie profilu usługi Traffic Manager

W tej sekcji sprawdzisz nazwę domeny profilu usługi Traffic Manager. Skonfigurujesz również podstawowy punkt końcowy tak, aby był niedostępny. Na końcu zobaczysz, że aplikacja internetowa jest nadal dostępna. Dzieje się tak, ponieważ usługa Traffic Manager wysyła ruch do punktu końcowego trybu failover.

### <a name="determine-the-dns-name"></a>Ustalanie nazwy DNS

Określ nazwę DNS profilu Traffic Manager pomocą [get-AzTrafficManagerProfile.](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile)

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Skopiuj **wartość RelativeDnsName.** Nazwa DNS profilu Traffic Manager to *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Wyświetlanie informacji o działaniu usługi Traffic Manager
1. W przeglądarce internetowej wprowadź nazwę DNS profilu usługi Traffic Manager *(http://<* relativednsname *>.trafficmanager.net),* aby wyświetlić domyślną witrynę internetową aplikacji internetowej.

    > [!NOTE]
    > W tym scenariuszu Szybki start wszystkie żądania są kierowane do podstawowego punktu końcowego. Jest on ustawiony na wartość **Priorytet 1**.
2. Aby wyświetlić Traffic Manager trybu failover, wyłącz lokację główną przy użyciu [opcji Disable-AzTrafficManagerEndpoint.](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint)

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Skopiuj nazwę DNS profilu Traffic Manager *(http://<* relativednsname *>.trafficmanager.net),* aby wyświetlić witrynę internetową w nowej sesji przeglądarki internetowej.
4. Sprawdź, czy aplikacja internetowa jest nadal dostępna.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy wszystko będzie gotowe, usuń grupy zasobów, aplikacje internetowe i wszystkie powiązane zasoby przy użyciu [narzędzia Remove-AzResourceGroup.](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono profil Traffic Manager, który zapewnia wysoką dostępność aplikacji internetowej. Aby dowiedzieć się więcej o kierowaniu ruchu, przejdź do samouczków usługi Traffic Manager.

> [!div class="nextstepaction"]
> [Samouczki usługi Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
