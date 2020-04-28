---
title: Praca z podstawowymi narzędziami usług Azure Functions
description: Dowiedz się, jak kodować i testować funkcje platformy Azure z wiersza polecenia lub terminala na komputerze lokalnym przed uruchomieniem ich w usłudze Azure Functions.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: 19691a654162ee3855cb257fd42e29d2e1fc0157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276662"
---
# <a name="work-with-azure-functions-core-tools"></a>Praca z podstawowymi narzędziami usług Azure Functions

Narzędzia Azure Functions Core Tools umożliwiają tworzenie i testowanie funkcji na komputerze lokalnym za pomocą wiersza polecenia lub terminala. Funkcje lokalne można połączyć się z usługami platformy Azure na żywo i można debugować swoje funkcje na komputerze lokalnym przy użyciu pełnego środowiska wykonawczego funkcji. Można nawet wdrożyć aplikację funkcji do subskrypcji platformy Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Tworzenie funkcji na komputerze lokalnym i publikowanie ich na platformie Azure przy użyciu narzędzi Core Tools wykonuje następujące podstawowe kroki:

> [!div class="checklist"]
> * [Zainstaluj podstawowe narzędzia i zależności.](#v2)
> * [Utwórz projekt aplikacji funkcji na podstawie szablonu specyficznego dla języka.](#create-a-local-functions-project)
> * [Zarejestruj rozszerzenia wyzwalania i powiązania.](#register-extensions)
> * [Zdefiniuj magazyn i inne połączenia.](#local-settings-file)
> * [Utwórz funkcję na podstawie wyzwalacza i szablonu specyficznego dla języka.](#create-func)
> * [Uruchom funkcję lokalnie.](#start)
> * [Opublikuj projekt na platformie Azure.](#publish)

## <a name="core-tools-versions"></a>Wersje narzędzi Core Tools

Istnieją trzy wersje narzędzi podstawowe usługi Azure Functions. Używana wersja zależy od lokalnego środowiska programistycznego, [wyboru języka](supported-languages.md)i wymaganego poziomu wsparcia:

+ **Wersja 1.x:** Obsługuje wersję 1.x środowiska wykonawczego usługi Azure Functions. Ta wersja narzędzi jest obsługiwana tylko na komputerach z systemem Windows i jest zainstalowana z [pakietu npm](https://www.npmjs.com/package/azure-functions-core-tools).

+ [**Wersja 2.x/3.x**](#v2): Obsługuje [wersję 2.x lub 3.x środowiska wykonawczego usługi Azure Functions](functions-versions.md). Te wersje obsługują [system Windows,](/azure/azure-functions/functions-run-local?tabs=windows#v2) [macOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)i [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2) oraz używają menedżerów pakietów specyficznych dla platformy lub npm do instalacji.

O ile nie zaznaczono inaczej, przykłady w tym artykule są dla wersji 3.x.

## <a name="install-the-azure-functions-core-tools"></a>Instalowanie podstawowych narzędzi usługi Azure Functions

[Podstawowe narzędzia azure functions zawiera] wersję tego samego środowiska wykonawczego, który zasila środowisko uruchomieniowe usługi Azure Functions, które można uruchomić na lokalnym komputerze deweloperskim. Zawiera również polecenia do tworzenia funkcji, łączenia się z platformą Azure i wdrażania projektów funkcji.

>[!IMPORTANT]
>Aby można było publikować na platformie Azure za pomocą podstawowych narzędzi azure functions, musi być zainstalowany system [interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)  

### <a name="version-2x-and-3x"></a><a name="v2"></a>Wersja 2.x i 3.x

Wersja 2.x/3.x narzędzi używa środowiska uruchomieniowego usługi Azure Functions, który jest zbudowany na .NET Core. Ta wersja jest obsługiwana na wszystkich platformach .NET Core obsługuje, w tym [Windows](/azure/azure-functions/functions-run-local?tabs=windows#v2), [macOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)i [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2). 

> [!IMPORTANT]
> Można pominąć wymagania dotyczące instalowania zestawu .NET Core SDK za pomocą [pakietów rozszerzeń].

# <a name="windows"></a>[Windows](#tab/windows)

W poniższych krokach użyj npm, aby zainstalować narzędzia Core Tools w systemie Windows. Można również użyć [Chocolatey](https://chocolatey.org/). Aby uzyskać więcej informacji, zobacz [program Readme Core Tools .](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows)

1. Zainstaluj [Node.js], który zawiera npm.
    - W przypadku wersji 2.x narzędzi obsługiwane są tylko wersje Node.js 8.5 i nowsze.
    - W przypadku wersji 3.x narzędzi obsługiwane są tylko wersje Node.js 10 i nowsze.

1. Zainstaluj pakiet Narzędzi Podstawowych:

    ##### <a name="v2x"></a>wersja 2.x

    ```cmd
    npm install -g azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>wersja 3.x

    ```cmd
    npm install -g azure-functions-core-tools@3
    ```

   Pobranie i zainstalowanie pakietu Core Tools może potrwać kilka minut.

1. Jeśli nie planujesz używać [pakietów rozszerzeń,]zainstaluj [zestaw .NET Core 2.x SDK dla systemu Windows](https://www.microsoft.com/net/download/windows).

# <a name="macos"></a>[macOS](#tab/macos)

Poniższe kroki za pomocą homebrew zainstalować narzędzia podstawowe w systemie macOS.

1. Zainstaluj [Homebrew](https://brew.sh/), jeśli nie jest jeszcze zainstalowany.

1. Zainstaluj pakiet Narzędzi Podstawowych:

    ##### <a name="v2x"></a>wersja 2.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>wersja 3.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```

# <a name="linux"></a>[Linux](#tab/linux)

Poniższe kroki wykorzystują [APT](https://wiki.debian.org/Apt) do zainstalowania podstawowych narzędzi w dystrybucji Systemu Linux Ubuntu/Debian. W przypadku innych dystrybucji linuksa zobacz [program Readme Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Zainstaluj klucz GPG repozytorium pakietów firmy Microsoft, aby sprawdzić integralność pakietu:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Przed wykonaniem aktualizacji APT należy skonfigurować listę źródeł programów .NET.

   Aby skonfigurować listę źródeł APT dla Ubuntu, uruchom to polecenie:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

   Aby skonfigurować listę źródeł APT dla Debiana, uruchom to polecenie:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Sprawdź `/etc/apt/sources.list.d/dotnetdev.list` plik dla jednego z odpowiednich ciągów wersji Systemu Linux wymienionych poniżej:

    | Dystrybucja Linuksa | Wersja |
    | --------------- | ----------- |
    | Debian 9 | `stretch` |
    | Debian 8 | `jessie` |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. Uruchom aktualizację źródła APT:

    ```bash
    sudo apt-get update
    ```

1. Zainstaluj pakiet Narzędzi Podstawowych:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

1. Jeśli nie planujesz używać [pakietów rozszerzeń,]zainstaluj [.NET Core 2.x SDK dla Linuksa](https://www.microsoft.com/net/download/linux).

---

## <a name="create-a-local-functions-project"></a>Tworzenie lokalnego projektu usługi Functions

Katalog projektu funkcji zawiera pliki [host.json](functions-host-json.md) i [local.settings.json](#local-settings-file), wraz z podfolderami, które zawierają kod dla poszczególnych funkcji. Ten katalog jest odpowiednikiem aplikacji funkcji na platformie Azure. Aby dowiedzieć się więcej o strukturze folderów Funkcji, zobacz [przewodnik dla deweloperów usług Azure Functions](functions-reference.md#folder-structure).

Wersja 2.x wymaga wybrania języka domyślnego dla projektu podczas jego inicjowania. W wersji 2.x wszystkie dodane funkcje używają domyślnych szablonów językowych. W wersji 1.x należy określić język przy każdym utworzeniu funkcji.

W oknie terminala lub w wierszu polecenia uruchom następujące polecenie, aby utworzyć projekt i lokalne repozytorium Git:

```
func init MyFunctionProj
```

Po podaniu nazwy projektu tworzony i inicjowany jest nowy folder o tej nazwie. W przeciwnym razie bieżący folder zostanie zainicjowany.  
W wersji 2.x po uruchomieniu polecenia należy wybrać środowisko wykonawcze dla projektu. 

<pre>
Select a worker runtime:
dotnet
node
python 
powershell
</pre>

Użyj klawiszy strzałek w górę/w dół, aby wybrać język, a następnie naciśnij klawisz Enter. Jeśli planujesz tworzyć funkcje JavaScript lub TypeScript, wybierz **węzeł**, a następnie wybierz język. TypeScript ma [pewne dodatkowe wymagania](functions-reference-node.md#typescript). 

Dane wyjściowe wyglądają jak w następującym przykładzie dla projektu JavaScript:

<pre>
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
</pre>

`func init`obsługuje następujące opcje, które są tylko w wersji 2.x, chyba że zaznaczono inaczej:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--csharp`**<br/> **`--dotnet`** | Inicjuje [projekt biblioteki klas C# (cs).](functions-dotnet-class-library.md) |
| **`--csx`** | Inicjuje [projekt skryptu C# (csx).](functions-reference-csharp.md) Należy określić `--csx` w kolejnych poleceniach. |
| **`--docker`** | Utwórz plik docker dla kontenera przy użyciu obrazu podstawowego opartego na wybranym `--worker-runtime`pliku . Użyj tej opcji, gdy planujesz opublikować do niestandardowego kontenera systemu Linux. |
| **`--docker-only`** |  Dodaje plik dockerfile do istniejącego projektu. Monituje o środowisko uruchomieniowe procesu roboczego, jeśli nie określono lub nie jest ustawionych w local.settings.json. Użyj tej opcji, gdy planujesz opublikować istniejący projekt do niestandardowego kontenera systemu Linux. |
| **`--force`** | Inicjuj projekt nawet wtedy, gdy istnieją istniejące pliki w projekcie. To ustawienie zastępuje istniejące pliki o tej samej nazwie. Nie ma to wpływu na inne pliki w folderze projektu. |
| **`--java`**  | Inicjuje [projekt Java](functions-reference-java.md). |
| **`--javascript`**<br/>**`--node`**  | Inicjuje [projekt JavaScript](functions-reference-node.md). |
| **`--no-source-control`**<br/>**`-n`** | Zapobiega domyślnemu tworzeniu repozytorium Git w wersji 1.x. W wersji 2.x repozytorium git nie jest tworzone domyślnie. |
| **`--powershell`**  | Inicjuje [projekt programu PowerShell](functions-reference-powershell.md). |
| **`--python`**  | Inicjuje [projekt języka Python](functions-reference-python.md). |
| **`--source-control`** | Określa, czy tworzone jest repozytorium git. Domyślnie repozytorium nie jest tworzone. Kiedy `true`tworzone jest repozytorium. |
| **`--typescript`**  | Inicjuje [projekt TypeScript](functions-reference-node.md#typescript). |
| **`--worker-runtime`** | Ustawia środowisko uruchomieniowe języka dla projektu. Obsługiwane `csharp`wartości to: `dotnet` `java`, `javascript``node` , , `powershell`, `python`(JavaScript), , , i `typescript`. Gdy nie jest ustawiona, zostanie wyświetlony monit o wybranie środowiska wykonawczego podczas inicjowania. |

> [!IMPORTANT]
> Domyślnie wersja 2.x podstawowych narzędzi tworzy projekty aplikacji funkcji dla środowiska uruchomieniowego .NET jako [projekty klasy C#](functions-dotnet-class-library.md) (csproj). Te projekty języka C#, które mogą być używane z programem Visual Studio lub Visual Studio Code, są kompilowane podczas testowania i podczas publikowania na platformie Azure. Jeśli zamiast tego chcesz utworzyć i pracować z tymi samymi plikami skryptu C# (csx) utworzonymi `--csx` w wersji 1.x i w portalu, należy dołączyć parametr podczas tworzenia i wdrażania funkcji.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Domyślnie te ustawienia nie są migrowane automatycznie po opublikowaniu projektu na platformie Azure. Użyj `--publish-local-settings` przełącznika [podczas publikowania,](#publish) aby upewnić się, że te ustawienia są dodawane do aplikacji funkcji na platformie Azure. Należy zauważyć, że wartości w **ConnectionStrings** nigdy nie są publikowane.

Wartości ustawień aplikacji funkcji można również odczytać w kodzie jako zmienne środowiskowe. Aby uzyskać więcej informacji, zobacz sekcję Zmienne środowiskowe tych tematów referencyjnych specyficznych dla języka:

* [Wstępnie skompilowany c#](functions-dotnet-class-library.md#environment-variables)
* [Skrypt języka C# (csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

Jeśli nie ustawiono prawidłowego [`AzureWebJobsStorage`] ciągu połączenia magazynu i emulator nie jest używany, wyświetlany jest następujący komunikat o błędzie:

> Brak wartości usługi AzureWebJobsStorage w local.settings.json. Jest to wymagane dla wszystkich wyzwalaczy innych niż HTTP. Możesz uruchomić 'func azure functionapp \<fetch-app-settings\>functionAppAppName ' lub określić parametry połączenia w local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Pobierz parametry połączenia magazynu

Nawet w przypadku korzystania z emulatora magazynu platformy Microsoft Azure do tworzenia, można przetestować z rzeczywistego połączenia magazynu. Zakładając, że konto magazynu zostało już [utworzone,](../storage/common/storage-create-storage-account.md)można uzyskać prawidłowy ciąg połączenia magazynu w jeden z następujących sposobów:

- W [witrynie Azure portal]wyszukaj i wybierz **pozycję Konta magazynu**. 
  ![Wybieranie kont magazynu z witryny Azure portal](./media/functions-run-local/select-storage-accounts.png)
  
  Wybierz konto magazynu, wybierz pozycję **Klawisze programu Access** w **ustawieniach,** a następnie skopiuj jedną z wartości **ciągu połączenia.**
  ![Kopiowanie ciągu połączenia z witryny Azure portal](./media/functions-run-local/copy-storage-connection-portal.png)

- Użyj [Eksploratora usługi Azure Storage,](https://storageexplorer.com/) aby połączyć się z kontem platformy Azure. W **Eksploratorze**rozwiń subskrypcję, rozwiń węzeł **Konta magazynu**, wybierz konto magazynu i skopiuj podstawowy lub pomocniczy ciąg połączenia.

  ![Kopiowanie ciągu połączenia z Eksploratora magazynu](./media/functions-run-local/storage-explorer.png)

+ Użyj narzędzi Core Tools, aby pobrać parametry połączenia z platformy Azure za pomocą jednego z następujących poleceń:

  + Pobierz wszystkie ustawienia z istniejącej aplikacji funkcji:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Pobierz ciąg połączenia dla określonego konta magazynu:

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Jeśli nie jesteś jeszcze zalogowany na platformie Azure, zostanie wyświetlony monit o to.

## <a name="create-a-function"></a><a name="create-func"></a>Tworzenie funkcji

Aby utworzyć funkcję, uruchom następujące polecenie:

```
func new
```

W wersji 2.x po `func new` uruchomieniu zostanie wyświetlony monit o wybranie szablonu w domyślnym języku aplikacji funkcyjnej, zostanie wyświetlony monit o wybranie nazwy funkcji. W wersji 1.x zostaniesz również poproszony o wybranie języka.

<pre>
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
</pre>

Kod funkcji jest generowany w podfolderze o podanej nazwie funkcji, jak widać w następujących danych wyjściowych wyzwalacza kolejki:

<pre>
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
</pre>

Można również określić te opcje w poleceniu, używając następujących argumentów:

| Argument     | Opis                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Wersja 2.x) Generuje te same szablony skryptu C# (csx) używane w wersji 1.x i w portalu. |
| **`--language`**, **`-l`**| Język programowania szablonu, taki jak C#, F#, lub JavaScript. Ta opcja jest wymagana w wersji 1.x. W wersji 2.x nie należy używać tej opcji ani wybierać języka, który odpowiada środowiska wykonawczemu procesu roboczego. |
| **`--name`**, **`-n`** | Nazwa funkcji. |
| **`--template`**, **`-t`** | Użyj `func templates list` polecenia, aby wyświetlić pełną listę dostępnych szablonów dla każdego obsługiwanego języka.   |

Na przykład, aby utworzyć wyzwalacz HTTP javascript w jednym poleceniu, uruchom:

```
func new --template "Http Trigger" --name MyHttpTrigger
```

Aby utworzyć funkcję wyzwalaną w kolejce w jednym poleceniu, uruchom:

```
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="run-functions-locally"></a><a name="start"></a>Uruchamianie funkcji lokalnie

Aby uruchomić projekt Functions, uruchom hosta Functions. Host włącza wyzwalacze dla wszystkich funkcji w projekcie. Polecenie start różni się w zależności od języka projektu.

# <a name="c"></a>[C\#](#tab/csharp)

```
func start --build
```
# <a name="javascript"></a>[JavaScript](#tab/node)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
To polecenie musi być [uruchamiane w środowisku wirtualnym](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv).

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> Wersja 1.x środowiska wykonawczego Functions `host` wymaga polecenia, jak w poniższym przykładzie:
>
> ```
> func host start
> ```

`func start`obsługuje następujące opcje:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Nie buduj bieżącego projektu przed uruchomieniem. Tylko dla projektów dotnet. Wartość domyślna jest ustawiona na false. Nie jest obsługiwana w wersji 1.x. |
| **`--cert`** | Ścieżka do pliku .pfx zawierającego klucz prywatny. Używane tylko `--useHttps`z . Nie jest obsługiwana w wersji 1.x. |
| **`--cors-credentials`** | Zezwalaj na uwierzytelnione żądania między źródłami (tj. pliki cookie i nagłówek uwierzytelniania) Nie jest obsługiwana w wersji 1.x. |
| **`--cors`** | Oddzielona przecinkami lista początków CORS bez spacji. |
| **`--language-worker`** | Argumenty, aby skonfigurować pracownika języka. Na przykład można włączyć debugowanie dla procesu roboczego języka, podając [port debugowania i inne wymagane argumenty](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers). Nie jest obsługiwana w wersji 1.x. |
| **`--nodeDebugPort`**, **`-n`** | Port debugera Node.js do użycia. Domyślnie: wartość z pliku launch.json lub 5858. Tylko wersja 1.x. |
| **`--password`** | Hasło lub plik zawierający hasło do pliku .pfx. Używane tylko `--cert`z . Nie jest obsługiwana w wersji 1.x. |
| **`--port`**, **`-p`** | Lokalny port do nasłuchiwać. Wartość domyślna: 7071. |
| **`--pause-on-error`** | Wstrzymaj, aby uzyskać dodatkowe dane wejściowe przed zamknięciem procesu. Używane tylko podczas uruchamiania narzędzi podstawowych ze zintegrowanego środowiska programistycznego (IDE).|
| **`--script-root`**, **`--prefix`** | Służy do określania ścieżki do katalogu głównego aplikacji funkcji, która ma zostać uruchomiony lub wdrożony. Jest to używane dla skompilowanych projektów, które generują pliki projektu do podfolderu. Na przykład podczas tworzenia projektu biblioteki klas języka C# pliki host.json, local.settings.json i *root* function.json są `MyProject/bin/Debug/netstandard2.0`generowane w głównym podfolderze ze ścieżką taką jak . W takim przypadku ustaw prefiks jako `--script-root MyProject/bin/Debug/netstandard2.0`. Jest to katalog główny aplikacji funkcji podczas uruchamiania na platformie Azure. |
| **`--timeout`**, **`-t`** | Limit czasu uruchomienia hosta funkcji w sekundach. Domyślnie: 20 sekund.|
| **`--useHttps`** | Powiązanie, `https://localhost:{port}` `http://localhost:{port}`a nie do . Domyślnie ta opcja tworzy zaufany certyfikat na komputerze.|

Po uruchomieniu hosta functions wyprowadza adres URL funkcji wyzwalanych przez HTTP:

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>Podczas uruchamiania lokalnie autoryzacja nie jest wymuszana dla punktów końcowych HTTP. Oznacza to, że wszystkie lokalne `authLevel = "anonymous"`żądania HTTP są obsługiwane jako . Aby uzyskać więcej informacji, zobacz [artykuł o powiązaniach HTTP](functions-bindings-http-webhook-trigger.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Przekazywanie danych testowych do funkcji

Aby przetestować swoje funkcje lokalnie, należy [uruchomić hosta funkcji](#start) i wywołać punkty końcowe na serwerze lokalnym przy użyciu żądań HTTP. Punkt końcowy, który można wywołać zależy od typu funkcji.

>[!NOTE]
> Przykłady w tym temacie używają narzędzia cURL do wysyłania żądań HTTP z terminala lub wiersza polecenia. Za pomocą wybranego narzędzia można wysyłać żądania HTTP do serwera lokalnego. Narzędzie cURL jest domyślnie dostępne w systemach opartych na systemie Linux i Windows 10 budować 17063 i nowsze. W starszym systemie Windows należy najpierw pobrać i zainstalować [narzędzie cURL](https://curl.haxx.se/).

Aby uzyskać bardziej ogólne informacje na temat testowania funkcji, zobacz [Strategie testowania kodu w usłudze Azure Functions.](functions-test-a-function.md)

#### <a name="http-and-webhook-triggered-functions"></a>Funkcje wyzwalane przez http i webhook

Wywołanie następującego punktu końcowego do lokalnego uruchamiania funkcji wyzwalanych http i elementu webhook:

    http://localhost:{port}/api/{function_name}

Upewnij się, że używasz tej samej nazwy serwera i portu, na który nasłuchuje host functions. Widać to w danych wyjściowych generowanych podczas uruchamiania hosta funkcji. Ten adres URL można wywołać przy użyciu dowolnej metody HTTP obsługiwanej przez wyzwalacz.

Następujące polecenie cURL wyzwala `MyHttpTrigger` funkcję szybkiego startu z żądania GET z parametrem _name_ przekazanym w ciągu zapytania.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

Poniższy przykład jest tą samą funkcją, która jest wywoływana z _nazwy_ przekazywania żądania POST w treści żądania:

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

Można wysyłać żądania GET z przeglądarki przekazując dane w ciągu zapytania. Dla wszystkich innych metod HTTP należy użyć cURL, Fiddler, Postman lub podobne narzędzie do testowania HTTP.

#### <a name="non-http-triggered-functions"></a>Funkcje wyzwalane przez inne niż HTTP

Dla wszystkich rodzajów funkcji innych niż wyzwalacze HTTP i webhooks i wyzwalacze siatki zdarzeń, można przetestować swoje funkcje lokalnie, wywołując punkt końcowy administracji. Wywołanie tego punktu końcowego z żądaniem HTTP POST na serwerze lokalnym wyzwala tę funkcję. 

Aby przetestować funkcje wyzwalane przez siatkę zdarzeń lokalnie, zobacz [Testowanie lokalne za pomocą aplikacji internetowej przeglądarki](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app).

Opcjonalnie można przekazać dane testowe do wykonania w treści żądania POST. Ta funkcja jest podobna do karty **Test** w witrynie Azure portal.

Wywołanie następującego punktu końcowego administratora wyzwalać funkcje inne niż HTTP:

    http://localhost:{port}/admin/functions/{function_name}

Aby przekazać dane testowe do punktu końcowego administratora funkcji, należy podać dane w treści komunikatu żądania POST. Treść wiadomości musi mieć następujący format JSON:

```JSON
{
    "input": "<trigger_input>"
}
```

Wartość `<trigger_input>` zawiera dane w formacie oczekiwanym przez funkcję. Poniższy przykład cURL jest `QueueTriggerJS` POST do funkcji. W takim przypadku dane wejściowe jest ciągiem, który jest odpowiednikiem komunikatu, który ma zostać znaleziony w kolejce.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

#### <a name="using-the-func-run-command-version-1x-only"></a>Korzystanie `func run` z polecenia (tylko wersja 1.x)

>[!IMPORTANT]
> Polecenie `func run` jest obsługiwane tylko w wersji 1.x narzędzi. Aby uzyskać więcej informacji, zobacz temat [Jak kierować wersje środowiska uruchomieniowego usługi Azure Functions](set-runtime-version.md).

W wersji 1.x można również wywołać funkcję `func run <FunctionName>` bezpośrednio przy użyciu i podać dane wejściowe dla funkcji. To polecenie jest podobne do uruchamiania funkcji przy użyciu karty **Test** w witrynie Azure portal.

`func run`obsługuje następujące opcje:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--content`**, **`-c`** | Zawartość wbudowana. |
| **`--debug`**, **`-d`** | Przed uruchomieniem funkcji należy dołączyć debuger do procesu hosta.|
| **`--timeout`**, **`-t`** | Czas oczekiwania (w sekundach), aż lokalny host funkcji będzie gotowy.|
| **`--file`**, **`-f`** | Nazwa pliku do użycia jako zawartość.|
| **`--no-interactive`** | Nie monituje o wprowadzenie danych wejściowych. Przydatne w scenariuszach automatyzacji.|

Na przykład, aby wywołać funkcję wyzwalaną przez http i przekazać treść zawartości, uruchom następujące polecenie:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-to-azure"></a><a name="publish"></a>Publikowanie na platformie Azure

Podstawowe narzędzia usługi Azure Functions core obsługuje dwa typy wdrożeń: wdrażanie plików projektu funkcji bezpośrednio do aplikacji funkcji za pośrednictwem [narzędzia Zip Deploy](functions-deployment-technologies.md#zip-deploy) i [wdrażanie niestandardowego kontenera platformy Docker.](functions-deployment-technologies.md#docker-container) Musisz już [utworzyć aplikację funkcji w subskrypcji platformy Azure](functions-cli-samples.md#create), do której można wdrożyć kod. Projekty, które wymagają kompilacji powinny być budowane tak, aby można było wdrożyć pliki binarne.

>[!IMPORTANT]
>Aby można było publikować na platformie Azure na platformie Azure, musi być zainstalowany [plik CLI platformy Azure](/cli/azure/install-azure-cli) z podstawowego narzędzia.  

Folder projektu może zawierać pliki i katalogi specyficzne dla języka, które nie powinny być publikowane. Wykluczone elementy są wyświetlane w pliku funcignore w głównym folderze projektu.     

### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>Wdrażanie plików projektu

Aby opublikować kod lokalny w aplikacji funkcji `publish` na platformie Azure, użyj polecenia:

```
func azure functionapp publish <FunctionAppName>
```

To polecenie publikuje do istniejącej aplikacji funkcji na platformie Azure. Jeśli spróbujesz opublikować `<FunctionAppName>` w subskrypcji, zostanie wyświetlony błąd. Aby dowiedzieć się, jak utworzyć aplikację funkcji z wiersza polecenia lub okna terminala przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [Tworzenie aplikacji funkcji do wykonywania bezserwerowego](./scripts/functions-cli-create-serverless.md). Domyślnie to polecenie używa [kompilacji zdalnej](functions-deployment-technologies.md#remote-build) i wdraża aplikację do [uruchamiania z pakietu wdrożeniowego](run-functions-from-deployment-package.md). Aby wyłączyć ten zalecany `--nozip` tryb wdrażania, użyj tej opcji.

>[!IMPORTANT]
> Podczas tworzenia aplikacji funkcji w witrynie Azure portal, domyślnie używa wersji 2.x środowiska wykonawczego funkcji. Aby aplikacja funkcji używać wersji 1.x środowiska wykonawczego, postępuj zgodnie z instrukcjami w Uruchom w [wersji 1.x](functions-versions.md#creating-1x-apps).
> Nie można zmienić wersji środowiska wykonawczego dla aplikacji funkcji, która ma istniejące funkcje.

W obu wersjach, 1.x i 2.x, obowiązują następujące opcje publikowania:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Publikowanie ustawień w local.settings.json na platformie Azure, monitując o zastąpienie, jeśli ustawienie już istnieje. Jeśli używasz emulatora magazynu platformy Microsoft Azure, najpierw zmień ustawienie aplikacji na [rzeczywiste połączenie magazynu](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Pomiń monit, aby zastąpić `--publish-local-settings -i` ustawienia aplikacji, gdy jest używany.|

Następujące opcje publikowania są obsługiwane tylko w wersji 2.x:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`**, **`-o`** |  Tylko publikować ustawienia i pominąć zawartość. Wartość domyślna to monit. |
|**`--list-ignored-files`** | Wyświetla listę plików, które są ignorowane podczas publikowania, która jest oparta na pliku funcignore. |
| **`--list-included-files`** | Wyświetla listę plików, które są publikowane, który jest oparty na pliku funcignore.Displays a list of files that are published, which is based on the .funcignore file. |
| **`--nozip`** | Wyłącza `Run-From-Package` tryb domyślny. |
| **`--build-native-deps`** | Pomija generowanie folderu .wheels podczas publikowania aplikacji funkcji Języka Python. |
| **`--build`**, **`-b`** | Wykonuje akcję kompilacji podczas wdrażania w aplikacji funkcji systemu Linux. Akceptuje: `remote` i `local`. |
| **`--additional-packages`** | Lista pakietów do zainstalowania podczas tworzenia natywnych zależności. Na przykład: `python3-dev libevent-dev`. |
| **`--force`** | Ignoruj weryfikację wstępnego publikowania w niektórych scenariuszach. |
| **`--csx`** | Publikowanie projektu skryptu języka C# (csx). |
| **`--no-build`** | Nie buduj funkcji biblioteki klas .NET. |
| **`--dotnet-cli-params`** | Podczas publikowania skompilowanych funkcji języka C# (csproj) podstawowe narzędzia wywołuje "dotnet build --output bin/publish". Wszystkie parametry przekazane do tego zostaną dołączone do wiersza polecenia. |

### <a name="deploy-custom-container"></a>Wdrażanie kontenera niestandardowego

Usługa Azure Functions umożliwia wdrożenie projektu funkcji w [niestandardowym kontenerze platformy Docker.](functions-deployment-technologies.md#docker-container) Aby uzyskać więcej informacji, zobacz [Tworzenie funkcji w systemie Linux przy użyciu niestandardowego obrazu](functions-create-function-linux-custom-image.md). Kontenery niestandardowe muszą mieć plik Dockerfile. Aby utworzyć aplikację z plikiem dockerfile, użyj opcji `func init`--dockerfile w programie .

```
func deploy
```

Dostępne są następujące opcje wdrażania kontenera niestandardowego:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Nazwa rejestru platformy Docker, do który zalogowano bieżącego użytkownika. |
| **`--platform`** | Platforma hostingowa dla aplikacji funkcyjnej. Prawidłowe opcje są`kubernetes` |
| **`--name`** | Nazwa aplikacji funkcji. |
| **`--max`**  | Opcjonalnie ustawia maksymalną liczbę wystąpień aplikacji funkcji do wdrożenia. |
| **`--min`**  | Opcjonalnie ustawia minimalną liczbę wystąpień aplikacji funkcji do wdrożenia. |
| **`--config`** | Ustawia opcjonalny plik konfiguracji wdrożenia. |

## <a name="monitoring-functions"></a>Funkcje monitorowania

Zalecanym sposobem monitorowania wykonywania funkcji jest integracja z usługą Azure Application Insights. Można również przesyłać strumieniowo dzienniki wykonywania do komputera lokalnego. Aby dowiedzieć się więcej, zobacz [Monitorowanie funkcji platformy Azure](functions-monitoring.md).

### <a name="application-insights-integration"></a>Integracja usługi Application Insights

Integracja usługi Application Insights powinna być włączona podczas tworzenia aplikacji funkcji na platformie Azure. Jeśli z jakiegoś powodu aplikacja funkcji nie jest połączony z wystąpieniem usługi Application Insights, można łatwo wykonać tę integrację w witrynie Azure portal. 

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

### <a name="enable-streaming-logs"></a>Włącz dzienniki przesyłania strumieniowego

Można wyświetlić strumień plików dziennika generowanych przez funkcje w sesji wiersza polecenia na komputerze lokalnym. 

#### <a name="native-streaming-logs"></a>Natywne dzienniki przesyłania strumieniowego

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Ten typ dzienników przesyłania strumieniowego wymaga włączenia integracji usługi Application Insights dla aplikacji funkcji.   


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak tworzyć, testować i publikować usługi Azure Functions przy użyciu narzędzi Azure Functions Core Tools, aby [firma Microsoft dowiedziała się, że moduł](https://docs.microsoft.com/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) Azure Functions Core Tools jest open source i [hostowany w usłudze GitHub.](https://github.com/azure/azure-functions-cli)  
Aby zgłosić żądanie błędu lub funkcji, [otwórz problem z gitHubem](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Podstawowe narzędzia funkcji platformy Azure]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
['AzureWebJobsStorage']: functions-app-settings.md#azurewebjobsstorage
[pakiety rozszerzeń]: functions-bindings-register.md#extension-bundles
