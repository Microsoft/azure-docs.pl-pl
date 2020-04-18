---
title: Konfigurowanie parametrów serwera — witryna Azure portal — usługa Azure Database for MariaDB
description: W tym artykule opisano sposób konfigurowania parametrów serwera MariaDB w usłudze Azure Database dla mariadb przy użyciu witryny Azure portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/16/2020
ms.openlocfilehash: f39e9450fb922e5b93d7b4b809df73cf5ab007c1
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81602408"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Jak skonfigurować parametry serwera w usłudze Azure Database for MariaDB przy użyciu witryny Azure portal

Usługa Azure Database for MariaDB obsługuje konfigurację niektórych parametrów serwera. W tym artykule opisano sposób konfigurowania tych parametrów przy użyciu witryny Azure portal. Nie wszystkie parametry serwera można regulować.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Przejdź do parametrów serwera w witrynie Azure portal

1. Zaloguj się do witryny Azure portal, a następnie znajdź swoją usługę Azure Database dla serwera MariaDB.
2. W sekcji **USTAWIENIA** kliknij pozycję **Parametry serwera,** aby otworzyć stronę parametrów serwera dla serwera usługi Azure Database for MariaDB server.
![Strona parametrów serwera portalu Azure](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Znajdź wszystkie ustawienia, które chcesz dostosować. Przejrzyj kolumnę **Opis,** aby zrozumieć cel i dozwolone wartości.
![Wyliczaj rozwijane](./media/howto-server-parameters/3-toggle_parameter.png)
4. Kliknij **przycisk Zapisz,** aby zapisać zmiany.
![Zapisywanie lub odrzucanie zmian](./media/howto-server-parameters/4-save_parameters.png)
5. Jeśli zapisano nowe wartości parametrów, zawsze możesz przywrócić wszystko do wartości domyślnych, wybierając **opcję Resetuj wszystko do wartości domyślnej**.
![Resetowanie wszystkich do ustawień domyślnych](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista konfigurowalnych parametrów serwera

Lista obsługiwanych parametrów serwera stale rośnie. Użyj karty parametry serwera w witrynie Azure portal, aby uzyskać definicję i skonfigurować parametry serwera na podstawie wymagań aplikacji.

## <a name="non-configurable-server-parameters"></a>Niekonfigurowalne parametry serwera

Pula buforów InnoDB i połączenia Max nie są konfigurowalne i powiązane z [warstwą cenową](concepts-pricing-tiers.md).

|**Warstwa cenowa**| **vCore(s)**|**Pula buforów InnoDB (MB)**|
|---|---|---|
|Podstawowy| 1| 1024|
|Podstawowy| 2| 2560|
|Ogólnego przeznaczenia| 2| 3584|
|Ogólnego przeznaczenia| 4| 7680|
|Ogólnego przeznaczenia| 8| 15360|
|Ogólnego przeznaczenia| 16| 31232|
|Ogólnego przeznaczenia| 32| 62976|
|Ogólnego przeznaczenia| 64| 125952|
|Optymalizacja pod kątem pamięci| 2| 7168|
|Optymalizacja pod kątem pamięci| 4| 15360|
|Optymalizacja pod kątem pamięci| 8| 30720|
|Optymalizacja pod kątem pamięci| 16| 62464|
|Optymalizacja pod kątem pamięci| 32| 125952|

Te dodatkowe parametry serwera nie są konfigurowane w systemie:

|**Parametr**|**Stała wartość**|
| :------------------------ | :-------- |
|innodb_file_per_table w warstwie Podstawowa|WYŁ.|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256 MB|
|innodb_log_files_in_group|2|

Inne parametry serwera, które nie są wymienione w tym miejscu są ustawione na ich MariaDB out-of-box wartości domyślne dla [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

## <a name="working-with-the-time-zone-parameter"></a>Praca z parametrem strefy czasowej

### <a name="populating-the-time-zone-tables"></a>Wypełnianie tabel stref czasowych

Tabele stref czasowych na serwerze `mysql.az_load_timezone` można wypełnić, wywołując procedurę składowaną za pomocą narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench.

> [!NOTE]
> Jeśli `mysql.az_load_timezone` używasz polecenia z mysql workbench, może być konieczne wyłączenie `SET SQL_SAFE_UPDATES=0;`trybu bezpiecznej aktualizacji przy użyciu .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Należy ponownie uruchomić serwer, aby upewnić się, że tabele stref czasowych są poprawnie wypełnione. Aby ponownie uruchomić serwer, użyj [witryny Azure portal](howto-restart-server-portal.md) lub [interfejsu wiersza polecenia](howto-restart-server-cli.md).
Aby wyświetlić dostępne wartości stref czasowych, uruchom następujące polecenie:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ustawianie strefy czasowej poziomu globalnego

Globalną strefę czasową poziomu można ustawić na stronie **Parametry serwera** w witrynie Azure portal. Poniżej ustawia globalną strefę czasową na wartość "US/Pacific".

![Ustawianie parametru strefy czasowej](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Ustawianie strefy czasowej poziomu sesji

Strefę czasową poziomu sesji można `SET time_zone` ustawić, uruchamiając polecenie za pomocą narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench. Poniższy przykład ustawia strefę czasową na strefę czasową **USA/Pacyfiku.**

```sql
SET time_zone = 'US/Pacific';
```

Zapoznaj się z dokumentacją MariaDB dotyczącą [funkcji daty i godziny](https://mariadb.com/kb/en/library/convert_tz/).

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
