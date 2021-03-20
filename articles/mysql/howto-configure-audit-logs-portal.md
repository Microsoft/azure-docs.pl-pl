---
title: Dzienniki inspekcji dostępu — Azure Portal — Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do dzienników inspekcji w programie Azure Database for MySQL z Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: a73a3c77310c0e19792758c0586975e14cfaebf8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541661"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Skonfiguruj i uzyskaj dostęp do dzienników inspekcji dla Azure Database for MySQL w Azure Portal

Można skonfigurować [Azure Database for MySQL dzienników inspekcji](concepts-audit-logs.md) i ustawień diagnostycznych z Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby krokowo poprowadzić ten przewodnik, musisz:

- [Serwer Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Konfigurowanie rejestrowania inspekcji

>[!IMPORTANT]
> Zaleca się tylko rejestrowanie typów zdarzeń i użytkowników wymaganych do celów inspekcji, aby upewnić się, że wydajność serwera nie jest w dużym stopniu zagrożona.

Włącz i skonfiguruj rejestrowanie inspekcji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz serwer Azure Database for MySQL.

1. W sekcji **Ustawienia** na pasku bocznym wybierz opcję **parametry serwera**.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/server-parameters.png" alt-text="Parametry serwera":::

1. Zaktualizuj parametr **audit_log_enabled** na wartość on.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Włączanie dzienników inspekcji":::

1. Wybierz [typy zdarzeń](concepts-audit-logs.md#configure-audit-logging) , które mają być rejestrowane, aktualizując parametr **audit_log_events** .
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-events.png" alt-text="Zdarzenia dziennika inspekcji":::

1. Należy dodać wszystkich użytkowników programu MySQL, którzy mają zostać dołączeni lub wykluczeni z rejestrowania przez aktualizację parametrów **audit_log_exclude_users** i **audit_log_include_users** . Określ użytkowników, podając ich nazwę użytkownika MySQL.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Dziennik inspekcji — Wyklucz użytkowników":::

1. Po zmianie parametrów możesz kliknąć przycisk **Zapisz**. Możesz też **odrzucić** zmiany.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/save-parameters.png" alt-text="Zapisz":::

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych

1. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Ustawienia diagnostyczne**.

1. Kliknij pozycję "+ Dodaj ustawienie diagnostyczne" :::image type="content" source="./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Dodaj ustawienie diagnostyczne":::

1. Podaj nazwę ustawienia diagnostycznego.

1. Określ, które ujścia danych mają wysyłać dzienniki inspekcji (konto magazynu, centrum zdarzeń i/lub Log Analytics obszar roboczy).

1. Wybierz pozycję "MySqlAuditLogs" jako typ dziennika.
:::image type="content" source="./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Konfiguruj ustawienie diagnostyczne":::

1. Po skonfigurowaniu ujścia danych do potoków dzienników inspekcji do programu można kliknąć przycisk **Zapisz**.
:::image type="content" source="./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Zapisz ustawienia diagnostyczne":::

1. Uzyskaj dostęp do dzienników inspekcji, przepoznając je w skonfigurowanych ujściach danych. Wyświetlenie dzienników może potrwać do 10 minut.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dziennikach inspekcji](concepts-audit-logs.md) w Azure Database for MySQL
- Dowiedz się, jak skonfigurować dzienniki inspekcji w [interfejsie wiersza polecenia platformy Azure](howto-configure-audit-logs-cli.md)