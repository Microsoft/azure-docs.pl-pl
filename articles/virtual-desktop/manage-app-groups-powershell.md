---
title: Zarządzanie grupami aplikacji dla systemu Windows Virtual Desktop PowerShell — Azure
description: Jak zarządzać grupami aplikacji klasycznymi systemu Windows przy użyciu programu PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 1652048da8084007bc86a6793f14f10b4a1f2e0f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446047"
---
# <a name="manage-app-groups-using-powershell"></a>Zarządzanie grupami aplikacji przy użyciu programu PowerShell

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/manage-app-groups-2019.md).

Domyślna grupa aplikacji utworzona dla nowej puli hostów systemu Windows Virtual Desktop również publikuje pełny pulpit. Ponadto można utworzyć co najmniej jedną grupę aplikacji RemoteApp dla puli hostów. Postępuj zgodnie z tym samouczkiem, aby utworzyć grupę aplikacji RemoteApp i opublikować poszczególne aplikacje menu **Start** .

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz grupę usługi RemoteApp.
> * Udzielanie dostępu do programów RemoteApp.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że wykonano instrukcje opisane w temacie [Konfigurowanie modułu programu PowerShell](powershell-module.md) w celu skonfigurowania modułu programu PowerShell i zalogowania się do konta platformy Azure.

## <a name="create-a-remoteapp-group"></a>Tworzenie grupy usługi RemoteApp

Aby utworzyć grupę usługi RemoteApp przy użyciu programu PowerShell:

1. Uruchom następujące polecenie cmdlet programu PowerShell, aby utworzyć nową pustą grupę aplikacji RemoteApp.

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. Obowiązkowe Aby sprawdzić, czy grupa aplikacji została utworzona, możesz uruchomić następujące polecenie cmdlet, aby wyświetlić listę wszystkich grup aplikacji dla puli hostów.

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. Uruchom następujące polecenie cmdlet, aby uzyskać listę aplikacji menu **Start** w obrazie maszyny wirtualnej puli hostów. Zapisz wartości **FilePath**, **IconPath**, **IconIndex** i inne ważne informacje dla aplikacji, którą chcesz opublikować.

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more
   ```

   Dane wyjściowe powinny zawierać wszystkie elementy menu Start w formacie podobnym do tego:

   ```powershell
   AppAlias            : access
   CommandLineArgument :
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access
   Name                : 0301RAG/Access
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems

   AppAlias            : charactermap
   CommandLineArgument :
   FilePath            : C:\windows\system32\charmap.exe
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\windows\system32\charmap.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map
   Name                : 0301RAG/Character Map
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   ```

4. Uruchom następujące polecenie cmdlet, aby zainstalować aplikację opartą na systemie `AppAlias` . `AppAlias` staną się widoczne po uruchomieniu danych wyjściowych z kroku 3.

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

5. Obowiązkowe Uruchom następujące polecenie cmdlet, aby opublikować nowy Program RemoteApp w grupie aplikacji utworzonej w kroku 1.

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

6. Aby sprawdzić, czy aplikacja została opublikowana, uruchom następujące polecenie cmdlet.

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. Powtórz kroki od 1 do 5 dla każdej aplikacji, która ma zostać opublikowana dla tej grupy aplikacji.
8. Uruchom następujące polecenie cmdlet, aby udzielić użytkownikom dostępu do programów RemoteApp w grupie aplikacji.

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

## <a name="next-steps"></a>Następne kroki

Jeśli wiesz, jak to zrobić z naszych samouczków, zapoznaj się z tematem [Tworzenie puli hostów w celu sprawdzenia poprawności aktualizacji usługi](create-validation-host-pool.md). Można użyć puli hostów weryfikacji do monitorowania aktualizacji usługi przed ich wycofaniem do środowiska produkcyjnego.