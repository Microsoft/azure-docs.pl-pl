---
title: Pulpit wirtualny systemu Windows — uruchamianie maszyn wirtualnych — często zadawane pytania — Azure
description: Często zadawane pytania i najlepsze rozwiązania dotyczące korzystania z funkcji Uruchom maszynę wirtualną w programie Connect.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0a35d0541358af2f5fac5e4c7486a1be93797922
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445588"
---
# <a name="start-vm-on-connect-faq-preview"></a>Uruchom maszynę wirtualną przy nawiązywaniu połączenia (wersja zapoznawcza)

> [!IMPORTANT]
> Funkcja Uruchom maszynę wirtualną w programie Connect jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule opisano często zadawane pytania dotyczące funkcji uruchamiania maszyn wirtualnych (VM) w programie Connect (wersja zapoznawcza) dla pul hostów usług pulpitu wirtualnego systemu Windows.

## <a name="are-vms-automatically-deallocated-when-a-user-stops-using-them"></a>Czy maszyny wirtualne są automatycznie cofane, gdy użytkownik zatrzyma ich używanie?

Nie. Należy skonfigurować dodatkowe zasady do podpisywania użytkownikom z ich sesji i uruchamiania skryptów usługi Azure Automation w celu cofnięcia alokacji maszyn wirtualnych.

Aby skonfigurować zasady cofania alokacji:

1. Połącz się zdalnie z maszyną wirtualną, dla której chcesz ustawić zasady.

2. Otwórz **Edytor zasady grupy**, następnie przejdź do pozycji   >  **Konfiguracja komputera** zasady komputera  >  **Szablony administracyjne**  >  **składniki systemu Windows**  >  **usługi pulpitu zdalnego**  >  **pulpit zdalny**  >  **limity czasu sesji** hosta sesji.

3. Znajdź zasady informujące **o ustawieniu limitu czasu dla rozłączonych sesji**, a następnie zmień jego wartość na **włączone**.

4. Po włączeniu zasad wybierz pozycję **Zakończ sesję rozłączoną**.

>[!NOTE]
>Upewnij się, że ustawiono limit czasu dla zasad "Zakończ sesję rozłączoną" na wartość większą niż pięć minut. Niski limit czasu może spowodować zakończenie sesji użytkowników, jeśli sieć straci połączenie zbyt długo, co spowodowało utratę pracy.

Wylogowanie użytkowników nie spowoduje cofnięcia przydziału maszyn wirtualnych. Aby dowiedzieć się, jak cofnąć alokację maszyn wirtualnych, zobacz [Uruchamianie lub zatrzymywanie maszyn wirtualnych poza godzinami pracy](../automation/automation-solution-vm-management.md).

## <a name="can-users-turn-off-the-vm-from-their-clients"></a>Czy użytkownicy mogą wyłączyć maszynę wirtualną od swoich klientów?

Tak. Użytkownicy mogą zamknąć maszynę wirtualną, korzystając z menu Start w swojej sesji, podobnie jak w przypadku maszyny fizycznej. Jednak wyłączenie maszyny wirtualnej nie spowoduje cofnięcia przydziału maszyny wirtualnej. Aby dowiedzieć się, jak cofnąć alokację maszyn wirtualnych, zobacz [Uruchamianie lub zatrzymywanie maszyn wirtualnych poza godzinami pracy](../automation/automation-solution-vm-management.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak skonfigurować uruchamianie maszyny wirtualnej w programie Connect, zobacz [Uruchamianie maszyny wirtualnej w programie Connect (wersja zapoznawcza)](start-virtual-machine-connect.md).

Jeśli masz więcej ogólnych pytań dotyczących pulpitu wirtualnego systemu Windows, zapoznaj się z naszymi ogólnymi [często zadawanymi pytaniami](faq.md).
