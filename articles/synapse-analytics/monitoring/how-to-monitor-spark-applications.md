---
title: Jak monitorować aplikacje Apache Spark w programie Synapse Studio
description: Dowiedz się, jak monitorować aplikacje Apache Spark przy użyciu programu Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 5f9733866e85d79bdb85b8a24d1878e1169c2479
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586044"
---
# <a name="how-to-use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Jak używać programu Synapse Studio do monitorowania aplikacji Apache Spark

Za pomocą usługi Azure Synapse Analytics można używać platformy Spark do uruchamiania notesów, zadań i innych rodzajów aplikacji w pulach platformy Spark w Twoim obszarze roboczym.

W tym artykule wyjaśniono, jak monitorować aplikacje Apache Spark, co pozwala na śledzenie najnowszego stanu, problemów i postępu.

## <a name="access-apache-spark-applications-list"></a>Lista aplikacji dostępu Apache Spark

Aby wyświetlić listę aplikacji Apache Spark w obszarze roboczym, najpierw [Otwórz program Synapse Studio](https://web.azuresynapse.net/) i wybierz obszar roboczy.

![Logowanie do obszaru roboczego](./media/common/login-workspace.png)

Po otwarciu obszaru roboczego wybierz sekcję **monitorowanie** po lewej stronie.

![Wybierz pozycję Monitor Hub](./media/common/left-nav.png)

Wybierz pozycję **Apache Spark aplikacje** , aby wyświetlić listę aplikacji Apache Spark.

 ![Wybierz aplikacje platformy Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-spark-applications.png)

## <a name="filter-your-apache-spark-applications"></a>Filtrowanie aplikacji Apache Spark

Możesz filtrować listę aplikacji Apache Spark, które interesują Cię. Filtry w górnej części ekranu umożliwiają określenie pola, do którego chcesz filtrować.

Na przykład można filtrować widok, aby wyświetlić tylko Apache Spark aplikacje, które zawierają nazwę "Sales":

![Przykładowy filtr](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-application"></a>Wyświetl szczegóły dotyczące konkretnej aplikacji Apache Spark

Aby wyświetlić szczegółowe informacje o jednej z Apache Spark aplikacji, wybierz aplikację Apache Spark i Wyświetl szczegóły. Jeśli aplikacja Apache Spark nadal działa, można monitorować postęp. [Przeczytaj więcej](apache-spark-applications.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat monitorowania uruchomień potoków, zobacz artykuł [monitorowanie przebiegów Synapse Studio](how-to-monitor-pipeline-runs.md) . 

Aby uzyskać więcej informacji na temat debugowania aplikacji Apache Spark, zobacz artykuł [monitorowanie Apache Spark aplikacji w programie Synapse Studio](apache-spark-applications.md) .