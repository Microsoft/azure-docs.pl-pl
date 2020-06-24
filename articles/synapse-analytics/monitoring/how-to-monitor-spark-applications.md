---
title: Monitorowanie aplikacji Apache Spark
description: Monitoruj Apache Spark aplikacje za pomocą usługi Azure Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 0e3b3178cd7600d21032fb8dd4b79dd41ec3f601
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194879"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Korzystanie z usługi Azure Synapse Studio (wersja zapoznawcza) do monitorowania aplikacji Apache Spark

Za pomocą usługi Azure Synapse Analytics można używać platformy Spark do uruchamiania notesów, zadań i innych rodzajów aplikacji w pulach platformy Spark w Twoim obszarze roboczym.

W tym artykule wyjaśniono, jak monitorować aplikacje platformy Spark, co pozwala śledzić najnowsze informacje o stanie, problemach i postępie.

## <a name="accessing-the-list-of-spark-applications"></a>Uzyskiwanie dostępu do listy aplikacji platformy Spark

Aby wyświetlić listę aplikacji platformy Spark w obszarze roboczym, najpierw [Otwórz usługę Azure Synapse Studio](https://web.azuresynapse.net/) i wybierz swój obszar roboczy.

  > [!div class="mx-imgBorder"]
  > ![Logowanie do obszaru roboczego](./media/common/login-workspace.png)

Po otwarciu obszaru roboczego wybierz sekcję **monitorowanie** po lewej stronie.

  > [!div class="mx-imgBorder"]
  > ![Wybierz pozycję Monitor Hub](./media/common/left-nav.png)

Wybierz pozycję **aplikacje platformy Spark** , aby wyświetlić listę aplikacji platformy Spark.

  > [!div class="mx-imgBorder"]
  > ![Wybierz aplikacje platformy Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Filtrowanie aplikacji platformy Spark

Możesz filtrować listę aplikacji platformy Spark do interesujących Cię elementów. Filtry w górnej części ekranu umożliwiają określenie pola, do którego chcesz filtrować.

Na przykład można filtrować widok, aby wyświetlić tylko aplikacje platformy Spark, które zawierają nazwę "Sales":

  > [!div class="mx-imgBorder"]
  > ![Przycisk filtrowania](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Przykładowy filtr](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Wyświetlanie szczegółowych informacji o określonej aplikacji platformy Spark

Aby wyświetlić szczegółowe informacje o jednej z aplikacji platformy Spark, wybierz aplikację Spark i Wyświetl szczegóły. Jeśli aplikacja Spark jest nadal uruchomiona, można monitorować postęp.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat monitorowania przebiegów potoku, zobacz artykuł [Azure Synapse Studio z potokiem monitorowanie](how-to-monitor-pipeline-runs.md) .  
