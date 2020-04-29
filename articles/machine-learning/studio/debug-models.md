---
title: Debuguj model
titleSuffix: ML Studio (classic) - Azure
description: Jak debugować błędy generowane przez model uczenia i moduły modelu oceny w Azure Machine Learning Studio (klasyczny).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 910e788830ec55b610a9234a8c8ac75dda1ea189
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79218101"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Debuguj model w Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Podczas uruchamiania modelu mogą wystąpić następujące błędy:

* moduł [uczenie modelu][train-model] generuje błąd 
* moduł [modelu oceny][score-model] generuje nieprawidłowe wyniki 

W tym artykule wyjaśniono potencjalne przyczyny tych błędów.


## <a name="train-model-module-produces-an-error"></a>Moduł uczenia modelu generuje błąd

![image1](./media/debug-models/train_model-1.png)

Moduł [uczenie modelu][train-model] oczekuje dwóch danych wejściowych:

1. Typ modelu uczenia maszynowego z kolekcji modeli dostarczonych przez Azure Machine Learning Studio (klasyczny).
2. Dane szkoleniowe z określoną kolumną etykieta, która określa zmienną do przewidywania (w przypadku innych kolumn przyjęto, że są to funkcje).

Ten moduł może generować błąd w następujących przypadkach:

1. Kolumna etykiety jest określona nieprawidłowo. Może się tak zdarzyć, jeśli wybrano więcej niż jedną kolumnę jako etykietę lub wybrano niepoprawny indeks kolumny. Na przykład drugi przypadek stosuje się, jeśli indeks kolumny 30 jest używany z wejściowym zestawem danych, który ma tylko 25 kolumn.

2. Zestaw danych nie zawiera żadnych kolumn funkcji. Na przykład, jeśli wejściowy zestaw danych ma tylko jedną kolumnę, która jest oznaczona jako kolumna etykiety, nie będzie żadnych funkcji, dla których można skompilować model. W takim przypadku moduł [uczenie modelu][train-model] generuje błąd.

3. Wejściowy zestaw danych (funkcje lub etykieta) zawiera nieskończoność jako wartość.

## <a name="score-model-module-produces-incorrect-results"></a>Moduł modelu oceny generuje nieprawidłowe wyniki

![image2](./media/debug-models/train_test-2.png)

W typowym doświadczeniu szkoleniowym/testowym w celu uczenia nadzorowanego moduł [Split Data][split] dzieli oryginalny zestaw danych na dwie części: jedna część jest używana do uczenia modelu, a jedna część jest używana do oceny, jak dobrze przeszkolony model wykonuje. Model przeszkolony jest następnie używany do oceny danych testowych, po upływie którego wyniki są oceniane w celu określenia dokładności modelu.

Moduł [modelu oceny][score-model] wymaga dwóch danych wejściowych:

1. Przeszkolony model danych wyjściowych z modułu [uczenie modelu][train-model] .
2. Zestaw danych oceniania, który różni się od zestawu danych używanego do uczenia modelu.

Istnieje możliwość, że mimo że eksperyment zakończy się pomyślnie, moduł [modelu oceny][score-model] generuje nieprawidłowe wyniki. Kilka scenariuszy może spowodować wystąpienie tego problemu:

1. Jeśli określona etykieta to kategorii, a model regresji jest szkolony na danych, moduł [modelu wynikowego][score-model] będzie generował nieprawidłowe dane wyjściowe. Wynika to z faktu, że regresja wymaga zmiennej ciągłej odpowiedzi. W takim przypadku bardziej odpowiednie byłoby użycie modelu klasyfikacji. 

2. Podobnie, jeśli model klasyfikacji jest przeszkolony na zestawie danych zawierającym liczby zmiennoprzecinkowe w kolumnie etykieta, może to spowodować niepożądane wyniki. Wynika to z faktu, że klasyfikacja wymaga oddzielnej zmiennej odpowiedzi, która zezwala tylko na wartości, które przenoszą zakres na skończoną i niewielki zestaw klas.

3. Jeśli zestaw danych oceniania nie zawiera wszystkich funkcji używanych do uczenia modelu, [model wyniku][score-model] generuje błąd.

4. Jeśli wiersz w zestawie danych oceniania zawiera brakującą wartość lub nieskończoną wartość dla którejkolwiek z jej funkcji, [model wyniku][score-model] nie tworzy żadnych danych wyjściowych odpowiadających temu wierszowi.

5. [Model wynikowy][score-model] może generować identyczne dane wyjściowe dla wszystkich wierszy w zestawie danych oceniania. Może się to zdarzyć na przykład podczas próby klasyfikacji przy użyciu lasów decyzyjnych, jeśli minimalna liczba próbek na węzeł liścia została wybrana jako większa niż liczba dostępnych przykładów szkoleniowych.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

