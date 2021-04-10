---
title: Dokumentacja dla deweloperów języka Python dla Azure Functions
description: Informacje na temat tworzenia funkcji w języku Python
ms.topic: article
ms.date: 11/4/2020
ms.custom: devx-track-python
ms.openlocfilehash: 3eb3b3b015f401e872a879c46ec6f8c69df5f87f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455420"
---
# <a name="azure-functions-python-developer-guide"></a>Przewodnik dewelopera w języku Python Azure Functions

Ten artykuł stanowi wprowadzenie do tworzenia Azure Functions przy użyciu języka Python. W poniższej zawartości przyjęto założenie, że został już odczytany [Przewodnik deweloperów Azure Functions](functions-reference.md).

Jako deweloper języka Python może być również interesujący jeden z następujących artykułów:

| Wprowadzenie | Pojęcia| Scenariusze/przykłady |
| -- | -- | -- | 
| <ul><li>[Funkcja języka Python wykorzystująca Visual Studio Code](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)</li><li>[Funkcja w języku Python z terminalem/wierszem polecenia](./create-first-function-cli-csharp.md?pivots=programming-language-python)</li></ul> | <ul><li>[Przewodnik dla deweloperów](functions-reference.md)</li><li>[Opcje hostingu](functions-scale.md)</li><li>[&nbsp;Zagadnienia dotyczące wydajności](functions-best-practices.md)</li></ul> | <ul><li>[Klasyfikacja obrazów za pomocą platformy PyTorch](machine-learning-pytorch.md)</li><li>[Przykład usługi Azure Automation](/samples/azure-samples/azure-functions-python-list-resource-groups/azure-functions-python-sample-list-resource-groups/)</li><li>[Uczenie maszynowe przy użyciu biblioteki TensorFlow](functions-machine-learning-tensorflow.md)</li><li>[Przeglądaj przykłady w języku Python](/samples/browse/?products=azure-functions&languages=python)</li></ul> |

> [!NOTE]
> Chociaż można [opracowywać Azure Functions oparte na języku Python lokalnie w systemie Windows](create-first-function-vs-code-python.md#run-the-function-locally), Język Python jest obsługiwany tylko w planie hostingu opartym na systemem Linux podczas uruchamiania na platformie Azure. Zapoznaj się z listą obsługiwanych kombinacji [systemu operacyjnego/środowiska uruchomieniowego](functions-scale.md#operating-systemruntime) .

## <a name="programming-model"></a>Model programowania

Azure Functions oczekuje, że funkcja będzie metodą bezstanową w skrypcie języka Python, która przetwarza dane wejściowe i generuje dane wyjściowe. Domyślnie środowisko uruchomieniowe oczekuje metody, która ma być zaimplementowana jako metoda globalna wywołana `main()` w `__init__.py` pliku. Można również [określić alternatywny punkt wejścia](#alternate-entry-point).

Dane z wyzwalaczy i powiązań są powiązane z funkcją za pośrednictwem atrybutów metody przy użyciu `name` właściwości zdefiniowanej w *function.jsw* pliku. Na przykład na poniższym  _function.js_ opisano prostą funkcję wyzwalaną przez żądanie HTTP o nazwie `req` :

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

W oparciu o tę definicję `__init__.py` plik zawierający kod funkcji może wyglądać podobnie jak w poniższym przykładzie:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Można również jawnie zadeklarować typy atrybutów i zwracany typ w funkcji przy użyciu adnotacji typu języka Python. Dzięki temu można korzystać z funkcji IntelliSense i autouzupełniania dostarczonych przez wiele edytorów kodu w języku Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Użyj adnotacji w języku Python zawartych w pakiecie [Azure. Functions. *](/python/api/azure-functions/azure.functions) , aby powiązać dane wejściowe i wyjściowe z metodami.

## <a name="alternate-entry-point"></a>Alternatywny punkt wejścia

Domyślne zachowanie funkcji można zmienić, opcjonalnie określając `scriptFile` `entryPoint` właściwości i w *function.js* pliku. Na przykład _function.jsna_ poniższym nakazuje środowisko uruchomieniowe użycie `customentry()` metody w pliku _Main.py_ jako punktu wejścia dla funkcji platformy Azure.

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

Zalecana struktura folderów dla projektu funkcji w języku Python wygląda następująco:

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

* *local.settings.js*: służy do przechowywania ustawień aplikacji i parametrów połączenia podczas lokalnego uruchamiania. Ten plik nie jest publikowany na platformie Azure. Aby dowiedzieć się więcej, zobacz [Local. Settings. File](functions-run-local.md#local-settings-file).
* *requirements.txt*: zawiera listę pakietów języka Python instalowanych przez system podczas publikowania na platformie Azure.
* *host.js*: zawiera globalne opcje konfiguracji, które mają wpływ na wszystkie funkcje w aplikacji funkcji. Ten plik jest publikowany na platformie Azure. Nie wszystkie opcje są obsługiwane w przypadku uruchamiania lokalnego. Aby dowiedzieć się więcej, zobacz [host.json](functions-host-json.md).
* *. programu vscode/*: (opcjonalnie) zawiera konfigurację programu vscode magazynu. Aby dowiedzieć się więcej, zobacz [ustawienie programu vscode](https://code.visualstudio.com/docs/getstarted/settings).
* *. venv/*: (opcjonalnie) zawiera środowisko wirtualne języka Python używane przez lokalne projektowanie.
* *Pliku dockerfile*: (opcjonalnie) używany podczas publikowania projektu w [kontenerze niestandardowym](functions-create-function-linux-custom-image.md).
* *testy/*: (opcjonalne) zawierają przypadki testowe aplikacji funkcji.
* *. funcignore*: (opcjonalnie) deklaruje pliki, które nie powinny zostać opublikowane na platformie Azure. Zazwyczaj ten plik zawiera `.vscode/` Ignorowanie ustawienia edytora, ignorowanie `.venv/` lokalnego środowiska wirtualnego języka Python, `tests/` Ignorowanie przypadków testowych i `local.settings.json` zapobieganie publikowaniu lokalnych ustawień aplikacji.

Każda funkcja ma własny plik kodu i plik konfiguracji powiązania (function.json).

Podczas wdrażania projektu w aplikacji funkcji na platformie Azure, cała zawartość folderu głównego projektu (*<project_root>*) powinna być uwzględniona w pakiecie, ale nie do samego folderu, co oznacza, `host.json` że musi znajdować się w katalogu głównym pakietu. Zalecane jest, aby zachować testy w folderze wraz z innymi funkcjami, w tym przykładzie `tests/` . Aby uzyskać więcej informacji, zobacz [testowanie jednostkowe](#unit-testing).

## <a name="import-behavior"></a>Zachowanie importowania

Moduły w kodzie funkcji można zaimportować przy użyciu odwołań bezwzględnych i względnych. W oparciu o pokazaną powyżej strukturę folderów, następujące Importy pracują z pliku funkcji *<project_root> \Moje \_ Pierwsza \_ Funkcja \\ _ \_ init \_ \_ . PR*:

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
>  *Shared_code/* folder musi zawierać \_ \_ \_ \_ plik init. PR, aby oznaczyć go jako pakiet języka Python, gdy używana jest składnia importu bezwzględnego.

Następująca \_ \_ \_ \_ importowana i niezgodna z importem względnym najwyższego poziomu jest przestarzała, ponieważ nie jest obsługiwana przez narzędzie sprawdzania typu statycznego i nie jest obsługiwana przez platformy testowe języka Python:

```python
from __app__.shared_code import my_first_helper_function #(deprecated __app__ import)
```

```python
from ..shared_code import my_first_helper_function #(deprecated beyond top-level relative import)
```

## <a name="triggers-and-inputs"></a>Wyzwalacze i dane wejściowe

Dane wejściowe są podzielone na dwie kategorie w Azure Functions: Wyzwól dane wejściowe i dodatkowe. Chociaż różnią się one w `function.json` pliku, użycie jest identyczne w kodzie języka Python.  Parametry połączenia lub wpisy tajne dla wyzwalacza i źródła danych wejściowych są mapowane na wartości w `local.settings.json` pliku podczas uruchamiania lokalnego oraz ustawienia aplikacji podczas uruchamiania na platformie Azure.

Na przykład poniższy kod ilustruje różnicę między tymi dwoma:

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

Gdy funkcja jest wywoływana, żądanie HTTP jest przesyłane do funkcji jako `req` . Wpis zostanie pobrany z usługi Azure Blob Storage w oparciu o _Identyfikator_ w adresie URL trasy i udostępniony jako `obj` w treści funkcji.  W tym miejscu jest określone konto magazynu z parametrami połączenia znalezionymi w ustawieniu aplikacji AzureWebJobsStorage, które jest tym samym kontem magazynu, które jest używane przez aplikację funkcji.


## <a name="outputs"></a>Dane wyjściowe

Dane wyjściowe można wyrazić zarówno w wartości zwracanej, jak i wyjściowych. Jeśli istnieje tylko jedno wyjście, zalecamy użycie wartości zwracanej. W przypadku wielu wyjść należy użyć parametrów wyjściowych.

Aby użyć wartości zwracanej funkcji jako wartości powiązania danych wyjściowych, `name` właściwość powiązania powinna być ustawiona na wartość `$return` w `function.json` .

Aby utworzyć wiele wyjść, użyj `set()` metody dostarczonej przez [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out) interfejs do przypisania wartości do powiązania. Na przykład następująca funkcja może wypchnąć komunikat do kolejki i zwrócić odpowiedź HTTP.

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

Dostęp do rejestratora środowiska uruchomieniowego Azure Functions jest dostępny za pośrednictwem głównego [`logging`](https://docs.python.org/3/library/logging.html#module-logging) programu obsługi w aplikacji funkcji. Ten rejestrator jest powiązany z Application Insights i pozwala na Flagowanie ostrzeżeń i błędów napotkanych podczas wykonywania funkcji.

Poniższy przykład rejestruje komunikat informacyjny, gdy funkcja jest wywoływana za pośrednictwem wyzwalacza HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Dostępne są dodatkowe metody rejestrowania umożliwiające zapisanie w konsoli na różnych poziomach śledzenia:

| Metoda                 | Opis                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Zapisuje komunikat o poziomie KRYTYCZNym w rejestratorze głównym.  |
| **`error(_message_)`**   | Zapisuje komunikat z BŁĘDem poziomu w rejestratorze głównym.    |
| **`warning(_message_)`**    | Zapisuje komunikat z OSTRZEŻENIEm dotyczącym poziomu rejestratora głównego.  |
| **`info(_message_)`**    | Zapisuje komunikat z informacjami o poziomie w rejestratorze głównym.  |
| **`debug(_message_)`** | Zapisuje komunikat z DEBUGOWANIEm na poziomie w rejestratorze głównym.  |

Aby dowiedzieć się więcej o rejestrowaniu, zobacz [Monitor Azure Functions](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Wyzwalacz i powiązania HTTP

Wyzwalacz HTTP jest zdefiniowany w function.jspliku. `name`Powiązanie musi być zgodne z nazwanym parametrem w funkcji.
W poprzednich przykładach jest używana nazwa powiązania `req` . Ten parametr jest obiektem [HttpRequest] i zwracany jest obiekt [HttpResponse] .

Z obiektu [HttpRequest] można uzyskać nagłówki żądań, parametry zapytania, parametry tras i treść wiadomości.

Poniższy przykład pochodzi z [szablonu wyzwalacza http dla języka Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python).

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

W tej funkcji wartość `name` parametru zapytania jest uzyskiwana z `params` parametru obiektu [HttpRequest] . Treść komunikatu zakodowanego w formacie JSON jest odczytywana przy użyciu `get_json` metody.

Analogicznie, można ustawić `status_code` i `headers` dla komunikatu odpowiedzi w zwracanym obiekcie [HttpResponse] .

## <a name="scaling-and-performance"></a>Skalowanie i wydajność

Najlepsze rozwiązania dotyczące skalowania i wydajności aplikacji funkcji języka Python można znaleźć w artykule dotyczącym [skalowania i wydajności języka Python](python-scale-performance-reference.md).

## <a name="context"></a>Kontekst

Aby uzyskać kontekst wywołania funkcji podczas wykonywania, należy uwzględnić [`context`](/python/api/azure-functions/azure.functions.context) argument w jego podpisie.

Na przykład:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

Klasa [**kontekstu**](/python/api/azure-functions/azure.functions.context) ma następujące atrybuty ciągu:

`function_directory` Katalog, w którym działa funkcja.

`function_name` Nazwa funkcji.

`invocation_id` Identyfikator bieżącego wywołania funkcji.

## <a name="global-variables"></a>Zmienne globalne

Nie ma gwarancji, że stan aplikacji zostanie zachowany do przyszłych wykonań. Jednak środowisko uruchomieniowe Azure Functions często używa tego samego procesu dla wielu wykonań tej samej aplikacji. Aby buforować wyniki kosztownych obliczeń, należy zadeklarować ją jako zmienną globalną.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Zmienne środowiskowe

W funkcjach, [Ustawienia aplikacji](functions-app-settings.md), takie jak parametry połączenia usługi, są ujawniane jako zmienne środowiskowe podczas wykonywania. Możesz uzyskać dostęp do tych ustawień `import os` , deklarując, a następnie używając, `setting = os.environ["setting-name"]` .

Poniższy przykład pobiera [ustawienie aplikacji](functions-how-to-use-azure-function-app-settings.md#settings)z kluczem o nazwie `myAppSetting` :

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

W przypadku lokalnego tworzenia ustawień aplikacji są [obsługiwane w local.settings.jspliku](functions-run-local.md#local-settings-file).

## <a name="python-version"></a>Wersja języka Python

Azure Functions obsługuje następujące wersje języka Python:

| Wersja funkcji | Wersje języka Python <sup>*</sup> |
| ----- | ----- |
| wersji | 3,9 (wersja zapoznawcza) <br/> 3,8<br/>3.7<br/>3,6 |
| 2.x | 3.7<br/>3,6 |

<sup>*</sup>Oficjalne dystrybucje CPython

Aby zażądać określonej wersji języka Python podczas tworzenia aplikacji funkcji na platformie Azure, użyj `--runtime-version` opcji [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) polecenia. Wersja środowiska uruchomieniowego funkcji jest ustawiana za pomocą `--functions-version` opcji. Wersja języka Python jest ustawiana podczas tworzenia aplikacji funkcji i nie można jej zmienić.

W przypadku uruchamiania lokalnego środowisko uruchomieniowe korzysta z dostępnej wersji języka Python.

## <a name="package-management"></a>Zarządzanie pakietami

Podczas programowania lokalnego przy użyciu Azure Functions Core Tools lub Visual Studio Code Dodaj nazwy i wersje wymaganych pakietów do `requirements.txt` pliku i zainstaluj je za pomocą polecenia `pip` .

Na przykład można użyć następującego pliku wymagań i polecenia PIP, aby zainstalować `requests` pakiet z PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publikowanie na platformie Azure

Gdy wszystko będzie gotowe do opublikowania, upewnij się, że wszystkie dostępne publicznie zależności są wymienione w pliku requirements.txt, który znajduje się w katalogu głównym katalogu projektu.

Pliki projektu i foldery, które są wykluczone z publikowania, łącznie z folderem środowiska wirtualnego, są wymienione w pliku. funcignore.

Istnieją trzy akcje kompilacji, które są obsługiwane w przypadku publikowania projektu w języku Python na platformie Azure: kompilacja zdalna, lokalna kompilacja i kompilacje korzystające z zależności niestandardowych.

Możesz również użyć Azure Pipelines do kompilowania zależności i publikowania przy użyciu ciągłego dostarczania (CD). Aby dowiedzieć się więcej, zobacz [dostarczanie ciągłe przy użyciu usługi Azure DevOps](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Kompilacja zdalna

W przypadku korzystania z kompilacji zdalnej zależności przywrócone na serwerze i natywnych zależnościach są zgodne ze środowiskiem produkcyjnym. Powoduje to przekazanie mniejszego pakietu wdrożeniowego. Użyj kompilacji zdalnej podczas tworzenia aplikacji w języku Python w systemie Windows. Jeśli projekt ma zależności niestandardowe, można [użyć zdalnej kompilacji z dodatkowym adresem URL indeksu](#remote-build-with-extra-index-url).

Zależności są uzyskiwane zdalnie na podstawie zawartości pliku requirements.txt. [Zdalna kompilacja](functions-deployment-technologies.md#remote-build) jest zalecaną metodą kompilacji. Domyślnie Azure Functions Core Tools żąda kompilacji zdalnej, gdy do publikowania projektu języka Python na platformie Azure jest używane następujące polecenie [Func Azure functionapp Publish](functions-run-local.md#publish) .

```bash
func azure functionapp publish <APP_NAME>
```

Pamiętaj, aby zamienić na `<APP_NAME>` nazwę aplikacji funkcji na platformie Azure.

[Rozszerzenie Azure Functions dla Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) również domyślnie żąda kompilacji zdalnej.

### <a name="local-build"></a>Lokalna kompilacja

Zależności są uzyskiwane lokalnie na podstawie zawartości pliku requirements.txt. Można zapobiec wykonywaniu zdalnej kompilacji przy użyciu następującego polecenia [Func usługi Azure functionapp Publish](functions-run-local.md#publish) do publikowania z lokalną kompilacją.

```command
func azure functionapp publish <APP_NAME> --build local
```

Pamiętaj, aby zamienić na `<APP_NAME>` nazwę aplikacji funkcji na platformie Azure.

Przy użyciu `--build local` opcji zależności projektu są odczytywane z pliku requirements.txt, a pakiety zależne są pobierane i instalowane lokalnie. Pliki i zależności projektu są wdrażane z komputera lokalnego na platformie Azure. Powoduje to przekazanie większego pakietu wdrożeniowego do platformy Azure. Jeśli z jakiegoś powodu nie można uzyskać zależności w pliku requirements.txt za pomocą podstawowych narzędzi, należy użyć opcji zależności niestandardowe do opublikowania.

Nie zalecamy używania kompilacji lokalnych podczas tworzenia lokalnie w systemie Windows.

### <a name="custom-dependencies"></a>Zależności niestandardowe

Jeśli projekt nie został znaleziony w [indeksie pakietu języka Python](https://pypi.org/), istnieją dwa sposoby kompilowania projektu. Metoda kompilacji zależy od sposobu kompilowania projektu.

#### <a name="remote-build-with-extra-index-url"></a>Zdalna kompilacja z dodatkowym adresem URL indeksu

Jeśli pakiety są dostępne z dostępnego niestandardowego indeksu pakietu, użyj kompilacji zdalnej. Przed opublikowaniem upewnij się, że [utworzono ustawienie aplikacji](functions-how-to-use-azure-function-app-settings.md#settings) o nazwie `PIP_EXTRA_INDEX_URL` . Wartość tego ustawienia jest adresem URL niestandardowego indeksu pakietu. Użycie tego ustawienia instruuje zdalną kompilację do uruchomienia `pip install` przy użyciu `--extra-index-url` opcji. Aby dowiedzieć się więcej, zobacz [dokumentację dotyczącą instalacji narzędzia Python PIP](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format).

Możesz również użyć poświadczeń uwierzytelniania podstawowego z dodatkowymi adresami URL indeksu pakietu. Aby dowiedzieć się więcej, zobacz [podstawowe poświadczenia uwierzytelniania](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) w dokumentacji języka Python.

#### <a name="install-local-packages"></a>Instalowanie pakietów lokalnych

Jeśli projekt używa pakietów, które nie są publicznie dostępne dla naszych narzędzi, możesz udostępnić je aplikacji, umieszczając je w \_ \_ \_ \_ katalogu aplikacji/.python_packages. Przed opublikowaniem Uruchom następujące polecenie, aby zainstalować zależności lokalnie:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

W przypadku korzystania z zależności niestandardowych należy użyć `--no-build` opcji publikowania, ponieważ zostały już zainstalowane zależności w folderze projektu.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Pamiętaj, aby zamienić na `<APP_NAME>` nazwę aplikacji funkcji na platformie Azure.

## <a name="unit-testing"></a>Testowanie jednostkowe

Funkcje w języku Python można testować podobnie jak w przypadku innych kodów języka Python przy użyciu standardowych platform testowania. W przypadku większości powiązań można utworzyć obiekt wejściowy imitacji, tworząc wystąpienie odpowiedniej klasy z `azure.functions` pakietu. Ponieważ [`azure.functions`](https://pypi.org/project/azure-functions/) pakiet nie jest od razu dostępny, pamiętaj, aby zainstalować go za pośrednictwem `requirements.txt` pliku zgodnie z opisem w powyższej sekcji [Zarządzanie pakietami](#package-management) .

Zrób *my_second_function* jak na przykład, poniżej przedstawiono test funkcji wyzwalanej przez protokół http:

Najpierw musimy utworzyć *<project_root>/my_second_function/function.jsw* pliku i zdefiniować tę funkcję jako wyzwalacz http.

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

Teraz możemy zaimplementować *my_second_function* i *shared_code. my _second_helper_function*.

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

Możemy zacząć pisać przypadki testowe dla naszego wyzwalacza http.

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

W `.venv` środowisku wirtualnym języka Python Zainstaluj ulubioną strukturę testów języka Python (np. `pip install pytest` ). Po prostu Uruchom, `pytest tests` Aby sprawdzić wynik testu.

## <a name="temporary-files"></a>Pliki tymczasowe

`tempfile.gettempdir()`Metoda zwraca folder tymczasowy, który jest w systemie Linux `/tmp` . Aplikacja może używać tego katalogu do przechowywania plików tymczasowych generowanych i używanych przez funkcje podczas wykonywania.

> [!IMPORTANT]
> Pliki zapisywane w katalogu tymczasowym nie są gwarantowane zachowywania się między wywołaniami. Podczas skalowania w poziomie pliki tymczasowe nie są współdzielone między wystąpieniami.

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

Zalecane jest, aby zachować testy w folderze innym niż folder projektu. Dzięki temu można wdrożyć kod testowy w aplikacji.

## <a name="preinstalled-libraries"></a>Wstępnie zainstalowane biblioteki

Istnieje kilka bibliotek, które są dostępne w środowisku uruchomieniowym funkcji języka Python.

### <a name="python-standard-library"></a>Standardowa biblioteka języka Python

Standardowa biblioteka języka Python zawiera listę wbudowanych modułów języka Python, które są dostarczane z każdą dystrybucją języka Python. Większość z tych bibliotek ułatwia dostęp do funkcji systemu, takich jak we/wy pliku. W systemach Windows te biblioteki są instalowane przy użyciu języka Python. W systemach opartych na systemie UNIX są one udostępniane przez kolekcje pakietów.

Aby wyświetlić szczegółowe informacje o liście tych bibliotek, Skorzystaj z poniższych linków:

* [Standardowa biblioteka języka Python 3,6](https://docs.python.org/3.6/library/)
* [Standardowa biblioteka języka Python 3,7](https://docs.python.org/3.7/library/)
* [Standardowa biblioteka języka Python 3,8](https://docs.python.org/3.8/library/)
* [Standardowa biblioteka języka Python 3,9](https://docs.python.org/3.9/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Azure Functions zależności procesów roboczych języka Python

Proces roboczy funkcji Python wymaga określonego zestawu bibliotek. Możesz również użyć tych bibliotek w swoich funkcjach, ale nie są one częścią standardu języka Python. Jeśli funkcje są zależne od dowolnej z tych bibliotek, mogą one nie być dostępne dla kodu podczas uruchamiania poza Azure Functions. Aby znaleźć szczegółową listę zależności w sekcji install, **należy \_ podać** w pliku [Setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) .

> [!NOTE]
> Jeśli requirements.txt aplikacji funkcji zawiera `azure-functions-worker` wpis, usuń go. Proces roboczy funkcji jest automatycznie zarządzany przez Azure Functions platformę, a firma Microsoft regularnie aktualizuje ją o nowe funkcje i poprawki błędów. Ręczne zainstalowanie starej wersji procesu roboczego w requirements.txt może spowodować nieoczekiwane problemy.

### <a name="azure-functions-python-library"></a>Azure Functions biblioteki języka Python

Każda aktualizacja procesu roboczego w języku Python obejmuje nową wersję [Azure Functions biblioteki języka Python (Azure. Functions)](https://github.com/Azure/azure-functions-python-library). Takie podejście ułatwia Ciągłe aktualizowanie aplikacji funkcji języka Python, ponieważ każda aktualizacja jest zgodna z poprzednimi wersjami. Listę wersji tej biblioteki można znaleźć w temacie [Azure-Functions PyPi](https://pypi.org/project/azure-functions/#history).

Wersja biblioteki środowiska uruchomieniowego jest ustalana przez platformę Azure i nie może zostać zastąpiona przez requirements.txt. `azure-functions`Wpis w requirements.txt jest tylko dla Zaznaczanie błędów i świadomości klientów.

Użyj poniższego kodu, aby śledzić rzeczywistą wersję biblioteki funkcji języka Python w środowisku uruchomieniowym:

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>Biblioteki systemowe środowiska uruchomieniowego

Aby zapoznać się z listą wstępnie zainstalowanych bibliotek systemowych w obrazach środowiska Docker procesu roboczego w języku Python, Skorzystaj z poniższych linków:

|  Środowisko uruchomieniowe funkcji  | Wersja Debian | Wersje języka Python |
|------------|------------|------------|
| Wersja 2. x | Stretch  | [Python 3,6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| Wersja 3. x | Buster | [Python 3,6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile)<br/> [Python 3,9](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python39/python39.Dockerfile)|

## <a name="cross-origin-resource-sharing"></a>Współużytkowanie zasobów między źródłami

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

Mechanizm CORS jest w pełni obsługiwany w przypadku aplikacji funkcji języka Python.

## <a name="known-issues-and-faq"></a>Znane problemy i często zadawane pytania

Poniżej znajduje się lista przewodników rozwiązywania problemów dotyczących typowych problemów:

* [ModuleNotFoundError i ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [Nie można zaimportować "cygrpc"](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

Wszystkie znane problemy i żądania funkcji są śledzone za pomocą listy [problemów usługi GitHub](https://github.com/Azure/azure-functions-python-worker/issues) . Jeśli napotkasz problem i nie możesz znaleźć problemu w usłudze GitHub, Otwórz nowy problem i podaj szczegółowy opis problemu.

## <a name="next-steps"></a>Następne kroki

Więcej informacji można znaleźć w następujących zasobach:

* [Dokumentacja interfejsu API pakietu Azure Functions](/python/api/azure-functions/azure.functions)
* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md)
* [Powiązania magazynu obiektów BLOB](functions-bindings-storage-blob.md)
* [Powiązania protokołu HTTP i elementu webhook](functions-bindings-http-webhook.md)
* [Kolejki powiązań magazynu](functions-bindings-storage-queue.md)
* [Wyzwalacz czasomierza](functions-bindings-timer.md)

[Masz problemy? Daj nam znać.](https://aka.ms/python-functions-ref-survey)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse