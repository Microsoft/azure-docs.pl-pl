---
title: Dostosowywanie kanału informacyjnego dla pulpitów wirtualnych systemu Windows 2019 użytkowników — Azure
description: Jak dostosować źródło danych dla użytkowników pulpitu wirtualnego systemu Windows za pomocą poleceń cmdlet programu PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9c573fc1aa220b9301e51ef0ccb1190d9243250e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011230"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Dostosowywanie kanału informacyjnego dla użytkowników usługi Windows Virtual Desktop

>[!IMPORTANT]
>Ta zawartość dotyczy wersji 2019, która nie Azure Resource Manager obsługuje obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektami pulpitu wirtualnego systemu Windows wprowadzonymi w ramach aktualizacji wiosną 2020, zobacz [ten artykuł](../customize-feed-for-virtual-desktop-users.md).

Możesz dostosować źródło danych, tak aby zasoby usługi RemoteApp i pulpitu zdalnego pojawiały się w rozpoznawanym sposobie dla użytkowników.

Najpierw [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](/powershell/windows-virtual-desktop/overview/) , który ma być używany w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono. Następnie uruchom następujące polecenie cmdlet, aby zalogować się do konta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Dostosowywanie nazwy wyświetlanej dla usługi RemoteApp

Można zmienić nazwę wyświetlaną opublikowanych programów RemoteApp, ustawiając przyjazną nazwę. Domyślnie przyjazna nazwa jest taka sama jak nazwa programu RemoteApp.

Aby pobrać listę opublikowanych programów RemoteApp dla grupy aplikacji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu poleceń cmdlet programu PowerShell Get-RDSRemoteApp z wyróżnioną nazwą i przyjaznymi nazwami.](../media/get-rdsremoteapp.png)

Aby przypisać przyjazną nazwę do usługi RemoteApp, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu poleceń cmdlet programu PowerShell Set-RDSRemoteApp o nazwie i nowej FriendlyName wyróżniony.](../media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Dostosuj nazwę wyświetlaną dla Pulpit zdalny

Można zmienić nazwę wyświetlaną opublikowanego pulpitu zdalnego, ustawiając przyjazną nazwę. Jeśli utworzono ręcznie pulę hostów i grupę aplikacji klasycznych za pomocą programu PowerShell, domyślną przyjazną nazwą będzie "sesja pulpitu". Jeśli utworzono pulę hostów i grupę aplikacji klasycznych za pomocą szablonu Azure Resource Manager GitHub lub oferty portalu Azure Marketplace, domyślna przyjazna nazwa jest taka sama jak nazwa puli hostów.

Aby pobrać zasób pulpitu zdalnego, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu poleceń cmdlet programu PowerShell Get-RDSRemoteApp z wyróżnioną nazwą i przyjaznymi nazwami.](../media/get-rdsremotedesktop.png)

Aby przypisać przyjazną nazwę do zasobu pulpitu zdalnego, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu poleceń cmdlet programu PowerShell Set-RDSRemoteApp o nazwie i nowej FriendlyName wyróżniony.](../media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Następne kroki

Teraz, po dostosowaniu źródła danych dla użytkowników, możesz zalogować się do klienta pulpitu wirtualnego systemu Windows w celu przetestowania go. Aby to zrobić, przejdź do programu Windows Virtual Desktop how-Toss:

 * [Łączenie z systemu Windows 10 lub Windows 7](connect-windows-7-10-2019.md)
 * [Łączenie z przeglądarki internetowej](connect-web-2019.md)
