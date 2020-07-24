---
title: Dzienniki — Citus) — Azure Database for PostgreSQL
description: Jak uzyskać dostęp do dzienników bazy danych dla Azure Database for PostgreSQL-Citus
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 4ca64a7793d229e7910fb122fb33996b1ac0c4fe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099045"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Dzienniki w Azure Database for PostgreSQL-ze skalowaniem (Citus)

Dzienniki PostgreSQL są dostępne w każdym węźle grupy serwerów ze skalowaniem (Citus). Dzienniki można przesłać na serwer magazynu lub do usługi analizy. Dzienniki mogą służyć do identyfikowania, rozwiązywania problemów i naprawiania błędów konfiguracji oraz nieoptymalnej wydajności.

## <a name="accessing-logs"></a>Uzyskiwanie dostępu do dzienników

Aby uzyskać dostęp do dzienników PostgreSQL dla koordynatora ze skalowaniem (Citus) lub węzła procesu roboczego, Otwórz węzeł w Azure Portal:

![Lista węzłów](media/howto-hyperscale-logging/choose-node.png)

W wybranym węźle otwórz pozycję **Ustawienia diagnostyczne**, a następnie kliknij pozycję **+ Dodaj ustawienie diagnostyczne**.

![Przycisk dodawania ustawień diagnostycznych](media/howto-hyperscale-logging/diagnostic-settings.png)

Wybierz nazwę nowych ustawień diagnostycznych i zaznacz pole **PostgreSQLLogs** .  Wybierz, które miejsca docelowe powinny otrzymywać dzienniki.

![Wybieranie dzienników PostgreSQL](media/howto-hyperscale-logging/diagnostic-create-setting.png)

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do zapytań usługi log Analytics](/azure/azure-monitor/log-query/get-started-portal)
- Informacje o [centrach zdarzeń platformy Azure](/azure/event-hubs/event-hubs-about)
