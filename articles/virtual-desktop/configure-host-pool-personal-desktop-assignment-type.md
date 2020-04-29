---
title: Typ przypisania pulpitu osobistego pulpitu wirtualnego systemu Windows — Azure
description: Jak skonfigurować typ przypisania dla puli hostów osobistych pulpitów wirtualnych systemu Windows.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 41b24a94d36b21fe5d5f539e056abb535bda433a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128291"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Konfigurowanie typu przypisania puli hostów pulpitu osobistego

Można skonfigurować typ przypisania puli hostów osobistych, aby dostosować środowisko pulpitu wirtualnego systemu Windows w celu lepszego dopasowania do Twoich potrzeb. W tym temacie pokazano, jak skonfigurować automatyczne lub bezpośrednie przypisanie dla użytkowników.

>[!NOTE]
> Instrukcje zawarte w tym artykule dotyczą tylko pul hostów osobistych, a nie pul hostów w puli, ponieważ użytkownicy w puli hostów z pulą nie są przypisani do określonych hostów sesji.

## <a name="configure-automatic-assignment"></a>Konfigurowanie automatycznego przypisywania

Automatyczne przypisywanie jest domyślnym typem przypisania dla nowych pul hostów osobistych tworzonych w środowisku pulpitu wirtualnego systemu Windows. Automatyczne przypisywanie użytkowników nie wymaga określonego hosta sesji.

Aby automatycznie przypisywać użytkowników, należy najpierw przypisać je do puli hostów osobistych, aby mogli zobaczyć pulpit w ich kanale informacyjnym. Gdy przypisany użytkownik uruchamia pulpit w ich kanale informacyjnym, będzie przejąć dostępnego hosta sesji, jeśli nie został jeszcze połączony z pulą hostów, co kończy proces przypisywania.

Przed rozpoczęciem [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](/powershell/windows-virtual-desktop/overview/) , jeśli jeszcze tego nie zrobiono. 

> [!NOTE]
> Upewnij się, że zainstalowano moduł PowerShell programu Windows Virtual Desktop w wersji 1.0.1534.2001 lub nowszej przed wykonaniem tych instrukcji.

Następnie uruchom następujące polecenie cmdlet, aby zalogować się do konta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Aby skonfigurować pulę hostów do automatycznego przypisywania użytkowników do maszyn wirtualnych, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Aby przypisać użytkownika do puli hostów osobistych, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Skonfiguruj przypisanie bezpośrednie

W przeciwieństwie do automatycznego przypisywania, w przypadku używania przypisania bezpośredniego należy przypisać użytkownika do puli hostów osobistych i określonego hosta sesji, zanim będą mogli połączyć się z pulpitem osobistym. Jeśli użytkownik jest przypisany do puli hostów bez przypisania hosta sesji, nie będzie mógł uzyskać dostępu do zasobów.

Aby skonfigurować pulę hostów do żądania bezpośredniego przypisywania użytkowników do hostów sesji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Aby przypisać użytkownika do puli hostów osobistych, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Aby przypisać użytkownika do określonego hosta sesji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Następne kroki

Teraz, po skonfigurowaniu typu przypisania pulpitu osobistego, można zalogować się do klienta pulpitu wirtualnego systemu Windows w celu przetestowania go w ramach sesji użytkownika. Te następne dwie porady informują Cię, jak nawiązać połączenie z sesją przy użyciu wybranego przez siebie klienta:

- [Łączenie się z klientem klasycznym systemu Windows](connect-windows-7-and-10.md)
- [Łączenie się z klientem internetowym](connect-web.md)
