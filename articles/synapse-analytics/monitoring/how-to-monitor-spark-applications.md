---
title: Jak monitorować aplikacje Apache Spark
description: Monitoruj Apache Spark aplikacje za pomocą usługi Azure Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: e646f1bc1a25f58dd54437cbd77750479ac9ae99
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970907"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Korzystanie z usługi Azure Synapse Studio (wersja zapoznawcza) do monitorowania aplikacji Apache Spark

Za pomocą usługi Azure Synapse Analytics można używać platformy Spark do uruchamiania notesów, zadań i innych rodzajów aplikacji w pulach platformy Spark w Twoim obszarze roboczym.

W tym artykule wyjaśniono, jak monitorować aplikacje Apache Spark, co pozwala na śledzenie najnowszego stanu, problemów i postępu.

## <a name="accessing-the-list-of-apache-spark-applications"></a>Uzyskiwanie dostępu do listy aplikacji Apache Spark

Aby wyświetlić listę aplikacji Apache Spark w obszarze roboczym, najpierw [Otwórz usługę Azure Synapse Studio](https://web.azuresynapse.net/) i wybierz swój obszar roboczy.

![Logowanie do obszaru roboczego](./media/common/login-workspace.png)

Po otwarciu obszaru roboczego wybierz sekcję **monitorowanie** po lewej stronie.

![Wybierz pozycję Monitor Hub](./media/common/left-nav.png)

Wybierz pozycję **Apache Spark aplikacje** , aby wyświetlić listę aplikacji Apache Spark.

 ![Wybierz aplikacje platformy Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-apache-spark-applications"></a>Filtrowanie aplikacji Apache Spark

Możesz filtrować listę aplikacji Apache Spark, które interesują Cię. Filtry w górnej części ekranu umożliwiają określenie pola, do którego chcesz filtrować.

Na przykład można filtrować widok, aby wyświetlić tylko Apache Spark aplikacje, które zawierają nazwę "Sales":

![Przycisk filtrowania](./media/common/filter-button.png)

![Przykładowy filtr](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-apache-spark-application"></a>Wyświetlanie szczegółowych informacji o określonej aplikacji Apache Spark

Aby wyświetlić szczegółowe informacje o jednej z Apache Spark aplikacji, wybierz aplikację Apache Spark i Wyświetl szczegóły. Jeśli aplikacja Apache Spark nadal działa, można monitorować postęp. [Przeczytaj więcej](apache-spark-applications.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat monitorowania przebiegów potoku, zobacz artykuł [Azure Synapse Studio z potokiem monitorowanie](how-to-monitor-pipeline-runs.md) . 

Aby uzyskać więcej informacji na temat debugowania Apache Spark aplikacji, zobacz artykuł [monitorowanie Apache Spark aplikacji w usłudze Azure Synapse Studio](apache-spark-applications.md) .