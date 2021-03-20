---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 1f8f8d314a8bb37a08b3696f597b395a8a4beb8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67183455"
---
## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

Jeśli nie wiesz, której lokalizacji użyć, możesz wyświetlić listę dostępnych lokalizacji. Wyświetl listę lokalizacji przy użyciu poniższego przykładu kodu i znajdź lokalizację, której chcesz użyć. W tym przykładzie użyto lokalizacji **eastus**. Zapisz lokalizację w zmiennej i używaj tej zmiennej, aby zmieniać lokalizację w jednym miejscu.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

```powershell
$resourceGroup = "myResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz standardowe konto magazynu ogólnego przeznaczenia z replikacją LRS za pomocą polecenia [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Następnie pobierz kontekst konta magazynu, który definiuje konto magazynu do użycia. Wykonując działania względem konta magazynu, odwołuj się do kontekstu, zamiast wielokrotnie przekazywać poświadczenia. Użyj poniższego przykładu, aby utworzyć konto magazynu o nazwie *mystorageaccount* z magazynem lokalnie nadmiarowym (LRS) i szyfrowaniem obiektów blob (włączonym domyślnie).

```powershell
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```
