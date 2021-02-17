---
title: Dzienniki — Citus) — Azure Database for PostgreSQL
description: Jak uzyskać dostęp do dzienników bazy danych dla Azure Database for PostgreSQL-Citus
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: ca3cc2873fbc6db72b10c80daecddf1471e30ff4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577059"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Dzienniki w Azure Database for PostgreSQL-ze skalowaniem (Citus)

Dzienniki PostgreSQL są dostępne w każdym węźle grupy serwerów ze skalowaniem (Citus). Dzienniki można przesłać na serwer magazynu lub do usługi analizy. Dzienniki mogą służyć do identyfikowania, rozwiązywania problemów i naprawiania błędów konfiguracji oraz nieoptymalnej wydajności.

## <a name="accessing-logs"></a>Uzyskiwanie dostępu do dzienników

Aby uzyskać dostęp do dzienników PostgreSQL dla koordynatora ze skalowaniem (Citus) lub węzła procesu roboczego, Otwórz węzeł w Azure Portal:

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="Lista węzłów":::

W wybranym węźle otwórz pozycję **Ustawienia diagnostyczne**, a następnie kliknij pozycję **+ Dodaj ustawienie diagnostyczne**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="Przycisk dodawania ustawień diagnostycznych":::

Wybierz nazwę nowych ustawień diagnostycznych i zaznacz pole **PostgreSQLLogs** .  Wybierz, które miejsca docelowe powinny otrzymywać dzienniki.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="Wybieranie dzienników PostgreSQL":::

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do zapytań usługi log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)
- Informacje o [centrach zdarzeń platformy Azure](../event-hubs/event-hubs-about.md)