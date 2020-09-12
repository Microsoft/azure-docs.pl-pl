---
title: Konfigurowanie parametrów serwera-Azure Portal-Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania parametrów serwera MySQL w Azure Database for MySQL przy użyciu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/11/2020
ms.openlocfilehash: 8a988895cd8999d15c32d7056d35abf40aeaba7e
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89420697"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Konfigurowanie parametrów serwera w Azure Database for MySQL przy użyciu Azure Portal

Azure Database for MySQL obsługuje konfigurację niektórych parametrów serwera. W tym artykule opisano sposób konfigurowania tych parametrów przy użyciu Azure Portal. Nie wszystkie parametry serwera można dostosować.

## <a name="configure-server-parameters"></a>Konfigurowanie parametrów serwera

1. Zaloguj się do Azure Portal, a następnie zlokalizuj serwer Azure Database for MySQL.
2. W sekcji **Ustawienia** kliknij pozycję **parametry serwera** , aby otworzyć stronę parametry serwera dla serwera Azure Database for MySQL.
![Strona parametrów serwera Azure Portal](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Znajdź wszystkie ustawienia, które należy dostosować. Przejrzyj kolumnę **Description** , aby zrozumieć przeznaczenie i dozwolone wartości.
![Wyliczenie listy rozwijanej](./media/howto-server-parameters/3-toggle_parameter.png)
4. Kliknij przycisk  **Zapisz** , aby zapisać zmiany.
![Zapisz lub Odrzuć zmiany](./media/howto-server-parameters/4-save_parameters.png)
5. Jeśli Zapisano nowe wartości parametrów, zawsze możesz przywrócić wszystkie elementy z powrotem do wartości domyślnych, wybierając pozycję **Zresetuj wszystkie do domyślnych**.
![Zresetuj wszystkie do domyślnych](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="setting-parameters-not-listed"></a>Nie wymieniono parametrów ustawień

Jeśli parametr serwera, który chcesz zaktualizować, nie znajduje się na liście Azure Portal, opcjonalnie możesz ustawić parametr na poziomie połączenia przy użyciu `init_connect` . Powoduje to ustawienie parametrów serwera dla każdego klienta łączącego się z serwerem. 

1. W sekcji **Ustawienia** kliknij pozycję **parametry serwera** , aby otworzyć stronę parametry serwera dla serwera Azure Database for MySQL.
2. Wyszukaj `init_connect`
3. Dodaj parametry serwera w formacie: `SET parameter_name=YOUR_DESIRED_VALUE` wartość w kolumnie wartość.

    Na przykład można zmienić zestaw znaków serwera, ustawiając wartość `init_connect` na `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

>[!Note]
> `init_connect` może służyć do zmiany parametrów, które nie wymagają uprawnień administratora na poziomie sesji. Aby sprawdzić, czy można ustawić parametr przy użyciu `init_connect` polecenia, wykonaj `set session parameter_name=YOUR_DESIRED_VALUE;` polecenie, a jeśli wystąpi błąd z **odmową dostępu, musisz mieć uprawnienia administratora** , a następnie nie można ustawić parametru przy użyciu "init_connect".

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

Zapoznaj się z dokumentacją programu MySQL dla [funkcji daty i godziny](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Następne kroki

- [Biblioteki połączeń dla Azure Database for MySQL](concepts-connection-libraries.md).
