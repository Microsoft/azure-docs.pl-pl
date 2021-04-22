---
title: Opracowywanie funkcji usługi Azure Functions przy użyciu programu Visual Studio Code
description: Dowiedz się, jak opracowywać i testować Azure Functions przy użyciu rozszerzenia Azure Functions dla Visual Studio Code.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: c2869b2b30722495523a9f0dfb2d70a17a205854
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871277"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Opracowywanie funkcji usługi Azure Functions przy użyciu programu Visual Studio Code

Rozszerzenie [Azure Functions dla Visual Studio Code] umożliwia lokalne tworzenie funkcji i wdrażanie ich na platformie Azure. Jeśli to pierwsze doświadczenie z Azure Functions, możesz dowiedzieć się więcej z [tematu An introduction to Azure Functions](functions-overview.md)( Wprowadzenie do Azure Functions ).

Rozszerzenie Azure Functions zapewnia następujące korzyści:

* Edytowanie, kompilowanie i uruchamianie funkcji na lokalnym komputerze dewelopera.
* Publikowanie projektu Azure Functions na platformie Azure.
* Pisz funkcje w różnych językach, jednocześnie korzystając z zalet Visual Studio Code.

Rozszerzenia można używać z następującymi językami, które są obsługiwane przez środowisko uruchomieniowe Azure Functions od wersji 2.x:

* [Skompilowane w języku C#](functions-dotnet-class-library.md)
* [Skrypt języka C#](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [Program PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Wymaga ustawienia [skryptu języka C# jako domyślnego języka projektu](#c-script-projects).

W tym artykule przykłady są obecnie dostępne tylko dla języka JavaScript (Node.js) i funkcji biblioteki klas języka C#.  

Ten artykuł zawiera szczegółowe informacje na temat używania rozszerzenia Azure Functions do tworzenia funkcji i publikowania ich na platformie Azure. Przed przeczytaniem tego artykułu należy utworzyć [pierwszą funkcję](./create-first-function-vs-code-csharp.md)przy użyciu Visual Studio Code .

> [!IMPORTANT]
> Nie mieszaj tworzenia aplikacji lokalnych i portalu dla jednej aplikacji funkcji. Podczas publikowania z lokalnego projektu do aplikacji funkcji proces wdrażania zastępuje wszystkie funkcje opracowane w portalu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed zainstalowaniem i uruchomieniem [rozszerzenia Azure Functions Azure Functions]dla programu[Visual Studio Code]należy spełnić następujące wymagania:

* [Visual Studio Code](https://code.visualstudio.com/) zainstalowany na jednej z [obsługiwanych platform.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

* Aktywna subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Inne potrzebne zasoby, takie jak konto usługi Azure Storage, są tworzone w ramach subskrypcji podczas publikowania przy użyciu [Visual Studio Code](#publish-to-azure). 

### <a name="run-local-requirements"></a>Uruchamianie wymagań lokalnych

Te wymagania wstępne są wymagane tylko do uruchamiania [i debugowania funkcji lokalnie.](#run-functions-locally) Nie są one wymagane do tworzenia ani publikowania projektów w Azure Functions.

# <a name="c"></a>[C\#](#tab/csharp)

+ Wersja [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 2.x lub nowsza. Pakiet narzędzi Core Tools jest pobierany i instalowany automatycznie podczas lokalnego uruchamiania projektu. Narzędzia Core Tools obejmują całe środowisko Azure Functions uruchomieniowe, więc pobieranie i instalacja może zająć trochę czasu.

+ [Rozszerzenie obsługujące język C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) dla programu Visual Studio Code. 

+ [interfejs wiersza polecenia platformy .NET Core narzędzi .](/dotnet/core/tools/?tabs=netcore2x)  

# <a name="java"></a>[Java](#tab/java)

+ Wersja [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 2.x lub nowsza. Pakiet Core Tools jest pobierany i instalowany automatycznie podczas lokalnego uruchamiania projektu. Narzędzia Core Tools obejmują całe środowisko Azure Functions uruchomieniowe, więc pobieranie i instalacja może zająć trochę czasu.

+ [Debuger rozszerzenia Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug).

+ [Zalecane środowisko Java 8.](/azure/developer/java/fundamentals/java-jdk-long-term-support) Aby uzyskać informacje o innych obsługiwanych wersjach, zobacz [Wersje języka Java](functions-reference-java.md#java-versions).

+ [Maven 3 lub nowszy](https://maven.apache.org/)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ Wersja [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 2.x lub nowsza. Pakiet narzędzi Core Tools jest pobierany i instalowany automatycznie podczas lokalnego uruchamiania projektu. Narzędzia Core Tools obejmują całe środowisko Azure Functions uruchomieniowe, więc pobieranie i instalacja może zająć trochę czasu.

+ [Node.js](https://nodejs.org/), Active LTS i Maintenance LTS (zalecane wersje 10.14.1). Użyj polecenia `node --version` , aby sprawdzić wersję. 

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

+ Wersja [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 2.x lub nowsza. Pakiet narzędzi Core Tools jest pobierany i instalowany automatycznie podczas lokalnego uruchamiania projektu. Narzędzia Core Tools obejmują całe środowisko Azure Functions uruchomieniowe, więc pobieranie i instalacja może zająć trochę czasu.

+ [Zalecany program PowerShell 7.](/powershell/scripting/install/installing-powershell-core-on-windows) Aby uzyskać informacje o wersji, zobacz [Wersje programu PowerShell.](functions-reference-powershell.md#powershell-versions)

+ Zarówno [środowisko uruchomieniowe .NET Core 3.1,](https://dotnet.microsoft.com/download) [jak i środowisko uruchomieniowe .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet/2.1)  

+ Rozszerzenie [programu PowerShell dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  

# <a name="python"></a>[Python](#tab/python)

+ Wersja [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 2.x lub nowsza. Pakiet narzędzi Core Tools jest pobierany i instalowany automatycznie podczas lokalnego uruchamiania projektu. Narzędzia Core Tools obejmują całe środowisko Azure Functions uruchomieniowe, więc pobieranie i instalacja może zająć trochę czasu.

+ [Python 3.x](https://www.python.org/downloads/). Aby uzyskać informacje o wersji, zobacz [Wersje języka Python](functions-reference-python.md#python-version) według Azure Functions uruchomieniowego.

+ [Rozszerzenie języka Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) dla Visual Studio Code.

---

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Tworzenie projektu usługi Azure Functions

Rozszerzenie Functions umożliwia utworzenie projektu aplikacji funkcji wraz z pierwszą funkcją. Poniższe kroki pokazują, jak utworzyć funkcję wyzwalaną przez protokół HTTP w nowym projekcie usługi Functions. [Wyzwalacz HTTP to](functions-bindings-http-webhook.md) najprostszy szablon wyzwalacza funkcji do zademonstrować.

1. Na **platformie Azure: Functions** wybierz **ikonę Utwórz funkcję:**

    ![Tworzenie funkcji](./media/functions-develop-vs-code/create-function.png)

1. Wybierz folder dla projektu aplikacji funkcji, a następnie wybierz **język dla projektu funkcji**.

1. Wybierz szablon **funkcji wyzwalacza HTTP** lub wybierz pozycję **Pomiń na razie,** aby utworzyć projekt bez funkcji. Zawsze możesz [później dodać funkcję do projektu.](#add-a-function-to-your-project)

    ![Wybieranie szablonu wyzwalacza HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Wpisz **HttpExample** jako nazwę funkcji i wybierz klawisz Enter, a następnie wybierz pozycję **Autoryzacja** funkcji. Ten poziom autoryzacji wymaga podania klucza [funkcji podczas](functions-bindings-http-webhook-trigger.md#authorization-keys) wywołania punktu końcowego funkcji.

    ![Wybierz pozycję Autoryzacja funkcji](./media/functions-develop-vs-code/create-function-auth.png)

    Funkcja jest tworzona w wybranym języku i w szablonie funkcji wyzwalanej przez protokół HTTP.

    ![Szablon funkcji wyzwalany przez protokół HTTP w Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Wygenerowane pliki projektu

Szablon projektu tworzy projekt w wybranym języku i instaluje wymagane zależności. Dla dowolnego języka nowy projekt ma następujące pliki:

* **host.jsna**: umożliwia skonfigurowanie hosta usługi Functions. Te ustawienia mają zastosowanie w przypadku uruchamiania funkcji lokalnie i na platformie Azure. Aby uzyskać więcej informacji, zobacz [host.jsna stronie .](functions-host-json.md)

* **local.settings.jsna**: przechowuje ustawienia używane podczas uruchamiania funkcji lokalnie. Te ustawienia są używane tylko wtedy, gdy funkcje są uruchomione lokalnie. Aby uzyskać więcej informacji, zobacz [Plik ustawień lokalnych](#local-settings-file).

    >[!IMPORTANT]
    >Ponieważ local.settings.jsw pliku może zawierać wpisy tajne, należy je wykluczyć z kontroli źródła projektu.

W zależności od języka tworzone są następujące pliki:

# <a name="c"></a>[C\#](#tab/csharp)

* [Plik biblioteki klas HttpExample.cs,](functions-dotnet-class-library.md#functions-class-library-project) który implementuje funkcję .

# <a name="java"></a>[Java](#tab/java)

+ Plik pom.xml w folderze głównym, który definiuje parametry projektu i wdrożenia, w tym zależności projektu i [wersję języka Java](functions-reference-java.md#java-versions). Ten pom.xml zawiera również informacje o zasobach platformy Azure, które są tworzone podczas wdrażania.   

+ Plik [Functions.java w](functions-reference-java.md#triggers-and-annotations) ścieżce src, który implementuje funkcję.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* Plik package.jsw folderze głównym.

* Folder HttpExample zawierający plik [function.js](functions-reference-node.md#folder-structure) definicji i plikindex.js [ ,](functions-reference-node.md#exporting-a-function)plik Node.js zawierający kod funkcji.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

* Folder HttpExample zawierający plik [function.jsdefinicji](functions-reference-powershell.md#folder-structure) i plik run.ps1, który zawiera kod funkcji.
 
# <a name="python"></a>[Python](#tab/python)
    
* Plik aplikacji na poziomie requirements.txt, który zawiera listę pakietów wymaganych przez usługę Functions.
    
* Folder HttpExample zawierający plik [function.jsdefinicji](functions-reference-python.md#folder-structure) i plik \_ \_ py init, który \_ \_ zawiera kod funkcji.

---

W tym momencie możesz dodać [powiązania wejściowe](#add-input-and-output-bindings) i wyjściowe do funkcji. Możesz również [dodać nową funkcję do projektu](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Instalowanie rozszerzeń powiązania

Z wyjątkiem wyzwalaczy HTTP i czasomierza powiązania są implementowane w pakietach rozszerzeń. Należy zainstalować pakiety rozszerzeń dla wyzwalaczy i powiązań, które ich potrzebują. Proces instalowania rozszerzeń powiązań zależy od języka projektu.

# <a name="c"></a>[C\#](#tab/csharp)

Uruchom polecenie [dotnet add package](/dotnet/core/tools/dotnet-add-package) w oknie terminalu, aby zainstalować pakiety rozszerzeń potrzebne w projekcie. Następujące polecenie instaluje rozszerzenie usługi Azure Storage, które implementuje powiązania dla usług Blob Storage, Queue Storage i Table Storage.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="java"></a>[Java](#tab/java)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Dodawanie funkcji do projektu

Nową funkcję można dodać do istniejącego projektu przy użyciu jednego ze wstępnie zdefiniowanych szablonów wyzwalaczy usługi Functions. Aby dodać nowy wyzwalacz funkcji, naciśnij klawisz F1, aby otworzyć paletę poleceń, a następnie wyszukaj i uruchom polecenie **Azure Functions: Create Function (Utwórz funkcję).** Postępuj zgodnie z monitami, aby wybrać typ wyzwalacza i zdefiniować wymagane atrybuty wyzwalacza. Jeśli wyzwalacz wymaga klucza dostępu lub parametrów połączenia w celu nawiązania połączenia z usługą, przygotuj go przed utworzeniem wyzwalacza funkcji.

Wyniki tej akcji zależą od języka projektu:

# <a name="c"></a>[C\#](#tab/csharp)

Do projektu zostanie dodany nowy plik biblioteki klas języka C# (cs).

# <a name="java"></a>[Java](#tab/java)

Do projektu zostanie dodany nowy plik Java (java).

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

W projekcie zostanie utworzony nowy folder. Folder zawiera nowy plik function.jsi nowy plik kodu JavaScript.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

W projekcie zostanie utworzony nowy folder. Folder zawiera nowy plik function.jsi nowy plik kodu programu PowerShell.

# <a name="python"></a>[Python](#tab/python)

W projekcie zostanie utworzony nowy folder. Folder zawiera nowy plik function.jsi nowy plik kodu w języku Python.

---

## <a name="connect-to-services"></a><a name="add-input-and-output-bindings"></a>Łączenie z usługami

Funkcję można połączyć z innymi usługami platformy Azure, dodając powiązania wejściowe i wyjściowe. Powiązania łączą funkcję z innymi usługami bez konieczności pisania kodu połączenia. Proces dodawania powiązań zależy od języka projektu. Aby dowiedzieć się więcej na temat powiązań, [zobacz Azure Functions wyzwalaczy i powiązań](functions-triggers-bindings.md).

Poniższe przykłady łączą się z kolejką magazynu o nazwie , w której w ustawieniu aplikacji w local.settings.js`outqueue` `MyStorageConnection` są ustawiane local.settings.jsmagazynu.

# <a name="c"></a>[C\#](#tab/csharp)

Zaktualizuj metodę funkcji, aby dodać następujący parametr do `Run` definicji metody:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

Parametr `msg` jest typem, który reprezentuje kolekcję komunikatów, które są zapisywane w powiązaniu danych wyjściowych `ICollector<T>` po zakończeniu działania funkcji. Poniższy kod dodaje komunikat do kolekcji:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="30-31":::

 Komunikaty są wysyłane do kolejki po zakończeniu działania funkcji.

Aby dowiedzieć się więcej, zobacz dokumentację [powiązania danych wyjściowych usługi Queue Storage.](functions-bindings-storage-queue-output.md?tabs=csharp) Aby dowiedzieć się więcej o tym, które powiązania można dodać do funkcji, zobacz Dodawanie powiązań do istniejącej [funkcji](add-bindings-existing-function.md?tabs=csharp)w Azure Functions . 

# <a name="java"></a>[Java](#tab/java)

Zaktualizuj metodę funkcji, aby dodać następujący parametr do `Run` definicji metody:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

Parametr jest typem, gdzie jest ciągiem, który jest zapisywany w powiązaniu danych wyjściowych `msg` `OutputBinding<T>` po `T` zakończeniu działania funkcji. Poniższy kod ustawia komunikat w powiązaniu danych wyjściowych:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33-34":::

Ten komunikat jest wysyłany do kolejki po zakończeniu działania funkcji.

Aby dowiedzieć się więcej, zobacz dokumentację dotyczącą powiązania [danych wyjściowych usługi Queue Storage.](functions-bindings-storage-queue-output.md?tabs=java) Aby dowiedzieć się więcej o tym, które powiązania można dodać do funkcji, zobacz Dodawanie powiązań do istniejącej funkcji w [Azure Functions](add-bindings-existing-function.md?tabs=java). 

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

W kodzie funkcji dostęp do powiązania `msg` uzyskuje się z , jak w poniższym `context` przykładzie:

:::code language="javascript" range="5-7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

Ten komunikat jest wysyłany do kolejki po zakończeniu działania funkcji.

Aby dowiedzieć się więcej, zobacz dokumentację dotyczącą powiązania [danych wyjściowych usługi Queue Storage.](functions-bindings-storage-queue-output.md?tabs=javascript) Aby dowiedzieć się więcej o tym, które powiązania można dodać do funkcji, zobacz Dodawanie powiązań do istniejącej funkcji w [Azure Functions](add-bindings-existing-function.md?tabs=javascript). 

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

Ten komunikat jest wysyłany do kolejki po zakończeniu działania funkcji.

Aby dowiedzieć się więcej, zobacz dokumentację dotyczącą powiązania [danych wyjściowych usługi Queue Storage.](functions-bindings-storage-queue-output.md?tabs=powershell) Aby dowiedzieć się więcej o tym, które powiązania można dodać do funkcji, zobacz Dodawanie powiązań do istniejącej funkcji w [Azure Functions](add-bindings-existing-function.md?tabs=powershell). 

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

Zaktualizuj `Main` definicję, aby dodać parametr wyjściowy, `msg: func.Out[func.QueueMessage]` tak aby definicja wyglądała jak w poniższym przykładzie:

:::code language="python" range="6" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Poniższy kod dodaje dane ciągu z żądania do kolejki wyjściowej:

:::code language="python" range="18" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Ten komunikat jest wysyłany do kolejki po zakończeniu działania funkcji.

Aby dowiedzieć się więcej, zobacz dokumentację dotyczącą powiązania [danych wyjściowych usługi Queue Storage.](functions-bindings-storage-queue-output.md?tabs=python) Aby dowiedzieć się więcej o tym, które powiązania można dodać do funkcji, zobacz Dodawanie powiązań do istniejącej funkcji w [Azure Functions](add-bindings-existing-function.md?tabs=python). 

---

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Visual Studio Code umożliwia publikowanie projektu usługi Functions bezpośrednio na platformie Azure. W ramach tego procesu tworzysz aplikację funkcji i powiązane zasoby w subskrypcji platformy Azure. Aplikacja funkcji zapewnia kontekst wykonywania dla Twoich funkcji. Projekt jest pakowany i wdrażany do nowej aplikacji funkcji w ramach subskrypcji platformy Azure.

Podczas publikowania z usługi Visual Studio Code do nowej aplikacji funkcji na platformie Azure możesz wybrać ścieżkę szybkiego tworzenia aplikacji funkcji przy użyciu wartości domyślnych lub ścieżki zaawansowanej, w której masz większą kontrolę nad utworzonymi zasobami zdalnymi. 

Podczas publikowania z Visual Studio Code można korzystać z technologii [wdrażania pliku zip.](functions-deployment-technologies.md#zip-deploy) 

### <a name="quick-function-app-create"></a>Szybkie tworzenie aplikacji funkcji

Po wybraniu opcji **+ Utwórz nową aplikację funkcji** na platformie Azure... rozszerzenie automatycznie generuje wartości dla zasobów platformy Azure wymaganych przez aplikację funkcji. Te wartości są oparte na nazwie aplikacji funkcji, która została przez Ciebie wybrana. Aby uzyskać przykład użycia wartości domyślnych do publikowania projektu w nowej aplikacji funkcji na platformie Azure, zobacz artykuł Szybki [Visual Studio Code szybki start.](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure)

Jeśli chcesz podać jawne nazwy utworzonych zasobów, musisz wybrać zaawansowaną ścieżkę tworzenia.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Publikowanie projektu w nowej aplikacji funkcji na platformie Azure przy użyciu opcji zaawansowanych

Poniżej przedstawiono procedurę publikowania projektu w nowej aplikacji funkcji utworzonej przy użyciu zaawansowanych opcji tworzenia:

1. Na palecie poleceń wprowadź **tekst Azure Functions: Deploy to function app**(Wdrażanie w aplikacji funkcji).

1. Jeśli nie zalogowano się, zostanie wyświetlony monit o zalogowanie **się do platformy Azure.** Możesz również utworzyć **bezpłatne konto platformy Azure.** Po zalogowaniu się z przeglądarki wróć do strony Visual Studio Code.

1. Jeśli masz wiele subskrypcji, wybierz **subskrypcję** dla aplikacji funkcji, a następnie wybierz **pozycję + Utwórz nową aplikację funkcji na platformie Azure... _Zaawansowane ._** Ta _opcja_ Zaawansowane zapewnia większą kontrolę nad zasobami, które tworzysz na platformie Azure. 

1. Po wyświetleniu monitów podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie aplikacji funkcji na platformie Azure | Tworzenie nowej aplikacji funkcji na platformie Azure | W następnym wierszu wpisz globalnie unikatową nazwę identyfikującą nową aplikację funkcji, a następnie naciśnij klawisz Enter. Prawidłowe znaki dla nazwy aplikacji funkcji to `a-z`, `0-9` i `-`. |
    | Wybieranie systemu operacyjnego | Windows | Aplikacja funkcji działa w systemie Windows. |
    | Wybieranie planu hostingu | Plan Zużycie | Używany jest bez użycia serwera plan zużycie [hostingu.](consumption-plan.md) |
    | Wybieranie środowiska uruchomieniowego dla nowej aplikacji | Język projektu | Środowisko uruchomieniowe musi odpowiadać projektowi, który publikujesz. |
    | Wybieranie grupy zasobów dla nowych zasobów | Tworzenie nowej grupy zasobów | W następnym wierszu polecenia wpisz nazwę grupy zasobów, na przykład `myResourceGroup` , a następnie naciśnij klawisz Enter. Możesz również wybrać istniejącą grupę zasobów. |
    | Wybieranie konta magazynu | Tworzenie nowego konta magazynu | W następnym wierszu polecenia wpisz globalnie unikatową nazwę nowego konta magazynu używanego przez aplikację funkcji, a następnie naciśnij klawisz Enter. Nazwy kont magazynu muszą mieć od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również wybrać istniejące konto. |
    | Wybieranie lokalizacji dla nowych zasobów | region | Wybierz lokalizację w [regionie w](https://azure.microsoft.com/regions/) pobliżu ciebie lub w pobliżu innych usług, do których Twoje funkcje mają dostęp. |

    Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. Wybierz **pozycję Wyświetl dane** wyjściowe w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym utworzone zasoby platformy Azure.

### <a name="get-the-url-of-an-http-triggered-function-in-azure"></a><a name="get-the-url-of-the-deployed-function"></a>Uzyskiwanie adresu URL funkcji wyzwalanych przez protokół HTTP na platformie Azure

Aby wywołać funkcję wyzwalaną przez protokół HTTP z klienta, potrzebny jest adres URL funkcji po jej wdrożeniu w aplikacji funkcji. Ten adres URL zawiera wszystkie wymagane klucze funkcji. Możesz użyć rozszerzenia , aby uzyskać te adresy URL dla wdrożonych funkcji. Jeśli chcesz tylko uruchomić funkcję zdalną na platformie Azure, użyj funkcji [Execute](#run-functions-in-azure) teraz rozszerzenia.

1. Naciśnij klawisz F1, aby otworzyć paletę poleceń, a następnie wyszukaj i uruchom polecenie **Azure Functions: Copy Function URL**(Kopiuj adres URL funkcji).

1. Postępuj zgodnie z monitami, aby wybrać aplikację funkcji na platformie Azure, a następnie określony wyzwalacz HTTP, który chcesz wywołać.

Adres URL funkcji jest kopiowany do schowka wraz z wymaganymi kluczami przekazanymi przez `code` parametr zapytania. Użyj narzędzia HTTP do przesyłania żądań POST lub przeglądarki dla żądań GET do funkcji zdalnej.  

Podczas pobierania adresu URL funkcji na platformie Azure rozszerzenie używa konta platformy Azure do automatycznego pobierania kluczy, których potrzebuje do uruchomienia funkcji. [Dowiedz się więcej o kluczach dostępu funkcji.](security-concepts.md#function-access-keys) Uruchamianie funkcji innych niż wyzwalane przez protokół HTTP wymaga użycia klucza administratora.

## <a name="republish-project-files"></a>Ponowne opublikować pliki projektu

Po skonfigurowaniu [ciągłego wdrażania](functions-continuous-deployment.md)aplikacja funkcji na platformie Azure jest aktualizowana podczas aktualizowania plików źródłowych w połączonej lokalizacji źródłowej. Zalecamy ciągłe wdrażanie, ale możesz również ponownie opublikować aktualizacje pliku projektu z Visual Studio Code.

> [!IMPORTANT]
> Publikowanie do istniejącej aplikacji funkcji spowoduje zastąpienie zawartości tej aplikacji na platformie Azure.

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="run-functions"></a>Uruchamianie funkcji

Rozszerzenie Azure Functions umożliwia uruchamianie poszczególnych funkcji w projekcie na lokalnym komputerze dewelopera lub w ramach subskrypcji platformy Azure. 

W przypadku funkcji wyzwalacza HTTP rozszerzenie wywołuje punkt końcowy HTTP. W przypadku innych rodzajów wyzwalaczy wywołuje interfejsy API administratora w celu uruchomienia funkcji. Treść komunikatu żądania wysłanego do funkcji zależy od typu wyzwalacza. Jeśli wyzwalacz wymaga danych testowych, zostanie wyświetlony monit o wprowadzenie danych w określonym formacie JSON.

### <a name="run-functions-in-azure"></a>Uruchamianie funkcji na platformie Azure

Aby wykonać funkcję na platformie Azure z Visual Studio Code. 

1. Na palecie poleceń wprowadź **Azure Functions: Wykonaj funkcję teraz** i wybierz subskrypcję platformy Azure. 

1. Wybierz aplikację funkcji na platformie Azure z listy. Jeśli nie widzisz aplikacji funkcji, upewnij się, że zalogowano się do odpowiedniej subskrypcji. 

1. Wybierz z listy funkcję, którą chcesz uruchomić, i wpisz treść komunikatu żądania w treści **żądania Enter**. Naciśnij klawisz Enter, aby wysłać ten komunikat żądania do funkcji. Domyślny tekst w treści **żądania Enter** powinien wskazywać format treści. Jeśli aplikacja funkcji nie ma żadnych funkcji, zostanie wyświetlone powiadomienie z tym błędem. 

1. Gdy funkcja jest wykonywana na platformie Azure i zwraca odpowiedź, w Visual Studio Code.
 
Możesz również uruchomić funkcję z obszaru **Azure: Functions,** klikając prawym przyciskiem myszy (klikając prawym przyciskiem myszy na komputerze Mac) funkcję, którą chcesz uruchomić z aplikacji funkcji w ramach subskrypcji platformy Azure, a następnie wybierając polecenie Wykonaj funkcję **teraz...**.

Podczas uruchamiania funkcji na platformie Azure rozszerzenie używa twojego konta platformy Azure do automatycznego pobierania kluczy, których potrzebuje do uruchomienia funkcji. [Dowiedz się więcej o kluczach dostępu funkcji.](security-concepts.md#function-access-keys) Uruchamianie funkcji innych niż wyzwalane przez protokół HTTP wymaga użycia klucza administratora.

### <a name="run-functions-locally"></a>Lokalne uruchamianie funkcji

Lokalne środowisko uruchomieniowe jest tym samym środowiskiem uruchomieniowym, które hostuje aplikację funkcji na platformie Azure. Ustawienia lokalne są odczytywane zlocal.settings.js[ pliku](#local-settings-file). Aby uruchomić projekt usługi Functions lokalnie, musisz spełnić [dodatkowe wymagania.](#run-local-requirements)

#### <a name="configure-the-project-to-run-locally"></a>Konfigurowanie projektu do uruchamiania lokalnego

Środowisko uruchomieniowe usługi Functions używa konta usługi Azure Storage wewnętrznie dla wszystkich typów wyzwalaczy innych niż HTTP i webhook. Dlatego należy ustawić klucz **Values.AzureWebJobsStorage** na prawidłowe ciąg połączenia konta usługi Azure Storage.

W tej sekcji używane jest [rozszerzenie usługi Azure Storage dla usługi Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) Eksplorator usługi Azure Storage do nawiązywania połączenia i pobierania parametrów połączenia magazynu. [](https://storageexplorer.com/)

Aby ustawić ciąg połączenia konta magazynu:

1. W Visual Studio Cloud **Explorer,** rozwiń pozycję **Konto** magazynu Twoje konto magazynu, a następnie wybierz pozycję Właściwości i skopiuj wartość  >  Podstawowe **ciągi** połączenia. 

2. W projekcie otwórz plik local.settings.jsi ustaw wartość klucza **AzureWebJobsStorage** na skopiowane ciągi połączenia.

3. Powtórz poprzedni krok, aby dodać unikatowe klucze do **tablicy Values** dla wszystkich innych połączeń wymaganych przez funkcje.

Aby uzyskać więcej informacji, zobacz [Plik ustawień lokalnych](#local-settings-file).

#### <a name="debug-functions-locally"></a><a name="debugging-functions-locally"></a>Lokalne debugowanie funkcji  

Aby debugować funkcje, wybierz klawisz F5. Jeśli narzędzia [Core Tools]nie zostały jeszcze pobrane[Azure Functions Core Tools,]zostanie wyświetlony monit o ich pobranie. Po zainstalowaniu i uruchomieniu narzędzi Core Tools dane wyjściowe są wyświetlane w terminalu. Jest to takie samo, jak uruchomienie polecenia Core Tools z terminalu, ale z dodatkowymi zadaniami `func host start` kompilacji i dołączonym debugerem.  

Po uruchomieniu projektu możesz użyć funkcji **Wykonaj teraz...** rozszerzenia, aby wyzwolić funkcje w sposób, jaki był wykonywany podczas wdrażania projektu na platformie Azure. Po uruchomieniu projektu w trybie debugowania punkty przerwania są Visual Studio Code zgodnie z oczekiwaniami. 

1. W palecie poleceń wprowadź **Azure Functions: Wykonaj funkcję teraz** i wybierz **pozycję Projekt lokalny.** 

1. Wybierz funkcję, którą chcesz uruchomić w projekcie, i wpisz treść komunikatu żądania w treści **żądania Enter**. Naciśnij klawisz Enter, aby wysłać ten komunikat żądania do funkcji. Domyślny tekst w treści **żądania Enter** powinien wskazywać format treści. Jeśli aplikacja funkcji nie ma żadnych funkcji, zostanie wyświetlone powiadomienie o błędzie z tym błędem. 

1. Gdy funkcja jest uruchamiana lokalnie i po otrzymaniu odpowiedzi, w Visual Studio Code. Informacje o wykonaniu funkcji są wyświetlane w **panelu terminalu.**

Uruchamianie funkcji lokalnie nie wymaga używania kluczy. 

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Domyślnie te ustawienia nie są migrowane automatycznie po opublikowaniu projektu na platformie Azure. Po zakończeniu publikowania masz dostęp do opcji publikowania ustawień z local.settings.jsw aplikacji funkcji na platformie Azure. Aby dowiedzieć się więcej, zobacz [Publikowanie ustawień aplikacji.](#publish-application-settings)

Wartości w **connectionStrings nigdy** nie są publikowane.

Wartości ustawień aplikacji funkcji można również odczytywać w kodzie jako zmienne środowiskowe. Aby uzyskać więcej informacji, zobacz sekcje Zmienne środowiskowe w tych artykułach referencyjnych specyficznych dla języka:

* [Prekompilowane w języku C#](functions-dotnet-class-library.md#environment-variables)
* [Skrypt języka C# (csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [Program PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

## <a name="application-settings-in-azure"></a>Ustawienia aplikacji na platformie Azure

Ustawienia w pliku local.settings.jsw projekcie powinny być takie same jak ustawienia aplikacji w aplikacji funkcji na platformie Azure. Wszystkie ustawienia, które dodasz local.settings.js, musisz również dodać do aplikacji funkcji na platformie Azure. Te ustawienia nie są przekazywane automatycznie podczas publikowania projektu. Podobnie wszystkie ustawienia, które utworzysz w aplikacji funkcji w [portalu,](functions-how-to-use-azure-function-app-settings.md#settings) muszą zostać pobrane do projektu lokalnego.

### <a name="publish-application-settings"></a>Publikowanie ustawień aplikacji

Najprostszym sposobem opublikowania wymaganych ustawień w aplikacji funkcji  na platformie Azure jest użycie linku Przekaż ustawienia, który jest wyświetlany po opublikowaniu projektu:

![Przekazywanie ustawień aplikacji](./media/functions-develop-vs-code/upload-app-settings.png)

Ustawienia można również opublikować za pomocą **polecenia Azure Functions: Upload Local Setting** (Przekaż ustawienie lokalne) w palecie poleceń. Możesz dodać poszczególne ustawienia do ustawień aplikacji na platformie Azure za pomocą **Azure Functions: Dodaj nowe** ustawienie.

> [!TIP]
> Pamiętaj, aby zapisać local.settings.jspliku przed jego opublikowaniem.

Jeśli plik lokalny jest zaszyfrowany, zostanie odszyfrowany, opublikowany i zaszyfrowany ponownie. Jeśli w tych dwóch lokalizacjach istnieją ustawienia, które mają wartości powodujące konflikt, zostanie wyświetlony monit o wybranie sposobu kontynuowania.

Wyświetl istniejące ustawienia aplikacji w obszarze **Azure: Functions,** rozwijając subskrypcję, aplikację funkcji i **ustawienia aplikacji.**

![Wyświetlanie ustawień aplikacji funkcji w Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Pobieranie ustawień z platformy Azure

Jeśli ustawienia aplikacji zostały utworzone na platformie Azure, możesz je pobrać do swojego local.settings.jspliku przy użyciu polecenia **Azure Functions:** Pobierz ustawienia zdalne.

Podobnie jak w przypadku przekazywania, jeśli plik lokalny jest zaszyfrowany, zostanie odszyfrowany, zaktualizowany i zaszyfrowany ponownie. Jeśli w tych dwóch lokalizacjach istnieją ustawienia, które mają wartości powodujące konflikt, zostanie wyświetlony monit o wybranie sposobu kontynuowania.

## <a name="monitoring-functions"></a>Funkcje monitorowania

Po [uruchomieniu funkcji lokalnie](#run-functions-locally)dane dziennika są przesyłane strumieniowo do konsoli terminali. Dane dzienników można również uzyskać, gdy projekt usługi Functions jest uruchomiony w aplikacji funkcji na platformie Azure. Możesz nawiązać połączenie z dziennikami przesyłania strumieniowego na platformie Azure, aby wyświetlić dane dzienników niemal w czasie rzeczywistym, lub włączyć usługę Application Insights, aby lepiej zrozumieć zachowanie aplikacji funkcji.

### <a name="streaming-logs"></a>Przesyłanie strumieniowe dzienników

Podczas tworzenia aplikacji często przydatne jest wyświetlanie informacji rejestrowania w czasie niemal rzeczywistym. Możesz wyświetlić strumień plików dziennika generowanych przez funkcje. Te dane wyjściowe są przykładem dzienników przesyłania strumieniowego dla żądania do funkcji wyzwalanych przez protokół HTTP:

![Dane wyjściowe dzienników przesyłania strumieniowego dla wyzwalacza HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Aby dowiedzieć się więcej, zobacz [Streaming logs (Dzienniki przesyłania strumieniowego).](functions-monitoring.md#streaming-logs)

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Dzienniki przesyłania strumieniowego obsługują tylko jedno wystąpienie hosta usługi Functions. Gdy funkcja jest skalowana do wielu wystąpień, dane z innych wystąpień nie są wyświetlane w strumieniu dziennika. [Live Metrics Stream](../azure-monitor/app/live-stream.md) w Application Insights obsługuje wiele wystąpień. W czasie niemal rzeczywistym analiza przesyłania strumieniowego jest oparta na [próbkach danych.](configure-monitoring.md#configure-sampling)

### <a name="application-insights"></a>Application Insights

Zalecamy monitorowanie wykonywania funkcji przez zintegrowanie aplikacji funkcji z Application Insights. Podczas tworzenia aplikacji funkcji w Azure Portal ta integracja odbywa się domyślnie. Podczas tworzenia aplikacji funkcji podczas Visual Studio publikowania musisz samodzielnie Application Insights aplikację. Aby dowiedzieć się, jak to zrobić, [zobacz Włączanie Application Insights integracji.](configure-monitoring.md#enable-application-insights-integration)

Aby dowiedzieć się więcej na temat monitorowania przy użyciu Application Insights, [zobacz Monitorowanie Azure Functions](functions-monitoring.md).

## <a name="c-script-projects"></a>Projekty \# skryptów języka C

Domyślnie wszystkie projekty języka C# są tworzone jako projekty bibliotek [klas skompilowanych w języku C#.](functions-dotnet-class-library.md) Jeśli wolisz pracować z projektami skryptów języka C#, musisz wybrać skrypt C# jako język domyślny w ustawieniach Azure Functions rozszerzenia:

1. Wybierz **pozycję Ustawienia**  >  **preferencji**  >  **pliku.**

1. Przejdź do **strony Rozszerzenia ustawień** użytkownika  >    >  **Azure Functions**.

1. Wybierz **pozycję C#Script z** usługi Azure **Function: Project Language**.

Po ukończeniu tych kroków wywołania do podstawowych narzędzi Core Tools obejmują opcję , która generuje i publikuje pliki projektu skryptu `--csx` języka C# (csx). Po podano ten język domyślny, wszystkie projekty, które tworzysz domyślnie, są projektami skryptów języka C#. Nie jest wyświetlany monit o wybranie języka projektu, gdy jest ustawiona wartość domyślna. Aby tworzyć projekty w innych językach, należy zmienić to ustawienie lub usunąć je z konta settings.jspliku. Po usunięciu tego ustawienia zostanie ponownie wyświetlony monit o wybranie języka podczas tworzenia projektu.

## <a name="command-palette-reference"></a>Informacje o palecie poleceń

Rozszerzenie Azure Functions udostępnia przydatny interfejs graficzny w obszarze do interakcji z aplikacjami funkcji na platformie Azure. Ta sama funkcja jest również dostępna jako polecenia w palecie poleceń (F1). Dostępne Azure Functions następujące polecenia:

|Azure Functions polecenia  | Opis  |
|---------|---------|
|**Dodawanie nowych ustawień**  |  Tworzy nowe ustawienie aplikacji na platformie Azure. Aby dowiedzieć się więcej, zobacz [Publikowanie ustawień aplikacji.](#publish-application-settings) Może być również konieczne [pobranie tego ustawienia do ustawień lokalnych.](#download-settings-from-azure) |
| **Konfigurowanie źródła wdrożenia** | Łączy aplikację funkcji na platformie Azure z lokalnym repozytorium Git. Aby dowiedzieć się więcej, zobacz [Continuous deployment for Azure Functions](functions-continuous-deployment.md)(Ciągłe wdrażanie dla Azure Functions ). |
| **Nawiązywanie połączenia z repozytorium GitHub** | Łączy aplikację funkcji z repozytorium GitHub. |
| **Kopiuj adres URL funkcji** | Pobiera zdalny adres URL funkcji wyzwalanej przez protokół HTTP, która działa na platformie Azure. Aby dowiedzieć się więcej, zobacz Get the URL of the deployed function (Uzyskiwanie adresu [URL wdrożonej funkcji).](#get-the-url-of-the-deployed-function) |
| **Tworzenie aplikacji funkcji na platformie Azure** | Tworzy nową aplikację funkcji w subskrypcji na platformie Azure. Aby dowiedzieć się więcej, zobacz sekcję na temat [publikowania w nowej aplikacji funkcji na platformie Azure.](#publish-to-azure)        |
| **Ustawienia odszyfrowywania** | [Odszyfrowuje ustawienia lokalne,](#local-settings-file) które zostały zaszyfrowane za **pomocą Azure Functions: Szyfrowanie ustawień**.  |
| **Usuwanie aplikacji funkcji** | Usuwa aplikację funkcji z subskrypcji na platformie Azure. Jeśli w planie App Service nie ma żadnych innych aplikacji, możesz je również usunąć. Inne zasoby, takie jak konta magazynu i grupy zasobów, nie są usuwane. Aby usunąć wszystkie zasoby, należy zamiast [tego usunąć grupę zasobów](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Nie ma to wpływu na projekt lokalny. |
|**Delete, funkcja**  | Usuwa istniejącą funkcję z aplikacji funkcji na platformie Azure. Ponieważ to usunięcie nie ma wpływu na projekt lokalny, zamiast tego rozważ usunięcie funkcji lokalnie, a następnie ponowne opublikowanie [projektu](#republish-project-files). |
| **Usuwanie serwera proxy** | Usuwa Azure Functions proxy z aplikacji funkcji na platformie Azure. Aby dowiedzieć się więcej na temat serwerów proxy, zobacz [Praca z serwery proxy usługi Azure Functions](functions-proxies.md). |
| **Usuwanie ustawienia** | Usuwa ustawienie aplikacji funkcji na platformie Azure. To usunięcie nie ma wpływu na ustawienia w local.settings.jspliku. |
| **Rozłączanie z repo**  | Usuwa połączenie [ciągłego wdrażania](functions-continuous-deployment.md) między aplikacją funkcji na platformie Azure i repozytorium kontroli źródła. |
| **Pobieranie ustawień zdalnych** | Pobiera ustawienia z wybranej aplikacji funkcji na platformie Azure do local.settings.jspliku. Jeśli plik lokalny jest zaszyfrowany, zostanie odszyfrowany, zaktualizowany i zaszyfrowany ponownie. Jeśli w obu lokalizacjach istnieją ustawienia, które mają wartości powodujące konflikt, zostanie wyświetlony monit o wybranie sposobu kontynuowania. Pamiętaj, aby zapisać zmiany w pliku local.settings.jsprzed uruchomieniem tego polecenia. |
| **Edytowanie ustawień** | Zmienia wartość istniejącego ustawienia aplikacji funkcji na platformie Azure. To polecenie nie ma wpływu na ustawienia w pliku local.settings.jspliku.  |
| **Szyfrowanie ustawień** | Szyfruje poszczególne elementy w `Values` tablicy w [ustawieniach lokalnych](#local-settings-file). W tym pliku jest również ustawiona wartość , która określa, że lokalne środowisko uruchomieniowe `IsEncrypted` `true` odszyfruje ustawienia przed ich użyciem. Szyfruj ustawienia lokalne, aby zmniejszyć ryzyko wycieku cennych informacji. Na platformie Azure ustawienia aplikacji są zawsze przechowywane w postaci zaszyfrowanej. |
| **Wykonaj teraz funkcję** | Ręcznie uruchamia funkcję przy użyciu interfejsów API administratora. To polecenie służy do testowania zarówno lokalnie podczas debugowania, jak i dla funkcji uruchomionych na platformie Azure. Podczas wyzwalania funkcji na platformie Azure rozszerzenie najpierw automatycznie uzyskuje klucz administratora, którego używa do wywoływania interfejsów API administratora zdalnego, które uruchamiają funkcje na platformie Azure. Treść komunikatu wysyłanego do interfejsu API zależy od typu wyzwalacza. Wyzwalacze czasomierza nie wymagają przekazania żadnych danych. |
| **Inicjowanie projektu do użycia z VS Code** | Dodaje wymagane Visual Studio Code projektu do istniejącego projektu funkcji. To polecenie umożliwia pracę z projektem utworzonym przy użyciu narzędzi Core Tools. |
| **Instalowanie lub aktualizowanie Azure Functions Core Tools** | Instaluje lub aktualizuje [Azure Functions Core Tools], który służy do lokalnego uruchamiania funkcji. |
| **Ponowne wdrożenie**  | Umożliwia ponowne wdrożenie plików projektu z połączonego repozytorium Git do określonego wdrożenia na platformie Azure. Aby ponownie opublikować aktualizacje lokalne z Visual Studio Code, ponownie [opublikować projekt](#republish-project-files). |
| **Zmienianie nazwy ustawień** | Zmienia nazwę klucza istniejącego ustawienia aplikacji funkcji na platformie Azure. To polecenie nie ma wpływu na ustawienia w local.settings.jspliku. Po zmianie nazwy ustawień na platformie Azure należy [pobrać te zmiany do lokalnego projektu](#download-settings-from-azure). |
| **Uruchom ponownie** | Ponownie uruchamia aplikację funkcji na platformie Azure. Wdrażanie aktualizacji powoduje również ponowne uruchomienie aplikacji funkcji. |
| **Ustawianie usługi AzureWebJobsStorage**| Ustawia wartość ustawienia `AzureWebJobsStorage` aplikacji. To ustawienie jest wymagane przez Azure Functions. Jest ona ustawiana podczas tworzenia aplikacji funkcji na platformie Azure. |
| **Początek** | Uruchamia zatrzymaną aplikację funkcji na platformie Azure. |
| **Uruchamianie dzienników przesyłania strumieniowego** | Uruchamia dzienniki przesyłania strumieniowego dla aplikacji funkcji na platformie Azure. Użyj dzienników przesyłania strumieniowego podczas zdalnego rozwiązywania problemów na platformie Azure, jeśli chcesz wyświetlić informacje rejestrowania niemal w czasie rzeczywistym. Aby dowiedzieć się więcej, zobacz [Streaming logs (Przesyłanie strumieniowe dzienników).](#streaming-logs) |
| **Zatrzymaj** | Zatrzymuje aplikację funkcji, która działa na platformie Azure. |
| **Zatrzymaj przesyłanie strumieniowe dzienników** | Zatrzymuje dzienniki przesyłania strumieniowego dla aplikacji funkcji na platformie Azure. |
| **Przełącz jako ustawienie miejsca** | Po włączeniu zapewnia, że ustawienie aplikacji będzie nadal zachowywane dla danego miejsca wdrożenia. |
| **Odinstalowywanie Azure Functions Core Tools** | Usuwa Azure Functions Core Tools, co jest wymagane przez rozszerzenie. |
| **Przekazywanie ustawień lokalnych** | Umożliwia przekazanie ustawień z pliku local.settings.jsdo wybranej aplikacji funkcji na platformie Azure. Jeśli plik lokalny jest zaszyfrowany, zostanie odszyfrowany, przekazany i zaszyfrowany ponownie. Jeśli w tych dwóch lokalizacjach istnieją ustawienia, które mają wartości powodujące konflikt, zostanie wyświetlony monit o wybranie sposobu kontynuowania. Pamiętaj, aby zapisać zmiany w pliku local.settings.jsprzed uruchomieniem tego polecenia. |
| **Wyświetlanie zatwierdzenia w usłudze GitHub** | Pokazuje najnowsze zatwierdzenie w określonym wdrożeniu, gdy aplikacja funkcji jest połączona z repozytorium. |
| **Wyświetlanie dzienników wdrażania** | Przedstawia dzienniki dla określonego wdrożenia aplikacji funkcji na platformie Azure. |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Functions Core Tools, zobacz [Praca z Azure Functions Core Tools](functions-run-local.md).

Aby dowiedzieć się więcej na temat tworzenia funkcji jako bibliotek klas .NET, zobacz [Azure Functions języka C# dla deweloperów.](functions-dotnet-class-library.md) Ten artykuł zawiera również linki do przykładów użycia atrybutów do deklarowania różnych typów powiązań obsługiwanych przez Azure Functions.

[Rozszerzenie usługi Azure Functions dla programu Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md