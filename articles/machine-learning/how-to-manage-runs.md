---
title: Uruchamianie, monitorowanie i anulowanie przebiegów szkoleniowych w języku Python
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchamiać, monitorować i śledzić eksperyment uczenia maszynowego za pomocą zestawu SDK języka Python Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 26880fd6e3688dd95cc9f16072a35d5c4ce7c31e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110274"
---
# <a name="start-monitor-and-track-run-history"></a>Uruchamianie, monitorowanie i śledzenie historii przebiegów 

[Zestaw Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/intro), [interfejsu wiersza polecenia Machine Learning](reference-azure-machine-learning-cli.md)i programu [Azure Machine Learning Studio](https://ml.azure.com) udostępnia różne metody monitorowania, organizowania i śledzenia przebiegów w celu uczenia się i eksperymentowania. Historia przebiegów w ML jest ważną częścią procesu deweloperskiego i powtarzalnego projektowania.

W tym artykule przedstawiono sposób wykonywania następujących zadań:

* Monitoruj wydajność uruchamiania.
* Monitoruj powiadomienie o stanie uruchomienia za pomocą wiadomości e-mail.
* Tagi i Znajdź uruchomienia.
* Dodaj opis uruchomienia. 
* Uruchom wyszukiwanie w historii uruchamiania. 
* Anulowanie lub niepowodzenie uruchomienia.
* Utwórz uruchomienia podrzędne.
 

> [!TIP]
> Jeśli szukasz informacji o monitorowaniu usługi Azure Machine Learning i skojarzonych usługach platformy Azure, zobacz [Jak monitorować Azure Machine Learning](monitor-azure-machine-learning.md).
> Jeśli szukasz informacji dotyczących modeli monitorowania wdrożonych jako usługi sieci Web lub moduły IoT Edge, zobacz [zbieranie danych modelu](how-to-enable-data-collection.md) i [monitorowanie z Application Insights](how-to-enable-app-insights.md).

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne będą następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md).

* Zestaw Azure Machine Learning SDK dla języka Python (wersja 1.0.21 lub nowsza). Aby zainstalować lub zaktualizować najnowszą wersję zestawu SDK, zobacz [Instalowanie lub aktualizowanie zestawu SDK](/python/api/overview/azure/ml/install).

    Aby sprawdzić wersję zestawu SDK Azure Machine Learning, użyj następującego kodu:

    ```python
    print(azureml.core.VERSION)
    ```

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/?preserve-view=true&view=azure-cli-latest) i [rozszerzenie interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md).


## <a name="monitor-run-performance"></a>Monitorowanie wydajności przebiegu

* Rozpocznij przebieg i proces rejestrowania

    # <a name="python"></a>[Python](#tab/python)
    
    1. Skonfiguruj eksperyment przez zaimportowanie klas [obszaru roboczego](/python/api/azureml-core/azureml.core.workspace.workspace), [eksperymentu](/python/api/azureml-core/azureml.core.experiment.experiment), [uruchamiania](/python/api/azureml-core/azureml.core.run%28class%29)i [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) z pakietu [Azure. Core](/python/api/azureml-core/azureml.core) .
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. Rozpocznij przebieg i proces rejestrowania przy użyciu [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) metody.
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    
    Aby rozpocząć wykonywanie eksperymentu, wykonaj następujące czynności:
    
    1. Z poziomu powłoki lub wiersza polecenia Użyj interfejsu wiersza poleceń platformy Azure do uwierzytelniania w ramach subskrypcji platformy Azure:
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. Dołącz konfigurację obszaru roboczego do folderu, który zawiera skrypt szkoleniowy. Zamień `myworkspace` na obszar roboczy Azure Machine Learning. Zamień `myresourcegroup` na grupę zasobów platformy Azure, która zawiera obszar roboczy:
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        To polecenie tworzy `.azureml` podkatalog zawierający przykładowe pliki środowiska runconfig i Conda. Zawiera również `config.json` plik, który jest używany do komunikowania się z obszarem roboczym Azure Machine Learning.
    
        Aby uzyskać więcej informacji, zobacz [AZ ml folder Attach](/cli/azure/ext/azure-cli-ml/ml/folder?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).
    
    2. Aby uruchomić przebieg, użyj następującego polecenia. Korzystając z tego polecenia, należy określić nazwę pliku runconfig (tekst przed \* . runconfig, Jeśli przeglądasz system plików) z parametrem-c.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > `az ml folder attach`Polecenie utworzyło `.azureml` podkatalog, który zawiera dwa przykładowe pliki runconfig.
        >
        > Jeśli masz skrypt języka Python, który programowo tworzy obiekt konfiguracji uruchomieniowej, możesz użyć [runconfig. Save ()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) , aby zapisać go jako plik runconfig.
        >
        > Więcej przykładowych plików runconfig można znaleźć w temacie [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/) .
    
        Aby uzyskać więcej informacji, zobacz [AZ ml Run Submit-Script](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

    # <a name="studio"></a>[Studio](#tab/azure-studio)

    Aby zapoznać się z przykładem szkoleń modelu w projektancie Azure Machine Learning, zobacz [Samouczek: przewidywanie ceny za samochód dla urządzeń przenośnych za pomocą projektanta](tutorial-designer-automobile-price-train-score.md).

    ---

* Monitorowanie stanu przebiegu

    # <a name="python"></a>[Python](#tab/python)
    
    * Pobierz stan uruchomienia za pomocą [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29#get-status--) metody.
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * Aby uzyskać identyfikator uruchomienia, czas wykonywania oraz inne szczegóły dotyczące przebiegu, użyj [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace#get-details--) metody.
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * Po pomyślnym zakończeniu przebiegu Użyj [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) metody, aby oznaczyć ją jako zakończoną.
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Jeśli używasz `with...as` wzorca projektowego Python, przebieg zostanie automatycznie oznaczony jako wykonany, gdy przebieg jest poza zakresem. Nie musisz ręcznie oznaczyć przebiegu jako zakończony.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    
    * Aby wyświetlić listę przebiegów eksperymentu, użyj następującego polecenia. Zamień na `experiment` nazwę eksperymentu:
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        To polecenie zwraca dokument JSON zawierający informacje o przebiegach dla tego eksperymentu.
    
        Aby uzyskać więcej informacji, zobacz [AZ ml eksperyment list](/cli/azure/ext/azure-cli-ml/ml/experiment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).
    
    * Aby wyświetlić informacje dotyczące określonego przebiegu, użyj następującego polecenia. Zamień na `runid` Identyfikator przebiegu:
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        To polecenie zwraca dokument JSON, który zawiera listę informacji o przebiegu.
    
        Aby uzyskać więcej informacji, zobacz [AZ ml Run show](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).
    
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Aby wyświetlić przebiegi w programie Studio: 
    
    1. Przejdź do karty **eksperymenty** .
    
    1. Wybierz **wszystkie eksperymenty** , aby wyświetlić wszystkie uruchomienia w eksperymentie, lub wybierz pozycję **wszystkie uruchomienia** , aby wyświetlić wszystkie uruchomienia przesłane w obszarze roboczym.
    
        Na stronie **wszystkie uruchomienia** można filtrować listę uruchamiania według tagów, eksperymentów, obiektów docelowych obliczeń i nie tylko w celu lepszego organizowania i określania zakresu pracy.  
    
    1. Wprowadź dostosowania na stronie, wybierając pozycję uruchomienia do porównania, dodając wykresy lub stosując filtry. Te zmiany można zapisać jako **widok niestandardowy** , aby można było łatwo wrócić do swojej pracy. Użytkownicy z uprawnieniami obszaru roboczego mogą edytować lub wyświetlać widok niestandardowy. Ponadto Udostępnij widok niestandardowy członkom zespołu w celu zwiększenia współpracy, wybierając pozycję **Udostępnij widok**.   
    
        :::image type="content" source="media/how-to-manage-runs/custom-views.gif" alt-text="Zrzut ekranu: Tworzenie widoku niestandardowego":::
    
    1. Aby wyświetlić dzienniki uruchamiania, wybierz konkretny przebieg i na karcie dane **wyjściowe + dzienniki** możesz znaleźć dzienniki diagnostyki i błędów dla przebiegu.
    
    ---

## <a name="monitor-the-run-status-by-email-notification"></a>Monitoruj powiadomienie o stanie uruchamiania według poczty e-mail

1. W [Azure Portal](https://ms.portal.azure.com/)na lewym pasku nawigacyjnym wybierz kartę **monitorowanie** . 

1. Wybierz pozycję **Ustawienia diagnostyczne** , a następnie wybierz pozycję **+ Dodaj ustawienie diagnostyczne**.

    ![Zrzut ekranu przedstawiający ustawienia diagnostyczne powiadomienia e-mail](./media/how-to-manage-runs/diagnostic-setting.png)

1. W ustawieniu diagnostycznym 
    1. w obszarze **szczegóły kategorii** wybierz pozycję **AmlRunStatusChangedEvent**. 
    1. W obszarze **szczegóły lokalizacji docelowej** wybierz **obszar roboczy Wyślij do log Analytics**  i określ **subskrypcję** i **log Analytics obszar roboczy**. 

    > [!NOTE]
    > **Obszar roboczy platformy azure log Analytics** jest innym typem zasobów platformy Azure niż **obszar roboczy usługi Azure Machine Learning**. Jeśli na liście nie ma żadnych opcji, możesz [utworzyć obszar roboczy log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace). 
    
    ![Miejsce zapisania powiadomienia e-mail](./media/how-to-manage-runs/log-location.png)

1. Na karcie **dzienniki** Dodaj **nową regułę alertu**. 

    ![Nowa reguła alertu](./media/how-to-manage-runs/new-alert-rule.png)

1. Zobacz [, jak tworzyć alerty dzienników i zarządzać nimi za pomocą Azure monitor](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-log).

## <a name="run-description"></a>Opis uruchomienia 

Opis uruchomienia można dodać do przebiegu, aby zapewnić więcej kontekstu i informacje o przebiegu. Możesz również wyszukać te opisy z listy uruchomień i dodać opis uruchomienia jako kolumnę na liście uruchomień. 

Przejdź do strony **szczegóły uruchamiania** dla przebiegu i wybierz ikonę Edytuj lub ołówka, aby dodać, edytować lub usunąć opisy dla przebiegu. Aby zachować zmiany na liście uruchomień, Zapisz zmiany w istniejącym widoku niestandardowym lub w nowym widoku niestandardowym. Format promocji jest obsługiwany w przypadku opisów uruchamiania, które umożliwiają osadzanie obrazów i głębokie łączenie, jak pokazano poniżej.

:::image type="content" source="media/how-to-manage-runs/run-description.gif" alt-text="Zrzut ekranu: Tworzenie opisu przebiegu"::: 

## <a name="tag-and-find-runs"></a>Tagi i Znajdź przebiegi

W Azure Machine Learning można użyć właściwości i tagów, aby ułatwić organizowanie i wykonywanie zapytań dotyczących przebiegów w celu uzyskania ważnych informacji.

* Dodaj właściwości i Tagi

    # <a name="python"></a>[Python](#tab/python)
    
    Aby dodać metadane z możliwością wyszukiwania do przebiegów, użyj [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29#add-properties-properties-) metody. Na przykład poniższy kod dodaje `"author"` Właściwość do przebiegu:
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    Właściwości są niezmienne, więc tworzą stałe rekordy do celów inspekcji. Poniższy przykład kodu powoduje błąd, ponieważ został już dodany `"azureml-user"` jako `"author"` wartość właściwości w poprzednim kodzie:
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    W przeciwieństwie do właściwości, Tagi są modyfikowalne. Aby dodać wyszukiwanie i istotne informacje dla klientów eksperymentu, użyj [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29#tag-key--value-none-) metody.
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    Możesz również dodać proste Tagi ciągu. Gdy Tagi są wyświetlane w słowniku tagów jako klucze, mają one wartość `None` .
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    
    > [!NOTE]
    > Za pomocą interfejsu wiersza polecenia można dodawać i aktualizować tylko Tagi.
    
    Aby dodać lub zaktualizować tag, użyj następującego polecenia:
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    Aby uzyskać więcej informacji, zobacz [AZ ml Run Update](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Możesz dodawać, edytować lub usuwać Tagi przebiegów z Studio. Przejdź do strony **szczegóły uruchamiania** dla przebiegu i wybierz ikonę Edytuj lub ołówka, aby dodać, edytować lub usunąć Tagi dla przebiegów. Możesz również wyszukiwać Tagi i filtrować je na stronie listy uruchamiania.
    
    :::image type="content" source="media/how-to-manage-runs/run-tags.gif" alt-text="Zrzut ekranu: Dodawanie, edytowanie lub usuwanie tagów przebiegu":::
    
    ---

* Właściwości zapytania i Tagi

    Możesz wykonywać zapytania w ramach eksperymentu, aby zwrócić listę przebiegów zgodnych z określonymi właściwościami i tagami.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    
    Interfejs wiersza polecenia platformy Azure obsługuje zapytania [JMESPath](http://jmespath.org) , które mogą służyć do filtrowania przebiegów w oparciu o właściwości i Tagi. Aby użyć zapytania JMESPath z interfejsem wiersza polecenia platformy Azure, określ go za pomocą `--query` parametru. Poniższe przykłady przedstawiają niektóre zapytania przy użyciu właściwości i tagów:
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Aby uzyskać więcej informacji na temat wykonywania zapytań dotyczących wyników interfejsu wiersza polecenia platformy Azure, zobacz temat [zapytanie dotyczące danych wyjściowych poleceń platformy Azure](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Aby wyszukać konkretne uruchomienia, przejdź do listy  **wszystkie uruchomienia** . Dostępne są dwie opcje:
    
    1. Użyj przycisku **Dodaj filtr** i wybierz pozycję Filtruj według tagów, aby odfiltrować uruchomienia przez tag, który został przypisany do przebiegów. <br><br>
    LUB
    
    1. Użyj paska wyszukiwania, aby szybko znaleźć przebiegi, wyszukując metadane uruchamiania, takie jak stan uruchomienia, opisy, nazwy eksperymentów i nazwisko osoby przesyłającej. 
    
## <a name="cancel-or-fail-runs"></a>Anulowanie lub niepowodzenie przebiegów

Jeśli zauważysz błąd lub jeśli wykonywanie przebiegu trwa zbyt długo, możesz anulować przebieg.

# <a name="python"></a>[Python](#tab/python)

Aby anulować przebieg przy użyciu zestawu SDK, użyj [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29#cancel--) metody:

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

Jeśli przebieg zostanie zakończony, ale zawiera błąd (na przykład użyto nieprawidłowego skryptu szkoleniowego), można użyć [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) metody, aby oznaczyć ją jako niepowodzenie.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby anulować uruchomienie przy użyciu interfejsu wiersza polecenia, należy użyć następujące polecenie. Zamień na `runid` Identyfikator przebiegu

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Aby uzyskać więcej informacji, zobacz [AZ ml Run Cancel](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Aby anulować uruchomienie w programie Studio, wykonaj następujące czynności:

1. Przejdź do działającego potoku w sekcji **eksperymenty** lub **potoki** . 

1. Wybierz numer uruchomienia potoku, który chcesz anulować.

1. Na pasku narzędzi wybierz pozycję **Anuluj** .

---

## <a name="create-child-runs"></a>Tworzenie przebiegów podrzędnych

Utwórz uruchomienia podrzędne, aby grupować powiązane z nimi przebiegi, na przykład dla różnych iteracji dostrajania parametrów.

> [!NOTE]
> Uruchomienia podrzędne można tworzyć tylko za pomocą zestawu SDK.

Ten przykład kodu używa `hello_with_children.py` skryptu do tworzenia partii pięciu przebiegów podrzędnych z poziomu przesłanego przebiegu przy użyciu [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29#child-run-name-none--run-id-none--outputs-none-) metody:

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Gdy przechodzą poza zakres, uruchomienia podrzędne są automatycznie oznaczane jako ukończone.

Aby wydajnie tworzyć wiele podrzędnych przebiegów, użyj [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) metody. Ze względu na to, że każde utworzenie powoduje wywołanie sieciowe, tworzenie partii przebiegów jest bardziej wydajne niż ich tworzenie.

### <a name="submit-child-runs"></a>Prześlij uruchomienia podrzędne

Uruchomienia podrzędne mogą być również przesyłane z przebiegu nadrzędnego. Pozwala to na tworzenie hierarchii uruchamiania obiektów nadrzędnych i podrzędnych. Nie można utworzyć przebiegu podrzędnego bez elementu nadrzędnego: nawet jeśli uruchomienie nadrzędne nie robi nic, ale uruchomienia podrzędnego, nadal trzeba utworzyć hierarchię. Stan wszystkich przebiegów jest niezależny: element nadrzędny może być w `"Completed"` stanie pomyślnym, nawet jeśli co najmniej jeden przebieg podrzędny został anulowany lub zakończył się niepowodzeniem.  

Możesz chcieć, aby dziecko uruchomiło inną konfigurację przebiegu niż uruchomienie nadrzędne. Na przykład można użyć mniej zaawansowanej konfiguracji opartej na procesorach CPU dla elementu nadrzędnego, a jednocześnie używać konfiguracji opartych na procesorze GPU dla elementów podrzędnych. Innym typowym życzeniem jest przekazanie każdego elementu podrzędnego różnych argumentów i danych. Aby dostosować uruchomienie podrzędne, Utwórz `ScriptRunConfig` obiekt dla uruchomienia podrzędnego. Poniższy kod:

- Pobiera zasób obliczeniowy o nazwie `"gpu-cluster"` z obszaru roboczego `ws`
- Wykonuje iterację przez różne wartości argumentu, aby można było przekazywać je do obiektów podrzędnych. `ScriptRunConfig`
- Tworzy i przesyła nowe uruchomienie podrzędne przy użyciu niestandardowego zasobu obliczeniowego i argumentu
- Bloki do momentu zakończenia wszystkich elementów podrzędnych

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

Aby utworzyć wiele podrzędnych przebiegów z identycznymi konfiguracjami, argumentami i danymi wejściowymi, użyj [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) metody. Ze względu na to, że każde utworzenie powoduje wywołanie sieciowe, tworzenie partii przebiegów jest bardziej wydajne niż ich tworzenie.

W ramach uruchomienia podrzędnego można wyświetlić identyfikator uruchomienia obiektu nadrzędnego:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Uruchomienia podrzędne zapytania

Aby zbadać podrzędne uruchomienia określonego elementu nadrzędnego, użyj [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) metody. ``recursive = True``Argument umożliwia wykonywanie zapytań do zagnieżdżonego drzewa elementów podrzędnych i podrzędne.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>Rejestruj do uruchomienia nadrzędnego lub głównego

Możesz użyć pola, `Run.parent` Aby uzyskać dostęp do uruchomienia, który uruchomił bieżące uruchomienie podrzędne. Typowym przypadkiem użycia `Run.parent` jest łączenie wyników dzienników w jednym miejscu. Należy zauważyć, że elementy podrzędne uruchamiają się asynchronicznie i nie ma gwarancji, że porządkowanie ani synchronizacja wykraczające poza zdolność elementu nadrzędnego do ukończenia jego działania podrzędnego.

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```

## <a name="example-notebooks"></a>Przykładowe notesy

W następujących notesach przedstawiono Koncepcje opisane w tym artykule:

* Aby dowiedzieć się więcej na temat interfejsów API rejestrowania, zobacz artykuł [Rejestrowanie interfejsu API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* Aby uzyskać więcej informacji na temat zarządzania przebiegami z zestawem SDK Azure Machine Learning, zobacz [Notes zarządzanie przebiegiem](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak rejestrować metryki dla eksperymentów, zobacz [Dziennik metryk podczas przebiegów szkoleniowych](how-to-track-experiments.md).
* Aby dowiedzieć się, jak monitorować zasoby i dzienniki z Azure Machine Learning, zobacz [Azure Machine Learning monitorowania](monitor-azure-machine-learning.md).
