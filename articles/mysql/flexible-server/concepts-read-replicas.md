---
title: Odczytaj repliki — Azure Database for MySQL — elastyczny serwer
description: 'Informacje na temat odczytu replik w Azure Database for MySQL elastycznym serwerze: Tworzenie replik, łączenie z replikami, monitorowanie replikacji i zatrzymywanie replikacji.'
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 01/14/2021
ms.openlocfilehash: fa7cc9b9a09bfd2bc503640272b5e7ac3a0a7b58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251305"
---
# <a name="read-replicas-in-azure-database-for-mysql---flexible-server"></a>Odczytywanie replik na serwerze elastycznym Azure Database for MySQL

> [!IMPORTANT]
> Odczytaj repliki w Azure Database for MySQL-elastycznym serwerze jest w wersji zapoznawczej.

MySQL to jeden z popularnych aparatów baz danych do uruchamiania aplikacji sieci Web i mobilnych w skali internetowej. Wielu naszych klientów używa jej do usług edukacyjnych online, usług przesyłania strumieniowego wideo, cyfrowych rozwiązań płatniczych, platform handlu elektronicznego, usług gier, portali Aktualności, administracji publicznej i usług opieki zdrowotnej. Te usługi są wymagane do obsłużenia i skalowania w miarę wzrostu ruchu w sieci Web lub aplikacji mobilnej.

Po stronie aplikacje aplikacja jest zazwyczaj opracowywana w języku Java lub php i migrowana do uruchamiania w ramach zestawów skalowania maszyn wirtualnych platformy Azure lub platformy Azure App Services lub są one kontenerami do uruchamiania w usłudze Azure Kubernetes Service (AKS). W przypadku zestawu skalowania maszyn wirtualnych, App Service lub AKS jako infrastruktury źródłowej, skalowanie aplikacji jest uproszczone przez natychmiastowe Inicjowanie obsługi nowych maszyn wirtualnych i replikowanie bezstanowych składników aplikacji w celu przewidzenia żądań, ale często baza danych ma wpływ na wąskie gardło.

Funkcja Read Replica umożliwia replikowanie danych z Azure Database for MySQL serwera elastycznego na serwer tylko do odczytu. Można replikować z serwera źródłowego do maksymalnie **10** replik. Repliki są aktualizowane asynchronicznie przy użyciu natywnej technologii replikacji aparatu programu MySQL opartej na pozycji w pliku dziennika binarnego (binlog). Aby dowiedzieć się więcej na temat replikacji binlog, zobacz [Omówienie replikacji MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Repliki to nowe serwery, którymi można zarządzać podobnie jak w przypadku źródeł Azure Database for MySQL elastycznych serwerów. Opłaty za każdą replikę odczytu są naliczane na podstawie zainicjowanej obliczeń w rdzeni wirtualnych i magazynu w GB/miesiąc. Aby uzyskać więcej informacji, zobacz [cennik](./concepts-compute-storage.md#pricing).

Aby dowiedzieć się więcej na temat funkcji i problemów związanych z replikacją MySQL, zobacz [dokumentację dotyczącą replikacji MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Ten artykuł zawiera odwołania do warunku _podrzędnego_, termin, który nie jest już wykorzystywany przez firmę Microsoft. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.
>

## <a name="common-use-cases-for-read-replica"></a>Typowe przypadki użycia dla repliki odczytu

Funkcja odczytu repliki pomaga zwiększyć wydajność i skalowalność obciążeń intensywnie korzystających z odczytu. Obciążenia odczytu mogą być izolowane dla replik, podczas gdy obciążenia zapisu mogą być kierowane do źródła.

Typowe scenariusze są następujące:

* Skalowanie zadań do odczytu z aplikacji przy użyciu uproszczonego serwera proxy połączenia, takiego jak [ProxySQL](https://aka.ms/ProxySQLLoadBalanceReplica) lub wzorca opartego na mikrousługach, do skalowania zapytań odczytu pochodzących z aplikacji do odczytu replik
* Obciążenia analizy biznesowej lub analitycznej mogą używać replik odczytu jako źródła danych do raportowania
* W przypadku scenariusza IoT lub produkcyjnego, w którym informacje telemetryczne są pozyskiwane w aparacie bazy danych MySQL, podczas gdy wiele replik odczytu jest używanych do raportowania danych

Ponieważ repliki są tylko do odczytu, nie zmniejszają bezpośrednio obciążeń związanych z możliwością zapisu w źródle. Ta funkcja nie jest przeznaczona dla obciążeń intensywnie korzystających z zapisu.

Funkcja odczytu repliki korzysta z replikacji asynchronicznej MySQL. Ta funkcja nie jest przeznaczona do scenariuszy replikacji synchronicznej. Nastąpi wymierne opóźnienie między źródłem a repliką. Dane w replice ostatecznie staną się spójne z danymi ze źródła. Użyj tej funkcji dla obciążeń, które mogą obsłużyć to opóźnienie.

## <a name="create-a-replica"></a>Tworzenie repliki

Jeśli serwer źródłowy nie ma istniejących serwerów repliki, źródło zostanie najpierw ponownie uruchomione w celu przygotowania się do replikacji.

Po uruchomieniu przepływu pracy tworzenia repliki zostanie utworzony pusty serwer Azure Database for MySQL. Nowy serwer jest wypełniony danymi znajdującymi się na serwerze źródłowym. Czas utworzenia zależy od ilości danych w źródle oraz czasu od ostatniego cotygodniowej pełnej kopii zapasowej. Czas może się wahać od kilku minut do kilku godzin.

> [!NOTE]
> Repliki odczytu są tworzone z tą samą konfiguracją serwera co źródło. Konfigurację serwera repliki można zmienić po jego utworzeniu. Serwer repliki jest zawsze tworzony w tej samej grupie zasobów, w tej samej lokalizacji i tej samej subskrypcji co serwer źródłowy. Jeśli chcesz utworzyć serwer repliki w innej grupie zasobów lub innej subskrypcji, możesz [przenieść serwer repliki](../../azure-resource-manager/management/move-resource-group-and-subscription.md) po utworzeniu. Zaleca się, aby konfiguracja serwera repliki była utrzymywana z równymi lub większymi wartościami niż źródło, aby upewnić się, że replika jest w stanie zachować ze źródłem.

Dowiedz się [, jak utworzyć replikę odczytu w Azure Portal](how-to-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Nawiązywanie połączenia z repliką

Podczas tworzenia repliki dziedziczy Metoda łączności serwera źródłowego. Nie można zmienić metody łączności repliki. Na przykład jeśli serwer źródłowy ma **dostęp prywatny (Integracja z siecią wirtualną)** , replika nie może być **dostęp publiczny (dozwolone adresy IP)**.

Replika dziedziczy konto administratora z serwera źródłowego. Wszystkie konta użytkowników na serwerze źródłowym są replikowane do replik odczytu. Można nawiązać połączenie z repliką odczytu tylko przy użyciu kont użytkowników, które są dostępne na serwerze źródłowym.

Można nawiązać połączenie z repliką przy użyciu nazwy hosta i prawidłowego konta użytkownika, tak jak w przypadku zwykłego Azure Database for MySQL elastycznego serwera. W przypadku serwera o nazwie Moja **replika** z **nazwą administratora** administrator można nawiązać połączenie z repliką przy użyciu interfejsu wiersza polecenia MySQL:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin -p
```

W wierszu polecenia wprowadź hasło dla konta użytkownika.

## <a name="monitor-replication"></a>Monitorowanie replikacji

Azure Database for MySQL elastyczny serwer zapewnia **opóźnienie replikacji w sekundach** w Azure monitor. Ta Metryka jest dostępna tylko dla replik. Ta Metryka jest obliczana przy użyciu `seconds_behind_master` metryki dostępnej w `SHOW SLAVE STATUS` poleceniu programu MySQL. Ustaw Alert, aby poinformować Cię, gdy zwłoka replikacji osiągnie wartość, która nie jest akceptowalna dla obciążenia.

Jeśli widzisz zwiększone opóźnienie replikacji, zapoznaj się z tematem [Rozwiązywanie problemów z opóźnieniem replikacji](./../howto-troubleshoot-replication-latency.md) , aby rozwiązać problemy i zrozumieć możliwe przyczyny.

## <a name="stop-replication"></a>Zatrzymywanie replikacji

Można zatrzymać replikację między źródłem a repliką. Po zatrzymaniu replikacji między serwerem źródłowym a repliką odczytu replika stanie się serwerem autonomicznym. Dane na serwerze autonomicznym to dane, które były dostępne w replice w momencie uruchomienia polecenia Zatrzymaj replikację. Serwer autonomiczny nie znajduje się na serwerze źródłowym.

Gdy zdecydujesz się zatrzymać replikację do repliki, utraci ona wszystkie linki do poprzednich źródeł i innych replik. Między źródłem i jego repliką nie ma automatycznej pracy awaryjnej.

> [!IMPORTANT]
> Serwer autonomiczny nie może zostać ponownie utworzony w replice.
> Przed zatrzymaniem replikacji w replice odczytu upewnij się, że replika ma wszystkie wymagane dane.

Dowiedz się, jak [zatrzymać replikację do repliki](how-to-read-replicas-portal.md).

## <a name="failover"></a>Tryb failover

Nie istnieje automatyczna praca awaryjna między serwerami źródłowym i repliki.

Odczyt replik jest przeznaczony do skalowania intensywnych obciążeń odczytu i nie jest przeznaczony do zaspokajania potrzeb wysokiej dostępności serwera. Nie istnieje automatyczna praca awaryjna między serwerami źródłowym i repliki. Zatrzymywanie replikacji w odniesieniu do repliki odczytu w celu przełączenia jej w tryb online w trybie odczytu jest sposobem, w jaki wykonywane jest ręczne przejście do trybu failover.

Ponieważ replikacja jest asynchroniczna, między źródłem a repliką występuje opóźnienie. Na okres zwłoki może wpływać wiele czynników, takich jak zmniejszanie obciążenia uruchomionego na serwerze źródłowym i opóźnienia między centrami danych. W większości przypadków opóźnienia repliki wynoszą od kilku sekund do kilku minut. Rzeczywiste opóźnienie replikacji można śledzić przy użyciu *opóźnienia repliki* metryk, które jest dostępne dla każdej repliki. Ta Metryka przedstawia czas od ostatniego odtworzonej transakcji. Zalecamy, aby określić, co to jest średnie opóźnienie, obserwując opóźnienie repliki w danym okresie czasu. Można ustawić alert w przypadku zwłoki repliki, aby w przypadku, gdy znajdzie się poza oczekiwanym zakresem, można wykonać akcję.

> [!Tip]
> W przypadku przejścia w tryb failover do repliki zwłoka w momencie odłączenia repliki od źródła będzie wskazywać, ile danych jest utraconych.

Po podjęciu decyzji o przejściu do trybu failover w replice:

1. Zatrzymaj replikację do repliki<br/>
   Ten krok jest niezbędny, aby serwer repliki mógł akceptować operacje zapisu. W ramach tego procesu serwer repliki zostanie odłączone od źródła. Po zainicjowaniu zatrzymania replikacji proces zaplecza zwykle trwa około 2 minuty. Zapoznaj się z sekcją [Zatrzymaj replikację](#stop-replication) tego artykułu, aby poznać konsekwencje tej akcji.

2. Wskazywanie aplikacji na (dawniej) replikę<br/>
   Każdy serwer ma unikatowe parametry połączenia. Zaktualizuj swoją aplikację, tak aby wskazywała na (dawniej) replikę, a nie źródłową.

Po pomyślnym przetworzeniu odczytów i zapisów aplikacja została ukończona w trybie failover. Czas przestoju, w jakim zależą od aplikacji, będzie zależny od tego, kiedy wykryjesz problem, i wykonaj kroki 1 i 2 powyżej.

## <a name="considerations-and-limitations"></a>Istotne zagadnienia i ograniczenia

| Scenariusz | Ograniczenie/zagadnienie |
|:-|:-|
| Replika na serwerze ze strefą — nadmiarowa HA włączona | Nieobsługiwane |
| Replikacja odczytu między regionami | Nieobsługiwane |
| Ceny | Koszt uruchomienia serwera repliki jest oparty na regionie, w którym jest uruchomiony serwer repliki |
| Ponowne uruchamianie serwera źródłowego | Podczas tworzenia repliki dla źródła, które nie ma istniejących replik, źródło zostanie najpierw ponownie uruchomione w celu przygotowania się do replikacji. Weź pod uwagę i wykonaj te operacje w okresie poza szczytem |
| Nowe repliki | Replika odczytu jest tworzona jako nowy Azure Database for MySQL elastyczny serwer. Nie można wykonać istniejącego serwera w replice. Nie można utworzyć repliki innej repliki odczytu |
| Konfiguracja repliki | Replika jest tworzona przy użyciu tej samej konfiguracji serwera co źródło. Po utworzeniu repliki można zmienić kilka ustawień niezależnie od serwera źródłowego: generowanie obliczeń, rdzeni wirtualnych, magazyn i okres przechowywania kopii zapasowych. Warstwę obliczeniową można także zmienić niezależnie.<br> <br> **WAŻNE**  <br> -Przed zaktualizowaniem konfiguracji serwera źródłowego do nowych wartości, zaktualizuj konfigurację repliki do wartości równej lub wyższej. Dzięki temu replika może być na bieżąco ze zmianami wprowadzonymi w źródle. <br/> Ustawienia metody i parametrów łączności są dziedziczone z serwera źródłowego do repliki podczas tworzenia repliki. Następnie reguły repliki są niezależne. |
| Repliki zatrzymane | Jeśli zatrzymasz replikację między serwerem źródłowym i repliką odczytu, zatrzymana replika stanie się autonomicznym serwerem, który akceptuje oba operacje odczytu i zapisu. Serwer autonomiczny nie może zostać ponownie utworzony w replice. |
| Usunięte serwery źródłowe i autonomiczne | Po usunięciu serwera źródłowego replikacja zostaje zatrzymana dla wszystkich replik odczytu. Te repliki automatycznie stają się serwerami autonomicznymi i mogą akceptować zarówno operacje odczytu, jak i zapisu. Serwer źródłowy jest usuwany. |
| Konta użytkowników | Użytkownicy na serwerze źródłowym są replikowana do replik odczytu. Można nawiązać połączenie z repliką odczytu tylko przy użyciu kont użytkowników dostępnych na serwerze źródłowym. |
| Parametry serwera | Aby zapobiec utracie synchronizacji danych i ich możliwej utracie lub uszkodzeniu, aktualizacja niektórych parametrów jest zablokowana w przypadku korzystania z replik do odczytu. <br> Następujące parametry serwera są blokowane zarówno na serwerze źródłowym, jak i w programie Replica:<br> - [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) <br> - [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) <br> [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler)Parametr jest zablokowany na serwerach repliki. <br> Aby zaktualizować jeden z powyższych parametrów na serwerze źródłowym, Usuń serwery repliki, zaktualizuj wartość parametru w źródle i ponownie utwórz repliki. |
| Inne | -Tworzenie repliki repliki nie jest obsługiwane. <br> Tabele w pamięci mogą spowodować, że repliki nie zostaną zsynchronizowane. Jest to ograniczenie technologii replikacji MySQL. Więcej informacji można znaleźć w [dokumentacji programu MySQL Reference](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) . <br>-Upewnij się, że tabele serwera źródłowego mają klucze podstawowe. Brak kluczy podstawowych może spowodować opóźnienie replikacji między źródłem i replikami.<br>— Zapoznaj się z pełną listą ograniczeń replikacji MySQL w [dokumentacji programu MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) |

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak tworzyć repliki odczytu i zarządzać nimi przy użyciu Azure Portal](how-to-read-replicas-portal.md)
* Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure](how-to-read-replicas-cli.md)