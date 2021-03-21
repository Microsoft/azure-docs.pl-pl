---
title: Odzyskiwanie po awarii dla wielowarstwowej aplikacji SharePoint przy użyciu Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii dla wielowarstwowej aplikacji SharePoint przy użyciu możliwości Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: sutalasi
ms.openlocfilehash: 08e971e52f994ec5fa5663708fa9f173daf33d80
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013755"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Konfigurowanie odzyskiwania po awarii dla wielowarstwowej aplikacji SharePoint na potrzeby odzyskiwania po awarii przy użyciu Azure Site Recovery

W tym artykule szczegółowo opisano sposób ochrony aplikacji programu SharePoint przy użyciu  [Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Omówienie

Microsoft SharePoint to zaawansowana aplikacja, która może pomóc grupie lub działowi w organizowaniu, współpracy i udostępnianiu informacji. Program SharePoint może udostępniać portale intranetowe, zarządzanie dokumentami i plikami, współpracę, sieci społecznościowe, ekstranety, witryny sieci Web, wyszukiwanie w przedsiębiorstwie i analizę biznesową. Ma także integrację systemu, integrację procesów i możliwości automatyzacji przepływu pracy. Zazwyczaj organizacje traktują je jako wrażliwe na przestoje i utratę danych.

Obecnie program Microsoft SharePoint nie udostępnia wbudowanych możliwości odzyskiwania po awarii. Bez względu na typ i skalę awarii odzyskiwanie obejmuje użycie centrum danych w stanie wstrzymania, do którego można odzyskać farmę. Centra danych w stanie wstrzymania są wymagane dla scenariuszy, w których lokalne nadmiarowe systemy i kopie zapasowe nie mogą zostać odzyskane po awarii w podstawowym centrum danych.

Dobre rozwiązanie do odzyskiwania po awarii powinno umożliwić modelowanie planów odzyskiwania na podstawie złożonych architektur aplikacji, takich jak SharePoint. Powinna również mieć możliwość dodawania dostosowanych kroków do obsługi mapowań aplikacji między różnymi warstwami, a tym samym zapewniania trybu failover pojedynczego kliknięcia z niższą RTOą w przypadku awarii.

W tym artykule szczegółowo opisano sposób ochrony aplikacji programu SharePoint przy użyciu [Azure Site Recovery](site-recovery-overview.md). W tym artykule opisano najlepsze rozwiązania dotyczące replikowania aplikacji programu SharePoint z trzema warstwami na platformę Azure, jak można przeprowadzić drążenie odzyskiwania po awarii oraz jak przełączać się do trybu failover aplikacji na platformie Azure.

Możesz obejrzeć poniższy film wideo dotyczący odzyskiwania aplikacji wielowarstwowej na platformie Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że rozumiesz następujące kwestie:

1. [Replikowanie maszyny wirtualnej na platformę Azure](./vmware-azure-tutorial.md)
2. [Projektowanie sieci odzyskiwania](./concepts-on-premises-to-azure-networking.md)
3. [Testowanie pracy w trybie failover na platformie Azure](site-recovery-test-failover-to-azure.md)
4. [Przełączenie w tryb failover na platformie Azure](site-recovery-failover.md)
5. Jak [replikować kontroler domeny](site-recovery-active-directory.md)
6. Jak [replikować SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>Architektura programu SharePoint

Program SharePoint można wdrożyć na jednym lub większej liczbie serwerów przy użyciu topologii warstwowej i ról serwera, aby zaimplementować projekt farmy, który spełnia określone cele i cele. Typowa farma serwerów programu SharePoint o dużej pojemności, która obsługuje dużą liczbę równoczesnych użytkowników i dużą liczbę elementów zawartości używa grupowania usług w ramach strategii skalowalności. Takie podejście obejmuje uruchamianie usług na dedykowanych serwerach, grupowanie tych usług, a następnie skalowanie serwerów jako grupy. Poniższa topologia ilustruje grupowanie usługi i serwera dla trzech warstw farmy serwerów programu SharePoint. Aby uzyskać szczegółowe wskazówki dotyczące różnych topologii programu SharePoint, zapoznaj się z dokumentacją programu SharePoint i architekturami linii produktów. Więcej szczegółów na temat wdrożenia programu SharePoint 2013 można znaleźć w [tym dokumencie](/SharePoint/sharepoint-server).



![Wzorzec wdrożenia 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Obsługa usługi Site Recovery

Site Recovery to Application-niezależny od i powinna działać z dowolną wersją programu SharePoint uruchomioną na obsługiwanej maszynie. W przypadku tworzenia tego artykułu są używane maszyny wirtualne VMware z systemem Windows Server 2012 R2 Enterprise. Użyto programu SharePoint 2013 Enterprise Edition i programu SQL Server 2014 Enterprise Edition.

### <a name="source-and-target"></a>Źródło i cel

**Scenariusz** | **Do lokacji dodatkowej** | **Na platformie Azure**
--- | --- | ---
**Hyper-V** | Tak | Tak
**VMware** | Tak | Tak
**Serwer fizyczny** | Tak | Tak
**Azure** | Nie dotyczy | Yes


### <a name="things-to-keep-in-mind"></a>Rzeczy, o których należy pamiętać

Jeśli w aplikacji jest używany udostępniony klaster oparty na dyskach, nie będzie można używać replikacji Site Recovery do replikowania tych maszyn wirtualnych. Możesz użyć natywnej replikacji dostarczonej przez aplikację, a następnie użyć [planu odzyskiwania](site-recovery-create-recovery-plans.md) w celu przełączenia w tryb failover wszystkich warstw.

## <a name="replicating-virtual-machines"></a>Replikowanie maszyn wirtualnych

Postępuj zgodnie z [poniższymi wskazówkami](./vmware-azure-tutorial.md) , aby rozpocząć replikację maszyny wirtualnej na platformie Azure.

* Po zakończeniu replikacji upewnij się, że przejdziesz do każdej maszyny wirtualnej każdej warstwy i wybierz ten sam zestaw dostępności w obszarze "zreplikowane elementy > ustawienia > właściwości > obliczenia i sieć". Jeśli na przykład warstwa sieci Web ma 3 maszyny wirtualne, upewnij się, że wszystkie 3 maszyny wirtualne są skonfigurowane jako należące do tego samego zestawu dostępności na platformie Azure.

    ![Set-Availability-Set](./media/site-recovery-sharepoint/select-av-set.png)

* Aby uzyskać wskazówki dotyczące ochrony Active Directory i systemu DNS, zobacz [ochrona Active Directory i dokumentu DNS](site-recovery-active-directory.md) .

* Wskazówki dotyczące ochrony warstwy bazy danych działającej w programie SQL Server można znaleźć w temacie [ochrona SQL Server](site-recovery-sql.md) dokumentu.

## <a name="networking-configuration"></a>Konfiguracja sieci

### <a name="network-properties"></a>Właściwości sieci

* W przypadku maszyn wirtualnych aplikacji i warstwy sieci Web Skonfiguruj ustawienia sieci w Azure Portal tak, aby maszyny wirtualne były dołączone do odpowiedniej sieci DR po przejściu w tryb failover.

    ![Wybieranie sieci](./media/site-recovery-sharepoint/select-network.png)


* Jeśli używasz statycznego adresu IP, określ adres IP, który ma być pobierany przez maszynę wirtualną w polu **docelowy adres IP** .

    ![Ustaw statyczny adres IP](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>Routing DNS i ruch sieciowy

W przypadku witryn mających dostęp do Internetu [Utwórz profil Traffic Manager typu "Priority"](../traffic-manager/quickstart-create-traffic-manager-profile.md) w subskrypcji platformy Azure. A następnie skonfiguruj profil DNS i Traffic Manager w następujący sposób.


| **Miejscu** | **Element źródłowy** | **Cel**|
| --- | --- | --- |
| Publiczna usługa DNS | Publiczna usługa DNS dla witryn programu SharePoint <br/><br/> Np.: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| Lokalna usługa DNS | sharepointonprem.contoso.com | Publiczny adres IP w farmie lokalnej |


W profilu Traffic Manager [Utwórz podstawowy i punkt końcowy odzyskiwania](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Użyj zewnętrznego punktu końcowego dla lokalnego punktu końcowego i publicznego adresu IP dla punktu końcowego platformy Azure. Upewnij się, że priorytet jest ustawiony powyżej lokalnego punktu końcowego.

Hostowanie strony testowej na określonym porcie (na przykład 800) w warstwie sieci Web programu SharePoint w celu Traffic Manager automatycznego wykrywania dostępności po przejściu w tryb failover. Jest to obejście w przypadku, gdy nie można włączyć uwierzytelniania anonimowego w żadnej z witryn programu SharePoint.

[Skonfiguruj profil Traffic Manager](../traffic-manager/traffic-manager-configure-priority-routing-method.md) przy użyciu poniższych ustawień.

* Metoda routingu — "priorytet"
* Czas wygaśnięcia (TTL) DNS — "30 s"
* Ustawienia monitora punktu końcowego — Jeśli można włączyć uwierzytelnianie anonimowe, możesz nadać konkretnemu punktowi końcowemu witryny sieci Web. Lub można użyć strony testowej na określonym porcie (na przykład 800).

## <a name="creating-a-recovery-plan"></a>Tworzenie planu odzyskiwania

Plan odzyskiwania umożliwia sekwencjonowanie trybu failover dla różnych warstw w aplikacji wielowarstwowej, w związku z czym zachowanie spójności aplikacji. Postępuj zgodnie z poniższymi instrukcjami podczas tworzenia planu odzyskiwania dla wielowarstwowej aplikacji sieci Web. [Dowiedz się więcej na temat tworzenia planu odzyskiwania](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do grup trybu failover

1. Utwórz plan odzyskiwania, dodając maszyny wirtualne aplikacji i warstwy sieci Web.
2. Kliknij przycisk "Dostosuj", aby zgrupować maszyny wirtualne. Domyślnie wszystkie maszyny wirtualne są częścią grupy 1.

    ![Dostosuj RP](./media/site-recovery-sharepoint/rp-groups.png)

3. Utwórz kolejną grupę (Grupa 2) i Przenieś maszyny wirtualne warstwy internetowej do nowej grupy. Maszyny wirtualne warstwy aplikacji powinny należeć do grupy "Grupa 1", a maszyny wirtualne warstwy sieci Web powinny należeć do grupy "Grupa 2". Jest to konieczne, aby upewnić się, że maszyny wirtualne warstwy aplikacji są uruchamiane najpierw przez maszyny wirtualne warstwy sieci Web.


### <a name="adding-scripts-to-the-recovery-plan"></a>Dodawanie skryptów do planu odzyskiwania

Najczęściej używane skrypty Azure Site Recovery można wdrożyć na koncie usługi Automation, klikając przycisk "wdróż na platformie Azure" poniżej. W przypadku korzystania z dowolnego opublikowanego skryptu upewnij się, że zostały podane wskazówki w skrypcie.

[![Wdrażanie na platformie Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Dodaj skrypt przed akcją w grupie dostępności programu SQL do trybu failover. Użyj skryptu "ASR-SQL-FailoverAG" opublikowanego w przykładowych skryptach. Upewnij się, że postępuj zgodnie ze wskazówkami w skrypcie i wprowadź odpowiednio wymagane zmiany w skrypcie.

    ![Add-AG-Script-Step-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-Script-Step-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Dodaj skrypt akcji po stronie w celu dołączenia modułu równoważenia obciążenia do maszyn wirtualnych w trybie failover w warstwie sieci Web (Grupa 2). Użyj skryptu "ASR-AddSingleLoadBalancer" opublikowanego w przykładowych skryptach. Upewnij się, że postępuj zgodnie ze wskazówkami w skrypcie i wprowadź odpowiednio wymagane zmiany w skrypcie.

    ![Add-LB-Script-Step-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-Script-Step-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Dodaj krok ręczny, aby zaktualizować rekordy DNS w celu wskazywania nowej farmy na platformie Azure.

    * W przypadku witryn mających dostęp do Internetu żadne aktualizacje DNS nie są wymagane po przejściu w tryb failover. Aby skonfigurować Traffic Manager, wykonaj kroki opisane w sekcji "wskazówki dotyczące sieci". Jeśli profil Traffic Manager został skonfigurowany zgodnie z opisem w poprzedniej sekcji, Dodaj skrypt, aby otworzyć fikcyjny port (800 w przykładzie) na maszynie wirtualnej platformy Azure.

    * W przypadku wewnętrznych witryn dodanych należy dodać krok ręczny, aby zaktualizować rekord DNS w taki sposób, aby wskazywał nowy adres IP modułu równoważenia obciążenia dla warstwy sieci Web.

4. Dodaj ręczny krok do przywracania aplikacji wyszukiwania z kopii zapasowej lub uruchamiania nowej usługi wyszukiwania.

5. Aby przywrócić aplikację usługi wyszukiwania z kopii zapasowej, wykonaj poniższe kroki.

    * W tej metodzie przyjęto założenie, że kopia zapasowa aplikacji Search Service była wykonywana przed katastrofalnym zdarzeniem i że kopia zapasowa jest dostępna w lokacji DR.
    * Można to łatwo osiągnąć przez zaplanowanie kopii zapasowej (na przykład raz dziennie) i użycie procedury kopiowania w celu umieszczenia kopii zapasowej w lokacji odzyskiwania po awarii. Procedury kopiowania mogą obejmować programy skryptów, takie jak AzCopy (kopia platformy Azure) lub Konfigurowanie usługi DFSR (replikacja usług plików rozproszonych).
    * Teraz, gdy farma programu SharePoint jest uruchomiona, przejdź do witryny Administracja centralna, "kopia zapasowa i przywracanie" i wybierz pozycję Przywróć. Przywracanie interrogates określoną lokalizację kopii zapasowej (może być konieczne zaktualizowanie wartości). Wybierz kopię zapasową aplikacji Search Service, którą chcesz przywrócić.
    * Wyszukiwanie zostało przywrócone. Należy pamiętać, że przywracanie oczekuje na znalezienie tej samej topologii (tej samej liczby serwerów) i tych samych liter dysków twardych przypisanych do tych serwerów. Aby uzyskać więcej informacji, zobacz sekcję ["Przywracanie aplikacji usługi Search w programie SharePoint 2013"](/SharePoint/administration/restore-a-search-service-application) .


6. Aby rozpocząć pracę z nową aplikacją usługi wyszukiwania, wykonaj poniższe czynności.

    * W tej metodzie przyjęto założenie, że kopia zapasowa bazy danych "Search Administration" jest dostępna w witrynie odzyskiwania po awarii.
    * Ponieważ inne bazy danych aplikacji Search Service nie są replikowane, muszą zostać ponownie utworzone. W tym celu przejdź do okna Administracja centralna i Usuń aplikację Search Service. Na każdym serwerze, który hostuje indeks wyszukiwania, Usuń pliki indeksów.
    * Utwórz ponownie aplikację Search Service i ponownie utworzy bazy danych. Zaleca się utworzenie przygotowanego skryptu, który odtwarza tę aplikację usługi, ponieważ nie jest możliwe wykonywanie wszystkich akcji za pośrednictwem graficznego interfejsu użytkownika. Na przykład ustawienie lokalizacji dysku indeksu i skonfigurowanie topologii wyszukiwania jest możliwe tylko przy użyciu poleceń cmdlet programu SharePoint PowerShell. Użyj polecenia cmdlet programu Windows PowerShell Restore-SPEnterpriseSearchServiceApplication i określ bazę danych z dziennikami, które zostały wysłane i zreplikowane, Search_Service__DB. To polecenie cmdlet zapewnia konfigurację wyszukiwania, schemat, zarządzane właściwości, reguły i źródła, a następnie tworzy domyślny zestaw innych składników.
    * Po ponownym utworzeniu aplikacji Search Service należy uruchomić pełne przeszukiwanie dla każdego źródła zawartości, aby przywrócić Search Service. Niektóre informacje o analizie są tracone z farmy lokalnej, na przykład na podstawie zaleceń dotyczących wyszukiwania.

7. Po zakończeniu wszystkich kroków Zapisz plan odzyskiwania, a końcowy plan odzyskiwania będzie wyglądać następująco.

    ![Zapisano RP](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Przeprowadzenie testu pracy w trybie failover
Postępuj zgodnie z [tymi wskazówkami](site-recovery-test-failover-to-azure.md) , aby przeprowadzić test pracy w trybie failover.

1.  Przejdź do Azure Portal i wybierz magazyn usługi Recovery Service.
2.  Kliknij plan odzyskiwania utworzony dla aplikacji programu SharePoint.
3.  Kliknij pozycję "test pracy w trybie failover".
4.  Wybierz pozycję punkt odzyskiwania i Sieć wirtualna platformy Azure, aby rozpocząć proces testowego przełączania do trybu failover.
5.  Gdy środowisko pomocnicze będzie działać, można wykonać walidację.
6.  Po zakończeniu walidacji można kliknąć pozycję "Oczyść test pracy w trybie failover" w planie odzyskiwania i wyczyścić środowisko testowego trybu failover.

Aby uzyskać wskazówki dotyczące testowania pracy w trybie failover dla usług AD i DNS, zobacz [uwagi dotyczące testowania pracy w trybie failover w przypadku dokumentów usług AD i DNS](site-recovery-active-directory.md#test-failover-considerations) .

Aby uzyskać wskazówki dotyczące przeprowadzania testowej pracy w trybie failover dla zawsze włączonych grup dostępności programu SQL Server, zapoznaj się z tematem [wykonywanie operacji odzyskiwania po awarii aplikacji z Azure Site Recovery i](site-recovery-sql.md#disaster-recovery-of-an-application)

## <a name="doing-a-failover"></a>Przełączenie w tryb failover
Postępuj zgodnie z [tymi wskazówkami](site-recovery-failover.md) , aby wykonać pracę w trybie failover.

1.  Przejdź do Azure Portal i wybierz swój magazyn Recovery Services.
2.  Kliknij plan odzyskiwania utworzony dla aplikacji programu SharePoint.
3.  Kliknij pozycję "praca awaryjna".
4.  Wybierz pozycję punkt odzyskiwania, aby rozpocząć proces przełączania do trybu failover.

## <a name="next-steps"></a>Następne kroki
Więcej informacji na temat [replikowania innych aplikacji](site-recovery-workload.md) można uzyskać przy użyciu Site Recovery.
