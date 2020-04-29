---
title: Konfigurowanie parametrów serwera-Azure Portal-Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania parametrów serwera MySQL w Azure Database for MySQL przy użyciu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/16/2020
ms.openlocfilehash: bd0a867cce9b2a9ad793b491b9042034ef5810f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605155"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Jak skonfigurować parametry serwera w Azure Database for MySQL przy użyciu Azure Portal

Azure Database for MySQL obsługuje konfigurację niektórych parametrów serwera. W tym artykule opisano sposób konfigurowania tych parametrów przy użyciu Azure Portal. Nie wszystkie parametry serwera można dostosować.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Przejdź do parametrów serwera na Azure Portal

1. Zaloguj się do Azure Portal, a następnie zlokalizuj serwer Azure Database for MySQL.
2. W sekcji **Ustawienia** kliknij pozycję **parametry serwera** , aby otworzyć stronę parametry serwera dla serwera Azure Database for MySQL.
![Strona parametrów serwera Azure Portal](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Znajdź wszystkie ustawienia, które należy dostosować. Przejrzyj kolumnę **Description** , aby zrozumieć przeznaczenie i dozwolone wartości.
![Wyliczenie listy rozwijanej](./media/howto-server-parameters/3-toggle_parameter.png)
4. Kliknij przycisk **Zapisz** , aby zapisać zmiany.
![Zapisz lub Odrzuć zmiany](./media/howto-server-parameters/4-save_parameters.png)
5. Jeśli Zapisano nowe wartości parametrów, zawsze możesz przywrócić wszystkie elementy z powrotem do wartości domyślnych, wybierając pozycję **Zresetuj wszystkie do domyślnych**.
![Zresetuj wszystkie do domyślnych](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista konfigurowalnych parametrów serwera

Lista obsługiwanych parametrów serwera stale rośnie. Karta parametry serwera w Azure Portal służy do uzyskiwania definicji i konfigurowania parametrów serwera na podstawie wymagań aplikacji.

## <a name="non-configurable-server-parameters"></a>Parametry serwera, które nie zostały konfigurowalne

Nie można skonfigurować rozmiaru puli buforów InnoDB i powiązana z Twoją [warstwą cenową](concepts-service-tiers.md).

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Rozmiar puli buforów InnoDB w <br>MB (serwery obsługujące do 4 TB magazynu)**| **Rozmiar puli buforów InnoDB w <br>MB (serwery obsługujące do 16 TB magazynu)**|
|:---|---:|---:|---:|
|Podstawowy| 1| 832| |
|Podstawowy| 2| 2560| |
|Ogólnego przeznaczenia| 2| 3584| 7168|
|Ogólnego przeznaczenia| 4| 7680| 15360|
|Ogólnego przeznaczenia| 8| 15360| 30720|
|Ogólnego przeznaczenia| 16| 31232| 62464|
|Ogólnego przeznaczenia| 32| 62976| 125952|
|Ogólnego przeznaczenia| 64| 125952| 251904|
|Optymalizacja pod kątem pamięci| 2| 7168| 14336|
|Optymalizacja pod kątem pamięci| 4| 15360| 30720|
|Optymalizacja pod kątem pamięci| 8| 30720| 61440|
|Optymalizacja pod kątem pamięci| 16| 62464| 124928|
|Optymalizacja pod kątem pamięci| 32| 125952| 251904|

Te dodatkowe parametry serwera nie są konfigurowane w systemie:

|**Konstruktora**|**Stała wartość**|
| :------------------------ | :-------- |
|innodb_file_per_table w warstwie Podstawowa|WYŁ.|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256 MB|
|innodb_log_files_in_group|2|

Inne parametry serwera, które nie są wymienione w tym miejscu, są ustawione na wartości domyślne dla programu MySQL w wersjach [5,7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) i [5,6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="working-with-the-time-zone-parameter"></a>Praca z parametrem strefy czasowej

### <a name="populating-the-time-zone-tables"></a>Wypełnianie tabel strefy czasowej

Tabele strefy czasowej na serwerze można wypełnić przez wywołanie procedury `mysql.az_load_timezone` składowanej z narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench.

> [!NOTE]
> Jeśli uruchamiasz `mysql.az_load_timezone` polecenie z programu MySQL Workbench, może być konieczne wyłączenie bezpiecznego trybu aktualizacji jako pierwszego przy użyciu programu `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Należy ponownie uruchomić serwer, aby upewnić się, że tabele strefy czasowej są prawidłowo wypełnione. Aby ponownie uruchomić serwer, użyj [Azure Portal](howto-restart-server-portal.md) lub [interfejsu wiersza polecenia](howto-restart-server-cli.md).

Aby wyświetlić dostępne wartości strefy czasowej, uruchom następujące polecenie:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ustawianie strefy czasowej na poziomie globalnym

Strefę czasową na poziomie globalnym można ustawić na stronie **parametrów serwera** w Azure Portal. Poniżej ustawia globalną strefę czasową na wartość "US/Pacyfik".

![Ustaw parametr strefy czasowej](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Ustawianie strefy czasowej na poziomie sesji

Strefę czasową na poziomie sesji można ustawić, uruchamiając `SET time_zone` polecenie za pomocą narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench. W poniższym przykładzie ustawiono strefę czasową dla strefy czasowej **USA/Pacyfiku** .

```sql
SET time_zone = 'US/Pacific';
```

Zapoznaj się z dokumentacją programu MySQL dla [funkcji daty i godziny](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Następne kroki

- [Biblioteki połączeń dla Azure Database for MySQL](concepts-connection-libraries.md).
