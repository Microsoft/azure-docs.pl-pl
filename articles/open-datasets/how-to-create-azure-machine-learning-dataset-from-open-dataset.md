---
title: Tworzenie zestawów danych za pomocą otwartych zestawów danych platformy Azure
titleSuffix: Azure Open Datasets
description: Dowiedz się, jak utworzyć zestaw danych Azure Machine Learning z usługi Azure Open DataSets.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: a80559761c8a3eba6045db5cd99a7719dd041fa8
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91704399"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Tworzenie zestawów danych Azure Machine Learning przy użyciu otwartych zestawów danych platformy Azure

W tym artykule dowiesz się, jak przenieść nadzorowane dane wzbogacania do swoich lokalnych lub zdalnych eksperymentów w usłudze Machine Learning za pomocą zestawów danych [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) i [otwartych zestawów danych platformy Azure](https://docs.microsoft.com/azure/open-datasets/). 

Tworząc [zestaw danych Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md), utworzysz odwołanie do lokalizacji źródła danych wraz z kopią jej metadanych. Ponieważ zestawy danych są opóźnieniem oceniane, a dane pozostają w istniejącej lokalizacji, należy
* Nie Powiąż dodatkowego kosztu magazynowania.
* Nie ryzykuj przypadkowego zmiany oryginalnych źródeł danych. 
* Zwiększ szybkość działania przepływu pracy w usłudze ML.

Aby dowiedzieć się, gdzie zestawy danych mieszczą się w przepływie pracy ogólnego dostępu do danych Azure Machine Learning, zobacz artykuł dotyczący [bezpiecznego dostępu do danych](../machine-learning/concept-data.md#data-workflow) .

Otwarte zestawy danych platformy Azure są nadzorowanymi publicznymi zestawami danych, których można użyć do dodawania funkcji specyficznych dla scenariusza w celu wzbogacania rozwiązań predykcyjnych i poprawy ich dokładności. Zapoznaj się z tematem [otwieranie wykazu zestawów](https://azure.microsoft.com/en-in/services/open-datasets/catalog/) danych w celu umożliwienia uczenia modeli uczenia maszynowego, takich jak:

* [meteorologiczn](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)
* [spisu](https://azure.microsoft.com/services/open-datasets/catalog/us-decennial-census-zip/)
* [Świąteczny](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
* [bezpieczeństwo publiczne](https://azure.microsoft.com/services/open-datasets/catalog/chicago-safety-data/)
* location

Otwarte zestawy danych znajdują się w chmurze w Microsoft Azure i znajdują się w [Azure Machine Learning SDK języka Python](#create-datasets-with-the-sdk) i [Azure Machine Learning Studio](#create-datasets-with-the-studio).


## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

* [Obszar roboczy Azure Machine Learning](../machine-learning/how-to-manage-workspace.md).

* [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true ), który obejmuje `azureml-datasets` pakiet.

    * Utwórz [wystąpienie obliczeniowe Azure Machine Learning](../machine-learning/how-to-create-manage-compute-instance.md), które jest w pełni skonfigurowane i zarządzane środowisko programistyczne, które zawiera zintegrowane notesy oraz już zainstalowany zestaw SDK.

    **OR**

    * Pracuj nad własnym środowiskiem Python i samodzielnie Instaluj zestaw SDK, korzystając z [tych instrukcji](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true ).

> [!NOTE]
> Niektóre klasy zestawu danych mają zależności w pakiecie [Azure preprodukcyjnym](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) , który jest zgodny z 64-bitowym językiem Python. W przypadku użytkowników systemu Linux te klasy są obsługiwane tylko w następujących dystrybucjach: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) i CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Tworzenie zestawów danych przy użyciu zestawu SDK

Aby utworzyć Azure Machine Learning zestawy danych za pomocą usługi Azure Open DataSets Classes w zestawie Python SDK, upewnij się, że pakiet został zainstalowany w programie `pip install azureml-opendatasets` . Każdy zestaw danych dyskretnych jest reprezentowany przez własną klasę w zestawie SDK, a niektóre klasy są dostępne jako Azure Machine Learning [ `TabularDataset` , `FileDataset` ](../machine-learning/how-to-create-register-datasets.md#dataset-types)lub obie. Pełną listę klas można znaleźć w [dokumentacji referencyjnej](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py&preserve-view=true ) `opendatasets` .

Niektóre klasy można pobrać `opendatasets` z `TabularDataset` lub `FileDataset` , co pozwala na manipulowanie i/lub pobranie plików bezpośrednio. Inne klasy mogą uzyskać zestaw danych **tylko** przy użyciu `get_tabular_dataset()` funkcji lub `get_file_dataset()` z `Dataset` klasy w zestawie SDK języka Python.

Poniższy kod pokazuje, że Klasa MNIST ręcznie `opendatasets` może zwracać albo `TabularDataset` lub `FileDataset` . 


```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()
```

W tym przykładzie `opendatasets` Klasa cukrzycą jest dostępna tylko jako a `TabularDataset` , dlatego użycie `get_tabular_dataset()` .

```python

from azureml.opendatasets import Diabetes
from azureml.core import Dataset

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```
## <a name="register-datasets"></a>Rejestrowanie zestawów danych

Zarejestruj zestaw danych Azure Machine Learning za pomocą swojego obszaru roboczego, dzięki czemu możesz udostępniać je innym osobom i korzystać z nich ponownie w ramach eksperymentów w obszarze roboczym. Po zarejestrowaniu zestawu danych Azure Machine Learning utworzonego na podstawie otwartych zestawów danych żadne dane nie zostaną natychmiast pobrane, ale dane będą dostępne później na żądanie (na przykład w przypadku szkolenia) z centralnej lokalizacji magazynu.

Aby zarejestrować zestawy danych za pomocą obszaru roboczego, użyj [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-&preserve-view=true ) metody. 
```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-with-the-studio"></a>Tworzenie zestawów danych za pomocą programu Studio

Możesz również tworzyć Azure Machine Learning zestawy danych z platformy Azure otwartych zestawów danych za pomocą programu [Azure Machine Learning Studio](https://ml.azure.com), skonsolidowanego interfejsu sieci Web, który zawiera narzędzia uczenia maszynowego do wykonywania scenariuszy analizy danych dla lekarzy danych wszystkich poziomów umiejętności.

> [!Note]
> Zestawy danych utworzone za pomocą Azure Machine Learning Studio są automatycznie rejestrowane w obszarze roboczym.

1. W obszarze roboczym wybierz kartę **zestawy danych** w obszarze **zasoby**. Z menu rozwijanego **Utwórz zestaw danych** wybierz pozycję **z otwarte zestawy**danych.

    ![Otwórz zestaw danych przy użyciu interfejsu użytkownika](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. Wybierz zestaw danych, wybierając jego kafelek. (Istnieje możliwość filtrowania przy użyciu paska wyszukiwania). Wybierz pozycję **dalej**.

    ![Wybierz zestaw danych](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. Wybierz nazwę, pod którą ma zostać zarejestrowany zestaw danych, i opcjonalnie odfiltruj dane przy użyciu dostępnych filtrów. W tym przypadku dla zestawu danych **dni wolnych** , należy odfiltrować przedział czasu do jednego roku i kod kraju tylko do USA. Zobacz [wykaz otwartych zestawów danych platformy Azure](https://azure.microsoft.com/services/open-datasets/catalog) , aby uzyskać szczegółowe informacje, takie jak opisy pól i zakresy dat. Wybierz przycisk **Utwórz**.

    ![Ustaw parametry zestawu danych i Utwórz zestaw danych](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    Zestaw danych jest teraz dostępny w obszarze roboczym w obszarze **zestawy danych**. Można go użyć w taki sam sposób jak inne utworzone zestawy danych.


## <a name="access-datasets-for-your-experiments"></a>Dostęp do zestawów danych dla eksperymentów

Używaj Twoich zestawów danych w eksperymentach uczenia maszynowego do uczenia modeli ML. [Dowiedz się więcej na temat uczenia się z zestawami danych](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>Przykładowe notesy

Aby zapoznać się z przykładami i pokazami funkcji otwartych zestawów danych, zobacz te [przykładowe notesy](samples.md).

## <a name="next-steps"></a>Następne kroki

* [Uczenie swojego pierwszego modelu ml](../machine-learning/tutorial-1st-experiment-sdk-train.md).

* [Uczenie się z zestawami danych](../machine-learning/how-to-train-with-datasets.md).

* [Utwórz zestaw danych usługi Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md).



