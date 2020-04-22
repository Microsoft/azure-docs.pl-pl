---
title: 'Wykonywanie skryptu Python: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu Wykonywanie skryptu języka Python w usłudze Azure Machine Learning do uruchamiania kodu języka Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 79dc1b188e91028a98f43dc24972228f2d2101be
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684726"
---
# <a name="execute-python-script-module"></a>Wykonywanie modułu skryptu Języka Python

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do uruchamiania kodu języka Python. Aby uzyskać więcej informacji na temat architektury i zasad projektowania języka Python, zobacz [następujący artykuł](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts).

Za pomocą języka Python można wykonywać zadania, które nie są obecnie obsługiwane przez istniejące moduły, takie jak:

+ Wizualizacja danych za pomocą`matplotlib`
+ Używanie bibliotek języka Python do wyliczania zestawów danych i modeli w obszarze roboczym
+ Odczytywanie, ładowanie i manipulowanie danymi ze źródeł nie obsługiwanych przez moduł [Importuj dane](./import-data.md)
+ Uruchamiaj własny kod uczenia głębokiego 


Usługa Azure Machine Learning używa dystrybucji Anaconda języka Python, która zawiera wiele typowych narzędzi do przetwarzania danych. Zaktualizujemy wersję Anaconda automatycznie. Aktualna wersja to:
 -  Dystrybucja Anaconda 4.5+ dla Pythona 3.6 

Wstępnie zainstalowane pakiety to:
-    adal==1,2,2
-    applicationinsights==0.11.9
-    attrs==19.3.0
-    azure-common==1.1.25
-    azure-core==1.3.0
-    azure-graphrbac==0,61,1
-    azure-identity==1.3.0
-    azure-mgmt-authorization==0.60.0
-    azure-mgmt-containerregistry==2.8.0
-    azure-mgmt-keyvault==2.2.0
-    azure-mgmt-resource==8.0.1
-    azure-mgmt-storage==8.0.0
-    azure-storage-blob==1.5.0
-    azure-storage-common==1.4.2
-    azureml-core==1.1.5.5
-    azureml-dataprep-native==14.1.0
-    azureml-dataprep==1.3.5
-    azureml-defaults==1.1.5.1
-    azureml-designer-classic-modules==0.0.118
-    azureml-designer-core==0.0.31
-    azureml-designer-internal==0.0.18
-    azureml-model-management-sdk==1.0.1b6.post1
-    azureml-pipeline-core==1.1.5
-    azureml-telemetria==1.1.5.3
-    backports.tempfile==1,0
-    backports.weakref==1.0.post1
-    boto3==1.12.29
-    botocore==1.15.29
-    cachetools==4.0.0
-    certifi==2019.11.28
-    cffi==1.12.3
-    chardet==3,0,4
-    click==7.1.1
-    cloudpickle==1.3.0
-    configparser==3,7,4
-    contextlib2==0.6.0.post1
-    kryptografia==2,8
-    cycler==0,10,0
-    koperek==0,3,1,1
-    distro==1,4,0
-    docker==4.2.0
-    docutils==0,15,2
-    dotnetcore2==2.1.13
-    kolba==1,0,3
-    fusepy==3,0,1
-    gensim==3,8,1
-    google-api-core==1.16.0
-    google-auth==1.12,0
-    google-cloud-core==1.3.0
-    google-cloud-storage==1.26.0
-    google-resumable-media==0.5.0
-    googleapis-common-protos==1.51.0
-    gunicorn==19.9.0
-    idna==2,9
-    imbalanced-learn==0,4,3
-    iodate==0,6,0
-    itsdangerous==1,1,0
-    jeepney==0,4,3
-    jinja2==2.11.1
-    jmespath==0,9,5
-    joblib==0.14.0
-    json-logging-py==0,2
-    jsonpickle==1,3
-    jsonschema==3,0,1
-    kiwisolver==1.1.0
-    liac-arff==2.4.0
-    lightgbm==2.2.3
-    markupsafe==1.1.1
-    matplotlib==3.1.3
-    więcej-itertools==6,0,0
-    msal-extensions==0.1.3
-    msal==1,1,0
-    msrest==0,6,11
-    msrestazure==0,6,3
-    ndg-httpsclient==0.5.1
-    nimbusml==1,6,1
-    numpy==1.18.2
-    oauthlib==3,1,0
-    pandy==0,25,3
-    pathspec==0,7,0
-    pip==20.0.2
-    portalocker==1.6.0
-    protobuf==3.11.3
-    pyarrow==0,16,0
-    pyasn1-modules==0.2.8
-    pyasn1==0,4,8
-    pycparser==2,20
-    pycryptodomex==3.7.3
-    pyjwt==1,7,1
-    pyopenssl==19.1.0
-    pyparsing==2,4,6
-    pyrsistent==0,16,0
-    python-dateutil==2.8.1
-    pytz==2019.3
-    żądania-oauthlib==1.3.0
-    żądania==2.23.0
-    rsa==4,0
-    ruamel.yaml==0,15,89
-    s3transfer==0.3.3
-    scikit-learn==0.22.2
-    scipy==1,4,1
-    sekretarektwo==3.1.2
-    setuptools==46.1.1.post20200323
-    sześć==1.14,0
-    smart-open==1.10.0
-    urllib3==1.25.8
-    websocket-client==0.57.0
-    werkzeug==0,16,1
-    koło==0,34,2

 Aby zainstalować inne pakiety, które nie znajdują się na wstępnie zainstalowanej liście, na przykład *scikit-misc*, dodaj do skryptu następujący kod: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>Przekazywanie plików
Skrypt **Wykonywanie języka Python** obsługuje przekazywanie plików przy użyciu narzędzia Azure Machine Learning Python [SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-).

W poniższym przykładzie pokazano, jak przekazać plik obrazu w module **Wykonywanie skryptu języka Python:**

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
> ![Przesłany obraz](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Jak skonfigurować wykonywanie skryptu Pythona

Moduł **Execute Python Script** zawiera przykładowy kod języka Python, którego można użyć jako punktu wyjścia. Aby skonfigurować moduł **Wykonywanie skryptu Pythona,** należy podać zestaw danych wejściowych i kod języka Python do wykonania w polu tekstowym **skryptu Pythona.**

1. Dodaj moduł **Wykonywanie skryptu języka Python** do potoku.

2. Dodaj i połącz w **zestawie danych1** wszystkie zestawy danych od projektanta, którego chcesz użyć do wprowadzenia danych. Odwołanie się do tego zestawu danych w skrypcie języka Python jako **DataFrame1**.

    Użycie zestawu danych jest opcjonalne, jeśli chcesz wygenerować dane za pomocą języka Python lub użyć kodu Języka Python, aby zaimportować dane bezpośrednio do modułu.

    Ten moduł obsługuje dodanie drugiego zestawu danych do **zestawu danych2**. Odwołanie się do drugiego zestawu danych w skrypcie języka Python jako DataFrame2.

    Zestawy danych przechowywane w usłudze Azure Machine Learning są automatycznie konwertowane na **pandas** data.frames po załadowaniu z tym modułem.

    ![Wykonywanie mapy wprowadzania języka Python](media/module/python-module.png)

4. Aby dołączyć nowe pakiety lub kod języka Python, dodaj spakowany plik zawierający te zasoby niestandardowe w **pakiecie skryptów**. Dane wejściowe do **pakietu skryptów** muszą być spakowanym plikiem przekazanym do obszaru roboczego jako zestaw danych typu plik. Zestaw danych można przekazać na stronie Zasobów **zestawów danych,** a moduł zestawu danych można przeciągnąć i upuścić z listy Moje zestawy danych w drzewie **lewego** modułu na stronie tworzenia projektanta. 

    Każdy plik zawarty w przesłanym archiwum spakowanym może być używany podczas wykonywania potoku. Jeśli archiwum zawiera strukturę katalogów, struktura jest zachowywana, ale należy dołączyć katalog o nazwie **src** do ścieżki.

5. W polu tekstowym **skryptu Python** wpisz lub wklej prawidłowy skrypt Języka Python.

    > [!NOTE]
    > Należy zachować szczególną ostrożność podczas pisania skryptu i upewnij się, że nie ma błędu składni, takich jak przy użyciu nieo zadeklarowanego obiektu lub niezaimportowanego modułu. Należy również zwrócić szczególną uwagę na listę wstępnie zainstalowanych modułów. Aby zaimportować moduły, których nie ma na liście, zainstaluj odpowiednie pakiety w skrypcie, takie jak
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    Pole tekstowe **skryptu języka Python** jest wstępnie wypełnione niektórymi instrukcjami w komentarzach i przykładowym kodem dostępu do danych i danych wyjściowych. Należy edytować lub zastąpić ten kod. Pamiętaj, aby postępować zgodnie z konwencjami Języka Python na temat wcięci i wielkości liter.

    + Skrypt musi zawierać funkcję `azureml_main` o nazwie jako punkt wejścia dla tego modułu.
    + Funkcja punktu wejścia musi mieć `Param<dataframe1>` dwa `Param<dataframe2>`argumenty wejściowe: i , nawet jeśli te argumenty nie są używane w skrypcie.
    + Spakowane pliki podłączone do trzeciego portu wejściowego są rozpakowane i przechowywane w `.\Script Bundle` `sys.path`katalogu, który jest również dodawany do Pythona. 

    Dlatego jeśli plik zip `mymodule.py`zawiera , `import mymodule`zaimportuj go za pomocą .

    + Dwa zestawy danych mogą być zwracane do projektanta, `pandas.DataFrame`który musi być sekwencją typu . Można utworzyć inne dane wyjściowe w kodzie języka Python i zapisać je bezpośrednio w usłudze Azure Storage.

6. Prześlij potok lub wybierz moduł i kliknij przycisk **Uruchom wybrany,** aby uruchomić tylko skrypt Języka Python.

    Wszystkie dane i kod jest ładowany do maszyny wirtualnej i uruchamiane przy użyciu określonego środowiska Python.

## <a name="results"></a>Wyniki

Wyniki wszelkich obliczeń wykonywanych przez osadzony kod Języka Python muszą być dostarczone jako pandy. DataFrame, który jest automatycznie konwertowany do formatu zestawu danych usługi Azure Machine Learning, dzięki czemu można używać wyników z innymi modułami w potoku.

Moduł zwraca dwa zestawy danych:  
  
+ **Wyniki Dataset 1**, zdefiniowane przez pierwszy zwrócony pandas dataframe w skrypcie Języka Python

+ **Wynik dataset 2**, zdefiniowany przez drugą zwróconą ramę danych pand w skrypcie języka Python


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 