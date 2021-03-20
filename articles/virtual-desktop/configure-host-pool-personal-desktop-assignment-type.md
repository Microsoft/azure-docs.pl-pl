---
title: Typ przypisania pulpitu osobistego pulpitu wirtualnego systemu Windows — Azure
description: Jak skonfigurować automatyczne lub bezpośrednie przypisanie dla puli hostów osobistych pulpitów wirtualnych systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 07/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 446dae3c064f5f23d35cb12b2b24bdfea9e27012
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88007815"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Konfigurowanie typu przypisania puli hostów pulpitu osobistego

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md).

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

Aby bezpośrednio przypisać użytkownika do hosta sesji w Azure Portal:

1. Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com>.
2. Na pasku wyszukiwania wprowadź **pulpit wirtualny systemu Windows** .
3. W obszarze **usługi** wybierz pozycję **pulpit wirtualny systemu Windows**.
4. Na stronie pulpit wirtualny systemu Windows przejdź do menu po lewej stronie okna i wybierz pozycję **Pule hostów**.
5. Wybierz nazwę puli hostów, którą chcesz zaktualizować.
6. Następnie przejdź do menu po lewej stronie okna i wybierz pozycję **grupy aplikacji**.
7. Wybierz nazwę grupy aplikacji klasycznych, którą chcesz edytować, a następnie wybierz pozycję **przypisania** w menu po lewej stronie okna.
8. Wybierz pozycję **+ Dodaj**, a następnie wybierz użytkowników lub grupy użytkowników, dla których chcesz opublikować tę grupę aplikacji klasycznych.
9. Wybierz pozycję **Przypisz maszynę wirtualną** na pasku informacji, aby przypisać Host sesji do użytkownika.
10. Wybierz hosta sesji, który chcesz przypisać do użytkownika, a następnie wybierz pozycję **Przypisz**.
11. Wybierz użytkownika, do którego chcesz przypisać hosta sesji z listy dostępnych użytkowników.
12. Gdy skończysz, wybierz pozycję **Wybierz**.

## <a name="next-steps"></a>Następne kroki

Teraz, po skonfigurowaniu typu przypisania pulpitu osobistego, można zalogować się do klienta pulpitu wirtualnego systemu Windows w celu przetestowania go w ramach sesji użytkownika. Te następne dwie porady informują Cię, jak nawiązać połączenie z sesją przy użyciu wybranego przez siebie klienta:

- [Łączenie się z klientem klasycznym systemu Windows](connect-windows-7-10.md)
- [Łączenie się z klientem internetowym](connect-web.md)
- [Łączenie się z klientem systemu Android](connect-android.md)
- [Nawiązywanie połączenia z klientem systemu iOS](connect-ios.md)
- [Nawiązywanie połączenia z klientem systemu macOS](connect-macos.md)