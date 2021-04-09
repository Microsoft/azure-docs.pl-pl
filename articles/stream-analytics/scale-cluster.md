---
title: Zmiana rozmiaru klastra Azure Stream Analytics
description: Dowiedz się, jak skalować w górę i w dół rozmiar klastra Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: e469e9bfd13bf8d89084b66e954cb51b27c2ebc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020030"
---
# <a name="resize-an-azure-stream-analytics-cluster"></a>Zmiana rozmiaru klastra Azure Stream Analytics

Pojemność klastra Stream Analytics jest mierzona w jednostkach przesyłania strumieniowego (SU). Wiele zadań może działać równolegle w tym samym klastrze, tak długo, jak suma programu SUs przypisana do wszystkich uruchomionych zadań nie przekracza pojemności klastra.

Pojemność klastra można skalować w górę i w dół w celu dopasowania do rozmiaru obciążeń przesyłania strumieniowego. Skalowanie klastra zabiera czas i częste skalowanie nie jest przeznaczone. Zalecamy planowanie i Inicjowanie obsługi klastra z dokładną liczbą programów SUs, które zamierzasz wykorzystać.

## <a name="change-the-scale-of-your-cluster"></a>Zmiana skali klastra

1. W Azure Portal zlokalizuj i wybierz klaster Stream Analytics.

1. W sekcji **Przegląd** wybierz pozycję **Skala**. Można sprawdzić, ile usług SUs jest przypisanych do klastra. Użyj selektora, aby zwiększyć lub zmniejszyć liczbę usług SUs zgodnie z wymaganiami.

   ![Skaluj klaster](./media/scale-cluster/scale-cluster.png)

Operacja skalowania nie ma wpływu na żadne zadania, które są obecnie uruchomione.

## <a name="next-steps"></a>Następne kroki

Teraz wiesz, jak skalować w górę i w dół klastry Stream Analytics. Następnie możesz uzyskać informacje na temat zarządzania prywatnymi punktami końcowymi i automatycznego skalowania zadań:

* [Zarządzanie prywatnymi punktami końcowymi w klastrze Azure Stream Analytics](private-endpoints.md)
* [Zarządzanie zadaniami w klastrze Azure Stream Analytics](manage-jobs-cluster.md)
