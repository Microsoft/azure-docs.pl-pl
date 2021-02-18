---
title: Odzyskiwanie po awarii
description: Dowiedz się, jak odzyskać bazę danych z regionalnego przestoju lub awarii centrum danych przy Azure SQL Database aktywnej replikacji geograficznej i możliwościach przywracania geograficznego.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/21/2019
ms.openlocfilehash: 322ef3b8ca30396bd4772850ff651f7545a21257
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580757"
---
# <a name="restore-your-azure-sql-database-or-failover-to-a-secondary"></a>Przywracanie Azure SQL Database lub przełączenia w tryb failover do pomocniczego
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database oferuje następujące możliwości odzyskiwania po awarii:

- [Aktywna replikacja geograficzna](active-geo-replication-overview.md)
- [Grupy automatycznego trybu failover](auto-failover-group-overview.md)
- [Przywracanie geograficzne](recovery-using-backups.md#point-in-time-restore)
- [Strefowo nadmiarowe bazy danych](high-availability-sla.md)

Aby dowiedzieć się więcej o scenariuszach ciągłości biznesowej i funkcjach obsługujących te scenariusze, zobacz [ciągłość](business-continuity-high-availability-disaster-recover-hadr-overview.md)działania.

> [!NOTE]
> W przypadku używania strefowo nadmiarowych baz danych lub Krytyczne dla działania firmy, proces odzyskiwania jest zautomatyzowany, a pozostała część tego materiału nie ma zastosowania.
>
> Podstawowa i pomocnicza baza danych muszą mieć tę samą warstwę usług. Zdecydowanie zaleca się również utworzenie pomocniczej bazy danych z tym samym rozmiarem obliczeń (DTU lub rdzeni wirtualnych) jako podstawową. Aby uzyskać więcej informacji, zobacz [Uaktualnianie lub obniżanie wersji jako podstawowej bazy danych](active-geo-replication-overview.md#upgrading-or-downgrading-primary-database).
>
> Aby zarządzać trybem failover wielu baz danych, należy użyć jednej lub kilku grup trybu failover.
> W przypadku dodania istniejącej relacji replikacji geograficznej do grupy trybu failover upewnij się, że dla elementu podrzędnego jest skonfigurowana ta sama warstwa usługi i rozmiar obliczeń jako podstawowa. Aby uzyskać więcej informacji, zobacz [Korzystanie z grup autotrybu failover w celu zapewnienia przezroczystej i skoordynowanej pracy w trybie failover wielu baz danych](auto-failover-group-overview.md).

## <a name="prepare-for-the-event-of-an-outage"></a>Przygotuj się na zdarzenie przestoju

W przypadku pomyślnego odzyskiwania do innego obszaru danych przy użyciu grup trybu failover lub kopii zapasowych nadmiarowych należy przygotować serwer w innej awarii centrum danych, aby stał się nowym serwerem podstawowym, w razie potrzeby również mieć dobrze zdefiniowane kroki udokumentowane i przetestowane w celu zapewnienia sprawnego odzyskiwania. Te kroki przygotowawcze obejmują:

- Zidentyfikuj serwer w innym regionie, aby stał się nowym serwerem podstawowym. W przypadku przywracania geograficznego jest to ogólnie serwer w [sparowanym regionie](../../best-practices-availability-paired-regions.md) dla regionu, w którym znajduje się baza danych. Eliminuje to dodatkowy koszt ruchu podczas operacji przywracania geograficznego.
- Zidentyfikuj i opcjonalnie Zdefiniuj reguły zapory adresów IP na poziomie serwera, które są konieczne dla użytkowników w celu uzyskania dostępu do nowej podstawowej bazy danych.
- Określ sposób przekierowywania użytkowników do nowego serwera podstawowego, na przykład przez zmianę parametrów połączenia lub zmianę wpisów DNS.
- Zidentyfikuj i opcjonalnie Utwórz, logowania, które muszą znajdować się w bazie danych Master na nowym serwerze podstawowym, i upewnij się, że te nazwy logowania mają odpowiednie uprawnienia w bazie danych Master (jeśli istnieją). Aby uzyskać więcej informacji, zobacz [SQL Database zabezpieczenia po odzyskiwaniu po awarii](active-geo-replication-security-configure.md)
- Zidentyfikuj reguły alertów, które muszą zostać zaktualizowane w celu zamapowania na nową podstawową bazę danych.
- Udokumentowanie konfiguracji inspekcji w bieżącej podstawowej bazie danych
- Wykonaj [drążenie odzyskiwania po awarii](disaster-recovery-drills.md). Aby symulować awarię w przypadku przywracania geograficznego, można usunąć źródłową bazę danych lub zmienić jej nazwę, aby spowodować awarię łączności aplikacji. Aby symulować awarię przy użyciu grup trybu failover, można wyłączyć aplikację sieci Web lub maszynę wirtualną połączonej z bazą danych lub przełączyć ją w tryb failover w celu spowodowania błędów łączności aplikacji.

## <a name="when-to-initiate-recovery"></a>Kiedy należy inicjować odzyskiwanie

Operacja odzyskiwania ma wpływ na aplikację. Wymaga zmiany parametrów połączenia SQL lub przekierowania przy użyciu systemu DNS, co może skutkować utratą danych trwałych. W związku z tym należy to zrobić tylko wtedy, gdy przestoje najprawdopodobniej będzie trwać dłużej niż cel czasu odzyskiwania aplikacji. Gdy aplikacja jest wdrażana w środowisku produkcyjnym, należy regularnie monitorować kondycję aplikacji i używać następujących punktów danych w celu potwierdzenia, że odzyskiwanie jest uzasadnione:

1. Trwała awaria łączności z poziomu aplikacji do bazy danych.
2. Azure Portal pokazuje alert dotyczący incydentu w regionie o szerokim wpływie.

> [!NOTE]
> Jeśli używasz grup trybu failover i wybierasz automatyczną pracę awaryjną, proces odzyskiwania jest zautomatyzowany i niewidoczny dla aplikacji.

W zależności od tolerancji aplikacji do przestoju i możliwej odpowiedzialności biznesowej można wziąć pod uwagę następujące opcje odzyskiwania.

Użyj funkcji [Pobierz odzyskiwalną bazę danych](/previous-versions/azure/reference/dn800985(v=azure.100)) (*LastAvailableBackupDate*), aby uzyskać najnowszy punkt przywracania z replikacją geograficzną.

## <a name="wait-for-service-recovery"></a>Zaczekaj na odzyskanie usługi

Zespoły platformy Azure pracujemyą w celu przywrócenia dostępności usługi tak szybko, jak to możliwe, ale w zależności od przyczyny głównej może upłynąć kilka godzin lub dni.  Jeśli aplikacja może tolerować znaczący przestój, możesz po prostu poczekać na ukończenie odzyskiwania. W takim przypadku nie jest wymagana żadna akcja z Twojej strony. Bieżący stan usługi można zobaczyć na naszym [pulpicie nawigacyjnym Azure Service Health](https://azure.microsoft.com/status/). Po odzyskaniu regionu dostępność aplikacji zostanie przywrócona.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Przełączanie do trybu failover na serwerze pomocniczym z replikacją geograficzną w grupie pracy awaryjnej

Jeśli przestój aplikacji może skutkować odpowiedzialnością biznesową, należy korzystać z grup trybu failover. Dzięki temu aplikacja może szybko przywrócić dostępność w innym regionie w przypadku przestoju. Aby zapoznać się z samouczkiem, zobacz [implementowanie rozproszonej geograficznie bazy danych](geo-distributed-application-configure-tutorial.md).

Aby przywrócić dostępność baz danych, należy zainicjować tryb failover na serwerze pomocniczym przy użyciu jednej z obsługiwanych metod.

Użyj jednej z następujących przewodników, aby przełączyć się w tryb failover do pomocniczej bazy danych replikowanej geograficznie:

- [Przełączenie w tryb failover na serwer pomocniczy z replikacją geograficzną przy użyciu Azure Portal](active-geo-replication-configure-portal.md)
- [Przechodzenie w tryb failover na serwerze pomocniczym przy użyciu programu PowerShell](scripts/setup-geodr-and-failover-database-powershell.md)
- [Przełączanie do trybu failover na serwerze pomocniczym przy użyciu języka Transact-SQL (T-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Odzyskaj przy użyciu funkcji przywracania geograficznego

Jeśli przestój aplikacji nie powoduje odpowiedzialności biznesowej, można użyć funkcji [przywracania geograficznego](recovery-using-backups.md) jako metody odzyskiwania baz danych aplikacji. Tworzy kopię bazy danych z jej najnowszej geograficznie nadmiarowej kopii zapasowej.

## <a name="configure-your-database-after-recovery"></a>Skonfiguruj bazę danych po odzyskaniu

Jeśli używasz funkcji przywracania geograficznego do odzyskiwania po awarii, musisz upewnić się, że połączenie z nowymi bazami danych jest prawidłowo skonfigurowane, aby można było wznowić normalną funkcję aplikacji. Jest to lista kontrolna zadań umożliwiających przywrócenie gotowej produkcji bazy danych.

### <a name="update-connection-strings"></a>Aktualizowanie parametrów połączenia

Ponieważ odzyskana baza danych znajduje się na innym serwerze, należy zaktualizować parametry połączenia aplikacji, aby wskazywały na ten serwer.

Aby uzyskać więcej informacji na temat zmiany parametrów połączenia, zobacz odpowiedni język programistyczny dla [biblioteki połączeń](connect-query-content-reference-guide.md#libraries).

### <a name="configure-firewall-rules"></a>Konfigurowanie reguł zapory

Należy upewnić się, że reguły zapory skonfigurowane na serwerze i w bazie danych są zgodne z tymi, które zostały skonfigurowane na serwerze podstawowym i w podstawowej bazie danych. Aby uzyskać więcej informacji, zobacz [How to: Configure firewall Settings (Azure SQL Database)](firewall-configure.md).

### <a name="configure-logins-and-database-users"></a>Konfigurowanie logowania i użytkowników bazy danych

Musisz się upewnić, że wszystkie logowania używane przez aplikację istnieją na serwerze, na którym jest obsługiwana odzyskana baza danych. Aby uzyskać więcej informacji, zobacz [Konfiguracja zabezpieczeń na potrzeby replikacji geograficznej](active-geo-replication-security-configure.md).

> [!NOTE]
> Podczas przechodzenia do szczegółów odzyskiwania po awarii należy skonfigurować i przetestować reguły zapory serwera oraz nazwy logowania (i ich uprawnienia). Te obiekty na poziomie serwera i ich konfiguracje mogą nie być dostępne podczas przestoju.

### <a name="setup-telemetry-alerts"></a>Konfigurowanie alertów telemetrycznych

Musisz się upewnić, że istniejące ustawienia reguły alertu zostały zaktualizowane, aby mapować do odzyskiwanej bazy danych i innego serwera.

Aby uzyskać więcej informacji na temat reguł alertów bazy danych, zobacz [Odbieranie powiadomień o alertach](../../azure-monitor/alerts/alerts-overview.md) i [śledzenie Service Health](../../service-health/service-notifications.md).

### <a name="enable-auditing"></a>Włącz inspekcję

Jeśli do uzyskania dostępu do bazy danych jest wymagana inspekcja, należy włączyć inspekcję po odzyskaniu bazy danych. Aby uzyskać więcej informacji, zobacz [Inspekcja bazy danych](../../azure-sql/database/auditing-overview.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o Azure SQL Database zautomatyzowanych kopii zapasowych, zobacz [SQL Database zautomatyzowane kopie zapasowe](automated-backups-overview.md)
- Aby dowiedzieć się więcej o scenariuszach związanych z planowaniem i odzyskiwaniem ciągłości biznesowej, zobacz [scenariusze ciągłości](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Aby dowiedzieć się więcej o korzystaniu z automatycznych kopii zapasowych na potrzeby odzyskiwania, zobacz [przywracanie bazy danych z kopii zapasowych inicjowanych przez usługę](recovery-using-backups.md)