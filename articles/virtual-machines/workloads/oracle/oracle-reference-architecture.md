---
title: Architektury referencyjne dla baz danych Oracle na platformie Azure | Dokumenty firmy Microsoft
description: Odwołuje się do architektury uruchamiania baz danych Oracle Database Enterprise Edition na maszynach wirtualnych platformy Microsoft Azure.
services: virtual-machines-linux
author: BorisB2015
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2019
ms.author: borisb
ms.custom: ''
ms.openlocfilehash: bbb6665299ce9b6521eeb8801d8621dfbdc17f4a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683487"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Architektury referencyjne dla oracle database enterprise edition na platformie Azure

W tym przewodniku opisano informacje dotyczące wdrażania bazy danych Oracle o wysokiej dostępności na platformie Azure. Ponadto w tym przewodniku zapoznaje się z zagadnieniami odzyskiwania po awarii. Te architektury zostały utworzone na podstawie wdrożeń klientów. Ten przewodnik dotyczy tylko Oracle Database Enterprise Edition.

Jeśli chcesz dowiedzieć się więcej na temat maksymalizacji wydajności bazy danych Oracle, zobacz [Architect a Oracle DB](oracle-design.md).

## <a name="assumptions"></a>Założenia

- Rozumiesz różne pojęcia platformy Azure, takie jak [strefy dostępności](../../../availability-zones/az-overview.md)
- Korzystasz z oracle database enterprise edition 12c lub nowszego
- Podczas korzystania z rozwiązań w tym artykule użytkownik jest świadomy i potwierdzasz implikacje licencjonowania.

## <a name="high-availability-for-oracle-databases"></a>Wysoka dostępność baz danych Oracle

Osiągnięcie wysokiej dostępności w chmurze jest ważną częścią planowania i projektowania każdej organizacji. Platforma Microsoft Azure oferuje [strefy dostępności](../../../availability-zones/az-overview.md) i zestawy dostępności (do użycia w regionach, w których strefy dostępności są niedostępne). Dowiedz się więcej o [zarządzaniu dostępnością maszyn wirtualnych](../../../virtual-machines/linux/manage-availability.md) do projektowania dla chmury.

Oprócz natywnych dla chmury narzędzi i ofert Oracle oferuje rozwiązania zapewniające wysoką dostępność, takie jak [Oracle Data Guard,](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7) [Data Guard with FSFO,](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html) [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)i [GoldenGate,](https://www.oracle.com/middleware/technologies/goldengate.html) które można skonfigurować na platformie Azure. W tym przewodniku opisano architektury referencyjne dla każdego z tych rozwiązań.

Na koniec podczas migracji lub tworzenia aplikacji dla chmury, ważne jest, aby dostosować kod aplikacji, aby dodać wzorce natywne dla chmury, takie jak [wzorzec ponawiania próby](https://docs.microsoft.com/azure/architecture/patterns/retry) i [wzorzec wyłącznika.](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) Dodatkowe wzorce zdefiniowane w [przewodniku wzorce projektowania chmury](https://docs.microsoft.com/azure/architecture/patterns/) może pomóc aplikacji być bardziej odporne.

### <a name="oracle-rac-in-the-cloud"></a>Oracle RAC w chmurze

Oracle Real Application Cluster (RAC) to rozwiązanie firmy Oracle, które pomaga klientom osiągnąć wysoką przepustowość dzięki wielu wystąpieniom dostępu do jednego magazynu bazy danych (wzorzec architektury shared-all). Oracle RAC może być również używany do wysokiej dostępności w środowisku lokalnym, sam Oracle RAC nie może być używany do wysokiej dostępności w chmurze, ponieważ chroni tylko przed awariami na poziomie wystąpienia, a nie przed awariami na poziomie stelaża lub centrum danych. Z tego powodu Oracle zaleca korzystanie z Oracle Data Guard z bazą danych (pojedyncze wystąpienie lub RAC) w celu zapewnienia wysokiej dostępności. Klienci zazwyczaj wymagają wysokiej umowy SLA do uruchamiania aplikacji o znaczeniu krytycznym. Oracle RAC nie jest obecnie certyfikowany ani obsługiwany przez oracle na platformie Azure. Jednak platforma Azure oferuje funkcje, takie jak platforma Azure oferuje strefy dostępności i planowane okna konserwacji, aby pomóc chronić przed awariami na poziomie wystąpienia. Oprócz tego klienci mogą korzystać z technologii takich jak Oracle Data Guard, Oracle GoldenGate i Oracle Sharding, aby uzyskać wysoką wydajność i resiliancję, chroniąc swoje bazy danych przed awariami na poziomie stelaża, a także na poziomie centrum danych i geopolitycznych.

Podczas uruchamiania baz danych Oracle w wielu [strefach dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview) w połączeniu z Oracle Data Guard lub GoldenGate klienci mogą uzyskać umowę SLA o dostępności na 99,99%. W regionach platformy Azure, w których strefy dostępności nie są jeszcze obecne, klienci mogą korzystać z [zestawów dostępności](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) i osiągnąć umowę SLA o dostępności na 99,95%.

>UWAGA: Możesz mieć cel czasu pracy, który jest znacznie wyższy niż umowa SLA dotycząca czasu pracy bez przestojów dostarczona przez firmę Microsoft.

## <a name="disaster-recovery-for-oracle-databases"></a>Odzyskiwanie po awarii dla baz danych Oracle

Podczas hostowania aplikacji o znaczeniu krytycznym w chmurze ważne jest, aby projektować dla wysokiej dostępności i odzyskiwania po awarii.

W przypadku Oracle Database Enterprise Edition oracle data guard jest użyteczną funkcją odzyskiwania po awarii. Można skonfigurować wystąpienie rezerwowej bazy danych w [sparowanym regionie platformy Azure](/azure/best-practices-availability-paired-regions) i skonfigurować tryb failover usługi Data Guard w celu odzyskiwania po awarii. W przypadku zerowej utraty danych zaleca się wdrożenie wystąpienia Oracle Data Guard Far Sync oprócz aktywnej ochrony danych. 

Należy rozważyć skonfigurowanie wystąpienia data guard far sync w innej strefie dostępności niż podstawowa baza danych Oracle, jeśli aplikacja zezwala na opóźnienie (wymagane jest dokładne testowanie). Użyj trybu **maksymalnej dostępności,** aby skonfigurować synchroniczne transport plików ponawianych do wystąpienia Far Sync. Pliki te są następnie przesyłane asynchronicznie do rezerwowej bazy danych. 

Jeśli aplikacja nie zezwala na utratę wydajności podczas konfigurowania wystąpienia Far Sync w innej strefie dostępności w trybie **maksymalnej dostępności** (synchroniczowy), można skonfigurować wystąpienie Dalekiej Synchronizacji w tej samej strefie dostępności co podstawowa baza danych. Aby uzyskać dodatkową dostępność, należy rozważyć skonfigurowanie wielu wystąpień Far Sync w pobliżu podstawowej bazy danych i co najmniej jednego wystąpienia w pobliżu bazy danych w trybie wstrzymania (jeśli rola zostanie przesunie). Przeczytaj więcej o Oracle Data Guard Far Sync w tym [białym papierie Oracle Active Data Guard Far Sync](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf).

Podczas korzystania z baz danych Oracle Standard Edition istnieją rozwiązania isv, takie jak DBVisit Standby, które umożliwiają skonfigurowanie wysokiej dostępności i odzyskiwania po awarii.

## <a name="reference-architectures"></a>Architektury referencyjne

### <a name="oracle-data-guard"></a>Oracle Data Guard

Oracle Data Guard zapewnia wysoką dostępność, ochronę danych i odzyskiwanie danych po awarii dla danych korporacyjnych. Usługa Data Guard przechowuje bazy danych w trybie gotowości jako spójne transakcyjnie kopie podstawowej bazy danych. W zależności od odległości między bazami danych podstawowych i pomocniczych oraz tolerancji aplikacji dla opóźnienia można skonfigurować replikację synchronikową lub asynchronizacyjną. Następnie jeśli podstawowa baza danych jest niedostępna z powodu planowanej lub nieplanowanej awarii, usługa Data Guard może przełączyć dowolną bazę danych rezerwy na rolę podstawową, minimalizując przestoje. 

Korzystając z oracle data guard, można również otworzyć dodatkową bazę danych w celach tylko do odczytu. Ta konfiguracja nosi nazwę Active Data Guard. Oracle Database 12c wprowadzono funkcję o nazwie Data Guard Far Sync Instance. To wystąpienie umożliwia skonfigurowanie konfiguracji bazy danych Oracle bez utraty danych bez konieczności naruszania wydajności.

> [!NOTE]
> Active Data Guard wymaga dodatkowej licencji. Ta licencja jest również wymagana do korzystania z funkcji Far Sync. Skontaktuj się z przedstawicielem Oracle, aby omówić implikacje związane z licencjonowaniem.

#### <a name="oracle-data-guard-with-fsfo"></a>Oracle Data Guard z FSFO
Oracle Data Guard z funkcją szybkiego uruchamiania trybu failover (FSFO) może zapewnić dodatkową odporność, konfigurując brokera na oddzielnym komputerze. Broker data guard i pomocnicza baza danych uruchamiają obserwatora i obserwują podstawową bazę danych dla przestojów. Pozwala to również na nadmiarowość w konfiguracji obserwatora ochrony danych. 

Dzięki oracle database w wersji 12.2 lub nowszej można również skonfigurować wielu obserwatorów za pomocą jednej konfiguracji brokera Oracle Data Guard. Ta konfiguracja zapewnia dodatkową dostępność, w przypadku jednego obserwatora i dodatkowego czasu pracy bazy danych. Data Guard Broker jest lekki i może być obsługiwany na stosunkowo małej maszynie wirtualnej. Aby dowiedzieć się więcej o brokerze data guard i jego zaletach, zapoznaj się z [dokumentacją Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html) na ten temat.

Poniższy diagram jest zalecaną architekturą do korzystania z oracle data guard na platformie Azure ze strefami dostępności. Ta architektura umożliwia uzyskanie umowy SLA o umowy SLA o czasie pracy maszyny Wirtualnej na 99,99%.

![Baza danych Oracle przy użyciu stref dostępności z Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

Na poprzednim diagramie system kliencki uzyskuje dostęp do niestandardowej aplikacji z zapleczem Oracle za pośrednictwem sieci Web. Frontend sieci web jest skonfigurowany w moduł równoważenia obciążenia. Frontend sieci web wywołuje odpowiedni serwer aplikacji do obsługi pracy. Serwer aplikacji wysyła zapytanie do podstawowej bazy danych Oracle. Baza danych Oracle została skonfigurowana przy użyciu [maszyny wirtualnej zoptymalizowanej pod kątem pamięci](../../../virtual-machines/windows/sizes-memory.md) hiperwątkowo z [ograniczonymi podstawowymi procesorami wirtualnymi,](../../../virtual-machines/windows/constrained-vcpu.md) aby zaoszczędzić na kosztach licencjonowania i zmaksymalizować wydajność. Wiele dysków premium lub ultra (dysków zarządzanych) jest używanych do wydajności i wysokiej dostępności.

Bazy danych Oracle są umieszczane w wielu strefach dostępności w celu zapewnienia wysokiej dostępności. Każda strefa składa się z jednego lub więcej centrów danych wyposażonych w niezależne zasilanie, chłodzenie i sieć. Aby zapewnić odporność, we wszystkich włączonych regionach są skonfigurowane co najmniej trzy oddzielne strefy. Fizyczne oddzielenie stref dostępności w regionie chroni dane przed awariami centrum danych. Ponadto dwóch obserwatorów FSFO są skonfigurowane w dwóch strefach dostępności do inicjowania i pracy awaryjnej bazy danych do pomocniczego, gdy wystąpi awaria. 

Można skonfigurować dodatkowe bazy danych obserwatorów i/lub rezerwowych w innej strefie dostępności (AZ 1, w tym przypadku) niż strefa pokazana w poprzedniej architekturze. Wreszcie bazy danych Oracle są monitorowane pod kątem czasu pracy bez przestojów i wydajności przez Oracle Enterprise Manager (OEM). OEM pozwala również na generowanie różnych raportów wydajności i użycia.

W regionach, w których strefy dostępności nie są obsługiwane, można użyć zestawów dostępności do wdrażania bazy danych Oracle Database w sposób wysoce dostępny. Zestawy dostępności umożliwiają osiągnięcie czasu pracy maszyny Wirtualnej na 99,95%. Poniższy diagram jest architekturą referencyjną tego zastosowania:

![Baza danych Oracle przy użyciu zestawów dostępności z Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * Maszyna wirtualna Oracle Enterprise Manager nie musi być umieszczana w zestawie dostępności, ponieważ jest tylko jedno wystąpienie wdrażania oem.
> * Dyski ultra nie są obecnie obsługiwane w konfiguracji zestawu dostępności.

#### <a name="oracle-data-guard-far-sync"></a>Oracle Data Guard Far Sync

Oracle Data Guard Far Sync zapewnia zero możliwości ochrony przed utratą danych dla baz danych Oracle Databases. Ta funkcja umożliwia ochronę przed utratą danych w przypadku awarii komputera bazy danych. Oracle Data Guard Far Sync musi być zainstalowany na osobnej maszynie wirtualnej. Far Sync to lekkie wystąpienie Oracle, które ma tylko plik kontrolny, plik hasła, spfile i dzienniki wstrzymania. Nie ma plików danych ani plików dziennika rego. 

Aby uzyskać ochronę przed utratą danych, musi istnieć synchroniczowa komunikacja między podstawową bazą danych a wystąpieniem Far Sync. Far Sync wystąpienie odbiera ponawiać z podstawowego w sposób synchroniczne i przekazuje go natychmiast do wszystkich baz danych w trybie gotowości w sposób asynchroniczne. Ta konfiguracja zmniejsza również obciążenie podstawowej bazy danych, ponieważ musi tylko wysłać ponajem do wystąpienia Far Sync, a nie do wszystkich rezerwowych baz danych. Jeśli wystąpienie Dalekiej Synchronizacji nie powiedzie się, usługa Data Guard automatycznie używa transportu asynchronialnego do pomocniczej bazy danych z podstawowej bazy danych w celu utrzymania ochrony przed utratą danych w pobliżu zera. Aby zwiększyć odporność, klienci mogą wdrażać wiele wystąpień far sync na każde wystąpienie bazy danych (podstawowe i pomocnicze).

Poniższy diagram to architektura o wysokiej dostępności wykorzystująca oracle data guard far sync:

![Baza danych Oracle z wykorzystaniem stref dostępności z Data Guard Far Sync & Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

W poprzedniej architekturze istnieje wystąpienie Dalekiej Synchronizacji wdrożone w tej samej strefie dostępności co wystąpienie bazy danych w celu zmniejszenia opóźnienia między nimi. W przypadkach, gdy aplikacja jest wrażliwa na opóźnienia, należy rozważyć wdrożenie bazy danych i wystąpienia far sync lub wystąpień w [grupie miejsc docelowych zbliżeniowych](../../../virtual-machines/linux/proximity-placement-groups.md).

Poniższy diagram to architektura wykorzystująca oracle data guard FSFO i Far Sync w celu osiągnięcia wysokiej dostępności i odzyskiwania po awarii:

![Baza danych Oracle database z wykorzystaniem stref dostępności do odzyskiwania po awarii dzięki Brokerowi & Data Guard Far Sync - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

GoldenGate umożliwia wymianę i manipulowanie danymi na poziomie transakcji między wieloma, heterogenizogennymi platformami w całym przedsiębiorstwie. Przenosi zatwierdzone transakcje z integralnością transakcji i minimalnym obciążeniem istniejącej infrastruktury. Jego modułowa architektura zapewnia elastyczność wyodrębniania i replikowania wybranych rekordów danych, zmian transakcyjnych i zmian w DDL (język definicji danych) w różnych topologiach.

Oracle GoldenGate umożliwia skonfigurowanie bazy danych pod kątem wysokiej dostępności, zapewniając replikację dwukierunkową. Dzięki temu można skonfigurować konfigurację **wielo-wzorcową** lub **aktywną.** Poniższy diagram jest zalecaną architekturą dla instalacji aktywnych aktywnych i aktywnych oracle goldengate na platformie Azure. W następującej architekturze baza danych Oracle została skonfigurowana przy użyciu [maszyny wirtualnej zoptymalizowanej pod kątem pamięci](../../../virtual-machines/windows/sizes-memory.md) hiperwątkowo z [ograniczonymi podstawowymi procesorami wirtualnymi,](../../../virtual-machines/windows/constrained-vcpu.md) aby zaoszczędzić na kosztach licencjonowania i zmaksymalizować wydajność. Wiele dysków premium lub ultra (dysków zarządzanych) jest używanych do wydajności i dostępności.

![Baza danych Oracle przy użyciu stref dostępności z Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> Podobną architekturę można skonfigurować przy użyciu zestawów dostępności w regionach, w których strefy dostępności nie są obecnie dostępne.

Oracle GoldenGate ma procesy, takie jak Wyodrębnianie, Pompy i Replika, które pomagają asynchronicznie replikować dane z jednego serwera bazy danych Oracle do drugiego. Te procesy umożliwiają skonfigurowanie replikacji dwukierunkowej w celu zapewnienia wysokiej dostępności bazy danych, jeśli istnieje przestoje na poziomie strefy dostępności. Na poprzednim diagramie proces wyodrębniania jest uruchamiany na tym samym serwerze co baza danych Oracle, podczas gdy procesy pompy danych i replikacji są uruchamiane na oddzielnym serwerze w tej samej strefie dostępności. Proces Replikacja służy do odbierania danych z bazy danych w innej strefie dostępności i zatwierdzania danych do bazy danych Oracle w strefie dostępności. Podobnie proces pompy danych wysyła dane, które zostały wyodrębnione przez proces wyodrębniania do procesu Replikacja w innej strefie dostępności. 

Podczas gdy na poprzednim diagramie architektury przedstawiono proces pompy danych i replikacji skonfigurowany na oddzielnym serwerze, można skonfigurować wszystkie procesy Oracle GoldenGate na tym samym serwerze, w oparciu o pojemność i użycie serwera. Zawsze sprawdzaj raport AWR i metryki na platformie Azure, aby zrozumieć wzorzec użycia serwera.

Podczas konfigurowania replikacji dwukierunkowej Oracle GoldenGate w różnych strefach dostępności lub w różnych regionach należy upewnić się, że opóźnienie między różnymi składnikami jest dopuszczalne dla aplikacji. Opóźnienie między strefami dostępności i regionów może się różnić i zależy od wielu czynników. Zaleca się skonfigurowanie testów wydajności między warstwą aplikacji a warstwą bazy danych w różnych strefach dostępności i/lub regionach, aby potwierdzić, że spełnia wymagania dotyczące wydajności aplikacji.

Warstwę aplikacji można skonfigurować we własnej podsieci, a warstwę bazy danych można podzielić na własną podsieć. Jeśli to możliwe, należy rozważyć użycie [usługi Azure Application Gateway](../../../application-gateway/overview.md) do równoważenia obciążenia ruchu między serwerami aplikacji. Usługa Azure Application Gateway to niezawodny moduł równoważenia obciążenia ruchu internetowego. Zapewnia koligację sesji opartą na plikach cookie, która utrzymuje sesję użytkownika na tym samym serwerze, minimalizując w ten sposób konflikty w bazie danych. Alternatywami dla bramy aplikacji są [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) i [Azure Traffic Manager.](../../../traffic-manager/traffic-manager-overview.md)

### <a name="oracle-sharding"></a>Dzielenie na fragmenty Oracle

Dzielenie na fragmenty to wzorzec warstwy danych, który został wprowadzony w oracle 12.2. Umożliwia partycjonowanie poziome i skalowanie danych w niezależnych bazach danych. Jest to architektura share-nothing, w której każda baza danych jest hostowana na dedykowanej maszynie wirtualnej, co umożliwia wysoką przepływność odczytu i zapisu oprócz odporności i zwiększonej dostępności. Ten wzorzec eliminuje pojedyncze punkty awarii, zapewnia izolację błędów i umożliwia uaktualnianie stopniowe bez przestojów. Czas przestoju jednego fragmentu lub awarii na poziomie centrum danych nie wpływa na wydajność ani dostępność innych fragmentów w innych centrach danych. 

Dzielenie na fragmenty jest odpowiednie dla aplikacji OLTP o wysokiej przepływności, które nie mogą sobie pozwolić na przestoje. Wszystkie wiersze z tym samym kluczem dzielenia na fragmenty są zawsze gwarantowane, aby być na tym samym niezależnuzyku, zwiększając w ten sposób wydajność zapewniając wysoką spójność. Aplikacje korzystające z dzielenia na fragmenty muszą mieć dobrze zdefiniowany model danych i strategię dystrybucji danych (spójny skrót, zakres, lista lub kompozyt), która przede wszystkim uzyskuje dostęp do danych przy użyciu klucza dzielenia na fragmenty (na przykład *customerId* lub *accountNum*). Dzielenia na fragmenty umożliwia również przechowywanie określonych zestawów danych bliżej klientów końcowych, co pomaga spełnić wymagania dotyczące wydajności i zgodności.

Zaleca się replikowanie fragmentów w celu zapewnienia wysokiej dostępności i odzyskiwania po awarii. Tę konfigurację można wykonać za pomocą technologii Oracle, takich jak Oracle Data Guard lub Oracle GoldenGate. Jednostką replikacji może być fragment, część niezależnego fragmentu lub grupa fragmentów. Dostępność podzielonej bazy danych nie ma wpływu na awarię lub spowolnienie jednego lub więcej fragmentów. Aby uzyskać wysoką dostępność, fragmenty rezerw można umieścić w tej samej strefie dostępności, w której są umieszczane fragmenty podstawowe. W przypadku odzyskiwania po awarii fragmenty wstrzymania mogą znajdować się w innym regionie. Można również wdrożyć fragmenty w wielu regionach do obsługi ruchu w tych regionach. Dowiedz się więcej o konfigurowaniu wysokiej dostępności i replikacji podzielonej bazy danych w [dokumentacji oracle sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html).

Oracle Sharding składa się głównie z następujących składników. Więcej informacji na temat tych składników można znaleźć w [dokumentacji Oracle Sharding:](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)

- **Katalog niezależnego fragmentu** — specjalna baza danych Oracle, która jest magazynem trwałym dla wszystkich danych konfiguracyjnych bazy danych niezależnego fragmentu. Wszystkie zmiany konfiguracji, takie jak dodawanie lub usuwanie fragmentów, mapowanie danych i DDLs w bazie danych niezależnego fragmentu są inicjowane w katalogu niezależnego fragmentu. Katalog niezależnego fragmentu zawiera również główną kopię wszystkich zduplikowanych tabel w pliku SDB. 

  Katalog niezależnego fragmentu używa zmaterializowanych widoków do automatycznego replikowania zmian w zduplikowanych tabelach we wszystkich fragmentach. Baza danych wykazu niezależnego fragmentu działa również jako koordynator kwerend używany do przetwarzania zapytań i kwerend wielu fragmentów, które nie określają klucza dzielenia na fragmenty. 
  
  Zalecanym najlepszym rozwiązaniem jest korzystanie z oracle data guard w połączeniu ze strefami dostępności lub zestawami dostępności dla wykazu niezależnego fragmentu o wysokiej dostępności. Dostępność katalogu niezależnego fragmentu nie ma wpływu na dostępność podzielonej bazy danych. Przestoje w katalogu niezależnego fragmentu tylko wpływa na operacje konserwacji i wiele zapytań multishard w krótkim okresie, który kończy pracę awaryjną usługi Data Guard. Transakcje online są nadal kierowane i wykonywane przez SDB i nie są dotknięte awarią katalogu.

- **Dyrektorzy niezależnego fragmentu** — lekkie usługi, które muszą zostać wdrożone w każdej strefie regionu/dostępności, w której znajdują się fragmenty. Shard Directors to globalni menedżerowie usług wdrożoni w kontekście oracle sharding. Aby uzyskać wysoką dostępność, zaleca się wdrożenie co najmniej jednego dyrektora niezależnego fragmentu w każdej strefie dostępności, w której istnieją fragmenty. 

  Podczas łączenia się z bazą danych początkowo informacje o routingu jest skonfigurowany przez dyrektora niezależnego fragmentu i jest buforowany dla kolejnych żądań, z pominięciem dyrektora niezależnego fragmentu. Po ustanowieniu sesji za pomocą niezależnego fragmentu wszystkie zapytania SQL i listy DMLs są obsługiwane i wykonywane w zakresie danego niezależnego fragmentu. Ta routing jest szybki i jest używany dla wszystkich obciążeń OLTP, które wykonują transakcje wewnątrz-shard. Zaleca się używanie routingu bezpośredniego dla wszystkich obciążeń OLTP, które wymagają najwyższej wydajności i dostępności. Pamięć podręczna routingu jest automatycznie odświeżana, gdy fragment stanie się niedostępny lub zmiany występują w topologii dzielenia na fragmenty. 
  
  Aby uzyskać routing zależny od danych o wysokiej wydajności, Oracle zaleca korzystanie z puli połączeń podczas uzyskiwania dostępu do danych w podzielonej bazie danych. Pule połączeń Oracle, biblioteki specyficzne dla języka i sterowniki obsługują oracle sharding. Więcej informacji można znaleźć w [dokumentacji oracle sharding.](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9)

- **Usługa globalna** — usługa globalna jest podobna do usługi bazy danych. Oprócz wszystkich właściwości usługi bazy danych usługa globalna ma właściwości podzielonej baz danych, takich jak koligacja regionu między klientami i tolerancja opóźnienia niezależnego fragmentu i replikacji. Tylko jedna usługa globalna musi zostać utworzona do odczytu/zapisu danych do/z podzielonej bazy danych. Korzystając z aktywnej ochrony danych i konfigurując repliki tylko do odczytu fragmentów, można utworzyć inną usługę gGobal dla obciążeń tylko do odczytu. Klient może używać tych usług globalnych do łączenia się z bazą danych.

- **Bazy danych niezależnego fragmentu** — bazy danych niezależnego fragmentu są bazami danych Oracle. Każda baza danych jest replikowana przy użyciu oracle data guard w konfiguracji brokera z włączoną funkcją szybkiego uruchamiania trybu failover (FSFO). Nie trzeba skonfigurować pracy awaryjnej i replikacji usługi Data Guard na każdym niezależnemu fragmentowi. Jest to automatycznie konfigurowane i wdrażane podczas tworzenia udostępnionej bazy danych. Jeśli określony fragment nie powiedzie się, Oracle Sharing automatycznie kończy się niepowodzeniem za połączenia bazy danych z podstawowego do wstrzymania.

Baz danych Oracle i zarządzanie nimi można z dwoma interfejsami: interfejsem graficznym Oracle Enterprise Manager Cloud Control i/lub narzędziem `GDSCTL` wiersza polecenia. Można nawet monitorować różne fragmenty dostępności i wydajności za pomocą kontroli w chmurze. Polecenie `GDSCTL DEPLOY` automatycznie tworzy fragmenty i ich odpowiednich odbiorników. Ponadto to polecenie automatycznie wdraża konfigurację replikacji używaną do wysokiej dostępności na poziomie niezależnego fragmentu określonej przez administratora.

Istnieją różne sposoby dzielenia na fragment bazy danych:

* Dzielenie na fragmenty zarządzane przez system — automatycznie rozdziela fragmenty za pomocą partycjonowania
* Dzielenie na fragmenty zdefiniowane przez użytkownika — umożliwia określenie mapowania danych na fragmenty, które działa dobrze, gdy istnieją wymagania regulacyjne lub dotyczące lokalizacji danych)
* Dzielenia na fragmenty złożone — połączenie dzielenia na fragmenty zarządzane przez system i zdefiniowane przez użytkownika dla różnych _obszarów niezależnego fragmentu_
* Podpartii tabeli — podobne do zwykłej tabeli podzielonej na partycje.

Przeczytaj więcej o różnych [metodach dzielenia na fragmenty](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) w dokumentacji Oracle.

Podczas gdy podzielona baza danych może wyglądać jak pojedyncza baza danych do aplikacji i deweloperów, podczas migracji z nie podzielonej bazy danych do podzielonej bazy danych, staranne planowanie jest wymagane, aby określić, które tabele będą duplikowane w porównaniu do podzielonego. 

Zduplikowane tabele są przechowywane na wszystkich fragmentów, podczas gdy tabele podzielone są rozłożone na różnych fragmentów. Zalecenie jest zduplikowanie tabel małych i wymiarowych i dystrybucji/niezależnego fragment tabel fakt. Dane można załadować do podzielonej bazy danych przy użyciu katalogu niezależnego fragmentu jako centralnego koordynatora lub uruchomionej pompy danych na każdym niezależniu. Dowiedz się więcej o [migracji danych do podzielonej bazy danych](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) w dokumentacji Oracle.

#### <a name="oracle-sharding-with-data-guard"></a>Oracle Sharding z osłoną danych

Oracle Data Guard może służyć do dzielenia na fragmenty za pomocą metod dzielenia na fragmenty zarządzanego przez system, zdefiniowanego przez użytkownika i złożonego.

Poniższy diagram jest architekturą referencyjną dla oracle sharding z Oracle Data Guard używane do wysokiej dostępności każdego niezależnego fragmentu. Diagram architektury przedstawia _metodę dzielenia na fragmenty kompozytowe_. Diagram architektury prawdopodobnie będzie się różnić dla aplikacji z różnych wymagań dotyczących lokalizacji danych, równoważenia obciążenia, wysokiej dostępności, odzyskiwania po awarii, itp. Oracle Sharding pozwala spełnić te wymagania i skalować poziomo i wydajnie, udostępniając te opcje. Podobną architekturę można nawet wdrożyć przy użyciu Oracle GoldenGate.

![Dzielenia na fragmenty bazy danych Oracle przy użyciu stref dostępności z Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

Podczas gdy dzielenie na fragmenty zarządzane przez system jest najłatwiejsze do skonfigurowania i zarządzania, dzielenia na fragmenty zdefiniowane przez użytkownika lub dzielenia na fragmenty kompozytowe jest dobrze nadaje się do scenariuszy, w których dane i aplikacji są rozproszone geograficznie lub w scenariuszach, w których trzeba mieć kontrolę nad replikacją każdego fragmentu. 

W poprzedniej architekturze dzielenia na fragmenty złożone jest używany do geograficznej dystrybucji danych i skalowania w poziomie w poziomie warstw aplikacji. Dzielenia dzielenia na fragmenty złożone jest kombinacją usg zarządzanych przez system i zdefiniowanych przez użytkownika, a tym samym zapewnia korzyści z obu metod. W poprzednim scenariuszu dane są najpierw podzielone na wiele obszarów niezależnego fragmentu oddzielonych regionem. Następnie dane są dalej partycjonowane przez spójne skróty w wielu fragmentów w przestrzeni niezależnej. Każdy shardspace zawiera wiele grup niezależnego fragmentu. Każda grupa niezależnego fragmentu ma wiele fragmentów i jest "jednostką" replikacji, w tym przypadku. Każda grupa niezależnego fragmentu zawiera wszystkie dane w przestrzeni niezależnej. Grupy niezależnego fragmentu A1 i B1 są grupami podstawowych niezależnego fragmentu, podczas gdy grupy niezależnego fragmentu A2 i B2 są rezerwami. Można wybrać, aby poszczególne fragmenty były jednostką replikacji, a nie grupą niezależnego fragmentu.

W poprzedniej architekturze gsm/shard dyrektor jest wdrażany w każdej strefie dostępności dla wysokiej dostępności. Zalecenie jest wdrożenie co najmniej jednego GSM/Shard director na centrum danych/regionu. Ponadto wystąpienie serwera aplikacji jest wdrażane w każdej strefie dostępności, która zawiera grupę niezależnego fragmentu. Ta konfiguracja umożliwia aplikacji utrzymanie opóźnienia między serwerem aplikacji a grupą bazy danych/niezależnego fragmentu na niskim poziomie. Jeśli baza danych ulegnie awarii, serwer aplikacji w tej samej strefie co rezerwowa baza danych może obsługiwać żądania po przejściu roli bazy danych. Azure Application Gateway and the shard director keep track of the request and response latency and route requests accordingly.

Z punktu widzenia aplikacji system kliencki żąda usługi Azure Application Gateway (lub innych technologii równoważenia obciążenia na platformie Azure), która przekierowuje żądanie do regionu najbliższego klientowi. Usługa Azure Application Gateway obsługuje również sesje przyklejone, więc wszelkie żądania pochodzące od tego samego klienta są kierowane do tego samego serwera aplikacji. Serwer aplikacji używa buforowania połączeń we sterownikach dostępu do danych. Ta funkcja jest dostępna w sterownikach takich jak JDBC, ODP.NET, OCI itp. Sterowniki mogą rozpoznawać klucze dzielenia na fragmenty określone jako część żądania. [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) dla klientów JDBC może umożliwić klientom aplikacji innych niż Oracle, takim jak Apache Tomcat i IIS, pracę z Oracle Sharding. 

Podczas początkowego żądania serwer aplikacji łączy się z dyrektorem niezależnego fragmentu w jego regionie, aby uzyskać informacje o routingu dla niezależnego fragmentu, do których żądanie musi być kierowane. Na podstawie klucza dzielenia na fragmenty przekazany, dyrektor kieruje serwer aplikacji do odpowiedniego niezależnego fragmentu. Serwer aplikacji buforuje te informacje, tworząc mapę, a w przypadku kolejnych żądań omija menedżera niezależnego fragmentu i kieruje żądania bezpośrednio do niezależnego fragmentu.

#### <a name="oracle-sharding-with-goldengate"></a>Oracle Sharding z GoldenGate

Poniższy diagram jest architekturą referencyjną dla Oracle Sharding z Oracle GoldenGate dla wysokiej dostępności każdego niezależnego fragmentu w regionie. W przeciwieństwie do poprzedniej architektury ta architektura przedstawia tylko wysoką dostępność w jednym regionie platformy Azure (wiele stref dostępności). Można wdrożyć wieloregionową bazę danych podzieloną na fragmenty (podobną do poprzedniego przykładu) przy użyciu programu Oracle GoldenGate.

![Dzielenia na fragmenty bazy danych Oracle przy użyciu stref dostępności z GoldenGate](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

Poprzednia architektura referencyjna używa metody dzielenia na fragmenty _zarządzanego przez system_ do dzielenia na fragmenty danych. Ponieważ replikacja Oracle GoldenGate odbywa się na poziomie fragmentu, połowa danych replikowanych do jednego fragmentu może być replikowana do innego fragmentu. Druga połowa może być replikowana do innego fragmentu. 

Sposób, w jaki dane są replikowane, zależy od współczynnika replikacji. Ze współczynnikiem replikacji 2, będziesz miał dwie kopie każdego fragmentu danych w trzech fragmentów w grupie niezależnego fragmentu. Podobnie ze współczynnikiem replikacji 3 i trzy fragmenty w grupie niezależnego fragmentu wszystkie dane w każdym niezależnego fragmentu będą replikowane do każdego innego niezależnego fragmentu w grupie niezależnego fragmentu. Każdy fragment w grupie niezależnego fragmentu może mieć inny współczynnik replikacji. Ta konfiguracja ułatwia wydajne definiowanie projektowania wysokiej dostępności i odzyskiwania po awarii w grupie niezależnego fragmentu i wielu grupach niezależnego fragmentu.

W poprzedniej architekturze grupa niezależnego fragmentu A i grupa niezależnego fragmentu B zawierają te same dane, ale znajdują się w różnych strefach dostępności. Jeśli zarówno grupa niezależnego fragmentu A, jak i grupa niezależnego fragmentu B mają ten sam współczynnik replikacji 3, każdy wiersz/fragment podzielonej tabeli będzie replikowany 6 razy w dwóch grupach niezależnego fragmentu. Jeśli grupa niezależnego fragmentu A ma współczynnik replikacji 3 i grupa niezależnego fragmentu B ma współczynnik replikacji 2, każdy wiersz/fragment będzie replikowany 5 razy w dwóch grupach niezależnego fragmentu.

Ta konfiguracja zapobiega utracie danych w przypadku wystąpienia na poziomie lub awarii na poziomie strefy dostępności. Warstwa aplikacji jest w stanie odczytać i zapisać do każdego fragmentu. Aby zminimalizować konflikty, Oracle Sharding wyznacza "fragment wzorca" dla każdego zakresu wartości skrótu. Ta funkcja zapewnia, że zapisuje żądania dla określonego fragmentu są kierowane do odpowiedniego fragmentu. Ponadto Oracle GoldenGate zapewnia automatyczne wykrywanie konfliktów i rozwiązywanie konfliktów w celu obsługi wszelkich konfliktów, które mogą się pojawić. Aby uzyskać więcej informacji i ograniczeń związanych z wdrażaniem GoldenGate z Oracle Sharding, zobacz dokumentację Oracle dotyczącą korzystania [z Oracle GoldenGate z podzieloną bazą danych.](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72)

W poprzedniej architekturze gsm/shard dyrektor jest wdrażany w każdej strefie dostępności dla wysokiej dostępności. Zalecenie jest wdrożenie co najmniej jednego GSM/Shard director na centrum danych lub regionu. Ponadto wystąpienie serwera aplikacji jest wdrażane w każdej strefie dostępności, która zawiera grupę niezależnego fragmentu. Ta konfiguracja umożliwia aplikacji utrzymanie opóźnienia między serwerem aplikacji a grupą bazy danych/niezależnego fragmentu na niskim poziomie. Jeśli baza danych ulegnie awarii, serwer aplikacji w tej samej strefie co rezerwowa baza danych może obsługiwać żądania po przejściu roli bazy danych. Azure Application Gateway and the shard director keep track of the request and response latency and route requests accordingly.

Z punktu widzenia aplikacji system kliencki żąda usługi Azure Application Gateway (lub innych technologii równoważenia obciążenia na platformie Azure), która przekierowuje żądanie do regionu najbliższego klientowi. Usługa Azure Application Gateway obsługuje również sesje przyklejone, więc wszelkie żądania pochodzące od tego samego klienta są kierowane do tego samego serwera aplikacji. Serwer aplikacji używa buforowania połączeń we sterownikach dostępu do danych. Ta funkcja jest dostępna w sterownikach takich jak JDBC, ODP.NET, OCI itp. Sterowniki mogą rozpoznawać klucze dzielenia na fragmenty określone jako część żądania. [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) dla klientów JDBC może umożliwić klientom aplikacji innych niż Oracle, takim jak Apache Tomcat i IIS, pracę z Oracle Sharding. 

Podczas początkowego żądania serwer aplikacji łączy się z dyrektorem niezależnego fragmentu w jego regionie, aby uzyskać informacje o routingu dla niezależnego fragmentu, do których żądanie musi być kierowane. Na podstawie klucza dzielenia na fragmenty przekazany, dyrektor kieruje serwer aplikacji do odpowiedniego niezależnego fragmentu. Serwer aplikacji buforuje te informacje, tworząc mapę, a w przypadku kolejnych żądań omija menedżera niezależnego fragmentu i kieruje żądania bezpośrednio do niezależnego fragmentu.

## <a name="patching-and-maintenance"></a>Łatanie i konserwacja

Podczas wdrażania obciążeń Oracle na platformie Azure firma Microsoft zajmuje się wprowadzaniem poprawek na poziomie systemu operacyjnego hosta. Wszelkie planowane konserwacji na poziomie systemu operacyjnego jest przekazywane klientom z wyprzedzeniem, aby umożliwić klientowi tej planowanej konserwacji. Dwa serwery z dwóch różnych stref dostępności nigdy nie są załatane jednocześnie. Zobacz [Zarządzanie dostępnością maszyn wirtualnych,](../../../virtual-machines/linux/manage-availability.md) aby uzyskać więcej informacji na temat konserwacji i poprawek maszyn wirtualnych. 

Łatanie systemu operacyjnego maszyny wirtualnej można zautomatyzować za pomocą [usługi Azure Automation](../../../automation/automation-tutorial-update-management.md). Wprowadzanie poprawek i obsługa bazy danych Oracle można zautomatyzować i zaplanować przy użyciu [usługi Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops) lub [usługi Azure Automation](../../../automation/automation-tutorial-update-management.md) w celu zminimalizowania przestojów. Zobacz [ciągłe dostarczanie i wdrożenia niebieski/zielony,](/azure/devops/learn/what-is-continuous-delivery) aby dowiedzieć się, jak można go używać w kontekście baz danych Oracle.

## <a name="architecture-and-design-considerations"></a>Zagadnienia dotyczące architektury i projektowania

- Rozważ użycie maszyny [wirtualnej zoptymalizowanej pod kątem pamięci](../../../virtual-machines/windows/sizes-memory.md) hiperwątczej z [procesorami wirtualnymi o ograniczonym rdzeniu](../../../virtual-machines/windows/constrained-vcpu.md) dla maszyny wirtualnej bazy danych Oracle Database, aby zaoszczędzić na kosztach licencjonowania i zmaksymalizować wydajność. Aby uzyskać wydajność i dostępność, użyj wielu dysków premium lub ultra (dysków zarządzanych).
- Podczas korzystania z dysków zarządzanych nazwa dysku/urządzenia może ulec zmianie po ponownym uruchomieniu komputera. Zaleca się użycie identyfikatora UUID urządzenia zamiast nazwy, aby upewnić się, że instalacje utrzymują się podczas ponownego uruchamiania. Więcej informacji można znaleźć [tutaj](../../../virtual-machines/linux/configure-raid.md#add-the-new-file-system-to-etcfstab).
- Użyj stref dostępności, aby osiągnąć wysoką dostępność w regionie.
- Rozważ użycie dysków ultra (jeśli są dostępne) lub dysków premium dla bazy danych Oracle.
- Rozważ utworzenie rezerwowej bazy danych Oracle w innym regionie platformy Azure przy użyciu programu Oracle Data Guard.
- Należy rozważyć użycie [grup miejsc docelowych zbliżeniowych,](../../../virtual-machines/linux/co-location.md#proximity-placement-groups) aby zmniejszyć opóźnienie między warstwą aplikacji i bazy danych.
- Skonfiguruj [Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/) do zarządzania, monitorowania i rejestrowania.
- Rozważ użycie oracle automatic storage management (ASM) w celu usprawnienia zarządzania pamięcią masową w bazie danych.
- Użyj [usługi Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) do zarządzania poprawkami i aktualizacjami bazy danych bez żadnych przestojów.
- Dostosuj kod aplikacji, aby dodać wzorce natywne dla chmury, takie jak [wzorzec ponawiania próby,](/azure/architecture/patterns/retry) [wzorzec wyłącznika](/azure/architecture/patterns/circuit-breaker)i inne wzorce zdefiniowane w [przewodniku Wzorce projektowania chmury,](/azure/architecture/patterns/) które mogą pomóc aplikacji być bardziej odporne.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami referencyjne Oracle, które mają zastosowanie do twojego scenariusza.

- [Wprowadzenie do Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Koncepcje brokera oracle data guard](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [Konfigurowanie oracle GoldenGate pod kątem wysokiej dostępności active-active](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Omówienie dzielenia na fragmenty Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle Active Data Guard Far Sync Zero Data Loss w dowolnej odległości](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
