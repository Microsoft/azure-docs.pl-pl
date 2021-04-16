---
title: Ustawianie Windows Virtual Desktop opróżniania — Azure
description: Jak skonfigurować i używać trybu opróżniania w Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 19ef7d520800ac703ed77dc0520e5b860306c4bd
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509127"
---
# <a name="set-drain-mode"></a>Ustawianie trybu opróżniania

Tryb opróżniania izoluje hosta sesji, gdy chcesz zastosować poprawki i wykonać konserwację bez zakłócania sesji użytkowników. W przypadku izolowania host sesji nie będzie akceptował nowych sesji użytkowników. Wszystkie nowe połączenia zostaną przekierowane do następnego dostępnego hosta sesji. Istniejące połączenia na hoście sesji będą nadal działać, dopóki użytkownik nie wyyestruje się lub administrator zakończy sesję. Gdy host sesji jest w trybie opróżniania, administratorzy mogą również zdalnie łączyć się z serwerem bez konieczności Windows Virtual Desktop usługi. To ustawienie można zastosować zarówno do komputerów stacjonarnych w puli, jak i osobistych.

## <a name="set-drain-mode-using-the-azure-portal"></a>Ustawianie trybu opróżniania przy użyciu Azure Portal

Aby włączyć tryb opróżniania w Azure Portal:

1. Otwórz Azure Portal i przejdź do puli hostów, którą chcesz odizolować.

2. W menu nawigacji wybierz pozycję **Hosty sesji**.

3. Następnie wybierz hosty, dla których chcesz włączyć tryb opróżniania, a następnie wybierz pozycję **Włącz tryb opróżniania.**

4. Aby wyłączyć tryb opróżniania, wybierz pule hostów, które mają włączony tryb opróżniania, a następnie wybierz pozycję **Wyłącz tryb opróżniania.**

## <a name="set-drain-mode-using-powershell"></a>Ustawianie trybu opróżniania przy użyciu programu PowerShell

Możesz ustawić tryb opróżniania w programie PowerShell za pomocą parametru *AllowNewSessions,* który jest częścią polecenia [Update-AzWvdSessionhost.](/powershell/module/az.desktopvirtualization/update-azwvdsessionhost?view=azps-5.8.0&preserve-view=true)

Uruchom to polecenie cmdlet, aby włączyć tryb opróżniania:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$False
```

Uruchom to polecenie cmdlet, aby wyłączyć tryb opróżniania:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$True
```

>[!IMPORTANT]
>To polecenie należy uruchomić dla każdego hosta sesji, do których stosujesz ustawienie.

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się więcej na temat Azure Portal dla Windows Virtual Desktop, zapoznaj się z [naszymi samouczkami.](create-host-pools-azure-marketplace.md) Jeśli znasz już podstawy, zapoznaj się z innymi funkcjami, których można używać z interfejsem Azure Portal, takimi jak dołączanie aplikacji [MSIX](app-attach-azure-portal.md) i Azure Advisor [.](azure-advisor.md)

Jeśli używasz metody programu PowerShell i chcesz zobaczyć, co jeszcze może zrobić moduł, zapoznaj się z tematem Konfigurowanie modułu [programu PowerShell](powershell-module.md) dla programu Windows Virtual Desktop i naszą odwołania [do programu PowerShell.](/powershell/module/az.desktopvirtualization/)