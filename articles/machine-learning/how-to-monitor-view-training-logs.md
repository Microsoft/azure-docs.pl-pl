---
title: Monitorowanie i przeglądanie dzienników przebiegów w ML & metryki
titleSuffix: Azure Machine Learning
description: Monitoruj eksperymenty z wodą i wyświetlaj metryki uruchamiania z Jupyter widżetów i Azure Machine Learning Studio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 845160d92100a27c32f16eddcd1f36e9e8624e80
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360602"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>Monitorowanie i wyświetlanie dzienników i metryk przebiegów ML

W tym artykule dowiesz się, jak monitorować uruchomienia Azure Machine Learning i przeglądać ich dzienniki. Przed wyświetleniem dzienników należy je najpierw włączyć. Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania w usłudze Azure ml Training](how-to-track-experiments.md).

Dzienniki mogą ułatwić Diagnozowanie błędów i ostrzeżeń lub śledzenie metryk wydajności, takich jak parametry i dokładność modelu. W tym artykule przedstawiono sposób wyświetlania dzienników przy użyciu następujących metod:

> [!div class="checklist"]
> * Monitorowanie przebiegów w Studio
> * Monitoruj przebiegi przy użyciu widżetu Jupyter Notebook
> * Monitoruj zautomatyzowane uruchomienia uczenia maszynowego
> * Wyświetlanie dzienników wyjściowych po zakończeniu
> * Wyświetlanie dzienników wyjściowych w programie Studio

Aby uzyskać ogólne informacje na temat sposobu zarządzania eksperymentami, zobacz [Uruchamianie, monitorowanie i anulowanie przebiegów szkoleniowych](how-to-manage-runs.md).

## <a name="monitor-runs-in-the-studio"></a>Monitorowanie przebiegów w Studio

Aby monitorować przebiegi dla określonego elementu docelowego obliczeń z przeglądarki, wykonaj następujące czynności:

1. W [Azure Machine Learning Studio](https://ml.azure.com/), wybierz obszar roboczy, a następnie wybierz pozycję __Oblicz__ z lewej strony.

1. Wybierz pozycję __klastry szkoleniowe__ , aby wyświetlić listę celów obliczeniowych używanych na potrzeby szkolenia. Następnie wybierz klaster.

    ![Wybierz klaster szkoleniowy](./media/how-to-track-experiments/select-training-compute.png)

1. Wybierz pozycję __uruchomienia__. Zostanie wyświetlona lista przebiegów korzystających z tego klastra. Aby wyświetlić szczegóły określonego uruchomienia, Użyj linku w kolumnie __Run__ . Aby wyświetlić szczegóły eksperymentu, Użyj linku w kolumnie __eksperymenty__ .

    ![Wybierz przebiegi dla klastra szkoleniowego](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Ponieważ docelowe cele obliczeniowe są udostępnionym zasobem, mogą mieć wiele uruchomionych w kolejce lub aktywnych w danym momencie.
    > 
    > Przebieg może zawierać przebiegi podrzędne, więc jedno zadanie szkoleniowe może spowodować powstanie wielu wpisów.

Po zakończeniu przebiegu nie jest on już wyświetlany na tej stronie. Aby wyświetlić informacje o zakończonych przebiegach, odwiedź sekcję __eksperymenty__ w programie Studio i wybierz eksperyment i Uruchom. Aby uzyskać więcej informacji, zobacz sekcję [wyświetlanie metryk dla ukończonych przebiegów](#view-the-experiment-in-the-web-portal).

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Monitorowanie uruchomień przy użyciu widżetu Notes Jupyter

Gdy używasz metody **ScriptRunConfig** do przesyłania przebiegów, możesz obserwować postęp przebiegu przy użyciu [widżetu Jupyter](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py). Podobnie jak przesyłanie przebiegu, widżet jest asynchroniczny i udostępnia aktualizacje na bieżąco co 10–15 sekund aż do zakończenia zadania.

Wyświetl widżet Jupyter podczas oczekiwania na ukończenie przebiegu.
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Zrzut ekranu widżetu Jupyter Notes](./media/how-to-track-experiments/run-details-widget.png)

Możesz również uzyskać link do tego samego ekranu w obszarze roboczym.

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>Monitoruj zautomatyzowane uruchomienia uczenia maszynowego

W przypadku zautomatyzowanych przebiegów uczenia maszynowego w celu uzyskania dostępu do wykresów z poprzedniego przebiegu Zastąp `<<experiment_name>>` odpowiednią nazwą eksperymentu:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Widżet Jupyter Notes dla zautomatyzowanych Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>Pokaż dane wyjściowe po zakończeniu

Gdy korzystasz z **ScriptRunConfig** , możesz użyć, ```run.wait_for_completion(show_output = True)``` Aby pokazać, kiedy szkolenie modelu zostało zakończone. ```show_output```Flaga zapewnia pełne dane wyjściowe. Aby uzyskać więcej informacji, zobacz sekcję ScriptRunConfig, [jak włączyć rejestrowanie](how-to-track-experiments.md#scriptrun-logs).

<a id="queryrunmetrics"></a>
## <a name="query-run-metrics"></a>Metryki uruchamiania zapytania

Możesz wyświetlić metryki modelu przeszkolonego za pomocą ```run.get_metrics()``` . Na przykład można użyć tego przykładu, aby określić najlepszy model, wyszukując model z najniższą wartością błędu kwadratowego (MSE).

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-run-records-in-the-studio"></a>Wyświetlanie rekordów uruchamiania w Studio

W programie [Azure Machine Learning Studio](https://ml.azure.com)można przeglądać ukończone rekordy przebiegu, w tym zarejestrowane metryki.

Przejdź do karty **eksperymenty** . Aby wyświetlić wszystkie uruchomienia w obszarze roboczym w ramach eksperymentów, wybierz kartę **wszystkie uruchomienia** . Możesz przejść do szczegółów przebiegów dla konkretnych eksperymentów, stosując filtr eksperymentów na górnym pasku menu. 

W widoku pojedynczego eksperymentu wybierz kartę **wszystkie eksperymenty** . Na pulpicie nawigacyjnym uruchamiania eksperymentu można zobaczyć śledzone metryki i dzienniki dla każdego przebiegu. 

Przechodzenie do określonego przebiegu w celu wyświetlenia jego danych wyjściowych lub dzienników lub pobranie migawki eksperymentu, aby umożliwić udostępnianie folderu eksperymentu innym osobom.

Możesz również edytować tabelę listy uruchamiania, aby wybrać wiele uruchomień i wyświetlić ostatnią, minimalną lub maksymalną zarejestrowana wartość dla przebiegów. Dostosuj wykresy, aby porównać wartości zarejestrowanej metryk i agregacje w wielu przebiegach.

![Szczegóły uruchamiania w programie Azure Machine Learning Studio](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="format-charts-in-the-studio"></a>Formatowanie wykresów w Studio

Użyj następujących metod w interfejsie API rejestrowania, aby mieć wpływ na program Studio wizualizuje metryki.

|Wartość rejestrowana|Przykładowy kod| Formatowanie w portalu|
|----|----|----|
|Rejestruj tablicę wartości liczbowych| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Wykres liniowy z pojedynczą zmienną|
|Rejestruj pojedynczą wartość liczbową o tej samej nazwie metryki wielokrotnie używanej (na przykład w pętli for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Wykres liniowy z pojedynczą zmienną|
|Rejestruj wiersz z 2 kolumnami numerycznymi wielokrotnie|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Wykres liniowy z dwoma zmiennymi|
|Tabela dzienników z 2 kolumnami liczbowymi|`run.log_table(name='Sine Wave', value=sines)`|Wykres liniowy z dwoma zmiennymi|


## <a name="next-steps"></a>Następne kroki

Spróbuj wykonać następujące kroki, aby dowiedzieć się, jak używać Azure Machine Learning:

* Dowiedz się [, jak śledzić eksperymenty i włączać dzienniki w projektancie Azure Machine Learning](how-to-track-designer-experiments.md).

* Zobacz w samouczku [Trenowanie modelu klasyfikacji obrazów za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md) przykład sposobu rejestrowania najlepszego modelu i wdrożenia go.