---
title: Architektura referencyjna dla baz danych Oracle na platformie Azure | Microsoft Docs
description: Odwołuje się do architektur do uruchamiania Oracle Database Enterprise Edition baz danych w Microsoft Azure Virtual Machines.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 12/13/2019
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: bbaf34c977546891c6ac05fbd4b5feb15f333e04
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737816"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Architektury referencyjne dla Oracle Database Enterprise Edition na platformie Azure

Ten przewodnik zawiera szczegółowe informacje na temat wdrażania bazy danych Oracle o wysokiej dostępności na platformie Azure. Ponadto w tym przewodniku omówieniach się na zagadnienia dotyczące odzyskiwania po awarii. Te architektury zostały utworzone na podstawie wdrożeń klientów. Ten przewodnik dotyczy tylko Oracle Database Enterprise Edition.

Jeśli chcesz dowiedzieć się więcej o maksymalizacji wydajności bazy danych Oracle, zobacz [architekt a Oracle DB](oracle-design.md).

## <a name="assumptions"></a>Założenia

- Znasz różne koncepcje platformy Azure, takie jak [strefy dostępności](../../../availability-zones/az-overview.md)
- Korzystasz z programu Oracle Database Enterprise Edition 12c lub nowszego
- Masz świadomość i potwierdzić implikacje związane z licencjonowaniem w przypadku korzystania z rozwiązań w tym artykule

## <a name="high-availability-for-oracle-databases"></a>Wysoka dostępność baz danych Oracle

Osiągnięcie wysokiej dostępności w chmurze jest ważną częścią planowania i projektowania każdej organizacji. Microsoft Azure oferuje [strefy dostępności](../../../availability-zones/az-overview.md) i zestawy dostępności (do użycia w regionach, w których strefy dostępności są niedostępne). Przeczytaj więcej na temat [zarządzania dostępnością maszyn wirtualnych](../../manage-availability.md) w celu zaprojektowania chmury.

Oprócz natywnych narzędzi i ofert w chmurze firma Oracle udostępnia rozwiązania wysokiej dostępności, takie jak [Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7), [Data Guard z FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [fragmentowania](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)i [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html) , które można skonfigurować na platformie Azure. Ten przewodnik obejmuje architektury referencyjne dla każdego z tych rozwiązań.

Na koniec podczas migrowania lub tworzenia aplikacji dla chmury ważne jest, aby dostosować kod aplikacji w celu dodania wzorców w chmurze, takich jak [wzorzec ponawiania](/azure/architecture/patterns/retry) i [wzorzec wyłącznika](/azure/architecture/patterns/circuit-breaker). Dodatkowe wzorce zdefiniowane w [przewodniku wzorów projektowych chmury](/azure/architecture/patterns/) mogą pomóc w większym odporności aplikacji.

### <a name="oracle-rac-in-the-cloud"></a>Oracle RAC w chmurze

Oracle Real Application Cluster (RAC) to rozwiązanie zapewniane przez firmę Oracle, które ułatwia klientom osiąganie dużej przepływności przez wiele wystąpień uzyskujących dostęp do jednego magazynu bazy danych (współdzielony wzorzec architektury). Mimo że certyfikat RAC dla programu Oracle może być używany w środowisku lokalnym o wysokiej dostępności, nie można używać go w celu zapewnienia wysokiej dostępności w chmurze, ponieważ chroni on tylko przed awariami na poziomie wystąpienia, a nie z awariami na poziomie stojaków lub centrów danych. Z tego powodu firma Oracle zaleca użycie usługi Oracle Data Guard z bazą danych (w przypadku pojedynczego wystąpienia lub certyfikatu RAC) w celu zapewnienia wysokiej dostępności. Klienci zazwyczaj wymagają dużej umowy SLA na potrzeby uruchamiania aplikacji o znaczeniu strategicznym. Certyfikat Oracle RAC nie jest obecnie certyfikowany ani obsługiwany przez program Oracle na platformie Azure. Jednak platforma Azure oferuje funkcje, takie jak platforma Azure, oferuje Strefy dostępności i planowane okna obsługi, aby ułatwić ochronę przed awariami na poziomie wystąpienia. Oprócz tego klienci mogą korzystać z technologii takich jak Oracle Data Guard, Oracle GoldenGate i Oracle fragmentowania w celu zapewnienia wysokiej wydajności i odporności przez ochronę swoich baz danych przed awariami na poziomie stojaków oraz geograficznie-politycznych.

W przypadku uruchamiania baz danych Oracle w wielu [strefach dostępności](../../../availability-zones/az-overview.md) w połączeniu z usługą Oracle Data Guard lub GoldenGate klienci mogą uzyskać umowę SLA na 99,99% czasu pracy. W regionach platformy Azure, w których strefy dostępności nie są jeszcze obecne, klienci mogą korzystać z [zestawów dostępności](../../manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) i uzyskać umowę SLA dotyczącą czasu działania wynoszącą 99,95% czasu.

>Uwaga: możesz mieć miejsce docelowe czasu pracy, które jest znacznie wyższe niż umowa SLA czasu działania świadczona przez firmę Microsoft.

## <a name="disaster-recovery-for-oracle-databases"></a>Odzyskiwanie po awarii dla baz danych Oracle

W przypadku hostowania aplikacji o znaczeniu krytycznym w chmurze ważne jest zaprojektowanie pod kątem wysokiej dostępności i odzyskiwania po awarii.

W przypadku Oracle Database Enterprise Edition Funkcja Oracle Data Guard jest przydatną funkcją odzyskiwania po awarii. Można skonfigurować wystąpienie bazy danych w stanie wstrzymania w [sparowanym regionie platformy Azure](../../../best-practices-availability-paired-regions.md) i skonfigurować tryb failover usługi Data Guard na potrzeby odzyskiwania po awarii. W przypadku utraty danych zero zaleca się wdrożenie wystąpienia funkcji synchronizacji danych Oracle Data Guard oprócz aktywnej usługi Data Guard. 

Rozważ skonfigurowanie wystąpienia funkcji ochrony danych o dużej synchronizacji w innej strefie dostępności niż podstawowa baza danych Oracle, jeśli aplikacja zezwala na opóźnienie (wymagane jest dokładne testowanie). Użyj **maksymalnego trybu dostępności** , aby skonfigurować synchroniczny transport plików ponownie do wystąpienia daleko synchronizacji. Te pliki są następnie przesyłane asynchronicznie do nieaktywnej bazy danych. 

Jeśli aplikacja nie zezwala na utratę wydajności podczas konfigurowania wystąpienia daleko synchronizacji w innej strefie dostępności w trybie **maksymalnym dostępności** (synchronicznym), można skonfigurować wystąpienie daleko synchronizacji w tej samej strefie dostępności co podstawowa baza danych. Aby zwiększyć dostępność, należy rozważyć skonfigurowanie wielu wystąpień o dużej synchronizacji blisko podstawowej bazy danych i co najmniej jednego wystąpienia w pobliżu bazy danych w stanie wstrzymania (jeśli przejścia roli). Przeczytaj więcej na temat usługi Oracle Data Guard daleko Sync w tym oficjalnym dokumencie [Oracle Data Guard](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf).

W przypadku korzystania z baz danych programu Oracle Standard Edition dostępne są rozwiązania niezależnych dostawców oprogramowania, na przykład w celu zapewnienia wysokiej dostępności i odzyskiwania po awarii.

## <a name="reference-architectures"></a>Architektury referencyjne

### <a name="oracle-data-guard"></a>Oracle Data Guard

Funkcja Oracle Data Guard zapewnia wysoką dostępność, ochronę danych i odzyskiwanie po awarii dla danych przedsiębiorstwa. Funkcja ochrony danych zachowuje gotowość baz danych jako transakcyjnie spójne kopie podstawowej bazy danych. W zależności od odległości między podstawową i pomocniczymi bazami danych i tolerancją aplikacji dla opóźnienia można skonfigurować replikację synchroniczną lub asynchroniczną. Jeśli podstawowa baza danych jest niedostępna ze względu na planowaną lub nieplanowaną awarię, Usługa Data Guard może przełączyć wszystkie nadmiarowe bazy danych do roli głównej, co minimalizuje przestoje. 

W przypadku korzystania z usługi Oracle Data Guard można także otworzyć pomocniczą bazę danych do celów tylko do odczytu. Ta konfiguracja jest nazywana aktywną ochroną danych. Oracle Database 12c wprowadził funkcję o nazwie "daleko synchronizacji danych". To wystąpienie umożliwia skonfigurowanie zerowej konfiguracji utraty danych programu Oracle Database bez konieczności naruszania wydajności.

> [!NOTE]
> Usługa Active Data Guard wymaga dodatkowej licencji. Ta licencja jest również wymagana do korzystania z funkcji daleko Sync. Skontaktuj się z przedstawicielem firmy Oracle, aby omówić implikacje związane z licencjonowaniem.

#### <a name="oracle-data-guard-with-fsfo"></a>Oracle Data Guard z FSFO
Funkcja Oracle Data Guard z Fast-Start tryb failover (FSFO) może zapewnić dodatkową odporność przez skonfigurowanie brokera na osobnej maszynie. Broker usługi Data Guard i pomocnicza baza danych uruchamiają obserwatora i obserwują podstawową bazę danych w celu przestoju. Pozwala to na nadmiarowość w instalacji obserwatora usługi Data Guard. 

W przypadku Oracle Database w wersji 12,2 lub nowszej można również skonfigurować wiele obserwatorów z jedną konfiguracją brokera funkcji Oracle Data Guard. Ta konfiguracja zapewnia dodatkową dostępność, na wypadek awarii jednego obserwatora i pomocniczej bazy danych. Broker funkcji Data Guard jest lekki i może być hostowany na stosunkowo niewielkiej maszynie wirtualnej. Aby dowiedzieć się więcej na temat brokera i jego zalet programu Data Guard, zapoznaj się z [dokumentacją firmy Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html) w tym temacie.

Poniższy diagram przedstawia zalecaną architekturę korzystania z funkcji Oracle Data Guard na platformie Azure ze strefami dostępności. Ta architektura pozwala uzyskać umowę SLA dotyczącą czasu działania maszyny wirtualnej wynoszącą 99,99% czasu.

![Diagram przedstawiający zalecaną architekturę do korzystania z funkcji Oracle Data Guard na platformie Azure z strefami dostępności.](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

Na powyższym diagramie system klienta uzyskuje dostęp do niestandardowej aplikacji z zapleczem firmy Oracle za pośrednictwem sieci Web. Fronton sieci Web jest skonfigurowany w usłudze równoważenia obciążenia. Fronton sieci Web tworzy połączenie z odpowiednim serwerem aplikacji w celu obsługi pracy. Serwer aplikacji wysyła zapytanie do podstawowej bazy danych Oracle. Baza danych Oracle została skonfigurowana przy użyciu [maszyny wirtualnej zoptymalizowanej pod kątem pamięci](../../sizes-memory.md) z [ograniczeniami procesorów wirtualnych vCPU](../../../virtual-machines/constrained-vcpu.md) , aby zaoszczędzić na kosztach licencjonowania i zwiększyć wydajność. Wiele dysków Premium lub Ultra (Managed Disks) jest używanych do zapewnienia wydajności i wysokiej dostępności.

Bazy danych Oracle są umieszczane w wielu strefach dostępności w celu zapewnienia wysokiej dostępności. Każda strefa składa się z jednego lub więcej centrów danych, które są wyposażone w niezależną moc, chłodzenie i sieci. W celu zapewnienia odporności należy skonfigurować co najmniej trzy oddzielne strefy we wszystkich włączonych regionach. Fizyczne rozdzielenie stref dostępności w regionie chroni dane przed awariami centrum danych. Ponadto dwa obserwatory FSFO są konfigurowane w dwóch strefach dostępności w celu zainicjowania bazy danych i przełączenia jej w tryb failover w przypadku wystąpienia awarii. 

Można skonfigurować dodatkowe obserwatory i/lub rezerwowe bazy danych w innej strefie dostępności (AZ 1, w tym przypadku) niż strefa pokazana w poprzedniej architekturze. Na koniec bazy danych Oracle są monitorowane pod kątem czasu i wydajności przez program Oracle Enterprise Manager (OEM). Producent OEM pozwala również generować różne raporty dotyczące wydajności i użycia.

W regionach, w których strefy dostępności nie są obsługiwane, możesz użyć zestawów dostępności do wdrożenia Oracle Database w trybie wysokiej dostępności. Zestawy dostępności umożliwiają osiągnięcie czasu działania maszyny wirtualnej w wysokości 99,95%. Na poniższym diagramie przedstawiono architekturę referencyjną tego zastosowania:

![Oracle Database przy użyciu zestawów dostępności z brokerem usługi Data Guard — FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * Maszyna wirtualna programu Oracle Enterprise Manager nie musi być umieszczona w zestawie dostępności, ponieważ jest wdrażanych tylko jedno wystąpienie producenta OEM.
> * W konfiguracji zestawu dostępności nie są obecnie obsługiwane Ultra Disks.

#### <a name="oracle-data-guard-far-sync"></a>Duża synchronizacja z programem Oracle Data Guard

W przypadku usługi Oracle Data Guard funkcja ochrony przed utratą danych w bazie danych Oracle. Ta funkcja umożliwia ochronę przed utratą danych w przypadku awarii maszyny bazy danych. Na oddzielnej maszynie wirtualnej należy zainstalować synchronizację danych Oracle Data Guard. Synchronizacja jest lekkim wystąpieniem programu Oracle, które ma tylko plik kontrolny, plik haseł, SPFile i dzienniki gotowości. Brak plików danych lub plików dziennika rego. 

W przypadku ochrony bez utraty danych wymagana jest synchroniczna komunikacja między podstawową bazą danych a wystąpieniem daleko synchronizacji. Wystąpienie daleko synchronizacji odbiera ponownie z poziomu podstawowego w sposób synchroniczny i natychmiast przekazuje je do wszystkich nieaktywnych baz danych w sposób asynchroniczny. Ta konfiguracja zmniejsza również obciążenie podstawowej bazy danych, ponieważ ma jedynie wysyłać ponownie do wystąpienia daleko synchronizacji, a nie wszystkich nieaktywnych baz danych. Jeśli wystąpienie o dużej synchronizacji nie powiedzie się, funkcja ochrony danych automatycznie użyje transportu asynchronicznego do pomocniczej bazy danych z podstawowej bazy danych, aby zapewnić ochronę przed utratą danych. W celu zwiększenia odporności klienci mogą wdrażać wiele wystąpień o dużej synchronizacji na każde wystąpienie bazy danych (podstawowe i pomocnicze).

Na poniższym diagramie przedstawiono architekturę wysokiej dostępności korzystającą z usługi Oracle Data Guard:

![Baza danych Oracle używająca stref dostępności z usługą Data Guard daleko synchronizacji & Broker — FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

W poprzedniej architekturze istnieje wystąpienie o dużej synchronizacji wdrożone w tej samej strefie dostępności co wystąpienie bazy danych, aby zmniejszyć opóźnienie między nimi. W przypadkach, gdy aplikacja jest wrażliwa na opóźnienia, rozważ wdrożenie bazy danych i wystąpienia daleko synchronizacji lub wystąpień w [grupie umieszczania sąsiedztwa](../../../virtual-machines/linux/proximity-placement-groups.md).

Na poniższym diagramie przedstawiono architekturę wykorzystującą funkcję Oracle Data Guard FSFO i synchronizację w celu uzyskania wysokiej dostępności i odzyskiwania po awarii:

![Oracle Database korzystania ze stref dostępności na potrzeby odzyskiwania po awarii z usługą Data Guard daleko Sync & Broker — FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

GoldenGate umożliwia wymianę i manipulowanie danymi na poziomie transakcji wśród wielu platform heterogenicznych w całym przedsiębiorstwie. Przenosi ona zatwierdzone transakcje z integralnością transakcji i minimalnym obciążeniem istniejącej infrastruktury. Jej modularna architektura zapewnia elastyczność wyodrębniania i replikowania wybranych rekordów danych, zmian transakcyjnych i zmian w języku DDL (język definicji danych) w różnych topologiach.

Program Oracle GoldenGate umożliwia skonfigurowanie bazy danych pod kątem wysokiej dostępności, zapewniając replikację dwukierunkową. Pozwala to na skonfigurowanie konfiguracji z **wieloma wzorcami** lub **aktywnym aktywnym**. Poniższy diagram jest zalecaną architekturą dla programu Oracle GoldenGate Active-Active Setup na platformie Azure. W poniższej architekturze baza danych Oracle została skonfigurowana przy użyciu [maszyny wirtualnej zoptymalizowanej pod kątem pamięci](../../sizes-memory.md) z [ograniczeniami procesorów wirtualnych vCPU](../../../virtual-machines/constrained-vcpu.md) , aby zaoszczędzić na kosztach licencjonowania i zwiększyć wydajność. Do wydajności i dostępności są używane wiele dysków w warstwie Premium lub Ultra (dyski zarządzane).

![Oracle Database używania stref dostępności z brokerem usługi Data Guard — FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> Podobną architekturę można skonfigurować przy użyciu zestawów dostępności w regionach, w których strefy dostępności nie są obecnie dostępne.

Program Oracle GoldenGate ma takie procesy jak wyodrębnianie, pompa i replikacja, które ułatwiają asynchroniczne replikowanie danych z jednego serwera bazy danych Oracle na inny. Procesy te umożliwiają skonfigurowanie replikacji dwukierunkowej w celu zapewnienia wysokiej dostępności bazy danych, jeśli występuje przestój na poziomie strefy dostępności. Na powyższym diagramie proces wyodrębniania działa na tym samym serwerze, na którym znajduje się baza danych Oracle, podczas gdy proces pompowania i replikowania danych działa na osobnym serwerze w tej samej strefie dostępności. Proces replikacji służy do odbierania danych z bazy danych w innej strefie dostępności i przekazywania danych do bazy danych programu Oracle w jej strefie dostępności. Podobnie proces pompy danych wysyła dane wyodrębnione przez proces wyodrębniania do procesu replikacji w innej strefie dostępności. 

Gdy poprzedni diagram architektury przedstawia proces pompowania danych i replikacji skonfigurowany na osobnym serwerze, można skonfigurować wszystkie procesy programu Oracle GoldenGate na tym samym serwerze, w zależności od pojemności i użycia serwera. Zawsze zapoznaj się z raportem programu AWR i metrykami na platformie Azure, aby zrozumieć wzorzec użycia serwera.

Podczas konfigurowania wielokierunkowej replikacji Oracle GoldenGate w różnych strefach dostępności lub w różnych regionach ważne jest, aby zapewnić, że opóźnienie między różnymi składnikami jest akceptowalne dla aplikacji. Opóźnienie między strefami dostępności i regionami może się różnić i zależy od wielu czynników. Zaleca się skonfigurowanie testów wydajności między warstwą aplikacji a warstwą bazy danych w różnych strefach dostępności i/lub regionach, aby potwierdzić, że spełniają one wymagania dotyczące wydajności aplikacji.

Warstwę aplikacji można skonfigurować we własnej podsieci, a warstwa bazy danych może być oddzielona do własnej podsieci. Jeśli to możliwe, należy rozważyć użycie [usługi Azure Application Gateway](../../../application-gateway/overview.md) do równoważenia obciążenia ruchu między serwerami aplikacji. Usługa Azure Application Gateway to niezawodny moduł równoważenia obciążenia sieci Web. Zapewnia ona koligację sesji na podstawie plików cookie, która utrzymuje sesję użytkownika na tym samym serwerze, co minimalizuje konflikty w bazie danych. Alternatywy dla Application Gateway są [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) i [Traffic Manager platformy Azure](../../../traffic-manager/traffic-manager-overview.md).

### <a name="oracle-sharding"></a>Fragmentowania firmy Oracle

Fragmentowania jest wzorcem warstwy danych, który został wprowadzony w programie Oracle 12,2. Pozwala to na skalowanie w poziomie i skalowalność danych między niezależnymi bazami danych. Jest to architektura udostępniania, w której każda baza danych jest hostowana na dedykowanej maszynie wirtualnej, co zapewnia wysoką przepływność odczytu i zapisu oprócz odporności i zwiększonej dostępności. Ten wzorzec eliminuje pojedyncze punkty awarii, zapewnia izolację błędów i umożliwia uaktualnianie stopniowe bez przestojów. Przestój jednego fragmentuu lub awarii na poziomie centrum danych nie ma wpływu na wydajność ani dostępność innych fragmentów w innych centrach danych. 

Fragmentowania jest odpowiednie dla aplikacji OLTP o wysokiej przepływności, które nie mogą zapewnić żadnego przestoju. Wszystkie wiersze o tym samym kluczu fragmentowania są zawsze gwarantowane w tym samym fragmentu, co zwiększa wydajność zapewniającą wysoką spójność. Aplikacje korzystające z fragmentowania muszą mieć dobrze zdefiniowany model danych i strategię dystrybucji danych (spójny skrót, zakres, lista lub kompozyt), które przede wszystkim uzyskują dostęp do danych przy użyciu klucza fragmentowania (na przykład *customerId* lub *accountNum*). Fragmentowania umożliwia również przechowywanie określonych zestawów danych bliżej klientów końcowych, co pomaga sprostać wymaganiom dotyczącym wydajności i zgodności.

Zaleca się replikowanie fragmentów w celu zapewnienia wysokiej dostępności i odzyskiwania po awarii. Tę konfigurację można wykonać przy użyciu technologii Oracle, takich jak Oracle Data Guard lub Oracle GoldenGate. Jednostką replikacji może być fragmentu, część fragmentu lub Grupa fragmentów. Na dostępność bazy danych podzielonej na fragmenty nie ma wpływ awaria ani spowolnienie jednej lub więcej fragmentów. Aby zapewnić wysoką dostępność, fragmentów w stanie wstrzymania można umieścić w tej samej strefie dostępności, w której są umieszczone podstawowe fragmentów. W przypadku odzyskiwania po awarii fragmentów w stanie wstrzymania może znajdować się w innym regionie. Możesz również wdrożyć fragmentów w wielu regionach w celu obsługi ruchu w tych regionach. Przeczytaj więcej na temat konfigurowania wysokiej dostępności i replikacji bazy danych podzielonej na fragmenty w [dokumentacji programu Oracle fragmentowania](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html).

Oprogramowanie Oracle fragmentowania obejmuje głównie następujące składniki. Więcej informacji o tych składnikach można znaleźć w [dokumentacji programu Oracle fragmentowania](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html):

- **Katalog fragmentu** — baza danych Oracle o specjalnym przeznaczeniu, która jest trwałym magazynem dla wszystkich danych konfiguracji bazy danych fragmentu. Wszystkie zmiany konfiguracji, takie jak dodawanie lub usuwanie fragmentów, mapowanie danych i DDLs w bazie danych fragmentu są inicjowane w wykazie fragmentu. Wykaz fragmentu zawiera również kopię główną wszystkich zduplikowanych tabel w programie SDB. 

  W wykazie fragmentu są stosowane widoki z materiałami, które umożliwiają automatyczne Replikowanie zmian w zduplikowanych tabelach we wszystkich fragmentówach. Baza danych wykazu fragmentu działa również jako koordynator zapytań służący do przetwarzania zapytań wielofragmentuowych i zapytań, które nie określają klucza fragmentowania. 
  
  Zalecanym najlepszym rozwiązaniem jest użycie funkcji Oracle Data Guard w połączeniu ze strefami dostępności lub zestawami dostępności usługi fragmentu Catalog. Dostępność wykazu fragmentu nie ma wpływu na dostępność bazy danych podzielonej na fragmenty. Przestój w katalogu fragmentu ma wpływ tylko na operacje konserwacji i zapytania multishard w krótkim czasie, w którym kończy się praca awaryjna ochrony danych. Transakcje online są nadal kierowane i wykonywane przez SDB i nie mają wpływ na awarie katalogu.

- **Fragmentus** -Lightweight Services, które muszą zostać wdrożone w każdej strefie regionu/dostępności, w której znajduje się Twój fragmentów. Fragmentue są usługami globalnymi, które są wdrożone w kontekście programu Oracle fragmentowania. Aby zapewnić wysoką dostępność, zaleca się wdrożenie co najmniej jednego programu fragmentu Director w każdej strefie dostępności, w której znajduje się fragmentów. 

  Podczas początkowego łączenia się z bazą danych informacje o routingu są konfigurowane przez fragmentu dyrektora i są buforowane dla kolejnych żądań, pomijając dyrektora fragmentu. Po ustanowieniu sesji z fragmentu wszystkie zapytania SQL i DML są obsługiwane i wykonywane w zakresie danego fragmentu. Ten Routing jest szybki i jest używany dla wszystkich obciążeń OLTP, które wykonują transakcje wewnętrzne fragmentu. Zaleca się używanie bezpośredniego routingu dla wszystkich obciążeń OLTP, które wymagają najwyższej wydajności i dostępności. Pamięć podręczna routingu jest automatycznie odświeżana, gdy fragmentu stanie się niedostępna lub zmiany nastąpiły w topologii fragmentowania. 
  
  W przypadku routingu opartego na danych o wysokiej wydajności firma Oracle zaleca użycie puli połączeń podczas uzyskiwania dostępu do danych w bazie danych podzielonej na fragmenty. Pule połączeń Oracle, biblioteki specyficzne dla języka i sterowniki obsługują oprogramowanie Oracle fragmentowania. Więcej informacji można znaleźć w [dokumentacji programu Oracle fragmentowania](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9) .

- **Globalna usługa** — usługa globalna jest podobna do zwykłej usługi bazy danych. Oprócz wszystkich właściwości usługi bazy danych globalna usługa ma właściwości dla baz danych podzielonej na fragmenty, takich jak koligacja regionów między klientami a fragmentu i odpornością na opóźnienia replikacji. Do odczytu/zapisu danych do/z bazy danych podzielonej na fragmenty należy utworzyć tylko jedną usługę globalną. W przypadku korzystania z usługi Active Data Guard i konfigurowania replik tylko do odczytu fragmentów można utworzyć kolejną usługę gGobal dla obciążeń tylko do odczytu. Klient może używać tych usług globalnych do łączenia się z bazą danych.

- Bazy **danych fragmentu** — bazy danych fragmentu są bazami danych Oracle. Każda baza danych jest replikowana przy użyciu funkcji Oracle Data Guard w konfiguracji brokera z włączonym Fast-Start trybem failover (FSFO). Nie ma potrzeby konfigurowania trybu failover i replikacji w usłudze Data Guard dla każdego fragmentuu. Jest to automatycznie konfigurowane i wdrażane podczas tworzenia udostępnionej bazy danych. Jeśli określony fragmentu ulegnie awarii, udostępnianie oprogramowania Oracle automatycznie przejdzie w tryb failover w przypadku połączeń bazy danych z poziomu podstawowego do stanu wstrzymania.

Bazy danych Oracle podzielonej na fragmenty można wdrożyć i zarządzać nimi za pomocą dwóch interfejsów: graficznego interfejsu użytkownika kontroli chmury programu Oracle Enterprise Manager i/lub `GDSCTL` narzędzia wiersza polecenia. Można nawet monitorować różne fragmentów pod kątem dostępności i wydajności przy użyciu funkcji Cloud Control. `GDSCTL DEPLOY`Polecenie automatycznie tworzy fragmentów i odpowiadające im detektory. Ponadto to polecenie automatycznie wdraża konfigurację replikacji używaną na poziomie fragmentu o wysokiej dostępności określonej przez administratora.

Istnieją różne sposoby fragmentu bazy danych:

* Fragmentowania zarządzane przez system — automatycznie dystrybuuje w fragmentów przy użyciu partycjonowania
* Fragmentowania zdefiniowany przez użytkownika — umożliwia określenie mapowania danych do fragmentów, które dobrze sprawdzają się w przypadku spełnienia wymagań prawnych lub lokalizacji danych.
* Złożone fragmentowania — kombinacja fragmentowania zdefiniowanych przez system i zdefiniowane przez użytkownika dla różnych _shardspaces_
* Podpartycje tabeli — podobnie jak w przypadku zwykłej partycjonowanej tabeli.

Więcej informacji na temat różnych [metod fragmentowania](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) można znaleźć w dokumentacji firmy Oracle.

Baza danych podzielonej na fragmenty może wyglądać podobnie do pojedynczej bazy danych do aplikacji i deweloperów w przypadku migrowania z bazy danych innej niż podzielonej na fragmenty do bazy danych podzielonej na fragmenty, jednak należy uważnie zaplanować planowanie, aby określić, które tabele będą duplikowane, a podzielonej na fragmenty. 

Zduplikowane tabele są przechowywane we wszystkich fragmentów, a podzielonej na fragmenty tabele są dystrybuowane w różnych fragmentów. Zalecenie polega na zduplikowaniu niewielkich i wymiarowych tabel oraz rozproszeniu/fragmentu tabel faktów. Dane można ładować do bazy danych podzielonej na fragmenty przy użyciu katalogu fragmentu jako koordynator centralny lub przez pompę danych na każdym fragmentu. Przeczytaj więcej na temat [migrowania danych do bazy danych podzielonej na fragmenty](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) w dokumentacji firmy Oracle.

#### <a name="oracle-sharding-with-data-guard"></a>Oracle fragmentowania with Data Guard

Funkcja Oracle Data Guard może służyć do fragmentowania z metodami zarządzanymi przez system, zdefiniowanymi przez użytkownika i złożonymi fragmentowania.

Na poniższym diagramie przedstawiono architekturę referencyjną dla programu Oracle fragmentowania z funkcją Oracle Data Guard służącą do zapewnienia wysokiej dostępności każdego fragmentuu. Diagram architektury przedstawia _złożoną metodę fragmentowania_. Diagram architektury będzie prawdopodobnie różny dla aplikacji o różnych wymaganiach dotyczących lokalizacji danych, równoważenia obciążenia, wysokiej dostępności, odzyskiwania po awarii itp. i mogą używać innej metody dla fragmentowania. Program Oracle fragmentowania umożliwia spełnienie tych wymagań i skalowanie w poziomie i wydajnie, dostarczając te opcje. Podobną architekturę można nawet wdrożyć przy użyciu programu Oracle GoldenGate.

![Oracle Database fragmentowania przy użyciu stref dostępności z brokerem usługi Data Guard — FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

Chociaż fragmentowania zarządzane przez system to najłatwiej skonfigurować i zarządzać, zdefiniowany przez użytkownika fragmentowania lub złożony fragmentowania jest dobrze dostosowany do scenariuszy, w których dane i aplikacje są dystrybuowane geograficznie lub w scenariuszach, w których należy mieć kontrolę nad replikacją poszczególnych fragmentuów. 

W poprzedniej architekturze kompozyt fragmentowania jest używany do geograficznie dystrybuowania danych i skalowania w poziomie warstw aplikacji. Fragmentowania złożony jest kombinacją fragmentowania zdefiniowanych przez system i zdefiniowane przez użytkownika, co zapewnia korzyść obu tych metod. W poprzednim scenariuszu dane są najpierw podzielonej na fragmenty w wielu shardspacesach rozdzielonych według regionów. Następnie dane są dalej podzielone na partycje przez spójny skrót między wieloma fragmentów w shardspace. Każdy shardspace zawiera wiele shardgroups. Każdy shardgroup ma wiele fragmentów i jest "jednostkową" replikacją w tym przypadku. Każdy shardgroup zawiera wszystkie dane w shardspace. Shardgroups a1 i B1 są podstawowymi Shardgroups, podczas gdy Shardgroups a2 i B2 są wstrzymane. Możesz zdecydować, że indywidualna fragmentów będzie jednostką replikacji, a nie shardgroup.

W poprzedniej architekturze program GSM/fragmentu Director jest wdrażany w każdej strefie dostępności w celu zapewnienia wysokiej dostępności. Zalecenie polega na wdrożeniu co najmniej jednego dyrektora GSM/fragmentu dla każdego centrum danych/regionu. Ponadto wystąpienie serwera aplikacji jest wdrażane w każdej strefie dostępności, która zawiera shardgroup. Ta konfiguracja umożliwia aplikacji zachowanie opóźnień między serwerem aplikacji a bazą danych/shardgroup Low. Jeśli baza danych ulegnie awarii, serwer aplikacji znajdujący się w tej samej strefie co zapasowa baza danych może obsługiwać żądania, gdy następuje przejście roli bazy danych. Usługa Azure Application Gateway i dyrektor fragmentu śledzą żądania i opóźnienia odpowiedzi oraz żądają odpowiednio tras.

Z punktu widzenia aplikacji system klienta wysyła żądanie do usługi Azure Application Gateway (lub innych technologii równoważenia obciążenia na platformie Azure), która przekierowuje żądanie do regionu znajdującego się najbliżej klienta. Usługa Azure Application Gateway obsługuje również sesje programu Sticky, dlatego wszystkie żądania wysyłane z tego samego klienta są kierowane do tego samego serwera aplikacji. Serwer aplikacji używa puli połączeń w sterownikach dostępu do danych. Ta funkcja jest dostępna w sterownikach, takich jak JDBC, ODP.NET, OCI itp. Sterowniki mogą rozpoznać klucze fragmentowania określone jako część żądania. [Uniwersalna pula połączeń Oracle (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) dla klientów JDBC może umożliwić klientom aplikacji innych niż Oracle korzystanie z programu Oracle fragmentowania. 

Podczas wstępnego żądania serwer aplikacji łączy się z fragmentu Director w jego regionie, aby uzyskać informacje o routingu dla fragmentu, do których żądanie musi być kierowane. W oparciu o przekazaną fragmentowania klucz, Dyrektor kieruje serwer aplikacji do odpowiednich fragmentu. Serwer aplikacji buforuje te informacje, tworząc mapę i dla kolejnych żądań, pomija fragmentu Director i kieruje żądania bezpośrednio do fragmentu.

#### <a name="oracle-sharding-with-goldengate"></a>Oracle fragmentowania z GoldenGate

Na poniższym diagramie przedstawiono architekturę referencyjną dla programu Oracle fragmentowania z oprogramowaniem Oracle GoldenGate w przypadku wysokiej dostępności każdego fragmentu. W przeciwieństwie do poprzedniej architektury, ta architektura przedstawia wysoką dostępność tylko w ramach jednego regionu świadczenia usługi Azure (wiele stref dostępności). Jedna z nich może wdrożyć bazę danych podzielonej na fragmenty o wysokiej dostępności w wielu regionach (podobnie jak w poprzednim przykładzie) przy użyciu programu Oracle GoldenGate.

![Oracle Database fragmentowania przy użyciu stref dostępności z GoldenGate](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

Poprzednia architektura referencyjna używa metody fragmentowania _zarządzanej przez system_ w celu fragmentu danych. Ponieważ replikacja Oracle GoldenGate odbywa się na poziomie fragmentu, połowa danych replikowanych do jednej fragmentu może być replikowana do innego fragmentu. Druga połowa może być replikowana do innego fragmentu. 

Sposób, w jaki dane są replikowane, zależy od współczynnika replikacji. Współczynnik replikacji równy 2 obejmuje dwie kopie każdego fragmentu danych na trzech fragmentówach w shardgroup. Podobnie w przypadku współczynnika replikacji o wartości 3 i trzech fragmentów w shardgroup wszystkie dane w poszczególnych fragmentuach zostaną zreplikowane do wszystkich innych fragmentu w shardgroup. Każdy fragmentu w shardgroup może mieć inny współczynnik replikacji. Ta konfiguracja ułatwia Definiowanie projektu wysokiej dostępności i odzyskiwania po awarii w shardgroup i wielu shardgroupsach.

W poprzedniej architekturze shardgroup a i shardgroup B zawierają te same dane, ale znajdują się w różnych strefach dostępności. Jeśli zarówno shardgroup a, jak i shardgroup B mają ten sam współczynnik replikacji równy 3, każdy wiersz/fragment tabeli podzielonej na fragmenty będzie replikowany 6 razy przez dwa shardgroups. Jeśli shardgroup A ma współczynnik replikacji 3, a shardgroup B ma współczynnik replikacji równy 2, każdy wiersz/fragment zostanie zreplikowany 5 razy przez dwa shardgroups.

Ta konfiguracja zapobiega utracie danych, jeśli wystąpi awaria poziomu wystąpienia lub strefy dostępności. Warstwa aplikacji może odczytywać i zapisywać dane w każdym fragmentu. Aby zminimalizować konflikty, program Oracle fragmentowania wyznacza "fragment główny" dla każdego zakresu wartości skrótu. Ta funkcja zapewnia, że żądania zapisu dla określonego fragmentu są kierowane do odpowiedniego fragmentu. Ponadto program Oracle GoldenGate zapewnia automatyczne wykrywanie konfliktów i rozwiązywanie problemów w celu obsługi wszelkich konfliktów, które mogą wystąpić. Aby uzyskać więcej informacji i ograniczeń implementacji GoldenGate z programem Oracle fragmentowania, zobacz dokumentację firmy Oracle dotyczącą korzystania z programu [Oracle GoldenGate z bazą danych podzielonej na fragmenty](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72).

W poprzedniej architekturze program GSM/fragmentu Director jest wdrażany w każdej strefie dostępności w celu zapewnienia wysokiej dostępności. Zalecenie polega na wdrożeniu co najmniej jednego dyrektora GSM/fragmentu na centrum danych lub w regionie. Ponadto wystąpienie serwera aplikacji jest wdrażane w każdej strefie dostępności, która zawiera shardgroup. Ta konfiguracja umożliwia aplikacji zachowanie opóźnień między serwerem aplikacji a bazą danych/shardgroup Low. Jeśli baza danych ulegnie awarii, serwer aplikacji znajdujący się w tej samej strefie co zapasowa baza danych może obsługiwać żądania po przejściu roli bazy danych. Usługa Azure Application Gateway i dyrektor fragmentu śledzą żądania i opóźnienia odpowiedzi oraz żądają odpowiednio tras.

Z punktu widzenia aplikacji system klienta wysyła żądanie do usługi Azure Application Gateway (lub innych technologii równoważenia obciążenia na platformie Azure), która przekierowuje żądanie do regionu znajdującego się najbliżej klienta. Usługa Azure Application Gateway obsługuje również sesje programu Sticky, dlatego wszystkie żądania wysyłane z tego samego klienta są kierowane do tego samego serwera aplikacji. Serwer aplikacji używa puli połączeń w sterownikach dostępu do danych. Ta funkcja jest dostępna w sterownikach, takich jak JDBC, ODP.NET, OCI itp. Sterowniki mogą rozpoznać klucze fragmentowania określone jako część żądania. [Uniwersalna pula połączeń Oracle (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) dla klientów JDBC może umożliwić klientom aplikacji innych niż Oracle korzystanie z programu Oracle fragmentowania. 

Podczas wstępnego żądania serwer aplikacji łączy się z fragmentu Director w jego regionie, aby uzyskać informacje o routingu dla fragmentu, do których żądanie musi być kierowane. W oparciu o przekazaną fragmentowania klucz, Dyrektor kieruje serwer aplikacji do odpowiednich fragmentu. Serwer aplikacji buforuje te informacje, tworząc mapę i dla kolejnych żądań, pomija fragmentu Director i kieruje żądania bezpośrednio do fragmentu.

## <a name="patching-and-maintenance"></a>Stosowanie poprawek i konserwacji

W przypadku wdrażania obciążeń Oracle na platformie Azure firma Microsoft bierze pod uwagę wszystkie poprawki na poziomie systemu operacyjnego hosta. Wszystkie planowane czynności konserwacyjne na poziomie systemu operacyjnego są przekazywane klientom z wyprzedzeniem, aby umożliwić klientowi zaplanowaną konserwację. Dwa serwery z dwóch różnych Strefy dostępności nigdy nie są jednocześnie poprawiane. Zobacz [Zarządzanie dostępnością maszyn wirtualnych](../../manage-availability.md) , aby uzyskać więcej szczegółowych informacji na temat konserwacji maszyn wirtualnych i stosowania poprawek. 

Stosowanie poprawek do systemu operacyjnego maszyny wirtualnej może być zautomatyzowane przy użyciu [Azure Automation Update Management](../../../automation/update-management/overview.md). Stosowanie poprawek i konserwacji bazy danych Oracle może być zautomatyzowane i zaplanowane przy użyciu [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) lub [Azure Automation Update Management](../../../automation/update-management/overview.md) w celu zminimalizowania przestojów. Zapoznaj się z [ciągłym dostarczaniem oraz wdrożeniami Blue/Green](/azure/devops/learn/what-is-continuous-delivery) , aby zrozumieć, jak można go używać w kontekście baz danych Oracle.

## <a name="architecture-and-design-considerations"></a>Zagadnienia dotyczące architektury i projektowania

- Rozważ użycie [maszyny wirtualnej zoptymalizowanej pod kątem pamięci](../../sizes-memory.md) z [ograniczonym rdzeniem procesorów wirtualnych vCPU](../../../virtual-machines/constrained-vcpu.md) dla maszyny wirtualnej Oracle Database, aby zaoszczędzić koszty licencjonowania i zwiększyć wydajność. Używaj wielu dysków Premium lub Ultra (Managed disks) w celu zapewnienia wydajności i dostępności.
- W przypadku korzystania z dysków zarządzanych nazwa dysku/urządzenia może ulec zmianie przy ponownych uruchomieniach. Zaleca się użycie identyfikatora UUID urządzenia zamiast nazwy, aby upewnić się, że instalacje są zachowywane w ramach ponownych uruchomień. Więcej informacji można znaleźć [tutaj](/previous-versions/azure/virtual-machines/linux/configure-raid#add-the-new-file-system-to-etcfstab).
- Użyj stref dostępności, aby uzyskać wysoką dostępność w regionie.
- Rozważ użycie dysków Ultra disks (jeśli jest dostępna) lub Premium dla bazy danych Oracle.
- Rozważ skonfigurowanie w stanie wstrzymania bazy danych Oracle w innym regionie świadczenia usługi Azure przy użyciu usługi Oracle Data Guard.
- Rozważ użycie [grup umieszczania w sąsiedztwie](../../../virtual-machines/linux/co-location.md#proximity-placement-groups) , aby zmniejszyć opóźnienie między aplikacją a warstwą bazy danych.
- Skonfiguruj program [Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/) do zarządzania, monitorowania i rejestrowania.
- Rozważ użycie funkcji automatycznego zarządzania magazynem (ASM) w programie Oracle w celu uproszczenia zarządzania magazynem w bazie danych.
- Użyj [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) , aby zarządzać poprawkami i aktualizacjami do bazy danych bez żadnego przestoju.
- Dostrajaj kod aplikacji w celu dodania wzorców chmurowych, takich jak [wzorzec ponawiania prób](/azure/architecture/patterns/retry), [wzorzec wyłącznika](/azure/architecture/patterns/circuit-breaker)i inne wzorce zdefiniowane w [przewodniku wzorców projektowych w chmurze](/azure/architecture/patterns/) , które mogą pomóc w zwiększeniu odporności aplikacji.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami referencyjnymi firmy Oracle, które są stosowane do Twojego scenariusza.

- [Wprowadzenie do funkcji Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Pojęcia związane z brokerem funkcji Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [Konfigurowanie usługi Oracle GoldenGate pod kątem Active-Active wysokiej dostępności](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Omówienie programu Oracle fragmentowania](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Usługa Oracle Active Data Guard nie synchronizuje zerowej utraty danych w dowolnej odległości](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
