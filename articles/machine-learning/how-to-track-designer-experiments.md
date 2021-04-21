---
title: Metryki dziennika w projektancie
titleSuffix: Azure Machine Learning
description: Monitorowanie eksperymentów projektanta usługi Azure ML. Włącz rejestrowanie przy użyciu modułu Execute Python Script (Wykonywanie skryptu języka Python) i wyświetl zarejestrowane wyniki w studio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 01/11/2021
ms.topic: conceptual
ms.custom: designer
ms.openlocfilehash: 13a3b86514428b0219aaf671260c07b4e197d2de
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817315"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>Włączanie rejestrowania w Azure Machine Learning potokach projektanta


Z tego artykułu dowiesz się, jak dodać kod rejestrowania do potoków projektanta. Dowiesz się również, jak wyświetlać te dzienniki przy użyciu Azure Machine Learning studio portalu internetowego.

Aby uzyskać więcej informacji na temat rejestrowania metryk przy użyciu środowiska tworzenia zestawu SDK, zobacz Monitorowanie przebiegów i metryk eksperymentów [usługi Azure ML.](how-to-log-view-metrics.md)

## <a name="enable-logging-with-execute-python-script"></a>Włączanie rejestrowania za pomocą polecenia Wykonaj skrypt języka Python

Użyj modułu [Execute Python Script (Wykonywanie skryptu języka Python),](./algorithm-module-reference/execute-python-script.md) aby włączyć rejestrowanie w potokach projektanta. Mimo że za pomocą tego przepływu pracy można rejestrować dowolną wartość, szczególnie przydatne jest rejestrowanie metryk z modułu __Evaluate Model__ (Ocena modelu) w celu śledzenia wydajności modelu między przebiegami.

W poniższym przykładzie pokazano, jak rejestrować błąd średniego kwadratu dwóch wytrenowany model przy użyciu modułów Evaluate Model (Ocena modelu) i Execute Python Script (Wykonaj skrypt języka Python).

1. Połącz moduł __Execute Python Script (Wykonywanie__ skryptu języka Python) z wyjściem modułu Evaluate Model __(Ocena__ modelu).

    ![Łączenie modułu Execute Python Script (Wykonywanie skryptu języka Python) w celu oceny modelu](./media/how-to-log-view-metrics/designer-logging-pipeline.png)

1. Wklej następujący kod w edytorze kodu Execute Python Script (Wykonywanie skryptu języka __Python),__ aby rejestrować średni bezwzględny błąd dla wytrenowany model. Podobny wzorzec umożliwia rejestrowanie dowolnej innej wartości w projektancie:

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
    
Ten kod używa zestawu SDK Azure Machine Learning Python do rejestrować wartości. Używa on Run.get_context() do uzyskania kontekstu bieżącego uruchomienia. Następnie rejestruje wartości w tym kontekście za pomocą metody run.parent.log(). Używa do `parent` rejestrować wartości w potoku nadrzędnym, a nie uruchomieniu modułu.

Aby uzyskać więcej informacji na temat rejestrowania wartości przy użyciu zestawu SDK języka Python, zobacz Enable logging in Azure ML training runs (Włączanie rejestrowania w przebiegach [trenowania usługi Azure ML).](how-to-log-view-metrics.md)

## <a name="view-logs"></a>Wyświetlanie dzienników

Po zakończeniu uruchamiania potoku na stronie *Experiments (Eksperymenty) będzie Mean_Absolute_Error* lista.

1. Przejdź do sekcji **Experiments (Eksperymenty).**
1. Wybierz eksperyment.
1. Wybierz przebieg w eksperymencie, który chcesz wyświetlić.
1. Wybierz pozycję **Metryki**.

    ![Wyświetlanie metryk uruchamiania w programie Studio](./media/how-to-log-view-metrics/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób używania dzienników w projektancie. Aby uzyskać więcej informacji na temat następnych kroków, zobacz następujące powiązane artykuły:


* Dowiedz się, jak rozwiązywać problemy z potokami projektanta, [zobacz Debugowanie & rozwiązywanie problemów z potokami uczenia maszynowego.](how-to-debug-pipelines.md#azure-machine-learning-designer)
* Dowiedz się, jak używać zestawu SDK języka Python do rejestrowania metryk w środowisku tworzenia zestawu SDK. Zobacz Włączanie rejestrowania [w przebiegach trenowania usługi Azure ML.](how-to-log-view-metrics.md)
* Dowiedz się, jak używać [polecenia Wykonaj skrypt języka Python](./algorithm-module-reference/execute-python-script.md) w projektancie.
