---
title: Ocena atrakcyjności modeli ML w języku Python (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak ocenić atrakcyjność modeli w Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 09/01/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 0332400ce6808e92ba09e3bee3277495f6b6d1a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90897490"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Użyj Azure Machine Learning z pakietem typu open source Fairlearn do oceny sprawiedliwości modeli ML (wersja zapoznawcza)



W tym przewodniku krok po kroku dowiesz się, jak używać pakietu języka Python Open Source [Fairlearn](https://fairlearn.github.io/) z Azure Machine Learning, aby wykonać następujące zadania:

* Oceniaj sprawiedliwe przewidywania modeli. Aby dowiedzieć się więcej o atrakcyjności uczenia maszynowego, zobacz [sprawiedliwe informacje w artykule dotyczącym uczenia maszynowego](concept-fairness-ml.md).
* Przekazuj, Wyświetlaj i pobieraj informacje o ocenie uczciwości do/z programu Azure Machine Learning Studio.
* Zobacz pulpit nawigacyjny oceny atrakcyjności w programie Azure Machine Learning Studio, aby móc korzystać z informacji o atrakcyjności Twoich modeli.

>[!NOTE]
> Ocena uczciwości nie jest czysto technicznym ćwiczeniem. **Ten pakiet może pomóc ocenić atrakcyjność modelu uczenia maszynowego, ale tylko ty można skonfigurować i podjąć decyzje dotyczące sposobu działania modelu.**  Chociaż ten pakiet pomaga identyfikować metryki ilościowe w celu oceny sprawiedliwości, deweloperzy modeli uczenia maszynowego muszą również przeprowadzać analizę jakościową, aby ocenić godziwość własnych modeli.

## <a name="azure-machine-learning-fairness-sdk"></a>Zestaw SDK Azure Machine Learning uczciwości 

Zestaw SDK Azure Machine Learning uczciwości, `azureml-contrib-fairness` integruje pakiet języka Python Open Source, [Fairlearn](http://fairlearn.github.io)w programie Azure Machine Learning. Aby dowiedzieć się więcej o integracji z usługą Fairlearn w ramach Azure Machine Learning, zapoznaj się z tymi [przykładowymi notesami](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness). Aby uzyskać więcej informacji na temat Fairlearn, zobacz [przykład podręcznika](https://fairlearn.github.io/auto_examples/) i [przykładowe notesy](https://github.com/fairlearn/fairlearn/tree/master/notebooks). 

Użyj następujących poleceń, aby zainstalować `azureml-contrib-fairness` pakiety i `fairlearn` :
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```



## <a name="upload-fairness-insights-for-a-single-model"></a>Przekazywanie szczegółowych informacji o jednym modelu

Poniższy przykład pokazuje, jak używać pakietu atrakcyjności do przekazywania szczegółowych informacji o atrakcyjności modelu do Azure Machine Learning i wyświetlania pulpitu nawigacyjnego oceny uczciwości w programie Azure Machine Learning Studio.

1. Uczenie przykładowego modelu w notesie Jupyter. 

    W przypadku zestawu danych używamy dobrze znanego zestawu danych dotyczących spisu dla dorosłych, który ładujemy przy użyciu `shap` (dla wygody). Na potrzeby tego przykładu traktujemy ten zestaw danych jako problem z decyzją pożyczki i poudawać, że etykieta wskazuje, czy każda osoba zapłaciła pożyczkę w przeszłości. Będziemy używać danych do uczenia predykcyjnego w celu przewidywania, czy wcześniej niewidziane osoby będą zwracały pożyczkę, czy nie. Przyjęto założenie, że przewidywania modelu są używane do decydowania, czy osoba powinna zaoferować pożyczkę.

    ```python
    from sklearn.model_selection import train_test_split
    from fairlearn.widget import FairlearnDashboard
    from sklearn.linear_model import LogisticRegression
    from sklearn.preprocessing import LabelEncoder, StandardScaler
    import pandas as pd
    import shap

    # Load the census dataset
    X_raw, Y = shap.datasets.adult()
    X_raw["Race"].value_counts().to_dict()
    

    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    A = X_raw[['Sex','Race']]
    X = X_raw.drop(labels=['Sex', 'Race'],axis = 1)
    X = pd.get_dummies(X)
    
    sc = StandardScaler()
    X_scaled = sc.fit_transform(X)
    X_scaled = pd.DataFrame(X_scaled, columns=X.columns)

    # Perform some standard data preprocessing steps to convert the data into a format suitable for the ML algorithms
    le = LabelEncoder()
    Y = le.fit_transform(Y)

    # Split data into train and test
    from sklearn.model_selection import train_test_split
    from sklearn.model_selection import train_test_split
    X_train, X_test, Y_train, Y_test, A_train, A_test = train_test_split(X_scaled, 
                                                        Y, 
                                                        A,
                                                        test_size = 0.2,
                                                        random_state=0,
                                                        stratify=Y)

    # Work around indexing issue
    X_train = X_train.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Improve labels
    A_test.Sex.loc[(A_test['Sex'] == 0)] = 'female'
    A_test.Sex.loc[(A_test['Sex'] == 1)] = 'male'


    A_test.Race.loc[(A_test['Race'] == 0)] = 'Amer-Indian-Eskimo'
    A_test.Race.loc[(A_test['Race'] == 1)] = 'Asian-Pac-Islander'
    A_test.Race.loc[(A_test['Race'] == 2)] = 'Black'
    A_test.Race.loc[(A_test['Race'] == 3)] = 'Other'
    A_test.Race.loc[(A_test['Race'] == 4)] = 'White'


    # Train a classification model
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Sex', 'Race'],
                       y_true=Y_test,
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
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
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

    Aby dowiedzieć się więcej na temat pulpitu nawigacyjnego wizualizacji i jego zawartości, zobacz [Podręcznik użytkownika](https://fairlearn.github.io/user_guide/assessment.html#fairlearn-dashboard)programu Fairlearn.

## <a name="upload-fairness-insights-for-multiple-models"></a>Przekazywanie szczegółowych informacji dotyczących wielu modeli

Jeśli interesuje Cię porównanie wielu modeli i dowiesz się, jak ich oceny uczciwości są różne, możesz przekazać więcej niż jeden model do pulpitu nawigacyjnego wizualizacji i przejść do ich wydajności.

1. Uczenie modeli:
    
    Oprócz wcześniejszego modelu regresji logistycznej tworzymymy teraz drugi klasyfikator oparty na maszynie wektorowej szacowania i przekazanie słownika pulpitu nawigacyjnego o atrakcyjności przy użyciu `metrics` pakietu Fairlearn. Należy pamiętać, że w tym miejscu pominiemy procedurę ładowania i przetwarzania danych, a następnie przejdź bezpośrednio do etapu szkolenia modelu.


    ```python
    # Train your first classification model
    from sklearn.linear_model import LogisticRegression
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # Train your second classification model
    from sklearn import svm
    svm_predictor = svm.SVC()
    svm_predictor.fit(X_train, Y_train)
    ```

2. Rejestrowanie modeli

    Następnie zarejestruj oba modele w Azure Machine Learning. Dla wygody w kolejnych wywołaniach metod, należy przechowywać wyniki w słowniku, który mapuje `id` zarejestrowany model (ciąg w `name:version` formacie) na samą predykcyjną:

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
                    sensitive_feature_names=['Sex', 'Race'],
                    y_true=Y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Metryki uczciwego obliczania.

    Utwórz słownik pulpitu nawigacyjnego przy użyciu `metrics` pakietu Fairlearn.

    ```python
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex }

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

Można użyć [algorytmów łagodzenia](https://fairlearn.github.io/user_guide/mitigation.html)Fairlearn, porównać ich wygenerowane modele z ograniczeniami na oryginalny, nieskorygowany model, a także przejść pod kątem wydajności/atrakcyjności w porównaniu do modeli.

Aby zapoznać się z przykładem, który demonstruje użycie algorytmu ograniczenia [wyszukiwania w siatce](https://fairlearn.github.io/user_guide/mitigation.html#grid-search) (co powoduje utworzenie kolekcji środków z ograniczeniami o różnej sprawności i wydajności), zapoznaj się z tym [przykładowym notesem](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb). 

Przekazanie szczegółowych informacji o atrakcyjności modeli w jednym przebiegu pozwoli na porównanie modeli w odniesieniu do uczciwości i wydajności. Możesz również kliknąć dowolny model wyświetlany na wykresie porównawczym modelu, aby zobaczyć szczegółowe informacje o sprawiedliwej szczegółowości określonego modelu.


[![Pulpit nawigacyjny Fairlearn porównania modelu](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej na temat godziwości modelu](concept-fairness-ml.md)

[Zapoznaj się z przykładami atrakcyjnych notesów Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
