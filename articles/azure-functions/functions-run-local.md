---
title: Korzystanie z narzędzi Azure Functions Core Tools
description: Dowiedz się, jak kodować i testować usługi Azure Functions w wierszu polecenia lub terminalu na komputerze lokalnym przed uruchomieniem ich na Azure Functions.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: devx-track-csharp, 80e4ff38-5174-43
ms.openlocfilehash: 8dfc1471955a6d10199a078922151ff3aeda4294
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88929497"
---
# <a name="work-with-azure-functions-core-tools"></a>Korzystanie z narzędzi Azure Functions Core Tools

Narzędzia Azure Functions Core Tools umożliwiają tworzenie i testowanie funkcji na komputerze lokalnym z poziomu wiersza polecenia lub terminalu. Funkcje lokalne mogą łączyć się z aktywnymi usługami platformy Azure, a funkcje można debugować na komputerze lokalnym za pomocą środowiska uruchomieniowego Full Functions. Możesz nawet wdrożyć aplikację funkcji w ramach subskrypcji platformy Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Tworzenie funkcji na komputerze lokalnym i publikowanie ich na platformie Azure przy użyciu narzędzi podstawowych wykonuje następujące podstawowe czynności:

> [!div class="checklist"]
> * [Zainstaluj podstawowe narzędzia i zależności.](#v2)
> * [Utwórz projekt aplikacji funkcji na podstawie szablonu specyficznego dla języka.](#create-a-local-functions-project)
> * [Zarejestruj wyzwalacz i rozszerzenia powiązań.](#register-extensions)
> * [Zdefiniuj magazyn i inne połączenia.](#local-settings-file)
> * [Utwórz funkcję z wyzwalacza i szablonu specyficznego dla języka.](#create-func)
> * [Uruchom funkcję lokalnie.](#start)
> * [Opublikuj projekt na platformie Azure.](#publish)

## <a name="core-tools-versions"></a>Wersje narzędzi Core Tools

Istnieją trzy wersje Azure Functions Core Tools. Używana wersja zależy od lokalnego środowiska programistycznego, [wyboru języka](supported-languages.md)i wymaganego poziomu pomocy technicznej:

+ [**Wersja 3. x/2. x**](#v2): obsługuje [wersję 3. x lub 2. x środowiska uruchomieniowego Azure Functions](functions-versions.md). Te wersje obsługują [systemy Windows](?tabs=windows#v2), [macOS](?tabs=macos#v2)i [Linux](?tabs=linux#v2) oraz korzystają z menedżerów pakietów lub npm do instalacji.

+ **Wersja 1. x**: obsługuje wersję 1. x środowiska uruchomieniowego Azure Functions. Ta wersja narzędzi jest obsługiwana tylko na komputerach z systemem Windows i jest instalowana z [pakietu npm](https://www.npmjs.com/package/azure-functions-core-tools).

Można zainstalować tylko jedną wersję podstawowych narzędzi na danym komputerze. Jeśli nie określono inaczej, przykłady w tym artykule dotyczą wersji 3. x.

## <a name="prerequisites"></a>Wymagania wstępne

Azure Functions Core Tools obecnie zależy od interfejsu wiersza polecenia platformy Azure na potrzeby uwierzytelniania przy użyciu konta platformy Azure. Oznacza to, że musisz [zainstalować interfejs wiersza polecenia platformy Azure lokalnie](/cli/azure/install-azure-cli) , aby można było [publikować na platformie azure](#publish) na podstawie Azure Functions Core Tools. 

## <a name="install-the-azure-functions-core-tools"></a>Instalowanie podstawowych narzędzi usługi Azure Functions

[Azure Functions Core Tools] obejmuje wersję tego samego środowiska uruchomieniowego, która umożliwia Azure Functions środowisko uruchomieniowe, które można uruchomić na lokalnym komputerze deweloperskim. Udostępnia również polecenia służące do tworzenia funkcji, łączenia się z platformą Azure i wdrażania projektów funkcji.

### <a name="version-3x-and-2x"></a><a name="v2"></a>Wersja 3. x i 2. x

W wersji 3. x/2. x narzędzi jest używane środowisko uruchomieniowe Azure Functions, które jest oparte na platformie .NET Core. Ta wersja jest obsługiwana na wszystkich platformach .NET Core obsługuje, w tym [Windows](?tabs=windows#v2), [macOS](?tabs=macos#v2)i [Linux](?tabs=linux#v2). 

> [!IMPORTANT]
> Możesz pominąć wymóg instalacji zestaw .NET Core SDK przy użyciu [pakietów rozszerzeń].

# <a name="windows"></a>[Windows](#tab/windows)

Poniższe kroki służą do instalowania podstawowych narzędzi v3. x przy użyciu Instalatora Windows (MSI). Aby uzyskać więcej informacji na temat innych instalatorów opartych na pakiecie, które są wymagane do zainstalowania podstawowych narzędzi v2. x, zobacz [plik Readme podstawowych narzędzi](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Pobierz i uruchom Instalatora podstawowych narzędzi, w zależności od używanej wersji systemu Windows:

    - [v3. x-Windows 64-bit](https://go.microsoft.com/fwlink/?linkid=2135274) (zalecane. [Debugowanie Visual Studio Code](functions-develop-vs-code.md#debugging-functions-locally) wymaga 64-bitowej.)
    - [v3. x-Windows 32-bit](https://go.microsoft.com/fwlink/?linkid=2135275)

1. Jeśli nie planujesz używania [pakietów rozszerzeń](functions-bindings-register.md#extension-bundles), zainstaluj [zestaw SDK programu .NET Core 3. x dla systemu Windows](https://dotnet.microsoft.com/download).

# <a name="macos"></a>[macOS](#tab/macos)

Poniższe kroki używają oprogramowania homebrew, aby zainstalować podstawowe narzędzia na macOS.

1. Zainstaluj program [oprogramowania Homebrew](https://brew.sh/), jeśli nie jest jeszcze zainstalowany.

1. Zainstaluj pakiet podstawowych narzędzi:

    ##### <a name="v3x-recommended"></a>v3. x (zalecane)

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```
    
    ##### <a name="v2x"></a>v2. x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@2
    ```
    
1. Jeśli nie planujesz używania [pakietów rozszerzeń](functions-bindings-register.md#extension-bundles), zainstaluj [zestaw SDK .NET Core 3. x dla macOS](https://dotnet.microsoft.com/download).

# <a name="linux"></a>[Linux](#tab/linux)

Poniższe kroki używają [apt](https://wiki.debian.org/Apt) do instalowania podstawowych narzędzi w dystrybucji systemu Ubuntu/Debian Linux. Aby poznać inne dystrybucje systemu Linux, zobacz [plik Readme podstawowych narzędzi](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Zainstaluj klucz GPG repozytorium pakietów Microsoft, aby zweryfikować integralność pakietu:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Przed wykonaniem aktualizacji APT skonfiguruj listę źródeł APT.

    ##### <a name="ubuntu"></a>Ubuntu

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

    ##### <a name="debian"></a>Debian

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Sprawdź `/etc/apt/sources.list.d/dotnetdev.list` plik pod kątem jednego z odpowiednich ciągów wersji systemu Linux wymienionych poniżej:

    | Dystrybucja systemu Linux | Wersja |
    | --------------- | ----------- |
    | Debian 10 | `buster`  |
    | Debian 9  | `stretch` |
    | Ubuntu 20.04    | `focal`     |
    | Ubuntu 19.04    | `disco`     |
    | Ubuntu 18,10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux mennic 18    | `xenial`  |

1. Uruchom aktualizację źródła APT:

    ```bash
    sudo apt-get update
    ```

1. Zainstaluj pakiet podstawowych narzędzi:

    ##### <a name="v3x-recommended"></a>v3. x (zalecane)
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-3
    ```
    
    ##### <a name="v2x"></a>v2. x
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-2
    ```

1. Jeśli nie planujesz używania [pakietów rozszerzeń](functions-bindings-register.md#extension-bundles), zainstaluj program [.NET Core 3. x SDK dla systemu Linux](https://dotnet.microsoft.com/download).

---

## <a name="create-a-local-functions-project"></a>Tworzenie lokalnego projektu usługi Functions

Katalog projektu usługi Functions zawiera pliki [host.jswłączone](functions-host-json.md) i [local.settings.js](#local-settings-file), wraz z podfolderami zawierającymi kod dla poszczególnych funkcji. Ten katalog jest odpowiednikiem aplikacji funkcji na platformie Azure. Aby dowiedzieć się więcej na temat struktury folderu Functions, zobacz [Przewodnik po programie Azure Functions Developers](functions-reference.md#folder-structure).

Wersja 3. x/2. x wymaga wybrania języka domyślnego dla projektu, gdy jest on zainicjowany. W wersji 3. x/2. x wszystkie funkcje dodane używają szablonów języka domyślnego. W wersji 1. x należy określić język za każdym razem, gdy tworzysz funkcję.

W oknie terminalu lub w wierszu polecenia Uruchom następujące polecenie, aby utworzyć projekt i lokalne repozytorium git:

```
func init MyFunctionProj
```

>[!IMPORTANT]
> Środowisko Java używa Maven Archetype do tworzenia projektu funkcji lokalnych wraz z pierwszą funkcją wyzwalaną przez protokół HTTP. Aby utworzyć projekt Java, użyj następującego polecenia: `mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype` . Aby zapoznać się z przykładem za pomocą Maven Archetype, zobacz [wiersz polecenia szybki start](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java).  

Po podaniu nazwy projektu zostanie utworzony i zainicjowany nowy folder o tej nazwie. W przeciwnym razie bieżący folder zostanie zainicjowany.  
W wersji 3. x/2. x po uruchomieniu polecenia należy wybrać środowisko uruchomieniowe dla projektu. 

<pre>
Select a worker runtime:
dotnet
node
python 
powershell
</pre>

Użyj klawiszy strzałek w górę/w dół, aby wybrać język, a następnie naciśnij klawisz ENTER. Jeśli planujesz programowanie funkcji JavaScript lub TypeScript, wybierz **węzeł**, a następnie wybierz język. Język TypeScript ma [pewne dodatkowe wymagania](functions-reference-node.md#typescript). 

Dane wyjściowe wyglądają podobnie do następującego przykładu dla projektu JavaScript:

<pre>
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
</pre>

`func init` Program obsługuje następujące opcje, które są w wersji 3. x/2. x-Only, chyba że wskazano inaczej:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--csx`** | Tworzy funkcje .NET jako skrypt języka C#, który jest zachowaniem w wersji 1. x. Prawidłowy tylko z `--worker-runtime dotnet` . |
| **`--docker`** | Tworzy pliku dockerfile dla kontenera przy użyciu obrazu podstawowego, który jest oparty na wybranym `--worker-runtime` . Użyj tej opcji, jeśli planujesz publikowanie do niestandardowego kontenera systemu Linux. |
| **`--docker-only`** |  Dodaje pliku dockerfile do istniejącego projektu. Jeśli nie zostanie określony lub ustawiony w local.settings.jsna. Użyj tej opcji, jeśli planujesz opublikować istniejący projekt w niestandardowym kontenerze systemu Linux. |
| **`--force`** | Zainicjuj projekt nawet wtedy, gdy istnieją pliki w projekcie. To ustawienie zastępuje istniejące pliki o tej samej nazwie. Nie ma to wpływu na inne pliki w folderze projektu. |
| **`--language`** | Inicjuje projekt specyficzny dla języka. Obecnie obsługiwane, gdy jest `--worker-runtime` ustawiony na `node` . Dostępne opcje to `typescript` i `javascript` . Można również użyć `--worker-runtime javascript` lub `--worker-runtime typescript` .  |
| **`--managed-dependencies`**  | Instaluje zależności zarządzane. Obecnie tylko środowisko uruchomieniowe środowiska wykonawczego programu PowerShell obsługuje tę funkcję. |
| **`--source-control`** | Określa, czy tworzone jest repozytorium git. Domyślnie repozytorium nie jest tworzone. Gdy `true` jest tworzone repozytorium. |
| **`--worker-runtime`** | Ustawia środowisko uruchomieniowe języka dla projektu. Obsługiwane są następujące wartości: `csharp` , `dotnet` , `javascript` , `node` (JavaScript), `powershell` , `python` , i `typescript` . W przypadku języka Java należy użyć [Maven](functions-reference-java.md#create-java-functions). Gdy nie jest ustawiona, zostanie wyświetlony monit o wybranie środowiska uruchomieniowego podczas inicjowania. |
|
> [!IMPORTANT]
> Domyślnie wersja 2. x i nowsze wersje podstawowych narzędzi tworzy projekty aplikacji funkcji dla środowiska uruchomieniowego .NET jako [projekty klas C#](functions-dotnet-class-library.md) (. csproj). Te projekty w języku C#, które mogą być używane z programem Visual Studio lub Visual Studio Code, są kompilowane podczas testowania i podczas publikowania na platformie Azure. Jeśli zamiast tego chcesz utworzyć i korzystać z tych samych plików skryptu C# (. CSX) utworzonych w wersji 1. x i w portalu, musisz uwzględnić `--csx` parametr podczas tworzenia i wdrażania funkcji.

## <a name="register-extensions"></a>Rejestrowanie rozszerzeń

Z wyjątkiem wyzwalaczy HTTP i Timer, powiązania funkcji w środowisku uruchomieniowym w wersji 2. x i nowszych są implementowane jako pakiety rozszerzeń. Powiązania HTTP i wyzwalacze czasomierza nie wymagają rozszerzeń. 

Aby zmniejszyć niezgodności między różnymi pakietami rozszerzeń, funkcje umożliwiają odwołanie do pakietu rozszerzenia w host.jsw pliku projektu. Jeśli zdecydujesz się nie używać pakietów rozszerzeń, musisz również zainstalować lokalnie zestaw .NET Core 2. x SDK i zachować rozszerzenia. csproj z projektem funkcji.  

W wersji 2. x i większej niż środowisko uruchomieniowe Azure Functions należy jawnie zarejestrować rozszerzenia dla typów powiązań używanych w funkcjach. Można wybrać opcję instalacji rozszerzeń powiązań pojedynczo lub dodać odwołanie do pakietu rozszerzenia do host.jsw pliku projektu. Pakiety rozszerzeń usuwają prawdopodobieństwo problemów ze zgodnością pakietu w przypadku korzystania z wielu typów powiązań. Jest to zalecane podejście do rejestrowania rozszerzeń powiązań. Pakiety rozszerzeń również usuwają wymagania dotyczące instalowania zestawu SDK programu .NET Core 2. x. 

### <a name="use-extension-bundles"></a>Użyj pakietów rozszerzeń

[!INCLUDE [Register extensions](../../includes/functions-extension-bundles.md)]

Aby dowiedzieć się więcej, zobacz [Rejestrowanie rozszerzeń powiązań Azure Functions](functions-bindings-register.md#extension-bundles). Przed dodaniem powiązań do function.jsw pliku należy dodać pakiety rozszerzeń do host.js.

### <a name="explicitly-install-extensions"></a>Jawne Instalowanie rozszerzeń

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Domyślnie te ustawienia nie są migrowane automatycznie, gdy projekt jest publikowany na platformie Azure. Użyj `--publish-local-settings` przełącznika [podczas publikowania](#publish) , aby upewnić się, że te ustawienia są dodawane do aplikacji funkcji na platformie Azure. Należy pamiętać, że wartości w **connectionStrings** nigdy nie są publikowane.

Wartości ustawień aplikacji funkcji można także odczytać w kodzie jako zmienne środowiskowe. Aby uzyskać więcej informacji, zobacz sekcję zmienne środowiskowe w następujących tematach referencyjnych dotyczących języka:

* [Wstępnie skompilowany język C#](functions-dotnet-class-library.md#environment-variables)
* [Skrypt języka C# (csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [Program PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

Gdy nie ustawiono prawidłowych parametrów połączenia magazynu dla [`AzureWebJobsStorage`] i emulator nie jest używany, wyświetlany jest następujący komunikat o błędzie:

> Brak wartości elementu AzureWebJobsStorage w local.settings.json. Jest to wymagane dla wszystkich wyzwalaczy innych niż HTTP. Można uruchomić polecenie "Func Azure functionapp Fetch-App-Settings \<functionAppName\> " lub określić parametry połączenia w local.settings.jsna.

### <a name="get-your-storage-connection-strings"></a>Pobieranie parametrów połączenia magazynu

Nawet w przypadku używania emulator magazynu Microsoft Azure do programowania można testować przy użyciu rzeczywistego połączenia magazynu. Przy założeniu, że [konto magazynu](../storage/common/storage-account-create.md)zostało już utworzone, można uzyskać prawidłowe parametry połączenia magazynu w jeden z następujących sposobów:

- W [Azure Portal]Wyszukaj i wybierz pozycję **konta magazynu**. 
  ![Wybierz konta magazynu z Azure Portal](./media/functions-run-local/select-storage-accounts.png)
  
  Wybierz konto magazynu, wybierz pozycję **klucze dostępu** w obszarze **Ustawienia**, a następnie skopiuj jedną z wartości **parametrów połączenia** .
  ![Kopiuj parametry połączenia z Azure Portal](./media/functions-run-local/copy-storage-connection-portal.png)

- Użyj [Eksplorator usługi Azure Storage](https://storageexplorer.com/) , aby nawiązać połączenie z kontem platformy Azure. W **Eksploratorze**Rozwiń swoją subskrypcję, rozwiń węzeł **konta magazynu**, wybierz konto magazynu i skopiuj podstawowe lub pomocnicze parametry połączenia.

  ![Kopiuj parametry połączenia z Eksplorator usługi Storage](./media/functions-run-local/storage-explorer.png)

+ Użyj podstawowych narzędzi z katalogu głównego projektu, aby pobrać parametry połączenia z platformy Azure przy użyciu jednego z następujących poleceń:

  + Pobierz wszystkie ustawienia z istniejącej aplikacji funkcji:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```

  + Pobierz parametry połączenia dla określonego konta magazynu:

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Gdy użytkownik nie jest jeszcze zalogowany do platformy Azure, zostanie wyświetlony monit. Te polecenia zastępują wszystkie istniejące ustawienia w local.settings.jspliku. 

## <a name="create-a-function"></a><a name="create-func"></a>Tworzenie funkcji

Aby utworzyć funkcję, uruchom następujące polecenie:

```
func new
```

W wersji 3. x/2. x po uruchomieniu `func new` zostanie wyświetlony monit o wybranie szablonu w domyślnym języku aplikacji funkcji, a następnie zostanie wyświetlony monit o wybranie nazwy funkcji. W wersji 1. x jest również wyświetlany monit o wybranie języka.

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

Możesz również określić te opcje w poleceniu przy użyciu następujących argumentów:

| Argument     | Opis                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Wersja 2. x i nowsze wersje). Generuje te same szablony skryptów C# (. CSX), które są używane w wersji 1. x i w portalu. |
| **`--language`**, **`-l`**| Język programowania szablonu, taki jak C#, F # lub JavaScript. Ta opcja jest wymagana w wersji 1. x. W wersji 2. x i nowszych wersje nie należy używać tej opcji ani wybierać języka odpowiadającego środowisku uruchomieniowemu procesu roboczego. |
| **`--name`**, **`-n`** | Nazwa funkcji. |
| **`--template`**, **`-t`** | Użyj `func templates list` polecenia, aby wyświetlić pełną listę dostępnych szablonów dla każdego obsługiwanego języka.   |


Na przykład aby utworzyć wyzwalacz HTTP JavaScript w pojedynczym poleceniu, uruchom polecenie:

```
func new --template "Http Trigger" --name MyHttpTrigger
```

Aby utworzyć funkcję wyzwalaną przez kolejkę w pojedynczym poleceniu, uruchom polecenie:

```
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="run-functions-locally"></a><a name="start"></a>Uruchamianie funkcji lokalnie

Aby uruchomić projekt funkcji, uruchom hosta funkcji. Host włącza wyzwalacze dla wszystkich funkcji w projekcie. Polecenie uruchamiania różni się w zależności od języka projektu.

# <a name="c"></a>[S\#](#tab/csharp)

```
func start --build
```

# <a name="java"></a>[Java](#tab/java)

```
mvn clean package 
mvn azure-functions:run
```

# <a name="javascript"></a>[JavaScript](#tab/node)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
To polecenie musi być [uruchamiane w środowisku wirtualnym](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python#create-venv).

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> Wersja 1. x środowiska uruchomieniowego funkcji wymaga `host` polecenia, jak w poniższym przykładzie:
>
> ```
> func host start
> ```

`func start` Program obsługuje następujące opcje:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Nie wykonuj kompilacji bieżącego projektu przed uruchomieniem. Tylko w przypadku projektów dotnet. Wartość domyślna to false. Nieobsługiwane w wersji 1. x. |
| **`--cors-credentials`** | Zezwalaj na żądania uwierzytelniane między źródłami (np. pliki cookie i nagłówek uwierzytelniania) nie są obsługiwane w wersji 1. x. |
| **`--cors`** | Rozdzielana przecinkami lista źródeł CORS bez spacji. |
| **`--language-worker`** | Argumenty umożliwiające skonfigurowanie procesu roboczego języka. Na przykład można włączyć debugowanie dla procesu roboczego, dostarczając [port debugowania i inne wymagane argumenty](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers). Nieobsługiwane w wersji 1. x. |
| **`--cert`** | Ścieżka do pliku PFX, który zawiera klucz prywatny. Używany tylko z `--useHttps` . Nieobsługiwane w wersji 1. x. |
| **`--password`** | Hasło lub plik zawierający hasło dla pliku PFX. Używany tylko z `--cert` . Nieobsługiwane w wersji 1. x. |
| **`--port`**, **`-p`** | Port lokalny, na którym nasłuchuje. Wartość domyślna: 7071. |
| **`--pause-on-error`** | Wstrzymaj, aby uzyskać dodatkowe dane wejściowe przed wyjściem z procesu. Używane tylko w przypadku uruchamiania podstawowych narzędzi z zintegrowanego środowiska programistycznego (IDE).|
| **`--script-root`**, **`--prefix`** | Służy do określania ścieżki do katalogu głównego aplikacji funkcji, która ma być uruchamiana lub wdrażana. Służy do kompilowania projektów, które generują pliki projektu w podfolderze. Na przykład podczas kompilowania projektu biblioteki klas C#, host.json, local.settings.json i function.jsdla plików są generowane w podfolderze *głównym* z ścieżką taką jak `MyProject/bin/Debug/netstandard2.0` . W takim przypadku należy ustawić prefiks jako `--script-root MyProject/bin/Debug/netstandard2.0` . Jest to katalog główny aplikacji funkcji w przypadku uruchamiania na platformie Azure. |
| **`--timeout`**, **`-t`** | Limit czasu uruchamiania hosta usługi Functions (w sekundach). Wartość domyślna: 20 sekund.|
| **`--useHttps`** | Powiąż z, `https://localhost:{port}` a nie z `http://localhost:{port}` . Domyślnie ta opcja tworzy zaufany certyfikat na komputerze.|

Po uruchomieniu hosta funkcji wyświetla adres URL funkcji wyzwalanych przez protokół HTTP:

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>W przypadku uruchamiania lokalnego autoryzacja nie jest wymuszana dla punktów końcowych HTTP. Oznacza to, że wszystkie lokalne żądania HTTP są obsługiwane jako `authLevel = "anonymous"` . Aby uzyskać więcej informacji, zobacz [artykuł dotyczący powiązań http](functions-bindings-http-webhook-trigger.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Przekazywanie danych testowych do funkcji

Aby przetestować funkcje lokalnie, należy [uruchomić hosta funkcji](#start) i punkty końcowe wywołań na serwerze lokalnym przy użyciu żądań HTTP. Wywoływany punkt końcowy zależy od typu funkcji.

>[!NOTE]
> Przykłady w tym temacie używają narzędzia zwinięcie do wysyłania żądań HTTP z terminalu lub wiersza polecenia. Możesz użyć wybranego narzędzia, aby wysyłać żądania HTTP do serwera lokalnego. Narzędzie zwinięcie jest dostępne domyślnie w systemach z systemem Linux i Windows 10 Build 17063 i nowszych. W starszych systemach Windows należy najpierw pobrać i zainstalować [Narzędzie zwinięcie](https://curl.haxx.se/).

Aby uzyskać więcej ogólnych informacji na temat funkcji testowania, zapoznaj się z tematem [strategie testowania kodu w Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Funkcje wyzwalane przez protokół HTTP i element webhook

Przywołująsz następujący punkt końcowy do lokalnego uruchamiania funkcji HTTP i elementu webhook:

```
http://localhost:{port}/api/{function_name}
```

Upewnij się, że używasz tej samej nazwy serwera i portu, na którym nasłuchuje hosty funkcji. Ta wartość jest wyświetlana w danych wyjściowych generowanych podczas uruchamiania hosta funkcji. Możesz wywołać ten adres URL przy użyciu dowolnej metody HTTP obsługiwanej przez wyzwalacz.

Następujące polecenie zwinięcie wyzwala `MyHttpTrigger` funkcję szybkiego startu z żądania GET z parametrem _name_ przekazaną w ciągu zapytania.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

Poniższy przykład to ta sama funkcja wywołana z żądania _post w treści_ żądania:

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

Żądania GET z przeglądarki mogą przekazywać dane w ciągu zapytania. W przypadku wszystkich innych metod HTTP należy użyć zwinięcie, programu Fiddler, Poster lub podobnego narzędzia do testowania HTTP.

#### <a name="non-http-triggered-functions"></a>Funkcje wyzwalane inne niż HTTP

Dla wszystkich rodzajów funkcji innych niż wyzwalacze HTTP i elementy webhook i wyzwalacze Event Grid można testować funkcje lokalnie, wywołując punkt końcowy administracji. Wywołanie tego punktu końcowego przez żądanie HTTP POST na serwerze lokalnym wyzwala funkcję. 

Aby przetestować Event Grid funkcje wyzwalane lokalnie, zobacz [testowanie lokalne przy użyciu aplikacji internetowej przeglądarki](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app).

Opcjonalnie można przekazać dane testowe do wykonania w treści żądania POST. Ta funkcja jest podobna do karty **test** w Azure Portal.

Aby wyzwolić funkcje inne niż HTTP, należy wywołać następujący punkt końcowy administratora:

```
http://localhost:{port}/admin/functions/{function_name}
```

Aby przekazać dane testowe do punktu końcowego administratora funkcji, należy podać dane w treści komunikatu żądania POST. Treść wiadomości musi mieć następujący format JSON:

```JSON
{
    "input": "<trigger_input>"
}
```

`<trigger_input>`Wartość zawiera dane w formacie oczekiwanym przez funkcję. Poniższy przykład zazwinięcia jest WPISem do `QueueTriggerJS` funkcji. W tym przypadku dane wejściowe to ciąg, który jest odpowiednikiem komunikatu, który powinien zostać znaleziony w kolejce.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

#### <a name="using-the-func-run-command-version-1x-only"></a>Korzystanie z `func run` polecenia (tylko wersja 1. x)

>[!IMPORTANT]
> `func run`Polecenie jest obsługiwane tylko w wersji 1. x narzędzi. Aby uzyskać więcej informacji, zobacz temat [jak dowiedzieć się, jak kierować Azure Functions wersji środowiska uruchomieniowego](set-runtime-version.md).

W wersji 1. x można także wywołać funkcję bezpośrednio przy użyciu `func run <FunctionName>` i podać dane wejściowe dla funkcji. To polecenie jest podobne do uruchamiania funkcji za pomocą karty **test** w Azure Portal.

`func run` Program obsługuje następujące opcje:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--content`**, **`-c`** | Zawartość wbudowana. |
| **`--debug`**, **`-d`** | Dołącz debuger do procesu hosta przed uruchomieniem funkcji.|
| **`--timeout`**, **`-t`** | Czas oczekiwania (w sekundach), aż do momentu, gdy host funkcji lokalnych jest gotowy.|
| **`--file`**, **`-f`** | Nazwa pliku do użycia jako zawartość.|
| **`--no-interactive`** | Nie monituje o dane wejściowe. Przydatne w scenariuszach automatyzacji.|

Na przykład aby wywołać funkcję wyzwalaną przez protokół HTTP i przekazać treść zawartości, uruchom następujące polecenie:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-to-azure"></a><a name="publish"></a>Publikowanie na platformie Azure

Azure Functions Core Tools obsługuje dwa typy wdrożeń: Wdrażanie plików projektów funkcji bezpośrednio w aplikacji funkcji za pomocą narzędzia [zip Deploy](functions-deployment-technologies.md#zip-deploy) i [wdrażanie niestandardowego kontenera Docker](functions-deployment-technologies.md#docker-container). Użytkownik musi już [utworzyć aplikację funkcji w ramach subskrypcji platformy Azure](functions-cli-samples.md#create), w której zostanie wdrożony swój kod. Projekty wymagające kompilacji powinny zostać skompilowane, aby można było wdrożyć pliki binarne.

>[!IMPORTANT]
>Aby można było publikować na platformie Azure z podstawowych narzędzi, musisz mieć zainstalowany [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lokalnie.  

Folder projektu może zawierać pliki i katalogi specyficzne dla języka, które nie powinny być publikowane. Wykluczone elementy są wymienione w pliku. funcignore w folderze głównym projektu.     

### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>Wdróż pliki projektu

Aby opublikować kod lokalny w aplikacji funkcji na platformie Azure, użyj `publish` polecenia:

```
func azure functionapp publish <FunctionAppName>
```

>[!IMPORTANT]
> Język Java używa Maven do publikowania projektu lokalnego na platformie Azure. Użyj następującego polecenia, aby opublikować na platformie Azure: `mvn azure-functions:deploy` . Zasoby platformy Azure są tworzone podczas wstępnego wdrażania.

To polecenie publikuje w istniejącej aplikacji funkcji na platformie Azure. Wystąpi błąd podczas próby opublikowania w usłudze `<FunctionAppName>` , która nie istnieje w Twojej subskrypcji. Aby dowiedzieć się, jak utworzyć aplikację funkcji z poziomu wiersza polecenia lub okna terminalu przy użyciu interfejsu CLI platformy Azure, zobacz [tworzenie aplikacja funkcji do wykonywania bezserwerowego](./scripts/functions-cli-create-serverless.md). Domyślnie to polecenie korzysta z [kompilacji zdalnej](functions-deployment-technologies.md#remote-build) i wdraża aplikację do [uruchamiania z pakietu wdrożeniowego](run-functions-from-deployment-package.md). Aby wyłączyć ten zalecany tryb wdrażania, użyj `--nozip` opcji.

>[!IMPORTANT]
> Podczas tworzenia aplikacji funkcji w Azure Portal jest domyślnie używane środowisko uruchomieniowe funkcji w wersji 3. x. Aby aplikacja funkcji korzystała z wersji 1. x środowiska uruchomieniowego, postępuj zgodnie z instrukcjami w temacie [Run on Version 1. x](functions-versions.md#creating-1x-apps).
> Nie można zmienić wersji środowiska uruchomieniowego dla aplikacji funkcji, która ma istniejące funkcje.

W przypadku wszystkich wersji są stosowane następujące opcje publikowania:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Publikuj ustawienia w local.settings.jsna platformie Azure, monitując o zastąpienie, jeśli to ustawienie już istnieje. Jeśli używasz emulator magazynu Microsoft Azure, najpierw Zmień ustawienie aplikacji na [rzeczywiste połączenie magazynu](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Pomiń monit o zastąpienie ustawień aplikacji, gdy `--publish-local-settings -i` jest używany.|

Następujące opcje publikowania są obsługiwane tylko dla wersji 2. x i nowszych:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`**, **`-o`** |  Tylko Publikuj ustawienia i Pomiń zawartość. Wartość domyślna to Prompt. |
|**`--list-ignored-files`** | Wyświetla listę plików, które są ignorowane podczas publikowania, oparte na pliku. funcignore. |
| **`--list-included-files`** | Wyświetla listę opublikowanych plików, która jest oparta na pliku. funcignore. |
| **`--nozip`** | Wyłącza tryb domyślny `Run-From-Package` . |
| **`--build-native-deps`** | Pomija generowanie folderu. kół podczas publikowania aplikacji funkcji języka Python. |
| **`--build`**, **`-b`** | Wykonuje akcję kompilacji podczas wdrażania w aplikacji funkcji systemu Linux. Akceptuje: `remote` i `local` . |
| **`--additional-packages`** | Lista pakietów do zainstalowania podczas kompilowania natywnych zależności. Przykład: `python3-dev libevent-dev`. |
| **`--force`** | Ignoruj weryfikację przed publikacją w określonych scenariuszach. |
| **`--csx`** | Opublikuj projekt skryptu C# (. CSX). |
| **`--no-build`** | Projekt nie został skompilowany podczas publikowania. Dla języka Python `pip install` nie jest wykonywane. |
| **`--dotnet-cli-params`** | Podczas publikowania skompilowanych funkcji języka C# (. csproj) podstawowe narzędzia powodują wywoływanie "dotnet Build--Output bin/Publish". Wszystkie parametry przesłane do tego zostaną dołączone do wiersza polecenia. |

### <a name="deploy-custom-container"></a>Wdróż kontener niestandardowy

Azure Functions umożliwia wdrożenie projektu funkcji w [niestandardowym kontenerze platformy Docker](functions-deployment-technologies.md#docker-container). Aby uzyskać więcej informacji, zobacz [Tworzenie funkcji w systemie Linux przy użyciu obrazu niestandardowego](functions-create-function-linux-custom-image.md). Kontenery niestandardowe muszą mieć pliku dockerfile. Aby utworzyć aplikację za pomocą pliku dockerfile, użyj opcji--pliku dockerfile w systemie `func init` .

```
func deploy
```

Dostępne są następujące opcje wdrożenia kontenera niestandardowego:

| Opcja     | Opis                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Nazwa rejestru platformy Docker, do którego jest zalogowany bieżący użytkownik. |
| **`--platform`** | Platforma hostingu dla aplikacji funkcji. Prawidłowe opcje to `kubernetes` |
| **`--name`** | Nazwa aplikacji funkcji. |
| **`--max`**  | Opcjonalnie ustawia maksymalną liczbę wystąpień aplikacji funkcji do wdrożenia. |
| **`--min`**  | Opcjonalnie ustawia minimalną liczbę wystąpień aplikacji funkcji do wdrożenia. |
| **`--config`** | Ustawia opcjonalny plik konfiguracyjny wdrożenia. |

## <a name="monitoring-functions"></a>Funkcje monitorowania

Zalecanym sposobem monitorowania wykonywania funkcji jest integracja z usługą Azure Application Insights. Dzienniki wykonywania można przesyłać strumieniowo na komputer lokalny. Aby dowiedzieć się więcej, zobacz [Monitor Azure Functions](functions-monitoring.md).

### <a name="application-insights-integration"></a>Integracja Application Insights

Integracja Application Insights powinna być włączona podczas tworzenia aplikacji funkcji na platformie Azure. Jeśli z jakiegoś powodu aplikacja funkcji nie jest połączona z wystąpieniem Application Insights, można ją łatwo wykonać w Azure Portal. 

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

### <a name="enable-streaming-logs"></a>Włączanie dzienników przesyłania strumieniowego

Można wyświetlić strumień plików dziennika generowanych przez funkcje w sesji wiersza polecenia na komputerze lokalnym. 

#### <a name="native-streaming-logs"></a>Natywne dzienniki przesyłania strumieniowego

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Ten typ dzienników przesyłania strumieniowego wymaga włączenia integracji Application Insightsj dla aplikacji funkcji.   


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak opracowywać, testować i publikować Azure Functions przy użyciu Azure Functions Core Tools Azure Functions Core Tools [modułu Microsoft uczenie](/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) się [w serwisie GitHub](https://github.com/azure/azure-functions-cli).  
Aby zgłosić błąd lub żądanie funkcji, [Otwórz problem z usługą GitHub](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[AzureWebJobsStorage]: functions-app-settings.md#azurewebjobsstorage
[Pakiety rozszerzeń]: functions-bindings-register.md#extension-bundles
