---
title: Interpretowanie & objaśnia modele ML w języku Python (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uzyskać wyjaśnienia dotyczące sposobu, w jaki model uczenia maszynowego określa ważność funkcji i wykonuje przewidywania przy użyciu zestawu SDK Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 14d15f54befba162b071b40e06e589f980708fd3
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740491"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>Korzystanie z pakietu do interpretacji w celu wyjaśnienia modeli ML & prognoz w języku Python (wersja zapoznawcza)



W tym przewodniku krok po kroku nauczysz się używać pakietu interpretera Azure Machine Learning Python SDK, aby wykonać następujące zadania:


* Wyjaśnij całe zachowanie modelu lub pojedyncze przewidywania na komputerze osobistym lokalnie.

* Włącz techniki interpretacji dla funkcji zaprojektowanych.

* Wyjaśnij zachowanie całego modelu i poszczególnych prognoz na platformie Azure.

* Za pomocą pulpitu nawigacyjnego wizualizacji można korzystać z wyjaśnień modelu.

* Wdróż wyjaśnienie oceniania wraz z modelem, aby obserwować wyjaśnienia podczas inferencing.



Aby uzyskać więcej informacji na temat obsługiwanych technik interpretacji i modeli uczenia maszynowego, zobacz [interpretowanie modeli w Azure Machine Learning](how-to-machine-learning-interpretability.md) i [przykładowych notesach](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Generuj wartość ważności funkcji na komputerze osobistym 
Poniższy przykład pokazuje, jak używać pakietu interpretera na komputerze osobistym bez kontaktowania się z usługami platformy Azure.

1. Zainstaluj pakiet `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```

2. Uczenie przykładowego modelu w Jupyter Notebook lokalnym.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

3. Wywołaj objaśnienie lokalnie.
   * Aby zainicjować obiekt objaśniający, Przekaż model i niektóre dane szkoleniowe do konstruktora programu wyjaśniającego.
   * Aby ułatwić wyjaśnienie i wizualizacje więcej informacji, możesz wybrać opcję przekazywania nazw funkcji i nazw klas wyjściowych w przypadku klasyfikacji.

   Poniższe bloki kodu pokazują, jak utworzyć wystąpienie obiektu wyjaśniającego z `TabularExplainer` , `MimicExplainer` i `PFIExplainer` lokalnie.
   * `TabularExplainer` wywołuje jeden z trzech objaśnień kształtu poniżej ( `TreeExplainer` , `DeepExplainer` , lub `KernelExplainer` ).
   * `TabularExplainer` automatycznie wybiera najbardziej odpowiedni dla przypadku użycia, ale można wywoływać każdego z trzech podstawowych objaśnień bezpośrednio.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    lub

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    lub

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Wyjaśnij całe zachowanie modelu (wyjaśnienie globalne) 

Zapoznaj się z poniższym przykładem, aby uzyskać informacje na temat wartości ważności funkcji agregującej (globalnej).

```python

# you can use the training data or the test data here, but test data would allow you to use Explanation Exploration
global_explanation = explainer.explain_global(x_test)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_train)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="explain-an-individual-prediction-local-explanation"></a>Wyjaśnij pojedyncze prognozowanie (lokalne wyjaśnienie)
Pobierz wartości ważności poszczególnych funkcji różnych punktów, wywołując wyjaśnienia dla pojedynczego wystąpienia lub grupy wystąpień.
> [!NOTE]
> `PFIExplainer` nie obsługuje lokalnych objaśnień.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Nieprzetworzone przekształcenia funkcji

Możesz zdecydować się na uzyskanie wyjaśnień pod względem nieprzetworzonych, nieprzekształconych funkcji zamiast wbudowanych funkcji. W przypadku tej opcji przeszedł potok transformacji funkcji do programu wyjaśniającego w `train_explain.py` . W przeciwnym razie wyjaśnienie zawiera wyjaśnienia dotyczące funkcji.

Format obsługiwanych transformacji jest taki sam, jak opisano w [skryptu sklearn-Pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Ogólnie rzecz biorąc, wszystkie przekształcenia są obsługiwane tak długo, jak działają w pojedynczej kolumnie, dzięki czemu są one jasne.

Zapoznaj się z wyjaśnieniem funkcji nieprzetworzonych, używając `sklearn.compose.ColumnTransformer` lub z listą dopasowanych krotek transformatora. Poniższy przykład używa `sklearn.compose.ColumnTransformer` .

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Jeśli chcesz uruchomić przykład z listą dopasowanych krotek transformatora, użyj następującego kodu:

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="generate-feature-importance-values-via-remote-runs"></a>Generuj wartości ważności funkcji za pośrednictwem zdalnych przebiegów

Poniższy przykład pokazuje, jak można użyć klasy, `ExplanationClient` Aby włączyć funkcję interpretowania modeli dla zdalnego uruchomienia. Jest on koncepcyjnie podobny do procesu lokalnego, z tą różnicą, że:

* Użyj `ExplanationClient` w zdalnym przebiegu do przekazania kontekstu interpretacji.
* Pobierz kontekst później w środowisku lokalnym.

1. Zainstaluj pakiet `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```
1. Utwórz skrypt szkoleniowy w Jupyter Notebook lokalnym. Na przykład `train_explain.py`.

    ```python
    from azureml.interpret import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. Skonfiguruj Azure Machine Learning obliczenia jako element docelowy obliczeń i prześlij swój przebieg szkoleniowy. Instrukcje można znaleźć w temacie [Tworzenie klastrów obliczeniowych Azure Machine Learning i zarządzanie nimi](how-to-create-attach-compute-cluster.md) . Przydatne może być również znalezienie [przykładowych notesów](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) .

1. Pobierz wyjaśnienie w Jupyter Notebook lokalnym.

    ```python
    from azureml.interpret import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```


## <a name="visualizations"></a>Wizualizacje

Po pobraniu wyjaśnień w lokalnym Jupyter Notebook można użyć pulpitu nawigacyjnego wizualizacji, aby zrozumieć i zinterpretować model. Aby załadować widżet pulpitu nawigacyjnego wizualizacji w Jupyter Notebook, użyj następującego kodu:

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, datasetX=x_test)
```

Wizualizacja obsługuje wyjaśnienia dotyczące funkcji programu, które zostały opracowane i nieprzetworzone. Nieprzetworzone wyjaśnienia opierają się na funkcjach oryginalnego zestawu danych, a zabudowane wyjaśnienia opierają się na funkcjach zestawu danych z zastosowaniem inżynierii funkcji.

Podczas próby zinterpretowania modelu w odniesieniu do oryginalnego zestawu danych zaleca się użycie nieprzetworzonych wyjaśnień, ponieważ każda ważność funkcji będzie odpowiadać kolumnie z oryginalnego zestawu danych. Jednym z scenariuszy, w których opracowane wyjaśnienia mogą być przydatne, jest zbadanie wpływu poszczególnych kategorii z funkcji kategorii. W przypadku zastosowania jednostronicowego kodowania do funkcji kategorii, utworzone wyjaśnienia będą zawierać inną wartość istotności dla każdej kategorii, jedną na funkcję wbudowaną. Może to być przydatne w przypadku zawężania części zestawu danych do modelu.

> [!NOTE]
> Objaśnienia zaprojektowane i nieprzetworzone są obliczane sekwencyjnie. Pierwsze stworzone wyjaśnienie jest tworzone na podstawie modelu i potoku cechowania. Następnie pierwotne wyjaśnienie jest tworzone na podstawie tego, który skonstruowano wyjaśnienie poprzez agregowanie znaczenia funkcji, które pochodzą z tej samej nieprzetworzonej funkcji.

### <a name="create-edit-and-view-dataset-cohorts"></a>Tworzenie, edytowanie i przeglądanie zestawu danych kohorty

Na górnej Wstążce są wyświetlane ogólne dane statystyczne dotyczące modelu i danych. Można wyróżnić i indeksować dane w zestawie danych kohorty lub podgrupach, aby zbadać lub porównać wydajność i wyjaśnienia modelu w ramach tych zdefiniowanych podgrup. Porównując dane statystyczne zestawu danych i wyjaśnienia w ramach tych podgrup, możesz uzyskać informacje o tym, dlaczego możliwe błędy występują w jednej grupie zamiast innej.

[![Tworzenie, edytowanie i przeglądanie zestawu danych kohorty](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif#lightbox)

### <a name="understand-entire-model-behavior-global-explanation"></a>Zrozumienie całego zachowania modelu (wyjaśnienie globalne) 

Pierwsze trzy karty na pulpicie nawigacyjnym wyjaśnienie zapewniają ogólną analizę przeszkolonego modelu wraz z jego przewidywaniami i wyjaśnieniami.

#### <a name="model-performance"></a>Wydajność modelu
Oceń wydajność modelu, przenosząc dystrybucję wartości prognozowanych i wartości metryk wydajności modelu. Można dokładniej zbadać model, patrząc na analizę porównawczą jej wydajności w różnych kohorty lub podgrupach zestawu danych. Wybierz pozycję Filtry i wartość y, aby wyciąć w różnych wymiarach. Wyświetl metryki, takie jak dokładność, precyzja, odwołanie, fałszywie dodatnia stawka (zarejestrowanego) i fałszywa ujemna stawka (FNR).

[![Karta wydajność modelu w wizualizacji wyjaśnień](./media/how-to-machine-learning-interpretability-aml/model-performance.gif)](./media/how-to-machine-learning-interpretability-aml/model-performance.gif#lightbox)

#### <a name="dataset-explorer"></a>Eksplorator zestawu danych
Poznaj statystyki zestawu danych, wybierając różne filtry wzdłuż osi X, Y i koloru, aby wydzielić dane na różne wymiary. Utwórz zestaw danych kohorty powyżej, aby analizować statystyki zestawu danych z filtrami, takimi jak przewidywany wynik, funkcje zestawu danych i grupy błędów. Użyj ikony koła zębatego w prawym górnym rogu wykresu, aby zmienić typy grafów.

[![Karta Eksplorator zestawu danych w wizualizacji wyjaśnień](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif#lightbox)

#### <a name="aggregate-feature-importance"></a>Ważność funkcji agregującej
Zapoznaj się z najważniejszymi funkcjami, które mają wpływ na ogólne przewidywania modeli (znane również jako wyjaśnienie globalne). Użyj suwaka, aby wyświetlić wartości malejącej ważności funkcji. Wybierz maksymalnie trzy kohorty, aby wyświetlić ich wartości ważności funkcji obok siebie. Kliknij dowolny z pasków funkcji na wykresie, aby zobaczyć, jak wartości wybranej funkcji mają być przewidywane na poniższym wykresie.

[![Karta ważność funkcji agregującej w wizualizacji wyjaśnień](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif)](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Zrozumienie indywidualnych prognoz (wyjaśnienie lokalne) 

Czwarta karta na karcie wyjaśnienie umożliwia przechodzenie do szczegółów poszczególnych punktów danych i ich indywidualnych ważności. Możesz załadować wykres dotyczący ważności funkcji dla dowolnego punktu danych, klikając dowolny z poszczególnych punktów danych na głównym rysunku punktowy lub wybierając konkretny punkt w Kreatorze panelu po prawej stronie.

|Wprowadź|Opis|
|----|-----------|
|Ważność poszczególnych funkcji|Pokazuje ważne funkcje najważniejsze dla poszczególnych prognoz. Pomaga zilustrować lokalne zachowanie modelu bazowego w określonym punkcie danych.|
|Analiza What-If|Zezwala na zmiany wartości funkcji wybranego rzeczywistego punktu danych i obserwuje zmiany wartości prognozowanych przez wygenerowanie hipotetycznych punktów końcowych z nowymi wartościami funkcji.|
|Oczekiwanie na pojedyncze warunkowe (lód)|Zezwala na zmianę wartości funkcji z minimalnej na wartość maksymalną. Program ułatwia zilustrowanie zmiany przewidywania punktu danych po zmianie funkcji.|

[![Ważność poszczególnych funkcji i karta "co w wyjaśnieniu" na pulpicie nawigacyjnym](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif)](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif#lightbox)

> [!NOTE]
> Są to wyjaśnienia w oparciu o wiele przybliżeń i nie są "przyczyną" prognoz. Bez ścisłej solidnej niezawodności przyczyn niezwiązanych z wnioskami firma Microsoft nie zaleca użytkownikom podejmowania decyzji w czasie rzeczywistym na podstawie funkcji perturbations narzędzia What-If. To narzędzie służy głównie do poznania modelu i debugowania.

### <a name="visualization-in-azure-machine-learning-studio"></a>Wizualizacja w programie Azure Machine Learning Studio

Jeśli wykonasz czynności z możliwością [interpretacji zdalnej](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) (przekazywanie wygenerowanego wyjaśnienia do historii uruchamiania Azure Machine Learning), możesz wyświetlić pulpit nawigacyjny wizualizacji w programie [Azure Machine Learning Studio](https://ml.azure.com). Ten pulpit nawigacyjny to prostsza wersja pulpitu nawigacyjnego wizualizacji opisana powyżej. What-If generowanie punktu danych i wykresy lodu są wyłączone, ponieważ nie ma aktywnych obliczeń w programie Azure Machine Learning Studio, które mogą wykonywać obliczenia w czasie rzeczywistym.

Jeśli zestaw danych, globalne i lokalne wyjaśnienia są dostępne, dane wypełniają wszystkie karty. Jeśli jest dostępne tylko ogólne wyjaśnienie, karta ważność poszczególnych funkcji zostanie wyłączona.

Aby uzyskać dostęp do pulpitu nawigacyjnego wizualizacji w programie Azure Machine Learning Studio, wykonaj jedną z tych ścieżek:

* Okienko **eksperymenty** (wersja zapoznawcza)
  1. Wybierz pozycję **eksperymenty** w okienku po lewej stronie, aby wyświetlić listę eksperymentów, które zostały uruchomione na Azure Machine Learning.
  1. Wybierz konkretny eksperyment, aby wyświetlić wszystkie uruchomienia w tym eksperymentie.
  1. Wybierz przebieg, a następnie kartę **wyjaśnienia** do pulpitu nawigacyjnego wizualizacji z wyjaśnieniem.

   [![Pulpit nawigacyjny wizualizacji o ważności funkcji agregującej w programie Azure Studio w eksperymentach](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png)](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png#lightbox)

* Okienko **modele**
  1. Jeśli Twój oryginalny model został zarejestrowany przez wykonanie kroków opisanych w temacie [Wdrażanie modeli przy użyciu Azure Machine Learning](./how-to-deploy-and-where.md), możesz wybrać **modele** w lewym okienku, aby je wyświetlić.
  1. Wybierz model, a następnie kartę **wyjaśnienia** , aby wyświetlić pulpit nawigacyjny wizualizacji z wyjaśnieniem.

## <a name="interpretability-at-inference-time"></a>Interpretowanie w czasie wnioskowania

Można wdrożyć program objaśniający wraz z oryginalnym modelem i użyć go w czasie wnioskowania, aby zapewnić wartości ważności poszczególnych funkcji (wyjaśnienie lokalne) dla każdego nowego punktu danych. Oferujemy również jaśniejsze oceny oceniające ocenę wydajności w czasie wnioskowania, który jest obecnie obsługiwany tylko w Azure Machine Learning SDK. Proces wdrażania bardziej jaśniejszej oceny oceniania jest podobny do wdrożenia modelu i obejmuje następujące kroki:

1. Utwórz obiekt wyjaśnień. Można na przykład użyć `TabularExplainer` :

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Utwórz wyjaśnienie oceniania przy użyciu obiektu wyjaśnienie.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Skonfiguruj i zarejestruj obraz, który używa modelu oceny oceniania.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Opcjonalnym krokiem jest pobranie objaśnień oceniania z chmury i przetestowanie wyjaśnień.

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Wdróż obraz w obiekcie docelowym obliczeń, wykonując następujące czynności:

   1. W razie konieczności Zarejestruj swój oryginalny model predykcyjny, wykonując kroki opisane w temacie [Wdrażanie modeli przy użyciu Azure Machine Learning](./how-to-deploy-and-where.md).

   1. Utwórz plik oceniania.

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. Zdefiniowanie konfiguracji wdrożenia.

         Ta konfiguracja zależy od wymagań modelu. W poniższym przykładzie zdefiniowano konfigurację, która używa jednego rdzenia procesora i jednej GB pamięci.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Utwórz plik ze zależnościami środowiska.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. Utwórz niestandardowy pliku dockerfile z zainstalowanym programem g + +.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Wdróż utworzony obraz.
   
         Ten proces trwa około pięciu minut.

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. Przetestuj wdrożenie.

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Wyczyść.

   Aby usunąć wdrożoną usługę sieci Web, użyj programu `service.delete()` .

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* **Dane rozrzedzone nie są obsługiwane**: w przypadku modelu pulpit nawigacyjny rozbicie/spowolnienie w dużej liczbie funkcji, dlatego obecnie nie obsługujemy formatu danych rozrzedzonych. Ponadto w przypadku dużych zestawów danych i dużej liczby funkcji występują ogólne problemy z pamięcią. 

* **Modele prognozowania nie są obsługiwane przy użyciu wyjaśnień modelu**: interpretowanie i najlepsze wyjaśnienie modelu nie jest dostępne dla eksperymentów prognozowania AutoML, które zalecają następujące algorytmy jak najlepszy model: TCNForecaster, AutoArima, Prophet, ExponentialSmoothing, Average, algorytm Bayesa, sezonowy średnia i sezonowe algorytm Bayesa. Prognozowanie AutoML ma modele regresji, które obsługują wyjaśnienia. Jednak na pulpicie nawigacyjnym wyjaśnień karta "ważność poszczególnych funkcji" nie jest obsługiwana w przypadku prognozowania ze względu na złożoność ich potoków danych.

* **Lokalne wyjaśnienie indeksu danych**: pulpit nawigacyjny wyjaśnień nie obsługuje poprawnych wartości ważności lokalnej do identyfikatora wiersza z oryginalnego zestawu danych walidacji, jeśli ten zestaw danych jest większy niż 5000 punktów danych, ponieważ pulpit nawigacyjny losowo próbkuje dane. Jednak na pulpicie nawigacyjnym są wyświetlane wartości funkcji pierwotnego zestawu danych dla każdego z nich przekazana do pulpitu nawigacyjnego na karcie ważność poszczególnych funkcji. Użytkownicy mogą mapować lokalne ważności z powrotem do oryginalnego zestawu danych za pomocą pasujących wartości funkcji zestawu danych pierwotnych. Jeśli rozmiar zestawu danych sprawdzania poprawności jest mniejszy niż 5000 próbek, `index` Funkcja w programie Azure Studio będzie odpowiadać indeksowi w zestawie danych walidacji.

* **Wykresy warunkowe/w przypadku lodu nie są obsługiwane w programie Studio**: What-If i pojedyncze wystąpienia warunkowe (lód) nie są obsługiwane w programie Azure Machine Learning Studio na karcie wyjaśnienia, ponieważ przekazane wyjaśnienie wymaga aktywnego obliczenia, aby ponownie obliczyć przewidywania i prawdopodobieństwa funkcji perturbed. Jest ona obecnie obsługiwana w notesach Jupyter, gdy jest uruchamiany jako widżet przy użyciu zestawu SDK.


## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o interpretacji modelu](how-to-machine-learning-interpretability.md)

[Zapoznaj się z przykładowymi notesami z interpretacją Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
