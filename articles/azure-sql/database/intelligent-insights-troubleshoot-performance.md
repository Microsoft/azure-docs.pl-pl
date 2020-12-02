---
title: Rozwiązywanie problemów z wydajnością przy użyciu funkcji Intelligent Insights
description: Intelligent Insights pomaga rozwiązywać problemy związane z wydajnością Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: troubleshooting
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: c42db1445c939069f334d04ea26d54cdb843c336
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488837"
---
# <a name="troubleshoot-azure-sql-database-and-azure-sql-managed-instance-performance-issues-with-intelligent-insights"></a>Rozwiązywanie problemów z wydajnością Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL w programie Intelligent Insights
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ta strona zawiera informacje dotyczące Azure SQL Database i problemów z wydajnością wystąpienia zarządzanego usługi Azure SQL wykrytych w dzienniku [Intelligent Insights](intelligent-insights-overview.md) zasobów. Metryki i dzienniki zasobów można przesyłać strumieniowo do [Azure monitor dzienników](../../azure-monitor/insights/azure-sql.md), [platformy Azure Event Hubs](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs), [usługi Azure Storage](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)lub rozwiązania innej firmy w celu uzyskania niestandardowych funkcji alertów DevOps i raportowania.

> [!NOTE]
> Aby uzyskać szybki Przewodnik rozwiązywania problemów dotyczących wydajności przy użyciu Intelligent Insights, zapoznaj się ze schematem blokowym [przepływu rozwiązywania problemów](intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) w tym dokumencie.
>
> Usługa Intelligent Insights to funkcja w wersji zapoznawczej, która nie jest dostępna w następujących regionach: Europa Zachodnia, Europa Północna, zachodnie stany USA 1 i Wschodnie stany USA 1.

## <a name="detectable-database-performance-patterns"></a>Wykryte wzorce wydajności bazy danych

Intelligent Insights automatycznie wykrywa problemy z wydajnością w zależności od czasu oczekiwania na wykonanie zapytania, błędów lub przekroczeń limitu czasu. Intelligent Insights dane wyjściowe wykryły wzorce wydajności w dzienniku zasobów. Wykrywalne wzorce wydajności są podsumowane w poniższej tabeli.

| Wykrywalne wzorce wydajności | Azure SQL Database | Wystąpienie zarządzane Azure SQL |
| :------------------- | ------------------- | ------------------- |
| [Osiąganie limitów zasobów](intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Limity zasobów dotyczące użycia dostępnych zasobów (DTU), wątków roboczych bazy danych lub sesji logowania do bazy danych dostępnych w monitorowanej subskrypcji osiągnęły. Ma to wpływ na wydajność. | Użycie zasobów procesora CPU zbliża się do limitów zasobów. Ma to wpływ na wydajność bazy danych. |
| [Wzrost obciążenia](intelligent-insights-troubleshoot-performance.md#workload-increase) | Wykryto wzrost obciążenia lub ciągłe gromadzenie obciążeń w bazie danych. Ma to wpływ na wydajność. | Wykryto wzrost obciążenia. Ma to wpływ na wydajność bazy danych. |
| [Wykorzystanie pamięci](intelligent-insights-troubleshoot-performance.md#memory-pressure) | Pracownicy, którzy zażądali przydzielenia pamięci, muszą oczekiwać na alokacje pamięci przez statystycznie znaczny czas lub zwiększony akumulację pracowników, którzy zażądali przyznanych pamięci. Ma to wpływ na wydajność. | Pracownicy, którzy zażądali przydzielenia pamięci, oczekują na alokacje pamięci przez statystycznie znaczący czas. Ma to wpływ na wydajność bazy danych. |
| [Blokowanie](intelligent-insights-troubleshoot-performance.md#locking) | Wykryto nadmierne blokowanie bazy danych wpływające na wydajność. | Wykryto nadmierne blokowanie bazy danych wpływające na wydajność bazy danych. |
| [Zwiększono MAXDOP](intelligent-insights-troubleshoot-performance.md#increased-maxdop) | Wartość opcji maksymalny stopień równoległości (MAXDOP) została zmieniona na wydajność wykonywania zapytania. Ma to wpływ na wydajność. | Wartość opcji maksymalny stopień równoległości (MAXDOP) została zmieniona na wydajność wykonywania zapytania. Ma to wpływ na wydajność. |
| [Rywalizacja o PAGELATCH](intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Wiele wątków jednocześnie próbuje uzyskać dostęp do tych samych stron buforu danych znajdujących się w pamięci, co spowodowało zwiększenie czasu oczekiwania i spowodowanie rywalizacji o PAGELATCH. Ma to wpływ na wydajność. | Wiele wątków jednocześnie próbuje uzyskać dostęp do tych samych stron buforu danych znajdujących się w pamięci, co spowodowało zwiększenie czasu oczekiwania i spowodowanie rywalizacji o PAGELATCH. Ma to wpływ na wydajność bazy danych. |
| [Brakujący indeks](intelligent-insights-troubleshoot-performance.md#missing-index) | Wykryto brakujący indeks mający wpływ na wydajność. | Wykryto brakujący indeks mający wpływ na wydajność bazy danych. |
| [Nowe zapytanie](intelligent-insights-troubleshoot-performance.md#new-query) | Wykryto nowe zapytanie wpływające na ogólną wydajność. | Wykryto nowe zapytanie wpływające na ogólną wydajność bazy danych. |
| [Zwiększona Statystyka oczekiwania](intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Wykryto zwiększony czas oczekiwania na wydajność bazy danych. | Wykryto zwiększony czas oczekiwania bazy danych na wydajność bazy danych. |
| [Rywalizacja o bazę danych TempDB](intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Wiele wątków próbuje uzyskać dostęp do tego samego zasobu bazy danych TempDB powodującego wąskie gardło. Ma to wpływ na wydajność. | Wiele wątków próbuje uzyskać dostęp do tego samego zasobu bazy danych TempDB powodującego wąskie gardło. Ma to wpływ na wydajność bazy danych. |
| [Niedobory jednostek DTU puli elastycznej](intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Brak dostępnych jednostek eDTU w elastycznej puli ma wpływ na wydajność. | Niedostępne dla wystąpienia zarządzanego usługi Azure SQL, ponieważ używa on modelu rdzeń wirtualny. |
| [Regresja planu](intelligent-insights-troubleshoot-performance.md#plan-regression) | Wykryto nowy plan lub zmianę obciążenia istniejącego planu. Ma to wpływ na wydajność. | Wykryto nowy plan lub zmianę obciążenia istniejącego planu. Ma to wpływ na wydajność bazy danych. |
| [Zmiana wartości konfiguracji z zakresem bazy danych](intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Wykryto zmianę konfiguracji bazy danych wpływającą na wydajność bazy danych. | Wykryto zmianę konfiguracji bazy danych wpływającą na wydajność bazy danych. |
| [Powolne klienta](intelligent-insights-troubleshoot-performance.md#slow-client) | Klient z niską aplikacją nie może korzystać z bazy danych na tyle szybko. Ma to wpływ na wydajność. | Klient z niską aplikacją nie może korzystać z bazy danych na tyle szybko. Ma to wpływ na wydajność bazy danych. |
| [Obniżenie warstwy cenowej](intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Akcja obniżenia warstwy cenowej zmniejszyła dostępne zasoby. Ma to wpływ na wydajność. | Akcja obniżenia warstwy cenowej zmniejszyła dostępne zasoby. Ma to wpływ na wydajność bazy danych. |

> [!TIP]
> W celu zapewnienia ciągłej optymalizacji wydajności baz danych Włącz [Automatyczne dostrajanie](automatic-tuning-overview.md). Ta wbudowana funkcja analizy ciągle monitoruje bazę danych, automatycznie dostosowuje indeksy i stosuje poprawki planu wykonywania zapytania.
>

W poniższej sekcji opisano wykrywalne wzorce wydajności w bardziej szczegółowy sposób.

## <a name="reaching-resource-limits"></a>Osiąganie limitów zasobów

### <a name="what-is-happening"></a>Co się dzieje

Ten możliwy do wykrycia wzorzec wydajności łączy problemy z wydajnością, które są związane z osiągnięciem dostępnych limitów zasobów, limitów procesów roboczych i limitów sesji. Po wykryciu tego problemu z wydajnością pole Opis dziennika diagnostycznego wskazuje, czy problem z wydajnością dotyczy limitów zasobów, procesów roboczych lub sesji.

Zasoby na Azure SQL Database są zwykle określane jako zasoby [DTU](service-tiers-dtu.md) lub [rdzeń wirtualny](service-tiers-vcore.md) , a zasoby w wystąpieniu zarządzanym usługi Azure SQL są określane jako zasoby rdzeń wirtualny. Wzorzec osiągania limitów zasobów jest rozpoznawany w przypadku wykrycia obniżenia wydajności zapytań jest spowodowany osiągnięciem dowolnych mierzonych limitów zasobów.

Sesja ogranicza liczbę dostępnych współbieżnych logowań do bazy danych. Ten wzorzec wydajności jest rozpoznawany, gdy aplikacje połączone z bazami danych osiągną liczbę dostępnych współbieżnych logowań do bazy danych. Jeśli aplikacje próbują użyć więcej sesji niż jest dostępnych w bazie danych, wpłynie to na wydajność zapytań.

Osiągnięcie limitów procesów roboczych jest szczególnym przypadkiem osiągania limitów zasobów, ponieważ dostępni pracownicy nie są wliczane do użycia jednostek DTU lub rdzeń wirtualny. Przekroczenie limitów procesów roboczych w bazie danych może spowodować wydłużenie czasu oczekiwania określonych zasobów, co spowoduje spadek wydajności zapytania.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

W dzienniku diagnostyki są wyprowadzane wartości skrótów zapytań, które mają oddziaływać na wartości procentowe zużycia wydajności i zasobów. Tych informacji można użyć jako punktu wyjścia do optymalizowania obciążenia bazy danych. W szczególności można zoptymalizować zapytania, które wpływają na spadek wydajności przez dodanie indeksów. Można też zoptymalizować aplikacje o bardziej równomiernym rozkładzie obciążenia. Jeśli nie możesz ograniczyć obciążeń lub dokonać optymalizacji, rozważ zwiększenie warstwy cenowej subskrypcji bazy danych w celu zwiększenia ilości dostępnych zasobów.

W przypadku osiągnięcia limitów dostępnych sesji można zoptymalizować aplikacje, zmniejszając liczbę logowań wprowadzonych w bazie danych. Jeśli nie możesz zmniejszyć liczby logowań z aplikacji do bazy danych, rozważ zwiększenie warstwy cenowej subskrypcji bazy danych. Można też podzielić i przenieść bazę danych do wielu baz danych w celu zapewnienia bardziej zrównoważonej dystrybucji obciążeń.

Aby uzyskać więcej sugestii dotyczących rozwiązywania limitów sesji, zobacz [temat jak radzić sobie z limitami maksymalnej liczby logowań](/archive/blogs/latam/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins). Zobacz [Omówienie limitów zasobów na serwerze](resource-limits-logical-server.md) , aby uzyskać informacje na temat limitów na poziomach serwera i subskrypcji.

## <a name="workload-increase"></a>Wzrost obciążenia

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności służy do identyfikowania problemów spowodowanych wzrostem obciążenia lub, w bardziej poważnym formularzu, sterty obciążenia.

To wykrywanie odbywa się za pomocą kombinacji kilku metryk. Mierzona Metryka podstawowa polega na wykryciu wzrostu obciążenia w porównaniu z linią bazową wcześniejszego obciążenia. Druga forma wykrywania opiera się na pomiarach dużego wzrostu aktywnych wątków roboczych, które są wystarczająco duże, aby wpływać na wydajność zapytań.

W bardziej poważniejszej postaci obciążenie może stale zwiększać się z powodu niezdolności do obsługi obciążenia bazy danych. Wynikiem jest ciągły wzrost rozmiaru obciążenia, który jest stanem sterty obciążenia. Ze względu na ten warunek czas, w którym obciążenie oczekuje na wykonanie, rośnie. Ten stan reprezentuje jeden z najważniejszych problemów z wydajnością bazy danych. Ten problem został wykryty przez monitorowanie wzrostu liczby przerwanych wątków roboczych.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyczny wyprowadza liczbę zapytań, których wykonanie zostało zwiększone, i skrót zapytania zapytania o największym udziale do wzrostu obciążenia. Tych informacji można użyć jako punktu wyjścia do optymalizowania obciążenia. Zapytanie zidentyfikowane jako największe współautor wzrostu obciążenia jest szczególnie przydatne jako punkt początkowy.

Można rozważyć równomierne dystrybuowanie obciążeń do bazy danych. Rozważ optymalizację zapytania, które ma wpływ na wydajność przez dodanie indeksów. Możliwe jest również rozproszenie obciążenia między wieloma bazami danych. Jeśli te rozwiązania nie są możliwe, należy rozważyć zwiększenie warstwy cenowej subskrypcji bazy danych w celu zwiększenia ilości dostępnych zasobów.

## <a name="memory-pressure"></a>Wykorzystanie pamięci

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności wskazuje spadek wydajności bieżącej bazy danych spowodowany przez wykorzystanie pamięci lub w większym poważnym wypełnieniu stanu sterty pamięci w porównaniu z poprzednią podstawową linią bazową wydajności.

Wykorzystanie pamięci oznacza sytuację, w której istnieje duża liczba wątków roboczych żądających dotacji do pamięci. Wysoki wolumin powoduje wysoki poziom użycia pamięci, w którym baza danych nie może efektywnie przydzielić pamięci do wszystkich procesów roboczych, które go żądają. Jedną z najczęstszych przyczyn tego problemu jest związek z ilością pamięci dostępnej dla bazy danych z jednej strony. Z drugiej strony wzrost obciążenia powoduje wzrost liczby wątków roboczych i wykorzystanie pamięci.

Bardziej silna forma wykorzystania pamięci to warunek sterty pamięci. Ten stan wskazuje, że większa liczba wątków roboczych żąda przydzielenia pamięci, niż zapytania zwalniają pamięć. Ta liczba wątków roboczych, które żądają dotacji do pamięci, również może stale wzrastać (piling), ponieważ aparat bazy danych nie może przydzielić pamięci wystarczającej do spełnienia wymagań. Warunek sterty pamięci reprezentuje jeden z najważniejszych problemów z wydajnością bazy danych.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyczny wyprowadza szczegóły magazynu obiektów pamięci z pracownikiem (to jest wątek roboczy) oznaczony jako najwyższy powód dla dużego użycia pamięci i odpowiednich sygnatur czasowych. Tych informacji można użyć jako podstawy rozwiązywania problemów.

Można zoptymalizować lub usunąć zapytania związane z urzędnikami z największą ilością pamięci. Możesz również upewnić się, że nie są wyświetlane zapytania o dane, których nie planujesz używać. Dobrym sposobem jest zawsze używanie klauzuli WHERE w zapytaniach. Ponadto zalecamy utworzenie indeksów nieklastrowanych w celu wyszukiwania danych zamiast ich skanowania.

Możesz również zmniejszyć obciążenie, optymalizując lub rozpowszechniając je w wielu bazach danych. Można też rozesłać obciążenie między wieloma bazami danych. Jeśli te rozwiązania nie są możliwe, należy rozważyć zwiększenie warstwy cenowej bazy danych w celu zwiększenia ilości zasobów pamięci dostępnej dla bazy danych.

Aby uzyskać dodatkowe sugestie dotyczące rozwiązywania problemów, zobacz część [Grants Meditation: The tajemniczymi SQL Server Memory Consumer z wieloma nazwami](https://techcommunity.microsoft.com/t5/sql-server-support/memory-grants-meditation-the-mysterious-sql-server-memory/ba-p/333994).

## <a name="locking"></a>Blokowanie

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności wskazuje spadek wydajności bieżącej bazy danych, w którym wykryto nadmierne blokowanie bazy danych w porównaniu do ostatnich siedmiu dni.

W nowoczesnych RDBMS zablokowanie jest niezbędne do implementowania systemów wielowątkowych, w których wydajność jest maksymalizowana przez uruchamianie wielu jednoczesnych pracowników i równoległych transakcji bazy danych, jeśli jest to możliwe. Blokowanie w tym kontekście odnosi się do wbudowanego mechanizmu dostępu, w którym tylko pojedyncza transakcja może uzyskiwać dostęp wyłącznie do wierszy, stron, tabel i plików, które są wymagane i nie konkurują z inną transakcją dla zasobów. Gdy transakcja, w której są blokowane zasoby, jest wykonywana z tymi zasobami, zostaje wydana blokada tych zasobów, co umożliwi innym transakcjom dostęp do wymaganych zasobów. Aby uzyskać więcej informacji na temat blokowania, zobacz [blokada w aparacie bazy danych](/previous-versions/sql/sql-server-2008-r2/ms190615(v=sql.105)).

Jeśli transakcje wykonywane przez aparat SQL oczekują na długotrwały okres uzyskiwania dostępu do zasobów zablokowanych do użycia, ten czas oczekiwania spowoduje spowolnienie wydajności wykonywania obciążeń.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyczny generuje szczegóły, których można użyć jako podstawy do rozwiązywania problemów. Można analizować zgłoszone zapytania blokujące, czyli zapytania, które powodują spadek wydajności i je usunąć. W niektórych przypadkach może się okazać, że optymalizacja zakończyła się powodzeniem.

Najprostszym i najbezpieczniejszym sposobem na ograniczenie problemu jest utrzymywanie transakcji krótko i zmniejszenie rozmiaru blokady najbardziej kosztownych zapytań. Duża partia operacji można podzielić na mniejsze operacje. Dobrym rozwiązaniem jest zredukowanie poziomu blokady zapytań, co sprawia, że zapytanie jest możliwie wydajne. Zmniejsz duże skanowania, ponieważ zwiększają one szanse zakleszczenii i niekorzystnie wpływają na ogólną wydajność bazy danych. Dla zidentyfikowanych zapytań, które powodują blokowanie, można utworzyć nowe indeksy lub dodać kolumny do istniejącego indeksu, aby uniknąć skanowania tabeli.

Aby uzyskać więcej sugestii, zobacz [jak rozwiązać problemy z blokowaniem, które są spowodowane eskalacją blokady w SQL Server](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Zwiększono MAXDOP

### <a name="what-is-happening"></a>Co się dzieje

Ten wykrywalny wzorzec wydajności wskazuje stan, w którym wybrany plan wykonania zapytania został wykonany równolegle. Optymalizator zapytań może zwiększyć wydajność obciążeń, wykonując zapytania równolegle, aby przyspieszyć, tam gdzie to możliwe. W niektórych przypadkach procesy robocze równoległe przetwarzają zapytania poświęcają więcej czasu na synchronizację i scalanie wyników w porównaniu z wykonywaniem tego samego zapytania z mniejszą liczbą równoległych procesów roboczych lub nawet w porównaniu z pojedynczym wątkiem roboczym.

System ekspercki analizuje bieżącą wydajność bazy danych w porównaniu z okresem bazowym. Określa, czy poprzednio uruchomione zapytanie działa wolniej niż poprzednio, ponieważ plan wykonania zapytania jest bardziej równoległy niż powinien.

Opcja konfiguracji serwera MAXDOP służy do kontrolowania liczby rdzeni procesora CPU, które mogą być używane do równoległego wykonywania tego samego zapytania.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

W dziennikach diagnostycznych są generowane skróty zapytania dotyczące zapytań, dla których czas wykonania został zwiększony, ponieważ zostały one równoległe. Dziennik spowoduje również wyjście CXP czasu oczekiwania. Ten czas przedstawia czas, przez który pojedynczy wątek organizatora/Koordynator (wątek 0) czeka na zakończenie wszystkich innych wątków przed scaleniem wyników i przechodzeniem do przodu. Ponadto dziennik diagnostyczny wyprowadza czas oczekiwania, w którym zapytania o niskiej wydajności oczekują na całkowite wykonanie. Tych informacji można użyć jako podstawy rozwiązywania problemów.

Najpierw Zoptymalizuj złożone zapytania lub Uprość je. Dobrym sposobem jest rozbicie długotrwałych zadań wsadowych na mniejsze. Ponadto upewnij się, że utworzono indeksy do obsługi zapytań. Można również ręcznie wymusić maksymalny stopień równoległości dla zapytania, które zostało oflagowane jako niska. Aby skonfigurować tę operację przy użyciu języka T-SQL, zobacz [Konfigurowanie opcji konfiguracji serwera MAXDOP](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Ustawienie opcji konfiguracji serwera MAXDOP na zero (0) jako wartości domyślnej oznacza, że baza danych może używać wszystkich dostępnych rdzeni procesora CPU do zrównoleglanie wątków do wykonywania jednego zapytania. Ustawienie MAXDOP na jeden (1) oznacza, że tylko jeden rdzeń może być używany do wykonywania jednego zapytania. W praktyce oznacza to, że równoległość jest wyłączona. W zależności od wielkości liter, dostępne rdzenie do bazy danych i informacje dziennika diagnostycznego można dostosować opcję MAXDOP do liczby rdzeni używanych do równoległego wykonywania zapytań, które mogą rozwiązać problem w Twoim przypadku.

## <a name="pagelatch-contention"></a>Rywalizacja o PAGELATCH

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności wskazuje spadek wydajności bieżącego obciążenia bazy danych spowodowany rywalizacją o PAGELATCH w porównaniu z poprzednią, 7-dniową linią bazową obciążenia.

Zamki są prostymi mechanizmami synchronizacji używanymi do włączania wielowątkowości. Gwarantują one spójność struktur w pamięci, które obejmują indeksy, strony danych i inne wewnętrzne struktury.

Dostępnych jest wiele typów zamków. Dla uproszczenia, zamki buforów są używane do ochrony stron znajdujących się w pamięci w puli buforów. Zatrzaśnięcia we/wy służy do ochrony stron, które nie zostały jeszcze załadowane do puli buforów. Za każdym razem, gdy dane są zapisywane lub odczytywane ze strony w puli buforów, wątek roboczy musi najpierw uzyskać zatrzask dla strony. Za każdym razem, gdy wątek roboczy próbuje uzyskać dostęp do strony, która nie jest jeszcze dostępna w puli buforów w pamięci, żądanie we/wy jest nawiązywane w celu załadowania wymaganych informacji z magazynu. Ta sekwencja zdarzeń wskazuje bardziej silną postać obniżenia wydajności.

Rywalizacja na zatrzaśnięcia stron występuje, gdy wiele wątków jednocześnie próbuje uzyskać zatrzaśnięcia w tej samej strukturze w pamięci, co wprowadza zwiększony czas oczekiwania na wykonanie zapytania. W przypadku rywalizacji o PAGELATCH we/wy, gdy dane wymagają dostępu z magazynu, ten czas oczekiwania jest jeszcze większy. Może to znacznie wpłynąć na wydajność obciążeń. PAGELATCH Content to najpopularniejszy scenariusz wątków, które oczekują na siebie nawzajem i konkurują o zasoby w wielu systemach procesora.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki wyświetla szczegóły rywalizacji PAGELATCH. Tych informacji można użyć jako podstawy rozwiązywania problemów.

Ponieważ PAGELATCH jest mechanizmem kontroli wewnętrznego, automatycznie określa, kiedy należy ich używać. Decyzje dotyczące aplikacji, w tym projekt schematu, mogą mieć wpływ na zachowanie PAGELATCH ze względu na deterministyczne zachowanie zamków.

Jedną z metod obsługi rywalizacji zamka jest zastępowanie sekwencyjnego klucza indeksu z niesekwencyjnym kluczem, aby równomiernie rozłożyć wstawki przez zakres indeksów. Zazwyczaj wiodąca kolumna w indeksie dystrybuuje obciążenie proporcjonalnie do obciążenia. Inną metodą, którą należy wziąć pod uwagę, jest partycjonowanie tabeli. Tworzenie schematu partycjonowania skrótów z kolumną obliczaną w tabeli partycjonowanej jest typowym podejściem do łagodzenia nadmiernej rywalizacji zamków. W przypadku rywalizacji o PAGELATCH we/wy, wprowadzenie indeksów pomaga wyeliminować ten problem z wydajnością.

Aby uzyskać więcej informacji, zobacz [diagnozowanie i rozwiązywanie rywalizacji o zatrzaski na SQL Server](http://databaser.net/moniwiki/pds/PerformanceTuning/SQLServerLatchContention.pdf) (pobieranie plików PDF).

## <a name="missing-index"></a>Brakujący indeks

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności wskazuje spadek wydajności bieżącego obciążenia bazy danych w porównaniu z poprzednią 7-dniową linią bazową ze względu na brak indeksu.

Indeks jest używany do przyspieszenia wydajności zapytań. Zapewnia szybki dostęp do danych tabeli poprzez zmniejszenie liczby stron zestawu danych, które należy odwiedzać lub przeskanować.

Określone zapytania, które spowodowały spadek wydajności, są identyfikowane za pomocą tego wykrywania, dla którego Tworzenie indeksów byłoby korzystne dla wydajności.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

W dzienniku diagnostyki są wyprowadzane wartości skrótu zapytania dla zapytań, które zostały zidentyfikowane w celu wpływu na wydajność obciążeń. Można tworzyć indeksy dla tych zapytań. Można także zoptymalizować lub usunąć te zapytania, jeśli nie są one wymagane. Dobrym sposobem jest uniknięcie wykonywania zapytań dotyczących danych, które nie są używane.

> [!TIP]
> Czy wiesz, że wbudowana analiza może automatycznie zarządzać najlepszymi indeksami dla baz danych?
>
> W przypadku ciągłej optymalizacji wydajności zalecamy włączenie [dostrajania automatycznego](automatic-tuning-overview.md). Ta unikatowa Wbudowana funkcja analizy ciągle monitoruje bazę danych i automatycznie dostosowuje i tworzy indeksy dla baz danych.
>

## <a name="new-query"></a>Nowe zapytanie

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności wskazuje, że wykryto nowe zapytanie, które jest wykonywane nieprawidłowo i wpływając na wydajność obciążeń w porównaniu z siódmą linią bazową wydajności.

Pisanie dobrego zapytania czasami może być trudne. Aby uzyskać więcej informacji na temat pisania zapytań, zobacz [Pisanie zapytań SQL](/previous-versions/sql/sql-server-2005/express-administrator/bb264565(v=sql.90)). Aby zoptymalizować istniejącą wydajność zapytań, zobacz [dostrajanie zapytań](/previous-versions/sql/sql-server-2008-r2/ms176005(v=sql.105)).

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyczny zawiera informacje o maksymalnie dwóch nowych zapytaniach zużywających procesor, łącznie z ich skrótami zapytań. Ponieważ wykryte zapytanie ma wpływ na wydajność obciążeń, można zoptymalizować zapytanie. Dobrym sposobem jest pobranie tylko tych danych, których należy użyć. Zalecamy także używanie zapytań z klauzulą WHERE. Zalecamy również uproszczenie złożonych zapytań i podzielenie ich na mniejsze zapytania. Innym dobrym sposobem jest podzielenie dużych zapytań wsadowych na mniejsze zapytania wsadowe. Wprowadzenie indeksów dla nowych zapytań jest zwykle dobrym sposobem na ograniczenie tego problemu dotyczącego wydajności.

W Azure SQL Database Rozważ użycie [szczegółowe informacje o wydajności zapytań](query-performance-insight-use.md).

## <a name="increased-wait-statistic"></a>Zwiększona Statystyka oczekiwania

### <a name="what-is-happening"></a>Co się dzieje

Ten wykrywalny wzorzec wydajności wskazuje spadek wydajności obciążeń, w którym zidentyfikowano niezadowalające zapytania w porównaniu do ostatnich siedmiu dni.

W takim przypadku system nie może sklasyfikować niewłaściwie wykonywanych zapytań w ramach żadnych innych standardowych, wykrywalnych kategorii wydajności, ale wykrył statystykę oczekiwania dla regresji. W związku z tym traktuje je jako zapytania z *zwiększoną statystyką oczekiwania*, gdzie jest również uwidoczniona Statystyka oczekiwania dla regresji.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyczny zawiera informacje na temat zwiększonych szczegółów czasu oczekiwania i skrótów zapytań dotyczących zaatakowanych zapytań.

Ponieważ system nie mógł pomyślnie zidentyfikować głównej przyczyny nieprawidłowych zapytań, informacje diagnostyczne są dobrym punktem wyjścia do ręcznego rozwiązywania problemów. Możesz zoptymalizować wydajność tych zapytań. Dobrym sposobem jest pobranie tylko tych danych, które są potrzebne, i uproszczenie i rozbicie złożonych zapytań na mniejsze.

Aby uzyskać więcej informacji na temat optymalizowania wydajności zapytań, zobacz [dostrajanie zapytań](/previous-versions/sql/sql-server-2008-r2/ms176005(v=sql.105)).

## <a name="tempdb-contention"></a>Rywalizacja o bazę danych TempDB

### <a name="what-is-happening"></a>Co się dzieje

Ten wykryty wzorzec wydajności wskazuje warunek wydajności bazy danych, w którym istnieje wąskie gardło wątków próbujących uzyskać dostęp do zasobów tempDB. (Ten warunek nie jest powiązany we/wy). Typowym scenariuszem tego problemu z wydajnością jest setki współbieżnych zapytań, które tworzą, wykorzystują i usuwają małe tabele tempDB. System wykrył, że liczba współbieżnych zapytań używających tych samych tabel bazy danych tempDB zwiększyła się z wystarczającą istotnością statystyczną, aby wpływać na wydajność bazy danych porównaną z poprzednią bazową.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Szczegóły rywalizacji o zawartość bazy danych w dzienniku diagnostyki. Możesz użyć informacji jako punktu wyjścia do rozwiązywania problemów. Istnieją dwie rzeczy, które można wykonać, aby uniknąć tego rodzaju rywalizacji i zwiększyć przepływność całego obciążenia: można zatrzymać korzystanie z tabel tymczasowych. Można również użyć tabel zoptymalizowanych pod kątem pamięci.

Aby uzyskać więcej informacji, zobacz [wprowadzenie do tabel zoptymalizowanych pod kątem pamięci](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables).

## <a name="elastic-pool-dtu-shortage"></a>Niedobory jednostek DTU puli elastycznej

### <a name="what-is-happening"></a>Co się dzieje

Ten wykrywalny wzorzec wydajności wskazuje spadek wydajności bieżącej bazy danych w porównaniu do ostatnich siedmiu dni. Jest to spowodowane brakiem dostępnego DTU w elastycznej puli subskrypcji.

[Zasoby puli elastycznej platformy Azure](elastic-pool-overview.md) są używane jako pula dostępnych zasobów współużytkowanych przez wiele baz danych w celu skalowania. Gdy dostępne zasoby eDTU w puli elastycznej nie są wystarczająco duże, aby zapewnić obsługę wszystkich baz danych w puli, w systemie wykryto problem z wydajnością niedoborów jednostek DTU puli elastycznej.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dzienniki diagnostyczne zawierają informacje wyjściowe w puli elastycznej, zawiera listę najważniejszych baz danych korzystających z jednostek DTU i zawiera procent jednostek DTU puli używanych przez bazę danych najwyższego poziomu.

Ponieważ ten warunek wydajności jest powiązany z wieloma bazami danych za pomocą tej samej puli jednostek eDTU w puli elastycznej, kroki rozwiązywania problemów koncentrują się na najważniejszych bazach danych używających jednostek DTU. Można zmniejszyć obciążenie najważniejszych baz danych, co obejmuje optymalizację najważniejszych zapytań do tych baz danych. Można również upewnić się, że nie są wyświetlane zapytania dotyczące danych, które nie są używane. Innym podejściem jest Optymalizowanie aplikacji przy użyciu najważniejszych baz danych, które korzystają z używania jednostek DTU, i rozproszenie obciążenia między wieloma bazami danych.

Jeśli nie jest możliwe zmniejszenie i optymalizacja bieżącego obciążenia dla najważniejszych baz danych korzystających z jednostek DTU, należy rozważyć zwiększenie warstwy cenowej puli elastycznej. Zwiększa to skutki wzrostu dostępnych DTU w puli elastycznej.

## <a name="plan-regression"></a>Regresja planu

### <a name="what-is-happening"></a>Co się dzieje

Ten wykryty wzorzec wydajności oznacza warunek, w którym baza danych wykorzystuje nieoptymalny plan wykonywania zapytań. Nieoptymalny plan zwykle powoduje zwiększenie wykonywania zapytań, co prowadzi do dłuższego czasu oczekiwania na bieżące i inne zapytania.

Aparat bazy danych określa plan wykonania zapytania z najniższym kosztem wykonywania zapytania. W miarę zmieniania typu zapytań i obciążeń czasami istniejące plany nie są już wydajne lub prawdopodobnie aparat bazy danych nie był w dobrej ocenie. W celu dokonania korekty plany wykonywania zapytań mogą być wymuszane ręcznie.

Ten wykrywalny wzorzec wydajności łączy trzy różne przypadki regresji planu: Nowa regresja planu, stara regresja planu i istniejące plany zmieniły obciążenie. Określony typ regresji planu, która wystąpiła, jest udostępniany we właściwości *szczegóły* w dzienniku diagnostyki.

Nowy warunek regresji planu odnosi się do stanu, w którym aparat bazy danych uruchamia wykonywanie nowego planu wykonywania zapytań, który nie jest tak wydajny jak stary plan. Stary warunek regresji planu odnosi się do stanu, gdy aparat bazy danych przełączy się przy użyciu nowego, wydajniejszego planu do starego planu, który nie jest tak wydajny jak nowy plan. W istniejących planach zmiana regresji obciążenia odnosi się do stanu, w którym stare i nowe plany są stale alternatywne, z saldem, które zwiększają się w kierunku niezadowalającego planu.

Aby uzyskać więcej informacji na temat regresji planu, zobacz [co to jest regresja planu w SQL Server?](/archive/blogs/sqlserverstorageengine/what-is-plan-regression-in-sql-server).

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyczny wyprowadza wartości skrótów zapytań, dobry identyfikator planu, nieprawidłowy identyfikator planu i identyfikatory zapytań. Tych informacji można użyć jako podstawy rozwiązywania problemów.

Można analizować, który plan jest lepiej wykonywany dla określonych zapytań, które można zidentyfikować za pomocą podanych skrótów zapytań. Po ustaleniu, który plan działa lepiej dla zapytań, można go wymusić ręcznie.

Aby uzyskać więcej informacji, zobacz [Informacje o tym, jak SQL Server uniemożliwiają regresje planu](/archive/blogs/sqlserverstorageengine/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions).

> [!TIP]
> Czy wiesz, że Wbudowana funkcja analizy może automatycznie zarządzać najlepszymi planami wykonywania zapytań dla baz danych?
>
> W przypadku ciągłej optymalizacji wydajności zalecamy włączenie [dostrajania automatycznego](automatic-tuning-overview.md). Ta wbudowana funkcja analizy ciągle monitoruje bazę danych i automatycznie dostosowuje i tworzy najlepsze, wydajne plany wykonywania zapytań dla baz danych.

## <a name="database-scoped-configuration-value-change"></a>Zmiana wartości konfiguracji z zakresem bazy danych

### <a name="what-is-happening"></a>Co się dzieje

Ten wykrywalny wzorzec wydajności wskazuje stan, w którym zmiana w konfiguracji z zakresem bazy danych powoduje, że regresja wydajności wykryto w porównaniu z siódmym 7-dniowym zachowaniem obciążenia bazy danych. Ten wzorzec oznacza, że Ostatnia zmiana w konfiguracji z zakresem bazy danych nie ma wpływu na wydajność bazy danych.

Zmiany konfiguracji w zakresie bazy danych można ustawić dla każdej pojedynczej bazy danych. Ta konfiguracja jest używana na podstawie wielkości liter w celu zoptymalizowania indywidualnej wydajności bazy danych. Dla każdej pojedynczej bazy danych można skonfigurować następujące opcje: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES i wyczyść PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyczny wyprowadza zmiany konfiguracji z zakresem bazy danych, które zostały ostatnio wykonane, co spowodowało spadek wydajności w porównaniu z poprzednim 7-dniowym zachowaniem obciążenia. Zmiany konfiguracji można przywrócić do poprzednich wartości. Możesz również dostroić wartość przez wartość do momentu osiągnięcia żądanego poziomu wydajności. Możesz kopiować wartości konfiguracyjne zakresu bazy danych z podobnej bazy danych o zadowalającą wydajności. Jeśli nie możesz rozwiązać problemu z wydajnością, Przywróć wartości domyślne i spróbuj precyzyjnie dostosować rozpoczęcie od tej linii bazowej.

Aby uzyskać więcej informacji na temat optymalizacji konfiguracji z zakresem bazy danych i składni języka T-SQL podczas zmiany konfiguracji, zobacz [ALTER DATABASE-scoped Configuration (Transact-SQL)](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).

## <a name="slow-client"></a>Powolne klienta

### <a name="what-is-happening"></a>Co się dzieje

Ten wykrywalny wzorzec wydajności wskazuje stan, w którym klient korzystający z bazy danych nie może zużywać danych wyjściowych z usługi, tak szybko, jak baza danych wysyła wyniki. Ponieważ baza danych nie przechowuje wyników wykonanych zapytań w buforze, spowalnia i czeka, aż klient zużywa przesłane wyniki zapytania przed kontynuowaniem. Ten stan może być również związany z siecią, która nie wystarcza wystarczająco szybko, aby można było przesyłać dane wyjściowe z bazy danych do klienta korzystającego z programu.

Ten warunek jest generowany tylko w przypadku wykrycia regresji wydajności w porównaniu z wcześniejszym 7-dniowym zachowaniem obciążenia bazy danych. Ten problem z wydajnością jest wykrywany tylko wtedy, gdy w porównaniu z poprzednim zachowaniem wydajności wystąpi statystyczny znaczący spadek wydajności.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Ten wykrywalny wzorzec wydajności wskazuje warunek po stronie klienta. W przypadku aplikacji po stronie klienta lub sieci po stronie klienta jest wymagane rozwiązywanie problemów. W dzienniku diagnostycznym są wyprowadzane wartości skrótów zapytań i czasy oczekiwania, które pozornie oczekują, aby klient korzystał z nich w ciągu ostatnich dwóch godzin. Tych informacji można użyć jako podstawy rozwiązywania problemów.

Możesz zoptymalizować wydajność aplikacji w celu użycia tych zapytań. Można również rozważyć potencjalne problemy z opóźnieniami w sieci. Ze względu na to, że problem spadek wydajności został spowodowany zmianą w ciągu ostatnich siedmiu dni, można sprawdzić, czy ostatnie zmiany stanu aplikacji lub sieci spowodowały to zdarzenie regresji wydajności.

## <a name="pricing-tier-downgrade"></a>Obniżenie warstwy cenowej

### <a name="what-is-happening"></a>Co się dzieje

Ten wykrywalny wzorzec wydajności wskazuje stan, w którym Obniżono poziom warstwy cenowej subskrypcji bazy danych. Ze względu na zmniejszenie ilości zasobów (DTU) dostępnych dla bazy danych System wykrył spadek bieżącej wydajności bazy danych w porównaniu z poprzednią 7-dniową linią bazową.

Ponadto może istnieć warunek, w którym warstwa cenowa subskrypcji bazy danych została obniżona, a następnie uaktualniona do wyższej warstwy w krótkim czasie. Wykrywanie tego tymczasowego obniżenia wydajności jest podano w sekcji szczegółów dziennika diagnostyki jako obniżenie warstwy cenowej i uaktualnienie.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

W przypadku zredukowania warstwy cenowej i w związku z tym DTU jest dostępna, a wydajność nie jest wymagana. W przypadku zredukowania warstwy cenowej, gdy wydajność bazy danych jest niezadowalająca, Zmniejsz obciążenie bazy danych lub Rozważ zwiększenie warstwy cenowej na wyższy poziom.

## <a name="recommended-troubleshooting-flow"></a>Zalecany przepływ rozwiązywania problemów

 Postępuj zgodnie ze schematem blokowym, aby uzyskać zalecane podejście do rozwiązywania problemów z wydajnością przy użyciu Intelligent Insights.

Uzyskaj dostęp do Intelligent Insights za pośrednictwem Azure Portal, przechodząc do Azure SQL Analytics. Spróbuj zlokalizować alert wydajności dla ruchu przychodzącego, a następnie wybierz go. Określ, co dzieje się na stronie wykrywania. Obserwuj podaną analizę głównej przyczyny problemu, tekstu zapytania, trendów czasu zapytania i ewolucji incydentu. Podjęto próbę rozwiązania problemu, korzystając z zalecenia Intelligent Insights w celu ograniczenia problemu z wydajnością.

[![Wykres przepływu rozwiązywania problemów](./media/intelligent-insights-troubleshoot-performance/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Wybierz schemat blokowy, aby pobrać wersję pliku PDF.

Intelligent Insights zwykle potrzebuje jednej godziny, aby przeprowadzić analizę głównej przyczyny problemu z wydajnością. Jeśli nie możesz znaleźć problemu w Intelligent Insights i ma on krytyczne znaczenie, użyj magazynu zapytań, aby ręcznie zidentyfikować główną przyczynę problemu z wydajnością. (Zwykle te problemy są krótsze niż godzina starego). Aby uzyskać więcej informacji, zobacz [monitorowanie wydajności przy użyciu magazynu zapytań](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Następne kroki

- Poznaj [Intelligent Insights](intelligent-insights-overview.md) pojęcia.
- Użyj [dziennika diagnostyki wydajności Intelligent Insights](intelligent-insights-use-diagnostics-log.md).
- Monitoruj przy użyciu [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Dowiedz się [, jak zbierać dane dzienników z zasobów platformy Azure i korzystać z nich](../../azure-monitor/platform/platform-logs-overview.md).