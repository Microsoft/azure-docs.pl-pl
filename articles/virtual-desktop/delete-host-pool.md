---
title: Usuwanie puli hostów pulpitów wirtualnych systemu Windows — Azure
description: Jak usunąć pulę hostów w programie Virtual Desktop systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 3693a82a0d9c9544ed6a14d4596d544725125bd3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447050"
---
# <a name="delete-a-host-pool"></a>Usuwanie puli hostów

Wszystkie pule hostów utworzone na pulpicie wirtualnym systemu Windows są dołączone do hostów sesji i grup aplikacji. Aby usunąć pulę hostów, należy usunąć skojarzone z nią grupy aplikacji i hosty sesji. Usuwanie grupy aplikacji jest dość proste, ale Usuwanie hosta sesji jest bardziej skomplikowane. Po usunięciu hosta sesji należy upewnić się, że nie ma żadnych aktywnych sesji użytkownika. Wszystkie sesje użytkowników na hoście sesji należy wylogować, aby uniemożliwić użytkownikom utratę danych.

## <a name="delete-a-host-pool-with-powershell"></a>Usuwanie puli hostów przy użyciu programu PowerShell

Aby usunąć pulę hostów przy użyciu programu PowerShell, należy najpierw usunąć wszystkie grupy aplikacji z puli hostów. Aby usunąć wszystkie grupy aplikacji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Remove-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
```

Następnie Uruchom to polecenie cmdlet, aby usunąć pulę hostów:

```powershell
Remove-AzWvdHostPool -Name <hostpoolname> -ResourceGroupName <resourcegroupname> -Force:$true
```

To polecenie cmdlet usuwa wszystkie istniejące sesje użytkownika na hoście sesji puli hostów. Spowoduje to również Wyrejestrowanie hosta sesji z puli hostów. Wszystkie powiązane maszyny wirtualne będą nadal istnieć w ramach subskrypcji.

## <a name="delete-a-host-pool-with-the-azure-portal"></a>Usuwanie puli hostów z Azure Portal

Aby usunąć pulę hostów w Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wyszukaj i wybierz pozycję **pulpit wirtualny systemu Windows**.

3. Wybierz pozycję **Pule hostów** w menu po lewej stronie, a następnie wybierz nazwę puli hostów, którą chcesz usunąć.

4. W menu po lewej stronie strony wybierz pozycję **grupy aplikacji**.

5. Wybierz wszystkie grupy aplikacji w puli hostów, które chcesz usunąć, a następnie wybierz pozycję **Usuń**.

6. Po usunięciu grup aplikacji przejdź do menu po lewej stronie i wybierz pozycję **Przegląd**.

7. Wybierz pozycję **Usuń**.

8. Jeśli istnieją hosty sesji w puli hostów, które są usuwane, zobaczysz komunikat z prośbą o zgodę na kontynuowanie. Wybierz pozycję **Tak**.

9. Azure Portal usunie teraz wszystkie hosty sesji i usunie pulę hostów. Maszyny wirtualne powiązane z hostem sesji nie zostaną usunięte i pozostaną w Twojej subskrypcji.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak utworzyć pulę hostów, zapoznaj się z następującymi artykułami:

- [Tworzenie puli hostów za pomocą witryny Azure Portal](create-host-pools-azure-marketplace.md)
- [Tworzenie puli hostów przy użyciu programu PowerShell](create-host-pools-powershell.md)

Aby dowiedzieć się, jak skonfigurować ustawienia puli hostów, zapoznaj się z następującymi artykułami:

- [Dostosowywanie Remote Desktop Protocol właściwości dla puli hostów](customize-rdp-properties.md)
- [Konfigurowanie metody równoważenia obciążenia usługi Windows Virtual Desktop](configure-host-pool-load-balancing.md)
- [Konfigurowanie typu przypisania puli hostów pulpitu osobistego](configure-host-pool-personal-desktop-assignment-type.md)