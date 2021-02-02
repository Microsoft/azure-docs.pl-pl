---
title: Parametr strojenia modelu
titleSuffix: Azure Machine Learning
description: Zautomatyzowanie dostrajania parametrów dla modeli uczenia głębokiego i uczenia maszynowego przy użyciu Azure Machine Learning.
ms.author: anumamah
author: Aniththa
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 01/29/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: a4be95561c097191803f2faa271c5d6bba875869
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430365"
---
# <a name="hyperparameter-tuning-a-model-with-azure-machine-learning"></a>Parametr strojenia modelu z Azure Machine Learning

Automatyzuj wydajne dostrajanie parametrów przy użyciu [pakietu](/python/api/azureml-train-core/azureml.train.hyperdrive?preserve-view=true&view=azure-ml-py)Azure Machine Learning. Dowiedz się, jak wykonać kroki wymagane do dostrajania parametrów z [zestawem SDK Azure Machine Learning](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py):

1. Zdefiniuj miejsce przeszukiwania parametrów
1. Określ metrykę podstawową do optymalizacji  
1. Określ zasady wczesnego zakończenia dla przebiegów o niskiej wydajności
1. Przydzielanie zasobów
1. Uruchom eksperyment ze zdefiniowaną konfiguracją
1. Wizualizuj przebiegi szkoleniowe
1. Wybierz najlepszą konfigurację dla modelu

## <a name="what-is-hyperparameter-tuning"></a>Co to jest Dostrajanie parametru?

Parametry **są dostosowywanymi parametrami** , które umożliwiają kontrolę nad procesem szkolenia modeli. Na przykład w przypadku sieci neuronowych należy określić liczbę ukrytych warstw oraz liczbę węzłów w każdej warstwie. Wydajność modelu zależy od zbyt wielu parametrów.

 **Dostrajanie parametrów**, nazywane również **optymalizacją parametrów**, to proces znajdowania konfiguracji parametrów, które zapewniają najlepszą wydajność. Proces jest zwykle kosztowny i ręczny.

Azure Machine Learning pozwala zautomatyzować dostrajanie parametrów i uruchamiać eksperymenty równolegle w celu wydajnej optymalizacji parametrów.


## <a name="define-the-search-space"></a>Zdefiniuj miejsce wyszukiwania

Dostrajanie parametrów przez Eksplorowanie zakresu wartości zdefiniowanych dla każdego parametru.

Parametry "parameter" mogą być dyskretne lub ciągłe i mają rozkład wartości opisany przez [wyrażenie parametru](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?preserve-view=true&view=azure-ml-py).

### <a name="discrete-hyperparameters"></a>Dyskretne parametry

Dyskretne podparametry są określone jako `choice` wartości dyskretne. `choice` może to być:

* co najmniej jedna wartość oddzielona przecinkami
* `range`obiekt
* dowolny dowolny `list` obiekt


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

W takim przypadku `batch_size` jedną z wartości [16, 32, 64, 128] i `number_of_hidden_layers` przyjmuje jedną z wartości [1, 2, 3, 4].

Następujące zaawansowane parametry dyskretne mogą być również określone przy użyciu dystrybucji:

* `quniform(low, high, q)` -Zwraca wartość jak Round (Uniform (Low, High)/q) * q
* `qloguniform(low, high, q)` -Zwraca wartość, taką jak Round (EXP (Uniform (Low, High))/q) * q
* `qnormal(mu, sigma, q)` -Zwraca wartość, jak Round (Normal, Sigma)/q) * q
* `qlognormal(mu, sigma, q)` -Zwraca wartość, np. Round (EXP (normalne (mu, Sigma))/q) * q

### <a name="continuous-hyperparameters"></a>Parametry ciągłe 

Parametry ciągłe są określane jako dystrybucja nad ciągłym zakresem wartości:

* `uniform(low, high)` -Zwraca wartość równomiernie rozdystrybuowaną między niskim i wysoką
* `loguniform(low, high)` -Zwraca wartość narysowana przy użyciu funkcji EXP (Uniform (Low, High)), aby logarytm wartości zwracanej był równomiernie dystrybuowany
* `normal(mu, sigma)` -Zwraca wartość rzeczywistą, która jest zwykle dystrybuowana z średnią i odchyleniem Standard Sigma
* `lognormal(mu, sigma)` -Zwraca wartość narysowana przy użyciu funkcji EXP (normalne (mu, Sigma)), aby logarytm wartości zwracanej był zwykle dystrybuowany

Przykład definicji przestrzeni parametrów:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Ten kod definiuje miejsce wyszukiwania z dwoma parametrami — `learning_rate` i `keep_probability` . `learning_rate` ma rozkład normalny z wartością średnia 10 i odchylenie standardowe równe 3. `keep_probability` ma jednolitą dystrybucję o wartości minimalnej 0,05 i maksymalnej wartości 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Próbkowanie obszaru hiperprzestrzeni

Określ metodę próbkowania parametrów, która ma być używana w miejscu parametru. Azure Machine Learning obsługuje następujące metody:

* Próbkowanie losowe
* Próbkowanie siatki
* Próbkowanie Bayesowskie

#### <a name="random-sampling"></a>Próbkowanie losowe

[Próbkowanie losowe](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?preserve-view=true&view=azure-ml-py) obsługuje dyskretne i ciągłe parametry. Obsługuje wczesne zakończenie uruchamiania niskiej wydajności. Niektórzy użytkownicy wykonują wstępne wyszukiwanie przy użyciu losowego próbkowania, a następnie ograniczają miejsce wyszukiwania, aby poprawić wyniki.

W przypadku próbkowania losowego wartości parametrów są losowo wybierane ze zdefiniowanego miejsca wyszukiwania. 

```Python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import normal, uniform, choice
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Próbkowanie siatki

[Próbkowanie siatki](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?preserve-view=true&view=azure-ml-py) obsługuje dyskretne parametry. Użyj próbkowania siatki, jeśli możesz zabudżetować, aby w sposób wyczerpujący przeszukiwać miejsce wyszukiwania. Obsługuje wczesne zakończenie uruchamiania niskiej wydajności.

Wykonuje proste przeszukiwanie siatki dla wszystkich możliwych wartości. Próbkowanie siatki może być używane tylko z `choice` parametrami. Na przykład następujące miejsce ma sześć przykładów:

```Python
from azureml.train.hyperdrive import GridParameterSampling
from azureml.train.hyperdrive import choice
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Próbkowanie Bayesowskie

[Próbkowanie bayesowskie](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?preserve-view=true&view=azure-ml-py) jest oparte na algorytmie optymalizacji bayesowskie. Wybiera próbki w oparciu o sposób wykonywania poprzednich przykładów, dzięki czemu nowe przykłady zwiększają podstawową metrykę.

Próbkowanie bayesowskie jest zalecane, jeśli masz wystarczającą ilość budżetu do eksplorowania miejsca na parametrze. Aby uzyskać najlepsze wyniki, zaleca się, aby maksymalna liczba przebiegów była większa niż lub równa 20 razy liczba dostrojenia parametrów. 

Liczba współbieżnych uruchomień ma wpływ na efektywność procesu dostrajania. Mniejsza liczba współbieżnych uruchomień może prowadzić do lepszego próbkowania zbieżności, ponieważ mniejszy stopień równoległości zwiększa liczbę przebiegów korzystających z wcześniej ukończonych przebiegów.

Próbkowanie bayesowskie obsługuje `choice` , `uniform` i `quniform` rozpowszechniać tylko w obszarze wyszukiwania.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> Określ metrykę podstawową

Określ [podstawową metrykę](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?preserve-view=true&view=azure-ml-py) , która ma zostać zoptymalizowana. Poszczególne przebiegi szkoleniowe są oceniane dla metryki podstawowej. Zasady wczesnej zakończenia wykorzystują podstawową metrykę do identyfikowania przebiegów o niskiej wydajności.

Określ następujące atrybuty głównej metryki:

* `primary_metric_name`: Nazwa głównej metryki musi dokładnie odpowiadać nazwie metryki rejestrowanej przez skrypt szkoleniowy
* `primary_metric_goal`: Może to być albo `PrimaryMetricGoal.MAXIMIZE` `PrimaryMetricGoal.MINIMIZE` i i określać, czy Metryka podstawowa będzie zmaksymalizowana, czy zminimalizowana podczas oceniania przebiegów. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Ten przykład maksymalizuje "dokładność".

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>Metryki dzienników dla strojenia parametru

Skrypt szkoleniowy dla Twojego modelu **musi** rejestrować podstawową metrykę podczas szkolenia modelu, aby można było uzyskać do niego dostęp do dostrajania parametrów.

Rejestruj podstawową metrykę w skrypcie szkoleniowym przy użyciu następującego fragmentu przykładu:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Skrypt szkoleniowy oblicza `val_accuracy` i rejestruje go jako podstawową metrykę "dokładność". Za każdym razem, gdy Metryka jest rejestrowana, jest odbierana przez usługę strojenia parametru. Istnieje możliwość ustalenia częstotliwości raportowania.

Aby uzyskać więcej informacji o rejestrowaniu wartości w przebiegach szkoleniowych modelu, zobacz [Włączanie rejestrowania w usłudze Azure ml Training](how-to-track-experiments.md).

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Określ zasady wczesnego zakończenia

Automatycznie kończy działać z zasadami wczesnego zakończenia. Wczesne zakończenie poprawia wydajność obliczeniową.

Można skonfigurować następujące parametry, które kontrolują, kiedy zasady są stosowane:

* `evaluation_interval`: częstotliwość stosowania zasad. Za każdym razem, gdy skrypt szkoleniowy rejestruje podstawową metrykę jako jeden interwał. `evaluation_interval`1 zastosuje zasady za każdym razem, gdy skrypt szkoleniowy zgłosi podstawową metrykę. `evaluation_interval`Zasada z 2 będzie stosować zasady co godzinę. Jeśli nie zostanie określona, `evaluation_interval` jest domyślnie ustawiona na 1.
* `delay_evaluation`: opóźnia pierwszą ocenę zasad przez określoną liczbę interwałów. Jest to opcjonalny parametr, który pozwala uniknąć przedwcześnie zakończenia przebiegów szkoleniowych przez zezwolenie na uruchamianie wszystkich konfiguracji przez minimalną liczbę interwałów. Jeśli ta wartość jest określona, zasady stosują się do każdej wielokrotności evaluation_interval, która jest większa lub równa delay_evaluation.

Azure Machine Learning obsługuje następujące zasady wczesnego zakończenia:
* [Zasady Bandit](#bandit-policy)
* [Średnie zatrzymywanie zasad](#median-stopping-policy)
* [Zasady wyboru obcinania](#truncation-selection-policy)
* [Brak zasad zakończenia](#no-termination-policy-default)


### <a name="bandit-policy"></a>Zasady Bandit

[Zasady Bandit](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?preserve-view=true&view=azure-ml-py#&preserve-view=truedefinition) są oparte na wartości współczynnika zapasu/zapasu czasu i interwału obliczania. Przerwania Bandit są uruchamiane, gdy Metryka podstawowa nie mieści się w określonym współczynniku zapasu/zapasu czasu w porównaniu do najlepszego przebiegu.

> [!NOTE]
> Próbkowanie bayesowskie nie obsługuje wczesnego kończenia pracy. W przypadku korzystania z próbkowania bayesowskie Ustaw `early_termination_policy = None` .

Określ następujące parametry konfiguracji:

* `slack_factor` lub `slack_amount` : zapasowy, który jest dozwolony w odniesieniu do najlepszego przebiegu szkoleniowego. `slack_factor` Określa dopuszczalny zapas czasu jako współczynnik. `slack_amount` Określa dozwolony czas zapasowy jako ilość bezwzględną, a nie współczynnik.

    Rozważmy na przykład, że zasady Bandit zostały zastosowane w interwale 10. Załóżmy, że najlepiej działający przebieg w interwale 10 zgłosił podstawową metrykę do 0,8 z celem, aby zmaksymalizować podstawową metrykę. Jeśli zasady określają `slack_factor` 0,2, każdy przebieg szkolenia, którego Najlepsza Metryka w interwale 10 jest mniejsza niż 0,66 (0,8/(1 + `slack_factor` )) zostanie zakończony.
* `evaluation_interval`: (opcjonalnie) częstotliwość stosowania zasad
* `delay_evaluation`: (opcjonalnie) opóźnia pierwszą ocenę zasad przez określoną liczbę interwałów


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

W tym przykładzie zasady wczesnej zakończenia są stosowane w każdym interwale, gdy raportowane są metryki, rozpoczynając od interwału próbnego 5. Każdy przebieg, którego Najlepsza Metryka jest mniejsza niż (1/(1 + 0,1) lub 91% najlepszego przebiegu, zostanie zakończony.

### <a name="median-stopping-policy"></a>Średnie zatrzymywanie zasad

[Mediana zatrzymywana](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?preserve-view=true&view=azure-ml-py) to zasady wczesnej zakończenia na podstawie średnich podstawowych metryk raportowanych przez uruchomienia. Te zasady obliczą średnie uruchamiania w ramach wszystkich przebiegów szkoleniowych i kończy działanie z podstawowymi wartościami metryk, które są mniejsze niż mediana średnia.

Te zasady pobierają następujące parametry konfiguracji:
* `evaluation_interval`: częstotliwość stosowania zasad (parametr opcjonalny).
* `delay_evaluation`: opóźnia pierwsze oszacowanie zasad dla określonej liczby interwałów (parametr opcjonalny).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

W tym przykładzie zasady wczesnej zakończenia są stosowane w każdym interwale, rozpoczynając od okresu ewaluacyjnego 5. Przebieg jest zakończony z upływem interwału 5, jeśli jego Najlepsza Metryka podstawowa jest gorsza niż wartość mediana średnich wartości z interwałów 1:5 dla wszystkich przebiegów szkoleniowych.

### <a name="truncation-selection-policy"></a>Zasady wyboru obcinania

[Zaznaczenie obcięcia](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?preserve-view=true&view=azure-ml-py) powoduje anulowanie procentu najniższych przebiegów wykonywanych w każdym interwale ewaluacyjnym. Przebiegi są porównywane przy użyciu metryki podstawowej. 

Te zasady pobierają następujące parametry konfiguracji:

* `truncation_percentage`: procent najniższych przebiegów wykonywanych w każdym interwale oceny. Wartość całkowita z zakresu od 1 do 99.
* `evaluation_interval`: (opcjonalnie) częstotliwość stosowania zasad
* `delay_evaluation`: (opcjonalnie) opóźnia pierwszą ocenę zasad przez określoną liczbę interwałów


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

W tym przykładzie zasady wczesnej zakończenia są stosowane w każdym interwale, rozpoczynając od okresu ewaluacyjnego 5. Przebieg kończy się w interwale 5, jeśli jego wydajność w interwale 5 jest w najmniejszej 20% wydajności wszystkich przebiegów w przedziale 5.

### <a name="no-termination-policy-default"></a>Brak zasad zakończenia (wartość domyślna)

Jeśli żadna zasada nie zostanie określona, usługa strojenia parametrów będzie zezwalać na wykonywanie wszystkich operacji szkoleniowych.

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>Wybieranie zasad wczesnego zakończenia

* Aby zapoznać się z zasadami, które zapewniają oszczędności bez kończenia zadań obietnicy, należy wziąć pod uwagę średnią zatrzymywanie zasad z `evaluation_interval` 1 i `delay_evaluation` 5. Są to ustawienia, które mogą zapewnić około 25%-35% oszczędności bez utraty podstawowej metryki (na podstawie naszych danych oceny).
* Aby uzyskać bardziej agresywne oszczędności, użyj zasad Banditymi z mniejszym dozwolonym użyciem zapasu lub zasad wyboru obcięcia z większym procentem obcięcia.

## <a name="allocate-resources"></a>Przydzielanie zasobów

Kontroluj budżet zasobów, określając maksymalną liczbę przebiegów szkoleniowych.

* `max_total_runs`: Maksymalna liczba przebiegów szkoleniowych. Musi być liczbą całkowitą z zakresu od 1 do 1000.
* `max_duration_minutes`: (opcjonalnie) maksymalny czas trwania eksperymentu strojenia parametru w minutach. Uruchamiany po anulowaniu tego czasu trwania.

>[!NOTE] 
>Jeśli oba `max_total_runs` i `max_duration_minutes` są określone, eksperyment strojenia parametrów kończy się, gdy zostanie osiągnięty pierwszy z tych dwóch progów.

Ponadto Określ maksymalną liczbę przebiegów szkoleniowych uruchamianych współbieżnie podczas wyszukiwania dostrajania parametrów.

* `max_concurrent_runs`: (opcjonalnie) Maksymalna liczba przebiegów, które mogą być uruchamiane współbieżnie. Jeśli nie zostanie określony, wszystkie uruchomienia są uruchamiane równolegle. Jeśli ta wartość jest określona, musi być liczbą całkowitą z zakresu od 1 do 100.

>[!NOTE] 
>Liczba współbieżnych uruchomień jest zależna od zasobów dostępnych w określonym elemencie docelowym obliczeń. Upewnij się, że element docelowy obliczeń ma dostępne zasoby dla żądanego współbieżności.

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Ten kod umożliwia skonfigurowanie eksperymentu strojenia parametrów w celu użycia maksymalnie 20 łącznych uruchomień z czterema konfiguracjami jednocześnie.

## <a name="configure-hyperparameter-tuning-experiment"></a>Konfigurowanie eksperymentu strojenia parametrów

Aby [skonfigurować eksperyment strojenia parametrów](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?preserve-view=true&view=azure-ml-py) , podaj następujące elementy:
* Zdefiniowany obszar wyszukiwania parametru
* Zasady wczesnego zakończenia
* Podstawowa metryka
* Ustawienia przydziału zasobów
* ScriptRunConfig `src`

ScriptRunConfig to skrypt szkoleniowy, który będzie uruchamiany przy użyciu parametrów z próbkami. Definiuje zasoby na zadanie (jeden lub wiele węzłów) oraz miejsce docelowe obliczeń do użycia.

> [!NOTE]
>Element docelowy obliczeń określony w elemencie `src` musi mieć wystarczającą ilość zasobów, aby spełnić poziom współbieżności. Aby uzyskać więcej informacji na temat ScriptRunConfig, zobacz [Konfigurowanie przebiegów szkoleniowych](how-to-set-up-training-targets.md).

Konfigurowanie eksperymentu strojenia parametrów:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="submit-hyperparameter-tuning-experiment"></a>Prześlij eksperyment strojenia parametru

Po zdefiniowaniu konfiguracji strojenia parametru [Prześlij eksperyment](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-hyperparameter-tuning-optional"></a>Dostrajanie parametru uruchomienia ciepłego (opcjonalnie)

Znalezienie najlepszych wartości parametrów dla modelu może być procesem iteracyjnym. Możesz ponownie użyć wiedzy z pięciu poprzednich przebiegów, aby przyspieszyć dostrajanie parametrów.


Uruchamianie ciepłe jest obsługiwane w różny sposób w zależności od metody próbkowania:
- **Bayesowskie próbkowanie**: próby z poprzedniego przebiegu są używane jako wcześniejsza wiedza, aby wybrać nowe przykłady i zwiększyć podstawową metrykę.
- **Próbkowanie losowe** lub **próbkowanie siatki**: wczesne zakończenie korzystania z wiedzy z poprzednich przebiegów, aby określić niewłaściwie wykonywane uruchomienia. 

Określ listę przebiegów nadrzędnych, z których chcesz zacząć uruchamiać.

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Jeśli eksperyment strojenia parametrów zostanie anulowany, można wznowić przebiegi szkoleniowe z ostatniego punktu kontrolnego. Jednak skrypt szkoleniowy musi obsługiwać logikę punktów kontrolnych.

Uruchomienie szkolenia musi używać tej samej konfiguracji z parametrami i zainstalowała foldery wyjściowe. Skrypt szkoleniowy musi akceptować `resume-from` argument, który zawiera punkt kontrolny lub pliki modelu, z których ma zostać wznowione uruchomienie szkolenia. Poszczególne przebiegi szkoleniowe można wznowić, korzystając z następującego fragmentu kodu:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Możesz skonfigurować eksperyment strojenia parametrów, aby rozpocząć pracę z poprzedniego eksperymentu lub wznowić indywidualne uruchomienie szkolenia przy użyciu parametrów opcjonalnych `resume_from` i `resume_child_runs` konfiguracji:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="visualize-hyperparameter-tuning-runs"></a>Wizualizacja przebiegów strojenia parametrów

Możesz wizualizować przebiegi strojenia parametrów w programie Azure Machine Learning Studio lub użyć widżetu Notes.

### <a name="studio"></a>Studio

Wszystkie przebiegi strojenia parametrów można wizualizować w [Azure Machine Learning Studio](https://ml.azure.com). Aby uzyskać więcej informacji na temat sposobu wyświetlania eksperymentu w portalu, zobacz [Wyświetlanie rekordów uruchamiania w Studio](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal).

- **Wykres metryk**: Ta Wizualizacja śledzi metryki zarejestrowane dla każdego podrzędnego przebiegu na dysku przez okres strojenia parametru. Każdy wiersz reprezentuje uruchomienie podrzędne, a każdy punkt mierzy podstawową wartość metryki w tej iteracji środowiska uruchomieniowego.  

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-metrics.png" alt-text="Wykres metryk dostrajania parametrów":::

- **Wykres równoległych współrzędnych**: Ta Wizualizacja przedstawia korelację między podstawową wydajnością metryki i poszczególnymi wartościami parametrów. Wykres jest interaktywny poprzez przenoszenie osi (kliknij i przeciągnij według etykiety osi), a następnie wyróżnij wartości na jednej osi (kliknij i przeciągnij pionowo wzdłuż jednej osi, aby zaznaczyć zakres żądanych wartości).

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates.png" alt-text="Wykres współrzędnych równoległych strojenia parametrów":::

- **Wykres punktowy 2-wymiarowy**: wizualizacja pokazuje korelację między dwoma pojedynczymi parametrami oraz skojarzoną z nią podstawową wartość metryki.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-2-dimensional-scatter.png" alt-text="Wykres punktowy strojenia Hyparameter 2-wymiarowego":::

- **Wykres punktowy** trójwymiarowy: wizualizacja jest taka sama jak 2D, ale pozwala na trzy parametry wymiarów korelacji z podstawową wartością metryki. Możesz również kliknąć i przeciągnąć, aby zmienić orientację wykresu, aby wyświetlić różne korelacje w przestrzeni trójwymiarowej.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-3-dimensional-scatter.png" alt-text="Wykres punktowy strojenia Hyparameter 3-wymiarowego":::

### <a name="notebook-widget"></a>Widżet notesu

Za pomocą [widżetu Notes](/python/api/azureml-widgets/azureml.widgets.rundetails?preserve-view=true&view=azure-ml-py) można wizualizować postępy przebiegów szkoleniowych. Poniższy fragment kodu wizualizuje wszystkie przebiegi strojenia parametrów w jednym miejscu w notesie Jupyter:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Ten kod przedstawia tabelę zawierającą szczegóły dotyczące szkoleń dla każdej konfiguracji parametrów.

:::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-table.png" alt-text="Tabela strojenia parametru":::

Możesz również wizualizować wydajność poszczególnych przebiegów w miarę postępów szkoleniowych.

## <a name="find-the-best-model"></a>Znajdź najlepszy model

Po zakończeniu wszystkich przebiegów strojenia parametrów należy określić największą wykonywaną konfigurację i wartości parametrów:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Przykładowy Notes

Zapoznaj się z notesem uczenia-*-* w tym folderze:
* [Jak używać — platforma Azure/ml — platformy](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki
* [Śledzenie eksperymentu](how-to-track-experiments.md)
* [Wdrażanie przeszkolonego modelu](how-to-deploy-and-where.md)
