---
title: Monitorowanie aplikacji Apache Spark
description: Użyj usługi Azure Synapse Studio do monitorowania aplikacji Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430749"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Użyj usługi Azure Synapse Studio (wersja zapoznawcza) do monitorowania aplikacji Apache Spark

Za pomocą usługi Azure Synapse Analytics można używać platformy Spark do uruchamiania notesów, zadań i innych aplikacji w pulach platformy Spark w obszarze roboczym.

W tym artykule wyjaśniono, jak monitorować aplikacje Platformy Spark, co pozwala mieć oko na najnowszy stan, problemy i postęp.

## <a name="accessing-the-list-of-spark-applications"></a>Uzyskiwanie dostępu do listy aplikacji Spark

Aby wyświetlić listę aplikacji platformy Spark w obszarze roboczym, najpierw [otwórz studio Usługi Azure Synapse i](https://web.azuresynapse.net/) wybierz obszar roboczy.

  > [!div class="mx-imgBorder"]
  > ![Zaloguj się do obszaru roboczego](./media/common/login-workspace.png)

Po otwarciu obszaru roboczego wybierz sekcję **Monitor** po lewej stronie.

  > [!div class="mx-imgBorder"]
  > ![Wybierz koncentrator Monitor](./media/common/left-nav.png)

Wybierz **aplikacje Platformy Spark,** aby wyświetlić listę aplikacji Platformy Spark.

  > [!div class="mx-imgBorder"]
  > ![Wybierz aplikacje Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Filtrowanie aplikacji Spark

Możesz filtrować listę aplikacji Spark do tych, które Cię interesują. Filtry w górnej części ekranu umożliwiają określenie pola, w którym chcesz filtrować.

Na przykład można filtrować widok, aby wyświetlić tylko aplikacje Spark, które zawierają nazwę "sprzedaż":

  > [!div class="mx-imgBorder"]
  > ![Przycisk Filtruj](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Filtr próbki](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Wyświetlanie szczegółów dotyczących określonej aplikacji Spark

Aby wyświetlić szczegółowe informacje o jednej z aplikacji Spark, wybierz aplikację Spark i wyświetl szczegóły. Jeśli aplikacja Spark jest nadal uruchomiona, można monitorować postęp.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat monitorowania przebiegów potoku, zobacz [potok monitora uruchamia artykuł usługi Azure Synapse Studio.](how-to-monitor-pipeline-runs.md)  
