---
title: Dzienniki — Citus) — Azure Database for PostgreSQL
description: Jak uzyskać dostęp do dzienników bazy danych dla Azure Database for PostgreSQL-Citus
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: f8840d5115cb552ed203705d37f8c692b3418947
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492339"
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

- [Wprowadzenie do zapytań usługi log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md)
- Informacje o [centrach zdarzeń platformy Azure](../event-hubs/event-hubs-about.md)