---
title: 'Wykonaj skrypt języka Python: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchomić kod w języku Python przy użyciu modułu uruchamiania skryptów języka Python w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/27/2020
ms.openlocfilehash: 9b2114672db755efba1818505c8f399ac01aea71
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983605"
---
# <a name="execute-python-script-module"></a>Wykonaj moduł skryptu języka Python

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Użyj tego modułu, aby uruchomić kod języka Python. Aby uzyskać więcej informacji na temat architektury i zasad projektowania języka Python, zobacz [następujący artykuł](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts).

W języku Python można wykonywać zadania, które nie są obecnie obsługiwane przez istniejące moduły, takie jak:

+ Wizualizacja danych przy użyciu`matplotlib`
+ Wyliczanie zestawów danych i modeli w obszarze roboczym przy użyciu bibliotek języka Python
+ Odczytywanie, ładowanie i manipulowanie danymi ze źródeł nieobsługiwanych przez moduł [Import danych](./import-data.md)
+ Uruchom własny kod uczenia głębokiego 


Azure Machine Learning używa dystrybucji Anaconda języka Python, która obejmuje wiele typowych narzędzi do przetwarzania danych. Zostanie automatycznie zaktualizowana wersja Anaconda. Bieżąca wersja:
 -  Anaconda 4.5 + Distribution for Python 3,6 

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

 Aby zainstalować inne pakiety spoza wstępnie zainstalowanej listy, na przykład *scikit-misc*, Dodaj następujący kod do skryptu: 

 ```python
import os
os.system(f"pip install scikit-misc")
```
> [!NOTE]
> Jeśli potok zawiera wiele modułów skryptu wykonywania w języku Python i potrzebujesz tych samych pakietów, które nie znajdują się na liście wstępnie zainstalowanych, zainstaluj pakiety w każdym module odpowiednio. 

## <a name="upload-files"></a>Przekazywanie plików
**Skrypt Execute Python** obsługuje przekazywanie plików przy użyciu [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-).

Poniższy przykład pokazuje, jak przekazać plik obrazu w module **wykonywania skryptu języka Python** :

```Python

# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to
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
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

Po zakończeniu przebiegu potoku można wyświetlić podgląd obrazu w prawym panelu modułu

> [!div class="mx-imgBorder"]
> ![Przekazany — obraz](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Jak skonfigurować skrypt wykonywania skryptu języka Python

Moduł **wykonywania skryptu języka Python** zawiera przykładowy kod w języku Python, którego można użyć jako punktu wyjścia. Aby skonfigurować moduł **wykonywania skryptu języka Python** , należy podać zestaw danych wejściowych i kod języka Python do wykonania w polu tekstowym **skrypt języka Python** .

1. Dodaj moduł **wykonywania skryptu języka Python** do potoku.

2. Dodaj i Połącz **pozycję DataSet1** dowolnych zestawów danych z projektanta, który ma być używany na potrzeby danych wejściowych. Odwołuje się do tego zestawu danych w skrypcie języka Python jako **DataFrame1**.

    Użycie zestawu danych jest opcjonalne, jeśli chcesz wygenerować dane przy użyciu języka Python, lub użyj kodu Python, aby zaimportować dane bezpośrednio do modułu.

    Ten moduł obsługuje Dodawanie drugiego zestawu danych w **Dataset2**. Odwołuje się do drugiego zestawu danych w skrypcie języka Python jako DataFrame2.

    Zestawy danych przechowywane w Azure Machine Learning są automatycznie konwertowane na dane **Pandas** . ramki po załadowaniu tego modułu.

    ![Wykonaj mapę wejściową języka Python](media/module/python-module.png)

4. Aby uwzględnić nowe pakiety lub kod w języku Python, Dodaj plik spakowane zawierający te zasoby niestandardowe w **pakiecie skryptu**. Dane wejściowe do **pakietu skryptu** muszą być plikiem skompresowanym przekazanym do obszaru roboczego jako zestaw danych typu plików. Można przekazać zestaw danych na stronie zasobów **zestawy** danych, a następnie przeciągnąć i upuścić moduł DataSet z listy **moje zbiory** w lewym drzewie modułów na stronie Tworzenie projektanta. 

    Podczas wykonywania potoku można użyć dowolnego pliku zawartego w przekazanym skompresowanym archiwum. Jeśli archiwum zawiera strukturę katalogów, struktura jest zachowywana, ale należy dołączać katalog o nazwie **src** do ścieżki.

5. W polu tekstowym **skrypt języka Python** wpisz lub wklej prawidłowy skrypt w języku Python.

    > [!NOTE]
    > Należy zachować ostrożność podczas pisania skryptu i upewnić się, że nie występuje błąd składniowy, na przykład przy użyciu niezadeklarowanych obiektów lub niezaimportowanych modułów. Należy również zwrócić szczególną uwagę na listę wstępnie zainstalowanych modułów. Aby zaimportować moduły, których nie ma na liście, zainstaluj odpowiednie pakiety w skrypcie, takie jak
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    Pole tekstowe **skrypt języka Python** jest wstępnie wypełnione kilkoma instrukcjami w komentarzach i przykładowym kodzie na potrzeby dostępu do danych i wyjścia. Należy edytować lub zamienić ten kod. Pamiętaj, aby przestrzegać Konwencji języka Python dotyczących wcięć i wielkości liter.

    + Skrypt musi zawierać funkcję o nazwie `azureml_main` jako punkt wejścia dla tego modułu.
    + Funkcja punktu wejścia musi mieć dwa argumenty wejściowe: `Param<dataframe1>` i `Param<dataframe2>`, nawet jeśli te argumenty nie są używane w skrypcie.
    + Pliki spakowane połączone z trzecim portem wejściowym są rozpakowane i przechowywane w katalogu, `.\Script Bundle`który jest również dodawany do języka Python `sys.path`. 

    W związku z tym, jeśli plik `mymodule.py`zip zawiera, zaimportuj go za pomocą `import mymodule`.

    + Do projektanta można zwrócić dwa zestawy danych, które muszą być sekwencją typu `pandas.DataFrame`. Możesz tworzyć inne dane wyjściowe w kodzie języka Python i zapisywać je bezpośrednio w usłudze Azure Storage.

6. Prześlij potok lub wybierz moduł, a następnie kliknij pozycję **Uruchom wybrane** , aby uruchomić tylko skrypt języka Python.

    Wszystkie dane i kod są ładowane do maszyny wirtualnej i uruchamiane przy użyciu określonego środowiska języka Python.

## <a name="results"></a>Wyniki

Wyniki wszelkich obliczeń wykonanych przez osadzony kod języka Python muszą zostać dostarczone jako Pandas. Ramka Dataframe, która jest automatycznie konwertowana do formatu zestawu danych Azure Machine Learning, dzięki czemu można używać wyników z innymi modułami w potoku.

Moduł zwraca dwa zestawy danych:  
  
+ **Zestaw danych wyników 1**, zdefiniowany przez pierwszą zwróconą ramkę datapandas w skrypcie języka Python

+ **Zestaw danych wynikowych 2**, zdefiniowany przez drugą zwróconą ramkę datapandas w skrypcie języka Python


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 