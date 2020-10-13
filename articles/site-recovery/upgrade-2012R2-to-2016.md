---
title: Uaktualnij system Windows Server/System Center VMM 2012 R2 do systemu Windows Server 2016 — Azure Site Recovery
description: Dowiedz się, jak uaktualnić system Windows Server 2012 R2 hosts & SCVMM 2012 R2 skonfigurowany przy użyciu Azure Site Recovery, do systemu Windows Server 2016 & SCVMM 2016.
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: sharrai
ms.openlocfilehash: b9869ae7dfbf5afd6b8d3b870a2ad4e56fd54c1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91250073"
---
# <a name="upgrade-windows-server-serversystem-center-2012-r2-vmm-to-windows-servervmm-2016"></a>Uaktualnij system Windows Server Server/System Center 2012 R2 VMM do systemu Windows Server/VMM 2016 

W tym artykule pokazano, jak uaktualnić system Windows Server 2012 R2 hosts & SCVMM 2012 R2 skonfigurowany z Azure Site Recovery, do systemu Windows Server 2016 & SCVMM 2016

Site Recovery przyczynia się do strategii zachowania ciągłości działania i odzyskiwania po awarii (BCDR). Usługa zapewnia, że obciążenia maszyny wirtualnej pozostają dostępne po oczekiwaniu i nieoczekiwanym wystąpieniu awarii.

> [!IMPORTANT]
> Po uaktualnieniu hostów systemu Windows Server 2012 R2, które zostały już skonfigurowane do replikacji za pomocą Azure Site Recovery, należy wykonać kroki opisane w tym dokumencie. Każda alternatywna ścieżka wybrana do uaktualnienia może spowodować nieobsługiwane Stany i może spowodować przerwanie replikacji lub możliwość przeprowadzenia przejścia w tryb failover.


W tym artykule dowiesz się, jak uaktualnić następujące konfiguracje w danym środowisku:

> [!div class="checklist"]
> * **Hosty z systemem Windows Server 2012 R2, które nie są zarządzane przez program SCVMM** 
> * **Hosty z systemem Windows Server 2012 R2, które są zarządzane przez autonomiczny serwer SCVMM 2012 R2** 
> * **Hosty z systemem Windows Server 2012 R2, które są zarządzane przez program SCVMM 2012 R2 programu o wysokiej dostępności**


## <a name="prerequisites--factors-to-consider"></a>Wymagania wstępne & czynniki do uwzględnienia

Przed uaktualnieniem programu należy zwrócić uwagę na następujące kwestie:

- Jeśli masz hosty z systemem Windows Server 2012 R2, które nie są zarządzane przez program SCVMM i są instalacją środowiska autonomicznego, w przypadku próby przeprowadzenia uaktualnienia wystąpi przerwa replikacja.
- Jeśli wybrano opcję "*nie przechowuj żadnych kluczy w Active Directory w obszarze rozproszone zarządzanie kluczami*" podczas instalowania programu SCVMM 2012 R2 w pierwszej kolejności, uaktualnienia nie zostaną ukończone pomyślnie.

- Jeśli używasz programu System Center 2012 R2 VMM, 

    - Sprawdź informacje o bazie danych w programie VMM: ustawienia **konsoli programu VMM**  ->  **settings**  ->  **Ogólne**  ->  **połączenie bazy danych**
    - Sprawdź konta usług używane na potrzeby usługi Agent System Center Virtual Machine Manager
    - Upewnij się, że masz kopię zapasową bazy danych programu VMM.
    - Zanotuj nazwę bazy danych serwerów programu SCVMM. Można to zrobić, przechodząc do ustawień **konsoli programu VMM**  ->  **Settings**  ->  **Ogólne**  ->  **połączenie bazy danych**
    - Zanotuj identyfikator programu VMM zarówno z 2012R2 podstawowego, jak i serwera programu VMM odzyskiwania. Identyfikator programu VMM można znaleźć w rejestrze "HKLM: \ SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup".
    - Upewnij się, że nowe SCVMM dodawane do klastra mają takie same nazwy jak wcześniej. 

- Jeśli replikujesz między dwiema lokacjami zarządzanymi przez SCVMM po obu stronach, przed uaktualnieniem strony głównej upewnij się, że najpierw uaktualniasz stronę odzyskiwania.
  > [!WARNING]
  > Podczas uaktualniania programu SCVMM 2012 R2 w obszarze rozproszone zarządzanie kluczami wybierz opcję **przechowywania kluczy szyfrowania w Active Directory**. Należy uważnie wybrać ustawienia dla konta usługi i rozproszonego zarządzania kluczami. Na podstawie wyboru zaszyfrowane dane, takie jak hasła w szablonach, mogą być niedostępne po uaktualnieniu i mogą mieć wpływ na replikację przy użyciu Azure Site Recovery

> [!IMPORTANT]
> Zapoznaj się ze szczegółową dokumentacją programu SCVMM dotyczącą [wymagań wstępnych](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Hosty z systemem Windows Server 2012 R2, które nie są zarządzane przez program SCVMM 
Lista kroków wymienionych poniżej ma zastosowanie do konfiguracji użytkownika z [hostów funkcji Hyper-V na platformie Azure](./hyper-v-azure-architecture.md) wykonywanych przez wykonanie kroków opisanych w tym [samouczku](./hyper-v-prepare-on-premises-tutorial.md)

> [!WARNING]
> Jak wspomniano w wymaganiach wstępnych, te czynności dotyczą tylko scenariusza środowiska klastra, a nie w konfiguracji autonomicznego hosta funkcji Hyper-V.

1. Postępuj zgodnie z instrukcjami, aby przeprowadzić [uaktualnienie stopniowe klastra.](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) Aby wykonać proces stopniowego uaktualniania klastra.
2. W przypadku każdego nowego hosta z systemem Windows Server 2016, który został wprowadzony w klastrze, usuń odwołanie do hosta z systemem Windows Server 2012 R2 z Azure Site Recovery, wykonując następujące czynności [tutaj]. Powinien to być host, który został wybrany do opróżnienia & wykluczenia z klastra.
3. Po wykonaniu polecenia *Update-VMVersion* dla wszystkich maszyn wirtualnych uaktualnienia zostały ukończone. 
4. Wykonaj kroki opisane w [tym miejscu](./hyper-v-azure-tutorial.md#set-up-the-source-environment) , aby zarejestrować nowy Host systemu Windows Server 2016 do Azure Site Recovery. Należy pamiętać, że Lokacja funkcji Hyper-V jest już aktywna i wystarczy zarejestrować nowego hosta w klastrze. 
5.  Przejdź do Azure Portal i sprawdź stan zreplikowanej kondycji w Recovery Services

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Uaktualnij hosty systemu Windows Server 2012 R2 zarządzane przez autonomiczny serwer SCVMM 2012 R2
Przed uaktualnieniem hostów z systemem Windows Server 2012 R2 należy uaktualnić program SCVMM 2012 R2 do programu SCVMM 2016. Wykonaj poniższe kroki:-

**Uaktualnianie autonomicznego SCVMM 2012 R2 do SCVMM 2016**

1.  Odinstaluj dostawcę usługi ASR, przechodząc do panelu sterowania — > programy — > programy i funkcje — >Microsoft Azure Site Recovery, a następnie klikając pozycję Odinstaluj
2. [Zachowaj bazę danych SCVMM i Uaktualnij system operacyjny](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. W obszarze **Dodaj/Usuń programy**wybierz pozycję Odinstaluj **programu VMM**  >  **Uninstall**. b. Wybierz pozycję **Usuń funkcje**, a następnie wybierz kolejno opcje**serwer zarządzania i konsola programu VMM**. c. W **opcji bazy danych**wybierz opcję **Zachowaj bazę danych**. d. Przejrzyj podsumowanie, a następnie kliknij przycisk **Odinstaluj**.

4. [Instalowanie programu VMM 2016](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Uruchom SCVMM i sprawdź stan każdego hosta na karcie sieci **szkieletowej** . Kliknij przycisk **Odśwież** , aby uzyskać najnowszy stan. Powinna zostać wyświetlona wartość status jako "wymaga uwagi". 
17. Zainstaluj najnowszego [dostawcę Site Recovery Microsoft Azure](https://aka.ms/downloaddra) w programie SCVMM.
16. Zainstaluj najnowszą wersję [agenta usługi odzyskiwania Microsoft Azure (MARS)](https://aka.ms/latestmarsagent) na każdym hoście klastra. Odśwież, aby upewnić się, że program SCVMM pomyślnie bada hosty.

**Uaktualnij hosty systemu Windows Server 2012 R2 do systemu Windows Server 2016**

1. Wykonaj kroki opisane [tutaj](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) , aby wykonać proces stopniowego uaktualniania klastra. 
2. Po dodaniu nowego hosta do klastra Odśwież hosta z konsoli SCVMM, aby zainstalować agenta programu VMM na tym zaktualizowanym hoście.
3. Wykonaj polecenie *Update-VMVersion* , aby zaktualizować wersje maszyny wirtualnej maszyn wirtualnych. 
4.  Przejdź do Azure Portal i sprawdź stan zreplikowanej kondycji maszyn wirtualnych znajdujących się w magazynie Recovery Services. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Uaktualnienie hostów systemu Windows Server 2012 R2 jest zarządzane przez program SCVMM 2012 R2 o wysokiej dostępności
Przed uaktualnieniem hostów z systemem Windows Server 2012 R2 należy uaktualnić program SCVMM 2012 R2 do programu SCVMM 2016. Podczas uaktualniania serwerów SCVMM 2012 R2 skonfigurowanych przy użyciu trybu Azure Site Recovery-mieszanego bez dodatkowych serwerów programu VMM & trybie mieszanym z dodatkowymi serwerami programu VMM obsługiwane są następujące tryby uaktualniania.

**Uaktualnij program SCVMM 2012 R2 do programu SCVMM 2016**

1.  Odinstaluj dostawcę usługi ASR, przechodząc do panelu sterowania — > programy — > programy i funkcje — >Microsoft Azure Site Recovery, a następnie klikając pozycję Odinstaluj
2. Wykonaj kroki opisane w [tym miejscu](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) na podstawie trybu uaktualniania, które chcesz wykonać.
3. Uruchom konsolę SCVMM i sprawdź stan każdego hosta na karcie sieci **szkieletowej** . Kliknij przycisk **Odśwież** , aby uzyskać najnowszy stan. Powinna zostać wyświetlona wartość status jako "wymaga uwagi".
4. Zainstaluj najnowszego [dostawcę Site Recovery Microsoft Azure](https://aka.ms/downloaddra) w programie SCVMM.
5. Zaktualizuj najnowszą wersję [agenta usługi odzyskiwania Microsoft Azure (MARS)](https://aka.ms/latestmarsagent) na każdym hoście klastra. Odśwież, aby upewnić się, że SC VMM może pomyślnie zbadać hosty.


**Uaktualnij hosty systemu Windows Server 2012 R2 do systemu Windows Server 2016**

1. Wykonaj kroki opisane [tutaj](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) , aby wykonać proces stopniowego uaktualniania klastra.
2. Po dodaniu nowego hosta do klastra Odśwież hosta z konsoli SCVMM, aby zainstalować agenta programu VMM na tym zaktualizowanym hoście.
3. Wykonaj polecenie *Update-VMVersion* , aby zaktualizować wersje maszyny wirtualnej maszyn wirtualnych. 
4.  Przejdź do Azure Portal i sprawdź stan zreplikowanej kondycji maszyn wirtualnych znajdujących się w magazynie Recovery Services. 

## <a name="next-steps"></a>Następne kroki
Po przeprowadzeniu uaktualnienia hostów można wykonać [test pracy w trybie failover](tutorial-dr-drill-azure.md) w celu przetestowania kondycji replikacji i stanu odzyskiwania po awarii.

