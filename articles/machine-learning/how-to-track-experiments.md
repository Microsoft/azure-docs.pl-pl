---
title: Rejestruj eksperymenty w ML & metryki
titleSuffix: Azure Machine Learning
description: Monitoruj eksperymenty dotyczące platformy Azure ML i monitoruj metryki uruchamiania, aby usprawnić proces tworzenia modelu. Dodaj rejestrowanie do skryptu szkoleniowego i Wyświetl zarejestrowane wyniki przebiegu.  Użyj Run. log, Run. start_logging lub ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/12/2020
ms.custom: seodec18
ms.openlocfilehash: dcd5668fa2c6e1840eed13a9ee0cbd30d8d8a25a
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983248"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Monitoruj uruchomienia eksperymentów i metryki usługi Azure ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Usprawnij proces tworzenia modelu, śledząc eksperymenty i monitorowane metryki. W tym artykule dowiesz się, jak dodać kod rejestrowania do skryptu szkoleniowego, przesłać przebieg eksperymentu, monitorować ten przebieg i sprawdzić wyniki w Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning mogą także rejestrować informacje z innych źródeł podczas szkoleń, takich jak automatyczne uruchomienia uczenia maszynowego lub kontener platformy Docker, który uruchamia zadanie szkoleniowe. Te dzienniki nie są udokumentowane. Jeśli wystąpią problemy i skontaktuje się z działem pomocy technicznej firmy Microsoft, mogą oni korzystać z tych dzienników podczas rozwiązywania problemów.

> [!TIP]
> Informacje przedstawione w tym dokumencie są przeznaczone głównie dla analityków danych i deweloperów, którzy chcą monitorować proces szkolenia modelu. Jeśli jesteś administratorem zainteresowani monitorowaniem użycia zasobów i zdarzeń z usługi Azure Machine Learning, takich jak przydziały, ukończone przebiegi szkoleniowe lub wdrożone wdrożenia modelu, zobacz [Azure Machine Learning monitorowania](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>Dostępne metryki do śledzenia

Następujące metryki można dodać do przebiegu podczas uczenia eksperymentu. Aby wyświetlić bardziej szczegółową listę elementów, które można śledzić w przebiegu, zobacz [dokumentację dotyczącą przebiegu klasy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Typ| Funkcja języka Python | Uwagi|
|----|:----|:----|
|Wartości skalarne |Funkcyjn<br>`run.log(name, value, description='')`<br><br>Przykład:<br>Run. log ("dokładność", 0,95) |Rejestruj wartość liczbową lub ciągu do uruchomienia o podaną nazwę. Zarejestrowanie metryki w przebiegu powoduje, że Metryka jest przechowywana w rekordzie uruchomienia w eksperymentie.  Tę samą metrykę można rejestrować wiele razy w ramach przebiegu, dlatego wynik jest uznawany za wektor tej metryki.|
|Listy|Funkcyjn<br>`run.log_list(name, value, description='')`<br><br>Przykład:<br>Uruchom polecenie. log_list ("dokładności", [0,6, 0,7, 0,87]) | Rejestruj listę wartości do uruchomienia o podanej nazwie.|
|Wiersz|Funkcyjn<br>`run.log_row(name, description=None, **kwargs)`<br>Przykład:<br>Uruchom polecenie. log_row ("t over X", X = 1, Y = 0.4) | Za pomocą *log_row* tworzy metrykę z wieloma kolumnami, zgodnie z opisem w kwargs. Każdy nazwany parametr generuje kolumnę o określonej wartości.  *log_row* można wywołać raz, aby zarejestrować dowolną krotkę lub wiele razy w pętli, aby wygenerować kompletną tabelę.|
|Tabela|Funkcyjn<br>`run.log_table(name, value, description='')`<br><br>Przykład:<br>Uruchom polecenie. log_table ("t over X", {"X": [1, 2, 3], "t": [0,6, 0,7, 0,89]}) | Rejestruj obiekt słownika do uruchomienia o podaną nazwę. |
|Obrazy|Funkcyjn<br>`run.log_image(name, path=None, plot=None)`<br><br>Przykład:<br>`run.log_image("ROC", plot=plt)` | Rejestruj obraz do rekordu uruchomienia. Użyj log_image, aby zarejestrować. Plik obrazu PNG lub wykres matplotlib do uruchomienia.  Te obrazy będą widoczne i porównywalne w rekordzie uruchomienia.|
|Oznacz przebieg|Funkcyjn<br>`run.tag(key, value=None)`<br><br>Przykład:<br>Run. tag ("selected", "yes") | Oznacz przebieg z kluczem ciągu i opcjonalną wartością ciągu.|
|Przekaż plik lub katalog|Funkcyjn<br>`run.upload_file(name, path_or_stream)`<br> <br> Przykład:<br>Uruchom polecenie. upload_file ("best_model. PKL", "./model.PKL") | Przekaż plik do rekordu uruchomienia. Uruchamia Automatyczne przechwytywanie pliku w określonym katalogu wyjściowym, który domyślnie przyjmuje wartość "./Outputs" dla większości typów uruchomienia.  Użyj upload_file tylko wtedy, gdy trzeba przekazać dodatkowe pliki lub nie określono katalogu wyjściowego. Zalecamy dodanie `outputs` do nazwy, aby została przekazana do katalogu danych wyjściowych. Wszystkie pliki, które są skojarzone z tym rekordem uruchamiania, można wyświetlić według nazwy`run.get_file_names()`|

> [!NOTE]
> Metryki dla skalarnych, list, wierszy i tabel mogą mieć typ: float, Integer lub String.

## <a name="choose-a-logging-option"></a>Wybierz opcję rejestrowania

Jeśli chcesz śledzić lub monitorować eksperyment, musisz dodać kod, aby rozpocząć rejestrowanie podczas przesyłania przebiegu. Poniżej przedstawiono sposoby wyzwalania przesłania przebiegu:
* __Uruchom. start_logging__ — Dodaj funkcje rejestrowania do skryptu szkoleniowego i Rozpocznij interaktywną sesję rejestrowania w określonym doświadczeniu. **start_logging** tworzy interaktywny przebieg do użycia w scenariuszach takich jak notesy. Wszystkie metryki, które są rejestrowane w trakcie sesji są dodawane do rekordu uruchomienia w eksperymentie.
* __ScriptRunConfig__ — Dodaj funkcje rejestrowania do skryptu szkoleniowego i Załaduj cały folder skryptu z przebiegiem.  **ScriptRunConfig** jest klasą do konfigurowania konfiguracji do uruchamiania skryptów. Za pomocą tej opcji można dodać kod monitorowania, aby otrzymywać powiadomienia o zakończeniu lub aby wyświetlić widżet wizualny do monitorowania.

## <a name="set-up-the-workspace"></a>Konfigurowanie obszaru roboczego
Przed dodaniem rejestrowania i przesłaniem eksperymentu należy skonfigurować obszar roboczy.

1. Załaduj obszar roboczy. Aby dowiedzieć się więcej na temat ustawiania konfiguracji obszaru roboczego, zobacz [plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).

[! Notes — Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb? Name = load_ws)]


## <a name="option-1-use-start_logging"></a>Opcja 1: Użyj start_logging

**start_logging** tworzy interaktywny przebieg do użycia w scenariuszach takich jak notesy. Wszystkie metryki, które są rejestrowane w trakcie sesji są dodawane do rekordu uruchomienia w eksperymentie.

Poniższy przykład pociągnie prostego modelu pierścienia skryptu sklearn lokalnie w lokalnym notesie Jupyter. Aby dowiedzieć się więcej o przesyłaniu eksperymentów do różnych środowisk, zobacz [Konfigurowanie celów obliczeniowych na potrzeby szkolenia modeli przy użyciu Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets).

### <a name="load-the-data"></a>Ładowanie danych

W tym przykładzie używa zestawu danych cukrzycą, dobrze znanego małego zestawu danych, który jest dostarczany z scikit. Ta komórka ładuje zestaw danych i dzieli go na losowe szkolenia i zestawy testów.

[! Notes — Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb? Name = load_data)]

### <a name="add-tracking"></a>Dodaj śledzenie
Dodaj śledzenie eksperymentu przy użyciu zestawu SDK Azure Machine Learning i przekaż utrwalony model do rekordu przebiegu eksperymentu. Poniższy kod dodaje Tagi, dzienniki i przekazuje plik modelu do przebiegu eksperymentu.

[! Notes — Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb? Name = create_experiment)]

Skrypt kończy się ```run.complete()```znakiem, co oznacza uruchomienie jako ukończone.  Ta funkcja jest zwykle używana w scenariuszach interaktywnego notesu.

## <a name="option-2-use-scriptrunconfig"></a>Opcja 2: korzystanie z ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) jest klasą do konfigurowania konfiguracji do uruchamiania skryptów. Za pomocą tej opcji można dodać kod monitorowania, aby otrzymywać powiadomienia o zakończeniu lub aby wyświetlić widżet wizualny do monitorowania.

Ten przykład rozszerza się w oparciu o podstawowy model pierścieniowy skryptu sklearn z powyższych. Wykonuje proste odwzorowanie parametrów, aby wyrównać wartości alfa modelu do przechwytywania metryk i modeli przeszkolonych w ramach przebiegów w ramach eksperymentu. Przykład jest uruchamiany lokalnie w środowisku zarządzanym przez użytkownika. 

1. Utwórz skrypt `train.py`szkoleniowy.

   [! code-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]

2. Odwołania `train.py` `mylib.py` do skryptów, które umożliwiają uzyskanie listy wartości alfa do użycia w modelu pierścieniowym.

   [! code-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py)] 

3. Skonfiguruj środowisko lokalne zarządzane przez użytkownika.

   [! Notes — Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? Name = user_managed_env)]


4. Prześlij ```train.py``` skrypt do uruchomienia w środowisku zarządzanym przez użytkownika. Ten cały folder skryptu jest przesyłany do szkolenia, łącznie ```mylib.py``` z plikiem.

   [! Notes — Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? Name = SRC)] [! Notes — Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? Name = Run)]




## <a name="manage-a-run"></a>Zarządzanie przebiegiem

W artykule [Uruchamianie, monitorowanie i anulowanie szkoleń zostanie](how-to-manage-runs.md) wyróżnione określone Azure Machine Learning przepływy pracy służące do zarządzania eksperymentami.

## <a name="view-run-details"></a>Wyświetl szczegóły przebiegu

### <a name="view-activequeued-runs-from-the-browser"></a>Wyświetl aktywne/kolejkowane uruchomienia z przeglądarki

Cele obliczeniowe używane do uczenia modeli są zasobem udostępnionym. W związku z tym mogą mieć wiele uruchomionych w kolejce lub aktywnych w danym momencie. Aby zobaczyć przebiegi dla określonego elementu docelowego obliczeń z przeglądarki, wykonaj następujące czynności:

1. W [Azure Machine Learning Studio](https://ml.azure.com/)wybierz swój obszar roboczy, a następnie wybierz pozycję __obliczenia__ z lewej strony.

1. Wybierz pozycję __klastry szkoleniowe__ , aby wyświetlić listę celów obliczeniowych używanych na potrzeby szkolenia. Następnie wybierz klaster.

    ![Wybierz klaster szkoleniowy](./media/how-to-track-experiments/select-training-compute.png)

1. Wybierz pozycję __uruchomienia__. Zostanie wyświetlona lista przebiegów korzystających z tego klastra. Aby wyświetlić szczegóły określonego uruchomienia, Użyj linku w kolumnie __Run__ . Aby wyświetlić szczegóły eksperymentu, Użyj linku w kolumnie __eksperymenty__ .

    ![Wybierz przebiegi dla klastra szkoleniowego](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Przebieg może zawierać przebiegi podrzędne, więc jedno zadanie szkoleniowe może spowodować powstanie wielu wpisów.

Po zakończeniu przebiegu nie jest on już wyświetlany na tej stronie. Aby wyświetlić informacje o zakończonych przebiegach, odwiedź sekcję __eksperymenty__ w programie Studio i wybierz eksperyment i Uruchom. Aby uzyskać więcej informacji, zobacz sekcję [kwerendy przebiegu zapytania](#queryrunmetrics) .

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Widżet uruchamiania z Jupyter notesu
Gdy używasz metody **ScriptRunConfig** do przesyłania przebiegów, możesz obserwować postęp przebiegu za pomocą [widżetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Podobnie jak przesyłanie przebiegu, widżet jest asynchroniczny i udostępnia aktualizacje na bieżąco co 10–15 sekund aż do zakończenia zadania.

1. Wyświetl widżet Jupyter podczas oczekiwania na ukończenie przebiegu.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Zrzut ekranu widżetu Jupyter Notes](./media/how-to-track-experiments/run-details-widget.png)

   Możesz również uzyskać link do tego samego ekranu w obszarze roboczym.

   ```python
   print(run.get_portal_url())
   ```

2. **[Dla zautomatyzowanych przebiegów uczenia maszynowego]** Aby uzyskać dostęp do wykresów z poprzedniego przebiegu. Zamień `<<experiment_name>>` na odpowiednią nazwę eksperymentu:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widżet Jupyter Notes dla zautomatyzowanych Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Aby wyświetlić dalsze szczegóły potoku, kliknij potok, który chcesz zbadać w tabeli, a wykresy będą renderowane w wyskakującym okienku z Azure Machine Learning Studio.

### <a name="get-log-results-upon-completion"></a>Pobieranie wyników dziennika po zakończeniu

Modelowanie szkoleń i monitorowania odbywa się w tle, dzięki czemu można uruchamiać inne zadania podczas oczekiwania. Możesz również poczekać, aż model ukończy szkolenie przed uruchomieniem większej liczby kodów. Gdy korzystasz z **ScriptRunConfig**, możesz użyć ```run.wait_for_completion(show_output = True)``` , aby pokazać, kiedy szkolenie modelu zostało zakończone. ```show_output``` Flaga zapewnia pełne dane wyjściowe. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Metryki uruchamiania zapytania

Możesz wyświetlić metryki modelu przeszkolonego za pomocą ```run.get_metrics()```. Teraz można uzyskać wszystkie metryki, które zostały zarejestrowane w powyższym przykładzie, aby określić najlepszy model.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>Wyświetlanie eksperymentu w obszarze roboczym w programie [Azure Machine Learning Studio](https://ml.azure.com)

Po zakończeniu eksperymentu możesz przejść do rekordu uruchomienia eksperymentu. Możesz uzyskać dostęp do historii z programu [Azure Machine Learning Studio](https://ml.azure.com).

Przejdź do karty eksperymenty i wybierz swój eksperyment. Nastąpi przełączenie do pulpitu nawigacyjnego uruchamiania eksperymentu, w którym można zobaczyć śledzone metryki i wykresy, które są rejestrowane dla każdego przebiegu. W tym przypadku rejestrujemy MSE i wartości alpha.

  ![Szczegóły uruchamiania w programie Azure Machine Learning Studio](./media/how-to-track-experiments/experiment-dashboard.png)

Możesz przejść do określonego przebiegu w celu wyświetlenia jego danych wyjściowych lub dzienników lub pobrać migawkę podanego eksperymentu, aby umożliwić udostępnienie folderu eksperymentu innym osobom.

### <a name="viewing-charts-in-run-details"></a>Wyświetlanie wykresów w szczegółach uruchamiania

Istnieją różne sposoby używania interfejsów API rejestrowania do rejestrowania różnych typów metryk podczas przebiegu i wyświetlania ich jako wykresów w programie Azure Machine Learning Studio.

|Wartość rejestrowana|Przykładowy kod| Wyświetl w portalu|
|----|----|----|
|Rejestruj tablicę wartości liczbowych| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Wykres liniowy z pojedynczą zmienną|
|Rejestruj pojedynczą wartość liczbową o tej samej nazwie metryki wielokrotnie używanej (na przykład w pętli for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Wykres liniowy z pojedynczą zmienną|
|Rejestruj wiersz z 2 kolumnami numerycznymi wielokrotnie|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Wykres liniowy z dwoma zmiennymi|
|Tabela dzienników z 2 kolumnami liczbowymi|`run.log_table(name='Sine Wave', value=sines)`|Wykres liniowy z dwoma zmiennymi|


## <a name="example-notebooks"></a>Przykładowe notesy
W poniższych notesach przedstawiono Koncepcje opisane w tym artykule:
* [jak to zrobić — Azure/szkolenia/uczenie w obrębie notesu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [Jak używać — Azure/szkolenia/uczenie lokalne](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [Jak korzystać z programu — usługi Azure/śledzenia i monitorowania — eksperymenty/rejestrowanie — interfejs API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

Skorzystaj z poniższych następnych kroków, aby dowiedzieć się, jak używać zestawu SDK usługi Azure Machine Learning dla języka Python:

* Zapoznaj się z przykładem sposobu rejestracji najlepszego modelu i wdrożenia go w samouczku, [uczenie modelu klasyfikacji obrazów przy użyciu Azure Machine Learning](tutorial-train-models-with-aml.md).

* Dowiedz się, jak [uczenie modeli PyTorch za pomocą Azure Machine Learning](how-to-train-pytorch.md).
