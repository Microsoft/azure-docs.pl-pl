---
title: Jak monitorować pule SQL w programie Synapse Studio
description: Dowiedz się, jak monitorować pule SQL przy użyciu programu Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 63b63526333435387c3ff5b5c9d5599ec851c1a8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467421"
---
# <a name="use-synapse-studio-to-monitor-your-sql-pools"></a>Monitorowanie pul SQL przy użyciu programu Synapse Studio

W programie Synapse Studio można uruchamiać skrypty SQL w pulach SQL w obszarze roboczym.

W tym artykule wyjaśniono, jak monitorować pule SQL, umożliwiając śledzenie stanu i aktywności pul.

## <a name="access-sql-pools-list"></a>Dostęp do listy pul SQL

Aby wyświetlić listę pul SQL w obszarze roboczym, najpierw [Otwórz program Synapse Studio](https://web.azuresynapse.net/) i wybierz obszar roboczy.

![Logowanie do obszaru roboczego](./media/common/login-workspace.png)

Po otwarciu obszaru roboczego wybierz sekcję **monitorowanie** po lewej stronie.

![Wybierz pozycję Monitor Hub](./media/common/left-nav.png)

Wybierz pozycję **Pule SQL** , aby wyświetlić listę pul SQL.

 ![Wybierz pule SQL](./media/how-to-monitor-sql-pools/monitor-hub-nav-sql-pools.png)

## <a name="filter-your-sql-pools"></a>Filtrowanie pul SQL

Listę pul SQL można filtrować według tych, które są interesujące. Filtry w górnej części ekranu umożliwiają określenie pola, do którego chcesz filtrować.

Na przykład można filtrować widok, aby wyświetlić tylko pule SQL zawierające nazwę "salesrecords":

![Przykładowy filtr](./media/how-to-monitor-sql-pools/filter-example.png)

## <a name="view-details-about-a-specific-sql-pool"></a>Wyświetlanie szczegółowych informacji o określonej puli SQL

Aby wyświetlić szczegółowe informacje o jednej z pul SQL, wybierz pulę SQL, aby wyświetlić szczegóły.

![Szczegóły puli SQL](./media/how-to-monitor-sql-pools/sql-pool-details.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat monitorowania uruchomień potoków, zobacz temat [monitorowanie przebiegów w artykule Synapse Studio](how-to-monitor-pipeline-runs.md) . 

Aby uzyskać więcej informacji na temat monitorowania żądań SQL, zobacz artykuł [monitorowanie żądań SQL w programie Synapse Studio](how-to-monitor-sql-requests.md) .