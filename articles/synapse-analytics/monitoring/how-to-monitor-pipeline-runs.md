---
title: Monitorowanie uruchomień potoków przy użyciu programu Synapse Studio
description: Użyj programu Synapse Studio do monitorowania uruchomień potoku obszaru roboczego.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: dbd890117c78274392d5745e0563332371b404c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96452578"
---
# <a name="use-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Monitorowanie uruchomień potoku obszaru roboczego za pomocą programu Synapse Studio

Usługa Azure Synapse Analytics umożliwia tworzenie złożonych potoków, które umożliwiają automatyzację i integrację przenoszenia danych, transformacji danych i działań obliczeniowych w ramach rozwiązania. Możesz tworzyć i monitorować te potoki przy użyciu programu Synapse Studio.

W tym artykule wyjaśniono, jak monitorować uruchomienia potoków, co pozwala na śledzenie najnowszego stanu, problemów i postępu potoku.

## <a name="access-pipeline-runs-list"></a>Lista uruchomień potoku dostępu

Aby wyświetlić listę uruchomień potoków w obszarze roboczym, najpierw [Otwórz program Synapse Studio](https://web.azuresynapse.net/) i wybierz obszar roboczy.

![Logowanie do obszaru roboczego](./media/common/login-workspace.png)

Po otwarciu obszaru roboczego wybierz sekcję **monitorowanie** po lewej stronie.

![Wybierz pozycję Monitor Hub](./media/common/left-nav.png)

Wybierz pozycję **uruchomienia potoku** , aby wyświetlić listę uruchomień potoku.

![Wybierz uruchomienia potoku](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filter-your-pipeline-runs"></a>Filtrowanie uruchomień potoku

Możesz filtrować listę uruchomień potoków do interesujących Cię elementów. Filtry w górnej części ekranu umożliwiają określenie pola, do którego chcesz filtrować.

Na przykład można filtrować widok, aby zobaczyć tylko uruchomienia potoku dla potoku o nazwie "dni wolne":

![Przykładowy filtr](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="view-details-about-a-specific-pipeline-run"></a>Wyświetl szczegóły dotyczące określonego uruchomienia potoku

Aby wyświetlić szczegóły dotyczące uruchomienia potoku, wybierz uruchomienie potoku. Następnie Wyświetl uruchomienia działania skojarzone z uruchomieniem potoku. Jeśli potok nadal działa, można monitorować postęp. 
  
## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o monitorowaniu aplikacji, zobacz artykuł [monitorowanie Apache Spark aplikacji](how-to-monitor-spark-applications.md) . 
