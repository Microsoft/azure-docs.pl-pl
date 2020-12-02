---
title: Jak monitorować pule Apache Spark w programie Synapse Studio
description: Dowiedz się, jak monitorować pule Apache Spark przy użyciu programu Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 8d95897e0c2d58b2a3955918be945800eed9ba56
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467244"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-pools"></a>Monitorowanie pul Apache Spark przy użyciu programu Synapse Studio

Usługa Azure Synapse Analytics umożliwia uruchamianie notesów, zadań i innych rodzajów aplikacji w pulach platformy Spark w obszarze roboczym.

W tym artykule wyjaśniono, jak monitorować pule Apache Spark, co pozwala śledzić stan pul, w tym liczbę rdzeni wirtualnych używanych przez różnych użytkowników obszaru roboczego.

## <a name="access-spark-pools-list"></a>Lista dostępu do pul platformy Spark

Aby wyświetlić listę pul Apache Spark w obszarze roboczym, najpierw [Otwórz program Synapse Studio](https://web.azuresynapse.net/) i wybierz obszar roboczy.

![Logowanie do obszaru roboczego](./media/common/login-workspace.png)

Po otwarciu obszaru roboczego wybierz sekcję **monitorowanie** po lewej stronie.

![Wybierz pozycję Monitor Hub](./media/common/left-nav.png)

Wybierz **pule Apache Spark** , aby wyświetlić listę pul Apache Spark.

 ![Wybierz pule Apache Spark](./media/how-to-monitor-spark-pools/monitor-hub-nav-spark-pools.png)

## <a name="filter-your-spark-pools"></a>Filtrowanie pul platformy Spark

Listę pul platformy Spark można filtrować według tych, które są interesujące. Filtry w górnej części ekranu umożliwiają określenie pola, do którego chcesz filtrować.

Na przykład można filtrować widok, aby wyświetlić tylko pule platformy Spark zawierające nazwę "dataprzygotowanie":

![Przykładowy filtr](./media/how-to-monitor-spark-pools/filter-example.png)

## <a name="view-details-about-a-specific-spark-pool"></a>Wyświetl szczegóły dotyczące określonej puli platformy Spark

Aby wyświetlić szczegółowe informacje o jednej z pul platformy Spark, wybierz pulę platformy Spark, aby wyświetlić szczegóły.

![Szczegóły puli Apache Spark](./media/how-to-monitor-spark-pools/spark-pool-details.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat monitorowania uruchomień potoków, zobacz temat [monitorowanie przebiegów w artykule Synapse Studio](how-to-monitor-pipeline-runs.md) . 

Aby uzyskać więcej informacji na temat monitorowania Apache Spark aplikacji, zobacz artykuł [monitorowanie Apache Spark aplikacji w programie Synapse Studio](how-to-monitor-spark-applications.md) .
