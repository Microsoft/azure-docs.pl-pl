---
title: Dostrajanie parametrów dla modelu
titleSuffix: Azure Machine Learning
description: Wydajnie Dostrajaj parametry dla modelu uczenie głębokiego i uczenia maszynowego przy użyciu Azure Machine Learning. Dowiesz się, jak definiować miejsce wyszukiwania parametrów, określać metrykę podstawową do optymalizacji i wczesne zakończenie działania.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 44616d5d90f9c5c3a4f3abf8b8cf2128dc4f0585
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333804"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Dostrajanie parametrów dla modelu za pomocą Azure Machine Learning


Efektywne dostrajanie parametrów dla modelu przy użyciu Azure Machine Learning.  Dostrajanie parametrów obejmuje następujące kroki:

* Zdefiniuj miejsce przeszukiwania parametrów
* Określ metrykę podstawową do optymalizacji  
* Określ kryteria wczesnej przyczyny dla niewłaściwie wykonywanych przebiegów
* Przydziel zasoby dla strojenia parametru
* Uruchom eksperyment z powyższą konfiguracją
* Wizualizuj przebiegi szkoleniowe
* Wybierz najlepszą konfigurację dla modelu

## <a name="what-are-hyperparameters"></a>Co to są parametry?

Parametry są ustawianymi parametrami służącymi do uczenia modelu, który zarządza procesem szkoleniowym. Na przykład aby przeprowadzić uczenie głębokiej sieci neuronowych, należy określić liczbę ukrytych warstw w sieci oraz liczbę węzłów w każdej warstwie przed przeszkoleniem modelu. Te wartości zwykle pozostają stałe podczas procesu szkoleniowego.

W scenariuszach uczenia głębokiego/uczenia maszynowego model wydajności jest zależny od wybranych wartości parametru. Celem eksplorowania parametrów jest wyszukiwanie w różnych konfiguracjach parametrów, aby znaleźć konfigurację, która skutkuje najlepszą wydajnością. Zazwyczaj proces eksploracji parametrów jest żmudne ręczny, pod kątem tego, że miejsce wyszukiwania jest ogromne i Ocena każdej konfiguracji może być kosztowna.

Azure Machine Learning umożliwia zautomatyzowanie eksplorowania parametrów w wydajny sposób i oszczędność czasu i zasobów. Należy określić zakres wartości parametrów i maksymalną liczbę przebiegów szkoleniowych. System automatycznie uruchamia wiele równoczesnych uruchomień z różnymi konfiguracjami parametrów i odnajduje konfigurację, która skutkuje najlepszą wydajnością, mierzoną przez wybraną metrykę. Niewłaściwie wykonywane przebiegi szkoleniowe są automatycznie kończone, co zmniejsza marnowanie zasobów obliczeniowych. Te zasoby są zamiast tego używane do eksplorowania innych konfiguracji parametrów.


## <a name="define-search-space"></a>Zdefiniuj obszar wyszukiwania

Automatyczne dostrajanie parametrów przez Eksplorowanie zakresu wartości zdefiniowanych dla każdego z parametrów.

### <a name="types-of-hyperparameters"></a>Typy parametrów

Każdy parametr może być dyskretny lub ciągły i ma rozkład wartości opisany przez [wyrażenie parametru](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py&preserve-view=true).

#### <a name="discrete-hyperparameters"></a>Dyskretne parametry 

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

W tym przypadku `batch_size` przyjmuje jedną z wartości [16, 32, 64, 128] i `number_of_hidden_layers` przyjmuje jedną z wartości [1, 2, 3, 4].

Zaawansowane dyskretne parametry można także określić przy użyciu dystrybucji. Obsługiwane są następujące dystrybucje:

* `quniform(low, high, q)` -Zwraca wartość jak Round (Uniform (Low, High)/q) * q
* `qloguniform(low, high, q)` -Zwraca wartość, taką jak Round (EXP (Uniform (Low, High))/q) * q
* `qnormal(mu, sigma, q)` -Zwraca wartość, jak Round (Normal, Sigma)/q) * q
* `qlognormal(mu, sigma, q)` -Zwraca wartość, np. Round (EXP (normalne (mu, Sigma))/q) * q

#### <a name="continuous-hyperparameters"></a>Parametry ciągłe 

Parametry ciągłe są określane jako dystrybucja nad ciągłym zakresem wartości. Obsługiwane dystrybucje obejmują:

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

Można również określić metodę próbkowania parametrów, która ma być używana w definicji przestrzeni parametru. Azure Machine Learning obsługuje Próbkowanie losowe, próbkowanie siatki i próbkowanie bayesowskie.

#### <a name="picking-a-sampling-method"></a>Pobieranie metody próbkowania

* Próbkowanie siatki może być używane, jeśli miejsce na parametrze można zdefiniować jako wybór spośród dyskretnych wartości, a jeśli masz wystarczający budżet, aby wyczerpująco przeszukiwać wszystkie wartości w zdefiniowanym obszarze wyszukiwania. Ponadto jeden może korzystać z zautomatyzowanego wczesnego zakończenia działania, co zmniejsza marnowanie zasobów.
* Próbkowanie losowe umożliwia przełączenie między parametrami dyskretnych i ciągłych. W tej chwili daje ona dobre wyniki, a także umożliwia korzystanie z zautomatyzowanego wczesnego zakończenia uruchamiania. Niektórzy użytkownicy wykonują początkowe wyszukiwanie przy użyciu próbkowania losowego, a następnie iteracyjnie ograniczają miejsce wyszukiwania, aby poprawić wyniki.
* Próbkowanie bayesowskie wykorzystuje znajomość poprzednich przykładów podczas wybierania wartości parametrów, efektywnie próbując zwiększyć zgłoszoną metrykę podstawową. Bayesowskie próbkowanie jest zalecane, gdy masz wystarczający budżet do eksplorowania miejsca na parametrze — w celu uzyskania najlepszych wyników z próbkowania bayesowskie zalecamy użycie maksymalnej liczby przebiegów o wartości większej lub równej 20 razy. Należy pamiętać, że próbkowanie bayesowskie nie obsługuje obecnie żadnych zasad wczesnego zakończenia.

#### <a name="random-sampling"></a>Próbkowanie losowe

W przypadku próbkowania losowego wartości parametrów są losowo wybierane ze zdefiniowanego miejsca wyszukiwania. [Próbkowanie losowe](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py&preserve-view=true) umożliwia miejsce do wyszukiwania, w którym są uwzględniane parametry dyskretne i ciągłe.

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

[Próbkowanie siatki](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py&preserve-view=true) wykonuje proste przeszukiwanie siatki dla wszystkich dopuszczalnych wartości w zdefiniowanym obszarze wyszukiwania. Może być używana tylko z parametrami podanymi przy użyciu `choice` . Na przykład następujące miejsce zawiera łącznie sześć próbek:

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

[Próbkowanie bayesowskie](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py&preserve-view=true) jest oparte na algorytmie optymalizacji bayesowskie i umożliwia inteligentne wybór wartości parametrów w celu próbkowania dalej. Wybiera przykład w zależności od tego, jak poprzednie przykłady są wykonywane, w taki sposób, że nowy przykład zwiększa raportowaną metrykę podstawową.

W przypadku korzystania z próbkowania bayesowskie liczba współbieżnych uruchomień ma wpływ na efektywność procesu dostrajania. Zwykle mniejsza liczba współbieżnych uruchomień może prowadzić do lepszego próbkowania zbieżności, ponieważ mniejszy stopień równoległości zwiększa liczbę przebiegów, które korzystają z wcześniej ukończonych przebiegów.

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

> [!NOTE]
> Próbkowanie bayesowskie nie obsługuje żadnych zasad wczesnego zakończenia (zobacz [Określanie zasad wczesnego zakończenia](#early-termination)). W przypadku korzystania z próbkowania parametru bayesowskie, ustawiania `early_termination_policy = None` lub pozostawiania `early_termination_policy` parametru.

## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> Określ metrykę podstawową

Określ [metrykę podstawową](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py&preserve-view=true) , która ma zostać zoptymalizowana przez dostrajanie parametrów. Poszczególne przebiegi szkoleniowe są oceniane dla metryki podstawowej. Niewłaściwie wykonywane przebiegi (gdy Metryka podstawowa nie spełnia kryteriów ustawionych przez zasady wczesnego zakończenia) zostanie zakończona. Oprócz nazwy metryki głównej należy również określić cel optymalizacji — czy można zmaksymalizować lub zminimalizować podstawową metrykę.

* `primary_metric_name`: Nazwa metryki głównej do optymalizacji. Nazwa metryki głównej musi dokładnie pasować do nazwy metryki rejestrowanej przez skrypt szkoleniowy. Zobacz [metryki dziennika dla dostrajania parametrów](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Może to być albo `PrimaryMetricGoal.MAXIMIZE` `PrimaryMetricGoal.MINIMIZE` i i określać, czy Metryka podstawowa będzie zmaksymalizowana, czy zminimalizowana podczas oceniania przebiegów. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Zoptymalizuj przebiegi w celu zmaksymalizowania "dokładności".  Upewnij się, że ta wartość jest zarejestrowana w skrypcie szkoleniowym.

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>Metryki dzienników dla strojenia parametru

Skrypt szkoleniowy dla Twojego modelu musi rejestrować odpowiednie metryki podczas uczenia modelu. Podczas konfigurowania dostrajania parametru należy określić metrykę główną, która będzie używana do oceny wydajności uruchamiania. (Zobacz [Określ metrykę podstawową do optymalizacji](#specify-primary-metric-to-optimize)).  W skrypcie szkoleniowym należy zarejestrować tę metrykę, aby była dostępna dla procesu strojenia parametru.

Zarejestruj tę metrykę w skrypcie szkoleniowym, korzystając z następującego fragmentu przykładu:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Skrypt szkoleniowy oblicza `val_accuracy` i rejestruje ją jako "dokładność", która jest używana jako Metryka podstawowa. Za każdym razem, gdy Metryka jest rejestrowana, jest odbierana przez usługę strojenia parametru. Aby określić częstotliwość raportowania tej metryki, należy do dewelopera modelu.

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Określ zasady wczesnego zakończenia

Bezbłędnie wykonuj uruchomienia automatycznie z zasadami wczesnego zakończenia. Przerwanie zmniejsza liczbę marnowanych zasobów i zamiast tego używa tych zasobów do eksplorowania innych konfiguracji parametrów.

Korzystając z zasad wczesnego zakończenia, można skonfigurować następujące parametry, które kontrolują, kiedy zasady są stosowane:

* `evaluation_interval`: częstotliwość stosowania zasad. Za każdym razem, gdy skrypt szkoleniowy rejestruje podstawową metrykę jako jeden interwał. W ten sposób `evaluation_interval` 1 zastosuje zasady za każdym razem, gdy skrypt szkoleniowy zgłosi podstawową metrykę. `evaluation_interval`2 zastosuje zasady za każdym razem, gdy skrypt szkoleniowy zgłosi podstawową metrykę. Jeśli nie zostanie określona, `evaluation_interval` jest domyślnie ustawiona na 1.
* `delay_evaluation`: opóźnia pierwszą ocenę zasad przez określoną liczbę interwałów. Jest to opcjonalny parametr, który umożliwia uruchamianie wszystkich konfiguracji w początkowej minimalnej liczbie interwałów, unikając przedwczesnego zakończenia przebiegów szkoleniowych. Jeśli ta wartość jest określona, zasady stosują się do każdej wielokrotności evaluation_interval, która jest większa lub równa delay_evaluation.

Azure Machine Learning obsługuje następujące zasady wczesnego zakończenia.

### <a name="bandit-policy"></a>Zasady Bandit

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py&preserve-view=true#&preserve-view=truedefinition) to zasady zakończenia na podstawie wartości współczynnika zapasu/zapasu czasu i interwału ewaluacji. Zasady wczesnie kończą wszystkie uruchomienia, w których podstawowa Metryka nie mieści się w określonym współczynniku zapasu/zapasu czasu, w odniesieniu do najlepszego przebiegu szkoleniowego. Przyjmuje następujące parametry konfiguracji:

* `slack_factor` lub `slack_amount` : zapasowy, który jest dozwolony w odniesieniu do najlepszego przebiegu szkoleniowego. `slack_factor` Określa dopuszczalny zapas czasu jako współczynnik. `slack_amount` Określa dozwolony czas zapasowy jako ilość bezwzględną, a nie współczynnik.

    Rozważmy na przykład, że zasady Bandit są stosowane w interwale 10. Przyjęto założenie, że najlepiej działający przebieg w interwale 10 zgłosił podstawową metrykę 0,8 z celem, aby zmaksymalizować podstawową metrykę. Jeśli zasady zostały określone z `slack_factor` 0,2, każdy przebieg szkolenia, którego Najlepsza Metryka w interwale 10 jest mniejsza niż 0,66 (0,8/(1 + `slack_factor` )) zostanie zakończony. Jeśli zamiast tego, zasady zostały określone z `slack_amount` 0,2, wszelkie przebiegi szkoleniowe, których Najlepsza Metryka w interwale 10 jest mniejsza niż 0,6 (0,8- `slack_amount` ) zostanie zakończony.
* `evaluation_interval`: częstotliwość stosowania zasad (parametr opcjonalny).
* `delay_evaluation`: opóźnia pierwsze oszacowanie zasad dla określonej liczby interwałów (parametr opcjonalny).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

W tym przykładzie zasady wczesnej zakończenia są stosowane w każdym interwale, gdy raportowane są metryki, rozpoczynając od interwału próbnego 5. Każdy przebieg, którego Najlepsza Metryka jest mniejsza niż (1/(1 + 0,1) lub 91% najlepszego przebiegu, zostanie zakończony.

### <a name="median-stopping-policy"></a>Średnie zatrzymywanie zasad

[Mediana zatrzymywana](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py&preserve-view=true) to zasady wczesnej zakończenia na podstawie średnich podstawowych metryk raportowanych przez uruchomienia. Te zasady obliczą średnie uruchomienia w ramach wszystkich przebiegów szkoleniowych i kończy przebiegi, których wydajność jest gorsza niż wartość mediana średnich. Te zasady pobierają następujące parametry konfiguracji:
* `evaluation_interval`: częstotliwość stosowania zasad (parametr opcjonalny).
* `delay_evaluation`: opóźnia pierwsze oszacowanie zasad dla określonej liczby interwałów (parametr opcjonalny).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

W tym przykładzie zasady wczesnej zakończenia są stosowane w każdym interwale, rozpoczynając od okresu ewaluacyjnego 5. Przebieg zostanie zakończony z upływem interwału 5, jeśli jego Najlepsza Metryka podstawowa jest gorsza niż wartość mediana średnich wartości z przedziałów 1:5 dla wszystkich przebiegów szkoleniowych.

### <a name="truncation-selection-policy"></a>Zasady wyboru obcinania

[Zaznaczenie obcięcia](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py&preserve-view=true) powoduje anulowanie danego procentu najniższych przebiegów wykonywanych w każdym interwale oceny. Przebiegi są porównywane na podstawie ich wydajności w głównej metryki, a najniższy X% kończy się. Przyjmuje następujące parametry konfiguracji:

* `truncation_percentage`: procent najniższych przebiegów wykonywanych w każdym interwale oceny. Określ liczbę całkowitą z zakresu od 1 do 99.
* `evaluation_interval`: częstotliwość stosowania zasad (parametr opcjonalny).
* `delay_evaluation`: opóźnia pierwsze oszacowanie zasad dla określonej liczby interwałów (parametr opcjonalny).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

W tym przykładzie zasady wczesnej zakończenia są stosowane w każdym interwale, rozpoczynając od okresu ewaluacyjnego 5. Przebieg zostanie zakończony z upływem interwału 5, jeśli jego wydajność w interwale 5 jest w najmniejszej 20% wydajności wszystkich przebiegów w interwale 5.

### <a name="no-termination-policy"></a>Brak zasad zakończenia

Jeśli chcesz, aby wszystkie uruchomienia szkolenia zostały wykonane, ustaw wartość Brak. Będzie to miało wpływ na niestosowanie zasad wczesnego zakończenia.

```Python
policy=None
```

### <a name="default-policy"></a>Zasady domyślne

Jeśli żadna zasada nie zostanie określona, usługa strojenia parametrów będzie zezwalać na wykonywanie wszystkich operacji szkoleniowych.

### <a name="picking-an-early-termination-policy"></a>Wybieranie zasad wczesnego zakończenia

* Jeśli szukasz zasad, które zapewniają oszczędności bez kończenia zadań obietnicy, możesz użyć środkowych zasad zatrzymywania z `evaluation_interval` 1 i `delay_evaluation` 5. Są to ustawienia, które mogą zapewnić około 25%-35% oszczędności bez utraty podstawowej metryki (na podstawie naszych danych oceny).
* Jeśli szukasz bardziej agresywnego oszczędności ze wczesnego zakończenia, możesz użyć zasad Bandit z bardziej restrykcyjną (mniejszą) lub zasadą wyboru obcinania z większym procentem obcięcia.

## <a name="allocate-resources"></a>Przydzielanie zasobów

Kontroluj budżet zasobów dla eksperymentu strojenia parametrów, określając maksymalną łączną liczbę przebiegów szkoleniowych.  Opcjonalnie można określić maksymalny czas trwania eksperymentu strojenia parametru.

* `max_total_runs`: Maksymalna łączna liczba uruchomień szkoleniowych, które zostaną utworzone. Górna granica — może być mniej przebiegów, na przykład jeśli przestrzeń parametru hiperprzestrzeni jest skończona i ma mniejszą liczbę próbek. Musi być liczbą z przedziału od 1 do 1000.
* `max_duration_minutes`: Maksymalny czas trwania eksperymentu strojenia parametru w minutach. Parametr jest opcjonalny, a jeśli jest obecny, wszystkie uruchomienia, które byłyby uruchomione po tym czasie, są automatycznie anulowane.

>[!NOTE] 
>Jeśli oba `max_total_runs` i `max_duration_minutes` są określone, eksperyment strojenia parametrów kończy się, gdy zostanie osiągnięty pierwszy z tych dwóch progów.

Ponadto Określ maksymalną liczbę przebiegów szkoleniowych uruchamianych współbieżnie podczas wyszukiwania dostrajania parametrów.

* `max_concurrent_runs`: Maksymalna liczba przebiegów uruchomionych współbieżnie w danym momencie. Jeśli żaden nie zostanie określony, wszystkie `max_total_runs` będą uruchamiane równolegle. Jeśli ta wartość jest określona, musi być liczbą z przedziału od 1 do 100.

>[!NOTE] 
>Liczba współbieżnych uruchomień jest zależna od zasobów dostępnych w określonym elemencie docelowym obliczeń. Z tego względu należy upewnić się, że obiekt docelowy obliczeń ma dostępne zasoby dla żądanego współbieżności.

Przydziel zasoby dla strojenia parametru:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Ten kod umożliwia skonfigurowanie eksperymentu strojenia parametrów w celu użycia maksymalnie 20 łącznych uruchomień z czterema konfiguracjami jednocześnie.

## <a name="configure-experiment"></a>Konfigurowanie eksperymentu

[Skonfiguruj eksperyment strojenia parametrów](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py&preserve-view=true) przy użyciu zdefiniowanego miejsca wyszukiwania z parametrami, zasad wczesnego zakończenia, metryki podstawowej i alokacji zasobów w powyższych sekcjach. Ponadto Podaj ScriptRunConfig `src` dla przebiegu, który zostanie wywołany przy użyciu parametrów z próbkami. ScriptRunConfig definiuje skrypt szkoleniowy do uruchomienia, zasoby na zadanie (jeden lub wiele węzłów) oraz miejsce docelowe obliczeń do użycia. Ponieważ współbieżność eksperymentu strojenia parametrów jest zależna od dostępnych zasobów, upewnij się, że obiekt docelowy obliczeń określony w `src` ma wystarczające zasoby dla żądanego współbieżności. (Aby uzyskać więcej informacji na temat ScriptRunConfig, zobacz [Konfigurowanie przebiegów szkoleniowych](how-to-set-up-training-targets.md)).

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

## <a name="submit-experiment"></a>Prześlij eksperyment

Po zdefiniowaniu konfiguracji strojenia parametru [Prześlij eksperyment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truesubmit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

`experiment_name` jest nazwą przypisaną do eksperymentu strojenia parametrów i `workspace` jest obszarem roboczym, w którym chcesz utworzyć eksperyment (Aby uzyskać więcej informacji na temat eksperymentów, zobacz [jak działa Azure Machine Learning?](concept-azure-machine-learning-architecture.md))

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Rozpoczęcie eksperymentu strojenia parametrów (opcjonalnie)

Często Znajdowanie wartości najlepszych parametrów dla modelu może być procesem iteracyjnym, wymagając wielu przebiegów strojenia, które uczy się od poprzednich przebiegów dostrajania. Użycie wiedzy z tych poprzednich przebiegów spowoduje przyspieszenie procesu strojenia parametrów, co zmniejsza koszt dostrajania modelu i może poprawić podstawową metrykę modelu powstającego. Po rozpoczęciu uruchamiania eksperymentu strojenia z bayesowskie próbkowania testy z poprzedniego przebiegu będą używane jako wcześniejsza wiedza, aby inteligentnie wybrać nowe przykłady, aby zwiększyć podstawową metrykę. Ponadto w przypadku próbkowania losowego lub siatki wszelkie wczesne decyzje o wygaśnięciu będą korzystać z metryk z poprzednich przebiegów, aby określić niewłaściwie wykonywane przebiegi szkoleniowe. 

Azure Machine Learning pozwala na rozgrzanie przebiegu strojenia parametrów, wykorzystując wiedzę z maksymalnie 5 poprzednio ukończonych/anulowanych przebiegów dostrojenia parametrów nadrzędnych. Możesz określić listę przebiegów nadrzędnych, z których chcesz zacząć korzystać z tego fragmentu kodu:

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Ponadto mogą wystąpić sytuacje, w których poszczególne przebiegi szkoleniowe dla eksperymentu strojenia parametrów są anulowane ze względu na ograniczenia budżetu lub niepowodzenie z powodu innych przyczyn. Teraz można wznowić takie indywidualne szkolenia z ostatniego punktu kontrolnego (przy założeniu, że skrypt szkoleniowy obsługuje punkty kontrolne). Wznowienie pojedynczego przebiegu szkoleniowego spowoduje użycie tej samej konfiguracji z parametrem i zainstalowanie folderu danych wyjściowych używanego dla tego przebiegu. Skrypt szkoleniowy powinien akceptować `resume-from` argument, który zawiera punkt kontrolny lub pliki modelu, z których ma zostać wznowione uruchomienie szkolenia. Poszczególne przebiegi szkoleniowe można wznowić, korzystając z następującego fragmentu kodu:

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

## <a name="visualize-experiment"></a>Wizualizacja eksperymentu

Zestaw Azure Machine Learning SDK udostępnia [widżet Notatnik](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py&preserve-view=true) , który wizualizuje postępy przebiegów szkoleniowych. Poniższy fragment kodu wizualizuje wszystkie przebiegi strojenia parametrów w jednym miejscu w notesie Jupyter:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Ten kod przedstawia tabelę zawierającą szczegóły dotyczące szkoleń dla każdej konfiguracji parametrów.

![Tabela strojenia parametru](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Możesz również wizualizować wydajność poszczególnych przebiegów w miarę postępów szkoleniowych. 

![Wykres strojenia parametru](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Ponadto możesz wizualnie zidentyfikować korelację między wydajnością i wartościami poszczególnych parametrów przy użyciu równoległych wykresów współrzędnych. 

[![Współrzędne równoległe strojenia parametrów](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Możesz również wizualizować wszystkie przebiegi strojenia parametrów w portalu internetowym platformy Azure. Aby uzyskać więcej informacji na temat sposobu wyświetlania eksperymentu w portalu internetowym, zobacz [jak śledzić eksperymenty](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>Znajdź najlepszy model

Po zakończeniu wszystkich przebiegów strojenia parametrów należy [zidentyfikować najlepszą wykonywaną konfigurację](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-best-run-by-primary-metric-include-failed-true--include-canceled-true--include-resume-from-runs-true-----typing-union-azureml-core-run-run--nonetype-) oraz odpowiednie wartości parametrów:

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
