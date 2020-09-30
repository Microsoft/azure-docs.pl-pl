---
title: Najlepsze praktyki dotyczące projektowania aplikacji — Azure Database for MySQL
description: Poznaj najlepsze rozwiązania dotyczące tworzenia aplikacji przy użyciu Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: dc9764ce68d54418578c293833c1fd38080ba0ef
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91538912"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-mysql"></a>Najlepsze rozwiązania dotyczące kompilowania aplikacji za pomocą Azure Database for MySQL 

Poniżej przedstawiono niektóre najlepsze rozwiązania ułatwiające tworzenie aplikacji gotowych do użycia w chmurze za pomocą Azure Database for MySQL. Te najlepsze rozwiązania mogą skrócić czas projektowania aplikacji. 

## <a name="configuration-of-application-and-database-resources"></a>Konfiguracja zasobów aplikacji i bazy danych

### <a name="keep-the-application-and-database-in-the-same-region"></a>Utrzymywanie aplikacji i bazy danych w tym samym regionie
Upewnij się, że wszystkie zależności są w tym samym regionie podczas wdrażania aplikacji na platformie Azure. Rozmieszczenie wystąpień między regionami lub strefami dostępności powoduje utworzenie opóźnienia sieci, co może mieć wpływ na ogólną wydajność aplikacji. 

### <a name="keep-your-mysql-server-secure"></a>Zachowaj bezpieczeństwo serwera MySQL
Skonfiguruj serwer MySQL jako [bezpieczny](https://docs.microsoft.com/azure/mysql/concepts-security) i niedostępny publicznie. Aby zabezpieczyć serwer, użyj jednej z następujących opcji: 
- [Reguły zapory](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules)
- [Sieci wirtualne](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet) 
- [Link prywatny platformy Azure](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)

W celu zapewnienia bezpieczeństwa należy zawsze łączyć się z serwerem MySQL za pośrednictwem protokołu SSL i konfigurować serwer MySQL oraz aplikację do korzystania z protokołu TLS 1,2. Zobacz [jak skonfigurować protokół SSL/TLS](https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security). 

### <a name="tune-your-server-parameters"></a>Dostrajanie parametrów serwera
W przypadku obciążeń intensywnie korzystających z odczytu dostraja parametry serwera `tmp_table_size` i `max_heap_table_size` może pomóc zoptymalizować wydajność. Aby obliczyć wartości wymagane dla tych zmiennych, należy przyjrzeć się łącznym wartościom pamięci dla poszczególnych połączeń i pamięci podstawowej. Suma parametrów pamięci dla poszczególnych połączeń, z wyłączeniem `tmp_table_size` , w połączeniu z kontami pamięci podstawowej dla całkowitej ilości pamięci serwera.

Aby obliczyć największy możliwy rozmiar `tmp_table_size` i, należy `max_heap_table_size` użyć następującej formuły:

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> Wartość całkowita pamięć wskazuje łączną ilość pamięci, jaką serwer ma w ramach aprowizacji rdzeni wirtualnych.  Na przykład, w Ogólnego przeznaczenia dwa rdzeń wirtualny Azure Database for MySQL Server, Łączna ilość pamięci wynosi 5 GB * 2. Więcej informacji o pamięci dla każdej warstwy można znaleźć w dokumentacji [warstwy cenowej](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) .
>
> Pamięć podstawowa wskazuje zmienne pamięci, takie jak `query_cache_size` i `innodb_buffer_pool_size` , które będą inicjowane i przydzielane przez program MySQL podczas uruchamiania serwera. Pamięć dla każdego połączenia, na przykład `sort_buffer_size` i `join_buffer_size` , jest pamięcią, która jest przypisana tylko wtedy, gdy wymaga tego zapytanie.

### <a name="create-non-admin-users"></a>Tworzenie użytkowników niebędących administratorami 
[Utwórz użytkowników niebędących administratorami](https://docs.microsoft.com/azure/mysql/howto-create-users) dla każdej bazy danych. Zazwyczaj nazwy użytkowników są identyfikowane jako nazwy baz danych.

### <a name="reset-your-password"></a>Resetowanie hasła
Możesz [zresetować hasło](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password) dla serwera MySQL przy użyciu Azure Portal. 

Zresetowanie hasła serwera dla produkcyjnej bazy danych może spowodować wyświetlenie aplikacji. Dobrym sposobem jest zresetowanie hasła dla wszelkich obciążeń produkcyjnych poza godzinami szczytu w celu zminimalizowania wpływu na użytkowników aplikacji.

## <a name="performance-and-resiliency"></a>Wydajność i odporność 
Poniżej przedstawiono kilka narzędzi i praktyk, których można użyć w celu ułatwienia debugowania problemów z wydajnością aplikacji.

### <a name="enable-slow-query-logs-to-identify-performance-issues"></a>Włącz dzienniki wolnych zapytań, aby identyfikować problemy z wydajnością
Można włączać [wolne dzienniki zapytań](https://docs.microsoft.com/azure/mysql/concepts-server-logs) i [dzienniki inspekcji](https://docs.microsoft.com/azure/mysql/concepts-audit-logs) na serwerze. Analizowanie wolnych dzienników zapytań może pomóc identyfikować wąskie gardła wydajności w celu rozwiązywania problemów. 

Dzienniki inspekcji są również dostępne za pomocą dzienników Diagnostyka Azure w dziennikach Azure Monitor, Azure Event Hubs i kontach magazynu. Zobacz [, jak rozwiązywać problemy z wydajnością zapytań](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).

### <a name="use-connection-pooling"></a>Korzystanie z puli połączeń
Zarządzanie połączeniami z bazą danych może mieć znaczny wpływ na wydajność aplikacji jako całości. Aby zoptymalizować wydajność, należy skrócić liczbę połączeń i czas ustanawiania połączeń w kluczowych ścieżkach kodu. Użyj [puli połączeń](https://docs.microsoft.com/azure/mysql/concepts-connectivity#access-databases-by-using-connection-pooling-recommended) , aby nawiązać połączenie z usługą Azure Database for MySQL w celu zwiększenia odporności i wydajności. 

Aby efektywnie zarządzać połączeniami, można użyć pulę połączenia [ProxySQL](https://proxysql.com/) . Użycie połączenia pulę może zmniejszyć bezczynne połączenia i ponownie użyć istniejących połączeń, co pomoże uniknąć problemów. Zobacz [jak skonfigurować ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) , aby dowiedzieć się więcej. 

### <a name="retry-logic-to-handle-transient-errors"></a>Logika ponawiania w celu obsługi błędów przejściowych
W aplikacji mogą występować [Błędy przejściowe](https://docs.microsoft.com/azure/mysql/concepts-connectivity#handling-transient-errors) , w przypadku których połączenia z bazą danych są usuwane lub tracone sporadycznie. W takich sytuacjach serwer działa po jednym do dwóch ponownych prób w ciągu 5 – 10 sekund. 

Dobrym sposobem jest odczekanie 5 sekund przed pierwszym ponowieniem próby. Następnie postępuj zgodnie z poszczególnymi ponowień, zwiększając czas oczekiwania, aż do 60 sekund. Ogranicz maksymalną liczbę ponownych prób, w których aplikacja uważa, że operacja nie powiodła się, więc możesz kontynuować badanie. Zobacz [, jak rozwiązywać problemy z błędami połączenia](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-common-connection-issues) , aby dowiedzieć się więcej. 

### <a name="enable-read-replication-to-mitigate-failovers"></a>Włącz replikację odczytu w celu ograniczenia trybu failover
Możesz użyć [replikacja typu Data-in](https://docs.microsoft.com/azure/mysql/howto-data-in-replication) w scenariuszach pracy awaryjnej. W przypadku korzystania z replik odczytu nie są wykonywane żadne automatyczne tryb failover między serwerami źródłowym i repliki. 

Zauważysz zwłokę między źródłem a repliką, ponieważ replikacja jest asynchroniczna. Opóźnienie sieci może mieć wpływ na wiele czynników, takich jak rozmiar obciążenia uruchomionego na serwerze źródłowym i opóźnienie między centrami danych. W większości przypadków opóźnienie replik z kilku sekund do kilku minut.

## <a name="database-deployment"></a>Wdrażanie bazy danych 

### <a name="configure-an-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>Konfigurowanie zadania usługi Azure Database for MySQL w potoku wdrażania CI/CD
Czasami należy wdrożyć zmiany w bazie danych. W takich przypadkach można korzystać z ciągłej integracji (CI) i ciągłego dostarczania (CD) za pomocą [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) i używać zadania dla [serwera MySQL](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops) do aktualizowania bazy danych przez uruchomienie skryptu niestandardowego względem tego programu.

### <a name="use-an-effective-process-for-manual-database-deployment"></a>Zastosuj obowiązujący proces ręcznego wdrażania bazy danych 
Podczas ręcznego wdrażania bazy danych wykonaj następujące kroki, aby zminimalizować przestoje lub ograniczyć ryzyko niepowodzenia wdrożenia: 

1. Utwórz kopię produkcyjnej bazy danych w nowej bazie danych przy użyciu [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) lub [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html). 
2. Aktualizowanie nowej bazy danych przy użyciu nowych zmian schematu lub aktualizacji wymaganych dla bazy danych. 
3. Umieść produkcyjną bazę danych w stanie tylko do odczytu. Nie należy mieć operacji zapisu w produkcyjnej bazie danych, dopóki wdrożenie nie zostanie ukończone. 
4. Przetestuj swoją aplikację przy użyciu nowo zaktualizowanej bazy danych z kroku 1.
5. Wdróż zmiany w aplikacji i upewnij się, że aplikacja korzysta teraz z nowej bazy danych, która ma najnowsze aktualizacje. 
6. Zachowaj starą produkcyjną bazę danych, aby można było wycofać zmiany. Następnie można oszacować, aby usunąć starą produkcyjną bazę danych lub wyeksportować ją do usługi Azure Storage w razie potrzeby. 

>[!NOTE]
>Jeśli aplikacja jest taka sama jak aplikacja handlu elektronicznego i nie można jej umieścić w stanie tylko do odczytu, należy wdrożyć zmiany bezpośrednio w produkcyjnej bazie danych po utworzeniu kopii zapasowej. Zmiany te powinny być wykonywane poza godzinami szczytu z niskim ruchem do aplikacji w celu zminimalizowania wpływu, ponieważ niektórzy użytkownicy mogą napotkać Nieudane żądania. 
>
>Upewnij się, że kod aplikacji obsługuje również wszelkie Nieudane żądania.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>Użyj natywnych metryk programu MySQL, aby sprawdzić, czy obciążenie przekracza tymczasowe rozmiary tabeli w pamięci
W przypadku obciążenia z dużym obciążeniem zapytania działające na serwerze MySQL mogą przekraczać tymczasowe rozmiary tabeli w pamięci. Obciążenie mocno odczytane może spowodować, że serwer przełączy się w celu zapisania tabel tymczasowych na dysku, co ma wpływ na wydajność aplikacji. Aby określić, czy serwer zapisuje na dysku w wyniku przekroczenia tymczasowego rozmiaru tabeli, zapoznaj się z następującymi metrykami:

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
`created_tmp_disk_tables`Metryka wskazuje, ile tabel zostało utworzonych na dysku. `created_tmp_table`Metryka informuje, ile tabel tymczasowych musi być utworzonych w pamięci, na podstawie obciążenia. Aby określić, czy uruchomienie określonego zapytania będzie korzystać z tabel tymczasowych, należy uruchomić instrukcję [Wyjaśnij](https://dev.mysql.com/doc/refman/8.0/en/explain.html) w zapytaniu. Szczegóły w `extra` kolumnie wskazują, `Using temporary` czy zapytanie zostanie uruchomione przy użyciu tabel tymczasowych.

Aby obliczyć procent obciążeń związanych z zapytaniami, które są rozlewane na dyski, użyj wartości metryk w następującej formule:

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

W idealnym przypadku ta wartość procentowa powinna być mniejsza o 25%. Jeśli zobaczysz, że wartość procentowa jest równa 25%, zalecamy modyfikowanie dwóch parametrów serwera, tmp_table_size i max_heap_table_size.

## <a name="database-schema-and-queries"></a>Schemat bazy danych i zapytania

Poniżej przedstawiono kilka wskazówek, które należy wziąć pod uwagę podczas budowania schematu bazy danych i zapytań.

### <a name="use-the-right-datatype-for-your-table-columns"></a>Użyj odpowiedniego elementu DataType dla kolumn tabeli
Użycie odpowiedniego typu danych w zależności od tego, jaki ma być magazyn, może zoptymalizować magazyn i zmniejszyć liczbę błędów, które mogą wystąpić z powodu nieprawidłowych typów.

### <a name="use-indexes"></a>Używanie indeksów
Aby uniknąć powolnych zapytań, można użyć indeksów. Indeksy mogą pomóc w szybkim wyszukiwaniu wierszy z określonymi kolumnami. Zobacz [jak używać indeksów w programie MySQL](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html).

### <a name="use-explain-for-your-select-queries"></a>Użyj WYJAŚNIeń dla wybranych zapytań
Użyj `EXPLAIN` instrukcji, aby uzyskać szczegółowe informacje na temat tego, co robi MySQL, aby uruchomić zapytanie. Może pomóc wykryć wąskie gardła lub problemy z zapytaniem. Zapoznaj się z tematem [jak używać Wyjaśnij, aby uzyskać profilowanie wydajności zapytań](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).


