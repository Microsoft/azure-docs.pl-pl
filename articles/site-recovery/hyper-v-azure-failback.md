---
title: Powrót po awarii maszyn wirtualnych funkcji Hyper-V z platformy Azure za pomocą Azure Site Recovery
description: Jak zakończyć przywracanie maszyn wirtualnych funkcji Hyper-V do lokacji lokalnej z platformy Azure przy użyciu Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: ramamill
ms.openlocfilehash: a31a28728dd0521262bd0518cc49a385f4314302
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87416234"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Uruchamianie powrotu po awarii dla maszyn wirtualnych funkcji Hyper-V

W tym artykule opisano sposób powrotu po awarii maszyn wirtualnych platformy Azure, które zostały utworzone po przejściu do trybu failover maszyn wirtualnych funkcji Hyper-V z lokacji lokalnej do platformy Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md).

- W przypadku powrotu po awarii maszyn wirtualnych funkcji Hyper-V z platformy Azure zostanie uruchomione planowane przejście w tryb failover z platformy Azure do lokacji lokalnej. Jeśli kierunek przejścia w tryb failover jest z platformy Azure do lokalnego, jest traktowany jako powrót po awarii.
- Ponieważ platforma Azure jest środowiskiem o wysokiej dostępności, a maszyny wirtualne są zawsze dostępne, powrót po awarii z platformy Azure jest zaplanowanym działaniem. Możesz zaplanować niewielki przestój, aby umożliwić ponowne uruchamianie obciążeń w środowisku lokalnym. 
- Planowane powrót po awarii powoduje wyłączenie maszyn wirtualnych na platformie Azure i pobranie najnowszych zmian. Nie jest oczekiwana utrata danych.

## <a name="before-you-start"></a>Przed rozpoczęciem

1. [Zapoznaj się z typami powrotu po awarii](failover-failback-overview.md#hyper-v-reprotectionfailback) , których można użyć — odzyskiwanie oryginalnej lokalizacji i odzyskiwanie lokalizacji alternatywnej.
2. Upewnij się, że maszyny wirtualne platformy Azure używają konta magazynu i nie są dyskami zarządzanymi. Powrót po awarii maszyn wirtualnych funkcji Hyper-V replikowanych za pomocą usługi Managed disks nie jest obsługiwany.
3. Sprawdź, czy lokalny host funkcji Hyper-V (lub serwer programu System Center VMM, jeśli używasz programu z usługą Site Recovery) jest uruchomiony i połączony z platformą Azure. 
4. Upewnij się, że maszyny wirtualne zostały wykonane w trybie failover i zatwierdzania. Nie trzeba konfigurować żadnych określonych składników Site Recovery w przypadku powrotu po awarii maszyn wirtualnych funkcji Hyper-V z platformy Azure.
5. Czas wymagany do ukończenia synchronizacji danych i uruchomienia lokalnej maszyny wirtualnej będzie zależeć od wielu czynników. Aby przyspieszyć pobieranie danych, można skonfigurować agenta Recovery Services firmy Microsoft w celu użycia większej liczby wątków do zrównoleglanie pobierania. [Dowiedz się więcej](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>Powrót po awarii do oryginalnej lokalizacji

Aby zakończyć przywracanie maszyn wirtualnych funkcji Hyper-V na platformie Azure do oryginalnej lokalnej maszyny wirtualnej, uruchom planowane przejście w tryb failover z platformy Azure do lokacji lokalnej w następujący sposób:

1. W magazynie > **zreplikowane elementy** wybierz maszynę wirtualną. Kliknij prawym przyciskiem myszy maszynę wirtualną > **planowanej pracy w trybie failover**. Jeśli plan odzyskiwania kończy się niepowodzeniem, wybierz nazwę planu i kliknij pozycję Planowana praca w trybie **failover**  >  .
2. W obszarze **Potwierdź planowane przejście w tryb failover** wybierz lokalizację źródłową i docelową. Zanotuj kierunek trybu failover. Jeśli przełączenie w tryb failover z lokacji głównej działa zgodnie z oczekiwaniami i wszystkie maszyny wirtualne znajdują się w lokalizacji pomocniczej, jest to tylko informacje.
3. W obszarze **Synchronizacja danych** wybierz opcję:
    - **Synchronizuj dane przed przełączeniem w tryb failover (zsynchronizuj tylko zmiany różnicowe)**— ta opcja minimalizuje przestoje maszyn wirtualnych w trakcie synchronizacji bez jej zamykania.
        - **Faza 1**: tworzy MIGAWKĘ maszyny wirtualnej platformy Azure i kopiuje ją do lokalnego hosta funkcji Hyper-V. Maszyna kontynuuje działanie na platformie Azure.
        - **Faza 2**: zamyka maszynę wirtualną platformy Azure, aby nie pojawiły się żadne nowe zmiany. Końcowy zestaw zmian różnicowych jest transferowany na serwer lokalny, a lokalna maszyna wirtualna jest uruchamiana.
    - **Synchronizuj dane tylko podczas pracy w trybie failover (pełne pobieranie)**— ta opcja jest szybsza, ponieważ zakłada się, że większość dysku uległa zmianie i nie chcesz spędzać czasu na obliczaniu sum kontrolnych. Ta opcja nie wykonuje żadnych obliczeń sum kontrolnych.
        - Pobiera dysk. 
        - Zalecamy użycie tej opcji, jeśli używasz platformy Azure przez pewien czas (co miesiąc lub więcej) lub jeśli lokalna maszyna wirtualna jest usuwana.

4. Tylko w przypadku programu VMM, jeśli szyfrowanie danych jest włączone dla chmury, w **kluczu szyfrowania** wybierz certyfikat, który został wystawiony po włączeniu szyfrowania danych podczas instalacji dostawcy na serwerze programu VMM.
5. Zainicjuj tryb failover. Na karcie **Zadania** można śledzić postęp trybu failover.
6. W przypadku wybrania opcji synchronizowania danych przed przełączeniem w tryb failover po zakończeniu początkowej synchronizacji danych i przygotowaniu do zamknięcia maszyn wirtualnych na platformie Azure kliknij pozycję **zadania** > nazwa zadania > **zakończyć pracę w trybie failover**. Spowoduje to wykonanie następujących czynności:
    - Zamyka maszynę Azure.
    - Przenosi najnowsze zmiany do lokalnej maszyny wirtualnej.
    - Uruchamia lokalną maszynę wirtualną.
7. Możesz teraz zalogować się do lokalnej maszyny wirtualnej, aby sprawdzić, czy jest ona dostępna zgodnie z oczekiwaniami.
8. Maszyna wirtualna jest w stanie oczekiwania na zatwierdzenie. Kliknij przycisk **Zatwierdź** , aby zatwierdzić przejście w tryb failover.
9. Aby ukończyć powrót po awarii, kliknij pozycję **replikacja odwrotna** , aby ponownie rozpocząć replikację lokalnej maszyny wirtualnej na platformę Azure.



## <a name="fail-back-to-an-alternate-location"></a>Powrót po awarii do lokalizacji alternatywnej 

Powrót po awarii do alternatywnej lokalizacji w następujący sposób:

1. Jeśli konfigurujesz nowy sprzęt, zainstaluj [obsługiwaną wersję systemu Windows](hyper-v-azure-support-matrix.md#replicated-vms)i rolę funkcji Hyper-V na komputerze.
2. Utwórz przełącznik sieci wirtualnej o takiej samej nazwie, jak na oryginalnym serwerze.
3. W   >  **grupie Ochrona** chronionych elementów  >  \<ProtectionGroupName>  ->  \<VirtualMachineName> Wybierz maszynę wirtualną, której chcesz użyć do powrotu po awarii, a następnie wybierz pozycję **Planowana praca w trybie failover**.
4. W obszarze **Potwierdź planowane przejście w tryb failover**, wybierz opcję **Utwórz lokalną maszynę wirtualną, jeśli nie istnieje**.
5. W polu **Nazwa hosta** wybierz nowy serwer hosta funkcji Hyper-V, na którym chcesz umieścić maszynę wirtualną.
6. W obszarze **Synchronizacja danych** zalecamy wybranie opcji synchronizowania danych przed przełączeniem w tryb failover. Pozwala to zminimalizować czas przestoju w przypadku synchronizacji z maszynami wirtualnymi bez ich zamykania. Wykonuje następujące czynności:
    - **Faza 1**: tworzy MIGAWKĘ maszyny wirtualnej platformy Azure i kopiuje ją do lokalnego hosta funkcji Hyper-V. Maszyna kontynuuje działanie na platformie Azure.
    - **Faza 2**: zamyka maszynę wirtualną platformy Azure, aby nie pojawiły się żadne nowe zmiany. Końcowy zestaw zmian jest transferowany na serwer lokalny, a lokalna maszyna wirtualna jest uruchamiana.
    
7. Kliknij znacznik wyboru, aby rozpocząć pracę w trybie failover (powrót po awarii).
8. Po zakończeniu początkowej synchronizacji i przygotowaniu się do zamykania maszyny wirtualnej platformy Azure kliknij pozycję **zadania**  >  \<planned failover job>  >  **Zakończ pracę w trybie failover**. Spowoduje to zamknięcie maszyny platformy Azure, przeniesienie najnowszych zmian do lokalnej maszyny wirtualnej i jej uruchomienie.
9. Możesz zalogować się do lokalnej maszyny wirtualnej, aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami.
10. Kliknij przycisk **Zatwierdź** , aby zakończyć pracę w trybie failover. Zatwierdzenie powoduje usunięcie maszyny wirtualnej platformy Azure i jej dysków, a następnie przygotowanie lokalnej maszyny wirtualnej do ochrony.
10. Kliknij pozycję **replikacja odwrotna** , aby rozpocząć replikację lokalnej maszyny wirtualnej do platformy Azure. Tylko zmiany różnicowe wprowadzone od momentu wyłączenia maszyny wirtualnej na platformie Azure zostaną zreplikowane.

    > [!NOTE]
    > Jeśli anulujesz zadanie powrotu po awarii podczas synchronizacji danych, lokalna maszyna wirtualna stanie się uszkodzona. Wynika to z faktu, że synchronizacja danych kopiuje najnowsze dane z dysków maszyny wirtualnej platformy Azure do lokalnych dysków danych i do momentu zakończenia synchronizacji dane dysku mogą nie być w stanie spójnym. Jeśli lokalna maszyna wirtualna jest uruchamiana po anulowaniu synchronizacji danych, uruchomienie jej może być niemożliwe. W takim przypadku należy ponownie uruchomić tryb failover w celu ukończenia synchronizacji danych.


## <a name="next-steps"></a>Następne kroki
Gdy lokalna maszyna wirtualna jest replikowana na platformę Azure, w razie potrzeby można [uruchomić kolejną pracę w trybie failover](site-recovery-failover.md) na platformie Azure.
