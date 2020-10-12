---
title: Często zadawane pytania dotyczące migracji Azure Migrate serwera
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące korzystania z migracji Azure Migrate serwera w celu migrowania maszyn.
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: 80334bb2f0d6c0284c9031a99c0eb469b348873d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91275544"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migracja serwera Azure Migrate: typowe pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Azure Migrate: serwerowego narzędzia migracji. Jeśli masz inne pytania, zapoznaj się z następującymi zasobami:

- [Ogólne pytania](resources-faq.md) dotyczące Azure Migrate
- Pytania dotyczące [urządzenia Azure Migrate](common-questions-appliance.md)
- Pytania dotyczące [odnajdywania, oceny i wizualizacji zależności](common-questions-discovery-assessment.md)
- Uzyskaj odpowiedzi na pytania na [forum Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="does-azure-migrate-convert-uefi-based-machines-to-bios-based-machines-and-migrate-them-to-azure-as-azure-generation-1-vms"></a>Czy Azure Migrate skonwertować maszyny oparte na interfejsie UEFI na maszyny z systemem BIOS i migrować je na platformę Azure jako maszyny wirtualne generacji 1 platformy Azure?
Azure Migrate: Narzędzie do migracji serwera migruje wszystkie maszyny oparte na interfejsie UEFI na platformę Azure jako maszyny wirtualne generacji 2 platformy Azure. Nie obsługujemy już konwersji maszyn wirtualnych opartych na interfejsie UEFI do maszyn wirtualnych opartych na systemie BIOS. Należy pamiętać, że wszystkie maszyny z systemem BIOS są migrowane do platformy Azure tylko w przypadku maszyn wirtualnych platformy Azure generacji 1.

## <a name="how-can-i-migrate-uefi-based-machines-to-azure-as-azure-generation-1-vms"></a>Jak przeprowadzić migrację maszyn opartych na interfejsie UEFI na platformę Azure jako maszyny wirtualne Azure Generation 1?
Azure Migrate: Narzędzie migracji serwera migruje maszyny oparte na interfejsie UEFI na platformę Azure jako maszyny wirtualne 2. generacji platformy Azure. Aby przeprowadzić migrację do maszyn wirtualnych usługi Azure Generation 1, przed rozpoczęciem replikacji przekonwertuj typ rozruchu na system BIOS, a następnie użyj narzędzia do migracji Azure Migrate: serwera, aby przeprowadzić migrację na platformę Azure.
 
## <a name="which-operating-systems-are-supported-for-migration-of-uefi-based-machines-to-azure"></a>Które systemy operacyjne są obsługiwane na potrzeby migracji maszyn opartych na interfejsie UEFI na platformę Azure?

| **Systemy operacyjne obsługiwane w przypadku komputerów z interfejsem UEFI** | **VMware bez agentów do platformy Azure**                                                                                                             | **Funkcja Hyper-V bez agentów na platformie Azure** | **Oparty na agencie oprogramowanie VMware, fizyczne i inne chmury na platformie Azure** |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ | ---------------------------------------------------------- |
| Windows Server 2019, 2016, 2012 R2, 201                 | T                                                                                                                                         | T                              | T                                                          |
| Windows 10 Pro, Windows 10 Enterprise                   | T                                                                                                                                         | T                              | T                                                          |
| SUSE Linux Enterprise Server 15 SP1                     | T                                                                                                                                         | T                              | T                                                          |
| SUSE Linux Enterprise Server 12 SP4                     | T                                                                                                                                         | T                              | T                                                          |
| Ubuntu Server 16,04, 18,04, 19,04, 19,10                | T                                                                                                                                         | T                              | T                                                          |
| RHEL 8,1, 8,0, 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x        | T<br>                 _RHEL 8. x wymaga [ręcznego przygotowania](https://go.microsoft.com/fwlink/?linkid=2143939)_   | T                              | T                                                          |
| Cent OS 8,1, 8,0, 7,7, 7,6, 7,5, 7,4, 6. x               | T<br>_Cent OS 8. x wymaga [przygotowania ręcznego](https://go.microsoft.com/fwlink/?linkid=2143939)_ | T                              | T                                                          |
| Oracle Linux 7,7, 7,7-CI                                |  T                                                                                                                                        | T                              | T                                                          |

## <a name="can-i-use-the-recovery-services-vault-created-by-azure-migrate-for-disaster-recovery-scenarios"></a>Czy mogę używać magazynu usługi Recovery Services utworzonego przez Azure Migrate na potrzeby scenariuszy odzyskiwania po awarii?
Nie zalecamy korzystania z magazynu usługi Recovery Services utworzonego przez Azure Migrate na potrzeby scenariuszy odzyskiwania po awarii. Wykonanie tej czynności może skutkować uruchomieniem niepowodzeń replikacji w Azure Migrate. 

## <a name="where-should-i-install-the-replication-appliance-for-agent-based-migrations"></a>Gdzie należy zainstalować urządzenie replikacji dla migracji opartych na agencie?

Urządzenie replikacji należy zainstalować na dedykowanym komputerze. Urządzenia replikacji nie należy instalować na maszynie źródłowej, która ma zostać zreplikowana lub na urządzeniu odnajdywania Azure Migrate i ocenie, które zainstalowano wcześniej. Aby uzyskać więcej informacji, postępuj zgodnie z [samouczkiem](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines) .

## <a name="how-can-i-migrate-my-aws-ec2-instances-to-azure"></a>Jak migrować wystąpienia AWS EC2 na platformę Azure?

Zapoznaj się z tym [artykułem](https://docs.microsoft.com/azure/migrate/tutorial-migrate-aws-virtual-machines) , aby odkrywać, oceniać i MIGROWAĆ wystąpienia AWS EC2 na platformie Azure.

## <a name="can-i-migrate-aws-vms-running-amazon-linux-operating-system"></a>Czy można migrować maszyny wirtualne AWS z systemem operacyjnym Amazon Linux?

Maszyn wirtualnych z systemem Amazon Linux nie można migrować, ponieważ system operacyjny Amazon Linux jest obsługiwany tylko w systemie AWS.
Aby migrować obciążenia działające w systemie Amazon Linux, można uruchomić maszynę wirtualną CentOS/RHEL na platformie Azure i przeprowadzić migrację obciążenia uruchomionego na maszynie z systemem AWS Linux przy użyciu odpowiedniego podejścia do migracji obciążeń. Na przykład w zależności od obciążenia mogą istnieć narzędzia specyficzne dla obciążenia, które ułatwiają migrację — na przykład w przypadku baz danych lub narzędzi wdrażania w przypadku serwerów sieci Web.

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Jakie lokalizacje geograficzne są obsługiwane w przypadku migracji z Azure Migrate?

Przejrzyj obsługiwane lokalizacje geograficzne [chmur publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [chmur dla instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-regions"></a>Czy można użyć tego samego projektu Azure Migrate do migracji do wielu regionów?

Chociaż można tworzyć oceny dla wielu regionów w projekcie Azure Migrate, jeden Azure Migrate projektu może służyć do migrowania serwerów tylko do jednego regionu platformy Azure. Można utworzyć dodatkowe projekty Azure Migrate dla każdego regionu, do którego należy przeprowadzić migrację.

- W przypadku migracji VMware bez agentów, region docelowy jest zablokowany po włączeniu pierwszej replikacji.
- W przypadku migracji opartych na agentach (VMware, serwerów fizycznych i serwerów z innych chmur) region docelowy jest blokowany po kliknięciu przycisku "Utwórz zasoby" w portalu podczas konfigurowania urządzenia do replikacji.
- W przypadku migracji funkcji Hyper-V bez agentów region docelowy jest blokowany po kliknięciu przycisku "Utwórz zasoby" w portalu podczas konfigurowania dostawcy replikacji funkcji Hyper-V.

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-subscriptions"></a>Czy można użyć tego samego projektu Azure Migrate, aby przeprowadzić migrację do wielu subskrypcji? 

Tak, można migrować do wielu subskrypcji w tym samym regionie docelowym dla projektu Azure Migrate. Można wybrać subskrypcję docelową podczas włączania replikacji dla maszyny lub zestawu maszyn. Region docelowy jest blokowany po pierwszej replikacji dla migracji oprogramowania VMware bez agentów oraz podczas instalowania urządzenia replikacji i dostawcy funkcji Hyper-V na potrzeby migracji opartych na agentach i migracji funkcji Hyper-V bez agenta.

## <a name="what-are-the-migration-options-in-azure-migrate-server-migration"></a>Jakie są opcje migracji w Azure Migrate: Migracja serwera?

Azure Migrate: Narzędzie migracji serwera udostępnia dwie opcje przeprowadzenia migracji serwerów źródłowych/maszyn wirtualnych na platformę Azure — migrację bez agenta i migrację opartą na agentach.

Bez względu na wybraną opcję migracji pierwszy krok migracji serwera przy użyciu migracji platformy Azure: Migracja serwera to włączenie replikacji serwera programu. Wykonuje to replikację początkową danych maszyny wirtualnej/serwera na platformie Azure. Po zakończeniu replikacji początkowej zostaje ustanowiona Ciągła replikacja (trwająca synchronizacja różnicowa) w celu migrowania danych przyrostowych na platformę Azure. Gdy operacja osiągnie etap synchronizacji różnicowej, można wybrać migrację do platformy Azure w dowolnym momencie.  

Poniżej przedstawiono kilka kwestii, które należy wziąć pod uwagę podczas wybierania opcji migracji.

**Migracja bez agentów** nie wymaga, aby żadne oprogramowanie (agenci) zostały wdrożone na źródłowych maszynach wirtualnych/serwerach, które są migrowane. Opcja bezagent organizuje replikację przez integrację z funkcjami udostępnianymi przez dostawcę wirtualizacji.
Opcje replikacji bez agenta są dostępne dla [maszyn wirtualnych VMware](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware) i [maszyn wirtualnych funkcji Hyper-V](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v).

**Migracje oparte na agentach** wymagają zainstalowania oprogramowania Azure Migrate (agenci) na źródłowych maszynach wirtualnych/maszynach, które mają zostać zmigrowane. Opcja oparta na agentach nie bazuje na platformie wirtualizacji funkcji replikacji i dlatego może być używana z dowolnym serwerem z uruchomioną architekturą x86/x64 i wersją systemu operacyjnego obsługiwaną przez metodę replikacji opartą na agentach.

Opcja migracji opartej na agencie może być używana w przypadku [maszyn wirtualnych VMware](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware-agent), [maszyn wirtualnych funkcji Hyper-V](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines), [serwerów fizycznych](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines), [maszyn wirtualnych działających w systemie AWS](https://docs.microsoft.com/azure/migrate/tutorial-migrate-aws-virtual-machines), maszyn wirtualnych działających w usłudze GCP lub maszyn wirtualnych działających na różnych dostawcach wirtualizacji. Migracja oparta na agencie traktuje maszyny jako serwery fizyczne na potrzeby migracji.

Mimo że migracja bez agenta oferuje dodatkową wygodę i prostotę nad opcjami replikacji opartymi na agentach dla obsługiwanych scenariuszy (VMWare i Hyper-V), warto rozważyć użycie scenariusza opartego na agentach dla następujących przypadków użycia:

- Ograniczone środowisko IOPS: replikacja bezagentowa korzysta z migawek i zużywa liczbę operacji we/wy magazynu. Zaleca się, aby Metoda migracji oparta na agentach była w przypadku ograniczeń dotyczących magazynu/operacji we/wy w danym środowisku.
- Jeśli nie masz vCenter Server, możesz traktować maszyny wirtualne VMware jako serwery fizyczne i używać przepływu pracy migracji opartego na agentach.

Aby dowiedzieć się więcej, zapoznaj się z tym [artykułem](https://docs.microsoft.com/azure/migrate/server-migrate-overview) , aby porównać opcje migracji programu VMware.

## <a name="how-does-agentless-migration-work"></a>Jak działa migracja bez agentów?

Azure Migrate: Migracja serwera zapewnia opcje replikacji bez agentów do migracji maszyn wirtualnych VMware i maszyn wirtualnych funkcji Hyper-V z systemem Windows lub Linux. Narzędzie udostępnia również dodatkową opcję replikacji opartą na agentach dla serwerów z systemami Windows i Linux, których można użyć do migrowania serwerów fizycznych, a także maszyn wirtualnych x86/x64 w oprogramowaniu VMware, funkcji Hyper-V, AWS, GCP itp. Opcja replikacji opartej na agentach wymaga zainstalowania oprogramowania agenta na serwerze/maszynie wirtualnej, która jest migrowana, natomiast w przypadku opcji bez agenta oprogramowanie nie musi być zainstalowane na maszynach wirtualnych, dzięki czemu można w ten sposób oferować dodatkową wygodę i prostotę przy użyciu opcji replikacji opartej na agentach.

Opcja replikacji bez agenta działa przy użyciu mechanizmów dostarczonych przez dostawcę wirtualizacji (VMware, Hyper-V). W przypadku maszyn wirtualnych programu VMware mechanizm replikacji bez agenta używa migawek oprogramowania VMware i oprogramowania VMware, które zmieniło technologię śledzenia bloków, aby replikować dane z dysków maszyn wirtualnych. Ten mechanizm jest podobny do używanego przez wiele produktów kopii zapasowych. W przypadku maszyn wirtualnych funkcji Hyper-V mechanizm replikacji bez agenta używa migawek maszyn wirtualnych i funkcji śledzenia zmian funkcji Hyper-V Replica do replikowania danych z dysków maszyny wirtualnej.

Po skonfigurowaniu replikacji dla maszyny wirtualnej najpierw przechodzi ona przez fazę replikacji początkowej. Podczas replikacji początkowej tworzona jest migawka maszyny wirtualnej, a pełna kopia danych z dysków migawek jest replikowana do dysków zarządzanych w ramach subskrypcji. Po zakończeniu replikacji początkowej dla maszyny wirtualnej proces replikacji przechodzi do fazy replikacji przyrostowej (replikacja różnicowa). W fazie replikacji przyrostowej zmiany danych, które wystąpiły od czasu ostatniego zakończonego cyklu replikacji, są okresowo replikowane i stosowane do dysków zarządzanych repliki, dzięki czemu Synchronizacja replikacji ze zmianami odbywa się na maszynie wirtualnej. W przypadku maszyn wirtualnych VMware zmieniono technologię śledzenia bloków programu VMware do śledzenia zmian między cyklami replikacji. Na początku cyklu replikacji tworzona jest migawka maszyny wirtualnej, a w celu uzyskania zmian między bieżącą migawką a ostatnią pomyślnie replikowana migawką zostanie użyta funkcja śledzenia bloków. Dzięki temu tylko dane, które uległy zmianie od czasu ostatniego zakończonego cyklu replikacji, muszą zostać zreplikowane, aby zachować replikację maszyny wirtualnej w celu synchronizacji. Na końcu każdego cyklu replikacji tworzona jest migawka i konsolidacja migawek dla maszyny wirtualnej. Podobnie w przypadku maszyn wirtualnych funkcji Hyper-V aparat śledzenia zmian funkcji Hyper-V Replica służy do śledzenia zmian między kolejnymi cyklami replikacji.
Podczas wykonywania operacji migrowania na replikacji maszyny wirtualnej można zamknąć lokalną maszynę wirtualną i wykonać jedną ostateczną replikację przyrostową w celu zapewnienia zerowej utraty danych. Po wykonaniu opcji Migrowanie dyski zarządzane repliki odpowiadające maszynie wirtualnej są używane do tworzenia maszyny wirtualnej na platformie Azure.

Aby rozpocząć, należy zapoznać się z samouczkiem dotyczącym [migracji bez](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware) agentów [funkcji Hyper-V](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) .

## <a name="how-does-agent-based-migration-work"></a>Jak działa migracja oparta na agencie?

Oprócz opcji migracji bez wykorzystania agentów dla maszyn wirtualnych VMware i maszyn wirtualnych funkcji Hyper-V, narzędzie migracji serwera udostępnia opcję migracji opartej na agencie służącą do migrowania serwerów z systemami Windows i Linux działających na serwerach fizycznych lub działających jako maszyny wirtualne x86/x64 w oprogramowaniu VMware, funkcji Hyper-V, AWS, Google Cloud Platform itd.

Metoda migracji oparta na agentach korzysta z oprogramowania agenta zainstalowanego na serwerze migrowanym w celu replikowania danych serwera na platformę Azure. Proces replikacji używa architektury odciążania, w której Agent przekazuje dane replikacji do dedykowanego serwera replikacji o nazwie urządzenie replikacji lub serwer konfiguracji (lub do serwera przetwarzania skalowalnego w poziomie). [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/agent-based-migration-architecture) o tym, jak działa opcja migracji opartej na agencie. 

Uwaga: urządzenie replikacji różni się od urządzenia odnajdywania Azure Migrate i musi być zainstalowane na oddzielnym/dedykowanym komputerze.

## <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>Jak mogę ocenić wymagania dotyczące przepustowości dla migracji?

Przepustowość replikacji danych na platformę Azure zależy od zakresu czynników i jest funkcją szybkiego odczytywania i replikowania danych na platformie Azure przez lokalne urządzenie Azure Migrate. Replikacja ma dwie fazy: replikację początkową i replikację przyrostową.

Gdy replikacja jest uruchamiana dla maszyny wirtualnej, cykl replikacji początkowej odbywa się w przypadku replikowania pełnych kopii dysków. Po zakończeniu replikacji początkowej cykle przyrostowej replikacji (cykle różnicowe) są planowane okresowo w celu przetransferowania wszelkich zmian, które wystąpiły od czasu poprzedniego cyklu replikacji.

### <a name="agentless-vmware-vm-migration"></a>Migracja maszyn wirtualnych VMware bez agentów

Wymagania dotyczące przepustowości można obsłużyć w zależności od ilości danych, które należy przenieść do rzutu i czasu, w którym chcesz przeprowadzić replikację początkową (najlepiej, aby replikacja początkowa została ukończona co najmniej 3-4 dni przed rzeczywistym oknem migracji, aby zapewnić wystarczającą ilość czasu na przeprowadzenie migracji testowej przed rzeczywistym oknem i przeprowadzenie przestoju do minimum w oknie).

Można oszacować przepustowość lub czas wymagany do migracji maszyn wirtualnych VMware bez agentów, korzystając z następującej formuły:

Czas do ukończenia replikacji początkowej = {size disks (lub używany rozmiar) * 0,7 (przy założeniu, że wartość średnia kompresji wynosząca 30 procent — zachowawcze oszacowanie)}/Bandwidth jest dostępna do replikacji.

### <a name="agent-based-vmware-vm-migration"></a>Migracja maszyny wirtualnej VMware oparta na agencie

W przypadku metody replikacji opartej na agencie planista wdrożenia może pomóc w założeniu środowiska na potrzeby zmiany danych i przewidzieć wymaganą przepustowość. Aby dowiedzieć się więcej, zobacz ten [artykuł](https://docs.microsoft.com/azure/migrate/agent-based-migration-architecture#plan-vmware-deployment). 

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Jak mogęnie przepustowości w przypadku używania urządzenia Azure Migrate do replikacji VMware bez agentów?  

Można ograniczyć użycie NetQosPolicy. Na przykład:

AppNamePrefix do użycia w NetQosPolicy to "GatewayWindowsService.exe". Można utworzyć zasady na urządzeniu Azure Migrate, aby ograniczyć ruch związany z replikacją z urządzenia przez utworzenie zasad, takich jak:

New-NetQosPolicy-Name "ThrottleReplication"-AppPathNameMatchCondition "GatewayWindowsService.exe"-ThrottleRateActionBitsPerSecond 1 MB

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>Jak dane są przesyłane ze środowiska Premium do platformy Azure? Czy jest szyfrowana przed przesłaniem?

Urządzenie Azure Migrate w przypadku replikacji bez agenta kompresuje dane i szyfruje przed przekazaniem. Dane są przesyłane przy użyciu bezpiecznego kanału komunikacyjnego za pośrednictwem protokołu HTTPS i korzystają z protokołu TLS 1,2 lub nowszego. Ponadto usługa Azure Storage automatycznie szyfruje dane, gdy zostaną utrwalone w chmurze (szyfrowanie w trybie REST).  

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Jak współczynnik zmian wpływa na replikację bezagentową?

Ponieważ replikacja bez agentów jest składana w danych, *wzorzec* zmian jest ważniejszy niż *współczynnik*zmian. Gdy plik zostanie ponownie zapisany i ponownie, szybkość nie ma znacznie wpływu. Jednak wzorzec, w którym każdy inny sektor jest zapisywana, powoduje duże zmiany w następnym cyklu. Ze względu na to, że minimalizujemy ilość przesyłanych danych, będziemy mogli złożyć dane tak dużo, jak to możliwe, przed zaplanowaniem następnego cyklu.

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Jak często zaplanowano cykl replikacji?

Formuła do zaplanowania następnego cyklu replikacji to (czas poprzedniego cyklu/2) lub jedna godzina, w zależności od tego, co jest wyższe.

Na przykład jeśli maszyna wirtualna zajmuje cztery godziny cyklu różnicowego, kolejny cykl jest zaplanowany w ciągu dwóch godzin, a nie w ciągu następnej godziny. Proces jest inny od razu po replikacji początkowej, gdy pierwszy cykl różnicowy jest zaplanowany natychmiast.

## <a name="how-do-i-migrate-windows-server-2003-running-on-vmwarehyper-v-to-azure"></a>Jak mogę przeprowadzić migrację systemu Windows Server 2003 uruchomionego w oprogramowaniu VMware/Hyper-V do platformy Azure?

[Rozszerzona pomoc techniczna systemu Windows Server 2003](https://go.microsoft.com/fwlink/?linkid=2140400) zakończyła się 14 lipca 2015.  Zespół pomocy technicznej systemu Azure nadal pomaga w rozwiązywaniu problemów związanych z uruchamianiem systemu Windows Server 2003 na platformie Azure. Ta obsługa jest jednak ograniczona do problemów, które nie wymagają rozwiązywania problemów lub poprawek na poziomie systemu operacyjnego.
Zalecanym podejściem do migrowania aplikacji do wystąpień platformy Azure z nowszą wersją systemu Windows Server jest zapewnienie elastyczności i niezawodności chmury platformy Azure.

Jeśli jednak nadal zdecydujesz się na migrację systemu Windows Server 2003 na platformę Azure, możesz użyć narzędzia migracji serwera Azure Migrate, jeśli serwer systemu Windows jest maszyną wirtualną działającą w oprogramowaniu VMware lub funkcji Hyper-V, zapoznaj się z tym artykułem, aby [przygotować maszyny z systemem Windows Server 2003 do migracji](https://go.microsoft.com/fwlink/?linkid=2140302).

## <a name="what-is-the-difference-between-the-test-migration-and-migrate-operations"></a>Jaka jest różnica między migracją testową i migracją operacji?

Migracja testowa umożliwia testowanie i weryfikowanie migracji przed rzeczywistą migracją. Migracja testowa działa przez umożliwienie tworzenia kopii testowych replikowanych maszyn wirtualnych w środowisku piaskownicy na platformie Azure. Środowisko piaskownicy jest rozdzielone testowaną siecią wirtualną, którą określisz. Operacja migracji testowej nie zakłóca działania, a aplikacje kontynuują działanie w źródle, jednocześnie umożliwiając wykonywanie testów na sklonowanej kopii w izolowanym środowisku piaskownicy. W razie potrzeby można wykonać wiele testów, aby zweryfikować migrację, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed rzeczywistą migracją.

## <a name="will-windows-server-2008-and-2008-r2-be-supported-in-azure-after-migration"></a>Czy po migracji systemu Windows Server 2008 i 2008 R2 będą obsługiwane na platformie Azure?

Lokalne serwery z systemem Windows Server 2008 i 2008 R2 można migrować do usługi Azure Virtual Machines i pobrać rozszerzone aktualizacje zabezpieczeń przez trzy lata po zakończeniu okresów pomocy technicznej. Aby przeprowadzić migrację obciążeń systemu Windows Server 2008 i 2008 R2, można użyć narzędzia do migracji serwera Azure Migrate.

## <a name="is-there-a-rollback-option-for-azure-migrate"></a>Czy istnieje opcja wycofywania dla Azure Migrate?

Możesz użyć opcji migracji testowej, aby zweryfikować funkcjonalność i wydajność aplikacji na platformie Azure. Można wykonać dowolną liczbę migracji testów i wykonać ostateczną migrację po ustanowieniu pewności w ramach operacji migracji testowej. Migracja testowa nie ma wpływu na maszynę lokalną, która pozostaje operacyjna i kontynuuje replikację do momentu przeprowadzenia rzeczywistej migracji. Jeśli wystąpiły błędy podczas migracji testowej przeprowadzających, można wybrać odroczenie końcowej migracji i zachować źródłową maszynę wirtualną/serwer z systemem Azure. Po rozwiązaniu błędów można ponowić próbę zakończenia migracji.  
Uwaga: po wykonaniu końcowej migracji na platformę Azure, gdy lokalna maszyna źródłowa została wyłączona, nie można przeprowadzić wycofywania z platformy Azure do środowiska lokalnego.

## <a name="can-i-select-the-virtual-network-and-subnet-to-use-for-test-migrations"></a>Czy można wybrać Virtual Network i podsieć do użycia na potrzeby migracji testowej?

Można wybrać Virtual Network do migracji testowej. Podsieć jest automatycznie wybierana na podstawie następujących logiki:

- Jeśli podsieć docelowa (inna niż domyślna) została określona jako dane wejściowe podczas włączania replikacji, Azure Migrate priorytetyzacja przy użyciu podsieci o tej samej nazwie w Virtual Network wybranym dla migracji testowej.
- Jeśli podsieć o tej samej nazwie nie zostanie znaleziona, Azure Migrate wybiera pierwszą podsieć dostępną alfabetycznie, która nie jest bramą/Application Gateway/zaporą/bastionu.

## <a name="why-is-the-test-migration-button-disabled-for-my-server"></a>Dlaczego przycisk Test migracji jest wyłączony dla mojego serwera?

Przycisk migracji testów może być w stanie wyłączenia w następujących scenariuszach:

- Nie można rozpocząć migracji testowej do momentu ukończenia replikacji początkowej dla maszyny wirtualnej. Przycisk migracja testowa zostanie wyłączony do momentu zakończenia procesu podczerwieni. Można przeprowadzić migrację testową, gdy maszyna wirtualna jest w fazie synchronizacji różnicowej.
- Przycisk można wyłączyć, jeśli migracja testowa została już zakończona, ale nie wykonano oczyszczania Test-Migration dla tej maszyny wirtualnej. Wykonaj oczyszczanie migracji testów i spróbuj ponownie wykonać operację.

## <a name="what-happens-if-i-dont-clean-up-my-test-migration"></a>Co się stanie, jeśli nie wyczyścię mojej migracji testowej?

Migracja testowa symuluje rzeczywistą migrację, tworząc testową maszynę wirtualną platformy Azure przy użyciu zreplikowanych danych. Serwer zostanie wdrożony z zastosowaniem w czasie kopii replikowanych danych do docelowej grupy zasobów (wybranej podczas włączania replikacji) przy użyciu sufiksu "-test". Migracje testowe są przeznaczone do weryfikowania funkcjonalności serwera w celu zminimalizowania problemów po migracji. Jeśli migracja testowa nie zostanie oczyszczona, testowa maszyna wirtualna będzie nadal działać na platformie Azure i będzie powodować naliczanie opłat. Aby oczyścić test po migracji, przejdź do widoku maszyny replikowane w narzędziu do migracji serwera i użyj akcji "Oczyść test Migration" na komputerze.

## <a name="can-i-migrate-active-directory-domain-controllers-using-azure-migrate"></a>Czy można migrować Active Directory kontrolery domeny za pomocą Azure Migrate?

Narzędzie migracji serwera jest niezależny od aplikacji i działa w przypadku większości aplikacji. Podczas migrowania serwera przy użyciu narzędzia do migracji serwera są migrowane wszystkie aplikacje zainstalowane na serwerze. Jednak w przypadku niektórych aplikacji alternatywne metody migracji inne niż Migracja serwera mogą być lepiej dopasowane do migracji.  Aby uzyskać Active Directory, w przypadku środowisk hybrydowych, w których lokacja lokalna jest połączona ze środowiskiem platformy Azure, możesz ją rozłożyć na platformę Azure, dodając do platformy Azure dodatkowe kontrolery domeny i konfigurując Active Directory replikację. W przypadku migrowania do środowiska izolowanego na platformie Azure wymagające własnych kontrolerów domeny (lub testowania aplikacji w środowisku piaskownicy) można migrować serwery za pomocą narzędzia migracji serwera.

## <a name="what-happens-if-i-dont-stop-replication-after-migration"></a>Co się stanie, jeśli nie zatrzymaję replikacji po migracji?

Po zatrzymaniu replikacji Azure Migrate: Narzędzie do migracji serwera czyści dyski zarządzane w ramach subskrypcji utworzonej na potrzeby replikacji. Jeśli po migracji nie zatrzymasz replikacji, nadal będą naliczane opłaty za te dyski. Zatrzymanie replikacji nie będzie miało wpływu na dyski dołączone do maszyn, które zostały już zmigrowane.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Czy do migrowania maszyn wirtualnych VMware jest potrzebny program VMware vCenter?

Aby [przeprowadzić migrację maszyn wirtualnych VMware](server-migrate-overview.md) przy użyciu migracji opartej na agencie VMware lub bez agentów, hosty ESXi, na których znajdują się maszyny wirtualne, muszą być zarządzane przez vCenter Server. Jeśli nie masz vCenter Server, możesz migrować maszyny wirtualne VMware przez migrowanie ich jako serwerów fizycznych. [Dowiedz się więcej](migrate-support-matrix-physical-migration.md).

## <a name="can-i-upgrade-my-os-while-migrating"></a>Czy mogę uaktualnić system operacyjny podczas migracji?

Azure Migrate: Narzędzie migracji serwera obsługuje obecnie tylko migracje podobne do tego. Narzędzie nie obsługuje uaktualniania wersji systemu operacyjnego podczas migracji. Migrowana maszyna będzie mieć ten sam system operacyjny, co maszyna źródłowa.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliances"></a>Wdrożono co najmniej dwa urządzenia do odnajdywania maszyn wirtualnych w vCenter Server. Jednak podczas próby migrowania maszyn wirtualnych są wyświetlane tylko maszyny wirtualne odpowiadające jednemu z urządzeń.

Jeśli skonfigurowano wiele urządzeń, wymaga się, aby maszyny wirtualne na podanych kontach programu vCenter nie nakładały się. Odnajdywanie z takim nakładaniem się jest nieobsługiwane.

## <a name="how-do-i-know-if-my-vm-was-successfully-migrated"></a>Jak mogę sprawdzić, czy moja maszyna wirtualna została pomyślnie zmigrowana?

Po pomyślnym przeprowadzeniu migracji maszyny wirtualnej/serwera możesz wyświetlić maszynę wirtualną i zarządzać nią ze strony Virtual Machines. Nawiąż połączenie z zmigrowanym maszyną wirtualną w celu zweryfikowania.
Alternatywnie można przejrzeć "stan zadania" dla operacji, aby sprawdzić, czy migracja została pomyślnie ukończona. Jeśli zobaczysz jakiekolwiek błędy, Rozwiąż je, a następnie ponów operację migracji.

## <a name="can-i-consolidate-multiple-source-vms-into-one-vm-while-migrating"></a>Czy można skonsolidować wiele źródłowych maszyn wirtualnych do jednej maszyny wirtualnej podczas migracji?

Możliwości migracji serwera Azure Migrate obsługują takie jak obecnie migracje. Nie obsługujemy konsolidacji serwerów ani uaktualniania systemu operacyjnego w ramach migracji. 

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>W jaki sposób replikacja bez agentów ma wpływ na serwery VMware?

Replikacja bez wykorzystania agentów powoduje pewien wpływ na wydajność na hostach VMware vCenter Server i VMware ESXi. Ponieważ replikacja bezagentowa używa migawek, zużywa ona operacje we/wy na sekundę, więc wymagana jest pewna przepustowość magazynu IOPS. Nie zalecamy korzystania z replikacji bez wykorzystania agentów, jeśli istnieją ograniczenia dotyczące magazynu lub operacji we/wy na sekundę w danym środowisku.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [omówieniem Azure Migrate](migrate-services-overview.md).
