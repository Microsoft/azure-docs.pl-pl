---
title: Klonowanie aplikacji za pomocą programu PowerShell
description: Dowiedz się, jak sklonować aplikację App Service do nowej aplikacji przy użyciu programu PowerShell. Ominięte są różne scenariusze klonowania, w tym Traffic Manager integracji.
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 63ab20b16ae41aa48822f1b5c8e733c93d97f581
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833188"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Azure App Service klonowania aplikacji przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W wersji Microsoft Azure PowerShell 1.1.0 dodano nową opcję, która umożliwia sklonowanie istniejącej aplikacji App Service do nowo utworzonej aplikacji w innym regionie lub w tym samym `New-AzWebApp` regionie. Ta opcja umożliwia klientom szybkie i łatwe wdrażanie wielu aplikacji w różnych regionach.

Klonowanie aplikacji jest obsługiwane w przypadku planów usługi App Service w chmurze Standardowa, Premium, Premium V2 i Izolowana. Nowa funkcja korzysta z tych samych ograniczeń co App Service Backup. Zobacz [Tworzenie kopii zapasowej](manage-backup.md)aplikacji w Azure App Service.

## <a name="cloning-an-existing-app"></a>Klonowanie istniejącej aplikacji
Scenariusz: Istniejąca aplikacja w regionie Południowo-środkowe usa i chcesz sklonować zawartość do nowej aplikacji w regionie Północno-środkowe usa. Można to zrobić za pomocą Azure Resource Manager polecenia cmdlet programu PowerShell, aby utworzyć nową aplikację z `-SourceWebApp` opcją .

Znając nazwę grupy zasobów, która zawiera aplikację źródłową, możesz użyć następującego polecenia programu PowerShell, aby uzyskać informacje o aplikacji źródłowej (w tym przypadku o nazwie `source-webapp` ):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Aby utworzyć nowy plan App Service, możesz użyć `New-AzAppServicePlan` polecenia , jak w poniższym przykładzie

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

Za pomocą polecenia możesz utworzyć nową aplikację w regionie Północno-środkowe usa i powiązać ją z istniejącym `New-AzWebApp` planem App Service. Ponadto można użyć tej samej grupy zasobów co aplikacja źródłowa lub zdefiniować nową grupę zasobów, jak pokazano w poniższym poleceniu:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Aby sklonować istniejącą aplikację, w tym wszystkie skojarzone miejsca wdrożenia, należy użyć `IncludeSourceWebAppSlots` parametru .  Należy `IncludeSourceWebAppSlots` pamiętać, że parametr jest obsługiwany tylko w przypadku klonowania całej aplikacji, w tym wszystkich jej miejsc. Następujące polecenie programu PowerShell demonstruje użycie tego parametru za pomocą `New-AzWebApp` polecenia :

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Aby sklonować istniejącą aplikację w tym samym regionie, musisz utworzyć nową grupę zasobów i nowy plan usługi App Service w tym samym regionie, a następnie użyć następującego polecenia programu PowerShell, aby sklonować aplikację:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Klonowanie istniejącej aplikacji do App Service Environment
Scenariusz: istniejąca aplikacja w regionie Południowo-środkowe usa i chcesz sklonować zawartość do nowej aplikacji do istniejącego App Service Environment (ASE).

Znając nazwę grupy zasobów, która zawiera aplikację źródłową, możesz użyć następującego polecenia programu PowerShell, aby uzyskać informacje o aplikacji źródłowej (w tym przypadku o nazwie `source-webapp` ):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Znając nazwę i nazwę grupy zasobów, do której należy ase, możesz utworzyć nową aplikację w istniejącym asecie, jak pokazano w następującym poleceniu:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

Parametr jest wymagany ze względu na starszą przyczynę, ale jest ignorowany podczas tworzenia `Location` aplikacji w asee. 

## <a name="cloning-an-existing-app-slot"></a>Klonowanie istniejącego miejsca aplikacji
Scenariusz: chcesz sklonować istniejące miejsce wdrożenia aplikacji do nowej aplikacji lub nowego miejsca. Nowa aplikacja może być w tym samym regionie co oryginalne miejsce aplikacji lub w innym regionie.

Znając nazwę grupy zasobów, która zawiera aplikację źródłową, możesz użyć następującego polecenia programu PowerShell, aby uzyskać informacje o miejscu aplikacji źródłowej (w tym przypadku o nazwie `source-appslot` ) powiązane z `source-app` :

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

Następujące polecenie demonstruje tworzenie klonu aplikacji źródłowej do nowej aplikacji:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Konfigurowanie Traffic Manager podczas klonowania aplikacji
Tworzenie aplikacji w wielu regionach i konfigurowanie Azure Traffic Manager do przekierowania ruchu do wszystkich tych aplikacji jest ważnym scenariuszem zapewniającym wysoką dostęp do aplikacji klientów. Podczas klonowania istniejącej aplikacji można połączyć obie aplikacje z nowym lub istniejącym profilem usługi Traffic Manager. Obsługiwana Azure Resource Manager jest Traffic Manager tylko Traffic Manager wersji programu .

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Tworzenie nowego profilu Traffic Manager podczas klonowania aplikacji
Scenariusz: Chcesz sklonować aplikację do innego regionu podczas konfigurowania profilu usługi Azure Resource Manager Traffic Manager, który obejmuje obie aplikacje. Następujące polecenie demonstruje tworzenie klonu aplikacji źródłowej do nowej aplikacji podczas konfigurowania nowego Traffic Manager profilu:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Dodawanie nowej sklonowanej aplikacji do istniejącego Traffic Manager profilu
Scenariusz: Masz już profil usługi Azure Resource Manager Traffic Manager i chcesz dodać obie aplikacje jako punkty końcowe. W tym celu należy najpierw złożyć istniejący identyfikator profilu usługi Traffic Manager. Potrzebujesz identyfikatora subskrypcji, nazwy grupy zasobów i istniejącej nazwy profilu usługi Traffic Manager.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Po utworzeniu identyfikatora usługi Traffic Manger następujące polecenie demonstruje tworzenie klonu aplikacji źródłowej do nowej aplikacji podczas dodawania ich do istniejącego Traffic Manager profilu:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```
> [!NOTE]
> Jeśli otrzymujesz komunikat o błędzie z komunikatem "Sprawdzanie poprawności protokołu SSL na nazwie hosta usługi Traffic Manager nie działa", sugerujemy użycie atrybutu -IgnoreCustomHostNames w poleceniach cmdlet programu PowerShell podczas wykonywania operacji klonowania lub użycia portalu.

## <a name="current-restrictions"></a>Bieżące ograniczenia
Poniżej podano znane ograniczenia klonowania aplikacji:

* Ustawienia automatycznego skalowania nie są klonowane
* Ustawienia harmonogramu tworzenia kopii zapasowych nie są klonowane
* Ustawienia sieci wirtualnej nie są klonowane
* Nie można automatycznie skonfigurować usługi App Insights w aplikacji docelowej
* Ustawienia łatwego uwierzytelniania nie są klonowane
* Rozszerzenie Kudu nie jest klonowane
* Reguły TiP nie są klonowane
* Zawartość bazy danych nie jest klonowana
* Wychodzące adresy IP zmienią się w przypadku klonowania do innej jednostki skalowania
* Niedostępne dla aplikacji systemu Linux
* Tożsamości zarządzane nie są klonowane

### <a name="references"></a>Odwołania
* [App Service klonowania](app-service-web-app-cloning.md)
* [Back up an app in Azure App Service](manage-backup.md)
* [Azure Resource Manager wersji zapoznawczej dla Azure Traffic Manager zapoznawczej](../traffic-manager/traffic-manager-powershell-arm.md)
* [Wprowadzenie do usługi App Service Environment](environment/intro.md)
* [Używanie programu Azure PowerShell z usługą Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)

