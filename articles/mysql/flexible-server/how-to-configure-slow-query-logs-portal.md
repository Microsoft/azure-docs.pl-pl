---
title: Konfigurowanie dzienników wolnych zapytań — Azure Portal-Azure Database for MySQL-elastyczny serwer
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do dzienników wolnych zapytań w Azure Database for MySQL elastycznym serwerze z Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: a61c8e3451d661dae2e5ad56a0d4a947252ec873
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540067"
---
# <a name="configure-and-access-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Konfigurowanie i uzyskiwanie dostępu do dzienników wolnych zapytań dla Azure Database for MySQL-elastyczny serwer przy użyciu Azure Portal

> [!IMPORTANT]
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Można skonfigurować, wyświetlić i pobrać Azure Database for MySQL elastycznych [dzienników wolnych zapytań](concepts-slow-query-logs.md) serwera z Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne
Kroki opisane w tym artykule wymagają, aby uzyskać [elastyczny serwer](quickstart-create-server-portal.md).

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Skonfiguruj dostęp do dziennika wolnych zapytań programu MySQL. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz swój elastyczny serwer.

1. W sekcji **Ustawienia** na pasku bocznym wybierz opcję **parametry serwera**.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/server-parameters.png" alt-text="Strona parametrów serwera.":::

1. Zaktualizuj parametr **Slow_query_log** na wartość **on**.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/slow-query-log-enable.png" alt-text="Włącz dzienniki wolnych zapytań.":::

1. Zmień wszystkie inne parametry, które są potrzebne (np. `long_query_time`, `log_slow_admin_statements`). Więcej parametrów można znaleźć w dokumentacji [dzienników wolnych zapytań](./concepts-slow-query-logs.md#configure-slow-query-logging) .  
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/long-query-time.png" alt-text="Aktualizowanie parametrów związanych z dziennikiem wolnych zapytań.":::

1. Wybierz pozycję **Zapisz**. 
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-parameters.png" alt-text="Zapisz parametry dziennika wolnych zapytań.":::

Na stronie **parametry serwera** możesz powrócić do listy dzienników, zamykając stronę.

## <a name="set-up-diagnostics"></a>Konfigurowanie diagnostyki

Dzienniki wolnych zapytań są zintegrowane z Azure Monitor ustawień diagnostycznych, aby umożliwić potokom dzienników Azure Monitor dzienników, Event Hubs lub Azure Storage.

1. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Ustawienia diagnostyczne**  >  **Dodaj ustawienia diagnostyczne**.

   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/add-diagnostic-setting.png" alt-text="Zrzut ekranu przedstawiający opcje ustawień diagnostycznych":::

1. Podaj nazwę ustawienia diagnostycznego.

1. Określ, które lokalizacje docelowe mają wysyłać dzienniki wolnych zapytań (konto magazynu, centrum zdarzeń lub Log Analytics obszar roboczy).

1. W polu Typ dziennika wybierz pozycję **MySqlSlowLogs** .
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/configure-diagnostic-setting.png" alt-text="Zrzut ekranu przedstawiający opcje konfiguracji ustawień diagnostycznych":::

1. Po skonfigurowaniu ujścia danych w celu pomyślnego przetworzenia połączeń dzienników wolnych zapytań wybierz pozycję **Zapisz**.
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-diagnostic-setting.png" alt-text="Zrzut ekranu opcji konfiguracji ustawień diagnostycznych z wyróżnioną pozycją Zapisz":::

1. Uzyskaj dostęp do dzienników wolnych zapytań, badając je w skonfigurowanych ujściach danych. Wyświetlenie dzienników może potrwać do 10 minut.

Jeśli dzienniki zostały przekierowane do Azure Monitor dzienników (Log Analytics), zapoznaj się z [przykładowymi zapytaniami](concepts-slow-query-logs.md#analyze-logs-in-azure-monitor-logs) , których można użyć do analizy. 

## <a name="next-steps"></a>Następne kroki
<!-- - See [Access slow query Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download slow query logs programmatically.-->
- Dowiedz się więcej o [wolnych dziennikach zapytań](concepts-slow-query-logs.md)
- Aby uzyskać więcej informacji na temat definicji parametrów i rejestrowania MySQL, zobacz dokumentację programu MySQL w [dzienniku](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).
- Informacje o [dziennikach inspekcji](concepts-audit-logs.md)
