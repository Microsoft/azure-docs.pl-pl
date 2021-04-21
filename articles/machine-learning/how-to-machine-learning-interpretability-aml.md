---
title: Interpretowanie & uczenia maszynowego w języku Python (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uzyskać wyjaśnienia dotyczące sposobu, w jaki model uczenia maszynowego określa ważność funkcji i tworzy przewidywania podczas korzystania z Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: d79458cfc76adcfd35a6b8dee40c0c45786abc28
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763293"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>Używanie pakietu możliwości interpretowania do wyjaśniania modeli uczenia maszynowego & w języku Python (wersja zapoznawcza)

Z tego przewodnika dowiesz się, jak używać pakietu możliwości interpretowania zestawu SDK Azure Machine Learning Python do wykonywania następujących zadań:


* Wyjaśnienie zachowania całego modelu lub poszczególnych przewidywań na komputerze osobistym lokalnie.

* Włącz techniki możliwości interpretowania dla funkcji inżynierowanych.

* Wyjaśnienie zachowania całego modelu i poszczególnych przewidywań na platformie Azure.

* Użyj pulpitu nawigacyjnego wizualizacji, aby wchodzić w interakcje z wyjaśnieniami modelu.

* Wdobyj objaśnienie oceniania obok modelu, aby zaobserwować wyjaśnienia podczas wnioskowania.


Aby uzyskać więcej informacji na temat obsługiwanych technik interpretacji i modeli uczenia maszynowego, zobacz Możliwości interpretowania modeli w notesach [Azure Machine Learning](how-to-machine-learning-interpretability.md) [i przykładowych notesach.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

Aby uzyskać wskazówki dotyczące włączania możliwości interpretowania modeli wytrenowany za pomocą zautomatyzowanego uczenia maszynowego, zobacz [Interpretability: model explanations for automated machine learning models (preview) (Możliwości interpretowania:](how-to-machine-learning-interpretability-automl.md)wyjaśnienia modeli zautomatyzowanego uczenia maszynowego (wersja zapoznawcza) ). 

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Generowanie wartości ważności funkcji na komputerze osobistym 
W poniższym przykładzie pokazano, jak używać pakietu możliwości interpretacji na komputerze osobistym bez kontaktowania się z usługami platformy Azure.

1. Zainstaluj pakiet `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```

2. Trenowanie przykładowego modelu w aplikacji Jupyter Notebook.

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

3. Wywołaj objaśnienia lokalnie.
   * Aby zainicjować obiekt objaśnienia, przekaż model i niektóre dane szkoleniowe do konstruktora objaśnienia.
   * Aby wyjaśnienia i wizualizacje zawierały więcej informacji, można przekazać nazwy funkcji i wyjściowe nazwy klas w przypadku klasyfikacji.

   Poniższe bloki kodu pokazują, jak utworzyć wystąpienia obiektu objaśnienia za pomocą `TabularExplainer` elementów `MimicExplainer` , i `PFIExplainer` lokalnie.
   * `TabularExplainer` Wywołuje jeden z trzech objaśnienia SHAP poniżej ( `TreeExplainer` `DeepExplainer` , lub `KernelExplainer` ).
   * `TabularExplainer` automatycznie wybiera najbardziej odpowiedni dla Twojego przypadku użycia, ale możesz bezpośrednio wywołać każdego z trzech podstawowych objaśnień.

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

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Wyjaśnienie całego zachowania modelu (wyjaśnienie globalne) 

Zapoznaj się z poniższym przykładem, aby uzyskać zagregowane (globalne) wartości ważności funkcji.

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

### <a name="explain-an-individual-prediction-local-explanation"></a>Wyjaśnienie poszczególnych przewidywań (wyjaśnienie lokalne)
Pobierz wartości ważności poszczególnych cech dla różnych punktów danych, wywołując wyjaśnienia dla poszczególnych wystąpień lub grup wystąpień.
> [!NOTE]
> `PFIExplainer` Nie obsługuje lokalnych wyjaśnień.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Nieprzetworzone przekształcenia funkcji

Możesz wybrać, aby uzyskać wyjaśnienia dotyczące nieprzetworzonych, nieprzekształcanych funkcji, a nie funkcji zaprojektowanych. W przypadku tej opcji potok przekształcania funkcji jest przekazywać do objaśnienia w programie `train_explain.py` . W przeciwnym razie objaśnienie zawiera wyjaśnienia dotyczące funkcji inżynierów.

Format obsługiwanych przekształceń jest taki sam, jak opisano w [temacie sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Ogólnie rzecz biorąc, wszystkie przekształcenia są obsługiwane, o ile działają na jednej kolumnie, dzięki czemu jest jasne, że są one "jeden do wielu".

Uzyskaj wyjaśnienie dotyczące cech pierwotnych przy użyciu funkcji lub z `sklearn.compose.ColumnTransformer` listą dopasowanych krotek transformatora. W poniższym przykładzie `sklearn.compose.ColumnTransformer` użyto .

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

## <a name="generate-feature-importance-values-via-remote-runs"></a>Generowanie wartości ważności funkcji za pośrednictwem przebiegów zdalnych

W poniższym przykładzie pokazano, jak można użyć `ExplanationClient` klasy w celu umożliwienia interpretacji modelu dla przebiegów zdalnych. Jest on koncepcyjnie podobny do procesu lokalnego, z wyjątkiem:

* Użyj w `ExplanationClient` zdalnym uruchomieniu, aby przekazać kontekst możliwości interpretacji.
* Pobierz kontekst później w środowisku lokalnym.

1. Zainstaluj pakiet `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```
1. Utwórz skrypt trenowania w lokalnym Jupyter Notebook. Na przykład `train_explain.py`.

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

1. Skonfiguruj usługę Azure Machine Learning Compute jako docelowy obiekt obliczeniowy i prześlij przebieg trenowania. Aby [uzyskać instrukcje, Azure Machine Learning tworzenie klastrów obliczeniowych i](how-to-create-attach-compute-cluster.md) zarządzanie nimi. Przydatne mogą być również [przykładowe notesy.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation)

1. Pobierz wyjaśnienie w pliku Jupyter Notebook.

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

Po pobraniu objaśnień w aplikacji Jupyter Notebook możesz użyć wizualizacji na pulpicie nawigacyjnym z wyjaśnieniami, aby zrozumieć i zinterpretować model. Aby załadować widżet pulpitu nawigacyjnego objaśnień w Jupyter Notebook, użyj następującego kodu:

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, datasetX=x_test)
```

Wizualizacje obsługują wyjaśnienia dotyczące zarówno cech inżynierowanych, jak i pierwotnych. Nieprzetworzone wyjaśnienia są oparte na funkcjach z oryginalnego zestawu danych, a inżynieryjne wyjaśnienia są oparte na funkcjach zestawu danych z zastosowaną inżynierią cech.

Podczas próby interpretacji modelu względem oryginalnego zestawu danych zaleca się użycie nieprzetworzonych wyjaśnień, ponieważ każda ważność funkcji będzie odpowiadać kolumnie z oryginalnego zestawu danych. Jednym ze scenariuszy, w którym inżynierowane wyjaśnienia mogą być przydatne, jest badanie wpływu poszczególnych kategorii z cech kategorianych. Jeśli kodowanie z gorącą zostanie zastosowane do cech kategorianych, wynikowe wyjaśnienia inżynierów będą zawierać inną wartość ważności na kategorię, jedną dla jednej funkcji zaprojektowanej na gorąco. Może to być przydatne podczas zawężania części zestawu danych, która jest najbardziej wartościowa dla modelu.

> [!NOTE]
> Inżynierowie i nieprzetworzone wyjaśnienia są obliczane sekwencyjnie. Najpierw tworzone jest inżynierowane wyjaśnienie na podstawie modelu i potoku cechowania. Następnie nieprzetworzone wyjaśnienie jest tworzone na podstawie tego utworzonego wyjaśnienia przez agregowanie znaczenia cech inżynierowanych, które pochodziły z tej samej funkcji pierwotnej.

### <a name="create-edit-and-view-dataset-cohorts"></a>Tworzenie, edytowanie i wyświetlanie kohort zestawu danych

Górna wstążka przedstawia ogólne statystyki dotyczące modelu i danych. Dane można ująć w kohorty zestawów danych lub podgrupy, aby zbadać lub porównać wydajność i wyjaśnienia modelu w tych zdefiniowanych podgrupach. Porównując statystyki zestawu danych i wyjaśnienia w tych podgrupach, można określić, dlaczego możliwe błędy występują w jednej grupie a innej.

[![Tworzenie, edytowanie i wyświetlanie kohort zestawu danych](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif#lightbox)

### <a name="understand-entire-model-behavior-global-explanation"></a>Opis całego zachowania modelu (wyjaśnienie globalne) 

Pierwsze trzy karty pulpitu nawigacyjnego wyjaśnienia zawierają ogólną analizę wytrenowany model wraz z jego przewidywaniami i wyjaśnieniami.

#### <a name="model-performance"></a>Wydajność modelu
Oceń wydajność modelu, eksplorując rozkład wartości przewidywania i wartości metryk wydajności modelu. Możesz dokładniej zbadać model, analizą porównawczą jego wydajności w różnych kohortach lub podgrupach zestawu danych. Wybierz filtry wzdłuż wartości y i x-value, aby wycinać w różnych wymiarach. Wyświetlanie metryk, takich jak dokładność, precyzja, przywołanie, wskaźnik wyników fałszywie dodatnich (FPR) i wskaźnik fałszywie ujemny (FNR).

[![Karta Wydajności modelu w wizualizacji wyjaśnienia](./media/how-to-machine-learning-interpretability-aml/model-performance.gif)](./media/how-to-machine-learning-interpretability-aml/model-performance.gif#lightbox)

#### <a name="dataset-explorer"></a>Eksplorator zestawów danych
Eksploruj statystyki zestawu danych, wybierając różne filtry wzdłuż osi X, Y i kolorów, aby fragmentować dane na różne wymiary. Utwórz kohorty zestawów danych powyżej, aby analizować statystyki zestawu danych za pomocą filtrów, takich jak przewidywany wynik, funkcje zestawu danych i grupy błędów. Użyj ikony koła zębatego w prawym górnym rogu wykresu, aby zmienić typy grafów.

[![Karta Eksploratora zestawu danych w wizualizacji wyjaśnienia](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif#lightbox)

#### <a name="aggregate-feature-importance"></a>Agregowanie ważności funkcji
Zapoznaj się z najważniejszymi funkcjami, które mają wpływ na ogólne przewidywania modelu (tzw. wyjaśnienie globalne). Użyj suwaka, aby wyświetlić malejące wartości ważności cech. Wybierz maksymalnie trzy kohorty, aby wyświetlić obok siebie wartości ważności funkcji. Kliknij dowolny z pasków funkcji na wykresie, aby zobaczyć, jak wartości wybranej funkcji wpływają na przewidywanie modelu na poniższym wykresie zależności.

[![Karta Agregowanie ważności funkcji w wizualizacji wyjaśnienia](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif)](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Opis poszczególnych przewidywań (wyjaśnienie lokalne) 

Czwarta karta na karcie wyjaśnienia umożliwia przechodzenie do szczegółów poszczególnych punktów danych i ich ważności poszczególnych funkcji. Wykres ważności poszczególnych cech można załadować dla dowolnego punktu danych, klikając dowolny punkt danych na głównym wykresie punktowym lub wybierając konkretny punkt danych w kreatorze panelu po prawej stronie.

|Działka|Opis|
|----|-----------|
|Ważność poszczególnych funkcji|Przedstawia najważniejsze cechy ważnych funkcji dla poszczególnych przewidywań. Pomaga zilustrować lokalne zachowanie modelu bazowego w określonym punkcie danych.|
|What-If analizy|Umożliwia zmiany wartości cech wybranego rzeczywistego punktu danych i obserwowanie wynikowych zmian wartości przewidywania przez wygenerowanie hipotetycznego punktu danych z nowymi wartościami cech.|
|Indywidualne oczekiwanie warunkowe (ICE)|Umożliwia zmianę wartości funkcji z wartości minimalnej na maksymalną. Pomaga zilustrować, jak zmienia się przewidywanie punktu danych po zmianie funkcji.|

[![Ważność poszczególnych funkcji i karta What-if na pulpicie nawigacyjnym wyjaśnienia](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif)](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif#lightbox)

> [!NOTE]
> Są to wyjaśnienia oparte na wielu przybliżeniach i nie są "przyczyną" przewidywań. Bez ścisłej odporności matematycznej wnioskowania przyczynowego nie zalecamy użytkownikom podejmowania rzeczywistych decyzji na podstawie perturbacji funkcji What-If narzędzia. To narzędzie jest przeznaczone głównie do zrozumienia modelu i debugowania.

### <a name="visualization-in-azure-machine-learning-studio"></a>Wizualizacja w Azure Machine Learning studio

Jeśli ukończysz kroki [zdalnej](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) możliwości interpretacji (przekazywanie wygenerowanych wyjaśnień do historii uruchamiania usługi Azure Machine Learning), możesz wyświetlić wizualizacje na pulpicie nawigacyjnym objaśnień w [Azure Machine Learning studio](https://ml.azure.com). Ten pulpit nawigacyjny jest prostszą wersją widżetu pulpitu nawigacyjnego wygenerowanego w notesie Jupyter. What-If generowania punktów danych i wykresów ICE są wyłączone, ponieważ nie ma aktywnych obliczeń w Azure Machine Learning studio, które mogą wykonywać obliczenia w czasie rzeczywistym.

Jeśli zestaw danych, wyjaśnienia globalne i lokalne są dostępne, dane wypełniają wszystkie karty. Jeśli dostępne jest tylko globalne wyjaśnienie, karta Ważność poszczególnych funkcji zostanie wyłączona.

Postępuj zgodnie z jedną z tych ścieżek, aby uzyskać dostęp do pulpitu nawigacyjnego objaśnień w Azure Machine Learning studio:

* **Okienko Eksperymenty** (wersja zapoznawcza)
  1. Wybierz **pozycję Experiments** (Eksperymenty) w okienku po lewej stronie, aby wyświetlić listę eksperymentów, które zostały uruchomione Azure Machine Learning.
  1. Wybierz konkretny eksperyment, aby wyświetlić wszystkie przebiegi w tym eksperymencie.
  1. Wybierz przebieg, a następnie **kartę Wyjaśnienia** na pulpicie nawigacyjnym wizualizacji wyjaśnienia.

   [![Pulpit nawigacyjny wizualizacji z zagregowaną ważnością funkcji w programie AzureML Studio w eksperymentach](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png)](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png#lightbox)

* **Okienko Modele**
  1. Jeśli oryginalny model został zarejestrowany, zgodnie z instrukcjami z tematu [Deploy models with Azure Machine Learning](./how-to-deploy-and-where.md)(Wdrażanie modeli za pomocą usługi Azure Machine Learning ), możesz wybrać pozycję **Modele** w okienku po lewej stronie, aby go wyświetlić.
  1. Wybierz model, a następnie **kartę Wyjaśnienia,** aby wyświetlić pulpit nawigacyjny wyjaśnienia.

## <a name="interpretability-at-inference-time"></a>Możliwości interpretowania w czasie wnioskowania

Możesz wdrożyć objaśnienie wraz z oryginalnym modelem i użyć go podczas wnioskowania, aby podać poszczególne wartości ważności funkcji (wyjaśnienie lokalne) dla każdego nowego punktu danych. Oferujemy również lżejsze objaśnienia oceniania, które poprawiają wydajność interpretacji w czasie wnioskowania, co jest obecnie obsługiwane tylko w Azure Machine Learning SDK. Proces wdrażania objaśnienia do oceny lżejszej wagi jest podobny do wdrażania modelu i obejmuje następujące kroki:

1. Utwórz obiekt wyjaśnienia. Na przykład można użyć `TabularExplainer` :

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Utwórz objaśnienie oceniania z obiektem wyjaśnienia.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Skonfiguruj i zarejestruj obraz, który używa modelu objaśnienia oceniania.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Opcjonalnie możesz pobrać objaśnienie oceniania z chmury i przetestować wyjaśnienia.

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

1. Wd wdrażaj obraz do docelowego obiektu obliczeniowego, korzystając z następujących kroków:

   1. W razie potrzeby zarejestruj oryginalny model przewidywania, korzystając z procedury opisanej w części [Wdrażanie modeli za pomocą Azure Machine Learning](./how-to-deploy-and-where.md).

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

         Ta konfiguracja zależy od wymagań modelu. W poniższym przykładzie zdefiniowano konfigurację, która używa jednego rdzenia procesora CPU i jednego GB pamięci.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Utwórz plik z zależnościami środowisk.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. Utwórz niestandardowy plik dockerfile z zainstalowanym g++.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Wdrażanie utworzonego obrazu.
   
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

1. Czyszczenie.

   Aby usunąć wdrożoną usługę internetową, użyj programu `service.delete()` .

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* **Rozrzedne** dane nie są obsługiwane: pulpit nawigacyjny wyjaśnienia modelu znacznie się psuje/spowalnia przy dużej liczbie funkcji, dlatego obecnie nie obsługujemy formatu rozrzedzianych danych. Ponadto w przypadku dużych zestawów danych i dużej liczby funkcji pojawią się ogólne problemy z pamięcią. 

* Modele prognozowania nie są obsługiwane za pomocą wyjaśnień **modelu:** Możliwości interpretowania, najlepsze wyjaśnienie modelu nie są dostępne dla eksperymentów prognozowania automatycznego uczenia maszynowego, które zalecają następujące algorytmy jako najlepszy model: TCNForecaster, AutoArima, Wymuś, ExponentialSmoothing, Average, Naive, Seasonal Average i Seasonal Naive. Prognozowanie automatycznegoml ma modele regresji, które obsługują wyjaśnienia. Jednak na pulpicie nawigacyjnym wyjaśnienia karta "Ważność poszczególnych funkcji" nie jest obsługiwana w przypadku prognozowania ze względu na złożoność potoków danych.

* Lokalne wyjaśnienie indeksu **danych:** Pulpit nawigacyjny wyjaśnienia nie obsługuje pomiania lokalnych wartości ważności z identyfikatorem wiersza z oryginalnego zestawu danych weryfikacji, jeśli ten zestaw danych jest większy niż 5000 punktów danych, ponieważ pulpit nawigacyjny losowo zmienia dane w dół. Jednak pulpit nawigacyjny pokazuje wartości funkcji nieprzetworzonych zestawów danych dla każdego punktu danych przekazanego do pulpitu nawigacyjnego na karcie Ważność poszczególnych funkcji. Użytkownicy mogą mapować lokalne ważności z powrotem na oryginalny zestaw danych, dopasowując wartości funkcji nieprzetworzonych zestawów danych. Jeśli rozmiar zestawu danych weryfikacji jest mniejszy niż 5000 przykładów, funkcja w `index` programie AzureML Studio będzie odpowiadać indeksowi w zestawie danych weryfikacji.

* **Wykresy warunkowe/ICE** nie są obsługiwane w studio: wykresy What-If i indywidualne oczekiwania warunkowe (ICE Azure Machine Learning studio) nie są obsługiwane na karcie Wyjaśnienia na karcie Wyjaśnienia, ponieważ przekazane wyjaśnienie wymaga aktywnego obliczenia w celu ponownego obliczenia przewidywań i prawdopodobieństwa perturbednych funkcji. Jest ona obecnie obsługiwana w notesach Jupyter, gdy jest uruchamiana jako widżet przy użyciu zestawu SDK.


## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o możliwości interpretowania modelu](how-to-machine-learning-interpretability.md)

[Zapoznaj się z Azure Machine Learning Przykładowe notesy z możliwością interpretacji](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
