---
title: Typ przypisania pulpitu osobistego pulpitu wirtualnego systemu Windows — Azure
description: Jak skonfigurować typ przypisania dla puli hostów osobistych pulpitów wirtualnych systemu Windows.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158ac92a930b53e02ee81570c62711ca27dc4ae8
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85200396"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Konfigurowanie typu przypisania puli hostów pulpitu osobistego

>[!IMPORTANT]
>Ta zawartość ma zastosowanie do aktualizacji wiosennej 2020 z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows, wykorzystaj wersję 2019 bez obiektów Azure Resource Manager, zobacz [ten artykuł](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md).
>
> Aktualizacja systemu Windows Virtual Desktop wiosna 2020 jest obecnie dostępna w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Można skonfigurować typ przypisania puli hostów osobistych, aby dostosować środowisko pulpitu wirtualnego systemu Windows w celu lepszego dopasowania do Twoich potrzeb. W tym temacie pokazano, jak skonfigurować automatyczne lub bezpośrednie przypisanie dla użytkowników.

>[!NOTE]
> Instrukcje zawarte w tym artykule dotyczą tylko pul hostów osobistych, a nie pul hostów w puli, ponieważ użytkownicy w puli hostów z pulą nie są przypisani do określonych hostów sesji.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że został już pobrany i zainstalowany moduł PowerShell pulpitu wirtualnego systemu Windows. Jeśli nie, postępuj zgodnie z instrukcjami podanymi w temacie [Konfigurowanie modułu programu PowerShell](powershell-module.md).

## <a name="configure-automatic-assignment"></a>Konfigurowanie automatycznego przypisywania

Automatyczne przypisywanie jest domyślnym typem przypisania dla nowych pul hostów osobistych tworzonych w środowisku pulpitu wirtualnego systemu Windows. Automatyczne przypisywanie użytkowników nie wymaga określonego hosta sesji.

Aby automatycznie przypisywać użytkowników, należy najpierw przypisać je do puli hostów osobistych, aby mogli zobaczyć pulpit w ich kanale informacyjnym. Gdy przypisany użytkownik uruchamia pulpit w ich kanale informacyjnym, będzie przejąć dostępnego hosta sesji, jeśli nie został jeszcze połączony z pulą hostów, co kończy proces przypisywania.

Aby skonfigurować pulę hostów do automatycznego przypisywania użytkowników do maszyn wirtualnych, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

Aby przypisać użytkownika do puli hostów osobistych, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>Skonfiguruj przypisanie bezpośrednie

W przeciwieństwie do automatycznego przypisywania, w przypadku używania przypisania bezpośredniego należy przypisać użytkownika do puli hostów osobistych i określonego hosta sesji, zanim będą mogli połączyć się z pulpitem osobistym. Jeśli użytkownik jest przypisany do puli hostów bez przypisania hosta sesji, nie będzie mógł uzyskać dostępu do zasobów.

Aby skonfigurować pulę hostów do żądania bezpośredniego przypisywania użytkowników do hostów sesji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

Aby przypisać użytkownika do puli hostów osobistych, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Aby przypisać użytkownika do określonego hosta sesji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Następne kroki

Teraz, po skonfigurowaniu typu przypisania pulpitu osobistego, można zalogować się do klienta pulpitu wirtualnego systemu Windows w celu przetestowania go w ramach sesji użytkownika. Te następne dwie porady informują Cię, jak nawiązać połączenie z sesją przy użyciu wybranego przez siebie klienta:

- [Łączenie się z klientem klasycznym systemu Windows](connect-windows-7-and-10.md)
- [Łączenie się z klientem internetowym](connect-web.md)
- [Łączenie się z klientem systemu Android](connect-android.md)
- [Nawiązywanie połączenia z klientem systemu iOS](connect-ios.md)
- [Nawiązywanie połączenia z klientem systemu macOS](connect-macos.md)