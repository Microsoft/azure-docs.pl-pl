---
title: 'Azure ExpressRoute: Przenieś obwody klasyczne do Menedżer zasobów'
description: Na tej stronie opisano, jak przenieść obwód klasyczny do Menedżer zasobów modelu wdrażania przy użyciu programu PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 460ea446fc6dfc43e81a1a57bbba032a61f3a72d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90532549"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>Przenoszenie obwodów usługi ExpressRoute z klasycznego do Menedżer zasobów modelu wdrażania przy użyciu programu PowerShell

Aby użyć obwodu usługi ExpressRoute dla modeli wdrażania klasycznego i Menedżer zasobów, należy przenieść obwód do Menedżer zasobów model wdrażania. Poniższe sekcje ułatwiają przenoszenie obwodu za pomocą programu PowerShell.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* Sprawdź, czy na komputerze zainstalowano lokalnie i AZ Azure PowerShell module. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/).
* Przed rozpoczęciem konfiguracji należy sprawdzić wymagania [wstępne](expressroute-prerequisites.md), [wymagania dotyczące routingu](expressroute-routing.md)i [przepływy pracy](expressroute-workflows.md) .
* Przejrzyj informacje podane w obszarze [przeniesienie obwodu usługi ExpressRoute z klasycznej do Menedżer zasobów](expressroute-move.md). Upewnij się, że w pełni rozumiesz limity i ograniczenia.
* Sprawdź, czy obwód jest w pełni funkcjonalny w klasycznym modelu wdrażania.
* Upewnij się, że masz grupę zasobów, która została utworzona w modelu wdrażania Menedżer zasobów.

## <a name="move-an-expressroute-circuit"></a>Przenoszenie obwodu ExpressRoute

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Krok 1. zbieranie szczegółów obwodu z klasycznego modelu wdrażania

Zaloguj się do klasycznego środowiska Azure i zbierz klucz usługi.

1. Zaloguj się do swojego konta platformy Azure.

   ```powershell
   Add-AzureAccount
   ```

2. Wybierz odpowiednią subskrypcję platformy Azure.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. Zaimportuj moduły programu PowerShell dla platformy Azure i usługi ExpressRoute.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. Użyj poniższego polecenia cmdlet, aby pobrać klucze usług dla wszystkich obwodów usługi ExpressRoute. Po pobraniu kluczy Skopiuj **klucz usługi** obwodu, który chcesz przenieść do modelu wdrażania Menedżer zasobów.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Krok 2. Logowanie i tworzenie grupy zasobów

Zaloguj się do środowiska Menedżer zasobów i Utwórz nową grupę zasobów.

1. Zaloguj się do środowiska Azure Resource Managerowego.

   ```powershell
   Connect-AzAccount
   ```

2. Wybierz odpowiednią subskrypcję platformy Azure.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Zmodyfikuj Poniższy fragment kodu, aby utworzyć nową grupę zasobów, jeśli nie masz jeszcze grupy zasobów.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Krok 3. przeniesienie obwodu ExpressRoute do modelu wdrażania Menedżer zasobów

Teraz można przystąpić do przenoszenia obwodu usługi ExpressRoute z klasycznego modelu wdrażania do modelu wdrażania Menedżer zasobów. Przed kontynuowaniem zapoznaj się z informacjami podanymi w temacie [przemieszczanie obwodu usługi ExpressRoute z klasycznego modelu wdrażania do Menedżer zasobów](expressroute-move.md).

Aby przenieść obwód, zmodyfikuj i uruchom następujący fragment kodu:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

W trybie klasycznym obwód ExpressRoute nie ma koncepcji powiązanej z regionem. Jednak w Menedżer zasobów każdy zasób należy zamapować do regionu platformy Azure. Region określony w poleceniu cmdlet Move-AzExpressRouteCircuit może być w dowolnym regionie. W celach organizacyjnych warto wybrać region, który dokładnie reprezentuje lokalizację komunikacji równorzędnej.

> [!NOTE]
> * Po przeniesieniu klasycznego obwodu usługi ExpressRoute do modelu wdrażania Menedżer zasobów będzie on miał domyślnie dostęp do modeli wdrożenia klasycznych i Menedżer zasobów.
> * Nowa nazwa wymieniona w poprzednim poleceniu cmdlet zostanie użyta do zaadresowania zasobu. Zmiana nazwy obwodu zostanie zmieniona.

## <a name="modify-circuit-access"></a>Modyfikuj dostęp do obwodu

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Aby włączyć dostęp do obwodu usługi ExpressRoute dla obu modeli wdrażania

Dostęp do klasycznego modelu wdrażania można włączyć dla obwodów usługi ExpressRoute, które zostały utworzone w ramach modelu wdrażania Menedżer zasobów. Uruchom następujące polecenia cmdlet, aby umożliwić dostęp do obu modeli wdrażania:

1. Pobierz szczegóły obwodu.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Dla opcji "Zezwalaj na działania klasyczne" Wybierz wartość PRAWDA.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Zaktualizuj obwód. Po pomyślnym zakończeniu tej operacji będzie można wyświetlić obwód w klasycznym modelu wdrażania.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Uruchom następujące polecenie cmdlet, aby uzyskać szczegóły obwodu ExpressRoute. Musisz mieć możliwość wyświetlenia na liście klucza usługi.

   ```powershell
   get-azurededicatedcircuit
   ```

5. Teraz można zarządzać łączami do obwodu usługi ExpressRoute przy użyciu klasycznych poleceń modelu wdrażania dla klasycznej sieci wirtualnych oraz poleceń Menedżer zasobów dla Menedżer zasobów sieci wirtualnych. Poniższe artykuły ułatwiają zarządzanie łączami do obwodu ExpressRoute:

    * [Łączenie sieci wirtualnej z obwodem ExpressRoute w modelu wdrażania Menedżer zasobów](expressroute-howto-linkvnet-arm.md)
    * [Łączenie sieci wirtualnej z obwodem ExpressRoute w klasycznym modelu wdrażania](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Aby wyłączyć dostęp obwodu ExpressRoute do klasycznego modelu wdrażania

Uruchom następujące polecenia cmdlet, aby wyłączyć dostęp do klasycznego modelu wdrażania.

1. Pobierz szczegóły obwodu ExpressRoute.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Dla ustawienia "Zezwalaj na operacje klasyczne" wartość FALSE.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Zaktualizuj obwód. Po pomyślnym zakończeniu tej operacji nie będzie można wyświetlić obwodu w klasycznym modelu wdrażania.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>Następne kroki

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-arm.md)
* [Łączenie sieci wirtualnej z obwodem ExpressRoute](expressroute-howto-linkvnet-arm.md)
