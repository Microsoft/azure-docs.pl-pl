---
title: Informacje dla deweloperów języka Python dla Azure Functions
description: Dowiedz się, jak opracowywać funkcje za pomocą języka Python
ms.topic: article
ms.date: 11/4/2020
ms.custom: devx-track-python
ms.openlocfilehash: 0c87be334847974627299f8e21109fe201675f0c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762177"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions dla deweloperów języka Python

Ten artykuł stanowi wprowadzenie do tworzenia aplikacji Azure Functions języka Python. W poniższej zawartości założono, że już przeczytaliśmy przewodnik Azure Functions [deweloperów.](functions-reference.md)

Jako deweloper języka Python możesz również zainteresować się jednym z następujących artykułów:

| Wprowadzenie | Pojęcia| Scenariusze/przykłady |
| -- | -- | -- | 
| <ul><li>[Funkcja języka Python przy użyciu Visual Studio Code](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)</li><li>[Funkcja języka Python z terminalem/wierszem polecenia](./create-first-function-cli-csharp.md?pivots=programming-language-python)</li></ul> | <ul><li>[Przewodnik dla deweloperów](functions-reference.md)</li><li>[Opcje hostingu](functions-scale.md)</li><li>[Zagadnienia &nbsp; dotyczące wydajności](functions-best-practices.md)</li></ul> | <ul><li>[Klasyfikacja obrazów za pomocą platformy PyTorch](machine-learning-pytorch.md)</li><li>[Przykład usługi Azure Automation](/samples/azure-samples/azure-functions-python-list-resource-groups/azure-functions-python-sample-list-resource-groups/)</li><li>[Uczenie maszynowe przy użyciu biblioteki TensorFlow](functions-machine-learning-tensorflow.md)</li><li>[Przeglądaj przykłady dla języka Python](/samples/browse/?products=azure-functions&languages=python)</li></ul> |

> [!NOTE]
> Chociaż możesz [opracowywać](create-first-function-vs-code-python.md#run-the-function-locally)aplikacje oparte na języku Python Azure Functions systemie Windows, język Python jest obsługiwany tylko w planie hostingu opartym na systemie Linux w przypadku uruchamiania na platformie Azure. Zobacz listę obsługiwanych [kombinacji systemu operacyjnego/środowiska uruchomieniowego.](functions-scale.md#operating-systemruntime)

## <a name="programming-model"></a>Model programowania

Azure Functions oczekuje, że funkcja będzie bez stanową metodą w skrypcie języka Python, która przetwarza dane wejściowe i generuje dane wyjściowe. Domyślnie środowisko uruchomieniowe oczekuje, że metoda zostanie zaimplementowana jako metoda globalna o nazwie `main()` w `__init__.py` pliku . Można również [określić alternatywny punkt wejścia](#alternate-entry-point).

Dane z wyzwalaczy i powiązań są powiązane z funkcją za pośrednictwem atrybutów metody przy użyciu właściwości zdefiniowanej w `name` *function.jspliku.* Na przykład w  _poniższejfunction.js_ opisano prostą funkcję wyzwalaną przez żądanie HTTP o nazwie `req` :

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Na podstawie tej definicji plik zawierający kod funkcji może `__init__.py` wyglądać podobnie do poniższego przykładu:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Możesz również jawnie zadeklarować typy atrybutów i zwrócić typ w funkcji przy użyciu adnotacji typu języka Python. Ułatwia to korzystanie z funkcji IntelliSense i funkcji autouzupełniania dostępnych w wielu edytorach kodu języka Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Użyj adnotacji języka Python zawartych w pakiecie [azure.functions.*,](/python/api/azure-functions/azure.functions) aby powiązać dane wejściowe i wyjściowe z metodami.

## <a name="alternate-entry-point"></a>Alternatywny punkt wejścia

Domyślne zachowanie funkcji można zmienić, opcjonalnie określając właściwości i wfunction.js`scriptFile` `entryPoint` *pliku.* Na przykład poniższy _function.js_ nakazuje środowisku uruchomienioweowi użycie metody w pliku main.py jako punktu wejścia `customentry()` dla funkcji platformy Azure. 

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Struktura folderów

Zalecana struktura folderów dla projektu funkcji języka Python wygląda jak w poniższym przykładzie:

```
 <project_root>/
 | - .venv/
 | - .vscode/
 | - my_first_function/
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function/
 | | - __init__.py
 | | - function.json
 | - shared_code/
 | | - __init__.py
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - tests/
 | | - test_my_second_function.py
 | - .funcignore
 | - host.json
 | - local.settings.json
 | - requirements.txt
 | - Dockerfile
```
Główny folder projektu (<project_root>) może zawierać następujące pliki:

* *local.settings.jsna*: służy do przechowywania ustawień aplikacji i parametry połączenia podczas uruchamiania lokalnego. Ten plik nie jest publikowany na platformie Azure. Aby dowiedzieć się więcej, [zobacz plik local.settings.file.](functions-run-local.md#local-settings-file)
* *requirements.txt:* zawiera listę pakietów języka Python instalowanych przez system podczas publikowania na platformie Azure.
* *host.jsna*: zawiera globalne opcje konfiguracji, które mają wpływ na wszystkie funkcje w aplikacji funkcji. Ten plik jest publikowany na platformie Azure. Nie wszystkie opcje są obsługiwane w przypadku uruchamiania lokalnego. Aby dowiedzieć się więcej, zobacz [host.jsna stronie](functions-host-json.md).
* *.vscode/*: (opcjonalnie) Zawiera konfigurację programu VSCode magazynu. Aby dowiedzieć się więcej, zobacz [Ustawienie programu VSCode](https://code.visualstudio.com/docs/getstarted/settings).
* *.venv/*: (opcjonalnie) Zawiera środowisko wirtualne języka Python używane przez programowanie lokalne.
* *Dockerfile:*(opcjonalnie) używany podczas publikowania projektu w [kontenerze niestandardowym.](functions-create-function-linux-custom-image.md)
* *tests/*: (opcjonalnie) Zawiera przypadki testowe aplikacji funkcji.
* *.funcignore:*(opcjonalnie) Deklaruje pliki, które nie powinny być publikowane na platformie Azure. Zazwyczaj ten plik zawiera informacje na temat ignorowania ustawień edytora, ignorowania lokalnego środowiska wirtualnego języka Python, ignorowania przypadków testowych i zapobiegania publikacji `.vscode/` `.venv/` ustawień aplikacji `tests/` `local.settings.json` lokalnych.

Każda funkcja ma własny plik kodu i plik konfiguracji powiązania (function.jsna).

Podczas wdrażania projektu w aplikacji funkcji na platformie Azure cała zawartość folderu głównego projektu *(<project_root>)* powinna zostać uwzględniona w pakiecie, ale nie w samym folderze, co oznacza, że powinna być w katalogu głównym `host.json` pakietu. Zalecamy utrzymanie testów w folderze wraz z innymi funkcjami, w tym przykładzie `tests/` . Aby uzyskać więcej informacji, zobacz [Unit Testing](#unit-testing).

## <a name="import-behavior"></a>Zachowanie importowania

Moduły w kodzie funkcji można importować przy użyciu zarówno odwołań bezwzględnych, jak i względnych. Na podstawie przedstawionej powyżej struktury folderów następujące importy działają z pliku funkcji *<project_root>\moja pierwsza funkcja \_ _ \_ \\ \_ init \_ \_ .py:*

```python
from shared_code import my_first_helper_function #(absolute)
```

```python
import shared_code.my_second_helper_function #(absolute)
```

```python
from . import example #(relative)
```

> [!NOTE]
>  Folder *shared_code/musi* zawierać plik \_ \_ init \_ .py, aby oznaczyć go jako pakiet języka Python w przypadku używania składni \_ bezwzględnej importu.

Następujące importowanie aplikacji poza importem względnym najwyższego poziomu jest przestarzałe, ponieważ nie jest obsługiwane przez statyczny moduł sprawdzania typu i nie jest obsługiwane przez struktury \_ \_ \_ \_ testowe języka Python:

```python
from __app__.shared_code import my_first_helper_function #(deprecated __app__ import)
```

```python
from ..shared_code import my_first_helper_function #(deprecated beyond top-level relative import)
```

## <a name="triggers-and-inputs"></a>Wyzwalacze i dane wejściowe

Dane wejściowe są podzielone na dwie kategorie w Azure Functions: wejście wyzwalacza i dodatkowe dane wejściowe. Chociaż różnią się one w `function.json` pliku, użycie jest identyczne w kodzie języka Python.  Parametry połączenia lub wpisy tajne dla wyzwalacza i źródeł danych wejściowych są mapowanie na wartości w pliku podczas uruchamiania lokalnego oraz ustawienia aplikacji w przypadku `local.settings.json` uruchamiania na platformie Azure.

Na przykład poniższy kod pokazuje różnicę między nimi:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Po wywołaniu funkcji żądanie HTTP jest przekazywane do funkcji jako `req` . Wpis zostanie pobrany z witryny Azure Blob Storage na podstawie  identyfikatora w adresie URL trasy i będzie dostępny jako w `obj` treści funkcji.  W tym miejscu określone konto magazynu to ciąg połączenia znaleziony w ustawieniu aplikacji AzureWebJobsStorage, które jest tym samym kontem magazynu, które jest używane przez aplikację funkcji.


## <a name="outputs"></a>Dane wyjściowe

Dane wyjściowe mogą być wyrażone zarówno w parametrach zwracanych, jak i wyjściowych. Jeśli dane wyjściowe są tylko jedno, zalecamy użycie wartości zwracanej. W przypadku wielu danych wyjściowych należy użyć parametrów wyjściowych.

Aby użyć wartości zwracanej funkcji jako wartości powiązania wyjściowego, właściwość powiązania powinna być ustawiona `name` na `$return` wartość w . `function.json`

Aby uzyskać wiele danych wyjściowych, użyj metody dostarczonej przez `set()` [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out) interfejs, aby przypisać wartość do powiązania. Na przykład następująca funkcja może wypchnąć komunikat do kolejki i zwrócić odpowiedź HTTP.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Rejestrowanie

Dostęp do rejestratora Azure Functions uruchomieniowego jest dostępny za pośrednictwem głównego programu obsługi [`logging`](https://docs.python.org/3/library/logging.html#module-logging) w aplikacji funkcji. Ten rejestrator jest powiązany z Application Insights i umożliwia flagowanie ostrzeżeń i błędów napotkanych podczas wykonywania funkcji.

Poniższy przykład rejestruje komunikat informacyjny, gdy funkcja jest wywoływana za pośrednictwem wyzwalacza HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Dostępne są dodatkowe metody rejestrowania, które umożliwiają zapis w konsoli na różnych poziomach śledzenia:

| Metoda                 | Opis                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Zapisuje komunikat z poziomem CRITICAL w rejestratorze głównym.  |
| **`error(_message_)`**   | Zapisuje komunikat z błędem poziomu w rejestratorze głównym.    |
| **`warning(_message_)`**    | Zapisuje komunikat z ostrzeżeniem poziomu w rejestratorze głównym.  |
| **`info(_message_)`**    | Zapisuje komunikat z poziomem INFO w rejestratorze głównym.  |
| **`debug(_message_)`** | Zapisuje komunikat z poziomem DEBUG w rejestratorze głównym.  |

Aby dowiedzieć się więcej na temat rejestrowania, [zobacz Monitorowanie Azure Functions](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Wyzwalacz HTTP i powiązania

Wyzwalacz HTTP jest definiowany w function.jspliku. Wartość `name` powiązania musi być dopasowana do nazwanego parametru w funkcji .
W poprzednich przykładach używana jest `req` nazwa powiązania. Ten parametr jest [obiektem HttpRequest] i zwracany jest obiekt [HttpResponse.]

Z obiektu [HttpRequest] można uzyskać nagłówki żądań, parametry zapytania, parametry trasy i treść komunikatu.

Poniższy przykład pochodzi z szablonu [wyzwalacza HTTP dla języka Python.](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

W tej funkcji wartość parametru zapytania jest uzyskiwana z `name` `params` parametru obiektu [HttpRequest.] Treść komunikatu zakodowana w formacie JSON jest odczytywana przy użyciu `get_json` metody .

Podobnie można ustawić i `status_code` dla komunikatu odpowiedzi w `headers` zwróconym [obiekcie HttpResponse.]

## <a name="scaling-and-performance"></a>Skalowanie i wydajność

Najlepsze rozwiązania dotyczące skalowania i wydajności dla aplikacji funkcji języka Python można znaleźć w artykule Skalowanie [i wydajność języka Python.](python-scale-performance-reference.md)

## <a name="context"></a>Kontekst

Aby uzyskać kontekst wywołania funkcji podczas wykonywania, uwzględnij [`context`](/python/api/azure-functions/azure.functions.context) argument w jej sygnaturze.

Na przykład:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

Klasa [**Context**](/python/api/azure-functions/azure.functions.context) ma następujące atrybuty ciągu:

`function_directory` Katalog, w którym jest uruchomiona funkcja .

`function_name` Nazwa funkcji.

`invocation_id` Identyfikator bieżącego wywołania funkcji.

## <a name="global-variables"></a>Zmienne globalne

Nie ma gwarancji, że stan aplikacji zostanie zachowany na przyszłość. Jednak środowisko Azure Functions często ponownie używa tego samego procesu dla wielu wykonań tej samej aplikacji. Aby buforować wyniki kosztownych obliczeń, zadeklaruj je jako zmienną globalną.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Zmienne środowiskowe

W usłudze Functions [ustawienia aplikacji](functions-app-settings.md), takie jak parametry połączenia usługi, są udostępniane jako zmienne środowiskowe podczas wykonywania. Dostęp do tych ustawień można uzyskać, deklarując, `import os` a następnie używając . `setting = os.environ["setting-name"]`

Poniższy przykład pobiera ustawienie [aplikacji z](functions-how-to-use-azure-function-app-settings.md#settings)kluczem o nazwie `myAppSetting` :

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

W przypadku tworzenia aplikacji lokalnych ustawienia aplikacji [są zachowywane w local.settings.jspliku](functions-run-local.md#local-settings-file).

## <a name="python-version"></a>Wersja języka Python

Azure Functions obsługuje następujące wersje języka Python:

| Wersja funkcji | Wersje języka Python <sup>*</sup> |
| ----- | ----- |
| 3.x | 3.9 (wersja zapoznawcza) <br/> 3,8<br/>3.7<br/>3,6 |
| 2.x | 3.7<br/>3,6 |

<sup>*</sup>Oficjalne dystrybucje języka CPython

Aby zażądać określonej wersji języka Python podczas tworzenia aplikacji funkcji na platformie Azure, `--runtime-version` użyj opcji [`az functionapp create`](/cli/azure/functionapp#az_functionapp_create) polecenia . Wersja środowiska uruchomieniowego usługi Functions jest ustawiana przez `--functions-version` opcję . Wersja języka Python jest ustawiana podczas tworzenia aplikacji funkcji i nie można jej zmienić.

W przypadku uruchamiania lokalnego środowisko uruchomieniowe używa dostępnej wersji języka Python.

## <a name="package-management"></a>Zarządzanie pakietami

Podczas tworzenia aplikacji lokalnie przy Azure Functions Core Tools lub Visual Studio Code dodaj nazwy i wersje wymaganych pakietów do pliku i `requirements.txt` zainstaluj je przy użyciu programu `pip` .

Na przykład do zainstalowania pakietu z pliku PyPI można użyć następującego pliku wymagań i polecenia `requests` pip.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publikowanie na platformie Azure

Gdy wszystko będzie gotowe do opublikowania, upewnij się, że wszystkie publicznie dostępne zależności są wymienione w pliku requirements.txt, który znajduje się w katalogu głównym projektu.

Pliki projektu i foldery, które są wykluczone z publikowania, w tym folder środowiska wirtualnego, są wymienione w pliku funcignore.

Istnieją trzy akcje kompilacji obsługiwane w przypadku publikowania projektu języka Python na platformie Azure: kompilacja zdalna, kompilacja lokalna i kompilacje przy użyciu zależności niestandardowych.

Możesz również użyć Azure Pipelines, aby tworzyć zależności i publikować przy użyciu ciągłego dostarczania (CD). Aby dowiedzieć się więcej, zobacz [Continuous delivery by using Azure DevOps](functions-how-to-azure-devops.md)(Ciągłe dostarczanie przy użyciu Azure DevOps ).

### <a name="remote-build"></a>Kompilacja zdalna

W przypadku korzystania z kompilacji zdalnej zależności przywrócone na serwerze i zależności natywne są zgodne ze środowiskiem produkcyjnym. Powoduje to przekazanie mniejszego pakietu wdrożeniowego. Używanie zdalnej kompilacji podczas tworzenia aplikacji języka Python w systemie Windows. Jeśli projekt ma zależności niestandardowe, możesz użyć kompilacji zdalnej [z dodatkowym adresem URL indeksu](#remote-build-with-extra-index-url).

Zależności są uzyskiwane zdalnie na podstawie zawartości requirements.txt pliku. [Zalecaną metodą](functions-deployment-technologies.md#remote-build) kompilacji jest kompilacja zdalna. Domyślnie aplikacja żąda Azure Functions Core Tools kompilacji zdalnej, gdy użyjesz następującego polecenia [func azure functionapp publish](functions-run-local.md#publish) w celu opublikowania projektu języka Python na platformie Azure.

```bash
func azure functionapp publish <APP_NAME>
```

Pamiętaj, aby `<APP_NAME>` zastąpić nazwą aplikacji funkcji na platformie Azure.

Rozszerzenie [Azure Functions dla Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) domyślnie żąda również zdalnej kompilacji.

### <a name="local-build"></a>Kompilacja lokalna

Zależności są uzyskiwane lokalnie na podstawie zawartości requirements.txt pliku. Możesz uniemożliwić wykonanie zdalnej kompilacji, używając następującego [polecenia func azure functionapp publish](functions-run-local.md#publish) do publikowania z lokalną kompilacją.

```command
func azure functionapp publish <APP_NAME> --build local
```

Pamiętaj, aby `<APP_NAME>` zastąpić nazwą aplikacji funkcji na platformie Azure.

Przy użyciu tej opcji zależności projektu są odczytywane z pliku requirements.txt, a te pakiety zależne są pobierane `--build local` i instalowane lokalnie. Pliki projektu i zależności są wdrażane z komputera lokalnego na platformie Azure. Powoduje to, że większy pakiet wdrożeniowy jest przekazywany na platformę Azure. Jeśli z jakiegoś powodu zależności w pliku requirements.txt nie mogą zostać uzyskane przez narzędzia Core Tools, należy użyć opcji zależności niestandardowych do publikowania.

Nie zalecamy używania kompilacji lokalnych podczas kompilowania lokalnie w systemie Windows.

### <a name="custom-dependencies"></a>Zależności niestandardowe

Jeśli w indeksie pakietów języka [Python](https://pypi.org/)nie odnaleziono zależności projektu, istnieją dwa sposoby jego skompilowania. Metoda kompilacji zależy od sposobu kompilowania projektu.

#### <a name="remote-build-with-extra-index-url"></a>Kompilacja zdalna z dodatkowym adresem URL indeksu

Jeśli pakiety są dostępne z dostępnego niestandardowego indeksu pakietów, użyj zdalnej kompilacji. Przed opublikowaniem upewnij się, [że tworzysz ustawienie aplikacji o](functions-how-to-use-azure-function-app-settings.md#settings) nazwie `PIP_EXTRA_INDEX_URL` . Wartością tego ustawienia jest adres URL niestandardowego indeksu pakietów. Użycie tego ustawienia nakazuje zdalnej kompilacji uruchomienie `pip install` przy użyciu `--extra-index-url` opcji . Aby dowiedzieć się więcej, zobacz dokumentację [instalacji programu pip języka Python.](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format)

Możesz również użyć poświadczeń uwierzytelniania podstawowego z dodatkowymi adresami URL indeksów pakietów. Aby dowiedzieć się więcej, zobacz [dokumentację Podstawowe poświadczenia uwierzytelniania](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) w języku Python.

#### <a name="install-local-packages"></a>Instalowanie pakietów lokalnych

Jeśli projekt używa pakietów, które nie są publicznie dostępne dla naszych narzędzi, możesz udostępnić je aplikacji, umieszczając je w katalogu \_ \_ \_ \_ aplikacji /.python_packages. Przed opublikowaniem uruchom następujące polecenie, aby zainstalować zależności lokalnie:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

W przypadku korzystania z zależności niestandardowych należy użyć opcji publikowania, ponieważ zależności zostały już zainstalowane `--no-build` w folderze projektu.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Pamiętaj, aby `<APP_NAME>` zastąpić nazwą aplikacji funkcji na platformie Azure.

## <a name="unit-testing"></a>Testowanie jednostkowe

Funkcje napisane w języku Python można testować tak jak inne kod języka Python przy użyciu standardowych platform testowania. W przypadku większości powiązań można utworzyć makietę obiektu wejściowego, tworząc wystąpienie odpowiedniej klasy z `azure.functions` pakietu. Ponieważ pakiet nie jest natychmiast dostępny, należy zainstalować go za pośrednictwem pliku zgodnie z opisem [`azure.functions`](https://pypi.org/project/azure-functions/) `requirements.txt` w powyższej sekcji [zarządzania](#package-management) pakietami.

Weźmy *my_second_function* przykład: poniżej przedstawiono makietę testu funkcji wyzwalanych przez protokół HTTP:

Najpierw musimy utworzyć plik *<project_root>/my_second_function/function.js* i zdefiniować tę funkcję jako wyzwalacz HTTP.

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "main",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Teraz możemy zaimplementować my_second_function *i* *shared_code.my_second_helper_function*.

```python
# <project_root>/my_second_function/__init__.py
import azure.functions as func
import logging

# Use absolute import to resolve shared_code modules
from shared_code import my_second_helper_function

# Define an http trigger which accepts ?value=<int> query parameter
# Double the value and return the result in HttpResponse
def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Executing my_second_function.')

    initial_value: int = int(req.params.get('value'))
    doubled_value: int = my_second_helper_function.double(initial_value)

    return func.HttpResponse(
      body=f"{initial_value} * 2 = {doubled_value}",
      status_code=200
    )
```

```python
# <project_root>/shared_code/__init__.py
# Empty __init__.py file marks shared_code folder as a Python package
```

```python
# <project_root>/shared_code/my_second_helper_function.py

def double(value: int) -> int:
  return value * 2
```

Możemy rozpocząć pisanie przypadków testowych dla naszego wyzwalacza HTTP.

```python
# <project_root>/tests/test_my_second_function.py
import unittest

import azure.functions as func
from my_second_function import main

class TestFunction(unittest.TestCase):
    def test_my_second_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/my_second_function',
            params={'value': '21'})

        # Call the function.
        resp = main(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'21 * 2 = 42',
        )
```

W `.venv` środowisku wirtualnym języka Python zainstaluj ulubioną platformę testową języka Python (np. `pip install pytest` ). Po prostu `pytest tests` uruchom , aby sprawdzić wynik testu.

## <a name="temporary-files"></a>Pliki tymczasowe

Metoda `tempfile.gettempdir()` zwraca folder tymczasowy, który w systemie Linux to `/tmp` . Aplikacja może używać tego katalogu do przechowywania plików tymczasowych generowanych i używanych przez funkcje podczas wykonywania.

> [!IMPORTANT]
> Nie ma gwarancji, że pliki zapisywane w katalogu tymczasowym będą utrwalane w wywołaniach. Podczas skalowania w zewnątrz pliki tymczasowe nie są współdzielone między wystąpieniami.

Poniższy przykład tworzy nazwany plik tymczasowy w katalogu tymczasowym ( `/tmp` ):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()
   fp = tempfile.NamedTemporaryFile()
   fp.write(b'Hello world!')
   filesDirListInTemp = listdir(tempFilePath)
```

Zalecamy utrzymanie testów w folderze oddzielnym od folderu projektu. Dzięki temu nie będzie można wdrażać kodu testowego w aplikacji.

## <a name="preinstalled-libraries"></a>Wstępnie zainstalowane biblioteki

Istnieje kilka bibliotek ze środowiskiem uruchomieniowym usługi Python Functions.

### <a name="python-standard-library"></a>Standardowa biblioteka języka Python

Biblioteka standardowa języka Python zawiera listę wbudowanych modułów języka Python dostarczanych z każdą dystrybucją języka Python. Większość z tych bibliotek pomaga uzyskać dostęp do funkcji systemowych, takich jak we/wy plików. W systemach Windows te biblioteki są instalowane przy użyciu języka Python. W systemach z systemem Unix są one udostępniane przez kolekcje pakietów.

Aby wyświetlić pełne szczegóły listy tych bibliotek, odwiedź poniższe linki:

* [Standardowa biblioteka języka Python 3.6](https://docs.python.org/3.6/library/)
* [Standardowa biblioteka języka Python 3.7](https://docs.python.org/3.7/library/)
* [Standardowa biblioteka języka Python 3.8](https://docs.python.org/3.8/library/)
* [Standardowa biblioteka języka Python 3.9](https://docs.python.org/3.9/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Azure Functions zależności procesu roboczego języka Python

Proces roboczy języka Python dla funkcji wymaga określonego zestawu bibliotek. Tych bibliotek można również używać w funkcjach, ale nie są one częścią standardu języka Python. Jeśli funkcje korzystają z dowolnej z tych bibliotek, mogą nie być dostępne dla twojego kodu podczas uruchamiania poza Azure Functions. Szczegółową listę zależności można znaleźć w sekcji wymaganej instalacji w [setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) instalacji. **\_**

> [!NOTE]
> Jeśli plik aplikacji funkcji requirements.txt `azure-functions-worker` wpis, usuń go. Proces roboczy funkcji jest automatycznie zarządzany Azure Functions platformie i regularnie aktualizujemy go przy użyciu nowych funkcji i poprawek błędów. Ręczne instalowanie starej wersji procesu roboczego w programie requirements.txt może spowodować nieoczekiwane problemy.

### <a name="azure-functions-python-library"></a>Azure Functions języka Python

Każda aktualizacja procesu roboczego języka Python zawiera nową [wersję biblioteki Azure Functions Python (azure.functions).](https://github.com/Azure/azure-functions-python-library) Takie podejście ułatwia ciągłe aktualizowanie aplikacji funkcji języka Python, ponieważ każda aktualizacja jest zgodna z poprzednimi wersjami. Listę wydań tej biblioteki można znaleźć w witrynie [azure-functions PyPi](https://pypi.org/project/azure-functions/#history).

Wersja biblioteki środowiska uruchomieniowego jest naprawiona przez platformę Azure i nie może zostać zastąpiona przez requirements.txt. Wpis `azure-functions` w requirements.txt dotyczy tylko kłaniania i świadomości klientów.

Użyj następującego kodu, aby śledzić rzeczywistą wersję biblioteki funkcji języka Python w środowisku uruchomieniowym:

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>Biblioteki systemu środowiska uruchomieniowego

Aby uzyskać listę wstępnie zainstalowanych bibliotek systemowych w obrazach platformy Docker procesów roboczych języka Python, skorzystaj z poniższych linków:

|  Środowisko uruchomieniowe usługi Functions  | Wersja Debian | Wersje języka Python |
|------------|------------|------------|
| Wersja 2.x | Stretch  | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| Wersja 3.x | Buster | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile)<br/> [Python 3.9](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python39/python39.Dockerfile)|

## <a name="cross-origin-resource-sharing"></a>Współużytkowanie zasobów między źródłami

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

Funkcja CORS jest w pełni obsługiwana w przypadku aplikacji funkcji języka Python.

## <a name="known-issues-and-faq"></a>Znane problemy i często zadawane pytania

Poniżej znajduje się lista przewodników rozwiązywania typowych problemów:

* [ModuleNotFoundError i ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [Nie można zaimportować pliku "cygrpc"](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

Wszystkie znane problemy i żądania funkcji są śledzone przy użyciu [listy problemów usługi GitHub.](https://github.com/Azure/azure-functions-python-worker/issues) Jeśli znajdziesz problem i nie możesz go znaleźć w usłudze GitHub, otwórz nowy problem i dołącz szczegółowy opis problemu.

## <a name="next-steps"></a>Następne kroki

Więcej informacji można znaleźć w następujących zasobach:

* [Azure Functions dokumentacji interfejsu API pakietu](/python/api/azure-functions/azure.functions)
* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Azure Functions wyzwalaczy i powiązań](functions-triggers-bindings.md)
* [Powiązania usługi Blob Storage](functions-bindings-storage-blob.md)
* [Powiązania http i webhook](functions-bindings-http-webhook.md)
* [Powiązania usługi Queue Storage](functions-bindings-storage-queue.md)
* [Wyzwalacz czasomierza](functions-bindings-timer.md)

[Masz problemy? Daj nam znać.](https://aka.ms/python-functions-ref-survey)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse