---
title: Znane problemy & Rozwiązywanie problemów
titleSuffix: Azure Machine Learning
description: Uzyskaj pomoc dotyczącą znajdowania i poprawiania błędów lub błędów w Azure Machine Learning. Poznaj znane problemy, rozwiązywanie problemów i ich obejścia.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting, contperfq4
ms.date: 10/02/2020
ms.openlocfilehash: b49e7ab7f3412177ee9eafad8d1a68525e054421
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314764"
---
# <a name="known-issues-and-troubleshooting-in-azure-machine-learning"></a>Znane problemy i rozwiązywanie problemów w usłudze Azure Machine Learning

Ten artykuł pomaga w rozwiązywaniu znanych problemów, które mogą wystąpić podczas korzystania z Azure Machine Learning. 

Aby uzyskać więcej informacji na temat rozwiązywania problemów, zobacz [następne kroki](#next-steps) na końcu tego artykułu.

> [!TIP]
> Błędy lub inne problemy mogą wynikać z [przydziałów zasobów](how-to-manage-quotas.md) , które można napotkać podczas pracy z Azure Machine Learning. 

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

Czasami pomocne może być podanie informacji diagnostycznych podczas pytania o pomoc. Aby wyświetlić niektóre dzienniki: 
1. Odwiedź [Azure Machine Learning Studio](https://ml.azure.com). 
1. Po lewej stronie wybierz pozycję **eksperyment** 
1. Wybierz eksperyment.
1. Wybierz przebieg.
1. W górnej części wybierz pozycję dane **wyjściowe + dzienniki**.

> [!NOTE]
> Azure Machine Learning rejestruje informacje z różnych źródeł podczas szkolenia, takie jak AutoML lub kontener platformy Docker, który uruchamia zadanie szkoleniowe. Wiele z tych dzienników nie jest udokumentowane. Jeśli wystąpią problemy i skontaktuje się z działem pomocy technicznej firmy Microsoft, mogą oni korzystać z tych dzienników podczas rozwiązywania problemów.


## <a name="installation-and-import"></a>Instalacja i importowanie
                           
* **Instalacja PIP: zależności nie są gwarantowane, aby były spójne z instalacją jednowierszową:** 

   Jest to znane ograniczenie typu PIP, ponieważ nie ma działającego programu rozpoznawania zależności podczas instalacji jako pojedynczej linii. Pierwsza unikatowa zależność jest tylko jednym z nich. 

   W poniższym kodzie `azureml-datadrift` i `azureml-train-automl` są instalowane przy użyciu jednowierszowej instalacji PIP. 
     ```
       pip install azureml-datadrift, azureml-train-automl
     ```
   Na potrzeby tego przykładu Załóżmy, że jest `azureml-datadrift` wymagana wersja > 1,0 i `azureml-train-automl` wymaga wersji < 1,2. Jeśli Najnowsza wersja programu `azureml-datadrift` to 1,3, oba pakiety zostaną uaktualnione do 1,3, niezależnie od `azureml-train-automl` wymagań pakietu dla starszej wersji. 

   Aby upewnić się, że odpowiednie wersje są zainstalowane dla pakietów, zainstaluj przy użyciu wielu wierszy, takich jak w poniższym kodzie. W tym miejscu nie jest to problem, ponieważ program PIP został jawnie obniżony jako część wywołania następnego wiersza. W związku z tym stosowane są odpowiednie zależności wersji.
    
     ```
        pip install azureml-datadrift
        pip install azureml-train-automl 
     ```
     
* **Nie ma gwarancji, że pakiet jest instalowany podczas instalowania programu Azure-pociąg-automl-Client:** 
   
   W przypadku uruchamiania zdalnego AutoML z włączonym wyjaśnieniem modelu zostanie wyświetlony komunikat o błędzie "Zainstaluj pakiet Azure-Wyjaśnij model dla wyjaśnień modelu". To jest znany problem. Jako obejście wykonaj jedną z poniższych czynności:
  
  1. Zainstaluj usługę Azure — Wyjaśnij model lokalnie.
   ```
      pip install azureml-explain-model
   ```
  2. Całkowicie wyłącz funkcję Wyjaśnij, przekazując model_explainability = false w konfiguracji AutoML.
   ```
      automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             model_explainability=False,
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)
    ``` 
    
* **Błędy Panda: zwykle widoczne podczas eksperymentu AutoML:**
   
   W przypadku ręcznego konfigurowania środowiska przy użyciu narzędzia PIP można zauważyć błędy atrybutów (zwłaszcza z Pandas) z powodu instalacji nieobsługiwanych wersji pakietu. Aby uniknąć takich błędów, [Zainstaluj zestaw AutoML SDK przy użyciu automl_setup. cmd](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md):
   
    1. Otwórz monit Anaconda i Sklonuj repozytorium GitHub dla zestawu przykładowych notesów.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. dysk CD do folderu How to-use-Azure/automat-Learning, w którym przykładowe notesy zostały wyodrębnione i następnie uruchomione:
    
    ```bash
    automl_setup
    ```
    
* **Błąd: "Mark" podczas uruchamiania AutoML na lokalnym klastrze obliczeniowym lub Azure Databricks**

    Jeśli nowe środowisko zostało utworzone po 10 czerwca 2020, przy użyciu zestawu SDK 1.7.0 lub starszego, szkolenie może zakończyć się niepowodzeniem z powodu aktualizacji pakietu cpuinfo. (W środowiskach utworzonych w dniu lub przed 10 czerwca 2020 nie ma to oddziaływać, ponieważ eksperymenty są uruchamiane w ramach obliczeń zdalnych, ponieważ są używane buforowane obrazy szkoleniowe). Aby obejść ten problem, wykonaj jedną z następujących czynności:
    
    * Zaktualizuj wersję zestawu SDK do wersji 1.8.0 lub nowszej (spowoduje to również obniżenie wersji z PR-cpuinfo do 5.0.0):
    
      ```bash
      pip install --upgrade azureml-sdk[automl]
      ```
    
    * Starsza wersja wersji pr-cpuinfo do 5.0.0:
    
      ```bash
      pip install py-cpuinfo==5.0.0
      ```
  
* **Komunikat o błędzie: nie można odinstalować "PyYAML"**

    Azure Machine Learning SDK dla języka Python: PyYAML jest `distutils` zainstalowanym projektem. W związku z tym nie można dokładnie określić, które pliki należą do niej, jeśli istnieje częściowe odinstalowanie. Aby kontynuować instalowanie zestawu SDK przy ignorowaniu tego błędu, użyj:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Wystąpił błąd instalacji zestawu SDK Azure Machine Learning z powodu wyjątku: ModuleNotFoundError: Brak modułu o nazwie "ruamel" lub "ImportError: No module o nazwie ruamel. YAML"**
   
   Ten problem występuje podczas instalacji zestawu SDK programu Azure Machine Learning dla języka Python na najnowszym PIP (>20.1.1) w środowisku podstawowym Conda dla wszystkich opublikowanych wersji zestawu SDK Azure Machine Learning dla języka Python. Zapoznaj się z następującymi obejściami:

    * Unikaj instalowania zestawu SDK języka Python w środowisku podstawowym Conda, zamiast tworzyć środowisko Conda i instalować zestaw SDK na nowo utworzonym środowisku użytkownika. Najnowsza wersja PIP powinna współpracować z nowym środowiskiem Conda.

    * W przypadku tworzenia obrazów w platformie Docker, w której nie można przełączać się w środowisku Conda Base, przypinaj polecenie PIP<= 20.1.1 w pliku Docker.

    ```Python
    conda install -c r -y conda python=3.6.2 pip=20.1.1
    ```
    
* **Wystąpił błąd podczas instalowania pakietów**

    Azure Machine Learning Instalacja zestawu SDK kończy się niepowodzeniem na Azure Databricks po zainstalowaniu większej liczby pakietów. Niektóre pakiety, takie jak `psutil` , mogą spowodować konflikty. Aby uniknąć błędów instalacji, należy zainstalować pakiety przez zamarzanie wersji biblioteki. Ten problem jest związany z kostkami, a nie z zestawem SDK Azure Machine Learning. Ten problem może również wystąpić z innymi bibliotekami. Przykład:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Alternatywnie można użyć skryptów init w przypadku, gdy występują problemy z instalacją w języku Python. Takie podejście nie jest oficjalnie obsługiwane. Aby uzyskać więcej informacji, zobacz [skrypty init z zakresem klastra](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

* **Błąd importowania danych: nie można zaimportować nazwy `Timedelta` z `pandas._libs.tslibs`** : Jeśli ten błąd wystąpi podczas korzystania z zautomatyzowanej usługi Machine Learning, Uruchom dwa następujące wiersze w notesie:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Błąd importowania datakostki: Brak modułu o nazwie "Pandas. Core. indexes"** : Jeśli ten błąd wystąpi podczas korzystania z zautomatyzowanej uczenia maszynowego:

    1. Uruchom to polecenie, aby zainstalować dwa pakiety w klastrze Azure Databricks:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Odłącz i ponownie Dołącz klaster do notesu.
    
    Jeśli te kroki nie rozwiążą problemu, spróbuj ponownie uruchomić klaster.

* **DataFailToSendFeathers** : Jeśli podczas `FailToSendFeather` odczytywania danych w klastrze Azure Databricks wystąpi błąd, zapoznaj się z następującymi rozwiązaniami:
    
    * Uaktualnij `azureml-sdk[automl]` pakiet do najnowszej wersji.
    * Dodaj `azureml-dataprep` wersję 1.1.8 lub nowszą.
    * Dodaj `pyarrow` wersję 0,11 lub nowszą.
    
## <a name="create-and-manage-workspaces"></a>Tworzenie obszarów roboczych i zarządzanie nimi

> [!WARNING]
> Przeniesienie obszaru roboczego Azure Machine Learning do innej subskrypcji lub przeniesienie subskrypcji będącej właścicielem do nowej dzierżawy nie jest obsługiwane. Wykonanie tej operacji może spowodować błędy.

* **Azure Portal** : 
  * Jeśli przejdziesz bezpośrednio do obszaru roboczego z linku udostępniania z zestawu SDK lub Azure Portal, nie możesz wyświetlić standardowej strony **przeglądu** , która zawiera informacje o subskrypcji w rozszerzeniu. W tym scenariuszu nie można również przełączyć się do innego obszaru roboczego. Aby wyświetlić inny obszar roboczy, przejdź bezpośrednio do [Azure Machine Learning Studio](https://ml.azure.com) i wyszukaj nazwę obszaru roboczego.
  * Wszystkie elementy zawartości (zestawy danych, eksperymenty, obliczenia itd.) są dostępne tylko w programie [Azure Machine Learning Studio](https://ml.azure.com). *Nie* są one dostępne w Azure Portal.

* **Obsługiwane przeglądarki w portalu internetowym programu Azure Machine Learning Studio** : zalecamy użycie najnowszej przeglądarki zgodnej z systemem operacyjnym. Obsługiwane są następujące przeglądarki:
  * Microsoft Edge (Nowa Microsoft Edge, Najnowsza wersja. Nie w starszej wersji programu Microsoft Edge)
  * Safari (najnowsza wersja, tylko Mac)
  * Chrome (najnowsza wersja)
  * Firefox (najnowsza wersja)

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

* **Problem z tworzeniem AmlCompute** : istnieje rzadka szansa, że niektórzy użytkownicy, którzy utworzyli swój Azure Machine Learningy obszar roboczy z Azure Portal przed wydaniem GA, nie będą mogli tworzyć AmlCompute w tym obszarze roboczym. Możesz zgłosić żądanie obsługi do usługi lub utworzyć nowy obszar roboczy za pomocą portalu lub zestawu SDK, aby natychmiast odblokować.

* **Azure Container Registry nie obsługuje obecnie znaków Unicode w nazwach grup zasobów** : istnieje możliwość, że żądania ACR kończą się niepowodzeniem, ponieważ nazwa grupy zasobów zawiera znaki Unicode. Aby wyeliminować ten problem, zalecamy utworzenie ACR w grupie zasobów o innej nazwie.

## <a name="work-with-data"></a>Praca z danymi

### <a name="overloaded-azurefile-storage"></a>Przeciążony magazyn AzureFile

Jeśli wystąpi błąd `Unable to upload project files to working directory in AzureFile because the storage is overloaded` , zastosuj następujące obejścia.

Jeśli używasz udziału plików dla innych obciążeń, takich jak transfer danych, zalecenie polega na użyciu obiektów blob, dzięki czemu udział plików jest bezpłatny do użycia na potrzeby przesyłania przebiegów. Obciążenie można także podzielić między dwa różne obszary robocze.

### <a name="passing-data-as-input"></a>Przekazywanie danych jako danych wejściowych

*  **TypeError: FileNotFound: Brak pliku lub katalogu** : ten błąd występuje, gdy podano ścieżkę do pliku, w którym znajduje się plik. Należy upewnić się, że sposób odwoływania się do pliku jest zgodny z miejscem, w którym został zainstalowany zestaw danych na obiekcie docelowym obliczeń. Aby zapewnić jednoznaczny stan, zalecamy użycie ścieżki abstrakcyjnej podczas instalowania zestawu danych do obiektu docelowego obliczeń. Na przykład, w poniższym kodzie instalujemy zestaw danych w katalogu głównym systemu plików obiektu docelowego obliczeń `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Jeśli nie dołączysz wiodącego ukośnika "/", musisz utworzyć prefiks katalogu roboczego, np. `/mnt/batch/.../tmp/dataset` w elemencie docelowym obliczeń, aby wskazać, gdzie ma być zainstalowany zestaw danych.

### <a name="mount-dataset"></a>Zainstaluj zestaw danych
* **Nie można zainicjować zestawu danych: upłynął limit czasu oczekiwania na gotowość punktu instalacji** : aby `azureml-sdk >=1.12.0` rozwiązać ten problem, dodano logikę ponownej próby. Jeśli korzystasz z wcześniejszych wersji zestawu Azure SDK, przeprowadź uaktualnienie do najnowszej wersji. Jeśli jesteś już w programie `azureml-sdk>=1.12.0` , Utwórz ponownie środowisko, aby dysponować najnowszą poprawką.

### <a name="data-labeling-projects"></a>Projekty etykietowania danych

|Problem  |Rozwiązanie  |
|---------|---------|
|Można używać tylko zestawów danych utworzonych w magazynach danych obiektów BLOB.     |  Jest to znane ograniczenie bieżącej wersji.       |
|Po utworzeniu projekt pokazuje "Inicjowanie" przez długi czas.     | Ręcznie Odśwież stronę. Inicjalizacja powinna być w przybliżeniu 20 punktów, na sekundę. Brak autoodświeżania to znany problem.         |
|Podczas recenzowania obrazów nie są wyświetlane nowe obrazy z etykietami.     |   Aby załadować wszystkie obrazy z etykietami, wybierz **pierwszy** przycisk. **Pierwszy** przycisk przeprowadzi Cię z powrotem do początku listy, ale ładuje wszystkie dane z etykietami.      |
|Naciśnięcie klawisza Esc podczas etykietowania dla wykrywania obiektów tworzy etykietę o zerowej wielkości w lewym górnym rogu. Przesyłanie etykiet w tym stanie nie powiodło się.     |   Usuń etykietę, klikając znak krzyżyka obok niego.  |

### <a name="data-drift-monitors"></a><a name="data-drift"></a> Monitory dryfowania danych

Ograniczenia i znane problemy dotyczące monitorów dryfowania danych:

* Zakres czasu podczas analizowania danych historycznych jest ograniczony do 31 interwałów ustawienia częstotliwości monitora. 
* Ograniczenie funkcji 200, chyba że lista funkcji nie zostanie określona (wszystkie używane funkcje).
* Rozmiar obliczeń musi być wystarczająco duży, aby można było obsłużyć dane.
* Upewnij się, że zestaw danych zawiera dane w dniu rozpoczęcia i zakończenia danego monitora.
* Monitory zestawu danych będą działały tylko w zestawach, które zawierają 50 wierszy lub więcej.
* Kolumny lub funkcje w zestawie danych są klasyfikowane jako kategorii lub liczbowe w zależności od warunków w poniższej tabeli. Jeśli funkcja nie spełnia tych warunków — na przykład kolumna typu ciąg z >100 unikatowymi wartościami — funkcja zostanie porzucona z algorytmu dryfowania danych, ale jest nadal profilowana. 

    | Typ funkcji | Typ danych | Warunek | Ograniczenia | 
    | ------------ | --------- | --------- | ----------- |
    | Podzielone na kategorie | String, bool, int, float | Liczba unikatowych wartości w funkcji jest mniejsza niż 100 i mniejsza niż 5% liczby wierszy. | Wartość null jest traktowana jako jej własna Kategoria. | 
    | Porządkow | int, float | Wartości w funkcji mają typ danych liczbowych i nie spełniają warunku funkcji kategorii. | Funkcja została porzucona, jeśli >15% wartości ma wartość null. | 

* Po [utworzeniu monitora dryfowania danych](how-to-monitor-datasets.md) , ale nie na stronie **Monitory zestawu danych** w programie Azure Machine Learning Studio, spróbuj wykonać poniższe czynności.

    1. Sprawdź, czy w górnej części strony wybrano prawy zakres dat.  
    1. Na karcie **Monitory zestawu danych** wybierz łącze eksperyment, aby sprawdzić stan uruchomienia.  Ten link znajduje się po prawej stronie tabeli.
    1. Jeśli uruchomienie zakończyło się pomyślnie, Sprawdź dzienniki sterowników, aby zobaczyć, ile metryk zostało wygenerowanych, lub jeśli istnieją komunikaty ostrzegawcze.  Na karcie **dane wyjściowe i dzienniki** Znajdź dzienniki sterowników po kliknięciu eksperymentu.

* Jeśli funkcja SDK nie `backfill()` generuje oczekiwanych danych wyjściowych, może to być spowodowane problemem z uwierzytelnianiem.  Podczas tworzenia obliczeń do przekazania do tej funkcji nie należy używać `Run.get_context().experiment.workspace.compute_targets` .  Zamiast tego należy użyć [ServicePrincipalAuthentication](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?preserve-view=true&view=azure-ml-py) , takich jak następujące, aby utworzyć obliczenia, które są przekazywane do tej `backfill()` funkcji: 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group"xxx")
   compute = ws.compute_targets.get("xxx")
   ```

## <a name="azure-machine-learning-designer"></a>Projektant usługi Azure Machine Learning

* **Długi czas przygotowania obliczeń:**

Może to potrwać kilka minut, a nawet dłużej przy pierwszym połączeniu z lub utworzyć obiekt docelowy obliczeń. 

Z modułu zbierającego dane modelu może upłynąć do (ale zazwyczaj mniej niż) 10 minut na dostarczenie danych na koncie usługi BLOB Storage. Poczekaj 10 minut, aby upewnić się, że poniższe komórki zostaną uruchomione.

```python
import time
time.sleep(600)
```

* **Rejestruj punkty końcowe w czasie rzeczywistym:**

Dzienniki punktów końcowych w czasie rzeczywistym to dane klientów. W przypadku rozwiązywania problemów z punktem końcowym w czasie rzeczywistym można użyć poniższego kodu, aby włączyć dzienniki. 

Zobacz więcej szczegółów na temat monitorowania punktów końcowych usługi sieci Web w [tym artykule](./how-to-enable-app-insights.md#query-logs-for-deployed-models).

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```
Jeśli masz wiele dzierżawców, może być konieczne dodanie następującego kodu uwierzytelniania przed `ws = Workspace.from_config()`

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

## <a name="train-models"></a>Szkolenie modeli

* **ModuleErrors (Brak modułu o nazwie)** : Jeśli korzystasz z programu ModuleErrors podczas przesyłania eksperymentów na platformie Azure ml, oznacza to, że skrypt szkoleniowy oczekuje na zainstalowanie pakietu, ale nie został dodany. Po podaniu nazwy pakietu usługa Azure ML instaluje pakiet w środowisku używanym do pracy z szkoleniiem. 

    Jeśli używasz szacowania do przesyłania eksperymentów, możesz określić nazwę pakietu za pośrednictwem `pip_packages` lub `conda_packages` parametru w szacowania, na podstawie którego źródła chcesz zainstalować pakiet. Można również określić plik yml ze wszystkimi zależnościami przy użyciu `conda_dependencies_file` lub wyświetlić wszystkie wymagania dotyczące PIP w pliku txt przy użyciu `pip_requirements_file` parametru. Jeśli masz własny obiekt środowiska usługi Azure ML, który chcesz przesłonić domyślny obraz używany przez szacowania, możesz określić to środowisko za pośrednictwem `environment` parametru konstruktora szacowania.

    Platforma Azure ML udostępnia również specyficzne dla platformy szacowania dla TensorFlow, PyTorch, łańcucha i skryptu sklearn. Przy użyciu tych szacowania upewnij się, że podstawowe zależności programu są zainstalowane w Twoim imieniu w środowisku używanym do uczenia się. Istnieje możliwość określenia dodatkowych zależności, jak opisano powyżej. 
 
    Platforma Azure ML obsługuje obrazy platformy Docker i ich zawartość można zobaczyć w [kontenerach usługi Azure](https://github.com/Azure/AzureML-Containers).
    Zależności specyficzne dla platformy są wymienione w odpowiedniej strukturze dokumentacji programu — [łańcucha](/python/api/azureml-train-core/azureml.train.dnn.chainer?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks), [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks), [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks), [skryptu sklearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks).

    > [!Note]
    > Jeśli uważasz, że określony pakiet jest wystarczająco powszechny do dodania do obrazów i środowisk konserwowanych platformy Azure, zgłoś problem w usłudze GitHub w [kontenerach usługi Azure](https://github.com/Azure/AzureML-Containers)ml. 
 
* **NameError (nazwa niezdefiniowana), AttributeError (obiekt nie ma atrybutu)** : ten wyjątek powinien pochodzić ze skryptów szkoleniowych. Można przyjrzeć się plikom dziennika z Azure Portal, aby uzyskać więcej informacji na temat konkretnej nazwy niezdefiniowanej lub błędu atrybutu. Korzystając z zestawu SDK, można `run.get_details()` sprawdzić komunikat o błędzie. Spowoduje to wyświetlenie listy wszystkich plików dziennika wygenerowanych dla danego przebiegu. Upewnij się, że zapoznaj się z skryptem szkoleniowym i usuń błąd przed ponownym przesłaniem uruchomienia. 

* **Horovod został zamknięty** : w większości przypadków, jeśli wystąpi "AbortedError: Horovod zostało zamknięte" ten wyjątek oznacza, że wystąpił podstawowy wyjątek w jednym z procesów, które spowodowały zamknięcie Horovod. Każda ranga w zadaniu MPI ma dedykowany plik dziennika w usłudze Azure Machine Learning. Te dzienniki mają nazwę `70_driver_logs`. W przypadku trenowania rozproszonego nazwy dzienników mają sufiks `_rank`, ułatwiający ich odróżnienie. Aby znaleźć dokładny błąd, który spowodował zamknięcie Horovod, przejdź przez wszystkie pliki dziennika i Znajdź na `Traceback` końcu plików driver_log. Jeden z tych plików daje rzeczywisty wyjątek podstawowy. 

* **Usuwanie przebiegu lub eksperymentu** : eksperymenty można archiwizować przy użyciu metody [eksperyment. Archive](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truearchive--) lub w widoku karty eksperymenty w programie Azure Machine Learning Studio Client za pośrednictwem przycisku "Archiwizuj eksperyment". Ta akcja ukrywa eksperyment z zapytań i widoków list, ale nie usuwa go.

    Trwałe usuwanie pojedynczych eksperymentów lub przebiegów obecnie nie jest obsługiwane. Aby uzyskać więcej informacji na temat usuwania zasobów obszaru roboczego, zobacz [Eksportowanie lub usuwanie danych obszaru roboczego usługi Machine Learning](how-to-export-delete-data.md).

* **Dokument metryki jest zbyt duży** : Azure Machine Learning ma wewnętrzne limity rozmiaru obiektów metryk, które mogą być rejestrowane jednocześnie z poziomu przebiegu szkoleniowego. Jeśli w trakcie rejestrowania metryki z wartościami listy wystąpi błąd „Dokument metryki jest za duży”, spróbuj podzielić listę na mniejsze fragmenty, na przykład:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Wewnętrznie usługa Azure ML łączy bloki z tą samą nazwą metryki w listę ciągłą.

## <a name="automated-machine-learning"></a>Zautomatyzowane uczenie maszynowe

* **Ostatnie uaktualnienie zależności AutoML do nowszych wersji spowoduje uszkodzenie zgodności** : w przypadku wersji 1.13.0 zestawu SDK modele nie będą ładowane w starszych zestawach SDK ze względu na niezgodność między starszymi wersjami przypiętymi w naszych poprzednich pakietach i nowszymi wersjami. Zobaczysz błąd, taki jak:
  * Nie znaleziono modułu: ex. `No module named 'sklearn.decomposition._truncated_svd` ,
  * Błędy importowania: ex. `ImportError: cannot import name 'RollingOriginValidator'` ,
  * Błędy atrybutów: ex. `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`
  
  Aby obejść ten problem, wykonaj jedną z następujących czynności, w zależności od wersji szkoleniowej zestawu AutoML SDK:
  1. Jeśli wersja szkoleniowa zestawu AutoML SDK jest nowsza niż 1.13.0, potrzebne są `pandas == 0.25.1` i `sckit-learn==0.22.1` . Jeśli występuje niezgodność wersji, Uaktualnij scikit-Dowiedz się i/lub Pandas, aby uzyskać poprawną wersję, jak pokazano poniżej:
  
  ```bash
     pip install --upgrade pandas==0.25.1
     pip install --upgrade scikit-learn==0.22.1
  ```
  
  2. Jeśli wersja szkoleniowa zestawu AutoML SDK jest mniejsza lub równa 1.12.0, potrzebne są `pandas == 0.23.4` i `sckit-learn==0.20.3` . Jeśli występuje niezgodność wersji, scikit obniżanie poziomu i/lub Pandas do prawidłowej wersji, jak pokazano poniżej:
  
  ```bash
    pip install --upgrade pandas==0.23.4
    pip install --upgrade scikit-learn==0.20.3
  ```
 
* **Ocena prognozy R2 jest zawsze zerowa** : ten problem występuje, jeśli dostarczone dane szkoleniowe mają serię czasową, która zawiera tę samą wartość dla ostatnich `n_cv_splits`  +  `forecasting_horizon` punktów danych. Jeśli ten wzorzec jest oczekiwany w szeregach czasowych, można przełączyć podstawową metrykę na znormalizowany błąd średnika głównego.
 
* **TensorFlow** : w wersji 1.5.0 zestawu SDK automatyczne Uczenie maszynowe nie domyślnie instaluje modeli TensorFlow. Aby zainstalować TensorFlow i używać go z zautomatyzowanymi eksperymentami ML, zainstaluj TensorFlow = = 1.12.0 za pośrednictwem CondaDependecies. 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```
* **Wykresy eksperymentowe** : binarne wykresy klasyfikacji (precyzja-odwoływanie, Roc, krzywa zysku itp.) pokazana w zautomatyzowanych iteracjach eksperymentów z badaniami nie jest prawidłowo renderowana w interfejsie użytkownika od 4/12. Wykresy wykresów są obecnie wyświetlane z wynikami odwrotnymi, gdzie lepsze są modele z niższymi wynikami. Zbadano rozwiązanie.

* W obszarze **datakostki można anulować automatyczne uruchamianie uczenia maszynowego** : w przypadku korzystania z funkcji automatycznego uczenia maszynowego na Azure Databricks, aby anulować uruchomienie i rozpocząć nowe uruchomienie eksperymentu, uruchom ponownie klaster Azure Databricks.

* **Datakosteks >10 iteracji dla automatycznej uczenia maszynowego** : w oknie ustawienia automatycznej uczenia maszynowego, jeśli masz więcej niż 10 iteracji, ustaw wartość `show_output` `False` podczas przesyłania przebiegu.

* **Widżet datakosteks dla zestawu sdk Azure Machine Learning i automatycznej uczenia maszynowego** : widżet zestawu Azure Machine Learning SDK nie jest obsługiwany w notesie datacegły, ponieważ notesy nie mogą analizować widżetów html. Widżet można wyświetlić w portalu przy użyciu tego kodu w języku Python w komórce notesu Azure Databricks:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **Niepowodzenie automl_setup** : 
    * W systemie Windows uruchom automl_setup z poziomu wiersza polecenia Anaconda. Użyj tego linku, aby [zainstalować Miniconda](https://docs.conda.io/en/latest/miniconda.html).
    * Upewnij się, że Conda 64-bit jest zainstalowany, a nie 32-bit, uruchamiając `conda info` polecenie. `platform`Powinien być `win-64` dla systemu Windows lub `osx-64` dla komputerów Mac.
    * Upewnij się, że zainstalowano Conda 4.4.10 lub nowszą. Możesz sprawdzić wersję za pomocą polecenia `conda -V` . Jeśli masz zainstalowaną poprzednią wersję, możesz ją zaktualizować za pomocą polecenia: `conda update conda` .
    * System `gcc: error trying to exec 'cc1plus'`
      *  Jeśli wystąpi `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` błąd, zainstaluj program Build Essentials przy użyciu polecenia `sudo apt-get install build-essential` .
      * Przekaż nową nazwę jako pierwszy parametr do automl_setup, aby utworzyć nowe środowisko Conda. Wyświetlanie istniejących środowisk Conda `conda env list` i usuwanie ich z programu `conda env remove -n <environmentname>` .
      
* **automl_setup_linux. sh nie powiodło się** : Jeśli automl_setup_linus. sh kończy się niepowodzeniem na Ubuntu Linux z powodu błędu: `unable to execute 'gcc': No such file or directory`-
  1. Upewnij się, że porty wychodzące 53 i 80 są włączone. Na maszynie wirtualnej platformy Azure możesz wykonać tę czynność z poziomu Azure Portal, wybierając maszynę wirtualną, a następnie klikając pozycję Sieć.
  2. Uruchom polecenie: `sudo apt-get update`
  3. Uruchom polecenie: `sudo apt-get install build-essential --fix-missing`
  4. Uruchom `automl_setup_linux.sh` ponownie

* **Konfiguracja. ipynb kończy się niepowodzeniem** :
  * W przypadku lokalnego Conda upewnij się, że automl_setup został pomyślnie uruchomiony.
  * Upewnij się, że subscription_ida jest poprawna. Znajdź subscription_id w Azure Portal, wybierając pozycję Wszystkie usługi, a następnie pozycję subskrypcje. Znaki "<" i ">" nie powinny być uwzględnione w wartości subscription_id. Na przykład `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` ma prawidłowy format.
  * Upewnij się, że współautor lub właściciel ma dostęp do subskrypcji.
  * Sprawdź, czy region jest jednym z obsługiwanych regionów:,,,,,, `eastus2` `eastus` `westcentralus` `southeastasia` `westeurope` `australiaeast` `westus2` , `southcentralus` .
  * Zapewnij dostęp do regionu przy użyciu Azure Portal.
  
* **Importowanie AutoMLConfig nie powiodło się** : wprowadzono zmiany pakietu w zautomatyzowanej usłudze Machine Learning w wersji 1.0.76, które wymagają odinstalowania poprzedniej wersji przed zaktualizowaniem do nowej wersji. Jeśli `ImportError: cannot import name AutoMLConfig` napotkasz po uaktualnieniu z wersji zestawu SDK przed v 1.0.76 do v 1.0.76 lub nowszej, usuń błąd, uruchamiając polecenie: `pip uninstall azureml-train automl` , a następnie `pip install azureml-train-auotml` . Skrypt automl_setup. cmd robi to automatycznie. 

* **Workspace.from_config nie powiodło się** : Jeśli wywołania ws = Workspace.from_config () "zakończą się niepowodzeniem —
  1. Upewnij się, że Notes Configuration. ipynb został uruchomiony pomyślnie.
  2. Jeśli Notes jest uruchamiany z folderu, który nie znajduje się w folderze, w którym `configuration.ipynb` został uruchomiony, skopiuj folder aml_config a plik config.js, który zawiera do nowego folderu. Workspace.from_config odczytuje config.jsna potrzeby folderu notesu lub jego folderu nadrzędnego.
  3. Jeśli jest używana nowa subskrypcja, Grupa zasobów, obszar roboczy lub region, pamiętaj, aby `configuration.ipynb` ponownie uruchomić Notes. Zmiana config.jsna bezpośrednio będzie działała tylko wtedy, gdy obszar roboczy już istnieje w grupie zasobów określonej w ramach określonej subskrypcji.
  4. Jeśli chcesz zmienić region, Zmień obszar roboczy, grupę zasobów lub subskrypcję. `Workspace.create` Program nie utworzy ani nie zaktualizuje obszaru roboczego, jeśli już istnieje, nawet jeśli określony region jest inny.
  
* **Przykładowy Notes kończy się niepowodzeniem** : w przypadku niepowodzenia przykładowego notesu Wystąpił błąd, że właściwość, metoda lub biblioteka nie istnieje:
  * Upewnij się, że wybrano poprawne jądro w notesie Jupyter. Jądro jest wyświetlane w prawym górnym rogu strony Notes. Wartość domyślna to azure_automl. Należy zauważyć, że jądro jest zapisywany jako część notesu. Dlatego w przypadku przełączenia do nowego środowiska Conda należy wybrać nowe jądro w notesie.
      * W przypadku Azure Notebooks powinna być to Python 3,6. 
      * W przypadku lokalnych środowisk Conda powinna to być nazwa środowiska Conda określona w automl_setup.
  * Upewnij się, że Notes dotyczy używanej wersji zestawu SDK. Możesz sprawdzić wersję zestawu SDK, wykonując ją `azureml.core.VERSION` w komórce notesu Jupyter. Możesz pobrać poprzednią wersję przykładowych notesów z usługi GitHub, klikając `Branch` przycisk, wybierając `Tags` kartę, a następnie wybierając wersję.

* **Importowanie numpy kończy się niepowodzeniem w systemie Windows** : niektóre środowiska systemu Windows zapoznają się z błędem ładowania numpy z najnowszą wersją języka Python 3.6.8. Jeśli widzisz ten problem, Wypróbuj wersję Python 3.6.7.

* **Numpy importowania nie powiodła się** : Sprawdź wersję TensorFlow w zautomatyzowanym środowisku Conda ml. Obsługiwane wersje to < 1,13. Odinstaluj TensorFlow ze środowiska, jeśli wersja jest >= 1,13 możesz sprawdzić wersję TensorFlow i odinstalować ją w następujący sposób:
  1. Uruchom powłokę poleceń, Aktywuj środowisko Conda, w którym są zainstalowane zautomatyzowane pakiety ml.
  2. Wprowadź `pip freeze` i Wyszukaj `tensorflow` , jeśli znaleziono, wyświetlana wersja powinna być < 1,13
  3. Jeśli wyświetlana wersja nie jest obsługiwaną wersją, `pip uninstall tensorflow` w powłoce poleceń i wprowadź y w celu potwierdzenia.

## <a name="deploy--serve-models"></a>Wdrażanie i obsługa modeli

Wykonaj następujące akcje dla następujących błędów:

|Błąd  | Rozwiązanie  |
|---------|---------|
|Niepowodzenie kompilowania obrazu podczas wdrażania usługi sieci Web     |  Dodaj "pynacl = = 1.2.1" jako zależność PIP do pliku Conda na potrzeby konfiguracji obrazu       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Zmień jednostkę SKU dla maszyn wirtualnych używanych we wdrożeniu na taką, która ma więcej pamięci. |
|Niepowodzenie FPGA     |  Nie będzie można wdrażać modeli w usłudze FPGA, dopóki nie zażądano i nie zatwierdzono do przydziału FPGA. Aby zażądać dostępu, Wypełnij formularz żądania limitu przydziału: https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Aktualizowanie składników Azure Machine Learning w klastrze AKS

Należy ręcznie zastosować aktualizacje Azure Machine Learning składników zainstalowanych w klastrze usługi Azure Kubernetes. 

Te aktualizacje można zastosować, odłączając klaster od obszaru roboczego Azure Machine Learning, a następnie dołączając ponownie klaster do obszaru roboczego. Jeśli w klastrze jest włączony protokół TLS, podczas ponownego dołączania klastra należy podać certyfikat TLS/SSL i klucz prywatny. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Jeśli nie masz już certyfikatu TLS/SSL i klucza prywatnego lub używasz certyfikatu wygenerowanego przez Azure Machine Learning, możesz pobrać pliki przed odłączeniem klastra, łącząc się z klastrem przy użyciu `kubectl` i pobierając klucz tajny `azuremlfessl` .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes przechowuje wpisy tajne w zakodowanym formacie Base-64. `cert.pem` `key.pem` Przed udostępnieniem tych elementów tajnych należy oprzeć na base-64 `attach_config.enable_ssl` . 

### <a name="detaching-azure-kubernetes-service"></a>Odłączanie usługi Azure Kubernetes

Korzystając z Azure Machine Learning Studio, SDK lub rozszerzenia interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning w celu odłączenia klastra AKS nie powoduje usunięcia klastra AKS. Aby usunąć klaster, zobacz [Korzystanie z interfejsu wiersza polecenia platformy Azure z AKS](../aks/kubernetes-walkthrough.md#delete-the-cluster).

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Awarie usług WebServices w usłudze Azure Kubernetes

Wiele błędów usług internetowych w usłudze Azure Kubernetes Service można debugować, łącząc się z klastrem przy użyciu narzędzia `kubectl`. Możesz uzyskać dostęp `kubeconfig.json` do klastra usługi Azure Kubernetes Service, uruchamiając

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>Błędy uwierzytelniania

W przypadku wykonywania operacji zarządzania na obiekcie docelowym obliczeń z zadania zdalnego zostanie wyświetlony jeden z następujących błędów: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Na przykład, jeśli spróbujesz utworzyć lub dołączyć obiekt docelowy obliczeń z potoku, który został przesłany do wykonania zdalnego, zostanie wyświetlony komunikat o błędzie.

## <a name="missing-user-interface-items-in-studio"></a>Brak elementów interfejsu użytkownika w programie Studio

Za pomocą kontroli dostępu opartej na rolach platformy Azure można ograniczyć akcje, które można wykonywać przy użyciu Azure Machine Learning. Ograniczenia te mogą uniemożliwić Wyświetlanie elementów interfejsu użytkownika w programie Azure Machine Learning Studio. Na przykład, jeśli przypisano rolę, która nie może utworzyć wystąpienia obliczeniowego, opcja tworzenia wystąpienia obliczeniowego nie będzie wyświetlana w Studio.

Aby uzyskać więcej informacji, zobacz [Zarządzanie użytkownikami i rolami](how-to-assign-roles.md).

## <a name="compute-cluster-wont-resize"></a>Nie można zmienić rozmiaru klastra obliczeniowego

Jeśli Azure Machine Learning klaster obliczeniowy ma zablokowany rozmiar (0 – > 0) dla stanu węzła, może to być spowodowane blokadami zasobów platformy Azure.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Następne kroki

Zobacz więcej artykułów do rozwiązywania problemów dotyczących Azure Machine Learning:

* [Rozwiązywanie problemów z wdrażaniem platformy Docker przy użyciu Azure Machine Learning](how-to-troubleshoot-deployment.md)
* [Debuguj potoki uczenia maszynowego](how-to-debug-pipelines.md)
* [Debugowanie klasy ParallelRunStep z zestawu SDK Azure Machine Learning](how-to-debug-parallel-run-step.md)
* [Interaktywne debugowanie wystąpienia obliczeniowego uczenia maszynowego przy użyciu VS Code](how-to-debug-visual-studio-code.md)
* [Używanie Application Insights do debugowania potoków uczenia maszynowego](./how-to-log-pipelines-application-insights.md)