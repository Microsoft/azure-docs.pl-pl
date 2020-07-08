---
title: Monitorowanie uruchomionych potoków Azure Synapse Studio (wersja zapoznawcza)
description: Użyj usługi Azure Synapse Studio do monitorowania przebiegów potoku obszaru roboczego.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 16400d7f292ad5844add3d4a5fc019e84bd27127
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85194896"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Monitorowanie uruchomień potoku obszaru roboczego za pomocą usługi Azure Synapse Studio

Usługa Azure Synapse Analytics umożliwia tworzenie złożonych potoków, które mogą zautomatyzować i zorganizować przenoszenie danych, Przekształcanie danych i działania obliczeniowe w ramach rozwiązania. Możesz tworzyć i monitorować te potoki przy użyciu usługi Azure Synapse Studio (wersja zapoznawcza).

W tym artykule wyjaśniono, jak monitorować uruchomienia potoków, co pozwala na śledzenie najnowszego stanu, problemów i postępu potoku.

## <a name="access-the-list-of-pipeline-runs"></a>Dostęp do listy uruchomień potoku

Aby wyświetlić listę uruchomień potoków w obszarze roboczym, najpierw [Otwórz usługę Azure Synapse Studio](https://web.azuresynapse.net/) i wybierz swój obszar roboczy.

![Logowanie do obszaru roboczego](./media/common/login-workspace.png)

Po otwarciu obszaru roboczego wybierz sekcję **monitorowanie** po lewej stronie.

![Wybierz pozycję Monitor Hub](./media/common/left-nav.png)

Wybierz pozycję **uruchomienia potoku** , aby wyświetlić listę uruchomień potoku.

![Wybierz uruchomienia potoku](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>Filtrowanie uruchomień potoku

Możesz filtrować listę uruchomień potoków do interesujących Cię elementów. Filtry w górnej części ekranu umożliwiają określenie pola, do którego chcesz filtrować.

Na przykład można filtrować widok, aby zobaczyć tylko uruchomienia potoku dla potoku o nazwie "dni wolne":

![Przycisk filtrowania](./media/common/filter-button.png)

![Przykładowy filtr](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>Wyświetlanie szczegółów dotyczących określonego uruchomienia potoku

Aby wyświetlić szczegóły dotyczące uruchomienia potoku, wybierz uruchomienie potoku. Następnie Wyświetl uruchomienia działania skojarzone z uruchomieniem potoku. Jeśli potok nadal działa, można monitorować postęp. 
  
## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o monitorowaniu aplikacji, zobacz artykuł [monitorowanie Apache Spark aplikacji](how-to-monitor-spark-applications.md) . 
