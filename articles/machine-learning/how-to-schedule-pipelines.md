---
title: Planowanie potoków usługi Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Zaplanuj potoki usługi Azure Machine Learning przy użyciu zestawu SDK usługi Azure Machine Learning dla języka Python. Zaplanowane potoki umożliwiają automatyzację rutynowych, czasochłonnych zadań, takich jak przetwarzanie danych, szkolenie i monitorowanie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.openlocfilehash: d9e1bff3d25a978b5159d8e6ab8ab2453df77ca3
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640514"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Planowanie potoków uczenia maszynowego za pomocą zestawu SDK usługi Azure Machine Learning dla języka Python

W tym artykule dowiesz się, jak programowo zaplanować potok do uruchomienia na platformie Azure. Można utworzyć harmonogram na podstawie czasu, który upłynął lub zmian w systemie plików. Harmonogramy oparte na czasie mogą służyć do dbania o rutynowych zadań, takich jak monitorowanie dryfu danych. Harmonogramy oparte na zmianach mogą służyć do reagowania na nieregularne lub nieprzewidywalne zmiany, takie jak przekazywanie nowych danych lub edytowanie starych danych. Po nauczeniu się tworzenia harmonogramów dowiesz się, jak je pobierać i dezaktywować.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://aka.ms/AMLFree).

* Środowisko języka Python, w którym jest instalowany zestaw SDK usługi Azure Machine Learning dla języka Python. Aby uzyskać więcej informacji, zobacz [Tworzenie środowisk wielokrotnego użytku i zarządzanie nimi do szkoleń i wdrażania za pomocą usługi Azure Machine Learning.](how-to-use-environments.md)

* Obszar roboczy uczenia maszynowego z opublikowanym potokiem. Można użyć tego wbudowanego [tworzenie i uruchamianie potoków uczenia maszynowego za pomocą narzędzia Azure Machine Learning SDK.](how-to-create-your-first-pipeline.md)

## <a name="initialize-the-workspace--get-data"></a>Inicjowanie obszaru roboczego & uzyskania danych

Aby zaplanować potok, musisz odwołać się do obszaru roboczego, identyfikator opublikowanego potoku i nazwę eksperymentu, w którym chcesz utworzyć harmonogram. Te wartości można uzyskać za pomocą następującego kodu:

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

Aby uruchomić potok cyklicznie, utworzysz harmonogram. A `Schedule` kojarzy potok, eksperyment i wyzwalacz. Wyzwalacz może być,`ScheduleRecurrence` który opisuje oczekiwania między uruchomieniami lub ścieżka magazynu danych, który określa katalog do obserwowania zmian. W obu przypadkach potrzebny jest identyfikator potoku i nazwa eksperymentu, w którym można utworzyć harmonogram.

W górnej części pliku python `Schedule` zaimportuj i `ScheduleRecurrence` klasy:

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Tworzenie harmonogramu opartego na czasie

Konstruktor `ScheduleRecurrence` ma wymagany `frequency` argument, który musi być jednym z następujących ciągów: "Minute", "Hour", "Day", "Week" lub "Month". Wymaga również argumentu `interval` liczby całkowitej określającego, ile jednostek powinno uginąć `frequency` między rozpoczęciem harmonogramu. Opcjonalne argumenty pozwalają być bardziej szczegółowe na temat czasów rozpoczęcia, jak wyszczególniono w [scheduleRecurrence SDK docs](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py).

Utwórz `Schedule` bieg, który rozpoczyna się co 15 minut:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Tworzenie harmonogramu opartego na zmianach

Potoki, które są wyzwalane przez zmiany w pliku może być bardziej wydajne niż harmonogramy oparte na czasie. Na przykład można wykonać krok przetwarzania wstępnego po zmianie pliku lub gdy nowy plik zostanie dodany do katalogu danych. Można monitorować wszelkie zmiany w magazynie danych lub zmiany w określonym katalogu w magazynie danych. Jeśli monitorujesz określony katalog, zmiany w podkatalogach tego katalogu _nie_ wyzwolą uruchomienia.

Aby utworzyć plik reaktywny `Schedule`, `datastore` należy ustawić parametr w wywołaniu [Schedule.create](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Aby monitorować folder, `path_on_datastore` ustaw argument.

Argument `polling_interval` umożliwia określenie w minutach częstotliwości, z jaką magazyn danych jest sprawdzany pod kątem zmian.

Jeśli potok został zbudowany za pomocąparametru [potoku](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py) `data_path_parameter_name` [DataPath,](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) można ustawić tę zmienną na nazwę zmienionego pliku, ustawiając argument.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on time",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Opcjonalne argumenty podczas tworzenia harmonogramu

Oprócz argumentów omówionych wcześniej można ustawić `status` argument, `"Disabled"` aby utworzyć nieaktywny harmonogram. Na koniec `continue_on_step_failure` umożliwia przekazanie wartości logicznej, która zastąpi domyślne zachowanie błędu potoku.

### <a name="use-azure-logic-apps-for-more-complex-workflows"></a>Korzystanie z usługi Azure Logic Apps w celu uzyskania bardziej złożonych przepływów pracy

Usługa Azure Logic Apps obsługuje bardziej złożone przepływy pracy i jest znacznie szerzej zintegrowana niż potoki usługi Azure Machine Learning. Zobacz [wyzwalanie uruchomienia potoku uczenia maszynowego z aplikacji logiki,](how-to-trigger-published-pipeline.md) aby uzyskać więcej informacji.

## <a name="view-your-scheduled-pipelines"></a>Wyświetlanie zaplanowanych potoków

W przeglądarce sieci Web przejdź do usługi Azure Machine Learning. W sekcji **Punkty końcowe** panelu nawigacyjnego wybierz pozycję **Punkty końcowe potoku**. Spowoduje to przejście do listy potoków opublikowanych w obszarze roboczym.

![Strona Potoki AML](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

Na tej stronie można zobaczyć podsumowanie informacji o wszystkich potoków w obszarze roboczym: nazwy, opisy, stan i tak dalej. Drąż, klikając w potoku. Na wynikowej stronie znajduje się więcej szczegółów na temat potoku i można przejść do szczegółów w poszczególnych uruchomieniach.

## <a name="deactivate-the-pipeline"></a>Dezaktywacja potoku

Jeśli `Pipeline` masz, który jest publikowany, ale nie jest zaplanowane, można go wyłączyć za pomocą:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Jeśli potok jest zaplanowany, należy najpierw anulować harmonogram. Pobierz identyfikator harmonogramu z portalu lub uruchamiając:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Gdy masz `schedule_id` chcesz wyłączyć, uruchom:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Jeśli następnie `Schedule.list(ws)` uruchomisz ponownie, powinieneś otrzymać pustą listę.

## <a name="next-steps"></a>Następne kroki

W tym artykule użyto zestawu SDK usługi Azure Machine Learning dla języka Python, aby zaplanować potok na dwa różne sposoby. Jeden harmonogram powtarza się na podstawie czasu zegara, który upłynął. Inny harmonogram jest uruchamiany, jeśli plik `Datastore` jest modyfikowany w określonym lub w katalogu w tym magazynie. Widziałeś, jak używać portalu do badania potoku i poszczególnych przebiegów. Na koniec dowiedziałeś się, jak wyłączyć harmonogram, tak aby potok przestał działać.

Aby uzyskać więcej informacji, zobacz:

> [!div class="nextstepaction"]
> [Używanie potoków usługi Azure Machine Learning do oceniania wsadowego](tutorial-pipeline-batch-scoring-classification.md)

* Dowiedz się więcej o [potokach](concept-ml-pipelines.md)
* Dowiedz się więcej o [eksplorowaniu usługi Azure Machine Learning za pomocą usługi Jupyter](samples-notebooks.md)

