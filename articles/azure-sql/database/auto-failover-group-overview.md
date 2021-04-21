---
title: Grupy automatycznego trybu failover
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Grupy automatycznego trybu failover umożliwiają zarządzanie replikacją i automatycznym/skoordynowanym trybem failover grupy baz danych na serwerze lub we wszystkich bazach danych w wystąpieniu zarządzanym.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 03/26/2021
ms.openlocfilehash: f3bc1dfcfeeb6dda110f71ed7a1c53909153cf00
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762159"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Używanie grup automatycznego trybu failover w celu umożliwienia przezroczystego i skoordynowanego trybu failover wielu baz danych
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Funkcja grup automatycznego trybu failover umożliwia zarządzanie replikacją i trybem failover grupy baz danych na serwerze lub wszystkich baz danych w wystąpieniu zarządzanym w innym regionie. Jest to deklaratywna abstrakcja na podstawie istniejącej aktywnej funkcji replikacji geograficznej, która została zaprojektowana w celu uproszczenia wdrażania baz danych replikowanych geograficznie na dużą skalę i zarządzania nimi. [](active-geo-replication-overview.md) Możesz zainicjować trybu failover ręcznie lub delegować go do usługi platformy Azure na podstawie zasad zdefiniowanych przez użytkownika. Druga opcja umożliwia automatyczne odzyskanie wielu powiązanych baz danych w regionie pomocniczym po katastrofalnej awarii lub innym nieplanowanym zdarzeniu, które powoduje pełną lub częściową utratę dostępności usługi SQL Database lub SQL Managed Instance w regionie podstawowym. Grupa trybu failover może zawierać jedną lub wiele baz danych, zwykle używanych przez tę samą aplikację. Ponadto można odciążać obciążenia zapytań tylko do odczytu przy użyciu pomocniczych baz danych z czytelnymi elementami. Ponieważ grupy automatycznego trybu failover obejmują wiele baz danych, te bazy danych muszą być skonfigurowane na serwerze podstawowym. Grupy automatycznego trybu failover obsługują replikację wszystkich baz danych w grupie tylko do jednego pomocniczego serwera lub wystąpienia w innym regionie.

> [!NOTE]
> Jeśli chcesz, aby wiele Azure SQL Database w tym samym lub różnych regionach, użyj [aktywnej replikacji geograficznej.](active-geo-replication-overview.md)

Jeśli używasz grup automatycznego trybu failover z zasadami automatycznego trybu failover, każda awarii, która ma wpływ na co najmniej jedną z baz danych w grupie powoduje automatyczne trybu failover. Zazwyczaj są to zdarzenia, które nie mogą zostać samodzielnie skorygowane przez wbudowane automatyczne operacje wysokiej dostępności. Przykłady wyzwalaczy trybu failover obejmują zdarzenie spowodowane awarią pierścienia dzierżawy lub pierścienia sterowania usługi SQL Database z powodu przecieku pamięci jądra systemu operacyjnego na kilku węzłach obliczeniowych lub zdarzenia spowodowanego awarią co najmniej jednego pierścienia dzierżawy, ponieważ podczas rutynowego likwidowania sprzętu został obcięty nieprawidłowy kabel sieciowy.  Aby uzyskać więcej informacji, [zobacz SQL Database wysoka dostępność.](high-availability-sla.md)

Ponadto grupy automatycznego trybu failover zapewniają punkty końcowe odbiornika tylko do odczytu i zapisu, które pozostają niezmienione podczas pracy w trybie failover. Niezależnie od tego, czy jest to aktywacja ręczna, czy automatyczna, tryb failover przełącza wszystkie pomocnicze bazy danych w grupie na podstawowe. Po zakończeniu trybu failover bazy danych rekord DNS jest automatycznie aktualizowany w celu przekierowania punktów końcowych do nowego regionu. Aby uzyskać konkretne dane RPO i RTO, zobacz [Overview of Business Continuity (Omówienie ciągłości działania).](business-continuity-high-availability-disaster-recover-hadr-overview.md)

W przypadku korzystania z grup automatycznego trybu failover z zasadami automatycznego trybu failover każda awarii, która ma wpływ na bazy danych na serwerze lub w wystąpieniu zarządzanym, powoduje automatyczne pracę w trybie failover. Grupą automatycznego trybu failover można zarządzać przy użyciu:

- [Witryna Azure Portal](geo-distributed-application-configure-tutorial.md)
- [Interfejs wiersza polecenia platformy Azure: grupa trybu failover](scripts/add-database-to-failover-group-cli.md)
- [PowerShell: grupa trybu failover](scripts/add-database-to-failover-group-powershell.md)
- [Interfejs API REST: grupa trybu failover](/rest/api/sql/failovergroups)

Po zakończeniu pracy w trybu failover upewnij się, że wymagania dotyczące uwierzytelniania dla bazy danych i serwera lub wystąpienia zostały skonfigurowane na nowej bazie danych i w nowym wystąpieniu podstawowym. Aby uzyskać szczegółowe informacje, [zobacz SQL Database zabezpieczeń po odzyskiwaniu po awarii.](active-geo-replication-security-configure.md)

Aby osiągnąć rzeczywistą ciągłość działalności biznesowej, dodanie nadmiarowości bazy danych między centrami danych jest tylko częścią rozwiązania. Odzyskiwanie aplikacji (usługi) end-to-end po katastrofalnej awarii wymaga odzyskania wszystkich składników, które stanowią usługę, i wszystkich usług zależnych. Przykładami tych składników są oprogramowanie klienckie (na przykład przeglądarka z niestandardowym kodem JavaScript), fronton sieci Web, magazyn i system DNS. Bardzo ważne jest, aby wszystkie składniki były odporne na te same awarie i były dostępne w ramach celu czasu odzyskiwania (RTO) aplikacji. W związku z tym należy zidentyfikować wszystkie usługi zależne oraz zrozumieć zapewniane przez nie gwarancje i możliwości. Następnie należy podjąć odpowiednie kroki, aby upewnić się, że usługa działa podczas trybu failover usług, od których zależy. Aby uzyskać więcej informacji na temat projektowania rozwiązań do odzyskiwania po awarii, zobacz Projektowanie rozwiązań w chmurze do odzyskiwania po awarii [przy użyciu aktywnej replikacji geograficznej.](designing-cloud-solutions-for-disaster-recovery.md)

## <a name="terminology-and-capabilities"></a>Terminologia i możliwości

- **Grupa trybu failover (TEŻ)**

  Grupa trybu failover to nazwana grupa baz danych zarządzanych przez pojedynczy serwer lub w ramach wystąpienia zarządzanego, która może przejść w tryb failover jako jednostka do innego regionu w przypadku, gdy wszystkie lub niektóre podstawowe bazy danych staną się niedostępne z powodu awarii w regionie podstawowym. Po utworzeniu dla SQL Managed Instance trybu failover grupa zawiera wszystkie bazy danych użytkowników w wystąpieniu i w związku z tym w wystąpieniu można skonfigurować tylko jedną grupę trybu failover.
  
  > [!IMPORTANT]
  > Nazwa grupy trybu failover musi być globalnie unikatowa w obrębie `.database.windows.net` domeny.

- **Serwery**

     W przypadku serwerów niektóre lub wszystkie bazy danych użytkowników na serwerze można umieścić w grupie trybu failover. Ponadto serwer obsługuje wiele grup trybu failover na jednym serwerze.

- **Podstawowe**

  Serwer lub wystąpienie zarządzane, które hostuje podstawowe bazy danych w grupie trybu failover.

- **Pomocniczy**

  Serwer lub wystąpienie zarządzane, które hostuje pomocnicze bazy danych w grupie trybu failover. Pomocniczy nie może być w tym samym regionie co podstawowy.

- **Dodawanie pojedynczych baz danych do grupy trybu failover**

  Można umieścić kilka pojedynczych baz danych na tym samym serwerze w tej samej grupie trybu failover. Dodanie pojedynczej bazy danych do grupy trybu failover powoduje automatyczne utworzenie pomocniczej bazy danych przy użyciu tej samej wersji i rozmiaru obliczeniowego na serwerze pomocniczym.  Ten serwer został określony podczas tworzenia grupy trybu failover. Jeśli dodasz bazę danych, która ma już pomocniczą bazę danych na serwerze pomocniczym, to łącze replikacji geograficznej jest dziedziczone przez grupę. Po dodaniu bazy danych, która ma już pomocniczą bazę danych na serwerze, który nie jest częścią grupy trybu failover, na serwerze pomocniczym jest tworzony nowy element pomocniczy.

  > [!IMPORTANT]
  > Upewnij się, że serwer pomocniczy nie ma bazy danych o tej samej nazwie, chyba że jest to istniejąca pomocnicza baza danych. W grupach trybu failover SQL Managed Instance replikowane są wszystkie bazy danych użytkowników. Nie można wybrać podzbioru baz danych użytkowników do replikacji w grupie trybu failover.

- **Dodawanie baz danych w elastycznej puli do grupy trybu failover**

  Możesz umieścić wszystkie lub kilka baz danych w elastycznej puli w tej samej grupie trybu failover. Jeśli podstawowa baza danych znajduje się w elastycznej puli, pomocnicza baza danych jest automatycznie tworzona w elastycznej puli o tej samej nazwie (puli pomocniczej). Należy upewnić się, że serwer pomocniczy zawiera elastyczną pulę o takiej samej nazwie i wystarczającej wolnej pojemności do hostowania pomocniczych baz danych, które zostaną utworzone przez grupę trybu failover. Jeśli dodasz bazę danych w puli, która ma już pomocniczą bazę danych w puli pomocniczej, to łącze replikacji geograficznej jest dziedziczone przez grupę. W przypadku dodawania bazy danych, która ma już pomocniczą bazę danych na serwerze, który nie jest częścią grupy trybu failover, w puli pomocniczej jest tworzona nowa baza danych pomocnicza.
  
- **Wstępne wstępne inicjowanie**

  Podczas dodawania baz danych, elastycznych pul lub wystąpień zarządzanych do grupy trybu failover przed rozpoczęciem replikacji danych istnieje początkowa faza wstępnego wstępnego inicjowania. Początkowa faza wstępnego inicjowania jest najdłuższą i najdroższą operacją. Po zakończeniu wstępnego inicjowania dane są synchronizowane, a następnie replikowane są tylko kolejne zmiany danych. Czas do ukończenia początkowego inicjowania zależy od rozmiaru danych, liczby replikowanych baz danych i szybkości połączenia między jednostkami w grupie trybu failover. W normalnych warunkach możliwa szybkość iniekcyjności wynosi do 500 GB na godzinę dla SQL Database, a do 360 GB na godzinę dla SQL Managed Instance. Iniekcjowanie jest wykonywane równolegle dla wszystkich baz danych.

  Na SQL Managed Instance należy wziąć pod uwagę szybkość połączenia usługi Express Route między dwoma wystąpieniami podczas szacowania czasu początkowej fazy wstępnego inicjowania. Jeśli szybkość połączenia między dwoma wystąpieniami jest mniejsza niż to, co jest konieczne, prawdopodobnie będzie to miało znaczący wpływ na czas iniekcjonu. Można użyć podanej szybkości inicjowania, liczby baz danych, całkowitego rozmiaru danych i szybkości łącza, aby oszacować, jak długo będzie trwać początkowa faza inicjowania przed rozpoczęciem replikacji danych. Na przykład w przypadku pojedynczej bazy danych o pojemności 100 GB początkowa faza inicjowania będzie trwać około 1,2 godziny, jeśli połączenie jest w stanie wypchnąć 84 GB na godzinę i jeśli nie ma żadnych innych baz danych, które są inicjowane. Jeśli łącze może przesyłać tylko 10 GB na godzinę, iniekcjowanie bazy danych o pojemności 100 GB potrwa około 10 godzin. Jeśli istnieje wiele baz danych do replikacji, wstępne wstępne inicjowanie danych ze wszystkich baz danych będzie wykonywane równolegle, a w połączeniu z powolną szybkością łącza początkowa faza inicjowania może trwać znacznie dłużej, zwłaszcza jeśli równoległe inicjowanie danych ze wszystkich baz danych przekracza dostępną przepustowość łącza. Jeśli przepustowość sieci między dwoma wystąpieniami jest ograniczona i dodajesz wiele wystąpień zarządzanych do grupy trybu failover, rozważ dodanie wielu wystąpień zarządzanych do grupy trybu failover sekwencyjnie, jeden po drugiej. Biorąc pod uwagę odpowiednio rozmiary sku bramy między dwoma wystąpieniami zarządzanymi i jeśli pozwala na to przepustowość sieci firmowej, możliwe jest osiągnięcie szybkości do 360 GB na godzinę.  

- **Strefa DNS**

  Unikatowy identyfikator generowany automatycznie po utworzeniu SQL Managed Instance aplikacji. Certyfikat z wieloma domenami (SAN) dla tego wystąpienia jest aprowizowany w celu uwierzytelniania połączeń klientów z dowolnym wystąpieniem w tej samej strefie DNS. Dwa wystąpienia zarządzane w tej samej grupie trybu failover muszą współdzielić strefę DNS.
  
  > [!NOTE]
  > Identyfikator strefy DNS nie jest wymagany dla grup trybu failover utworzonych dla SQL Database.

- **Odbiornik odczytu i zapisu grupy trybu failover**

  Rekord CNAME systemu DNS, który wskazuje adres URL bieżącego serwera podstawowego. Jest ona tworzona automatycznie podczas tworzenia grupy trybu failover i umożliwia obciążeniu odczytu i zapisu niewidoczne ponowne nawiązywanie połączenia z podstawową bazą danych po zmianie podstawowej po zakończeniu pracy w awarii. Po utworzeniu grupy trybu failover na serwerze rekord CNAME systemu DNS dla adresu URL odbiornika jest tworzony jako `<fog-name>.database.windows.net` . Gdy grupa trybu failover jest tworzona na SQL Managed Instance, rekord CNAME systemu DNS dla adresu URL odbiornika jest tworzony jako `<fog-name>.<zone_id>.database.windows.net` .

- **Odbiornik grupy trybu failover tylko do odczytu**

  Rekord CNAME systemu DNS, który wskazuje na odbiornik tylko do odczytu, który wskazuje na adres URL pomocniczego. Jest ona tworzona automatycznie podczas tworzenia grupy trybu failover i umożliwia obciążeniu SQL tylko do odczytu przezroczyste łączenie się z pomocniczą bazą danych przy użyciu określonych reguł równoważenia obciążenia. Po utworzeniu grupy trybu failover na serwerze rekord CNAME systemu DNS dla adresu URL odbiornika jest tworzony jako `<fog-name>.secondary.database.windows.net` . Gdy grupa trybu failover jest tworzona na SQL Managed Instance, rekord CNAME systemu DNS dla adresu URL odbiornika jest tworzony jako `<fog-name>.secondary.<zone_id>.database.windows.net` .

- **Zasady automatycznego trybu failover**

  Domyślnie grupa trybu failover jest konfigurowana przy użyciu zasad automatycznego trybu failover. Po wykryciu błędu i upływie okresu prolongaty platforma Azure wyzwala trybu failover. System musi sprawdzić, czy ze względu na skalę [](high-availability-sla.md) wpływu wbudowanej infrastruktury o wysokiej dostępności nie można temu uniknąć. Jeśli chcesz kontrolować przepływ pracy trybu failover z aplikacji lub ręcznie, możesz wyłączyć automatyczne tryb failover.
  
  > [!NOTE]
  > Ze względu na to, że weryfikacja skali outage i tego, jak szybko można temu uniknąć, obejmuje działania człowieka wykonywane przez zespół operacyjny, nie można ustawić okresu prolongaty poniżej jednej godziny. To ograniczenie dotyczy wszystkich baz danych w grupie trybu failover niezależnie od ich stanu synchronizacji danych.

- **Zasady trybu failover tylko do odczytu**

  Domyślnie tryb failover odbiornika tylko do odczytu jest wyłączony. Gwarantuje to, że nie ma to wpływu na wydajność podstawowej, gdy pomocnicza jest w trybie offline. Oznacza to jednak również, że sesje tylko do odczytu nie będą mogły nawiązać połączenia, dopóki pomocnicza nie zostanie odzyskana. Jeśli nie można tolerować przestojów dla sesji tylko do odczytu i można użyć podstawowego ruchu tylko do odczytu i zapisu kosztem potencjalnego spadku wydajności podstawowego, można włączyć obsługę trybu failover dla odbiornika tylko do odczytu, konfigurując właściwość `AllowReadOnlyFailoverToPrimary` . W takim przypadku ruch tylko do odczytu zostanie automatycznie przekierowany do podstawowej, jeśli pomocnicza nie jest dostępna.

  > [!NOTE]
  > Właściwość ma wpływ tylko wtedy, gdy zasady automatycznego trybu failover są włączone i `AllowReadOnlyFailoverToPrimary` platforma Azure wyzwoliła automatyczne tryb failover. W takim przypadku, jeśli właściwość jest ustawiona na wartość True, nowa podstawowa będzie obsługiwać sesje tylko do odczytu i zapisu oraz tylko do odczytu.

- **Planowanego trybu failover**

   Planowane przełączenie w tryb failover wykonuje pełną synchronizację między podstawową i pomocniczą bazą danych przed przełączeniem pomocniczym do roli podstawowej. Gwarantuje to brak utraty danych. Planowanego trybu failover używa się w następujących scenariuszach:

  - Przeprowadzanie procesów awarii (DR) w środowisku produkcyjnym, gdy utrata danych jest niedopuszczalna
  - Przenoszenie baz danych do innego regionu
  - Zwracanie baz danych do regionu podstawowego po zminimalizowaniu awarii (powrót po awarii)

- **Nieplanowany tryb failover**

   Nieplanowane lub wymuszone przełączenie w tryb failover natychmiast przełącza rolę pomocniczą do roli podstawowej bez synchronizacji z rolą podstawową. Ta operacja spowoduje utratę danych. Nieplanowany tryb failover jest używany jako metoda odzyskiwania podczas awarii, gdy podstawowy tryb failover jest niedostępny. Gdy oryginalna podstawowa wersja jest ponownie w trybie online, automatycznie ponownie nawiąze połączenie bez synchronizacji i stanie się nową pomocniczą.

- **Ręczne tryb failover**

  Tryb failover można zainicjować ręcznie w dowolnym momencie, niezależnie od konfiguracji automatycznego trybu failover. Jeśli nie skonfigurowano zasad automatycznego trybu failover, do odzyskania baz danych w grupie trybu failover do lokacji dodatkowej jest wymagany ręczny tryb failover. Możesz zainicjować wymuszony lub przyjazny tryb failover (z pełną synchronizacją danych). Tych ostatnich można użyć do przeniesienia podstawowego do regionu pomocniczego. Po zakończeniu trybu failover rekordy DNS są automatycznie aktualizowane w celu zapewnienia łączności z nowym serwerem podstawowym.

- **Okres prolongaty z utratą danych**

  Ponieważ podstawowa i pomocnicza baza danych są synchronizowane przy użyciu replikacji asynchronicznej, tryb failover może spowodować utratę danych. Możesz dostosować zasady automatycznego trybu failover, aby odzwierciedlić tolerancję aplikacji na utratę danych. Konfigurując program , można kontrolować czas oczekiwania systemu przed zainicjowaniem trybu failover, co może `GracePeriodWithDataLossHours` spowodować utratę danych.

- **Wiele grup trybu failover**

  Można skonfigurować wiele grup trybu failover dla tej samej pary serwerów, aby kontrolować zakres trybu failover. Każda grupa jest wywłaszczana w sposób niezależny. Jeśli aplikacja wielodostępna korzysta z elastycznych pul, można użyć tej funkcji, aby mieszać podstawowe i pomocnicze bazy danych w każdej puli. W ten sposób można zmniejszyć wpływ 3000 000 000 000 000 000 000 000 000 000 000 000 000

  > [!NOTE]
  > SQL Managed Instance nie obsługuje wielu grup trybu failover.
  
## <a name="permissions"></a>Uprawnienia

Uprawnienia dla grupy trybu failover są zarządzane za pośrednictwem kontroli dostępu na podstawie ról [(RBAC) platformy Azure.](../../role-based-access-control/overview.md) Rola [SQL Server ma wszystkie](../../role-based-access-control/built-in-roles.md#sql-server-contributor) uprawnienia niezbędne do zarządzania grupami trybu failover.

### <a name="create-failover-group"></a>Tworzenie grupy trybu failover

Aby utworzyć grupę trybu failover, musisz mieć dostęp do zapisu RBAC platformy Azure dla serwerów podstawowych i pomocniczych oraz do wszystkich baz danych w grupie trybu failover. W przypadku usługi SQL Managed Instance potrzebny jest dostęp do zapisu RBAC platformy Azure zarówno do podstawowej, jak i pomocniczej bazy danych SQL Managed Instance, ale uprawnienia do poszczególnych baz danych nie są istotne, ponieważ poszczególnych baz danych programu SQL Managed Instance nie można dodać do grupy trybu failover ani usunąć z grupy trybu failover.

### <a name="update-a-failover-group"></a>Aktualizowanie grupy trybu failover

Aby zaktualizować grupę trybu failover, musisz mieć dostęp do zapisu RBAC platformy Azure dla grupy trybu failover i wszystkich baz danych na bieżącym serwerze podstawowym lub w wystąpieniu zarządzanym.  

### <a name="fail-over-a-failover-group"></a>Failover a failover group (Przejmij grupę trybu failover)

Aby przechować grupę trybu failover do trybu failover, musisz mieć dostęp do zapisu RBAC platformy Azure do grupy trybu failover na nowym serwerze podstawowym lub w wystąpieniu zarządzanym.

## <a name="best-practices-for-sql-database"></a>Najlepsze rozwiązania dotyczące SQL Database

Grupa automatycznego trybu failover musi być skonfigurowana na serwerze podstawowym i połączy ją z serwerem pomocniczym w innym regionie świadczenia usługi Azure. Grupy mogą zawierać wszystkie lub niektóre bazy danych na tych serwerach. Na poniższym diagramie przedstawiono typową konfigurację geograficznie nadmiarowej aplikacji w chmurze korzystającej z wielu baz danych i grupy automatycznego trybu failover.

![Diagram przedstawia typową konfigurację geograficznie nadmiarowej aplikacji w chmurze korzystającej z wielu baz danych i grupy automatycznego trybu failover.](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> Zobacz Dodawanie SQL Database do grupy [trybu failover,](failover-group-add-single-database-tutorial.md) aby uzyskać szczegółowy samouczek krok po kroku dotyczący dodawania bazy danych SQL Database do grupy trybu failover.

Podczas projektowania usługi z myślą o ciągłości działalności biznesowej należy przestrzegać tych ogólnych wytycznych:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Zarządzanie trybem failover wielu baz danych przy użyciu co najmniej jednej grupy trybu failover

Można utworzyć jedną lub wiele grup trybu failover między dwoma serwerami w różnych regionach (serwer podstawowy i pomocniczy). Każda grupa może zawierać jedną lub kilka baz danych, które są odzyskiwane jako jednostka na wypadek, gdy wszystkie lub niektóre podstawowe bazy danych staną się niedostępne z powodu niedostępności w regionie podstawowym. Grupa trybu failover tworzy pomocniczą bazę danych z tym samym celem usługi co podstawowa. Jeśli dodasz istniejącą relację replikacji geograficznej do grupy trybu failover, upewnij się, że pomocniczy obszar geograficzny jest skonfigurowany z taką samą warstwą usługi i rozmiarem obliczeniowym jak podstawowy.
  
> [!IMPORTANT]
> Tworzenie grup trybu failover między dwoma serwerami w różnych subskrypcjach nie jest obecnie obsługiwane w Azure SQL Database. Przeniesienie serwera podstawowego lub pomocniczego do innej subskrypcji po utworzeniu grupy trybu failover może spowodować błędy żądań trybu failover i innych operacji.

### <a name="using-read-write-listener-for-oltp-workload"></a>Korzystanie z odbiornika odczytu i zapisu dla obciążenia OLTP

Podczas wykonywania operacji OLTP użyj jako adresu URL serwera, a `<fog-name>.database.windows.net` połączenia są automatycznie kierowane do serwera podstawowego. Ten adres URL nie zmienia się po zakończeniu pracy w trybu failover. Należy pamiętać, że tryb failover obejmuje zaktualizowanie rekordu DNS, tak aby połączenia klientów zostały przekierowane do nowej podstawowej pamięci podręcznej dopiero po odświeżeniu pamięci podręcznej DNS klienta.

### <a name="using-read-only-listener-for-read-only-workload"></a>Używanie odbiornika tylko do odczytu dla obciążenia tylko do odczytu

Jeśli masz logicznie izolowane obciążenie tylko do odczytu, które jest tolerowane na niektóre nieaktualności danych, możesz użyć pomocniczej bazy danych w aplikacji. W przypadku sesji tylko do odczytu użyj jako adresu URL serwera, a `<fog-name>.secondary.database.windows.net` połączenie jest automatycznie kierowane do pomocniczej. Zaleca się również wskazanie intencji read w ciągu połączenia przy użyciu `ApplicationIntent=ReadOnly` .

> [!NOTE]
> W warstwach usług Premium, Krytyczne dla działania firmy i Hiperskala usługa SQL Database obsługuje [](read-scale-out.md) odciążanie obciążeń zapytań tylko do odczytu przy użyciu parametru w parametrach połączenia przy użyciu replik tylko do `ApplicationIntent=ReadOnly` odczytu. Jeśli skonfigurowano pomocnicze wystąpienie replikowane geograficznie, można użyć tej funkcji do łączenia się z repliką tylko do odczytu w lokalizacji podstawowej lub w lokalizacji zreplikowanej geograficznie.
>
> - Aby nawiązać połączenie z repliką tylko do odczytu w lokalizacji podstawowej, użyj `ApplicationIntent=ReadOnly` i `<fog-name>.database.windows.net` .
> - Aby nawiązać połączenie z repliką tylko do odczytu w lokalizacji pomocniczej, użyj `ApplicationIntent=ReadOnly` i `<fog-name>.secondary.database.windows.net` .

### <a name="preparing-for-performance-degradation"></a>Przygotowywanie do spadku wydajności

Typowa aplikacja platformy Azure używa wielu usług platformy Azure i składa się z wielu składników. Automatyczne trybu failover grupy trybu failover jest wyzwalane na podstawie stanu samej Azure SQL trybu failover. Na inne usługi platformy Azure w regionie podstawowym może nie mieć wpływu tańsza usługa, a ich składniki mogą być nadal dostępne w tym regionie. Po przełączeniu podstawowych baz danych do regionu dr ygowego może zwiększyć się opóźnienie między składnikami zależnym. Aby uniknąć wpływu większych opóźnień na wydajność aplikacji, upewnij się, że nadmiarowość wszystkich składników aplikacji w regionie dr i postępuj zgodnie z tymi wytycznymi [dotyczącymi zabezpieczeń sieci.](#failover-groups-and-network-security)

### <a name="preparing-for-data-loss"></a>Przygotowanie do utraty danych

W przypadku wykrycia nieprawidłowości platforma Azure czeka przez okres określony przez `GracePeriodWithDataLossHours` . Wartość domyślna to 1 godzina. Jeśli nie możesz sobie pozwolić na utratę danych, upewnij się, że ustawiono wystarczająco dużą `GracePeriodWithDataLossHours` liczbę, taką jak 24 godziny. Użyj ręcznego trybu failover grupy, aby wrócić po awarii z pomocniczej do podstawowej.

> [!IMPORTANT]
> Elastyczne pule z 800 lub mniejszą liczbą jednostek DTU i więcej niż 250 baz danych korzystających z replikacji geograficznej mogą napotkać problemy, takie jak dłuższe planowane tryby failover i obniżona wydajność.  Te problemy są bardziej prawdopodobne w przypadku obciążeń intensywnie korzystających z zapisu, gdy punkty końcowe replikacji geograficznej są szeroko oddzielone lokalizacją geograficzną lub gdy dla każdej bazy danych jest używanych wiele dodatkowych punktów końcowych.  Objawy tych problemów są wskazywane, gdy opóźnienie replikacji geograficznej zwiększa się wraz z upływem czasu.  To opóźnienie można monitorować za pomocą [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Jeśli te problemy wystąpią, środki zaradcze obejmują zwiększenie liczby jednostek DTU puli lub zmniejszenie liczby baz danych replikowanych geograficznie w tej samej puli.

### <a name="changing-secondary-region-of-the-failover-group"></a>Zmienianie regionu pomocniczego grupy trybu failover

Aby zilustrować sekwencję zmian, zakładamy, że serwer A jest serwerem podstawowym, serwer B jest istniejącym serwerem pomocniczym, a serwer C jest nowym serwerem pomocniczym w trzecim regionie.  Aby wykonać przejście, wykonaj następujące kroki:

1. Utwórz dodatkowe bazy danych dla każdej bazy danych na serwerze A do serwera C przy użyciu [aktywnej replikacji geograficznej.](active-geo-replication-overview.md) Każda baza danych na serwerze A będzie mieć dwa serwer proxy, jeden na serwerze B i jeden na serwerze C. Gwarantuje to, że podstawowe bazy danych pozostaną chronione podczas przejścia.
2. Usuń grupę trybu failover. W tym momencie logowania będą się nie pomylić. Jest to spowodowane tym, że aliasy SQL dla odbiorników grupy trybu failover zostały usunięte, a brama nie rozpozna nazwy grupy trybu failover.
3. Utwórz ponownie grupę trybu failover o tej samej nazwie między serwerami A i C. W tym momencie logowania przestaną kończyć się niepowodzeniem.
4. Dodaj wszystkie podstawowe bazy danych na serwerze A do nowej grupy trybu failover.
5. Upuść serwer B. Wszystkie bazy danych w bazie danych B zostaną usunięte automatycznie.

### <a name="changing-primary-region-of-the-failover-group"></a>Zmienianie regionu podstawowego grupy trybu failover

Aby zilustrować sekwencję zmian, zakładamy, że serwer A jest serwerem podstawowym, serwer B jest istniejącym serwerem pomocniczym, a serwer C jest nowym serwerem podstawowym w trzecim regionie.  Aby wykonać przejście, wykonaj następujące kroki:

1. Wykonaj planowane przejście w tryb failover, aby przełączyć serwer podstawowy na serwer B. Serwer A stanie się nowym serwerem pomocniczym. W przypadku trybu failover może wystąpić kilkuminutowy przestój. Rzeczywisty czas będzie zależeć od rozmiaru grupy trybu failover.
2. Utwórz dodatkowe bazy danych sekundowe dla każdej bazy danych na serwerze B na serwerze C przy użyciu [aktywnej replikacji geograficznej.](active-geo-replication-overview.md) Każda baza danych na serwerze B będzie mieć dwa bazy danych, jeden na serwerze A i jeden na serwerze C. Gwarantuje to, że podstawowe bazy danych pozostaną chronione podczas przejścia.
3. Usuń grupę trybu failover. W tym momencie logowania będą się nie pomylić. Jest to spowodowane tym, że aliasy SQL dla odbiorników grupy trybu failover zostały usunięte, a brama nie rozpozna nazwy grupy trybu failover.
4. Utwórz ponownie grupę trybu failover o takiej samej nazwie między serwerami B i C. W tym momencie logowania przestaną kończyć się niepowodzeniem.
5. Dodaj wszystkie podstawowe bazy danych w bazie danych B do nowej grupy trybu failover.
6. Wykonaj planowane przejście grupy trybu failover w tryb failover, aby przełączyć B i C. Teraz serwer C stanie się serwerem podstawowym, a B — pomocniczym. Wszystkie pomocnicze bazy danych na serwerze A zostaną automatycznie połączone z pomocniczymi bazami danych w języku C. Podobnie jak w kroku 1, w wyniku pracy w failover może wystąpić kilkuminutowy przestój.
7. Upuść serwer A. Wszystkie bazy danych na platformie A zostaną usunięte automatycznie.

> [!IMPORTANT]
> Po usunięciu grupy trybu failover zostaną również usunięte rekordy DNS dla punktów końcowych odbiornika. W tym momencie istnieje niezerowe prawdopodobieństwo, że ktoś inny tworzy grupę trybu failover lub alias serwera o tej samej nazwie, co uniemożliwi jej użycie ponownie. Aby zminimalizować ryzyko, nie używaj ogólnych nazw grup trybu failover.

## <a name="best-practices-for-sql-managed-instance"></a>Najlepsze rozwiązania dotyczące SQL Managed Instance

Grupa automatycznego trybu failover musi być skonfigurowana w wystąpieniu podstawowym i będzie nawiązywać połączenie z wystąpieniem pomocniczym w innym regionie świadczenia usługi Azure.  Wszystkie bazy danych w wystąpieniu zostaną zreplikowane do wystąpienia pomocniczego.

Na poniższym diagramie przedstawiono typową konfigurację geograficznie nadmiarowej aplikacji w chmurze przy użyciu wystąpienia zarządzanego i grupy automatycznego trybu failover.

![Diagram automatycznego trybu failover](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> Zobacz Dodawanie wystąpienia zarządzanego do grupy [trybu failover,](../managed-instance/failover-group-add-instance-tutorial.md) aby uzyskać szczegółowy samouczek krok po kroku dotyczący dodawania SQL Managed Instance do używania grupy trybu failover.

Jeśli aplikacja używa warstwy SQL Managed Instance jako warstwy danych, podczas projektowania pod SQL Managed Instance ciągłości działania postępuj zgodnie z tymi ogólnymi wytycznymi:

### <a name="creating-the-secondary-instance"></a>Tworzenie wystąpienia pomocniczego

Aby zapewnić nieprzerwaną łączność z serwerem podstawowym SQL Managed Instance po zakończeniu trybu failover zarówno wystąpienie podstawowe, jak i pomocnicze musi znajdować się w tej samej strefie DNS. Gwarantuje to, że ten sam certyfikat wielo domenowy (SAN) może służyć do uwierzytelniania połączeń klientów z jednym z dwóch wystąpień w grupie trybu failover. Gdy aplikacja jest gotowa do wdrożenia produkcyjnego, utwórz pomocniczy serwer SQL Managed Instance w innym regionie i upewnij się, że strefa DNS jest udostępniana serwerowi SQL Managed Instance. Można to zrobić, określając opcjonalny parametr podczas tworzenia. Jeśli używasz programu PowerShell lub interfejsu API REST, nazwa opcjonalnego parametru to , a nazwa odpowiedniego pola opcjonalnego w polu Azure Portal to Podstawowe wystąpienie `DNS Zone Partner` zarządzane.

> [!IMPORTANT]
> Pierwsze wystąpienie zarządzane utworzone w podsieci określa strefę DNS dla wszystkich kolejnych wystąpień w tej samej podsieci. Oznacza to, że dwa wystąpienia z tej samej podsieci nie mogą należeć do różnych stref DNS.

Aby uzyskać więcej informacji na temat tworzenia pomocniczego wystąpienia SQL Managed Instance w tej samej strefie DNS co wystąpienie podstawowe, zobacz [Tworzenie pomocniczego wystąpienia zarządzanego.](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance)

### <a name="using-geo-paired-regions"></a>Korzystanie z regionów sparowanych geograficznie

Wdobyj oba wystąpienia zarządzane w [sparowanych regionach](../../best-practices-availability-paired-regions.md) ze względu na wydajność. Wystąpienia zarządzane, które są w regionach sparowanych geograficznie, mają znacznie lepszą wydajność niż niezapowolone regiony. 

### <a name="enabling-replication-traffic-between-two-instances"></a>Włączanie ruchu replikacji między dwoma wystąpieniami

Ponieważ każde wystąpienie jest izolowane we własnej sieci wirtualnej, ruch dwukierunkowy między tymi sieciami wirtualnym musi być dozwolony. Zobacz [Brama sieci VPN platformy Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Tworzenie grupy trybu failover między wystąpieniami zarządzanymi w różnych subskrypcjach

Grupę trybu failover można utworzyć między wystąpieniami zarządzanymi SQL w dwóch różnych subskrypcjach, o ile subskrypcje są skojarzone z tą samą [Azure Active Directory dzierżawy.](../../active-directory/fundamentals/active-directory-whatis.md#terminology) W przypadku korzystania z interfejsu API programu PowerShell można to zrobić, określając `PartnerSubscriptionId` parametr pomocniczy SQL Managed Instance. W przypadku korzystania z interfejsu API REST każdy identyfikator wystąpienia zawarty w `properties.managedInstancePairs` parametrze może mieć własny identyfikator subskrypcji.
  
> [!IMPORTANT]
> Azure Portal nie obsługuje tworzenia grup trybu failover w różnych subskrypcjach. Ponadto w przypadku istniejących grup trybu failover w różnych subskrypcjach i/lub grupach zasobów nie można zainicjować trybu failover ręcznie za pośrednictwem portalu z podstawowego SQL Managed Instance. Zamiast tego należy zainicjować go z wystąpienia pomocniczego obszaru geograficznego.

### <a name="managing-failover-to-secondary-instance"></a>Zarządzanie trybem failover do wystąpienia pomocniczego

Grupa trybu failover będzie zarządzać trybem failover wszystkich baz danych w usłudze SQL Managed Instance. Gdy grupa zostanie utworzona, każda baza danych w wystąpieniu zostanie automatycznie zreplikowana geograficznie do pomocniczego wystąpienia usługi SQL Managed Instance. Nie można użyć grup trybu failover w celu zainicjowania częściowego trybu failover dla podzestawu baz danych.

> [!IMPORTANT]
> Jeśli baza danych zostanie usunięta z serwera SQL Managed Instance, również zostanie ona automatycznie usunięta na pomocniczym SQL Managed Instance.

### <a name="using-read-write-listener-for-oltp-workload"></a>Używanie odbiornika odczytu i zapisu dla obciążenia OLTP

Podczas wykonywania operacji OLTP użyj jako adresu URL serwera, a `<fog-name>.zone_id.database.windows.net` połączenia są automatycznie kierowane do serwera podstawowego. Ten adres URL nie zmienia się po zakończeniu pracy w trybu failover. Tryb failover obejmuje zaktualizowanie rekordu DNS, tak aby połączenia klientów zostały przekierowane do nowej podstawowej pamięci podręcznej dopiero po odświeżeniu pamięci podręcznej DNS klienta. Ponieważ wystąpienie pomocnicze udostępnia strefę DNS podstawowej, aplikacja kliency będzie mogła ponownie połączyć się z nim przy użyciu tego samego certyfikatu sieci SAN.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Nawiązywanie połączenia z wystąpieniem pomocniczym przy użyciu odbiornika tylko do odczytu

Jeśli masz logicznie izolowane obciążenie tylko do odczytu, które jest tolerowane na niektóre nieaktualności danych, możesz użyć pomocniczej bazy danych w aplikacji. Aby połączyć się bezpośrednio z pomocniczą repliką geograficzną, użyj jako adresu URL serwera, a połączenie jest nawiązywane bezpośrednio z pomocniczym serwerem `<fog-name>.secondary.<zone_id>.database.windows.net` replikowanym geograficznie.

> [!NOTE]
> W Krytyczne dla działania firmy warstwa SQL Managed Instance obsługuje używanie replik tylko [](read-scale-out.md) do odczytu do odciążania obciążeń zapytań tylko do odczytu przy użyciu parametru w `ApplicationIntent=ReadOnly` parametrach połączenia. Jeśli skonfigurowano pomocnicze wystąpienie replikowane geograficznie, można użyć tej funkcji do łączenia się z repliką tylko do odczytu w lokalizacji podstawowej lub w lokalizacji zreplikowanej geograficznie.
>
> - Aby nawiązać połączenie z repliką tylko do odczytu w lokalizacji podstawowej, użyj `ApplicationIntent=ReadOnly` i `<fog-name>.<zone_id>.database.windows.net` .
> - Aby nawiązać połączenie z repliką tylko do odczytu w lokalizacji pomocniczej, użyj `ApplicationIntent=ReadOnly` i `<fog-name>.secondary.<zone_id>.database.windows.net` .

### <a name="preparing-for-performance-degradation"></a>Przygotowywanie do spadku wydajności

Typowa aplikacja platformy Azure używa wielu usług platformy Azure i składa się z wielu składników. Automatyczne trybu failover grupy trybu failover jest wyzwalane na podstawie stanu samej Azure SQL trybu failover. Na inne usługi platformy Azure w regionie podstawowym może nie mieć wpływu tańsza usługa, a ich składniki mogą być nadal dostępne w tym regionie. Gdy podstawowe bazy danych przełączą się do regionu pomocniczego, opóźnienie między składnikami zależnym może wzrosnąć. Aby uniknąć wpływu większych opóźnień na wydajność aplikacji, upewnij się, że nadmiarowość wszystkich składników aplikacji w regionie pomocniczym oraz składników aplikacji w pracy awaryjnej wraz z bazą danych. W czasie konfiguracji postępuj zgodnie z [wytycznymi dotyczącymi zabezpieczeń sieci,](#failover-groups-and-network-security) aby zapewnić łączność z bazą danych w regionie pomocniczym.

### <a name="preparing-for-data-loss"></a>Przygotowanie do utraty danych

W przypadku wykrycia awarii w przypadku utraty danych zostanie wyzwolony odczyt/zapis w przypadku utraty danych, co jest najlepsze z naszej wiedzy. W przeciwnym razie trybu failover zostanie odroczony o okres określony przy użyciu `GracePeriodWithDataLossHours` . Jeśli określono wartość `GracePeriodWithDataLossHours` , przygotuj się na utratę danych. Ogólnie rzecz biorąc, podczas 30. yjść na platformę Azure preferowana jest dostępność. Jeśli nie możesz sobie pozwolić na utratę danych, ustaw wartość GracePeriodWithDataLossHours na wystarczająco dużą liczbę, taką jak 24 godziny, lub wyłącz automatyczne tryb failover.

Aktualizacja DNS odbiornika odczytu i zapisu nastąpi natychmiast po zainicjowaniu trybu failover. Ta operacja nie spowoduje utraty danych. Jednak w normalnych warunkach proces przełączania ról bazy danych może potrwać do 5 minut. Do czasu jego ukończenia niektóre bazy danych w nowym wystąpieniu podstawowym będą nadal tylko do odczytu. Jeśli tryb failover jest inicjowany przy użyciu programu PowerShell, operacja przełączenia roli repliki podstawowej jest synchroniczna. Jeśli zostanie on zainicjowany przy użyciu Azure Portal, interfejs użytkownika wskaże stan ukończenia. Jeśli jest on inicjowany przy użyciu interfejsu API REST, użyj Azure Resource Manager sondowania usługi Azure Resource Manager do monitorowania ukończenia.

> [!IMPORTANT]
> Użyj ręcznego trybu failover grupy, aby przenieść pierwszeństwo z powrotem do oryginalnej lokalizacji. Po zminimalizowaniu awarii, która spowodowała przejście do trybu failover, możesz przenieść podstawowe bazy danych do oryginalnej lokalizacji. W tym celu należy zainicjować ręcznego trybu failover grupy.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Zmienianie regionu pomocniczego grupy trybu failover

Załóżmy, że wystąpienie A jest wystąpieniem podstawowym, wystąpienie B jest istniejącym wystąpieniem pomocniczym, a wystąpienie C jest nowym wystąpieniem pomocniczym w trzecim regionie.  Aby wykonać przejście, wykonaj następujące kroki:

1. Utwórz wystąpienie C o takim samym rozmiarze jak A i w tej samej strefie DNS.
2. Usuń grupę trybu failover między wystąpieniami A i B. W tym momencie logowania będą się nie powiodły, ponieważ aliasy SQL dla odbiorników grupy trybu failover zostały usunięte, a brama nie rozpozna nazwy grupy trybu failover. Pomocnicze bazy danych zostaną odłączone od szkół podstawowych i staną się bazami danych do odczytu i zapisu.
3. Utwórz grupę trybu failover o takiej samej nazwie między wystąpieniem A i C. Postępuj zgodnie z instrukcjami w grupie trybu [failover, SQL Managed Instance samouczek](../managed-instance/failover-group-add-instance-tutorial.md). Jest to operacja rozmiaru danych, która zostanie ukończona, gdy wszystkie bazy danych z wystąpienia A zostaną iniekcjowane i zsynchronizowane.
4. Usuń wystąpienie B, jeśli nie jest to konieczne, aby uniknąć niepotrzebnych opłat.

> [!NOTE]
> Po kroku 2 i do momentu ukończenia kroku 3 bazy danych w wystąpieniu A pozostaną niechronione przed katastrofalną awarią wystąpienia A.

### <a name="changing-primary-region-of-the-failover-group"></a>Zmienianie regionu podstawowego grupy trybu failover

Załóżmy, że wystąpienie A jest wystąpieniem podstawowym, wystąpienie B jest istniejącym wystąpieniem pomocniczym, a wystąpienie C jest nowym wystąpieniem podstawowym w trzecim regionie.  Aby wykonać przejście, wykonaj następujące kroki:

1. Utwórz wystąpienie C o takim samym rozmiarze jak B i w tej samej strefie DNS.
2. Połącz się z wystąpieniem B i ręcznie przejdź do trybu failover, aby przełączyć wystąpienie podstawowe na wystąpienie B. Wystąpienie A automatycznie stanie się nowym wystąpieniem pomocniczym.
3. Usuń grupę trybu failover między wystąpieniami A i B. W tym momencie logowania będą się nie powiodły, ponieważ aliasy SQL dla odbiorników grupy trybu failover zostały usunięte, a brama nie rozpozna nazwy grupy trybu failover. Pomocnicze bazy danych zostaną odłączone od średnich baz danych i staną się bazami danych do odczytu i zapisu.
4. Utwórz grupę trybu failover o takiej samej nazwie między wystąpieniem A i C. Postępuj zgodnie z instrukcjami w samouczku dotyczącym grupy [trybu failover z wystąpieniem zarządzanym](../managed-instance/failover-group-add-instance-tutorial.md). Jest to operacja rozmiaru danych, która zostanie ukończona, gdy wszystkie bazy danych z wystąpienia A zostaną iniekcjowane i zsynchronizowane.
5. Usuń wystąpienie A, jeśli nie jest to konieczne, aby uniknąć niepotrzebnych opłat.

> [!CAUTION]
> Po kroku 3 i do momentu ukończenia kroku 4 bazy danych w wystąpieniu A pozostaną niechronione przed katastrofalną awarią wystąpienia A.

> [!IMPORTANT]
> Po usunięciu grupy trybu failover zostaną również usunięte rekordy DNS dla punktów końcowych odbiornika. W tym momencie istnieje niezerowe prawdopodobieństwo utworzenia grupy trybu failover lub aliasu serwera o tej samej nazwie, co uniemożliwi jej użycie ponownie. Aby zminimalizować ryzyko, nie używaj ogólnych nazw grup trybu failover.

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>Włączanie scenariuszy zależnych od obiektów z systemowych baz danych
Systemowe bazy danych nie są replikowane do wystąpienia pomocniczego w grupie trybu failover. Aby włączyć scenariusze, które zależą od obiektów z systemowych baz danych, w wystąpieniu pomocniczym należy utworzyć te same obiekty w pomocniczym. Jeśli na przykład planujesz używać tych samych identyfikatorów logowania w wystąpieniu pomocniczym, pamiętaj, aby utworzyć je przy użyciu identycznego identyfikatora SID. 
```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 


## <a name="failover-groups-and-network-security"></a>Grupy trybu failover i zabezpieczenia sieci

W przypadku niektórych aplikacji reguły zabezpieczeń wymagają, aby dostęp sieciowy do warstwy danych był ograniczony do określonego składnika lub składników, takich jak maszyna wirtualna, usługa internetowa itp. To wymaganie przedstawia pewne wyzwania dotyczące projektowania ciągłości działalności biznesowej i korzystania z grup trybu failover. Podczas implementowania takiego ograniczonego dostępu należy wziąć pod uwagę następujące opcje.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Używanie grup trybu failover i reguł sieci wirtualnej

Jeśli używasz reguł i punktów końcowych usługi [Virtual Network](vnet-service-endpoint-rule-overview.md) do ograniczania dostępu do bazy danych w programie SQL Database lub SQL Managed Instance, należy pamiętać, że każdy punkt końcowy usługi dla sieci wirtualnej ma zastosowanie tylko do jednego regionu świadczenia usługi Azure. Punkt końcowy nie umożliwia innym regionom akceptowania komunikacji z podsieci. W związku z tym tylko aplikacje klienckie wdrożone w tym samym regionie mogą łączyć się z podstawową bazą danych. Ponieważ tryb failover powoduje, że SQL Database klientów są przekierowywani do serwera w innym regionie (pomocniczym), te sesje nie powiodą się, jeśli pochodzą z klienta spoza tego regionu. Z tego powodu nie można włączyć zasad automatycznego trybu failover, jeśli uwzględnione serwery lub wystąpienia są uwzględnione w Virtual Network failover. Aby obsługiwać ręczne tryb failover, wykonaj następujące kroki:

1. Aprowizować nadmiarowe kopie składników frontonia aplikacji (usługi internetowej, maszyn wirtualnych itp.) w regionie pomocniczym
2. Konfigurowanie reguł [sieci wirtualnej osobno](vnet-service-endpoint-rule-overview.md) dla serwera podstawowego i pomocniczego
3. Włączanie trybu [failover frontonu przy użyciu konfiguracji usługi Traffic Manager](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Inicjowanie ręcznego trybu failover po wykryciu awarii. Ta opcja jest zoptymalizowana pod kątem aplikacji, które wymagają spójnego opóźnienia między frontoną a warstwą danych i obsługują odzyskiwanie, gdy na frontony, warstwę danych lub obie te warstwy mają wpływ awarii.

> [!NOTE]
> Jeśli używasz odbiornika tylko do odczytu do równoważenia obciążenia obciążenia tylko do odczytu, upewnij się, że to obciążenie jest wykonywane na maszynie wirtualnej lub innym zasobie w regionie pomocniczym, aby można było nawiązać połączenie z pomocniczą bazą danych. 

### <a name="use-failover-groups-and-firewall-rules"></a>Używanie grup trybu failover i reguł zapory

Jeśli plan ciągłości działania wymaga pracy w trybie failover przy użyciu grup z automatycznym trybem failover, możesz ograniczyć dostęp do bazy danych SQL Database przy użyciu tradycyjnych reguł zapory. Aby obsługiwać automatyczne tryb failover, wykonaj następujące kroki:

1. [Tworzenie publicznego adresu IP](../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Utwórz publiczny usługę równoważenia obciążenia](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) i przypisz do niego publiczny adres IP.
3. [Tworzenie sieci wirtualnej i maszyn wirtualnych](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) dla składników frontonia
4. [Utwórz sieciową grupę zabezpieczeń](../../virtual-network/network-security-groups-overview.md) i skonfiguruj połączenia przychodzące.
5. Upewnij się, że połączenia wychodzące są otwarte dla Azure SQL Database przy użyciu tagu usługi ["Sql".](../../virtual-network/network-security-groups-overview.md#service-tags)
6. Utwórz [regułę SQL Database,](firewall-configure.md) aby zezwolić na ruch przychodzący z publicznego adresu IP, który został utworzyć w kroku 1.

Aby uzyskać więcej informacji na temat sposobu konfigurowania dostępu wychodzącego i adresu IP do użycia w regułach zapory, zobacz [Połączenia wychodzące usługi Load Balancer.](../../load-balancer/load-balancer-outbound-connections.md)

Powyższe ustawienia konfiguracji zapewnią, że automatyczne tryb failover nie zablokuje połączeń ze składników frontou i zakłada, że aplikacja może tolerować dłuższe opóźnienie między frontoną a warstwą danych.

> [!IMPORTANT]
> Aby zagwarantować ciągłość działania w przypadku procesów regionalnych, należy zapewnić nadmiarowość geograficzną zarówno dla składników frontou, jak i baz danych.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Włączanie replikacji geograficznej między wystąpieniami zarządzanymi i ich sieciami wirtualnymi

Po skonfigurowaniu grupy trybu failover między podstawowym i pomocniczym wystąpieniem zarządzanym SQL w dwóch różnych regionach każde wystąpienie jest izolowane przy użyciu niezależnej sieci wirtualnej. Aby zezwolić na ruch replikacji między tymi sieciami wirtualnym, upewnij się, że spełnione są następujące wymagania wstępne:

- Dwa wystąpienia usługi SQL Managed Instance muszą być w różnych regionach świadczenia usługi Azure.
- Dwa wystąpienia usługi SQL Managed Instance muszą być tej samej warstwy usługi i mieć taki sam rozmiar magazynu.
- Wystąpienie pomocnicze usługi SQL Managed Instance musi być puste (bez baz danych użytkownika).
- Sieci wirtualne używane przez wystąpienia usługi SQL Managed Instance muszą być połączone za pośrednictwem usługi [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) express [Route.](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) Gdy dwie sieci wirtualne nawiązują połączenie za pośrednictwem sieci lokalnej, upewnij się, że nie ma reguły zapory blokującej porty 5022 i 11000-11999. Globalna komunikacja równorzędna sieci wirtualnych jest obsługiwana z ograniczeniami opisanymi w poniższej uwadze.

   > [!IMPORTANT]
   > [W dniu 22.09.2020 ogłoszono globalną komunikację równorzędna sieci wirtualnych dla nowo utworzonych klastrów wirtualnych](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Oznacza to, że globalna komunikacja równorzędna sieci wirtualnych jest obsługiwana dla wystąpień zarządzanych SQL utworzonych w pustych podsieciach, a także dla wszystkich kolejnych wystąpień zarządzanych utworzonych w tych podsieciach. W przypadku wszystkich innych usług SQL Managed Instances obsługa komunikacji równorzędnej jest ograniczona do sieci w tym samym regionie ze względu na ograniczenia globalnej komunikacji [równorzędnej sieci wirtualnych.](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) Zobacz również odpowiednią sekcję artykułu [Azure Virtual Networks frequently asked questions (Często zadawane](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) pytania dotyczące sieci wirtualnych platformy Azure), aby uzyskać więcej szczegółów. 

- Dwie SQL Managed Instance wirtualne nie mogą mieć nakładających się adresów IP.
- Należy skonfigurować sieciowe grupy zabezpieczeń tak, aby porty 5022 i zakres 11000 ~12000 były dla połączeń przychodzących i wychodzących z podsieci drugiego wystąpienia zarządzanego. Ma to na celu umożliwienie ruchu związanego z replikacją pomiędzy wystąpieniami.

   > [!IMPORTANT]
   > Błędnie skonfigurowane reguły zabezpieczeń sieciowej sieciowej organizacji prowadzą do zablokowanych operacji kopiowania bazy danych.

- Pomocniczy SQL Managed Instance jest skonfigurowany z prawidłowym identyfikatorem strefy DNS. Strefa DNS jest właściwością klastra SQL Managed Instance i podstawowego klastra wirtualnego, a jej identyfikator jest uwzględniony w adresie nazwy hosta. Identyfikator strefy jest generowany jako ciąg losowy podczas tworzenia pierwszego SQL Managed Instance w każdej sieci wirtualnej, a ten sam identyfikator jest przypisywany do wszystkich innych wystąpień w tej samej podsieci. Po przypisaniu nie można zmodyfikować strefy DNS. Wystąpienia zarządzane SQL dołączone do tej samej grupy trybu failover muszą współużyt serwer DNS. Można to zrobić, przekazując identyfikator strefy wystąpienia podstawowego jako wartość parametru DnsZonePartner podczas tworzenia wystąpienia pomocniczego.

   > [!NOTE]
   > Aby uzyskać szczegółowy samouczek dotyczący konfigurowania grup trybu failover przy użyciu SQL Managed Instance, zobacz dodawanie SQL Managed Instance do grupy [trybu failover.](../managed-instance/failover-group-add-instance-tutorial.md)

## <a name="upgrading-or-downgrading-a-primary-database"></a>Uaktualnianie lub zmiana podstawowej bazy danych na niższą

Podstawową bazę danych można podwyższyć lub obniżyć do innego rozmiaru obliczeniowego (w tej samej warstwie usługi, a nie między Ogólnego przeznaczenia i Krytyczne dla działania firmy) bez rozłączania jakichkolwiek pomocniczych baz danych. Podczas uaktualniania zalecamy najpierw uaktualnienie wszystkich pomocniczych baz danych, a następnie uaktualnienie podstawowej bazy danych. Podczas obniżania poziomu należy odwrócić kolejność: najpierw obniżyć poziom podstawowej bazy danych, a następnie obniżyć poziom wszystkich pomocniczych baz danych. W przypadku uaktualnienia lub obniżenia poziomu bazy danych do innej warstwy usługi to zalecenie jest wymuszane.

Ta sekwencja jest zalecana specjalnie po to, aby uniknąć problemu polegającego na tym, że wystąpienie pomocnicze z niższym poziomem jednostki SKU zostaje przeciążone i musi zostać ponownie rozmieszczone podczas procesu podwyższania lub obniżania poziomu. Można również uniknąć problemu, tworząc wystąpienie podstawowe tylko do odczytu, kosztem wpływu na wszystkie obciążenia odczytu i zapisu względem wystąpienia podstawowego.

> [!NOTE]
> Jeśli pomocnicza baza danych została utworzona w ramach konfiguracji grupy trybu failover, nie zaleca się obniżania poziomu pomocniczej bazy danych. Ma to na celu zapewnienie, że warstwa danych ma wystarczającą pojemność do przetwarzania zwykłego obciążenia po aktywowaniu trybu failover.

## <a name="preventing-the-loss-of-critical-data"></a>Zapobieganie utracie danych krytycznych

Ze względu na duże opóźnienie sieci szerokich obszarów kopiowanie ciągłe korzysta z mechanizmu replikacji asynchronicznej. Replikacja asynchroniczna sprawia, że utrata niektórych danych jest nieunikniona w przypadku wystąpienia awarii. Jednak niektóre aplikacje mogą nie wymagać utraty danych. Aby chronić te aktualizacje krytyczne, deweloper aplikacji może wywołać sp_wait_for_database_copy_sync [procedury](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) systemowej natychmiast po zatwierdzeniu transakcji. Wywołanie `sp_wait_for_database_copy_sync` blokuje wywołujący wątek, dopóki ostatnia zatwierdzona transakcja nie zostanie przesłana do pomocniczej bazy danych. Jednak nie czeka na przesyłane transakcje, które mają zostać powtórzone i zatwierdzone w pomocniczym. `sp_wait_for_database_copy_sync` jest ograniczony do określonego linku kopiowania ciągłego. Tę procedurę może wywołać każdy użytkownik z prawami połączenia z podstawową bazą danych.

> [!NOTE]
> `sp_wait_for_database_copy_sync` zapobiega utracie danych po trybie failover, ale nie gwarantuje pełnej synchronizacji dostępu do odczytu. Opóźnienie spowodowane przez wywołanie procedury może być znaczące i zależy od rozmiaru dziennika `sp_wait_for_database_copy_sync` transakcji w czasie wywołania.

## <a name="failover-groups-and-point-in-time-restore"></a>Grupy trybu failover i przywracanie do punktu w czasie

Aby uzyskać informacje na temat używania przywracania do punktu w czasie z grupami trybu failover, zobacz Odzyskiwanie do punktu w czasie [(PITR).](recovery-using-backups.md#point-in-time-restore)

## <a name="limitations-of-failover-groups"></a>Ograniczenia grup trybu failover

Należy pamiętać o następujących ograniczeniach:

- Nie można tworzyć grup trybu failover między dwoma serwerami lub wystąpieniami w tym samym regionie świadczenia usługi Azure.
- Nie można zmienić nazw grup trybu failover. Musisz usunąć grupę i utworzyć ją ponownie pod inną nazwą.
- Zmiana nazwy bazy danych nie jest obsługiwana w przypadku wystąpień w grupie trybu failover. Aby można było zmienić nazwę bazy danych, należy tymczasowo usunąć grupę trybu failover.
- Systemowe bazy danych nie są replikowane do wystąpienia pomocniczego w grupie trybu failover. W związku z tym scenariusze zależne od obiektów z systemowych baz danych będą niemożliwe w wystąpieniu pomocniczym, chyba że obiekty zostaną utworzone ręcznie w pomocniczym wystąpieniu.

## <a name="programmatically-managing-failover-groups"></a>Programowe zarządzanie grupami trybu failover

Jak wspomniano wcześniej, grupy automatycznego trybu failover i aktywna replikacja geograficzna mogą być również zarządzane programowo przy użyciu Azure PowerShell i interfejsu API REST. W poniższych tabelach opisano zestaw dostępnych poleceń. Aktywna replikacja geograficzna obejmuje zestaw interfejsów API Azure Resource Manager do zarządzania, w tym interfejs [API REST](/rest/api/sql/) usługi Azure SQL Database i Azure PowerShell [cmdlet](/powershell/azure/). Te interfejsy API wymagają użycia grup zasobów i obsługują kontrolę dostępu na podstawie ról (RBAC) platformy Azure. Aby uzyskać więcej informacji na temat wdrażania ról dostępu, zobacz [Azure role-based access control (Azure RBAC) (Kontrola](../../role-based-access-control/overview.md)dostępu na podstawie ról (RBAC) platformy Azure).

### <a name="manage-sql-database-failover"></a>Zarządzanie SQL Database trybu failover

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

| Polecenie cmdlet | Opis |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |To polecenie tworzy grupę trybu failover i rejestruje ją zarówno na serwerze podstawowym, jak i pomocniczym|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Usuwa grupę trybu failover z serwera |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Pobiera konfigurację grupy trybu failover |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modyfikuje konfigurację grupy trybu failover |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Wyzwalanie trybu failover grupy trybu failover na serwerze pomocniczym |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Dodaje co najmniej jedną bazę danych do grupy trybu failover|

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

| Polecenie | Opis |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az_sql_failover_group_create) |To polecenie tworzy grupę trybu failover i rejestruje ją zarówno na serwerze podstawowym, jak i pomocniczym|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | Usuwa grupę trybu failover z serwera |
| [az sql failover-group show](/cli/azure/sql/failover-group#az_sql_failover_group_show) | Pobiera konfigurację grupy trybu failover |
| [az sql failover-group update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |Modyfikuje konfigurację grupy trybu failover i/lub dodaje co najmniej jedną bazę danych do grupy trybu failover|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | Wyzwalanie trybu failover grupy trybu failover na serwerze pomocniczym |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| Interfejs API | Opis |
| --- | --- |
| [Tworzenie lub aktualizowanie grupy trybu failover](/rest/api/sql/failovergroups/createorupdate) | Tworzy lub aktualizuje grupę trybu failover |
| [Usuwanie grupy trybu failover](/rest/api/sql/failovergroups/delete) | Usuwa grupę trybu failover z serwera |
| [Trybu failover (planowane)](/rest/api/sql/failovergroups/failover) | Wyzwala tryb failover z bieżącego serwera podstawowego na serwer pomocniczy z pełną synchronizacją danych.|
| [Wymuszanie trybu failover zezwala na utratę danych](/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Wyzwala trybu failover z bieżącego serwera podstawowego do serwera pomocniczego bez synchronizowania danych. Ta operacja może spowodować utratę danych. |
| [Uzyskiwanie grupy trybu failover](/rest/api/sql/failovergroups/get) | Pobiera konfigurację grupy trybu failover. |
| [Lista grup trybu failover według serwera](/rest/api/sql/failovergroups/listbyserver) | Wyświetla listę grup trybu failover na serwerze. |
| [Aktualizowanie grupy trybu failover](/rest/api/sql/failovergroups/update) | Aktualizuje konfigurację grupy trybu failover. |

---

### <a name="manage-sql-managed-instance-failover"></a>Zarządzanie SQL Managed Instance trybu failover


# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

| Polecenie cmdlet | Opis |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |To polecenie tworzy grupę trybu failover i rejestruje ją zarówno w wystąpieniu podstawowym, jak i pomocniczym|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Modyfikuje konfigurację grupy trybu failover|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Pobiera konfigurację grupy trybu failover|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Wyzwalanie trybu failover grupy trybu failover do wystąpienia pomocniczego|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Usuwa grupę trybu failover|


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

| Polecenie | Opis |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az_sql_failover_group_create) |To polecenie tworzy grupę trybu failover i rejestruje ją zarówno na serwerze podstawowym, jak i pomocniczym|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | Usuwa grupę trybu failover z serwera |
| [az sql failover-group show](/cli/azure/sql/failover-group#az_sql_failover_group_show) | Pobiera konfigurację grupy trybu failover |
| [az sql failover-group update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |Modyfikuje konfigurację grupy trybu failover i/lub dodaje co najmniej jedną bazę danych do grupy trybu failover|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | Wyzwalanie trybu failover grupy trybu failover na serwerze pomocniczym |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| Interfejs API | Opis |
| --- | --- |
| [Tworzenie lub aktualizowanie grupy trybu failover](/rest/api/sql/instancefailovergroups/createorupdate) | Tworzy lub aktualizuje konfigurację grupy trybu failover |
| [Usuwanie grupy trybu failover](/rest/api/sql/instancefailovergroups/delete) | Usuwa grupę trybu failover z wystąpienia |
| [Trybu failover (planowane)](/rest/api/sql/instancefailovergroups/failover) | Wyzwala tryb failover z bieżącego wystąpienia podstawowego do tego wystąpienia z pełną synchronizacją danych. |
| [Wymuszanie trybu failover zezwala na utratę danych](/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Wyzwala trybu failover z bieżącego wystąpienia podstawowego do wystąpienia pomocniczego bez synchronizowania danych. Ta operacja może spowodować utratę danych. |
| [Uzyskiwanie grupy trybu failover](/rest/api/sql/instancefailovergroups/get) | pobiera konfigurację grupy trybu failover. |
| [Lista grup trybu failover — lista według lokalizacji](/rest/api/sql/instancefailovergroups/listbylocation) | Wyświetla listę grup trybu failover w lokalizacji. |

---

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe samouczki, zobacz
  - [Dodawanie SQL Database do grupy trybu failover](failover-group-add-single-database-tutorial.md)
  - [Dodawanie elastycznej puli do grupy trybu failover](failover-group-add-elastic-pool-tutorial.md)
  - [Dodawanie SQL Managed Instance do grupy trybu failover](../managed-instance/failover-group-add-instance-tutorial.md)
- Aby uzyskać przykładowe skrypty, zobacz:
  - [Konfigurowanie aktywnej replikacji geograficznej dla usługi przy użyciu programu PowerShell Azure SQL Database](scripts/setup-geodr-and-failover-database-powershell.md)
  - [Konfigurowanie aktywnej replikacji geograficznej dla bazy danych w puli w usłudze Azure SQL Database przy użyciu programu PowerShell](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [Dodawanie grupy Azure SQL Database do grupy trybu failover przy użyciu programu PowerShell](scripts/add-database-to-failover-group-powershell.md)
- Aby uzyskać omówienie ciągłości działania i scenariuszy, zobacz [Omówienie ciągłości działania](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Aby dowiedzieć się więcej Azure SQL Database automatycznych kopii zapasowych, zobacz [SQL Database automatyczne kopie zapasowe.](automated-backups-overview.md)
- Aby dowiedzieć się więcej o używaniu automatycznych kopii zapasowych do odzyskiwania, zobacz Przywracanie bazy danych z kopii zapasowych [inicjowanych przez usługę](recovery-using-backups.md).
- Aby dowiedzieć się więcej o wymaganiach dotyczących uwierzytelniania dla nowego serwera podstawowego i bazy danych, zobacz [SQL Database zabezpieczeń po odzyskiwaniu po awarii.](active-geo-replication-security-configure.md)
