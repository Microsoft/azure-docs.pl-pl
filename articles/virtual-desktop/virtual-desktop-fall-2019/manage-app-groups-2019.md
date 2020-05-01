---
title: Zarządzanie grupami aplikacji dla pulpitu wirtualnego systemu Windows — Azure
description: Opisuje sposób konfigurowania dzierżawców pulpitów wirtualnych systemu Windows w Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 55c2ed5f9066d85643588f77806bbd41e92dd16b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614417"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop"></a>Samouczek: Zarządzanie grupami aplikacji dla pulpitu wirtualnego systemu Windows

>[!IMPORTANT]
>Ta zawartość dotyczy wersji 2019, która nie Azure Resource Manager obsługuje obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektami pulpitu wirtualnego systemu Windows wprowadzonymi w ramach aktualizacji wiosną 2020, zobacz [ten artykuł](../manage-app-groups.md).

Domyślna grupa aplikacji utworzona dla nowej puli hostów systemu Windows Virtual Desktop również publikuje pełny pulpit. Ponadto można utworzyć co najmniej jedną grupę aplikacji RemoteApp dla puli hostów. Postępuj zgodnie z tym samouczkiem, aby utworzyć grupę aplikacji RemoteApp i opublikować poszczególne aplikacje menu **Start** .

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz grupę usługi RemoteApp.
> * Udzielanie dostępu do programów RemoteApp.

Przed rozpoczęciem [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](/powershell/windows-virtual-desktop/overview/) , który ma być używany w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono. Następnie uruchom następujące polecenie cmdlet, aby zalogować się do konta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Tworzenie grupy usługi RemoteApp

1. Uruchom następujące polecenie cmdlet programu PowerShell, aby utworzyć nową pustą grupę aplikacji RemoteApp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. Obowiązkowe Aby sprawdzić, czy grupa aplikacji została utworzona, możesz uruchomić następujące polecenie cmdlet, aby wyświetlić listę wszystkich grup aplikacji dla puli hostów.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Uruchom następujące polecenie cmdlet, aby uzyskać listę aplikacji menu **Start** w obrazie maszyny wirtualnej puli hostów. Zapisz wartości **FilePath**, **IconPath**, **IconIndex**i inne ważne informacje dla aplikacji, którą chcesz opublikować.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Uruchom następujące polecenie cmdlet, aby zainstalować aplikację opartą `AppAlias`na systemie. `AppAlias`staną się widoczne po uruchomieniu danych wyjściowych z kroku 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. Obowiązkowe Uruchom następujące polecenie cmdlet, aby opublikować nowy Program RemoteApp w grupie aplikacji utworzonej w kroku 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Aby sprawdzić, czy aplikacja została opublikowana, uruchom następujące polecenie cmdlet.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Powtórz kroki od 1 do 5 dla każdej aplikacji, która ma zostać opublikowana dla tej grupy aplikacji.
8. Uruchom następujące polecenie cmdlet, aby udzielić użytkownikom dostępu do programów RemoteApp w grupie aplikacji.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia grupy aplikacji, wypełniania jej przy użyciu programów RemoteApp i przypisywania użytkowników do grupy aplikacji. Aby dowiedzieć się, jak utworzyć pulę hostów weryfikacji, zobacz poniższy samouczek. Można użyć puli hostów weryfikacji do monitorowania aktualizacji usługi przed ich wycofaniem do środowiska produkcyjnego.

> [!div class="nextstepaction"]
> [Tworzenie puli hostów na potrzeby weryfikacji aktualizacji usług](create-validation-host-pool-2019.md)
