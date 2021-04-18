---
title: Tworzenie i usuwanie zadań w klastrze Azure Stream Analytics klastra
description: Dowiedz się, jak zarządzać zadaniami Stream Analytics w klastrze Azure Stream Analytics klastra
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 04/16/2021
ms.openlocfilehash: 4a6334d33dea959bdd3704f848e7bc8250b6e7c6
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600727"
---
# <a name="add-and-remove-jobs-in-an-azure-stream-analytics-cluster"></a>Dodawanie i usuwanie zadań w klastrze Azure Stream Analytics klastra

Można uruchomić wiele zadań Azure Stream Analytics w klastrze Stream Analytics klastra. Uruchamianie zadań w klastrze to prosty, dwuetapowy proces: dodanie zadania do klastra i uruchomienie zadania. W tym artykule pokazano, jak dodawać i usuwać zadania z istniejącego klastra. Postępuj zgodnie z przewodnika Szybki [start, Stream Analytics klastra,](create-cluster.md) jeśli jeszcze go nie masz.

## <a name="add-a-stream-analytics-job-to-a-cluster"></a>Dodawanie zadania Stream Analytics do klastra

Do klastrów można dodawać tylko istniejące zadania Stream Analytics zadań. Postępuj zgodnie z przewodnika Szybki [start, aby dowiedzieć się, jak utworzyć zadanie przy](stream-analytics-quick-create-portal.md) użyciu Azure Portal. Po dodaniu zadania do klastra użyj następujących kroków, aby dodać zadanie do klastra.

1. W Azure Portal znajdź i wybierz swój Stream Analytics klastra.

1. W **obszarze Ustawienia** wybierz pozycję Stream Analytics **zadania.** Następnie wybierz pozycję **Dodaj istniejące zadanie.**

1. Wybierz subskrypcję i Stream Analytics, które chcesz dodać do klastra. Do Stream Analytics można dodawać tylko te zadania, które znajdują się w tym samym regionie co klaster.

   ![Dodawanie zadania do klastra](./media/manage-jobs-cluster/add-job.png)

1. Po dodaniu zadania do klastra przejdź do zasobu zadania i [uruchom zadanie](start-job.md#azure-portal). Zadanie zostanie uruchomione w klastrze.

Wszystkie inne operacje, takie jak monitorowanie, alerty i dzienniki diagnostyczne, można wykonać na Stream Analytics zasobu zadania.

## <a name="remove-a-stream-analytics-job-from-a-cluster"></a>Usuwanie zadania Stream Analytics z klastra

Stream Analytics zadania muszą być zatrzymane, zanim będzie można je usunąć z klastra. Jeśli zadanie jest nadal uruchomione, zatrzymaj je przed przejściem do poniższych kroków.

1. Znajdź i wybierz swój Stream Analytics klastra.

1. W **obszarze Ustawienia** wybierz pozycję Stream Analytics **zadania.**

1. Wybierz zadania, które chcesz usunąć z klastra, a następnie wybierz pozycję **Usuń**.

   ![usuwanie zadania z klastra](./media/manage-jobs-cluster/remove-job.png)

   Po usunięciu zadania z klastra Stream Analytics jest ono przywracane do standardowego środowiska wielodostępu.

## <a name="next-steps"></a>Następne kroki

Teraz wiesz, jak dodawać i usuwać zadania w klastrze Azure Stream Analytics klastra. Następnie dowiesz się, jak zarządzać prywatnymi punktami końcowymi i skalować klastry:

* [Skalowanie klastra Azure Stream Analytics klastra](scale-cluster.md)
* [Zarządzanie prywatnymi punktami końcowymi w klastrze Azure Stream Analytics klastra](private-endpoints.md)
