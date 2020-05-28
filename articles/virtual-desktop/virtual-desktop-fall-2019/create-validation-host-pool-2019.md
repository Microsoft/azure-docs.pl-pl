---
title: Aktualizacje usługi puli hostów usług pulpitu wirtualnego systemu Windows — Azure
description: Jak utworzyć pulę hostów weryfikacji do monitorowania aktualizacji usługi przed przeprowadzeniem aktualizacji do środowiska produkcyjnego.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fe6b047d10b8aef8faf81365cae940b05b3d798c
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117739"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Samouczek: Tworzenie puli hostów w celu sprawdzenia poprawności aktualizacji usługi

>[!IMPORTANT]
>Ta zawartość dotyczy wersji 2019, która nie Azure Resource Manager obsługuje obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektami pulpitu wirtualnego systemu Windows wprowadzonymi w ramach aktualizacji wiosną 2020, zobacz [ten artykuł](../create-validation-host-pool.md).

Pule hostów są kolekcją co najmniej jednej identycznej maszyny wirtualnej w środowiskach dzierżawy usług pulpitu wirtualnego systemu Windows. Przed wdrożeniem pul hostów w środowisku produkcyjnym zdecydowanie zalecamy utworzenie puli hostów weryfikacji. Aktualizacje są stosowane najpierw w przypadku pul hostów weryfikacji, umożliwiając monitorowanie aktualizacji usługi przed ich wycofaniem do środowiska produkcyjnego. Bez puli hostów weryfikacji nie można wykryć zmian wprowadzających błędy, co może spowodować przestoje dla użytkowników w środowisku produkcyjnym.

Aby zapewnić, że aplikacje będą działać z najnowszymi aktualizacjami, Pula hostów weryfikacji powinna być podobna do pul hostów w środowisku produkcyjnym, jak to możliwe. Użytkownicy powinni często łączyć się z pulą hostów weryfikacji, tak jak w przypadku puli hostów produkcyjnych. W przypadku automatycznego testowania w puli hostów należy uwzględnić automatyczne testowanie w puli hostów weryfikacji.

Problemy w puli hostów weryfikacji można debugować za pomocą [funkcji diagnostyki](diagnostics-role-service-2019.md) lub [artykułów do rozwiązywania problemów z pulpitem wirtualnym systemu Windows](troubleshoot-set-up-overview-2019.md).

>[!NOTE]
> Zaleca się pozostawienie puli hostów weryfikacji w celu przetestowania wszystkich przyszłych aktualizacji.

Przed rozpoczęciem [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](/powershell/windows-virtual-desktop/overview/), jeśli jeszcze tego nie zrobiono. Następnie uruchom następujące polecenie cmdlet, aby zalogować się do konta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>Tworzenie puli hostów

Pulę hostów można utworzyć, postępując zgodnie z instrukcjami zawartymi w poniższych artykułach:
- [Samouczek: Tworzenie puli hostów za pomocą witryny Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
- [Tworzenie puli hostów za pomocą szablonu usługi Azure Resource Manager](create-host-pools-arm-template.md)
- [Tworzenie puli hostów przy użyciu programu PowerShell](create-host-pools-powershell-2019.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definiowanie puli hostów jako puli hostów weryfikacji

Uruchom następujące polecenia cmdlet programu PowerShell, aby zdefiniować nową pulę hostów jako pulę hostów weryfikacji. Zamień wartości w cudzysłowy przez wartości odpowiednie dla danej sesji:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Uruchom następujące polecenie cmdlet programu PowerShell, aby upewnić się, że właściwość walidacji została ustawiona. Zamień wartości w cudzysłowy przez wartości odpowiednie dla danej sesji.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

Wyniki polecenia cmdlet powinny wyglądać podobnie do tych danych wyjściowych:

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Harmonogram aktualizacji

Aktualizacje usługi są wykonywane co miesiąc. W przypadku wystąpienia poważnych problemów aktualizacje krytyczne będą udostępniane w krótszym tempie.

## <a name="next-steps"></a>Następne kroki

Teraz, po utworzeniu puli hostów weryfikacji, można dowiedzieć się, jak używać Azure Service Health do monitorowania wdrożenia pulpitu wirtualnego systemu Windows. 

> [!div class="nextstepaction"]
> [Konfigurowanie alertów usługi](set-up-service-alerts-2019.md)
