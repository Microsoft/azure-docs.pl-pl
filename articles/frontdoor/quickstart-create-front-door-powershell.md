---
title: 'Szybki start: konfigurowanie wysokiej dostępności za pomocą Azure Front Door — Azure PowerShell'
description: Ten przewodnik Szybki start pokazuje, jak za pomocą usługi Azure Front Door utworzyć globalną aplikację internetową o wysokiej dostępności i wysokiej wydajności przy użyciu Azure PowerShell.
services: front-door
documentationcenter: na
author: duongau
ms.author: duau
manager: KumudD
ms.date: 09/21/2020
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-api
ms.openlocfilehash: cd439a5931340f56401e5f6ba7a4e09f35ab7c7d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107539059"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-powershell"></a>Szybki start: tworzenie aplikacji Front Door dla globalnej aplikacji internetowej o wysokiej Azure PowerShell

Rozpoczynanie pracy z Azure Front Door przy użyciu Azure PowerShell tworzenia globalnej aplikacji internetowej o wysokiej Azure PowerShell o wysokiej wydajności.

Interfejs Front Door kieruje ruch internetowy do określonych zasobów w puli zaplecza. Zdefiniowano domenę frontonia, dodasz zasoby do puli zaplecza i utworzysz regułę rozsyłania. W tym artykule użyto prostej konfiguracji jednej puli zaplecza z dwoma zasobami aplikacji internetowej i jednej reguły rozsyłania przy użyciu domyślnego dopasowania ścieżki "/*".

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell zainstalowane lokalnie lub Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Możesz użyć istniejącej grupy zasobów lub utworzyć nową.

Utwórz grupę zasobów za pomocą [new-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupFD -Location centralus
```

## <a name="create-two-instances-of-a-web-app"></a>Tworzenie dwóch wystąpień aplikacji internetowej

Ten przewodnik Szybki start wymaga dwóch wystąpień aplikacji internetowej, które działają w różnych regionach świadczenia usługi Azure. Oba wystąpienia aplikacji internetowej działają w trybie aktywny/aktywny, więc jedno z nich może przyjmować ruch. Ta konfiguracja różni się od konfiguracji aktywnej/autonomicznej, w której działa jako tryb failover.

Jeśli nie masz jeszcze aplikacji internetowej, użyj poniższego skryptu, aby skonfigurować dwie przykładowe aplikacje internetowe.

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

W tej sekcji szczegółowo opisano sposób tworzenia i konfigurowania następujących składników Front Door:
    
* Obiekt frontendu zawiera Front Door domenę domyślną.
* Pula zaplecza to zestaw równoważnych za zaplecza, do których Front Door równoważenie obciążenia żądania klienta.
* Reguła rozsyłania mapuje hosta frontonia i pasujący wzorzec ścieżki URL na określoną pulę zaplecza.

### <a name="create-a-frontend-object"></a>Tworzenie obiektu frontonia

Obiekt frontendu konfiguruje nazwę hosta dla Front Door. Domyślnie nazwa hosta będzie mieć sufiks **.azurefd.net*.

```azurepowershell-interactive
# Create a unique name
$fdname = "contoso-frontend-$(Get-Random)"

#Create the frontend object
$FrontendEndObject = New-AzFrontDoorFrontendEndpointObject `
-Name "frontendEndpoint1" `
-HostName $fdname".azurefd.net"
```

### <a name="create-the-backend-pool"></a>Tworzenie puli zaplecza

Pula zaplecza składa się z dwóch aplikacji internetowych utworzonych na początku tego przewodnika Szybki start. Ustawienia sondy kondycji i równoważenia obciążenia zdefiniowane w tym kroku wykorzystują wartości domyślne.

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

Reguła rozsyłania mapuje pulę zaplecza na domenę frontonia i ustawia domyślną wartość dopasowania ścieżki na "/*".

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
### <a name="create-the-front-door"></a>Tworzenie Front Door

Teraz, po utworzeniu niezbędnych obiektów, utwórz Front Door:

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

Po pomyślnym wdrożeniu możesz je przetestować, korzystając z kroków w następnej sekcji.

## <a name="test-the-front-door"></a>Testowanie Front Door

Uruchom następujące polecenia, aby uzyskać nazwę hosta dla Front Door.

```azurepowershell-interactive
# Gets Front Door in resource group and output the hostname of the frontend domain.
$fd = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD
$fd.FrontendEndpoints[0].Hostname
```

Otwórz przeglądarkę internetową i wprowadź nazwę hosta uzyskaną z poleceń . Żądanie Front Door kierowanie żądania do jednego z zasobów zaplecza. 

:::image type="content" source="./media/quickstart-create-front-door-powershell/front-door-test-page.png" alt-text="Front Door testowa":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą Front Door, usuń grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie grupy Front Door i wszystkich powiązanych z nią zasobów. 

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupFD
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono:
* Front Door
* Dwie aplikacje internetowe

Aby dowiedzieć się, jak dodać domenę niestandardową do Front Door, przejdź do Front Door samouczków.

> [!div class="nextstepaction"]
> [Dodawanie domeny niestandardowej](front-door-custom-domain.md)
