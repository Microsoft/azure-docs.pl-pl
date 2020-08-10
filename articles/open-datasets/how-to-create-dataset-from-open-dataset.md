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
ms.openlocfilehash: db2e493f04d74308b21e8a63222f26b1e2af8546
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038240"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Tworzenie zestawów danych Azure Machine Learning przy użyciu otwartych zestawów danych platformy Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak utworzyć zestaw danych Azure Machine Learning z [platformy Azure Open DataSets](https://azure.microsoft.com/services/open-datasets/) w celu uzyskania dostępu do danych dla eksperymentów lokalnych lub zdalnych. 

Tworząc [zestaw danych Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md), utworzysz odwołanie do lokalizacji źródła danych wraz z kopią jej metadanych. Ponieważ dane pozostają w istniejącej lokalizacji, nie pociągnie za sobą dodatkowych kosztów magazynu i nie grozi przypadkowemu zmianie oryginalnych źródeł danych. Ponadto zestawy danych są opóźnieniem oceniane, co ułatwia szybkość działania przepływu pracy.
 
Aby dowiedzieć się, gdzie zestawy danych mieszczą się w przepływie pracy ogólnego dostępu do danych Azure Machine Learning, zobacz artykuł dotyczący [bezpiecznego dostępu do danych](../machine-learning/concept-data.md#data-workflow) .


Otwarte zestawy danych znajdują się w chmurze w Microsoft Azure i znajdują się w [Azure Machine Learning SDK języka Python](#create-datasets-with-the-sdk) i [Azure Machine Learning Studio](#create-datasets-with-the-studio).

## <a name="why-use-azure-open-datasets"></a>Dlaczego warto używać otwartych zestawów danych platformy Azure? 

Otwarte zestawy danych platformy Azure mają nadzorowane zestawy danych, których można użyć do dodawania funkcji specyficznych dla scenariusza do rozwiązań uczenia maszynowego w celu uzyskania dokładniejszych modeli. Zestawy danych obejmują dane z domeny publicznej na potrzeby pogodowych, spisu, świąt, bezpieczeństwa publicznego i lokalizacji, które ułatwiają uczenie modeli uczenia maszynowego i wzbogacanie rozwiązań predykcyjnych. 

Aby uzyskać więcej informacji, zobacz [co to są otwarte zestawy danych?](overview-what-are-open-datasets.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby tworzyć zestawy danych i korzystać z nich, potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

* [Obszar roboczy Azure Machine Learning](../machine-learning/how-to-manage-workspace.md).

* [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), który obejmuje pakiet usługi Azure DataSets.

    * Utwórz [wystąpienie obliczeniowe Azure Machine Learning](../machine-learning/concept-compute-instance.md#managing-a-compute-instance), które jest w pełni skonfigurowane i zarządzane środowisko programistyczne, które zawiera zintegrowane notesy oraz już zainstalowany zestaw SDK.

    **ORAZ**

    * Pracuj nad własnym notesem Jupyter i samodzielnie Instaluj zestaw SDK, korzystając z [tych instrukcji](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

> [!NOTE]
> Niektóre klasy zestawu danych mają zależności w pakiecie [Azure preprodukcyjnym](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) , który jest zgodny z 64-bitowym językiem Python. W przypadku użytkowników systemu Linux te klasy są obsługiwane tylko w następujących dystrybucjach: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) i CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Tworzenie zestawów danych przy użyciu zestawu SDK

Aby utworzyć zestawy danych za pośrednictwem otwartych klas zestawów danych w Azure Machine Learning Python SDK, upewnij się, że pakiet został zainstalowany za pomocą programu `pip install azureml-opendatasets` . Każdy zestaw danych dyskretnych jest reprezentowany przez własną klasę w zestawie SDK, a niektóre klasy są dostępne jako `TabularDataset` , `FileDataset` lub obie. Pełną listę klas można znaleźć w [dokumentacji referencyjnej](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) .

Niektóre klasy można pobrać z `TabularDataset` lub `FileDataset` , co pozwala na manipulowanie i/lub pobranie plików bezpośrednio. Inne klasy mogą uzyskać zestaw danych **tylko** przy użyciu jednej `get_tabular_dataset()` lub `get_file_dataset()` funkcji. Poniższy przykład kodu pokazuje kilka przykładów tych typów klas.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Po zarejestrowaniu zestawu danych utworzonego na podstawie otwartych zestawów danych żadne dane nie są natychmiast pobierane, ale dane będą dostępne później na żądanie (na przykład na potrzeby szkolenia) z centralnej lokalizacji magazynu.

## <a name="create-datasets-with-the-studio"></a>Tworzenie zestawów danych za pomocą programu Studio

Zestawy danych można także tworzyć z otwartych zestawów danych za pomocą programu [Azure Machine Learning Studio](https://ml.azure.com). W obszarze roboczym wybierz kartę **zestawy danych** w obszarze **zasoby**. Z menu rozwijanego **Utwórz zestaw danych** wybierz pozycję **z otwarte zestawy**danych.

![Otwórz zestaw danych przy użyciu interfejsu użytkownika](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

Wybierz zestaw danych, wybierając jego kafelek. (Istnieje możliwość filtrowania przy użyciu paska wyszukiwania). Wybierz pozycję **dalej**.

![Wybierz zestaw danych](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

Wybierz nazwę, pod którą ma zostać zarejestrowany zestaw danych, i opcjonalnie odfiltruj dane przy użyciu dostępnych filtrów. W tym przypadku dla zestawu danych dni wolnych, należy odfiltrować przedział czasu do jednego roku i kod kraju tylko do USA. Wybierz pozycję **Utwórz**.

![Ustaw parametry zestawu danych i Utwórz zestaw danych](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

Zestaw danych jest teraz dostępny w obszarze roboczym w obszarze **zestawy danych**. Można go użyć w taki sam sposób jak inne utworzone zestawy danych.


## <a name="access-datasets-for-your-experiments"></a>Dostęp do zestawów danych dla eksperymentów

Używaj Twoich zestawów danych w eksperymentach uczenia maszynowego do uczenia modeli ML. [Dowiedz się więcej na temat uczenia się z zestawami danych](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>Przykładowe notesy

Aby zapoznać się z przykładami i pokazami funkcji otwartych zestawów danych, zobacz te [notesy](https://github.com/Azure/OpenDatasetsNotebooks/tree/master/tutorials).

## <a name="next-steps"></a>Następne kroki

* [Uczenie modelu z zestawami danych](../machine-learning/how-to-train-with-datasets.md).

* [Utwórz zestaw danych usługi Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md).



