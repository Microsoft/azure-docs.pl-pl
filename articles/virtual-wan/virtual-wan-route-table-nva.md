---
title: 'Wirtualna sieć WAN: Tworzenie tabeli tras koncentratora wirtualnego do urządzenie WUS: Azure PowerShell'
description: Tabela tras wirtualnego koncentratora sieci WAN do kierowania ruchu do sieciowego urządzenia wirtualnego.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: 40154a9c3cefed69bd3f1639153099b944da79b5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91267129"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Tworzenie tabeli tras koncentratora wirtualnego w celu kierowania ruchu do sieciowego urządzenia wirtualnego

W tym artykule opisano sposób kierowania ruchu z koncentratora wirtualnego do sieciowego urządzenia wirtualnego. 

![Diagram usługi Virtual WAN](./media/virtual-wan-route-table-nva/vwanroute.png)

W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie sieci WAN
* Tworzenie koncentratora
* Tworzenie wirtualnych połączeń sieciowych centrów
* Tworzenie trasy centrum
* Tworzenie tabeli tras
* Zastosuj tabelę tras

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sprawdź, czy zostały spełnione następujące kryteria:

* Masz wirtualne urządzenie sieciowe (urządzenie WUS). Jest to oprogramowanie innych firm, które jest zazwyczaj inicjowane z portalu Azure Marketplace w sieci wirtualnej.
* Masz prywatny adres IP przypisany do interfejsu sieciowego urządzenie WUS. 
* Nie można wdrożyć urządzenie WUS w koncentratorze wirtualnym. Należy ją wdrożyć w oddzielnej sieci wirtualnej. W tym artykule Sieć wirtualna urządzenie WUS jest określana jako "Sieć wirtualna strefy DMZ".
* "Sieć wirtualna strefy DMZ" może mieć co najmniej jedną podłączoną liczbę sieci wirtualnych. W tym artykule ta sieć wirtualna jest określana jako "pośrednia Sieć wirtualna". Te sieci wirtualnych mogą być połączone z siecią wirtualną DMZ przy użyciu komunikacji równorzędnej sieci wirtualnej.
* Sprawdź, czy utworzono już 2 sieci wirtualnych. Zostaną one użyte jako szprych sieci wirtualnych. W tym artykule przestrzenie adresowe sieci wirtualnej szprych to 10.0.2.0/24 i 10.0.3.0/24. Jeśli potrzebujesz informacji na temat sposobu tworzenia sieci wirtualnej, zobacz [Create a Virtual Network using PowerShell](../virtual-network/quick-create-powershell.md).
* Upewnij się, że w żadnym sieci wirtualnych nie ma żadnych bram sieci wirtualnej.

## <a name="1-sign-in"></a><a name="signin"></a>1. Zaloguj się

Upewnij się, że zainstalowano najnowszą wersję Menedżer zasobów poleceń cmdlet programu PowerShell. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-az-ps). Jest to ważne, ponieważ wcześniejsze wersje poleceń cmdlet nie zawierają bieżących wartości potrzebnych w tym ćwiczeniu.

1. Otwórz konsolę programu PowerShell z podniesionymi uprawnieniami i zaloguj się na koncie platformy Azure. To polecenie cmdlet poprosi o poświadczenia logowania. Po zalogowaniu pobiera Twoje ustawienia konta, aby były dostępne do Azure PowerShell.

   ```powershell
   Connect-AzAccount
   ```
2. Pobierz listę subskrypcji platformy Azure.

   ```powershell
   Get-AzSubscription
   ```
3. Wskaż subskrypcję, której chcesz użyć.

   ```powershell
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

## <a name="2-create-resources"></a><a name="rg"></a>2. Tworzenie zasobów

1. Utwórz grupę zasobów.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. Utwórz wirtualną sieć WAN.

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. Tworzenie koncentratora wirtualnego.

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24" -Location "West US"
   ```

## <a name="3-create-connections"></a><a name="connections"></a>3. Utwórz połączenia

Utwórz centra połączeń sieci wirtualnej z pośrednią satelitą i sieć wirtualną DMZ do koncentratora wirtualnego.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="4-create-a-virtual-hub-route"></a><a name="route"></a>4. Tworzenie trasy koncentratora wirtualnego

W tym artykule pośrednie przestrzenie adresowe sieci wirtualnej szprych to 10.0.2.0/24 i 10.0.3.0/24, a prywatny adres IP strefy DMZ urządzenie WUS to 10.0.4.5.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="5-create-a-virtual-hub-route-table"></a><a name="applyroute"></a>5. Tworzenie tabeli tras koncentratora wirtualnego

Utwórz tabelę tras koncentratora wirtualnego, a następnie Zastosuj do niej utworzoną trasę.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="6-commit-the-changes"></a><a name="commit"></a>6. Zatwierdź zmiany

Zatwierdź zmiany w koncentratorze wirtualnym.

```powershell
Update-AzVirtualHub -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
