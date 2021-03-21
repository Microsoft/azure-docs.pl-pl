---
title: Konfigurowanie dzienników inspekcji — Azure Portal-Azure Database for MySQL-elastyczny serwer
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do dzienników inspekcji w Azure Database for MySQL elastycznym serwerze z Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: ebb980aa257fc09c3d6a407febbf60f2d1a26a4e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94536476"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Skonfiguruj i uzyskaj dostęp do dzienników inspekcji dla Azure Database for MySQL-elastyczny serwer przy użyciu Azure Portal

> [!IMPORTANT]
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Można skonfigurować Azure Database for MySQL elastycznych [dzienników inspekcji](concepts-audit-logs.md) serwera i ustawień diagnostycznych z Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne
Kroki opisane w tym artykule wymagają, aby uzyskać [elastyczny serwer](quickstart-create-server-portal.md).

## <a name="configure-audit-logging"></a>Konfigurowanie rejestrowania inspekcji

>[!IMPORTANT]
> Zaleca się tylko rejestrowanie typów zdarzeń i użytkowników wymaganych do celów inspekcji, aby upewnić się, że wydajność serwera nie jest w dużym stopniu zagrożona.

Włącz i skonfiguruj rejestrowanie inspekcji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz swój elastyczny serwer.

1. W sekcji **Ustawienia** na pasku bocznym wybierz opcję **parametry serwera**.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/server-parameters.png" alt-text="Parametry serwera":::

1. Zaktualizuj parametr **audit_log_enabled** na wartość on.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Włączanie dzienników inspekcji":::

1. Wybierz [typy zdarzeń](concepts-audit-logs.md#configure-audit-logging) , które mają być rejestrowane, aktualizując parametr **audit_log_events** .
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-events.png" alt-text="Zdarzenia dziennika inspekcji":::

1. Należy dodać wszystkich użytkowników programu MySQL, którzy mają zostać dołączeni lub wykluczeni z rejestrowania przez aktualizację parametrów **audit_log_exclude_users** i **audit_log_include_users** . Określ użytkowników, podając ich nazwę użytkownika MySQL.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Dziennik inspekcji — Wyklucz użytkowników":::

1. Po zmianie parametrów możesz kliknąć przycisk **Zapisz**. Możesz też **odrzucić** zmiany.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-parameters.png" alt-text="Zapisz":::

## <a name="set-up-diagnostics"></a>Konfigurowanie diagnostyki

Dzienniki inspekcji są zintegrowane z Azure Monitor ustawień diagnostycznych, aby umożliwić potokom dzienników Azure Monitor dzienników, Event Hubs lub Azure Storage.

1. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Ustawienia diagnostyczne**.

1. Kliknij pozycję "+ Dodaj ustawienie diagnostyczne"  :::image type="content" source="./media/how-to-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Dodaj ustawienie diagnostyczne":::

1. Podaj nazwę ustawienia diagnostycznego.

1. Określ, które miejsca docelowe mają wysyłać dzienniki inspekcji (konto magazynu, centrum zdarzeń i/lub Log Analytics obszar roboczy).

1. W polu Typ dziennika wybierz pozycję **MySqlAuditLogs** .
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Konfiguruj ustawienie diagnostyczne":::

1. Po skonfigurowaniu ujścia danych do potoków dzienników inspekcji do programu można kliknąć przycisk **Zapisz**.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Zapisz ustawienia diagnostyczne":::

1. Uzyskaj dostęp do dzienników inspekcji, przepoznając je w skonfigurowanych ujściach danych. Wyświetlenie dzienników może potrwać do 10 minut.

Jeśli dzienniki inspekcji zostały potokowe do Azure Monitor dzienników (Log Analytics), zapoznaj się z [przykładowymi zapytaniami](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs) , których można użyć do analizy.  

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dziennikach inspekcji](concepts-audit-logs.md)
- Więcej informacji o [dziennikach wolnych zapytań](concepts-slow-query-logs.md)
<!-- - Learn how to configure audit logs in the [Azure CLI](howto-configure-audit-logs-cli.md)-->