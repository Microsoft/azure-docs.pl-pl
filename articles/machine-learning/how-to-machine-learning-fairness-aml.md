---
title: Ocena atrakcyjności modeli ML w języku Python (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak oceniać i łagodzenie atrakcyjności modeli uczenia maszynowego przy użyciu Fairlearn i zestawu SDK języka Python Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 17b0564b4b73f5a5032343dcb78669cbf4cabd5a
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516154"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Użyj Azure Machine Learning z pakietem typu open source Fairlearn do oceny sprawiedliwości modeli ML (wersja zapoznawcza)

W tym przewodniku krok po kroku dowiesz się, jak używać pakietu języka Python Open Source [Fairlearn](https://fairlearn.github.io/) z Azure Machine Learning, aby wykonać następujące zadania:

* Oceniaj sprawiedliwe przewidywania modeli. Aby dowiedzieć się więcej o atrakcyjności uczenia maszynowego, zobacz [sprawiedliwe informacje w artykule dotyczącym uczenia maszynowego](concept-fairness-ml.md).
* Przekazuj, Wyświetlaj i pobieraj informacje o ocenie uczciwości do/z programu Azure Machine Learning Studio.
* Zobacz pulpit nawigacyjny oceny atrakcyjności w programie Azure Machine Learning Studio, aby móc korzystać z informacji o atrakcyjności Twoich modeli.

>[!NOTE]
> Ocena uczciwości nie jest czysto technicznym ćwiczeniem. **Ten pakiet może pomóc ocenić atrakcyjność modelu uczenia maszynowego, ale tylko ty można skonfigurować i podjąć decyzje dotyczące sposobu działania modelu.**  Chociaż ten pakiet pomaga identyfikować metryki ilościowe w celu oceny sprawiedliwości, deweloperzy modeli uczenia maszynowego muszą również przeprowadzać analizę jakościową, aby ocenić godziwość własnych modeli.

## <a name="azure-machine-learning-fairness-sdk"></a>Zestaw SDK Azure Machine Learning uczciwości 

Zestaw SDK Azure Machine Learning uczciwości, `azureml-contrib-fairness` integruje pakiet języka Python Open Source, [Fairlearn](http://fairlearn.github.io)w programie Azure Machine Learning. Aby dowiedzieć się więcej o integracji z usługą Fairlearn w ramach Azure Machine Learning, zapoznaj się z tymi [przykładowymi notesami](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness). Aby uzyskać więcej informacji na temat Fairlearn, zobacz [przykład podręcznika](https://fairlearn.github.io/master/auto_examples/) i [przykładowe notesy](https://github.com/fairlearn/fairlearn/tree/master/notebooks). 

Użyj następujących poleceń, aby zainstalować `azureml-contrib-fairness` pakiety i `fairlearn` :
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```
Nowsze wersje Fairlearn powinny również funkcjonować w poniższym przykładowym kodzie.



## <a name="upload-fairness-insights-for-a-single-model"></a>Przekazywanie szczegółowych informacji o jednym modelu

Poniższy przykład pokazuje, jak używać pakietu o atrakcyjności. Będziemy przesyłać szczegółowe informacje o atrakcyjności modelu do Azure Machine Learning i wyświetlać pulpit nawigacyjny oceny uczciwości w programie Azure Machine Learning Studio.

1. Uczenie przykładowego modelu w notesie Jupyter. 

    Dla zestawu danych używamy dobrze znanego zestawu danych dla dorosłych, który pobieramy z OpenML. Poudawać mamy problem z decyzją pożyczki z etykietą wskazującą, czy osoba zapłaciła za poprzednią pożyczkę. Będziemy przeszkolić model, aby przewidzieć, czy wcześniej niewidziane osoby będą zwracały pożyczkę. Taki model może być używany podczas podejmowania decyzji kredytowych.

    ```python
    import copy
    import numpy as np
    import pandas as pd

    from sklearn.compose import ColumnTransformer
    from sklearn.datasets import fetch_openml
    from sklearn.impute import SimpleImputer
    from sklearn.linear_model import LogisticRegression
    from sklearn.model_selection import train_test_split
    from sklearn.preprocessing import StandardScaler, OneHotEncoder
    from sklearn.compose import make_column_selector as selector
    from sklearn.pipeline import Pipeline
    
    from fairlearn.widget import FairlearnDashboard

    # Load the census dataset
    data = fetch_openml(data_id=1590, as_frame=True)
    X_raw = data.data
    y = (data.target == ">50K") * 1
    
    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    X_raw = data.data
    y = (data.target == ">50K") * 1
    A = X_raw[["race", "sex"]]
    X = X_raw.drop(labels=['sex', 'race'],axis = 1)
    
    # Split the data in "train" and "test" sets
    (X_train, X_test, y_train, y_test, A_train, A_test) = train_test_split(
        X_raw, y, A, test_size=0.3, random_state=12345, stratify=y
    )

    # Ensure indices are aligned between X, y and A,
    # after all the slicing and splitting of DataFrames
    # and Series
    X_train = X_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    y_train = y_train.reset_index(drop=True)
    y_test = y_test.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Define a processing pipeline. This happens after the split to avoid data leakage
    numeric_transformer = Pipeline(
        steps=[
            ("impute", SimpleImputer()),
            ("scaler", StandardScaler()),
        ]
    )
    categorical_transformer = Pipeline(
        [
            ("impute", SimpleImputer(strategy="most_frequent")),
            ("ohe", OneHotEncoder(handle_unknown="ignore")),
        ]
    )
    preprocessor = ColumnTransformer(
        transformers=[
            ("num", numeric_transformer, selector(dtype_exclude="category")),
            ("cat", categorical_transformer, selector(dtype_include="category")),
        ]
    )

    # Put an estimator onto the end of the pipeline
    lr_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                LogisticRegression(solver="liblinear", fit_intercept=True),
            ),
        ]
    )

    # Train the model on the test data
    lr_predictor.fit(X_train, y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Race', 'Sex'],
                       y_true=y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Zaloguj się do Azure Machine Learning i zarejestruj model.
   
    Pulpit nawigacyjny uczciwości można zintegrować z zarejestrowanymi lub niezastrzeżonymi modelami. Zarejestruj model w Azure Machine Learning, wykonując następujące czynności:
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    ```

3. Metryki uczciwego obliczania.

    Utwórz słownik pulpitu nawigacyjnego przy użyciu `metrics` pakietu Fairlearn. `_create_group_metric_set`Metoda ma argumenty podobne do konstruktora pulpitu nawigacyjnego, z tą różnicą, że poufne funkcje są przekazane jako słownik (aby upewnić się, że nazwy są dostępne). W przypadku wywołania tej metody należy również określić typ prognozowania (w tym przypadku w przypadku klasyfikacji binarnej).

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.race, 'Sex': A_test.sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Przekaż wstępnie obliczone metryki uczciwe.
    
    Teraz Importuj `azureml.contrib.fairness` pakiet, aby wykonać przekazywanie:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Utwórz eksperyment, a następnie uruchom i przekaż pulpit nawigacyjny do niego:
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. Sprawdź pulpit nawigacyjny uczciwości w programie Azure Machine Learning Studio

    Jeśli wykonano poprzednie kroki (przekazanie wygenerowanego wglądu w dane Azure Machine Learning o uczciwych możliwościach), możesz wyświetlić pulpit nawigacyjny uczciwości w programie [Azure Machine Learning Studio](https://ml.azure.com). Ten pulpit nawigacyjny jest tym samym pulpitem nawigacyjnym wizualizacji udostępnianym w Fairlearn, co pozwala na analizowanie różnic między podgrupami poufnych funkcji (np. samców a kobieta).
    Aby uzyskać dostęp do pulpitu nawigacyjnego wizualizacji w programie Azure Machine Learning Studio, wykonaj jedną z tych ścieżek:

    * **Okienko eksperymenty (wersja zapoznawcza)**
    1. Wybierz pozycję **eksperymenty** w okienku po lewej stronie, aby wyświetlić listę eksperymentów, które zostały uruchomione na Azure Machine Learning.
    1. Wybierz konkretny eksperyment, aby wyświetlić wszystkie uruchomienia w tym eksperymentie.
    1. Wybierz przebieg, a następnie kartę **sprawiedliwość** do pulpitu nawigacyjnego wizualizacji z wyjaśnieniem.


    [![Pulpit nawigacyjny uczciwości](./media/how-to-machine-learning-fairness-aml/dashboard.png)](./media/how-to-machine-learning-fairness-aml/dashboard.png#lightbox)
    
    * **Okienko modele**
    1. Jeśli oryginalny model został zarejestrowany zgodnie z poprzednimi krokami, w okienku po lewej stronie możesz wybrać **modele** , aby je wyświetlić.
    1. Wybierz model, a następnie kartę **godziwość** , aby wyświetlić pulpit nawigacyjny wizualizacji z wyjaśnieniem.

    Aby dowiedzieć się więcej na temat pulpitu nawigacyjnego wizualizacji i jego zawartości, zapoznaj się z [podręcznikiem użytkownika](https://fairlearn.github.io/master/user_guide/assessment.html#fairlearn-dashboard)Fairlearn.

## <a name="upload-fairness-insights-for-multiple-models"></a>Przekazywanie szczegółowych informacji dotyczących wielu modeli

Aby porównać wiele modeli i zobaczyć, jak ich oceny uczciwości są różne, można przekazać więcej niż jeden model do pulpitu nawigacyjnego wizualizacji i porównać ich wydajność.

1. Uczenie modeli:
    
    Teraz tworzymy drugi klasyfikator w oparciu o obsługę maszyny wektorowej szacowania i przekazanie słownika pulpitu nawigacyjnego o atrakcyjności przy użyciu pakietu Fairlearn `metrics` . Przyjęto założenie, że wcześniej szkolony model jest nadal dostępny.


    ```python
    # Put an SVM predictor onto the preprocessing pipeline
    from sklearn import svm
    svm_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                svm.SVC(),
            ),
        ]
    )

    # Train your second classification model
    svm_predictor.fit(X_train, y_train)
    ```

2. Rejestrowanie modeli

    Następnie zarejestruj oba modele w Azure Machine Learning. Dla wygody należy przechowywać wyniki w słowniku, który mapuje `id` zarejestrowany model (ciąg w `name:version` formacie) do samego predykcyjnego:

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. Lokalne ładowanie pulpitu nawigacyjnego Fairlearn

    Przed przekazaniem szczegółowych informacji do Azure Machine Learning można przeanalizować te przewidywania na lokalnie wywołanym pulpicie nawigacyjnym Fairlearn. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Race', 'Sex'],
                    y_true=y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Metryki uczciwego obliczania.

    Utwórz słownik pulpitu nawigacyjnego przy użyciu `metrics` pakietu Fairlearn.

    ```python
    sf = { 'Race': A_test.race, 'Sex': A_test.sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Przekaż wstępnie obliczone metryki uczciwe.
    
    Teraz Importuj `azureml.contrib.fairness` pakiet, aby wykonać przekazywanie:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Utwórz eksperyment, a następnie uruchom i przekaż pulpit nawigacyjny do niego:
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    Podobnie jak w poprzedniej sekcji, można wykonać jedną z opisanych powyżej ścieżek (za pośrednictwem **eksperymentów** lub **modeli**) w programie Azure Machine Learning Studio, aby uzyskać dostęp do pulpitu nawigacyjnego wizualizacji i porównać te dwa modele pod kątem godziwości i wydajności.


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Przekazywanie nieskorygowanych i nieuczciwych informacji o godziwym wglądie

Można użyć [algorytmów łagodzenia](https://fairlearn.github.io/master/user_guide/mitigation.html)Fairlearn, porównać ich wygenerowane modele z ograniczeniami na oryginalny, nieskorygowany model, a także przejść pod kątem wydajności/atrakcyjności w porównaniu do modeli.

Aby zapoznać się z przykładem, który demonstruje użycie algorytmu ograniczenia [wyszukiwania w siatce](https://fairlearn.github.io/master/user_guide/mitigation.html#grid-search) (co powoduje utworzenie kolekcji środków z ograniczeniami i efektywnością), zapoznaj się z tym [przykładowym notesem](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb). 

Przekazanie szczegółowych informacji o atrakcyjności dla wielu modeli w jednym przebiegu umożliwia porównanie modeli w odniesieniu do uczciwości i wydajności. Możesz kliknąć dowolny z modeli wyświetlanych na wykresie porównawczym modelu, aby zobaczyć szczegółowe informacje o sprawiedliwym wykorzystaniu określonego modelu.


[![Pulpit nawigacyjny Fairlearn porównania modelu](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej na temat godziwości modelu](concept-fairness-ml.md)

[Zapoznaj się z przykładami atrakcyjnych notesów Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
