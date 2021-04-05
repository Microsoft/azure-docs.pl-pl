---
title: Tworzenie i usuwanie zadań w klastrze Azure Stream Analytics
description: Dowiedz się, jak zarządzać zadaniami Stream Analytics w klastrze Azure Stream Analytics
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 09a67d11ac4daf3e87a50ee2171f1ca49060c5bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018126"
---
# <a name="add-and-remove-jobs-in-an-azure-stream-analytics-cluster"></a>Dodawanie i usuwanie zadań w klastrze Azure Stream Analytics

W klastrze Stream Analytics można uruchamiać wiele zadań Azure Stream Analytics. Uruchomione zadania w klastrze to prosty proces 2-etapowy: Dodaj zadanie do klastra i uruchom zadanie. W tym artykule opisano sposób dodawania i usuwania zadań z istniejącego klastra. Postępuj zgodnie z przewodnikiem Szybki Start, aby [utworzyć klaster Stream Analytics](create-cluster.md) , jeśli go jeszcze nie masz.

## <a name="add-a-stream-analytics-job-to-a-cluster"></a>Dodawanie zadania Stream Analytics do klastra

Do klastrów można dodawać tylko istniejące zadania Stream Analytics. Postępuj zgodnie z przewodnikiem Szybki Start, aby [dowiedzieć się, jak utworzyć zadanie](stream-analytics-quick-create-portal.md) przy użyciu Azure Portal. Gdy masz zadanie, które chcesz dodać do klastra, wykonaj następujące kroki, aby dodać zadanie do klastra.

1. W Azure Portal zlokalizuj i wybierz klaster Stream Analytics.

1. W obszarze **Ustawienia** wybierz pozycję **zadania Stream Analytics**. Następnie wybierz pozycję **Dodaj istniejące zadanie**.

1. Wybierz subskrypcję i zadanie Stream Analytics, które chcesz dodać do klastra. Tylko zadania Stream Analytics, które znajdują się w tym samym regionie, co klaster, można dodać do klastra.

   ![Dodawanie zadania do klastra](./media/manage-jobs-cluster/add-job.png)

1. Po dodaniu zadania do klastra przejdź do zasobu zadania i [Uruchom zadanie](start-job.md#azure-portal). Następnie zadanie zostanie uruchomione w klastrze.

Wszystkie inne operacje, takie jak monitorowanie, alerty i dzienniki diagnostyczne, można wykonać na stronie zasobów zadania Stream Analytics.

## <a name="remove-a-stream-analytics-job-from-a-cluster"></a>Usuwanie zadania Stream Analytics z klastra

Zadania Stream Analytics muszą być w stanie zatrzymania, aby można było je usunąć z klastra. Jeśli zadanie jest nadal uruchomione, Zatrzymaj zadanie przed przejściem do poniższych kroków.

1. Znajdź i wybierz klaster Stream Analytics.

1. W obszarze **Ustawienia** wybierz pozycję **zadania Stream Analytics**.

1. Wybierz zadania, które chcesz usunąć z klastra, a następnie wybierz pozycję **Usuń**.

   ![Usuń zadanie z klastra](./media/manage-jobs-cluster/remove-job.png)

   Gdy zadanie zostanie usunięte z klastra Stream Analytics, powróci do standardowego środowiska wielu dzierżawców.

## <a name="next-steps"></a>Następne kroki

Teraz wiesz, jak dodawać i usuwać zadania w klastrze Azure Stream Analytics. Następnie możesz dowiedzieć się, jak zarządzać prywatnymi punktami końcowymi i skalować swoje klastry:

* [Skalowanie klastra Azure Stream Analytics](scale-cluster.md)
* [Zarządzanie prywatnymi punktami końcowymi w klastrze Azure Stream Analytics](private-endpoints.md)
