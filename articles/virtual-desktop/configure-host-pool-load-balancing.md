---
title: Konfigurowanie równoważenia obciążenia systemu Windows Virtual Desktop — Azure
description: Jak skonfigurować metodę równoważenia obciążenia dla środowiska pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8670994791e360f5e3b30b90b4bea5d55464b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128308"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Konfigurowanie metody równoważenia obciążenia usługi Windows Virtual Desktop

Skonfigurowanie metody równoważenia obciążenia dla puli hostów umożliwia dostosowanie środowiska pulpitu wirtualnego systemu Windows w celu lepszego dopasowania do Twoich potrzeb.

>[!NOTE]
> Nie dotyczy to trwałej puli hostów komputerów stacjonarnych, ponieważ użytkownicy mają zawsze mapowanie 1:1 do hosta sesji w puli hostów.

## <a name="configure-breadth-first-load-balancing"></a>Konfigurowanie równoważenia obciążenia w pierwszej kolejności

Pierwsze Równoważenie obciążenia jest konfiguracją domyślną dla nowych pul hostów nietrwałych. Równoważenie obciążenia w pierwszej kolejności dystrybuuje nowe sesje użytkowników między wszystkimi dostępnymi hostami sesji w puli hostów. Podczas konfigurowania równoważenia obciążenia w pierwszej kolejności można ustawić maksymalny limit sesji dla każdego hosta sesji w puli hostów.

Najpierw [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](/powershell/windows-virtual-desktop/overview/) , który ma być używany w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono. Następnie uruchom następujące polecenie cmdlet, aby zalogować się do konta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Aby skonfigurować pulę hostów do wykonywania równoważenia obciążenia w pierwszej kolejności bez dostosowywania limitu liczby sesji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Aby skonfigurować pulę hostów do wykonywania równoważenia obciążenia w pierwszej kolejności i użyć nowego limitu maksymalnej liczby sesji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Konfigurowanie pierwszej równoważenia obciążenia

Usługa Równoważenie obciążenia po raz pierwszy dystrybuuje nowe sesje użytkowników do dostępnego hosta sesji o największej liczbie połączeń, ale nie osiągnął progu limitu liczby sesji. Podczas konfigurowania równoważenia obciążenia najpierw **należy** ustawić maksymalny limit sesji dla każdego hosta sesji w puli hostów.

Aby skonfigurować pulę hostów do wykonywania równoważenia obciążenia w pierwszej kolejności, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
