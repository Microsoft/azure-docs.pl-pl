---
title: Najlepsze rozwiązania w zakresie wydajności — Azure Database for MySQL
description: W tym artykule opisano niektóre zalecenia dotyczące monitorowania i dostrajania wydajności Azure Database for MySQL.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: 7b5223bc08c470a0e8722b76b80473aaa235b51a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727162"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Najlepsze rozwiązania w zakresie optymalnej wydajności Azure Database for MySQL pojedynczego serwera

Dowiedz się, jak uzyskać najlepszą wydajność podczas pracy z jednym serwerem Azure Database for MySQL. Po dodaniu nowych funkcji do platformy będziemy nadal udoskonalać nasze zalecenia w tej sekcji.

## <a name="physical-proximity"></a>Fizyczna bliskość

 Upewnij się, że wdrożono aplikację i bazę danych w tym samym regionie. Szybkie sprawdzenie przed uruchomieniem testu porównawczego wydajności polega na określeniu opóźnienia sieci między klientem i bazą danych przy użyciu prostego zapytania SELECT 1. 

## <a name="accelerated-networking"></a>Accelerated Networking

Używaj przyspieszonej sieci dla serwera aplikacji, jeśli używasz usługi Azure Virtual Machines, Azure Kubernetes lub App Services. Przyspieszona sieć umożliwia wirtualizację we/wy z jednym elementem głównym (SR-IOV) do maszyny wirtualnej, co znacznie zwiększa wydajność sieci. Ta ścieżka o wysokiej wydajności pomija hosta ze ścieżki datapath, skracając czas opóźnienia, wahania i użycie procesora CPU w celu użycia z najbardziej wymagającymi obciążeniami sieci na obsługiwanych typach maszyn wirtualnych.

## <a name="connection-efficiency"></a>Efektywność połączenia

Ustanowienie nowego połączenia jest zawsze kosztownym i czasochłonnym zadaniem. Gdy aplikacja żąda połączenia z bazą danych, ustala priorytet przydziału istniejących bezczynnych połączeń bazy danych zamiast tworzyć nowe.  Oto kilka opcji dotyczących dobrych rozwiązań z zakresu połączeń:

- **ProxySQL**: Użyj [ProxySQL](https://proxysql.com/) , która zapewnia wbudowaną pulę połączeń i [równoważenia obciążenia](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) do wielu replik odczytu zgodnie z wymaganiami na żądanie ze wszystkimi zmianami w kodzie aplikacji.

- **Serwer proxy danych Heimdall**: Alternatywnie można również korzystać z serwera proxy danych Heimdall, niezależnego od dostawcy rozwiązania serwera proxy. Obsługuje buforowanie zapytań i dzieli odczyt/zapis z wykrywaniem opóźnienia replikacji. Możesz również zapoznać się z tematem jak [przyspieszyć działanie programu MySQL za pomocą serwera proxy Heimdall](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349).  

- **Połączenie trwałe lub Long-Lived**: Jeśli aplikacja ma krótkie transakcje lub zwykle zapytania z czasem wykonywania < 5-10 ms, Zastąp krótkie połączenia z trwałymi połączeniami. Zastępowanie krótkich połączeń z trwałymi połączeniami wymaga tylko drobnych zmian w kodzie, ale ma on istotny wpływ na poprawę wydajności w wielu typowych scenariuszach aplikacji. Upewnij się, że ustawiono limit czasu lub Zamknij połączenie po zakończeniu transakcji.

- **Replika**: Jeśli korzystasz z repliki, użyj [ProxySQL](https://proxysql.com/) , aby zrównoważyć obciążenie między serwerem podstawowym i serwerem repliki pomocniczej z możliwością odczytu. Dowiedz się [, jak skonfigurować ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847).

## <a name="data-import-configurations"></a>Konfiguracje importowania danych

- Możesz tymczasowo skalować wystąpienie do wyższego rozmiaru jednostki SKU przed rozpoczęciem operacji importowania danych, a następnie skalować ją w dół, gdy import zakończy się pomyślnie.
- Możesz zaimportować dane z minimalnym przestojem, korzystając z [Azure Database Migration Service (DMS)](https://datamigration.microsoft.com/) dla migracji w trybie online lub offline. 

## <a name="azure-database-for-mysql-memory-recommendations"></a>Zalecenia dotyczące pamięci Azure Database for MySQL

Najlepszym rozwiązaniem w zakresie wydajności Azure Database for MySQL jest przydzielenie wystarczającej ilości pamięci RAM, aby zestaw roboczy był już całkowicie w pamięci. 

- Sprawdź, czy procent pamięci używany do osiągnięcia [limitów](./concepts-pricing-tiers.md) przy użyciu [metryk dla serwera MySQL](./concepts-monitoring.md). 
- Skonfiguruj alerty dotyczące tych numerów, aby upewnić się, że w miarę jak serwery osiągnieją limity, aby rozwiązać ten problem. Na podstawie zdefiniowanych limitów Sprawdź, czy skalowanie w górę jednostki SKU bazy danych jest większe niż w przypadku wyższego rozmiaru lub do lepszej warstwy cenowej, co powoduje znaczący wzrost wydajności. 
- Skaluj w górę do momentu, aż numery wydajności nie znikną znacznie po operacji skalowania. Aby uzyskać informacje na temat monitorowania metryk wystąpienia bazy danych, zobacz [metryki bazy danych MySQL](./concepts-monitoring.md#metrics).
 
## <a name="use-innodb-buffer-pool-warmup"></a>Korzystanie z puli buforów InnoDB rozgrzewania

Po ponownym uruchomieniu Azure Database for MySQL Server strony danych znajdujące się w magazynie są ładowane jako tabele zapytania, co prowadzi do zwiększonego opóźnienia i wolniejszej wydajności podczas pierwszego wykonywania zapytań. Może to nie być akceptowalne dla obciążeń z opóźnieniem. 

Wykorzystanie puli buforów InnoDB rozgrzewania skraca okres rozgrzewania przez ponowne załadowanie stron dysku, które znajdowały się w puli buforów przed ponownym uruchomieniem, zamiast czekać na operacje DML lub SELECT w celu uzyskania dostępu do odpowiednich wierszy.

Można skrócić okres rozgrzewania po ponownym uruchomieniu serwera Azure Database for MySQL, który reprezentuje zalety wydajności przez skonfigurowanie [parametrów serwera puli buforów InnoDB](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html). InnoDB zapisuje wartość procentową ostatnio używanych stron dla każdej puli buforów podczas zamykania serwera i przywraca te strony podczas uruchamiania serwera.

Należy również pamiętać, że zwiększona wydajność zapewnia koszt dłuższy od momentu uruchomienia serwera. Gdy ten parametr jest włączony, oczekiwane jest uruchamianie i czas ponownego uruchomienia serwera w zależności od liczby operacji we/wy zainicjowanej na serwerze. 

Zalecamy testowanie i monitorowanie czasu ponownego uruchomienia, aby upewnić się, że wydajność uruchamiania/ponownego uruchamiania jest akceptowalna, ponieważ serwer jest niedostępny w tym czasie. Nie zaleca się używania tego parametru z mniej niż 1000 aprowizacji operacji we/wy (lub innymi słowy, gdy obsługa magazynu jest mniejsza niż 335 GB).

Aby zapisać stan puli buforów podczas zamykania serwera, ustaw parametr serwera `innodb_buffer_pool_dump_at_shutdown` na `ON` . Analogicznie ustaw parametr Server `innodb_buffer_pool_load_at_startup` na, aby `ON` przywrócić stan puli buforów podczas uruchamiania serwera. Można kontrolować wpływ na czas uruchamiania/ponownego uruchamiania przez obniżenie i dostosowanie wartości parametru serwera `innodb_buffer_pool_dump_pct` . Domyślnie ten parametr jest ustawiony na `25` .

> [!Note]
> Parametry rozgrzewania puli buforów InnoDB są obsługiwane tylko na serwerach magazynu ogólnego przeznaczenia z magazynem o pojemności do 16 TB. Dowiedz się więcej na temat [opcji magazynu Azure Database for MySQL w tym miejscu](./concepts-pricing-tiers.md#storage).

## <a name="next-steps"></a>Następne kroki

- [Najlepsze rozwiązanie w zakresie operacji serwera przy użyciu Azure Database for MySQL](concept-operation-excellence-best-practices.md) <br/>
- [Najlepsze rozwiązanie w zakresie monitorowania Azure Database for MySQL](concept-monitoring-best-practices.md)<br/>
- [Wprowadzenie do Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>