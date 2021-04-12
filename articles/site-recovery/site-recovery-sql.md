---
title: Skonfiguruj odzyskiwanie po awarii dla SQL Server z Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii dla SQL Server przy użyciu SQL Server i Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: ab2eb8a43fc75eea61a03bc25b2b6afc850d30aa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644413"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Skonfiguruj odzyskiwanie po awarii dla SQL Server

W tym artykule opisano, jak pomóc chronić SQL Server zaplecza aplikacji. Można to zrobić przy użyciu kombinacji technologii SQL Server i [Azure Site Recovery](site-recovery-overview.md)odzyskiwania po awarii (BCDR).

Przed rozpoczęciem upewnij się, że rozumiesz SQL Server możliwości odzyskiwania po awarii. Te funkcje obejmują:

* Klaster trybu failover
* Zawsze włączone grupy dostępności
* Dublowanie bazy danych
* Wysyłanie dziennika
* Aktywna replikacja geograficzna
* Grupy automatycznego trybu failover

## <a name="combining-bcdr-technologies-with-site-recovery"></a>Łączenie technologii BCDR z Site Recovery

Wybór technologii BCDR do odzyskania wystąpień SQL Server powinien opierać się na zamierzeniu czasu odzyskiwania (RTO) i celu punktu odzyskiwania (RPO), zgodnie z opisem w poniższej tabeli. Połącz Site Recovery z pracą w trybie failover wybranej technologii, aby zorganizować odzyskiwanie całej aplikacji.

Typ wdrożenia | Technologia BCDR | Oczekiwano RTO dla SQL Server | Oczekiwany cel punktu odzyskiwania dla SQL Server |
--- | --- | --- | ---
SQL Server na maszynę wirtualną usługi Azure Infrastructure as Service (IaaS) lub w środowisku lokalnym.| [Zawsze włączona grupa dostępności](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) | Czas potrzebny na udostępnienie repliki pomocniczej jako podstawowej. | Ponieważ replikacja do repliki pomocniczej jest asynchroniczna, istnieje pewna utrata danych.
SQL Server na maszynie wirtualnej IaaS platformy Azure lub w środowisku lokalnym.| [Klaster trybu failover (zawsze na FCI)](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | Czas przejścia w tryb failover między węzłami. | Ponieważ usługa Always On FCI używa magazynu udostępnionego, ten sam widok wystąpienia magazynu jest dostępny w trybie failover.
SQL Server na maszynie wirtualnej IaaS platformy Azure lub w środowisku lokalnym.| [Dublowanie bazy danych (tryb wysokiej wydajności)](/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Czas wymuszania usługi, która używa serwera dublowanego jako serwera rezerwy ciepłej. | Replikacja jest asynchroniczna. Duplikat bazy danych może być nieco opóźniony za główną bazą danych. Opóźnienie jest zwykle małe. Może to jednak być duże, jeśli system serwera głównego lub dublowanego jest w dużym obciążeniu.<br/><br/>Wysyłanie dziennika może być uzupełnieniem funkcji dublowania baz danych. Jest to bardziej korzystne rozwiązanie do asynchronicznej dublowania baz danych.
Platforma SQL jako usługa (PaaS) na platformie Azure.<br/><br/>Ten typ wdrożenia obejmuje pojedyncze bazy danych i pule elastyczne. | Aktywna replikacja geograficzna | 30 sekund po wyzwoleniu trybu failover.<br/><br/>Po aktywowaniu trybu failover dla jednej z pomocniczych baz danych wszystkie inne serwery podrzędne są automatycznie łączone z nowym serwerem podstawowym. | Cel punktu odzyskiwania przez pięć sekund.<br/><br/>Aktywna replikacja geograficzna używa technologii Always On w SQL Server. Asynchronicznie replikuje zatwierdzone transakcje w podstawowej bazie danych do pomocniczej bazy danych za pomocą izolacji migawki.<br/><br/>Dane pomocnicze mają gwarancję, że nigdy nie mają transakcji częściowych.
Program SQL as PaaS skonfigurowany z aktywną replikacją geograficzną na platformie Azure.<br/><br/>Ten typ wdrożenia obejmuje wystąpienia zarządzane, elastyczne pule i pojedyncze bazy danych. | Grupy automatycznego trybu failover | RTO o godzinie. | Cel punktu odzyskiwania przez pięć sekund.<br/><br/>Grupy autotrybu failover zapewniają semantykę grupy na aktywnej replikacji geograficznej. Jednak używany jest ten sam mechanizm replikacji asynchronicznej.
SQL Server na maszynie wirtualnej IaaS platformy Azure lub w środowisku lokalnym.| Replikacja przy użyciu Azure Site Recovery | RTO jest zwykle krótszy niż 15 minut. Aby dowiedzieć się więcej, przeczytaj umowę [SLA RTO zapewnianą przez Site Recovery](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). | Jedna godzina dla spójności aplikacji i pięć minut na potrzeby spójności awaryjnej. Jeśli szukasz mniejszego celu punktu odzyskiwania, użyj innych technologii BCDR.

> [!NOTE]
> Kilka ważnych zagadnień związanych z ochroną obciążeń SQL przy użyciu Site Recovery:
> * Site Recovery jest aplikacją niezależny od. Site Recovery może pomóc w ochronie dowolnej wersji SQL Server wdrożonej w obsługiwanym systemie operacyjnym. Aby dowiedzieć się więcej, zobacz [Macierz obsługi na potrzeby odzyskiwania](vmware-physical-azure-support-matrix.md#replicated-machines) replikowanych maszyn.
> * Możesz użyć Site Recovery do dowolnego wdrożenia na platformie Azure, funkcji Hyper-V, VMware lub infrastruktury fizycznej. Postępuj zgodnie ze wskazówkami znajdującymi się na końcu tego artykułu, [Aby uzyskać informacje na temat ochrony klastra SQL Server](#how-to-help-protect-a-sql-server-cluster) przy użyciu Site Recovery.
> * Upewnij się, że współczynnik zmian danych zaobserwowany na komputerze mieści się w [granicach Site Recovery](vmware-physical-azure-support-matrix.md#churn-limits). Częstotliwość zmian jest mierzona w bajtach zapisu na sekundę. W przypadku maszyn z systemem Windows można wyświetlić tę stawkę zmiany, wybierając kartę **wydajność** w Menedżerze zadań. Obserwuj szybkość zapisu dla każdego dysku.
> * Site Recovery obsługuje replikację wystąpień klastra trybu failover w Bezpośrednie miejsca do magazynowania. Aby dowiedzieć się więcej, zobacz [jak włączyć replikację bezpośrednie miejsca do magazynowania](azure-to-azure-how-to-enable-replication-s2d-vms.md).
> 
> Podczas migrowania obciążenia SQL na platformę Azure zaleca się zastosowanie [wytycznych dotyczących wydajności SQL Server w usłudze azure Virtual Machines](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md).

## <a name="disaster-recovery-of-an-application"></a>Odzyskiwanie po awarii aplikacji

Site Recovery organizuje test pracy w trybie failover i przełączenie w tryb failover całej aplikacji przy użyciu planów odzyskiwania.

Istnieją pewne wymagania wstępne, aby zapewnić, że plan odzyskiwania jest w pełni dostosowany zgodnie z potrzebami. Każde wdrożenie SQL Server zwykle wymaga wdrożenia Active Directory. Wymaga także łączności dla warstwy aplikacji.

### <a name="step-1-set-up-active-directory"></a>Krok 1. Konfigurowanie Active Directory

Skonfiguruj Active Directory w dodatkowej lokacji odzyskiwania, aby SQL Server działała prawidłowo.

* **Małe przedsiębiorstwa**: masz niewielką liczbę aplikacji i jeden kontroler domeny dla lokacji lokalnej. Jeśli chcesz przełączyć całą lokację do trybu failover, Użyj replikacji Site Recovery. Ta usługa replikuje kontroler domeny do pomocniczego centrum danych lub na platformie Azure.
* **Średnio do dużego przedsiębiorstwa**: może być konieczne skonfigurowanie dodatkowych kontrolerów domeny.
  - Jeśli masz dużą liczbę aplikacji, mają Las Active Directory i chcesz przełączać w tryb failover przez aplikacje lub obciążenia, skonfiguruj inny kontroler domeny w dodatkowym centrum danych lub na platformie Azure.
  -  Jeśli używasz zawsze włączonych grup dostępności do odzyskiwania do zdalnej lokacji, skonfiguruj inny kontroler domeny w lokacji dodatkowej lub na platformie Azure. Ten kontroler domeny jest używany dla przywróconego wystąpienia SQL Server.

W instrukcjach przedstawionych w tym artykule przyjęto założenie, że kontroler domeny jest dostępny w lokalizacji pomocniczej. Aby dowiedzieć się więcej, zobacz procedury [pomagające w ochronie Active Directory przy użyciu Site Recovery](site-recovery-active-directory.md).

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>Krok 2. Zapewnianie łączności z innymi warstwami

Po uruchomieniu warstwy bazy danych w docelowym regionie platformy Azure upewnij się, że masz połączenie z aplikacją i warstwami sieci Web. Aby sprawdzić poprawność łączności z testem pracy w trybie failover, wykonaj kroki opisane w temacie.

Aby zrozumieć, jak można zaprojektować aplikacje pod kątem zagadnień związanych z łącznością, zobacz następujące przykłady:

* [Projektowanie aplikacji na potrzeby odzyskiwania po awarii w chmurze](../azure-sql/database/designing-cloud-solutions-for-disaster-recovery.md)
* [Strategie odzyskiwania po awarii puli elastycznej](../azure-sql/database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>Krok 3. współdziałanie z zawsze włączonym, aktywną replikacją geograficzną i grupami autotrybu failover

Technologie BCDR zawsze włączone, aktywnej replikacji geograficznej i grup autotrybu failover mają pomocnicze repliki SQL Server uruchomione w docelowym regionie platformy Azure. Pierwszym krokiem w trybie failover aplikacji jest określenie tej repliki jako podstawowej. W tym kroku przyjęto założenie, że masz już kontroler domeny w pomocniczym. Ten krok może nie być konieczny w przypadku wybrania opcji przełączenia w tryb failover. Przełączenie w tryb failover warstw sieci Web i aplikacji jest możliwe dopiero po zakończeniu przełączenia na bazę danych.

> [!NOTE]
> Jeśli masz pomoc w ochronie maszyn SQL za pomocą Site Recovery, wystarczy utworzyć grupę odzyskiwania tych maszyn i dodać do trybu failover w planie odzyskiwania.

[Utwórz plan odzyskiwania](site-recovery-create-recovery-plans.md) z maszynami wirtualnymi aplikacji i warstwami sieci Web. Poniższe kroki pokazują, jak dodać tryb failover warstwy bazy danych:

1. Zaimportuj skrypty do trybu failover grupy dostępności SQL w ramach [maszyny wirtualnej Menedżer zasobów](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) i [klasycznej maszyny wirtualnej](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1). Zaimportuj skrypty do konta Azure Automation.

    [![Obraz logo "Deploy to Azure"](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Dodaj skrypt ASR-SQL-FailoverAG jako wstępną akcję pierwszej grupy planu odzyskiwania.

1. Postępuj zgodnie z instrukcjami w skrypcie, aby utworzyć zmienną automatyzacji. Ta zmienna zawiera nazwę grup dostępności.

### <a name="step-4-conduct-a-test-failover"></a>Krok 4. przeprowadzenie testu pracy w trybie failover

Niektóre technologie BCDR, takie jak SQL Always On, nie obsługują natywnie testu pracy w trybie failover. Zaleca się następujące podejście *tylko w przypadku korzystania z takich technologii*.

1. Skonfiguruj [Azure Backup](../backup/backup-azure-vms-first-look-arm.md) na maszynie wirtualnej, która hostuje replikę grupy dostępności na platformie Azure.

1. Przed wyzwoleniem testowego przejścia w tryb failover planu odzyskiwania Odzyskaj maszynę wirtualną z kopii zapasowej wykonanej w poprzednim kroku.

    ![Zrzut ekranu przedstawiający okno do przywracania konfiguracji z Azure Backup](./media/site-recovery-sql/restore-from-backup.png)

1. [Wymuś kworum](/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) w maszynie wirtualnej, która została przywrócona z kopii zapasowej.

1. Zaktualizuj adres IP odbiornika jako adres dostępny w sieci testowej pracy w trybie failover.

    ![Zrzut ekranu okna dialogowego Właściwości okna reguły i adresu IP](./media/site-recovery-sql/update-listener-ip.png)

1. Przełączenie odbiornika w tryb online.

    ![Zrzut ekranu okna z etykietą Content_AG pokazujący nazwy i Stany serwera](./media/site-recovery-sql/bring-listener-online.png)

1. Upewnij się, że moduł równoważenia obciążenia w sieci trybu failover ma jeden adres IP z puli adresów IP frontonu odpowiadający każdemu odbiornikowi grupy dostępności oraz z maszyną wirtualną SQL Server w puli zaplecza.

     ![Zrzut ekranu przedstawiający okno "SQL-AlwaysOn-LB-fronton IP Pool](./media/site-recovery-sql/create-load-balancer1.png)

    ![Zrzut ekranu przedstawiający okno "SQL-AlwaysOn-LB-wewnętrzna Pula adresów IP](./media/site-recovery-sql/create-load-balancer2.png)

1. W nowszych grupach odzyskiwania Dodaj tryb failover warstwy aplikacji, a następnie warstwę sieci Web dla tego planu odzyskiwania.

1. Wykonaj test pracy w trybie failover planu odzyskiwania w celu przetestowania kompleksowej pracy w trybie failover aplikacji.

## <a name="steps-to-do-a-failover"></a>Kroki prowadzące do przejścia w tryb failover

Po dodaniu skryptu w kroku 3 i sprawdzeniu go w kroku 4 można wykonać tryb failover planu odzyskiwania utworzonego w kroku 3.

Kroki w trybie failover dla warstw aplikacji i sieci Web powinny być takie same w testowej pracy w trybie failover i w trybie failover.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>Jak pomóc chronić klaster SQL Server

W przypadku klastra z systemem SQL Server Standard Edition lub SQL Server 2008 R2 zalecamy korzystanie z replikacji Site Recovery, aby pomóc w ochronie SQL Server.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Platforma Azure z platformą Azure i platforma lokalna na platformie Azure

Site Recovery nie zapewnia obsługi klastra gościa podczas replikowania do regionu platformy Azure. Wersja SQL Server Standard nie zapewnia również niedrogiego rozwiązania do odzyskiwania po awarii. W tym scenariuszu zalecamy ochronę klastra SQL Server do autonomicznego wystąpienia SQL Server w lokalizacji podstawowej i odzyskiwanie go w ramach pomocniczej.

1. Skonfiguruj dodatkowe autonomiczne wystąpienie SQL Server w podstawowym regionie platformy Azure lub w lokacji lokalnej.

1. Skonfiguruj wystąpienie jako dublowanie baz danych, które mają być chronione. Skonfiguruj dublowanie w trybie wysokiego poziomu zabezpieczeń.

1. Skonfiguruj Site Recovery w lokacji głównej dla maszyn wirtualnych [platformy Azure](azure-to-azure-tutorial-enable-replication.md), [funkcji Hyper-V](./hyper-v-azure-tutorial.md)lub oprogramowania [VMware i serwerów fizycznych](./vmware-azure-tutorial.md).

1. Użyj replikacji Site Recovery, aby replikować nowe wystąpienie SQL Server do lokacji dodatkowej. Ponieważ jest to kopia duplikatu wysokiego poziomu zabezpieczeń, będzie synchronizowana z klastrem podstawowym, ale replikowana przy użyciu Site Recovery replikacji.

   ![Obraz standardowego klastra, który pokazuje relację i przepływ między lokacją główną, Site Recovery i platformą Azure](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Uwagi dotyczące powrotu po awarii

W przypadku klastrów SQL Server Standard powrót po awarii po nieplanowanym przejściu w tryb failover wymaga SQL Server kopii zapasowej i przywracania. Ta operacja jest wykonywana z wystąpienia dublowania do oryginalnego klastra z ponownym utworzeniem dublowania.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>Jak SQL Server uzyskać licencji w przypadku użycia z Site Recovery?

Site Recovery replikacja dla SQL Server jest objęta korzyścią odzyskiwania po awarii programu Software Assurance. To pokrycie ma zastosowanie do wszystkich scenariuszy Site Recoveryych: lokalne odzyskiwanie po awarii platformy Azure i międzyregionowe odzyskiwanie po awarii usługi Azure IaaS. Aby uzyskać więcej informacji, zobacz [cennik Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) .

### <a name="will-site-recovery-support-my-sql-server-version"></a>Czy Site Recovery będzie obsługiwać moją SQL Server wersję?

Site Recovery jest aplikacją niezależny od. Site Recovery może pomóc w ochronie dowolnej wersji SQL Server wdrożonej w obsługiwanym systemie operacyjnym. Aby uzyskać więcej informacji, zobacz [Macierz obsługi na potrzeby odzyskiwania](vmware-physical-azure-support-matrix.md#replicated-machines) replikowanych maszyn.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [architekturze Site Recovery](./azure-to-azure-architecture.md).
* Aby uzyskać SQL Server na platformie Azure, Dowiedz się więcej o [rozwiązaniach o wysokiej dostępności](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions) na potrzeby odzyskiwania w regionie pomocniczym platformy Azure.
* Aby uzyskać SQL Database, Dowiedz się więcej o opcjach zapewniania [ciągłości działalności biznesowej](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) i [wysokiej dostępności](../azure-sql/database/high-availability-sla.md) w ramach odzyskiwania w pomocniczym regionie platformy Azure.
* W przypadku SQL Server maszyn w środowisku lokalnym Dowiedz się więcej o [opcjach wysokiej dostępności](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions) na potrzeby odzyskiwania na platformie Azure Virtual Machines.