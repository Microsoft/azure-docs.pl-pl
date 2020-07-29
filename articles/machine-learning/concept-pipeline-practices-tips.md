---
title: Iterowanie i rozwijanie potoków uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Wzorce, praktyki i wskazówki dotyczące szybkiego programowania
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 05/01/2020
ms.openlocfilehash: 2ea353469ed111eebb591aa6ba86c652683cc2f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82858180"
---
# <a name="iterating-and-evolving-machine-learning-pipelines"></a>Iterowanie i rozwijanie potoków uczenia maszynowego

Potoki Azure Machine Learning zapewniają wydajny sposób modularyzacji kodu, ponownego użycia wyników i optymalizowania zasobów obliczeniowych. Poniżej przedstawiono niektóre praktyczne wskazówki i praktyki dotyczące pracy z potokami.

## <a name="how-do-you-get-started-with-pipelines"></a>Jak rozpocząć pracę z potokami?

Istnieje kilka opcji rozpoczynania pracy, jeśli są nowe dla potoków:

* Jeśli wiesz, jak najlepiej odczytać i ponownie utworzyć proces konstruowania, artykuł [Tworzenie i uruchamianie potoków uczenia maszynowego z zestawem SDK Azure Machine Learning](how-to-create-your-first-pipeline.md) jest dobrym rozwiązaniem 
* Jeśli lubisz interaktywną naukę w notesie Jupyter, potok został utworzony w [potokach Azure Machine Learning: wprowadzenie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-getting-started.ipynb) Notes może być dla Ciebie prawidłowy
* Jeśli wolisz w pierwszej kolejności, klonowanie repozytorium [pokazów Azure Machine Learning potoku](https://github.com/microsoft/aml-pipelines-demo) jest dobrym punktem początkowym

## <a name="how-do-you-modularize-pipeline-code"></a>Jak modularyzacji kod potoku? 

Moduły i `ModuleStep` Klasa dają doskonałą okazję do modularyzacji kodu ml. Należy jednak pamiętać, że przechodzenie między etapami potoku jest znacznie droższe niż wywołanie funkcji. Pytanie, które należy zadać, nie jest tak wiele "czy te funkcje i dane koncepcyjnie różnią się od tych w innych sekcjach?" ale "czy te funkcje i dane mają być rozdzielone osobno?" lub "czy to obliczenie jest kosztowne i czy można ponownie użyć jego danych wyjściowych?" Aby uzyskać więcej informacji, zobacz thisn'tebook [How to Create module, ModuleVersion i use one in the Pipeline with ModuleStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb).

Jak wspomniano wcześniej, oddzielenie przygotowywania danych od szkoleń jest często jedną okazją. Czasami przygotowanie danych jest skomplikowane i czasochłonne, aby można było przerwać proces w osobnych krokach potoku. Inne możliwości obejmują testowanie po szkoleniu i analizę. 

## <a name="how-do-you-speed-pipeline-iteration"></a>Jak przyspieszyć iterację potoku? 

Typowe techniki umożliwiające szybkie Iterowanie potoków obejmują: 

- Klonowanie potoku (Tworzenie kopii) i szybkie uruchamianie potoku
- Zachowywanie uruchomionego wystąpienia obliczeniowego, więc aby uniknąć czasu uruchamiania
- Skonfigurowanie danych i kroków umożliwiających ponowne użycie umożliwi potoku pominięcie ponownego obliczania niezmienionych danych

Aby szybko wykonać iterację, można sklonować potok, utworzyć potok i ponownie uruchomić potok. Kolejną przydatną techniką jest to, że w przypadku utrzymania danych obliczeniowych nie będzie ponosić kosztów nanoszenia nowego obliczenia. Jeśli skonfigurujesz krok, aby umożliwić ponowne użycie wyniku przebiegu, powtórzone wykonanie będzie ponownie używać wyników, gdy jest to możliwe (w przypadku braku zmian w krokach).

## <a name="how-do-you-collaborate-using-ml-pipelines"></a>Jak współpracować z potokami ML? 

Oddzielne potoki to naturalne linie, w których należy podzielić nakład pracy. Wielu deweloperów lub nawet wielu zespołów może współdziałać nad różnymi krokami, tak długo, jak dane i argumenty przepływające między krokami są uzgodnione. 

Podczas aktywnego programowania można pobrać `PipelineRun` i `StepRun` uruchomić wyniki z obszaru roboczego, użyć tych obiektów do pobrania końcowych i pośrednich danych wyjściowych, a także użyć tych artefaktów dla własnej modularnej pracy.

## <a name="use-pipelines-to-test-techniques-in-isolation"></a>Używanie potoków do testowania technik w izolacji

Rzeczywiste rozwiązania z zakresu środowiska ML zwykle obejmują znaczące dostosowanie każdego kroku. Dane pierwotne często muszą być przygotowane w jakiś sposób: filtrowane, przekształcone i rozszerzane. Procesy szkoleniowe mogą mieć kilka potencjalnych architektur i, w przypadku uczenia głębokiego, wiele możliwych wariantów rozmiaru warstw i funkcji aktywacji. Nawet w przypadku spójnej architektury wyszukiwanie z parametrami może generować znaczący wpływ na usługę WINS.

Oprócz narzędzi, takich jak [AutoML](concept-automated-ml.md) i [zautomatyzowane wyszukiwanie parametrów](how-to-tune-hyperparameters.md), potoki mogą być ważnym narzędziem dla rozwiązań do testowania A/B. Jeśli masz kilka wariantów kroków potoku, możesz łatwo wygenerować oddzielne uruchomienia, próbując ich Wariacje: 

```python
data_preparation_variants = [data1, data2, data3]
data_augmentation_variants = [aug1, aug2]
architecture_variants = [train1, train2, train3]

# Cartesian product
all_variants = np.array(np.meshgrid(data_preparation_variants,data_augmentation_variants,architecture_variants)).T.reshape(-1,3)

pipelines = [Pipeline(workspace=ws, steps=variant.tolist(), description=str(variant)) for variant in all_variants]

```

