---
title: Procedury składowane zarządzania — Azure Database for MariaDB
description: Dowiedz się, które procedury składowane w Azure Database for MariaDB są przydatne, aby ułatwić Konfigurowanie replikacji danych, ustawianie strefy czasowej i zabicia zapytań.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 103bba37f5574185f10f5c4e28e66268da0c7f39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664635"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Procedury składowane zarządzania Azure Database for MariaDB

Procedury składowane są dostępne na serwerach Azure Database for MariaDB, aby ułatwić zarządzanie serwerem MariaDB. Obejmuje to zarządzanie połączeniami serwera, kwerendami i konfigurowaniem replikacja typu data-in.  

## <a name="data-in-replication-stored-procedures"></a>Replikacja typu data-in procedury składowane

Replikacja typu data-in umożliwia synchronizowanie danych z serwera MariaDB uruchomionego lokalnie, na maszynach wirtualnych lub w usługach bazy danych hostowanych przez innych dostawców chmury do usługi Azure Database for MariaDB.

Poniższe procedury składowane służą do konfigurowania lub usuwania replikacja typu data-in między źródłem a repliką.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Uwaga dotycząca użycia**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Nie dotyczy|Aby przenieść dane z trybem SSL, Przekaż kontekst certyfikatu urzędu certyfikacji do parametru master_ssl_ca. </br><br>Aby przesłać dane bez protokołu SSL, Przekaż pusty ciąg do parametru master_ssl_ca.|
|*mysql.az_replication _start*|NIE DOTYCZY|NIE DOTYCZY|Uruchamia replikację.|
|*mysql.az_replication _stop*|NIE DOTYCZY|NIE DOTYCZY|Kończy replikację.|
|*mysql.az_replication _remove_master*|NIE DOTYCZY|NIE DOTYCZY|Usuwa relację replikacji między źródłem a repliką.|
|*mysql.az_replication_skip_counter*|NIE DOTYCZY|NIE DOTYCZY|Pomija jeden błąd replikacji.|

Aby skonfigurować replikacja typu data-in między źródłem a repliką w Azure Database for MariaDB, zapoznaj się z [tematem konfigurowanie replikacja typu Data-in](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Inne procedury składowane

Poniższe procedury składowane są dostępne w Azure Database for MariaDB zarządzania serwerem.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Uwaga dotycząca użycia**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|Nie dotyczy|Równoważne [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) polecenie. Nastąpi przerwanie połączenia skojarzonego z podanym processlist_id po zakończeniu wszelkich instrukcji wykonywanych przez połączenie.|
|*mysql.az_kill_query*|processlist_id|Nie dotyczy|Równoważne [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) polecenie. Zakończy instrukcję, że połączenie jest aktualnie wykonywane. Opuszcza połączenie.|
|*mysql.az_load_timezone*|NIE DOTYCZY|NIE DOTYCZY|Ładuje tabele strefy czasowej, aby zezwolić na `time_zone` ustawienie wartości nazwanych (np. "USA/Pacyfik").|

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak skonfigurować [replikacja typu Data-in](howto-data-in-replication.md)
- Dowiedz się, jak używać [tabel strefy czasowej](howto-server-parameters.md#working-with-the-time-zone-parameter)