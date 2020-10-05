---
title: 'Szybki Start: Konfigurowanie wysokiej dostępności przy użyciu usług Azure Front-Azure PowerShell'
description: W tym przewodniku szybki start pokazano, jak utworzyć aplikację sieci Web o wysokiej dostępności i wysokiej wydajności przy użyciu programu Azure PowerShell.
services: front-door
documentationcenter: na
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: 8ccc50dfc4c53f1ee207a04d7d7775775f13cb65
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91348964"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-powershell"></a>Szybki Start: Tworzenie czołowych drzwi dla globalnej aplikacji sieci Web o wysokiej dostępności przy użyciu Azure PowerShell

Rozpocznij pracę z usługami Azure Front platform przy użyciu Azure PowerShell, aby utworzyć globalną aplikację sieci Web o wysokiej dostępności i wysokiej wydajności.

Drzwi tylne kierują ruch internetowy do określonych zasobów w puli zaplecza. Zdefiniowano domenę frontonu, dodanie zasobów do puli zaplecza i utworzenie reguły routingu. W tym artykule jest używana prosta konfiguracja jednej puli zaplecza z dwoma zasobami aplikacji sieci Web i pojedynczą regułą routingu korzystającą ze ścieżki domyślnej "/*".

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell zainstalowane lokalnie lub Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Możesz użyć istniejącej grupy zasobów lub utworzyć nową.

Utwórz grupę zasobów przy użyciu elementu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupFD -Location centralus
```

## <a name="create-two-instances-of-a-web-app"></a>Tworzenie dwóch wystąpień aplikacji sieci Web

Ten przewodnik Szybki Start wymaga dwóch wystąpień aplikacji sieci Web, które działają w różnych regionach świadczenia usługi Azure. Oba wystąpienia aplikacji sieci Web działają w trybie aktywny/aktywny, więc jeden może przyjmować ruch. Ta konfiguracja różni się od konfiguracji aktywnej/gotowości, która działa jako tryb failover.

Jeśli nie masz jeszcze aplikacji sieci Web, użyj poniższego skryptu, aby skonfigurować dwa przykładowe aplikacje sieci Web.

```azurepowershell-interactive
# Create first web app in Central US region.
$webapp1 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location centralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanCentralUS

# Create second web app in South Central US region.
$webapp2 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location southcentralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanSouthCentralUS
```

## <a name="create-a-front-door"></a>Tworzenie usługi Front Door

W tej sekcji szczegółowo opisano, jak utworzyć i skonfigurować następujące składniki drzwi przednich:
    
* Obiekt frontonu zawiera domyślną domenę drzwi z przodu.
* Pula zaplecza jest zestawem równorzędnych punktów końcowych, do których obciążenie przede wszystkim równoważy żądanie klienta.
* Reguła routingu mapuje hosta frontonu i zgodny ze wzorcem ścieżka URL do określonej puli zaplecza.

### <a name="create-a-frontend-object"></a>Tworzenie obiektu frontonu

Obiekt frontonu konfiguruje nazwę hosta dla drzwi z przodu. Domyślnie nazwa hosta będzie miała sufiks **. azurefd.NET*.

```azurepowershell-interactive
# Create a unique name
$fdname = "contoso-frontend-$(Get-Random)"

#Create the frontend object
$FrontendEndObject = New-AzFrontDoorFrontendEndpointObject `
-Name "frontendEndpoint1" `
-HostName $fdname".azurefd.net"
```

### <a name="create-the-backend-pool"></a>Tworzenie puli zaplecza

Pula zaplecza składa się z dwóch aplikacji sieci Web utworzonych na początku tego przewodnika Szybki Start. Ustawienia sondy kondycji i równoważenia obciążenia zdefiniowane w tym kroku używają wartości domyślnych.

```azurepowershell-interactive
# Create backend objects that points to the hostname of the web apps
$backendObject1 = New-AzFrontDoorBackendObject `
-Address $webapp1.DefaultHostName
$backendObject2 = New-AzFrontDoorBackendObject `
-Address $webapp2.DefaultHostName

# Create a health probe object
$HealthProbeObject = New-AzFrontDoorHealthProbeSettingObject `
-Name "HealthProbeSetting"

# Create the load balancing setting object
$LoadBalancingSettingObject = New-AzFrontDoorLoadBalancingSettingObject `
-Name "Loadbalancingsetting" `
-SampleSize "4" `
-SuccessfulSamplesRequired "2" `
-AdditionalLatencyInMilliseconds "0"

# Create a backend pool using the backend objects, health probe, and load balancing settings
$BackendPoolObject = New-AzFrontDoorBackendPoolObject `
-Name "myBackendPool" `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-Backend $backendObject1,$backendObject2 `
-HealthProbeSettingsName "HealthProbeSetting" `
-LoadBalancingSettingsName "Loadbalancingsetting"
```

### <a name="create-a-routing-rule"></a>Tworzenie reguły rozsyłania

Reguła routingu mapuje pulę zaplecza na domenę frontonu i ustawia domyślną wartość dopasowania ścieżki na "/*".

```azurepowershell-interactive
# Create a routing rule mapping the frontend host to the backend pool
$RoutingRuleObject = New-AzFrontDoorRoutingRuleObject `
-Name LocationRule `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-FrontendEndpointName "frontendEndpoint1" `
-BackendPoolName "myBackendPool" `
-PatternToMatch "/*"
```
### <a name="create-the-front-door"></a>Utwórz drzwi tylne

Po utworzeniu niezbędnych obiektów Utwórz drzwi z przodu:

```azurepowershell-interactive
# Creates the Front Door
New-AzFrontDoor `
-Name $fdname `
-ResourceGroupName myResourceGroupFD `
-RoutingRule $RoutingRuleObject `
-BackendPool $BackendPoolObject `
-FrontendEndpoint $FrontendEndObject `
-LoadBalancingSetting $LoadBalancingSettingObject `
-HealthProbeSetting $HealthProbeObject
```

Po pomyślnym wdrożeniu możesz go przetestować, wykonując kroki opisane w następnej sekcji.

## <a name="test-the-front-door"></a>Testowanie drzwi przednich

Uruchom następujące polecenia, aby uzyskać nazwę hosta dla drzwi z przodu.

```azurepowershell-interactive
# Gets Front Door in resource group and output the hostname of the frontend domain.
$fd = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD
$fd.FrontendEndpoints[0].Hostname
```

Otwórz przeglądarkę internetową i wprowadź nazwę hosta uzyskaną z poleceń. Drzwi tylne przekierują Twoje żądanie do jednego z zasobów zaplecza. 

:::image type="content" source="./media/quickstart-create-front-door-powershell/front-door-test-page.png" alt-text="Strona testowa z przednim Drzwiem":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już zasobów utworzonych przy użyciu drzwi przednich, Usuń grupę zasobów. Po usunięciu grupy zasobów można także usunąć drzwi z przodu i wszystkie powiązane z nią zasoby. 

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupFD
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono:
* Front Door
* Dwie aplikacje sieci Web

Aby dowiedzieć się, jak dodać domenę niestandardową do swoich drzwi, przejdź do samouczków dotyczących drzwi.

> [!div class="nextstepaction"]
> [Dodawanie domeny niestandardowej](front-door-custom-domain.md)
