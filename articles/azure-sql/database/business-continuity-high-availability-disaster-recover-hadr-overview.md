---
title: Ciągłość działalności biznesowej w chmurze — odzyskiwanie bazy danych
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Dowiedz się, w jaki sposób usługa Azure SQL Database i wystąpienie zarządzane SQL obsługują ciągłość biznesową i odzyskiwanie bazy danych, a następnie Pomóż w działaniu aplikacji w chmurze o znaczeniu strategicznym
keywords: business continuity,cloud business continuity,database disaster recovery,database recovery
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/25/2019
ms.openlocfilehash: 7bd991bd709bb4be69325afe967d7e5600a9e1a4
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222568"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Omówienie zagadnień dotyczących ciągłości działalności biznesowej zapewnianej przez usługę Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

**Ciągłość działalności biznesowej** w Azure SQL Database i wystąpieniu ZARZĄDZAnym SQL odnosi się do mechanizmów, zasad i procedur, które umożliwiają firmie kontynuowanie pracy w obliczu zakłóceń, szczególnie w przypadku infrastruktury obliczeniowej. W większości przypadków SQL Database i wystąpienie zarządzane SQL będą obsługiwały zdarzenia zakłócające, które mogą wystąpić w środowisku chmury i zachować działania aplikacji i procesów roboczych. Istnieją jednak pewne zdarzenia powodujące zakłócenia, które nie mogą być obsługiwane przez SQL Database automatycznie takie jak:

- Użytkownik przypadkowo usunął lub zaktualizował wiersz w tabeli.
- Złośliwa osoba atakująca pomyślnie usunął dane lub Porzuć bazę danych.
- Trzęsienie ziemi spowodowało awarię i tymczasowe wyłączenie centrum danych.

To omówienie zawiera opis możliwości, które SQL Database i wystąpienie zarządzane SQL zapewniają ciągłość działania i odzyskiwanie po awarii. Dowiedz się więcej na temat opcji, zaleceń i samouczków dotyczących odzyskiwania po zdarzeniach powodujących zakłócenia, które mogą spowodować utratę danych lub spowodować, że baza danych i aplikacja staną się niedostępne. Dowiedz się, co zrobić, gdy błąd użytkownika lub aplikacji wpływa na integralność danych, region platformy Azure ma awarię lub aplikacja wymaga konserwacji.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funkcje usługi SQL Database, których można użyć, aby zapewnić ciągłość działalności biznesowej

Z punktu widzenia bazy danych istnieją cztery główne potencjalne scenariusze zakłóceń:

- Lokalne awarie sprzętu lub oprogramowania wpływające na węzeł bazy danych, takie jak awaria stacji dysków.
- Uszkodzenie lub usunięcie danych jest zwykle spowodowane usterką aplikacji lub błędem ludzkim. Takie błędy są specyficzne dla aplikacji i zwykle nie mogą być wykrywane przez usługę bazy danych.
- Awaria centrum danych, prawdopodobnie spowodowana klęską żywiołową. Ten scenariusz wymaga pewnego poziomu nadmiarowości geograficznej z trybem failover aplikacji w innym centrum danych.
- Błędy uaktualniania lub konserwacji, nieoczekiwane problemy występujące podczas konserwacji lub uaktualnienia infrastruktury mogą wymagać szybkiego wycofania do wcześniejszego stanu bazy danych.

Aby zmniejszyć ryzyko awarii sprzętu i oprogramowania, SQL Database obejmuje [architekturę o wysokiej dostępności](high-availability-sla.md), która gwarantuje automatyczne odzyskiwanie po tych błędach z uwzględnieniem umowy SLA do 99,995% dostępności.  

Aby chronić firmę przed utratą danych, SQL Database i wystąpienie zarządzane SQL automatycznie tworzy pełne kopie zapasowe bazy danych co tydzień, różnicowe kopie zapasowe bazy danych co 12 godzin oraz kopie zapasowe dziennika transakcji co 5-10 minut. Kopie zapasowe są przechowywane w magazynie RA-GRS przez co najmniej 7 dni dla wszystkich warstw usług. Wszystkie warstwy usług, z wyjątkiem pomoc techniczna Basic konfigurowalnego okresu przechowywania kopii zapasowych dla przywracania do punktu w czasie, do 35 dni.

Wystąpienia zarządzane SQL Database i SQL oferują również kilka funkcji ciągłości biznesowej, których można użyć do ograniczenia różnych nieplanowanych scenariuszy.

- [Tabele danych czasowych](../temporal-tables.md) umożliwiają przywrócenie wersji wiersza z dowolnego punktu w czasie.
- [Wbudowane automatyczne kopie zapasowe](automated-backups-overview.md) i [przywracanie do punktu w czasie](recovery-using-backups.md#point-in-time-restore) umożliwiają przywrócenie kompletnej bazy danych do pewnego momentu w skonfigurowanym okresie przechowywania do 35 dni.
- [Usuniętą bazę danych można przywrócić](recovery-using-backups.md#deleted-database-restore) do punktu, w którym został usunięty, jeśli **serwer nie został usunięty**.
- [Długoterminowe przechowywanie kopii zapasowych](long-term-retention-overview.md) umożliwia przechowywanie kopii zapasowych do 10 lat. Jest to w ograniczonej publicznej wersji zapoznawczej wystąpienia zarządzanego SQL
- [Aktywna replikacja geograficzna](active-geo-replication-overview.md) pozwala na tworzenie replik z możliwością odczytu i ręczne przełączenie w tryb failover do dowolnej repliki w przypadku awarii centrum danych lub uaktualnienia aplikacji.
- [Automatyczna Grupa trybu failover](auto-failover-group-overview.md#terminology-and-capabilities) umożliwia aplikacji automatyczne odzyskiwanie w przypadku awarii centrum danych.

## <a name="recover-a-database-within-the-same-azure-region"></a>Odzyskiwanie bazy danych w tym samym regionie świadczenia usługi Azure

Możesz użyć automatycznych kopii zapasowych bazy danych, aby przywrócić bazę danych do punktu w czasie w przeszłości. W ten sposób można odzyskać dane z uszkodzeń spowodowanych błędami ludzkimi. Przywracanie do punktu w czasie umożliwia utworzenie nowej bazy danych na tym samym serwerze, która reprezentuje stan danych przed uszkodzeniem zdarzenia. W przypadku większości baz danych operacje przywracania trwają krócej niż 12 godzin. Odzyskanie bardzo dużej lub bardzo aktywnej bazy danych może trwać dłużej. Aby uzyskać więcej informacji na temat czasu odzyskiwania, zobacz temat [czas odzyskiwania bazy danych](recovery-using-backups.md#recovery-time).

Jeśli maksymalny obsługiwany okres przechowywania kopii zapasowej dla operacji przywracania do punktu w czasie (kopie) nie jest wystarczający dla aplikacji, można ją przedłużyć, konfigurując zasady długoterminowego przechowywania (LTR) dla baz danych. Aby uzyskać więcej informacji, zobacz [długoterminowe przechowywanie kopii zapasowych](long-term-retention-overview.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Porównywanie replikacji geograficznej z grupami trybu failover

[Grupy autotrybu failover](auto-failover-group-overview.md#terminology-and-capabilities) upraszczają wdrażanie i użycie [replikacji geograficznej](active-geo-replication-overview.md) oraz dodawanie dodatkowych funkcji, zgodnie z opisem w poniższej tabeli:

|                                              | Replikacja geograficzna | Grupy trybu failover  |
|:---------------------------------------------| :-------------- | :----------------|
| **Automatyczne przełączanie w tryb failover**                          |     Nie          |      Tak         |
| **Jednoczesne przełączanie wielu baz danych w tryb failover**  |     Nie          |      Tak         |
| **Użytkownik musi zaktualizować parametry połączenia po przełączeniu w tryb failover.**      |     Tak         |      Nie          |
| **Obsługa usługi SQL Managed Instance**                   |     Nie          |      Tak         |
| **Może znajdować się w tym samym regionie co podstawowa**             |     Tak         |      Nie          |
| **Wiele replik**                            |     Tak         |      Nie          |
| **Obsługuje skalowanie odczytu**                          |     Tak         |      Tak         |


## <a name="recover-a-database-to-the-existing-server"></a>Odzyskiwanie bazy danych na istniejący serwer

Chociaż rzadko, centrum danych platformy Azure może mieć awarię. Taka awaria powoduje zakłócenia działania firmy, które mogą trwać tylko kilka minut, ale mogą też trwać wiele godzin.

- Jedną z opcji jest poczekanie, aż baza danych powróci do trybu online, gdy awarie centrum danych zakończy się. Takie rozwiązanie sprawdza się dla aplikacji, w przypadku których baza danych może być w trybie offline. Może to na przykład dotyczyć projektu tworzenia oprogramowania lub bezpłatnej wersji próbnej, nad którymi nie trzeba pracować na bieżąco. Gdy centrum danych wystąpiło awarie, nie wiesz, jak długo może trwać awaria, więc ta opcja działa tylko wtedy, gdy nie jest potrzebna przez pewien czas.
- Inną opcją jest przywrócenie bazy danych na dowolnym serwerze w dowolnym regionie świadczenia usługi Azure przy użyciu [geograficznie nadmiarowych kopii zapasowych bazy danych](recovery-using-backups.md#geo-restore) (czyli przywracania geograficznego). Przywracanie geograficzne wykorzystuje geograficznie nadmiarową kopię zapasową jako źródło i może służyć do odzyskania bazy danych, nawet jeśli baza danych lub Datacenter jest niedostępna z powodu przestoju.
- Na koniec można szybko odzyskać sprawność po awarii, jeśli skonfigurowano lokację geograficzną z [aktywną replikacją geograficzną](active-geo-replication-overview.md) lub [grupą automatycznego trybu failover](auto-failover-group-overview.md) dla bazy danych lub baz danych. W zależności od wybranych technologii można użyć opcji ręcznego lub automatycznego przełączania do trybu failover. Gdy sama praca awaryjna trwa zaledwie kilka sekund, jej aktywowanie trwa co najmniej 1 godzina. Jest to konieczne, aby zapewnić, że tryb failover jest uzasadniony przez skalę przestojów. Ponadto przejście w tryb failover może spowodować niewielką utratę danych z powodu charakteru replikacji asynchronicznej.

Podczas opracowywania planu zapewniania ciągłości działalności biznesowej należy zrozumieć znaczenie maksymalnego dopuszczalnego czasu oczekiwania na pełne odzyskanie aplikacji po wystąpieniu zdarzenia powodującego zakłócenia. Czas wymagany do pełnego odzyskania aplikacji jest znany jako cel czasu odzyskiwania (RTO). Należy również zapoznać się z maksymalnym okresem ostatnich aktualizacji danych (przedział czasu), przez jaki aplikacja może tolerować utratę przy nieplanowanym zdarzeniu. Potencjalna utrata danych jest znana jako cel punktu odzyskiwania (RPO).

Różne metody odzyskiwania oferują różne poziomy RPO i RTO. Można wybrać określoną metodę odzyskiwania lub użyć kombinacji metod w celu zapewnienia pełnego odzyskiwania aplikacji. Poniższa tabela zawiera porównanie celu punktu odzyskiwania i RTO każdej opcji odzyskiwania. Grupy autotrybu failover upraszczają wdrażanie i użycie replikacji geograficznej oraz dodaje dodatkowe możliwości zgodnie z opisem w poniższej tabeli.

| Metoda odzyskiwania | Cel czasu odzyskiwania | Cel punktu odzyskiwania |
| --- | --- | --- |
| Przywracanie geograficzne z kopii zapasowych replikowanych geograficznie | 12 h | 1 h |
| Grupy automatycznego trybu failover | 1 h | 5 s |
| Ręczna praca awaryjna bazy danych | 30 s | 5 s |

> [!NOTE]
> Ręczne przełączenie do *trybu failover bazy danych* odnosi się do trybu failover pojedynczej bazy danych w jej zapasowej replikacji geograficznej przy użyciu [trybu nieplanowanego](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities)
Zapoznaj się z tabelą we wcześniejszej części tego artykułu, aby uzyskać szczegółowe informacje na temat RTO trybu failover i punktu odzyskiwania.

Użyj grup autotrybu failover, jeśli aplikacja spełnia dowolne z następujących kryteriów:

- Ma kluczowe znaczenie.
- Ma umowę dotyczącą poziomu usług (SLA), która nie jest dozwolona przez 12 godzin lub kilka przestojów.
- Przestój może skutkować odpowiedzialnością finansową.
- Ma wysoką zmianę danych i 1 godzinę utraty danych nie jest akceptowalna.
- Dodatkowy koszt związany z aktywną replikacją geograficzną jest niższy niż potencjalna odpowiedzialność finansowa i powiązane straty biznesowe.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

W zależności od wymagań aplikacji można użyć kombinacji kopii zapasowych bazy danych i aktywnej replikacji geograficznej. Omówienie zagadnień dotyczących projektowania dla autonomicznych baz danych i pul elastycznych korzystających z tych funkcji zapewniania ciągłości działalności biznesowej można znaleźć w temacie Designing a Cloud and rerecoverys ( [Tworzenie](disaster-recovery-strategies-for-applications-with-elastic-pool.md) [aplikacji na potrzeby odzyskiwania po awarii w chmurze](designing-cloud-solutions-for-disaster-recovery.md) )

Poniższe sekcje zawierają omówienie czynności do odzyskania przy użyciu kopii zapasowych bazy danych lub aktywnej replikacji geograficznej. Aby zapoznać się ze szczegółowymi krokami, w tym wymaganiami dotyczącymi planowania, po wykonaniu czynności po odzyskiwaniu i informacje o sposobach symulowania awarii w celu przeprowadzenia przechodzenia do odzyskiwania po awarii, zobacz [odzyskiwanie bazy danych w SQL Database przed awarią](disaster-recovery-guidance.md).

### <a name="prepare-for-an-outage"></a>Przygotowanie do awarii

Niezależnie od używanej funkcji zapewniania ciągłości działalności biznesowej należy:

- Zidentyfikuj i przygotuj serwer docelowy, w tym reguły zapory adresów IP na poziomie serwera, logowania i uprawnienia na poziomie bazy danych Master.
- Określić sposób przekierowania klientów i aplikacji klienckich na nowy serwer.
- Udokumentować inne zależności, takie jak ustawienia inspekcji i alerty

Jeśli nie przygotujesz się prawidłowo, przełączasz aplikacje w tryb online po przejściu w tryb failover lub odzyskiwanie bazy danych trwa dodatkowy czas i prawdopodobnie wymagają również rozwiązywania problemów w czasie nieprawidłowej kombinacji.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Przełączenie w tryb failover do pomocniczej bazy danych z replikacją geograficzną

Jeśli używasz aktywnej replikacji geograficznej lub automatycznych grup trybu failover jako mechanizmu odzyskiwania, możesz skonfigurować zasady automatycznego trybu failover lub [ręcznie nieplanowane przejście w tryb failover](active-geo-replication-configure-portal.md#initiate-a-failover). Po zainicjowaniu tryb failover powoduje, że pomocniczy staje się nowym serwerem podstawowym i gotowy do rejestrowania nowych transakcji i odpowiada na zapytania — z minimalną utratą danych danych, które nie zostały jeszcze zreplikowane. Aby uzyskać informacje na temat projektowania procesu przełączania do trybu failover, zobacz [projektowanie aplikacji na potrzeby odzyskiwania po awarii w chmurze](designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Gdy centrum danych powróci do trybu online, stary Primaries automatycznie ponownie nawiązuje połączenie z nowym serwerem podstawowym i staje się pomocniczymi bazami czasu. Jeśli chcesz zmienić lokalizację główną z powrotem do oryginalnego regionu, możesz zainicjować zaplanowaną pracę w trybie failover ręcznie (powrót po awarii).

### <a name="perform-a-geo-restore"></a>Przeprowadzanie przywracania geograficznego

W przypadku korzystania z automatycznych kopii zapasowych z magazynem geograficznie nadmiarowym (domyślnie włączony) można odzyskać bazę danych przy użyciu funkcji [przywracania geograficznego](disaster-recovery-guidance.md#recover-using-geo-restore). Odzyskiwanie odbywa się zwykle w ciągu 12 godzin — dzięki utracie danych do godziny, która jest określana przez czas wykonania ostatniej kopii zapasowej dziennika i zreplikowanej. Do momentu ukończenia odzyskiwania baza danych nie może rejestrować żadnych transakcji ani odpowiadać na żadne zapytania. Pamiętaj, że przywracanie geograficzne przywraca bazę danych tylko do ostatniego dostępnego punktu w czasie.

> [!NOTE]
> Jeśli centrum danych powróci do trybu online przed przełączeniem aplikacji do odzyskiwanej bazy danych, można anulować odzyskiwanie.

### <a name="perform-post-failover--recovery-tasks"></a>Wykonywanie zadań po przejściu do trybu failover lub po odzyskiwaniu

Po odzyskaniu za pomocą dowolnego mechanizmu odzyskiwania należy wykonać następujące zadania dodatkowe, zanim będzie możliwe ponowne rozpoczęcie pracy przez użytkowników i aplikacje:

- Przekieruj klientów i aplikacje klienckie do nowego serwera i przywróconej bazy danych.
- Upewnij się, że istnieją odpowiednie reguły zapory adresów IP na poziomie serwera umożliwiające użytkownikom łączenie się z [zaporami na poziomie bazy danych](firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) lub korzystanie z nich w celu włączenia odpowiednich reguł.
- Upewnij się, że istnieją odpowiednie uprawnienia na poziomie bazy danych (lub Użyj [zawartych użytkowników](/sql/relational-databases/security/contained-database-users-making-your-database-portable)).
- Skonfiguruj inspekcję zgodnie z potrzebami.
- Skonfiguruj alerty odpowiednio do potrzeb.

> [!NOTE]
> Jeśli używasz grupy trybu failover i nawiążesz połączenie z bazami danych przy użyciu odbiornika odczytu i zapisu, przekierowanie po przejściu do trybu failover będzie wykonywane automatycznie i w sposób przezroczysty dla aplikacji.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Uaktualnianie aplikacji przy minimalnych przestojach

Czasami aplikacja musi być przełączona w tryb offline z powodu planowanej konserwacji, takiej jak uaktualnienie aplikacji. [Zarządzanie uaktualnieniami aplikacji](manage-application-rolling-upgrade.md) zawiera opis sposobu korzystania z funkcji aktywnej replikacji geograficznej w celu zapewnienia stopniowego uaktualniania aplikacji w chmurze w celu zminimalizowania przestojów podczas uaktualniania i zapewnienia ścieżki odzyskiwania, jeśli coś się nie stało.

## <a name="next-steps"></a>Następne kroki

Omówienie zagadnień dotyczących projektowania aplikacji dla pojedynczych baz danych i pul elastycznych znajdują się w temacie [Designing a Cloud](designing-cloud-solutions-for-disaster-recovery.md) and deawaryjne [strategie odzyskiwania](disaster-recovery-strategies-for-applications-with-elastic-pool.md)po awarii w chmurze.
