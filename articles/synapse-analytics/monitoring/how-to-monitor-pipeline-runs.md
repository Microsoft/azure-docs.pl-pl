---
title: Potok monitora uruchamia usługę Azure Synapse Studio (wersja zapoznawcza)
description: Użyj usługi Azure Synapse Studio do monitorowania przebiegów potoku obszaru roboczego.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 712dc62e29229f03dec12afdf18edbf55667dbdf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430788"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Monitorowanie przebiegów potoku obszaru roboczego za pomocą usługi Azure Synapse Studio

Za pomocą usługi Azure Synapse Analytics można tworzyć złożone potoki, które mogą automatyzować i aranżować działania związane z przenoszeniem danych, transformacją danych i obliczeniami w ramach rozwiązania. Potoki można tworzyć i monitorować za pomocą usługi Azure Synapse Studio (wersja zapoznawcza).

W tym artykule wyjaśniono, jak monitorować przebiegi potoku, co pozwala mieć oko na najnowszy stan, problemy i postęp rurociągów.

## <a name="access-the-list-of-pipeline-runs"></a>Dostęp do listy przebiegów potoku

Aby wyświetlić listę uruchomień potoku w obszarze roboczym, najpierw [otwórz studio Synapse i](https://web.azuresynapse.net/) wybierz obszar roboczy.

![Zaloguj się do obszaru roboczego](./media/common/login-workspace.png)

Po otwarciu obszaru roboczego wybierz sekcję **Monitor** po lewej stronie.

![Wybierz koncentrator Monitor](./media/common/left-nav.png)

Wybierz **Potok uruchamia,** aby wyświetlić listę przebiegów potoku.

![Wybierz przebiegi potoku](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>Filtrowanie przebiegów potoku

Możesz filtrować listę uruchomień potoku do tych, które Cię interesują. Filtry w górnej części ekranu umożliwiają określenie pola, w którym chcesz filtrować.

Na przykład można filtrować widok, aby wyświetlić tylko potok działa dla potoku o nazwie "holiday":

![Przycisk Filtruj](./media/common/filter-button.png)

![Filtr próbki](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>Wyświetlanie szczegółów dotyczących określonego przebiegu potoku

Aby wyświetlić szczegółowe informacje o przebiegu potoku, wybierz uruchomienie potoku. Następnie wyświetl przebiegi działania skojarzone z uruchomieniem potoku. Jeśli potok jest nadal uruchomiony, można monitorować postęp. 
  
## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat monitorowania aplikacji, zobacz [monitor apache aplikacji Spark](how-to-monitor-spark-applications.md) artykułu. 
