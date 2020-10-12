---
title: Monitorowanie uruchomień potoków przy użyciu programu Synapse Studio
description: Użyj programu Synapse Studio do monitorowania uruchomień potoku obszaru roboczego.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 7676f4aeeb9485ce5e3a702027884bc54d79a863
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87387323"
---
# <a name="use-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Monitorowanie uruchomień potoku obszaru roboczego za pomocą programu Synapse Studio

Usługa Azure Synapse Analytics umożliwia tworzenie złożonych potoków, które mogą zautomatyzować i zorganizować przenoszenie danych, Przekształcanie danych i działania obliczeniowe w ramach rozwiązania. Możesz tworzyć i monitorować te potoki przy użyciu programu Synapse Studio (wersja zapoznawcza).

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

![Przycisk filtrowania](./media/common/filter-button.png)

![Przykładowy filtr](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="view-details-about-a-specific-pipeline-run"></a>Wyświetl szczegóły dotyczące określonego uruchomienia potoku

Aby wyświetlić szczegóły dotyczące uruchomienia potoku, wybierz uruchomienie potoku. Następnie Wyświetl uruchomienia działania skojarzone z uruchomieniem potoku. Jeśli potok nadal działa, można monitorować postęp. 
  
## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o monitorowaniu aplikacji, zobacz artykuł [monitorowanie Apache Spark aplikacji](how-to-monitor-spark-applications.md) . 
