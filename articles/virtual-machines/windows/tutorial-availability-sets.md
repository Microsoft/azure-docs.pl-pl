---
title: Wdrażanie maszyn wirtualnych w zestawie dostępności za pomocą Azure PowerShell
description: Dowiedz się, jak używać Azure PowerShell do wdrażania maszyn wirtualnych wysokiej dostępności w zestawach dostępności
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.custom: mvc
ms.openlocfilehash: 178a29ea37195ddd2013ca5220663a75132beb24
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555911"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-powershell"></a>Tworzenie i wdrażanie maszyn wirtualnych w zestawie dostępności za pomocą Azure PowerShell

Z tego samouczka dowiesz się, jak zwiększyć dostępność i niezawodność maszyn wirtualnych przy użyciu funkcji zestawów dostępności. Zestawy dostępności zapewniają rozproszenie maszyn wirtualnych wdrożonych na platformie Azure między wieloma izolowanymi węzłami sprzętowymi w klastrze. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zestawu dostępności
> * Tworzenie maszyny wirtualnej w zestawie dostępności
> * Sprawdzanie dostępnych rozmiarów maszyn wirtualnych
> * Sprawdzanie usługi Azure Advisor


## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności

Sprzęt w jednej lokalizacji jest podzielony na wiele domen aktualizacji i domen błędów. **Domena aktualizacji** to grupa maszyn wirtualnych i używanego przez nie sprzętu fizycznego, które mogą być jednocześnie ponownie uruchamiane. Maszyny wirtualne w jednej **domenie błędów** korzystają ze wspólnego magazynu oraz ze wspólnego źródła zasilania i przełącznika sieciowego.  

Aby utworzyć zestaw dostępności, użyj polecenia [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). W tym przykładzie liczba domen aktualizacji i domen błędów wynosi *2*, a zestaw dostępności ma nazwę *myAvailabilitySet*.

Utwórz grupę zasobów.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Utwórz zarządzany zestaw dostępności przy użyciu polecenia [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) z parametrem `-sku aligned`.

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Tworzenie maszyn wirtualnych w zestawie dostępności
Aby zapewnić właściwe rozproszenie maszyn wirtualnych na sprzęcie, należy utworzyć je w ramach zestawu dostępności. Nie można dodać istniejącej, wcześniej utworzonej maszyny wirtualnej do zestawu dostępności. 


Podczas tworzenia maszyny wirtualnej przy użyciu polecenia [New-AzVM](/powershell/module/az.compute/new-azvm) parametr `-AvailabilitySetName` umożliwia określenie nazwy zestawu dostępności.

Najpierw ustaw nazwę użytkownika i hasło administratora maszyny wirtualnej przy użyciu polecenia [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Następnie utwórz dwie maszyny wirtualne w zestawie dostępności, używając polecenia [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

Utworzenie i skonfigurowanie obu maszyn wirtualnych może potrwać kilka minut. Po zakończeniu powstaną dwie maszyny wirtualne rozproszone między wiele elementów sprzętowych. 

Jeśli zapoznaj się z zestawem dostępności w portalu, przejdź do pozycji **grupy zasobów**  >  **myResourceGroupAvailability**  >  **myAvailabilitySet**, zobacz, jak maszyny wirtualne są dystrybuowane w ramach dwóch domen błędów i aktualizacji.

![Zestaw dostępności w portalu](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Sprawdzanie dostępnych rozmiarów maszyn wirtualnych 

Podczas tworzenia maszyny wirtualnej w ramach zestawu dostępności należy wiedzieć, jakie rozmiary maszyn wirtualnych są dostępne na sprzęcie. Użyj polecenia [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) , aby uzyskać wszystkie dostępne rozmiary maszyn wirtualnych, które można wdrożyć w zestawie dostępności.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Sprawdzanie usługi Azure Advisor 

Możesz także uzyskać dodatkowe informacje na temat sposobów poprawy dostępności maszyn wirtualnych, korzystając z usługi Azure Advisor. Usługa Azure Advisor analizuje konfigurację i dane telemetryczne dotyczące użycia, a następnie zaleca rozwiązania, które mogą pomóc w zapewnieniu dostępności, bezpieczeństwa, wydajności i efektywności kosztowej zasobów platformy Azure.

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com), wybierz pozycję **Wszystkie usługi**, a następnie wpisz **Advisor**. Pulpit nawigacyjny usługi Advisor będzie zawierać spersonalizowane zalecenia dotyczące wybranej subskrypcji. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zestawu dostępności
> * Tworzenie maszyny wirtualnej w zestawie dostępności
> * Sprawdzanie dostępnych rozmiarów maszyn wirtualnych
> * Sprawdzanie usługi Azure Advisor

Przejdź do następnego samouczka, aby poznać zestawy skalowania maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Tworzenie zestawu skalowania maszyn wirtualnych](tutorial-create-vmss.md)
