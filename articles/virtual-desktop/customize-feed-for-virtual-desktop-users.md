---
title: Dostosowywanie kanału informacyjnego dla użytkowników pulpitu wirtualnego systemu Windows — Azure
description: Jak dostosować źródło danych dla użytkowników pulpitu wirtualnego systemu Windows za pomocą poleceń cmdlet programu PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 09/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: e47486f29537cb948aaae7cf17e97bae14b60700
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90084298"
---
# <a name="customize-the-feed-for-windows-virtual-desktop-users"></a>Dostosowywanie kanału informacyjnego dla użytkowników pulpitu wirtualnego systemu Windows

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md).

Możesz dostosować źródło danych, tak aby zasoby usługi RemoteApp i pulpitu zdalnego pojawiały się w rozpoznawanym sposobie dla użytkowników.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że został już pobrany i zainstalowany moduł PowerShell pulpitu wirtualnego systemu Windows. Jeśli nie, postępuj zgodnie z instrukcjami podanymi w temacie [Konfigurowanie modułu programu PowerShell](powershell-module.md).

## <a name="customize-the-display-name-for-a-remoteapp"></a>Dostosowywanie nazwy wyświetlanej dla usługi RemoteApp

Można zmienić nazwę wyświetlaną opublikowanych programów RemoteApp, ustawiając przyjazną nazwę. Domyślnie przyjazna nazwa jest taka sama jak nazwa programu RemoteApp.

Aby pobrać listę opublikowanych programów RemoteApp dla grupy aplikacji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

Aby przypisać przyjazną nazwę do usługi RemoteApp, uruchom następujące polecenie cmdlet z wymaganymi parametrami:

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

Załóżmy na przykład, że pobrano bieżące aplikacje za pomocą następującego przykładowego polecenia cmdlet:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

Dane wyjściowe będą wyglądać następująco:

```powershell
CommandLineArgument :
CommandLineSetting  : DoNotAllow
Description         :
FilePath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
FriendlyName        : Microsoft Word
IconContent         : {0, 0, 1, 0…}
IconHash            : --iom0PS6XLu-EMMlHWVW3F7LLsNt63Zz2K10RE0_64
IconIndex           : 0
IconPath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
Id                  : /subscriptions/<subid>/resourcegroups/0301RG/providers/Microsoft.DesktopVirtualization/applicationgroups/0301RAG/applications/Microsoft Word
Name                : 0301RAG/Microsoft Word
ShowInPortal        : False
Type                : Microsoft.DesktopVirtualization/applicationgroups/applications
```
Aby zaktualizować przyjazną nazwę, uruchom następujące polecenie cmdlet:

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe"
```

Aby upewnić się, że pomyślnie Zaktualizowano przyjazną nazwę, Uruchom to polecenie cmdlet:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

Polecenie cmdlet powinno dać następujące dane wyjściowe:

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Dostosuj nazwę wyświetlaną dla Pulpit zdalny

Można zmienić nazwę wyświetlaną opublikowanego pulpitu zdalnego, ustawiając przyjazną nazwę. Jeśli utworzono ręcznie pulę hostów i grupę aplikacji klasycznych za pomocą programu PowerShell, domyślną przyjazną nazwą będzie "sesja pulpitu". Jeśli utworzono pulę hostów i grupę aplikacji klasycznych za pomocą szablonu Azure Resource Manager GitHub lub oferty portalu Azure Marketplace, domyślna przyjazna nazwa jest taka sama jak nazwa puli hostów.

Aby pobrać zasób pulpitu zdalnego, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

Aby przypisać przyjazną nazwę do zasobu pulpitu zdalnego, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>Dostosuj nazwę wyświetlaną w Azure Portal

Można zmienić nazwę wyświetlaną opublikowanego pulpitu zdalnego, ustawiając przyjazną nazwę przy użyciu Azure Portal.

1. Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com>.

2. Wyszukaj **pulpit wirtualny systemu Windows**.

3. W obszarze usługi wybierz pozycję **pulpit wirtualny systemu Windows**.

4. Na stronie pulpit wirtualny systemu Windows wybierz pozycję **grupy aplikacji** po lewej stronie ekranu, a następnie wybierz nazwę grupy aplikacji, którą chcesz edytować. (Na przykład jeśli chcesz edytować nazwę wyświetlaną grupy aplikacji klasycznych, wybierz grupę aplikacji o nazwie **Desktop**).

5. Wybierz pozycję **aplikacje** w menu po lewej stronie ekranu.

6. Wybierz aplikację, którą chcesz zaktualizować, a następnie wprowadź nową **nazwę wyświetlaną**.

7. Wybierz pozycję **Zapisz**. Edytowana aplikacja powinna teraz wyświetlić zaktualizowaną nazwę.

## <a name="next-steps"></a>Następne kroki

Teraz, po dostosowaniu źródła danych dla użytkowników, możesz zalogować się do klienta pulpitu wirtualnego systemu Windows w celu przetestowania go. Aby to zrobić, przejdź do programu Windows Virtual Desktop how-Toss:

 * [Nawiązywanie połączenia z systemem Windows 10 lub Windows 7](connect-windows-7-10.md)
 * [Łączenie się z klientem internetowym](connect-web.md)
 * [Łączenie się z klientem systemu Android](connect-android.md)
 * [Nawiązywanie połączenia z klientem systemu iOS](connect-ios.md)
 * [Nawiązywanie połączenia z klientem systemu macOS](connect-macos.md)
