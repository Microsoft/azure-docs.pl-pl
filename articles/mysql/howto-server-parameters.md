---
title: Konfigurowanie parametrów serwera-Azure Portal-Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania parametrów serwera MySQL w Azure Database for MySQL przy użyciu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: c28f0edafd72794a60ef577fc3177e4436157950
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631481"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Konfigurowanie parametrów serwera w Azure Database for MySQL przy użyciu Azure Portal

Azure Database for MySQL obsługuje konfigurację niektórych parametrów serwera. W tym artykule opisano sposób konfigurowania tych parametrów przy użyciu Azure Portal. Nie wszystkie parametry serwera można dostosować.

>[!Note]
> Parametry serwera można aktualizować globalnie na poziomie serwera, korzystać z [interfejsu wiersza polecenia platformy Azure](./howto-configure-server-parameters-using-cli.md), [programu PowerShell](./howto-configure-server-parameters-using-powershell.md)lub [Azure Portal](./howto-server-parameters.md).

## <a name="configure-server-parameters"></a>Konfigurowanie parametrów serwera

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie zlokalizuj serwer Azure Database for MySQL.
2. W sekcji **Ustawienia** kliknij pozycję **parametry serwera** , aby otworzyć stronę parametry serwera dla serwera Azure Database for MySQL.
:::image type="content" source="./media/howto-server-parameters/auzre-portal-server-parameters.png" alt-text="Strona parametrów serwera Azure Portal":::
3. Znajdź wszystkie ustawienia, które należy dostosować. Przejrzyj kolumnę **Description** , aby zrozumieć przeznaczenie i dozwolone wartości.
:::image type="content" source="./media/howto-server-parameters/3-toggle_parameter.png" alt-text="Strona parametrów serwera Azure Portal":::
4. Kliknij przycisk  **Zapisz** , aby zapisać zmiany.
:::image type="content" source="./media/howto-server-parameters/4-save_parameters.png" alt-text="Strona parametrów serwera Azure Portal":::
5. Jeśli Zapisano nowe wartości parametrów, zawsze możesz przywrócić wszystkie elementy z powrotem do wartości domyślnych, wybierając pozycję **Zresetuj wszystkie do domyślnych**.
:::image type="content" source="./media/howto-server-parameters/5-reset_parameters.png" alt-text="Strona parametrów serwera Azure Portal":::

## <a name="setting-parameters-not-listed"></a>Nie wymieniono parametrów ustawień

Jeśli parametr serwera, który chcesz zaktualizować, nie znajduje się na liście Azure Portal, opcjonalnie możesz ustawić parametr na poziomie połączenia przy użyciu `init_connect` . Powoduje to ustawienie parametrów serwera dla każdego klienta łączącego się z serwerem. 

1. W sekcji **Ustawienia** kliknij pozycję **parametry serwera** , aby otworzyć stronę parametry serwera dla serwera Azure Database for MySQL.
2. Wyszukaj `init_connect`
3. Dodaj parametry serwera w formacie: `SET parameter_name=YOUR_DESIRED_VALUE` wartość w kolumnie wartość.

    Na przykład można zmienić zestaw znaków serwera, ustawiając wartość `init_connect` na `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. Kliknij pozycję **Zapisz**, aby zapisać zmiany.

>[!Note]
> Polecenie `init_connect` może służyć do zmieniania parametrów, które nie wymagają uprawnień administratora na poziomie sesji. Aby sprawdzić, czy można ustawić parametr przy użyciu polecenia `init_connect`, wykonaj polecenie `set session parameter_name=YOUR_DESIRED_VALUE;`, a jeśli zwraca ono błąd **Odmowa dostępu; wymagane są uprawnienia administratora**, to nie można ustawić parametru przy użyciu polecenia „init_connect”.

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

:::image type="content" source="./media/howto-server-parameters/timezone.png" alt-text="Strona parametrów serwera Azure Portal":::

### <a name="setting-the-session-level-time-zone"></a>Ustawianie strefy czasowej na poziomie sesji

Strefę czasową na poziomie sesji można ustawić, uruchamiając `SET time_zone` polecenie za pomocą narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench. W poniższym przykładzie ustawiono strefę czasową dla strefy czasowej **USA/Pacyfiku** .

```sql
SET time_zone = 'US/Pacific';
```

Zapoznaj się z dokumentacją programu MySQL dla [funkcji daty i godziny](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Następne kroki

- [Biblioteki połączeń dla Azure Database for MySQL](concepts-connection-libraries.md).
