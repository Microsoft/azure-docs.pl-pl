---
title: Grupy automatycznego trybu failover
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Grupy automatyczne trybu failover umożliwiają zarządzanie replikacją i automatyczną/koordynowaną trybem failover grupy baz danych na serwerze lub wszystkich bazach danych w wystąpieniu zarządzanym.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/26/2020
ms.openlocfilehash: 91375f4460b55617ace0b18b60d59d961a762f4c
ms.sourcegitcommit: 00aa5afaa9fac91f1059cfed3d8dbc954caaabe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2020
ms.locfileid: "97792504"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Używanie grup z obsługą trybu failover w celu zapewnienia przezroczystej i skoordynowanej pracy w trybie failover wielu baz danych
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Funkcja grup autopracy awaryjnej umożliwia zarządzanie replikacją i trybem failover grupy baz danych na serwerze lub wszystkich bazach danych w wystąpieniu zarządzanym w innym regionie. Jest to deklaratywne streszczenie na istniejącej funkcji [aktywnej replikacji geograficznej](active-geo-replication-overview.md) , zaprojektowane w celu uproszczenia wdrażania geograficznie replikowanych baz danych i zarządzania nimi na dużą skalę. Możesz zainicjować tryb failover ręcznie lub można delegować go do usługi platformy Azure na podstawie zasad zdefiniowanych przez użytkownika. Ta ostatnia opcja umożliwia automatyczne odzyskanie wielu pokrewnych baz danych w regionie pomocniczym po katastrofalnym błędzie lub innym nieplanowanym zdarzeniu powodującym całkowite lub częściowe utratę dostępności wystąpienia zarządzanego SQL Database lub SQL w regionie podstawowym. Grupa trybu failover może zawierać jedną lub wiele baz danych, zwykle używanych przez tę samą aplikację. Ponadto można użyć dodatkowych baz danych z możliwością odczytu, aby odciążać obciążenia zapytań służących tylko do odczytywania. Ponieważ grupy autotrybu failover obejmują wiele baz danych, te bazy danych muszą być skonfigurowane na serwerze podstawowym. Grupy autotrybu failover obsługują replikację wszystkich baz danych w grupie tylko do jednego serwera pomocniczego lub wystąpienia w innym regionie.

> [!NOTE]
> Jeśli potrzebujesz wielu Azure SQL Database pomocniczych w tym samym lub różnych regionach, użyj [aktywnej replikacji geograficznej](active-geo-replication-overview.md).

W przypadku korzystania z grup automatycznych trybu failover z automatycznymi zasadami trybu failover każda awaria wpływająca na jedną lub kilka baz danych w grupie powoduje automatyczne przejście w tryb failover. Zazwyczaj są to zdarzenia, które nie mogą być nieznacznie ograniczane przez wbudowane operacje automatycznej wysokiej dostępności. Przykładowe wyzwalacze trybu failover obejmują zdarzenie spowodowane przez SQL Database pierścień dzierżawy lub pierścienia kontroli, z powodu wycieku pamięci jądra systemu operacyjnego na kilku węzłach obliczeniowych, lub zdarzenia spowodowanego przez co najmniej jeden pierścień dzierżawy, ponieważ niewłaściwy kabel sieciowy został wycięty podczas rutynowego wycofywania sprzętu.  Aby uzyskać więcej informacji, zobacz [SQL Database wysoka dostępność](high-availability-sla.md).

Ponadto grupy autotrybu failover udostępniają punkty końcowe odbiornika do odczytu i zapisu oraz tylko do odczytu, które pozostaną bez zmian podczas pracy w trybie failover. Bez względu na to, czy jest używana ręczna czy automatyczna aktywacja trybu failover, przełączanie do trybu failover wszystkie pomocnicze bazy danych w grupie do podstawowego. Po zakończeniu pracy w trybie failover bazy danych rekord DNS zostanie automatycznie zaktualizowany, aby przekierować punkty końcowe do nowego regionu. Aby uzyskać informacje na temat określonych elementów RPO i RTO, zobacz [Omówienie ciągłości działalności biznesowej](business-continuity-high-availability-disaster-recover-hadr-overview.md).

W przypadku korzystania z grup automatycznych trybu failover z automatycznymi zasadami trybu failover wszelkie awarie, które mają wpływ na bazy danych na serwerze lub wystąpieniu zarządzanym, są wykonywane automatycznie w trybie failover. Grupa autotrybu failover można zarządzać przy użyciu:

- [Witryna Azure Portal](geo-distributed-application-configure-tutorial.md)
- [Interfejs wiersza polecenia platformy Azure: Grupa trybu failover](scripts/add-database-to-failover-group-cli.md)
- [PowerShell: Grupa trybu failover](scripts/add-database-to-failover-group-powershell.md)
- [Interfejs API REST: Grupa trybu failover](/rest/api/sql/failovergroups).

Po przejściu w tryb failover upewnij się, że wymagania dotyczące uwierzytelniania dla bazy danych i serwera są skonfigurowane na nowym serwerze podstawowym. Aby uzyskać szczegółowe informacje, zobacz [SQL Database zabezpieczenia po odzyskiwaniu po awarii](active-geo-replication-security-configure.md).

Aby osiągnąć prawdziwą ciągłość biznesową, Dodawanie nadmiarowości bazy danych między centrami elementów jest tylko częścią rozwiązania. Odzyskiwanie aplikacji (usługi) od końca do końca po katastrofalnym błędzie wymaga odzyskania wszystkich składników wchodzących w skład usługi i usług zależnych. Przykładowe składniki obejmują oprogramowanie klienta (na przykład przeglądarkę z niestandardowym językiem JavaScript), frontony sieci Web, magazyn i system DNS. Należy pamiętać, że wszystkie składniki są odporne na te same awarie i stają się dostępne w ramach celu czasu odzyskiwania (RTO) aplikacji. W związku z tym należy zidentyfikować wszystkie usługi zależne i zrozumieć gwarancje i funkcje, które zapewnia. Następnie należy wykonać odpowiednie czynności, aby upewnić się, że usługa działa w trybie failover usług, na których jest ona zależna. Aby uzyskać więcej informacji na temat projektowania rozwiązań na potrzeby odzyskiwania po awarii, zobacz [projektowanie rozwiązań w chmurze na potrzeby odzyskiwania po awarii przy użyciu aktywnej replikacji geograficznej](designing-cloud-solutions-for-disaster-recovery.md).

## <a name="terminology-and-capabilities"></a>Terminologia i możliwości

- **Grupa trybu failover (przeciwmgielne)**

  Grupa trybu failover to nazwana grupa baz danych zarządzana przez jeden serwer lub w zarządzanym wystąpieniu, które może być przełączane w tryb pracy awaryjnej jako jednostka do innego regionu w przypadku, gdy wszystkie lub niektóre podstawowe bazy danych staną się niedostępne z powodu awarii w regionie podstawowym. Po utworzeniu dla wystąpienia zarządzanego SQL grupa trybu failover zawiera wszystkie bazy danych użytkownika w tym wystąpieniu, w związku z czym w wystąpieniu można skonfigurować tylko jedną grupę trybu failover.
  
  > [!IMPORTANT]
  > Nazwa grupy trybu failover musi być globalnie unikatowa w obrębie `.database.windows.net` domeny.

- **Serwery**

     W przypadku serwerów, niektóre lub wszystkie bazy danych użytkownika na serwerze można umieścić w grupie trybu failover. Ponadto serwer obsługuje wiele grup trybu failover na jednym serwerze.

- **Podstawowe**

  Serwer lub wystąpienie zarządzane, które hostuje podstawowe bazy danych w grupie trybu failover.

- **Pomocniczy**

  Serwer lub wystąpienie zarządzane, które hostuje pomocnicze bazy danych w grupie trybu failover. Pomocnicza nie może znajdować się w tym samym regionie co podstawowy.

- **Dodawanie pojedynczych baz danych do grupy trybu failover**

  Kilka pojedynczych baz danych można umieścić na tym samym serwerze w tej samej grupie trybu failover. W przypadku dodania pojedynczej bazy danych do grupy trybu failover automatycznie zostanie utworzona pomocnicza baza danych z tą samą wersją i rozmiarem obliczeniowym na serwerze pomocniczym.  Ten serwer został określony podczas tworzenia grupy trybu failover. Po dodaniu bazy danych, która ma już pomocniczą bazę danych na serwerze pomocniczym, łącze replikacji geograficznej jest dziedziczone przez grupę. Po dodaniu bazy danych, która ma już pomocniczą bazę danych na serwerze, który nie jest częścią grupy trybu failover, na serwerze pomocniczym zostanie utworzony nowy element pomocniczy.

  > [!IMPORTANT]
  > Upewnij się, że serwer pomocniczy nie ma bazy danych o tej samej nazwie, chyba że jest to istniejąca pomocnicza baza danych. W grupach trybu failover dla wystąpienia zarządzanego SQL są replikowane wszystkie bazy danych użytkowników. Nie można wybrać podzestawu baz danych użytkowników na potrzeby replikacji w grupie trybu failover.

- **Dodawanie baz danych w puli elastycznej do grupy trybu failover**

  Wszystkie lub kilka baz danych w puli elastycznej można umieścić w tej samej grupie trybu failover. Jeśli podstawowa baza danych znajduje się w puli elastycznej, zostanie ona automatycznie utworzona w puli elastycznej o tej samej nazwie (puli pomocniczej). Musisz się upewnić, że serwer pomocniczy zawiera pulę elastyczną o tej samej dokładnej nazwie i wystarczającej pojemności do hostowania pomocniczych baz danych, które zostaną utworzone przez grupę trybu failover. W przypadku dodania bazy danych w puli, która ma już pomocniczą bazę danych w puli pomocniczej, to łącze replikacji geograficznej jest dziedziczone przez grupę. Po dodaniu bazy danych, która ma już pomocniczą bazę danych na serwerze, który nie jest częścią grupy trybu failover, w dodatkowej puli zostanie utworzony nowy element pomocniczy.
  
- **Początkowe rozpełnianie**

  Podczas dodawania baz danych, pul elastycznych lub wystąpień zarządzanych do grupy trybu failover istnieje początkowa faza wypełniania, przed rozpoczęciem replikacji danych. Początkowa faza wypełniania jest najdłuższym i najtańszą operacją. Po zakończeniu początkowego umieszczania dane są synchronizowane, a następnie replikowane są tylko kolejne zmiany danych. Czas potrzebny na ukończenie początkowego inicjatora zależy od rozmiaru danych, liczby zreplikowanych baz danych oraz szybkości połączenia między jednostkami w grupie trybu failover. W normalnych warunkach możliwa szybkość wypełniania jest równa 500 GB za godzinę dla SQL Database i nawet do 360 GB czasu dla wystąpienia zarządzanego SQL. Rozmieszczanie jest wykonywane dla wszystkich baz danych równolegle.

  W przypadku wystąpienia zarządzanego SQL należy wziąć pod uwagę szybkość łącza usługi Express Route między dwoma wystąpieniami podczas szacowania czasu początkowej fazy umieszczania. Jeśli szybkość łącza między dwoma wystąpieniami jest mniejsza niż to, co jest konieczne, może to mieć wpływ na czas, w którym jest to możliwe. Można użyć podanej szybkości wypełniania, liczby baz danych, łącznego rozmiaru danych i szybkości łącza, aby oszacować, jak długo początkowa faza rozsadzenia będzie trwać przed rozpoczęciem replikacji danych. Na przykład w przypadku pojedynczej bazy danych 100 GB początkowa faza inicjatora zajmie około 1,2 godzin, jeśli Link umożliwia wypychanie 84 GB na godzinę, a w przypadku braku innych baz danych. Jeśli link umożliwia transfer 10 GB na godzinę, wówczas wypełnianie bazy danych 100 GB zajmie około 10 godzin. Jeśli istnieje wiele baz danych do replikowania, umieszczanie zostanie wykonane równolegle, a w połączeniu z powolnej szybkością łącza, początkowa faza wypełniania może potrwać znacznie dłużej, zwłaszcza jeśli równoległe umieszczanie danych ze wszystkich baz danych przekracza dostępną przepustowość łącza. Jeśli przepustowość sieci między dwoma wystąpieniami jest ograniczona i dodajesz wiele wystąpień zarządzanych do grupy trybu failover, rozważ dodanie wielu wystąpień zarządzanych do grupy trybu failover sekwencyjnie, jeden według jednego. Mając odpowiednią wielkość jednostki SKU bramy między dwoma wystąpieniami zarządzanymi, a w przypadku, gdy przepustowość sieci firmowej pozwala na to, możliwe jest osiągnięcie szybkości 360 GB.  

- **Strefa DNS**

  Unikatowy identyfikator, który jest generowany automatycznie podczas tworzenia nowego wystąpienia zarządzanego SQL. Obsługa certyfikatu wielodomenowego (SAN) dla tego wystąpienia jest obsługiwana w celu uwierzytelnienia połączeń klientów z dowolnym wystąpieniem w tej samej strefie DNS. Dwa wystąpienia zarządzane w tej samej grupie trybu failover muszą współdzielić strefę DNS.
  
  > [!NOTE]
  > Identyfikator strefy DNS nie jest wymagany dla grup trybu failover utworzonych dla SQL Database.

- **Odbiornik odczytu i zapisu grupy trybu failover**

  Rekord CNAME systemu DNS wskazujący na adres URL bieżącego elementu podstawowego. Jest tworzony automatycznie podczas tworzenia grupy trybu failover i umożliwia obciążenia do odczytu i zapisu, aby w sposób przezroczysty ponownie połączyć się z podstawową bazą danych podczas pracy w trybie failover. Po utworzeniu grupy trybu failover na serwerze rekord CNAME DNS dla adresu URL odbiornika zostanie utworzony jako `<fog-name>.database.windows.net` . Po utworzeniu grupy trybu failover w wystąpieniu zarządzanym SQL rekord CNAME DNS dla adresu URL odbiornika jest tworzony jako `<fog-name>.<zone_id>.database.windows.net` .

- **Odbiornik tylko do odczytu grupy trybu failover**

  Utworzony rekord CNAME systemu DNS wskazujący odbiornik tylko do odczytu wskazujący na adres URL pomocniczy. Jest tworzony automatycznie podczas tworzenia grupy trybu failover i zezwala na nieprzezroczyste połączenie z serwerem pomocniczym tylko do odczytu z usługą przy użyciu określonych reguł równoważenia obciążenia. Po utworzeniu grupy trybu failover na serwerze rekord CNAME DNS dla adresu URL odbiornika zostanie utworzony jako `<fog-name>.secondary.database.windows.net` . Po utworzeniu grupy trybu failover w wystąpieniu zarządzanym SQL rekord CNAME DNS dla adresu URL odbiornika jest tworzony jako `<fog-name>.secondary.<zone_id>.database.windows.net` .

- **Zasady automatycznego trybu failover**

  Domyślnie grupa trybu failover jest konfigurowana z użyciem zasad automatycznego trybu failover. Usługa Azure wyzwala tryb failover po wykryciu awarii i upłynął okres prolongaty. System musi sprawdzić, czy nie można zmniejszyć przestoju dzięki wbudowanej [infrastrukturze wysokiej dostępności](high-availability-sla.md) ze względu na skalę wpływu. Jeśli chcesz kontrolować przepływ pracy trybu failover z aplikacji lub ręcznie, możesz wyłączyć automatyczne przełączanie do trybu failover.
  
  > [!NOTE]
  > Ze względu na to, że weryfikacja skali przestoju i szybkość, z jaką można ją ograniczyć, wiąże się z działaniami ludzkimi przez zespół operacyjny, okres prolongaty nie może być ustawiony poniżej jednej godziny. To ograniczenie ma zastosowanie do wszystkich baz danych w grupie trybu failover niezależnie od ich stanu synchronizacji danych.

- **Zasady trybu failover tylko do odczytu**

  Domyślnie tryb failover odbiornika tylko do odczytu jest wyłączony. Gwarantuje to, że nie ma to wpływu na wydajność podstawowego, gdy pomocnicza jest w trybie offline. Oznacza to jednak również, że sesje tylko do odczytu nie będą mogły nawiązywać połączenia do momentu odzyskania pomocniczego. Jeśli nie można tolerować przestoju w przypadku sesji tylko do odczytu i można użyć podstawowego dla ruchu tylko do odczytu i zapisu na koszt potencjalnego obniżenia wydajności podstawowego, można włączyć tryb failover dla odbiornika tylko do odczytu przez skonfigurowanie `AllowReadOnlyFailoverToPrimary` właściwości. W takim przypadku ruch tylko do odczytu zostanie automatycznie przekierowany do podstawowego, jeśli pomocnicza nie jest dostępna.

  > [!NOTE]
  > `AllowReadOnlyFailoverToPrimary`Właściwość działa tylko wtedy, gdy włączona jest automatyczna zasada trybu failover, a platforma Azure wyzwoliła automatyczne przełączenie w tryb failover. W takim przypadku, jeśli właściwość ma wartość true, nowy podstawowy będzie obsługiwał zarówno do odczytu, jak i tylko do odczytu.

- **Planowana praca w trybie failover**

   Planowana praca w trybie failover wykonuje pełną synchronizację między podstawowymi a pomocniczymi bazami danych przed przełączeniem do roli podstawowej. Gwarantuje to brak utraty danych. Planowana praca w trybie failover jest używana w następujących scenariuszach:

  - Przeprowadzaj ćwiczenia odzyskiwania po awarii (DR) w środowisku produkcyjnym, gdy utrata danych nie jest akceptowalna
  - Przeniesienie baz danych do innego regionu
  - Zwróć bazy danych do regionu podstawowego po usunięciu awarii (powrót po awarii).

- **Nieplanowany tryb failover**

   Nieplanowane lub wymuszone przejście w tryb failover natychmiast przełącza pomocnicze do roli podstawowej bez synchronizacji z serwerem podstawowym. Ta operacja spowoduje utratę danych. Nieplanowana praca w trybie failover jest używana jako metoda odzyskiwania w trakcie awarii, gdy podstawowa jest niedostępna. Gdy pierwotny element podstawowy jest w trybie online, zostanie automatycznie ponownie nawiązać połączenie bez synchronizacji i stanie się nowym pomocniczym.

- **Ręczna praca awaryjna**

  Tryb failover można zainicjować ręcznie w dowolnym momencie, niezależnie od konfiguracji automatycznej pracy awaryjnej. Jeśli zasady automatycznej pracy awaryjnej nie są skonfigurowane, do odzyskania baz danych w grupie trybu failover jest wymagane ręczne przełączenie w tryb failover. Możesz inicjować wymuszone lub przyjazne przejście w tryb failover (z pełną synchronizacją danych). Ten drugi może służyć do przemieszczenie podstawowego do regionu pomocniczego. Po zakończeniu pracy w trybie failover rekordy DNS są automatycznie aktualizowane, aby zapewnić łączność z nowym podstawowym.

- **Okres prolongaty z utratą danych**

  Ponieważ podstawowe i pomocnicze bazy danych są synchronizowane przy użyciu replikacji asynchronicznej, przełączenie w tryb failover może spowodować utratę danych. Można dostosować zasady automatycznego trybu failover w celu odzwierciedlenia tolerancji aplikacji do utraty danych. Konfigurując `GracePeriodWithDataLossHours` , można kontrolować czas oczekiwania systemu przed zainicjowaniem trybu failover, który prawdopodobnie spowoduje utratę danych.

- **Wiele grup trybu failover**

  Można skonfigurować wiele grup trybu failover dla tej samej pary serwerów, aby sterować zakresem trybu failover. Każda grupa przejdzie w tryb failover niezależnie. Jeśli aplikacja wielodostępna korzysta z pul elastycznych, można użyć tej funkcji do mieszania podstawowych i pomocniczych baz danych w każdej puli. W ten sposób można zmniejszyć wpływ przestoju tylko na połowę dzierżawców.

  > [!NOTE]
  > Wystąpienie zarządzane SQL nie obsługuje wielu grup trybu failover.
  
## <a name="permissions"></a>Uprawnienia

Uprawnienia dla grupy trybu failover są zarządzane za pośrednictwem [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/overview.md). Rola [współautor SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) ma wszystkie uprawnienia niezbędne do zarządzania grupami trybu failover.

### <a name="create-failover-group"></a>Utwórz grupę trybu failover

Aby utworzyć grupę trybu failover, wymagany jest dostęp do zapisu z usługi Azure RBAC do serwera podstawowego i pomocniczego oraz do wszystkich baz danych w grupie trybu failover. W przypadku wystąpienia zarządzanego SQL wymagany jest dostęp do zapisu w usłudze Azure RBAC zarówno do podstawowego, jak i pomocniczego wystąpienia zarządzanego SQL, ale uprawnienia do poszczególnych baz danych nie są istotne, ponieważ nie można dodać do grupy trybu failover ani usunąć z niej pojedynczych baz danych wystąpienia zarządzanego SQL.

### <a name="update-a-failover-group"></a>Aktualizowanie grupy trybu failover

Aby zaktualizować grupę trybu failover, wymagany jest dostęp do zapisu z usługi Azure RBAC do grupy trybu failover oraz wszystkich baz danych na bieżącym serwerze podstawowym lub zarządzanym wystąpieniu.  

### <a name="fail-over-a-failover-group"></a>Praca awaryjna grupy trybu failover

Aby można było przełączyć grupę trybu failover w tryb pracy awaryjnej, do grupy trybu failover na nowym serwerze podstawowym lub wystąpieniu zarządzanym jest wymagany dostęp do zapisu z usługi Azure RBAC.

## <a name="best-practices-for-sql-database"></a>Najlepsze rozwiązania dotyczące SQL Database

Grupa autotrybu failover musi być skonfigurowana na serwerze podstawowym i nawiązać połączenie z serwerem pomocniczym w innym regionie świadczenia usługi Azure. Grupy mogą obejmować wszystkie lub niektóre bazy danych na tych serwerach. Na poniższym diagramie przedstawiono typową konfigurację aplikacji w chmurze nadmiarowej geograficznie przy użyciu wielu baz danych i grupy automatycznej pracy awaryjnej.

![Diagram przedstawia typową konfigurację aplikacji w chmurze nadmiarowej geograficznie przy użyciu wielu baz danych i grupy automatycznej pracy awaryjnej.](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> Zobacz [dodawanie SQL Database do grupy trybu failover](failover-group-add-single-database-tutorial.md) w celu uzyskania szczegółowego samouczka krok po kroku Dodawanie bazy danych w SQL Database do grupy trybu failover.

Podczas projektowania usługi z zachowaniem ciągłości działania postępuj zgodnie z następującymi ogólnymi wskazówkami:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Korzystanie z jednej lub kilku grup trybu failover w celu zarządzania trybem failover wielu baz danych

Można utworzyć co najmniej jedną grupę trybu failover między dwoma serwerami w różnych regionach (serwery podstawowe i pomocnicze). Każda grupa może zawierać jedną lub kilka baz danych, które są odzyskiwane jako jednostki na wypadek, a niektóre podstawowe bazy danych staną się niedostępne z powodu awarii w regionie podstawowym. Grupa trybu failover tworzy geograficzną i pomocniczą bazę danych z tym samym celem usługi co podstawowy. W przypadku dodania istniejącej relacji replikacji geograficznej do grupy trybu failover upewnij się, że dla elementu podrzędnego jest skonfigurowana ta sama warstwa usługi i rozmiar obliczeń jako podstawowa.
  
> [!IMPORTANT]
> Tworzenie grup trybu failover między dwoma serwerami w różnych subskrypcjach nie jest obecnie obsługiwane dla Azure SQL Database. Przeniesienie podstawowego lub pomocniczego serwera do innej subskrypcji po utworzeniu grupy trybu failover może spowodować błędy żądań trybu failover i innych operacji.

### <a name="using-read-write-listener-for-oltp-workload"></a>Używanie odbiornika do odczytu i zapisu w obciążeniu OLTP

Podczas wykonywania operacji OLTP Użyj `<fog-name>.database.windows.net` jako adresu URL serwera, a połączenia są automatycznie kierowane do podstawowego. Ten adres URL nie zmienia się po przełączeniu w tryb failover. Należy zauważyć, że tryb failover obejmuje aktualizację rekordu DNS, dlatego połączenia klientów są przekierowywane do nowego podstawowego tylko po odświeżeniu pamięci podręcznej DNS klienta.

### <a name="using-read-only-listener-for-read-only-workload"></a>Używanie odbiornika tylko do odczytu dla obciążenia przeznaczonego tylko do odczytu

Jeśli istnieje logicznie izolowane obciążenie przeznaczone tylko do odczytu, które jest odporne na określoną nieaktualność danych, możesz użyć pomocniczej bazy danych w aplikacji. W przypadku sesji tylko do odczytu Użyj `<fog-name>.secondary.database.windows.net` jako adresu URL serwera, a połączenie jest automatycznie przekierowywane do pomocniczego. Zaleca się również, aby wskazać w polu cel odczytu parametrów połączenia przy użyciu `ApplicationIntent=ReadOnly` .

### <a name="preparing-for-performance-degradation"></a>Przygotowanie do obniżenia wydajności

Typowa aplikacja platformy Azure używa wielu usług platformy Azure i składa się z wielu składników. Automatyczna praca awaryjna grupy trybu failover jest wyzwalana na podstawie stanu samych składników usługi Azure SQL. Awaria może nie wpływać na inne usługi platformy Azure w regionie podstawowym, a ich składniki nadal mogą być dostępne w tym regionie. Po przełączeniu podstawowych baz danych do regionu DR opóźnienie między składnikami zależnymi może się zwiększyć. Aby uniknąć wpływu większego opóźnienia na wydajność aplikacji, należy upewnić się, że nadmiarowość wszystkich składników aplikacji w regionie odzyskiwania po awarii jest zgodna z tymi [wskazówkami dotyczącymi zabezpieczeń sieci](#failover-groups-and-network-security).

### <a name="preparing-for-data-loss"></a>Przygotowywanie do utraty danych

Jeśli zostanie wykryta awaria, platforma Azure czeka przez określony czas `GracePeriodWithDataLossHours` . Wartość domyślna to 1 godzina. Jeśli nie możesz zapewnić utraty danych, upewnij się, że ustawiono `GracePeriodWithDataLossHours` wystarczająco dużą liczbę, na przykład 24-godzinny. Użyj ręcznej pracy awaryjnej grupy, aby wrócić z poziomu pomocniczego do podstawowego.

> [!IMPORTANT]
> Pule elastyczne z 800 lub mniej DTU i więcej niż 250 baz danych korzystających z replikacji geograficznej mogą napotkać problemy, w tym dłuższe planowane przełączanie w tryb failover i wydajność o obniżonej wydajności.  Te problemy mogą wystąpić w przypadku obciążeń intensywnie korzystających z zapisu, gdy punkty końcowe replikacji geograficznej są szeroko oddzielane przez geografię lub gdy dla każdej bazy danych są używane wiele pomocniczych punktów końcowych.  Objawy tych problemów są wskazywane, gdy opóźnienie replikacji geograficznej rośnie wraz z upływem czasu.  Te opóźnienia można monitorować przy użyciu [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Jeśli te problemy wystąpią, środki zaradcze obejmują zwiększenie liczby DTU puli lub zmniejszenie liczby replikowanych geograficznie baz danych w tej samej puli.

### <a name="changing-secondary-region-of-the-failover-group"></a>Zmienianie regionu pomocniczego grupy trybu failover

W celu zilustrowania sekwencji zmian przyjęto założenie, że serwer A jest serwerem podstawowym, serwer B jest istniejącym serwerem pomocniczym, a serwer C jest nowym dodatkowym w trzecim regionie.  Aby dokonać przejścia, wykonaj następujące kroki:

1. Utwórz dodatkowe usługi pomocnicze dla każdej bazy danych na serwerze A na serwerze C przy użyciu [aktywnej replikacji geograficznej](active-geo-replication-overview.md). Każda baza danych na serwerze A będzie mieć dwie serwery pomocnicze, jeden na serwerze B i jeden na serwerze C. Pozwoli to zagwarantować, że podstawowe bazy danych pozostaną chronione podczas przejścia.
2. Usuń grupę trybu failover. W tym momencie logowania będą kończyć się niepowodzeniem. Wynika to z faktu, że aliasy SQL dla odbiorników grupy trybu failover zostały usunięte, a Brama nie rozpozna nazwy grupy trybu failover.
3. Utwórz ponownie grupę trybu failover o tej samej nazwie między serwerami A i C. W tym momencie logowania zakończą się niepowodzeniem.
4. Dodaj wszystkie podstawowe bazy danych na serwerze A do nowej grupy trybu failover.
5. Upuść serwer B. Wszystkie bazy danych w B zostaną usunięte automatycznie.

### <a name="changing-primary-region-of-the-failover-group"></a>Zmienianie regionu podstawowego grupy trybu failover

W celu zilustrowania sekwencji zmian przyjęto, że serwer A jest serwerem podstawowym, serwer B jest istniejącym serwerem pomocniczym, a serwer C jest nowym podstawowym w trzecim regionie.  Aby dokonać przejścia, wykonaj następujące kroki:

1. Wykonaj planowaną pracę w trybie failover w celu przełączenia serwera podstawowego na B. serwer A stanie się nowym serwerem pomocniczym. Przejście w tryb failover może spowodować powstanie kilku minut przestoju. Rzeczywista godzina będzie zależeć od rozmiaru grupy trybu failover.
2. Utwórz dodatkowe usługi pomocnicze dla każdej bazy danych na serwerze B na serwerze C przy użyciu [aktywnej replikacji geograficznej](active-geo-replication-overview.md). Każda baza danych na serwerze B będzie miała dwie serwery pomocnicze, jeden na serwerze A i jeden na serwerze C. Pozwoli to zagwarantować, że podstawowe bazy danych pozostaną chronione podczas przejścia.
3. Usuń grupę trybu failover. W tym momencie logowania będą kończyć się niepowodzeniem. Wynika to z faktu, że aliasy SQL dla odbiorników grupy trybu failover zostały usunięte, a Brama nie rozpozna nazwy grupy trybu failover.
4. Utwórz ponownie grupę trybu failover o tej samej nazwie między serwerami B i C. W tym momencie logowania zakończą się niepowodzeniem.
5. Dodaj wszystkie podstawowe bazy danych w B do nowej grupy trybu failover.
6. Wykonaj planowaną pracę w trybie failover grupy trybu failover w celu przełączenia B i C. Teraz serwer C stanie się podstawowym i B-pomocniczym. Wszystkie pomocnicze bazy danych na serwerze A zostaną automatycznie połączone z Primaries na dysku C. Jak w kroku 1 w trybie failover może wystąpić kilka minut przestoju.
7. Usuń serwer A. Wszystkie bazy danych na serwerze zostaną usunięte automatycznie.

> [!IMPORTANT]
> Po usunięciu grupy trybu failover rekordy DNS dla punktów końcowych odbiornika również są usuwane. W tym momencie istnieje prawdopodobieństwo, że ktoś inny tworzy grupę trybu failover lub alias serwera o tej samej nazwie, co uniemożliwi jego ponowne użycie. Aby zminimalizować ryzyko, nie używaj ogólnych nazw grup trybu failover.

## <a name="best-practices-for-sql-managed-instance"></a>Najlepsze rozwiązania dotyczące wystąpienia zarządzanego SQL

Grupa automatycznego trybu failover musi być skonfigurowana w wystąpieniu podstawowym i będzie nawiązywać połączenie z wystąpieniem pomocniczym w innym regionie świadczenia usługi Azure.  Wszystkie bazy danych w wystąpieniu zostaną zreplikowane do wystąpienia pomocniczego.

Na poniższym diagramie przedstawiono typową konfigurację aplikacji w chmurze nadmiarowej geograficznie przy użyciu wystąpienia zarządzanego i grupy autotrybu failover.

![Diagram Autotryb failover](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> Zobacz [Dodawanie wystąpienia zarządzanego do grupy trybu failover](../managed-instance/failover-group-add-instance-tutorial.md) , aby uzyskać szczegółowy samouczek krok po kroku dodawania wystąpienia zarządzanego SQL do korzystania z grupy trybu failover.

Jeśli aplikacja używa wystąpienia zarządzanego SQL jako warstwy danych, należy postępować zgodnie z ogólnymi wskazówkami podczas projektowania pod kątem ciągłości działania:

### <a name="creating-the-secondary-instance"></a>Tworzenie wystąpienia dodatkowego

Aby zapewnić nieprzerwane połączenie z podstawowym wystąpieniem zarządzanym SQL po przejściu w tryb failover, oba wystąpienia muszą znajdować się w tej samej strefie DNS. Gwarantuje to, że ten sam certyfikat wielodomenowy (SAN) może służyć do uwierzytelniania połączeń klientów z jednym z dwóch wystąpień w grupie trybu failover. Gdy aplikacja jest gotowa do wdrożenia produkcyjnego, Utwórz pomocnicze wystąpienie zarządzane SQL w innym regionie i upewnij się, że współużytkuje strefę DNS z podstawowym wystąpieniem zarządzanym SQL. Można to zrobić przez określenie opcjonalnego parametru podczas tworzenia. Jeśli używasz programu PowerShell lub interfejsu API REST, nazwa opcjonalnego parametru to `DNS Zone Partner` , a nazwa odpowiedniego pola opcjonalnego w Azure Portal jest podstawowym wystąpieniem zarządzanym.

> [!IMPORTANT]
> Pierwsze wystąpienie zarządzane utworzone w podsieci określa strefę DNS dla wszystkich kolejnych wystąpień w tej samej podsieci. Oznacza to, że dwa wystąpienia z tej samej podsieci nie mogą należeć do różnych stref DNS.

Aby uzyskać więcej informacji na temat tworzenia pomocniczego wystąpienia zarządzanego SQL w tej samej strefie DNS co wystąpienie podstawowe, zobacz [Tworzenie pomocniczego wystąpienia zarządzanego](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance).

### <a name="using-geo-paired-regions"></a>Używanie regionów geograficznych

Wdróż oba wystąpienia zarządzane do [sparowanych regionów](../../best-practices-availability-paired-regions.md) ze względu na wydajność. Wystąpienia zarządzane znajdujące się w regionach z parą geograficzną mają znacznie lepszą wydajność w porównaniu z niesparowanymi regionami. 

### <a name="enabling-replication-traffic-between-two-instances"></a>Włączanie ruchu replikacji między dwoma wystąpieniami

Ze względu na to, że każde wystąpienie jest izolowane w własnej sieci wirtualnej, należy zezwolić na ruch dwukierunkowy między tymi sieci wirtualnychami. Zobacz [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Tworzenie grupy trybu failover między wystąpieniami zarządzanymi w różnych subskrypcjach

Można utworzyć grupę trybu failover między wystąpieniami zarządzanymi SQL w dwóch różnych subskrypcjach, o ile subskrypcje są skojarzone z tą samą [dzierżawą Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md#terminology). Korzystając z interfejsu API programu PowerShell, można to zrobić, określając `PartnerSubscriptionId` parametr pomocniczego wystąpienia zarządzanego SQL. W przypadku korzystania z interfejsu API REST każdy identyfikator wystąpienia zawarty w `properties.managedInstancePairs` parametrze może mieć swój własny identyfikatora subskrypcji.
  
> [!IMPORTANT]
> Azure Portal nie obsługuje tworzenia grup trybu failover w różnych subskrypcjach. Ponadto w przypadku istniejących grup trybu failover w różnych subskrypcjach i/lub grupach zasobów nie można zainicjować trybu failover ręcznie za pośrednictwem portalu z podstawowego wystąpienia zarządzanego SQL. Zamiast tego należy zainicjować go z wystąpienia pomocniczego obszaru geograficznego.

### <a name="managing-failover-to-secondary-instance"></a>Zarządzanie trybem failover do wystąpienia pomocniczego

Grupa trybu failover będzie zarządzać trybem failover wszystkich baz danych w usłudze SQL Managed Instance. Gdy grupa zostanie utworzona, każda baza danych w wystąpieniu zostanie automatycznie zreplikowana geograficznie do pomocniczego wystąpienia usługi SQL Managed Instance. Nie można użyć grup trybu failover w celu zainicjowania częściowego trybu failover dla podzestawu baz danych.

> [!IMPORTANT]
> Jeśli baza danych zostanie usunięta z podstawowego wystąpienia zarządzanego SQL, zostanie ona również porzucona automatycznie w wystąpieniu zarządzanym geograficznie pomocnicze wystąpienie bazy danych SQL.

### <a name="using-read-write-listener-for-oltp-workload"></a>Używanie odbiornika do odczytu i zapisu w obciążeniu OLTP

Podczas wykonywania operacji OLTP Użyj `<fog-name>.zone_id.database.windows.net` jako adresu URL serwera, a połączenia są automatycznie kierowane do podstawowego. Ten adres URL nie zmienia się po przełączeniu w tryb failover. Tryb failover obejmuje aktualizację rekordu DNS, dlatego połączenia klienckie są przekierowywane do nowego serwera podstawowego dopiero po odświeżeniu pamięci podręcznej DNS klienta. Ponieważ wystąpienie pomocnicze współużytkuje strefę DNS z serwerem podstawowym, aplikacja kliencka będzie mogła ponownie połączyć się z nim przy użyciu tego samego certyfikatu sieci SAN.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Używanie odbiornika z tylko odczytem do nawiązywania połączenia z wystąpieniem pomocniczym

Jeśli istnieje logicznie izolowane obciążenie przeznaczone tylko do odczytu, które jest odporne na określoną nieaktualność danych, możesz użyć pomocniczej bazy danych w aplikacji. Aby nawiązać bezpośrednie połączenie z replikacją geograficzną, użyj `<fog-name>.secondary.<zone_id>.database.windows.net` jako adresu URL serwera, a połączenie jest nawiązywane bezpośrednio z bazą replikacji geograficznej.

> [!NOTE]
> W warstwach usług premium, Krytyczne dla działania firmy i skalowania, SQL Database obsługuje używanie [replik tylko](read-scale-out.md) do odczytu do uruchamiania obciążeń zapytań tylko do odczytu przy użyciu pojemności co najmniej jednej repliki tylko do odczytu, przy użyciu `ApplicationIntent=ReadOnly` parametru w parametrach połączenia. Jeśli skonfigurowano pomocnicze wystąpienie replikowane geograficznie, można użyć tej funkcji do łączenia się z repliką tylko do odczytu w lokalizacji podstawowej lub w lokalizacji zreplikowanej geograficznie.
>
> - Aby nawiązać połączenie z repliką tylko do odczytu w lokalizacji głównej, użyj `ApplicationIntent=ReadOnly` i `<fog-name>.<zone_id>.database.windows.net` .
> - Aby nawiązać połączenie z repliką tylko do odczytu w lokalizacji pomocniczej, użyj `ApplicationIntent=ReadOnly` i `<fog-name>.secondary.<zone_id>.database.windows.net` .

### <a name="preparing-for-performance-degradation"></a>Przygotowanie do obniżenia wydajności

Typowa aplikacja platformy Azure używa wielu usług platformy Azure i składa się z wielu składników. Automatyczna praca awaryjna grupy trybu failover jest wyzwalana na podstawie stanu samych składników usługi Azure SQL. Awaria może nie wpływać na inne usługi platformy Azure w regionie podstawowym, a ich składniki nadal mogą być dostępne w tym regionie. Po przełączeniu podstawowych baz danych do regionu pomocniczego opóźnienie między składnikami zależnymi może się zwiększyć. Aby uniknąć wpływu wyższego opóźnienia na wydajność aplikacji, należy upewnić się, że nadmiarowość wszystkich składników aplikacji w regionie pomocniczym i przełączenie w tryb failover składników aplikacji razem z bazą danych. W czasie konfiguracji postępuj zgodnie z [zaleceniami dotyczącymi zabezpieczeń sieci](#failover-groups-and-network-security) , aby zapewnić łączność z bazą danych w regionie pomocniczym.

### <a name="preparing-for-data-loss"></a>Przygotowywanie do utraty danych

Jeśli zostanie wykryta awaria, tryb failover do odczytu i zapisu jest wyzwalany, jeśli nie ma utraty danych, a najlepiej z naszej wiedzy. W przeciwnym razie tryb failover jest odroczony dla okresu określonego przy użyciu `GracePeriodWithDataLossHours` . Jeśli określono `GracePeriodWithDataLossHours` , przygotuj się na utratę danych. Ogólnie rzecz biorąc, platforma Azure preferuje dostępność. Jeśli nie możesz zapewnić utraty danych, pamiętaj, aby ustawić GracePeriodWithDataLossHours na wystarczająco dużą liczbę, na przykład 24 godziny, lub Wyłącz automatyczną pracę awaryjną.

Aktualizacja systemu DNS odbiornika do odczytu i zapisu stanie się natychmiast po zainicjowaniu trybu failover. Ta operacja nie powoduje utraty danych. Jednak proces przełączania ról bazy danych może potrwać do 5 minut w normalnych warunkach. Dopóki nie zostanie ukończona, niektóre bazy danych w nowym wystąpieniu podstawowym nadal będą tylko do odczytu. Jeśli zostanie zainicjowane przejście w tryb failover przy użyciu programu PowerShell, operacja przełączenia roli repliki podstawowej jest synchroniczna. Jeśli zostanie zainicjowany przy użyciu Azure Portal, interfejs użytkownika będzie wskazywać stan ukończenia. Jeśli zostanie zainicjowany przy użyciu interfejsu API REST, należy użyć mechanizmu sondowania standardowej Azure Resource Manager, aby monitorować ukończenie.

> [!IMPORTANT]
> Aby przenieść Primaries z powrotem do oryginalnej lokalizacji, Użyj ręcznego trybu failover grupy. Gdy awaria, która spowodowała przejście w tryb failover, jest zmniejszana, można przenieść podstawowe bazy danych do oryginalnej lokalizacji. W tym celu należy zainicjować ręczną pracę awaryjną grupy.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Zmienianie regionu pomocniczego grupy trybu failover

Załóżmy, że wystąpienie A jest wystąpieniem podstawowym, wystąpienie B jest istniejącym wystąpieniem pomocniczym, a wystąpienie C jest nowym wystąpieniem pomocniczym w trzecim regionie.  Aby dokonać przejścia, wykonaj następujące kroki:

1. Utwórz wystąpienie C o takim samym rozmiarze jak i w tej samej strefie DNS.
2. Usuń grupę trybu failover między wystąpieniami A i B. W tym momencie logowania będą kończyć się niepowodzeniem, ponieważ aliasy SQL dla odbiorników grupy trybu failover zostały usunięte, a Brama nie rozpozna nazwy grupy trybu failover. Pomocnicze bazy danych zostaną rozłączone z Primaries i staną się bazami danych do odczytu i zapisu.
3. Utwórz grupę trybu failover o tej samej nazwie między wystąpieniem A i C. Postępuj zgodnie z instrukcjami w [grupie trybu failover z użyciem samouczka wystąpienia zarządzanego SQL](../managed-instance/failover-group-add-instance-tutorial.md). Jest to operacja o rozmiarze danych i zostanie ukończona, gdy wszystkie bazy danych z wystąpienia A zostaną rozłączone i zsynchronizowane.
4. Usuń wystąpienie B, jeśli nie jest potrzebne, aby uniknąć niepotrzebnych opłat.

> [!NOTE]
> Po wykonaniu kroku 2 i zakończeniu kroku 3 bazy danych w wystąpieniu A pozostaną niechronione przed uszkodzeniem wystąpienia A.

### <a name="changing-primary-region-of-the-failover-group"></a>Zmienianie regionu podstawowego grupy trybu failover

Załóżmy, że wystąpienie A jest wystąpieniem podstawowym, wystąpienie B jest istniejącym wystąpieniem pomocniczym, a wystąpienie C jest nowym wystąpieniem podstawowym w trzecim regionie.  Aby dokonać przejścia, wykonaj następujące kroki:

1. Utwórz wystąpienie C o takim samym rozmiarze jak B i w tej samej strefie DNS.
2. Połącz się z wystąpieniem B i ręcznie trybem failover w celu przełączenia wystąpienia podstawowego na B. wystąpienie A stanie się automatycznie nowym wystąpieniem pomocniczym.
3. Usuń grupę trybu failover między wystąpieniami A i B. W tym momencie logowania będą kończyć się niepowodzeniem, ponieważ aliasy SQL dla odbiorników grupy trybu failover zostały usunięte, a Brama nie rozpozna nazwy grupy trybu failover. Pomocnicze bazy danych zostaną rozłączone z Primaries i staną się bazami danych do odczytu i zapisu.
4. Utwórz grupę trybu failover o tej samej nazwie między wystąpieniem A i C. Postępuj zgodnie z instrukcjami podanymi w temacie [Grupa trybu failover z wystąpieniem zarządzanym](../managed-instance/failover-group-add-instance-tutorial.md). Jest to operacja o rozmiarze danych i zostanie ukończona, gdy wszystkie bazy danych z wystąpienia A zostaną rozłączone i zsynchronizowane.
5. Usuń wystąpienie A, jeśli nie jest potrzebne, aby uniknąć niepotrzebnych opłat.

> [!CAUTION]
> Po kroku 3 i do momentu zakończenia krok 4 bazy danych w wystąpieniu A pozostaną niechronione przed uszkodzeniem wystąpienia A.

> [!IMPORTANT]
> Po usunięciu grupy trybu failover rekordy DNS dla punktów końcowych odbiornika również są usuwane. W tym momencie istnieje prawdopodobieństwo, że ktoś inny tworzy grupę trybu failover lub alias serwera o tej samej nazwie, co uniemożliwi jego ponowne użycie. Aby zminimalizować ryzyko, nie używaj ogólnych nazw grup trybu failover.

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>Włącz scenariusze zależne od obiektów z systemowych baz danych
Systemowe bazy danych nie są replikowane do wystąpienia dodatkowego w grupie trybu failover. Aby włączyć scenariusze, które są zależne od obiektów z systemowych baz danych, w wystąpieniu pomocniczym upewnij się, że utworzono te same obiekty na pomocniczym. Na przykład jeśli planujesz używać tych samych nazw logowania w wystąpieniu pomocniczym, upewnij się, że zostały one utworzone przy użyciu identycznego identyfikatora SID. 
```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 


## <a name="failover-groups-and-network-security"></a>Grupy trybu failover i zabezpieczenia sieci

W przypadku niektórych aplikacji reguły zabezpieczeń wymagają, aby dostęp sieciowy do warstwy danych był ograniczony do określonego składnika lub składników, takich jak maszyna wirtualna, usługa sieci Web itd. To wymaganie wiąże się z pewnymi wyzwaniami związanymi z projektowaniem ciągłości biznesowej i użyciem grup trybu failover. Podczas wdrażania takiego ograniczonego dostępu należy wziąć pod uwagę następujące opcje.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Korzystanie z grup trybu failover i reguł sieci wirtualnej

Jeśli używasz [punktów końcowych usługi Virtual Network i reguł](vnet-service-endpoint-rule-overview.md) w celu ograniczenia dostępu do bazy danych w SQL Database lub wystąpieniu zarządzanym SQL, pamiętaj, że każdy punkt końcowy usługi sieci wirtualnej ma zastosowanie tylko do jednego regionu platformy Azure. Punkt końcowy nie umożliwia innym regionom akceptowania komunikacji z podsieci. W związku z tym tylko aplikacje klienckie wdrożone w tym samym regionie mogą łączyć się z podstawową bazą danych. Ponieważ w trybie failover wyniki sesji klientów SQL Database są przekierowywane do serwera w innym (pomocniczym) regionie, te sesje zakończą się niepowodzeniem, jeśli pochodzą z klienta spoza tego regionu. Z tego powodu nie można włączyć zasad automatycznych trybu failover, jeśli serwery lub wystąpienia uczestniczące zostaną uwzględnione w regułach Virtual Network. Aby zapewnić obsługę ręcznego przełączania do trybu failover, wykonaj następujące kroki:

1. Udostępnianie nadmiarowych kopii składników frontonu aplikacji (usługi sieci Web, maszyn wirtualnych itp.) w regionie pomocniczym
2. Skonfiguruj osobno [reguły sieci wirtualnej](vnet-service-endpoint-rule-overview.md) dla serwera głównego i pomocniczego
3. Włączanie [trybu failover frontonu przy użyciu konfiguracji usługi Traffic Manager](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Zainicjuj ręczną pracę awaryjną po wykryciu awarii. Ta opcja jest zoptymalizowana pod kątem aplikacji, które wymagają spójnego opóźnienia między frontonem a warstwą danych i obsługuje odzyskiwanie w przypadku, gdy awaria występuje zarówno na frontonie, na warstwie danych, jak i w obu tych przypadkach.

> [!NOTE]
> Jeśli używasz **odbiornika z tylko odczytem** , aby zrównoważyć obciążenie tylko do odczytu, upewnij się, że to obciążenie jest wykonywane na maszynie wirtualnej lub innym zasobie w regionie pomocniczym, dzięki czemu można nawiązać połączenie z pomocniczą bazą danych.

### <a name="use-failover-groups-and-firewall-rules"></a>Korzystanie z grup trybu failover i reguł zapory

Jeśli plan ciągłości działalności biznesowej wymaga przejścia w tryb failover przy użyciu grup z automatyczną obsługą trybu failover, możesz ograniczyć dostęp do bazy danych w SQL Database przy użyciu tradycyjnych reguł zapory. Aby zapewnić obsługę automatycznego trybu failover, wykonaj następujące kroki:

1. [Tworzenie publicznego adresu IP](../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Utwórz publiczny moduł równoważenia obciążenia](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) i przypisz do niego publiczny adres IP.
3. [Tworzenie sieci wirtualnej i maszyn wirtualnych](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) dla składników frontonu
4. [Utwórz sieciową grupę zabezpieczeń](../../virtual-network/network-security-groups-overview.md) i skonfiguruj połączenia przychodzące.
5. Upewnij się, że połączenia wychodzące są otwarte do Azure SQL Database przy użyciu [tagu usługi](../../virtual-network/network-security-groups-overview.md#service-tags)"SQL".
6. Utwórz [regułę zapory SQL Database](firewall-configure.md) , aby zezwalać na ruch przychodzący z publicznego adresu IP utworzonego w kroku 1.

Aby uzyskać więcej informacji na temat konfigurowania dostępu wychodzącego i adresu IP do użycia w regułach zapory, zobacz [połączenia wychodzące modułu równoważenia obciążenia](../../load-balancer/load-balancer-outbound-connections.md).

Powyższa konfiguracja gwarantuje, że automatyczna praca awaryjna nie będzie blokować połączeń z składników frontonu i zakłada, że aplikacja może tolerować dłuższy czas oczekiwania między frontonem a warstwą danych.

> [!IMPORTANT]
> Aby zagwarantować ciągłość działania w regionie awarii, należy zapewnić geograficzną nadmiarowość dla składników frontonu i baz danych.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Włączanie replikacji geograficznej między wystąpieniami zarządzanymi i ich sieciami wirtualnymi

Po skonfigurowaniu grupy trybu failover między podstawowym i pomocniczym wystąpieniem zarządzanym SQL w dwóch różnych regionach każde wystąpienie jest izolowane przy użyciu niezależnej sieci wirtualnej. Aby zezwolić na ruch związany z replikacją między tymi sieci wirtualnychami, upewnij się, że spełniono następujące wymagania wstępne:

- Dwa wystąpienia wystąpienia zarządzanego SQL muszą znajdować się w różnych regionach świadczenia usługi Azure.
- Dwa wystąpienia wystąpienia zarządzanego SQL muszą być tą samą warstwą usług i mieć ten sam rozmiar magazynu.
- Dodatkowe wystąpienie wystąpienia zarządzanego SQL musi być puste (bez baz danych użytkowników).
- Sieci wirtualne używane przez wystąpienia wystąpienia zarządzanego SQL muszą być połączone za pomocą [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [Express Route](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md). Gdy dwie sieci wirtualne nawiązują połączenie za pośrednictwem sieci lokalnej, upewnij się, że nie ma reguły zapory blokującej porty 5022 i 11000-11999. Globalna komunikacja równorzędna sieci wirtualnych jest obsługiwana z ograniczeniami opisanymi w poniższej uwadze.

   > [!IMPORTANT]
   > [W dniu 9/22/2020 ogłoszono globalne wirtualne sieci równorzędne dla nowo utworzonych klastrów wirtualnych](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Oznacza to, że globalne wirtualne sieci równorzędne są obsługiwane dla wystąpień zarządzanych SQL utworzonych w pustych podsieciach, a także dla wszystkich kolejnych wystąpień zarządzanych utworzonych w tych podsieciach. W przypadku wszystkich innych obsługi komunikacji równorzędnej usługi SQL Managed Instances jest ograniczone do sieci w tym samym regionie ze względu na [ograniczenia globalnej komunikacji równorzędnej sieci wirtualnej](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Więcej informacji można znaleźć w sekcji dotyczącej [często zadawanych pytań dotyczących usługi Azure Virtual Networks](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . 

- Dwa sieci wirtualnych wystąpienie zarządzane SQL nie może mieć nakładających się adresów IP.
- Należy skonfigurować sieciowe grupy zabezpieczeń tak, aby porty 5022 i zakres 11000 ~12000 były dla połączeń przychodzących i wychodzących z podsieci drugiego wystąpienia zarządzanego. Ma to na celu umożliwienie ruchu związanego z replikacją pomiędzy wystąpieniami.

   > [!IMPORTANT]
   > Nieprawidłowo skonfigurowane reguły zabezpieczeń sieciowej grupy zabezpieczeń prowadzą do zablokowanych operacji kopiowania bazy danych.

- Na pomocniczym wystąpieniu zarządzanym SQL jest skonfigurowany prawidłowy identyfikator strefy DNS. Strefa DNS jest właściwością wystąpienia zarządzanego SQL i bazowego klastra wirtualnego, a jego identyfikator jest zawarty w adresie nazwy hosta. Identyfikator strefy jest generowany jako ciąg losowy podczas tworzenia pierwszego wystąpienia zarządzanego SQL w każdej sieci wirtualnej, a ten sam identyfikator jest przypisany do wszystkich innych wystąpień w tej samej podsieci. Po przypisaniu strefa DNS nie może być modyfikowana. Wystąpienia zarządzane SQL uwzględnione w tej samej grupie trybu failover muszą współdzielić strefę DNS. W tym celu należy przekazać Identyfikator strefy wystąpienia podstawowego jako wartość parametru DnsZonePartner podczas tworzenia wystąpienia pomocniczego.

   > [!NOTE]
   > Szczegółowy samouczek dotyczący konfigurowania grup trybu failover z wystąpieniem zarządzanym SQL można znaleźć w temacie [Dodawanie wystąpienia zarządzanego SQL do grupy trybu failover](../managed-instance/failover-group-add-instance-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Uaktualnianie lub obniżanie poziomu podstawowej bazy danych

Możesz uaktualnić lub obniżyć podstawową bazę danych do innego rozmiaru obliczeniowego (w ramach tej samej warstwy usług, a nie między Ogólnego przeznaczenia i Krytyczne dla działania firmy) bez rozłączania pomocniczych baz danych. Podczas uaktualniania zalecamy najpierw uaktualnić wszystkie pomocnicze bazy danych, a następnie uaktualnić podstawową. W przypadku obniżenia poziomu należy odwrócić kolejność: najpierw Obniż poziom podstawowego, a następnie obniżyć wszystkie pomocnicze bazy danych. W przypadku uaktualnienia lub obniżenia poziomu bazy danych do innej warstwy usługi to zalecenie jest wymuszane.

Ta sekwencja jest zalecana specjalnie po to, aby uniknąć problemu polegającego na tym, że wystąpienie pomocnicze z niższym poziomem jednostki SKU zostaje przeciążone i musi zostać ponownie rozmieszczone podczas procesu podwyższania lub obniżania poziomu. Można również uniknąć problemu, tworząc wystąpienie podstawowe tylko do odczytu, kosztem wpływu na wszystkie obciążenia odczytu i zapisu względem wystąpienia podstawowego.

> [!NOTE]
> Jeśli utworzono pomocniczą bazę danych w ramach konfiguracji grupy trybu failover, nie jest zalecane obniżenie poziomu pomocniczej bazy danych. Ma to na celu zapewnienie wystarczającej wydajności warstwy danych do przetwarzania zwykłego obciążenia po aktywowaniu trybu failover.

## <a name="preventing-the-loss-of-critical-data"></a>Zapobieganie utracie danych o kluczowym znaczeniu

Ze względu na duże opóźnienie sieci rozległej, ciągła kopia używa mechanizmu replikacji asynchronicznej. Replikacja asynchroniczna powoduje nieuniknięcie utraty danych w przypadku wystąpienia błędu. Jednak niektóre aplikacje mogą nie wymagać utraty danych. Aby chronić te aktualizacje krytyczne, Deweloper aplikacji może wywoływać procedurę systemu [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) natychmiast po zatwierdzeniu transakcji. Wywołanie `sp_wait_for_database_copy_sync` blokuje wątek wywołujący do momentu przekazania ostatniej zatwierdzonej transakcji do pomocniczej bazy danych. Jednak nie czeka na odtworzenie i zatwierdzenie wysłanych transakcji na serwerze pomocniczym. `sp_wait_for_database_copy_sync` jest zakresem do określonego linku ciągłego kopiowania. Każdy użytkownik z uprawnieniami do nawiązywania połączenia z podstawową bazą danych może wywoływać tę procedurę.

> [!NOTE]
> `sp_wait_for_database_copy_sync` zapobiega utracie danych po przejściu w tryb failover, ale nie gwarantuje pełnej synchronizacji dostępu do odczytu. Opóźnienie spowodowane przez `sp_wait_for_database_copy_sync` wywołanie procedury może być znaczące i zależy od rozmiaru dziennika transakcji w czasie wywołania.

## <a name="failover-groups-and-point-in-time-restore"></a>Grupy trybu failover i przywracanie do punktu w czasie

Informacje o korzystaniu z funkcji przywracania do punktu w czasie z grupami trybu failover można znaleźć [w temacie odzyskiwanie do punktu w czasie (kopie)](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations-of-failover-groups"></a>Ograniczenia grup trybu failover

Należy pamiętać o następujących ograniczeniach:

- Nie można tworzyć grup trybu failover między dwoma serwerami lub wystąpieniami w tym samym regionie świadczenia usługi Azure.
- Nie można zmienić nazwy grup trybu failover. Musisz usunąć grupę i utworzyć ją ponownie, podając inną nazwę.
- Zmiana nazwy bazy danych nie jest obsługiwana w przypadku wystąpień w grupie trybu failover. Należy tymczasowo usunąć grupę trybu failover, aby można było zmienić nazwę bazy danych.
- Systemowe bazy danych nie są replikowane do wystąpienia dodatkowego w grupie trybu failover. W związku z tym scenariusze, które są zależne od obiektów z systemowych baz danych, będą niedostępne w wystąpieniu pomocniczym, chyba że obiekty są tworzone ręcznie na serwerze pomocniczym.

## <a name="programmatically-managing-failover-groups"></a>Programowe zarządzanie grupami trybu failover

Jak wspomniano wcześniej, grupy autotrybu failover i aktywnej replikacji geograficznej mogą być również zarządzane programowo przy użyciu Azure PowerShell i interfejsu API REST. W poniższych tabelach opisano zestaw dostępnych poleceń. Aktywna replikacja geograficzna obejmuje zestaw Azure Resource Manager interfejsów API do zarządzania, w tym [Azure SQL Database interfejsu API REST](/rest/api/sql/) i [poleceń cmdlet Azure PowerShell](/powershell/azure/). Te interfejsy API wymagają używania grup zasobów i obsługują kontrolę dostępu opartą na rolach (Azure RBAC). Aby uzyskać więcej informacji na temat implementowania ról dostępu, zobacz [Kontrola dostępu oparta na rolach na platformie Azure (RBAC)](../../role-based-access-control/overview.md).

### <a name="manage-sql-database-failover"></a>Zarządzanie trybem failover SQL Database

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

| Polecenie cmdlet | Opis |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |To polecenie tworzy grupę trybu failover i rejestruje ją na serwerze podstawowym i pomocniczym|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Usuwa grupę trybu failover z serwera |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Pobiera konfigurację grupy trybu failover |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modyfikuje konfigurację grupy trybu failover |
| [Przełącznik-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Wyzwala tryb failover grupy trybu failover na serwerze pomocniczym |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Dodaje co najmniej jedną bazę danych do grupy trybu failover|

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

| Polecenie | Opis |
| --- | --- |
| [AZ SQL failover-Group Create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |To polecenie tworzy grupę trybu failover i rejestruje ją na serwerze podstawowym i pomocniczym|
| [AZ SQL failover-Group Delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Usuwa grupę trybu failover z serwera |
| [AZ SQL failover-Group Show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Pobiera konfigurację grupy trybu failover |
| [AZ SQL failover — aktualizacja grupy](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Modyfikuje konfigurację grupy trybu failover i/lub dodaje co najmniej jedną bazę danych do grupy trybu failover|
| [AZ SQL failover-Group Set-Primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Wyzwala tryb failover grupy trybu failover na serwerze pomocniczym |

# <a name="rest-api"></a>[Interfejs API REST](#tab/rest-api)

| Interfejs API | Opis |
| --- | --- |
| [Utwórz lub Zaktualizuj grupę trybu failover](/rest/api/sql/failovergroups/createorupdate) | Tworzy lub aktualizuje grupę trybu failover |
| [Usuń grupę trybu failover](/rest/api/sql/failovergroups/delete) | Usuwa grupę trybu failover z serwera |
| [Tryb failover (zaplanowany)](/rest/api/sql/failovergroups/failover) | Wyzwala tryb failover z bieżącego serwera podstawowego na serwerze pomocniczym z pełną synchronizacją danych.|
| [Wymuś utratę danych w trybie failover](/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Wyzwala tryb failover z bieżącego serwera podstawowego na serwerze pomocniczym bez synchronizowania danych. Ta operacja może spowodować utratę danych. |
| [Pobierz grupę trybu failover](/rest/api/sql/failovergroups/get) | Pobiera konfigurację grupy trybu failover. |
| [Wyświetl listę grup trybu failover według serwera](/rest/api/sql/failovergroups/listbyserver) | Wyświetla listę grup trybu failover na serwerze. |
| [Aktualizuj grupę trybu failover](/rest/api/sql/failovergroups/update) | Aktualizuje konfigurację grupy trybu failover. |

---

### <a name="manage-sql-managed-instance-failover"></a>Zarządzanie trybem failover wystąpienia zarządzanego SQL


# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

| Polecenie cmdlet | Opis |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |To polecenie tworzy grupę trybu failover i rejestruje ją w wystąpieniach podstawowych i pomocniczych|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Modyfikuje konfigurację grupy trybu failover|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Pobiera konfigurację grupy trybu failover|
| [Przełącznik-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Wyzwala tryb failover grupy trybu failover w wystąpieniu pomocniczym|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Usuwa grupę trybu failover|


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

| Polecenie | Opis |
| --- | --- |
| [AZ SQL failover-Group Create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |To polecenie tworzy grupę trybu failover i rejestruje ją na serwerze podstawowym i pomocniczym|
| [AZ SQL failover-Group Delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Usuwa grupę trybu failover z serwera |
| [AZ SQL failover-Group Show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Pobiera konfigurację grupy trybu failover |
| [AZ SQL failover — aktualizacja grupy](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Modyfikuje konfigurację grupy trybu failover i/lub dodaje co najmniej jedną bazę danych do grupy trybu failover|
| [AZ SQL failover-Group Set-Primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Wyzwala tryb failover grupy trybu failover na serwerze pomocniczym |

# <a name="rest-api"></a>[Interfejs API REST](#tab/rest-api)

| Interfejs API | Opis |
| --- | --- |
| [Utwórz lub Zaktualizuj grupę trybu failover](/rest/api/sql/instancefailovergroups/createorupdate) | Tworzy lub aktualizuje konfigurację grupy trybu failover |
| [Usuń grupę trybu failover](/rest/api/sql/instancefailovergroups/delete) | Usuwa grupę trybu failover z wystąpienia |
| [Tryb failover (zaplanowany)](/rest/api/sql/instancefailovergroups/failover) | Wyzwala przejście w tryb failover z bieżącego wystąpienia podstawowego do tego wystąpienia z pełną synchronizacją danych. |
| [Wymuś utratę danych w trybie failover](/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Wyzwala przejście w tryb failover z bieżącego wystąpienia podstawowego do wystąpienia pomocniczego bez synchronizowania danych. Ta operacja może spowodować utratę danych. |
| [Pobierz grupę trybu failover](/rest/api/sql/instancefailovergroups/get) | Pobiera konfigurację grupy trybu failover. |
| [Lista grup trybu failover — lista według lokalizacji](/rest/api/sql/instancefailovergroups/listbylocation) | Wyświetla listę grup trybu failover w lokalizacji. |

---

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z szczegółowymi samouczkami, zobacz
  - [Dodawanie SQL Database do grupy trybu failover](failover-group-add-single-database-tutorial.md)
  - [Dodawanie elastycznej puli do grupy trybu failover](failover-group-add-elastic-pool-tutorial.md)
  - [Dodawanie wystąpienia zarządzanego SQL do grupy trybu failover](../managed-instance/failover-group-add-instance-tutorial.md)
- Aby zapoznać się z przykładowymi skryptami, zobacz:
  - [Konfigurowanie aktywnej replikacji geograficznej dla Azure SQL Database przy użyciu programu PowerShell](scripts/setup-geodr-and-failover-database-powershell.md)
  - [Konfigurowanie aktywnej replikacji geograficznej dla bazy danych w puli w usłudze Azure SQL Database przy użyciu programu PowerShell](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [Dodawanie Azure SQL Database do grupy trybu failover przy użyciu programu PowerShell](scripts/add-database-to-failover-group-powershell.md)
- Aby zapoznać się z omówieniem i scenariuszami ciągłości działania, zobacz temat [ciągłość działania — Omówienie](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Aby dowiedzieć się więcej o Azure SQL Database zautomatyzowanych kopii zapasowych, zobacz [SQL Database zautomatyzowane kopie zapasowe](automated-backups-overview.md).
- Aby dowiedzieć się więcej o korzystaniu z automatycznych kopii zapasowych na potrzeby odzyskiwania, zobacz [przywracanie bazy danych z kopii zapasowych inicjowanych przez usługę](recovery-using-backups.md).
- Aby dowiedzieć się więcej o wymaganiach dotyczących uwierzytelniania dla nowego serwera podstawowego i bazy danych, zobacz [SQL Database zabezpieczenia po odzyskiwaniu po awarii](active-geo-replication-security-configure.md).
