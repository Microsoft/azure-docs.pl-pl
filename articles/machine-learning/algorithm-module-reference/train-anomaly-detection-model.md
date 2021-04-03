---
title: 'Model wykrywania anomalii szkolenia: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć szkolony model wykrywania anomalii przy użyciu modułu wykrywania anomalii w modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: edf35fada4233fbe43bc7f859c2414bfb8130714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90905732"
---
# <a name="train-anomaly-detection-model-module"></a>Moduł wykrywania anomalii szkolenia

W tym artykule opisano, jak za pomocą modułu wykrywania anomalii w programie Azure Machine Learning Designer utworzyć szkolony model wykrywania anomalii.

Moduł przyjmuje jako wejściowy zestaw parametrów dla modelu wykrywania anomalii i nieoznaczonego zestawu danych. Zwraca on szkolony model wykrywania anomalii wraz z zestawem etykiet dla danych szkoleniowych.  

Aby uzyskać więcej informacji na temat algorytmów wykrywania anomalii, które znajdują się w projektancie, zobacz [wykrywanie anomalii oparte na partnerstwie](pca-based-anomaly-detection.md).  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Jak skonfigurować model wykrywania anomalii dla uczenia 

1.  Dodaj moduł **wykrywania anomalii** do potoku w projektancie. Ten moduł można znaleźć w kategorii **wykrywania anomalii** .

2. Połącz jeden z modułów przeznaczonych do wykrywania anomalii, takich jak [wykrywanie anomalii oparte na UPW](pca-based-anomaly-detection.md).

    Inne typy modeli nie są obsługiwane. Po uruchomieniu potoku zostanie wyświetlony komunikat o błędzie "wszystkie modele muszą mieć ten sam typ informacji".  

3.  Skonfiguruj moduł wykrywania anomalii, wybierając kolumnę etykieta i ustawiając inne parametry specyficzne dla algorytmu.  

4.  Dołączanie zestawu danych szkoleniowych do prawego wejścia do **modelu wykrywania anomalii w szkoleniu**.  

5.  Prześlij potok.  

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby wyświetlić parametry modelu, kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Wizualizuj**. 

+ Aby utworzyć prognozy, użyj modułu [oceny modelu](score-model.md) z nowymi danymi wejściowymi.

+ Aby zapisać migawkę przeszkolonego modelu, wybierz moduł. Następnie wybierz ikonę **zarejestruj zestaw danych** na karcie dane **wyjściowe i dzienniki** na prawym panelu.   

 
## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 

Zobacz [wyjątki i kody błędów dla projektanta,](designer-error-codes.md) aby zapoznać się z listą błędów specyficznych dla modułów projektanta.
'