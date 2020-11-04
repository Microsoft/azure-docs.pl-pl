---
title: Planowanie potoków Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Planowanie potoków Azure Machine Learning przy użyciu zestawu Azure Machine Learning SDK dla języka Python. Zaplanowane potoki umożliwiają automatyzowanie rutynowych, czasochłonnych zadań, takich jak przetwarzanie danych, szkolenia i monitorowanie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 97d0f822e63bb6eb32b1cd2f211621af8ad1c4b8
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313991"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Planowanie potoków uczenia maszynowego za pomocą zestawu SDK Azure Machine Learning dla języka Python

W tym artykule dowiesz się, jak programowo zaplanować uruchamianie potoku na platformie Azure. Można utworzyć harmonogram na podstawie czasu, który upłynął lub zmiany w systemie plików. Harmonogramy oparte na czasie mogą służyć do wykonywania codziennych zadań, takich jak monitorowanie dryfowania danych. Harmonogramy oparte na zmianach mogą służyć do reagowania na zmiany nieregularne lub nieprzewidywalne, takie jak nowe przekazane dane lub stare dane. Po zapoznaniu się ze sposobem tworzenia harmonogramów dowiesz się, jak je pobrać i dezaktywować.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://aka.ms/AMLFree).

* Środowisko języka Python, w którym jest zainstalowany zestaw SDK Azure Machine Learning dla języka Python. Aby uzyskać więcej informacji, zobacz [Tworzenie środowisk wielokrotnego użytku i zarządzanie nimi na potrzeby szkoleń i wdrażania przy użyciu Azure Machine Learning.](how-to-use-environments.md)

* Obszar roboczy Machine Learning z opublikowanym potokiem. Można użyć wbudowanego [tworzenia i uruchamiania potoków uczenia maszynowego z zestawem SDK Azure Machine Learning](how-to-create-your-first-pipeline.md).

## <a name="initialize-the-workspace--get-data"></a>Inicjowanie obszaru roboczego & pobieranie danych

Do zaplanowania potoku konieczne będzie odwołanie do obszaru roboczego, identyfikator opublikowanego potoku oraz nazwę eksperymentu, w którym ma zostać utworzony harmonogram. Te wartości można uzyskać przy użyciu następującego kodu:

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>Tworzenie harmonogramu

Aby uruchomić potok cyklicznie, należy utworzyć harmonogram. A powoduje `Schedule` skojarzenie potoku, eksperymentu i wyzwalacza. Wyzwalacz może być obiektem `ScheduleRecurrence` opisującym oczekiwania między uruchomieniami lub ścieżką magazynu danych, która określa katalog, w którym mają być monitorowane zmiany. W obu przypadkach potrzebny będzie identyfikator potoku i nazwa eksperymentu, w którym ma zostać utworzony harmonogram.

W górnej części pliku języka Python zaimportuj `Schedule` klasy i `ScheduleRecurrence` :

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Tworzenie harmonogramu opartego na czasie

`ScheduleRecurrence`Konstruktor ma wymagany `frequency` argument, który musi być jednym z następujących ciągów: "minute", "Hour", "Day", "Week" lub "Month". Wymaga również `interval` argumentu Integer określającego, ile `frequency` jednostek powinna upłynąć między harmonogramem. Opcjonalne argumenty umożliwiają bardziej szczegółowe określenie czasu rozpoczęcia, zgodnie z opisem w dokumentacji [zestawu SDK ScheduleRecurrence](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?preserve-view=true&view=azure-ml-py).

Utwórz `Schedule` Uruchamianie co 15 minut:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Tworzenie harmonogramu opartego na zmianach

Potoki wyzwalane przez zmiany plików mogą być bardziej wydajne niż harmonogramy oparte na czasie. Na przykład możesz chcieć wykonać krok przetwarzania wstępnego, gdy plik zostanie zmieniony lub gdy nowy plik zostanie dodany do katalogu danych. Możesz monitorować wszelkie zmiany w magazynie danych lub zmiany w określonym katalogu w magazynie danych. W przypadku monitorowania określonego katalogu zmiany w podkatalogach tego katalogu _nie_ będą powodowały uruchomienia.

Aby utworzyć plik — reaktywny `Schedule` , należy ustawić `datastore` parametr w wywołaniu metody [Schedule. Create](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Aby monitorować folder, należy ustawić `path_on_datastore` argument.

`polling_interval`Argument pozwala określić, w minutach, częstotliwość sprawdzania zmian w magazynie danych.

Jeśli potok został skonstruowany przy użyciu [ścieżki datapath](/python/api/azureml-core/azureml.data.datapath.datapath?preserve-view=true&view=azure-ml-py) [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?preserve-view=true&view=azure-ml-py), można ustawić tę zmienną na nazwę zmienionego pliku przez ustawienie `data_path_parameter_name` argumentu.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Opcjonalne argumenty podczas tworzenia harmonogramu

Oprócz argumentów omówionych wcześniej, można ustawić `status` argument na, aby `"Disabled"` utworzyć nieaktywny harmonogram. Na koniec `continue_on_step_failure` umożliwia przekazanie wartości logicznej, która zastąpi domyślne zachowanie potoku.

### <a name="use-azure-logic-apps-for-more-complex-workflows"></a>Użyj Azure Logic Apps, aby uzyskać bardziej złożone przepływy pracy

Azure Logic Apps obsługuje bardziej złożone przepływy pracy i jest w szerszym stopniu zintegrowane od Azure Machine Learning potoków. Aby uzyskać więcej informacji [, zobacz Wyzwalanie przebiegu potoku Machine Learning z poziomu aplikacji logiki](how-to-trigger-published-pipeline.md) .

## <a name="view-your-scheduled-pipelines"></a>Wyświetlanie zaplanowanych potoków

W przeglądarce sieci Web przejdź do Azure Machine Learning. W sekcji **punkty końcowe** panelu nawigacji wybierz pozycję **punkty końcowe potoku**. Spowoduje to przejście do listy potoków opublikowanych w obszarze roboczym.

![Strona potoki elementu AML](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

Na tej stronie można wyświetlić informacje podsumowujące dotyczące wszystkich potoków w obszarze roboczym: nazwy, opisy, Stany i tak dalej. Aby przejść do szczegółów, kliknij potok. Na otrzymanej stronie znajduje się więcej szczegółów na temat potoku i można przechodzić do poszczególnych uruchomień.

## <a name="deactivate-the-pipeline"></a>Dezaktywuj potok

Jeśli masz `Pipeline` opublikowaną wersję, ale nie została ona zaplanowana, możesz ją wyłączyć przy użyciu:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

W przypadku zaplanowania potoku należy najpierw anulować harmonogram. Pobierz identyfikator harmonogramu z portalu lub uruchamiając:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Gdy chcesz `schedule_id` wyłączyć, uruchom polecenie:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Po ponownym uruchomieniu należy `Schedule.list(ws)` uzyskać pustą listę.

## <a name="next-steps"></a>Następne kroki

W tym artykule użyto zestawu SDK Azure Machine Learning dla języka Python w celu zaplanowania potoku na dwa różne sposoby. Jeden harmonogram powtarza się w oparciu o czas zegara, który upłynął. Drugi harmonogram jest uruchamiany, jeśli plik zostanie zmodyfikowany w określonym `Datastore` lub w katalogu w tym magazynie. Pokazano, jak używać portalu do badania potoku i poszczególnych uruchomień. Na koniec wiesz już, jak wyłączyć harmonogram, aby potok przestanie działać.

Aby uzyskać więcej informacji, zobacz:

> [!div class="nextstepaction"]
> [Korzystanie z potoków Azure Machine Learning na potrzeby oceniania partii](tutorial-pipeline-batch-scoring-classification.md)

* Dowiedz się więcej o [potokach](concept-ml-pipelines.md)
* Dowiedz się więcej o [eksplorowaniu Azure Machine Learning za pomocą Jupyter](samples-notebooks.md)