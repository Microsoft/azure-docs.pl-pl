---
title: Dostrajanie hiperparametryczne modelu
titleSuffix: Azure Machine Learning
description: Automatyzuj dostrajanie hiperparametryczne dla modeli uczenia głębokiego i uczenia maszynowego przy użyciu Azure Machine Learning.
ms.author: anumamah
author: Aniththa
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 02/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: ec01561c5c44c850b32187629552b1bdb99537e7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819182"
---
# <a name="hyperparameter-tuning-a-model-with-azure-machine-learning"></a>Dostrajanie hiperparametryczne modelu za pomocą Azure Machine Learning

Zautomatyzuj wydajne dostrajanie hiperparametryczne przy użyciu Azure Machine Learning [HyperDrive.](/python/api/azureml-train-core/azureml.train.hyperdrive) Dowiedz się, jak wykonać kroki wymagane do dostrojenia hiperparametrów przy użyciu Azure Machine Learning [SDK:](/python/api/overview/azure/ml/)

1. Definiowanie obszaru wyszukiwania parametrów
1. Określanie podstawowej metryki do optymalizacji  
1. Określanie zasad wczesnego zakończenia dla przebiegów o niskiej mocy
1. Tworzenie i przypisywanie zasobów
1. Uruchamianie eksperymentu ze zdefiniowaną konfiguracją
1. Wizualizowanie przebiegów trenowania
1. Wybieranie najlepszej konfiguracji dla modelu

## <a name="what-is-hyperparameter-tuning"></a>Co to jest dostrajanie hiperparametryczne?

**Hiperparametry to** dostosowywane parametry, które umożliwiają kontrolowanie procesu trenowania modelu. Na przykład w przypadku sieci neuronowych decydujesz o liczbie ukrytych warstw i liczbie węzłów w każdej warstwie. Wydajność modelu w dużym stopniu zależy od hiperparametrów.

 **Dostrajanie** **hiperparametryczne**, nazywane również optymalizacją hiperparametrywną, to proces znajdowania konfiguracji hiperparametrów, co skutkuje najlepszą wydajnością. Proces jest zwykle kosztowny obliczeniowo i ręczny.

Azure Machine Learning umożliwia automatyzację dostrajania hiperparametrów i równoległe uruchamianie eksperymentów w celu wydajnej optymalizacji hiperparametrów.


## <a name="define-the-search-space"></a>Definiowanie obszaru wyszukiwania

Dostosuj hiperparametry, eksplorując zakres wartości zdefiniowanych dla każdego hiperparametru.

Hiperparametry mogą być dyskretne lub ciągłe i mają rozkład wartości opisanych przez [wyrażenie parametru](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions).

### <a name="discrete-hyperparameters"></a>Hiperparametry dyskretne

Hiperparametry dyskretne są określane jako wartości `choice` dyskretne. `choice` Może to być:

* co najmniej jedna wartość rozdzielona przecinkami
* obiekt `range`
* dowolny `list` obiekt


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

W tym przypadku jedna z wartości `batch_size` [16, 32, 64, 128] i przyjmuje jedną z wartości `number_of_hidden_layers` [1, 2, 3, 4].

Następujące zaawansowane hiperparametry dyskretne można również określić przy użyciu dystrybucji:

* `quniform(low, high, q)` - Zwraca wartość, na przykład round(uniform(low, high) /q) * q
* `qloguniform(low, high, q)` - Zwraca wartość, na przykład round(exp(uniform(low, high)) / q) * q
* `qnormal(mu, sigma, q)` - Zwraca wartość, na przykład round(normal(mu, sigma) /q) * q
* `qlognormal(mu, sigma, q)` - Zwraca wartość, na przykład round(exp(normal(mu, sigma)) / q) * q

### <a name="continuous-hyperparameters"></a>Hiperparametry ciągłe 

Hiperparametry ciągłe są określane jako rozkład w ciągłym zakresie wartości:

* `uniform(low, high)` - Zwraca wartość równomiernie rozmieszczaną między wartościami niskimi i wysokimi
* `loguniform(low, high)` - Zwraca wartość rysowaną zgodnie z exp(uniform(low, high)), tak aby logarytm wartości zwracanej był równomiernie rozłożony
* `normal(mu, sigma)` - Zwraca wartość rzeczywistą, która jest zwykle dystrybuowana ze średnią mu i odchyleniem standardowym sigma
* `lognormal(mu, sigma)` - Zwraca wartość rysowaną zgodnie z wartością exp(normal(mu, sigma)), tak aby logarytm wartości zwracanej był rozkład normalny

Przykład definicji przestrzeni parametrów:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Ten kod definiuje obszar wyszukiwania z dwoma parametrami — `learning_rate` i `keep_probability` . `learning_rate` ma rozkład normalny z wartością średnią 10 i odchyleniem standardowym o wartości 3. `keep_probability` ma jednolity rozkład z minimalną wartością 0,05 i maksymalną wartością 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Próbkowanie przestrzeni hiperparametrów

Określ metodę próbkowania parametrów, która ma być stosowana w przestrzeni hiperparametrów. Azure Machine Learning obsługuje następujące metody:

* Losowanie losowe
* Próbkowanie siatki
* Próbkowanie Bayesowskie

#### <a name="random-sampling"></a>Losowanie losowe

[Próbkowanie losowe](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling) obsługuje dyskretne i ciągłe hiperparametry. Obsługuje wczesne zakończenie przebiegów o niskiej wydajności. Niektórzy użytkownicy wykonają wstępne wyszukiwanie przy użyciu próbkowania losowego, a następnie uściślij obszar wyszukiwania, aby poprawić wyniki.

W próbkowaniu losowym wartości hiperparametrów są wybierane losowo ze zdefiniowanego obszaru wyszukiwania. 

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

[Próbkowanie siatki](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling) obsługuje dyskretne hiperparametry. Użyj próbkowania siatki, jeśli możesz uzyskać budżet, aby dokładnie przeszukać obszar wyszukiwania. Obsługuje wcześniejsze zakończenie przebiegów o niskiej wydajności.

Próbkowanie siatki umożliwia proste wyszukiwanie siatki dla wszystkich możliwych wartości. Próbkowania siatki można używać tylko z `choice` hiperparametrami. Na przykład następujące miejsce ma sześć próbek:

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

[Próbkowanie bayesów](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling) opiera się na algorytmie optymalizacji bayesów. Wybiera ona przykłady na podstawie tego, jak poprzednie próbki były, aby nowe próbki poprawiły podstawową metrykę.

Próbkowanie bayesów jest zalecane, jeśli masz wystarczająco dużo budżetu, aby zbadać obszar hiperparametrów. Aby uzyskać najlepsze wyniki, zalecamy maksymalną liczbę przebiegów większą lub równą 20-krotności liczby strojonych hiperparametrów. 

Liczba współbieżnych przebiegów ma wpływ na efektywność procesu dostrajania. Mniejsza liczba współbieżnych przebiegów może prowadzić do lepszej zbieżności próbkowania, ponieważ mniejszy stopień równoległości zwiększa liczbę przebiegów, które korzystają z wcześniej ukończonych przebiegów.

Próbkowanie Bayesa obsługuje `choice` tylko `uniform` dystrybucje , i `quniform` w przestrzeni wyszukiwania.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> Określanie metryki podstawowej

Określ [podstawową metrykę,](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal) którą chcesz zoptymalizować dostrajania hiperparametrów. Każdy przebieg trenowania jest oceniany dla metryki podstawowej. Zasady wczesnego zakończenia wykorzystują podstawową metrykę do identyfikowania przebiegów o niskiej wydajności.

Określ następujące atrybuty dla metryki podstawowej:

* `primary_metric_name`: nazwa podstawowej metryki musi dokładnie odpowiadać nazwie metryki zarejestrowanej przez skrypt trenowania
* `primary_metric_goal`: może to być metryka lub i określa, czy metryka podstawowa będzie zmaksymalizowana, czy zminimalizowana podczas `PrimaryMetricGoal.MAXIMIZE` `PrimaryMetricGoal.MINIMIZE` oceny przebiegów. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Ten przykład maksymalizuje "dokładność".

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>Metryki dzienników dostrajania hiperparametrów

Skrypt trenowania modelu **musi** rejestrować podstawową metrykę podczas trenowania modelu, aby funkcja HyperDrive może uzyskać do niego dostęp w celu dostrajania hiperparametrów.

Rejestruj podstawową metrykę w skrypcie trenowania przy użyciu następującego przykładowego fragmentu kodu:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Skrypt trenowania oblicza wartość i `val_accuracy` rejestruje ją jako "dokładność" podstawowej metryki. Za każdym razem, gdy metryka jest rejestrowana, jest odbierana przez usługę dostrajania hiperparametrów. To Ty decydujesz o częstotliwości raportowania.

Aby uzyskać więcej informacji na temat rejestrowania wartości w przebiegach trenowania modelu, zobacz [Enable logging in Azure ML training runs (Włączanie rejestrowania w](how-to-log-view-metrics.md)przebiegach trenowania usługi Azure ML).

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Określanie zasad wcześniejszego zakończenia

Automatyczne zakończenie przebiegów o niskiej jakości za pomocą zasad wczesnego zakończenia. Wczesne zakończenie zwiększa wydajność obliczeniową.

Można skonfigurować następujące parametry, które kontrolują zastosowanie zasad:

* `evaluation_interval`: częstotliwość stosowania zasad. Za każdym razem, gdy skrypt trenowania rejestruje metrykę podstawową, jest liczony jako jeden interwał. Wartość `evaluation_interval` 1 spowoduje zastosowanie zasad za każdym razem, gdy skrypt trenowania zgłasza podstawową metrykę. Dla `evaluation_interval` ustawienia 2 zasady będą stosowane za każdym razem. Jeśli nie zostanie określony, `evaluation_interval` domyślnie jest ustawiona wartość 1.
* `delay_evaluation`: opóźnia pierwszą ocenę zasad o określoną liczbę interwałów. Jest to opcjonalny parametr, który pozwala uniknąć przedwczesnego zakończenia przebiegów trenowania, zezwalając na uruchamianie wszystkich konfiguracji w minimalnej liczbie interwałów. Jeśli określono, zasady mają zastosowanie do każdej wielokrotności evaluation_interval, która jest większa lub równa delay_evaluation.

Azure Machine Learning obsługuje następujące zasady wcześniejszego zakończenia:
* [Zasady bandyta](#bandit-policy)
* [Zasady zatrzymywania mediany](#median-stopping-policy)
* [Zasady wyboru przycinania](#truncation-selection-policy)
* [Brak zasad zakończenia](#no-termination-policy-default)


### <a name="bandit-policy"></a>Zasady bandyt

[Zasady bandyt](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy#definition) są oparte na wartości współczynnika slack/wartości slack i interwale oceny. Bandyt kończy się, gdy metryka podstawowa nie znajduje się w określonym współczynniku slack/ilości slack najbardziej pomyślnego uruchomienia.

> [!NOTE]
> Próbkowanie Bayesa nie obsługuje wcześniejszego zakończenia. W przypadku korzystania z próbkowania Bayesa ustaw wartość `early_termination_policy = None` .

Określ następujące parametry konfiguracji:

* `slack_factor` lub `slack_amount` : dozwolone są zapasy w odniesieniu do przebiegów trenowania o najlepszej jakości. `slack_factor` określa dopuszczalne zapasy jako współczynnik. `slack_amount` określa luz jako wartość bezwzględną, a nie współczynnik.

    Rozważmy na przykład zasady bandyt stosowane w interwale 10. Załóżmy, że najbardziej wydajny przebieg w interwale 10 zgłosił metrykę podstawową o wartości 0,8, aby zmaksymalizować metrykę podstawową. Jeśli zasady określają wartość 0,2, wszystkie przebiegi trenowania, których najlepsza metryka w interwale 10 jest mniejsza niż `slack_factor` 0,66 (0,8/(1+ )), zostanie `slack_factor` zakończona.
* `evaluation_interval`: (opcjonalnie) częstotliwość stosowania zasad
* `delay_evaluation`: (opcjonalnie) opóźnia pierwszą ocenę zasad o określoną liczbę interwałów


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

W tym przykładzie zasady wczesnego zakończenia są stosowane w każdym interwale, gdy są zgłaszane metryki, począwszy od interwału oceny 5. Każdy przebieg, którego najlepsza metryka jest mniejsza niż (1/(1+0,1) lub 91% przebiegów o najlepszej wydajności, zostanie zakończony.

### <a name="median-stopping-policy"></a>Zasady zatrzymywania mediany

[Zatrzymanie mediany](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy) to zasady wczesnego zakończenia oparte na średnich uruchomieniach metryk podstawowych zgłaszanych przez przebiegi. Ta zasada oblicza średnie bieżące we wszystkich przebiegach trenowania i zatrzymuje przebiegi, których podstawowa wartość metryki jest gorsza niż mediana średnich.

Te zasady mają następujące parametry konfiguracji:
* `evaluation_interval`: częstotliwość stosowania zasad (parametr opcjonalny).
* `delay_evaluation`: opóźnia pierwszą ocenę zasad dla określonej liczby interwałów (parametr opcjonalny).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

W tym przykładzie zasady wczesnego zakończenia są stosowane w każdym interwale, począwszy od interwału oceny 5. Przebieg jest zatrzymywany w interwale 5, jeśli jego najlepsza metryka podstawowa jest gorsza niż mediana średnich uruchomionych w interwałach 1:5 we wszystkich przebiegach trenowania.

### <a name="truncation-selection-policy"></a>Zasady wyboru przycinania

[Wybór przycinania anuluje](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy) procent najmniej wydajnych przebiegów w każdym interwale oceny. Przebiegi są porównywane przy użyciu metryki podstawowej. 

Te zasady mają następujące parametry konfiguracji:

* `truncation_percentage`: procent przebiegów o najniższej wykonywaniu do zakończenia w każdym interwale oceny. Wartość całkowita z wartości od 1 do 99.
* `evaluation_interval`: (opcjonalnie) częstotliwość stosowania zasad
* `delay_evaluation`: (opcjonalnie) opóźnia pierwszą ocenę zasad o określoną liczbę interwałów


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

W tym przykładzie zasady wczesnego zakończenia są stosowane w każdym interwale, począwszy od interwału oceny 5. Przebieg kończy się w interwale 5, jeśli jego wydajność w interwale 5 jest w najniższym 20% wydajności wszystkich przebiegów w interwale 5.

### <a name="no-termination-policy-default"></a>Brak zasad zakończenia (ustawienie domyślne)

Jeśli żadne zasady nie zostaną określone, usługa dostrajania hiperparametrów pozwoli na ukończenie wszystkich przebiegów trenowania.

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>Wybieranie zasad wcześniejszego zakończenia

* W przypadku zachowawczych zasad, które zapewniają oszczędności bez przerywania obietnicowych zadań, należy wziąć pod uwagę zasady zatrzymywania mediany z `evaluation_interval` wartościami 1 i `delay_evaluation` 5. Są to ustawienia zachowawcze, które mogą zapewnić oszczędności na poziomie około 25–35% bez utraty podstawowej metryki (na podstawie naszych danych oceny).
* Aby uzyskać bardziej agresywne oszczędności, użyj zasad Bandit Policy z mniejszymi dopuszczalnymi usługami Slack lub Truncation Selection Policy z większym procentem przycinania.

## <a name="create-and-assign-resources"></a>Tworzenie i przypisywanie zasobów

Kontroluj budżet zasobów, określając maksymalną liczbę przebiegów trenowania.

* `max_total_runs`: maksymalna liczba przebiegów trenowania. Musi być liczbą całkowitą z od 1 do 1000.
* `max_duration_minutes`: (opcjonalnie) Maksymalny czas trwania eksperymentu dostrajania hiperparametrów w minutach. Przebiegi po tym czasie trwania zostaną anulowane.

>[!NOTE] 
>Jeśli określono wartości i , eksperyment `max_total_runs` dostrajania hiperparametrów kończy działanie po osiągnięciu pierwszego z `max_duration_minutes` tych dwóch progów.

Ponadto określ maksymalną liczbę przebiegów trenowania, które mają być uruchamiane współbieżnie podczas wyszukiwania dostrajania hiperparametrów.

* `max_concurrent_runs`: (opcjonalnie) Maksymalna liczba przebiegów, które mogą być uruchamiane współbieżnie. Jeśli nie zostanie określony, wszystkie przebiegi będą uruchamiane równolegle. Jeśli jest określony, musi być liczbą całkowitą z zakresie od 1 do 100.

>[!NOTE] 
>Liczba współbieżnych przebiegów jest określana na podstawie zasobów dostępnych w określonym docelowym obiektem obliczeniowym. Upewnij się, że docelowy obiekt obliczeniowy ma dostępne zasoby dla żądanej współbieżności.

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Ten kod konfiguruje eksperyment dostrajania hiperparametrów tak, aby używać maksymalnie 20 przebiegów, uruchamiając cztery konfiguracje jednocześnie.

## <a name="configure-hyperparameter-tuning-experiment"></a>Konfigurowanie eksperymentu dostrajania hiperparametrów

Aby [skonfigurować eksperyment dostrajania hiperparametrów,](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig) podaj następujące informacje:
* Zdefiniowana przestrzeń wyszukiwania hiperparametrów
* Twoje zasady wcześniejszego zakończenia
* Metryka podstawowa
* Ustawienia alokacji zasobów
* ScriptRunConfig `script_run_config`

ScriptRunConfig to skrypt trenowania, który będzie uruchamiany z próbkami hiperparametrów. Definiuje ona zasoby na zadanie (jednowęzłowe lub wielowęzłowe) oraz docelowy obiekt obliczeniowy do użycia.

> [!NOTE]
>Docelowy obiekt obliczeniowy używany w programie musi mieć wystarczającą ilość zasobów, aby spełnić wymagania poziomu `script_run_config` współbieżności. Aby uzyskać więcej informacji na temat polecenia ScriptRunConfig, zobacz Configure training runs (Konfigurowanie [przebiegów trenowania).](how-to-set-up-training-targets.md)

Skonfiguruj eksperyment dostrajania hiperparametrów:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
from azureml.train.hyperdrive import RandomParameterSampling, BanditPolicy, uniform, PrimaryMetricGoal

param_sampling = RandomParameterSampling( {
        'learning_rate': uniform(0.0005, 0.005),
        'momentum': uniform(0.9, 0.99)
    }
)

early_termination_policy = BanditPolicy(slack_factor=0.15, evaluation_interval=1, delay_evaluation=10)

hd_config = HyperDriveConfig(run_config=script_run_config,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

Ustawia `HyperDriveConfig` parametry przekazywane do `ScriptRunConfig script_run_config` . Z `script_run_config` kolei parametr przekazuje do skryptu trenowania. Powyższy fragment kodu pochodzi z przykładowego notesu [Trenuj, dostrajanie hiperparametryczne i wdrażaj za pomocą programu PyTorch.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch) W tym przykładzie `learning_rate` parametry `momentum` i zostaną dostrojone. Wczesne zatrzymywanie przebiegów będzie określane przez parametr , który zatrzymuje przebieg, którego metryka podstawowa znajduje się poza parametrem (zobacz informacje o klasach `BanditPolicy` `slack_factor` [BanditPolicy).](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy) 

Poniższy kod z przykładu pokazuje, jak strojone wartości są odbierane, analizowane i przekazywane do funkcji skryptu `fine_tune_model` trenowania:

```python
# from pytorch_train.py
def main():
    print("Torch version:", torch.__version__)

    # get command-line arguments
    parser = argparse.ArgumentParser()
    parser.add_argument('--num_epochs', type=int, default=25,
                        help='number of epochs to train')
    parser.add_argument('--output_dir', type=str, help='output directory')
    parser.add_argument('--learning_rate', type=float,
                        default=0.001, help='learning rate')
    parser.add_argument('--momentum', type=float, default=0.9, help='momentum')
    args = parser.parse_args()

    data_dir = download_data()
    print("data directory is: " + data_dir)
    model = fine_tune_model(args.num_epochs, data_dir,
                            args.learning_rate, args.momentum)
    os.makedirs(args.output_dir, exist_ok=True)
    torch.save(model, os.path.join(args.output_dir, 'model.pt'))
```

> [!Important]
> Każdy przebieg hiperparametru powoduje ponowne uruchomienie trenowania od podstaw, w tym ponowne skompilowanie modelu i _wszystkich modułu ładującego dane_. Ten koszt można zminimalizować, używając potoku Azure Machine Learning lub procesu ręcznego, aby przygotować jak najwięcej danych przed przebiegami trenowania. 

## <a name="submit-hyperparameter-tuning-experiment"></a>Przesyłanie eksperymentu dostrajania hiperparametrów

Po zdefiniowaniu konfiguracji dostrajania hiperparametrów [prześlij eksperyment](/python/api/azureml-core/azureml.core.experiment%28class%29#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-hyperparameter-tuning-optional"></a>Dostrajanie hiperparametrów na gorąco początek (opcjonalnie)

Znalezienie najlepszych wartości hiperparametrów dla modelu może być procesem iteracyjnym. Możesz ponownie użyć wiedzy z pięciu poprzednich przebiegów, aby przyspieszyć dostrajanie hiperparametrów.

Uruchamianie na ciepło jest obsługiwane inaczej w zależności od metody próbkowania:
- **Próbkowanie bayesów:** Próby z poprzedniego uruchomienia są używane jako wcześniejsza wiedza do pobierania nowych próbek i ulepszania podstawowej metryki.
- **Próbkowanie losowe** **lub próbkowanie siatki:** Wczesne zakończenie używa wiedzy z poprzednich przebiegów w celu określenia przebiegów o niskiej jakości. 

Określ listę przebiegów nadrzędnych, z których chcesz rozpocząć ciepło.

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Jeśli eksperyment dostrajania hiperparametrów zostanie anulowany, możesz wznowić przebiegi trenowania od ostatniego punktu kontrolnego. Jednak skrypt trenowania musi obsługiwać logikę punktów kontrolnych.

Przebieg trenowania musi korzystać z tej samej konfiguracji hiperparametrów i zainstalowane foldery danych wyjściowych. Skrypt trenowania musi akceptować argument , który zawiera pliki punktu kontrolnego lub `resume-from` modelu, z których ma zostać wznowiony przebieg trenowania. Poszczególne przebiegi trenowania można wznowić przy użyciu następującego fragmentu kodu:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Eksperyment dostrajania hiperparametrów można skonfigurować tak, aby rozgrzewał się z poprzedniego eksperymentu, lub wznowić poszczególne przebiegi trenowania przy użyciu parametrów opcjonalnych i `resume_from` `resume_child_runs` w konfiguracji:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hd_config = HyperDriveConfig(run_config=script_run_config,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="visualize-hyperparameter-tuning-runs"></a>Wizualizowanie przebiegów dostrajania hiperparametrów

Możesz zwizualizować przebiegi dostrajania hiperparametrów w Azure Machine Learning studio lub użyć widżetu notesu.

### <a name="studio"></a>Studio

Wszystkie przebiegi dostrajania hiperparametrów można wizualizować w Azure Machine Learning studio [.](https://ml.azure.com) Aby uzyskać więcej informacji na temat wyświetlania eksperymentu w portalu, zobacz [Wyświetlanie rekordów przebiegów w studio.](how-to-log-view-metrics.md#view-the-experiment-in-the-web-portal)

- **Wykres metryk:** ta wizualizacja śledzi metryki zarejestrowane dla każdego uruchomienia podrzędnego funkcji hyperdrive w czasie trwania dostrajania hiperparametryowego. Każdy wiersz reprezentuje przebieg podrzędny, a każdy punkt mierzy wartość podstawowej metryki w tej iteracji środowiska uruchomieniowego.  

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-metrics.png" alt-text="Wykres metryk dostrajania hiperparametrów":::

- **Wykres współrzędnych równoległych:** ta wizualizacja przedstawia korelację między wydajnością metryki podstawowej a poszczególnymi wartościami hiperparametrów. Wykres jest interakcyjny za pośrednictwem przesuwania osi (klikanie i przeciąganie według etykiety osi) oraz wyróżnianie wartości na jednej osi (kliknięcie i przeciągnięcie w pionie wzdłuż jednej osi w celu wyróżnienia zakresu żądanych wartości).

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates.png" alt-text="Wykres współrzędnych dostrajania hiperparametrów":::

- **Dwuwymiarowy wykres punktowy:** ta wizualizacja przedstawia korelację między dowolnymi dwoma poszczególnymi hiperparametrami wraz ze skojarzoną z nimi wartością metryki podstawowej.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-2-dimensional-scatter.png" alt-text="Strojenie hiperparametryczne dwuwymiarowego wykresu punktowego":::

- **3-wymiarowy wykres** punktowy: ta wizualizacja jest taka sama jak 2D, ale umożliwia trzy hiperparametryczne wymiary korelacji z podstawową wartością metryki. Możesz również kliknąć i przeciągnąć, aby zmienić orientację wykresu w celu wyświetlenia różnych korelacji w przestrzeni 3D.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-3-dimensional-scatter.png" alt-text="Strojenie hiperparametryczne 3-wymiarowego wykresu punktowego":::

### <a name="notebook-widget"></a>Widżet notesu

Użyj [widżetu Notes,](/python/api/azureml-widgets/azureml.widgets.rundetails) aby zwizualizować postęp przebiegów trenowania. Poniższy fragment kodu wizualizuje wszystkie przebiegi dostrajania hiperparametrów w jednym miejscu w notesie Jupyter:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Ten kod wyświetla tabelę ze szczegółami przebiegów trenowania dla każdej konfiguracji hiperparametrów.

:::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-table.png" alt-text="Tabela dostrajania hiperparametrów":::

Można również wizualizować wydajność każdego z przebiegów w trakcie trenowania.

## <a name="find-the-best-model"></a>Znajdowanie najlepszego modelu

Po zakończeniu wszystkich przebiegów dostrajania hiperparametrów zidentyfikuj najlepszą konfigurację i wartości hiperparametrów:

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

## <a name="sample-notebook"></a>Przykładowy notes

Zapoznaj się z notesami train-hyperparameter-* w tym folderze:
* [how-to-use-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki
* [Śledzenie eksperymentu](how-to-log-view-metrics.md)
* [Wdrażanie przeszkolonego modelu](how-to-deploy-and-where.md)
