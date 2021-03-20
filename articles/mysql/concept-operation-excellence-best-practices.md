---
title: Najlepsze rozwiązania operacyjne serwera MySQL — Azure Database for MySQL
description: W tym artykule opisano najlepsze rozwiązania dotyczące korzystania z bazy danych MySQL na platformie Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1d84483a701bf7fb4e6c50b9dc4f4f89993c64d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96355116"
---
# <a name="best-practices-for-server-operations-on-azure-database-for-mysql--single-server"></a>Najlepsze rozwiązania dotyczące operacji serwera na Azure Database for MySQL-pojedynczym serwerze

Zapoznaj się z najlepszymi rozwiązaniami dotyczącymi pracy z Azure Database for MySQLami. Po dodaniu nowych funkcji do platformy będziemy nadal skupić się na sposobie udoskonalania najlepszych rozwiązań w tej sekcji.

## <a name="azure-database-for-mysql-operational-guidelines"></a>Azure Database for MySQL wytycznych operacyjnych 

Poniżej znajdują się wskazówki dotyczące działania, które należy wykonać podczas pracy z Azure Database for MySQL w celu poprawy wydajności bazy danych: 

* Wspólna **Lokalizacja**: aby zmniejszyć opóźnienie sieci, należy umieścić klienta i serwer bazy danych w tym samym regionie świadczenia usługi Azure.

* **Monitoruj użycie pamięci, procesora CPU i magazynu**: [alerty można skonfigurować](howto-alert-on-metric.md) w taki sposób, aby powiadomienia o zmianie wzorców użytkowania lub wydajności wdrożenia były możliwe, aby zapewnić wydajność i dostępność systemu. 

* **Skalowanie w górę wystąpienia bazy danych**: można [skalować w górę](howto-create-manage-server-portal.md) , gdy zbliża się limit pojemności magazynu. Należy dysponować buforem w magazynie i pamięci, aby zapewnić nieprzewidziane zwiększenie popytu z aplikacji. Możesz również [włączyć funkcję Automatyczne zwiększanie magazynu](howto-auto-grow-storage-portal.md) "on", aby upewnić się, że usługa automatycznie skaluje magazyn w taki sposób, w jaki zbliża się limit magazynu. 

* **Skonfiguruj kopie zapasowe**: Włącz [lokalne lub geograficznie nadmiarowe kopie zapasowe](howto-restore-server-portal.md#set-backup-configuration) na podstawie wymagań firmy. Ponadto należy zmodyfikować okres przechowywania kopii zapasowych w celu zapewnienia ciągłości działania. 

* **Zwiększ wydajność operacji we/wy**: Jeśli obciążenie bazy danych wymaga większej liczby operacji we/wy niż w przypadku aprowizacji, odzyskiwanie lub inne operacje transakcyjne dla bazy danych będą powolne. Aby zwiększyć wydajność operacji we/wy wystąpienia serwera, należy wykonać dowolne lub wszystkie z następujących czynności: 

    * Usługa Azure Database for MySQL oferuje skalowanie IOPS przy częstotliwości trzech operacji we/wy na GB miejsca do magazynowania. [Zwiększ magazyn aprowizacji](howto-create-manage-server-portal.md#scale-storage-up) , aby skalować operacje we/wy w celu zapewnienia lepszej wydajności. 

    * Jeśli korzystasz już z obsługiwanego magazynu operacji we/wy, Zapewnij [dodatkową przepustowość](howto-create-manage-server-portal.md#scale-storage-up). 

* **Skalowanie obliczeniowe**: obciążenie bazy danych może być również ograniczone ze względu na procesor lub pamięć, co może mieć poważny wpływ na przetwarzanie transakcji. Należy pamiętać, że obliczenia (warstwa cenowa) można skalować w górę lub w dół między [ogólnego przeznaczenia lub warstw zoptymalizowanych pod kątem pamięci](concepts-pricing-tiers.md) . 

* **Test w celu przełączenia w tryb failover**: ręcznie Przetestuj tryb failover dla wystąpienia serwera, aby zrozumieć, jak długo trwa proces używania i upewnij się, że aplikacja, która uzyskuje dostęp do wystąpienia serwera, może automatycznie połączyć się z nowym wystąpieniem serwera po przejściu do trybu failover.

* **Użyj klucza podstawowego**: Upewnij się, że tabele mają podstawowy lub unikatowy klucz podczas działania na Azure Database for MySQL. Pomaga to w tworzeniu dużej ilości kopii zapasowych, replik itp. i poprawia wydajność.

* **Skonfiguruj wartość czasu wygaśnięcia**: Jeśli aplikacja kliencka buforuje dane usługi nazw domen (DNS) wystąpień serwera, ustaw wartość czasu wygaśnięcia (TTL) mniejszą niż 30 sekund. Ponieważ podstawowy adres IP wystąpienia serwera może ulec zmianie po przejściu w tryb failover, buforowanie danych DNS przez dłuższy czas może prowadzić do błędów połączenia, jeśli aplikacja próbuje połączyć się z adresem IP, który nie jest już w usłudze.

* Użyj puli połączeń, aby uniknąć [maksymalnego limitu połączeń](concepts-server-parameters.md#max_connections)i użyć logiki ponawiania, aby uniknąć sporadycznych problemów z połączeniami. 

* Jeśli używasz repliki, użyj ProxySQL, [Aby zrównoważyć obciążenie](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847) między serwerem podstawowym i pomocniczym serwerem repliki do odczytu. Zobacz kroki instalacji tutaj. </br> 

* Po zainicjowaniu obsługi administracyjnej zasobu upewnij się, że [Automatyczne zwiększanie został włączony](howto-auto-grow-storage-portal.md) dla Azure Database for MySQL. Nie powoduje to dodania dodatkowego kosztu i chroni bazę danych przed wszelkimi wąskimi gardłami magazynowania, które mogą zostać uruchomione. </br> 


### <a name="using-innodb-with-azure-database-for-mysql"></a>Używanie InnoDB z Azure Database for MySQL

*   Jeśli `ibdata1` jest używana funkcja, która jest plikiem danych obszaru systemu, nie można zmniejszyć lub przeczyścić danych z tabeli ani przenieść tabeli do pliku na tabelę `tablespaces` .

* W przypadku bazy danych o rozmiarze większym niż 1 TB należy utworzyć tabelę w **innodb_file_per_table** `tablespace` . W przypadku pojedynczej tabeli o rozmiarze większym niż 1 TB należy tabelę [partycji](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) .

*   W przypadku serwera, który ma dużą liczbę `tablespace` , uruchomienie aparatu będzie bardzo wolne z powodu przeskanowania sekwencyjnego obszaru tabel podczas uruchamiania programu MySQL lub przełączenia w tryb failover. 

* Ustaw wartość innodb_file_per_table = ON przed utworzeniem tabeli, jeśli łączna liczba tabeli jest mniejsza niż 500.

* Jeśli masz więcej niż 500 tabel w bazie danych, przejrzyj rozmiar tabeli dla każdej tabeli. W przypadku dużej tabeli należy rozważyć użycie obszaru tabel plików na tabelę, aby uniknąć maksymalnego limitu magazynu w pliku obszaru tabel systemu.

> [!NOTE]
> W przypadku tabel o rozmiarze mniejszym niż 5 GB Rozważ użycie obszaru tabel systemu 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* [Podziel](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) tabelę na tworzenie tabeli, jeśli masz bardzo dużą tabelę, która może być większa niż 1 TB.

* Użyj wielu serwerów MySQL i rozłożyć tabele na te serwery. Unikaj umieszczania zbyt wielu tabel na jednym serwerze, jeśli masz około 10000 tabel lub więcej. 

## <a name="next-steps"></a>Następne kroki
- [Najlepsze rozwiązanie w zakresie wydajności Azure Database for MySQL](concept-performance-best-practices.md)
- [Najlepsze rozwiązanie w zakresie monitorowania Azure Database for MySQL](concept-monitoring-best-practices.md)
