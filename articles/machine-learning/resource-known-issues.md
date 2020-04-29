---
title: Znane problemy & Rozwiązywanie problemów
titleSuffix: Azure Machine Learning
description: Zapoznaj się z listą znanych problemów, obejść i rozwiązywania problemów dotyczących Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 58fd9225298b4322567f4feb02629e3ad4e0f00d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82127570"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Znane problemy i rozwiązywanie problemów Azure Machine Learning

Ten artykuł pomaga znaleźć i poprawić błędy lub błędy, które można napotkać podczas korzystania z Azure Machine Learning.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Czasami pomocne może być podanie informacji diagnostycznych podczas pytania o pomoc. Aby wyświetlić niektóre dzienniki: 
1. Odwiedź [Azure Machine Learning Studio](https://ml.azure.com). 
1. Po lewej stronie wybierz pozycję **eksperyment** 
1. Wybierz eksperyment.
1. Wybierz przebieg.
1. W górnej części wybierz pozycję dane **wyjściowe + dzienniki**.

> [!NOTE]
> Azure Machine Learning rejestruje informacje z różnych źródeł podczas szkolenia, takie jak AutoML lub kontener platformy Docker, który uruchamia zadanie szkoleniowe. Wiele z tych dzienników nie jest udokumentowane. Jeśli wystąpią problemy i skontaktuje się z działem pomocy technicznej firmy Microsoft, mogą oni korzystać z tych dzienników podczas rozwiązywania problemów.


## <a name="resource-quotas"></a>Limity przydziałów zasobów

Dowiedz się więcej na temat [przydziałów zasobów](how-to-manage-quotas.md) , które można napotkać podczas pracy z Azure Machine Learning.

## <a name="installation-and-import"></a>Instalacja i importowanie

* **Instalacja PIP: zależności nie są gwarantowane, aby były spójne z instalacją jednowierszową**: 

   Jest to znane ograniczenie typu PIP, ponieważ nie ma działającego programu rozpoznawania zależności podczas instalacji jako pojedynczej linii. Pierwsza unikatowa zależność jest tylko jednym z nich. 

   W poniższym kodzie `azure-ml-datadrift` i `azureml-train-automl` są one instalowane przy użyciu jednej linii instalacji PIP. 
     ```
       pip install azure-ml-datadrift, azureml-train-automl
     ```
   Na potrzeby tego przykładu Załóżmy `azure-ml-datadrift` , że jest wymagana wersja > `azureml-train-automl` 1,0 i wymaga wersji < 1,2. Jeśli Najnowsza wersja programu `azure-ml-datadrift` to 1,3, oba pakiety zostaną uaktualnione do 1,3, niezależnie od wymagań `azureml-train-automl` pakietu dla starszej wersji. 

   Aby upewnić się, że odpowiednie wersje są zainstalowane dla pakietów, zainstaluj przy użyciu wielu wierszy, takich jak w poniższym kodzie. W tym miejscu nie jest to problem, ponieważ program PIP został jawnie obniżony jako część wywołania następnego wiersza. W związku z tym stosowane są odpowiednie zależności wersji.
    
     ```
        pip install azure-ml-datadrift
        pip install azureml-train-automl 
     ```
     
* **Komunikat o błędzie: nie można odinstalować "PyYAML"**

    Azure Machine Learning SDK dla języka Python: PyYAML jest `distutils` zainstalowanym projektem. W związku z tym nie można dokładnie określić, które pliki należą do niej, jeśli istnieje częściowe odinstalowanie. Aby kontynuować instalowanie zestawu SDK przy ignorowaniu tego błędu, użyj:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Wystąpił błąd podczas instalowania pakietów**

    Azure Machine Learning Instalacja zestawu SDK kończy się niepowodzeniem na Azure Databricks po zainstalowaniu większej liczby pakietów. Niektóre pakiety, takie jak `psutil`, mogą spowodować konflikty. Aby uniknąć błędów instalacji, należy zainstalować pakiety przez zamarzanie wersji biblioteki. Ten problem jest związany z kostkami, a nie z zestawem SDK Azure Machine Learning. Ten problem może również wystąpić z innymi bibliotekami. Przykład:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Alternatywnie można użyć skryptów init w przypadku, gdy występują problemy z instalacją w języku Python. Takie podejście nie jest oficjalnie obsługiwane. Aby uzyskać więcej informacji, zobacz [skrypty init z zakresem klastra](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

* **Błąd importowania danych: nie można zaimportować nazwy "Timedelta" z "Pandas. _libs. tslibs"**: Jeśli ten błąd wystąpi podczas korzystania z automatycznego uczenia maszynowego, Uruchom dwa następujące wiersze w notesie:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Błąd importowania datakostki: Brak modułu o nazwie "Pandas. Core. indexes"**: Jeśli ten błąd wystąpi podczas korzystania z zautomatyzowanej uczenia maszynowego:

    1. Uruchom to polecenie, aby zainstalować dwa pakiety w klastrze Azure Databricks:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Odłącz i ponownie Dołącz klaster do notesu.
    
    Jeśli te kroki nie rozwiążą problemu, spróbuj ponownie uruchomić klaster.

* **DataFailToSendFeathers**: Jeśli podczas odczytywania danych `FailToSendFeather` w klastrze Azure Databricks wystąpi błąd, zapoznaj się z następującymi rozwiązaniami:
    
    * Uaktualnij `azureml-sdk[automl]` pakiet do najnowszej wersji.
    * Dodaj `azureml-dataprep` wersję 1.1.8 lub nowszą.
    * Dodaj `pyarrow` wersję 0,11 lub nowszą.
    
## <a name="create-and-manage-workspaces"></a>Tworzenie obszarów roboczych i zarządzanie nimi

> [!WARNING]
> Przeniesienie obszaru roboczego Azure Machine Learning do innej subskrypcji lub przeniesienie subskrypcji będącej właścicielem do nowej dzierżawy nie jest obsługiwane. Wykonanie tej operacji może spowodować błędy.

* **Azure Portal**: Jeśli przejdziesz bezpośrednio do wyświetlania obszaru roboczego z linku udostępniania z zestawu SDK lub portalu, nie będzie możliwe wyświetlenie zwykłej strony **przeglądu** z informacjami o subskrypcji w rozszerzeniu. Nie będzie też możliwe przełączenie do innego obszaru roboczego. Jeśli zachodzi potrzeba wyświetlenia innego obszaru roboczego, przejdź bezpośrednio do [Azure Machine Learning Studio](https://ml.azure.com) i wyszukaj nazwę obszaru roboczego.

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

* **Problem z tworzeniem AmlCompute**: istnieje rzadka szansa, że niektórzy użytkownicy, którzy utworzyli swój Azure Machine Learningy obszar roboczy z Azure Portal przed wydaniem GA, nie będą mogli tworzyć AmlCompute w tym obszarze roboczym. Możesz zgłosić żądanie obsługi do usługi lub utworzyć nowy obszar roboczy za pomocą portalu lub zestawu SDK, aby natychmiast odblokować.

## <a name="work-with-data"></a>Praca z danymi

### <a name="overloaded-azurefile-storage"></a>Przeciążony magazyn AzureFile

Jeśli wystąpi błąd `Unable to upload project files to working directory in AzureFile because the storage is overloaded`, zastosuj następujące obejścia.

Jeśli używasz udziału plików dla innych obciążeń, takich jak transfer danych, zalecenie polega na użyciu obiektów blob, dzięki czemu udział plików jest bezpłatny do użycia na potrzeby przesyłania przebiegów. Obciążenie można także podzielić między dwa różne obszary robocze.

### <a name="passing-data-as-input"></a>Przekazywanie danych jako danych wejściowych

*  **TypeError: FileNotFound: Brak pliku lub katalogu**: ten błąd występuje, gdy podano ścieżkę do pliku, w którym znajduje się plik. Należy upewnić się, że sposób odwoływania się do pliku jest zgodny z miejscem, w którym został zainstalowany zestaw danych na obiekcie docelowym obliczeń. Aby zapewnić jednoznaczny stan, zalecamy użycie ścieżki abstrakcyjnej podczas instalowania zestawu danych do obiektu docelowego obliczeń. Na przykład, w poniższym kodzie instalujemy zestaw danych w katalogu głównym systemu plików obiektu docelowego obliczeń `/tmp`. 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Jeśli nie dołączysz wiodącego ukośnika "/", musisz utworzyć prefiks katalogu roboczego, np. `/mnt/batch/.../tmp/dataset` w elemencie docelowym obliczeń, aby wskazać, gdzie ma być zainstalowany zestaw danych.

### <a name="data-labeling-projects"></a>Projekty etykietowania danych

|Problem  |Rozwiązanie  |
|---------|---------|
|Można używać tylko zestawów danych utworzonych w magazynach danych obiektów BLOB     |  jest to znane ograniczenie bieżącej wersji.       |
|Po utworzeniu projekt pokazuje "Inicjowanie" przez długi czas     | Ręcznie Odśwież stronę. Inicjalizacja powinna być w przybliżeniu 20 punktów, na sekundę. Brak autoodświeżania to znany problem.         |
|Podczas przeglądania obrazów nie są wyświetlane nowe obrazy z etykietami     |   Aby załadować wszystkie obrazy z etykietami, wybierz **pierwszy** przycisk. **Pierwszy** przycisk przeprowadzi Cię z powrotem do początku listy, ale ładuje wszystkie dane z etykietami.      |
|Naciśnięcie klawisza Esc podczas etykietowania dla wykrywania obiektów tworzy etykietę o zerowej wielkości w lewym górnym rogu. Przesyłanie etykiet w tym stanie nie powiodło się.     |   Usuń etykietę, klikając znak krzyżyka obok niego.  |

## <a name="azure-machine-learning-designer"></a>Projektant Azure Machine Learning

Znane problemy:

* **Długi czas przygotowania obliczeń**: może to potrwać kilka minut, a nawet dłużej przy pierwszym połączeniu z lub utworzyć obiekt docelowy obliczeń. 

## <a name="train-models"></a>Szkolenie modeli

* **ModuleErrors (Brak modułu o nazwie)**: Jeśli korzystasz z programu ModuleErrors podczas przesyłania eksperymentów na platformie Azure ml, oznacza to, że skrypt szkoleniowy oczekuje na zainstalowanie pakietu, ale nie został dodany. Po podaniu nazwy pakietu, usługa Azure ML zainstaluje pakiet w środowisku używanym na potrzeby danego przebiegu szkoleniowego. 

    Jeśli używasz [szacowania](concept-azure-machine-learning-architecture.md#estimators) do przesyłania eksperymentów, możesz określić nazwę pakietu za pośrednictwem `pip_packages` lub `conda_packages` parametru w szacowania, na podstawie którego źródła chcesz zainstalować pakiet. Można również określić plik yml ze wszystkimi zależnościami przy użyciu `conda_dependencies_file`lub wyświetlić wszystkie wymagania dotyczące PIP w pliku txt przy użyciu `pip_requirements_file` parametru. Jeśli masz własny obiekt środowiska usługi Azure ML, który chcesz przesłonić domyślny obraz używany przez szacowania, możesz określić to środowisko za pośrednictwem `environment` parametru konstruktora szacowania.

    Platforma Azure ML udostępnia również specyficzne dla platformy szacowania dla Tensorflow, PyTorch, łańcucha i skryptu sklearn. Przy użyciu tych szacowania upewnij się, że podstawowe zależności programu są zainstalowane w Twoim imieniu w środowisku używanym do uczenia się. Istnieje możliwość określenia dodatkowych zależności, jak opisano powyżej. 
 
    Platforma Azure ML obsługuje obrazy platformy Docker i ich zawartość można zobaczyć w [kontenerach usługi Azure](https://github.com/Azure/AzureML-Containers).
    Zależności specyficzne dla platformy są wymienione w odpowiedniej strukturze dokumentacji programu — [łańcucha](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [skryptu sklearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

    > [!Note]
    > Jeśli uważasz, że określony pakiet jest wystarczająco powszechny do dodania do obrazów i środowisk konserwowanych platformy Azure, zgłoś problem w usłudze GitHub w [kontenerach usługi Azure](https://github.com/Azure/AzureML-Containers)ml. 
 
* **NameError (nazwa niezdefiniowana), AttributeError (obiekt nie ma atrybutu)**: ten wyjątek powinien pochodzić ze skryptów szkoleniowych. Można przyjrzeć się plikom dziennika z Azure Portal, aby uzyskać więcej informacji na temat konkretnej nazwy niezdefiniowanej lub błędu atrybutu. Korzystając z zestawu SDK, można `run.get_details()` sprawdzić komunikat o błędzie. Spowoduje to wyświetlenie listy wszystkich plików dziennika wygenerowanych dla danego przebiegu. Upewnij się, że zapoznaj się z skryptem szkoleniowym i usuń błąd przed ponownym przesłaniem uruchomienia. 

* **Horovod został zamknięty**: w większości przypadków, jeśli wystąpi "AbortedError: Horovod zostało zamknięte" ten wyjątek oznacza, że wystąpił podstawowy wyjątek w jednym z procesów, które spowodowały zamknięcie Horovod. Każda ranga w zadaniu MPI pobiera własny dedykowany plik dziennika w usłudze Azure ML. Te dzienniki mają nazwę `70_driver_logs`. W przypadku szkolenia rozproszonego nazwy dzienników są sufiksem, `_rank` aby ułatwić odróżnienie dzienników. Aby znaleźć dokładny błąd, który spowodował zamknięcie Horovod, przejdź przez wszystkie pliki dziennika i Znajdź `Traceback` na końcu plików driver_log. Jeden z tych plików daje rzeczywisty wyjątek podstawowy. 

* **Usuwanie przebiegu lub eksperymentu**: eksperymenty można archiwizować przy użyciu metody [eksperyment. Archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) lub w widoku karty eksperymenty w programie Azure Machine Learning Studio Client za pośrednictwem przycisku "Archiwizuj eksperyment". Ta akcja ukrywa eksperyment z zapytań i widoków list, ale nie usuwa go.

    Trwałe usuwanie pojedynczych eksperymentów lub przebiegów nie jest obecnie obsługiwane. Aby uzyskać więcej informacji na temat usuwania zasobów obszaru roboczego, zobacz [Eksportowanie lub usuwanie danych obszaru roboczego usługi Machine Learning](how-to-export-delete-data.md).

* **Dokument metryki jest zbyt duży**: Azure Machine Learning ma wewnętrzne limity rozmiaru obiektów metryk, które mogą być rejestrowane jednocześnie z poziomu przebiegu szkoleniowego. Jeśli wystąpi błąd "dokument metryki jest zbyt duży" podczas rejestrowania metryki o wartościach listy, spróbuj podzielić listę na mniejsze fragmenty, na przykład:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Wewnętrznie usługa Azure ML łączy bloki z tą samą nazwą metryki na listę ciągłą.

## <a name="automated-machine-learning"></a>Zautomatyzowane uczenie maszynowe

* **Przepływ dwuosiowy**: automatyczne Uczenie maszynowe aktualnie nie obsługuje przepływu dwuosiowego w wersji 1,13. Zainstalowanie tej wersji spowoduje, że zależności pakietu przestaną działać. Pracujemy nad rozwiązaniem tego problemu w przyszłej wersji.

* **Wykresy eksperymentowe**: binarne wykresy klasyfikacji (precyzja-odwoływanie, Roc, krzywa zysku itp.) pokazana w zautomatyzowanych iteracjach eksperymentów z badaniami nie jest prawidłowo renderowana w interfejsie użytkownika od 4/12. Wykresy wykresów są obecnie wyświetlane z wynikami odwrotnymi, gdzie lepsze są modele z niższymi wynikami. Zbadano rozwiązanie.

* W obszarze **datakostki można anulować automatyczne uruchamianie uczenia maszynowego**: w przypadku korzystania z funkcji automatycznego uczenia maszynowego na Azure Databricks, aby anulować uruchomienie i rozpocząć nowe uruchomienie eksperymentu, uruchom ponownie klaster Azure Databricks.

* **Datakosteks >10 iteracji dla automatycznej uczenia maszynowego**: w oknie ustawienia automatycznej uczenia maszynowego, jeśli masz więcej niż 10 iteracji `show_output` , `False` ustaw wartość podczas przesyłania przebiegu.

* **Widżet datakosteks dla zestawu sdk Azure Machine Learning i automatycznej uczenia maszynowego**: widżet zestawu Azure Machine Learning SDK nie jest obsługiwany w notesie datacegły, ponieważ notesy nie mogą analizować widżetów html. Widżet można wyświetlić w portalu przy użyciu tego kodu w języku Python w komórce notesu Azure Databricks:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>Wdrażanie i obsługa modeli

Wykonaj następujące akcje dla następujących błędów:

|Error  | Rozwiązanie  |
|---------|---------|
|Niepowodzenie kompilowania obrazu podczas wdrażania usługi sieci Web     |  Dodaj "pynacl = = 1.2.1" jako zależność PIP do pliku Conda na potrzeby konfiguracji obrazu       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Zmień jednostkę SKU dla maszyn wirtualnych używanych we wdrożeniu na taką, która ma więcej pamięci. |
|Niepowodzenie FPGA     |  Nie będzie można wdrażać modeli w usłudze FPGA, dopóki nie zażądano i nie zatwierdzono do przydziału FPGA. Aby zażądać dostępu, Wypełnij formularz żądania limitu przydziału:https://aka.ms/aml-real-time-ai       |

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

Jeśli nie masz już certyfikatu TLS/SSL i klucza prywatnego lub używasz certyfikatu wygenerowanego przez Azure Machine Learning, możesz pobrać pliki przed odłączeniem klastra, łącząc się z klastrem przy użyciu `kubectl` i pobierając klucz tajny. `azuremlfessl`

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes przechowuje wpisy tajne w zakodowanym formacie Base-64. Przed udostępnieniem tych `cert.pem` `key.pem` elementów tajnych należy oprzeć na Base-64 `attach_config.enable_ssl`. 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Awarie usług WebServices w usłudze Azure Kubernetes

Wiele błędów sieci Web w usłudze Azure Kubernetes można debugować, łącząc się z klastrem przy użyciu `kubectl`programu. Możesz uzyskać dostęp `kubeconfig.json` do klastra usługi Azure Kubernetes Service, uruchamiając

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
