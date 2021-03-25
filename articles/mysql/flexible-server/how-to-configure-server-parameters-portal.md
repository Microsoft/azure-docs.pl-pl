---
title: Konfigurowanie parametrów serwera — Azure Portal-Azure Database for MySQL elastyczny serwer
description: W tym artykule opisano sposób konfigurowania parametrów serwera MySQL w Azure Database for MySQL elastycznym serwerze przy użyciu Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 618862e12bd62fbe37ef5e621c89babd7942c04b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105106959"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Konfigurowanie parametrów serwera w Azure Database for MySQL-elastycznym serwerze przy użyciu Azure Portal

> [!IMPORTANT] 
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Za pomocą parametrów serwera można zarządzać Azure Database for MySQL elastyczną konfiguracją serwera. Parametry serwera są skonfigurowane z domyślną i zalecaną wartością podczas tworzenia serwera.  

W tym artykule opisano sposób wyświetlania i konfigurowania parametrów serwera przy użyciu Azure Portal. Blok parametrów serwera na Azure Portal pokazuje zarówno parametr serwera modyfikowalny, jak i niemodyfikowalny. Niemodyfikowalne parametry serwera są wyszarzone.

>[!Note]
> Parametry serwera można aktualizować globalnie na poziomie serwera przy użyciu [interfejsu wiersza polecenia platformy Azure](./how-to-configure-server-parameters-cli.md) lub [Azure Portal](./how-to-configure-server-parameters-portal.md).

## <a name="configure-server-parameters"></a>Konfigurowanie parametrów serwera

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie Znajdź Azure Database for MySQL elastyczny serwer.
2. W sekcji **Ustawienia** kliknij pozycję **parametry serwera** , aby otworzyć stronę parametry serwera dla Azure Database for MySQL elastycznego serwera.
[:::image type="content" source="./media/how-to-server-parameters/azure-portal-server-parameters.png" alt-text="Strona parametrów serwera Azure Portal":::](./media/how-to-server-parameters/azure-portal-server-parameters.png#lightbox)
3. Zlokalizuj dowolny parametr serwera, który ma zostać dostosowany. Przejrzyj kolumnę **Description** , aby zrozumieć przeznaczenie i dozwolone wartości.
[:::image type="content" source="./media/how-to-server-parameters/3-toggle-parameter.png" alt-text="Wyliczenie listy rozwijanej":::](./media/how-to-server-parameters/3-toggle-parameter.png#lightbox)
4. Kliknij przycisk  **Zapisz** , aby zapisać zmiany.
[:::image type="content" source="./media/how-to-server-parameters/4-save-parameters.png" alt-text="Zapisz lub Odrzuć zmiany":::](./media/how-to-server-parameters/4-save-parameters.png#lightbox)
5. Parametry statyczne to te, które wymagają ponownego uruchomienia serwera. Jeśli modyfikujesz parametr statyczny, zostanie wyświetlony monit o **ponowne uruchomienie komputera** lub **ponowne uruchomienie systemu później**.
[:::image type="content" source="./media/how-to-server-parameters/5-save-parameter.png" alt-text="Uruchom ponownie przy zapisywaniu parametru statycznego":::](./media/how-to-server-parameters/5-save-parameter.png#lightbox)
6. Jeśli Zapisano nowe wartości parametrów, zawsze możesz przywrócić wszystkie elementy z powrotem do wartości domyślnych, wybierając pozycję **Zresetuj wszystkie do domyślnych**.
[:::image type="content" source="./media/how-to-server-parameters/6-reset-parameters.png" alt-text="Zresetuj wszystkie do domyślnych":::](./media/how-to-server-parameters/6-reset-parameters.png#lightbox)

## <a name="setting-non-modifiable-server-parameters"></a>Ustawianie niemodyfikowalnych parametrów serwera

Jeśli parametr serwera, który chcesz zaktualizować, nie jest modyfikowalny, możesz opcjonalnie ustawić parametr na poziomie połączenia przy użyciu `init_connect` . Powoduje to ustawienie parametrów serwera dla każdego klienta łączącego się z serwerem. 

1. W sekcji **Ustawienia** kliknij pozycję **parametry serwera** , aby otworzyć stronę parametry serwera dla serwera Azure Database for MySQL.
2. Wyszukaj `init_connect`
3. Dodaj parametry serwera w formacie: `SET parameter_name=YOUR_DESIRED_VALUE` wartość w kolumnie wartość.

    Na przykład można zmienić zestaw znaków serwera, ustawiając wartość `init_connect` na `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

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
> Należy ponownie uruchomić serwer, aby upewnić się, że tabele strefy czasowej są prawidłowo wypełnione.<!-- FIX ME To restart the server, use the [Azure portal](how-to-restart-server-portal.md) or [CLI](how-to-restart-server-cli.md).-->

Aby wyświetlić dostępne wartości strefy czasowej, uruchom następujące polecenie:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ustawianie strefy czasowej na poziomie globalnym

Strefę czasową na poziomie globalnym można ustawić na stronie **parametrów serwera** w Azure Portal. Poniżej ustawia globalną strefę czasową na wartość "US/Pacyfik".

[:::image type="content" source="./media/how-to-server-parameters/timezone.png" alt-text="Ustaw parametr strefy czasowej":::](./media/how-to-server-parameters/timezone.png#lightbox)

### <a name="setting-the-session-level-time-zone"></a>Ustawianie strefy czasowej na poziomie sesji

Strefę czasową na poziomie sesji można ustawić, uruchamiając `SET time_zone` polecenie za pomocą narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench. W poniższym przykładzie ustawiono strefę czasową dla strefy czasowej **USA/Pacyfiku** .

```sql
SET time_zone = 'US/Pacific';
```

Zapoznaj się z dokumentacją programu MySQL dla [funkcji daty i godziny](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Następne kroki

- Jak skonfigurować [parametry serwera w interfejsie wiersza polecenia platformy Azure](./how-to-configure-server-parameters-cli.md)
