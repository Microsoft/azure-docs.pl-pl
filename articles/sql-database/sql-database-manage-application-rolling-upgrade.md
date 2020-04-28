---
title: Stopniowe uaktualnienia aplikacji
description: Dowiedz się, jak używać replikacji geograficznej Azure SQL Database do obsługi uaktualnień online aplikacji w chmurze.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 9c627c3e597fdcd3859ce02ea208fc7a8b5d612b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73822853"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Zarządzanie uaktualnieniami stopniowymi aplikacji w chmurze za pomocą SQL Database aktywnej replikacji geograficznej

Dowiedz się, jak korzystać z [aktywnej replikacji geograficznej](sql-database-auto-failover-group.md) w Azure SQL Database, aby umożliwić uaktualnianie stopniowe aplikacji w chmurze. Ze względu na to, że uaktualnienia są operacją zakłócania, powinny być częścią planowania i projektowania ciągłości działania firmy. W tym artykule przedstawiono dwie różne metody organizowania procesu uaktualniania i omawiają zalety i wady poszczególnych opcji. Na potrzeby tego artykułu odwołujemy się do aplikacji, która składa się z witryny sieci Web, która jest połączona z pojedynczą bazą danych jako jej warstwy danych. Naszym celem jest uaktualnienie wersji 1 (v1) aplikacji do wersji 2 (v2) bez znaczącego wpływu na środowisko użytkownika.

Podczas oceny opcji uaktualniania należy wziąć pod uwagę następujące czynniki:

* Wpływ na dostępność aplikacji podczas uaktualnień, takich jak długotrwałe funkcje aplikacji mogą być ograniczone lub obniżone.
* Możliwość wycofywania, Jeśli uaktualnienie nie powiedzie się.
* Luka w zabezpieczeniach aplikacji w przypadku niepowiązanego błędu krytycznego podczas uaktualniania.
* Łączny koszt dolara. Ten czynnik obejmuje dodatkową nadmiarowość bazy danych i przyrostowe koszty składników tymczasowych używanych przez proces uaktualniania.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Uaktualnianie aplikacji korzystających z kopii zapasowych bazy danych na potrzeby odzyskiwania po awarii

Jeśli aplikacja korzysta z automatycznych kopii zapasowych bazy danych i używa przywracania geograficznego do odzyskiwania po awarii, jest wdrażana w jednym regionie świadczenia usługi Azure. Aby zminimalizować zakłócenia użytkownika, należy utworzyć środowisko przejściowe w tym regionie ze wszystkimi składnikami aplikacji należącymi do uaktualnienia. Pierwszy diagram ilustruje środowisko operacyjne przed procesem uaktualniania. Punkt końcowy `contoso.azurewebsites.net` reprezentuje środowisko produkcyjne aplikacji sieci Web. Aby można było wycofać uaktualnienie, należy utworzyć środowisko przejściowe z w pełni zsynchronizowaną kopią bazy danych. Wykonaj następujące kroki, aby utworzyć środowisko przejściowe dla uaktualnienia:

1. Utwórz pomocniczą bazę danych w tym samym regionie świadczenia usługi Azure. Monitoruj pomocniczą, aby sprawdzić, czy proces umieszczania jest zakończony (1).
2. Utwórz nowe środowisko dla aplikacji sieci Web i Wywołaj je "przemieszczanie". Zostanie on zarejestrowany w Azure DNS z adresem URL `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Te kroki przygotowawcze nie wpłyną na środowisko produkcyjne, które może działać w trybie pełnego dostępu.

![SQL Database konfigurację replikacji geograficznej na potrzeby odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Po zakończeniu kroków przygotowania aplikacja jest gotowa do rzeczywistego uaktualnienia. Na następnym diagramie przedstawiono kroki związane z procesem uaktualniania:

1. Ustaw podstawową bazę danych w tryb tylko do odczytu (3). Ten tryb gwarantuje, że środowisko produkcyjne aplikacji sieci Web (v1) pozostaje tylko do odczytu podczas uaktualniania, co uniemożliwia rozbieżność danych między wystąpieniami bazy danych V1 i v2.
2. Odłączanie pomocniczej bazy danych przy użyciu trybu planowanego zakończenia (4). Ta akcja tworzy w pełni zsynchronizowaną, niezależną kopię podstawowej bazy danych. Ta baza danych zostanie uaktualniona.
3. Zmień pomocniczą bazę danych w tryb do odczytu i zapisu, a następnie uruchom skrypt uaktualnienia (5).

![SQL Database konfigurację replikacji geograficznej na potrzeby odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Jeśli uaktualnienie zakończyło się pomyślnie, możesz teraz przystąpić do przełączania użytkowników do uaktualnionej kopii aplikacji, która stanie się środowiskiem produkcyjnym. Przełączenie obejmuje kilka dodatkowych kroków, jak pokazano na następnym diagramie:

1. Aktywuj operację wymiany między środowiskami produkcyjnymi i tymczasowymi aplikacji sieci Web (6). Ta operacja umożliwia przełączenie adresów URL dwóch środowisk. Teraz `contoso.azurewebsites.net` wskazuje wersję w wersji 2 witryny sieci Web i bazę danych (środowisko produkcyjne). 
2. Jeśli wersja w wersji 1 nie jest już potrzebna, która stała się kopią przejściową po wymianie, można zlikwidować środowisko przejściowe (7).

![SQL Database konfigurację replikacji geograficznej na potrzeby odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Jeśli proces uaktualniania zakończy się niepowodzeniem (na przykład ze względu na błąd w skrypcie uaktualnienia), weź pod uwagę środowisko przejściowe. Aby wycofać aplikację do stanu sprzed uaktualnienia, Przywróć dostęp do aplikacji w środowisku produkcyjnym do pełnego dostępu. Na następnym diagramie przedstawiono kroki dotyczące wersji:

1. Ustaw kopię bazy danych na tryb do odczytu i zapisu (8). Ta akcja powoduje przywrócenie pełnej funkcjonalności w wersji 1 kopii produkcyjnej.
2. Wykonaj analizę głównych przyczyn i zlikwidować środowisko przejściowe (9).

W tym momencie aplikacja jest w pełni funkcjonalna i można powtórzyć kroki uaktualniania.

> [!NOTE]
> Wycofanie nie wymaga zmian w systemie DNS, ponieważ nie wykonano jeszcze operacji zamiany.

![SQL Database konfigurację replikacji geograficznej na potrzeby odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Główną zaletą tej opcji jest możliwość uaktualnienia aplikacji w jednym regionie, zgodnie z zestawem prostych kroków. Koszt dolara uaktualnienia jest stosunkowo niski. 

Głównym kompromisem jest to, że w przypadku wystąpienia awarii podczas uaktualniania odzyskiwanie do stanu sprzed uaktualnienia obejmuje ponowne wdrożenie aplikacji w innym regionie i przywrócenie bazy danych z kopii zapasowej przy użyciu funkcji przywracania geograficznego. Ten proces skutkuje znaczącym przestojem.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Uaktualnianie aplikacji korzystających z replikacji geograficznej bazy danych na potrzeby odzyskiwania po awarii

Jeśli aplikacja używa aktywnej replikacji geograficznej lub grupy autotrybu failover w celu zapewnienia ciągłości działania, jest wdrażana do co najmniej dwóch różnych regionów. Istnieje aktywna, podstawowa baza danych w regionie podstawowym i tylko do odczytu, pomocnicza baza danych w regionie kopii zapasowej. Wraz z czynnikami wymienionymi na początku tego artykułu, proces uaktualniania musi również zagwarantować, że:

* Aplikacja pozostaje chroniona przed błędami w trakcie procesu uaktualniania.
* Elementy geograficznie nadmiarowe aplikacji są uaktualniane równolegle z aktywnymi składnikami.

Aby osiągnąć te cele, oprócz korzystania ze środowisk Web Apps można korzystać z usługi Azure Traffic Manager przy użyciu profilu trybu failover z jednym aktywnym punktem końcowym i jednym punktem końcowym kopii zapasowej. Następny diagram ilustruje środowisko operacyjne przed procesem uaktualniania. Witryny `contoso-1.azurewebsites.net` sieci Web i `contoso-dr.azurewebsites.net` reprezentują środowisko produkcyjne aplikacji z pełną nadmiarowością geograficzną. Środowisko produkcyjne obejmuje następujące składniki:

* Środowisko produkcyjne aplikacji `contoso-1.azurewebsites.net` sieci Web w regionie podstawowym (1)
* Podstawowa baza danych w regionie podstawowym (2)
* Wystąpienie w stanie wstrzymania aplikacji sieci Web w regionie kopii zapasowej (3)
* Pomocnicza baza danych z replikacją geograficzną w regionie kopii zapasowej (4)
* Profil wydajności Traffic Manager z wywoływanym `contoso-1.azurewebsites.net` punktem końcowym online i wywoływanym punktem końcowym w trybie offline`contoso-dr.azurewebsites.net`

Aby umożliwić wycofanie uaktualnienia, należy utworzyć środowisko przejściowe z w pełni zsynchronizowaną kopią aplikacji. Ponieważ trzeba upewnić się, że aplikacja może szybko wykonać odzyskiwanie w przypadku wystąpienia awarii w procesie uaktualniania, środowisko przejściowe musi być również Geograficznie nadmiarowy. Następujące kroki są wymagane do utworzenia środowiska przejściowego dla uaktualnienia:

1. Wdróż środowisko przejściowe aplikacji sieci Web w regionie podstawowym (6).
2. Utwórz pomocniczą bazę danych w podstawowym regionie platformy Azure (7). Skonfiguruj środowisko przejściowe aplikacji sieci Web, aby nawiązać z nią połączenie. 
3. Utwórz kolejną geograficznie nadmiarową, pomocniczą bazę danych w regionie kopii zapasowej, replikując pomocniczą bazę danych w regionie podstawowym. (Ta metoda jest nazywana *replikacją geograficzną*). (8).
4. Wdróż środowisko przejściowe wystąpienia aplikacji sieci Web w regionie kopii zapasowej (9) i skonfiguruj je w taki sposób, aby połączyć geograficznie nadmiarową bazę pomocniczą utworzoną w (8).

> [!NOTE]
> Te kroki przygotowawcze nie wpłyną na aplikację w środowisku produkcyjnym. Będzie on w pełni funkcjonalny w trybie do odczytu i zapisu.

![SQL Database konfigurację replikacji geograficznej na potrzeby odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Po zakończeniu czynności przygotowawczych środowisko przejściowe jest gotowe do uaktualnienia. Na następnym diagramie przedstawiono następujące kroki uaktualniania:

1. Ustaw podstawową bazę danych w środowisku produkcyjnym na tryb tylko do odczytu (10). Ten tryb gwarantuje, że produkcyjna baza danych (v1) nie ulegnie zmianie podczas uaktualniania, uniemożliwiając w ten sposób rozbieżność danych między wystąpieniami bazy danych V1 i v2.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Przerwij replikację geograficzną, odłączając dodatkową (11). Ta akcja tworzy niezależną, ale w pełni zsynchronizowaną kopię produkcyjnej bazy danych. Ta baza danych zostanie uaktualniona. W poniższym przykładzie jest stosowane [środowisko](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) Transact-SQL, ale jest on również dostępny. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Uruchom skrypt uaktualnienia w odniesieniu `contoso-1-staging.azurewebsites.net`do, `contoso-dr-staging.azurewebsites.net`i tymczasowej podstawowej bazy danych (12). Zmiany w bazie danych zostaną zreplikowane automatycznie do tymczasowego pomocniczego.

![SQL Database konfigurację replikacji geograficznej na potrzeby odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Jeśli uaktualnienie zakończyło się pomyślnie, możesz teraz przystąpić do przełączania użytkowników do wersji v2 aplikacji. Następny diagram ilustruje następujące czynności:

1. Aktywuj operację wymiany między środowiskami produkcyjnymi i tymczasowymi aplikacji sieci Web w regionie podstawowym (13) i w regionie kopii zapasowej (14). Wersja 2 aplikacji jest teraz środowiskiem produkcyjnym z nadmiarową kopią w regionie kopii zapasowej.
2. Jeśli aplikacja V1 (15 i 16) nie jest już potrzebna, można zlikwidować środowisko przejściowe.

![SQL Database konfigurację replikacji geograficznej na potrzeby odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Jeśli proces uaktualniania zakończy się niepowodzeniem (na przykład ze względu na błąd w skrypcie uaktualnienia), należy wziąć pod uwagę, że środowisko przejściowe będzie w stanie niespójnym. Aby wycofać aplikację do stanu sprzed uaktualnienia, powróć do korzystania z wersji 1 aplikacji w środowisku produkcyjnym. Wymagane kroki są wyświetlane na następnym diagramie:

1. Ustaw kopię podstawowej bazy danych w środowisku produkcyjnym na tryb odczytu i zapisu (17). Ta akcja przywraca pełne funkcje w wersji 1 w środowisku produkcyjnym.
2. Wykonaj analizę głównych przyczyn i napraw lub Usuń środowisko przejściowe (18 i 19).

W tym momencie aplikacja jest w pełni funkcjonalna i można powtórzyć kroki uaktualniania.

> [!NOTE]
> Wycofanie nie wymaga zmian w systemie DNS, ponieważ nie wykonano operacji zamiany.

![SQL Database konfigurację replikacji geograficznej na potrzeby odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

Zaletą tej opcji jest możliwość uaktualnienia zarówno aplikacji, jak i jej geograficznie nadmiarowej kopii bez naruszania ciągłości działalności biznesowej podczas uaktualniania.

Głównym kompromisem jest to, że wymaga podwójnej nadmiarowości każdego składnika aplikacji i w związku z tym jest większym kosztem dolara. Obejmuje również bardziej skomplikowany przepływ pracy.

## <a name="summary"></a>Podsumowanie

Dwie metody uaktualniania opisane w artykule różnią się w zależności od stopnia złożoności i kosztu dolara, ale jednocześnie koncentrują się na minimalizacji czasu, w którym użytkownik jest ograniczony do operacji tylko do odczytu. Czas ten jest definiowany bezpośrednio przez czas trwania skryptu uaktualniania. Nie zależy to od rozmiaru bazy danych, wybranej warstwy usług, konfiguracji witryny sieci Web ani innych czynników, których nie można łatwo kontrolować. Wszystkie kroki przygotowania są oddzielone od kroków uaktualnienia i nie mają wpływu na aplikację produkcyjną. Wydajność skryptu uaktualnienia jest kluczowym czynnikiem, który określa środowisko użytkownika podczas uaktualniania. Dlatego najlepszym sposobem na ulepszenie tego środowiska jest skoncentrowanie się na zadaniu skryptu uaktualniania, jak to możliwe.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z omówieniem i scenariuszami ciągłości działania, zobacz temat [ciągłość działania — Omówienie](sql-database-business-continuity.md).
* Aby dowiedzieć się więcej o Azure SQL Database aktywnej replikacji geograficznej, zobacz temat [Tworzenie pomocniczych baz danych przy użyciu aktywnej replikacji geograficznej](sql-database-active-geo-replication.md).
* Aby dowiedzieć się więcej na temat Azure SQL Database grup autotrybu failover, zobacz [Korzystanie z grup autotrybu failover w celu zapewnienia przezroczystej i skoordynowanej pracy w trybie failover wielu baz danych](sql-database-auto-failover-group.md).
* Aby dowiedzieć się więcej na temat środowisk przejściowych w Azure App Service, zobacz [Konfigurowanie środowisk przejściowych w programie Azure App Service](../app-service/deploy-staging-slots.md).
* Aby dowiedzieć się więcej o profilach usługi Azure Traffic Manager, zobacz [Zarządzanie profilem Traffic Manager platformy Azure](../traffic-manager/traffic-manager-manage-profiles.md).
