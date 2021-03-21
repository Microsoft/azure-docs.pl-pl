---
title: Jak monitorować żądania SQL w programie Synapse Studio
description: Dowiedz się, jak monitorować żądania SQL przy użyciu programu Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 7296fec91decaf1bd80829f9f3a743a518fbb7a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467404"
---
# <a name="use-synapse-studio-to-monitor-your-sql-requests"></a>Monitorowanie żądań SQL przy użyciu programu Synapse Studio

W programie Synapse Studio można uruchamiać skrypty SQL w pulach SQL w obszarze roboczym.

W tym artykule wyjaśniono, jak monitorować żądania SQL, dzięki czemu można śledzić stan uruchomionych żądań i odnajdywać szczegóły żądań historycznych.

## <a name="access-sql-requests-list"></a>Dostęp do listy żądań SQL

Aby wyświetlić listę żądań SQL w obszarze roboczym, najpierw [Otwórz program Synapse Studio](https://web.azuresynapse.net/) i wybierz obszar roboczy.

![Logowanie do obszaru roboczego](./media/common/login-workspace.png)

Po otwarciu obszaru roboczego wybierz sekcję **monitorowanie** po lewej stronie.

![Wybierz pozycję Monitor Hub](./media/common/left-nav.png)

Wybierz pozycję **żądania SQL** , aby wyświetlić listę żądań SQL.

 ![Wybierz żądania SQL](./media/how-to-monitor-sql-requests/monitor-hub-nav-sql-requests.png)

## <a name="select-a-sql-pool"></a>Wybierz pulę SQL

Domyślnie w tym widoku przedstawiono historię żądań SQL dla wbudowanej puli SQL bezserwerowej. Możesz wybrać jedną z dedykowanych pul SQL, aby wyświetlić historię żądań SQL tej puli.

![Wybierz pulę SQL](./media/how-to-monitor-sql-requests/select-pool.png)

## <a name="filter-your-sql-requests"></a>Filtrowanie żądań SQL

Możesz filtrować listę żądań SQL, które są im interesujące. Filtry w górnej części ekranu umożliwiają określenie pola, do którego chcesz filtrować.

Na przykład można filtrować widok, aby wyświetlić tylko żądania SQL przesłane przez `maria@contoso.com` :

![Przykładowy filtr](./media/how-to-monitor-sql-requests/filter-example.png)

## <a name="view-details-about-a-specific-sql-request"></a>Wyświetl szczegóły dotyczące określonego żądania SQL

Aby wyświetlić szczegóły jednego z żądań SQL, Otwórz żądanie SQL, aby przejść do widoku szczegółów. W przypadku złożonych żądań uruchomionych w dedykowanych pulach SQL można monitorować postęp.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat monitorowania uruchomień potoków, zobacz temat [monitorowanie przebiegów w artykule Synapse Studio](how-to-monitor-pipeline-runs.md) . 

Aby uzyskać więcej informacji na temat monitorowania Apache Spark aplikacji, zobacz artykuł [monitorowanie Apache Spark aplikacji w programie Synapse Studio](how-to-monitor-spark-applications.md) .