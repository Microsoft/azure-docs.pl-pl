---
title: Wykrywanie dryfowania danych na wdrożeniach AKS
titleSuffix: Azure Machine Learning
description: Wykrywaj dryfy danych (wersja zapoznawcza) w ramach wdrożonych modeli usługi Azure Kubernetes w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: 0f56ab853983ebf9b3e27f38ae1737c0c2bce4ed
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84430298"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Wykrywaj dryfowanie danych (wersja zapoznawcza) dla modeli wdrożonych w usłudze Azure Kubernetes Service (AKS)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

W tym artykule dowiesz się, jak monitorować dryf danych między zestawem danych szkoleniowych i danymi wnioskowania wdrożonego modelu. W kontekście uczenia maszynowego, przeszkolone modele uczenia maszynowego mogą powodować spadek wydajności przewidywania z powodu dryfu. Za pomocą Azure Machine Learning można monitorować dryfowanie danych, a usługa może wysłać do Ciebie alert e-mail po wykryciu dryfu.

## <a name="what-is-data-drift"></a>Co to jest dryfowanie danych?

W kontekście uczenia maszynowego, dryfowanie danych to zmiana danych wejściowych modelu, które prowadzą do obniżenia wydajności modelu. Jest to jedno z najważniejszych powodów, w których dokładność modelu ulega degradacji z upływem czasu, dlatego monitorowanie dryfowania danych pomaga wykrywać problemy z wydajnością modelu. 

## <a name="what-can-i-monitor"></a>Co mogę monitorować?

Za pomocą Azure Machine Learning można monitorować dane wejściowe modelu wdrożonego w AKS i porównywać je z zestawem danych szkoleniowym dla modelu. W regularnych odstępach czasu dane wnioskowania są [migawki i](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)profilowane, a następnie obliczane względem bazowego zestawu danych w celu utworzenia analizy dryfowania danych: 

+ Mierzy wielkość dryfu danych o nazwie współczynnik dryfu.
+ Mierzy współudziały danych przez funkcję, wskazując, które funkcje spowodowały dryfowanie danych.
+ Metryki odległości miary. Obecnie Wasserstein i odległość energii są obliczane.
+ Mierzy dystrybucje funkcji. Obecnie szacowanie gęstości jądra i histogramy.
+ Wysyłaj alerty do dryfowania danych za pośrednictwem poczty e-mail.

> [!Note]
> Ta usługa jest w wersji (wersja zapoznawcza) i jest ograniczona w opcjach konfiguracji. Szczegółowe informacje i aktualizacje można znaleźć w naszej [dokumentacji interfejsu API](https://docs.microsoft.com/python/api/azureml-datadrift/) i [informacjach o wersji](azure-machine-learning-release-notes.md) . 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Sposób monitorowania dryfowania danych w Azure Machine Learning

Za pomocą Azure Machine Learning, dryfowanie danych jest monitorowane za pośrednictwem zestawów danych lub wdrożeń. Aby monitorować do dryfowania danych, bazowy zestaw danych — zwykle jest to zestaw danych szkoleniowych dla modelu — jest określony. Drugi zestaw danych — zwykle dane wejściowe modelu zebrane z wdrożenia — są testowane względem bazowego zestawu danych. Oba zestawy danych są profilowane i są danymi wejściowymi do usługi monitorowania dryfowania danych. Model uczenia maszynowego jest szkolony w celu wykrywania różnic między dwoma zestawami danych. Wydajność modelu jest konwertowana na współczynnik dryfu, który mierzy wielkość dryfu między dwoma zestawami danych. Przy użyciu funkcji [interpretacji modelu](how-to-machine-learning-interpretability.md)są obliczane funkcje, które przyczyniają się do współczynnika dryfu. W profilu zestawu danych są śledzone informacje statystyczne dotyczące każdej funkcji. 

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli go nie masz, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

- Azure Machine Learning zestawu SDK dla języka Python. Użyj instrukcji w [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) , aby wykonać następujące czynności:

    - Tworzenie środowiska Miniconda
    - Instalowanie zestawu SDK Azure Machine Learning dla języka Python

- [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md).

- [Plik konfiguracji](how-to-configure-environment.md#workspace)obszaru roboczego.

- Zainstaluj zestaw SDK dryfowania danych przy użyciu następującego polecenia:

    ```shell
    pip install azureml-datadrift
    ```

- Utwórz [zestaw](how-to-create-register-datasets.md) danych na podstawie danych szkoleniowych modelu.

- Określ zestaw danych szkoleniowych podczas [rejestrowania](concept-model-management-and-deployment.md) modelu. Poniższy przykład ilustruje użycie `datasets` parametru w celu określenia zestawu danych szkoleniowych:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Włącz zbieranie danych modelu](how-to-enable-data-collection.md) , aby zbierać dane z wdrożenia AKS modelu i potwierdzić, że dane są zbierane w `modeldata` kontenerze obiektów BLOB.

## <a name="configure-data-drift"></a>Konfigurowanie dryfowania danych
Aby skonfigurować dryf danych dla eksperymentu, zaimportuj zależności, jak pokazano w poniższym przykładzie języka Python. 

Ten przykład ilustruje Konfigurowanie [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) obiektu:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DataDriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Prześlij DataDriftDetector uruchomienie

Po `DataDriftDetector` skonfigurowaniu obiektu można przesłać [dane dotyczące przebiegu danych](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-) w danym dniu dla modelu. W ramach przebiegu należy włączyć alerty DataDriftDetector przez ustawienie `drift_threshold` parametru. Jeśli [datadrift_coefficient](#visualize-drift-metrics) znajduje się powyżej podanej `drift_threshold` wiadomości e-mail, zostanie ona wysłana.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run.id)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Wizualizuj metryki dryfu

<a name="metrics"></a>

Po przesłaniu uruchomienia DataDriftDetector można zobaczyć metryki dryfu, które są zapisywane w każdej iteracji uruchomienia dla zadania dryfowania danych:


|Metric|Opis|
--|--|
wasserstein_distance|Odległość statystyczna zdefiniowana dla jednowymiarowej dystrybucji liczbowej.|
energy_distance|Odległość statystyczna zdefiniowana dla jednowymiarowej dystrybucji liczbowej.|
datadrift_coefficient|Obliczana podobnie jak współczynnik korelacji Matthew, ale dane wyjściowe to liczba rzeczywista z zakresu od 0 do 1. W kontekście dryfu wartość 0 oznacza brak dryfu i 1 oznacza maksymalne dryfowanie.|
datadrift_contribution|Znaczenie funkcji dotyczącej dryfu.|

Istnieje wiele sposobów wyświetlania metryk dryfu:

* Użyj `RunDetails` [widżetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Użyj [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) funkcji na dowolnym `datadrift` obiekcie Run.
* Wyświetl metryki z sekcji **modele** obszaru roboczego w programie [Azure Machine Learning Studio](https://ml.azure.com).

W poniższym przykładzie w języku Python pokazano, jak wykreślić odpowiednie metryki dotyczące dryfowania danych. Możesz użyć zwróconych metryk do kompilowania wizualizacji niestandardowych:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Zobacz dryfowanie danych wykryte przez Azure Machine Learning](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Zaplanuj skanowanie dryfowania danych 

Po włączeniu wykrywania dryfowania danych DataDriftDetector jest uruchamiany zgodnie z określoną, zaplanowaną częstotliwością. Jeśli datadrift_coefficient osiągnie daną wartość `drift_threshold` , zostanie wysłana wiadomość e-mail z każdym zaplanowanym przebiegiem. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Konfigurację detektora dryfowania danych można zobaczyć w obszarze **modele** na karcie **szczegóły** w obszarze roboczym w programie [Azure Machine Learning Studio](https://ml.azure.com).

[![Dryfowanie danych Azure Machine Learning Studio](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Wyświetl wyniki w Azure Machine Learning Studio

Aby wyświetlić wyniki w obszarze roboczym w programie [Azure Machine Learning Studio](https://ml.azure.com), przejdź do strony model. Na karcie Szczegóły w modelu jest wyświetlana konfiguracja dryfowania danych. Karta **dryfowanie danych** jest teraz dostępna Wizualizacja metryk dryfowania danych. 

[![Dryfowanie danych Azure Machine Learning Studio](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>Otrzymywanie alertów o dryfach

Ustawienie progu alertu dotyczącego współczynnika dryfu i podanie adresu e-mail powoduje automatyczne wysłanie alertu [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) e-mail za każdym razem, gdy współczynnik dryfu przekracza wartość progową. 

Aby można było skonfigurować niestandardowe alerty i akcje, wszystkie metryki dotyczące dryfowania danych są przechowywane w zasobie [Application Insights](how-to-enable-app-insights.md) , który został utworzony wraz z obszarem roboczym Azure Machine Learning. Możesz użyć linku w alercie e-mail do zapytania Application Insights.

![Alert E-mail z dryfem danych](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Ponowne uczenie modelu po przedryfze

Gdy odchylenie danych ma negatywny wpływ na wydajność wdrożonego modelu, jest to czas na ponowne nauczenie modelu. Aby to zrobić, wykonaj następujące czynności.

* Zbadaj zebrane dane i przygotuj dane, aby szkolić nowy model.
* Podziel ją na dane szkolenia/testowania.
* Ponownie przeszkol model przy użyciu nowych danych.
* Oceń wydajność nowo wygenerowanego modelu.
* Wdróż nowy model, jeśli wydajność jest lepsza niż model produkcyjny.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z pełnym przykładem korzystania z funkcji dryfowania danych, zobacz [Notes dryfowania danych w usłudze Azure ml](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb). Ten Jupyter Notebook ilustruje użycie [otwartego zestawu danych platformy Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) do uczenia modelu, aby przewidzieć Pogoda, wdrożyć go w usłudze AKS oraz monitorować dane dotyczące dryfowania danych. 

* Wykrywaj dryfowanie danych za pomocą [monitorów zestawu danych](how-to-monitor-datasets.md).

* Znacznie doceniamy Twoje pytania, komentarze lub sugestie, ponieważ dryf danych zbliża się do ogólnej dostępności. Skorzystaj z przycisku opinii o produkcie poniżej. 
