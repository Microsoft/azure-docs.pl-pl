---
title: Zarządzanie grupami aplikacji dla pulpitu wirtualnego systemu Windows (klasyczny) — Azure
description: Dowiedz się, jak skonfigurować dzierżawy pulpitu wirtualnego systemu Windows (klasyczne) w Azure Active Directory (AD).
author: Heidilohr
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 0b95c87ad447b92532bc6c456c06eb85162c7247
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444415"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-classic"></a>Samouczek: Zarządzanie grupami aplikacji dla pulpitu wirtualnego systemu Windows (klasyczny)

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows, zobacz [ten artykuł](../manage-app-groups.md).

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

3. Uruchom następujące polecenie cmdlet, aby uzyskać listę aplikacji menu **Start** w obrazie maszyny wirtualnej puli hostów. Zapisz wartości **FilePath**, **IconPath**, **IconIndex** i inne ważne informacje dla aplikacji, którą chcesz opublikować.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```

4. Uruchom następujące polecenie cmdlet, aby zainstalować aplikację opartą na systemie `AppAlias` . `AppAlias` staną się widoczne po uruchomieniu danych wyjściowych z kroku 3.

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
