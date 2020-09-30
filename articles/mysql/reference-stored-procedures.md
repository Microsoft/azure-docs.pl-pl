---
title: Procedury składowane zarządzania — Azure Database for MySQL
description: Dowiedz się, które procedury składowane w Azure Database for MySQL są przydatne, aby ułatwić Konfigurowanie replikacji danych, ustawianie strefy czasowej i zabicia zapytań.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: e67130516410f64c32eadbf15857ca3ec4c976fc
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542482"
---
# <a name="azure-database-for-mysql-management-stored-procedures"></a>Procedury składowane zarządzania Azure Database for MySQL

Procedury składowane są dostępne na serwerach Azure Database for MySQL, aby ułatwić zarządzanie serwerem MySQL. Obejmuje to zarządzanie połączeniami serwera, kwerendami i konfigurowaniem replikacja typu data-in.  

## <a name="data-in-replication-stored-procedures"></a>Replikacja typu data-in procedury składowane

Replikacja typu data-in umożliwia synchronizowanie danych z serwera MySQL uruchomionego lokalnie, na maszynach wirtualnych lub w usługach bazy danych hostowanych przez innych dostawców chmury do usługi Azure Database for MySQL.

Poniższe procedury składowane służą do konfigurowania lub usuwania replikacja typu data-in między źródłem a repliką.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Uwaga dotycząca użycia**|
|-----|-----|-----|-----|
|*MySQL. az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Nie dotyczy|Aby przenieść dane z trybem SSL, Przekaż kontekst certyfikatu urzędu certyfikacji do parametru master_ssl_ca. </br><br>Aby przesłać dane bez protokołu SSL, Przekaż pusty ciąg do parametru master_ssl_ca.|
|*Baza danych MySQL. az_replication _start*|NIE DOTYCZY|NIE DOTYCZY|Uruchamia replikację.|
|*Baza danych MySQL. az_replication _stop*|NIE DOTYCZY|NIE DOTYCZY|Kończy replikację.|
|*Baza danych MySQL. az_replication _remove_master*|NIE DOTYCZY|NIE DOTYCZY|Usuwa relację replikacji między źródłem a repliką.|
|*MySQL. az_replication_skip_counter*|NIE DOTYCZY|NIE DOTYCZY|Pomija jeden błąd replikacji.|

Aby skonfigurować replikacja typu data-in między źródłem a repliką w Azure Database for MySQL, zapoznaj się z [tematem konfigurowanie replikacja typu Data-in](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Inne procedury składowane

Poniższe procedury składowane są dostępne w Azure Database for MySQL zarządzania serwerem.

|**Nazwa procedury składowanej**|**Parametry wejściowe**|**Parametry wyjściowe**|**Uwaga dotycząca użycia**|
|-----|-----|-----|-----|
|*MySQL. az_kill*|processlist_id|Nie dotyczy|Równoważne [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) polecenie. Nastąpi przerwanie połączenia skojarzonego z podanym processlist_id po zakończeniu wszelkich instrukcji wykonywanych przez połączenie.|
|*MySQL. az_kill_query*|processlist_id|Nie dotyczy|Równoważne [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) polecenie. Zakończy instrukcję, że połączenie jest aktualnie wykonywane. Opuszcza połączenie.|
|*MySQL. az_load_timezone*|NIE DOTYCZY|NIE DOTYCZY|Ładuje tabele strefy czasowej, aby zezwolić na `time_zone` ustawienie wartości nazwanych (np. "USA/Pacyfik").|

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak skonfigurować [replikacja typu Data-in](howto-data-in-replication.md)
- Dowiedz się, jak używać [tabel strefy czasowej](howto-server-parameters.md#working-with-the-time-zone-parameter)
