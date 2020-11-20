---
title: Tworzenie i eksplorowanie zestawów danych przy użyciu etykiet
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak eksportować etykiety danych z Azure Machine Learning etykietowania projektów i używać ich do zadań uczenia maszynowego.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.date: 05/14/2020
ms.openlocfilehash: b7eaf374e72997013f09b1c94ce16c67d115a818
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94987375"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Tworzenie i eksplorowanie zestawu danych Azure Machine Learning przy użyciu etykiet

W tym artykule dowiesz się, jak eksportować etykiety danych z Azure Machine Learning projektu etykietowania danych i ładować je do popularnych formatów, takich jak Pandas Dataframe na potrzeby eksploracji danych lub zestawu danych Torchvision na potrzeby transformacji obrazu. 

## <a name="what-are-datasets-with-labels"></a>Co to są zestawy danych z etykietami 

Odwołujemy się do Azure Machine Learning zestawów danych z etykietami jako zestawami danych z etykietami. Te typy zestawów danych z etykietami są tworzone tylko jako dane wyjściowe Azure Machine Learning projekty etykietowania danych. Utwórz projekt etykietowania danych z [tymi krokami](how-to-create-labeling-projects.md). Machine Learning obsługuje projekty etykietowania danych dla klasyfikacji obrazów, wiele etykiet lub wiele klas oraz identyfikację obiektów razem z ograniczonymi polami.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://aka.ms/AMLFree) .
* [Zestaw Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)lub dostęp do programu [Azure Machine Learning Studio](https://ml.azure.com/).
    * Instalowanie pakietu [Azure-contrib-DataSet](/python/api/azureml-contrib-dataset/?preserve-view=true&view=azure-ml-py)
* Obszar roboczy Machine Learning. Zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).
* Dostęp do projektu etykietowania danych Azure Machine Learning. Jeśli nie masz projektu etykietowania, utwórz go za pomocą [tych kroków](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Eksportuj etykiety danych 

Po zakończeniu projektu etykietowania danych można wyeksportować dane etykiet z projektu etykietowania. Wykonanie tej czynności umożliwia przechwycenie odwołania do danych i jego etykiet oraz wyeksportowanie ich w [formacie Coco](http://cocodataset.org/#format-data) lub jako zestaw danych Azure Machine Learning. Użyj przycisku **Eksportuj** na stronie **szczegóły projektu** w projekcie etykietowania.

### <a name="coco"></a>COCO 

 Plik COCO jest tworzony w domyślnym magazynie obiektów BLOB obszaru roboczego Azure Machine Learning w folderze w obszarze *Export/Coco*. 

### <a name="azure-machine-learning-dataset"></a>Zestaw danych Azure Machine Learning

Możesz uzyskać dostęp do wyeksportowanego zestawu danych Azure Machine Learning w sekcji **zestawy** danych w programie Azure Machine Learning Studio. Strona **szczegóły** zestawu danych zawiera również przykładowy kod umożliwiający uzyskanie dostępu do etykiet z poziomu języka Python.

![Wyeksportowany zestaw danych](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Eksploruj zestawy danych z etykietami

Załaduj zestawy danych z etykietami do Pandas Dataframe lub Torchvision DataSet, aby wykorzystać popularne biblioteki typu "open source" do eksplorowania danych, jak również udostępniane biblioteki do przekształcania i uczenia obrazów.

### <a name="pandas-dataframe"></a>Ramka dataPandas

Zestawy danych z etykietami można ładować do Pandas Dataframe z [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueto-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metodą z `azureml-contrib-dataset` klasy. Zainstaluj klasę przy użyciu następującego polecenia powłoki: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Przestrzeń nazw Azure. contrib często zmienia się, ponieważ usprawniamy działanie usługi. W związku z tym wszystkie elementy w tej przestrzeni nazw powinny być traktowane jako wersja zapoznawcza i nie są w pełni obsługiwane przez firmę Microsoft.

Azure Machine Learning oferuje następujące opcje obsługi plików strumieni plików podczas konwertowania na Pandas Dataframe.
* Pobieranie: pobieranie plików danych do ścieżki lokalnej.
* Instalacja: Zainstaluj pliki danych w punkcie instalacji. Instalacja działa tylko w przypadku obliczeń opartych na systemie Linux, w tym Azure Machine Learning maszyn wirtualnych i Azure Machine Learning obliczeniowych notesu.

W poniższym kodzie `animal_labels` zestaw danych jest danymi wyjściowymi z projektu etykietowego, który został wcześniej zapisany w obszarze roboczym.

```Python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Torchvision zbiory danych

Można załadować zestawy danych z etykietami do Torchvision DataSet z metodą [to_torchvision ()](/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueto-torchvision--) również z `azureml-contrib-dataset` klasy. Aby można było użyć tej metody, należy zainstalować [PyTorch](https://pytorch.org/) . 

W poniższym kodzie `animal_labels` zestaw danych jest danymi wyjściowymi z projektu etykietowego, który został wcześniej zapisany w obszarze roboczym.

```python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

from torchvision.transforms import functional as F

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się [z zestawem danych zawierającym Notes etykiet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) , aby uzyskać pełny przykład szkolenia.
