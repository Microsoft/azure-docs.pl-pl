---
title: Śledzenie, monitorowanie i analizowanie przebiegów
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchamiać, monitorować i śledzić przebiegi eksperymentów uczenia maszynowego przy użyciu Azure Machine Learning SDK języka Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.date: 04/19/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 01c3dacc2754a3df6afb87b160ca2feea81807ad
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820443"
---
# <a name="start-monitor-and-track-run-history"></a>Uruchamianie, monitorowanie i śledzenie historii uruchamiania

Zestaw Azure Machine Learning SDK dla języka [Python,](/python/api/overview/azure/ml/intro) [interfejs Machine Learning](reference-azure-machine-learning-cli.md)wiersza polecenia i Azure Machine Learning studio [różne](https://ml.azure.com) metody monitorowania, organizowania i śledzenia przebiegów w celu trenowania i eksperymentowania. Historia przebiegów uczenia maszynowego jest ważną częścią objaśnianego i powtarzalnego procesu tworzenia uczenia maszynowego.

W tym artykule pokazano, jak wykonywać następujące zadania:

* Monitorowanie wydajności uruchamiania.
* Utwórz widok niestandardowy. 
* Dodaj opis uruchomienia. 
* Oznaczanie tagami i znajdowanie przebiegów.
* Uruchom wyszukiwanie w historii uruchamiania. 
* Anulowanie lub niepowodzenie przebiegów.
* Utwórz podrzędne przebiegi.
* Monitoruj stan uruchomienia za pomocą powiadomień e-mail.
 

> [!TIP]
> Jeśli szukasz informacji na temat monitorowania usługi Azure Machine Learning service skojarzonych usług platformy Azure, zobacz [Jak monitorować](monitor-azure-machine-learning.md)Azure Machine Learning .
> Jeśli szukasz informacji na temat modeli monitorowania wdrożonych jako usługi internetowe lub moduły IoT Edge, zobacz Temat Collect [model data](how-to-enable-data-collection.md) and Monitor with Application Insights (Zbieranie danych modelu i monitorowanie za pomocą [Application Insights).](how-to-enable-app-insights.md)

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne będą następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* Obszar [Azure Machine Learning roboczy](how-to-manage-workspace.md).

* Zestaw Azure Machine Learning SDK dla języka Python (wersja 1.0.21 lub nowsza). Aby zainstalować lub zaktualizować zestaw SDK do najnowszej wersji, zobacz [Instalowanie lub aktualizowanie zestawu SDK.](/python/api/overview/azure/ml/install)

    Aby sprawdzić wersję zestawu AZURE MACHINE LEARNING SDK, użyj następującego kodu:

    ```python
    print(azureml.core.VERSION)
    ```

* Interfejs [wiersza polecenia platformy Azure](/cli/azure/?preserve-view=true&view=azure-cli-latest) i rozszerzenie [interfejsu wiersza polecenia Azure Machine Learning](reference-azure-machine-learning-cli.md).


## <a name="monitor-run-performance"></a>Monitorowanie wydajności uruchamiania

* Uruchamianie uruchomienia i jego procesu rejestrowania

    # <a name="python"></a>[Python](#tab/python)
    
    1. Skonfiguruj eksperyment, importując klasy [Workspace](/python/api/azureml-core/azureml.core.workspace.workspace), [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment), [Run](/python/api/azureml-core/azureml.core.run%28class%29)i [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) z [pakietu azureml.core.](/python/api/azureml-core/azureml.core)
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. Uruchom przebieg i jego proces rejestrowania za pomocą [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) metody .
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    
    Aby rozpocząć przebieg eksperymentu, należy wykonać następujące czynności:
    
    1. W powłoki lub wierszu polecenia użyj interfejsu wiersza polecenia platformy Azure, aby uwierzytelnić się w subskrypcji platformy Azure:
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. Dołącz konfigurację obszaru roboczego do folderu zawierającego skrypt trenowania. Zastąp `myworkspace` Azure Machine Learning obszarem roboczym. Zastąp `myresourcegroup` grupę zasobów platformy Azure zawierającą obszar roboczy:
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        To polecenie tworzy `.azureml` podkatalog zawierający przykładowe pliki środowiska runconfig i conda. Zawiera on również `config.json` plik, który jest używany do komunikowania się z Azure Machine Learning roboczym.
    
        Aby uzyskać więcej informacji, zobacz [az ml folder attach](/cli/azure/ext/azure-cli-ml/ml/folder?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).
    
    2. Aby uruchomić uruchomienie, użyj następującego polecenia. W przypadku korzystania z tego polecenia określ nazwę pliku runconfig (tekst przed plikiem .runconfig, jeśli patrzysz na system plików) względem \* parametru -c.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > Polecenie `az ml folder attach` utworzyło `.azureml` podkatalog zawierający dwa przykładowe pliki runconfig.
        >
        > Jeśli masz skrypt języka Python, który tworzy obiekt konfiguracji uruchamiania programowo, możesz użyć polecenia [RunConfig.save(),](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) aby zapisać go jako plik runconfig.
        >
        > Aby uzyskać więcej przykładowych plików runconfig, zobacz [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/) .
    
        Aby uzyskać więcej informacji, zobacz [az ml run submit-script](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

    # <a name="studio"></a>[Studio](#tab/azure-studio)

    Aby uzyskać przykład trenowania modelu w projektancie Azure Machine Learning, zobacz [Tutorial: Predict automobile price with the designer (Samouczek: przewidywanie](tutorial-designer-automobile-price-train-score.md)ceny samochodów przy użyciu projektanta ).

    ---

* Monitorowanie stanu uruchomienia

    # <a name="python"></a>[Python](#tab/python)
    
    * Pobierz stan uruchomienia za pomocą [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29#get-status--) metody .
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * Aby uzyskać identyfikator uruchomienia, czas wykonywania i inne szczegóły dotyczące uruchomienia, użyj [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace#get-details--) metody .
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * Po pomyślnym zakończeniu uruchomienia użyj metody , aby oznaczyć ją [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) jako ukończoną.
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Jeśli używasz wzorca projektowego języka Python, przebieg automatycznie oznaczy się jako ukończony, `with...as` gdy przebieg będzie poza zakresem. Nie trzeba ręcznie oznaczać uruchomienia jako ukończonego.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    
    * Aby wyświetlić listę przebiegów eksperymentu, użyj następującego polecenia. Zastąp `experiment` nazwą swojego eksperymentu:
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        To polecenie zwraca dokument JSON, który zawiera informacje o przebiegach dla tego eksperymentu.
    
        Aby uzyskać więcej informacji, zobacz [az ml experiment list](/cli/azure/ext/azure-cli-ml/ml/experiment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).
    
    * Aby wyświetlić informacje dotyczące określonego uruchomienia, użyj następującego polecenia. Zastąp `runid` identyfikatorem uruchomienia:
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        To polecenie zwraca dokument JSON, który zawiera listę informacji o uruchomieniu.
    
        Aby uzyskać więcej informacji, zobacz [az ml run show](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).
    
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    ---
## <a name="custom-view"></a>Widok niestandardowy 
    
Aby wyświetlić przebiegi w studio: 
    
1. Przejdź do karty **Experiments (Eksperymenty).**
    
1. Wybierz pozycję **Wszystkie eksperymenty,** aby wyświetlić wszystkie przebiegi w eksperymencie, lub wybierz pozycję **Wszystkie** przebiegi, aby wyświetlić wszystkie przebiegi przesłane w obszarze roboczym.
    
Na stronie **Wszystkie przebiegi** możesz filtrować listę przebiegów według tagów, eksperymentów, docelowego obiektu obliczeniowego i innych elementów, aby lepiej zorganizować i określić zakres pracy.  
    
1. Dostosowywanie strony przez wybranie przebiegów do porównania, dodanie wykresów lub zastosowanie filtrów. Te zmiany można zapisać jako widok **niestandardowy,** aby można było łatwo wrócić do pracy. Użytkownicy z uprawnieniami obszaru roboczego mogą edytować lub wyświetlać widok niestandardowy. Ponadto udostępnij widok niestandardowy członkom zespołu w celu zwiększenia współpracy, wybierając pozycję **Widok udostępniania.**   
    
:::image type="content" source="media/how-to-track-monitor-analyze-runs/custom-views.gif" alt-text="Zrzut ekranu: tworzenie widoku niestandardowego":::
    
1. Aby wyświetlić dzienniki uruchamiania, wybierz określony przebieg, a na karcie Dane wyjściowe **i** dzienniki możesz znaleźć dzienniki diagnostyczne i dzienniki błędów dla danego uruchomienia.

## <a name="run-description"></a>Opis uruchomienia 

Opis uruchomienia można dodać do uruchomienia, aby zapewnić więcej kontekstu i informacji o uruchomieniu. Możesz również wyszukać te opisy na liście przebiegów i dodać opis uruchomienia jako kolumnę na liście przebiegów. 

Przejdź do strony **Szczegóły uruchomienia** dla swojego uruchomienia i wybierz ikonę edycji lub ołówka, aby dodać, edytować lub usunąć opisy przebiegów. Aby utrwalić zmiany na liście przebiegów, zapisz zmiany w istniejącym widoku niestandardowym lub nowym widoku niestandardowym. Opisy przebiegów są obsługiwane w formacie Markdown, co umożliwia osadzoną i głęboką konsolidację obrazów, jak pokazano poniżej.

:::image type="content" source="media/how-to-track-monitor-analyze-runs/run-description.gif" alt-text="Zrzut ekranu: tworzenie opisu uruchomienia"::: 

## <a name="tag-and-find-runs"></a>Tagowanie i znajdowanie przebiegów

W Azure Machine Learning można użyć właściwości i tagów, aby ułatwić organizowanie przebiegów i wykonywanie zapytań o ważne informacje.

* Dodawanie właściwości i tagów

    # <a name="python"></a>[Python](#tab/python)
    
    Aby dodać metadane z wyszukiwaniem do przebiegów, użyj [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29#add-properties-properties-) metody . Na przykład poniższy kod dodaje właściwość `"author"` do uruchomienia:
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    Właściwości są niezmienne, więc tworzą trwały rekord na potrzeby inspekcji. Poniższy przykład kodu powoduje błąd, ponieważ dodaliśmy już jako wartość `"azureml-user"` `"author"` właściwości w poprzednim kodzie:
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    W przeciwieństwie do właściwości tagi są modyfikowalne. Aby dodać informacje, które można przeszukiwać i które mają znaczenie dla użytkowników eksperymentu, użyj [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29#tag-key--value-none-) metody .
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    Można również dodać proste tagi ciągu. Gdy te tagi są wyświetlane w słowniku tagów jako klucze, mają wartość `None` .
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    
    > [!NOTE]
    > Za pomocą interfejsu wiersza polecenia można dodawać lub aktualizować tylko tagi.
    
    Aby dodać lub zaktualizować tag, użyj następującego polecenia:
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    Aby uzyskać więcej informacji, zobacz [az ml run update](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Tagi uruchamiania można dodawać, edytować i usuwać z programu Studio. Przejdź do strony **Szczegóły uruchomienia** dla swojego uruchomienia i wybierz ikonę edycji lub ołówka, aby dodać, edytować lub usunąć tagi dla przebiegów. Możesz również wyszukiwać i filtrować według tych tagów na stronie listy przebiegów.
    
    :::image type="content" source="media/how-to-track-monitor-analyze-runs/run-tags.gif" alt-text="Zrzut ekranu: Dodawanie, edytowanie lub usuwanie tagów uruchamiania":::
    
    ---

* Właściwości i tagi zapytań

    Zapytania dotyczące przebiegów w eksperymencie mogą zwracać listę przebiegów, które pasują do określonych właściwości i tagów.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    
    Interfejs wiersza polecenia platformy Azure obsługuje [zapytania JMESPath,](http://jmespath.org) których można użyć do filtrowania przebiegów na podstawie właściwości i tagów. Aby użyć zapytania JMESPath z interfejsem wiersza polecenia platformy Azure, określ je za pomocą `--query` parametru . W poniższych przykładach przedstawiono niektóre zapytania korzystające z właściwości i tagów:
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Aby uzyskać więcej informacji na temat wykonywania zapytań dotyczących wyników interfejsu wiersza polecenia platformy Azure, zobacz Query Azure CLI command output (Wykonywanie [zapytań dotyczących danych wyjściowych polecenia interfejsu wiersza polecenia platformy Azure).](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest)
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Aby wyszukać określone przebiegi, przejdź do **listy Wszystkie przebiegi.** W tym miejscu masz dwie opcje:
    
    1. Użyj **przycisku Dodaj filtr** i wybierz pozycję Filtruj według tagów, aby filtrować przebiegi według tagów przypisanych do przebiegów. <br><br>
    LUB
    
    1. Użyj paska wyszukiwania, aby szybko znaleźć przebiegi, wyszukując metadane uruchomienia, takie jak stan uruchomienia, opisy, nazwy eksperymentów i nazwa osoby przesyłacej. 
    
## <a name="cancel-or-fail-runs"></a>Anulowanie przebiegów lub niepowodzenie przebiegów

Jeśli zauważysz błąd lub jeśli przebieg trwa zbyt długo, możesz anulować przebieg.

# <a name="python"></a>[Python](#tab/python)

Aby anulować przebieg przy użyciu zestawu SDK, użyj [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29#cancel--) metody :

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

Jeśli przebieg zakończy się, ale zawiera błąd (na przykład został użyty nieprawidłowy skrypt trenowania), możesz użyć metody , aby oznaczyć go [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) jako niepowodzenie.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby anulować przebieg przy użyciu interfejsu wiersza polecenia, użyj następującego polecenia. Zastąp `runid` identyfikatorem uruchomienia

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Aby uzyskać więcej informacji, zobacz [az ml run cancel](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Aby anulować przebieg w studio, należy wykonać następujące czynności:

1. Przejdź do uruchomionego potoku w sekcji **Experiments (Eksperymenty)** **lub Pipelines (Potoki).** 

1. Wybierz numer uruchomienia potoku, który chcesz anulować.

1. Na pasku narzędzi wybierz pozycję **Anuluj**

---

## <a name="create-child-runs"></a>Tworzenie przebiegów podrzędnych

Utwórz podrzędne przebiegi, aby zgrupować ze sobą powiązane przebiegi, na przykład dla różnych iteracji dostrajania hiperparametrów.

> [!NOTE]
> Przebiegi podrzędne można tworzyć tylko przy użyciu zestawu SDK.

W tym przykładzie kodu użyto skryptu do utworzenia partii pięciu przebiegów podrzędnych z `hello_with_children.py` przesłanego uruchomienia przy użyciu [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29#child-run-name-none--run-id-none--outputs-none-) metody :

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
> Gdy przejdą poza zakres, podrzędne przebiegi są automatycznie oznaczane jako ukończone.

Aby wydajnie utworzyć wiele przebiegów podrzędnych, użyj [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) metody . Ponieważ każde utworzenie powoduje wywołanie sieciowe, tworzenie partii przebiegów jest bardziej wydajne niż tworzenie ich po jednym.

### <a name="submit-child-runs"></a>Przesyłanie przebiegów podrzędnych

Przebiegi podrzędne można również przesłać z uruchomienia nadrzędnego. Dzięki temu można tworzyć hierarchie przebiegów nadrzędnych i podrzędnych. Nie można utworzyć podrzędnego uruchomienia bez elementu nadrzędnego: nawet jeśli przebieg nadrzędny nic nie robi, ale uruchamia przebiegi podrzędne, nadal konieczne jest utworzenie hierarchii. Stany wszystkich przebiegów są niezależne: element nadrzędny może być w stanie sukcesu, nawet jeśli co najmniej jeden przebieg podrzędny został `"Completed"` anulowany lub zakończył się niepowodzeniem.  

Możesz chcieć, aby przebiegi podrzędne używały innej konfiguracji uruchamiania niż przebieg nadrzędny. Na przykład można użyć mniej wydajnej konfiguracji opartej na procesorze CPU dla elementu nadrzędnego, podczas gdy dla dzieci można użyć konfiguracji opartych na procesorze GPU. Innym często spotykaną chęć jest przekazania do każdego podrzędnego różnych argumentów i danych. Aby dostosować przebieg podrzędny, utwórz `ScriptRunConfig` obiekt dla podrzędnego uruchomienia. 

> [!IMPORTANT]
> Aby przesłać przebieg podrzędny z nadrzędnego uruchomienia w zdalnym obliczeniach, należy najpierw zalogować się do obszaru roboczego w kodzie uruchomienia nadrzędnego. Domyślnie obiekt kontekstu uruchomienia w zdalnym uruchomieniu nie ma poświadczeń do przesyłania przebiegów podrzędnych. Użyj jednostki usługi lub poświadczeń tożsamości zarządzanej, aby się zalogować. Aby uzyskać więcej informacji na temat uwierzytelniania, [zobacz Konfigurowanie uwierzytelniania](how-to-setup-authentication.md).

Poniższy kod:

- Pobiera zasób obliczeniowy o nazwie `"gpu-cluster"` z obszaru roboczego `ws`
- Iteruje różne wartości argumentów, które mają zostać przekazane do obiektów `ScriptRunConfig` podrzędnych
- Tworzy i przesyła nowy przebieg podrzędny przy użyciu niestandardowego zasobu obliczeniowego i argumentu
- Blokuje do momentu ukończenia wszystkich przebiegów podrzędnych

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

Aby efektywnie utworzyć wiele przebiegów podrzędnych z identycznymi konfiguracjami, argumentami i wejściami, użyj [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) metody . Ponieważ każde utworzenie powoduje wywołanie sieciowe, utworzenie partii przebiegów jest bardziej wydajne niż tworzenie ich po jednym.

W ramach podrzędnego uruchomienia można wyświetlić identyfikator uruchomienia nadrzędnego:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Wykonywanie zapytań o przebiegi podrzędne

Aby odpytać podrzędne przebiegi określonego elementu nadrzędnego, użyj [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) metody . Argument ``recursive = True`` pozwala na wykonywanie zapytań o zagnieżdżone drzewo dzieci i grandee.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>Logowanie do uruchomienia nadrzędnego lub głównego

Możesz użyć pola `Run.parent` , aby uzyskać dostęp do uruchomienia, w przypadku których uruchomiono bieżący przebieg podrzędny. Powszechnym zastosowaniem użycia jest `Run.parent` łączenie wyników dziennika w jednym miejscu. Przebiegi podrzędne są wykonywane asynchronicznie i nie ma gwarancji, że kolejność lub synchronizacja wykracza poza możliwość oczekiwania przez element nadrzędny na ukończenie jego przebiegów podrzędnych.

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```

## <a name="monitor-the-run-status-by-email-notification"></a>Monitorowanie stanu uruchomienia za pomocą powiadomienia e-mail

1. Na pasku [Azure Portal](https://ms.portal.azure.com/)na pasku nawigacyjnym po lewej stronie wybierz **kartę** Monitor. 

1. Wybierz **pozycję Ustawienia diagnostyczne,** a następnie **wybierz pozycję + Dodaj ustawienie diagnostyczne.**

    ![Zrzut ekranu przedstawiający ustawienia diagnostyczne dla powiadomień e-mail](./media/how-to-track-monitor-analyze-runs/diagnostic-setting.png)

1. W ustawieniu diagnostycznym 
    1. w obszarze **Szczegóły kategorii** wybierz pozycję **AmlRunStatusChangedEvent.** 
    1. W obszarze **Szczegóły lokalizacji docelowej** wybierz obszar roboczy Wyślij do usługi Log **Analytics** i określ obszar **roboczy Subskrypcja** i **Log Analytics.** 

    > [!NOTE]
    > Obszar **roboczy usługi Azure Log Analytics** jest innym typem zasobu platformy Azure niż Azure Machine Learning service **Workspace.** Jeśli na tej liście nie ma żadnych opcji, możesz utworzyć [obszar roboczy usługi Log Analytics.](../azure-monitor/logs/quick-create-workspace.md) 
    
    ![Gdzie zapisać powiadomienie e-mail](./media/how-to-track-monitor-analyze-runs/log-location.png)

1. Na karcie **Dzienniki** dodaj nową **regułę alertu**. 

    ![Nowa reguła alertu](./media/how-to-track-monitor-analyze-runs/new-alert-rule.png)

1. Zobacz, [jak tworzyć alerty dzienników i zarządzać nimi przy użyciu Azure Monitor](../azure-monitor/alerts/alerts-log.md).

## <a name="example-notebooks"></a>Przykładowe notesy

W następujących notesach przedstawiono pojęcia z tego artykułu:

* Aby dowiedzieć się więcej na temat interfejsów API rejestrowania, zobacz notes [interfejsu API rejestrowania](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* Aby uzyskać więcej informacji na temat zarządzania przebiegami za pomocą zestawu SDK Azure Machine Learning, zobacz [notes zarządzania przebiegami](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak rejestrować metryki dla eksperymentów, zobacz [Rejestrowanie metryk podczas przebiegów trenowania.](how-to-log-view-metrics.md)
* Aby dowiedzieć się, jak monitorować zasoby i dzienniki z Azure Machine Learning, zobacz [Monitorowanie Azure Machine Learning](monitor-azure-machine-learning.md).