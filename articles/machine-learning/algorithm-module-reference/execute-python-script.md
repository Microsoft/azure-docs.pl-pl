---
title: 'Wykonaj skrypt języka Python: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchomić kod w języku Python przy użyciu modułu uruchamiania skryptów języka Python w programie Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: devx-track-python
author: likebupt
ms.author: keli19
ms.date: 01/02/2021
ms.openlocfilehash: 6003ca9156d8553604d7ebbf94c5c3373d077f0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102500703"
---
# <a name="execute-python-script-module"></a>Wykonaj moduł skryptu języka Python

W tym artykule opisano moduł uruchamiania skryptów języka Python w programie Azure Machine Learning Designer.

Użyj tego modułu, aby uruchomić kod języka Python. Aby uzyskać więcej informacji na temat architektury i zasad projektowania języka Python, zobacz [jak uruchomić kod w języku Python w programie Azure Machine Learning Designer](../how-to-designer-python.md).

W języku Python można wykonywać zadania, które nie są obsługiwane przez istniejące moduły, takie jak:

+ Wizualizowanie danych przy użyciu `matplotlib` .
+ Korzystanie z bibliotek języka Python do wyliczania zestawów danych i modeli w obszarze roboczym.
+ Odczytywanie, ładowanie i manipulowanie danymi ze źródeł, które nie są obsługiwane przez moduł [importu danych](./import-data.md) .
+ Uruchom własny kod uczenia głębokiego. 

## <a name="supported-python-packages"></a>Obsługiwane pakiety języka Python

Azure Machine Learning używa dystrybucji Anaconda języka Python, która obejmuje wiele typowych narzędzi do przetwarzania danych. Zostanie automatycznie zaktualizowana wersja Anaconda. Bieżąca wersja to:
 -  Anaconda 4.5 + Distribution for Python 3,6 

Aby uzyskać pełną listę, zapoznaj się z sekcją [preinstalowane pakiety Python](#preinstalled-python-packages).

Aby zainstalować pakiety, które nie znajdują się na wstępnie zainstalowanej liście (na przykład *scikit-misc*), Dodaj następujący kod do skryptu: 

```python
import os
os.system(f"pip install scikit-misc")
```

Użyj poniższego kodu, aby zainstalować pakiety w celu uzyskania lepszej wydajności, szczególnie w przypadku wnioskowania:
```python
import importlib.util
package_name = 'scikit-misc'
spec = importlib.util.find_spec(package_name)
if spec is None:
    import os
    os.system(f"pip install scikit-misc")
```

> [!NOTE]
> Jeśli potok zawiera wiele modułów skryptu wykonywania w języku Python, które wymagają pakietów, które nie znajdują się na liście wstępnie zainstalowanych, zainstaluj pakiety w każdym module.

> [!WARNING]
> Moduł skryptu języka Python excute nie obsługuje instalowania pakietów, które są zależne od dodatkowych bibliotek natywnych za pomocą polecenia, takiego jak "apt-get", takie jak Java, moduł pyodbc i itp. Jest to spowodowane tym, że ten moduł jest wykonywany w prostym środowisku z wstępnie zainstalowanym językiem Python i z uprawnieniem innym niż administrator.  

## <a name="access-to-current-workspace-and-registered-datasets"></a>Dostęp do bieżącego obszaru roboczego i zarejestrowanych zestawów danych

Aby uzyskać dostęp do [zarejestrowanych zestawów danych](../how-to-create-register-datasets.md) w obszarze roboczym, można odwołać się do następującego przykładowego kodu:

```Python
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')
    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    #access to current workspace
    ws = run.experiment.workspace

    #access to registered dataset of current workspace
    from azureml.core import Dataset
    dataset = Dataset.get_by_name(ws, name='test-register-tabular-in-designer')
    dataframe1 = dataset.to_pandas_dataframe()
     
    # If a zip file is connected to the third input port,
    # it is unzipped under "./Script Bundle". This directory is added
    # to sys.path. Therefore, if your zip file contains a Python file
    # mymodule.py you can import it using:
    # import mymodule

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
```

## <a name="upload-files"></a>Przekazywanie plików
Moduł wykonywania skryptu języka Python obsługuje przekazywanie plików przy użyciu [zestawu SDK języka python Azure Machine Learning](/python/api/azureml-core/azureml.core.run%28class%29#upload-file-name--path-or-stream-).

Poniższy przykład pokazuje, jak przekazać plik obrazu w module wykonywania skryptu języka Python:

```Python

# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Imports up here can be used to
import pandas as pd

# The entry point function must have two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # For example:
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

Po zakończeniu przebiegu potoku można wyświetlić podgląd obrazu w prawym panelu modułu.

> [!div class="mx-imgBorder"]
> ![Podgląd przekazanego obrazu](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Jak skonfigurować skrypt wykonywania skryptu języka Python

Moduł wykonywania skryptu języka Python zawiera przykładowy kod w języku Python, którego można użyć jako punktu wyjścia. Aby skonfigurować moduł wykonywania skryptu języka Python, podaj zestaw danych wejściowych i kod w języku Python do uruchomienia w polu tekstowym **skrypt języka Python** .

1. Dodaj moduł **wykonywania skryptu języka Python** do potoku.

2. Dodaj i Połącz **pozycję DataSet1** dowolnych zestawów danych z projektanta, który ma być używany na potrzeby danych wejściowych. Odwołuje się do tego zestawu danych w skrypcie języka Python jako **DataFrame1**.

    Użycie zestawu danych jest opcjonalne. Użyj go, jeśli chcesz wygenerować dane przy użyciu języka Python, lub użyj kodu Python, aby zaimportować dane bezpośrednio do modułu.

    Ten moduł obsługuje Dodawanie drugiego zestawu danych w **Dataset2**. Odwołuje się do drugiego zestawu danych w skrypcie języka Python jako **DataFrame2**.

    Zestawy danych przechowywane w Azure Machine Learning są automatycznie konwertowane na Pandas ramki, gdy są ładowane z tym modułem.

    ![Wykonaj mapę wejściową języka Python](media/module/python-module.png)

4. Aby uwzględnić nowe pakiety lub kod w języku Python, Połącz spakowany plik zawierający te zasoby niestandardowe z portem **pakietu skryptu** . Lub jeśli skrypt ma rozmiar większy niż 16 KB, użyj portu **pakietu skryptu** , aby uniknąć błędów, takich jak *CommandLine, przekracza limit 16597 znaków*. 

    
    1. Pakiet skryptu i innych zasobów niestandardowych należy powiązać z plikiem zip.
    1. Przekaż plik zip jako **zestaw danych pliku** do Studio. 
    1. Przeciągnij moduł DataSet z listy *DataSets* w lewym okienku modułu na stronie Tworzenie projektanta. 
    1. Połącz moduł DataSet z portem **pakietu skryptu** wykonaj moduł **skryptu języka Python** .
    
    Podczas wykonywania potoku można użyć dowolnego pliku zawartego w przekazanym skompresowanym archiwum. Jeśli archiwum zawiera strukturę katalogów, struktura jest zachowywana.
 
    > [!WARNING]
    > **Nie** używaj **aplikacji** jako nazwy folderu lub skryptu, ponieważ **aplikacja** jest słowem zastrzeżonym dla wbudowanych usług. Można jednak użyć innych przestrzeni nazw, takich jak `app123` .
   
    Poniżej znajduje się przykład pakietu skryptu, który zawiera plik skryptu w języku Python i plik txt:
      
    > [!div class="mx-imgBorder"]
    > ![Przykład pakietu skryptu](media/module/python-script-bundle.png)  

    Poniżej znajduje się zawartość `my_script.py` :

    ```python
    def my_func(dataframe1):
    return dataframe1
    ```
    Poniżej przedstawiono przykładowy kod pokazujący, jak używać plików w pakiecie skryptu:    

    ```python
    import pandas as pd
    from my_script import my_func
 
    def azureml_main(dataframe1 = None, dataframe2 = None):
 
        # Execution logic goes here
        print(f'Input pandas.DataFrame #1: {dataframe1}')
 
        # Test the custom defined python function
        dataframe1 = my_func(dataframe1)
 
        # Test to read custom uploaded files by relative path
        with open('./Script Bundle/my_sample.txt', 'r') as text_file:
            sample = text_file.read()
    
        return dataframe1, pd.DataFrame(columns=["Sample"], data=[[sample]])
    ```

5. W polu tekstowym **skrypt języka Python** wpisz lub wklej prawidłowy skrypt w języku Python.

    > [!NOTE]
    >  Należy zachować ostrożność podczas pisania skryptu. Upewnij się, że nie ma błędów składniowych, takich jak użycie niezadeklarowanych zmiennych lub nieimportowanych modułów lub funkcji. Zwróć szczególną uwagę na listę wstępnie zainstalowanych modułów. Aby zaimportować moduły, które nie znajdują się na liście, zainstaluj odpowiednie pakiety w skrypcie, takie jak:
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    Pole tekstowe skryptu w języku **Python** jest wstępnie wypełniane kilkoma instrukcjami w komentarzach i przykładowym kodzie na potrzeby dostępu do danych i wyjścia. Należy edytować lub zamienić ten kod. Postępuj zgodnie z konwencjami języka Python, aby uzyskać wcięcia i wielkość liter:

    + Skrypt musi zawierać funkcję o nazwie `azureml_main` jako punkt wejścia dla tego modułu.
    + Funkcja punktu wejścia musi mieć dwa argumenty wejściowe, `Param<dataframe1>` a `Param<dataframe2>` nawet wtedy, gdy te argumenty nie są używane w skrypcie.
    + Pliki spakowane połączone z trzecim portem wejściowym są rozpakowane i przechowywane w katalogu `.\Script Bundle` , który jest również dodawany do języka Python `sys.path` . 

    Jeśli plik zip zawiera `mymodule.py` , zaimportuj go za pomocą polecenia `import mymodule` .

    Do projektanta można zwrócić dwa zestawy danych, które muszą być sekwencją typu `pandas.DataFrame` . Możesz tworzyć inne dane wyjściowe w kodzie języka Python i zapisywać je bezpośrednio w usłudze Azure Storage.

    > [!WARNING]
    > **Nie** zaleca się łączenia się z bazą danych ani innymi magazynami zewnętrznymi w **module wykonywania skryptów języka Python**. Możesz użyć modułu [Importuj dane](./import-data.md) i [modułu eksport danych](./export-data.md)     

6. Prześlij potok.

    Jeśli moduł został ukończony, sprawdź dane wyjściowe, jeśli jest to oczekiwane.

    W przypadku niepowodzenia modułu należy wykonać pewne Rozwiązywanie problemów. Wybierz moduł, a następnie otwórz okno dane **wyjściowe + dzienniki** w okienku po prawej stronie. Otwórz **70_driver_log.txt** i wyszukaj **w azureml_main**, a następnie sprawdź, który wiersz spowodował błąd. Na przykład "plik"/tmp/tmp01_ID/user_script. pr ", wiersz 17, w azureml_main" wskazuje, że wystąpił błąd w 17 wierszach skryptu języka Python.

## <a name="results"></a>Wyniki

Wyniki wszelkich obliczeń przez osadzony kod języka Python muszą zostać podane jako `pandas.DataFrame` , które są automatycznie konwertowane na format zestawu danych Azure Machine Learning. Następnie można użyć wyników z innymi modułami w potoku.

Moduł zwraca dwa zestawy danych:  
  
+ **Zestaw danych wyników 1**, zdefiniowany przez pierwszą zwróconą ramkę danych Pandas w skrypcie języka Python.

+ **Zestaw danych wyników 2**, zdefiniowany przez sekundę zwróconą ramkę danych Pandas w skrypcie języka Python.

## <a name="preinstalled-python-packages"></a>Wstępnie zainstalowane pakiety języka Python
Wstępnie zainstalowane pakiety są następujące:
-    ADAL = = 1.2.2
-    ApplicationInsights = = 0.11.9
-    attri = = 19.3.0
-    Azure — wspólne = = 1.1.25
-    Azure-Core = = 1.3.0
-    Azure-graphrbac = = 0.61.1
-    Azure — tożsamość = = 1.3.0
-    Azure-zarządzanie autoryzacją = = 0.60.0
-    Azure-zarządzanie-containerregistry = = 2.8.0
-    Azure-zarządzanie — Magazyn kluczy = = 2.2.0
-    Azure-zarządzanie-Resource = = 8.0.1
-    Azure-Zarządzanie magazynem = = 8.0.0
-    Azure-Storage-BLOB = = 1.5.0
-    Azure-Storage-Common = = 1.4.2
-    Azure-Core = = 1.1.5.5
-    Azure-dataprzygotowanie — natywny = = 14.1.0
-    Azure — dataprzygotowanie = = 1.3.5
-    Azure-wartości domyślne = = 1.1.5.1
-    Azure-Designer-Classic-module = = 0.0.118
-    Azure-Designer-Core = = 0.0.31
-    Azure-Designer-Internal = = 0.0.18
-    Azure-Model-Management-SDK = = 1.0.1 B6. post1
-    Azure-Pipeline-Core = = 1.1.5
-    Azure-Telemetria = = 1.1.5.3
-    porty wieloportowe. tempfile = = 1.0
-    porty wieloportowe. WeakRef = = 1.0. post1
-    boto3 = = 1.12.29
-    botocore = = 1.15.29
-    cachetools = = 4.0.0
-    poświadcza = = 2019.11.28
-    cffi = = 1.12.3
-    chardet = = 3.0.4
-    Kliknij = = 7.1.1
-    cloudpickle = = 1.3.0
-    ConfigParser = = 3.7.4
-    contextlib2 = = 0.6.0. post1
-    Kryptografia = = 2.8
-    Cycle = = 0.10.0
-    Dill = = 0.3.1.1
-    dystrybucji = = 1.4.0
-    Docker = = 4.2.0
-    docutils = = 0.15.2
-    dotnetcore2 = = 2.1.13
-    Kolba = = 1.0.3
-    fusepy = = 3.0.1
-    gensim = = 3.8.1
-    Google-API-Core = = 1.16.0
-    Google-auth = = 1.12.0
-    Google-Cloud-Core = = 1.3.0
-    Google-Cloud-Storage = = 1.26.0
-    Google-możliwością wznowienia-Media = = 0.5.0
-    googleapis-Common-Protos = = 1.51.0
-    gunicorn = = 19.9.0
-    IDNA = = 2.9
-    niezrównoważone — Dowiedz się = = 0.4.3
-    isodate = = 0.6.0
-    itsdangerous = = 1.1.0
-    jeepney = = 0.4.3
-    jinja2 = = 2.11.1
-    jmespath = = 0.9.5
-    joblib = = 0.14.0
-    JSON-rejestrowanie-PR = = 0,2
-    jsonpickle = = 1.3
-    jsonschema = = 3.0.1
-    kiwisolver = = 1.1.0
-    Liac-ARFF = = 2.4.0
-    lightgbm = = 2.2.3
-    markupsafe = = 1.1.1
-    matplotlib = = 3.1.3
-    Więcej — itertools = = 6.0.0
-    msal-Extensions = = 0.1.3
-    msal = = 1.1.0
-    msrest = = 0.6.11
-    msrestazure = = 0.6.3
-    NDG-httpsclient = = 0.5.1
-    nimbusml = = 1.6.1
-    numpy = = 1.18.2
-    oauthlib = = 3.1.0
-    Pandas = = 0.25.3
-    pathspec = = 0.7.0
-    PIP = = 20.0.2
-    portalocker = = 1.6.0
-    protobuf = = 3.11.3
-    pyarrow = = 0.16.0
-    pyasn1-modules = = 0.2.8
-    pyasn1 = = 0.4.8
-    pycparser = = 2.20
-    pycryptodomex = = 3.7.3
-    pyjwt = = 1.7.1
-    pyopenssl = = 19.1.0
-    pyparsing = = 2.4.6
-    pyrsistent = = 0.16.0
-    Python-dateutil = = 2.8.1
-    pytz = = 2019.3
-    żądania — oauthlib = = 1.3.0
-    żądania = = 2.23.0
-    RSA = = 4.0
-    ruamel. YAML = = 0.15.89
-    s3transfer = = 0.3.3
-    scikit-Dowiedz się = = 0.22.2
-    scipy = = 1.4.1
-    secretstorage = = 3.1.2
-    setuptools = = 46.1.1. post20200323
-    sześć = = 1.14.0
-    Inteligentne-Open = = 1.10.0
-    urllib3 = = 1.25.8
-    WebSocket-Client = = 0.57.0
-    Werkzeug = = 0.16.1
-    koło = = 0.34.2

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning.