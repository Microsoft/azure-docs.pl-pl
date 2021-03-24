---
title: Dostęp do zestawów danych przy użyciu biblioteki klienta języka Python — zespół ds. procesów naukowych
description: Zainstaluj i użyj biblioteki klienta języka Python, aby bezpiecznie uzyskiwać dostęp do danych Azure Machine Learning i zarządzać nimi z poziomu lokalnego środowiska Python.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b773241fdff41323272422f99e88f0d01e884055
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889556"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Dostęp do zestawów danych z językiem Python za pomocą biblioteki klienta Python usługi Azure Machine Learning
Wersja zapoznawcza Microsoft Azure Machine Learning Biblioteka kliencka języka Python umożliwia bezpieczny dostęp do Azure Machine Learning zestawów danych ze środowiska lokalnego języka Python oraz umożliwia tworzenie zestawów danych i zarządzanie nimi w obszarze roboczym.

Ten temat zawiera instrukcje dotyczące:

* Zainstaluj bibliotekę kliencką Machine Learning Python
* dostęp do zestawów danych i przekazywanie ich, w tym instrukcje dotyczące uzyskiwania autoryzacji dostępu Azure Machine Learning zestawów danych ze środowiska lokalnego języka Python
* Uzyskiwanie dostępu do pośrednich zestawów danych z eksperymentów
* za pomocą biblioteki klienta języka Python można wyliczać zestawy danych, uzyskiwać dostęp do metadanych, odczytywać zawartość zestawu danych, tworzyć nowe zestawy danych i aktualizować istniejące zestawy danych

## <a name="prerequisites"></a><a name="prerequisites"></a>Wymagania wstępne
Biblioteka klienta języka Python została przetestowana w następujących środowiskach:

* Windows, Mac i Linux
* Python 2,7 i 3.6 +

Ma zależność od następujących pakietów:

* żądań
* Python — dateutil
* Pandas

Zalecamy używanie dystrybucji języka Python, takiej jak [Anaconda](https://www.anaconda.com/) lub [koroner](https://store.enthought.com/downloads/), która jest dostarczana z zainstalowanymi w języku Python, IPython i trzema wymienionymi powyżej pakietami. Chociaż IPython nie jest ściśle wymagane, jest to doskonałe środowisko do interaktywnego manipulowania danymi i wizualizacji danych.

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>Jak zainstalować bibliotekę kliencką Azure Machine Learning Python
Zainstaluj bibliotekę kliencką Azure Machine Learning Python, aby wykonać zadania opisane w tym temacie. Ta biblioteka jest dostępna w [indeksie pakietu języka Python](https://pypi.python.org/pypi/azureml). Aby zainstalować ją w środowisku języka Python, uruchom następujące polecenie w lokalnym środowisku języka Python:

```console
pip install azureml
```

Alternatywnie możesz pobrać i zainstalować ze źródeł w serwisie [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

```console
python setup.py install
```

Jeśli na komputerze jest zainstalowany program git, możesz zainstalować go bezpośrednio z repozytorium git przy użyciu narzędzia PIP:

```console
pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git
```

## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>Używanie fragmentów kodu do uzyskiwania dostępu do zestawów danych
Biblioteka klienta języka Python zapewnia programistyczny dostęp do istniejących zestawów danych z eksperymentów, które zostały uruchomione.

Z poziomu interfejsu sieci Web Azure Machine Learning Studio (klasycznego) można generować fragmenty kodu zawierające wszystkie informacje niezbędne do pobrania i deserializacji zestawów danych jako Pandas obiektów Dataframe na komputerze lokalnym.

### <a name="security-for-data-access"></a><a name="security"></a>Zabezpieczenia dostępu do danych
Fragmenty kodu udostępniane przez Azure Machine Learning Studio (klasyczne) do użycia z biblioteką klienta języka Python obejmują identyfikator obszaru roboczego i token autoryzacji. Zapewniają one pełen dostęp do obszaru roboczego i muszą być chronione, takie jak hasło.

Ze względów bezpieczeństwa funkcja fragmentu kodu jest dostępna tylko dla użytkowników, którzy mają ustawioną rolę **właściciel** dla obszaru roboczego. Twoja rola jest wyświetlana w Azure Machine Learning Studio (klasyczny) na stronie **Użytkownicy** w obszarze **Ustawienia**.

![Zrzut ekranu przedstawia ustawienia na stronie użytkownicy Azure Machine Learning Studio.][security]

Jeśli rola nie jest ustawiona jako **właściciel**, możesz wysłać żądanie do osoby, która ma zostać zaproszona jako właściciel, lub poprosić właściciela obszaru roboczego o udostępnienie fragmentu kodu.

Aby uzyskać Token autoryzacji, można wybrać jedną z następujących opcji:

* Poproszenie o token od właściciela. Właściciele mogą uzyskać dostęp do tokenów autoryzacji ze strony ustawień obszaru roboczego w Azure Machine Learning Studio (klasyczny). Wybierz pozycję **Ustawienia** w okienku po lewej stronie, a następnie kliknij pozycję **tokeny autoryzacji** , aby zobaczyć token podstawowy i pomocniczy. Mimo że tokeny uwierzytelniania podstawowego lub pomocniczego mogą być używane we fragmencie kodu, zaleca się, aby właściciele współdzielą tokeny autoryzacji pomocniczej.

   ![Tokeny autoryzacji](./media/python-data-access/ml-python-access-settings-tokens.png)

* Poproś o podwyższenie poziomu roli właściciela: Bieżący właściciel obszaru roboczego musi najpierw usunąć użytkownika z obszaru roboczego, a następnie ponownie zaprosić użytkownika jako właściciela.

Gdy deweloperzy uzyskali identyfikator obszaru roboczego i token autoryzacji, będą mogli uzyskać dostęp do obszaru roboczego przy użyciu fragmentu kodu, niezależnie od ich roli.

Tokeny autoryzacji są zarządzane na stronie **tokeny autoryzacji** w obszarze **Ustawienia**. Można je ponownie wygenerować, ale ta procedura odwołuje dostęp do poprzedniego tokenu.

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>Dostęp do zestawów danych z lokalnej aplikacji języka Python
1. W Machine Learning Studio (klasyczny) kliknij pozycję **zestawy danych** na pasku nawigacyjnym po lewej stronie.
2. Wybierz zestaw danych, do którego chcesz uzyskać dostęp. Możesz wybrać dowolny z zestawów danych z listy **moje zbiory danych** lub z listy **przykładów** .
3. Na dolnym pasku narzędzi kliknij pozycję **Generuj kod dostępu do danych**. Jeśli dane są w formacie niezgodnym z biblioteką klienta języka Python, ten przycisk jest wyłączony.
   
    ![Zrzut ekranu przedstawia zestawy danych z GENEROWANIEm kodu dostępu z danymi.][datasets]
4. Wybierz fragment kodu z wyświetlonego okna i skopiuj go do Schowka.
   
    ![Przycisk generowania kodu dostępu do danych][dataset-access-code]
5. Wklej kod do notesu lokalnej aplikacji w języku Python.
   
    ![Wklej kod do notesu][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>Uzyskiwanie dostępu do pośrednich zestawów danych z Machine Learning eksperymenty
Po uruchomieniu eksperymentu w Machine Learning Studio (klasyczny) możliwe jest uzyskanie dostępu do pośrednich zestawów danych z węzłów wyjściowych modułów. Pośrednie zestawy danych to dane, które zostały utworzone i użyte do pośrednich etapów, gdy narzędzie modelu zostało uruchomione.

Do pośrednich zestawów danych można uzyskać dostęp, o ile jest on zgodny z biblioteką klienta języka Python.

Obsługiwane są następujące formaty (stałe dla tych formatów są w `azureml.DataTypeIds` klasie):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Możesz określić format, ustawiając kursor nad węzłem wyjściowym modułu. Jest wyświetlana wraz z nazwą węzła w etykietce narzędzia.

Niektóre moduły, takie jak moduł [Split][split] , są wyprowadzane do formatu o nazwie `Dataset` , który nie jest obsługiwany przez bibliotekę kliencką języka Python.

![Format zestawu danych][dataset-format]

Musisz użyć modułu konwersji, takiego jak [Convert to CSV][convert-to-csv], aby uzyskać dane wyjściowe do obsługiwanego formatu.

![Format GenericCSV][csv-format]

W poniższych krokach przedstawiono przykład tworzenia eksperymentu, uruchamiania go i uzyskiwania dostępu do pośredniego zestawu danych.

1. Utwórz nowy eksperyment.
2. Wstaw **binarny moduł zestawu danych klasyfikacji dochodów z spisu dla dorosłych** .
3. Wstaw moduł [podzielony][split] i Połącz jego dane wejściowe z danymi wyjściowymi modułu DataSet.
4. Wstaw moduł [Convert to CSV][convert-to-csv] i Połącz jego dane wejściowe z jednym z danych wyjściowych modułu [Split][split] .
5. Zapisz eksperyment, uruchom go i poczekaj na zakończenie zadania.
6. Kliknij węzeł wyjście w module [Konwertuj na wolumin CSV][convert-to-csv] .
7. Po wyświetleniu menu kontekstowego wybierz pozycję **Generuj kod dostępu do danych**.
   
    ![Menu kontekstowe][experiment]
8. Wybierz fragment kodu i skopiuj go do schowka z wyświetlonego okna.
   
    ![Generuj kod dostępu z menu kontekstowego][intermediate-dataset-access-code]
9. Wklej kod w notesie.
   
    ![Wklej kod do notesu][ipython-intermediate-dataset]
10. Możesz wizualizować dane przy użyciu matplotlib. Spowoduje to wyświetlenie w histogramie kolumny wiek:
    
    ![Histogram][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>Korzystanie z Machine Learning biblioteki klienta języka Python w celu uzyskiwania dostępu do zestawów danych, ich odczytywania, tworzenia i zarządzania nimi
### <a name="workspace"></a>Workspace
Obszar roboczy jest punktem wejścia dla biblioteki klienta języka Python. Podaj `Workspace` klasę z identyfikatorem obszaru roboczego i tokenem autoryzacji, aby utworzyć wystąpienie:

```python
ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
               authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')
```

### <a name="enumerate-datasets"></a>Wyliczanie zestawów danych
Aby wyliczyć wszystkie zestawy danych w danym obszarze roboczym:

```python
for ds in ws.datasets:
    print(ds.name)
```

Aby wyliczyć tylko zestawy danych utworzone przez użytkownika:

```python
for ds in ws.user_datasets:
    print(ds.name)
```

Aby wyliczyć tylko przykładowe zestawy danych:

```python
for ds in ws.example_datasets:
    print(ds.name)
```

Dostęp do zestawu danych można uzyskać przy użyciu nazwy (w przypadku uwzględniania wielkości liter):

```python
ds = ws.datasets['my dataset name']
```

Lub dostęp do niego można uzyskać za pomocą indeksu:

```python
ds = ws.datasets[0]
```

### <a name="metadata"></a>Metadane
Zestawy danych zawierają metadane, a także zawartość. (Pośrednie zestawy danych są wyjątkiem od tej reguły i nie mają żadnych metadanych).

Niektóre wartości metadanych są przypisywane przez użytkownika podczas tworzenia:

* `print(ds.name)`
* `print(ds.description)`
* `print(ds.family_id)`
* `print(ds.data_type_id)`

Inne są wartościami przypisanymi przez usługę Azure ML:

* `print(ds.id)`
* `print(ds.created_date)`
* `print(ds.size)`

Zapoznaj się z `SourceDataset` klasą, aby uzyskać więcej informacji na temat dostępnych metadanych.

### <a name="read-contents"></a>Odczytaj zawartość
Fragmenty kodu udostępniane przez Machine Learning Studio (klasyczne) automatycznie pobierają i deserializacjiją zestaw danych do obiektu Pandas Dataframe. Jest to realizowane za pomocą `to_dataframe` metody:

```python
frame = ds.to_dataframe()
```

Jeśli wolisz pobrać pierwotne dane i wykonać deserializacji samodzielnie, to jest opcja. Obecnie jest to jedyna opcja dla formatów takich jak "ARFF", które nie mogą deserializować biblioteki klienta języka Python.

Aby odczytać zawartość jako tekst:

```python
text_data = ds.read_as_text()
```

Aby odczytać zawartość jako plik binarny:

```python
binary_data = ds.read_as_binary()
```

Możesz również po prostu otworzyć strumień do zawartości:

```python
with ds.open() as file:
    binary_data_chunk = file.read(1000)
```

### <a name="create-a-new-dataset"></a>Utwórz nowy zestaw danych
Biblioteka klienta języka Python umożliwia przekazywanie zestawów danych z programu w języku Python. Te zestawy danych są następnie dostępne do użycia w Twoim obszarze roboczym.

Jeśli masz dane w Pandas Dataframe, użyj następującego kodu:

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_dataframe(
    dataframe=frame,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

Jeśli dane są już serializowane, można użyć:

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_raw_data(
    raw_data=raw_data,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

Biblioteka klienta języka Python jest w stanie serializować pandasą ramkę danych do następujących formatów (stałe dla nich znajdują się w `azureml.DataTypeIds` klasie):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Aktualizowanie istniejącego zestawu danych
Jeśli próbujesz przekazać nowy zestaw danych o nazwie zgodnej z istniejącym zestawem danych, należy uzyskać błąd konfliktu.

Aby zaktualizować istniejący zestaw danych, należy najpierw pobrać odwołanie do istniejącego zestawu danych:

```python
dataset = ws.datasets['existing dataset']

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Następnie użyj, `update_from_dataframe` Aby serializować i zastąpić zawartość zestawu danych na platformie Azure:

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(frame2)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Jeśli chcesz serializować dane w innym formacie, określ wartość parametru opcjonalnego `data_type_id` .

```python
from azureml import DataTypeIds

dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    data_type_id=DataTypeIds.GenericTSV,
)

print(dataset.data_type_id) # 'GenericTSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Opcjonalnie można ustawić nowy opis, określając wartość `description` parametru.

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    description='data up to feb 2015',
)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to feb 2015'
```

Opcjonalnie można ustawić nową nazwę, określając wartość `name` parametru. Od teraz pobierasz zestaw danych tylko przy użyciu nowej nazwy. Poniższy kod aktualizuje dane, nazwę i opis.

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    name='existing dataset v2',
    description='data up to feb 2015',
)

print(dataset.data_type_id)                    # 'GenericCSV'
print(dataset.name)                            # 'existing dataset v2'
print(dataset.description)                     # 'data up to feb 2015'

print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
print(ws.datasets['existing dataset'].name)    # IndexError
```

`data_type_id` `name` `description` Parametry i są opcjonalne i domyślne dla ich poprzedniej wartości. `dataframe`Parametr jest zawsze wymagany.

Jeśli dane są już serializowane, użyj `update_from_raw_data` zamiast `update_from_dataframe` . Jeśli przeszedł po prostu `raw_data` , zamiast  `dataframe` , działa w podobny sposób.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: /azure/machine-learning/studio-module-reference/convert-to-csv
[split]: /azure/machine-learning/studio-module-reference/split-data