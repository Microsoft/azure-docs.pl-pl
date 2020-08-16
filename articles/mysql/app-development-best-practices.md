---
title: Najlepsze praktyki dotyczące projektowania aplikacji — Azure Database for MySQL
description: Poznaj najlepsze rozwiązania podczas kompilowania aplikacji za pomocą Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 36f9cfa2369032351f6ed2948fc0c98c1648bcb6
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2020
ms.locfileid: "88259255"
---
# <a name="best-practices-for-building-applications-with-azure-database-for-mysql"></a>Najlepsze rozwiązania dotyczące kompilowania aplikacji za pomocą Azure Database for MySQL 

Poniżej przedstawiono niektóre najlepsze rozwiązania ułatwiające tworzenie aplikacji gotowych do użycia w chmurze przy użyciu Azure Database for MySQL, które mogą skrócić czas projektowania aplikacji. 

## <a name="application-and-database-resource-configuration"></a>Konfiguracja zasobów aplikacji i bazy danych

### <a name="application-and-database-in-the-same-region"></a>Aplikacja i baza danych w tym samym regionie
Upewnij się **, że wszystkie zależności są w tym samym regionie**  podczas wdrażania aplikacji na platformie Azure. Rozmieszczenie wystąpień między regionami lub strefami dostępności powoduje utworzenie opóźnienia sieci, co może mieć wpływ na ogólną wydajność aplikacji. 

### <a name="keep-your-mysql-server-secure"></a>Zachowaj bezpieczeństwo serwera MySQL
Serwer MySQL powinien być skonfigurowany jako [bezpieczny](https://docs.microsoft.com/azure/mysql/concepts-security) i niedostępny publicznie. Aby zabezpieczyć serwer, użyj jednej z następujących opcji: 
- [Reguły zapory](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) lub
- [Sieci wirtualne](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet) lub 
- [Link prywatny](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)

W celu zapewnienia bezpieczeństwa należy zawsze łączyć się z serwerem MySQL za pośrednictwem **protokołu SSL** i konfigurować serwer MySQL oraz aplikację do korzystania z **protokołu TLS 1.2**. Zobacz [jak skonfigurować protokół SSL/TLS](https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security). 

### <a name="tune-your-server-parameters"></a>Dostrajanie parametrów serwera
W przypadku obciążeń z dużą ilością odczytu te parametry "tmp_table_size i max_heap_table_size" mogą pomóc zoptymalizować wydajność. Aby obliczyć wartości wymagane dla tmp_table_size i max_heap_table_size, należy przyjrzeć się łącznym wartościom pamięci dla poszczególnych połączeń i pamięci podstawowej. Suma parametrów pamięci dla poszczególnych połączeń, z wyłączeniem tmp_table_size, w połączeniu z kontami pamięci podstawowej dla całkowitej ilości pamięci serwera.

Aby obliczyć największy możliwy rozmiar tmp_table_size i max_heap_table_size, należy użyć następującej formuły:

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> Wartość całkowita pamięć wskazuje łączną ilość pamięci zainicjowanej przez serwer w ramach rdzeni wirtualnych.  Na przykład na serwerze Ogólnego przeznaczenia 2 rdzeń wirtualny Azure Database for MySQL, Łączna ilość pamięci wynosi 5 GB * 2.  Więcej informacji o pamięci dla każdej warstwy można znaleźć w dokumentacji [warstwy cenowej](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) .
> Pamięć podstawowa wskazuje zmienne pamięci, takie jak query_cache_size i innodb_buffer_pool_size, które będą inicjowane i przydzielane przez program MySQL podczas uruchamiania serwera.  Na pamięć połączenia, np. sort_buffer_size i join_buffer_size, jest pamięć, która jest przypisana tylko wtedy, gdy wymaga tego zapytanie.

### <a name="create-a-non-admin-user"></a>Tworzenie użytkownika niebędącego administratorem 
[Utwórz użytkowników niebędących administratorami](https://docs.microsoft.com/azure/mysql/howto-create-users) dla każdej z baz danych. Zazwyczaj nazwy użytkowników są identyfikowane jako nazwy baz danych.

### <a name="resetting-your-password"></a>Resetowanie hasła
Możesz [zresetować hasło](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password) dla serwera MySQL przy użyciu Azure Portal. 

Zresetowanie hasła serwera dla produkcyjnej bazy danych może spowodować wyświetlenie aplikacji. Jest dobrym wzorcem do resetowania hasła dla wszystkich obciążeń produkcyjnych poza godzinami szczytu, aby zminimalizować wpływ na użytkowników końcowych aplikacji.

## <a name="performance-and-resiliency"></a>Wydajność i odporność 
Oto kilka narzędzi i wzorców, których można użyć w celu ułatwienia debugowania problemów z wydajnością w aplikacji.

### <a name="enable-slow-query-logs-identify-performance-issues"></a>Włącz dzienniki wolnych zapytań, aby identyfikować problemy z wydajnością
Można włączać [wolne dzienniki zapytań](https://docs.microsoft.com/azure/mysql/concepts-server-logs) i [dzienniki inspekcji](https://docs.microsoft.com/azure/mysql/concepts-audit-logs) na serwerze. Analizowanie wolnych dzienników zapytań może pomóc identyfikować wąskie gardła wydajności w celu rozwiązywania problemów. 

Dzienniki inspekcji są również dostępne za pomocą dzienników diagnostycznych platformy Azure w Azure Monitor dziennikach, Event Hubs i konta magazynu. Zobacz [, jak rozwiązywać problemy z wydajnością zapytań](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).

### <a name="use-connection-pooling"></a>Korzystanie z puli połączeń
Zarządzanie połączeniami z bazą danych może mieć znaczny wpływ na wydajność aplikacji jako całości. Aby zoptymalizować wydajność, należy zmniejszyć liczbę połączeń i czas na nawiązywanie połączeń w kluczowych ścieżkach kodu.  Użyj [puli połączeń](https://docs.microsoft.com/azure/mysql/concepts-connectivity#access-databases-by-using-connection-pooling-recommended) , aby nawiązać połączenie z usługą Azure Database for MySQL w celu zwiększenia odporności i wydajności. 

[ProxySQL](https://proxysql.com/), który jest pulę połączenia, może być efektywnie używany do zarządzania połączeniami. Użycie połączenia pulę może zmniejszyć czas bezczynności i ponownie wykorzystać istniejące połączenia, aby uniknąć problemów. Aby dowiedzieć się więcej, zobacz artykuł [jak skonfigurować ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) . 

### <a name="retry-logic-to-handle-transient-errors"></a>Logika ponawiania w celu obsługi błędów przejściowych
W aplikacji mogą wystąpić [Błędy przejściowe](https://docs.microsoft.com/azure/mysql/concepts-connectivity#handling-transient-errors) , w przypadku których połączenia z bazą danych są usuwane lub tracone sporadycznie. W takich sytuacjach serwer działa po upływie jednej do dwóch ponownych prób w ciągu 5-10 sekund. 

Dobrym wzorcem, które należy wykonać w przypadku ponowienia próby, jest odczekanie 5 sekund przed pierwszym ponowieniem próby, a następnie wykonanie każdego ponowienia próby przez zwiększenie czasu oczekiwania na 60 sekund. Ogranicz maksymalną liczbę ponownych prób, w których dana aplikacja uważa, że operacja nie powiodła się, aby można było dalej zbadać. Zobacz [, jak rozwiązywać problemy z błędami połączenia](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-common-connection-issues) , aby dowiedzieć się więcej. 

### <a name="enable-read-replication-to-mitigate-failovers"></a>Włącz replikację odczytu w celu ograniczenia trybu failover
Możesz użyć [replikacji danych w](https://docs.microsoft.com/azure/mysql/howto-data-in-replication) scenariuszach trybu failover. W przypadku korzystania z replik odczytu nie są wykonywane żadne automatyczne przejścia w tryb failover między serwerami Master i Replica. Zauważysz zwłokę między wzorcem a repliką, ponieważ replikacja jest asynchroniczna. Opóźnienie sieci może mieć wpływ na wiele czynników, takich jak zmniejszanie obciążenia uruchomionego na serwerze głównym oraz opóźnienia między centrami danych. W większości przypadków zwłoki repliki od kilku sekund do kilku minut.

## <a name="database-deployment"></a>Wdrażanie bazy danych 

### <a name="configure-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>Zadanie konfiguracji usługi Azure Database for MySQL w potoku wdrażania CI/CD
Czasami trzeba wdrożyć zmiany w bazie danych. W takich przypadkach można utworzyć opcję Użyj ciągłej integracji (CI) i ciągłego dostarczania (CD) za pomocą [potoków platformy Azure](https://azure.microsoft.com/services/devops/pipelines/) , a następnie użyć zadania dla [serwera MySQL](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops) do zaktualizowania bazy danych, uruchamiając skrypt niestandardowy w bazie danych.

### <a name="manual-database-deployment"></a>Ręczne wdrażanie bazy danych 
Podczas ręcznego wdrażania bazy danych poniżej przedstawiono dobry wzorzec, który pozwala na zminimalizowanie przestojów lub zmniejszenie ryzyka wdrożenia zakończonego niepowodzeniem: 

1. Tworzenie kopii produkcyjnej bazy danych w nowej bazie danych przy użyciu programu [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) lub [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html) 
2. Aktualizowanie nowej bazy danych przy użyciu nowych zmian schematu lub aktualizacji wymaganych dla bazy danych. 
3. Umieść produkcyjną bazę danych w stanie tylko do odczytu. Nie należy mieć operacji zapisu w produkcyjnej bazie danych, dopóki wdrożenie nie zostanie ukończone. 
4. Przetestuj swoją aplikację przy użyciu nowo zaktualizowanej bazy danych z kroku 1.
5. Wdróż zmiany w aplikacji i upewnij się, że aplikacja korzysta teraz z nowej bazy danych, która ma najnowsze aktualizacje. 
6. Zachowaj starą produkcyjną bazę danych, aby można było wycofać zmiany. Następnie można oszacować, aby usunąć starą produkcyjną bazę danych lub wyeksportować ją do usługi Azure Storage w razie potrzeby. 

>[!NOTE]
>  - Jeśli aplikacja jest taka sama jak aplikacja handlu elektronicznego, w której może nie być możliwe umieszczenie jej w stanie tylko do odczytu, należy wdrożyć zmiany bezpośrednio w produkcyjnej bazie danych po utworzeniu kopii zapasowej.  Zmiany te powinny być wykonywane w godzinach poza godzinami pracy z niskim ruchem do aplikacji, aby minimze wpływ, w jaki niektórzy użytkownicy mogą napotkać Nieudane żądania. 
>  - Upewnij się, że kod aplikacji obsługuje również wszelkie Nieudane żądania.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>Użyj natywnych metryk programu MySQL, aby sprawdzić, czy obciążenie przekracza tymczasowe rozmiary tabeli w pamięci
Dzięki obciążeniu z dużym obciążeniem zapytania wykonywane na serwerze MySQL mogą przekroczyć rozmiar tabeli tymczasowej w pamięci. Może to spowodować, że serwer przełączy się w celu zapisania tabel tymczasowych na dysku, co ma wpływ na wydajność aplikacji. Aby określić, czy serwer zapisuje na dysku w wyniku przekroczenia tymczasowego rozmiaru tabeli, zapoznaj się z następującymi metrykami:

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
Metryka created_tmp_disk_tables wskazuje, ile tabel zostało utworzonych na dysku, podczas gdy Metryka created_tmp_table informuje o liczbie tabel tymczasowych, które mają być tworzone w pamięci na podstawie obciążenia. Aby określić, czy uruchomienie określonego zapytania będzie używać tabel tymczasowych, należy uruchomić polecenie Wyjaśnij w zapytaniu. Szczegóły w kolumnie "ekstra" wskazują na użycie tymczasowego, jeśli zapytanie zostanie uruchomione przy użyciu tabel tymczasowych.

Aby obliczyć procent obciążeń związanych z zapytaniami, które są rozlewane na dyski, użyj wartości metryk w poniższej formule:

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

W idealnym przypadku ta wartość procentowa powinna być mniejsza o 25%. Jeśli zobaczysz, że wartość procentowa jest równa 25%, zalecamy modyfikowanie dwóch parametrów serwera, tmp_table_size i max_heap_table_si


## <a name="database-schema-and-queries"></a>Schemat bazy danych i zapytania

Poniżej przedstawiono kilka porad i wskazówek, które należy wziąć pod uwagę podczas budowania schematu bazy danych i zapytań.

### <a name="always-use-the-right-datatype-for-your--table-columns"></a>Zawsze używaj odpowiedniego elementu DataType dla kolumn tabeli
Korzystanie z prawidłowych typów danych w oparciu o typ przechowywanych w pamięci masowej może zoptymalizować magazyn i zmniejszyć liczbę błędów, które mogą wystąpić z powodu nieprawidłowych typów.

### <a name="use-indexes"></a>Używanie indeksów
Aby uniknąć powolnych zapytań, można użyć indeksów. Indeksy mogą pomóc w szybkim wyszukiwaniu wierszy z określonymi kolumnami. Zobacz [jak używać indeksów w programie MySQL](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html).

### <a name="explain-your-select-queries"></a>WYJAŚNIj wybrane zapytania
Użyj [Wyjaśnij](https://dev.mysql.com/doc/refman/8.0/en/explain.html) , aby uzyskać szczegółowe informacje na temat tego, co robi MySQL, aby wykonać zapytanie. Może to pomóc wykryć wąskie gardła lub problemy z zapytaniem. Zapoznaj się z tematem [jak używać Wyjaśnij, aby uzyskać profilowanie wydajności zapytań](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).


