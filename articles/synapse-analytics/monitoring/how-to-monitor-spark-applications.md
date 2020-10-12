---
title: Jak monitorować aplikacje Apache Spark w programie Synapse Studio
description: Dowiedz się, jak monitorować aplikacje Apache Spark przy użyciu programu Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 2b8dbd20e79b9a6f48ca2d39079ebb452a3b46b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530815"
---
# <a name="use-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Użyj programu Synapse Studio (wersja zapoznawcza) do monitorowania aplikacji Apache Spark

Za pomocą usługi Azure Synapse Analytics można używać platformy Spark do uruchamiania notesów, zadań i innych rodzajów aplikacji w pulach platformy Spark w Twoim obszarze roboczym.

W tym artykule wyjaśniono, jak monitorować aplikacje Apache Spark, co pozwala na śledzenie najnowszego stanu, problemów i postępu.

## <a name="access-apache-spark-applications-list"></a>Lista aplikacji dostępu Apache Spark

Aby wyświetlić listę aplikacji Apache Spark w obszarze roboczym, najpierw [Otwórz program Synapse Studio](https://web.azuresynapse.net/) i wybierz obszar roboczy.

![Logowanie do obszaru roboczego](./media/common/login-workspace.png)

Po otwarciu obszaru roboczego wybierz sekcję **monitorowanie** po lewej stronie.

![Wybierz pozycję Monitor Hub](./media/common/left-nav.png)

Wybierz pozycję **Apache Spark aplikacje** , aby wyświetlić listę aplikacji Apache Spark.

 ![Wybierz aplikacje platformy Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filter-your-apache-spark-applications"></a>Filtrowanie aplikacji Apache Spark

Możesz filtrować listę aplikacji Apache Spark, które interesują Cię. Filtry w górnej części ekranu umożliwiają określenie pola, do którego chcesz filtrować.

Na przykład można filtrować widok, aby wyświetlić tylko Apache Spark aplikacje, które zawierają nazwę "Sales":

![Przycisk filtrowania](./media/common/filter-button.png)

![Przykładowy filtr](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-application"></a>Wyświetl szczegóły dotyczące konkretnej aplikacji Apache Spark

Aby wyświetlić szczegółowe informacje o jednej z Apache Spark aplikacji, wybierz aplikację Apache Spark i Wyświetl szczegóły. Jeśli aplikacja Apache Spark nadal działa, można monitorować postęp. [Przeczytaj więcej](apache-spark-applications.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat monitorowania uruchomień potoków, zobacz artykuł [monitorowanie przebiegów Synapse Studio](how-to-monitor-pipeline-runs.md) . 

Aby uzyskać więcej informacji na temat debugowania aplikacji Apache Spark, zobacz artykuł [monitorowanie Apache Spark aplikacji w programie Synapse Studio](apache-spark-applications.md) .