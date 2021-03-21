---
title: Rejestrowanie metryk w projektancie
titleSuffix: Azure Machine Learning
description: Monitoruj eksperymenty projektanta Azure ML. Włącz rejestrowanie przy użyciu modułu uruchamiania skryptu Python i Wyświetl zarejestrowane wyniki w programie Studio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 01/11/2021
ms.topic: conceptual
ms.custom: designer
ms.openlocfilehash: b940f5c9bd14bcec404827daaef666da802d969b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98065256"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>Włącz rejestrowanie w potokach projektanta Azure Machine Learning


W tym artykule dowiesz się, jak dodać kod rejestrowania do potoków projektanta. Dowiesz się również, jak wyświetlić te dzienniki przy użyciu portalu sieci Web programu Azure Machine Learning Studio.

Aby uzyskać więcej informacji na temat rejestrowania metryk przy użyciu środowiska tworzenia zestawu SDK, zobacz [monitorowanie przebiegów eksperymentów i metryk usługi Azure ml](how-to-track-experiments.md).

## <a name="enable-logging-with-execute-python-script"></a>Włączanie rejestrowania przy użyciu skryptu wykonaj skrypt języka Python

Użyj modułu [skryptu języka Python](./algorithm-module-reference/execute-python-script.md) , aby włączyć rejestrowanie w potokach projektanta. Chociaż można rejestrować dowolną wartość przy użyciu tego przepływu pracy, jest szczególnie przydatne do rejestrowania metryk z modułu __oceny modelu__ do śledzenia wydajności modelu w ramach przebiegów.

W poniższym przykładzie pokazano, jak zarejestrować średni kwadratowy błąd dwóch przeszkolonych modeli przy użyciu modelu oszacowania i wykonywania modułów skryptów języka Python.

1. Połącz moduł __wykonywania skryptu Python__ z danymi wyjściowymi modułu __Oceń model__ .

    ![Połącz moduł skryptu języka Python w celu oszacowania modułu modelu](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. Wklej następujący kod do edytora kodu __skryptu języka Python__ , aby zarejestrować średni błąd bezwzględny dla Twojego przeszkolonego modelu. Możesz użyć podobnego wzorca, aby zarejestrować dowolną inną wartość w projektancie:

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.
        parent_run = Run.get_context().parent
        
        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        parent_run.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])
        # Log right output port result of Evaluate Model. The following line should be deleted if you only connect one Score Module to the` left port of Evaluate Model module.
        parent_run.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])

        return dataframe1,
    ```
    
Ten kod używa Azure Machine Learning Python SDK, aby rejestrować wartości. Używa Run.get_context () do pobrania kontekstu bieżącego przebiegu. Następnie rejestruje wartości w tym kontekście za pomocą metody Run. Parent. log (). Używa `parent` do rejestrowania wartości w nadrzędnym przebiegu potoku, a nie do uruchomienia modułu.

Aby uzyskać więcej informacji na temat rejestrowania wartości przy użyciu zestawu SDK języka Python, zobacz [Włączanie rejestrowania w usłudze Azure ml Training](how-to-track-experiments.md).

## <a name="view-logs"></a>Wyświetlanie dzienników

Po zakończeniu przebiegu potoku można zobaczyć *Mean_Absolute_Error* na stronie eksperymenty.

1. Przejdź do sekcji **eksperymenty** .
1. Wybierz swój eksperyment.
1. Wybierz przebieg w eksperymentie, który chcesz wyświetlić.
1. Wybierz pozycję **Metryki**.

    ![Wyświetl metryki uruchamiania w programie Studio](./media/how-to-track-experiments/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób korzystania z dzienników w projektancie. Aby uzyskać kolejne kroki, zobacz następujące artykuły pokrewne:


* Informacje na temat rozwiązywania problemów z potokami projektanta można znaleźć w temacie [Debug & Rozwiązywanie problemów z potokami ml](how-to-debug-pipelines.md#azure-machine-learning-designer).
* Dowiedz się, jak używać zestawu SDK języka Python do rejestrowania metryk w środowisku tworzenia zestawu SDK, zobacz [Włączanie rejestrowania w usłudze Azure ml szkoleń](how-to-track-experiments.md).
* Dowiedz się, jak używać [skryptu Execute języka Python](./algorithm-module-reference/execute-python-script.md) w projektancie.
