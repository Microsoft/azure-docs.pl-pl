---
title: Monitorowanie & zbieranie plików dziennika potoku
titleSuffix: Azure Machine Learning
description: Dodaj rejestrowanie do potoków szkoleń i wsadowych oceniania, a następnie Wyświetl zarejestrowane wyniki w Application Insights.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.date: 08/11/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 7ed27eba66b3d18bed8017934fce85928b961392
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520051"
---
# <a name="collect-machine-learning-pipeline-log-files-in-application-insights-for-alerts-and-debugging"></a>Zbierz pliki dziennika potoku uczenia maszynowego w Application Insights na potrzeby alertów i debugowania


Biblioteka języka Python [OpenCensus](https://opencensus.io/quickstart/python/) może służyć do kierowania dzienników do Application Insights ze skryptów. Agregowanie dzienników z uruchamiania potoków w jednym miejscu umożliwia tworzenie zapytań i diagnozowanie problemów. Użycie Application Insights umożliwi śledzenie dzienników w czasie i porównywanie dzienników potoku w ramach przebiegów.

Umieszczenie dzienników w miejscu spowoduje udostępnienie historii wyjątków i komunikatów o błędach. Ponieważ Application Insights integrują się z alertami platformy Azure, można także tworzyć alerty oparte na zapytaniach Application Insights.

## <a name="prerequisites"></a>Wymagania wstępne

* Postępuj zgodnie z instrukcjami, aby utworzyć obszar roboczy [Azure Machine Learning](./how-to-manage-workspace.md) i [utworzyć pierwszy potok](./how-to-create-machine-learning-pipelines.md)
* [Skonfiguruj środowisko programistyczne](./how-to-configure-environment.md) , aby zainstalować zestaw SDK Azure Machine Learning.
* Zainstaluj lokalnie pakiet [OpenCensus Azure monitor wywożącego](https://pypi.org/project/opencensus-ext-azure/) :
  ```python
  pip install opencensus-ext-azure
  ```
* Utwórz [wystąpienie Application Insights](../azure-monitor/app/opencensus-python.md) (ten dokument zawiera również informacje dotyczące uzyskiwania parametrów połączenia dla zasobu)

## <a name="getting-started"></a>Wprowadzenie

Ta sekcja zawiera wprowadzenie do korzystania z programu OpenCensus z potoku Azure Machine Learning. Aby uzyskać szczegółowy samouczek, zobacz [OpenCensus Azure monitor exports](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

Dodaj PythonScriptStep do potoku usługi Azure ML. Skonfiguruj [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration) z zależnością od opencensus-EXT-Azure. Skonfiguruj `APPLICATIONINSIGHTS_CONNECTION_STRING` zmienną środowiskową.

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

Utwórz plik o nazwie `sample_step.py`. Zaimportuj klasę AzureLogHandler, aby kierować dzienniki do Application Insights. Należy również zaimportować bibliotekę rejestrowania języka Python.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Następnie Dodaj AzureLogHandler do rejestratora języka Python.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Rejestrowanie przy użyciu wymiarów niestandardowych
 
Domyślnie dzienniki przekazywane do Application Insights nie będą miały wystarczającego kontekstu do śledzenia z powrotem do uruchomienia lub eksperymentu. Aby umożliwić działanie dzienników w celu diagnozowania problemów, konieczne są dodatkowe pola. 

Aby dodać te pola, można dodać wymiary niestandardowe w celu zapewnienia kontekstu do komunikatu dziennika. Przykładem jest to, że ktoś chce wyświetlić dzienniki w wielu krokach tego samego uruchomienia potoku.

Wymiary niestandardowe tworzą słownik wartości par klucz-wartość (przechowywanych jako pary ciągów i ciągów). Słownik jest następnie wysyłany do Application Insights i wyświetlany jako kolumna w wynikach zapytania. Jego poszczególne wymiary mogą służyć jako [parametry zapytania](#additional-helpful-queries).

### <a name="helpful-context-to-include"></a>Przydatny kontekst do uwzględnienia

| Pole                          | Powód/przykład                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Może wykonywać zapytania dotyczące dzienników dla tych samych parent_run_id, aby wyświetlić dzienniki w czasie dla wszystkich kroków, zamiast konieczności szczegółowe do każdego pojedynczego kroku                                        |
| step_id                        | Może wykonywać zapytania dotyczące dzienników dla tych samych step_id, aby zobaczyć, gdzie wystąpił problem z wąskim zakresem tylko do pojedynczego kroku                                                        |
| step_name                      | Może wykonywać zapytania dotyczące dzienników, aby zobaczyć wydajność etapową w miarę upływu czasu. Pomaga również znaleźć step_id ostatnich przebiegów bez nadania do interfejsu użytkownika portalu                                          |
| experiment_name                | Może wykonywać zapytania między dziennikami, aby zobaczyć wydajność eksperymentu w miarę upływu czasu. Pomaga również w znalezieniu parent_run_id lub step_id dla ostatnich przebiegów bez nadania do interfejsu użytkownika portalu                   |
| run_url                 | Może udostępnić link bezpośrednio z powrotem do przebiegu badania. |

**Inne przydatne pola**

Te pola mogą wymagać dodatkowej Instrumentacji kodu i nie są dostarczane przez kontekst uruchomienia.

| Pole                   | Powód/przykład                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | W przypadku używania ciągłej integracji/ciągłego wdrażania to pole może skorelować dzienniki z wersją kodu, która dostarczyła krok i logikę potoku. Ten link może dodatkowo ułatwić diagnozowanie problemów lub Identyfikowanie modeli z określonymi cechami (wartości dzienników/metryk) |
| run_type                       | Może różnić się między różnymi typami modeli lub szkoleniem a przebiegami oceniania |

### <a name="creating-a-custom-dimensions-dictionary"></a>Tworzenie niestandardowego słownika wymiarów

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", extra= {"custom_dimensions":custom_dimensions})
```

## <a name="opencensus-python-logging-considerations"></a>Zagadnienia dotyczące rejestrowania w języku Python OpenCensus

OpenCensus AzureLogHandler jest używany do kierowania dzienników w języku Python do Application Insights. W związku z tym należy rozważyć rejestrowanie w języku Python wszystkie szczegóły. Po utworzeniu rejestratora ma domyślny poziom dziennika i będzie wyświetlał dzienniki większe niż lub równe tego poziomu. Dobrym odwołaniem do korzystania z funkcji rejestrowania języka Python jest [Cookbook rejestrowania](https://docs.python.org/3/howto/logging-cookbook.html).

`APPLICATIONINSIGHTS_CONNECTION_STRING`Zmienna środowiskowa jest wymagana dla biblioteki OpenCensus. Zalecamy ustawienie tej zmiennej środowiskowej zamiast przekazywania jej w postaci parametru potoku, aby uniknąć pominięcia ciągów połączeń z tekstem.

## <a name="querying-logs-in-application-insights"></a>Wykonywanie zapytania dotyczącego dzienników w Application Insights

Dzienniki kierowane do Application Insights będą wyświetlane w obszarze "Traces" lub "Exceptions". Pamiętaj o dostosowaniu przedziału czasowego w celu uwzględnienia uruchomienia potoku.

![Application Insights wynik zapytania](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

W wyniku Application Insights zostanie wyświetlony komunikat dziennika, poziom, ścieżka pliku i numer wiersza kodu. Zostaną również wyświetlone wszystkie dołączone wymiary niestandardowe. Na tym obrazie słownik customDimensions pokazuje pary klucz/wartość z poprzedniego [przykładu kodu](#creating-a-custom-dimensions-dictionary).

### <a name="additional-helpful-queries"></a>Dodatkowe pomocne zapytania

Niektóre z zapytań poniżej używają elementu "customDimensions. Level". Te poziomy ważności odpowiadają poziomowi, który został pierwotnie Wysłany przez dziennik Python. Aby uzyskać dodatkowe informacje dotyczące zapytań, zobacz [Azure monitor kwerendy dzienników](/azure/data-explorer/kusto/query/).

| Przypadek użycia                                                               | Zapytanie                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Wyniki dziennika dla określonego wymiaru niestandardowego, na przykład "parent_run_id" | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Wyniki dziennika dla wszystkich przebiegów szkoleniowych w ciągu ostatnich 7 dni                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Rejestruj wyniki z błędem severityLevel z ostatnich 7 dni              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Liczba wyników dziennika z błędem severityLevel w ciągu ostatnich 7 dni     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Następne kroki

Po umieszczeniu dzienników w wystąpieniu Application Insights mogą one służyć do ustawiania [alertów Azure monitor](../azure-monitor/alerts/alerts-overview.md#what-you-can-alert-on) na podstawie wyników zapytania.

Możesz również dodawać wyniki z zapytań do [pulpitu nawigacyjnego platformy Azure](../azure-monitor/app/tutorial-app-dashboards.md#add-logs-query) , aby uzyskać dodatkowe informacje.