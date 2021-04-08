---
title: Korzystanie z modułu modelu obrazu oceny
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu modelu obrazu wynikowego w Azure Machine Learning do generowania prognoz przy użyciu przeszkolonego modelu obrazu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fe57a9e8ce9b14f7d1346d819965576770afef3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93324886"
---
# <a name="score-image-model"></a>Ocenianie modelu obrazów

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do generowania prognoz przy użyciu przeszkolonego modelu obrazu w danych wejściowych obrazu.

## <a name="how-to-configure-score-image-model"></a>Jak skonfigurować model obrazu oceny

1. Dodaj moduł **modelu obrazu oceny** do potoku.

2. Dołącz model obrazu przeszkolony i zestaw danych zawierający dane wejściowe obrazu. 

    Dane powinny być typu ImageDirectory. Aby uzyskać więcej informacji o tym, jak uzyskać katalog obrazu, zapoznaj się z artykułem [konwertowanie do modułu usługi Image Directory](convert-to-image-directory.md) . Schemat wejściowego zestawu danych powinien również zwykle odpowiadać schematowi danych używanych do uczenia modelu.

3. Prześlij potok.

## <a name="results"></a>Wyniki

Po wygenerowaniu zestawu wyników przy użyciu [modelu obrazu oceny](score-image-model.md)w celu wygenerowania zestawu metryk używanych do oceny dokładności modelu (wydajność) można połączyć ten moduł z obliczanym zestawem danych w celu [oceny modelu](evaluate-model.md), 

### <a name="publish-scores-as-a-web-service"></a>Publikowanie wyników jako usługi sieci Web

Typowym użyciem oceniania jest zwrócenie danych wyjściowych w ramach predykcyjnej usługi sieci Web. Aby uzyskać więcej informacji, zobacz [ten samouczek](../tutorial-designer-automobile-price-deploy.md) dotyczący wdrażania punktu końcowego w czasie rzeczywistym na podstawie potoku w programie Azure Machine Learning Designer.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning.