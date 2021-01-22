---
title: Konfigurowanie parametrów serwera-Azure Portal-Azure Database for MariaDB
description: W tym artykule opisano sposób konfigurowania parametrów serwera MariaDB w Azure Database for MariaDB przy użyciu Azure Portal.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: cda6b2bd3d49bd71b4c57d5d459c07b61addb493
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664804"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-the-azure-portal"></a>Konfigurowanie parametrów serwera w Azure Database for MariaDB przy użyciu Azure Portal

Azure Database for MariaDB obsługuje konfigurację niektórych parametrów serwera. W tym artykule opisano sposób konfigurowania tych parametrów przy użyciu Azure Portal. Nie wszystkie parametry serwera można dostosować.

>[!Note]
> Parametry serwera można zaktualizować globalnie na poziomie serwera, przy użyciu [interfejsu wiersza polecenia platformy Azure](./howto-configure-server-parameters-cli.md), programu [PowerShell](./howto-configure-server-parameters-using-powershell.md) lub witryny [Azure Portal](./howto-server-parameters.md).

## <a name="configure-server-parameters"></a>Konfigurowanie parametrów serwera

1. Zaloguj się do Azure Portal, a następnie zlokalizuj serwer Azure Database for MariaDB.
2. W sekcji **Ustawienia** kliknij pozycję **parametry serwera** , aby otworzyć stronę parametry serwera dla serwera Azure Database for MariaDB.
![Strona parametrów serwera Azure Portal](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Znajdź wszystkie ustawienia, które należy dostosować. Przejrzyj kolumnę **Description** , aby zrozumieć przeznaczenie i dozwolone wartości.
![Wyliczenie listy rozwijanej](./media/howto-server-parameters/3-toggle_parameter.png)
4. Kliknij przycisk  **Zapisz** , aby zapisać zmiany.
![Zapisz lub Odrzuć zmiany](./media/howto-server-parameters/4-save_parameters.png)
5. Jeśli Zapisano nowe wartości parametrów, zawsze możesz przywrócić wszystkie elementy z powrotem do wartości domyślnych, wybierając pozycję **Zresetuj wszystkie do domyślnych**.
![Zresetuj wszystkie do domyślnych](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="setting-parameters-not-listed"></a>Nie wymieniono parametrów ustawień

Jeśli parametr serwera, który chcesz zaktualizować, nie znajduje się na liście Azure Portal, opcjonalnie możesz ustawić parametr na poziomie połączenia przy użyciu `init_connect` . Powoduje to ustawienie parametrów serwera dla każdego klienta łączącego się z serwerem. 

1. W sekcji **Ustawienia** kliknij pozycję **parametry serwera** , aby otworzyć stronę parametry serwera dla serwera Azure Database for MariaDB.
2. Wyszukaj `init_connect`
3. Dodaj parametry serwera w formacie: `SET parameter_name=YOUR_DESIRED_VALUE` wartość w kolumnie wartość.

    Na przykład można zmienić zestaw znaków serwera, ustawiając wartość `init_connect` na `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

## <a name="working-with-the-time-zone-parameter"></a>Praca z parametrem strefy czasowej

### <a name="populating-the-time-zone-tables"></a>Wypełnianie tabel strefy czasowej

Tabele strefy czasowej na serwerze można wypełnić przez wywołanie `mysql.az_load_timezone` procedury składowanej z narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench.

> [!NOTE]
> Jeśli uruchamiasz `mysql.az_load_timezone` polecenie z programu MySQL Workbench, może być konieczne wyłączenie bezpiecznego trybu aktualizacji jako pierwszego przy użyciu programu `SET SQL_SAFE_UPDATES=0;` .

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

Zapoznaj się z dokumentacją MariaDB dla [funkcji daty i godziny](https://mariadb.com/kb/en/library/convert_tz/).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [parametrów serwera](concepts-server-parameters.md)
