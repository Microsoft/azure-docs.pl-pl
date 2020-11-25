---
title: Opracowywanie funkcji usługi Azure Functions przy użyciu programu Visual Studio Code
description: Dowiedz się, jak opracowywać i testować Azure Functions przy użyciu rozszerzenia Azure Functions dla Visual Studio Code.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: c851f5284b87f224932b027fd10ce720327639c2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010514"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Opracowywanie funkcji usługi Azure Functions przy użyciu programu Visual Studio Code

[Rozszerzenie Azure Functions dla Visual Studio Code] umożliwia lokalne opracowywanie funkcji i wdrażanie ich na platformie Azure. Jeśli to środowisko jest pierwsze dzięki Azure Functions, możesz dowiedzieć się więcej na temat [wprowadzenia do Azure Functions](functions-overview.md).

Rozszerzenie Azure Functions zapewnia następujące korzyści:

* Edytuj, Kompiluj i uruchamiaj funkcje na lokalnym komputerze deweloperskim.
* Opublikuj projekt Azure Functions bezpośrednio na platformie Azure.
* Pisz swoje funkcje w różnych językach, wykorzystując zalety Visual Studio Code.

Rozszerzenia można używać w następujących językach, które są obsługiwane przez środowisko uruchomieniowe Azure Functions, począwszy od wersji 2. x:

* [Skompilowane w języku C#](functions-dotnet-class-library.md)
* [Skrypt C#](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [Program PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Wymaga [Ustawienia skryptu języka C# jako domyślnego języka projektu](#c-script-projects).

W tym artykule przykłady są obecnie dostępne tylko w przypadku funkcji JavaScript (Node.js) i biblioteki klas języka C#.  

W tym artykule przedstawiono szczegółowe informacje dotyczące sposobu tworzenia funkcji i publikowania ich na platformie Azure przy użyciu rozszerzenia Azure Functions. Przed przeczytaniem tego artykułu należy [utworzyć pierwszą funkcję przy użyciu Visual Studio Code](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Nie mieszaj programowania lokalnego i opracowywania portalu dla jednej aplikacji funkcji. W przypadku publikowania z projektu lokalnego w aplikacji funkcji proces wdrażania zastępuje wszystkie funkcje, które zostały opracowane w portalu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed zainstalowaniem i uruchomieniem rozszerzenia [Azure Functions rozszerzenia][Azure Functions Visual Studio Code]należy spełnić następujące wymagania:

* [Visual Studio Code](https://code.visualstudio.com/) zainstalowane na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Aktywna subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Inne zasoby, które są potrzebne, takie jak konto usługi Azure Storage, są tworzone w ramach subskrypcji podczas [publikowania przy użyciu Visual Studio Code](#publish-to-azure).

> [!IMPORTANT]
> Możesz opracowywać funkcje lokalnie i publikować je na platformie Azure bez konieczności uruchamiania i uruchamiania ich lokalnie. Aby uruchamiać funkcje lokalnie, należy spełnić pewne dodatkowe wymagania, w tym automatyczne pobieranie Azure Functions Core Tools. Aby dowiedzieć się więcej, zobacz [dodatkowe wymagania dotyczące uruchamiania projektu lokalnie](#additional-requirements-for-running-a-project-locally).

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Tworzenie projektu usługi Azure Functions

Rozszerzenie Functions umożliwia utworzenie projektu aplikacji funkcji wraz z pierwszą funkcją. Poniższe kroki pokazują, jak utworzyć funkcję wyzwalaną przez protokół HTTP w nowym projekcie funkcji. [Wyzwalacz http](functions-bindings-http-webhook.md) jest najprostszym szablonem wyzwalacza funkcji do zademonstrowania.

1. Na **platformie Azure: funkcje** wybierz ikonę **Utwórz funkcję** :

    ![Tworzenie funkcji](./media/functions-develop-vs-code/create-function.png)

1. Wybierz folder dla projektu aplikacji funkcji, a następnie **Wybierz język dla projektu funkcji**.

1. Jeśli narzędzia podstawowe nie zostały jeszcze zainstalowane, zostanie wyświetlony monit o **wybranie wersji** podstawowych narzędzi do zainstalowania. Wybierz wersję 2. x lub nowszą. 

1. Wybierz szablon funkcji **wyzwalacz http** lub wybierz pozycję **Pomiń teraz** , aby utworzyć projekt bez funkcji. Funkcję można zawsze [dodać do projektu](#add-a-function-to-your-project) później.

    ![Wybieranie szablonu wyzwalacza HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. W polu Nazwa funkcji wpisz **HttpExample** , a następnie wybierz pozycję ENTER, a następnie wybierz pozycję autoryzacja **funkcji** . Ten poziom autoryzacji wymaga podania [klucza funkcji](functions-bindings-http-webhook-trigger.md#authorization-keys) podczas wywoływania punktu końcowego funkcji.

    ![Wybierz autoryzację funkcji](./media/functions-develop-vs-code/create-function-auth.png)

    Funkcja jest tworzona w wybranym języku i w szablonie dla funkcji wyzwalanej przez protokół HTTP.

    ![Szablon funkcji wyzwalanej przez protokół HTTP w Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Wygenerowane pliki projektu

Szablon projektu tworzy projekt w wybranym języku i instaluje wymagane zależności. Dla każdego języka nowy projekt zawiera następujące pliki:

* **host.js**: umożliwia skonfigurowanie hosta funkcji. Te ustawienia są stosowane, gdy program działa lokalnie i gdy są uruchamiane na platformie Azure. Aby uzyskać więcej informacji, zobacz [host.json Reference](functions-host-json.md).

* **local.settings.js**: utrzymuje ustawienia używane podczas lokalnego uruchamiania funkcji. Te ustawienia są używane tylko w przypadku uruchamiania funkcji lokalnie. Aby uzyskać więcej informacji, zobacz [plik ustawień lokalnych](#local-settings-file).

    >[!IMPORTANT]
    >Ponieważ local.settings.jsw pliku może zawierać wpisy tajne, należy wykluczyć je z kontroli źródła projektu.

W zależności od języka tworzone są te inne pliki:

# <a name="c"></a>[S\#](#tab/csharp)

* [Plik biblioteki klas HttpExample.cs](functions-dotnet-class-library.md#functions-class-library-project) , który implementuje funkcję.

W tym momencie można dodać powiązania danych wejściowych i wyjściowych do funkcji, [dodając parametr do funkcji biblioteki klas języka C#](#add-input-and-output-bindings).

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* package.jspliku w folderze głównym.

* Folder HttpExample zawierający [function.jsw pliku definicji](functions-reference-node.md#folder-structure) oraz [ plikindex.js](functions-reference-node.md#exporting-a-function), plik Node.js, który zawiera kod funkcji.

W tym momencie można dodać powiązania danych wejściowych i wyjściowych do funkcji, [modyfikując function.jsw pliku](#add-input-and-output-bindings).

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

Możesz również [dodać nową funkcję do projektu](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Instalowanie rozszerzeń powiązania

Z wyjątkiem wyzwalaczy HTTP i Timer, powiązania są implementowane w pakietach rozszerzeń. Należy zainstalować pakiety rozszerzeń dla wyzwalaczy i powiązań, które ich potrzebują. Proces instalacji rozszerzeń powiązań zależy od języka projektu.

# <a name="c"></a>[S\#](#tab/csharp)

Uruchom polecenie [dotnet Add Package](/dotnet/core/tools/dotnet-add-package) w oknie terminalu, aby zainstalować pakiety rozszerzeń, które są potrzebne w projekcie. Następujące polecenie instaluje rozszerzenie usługi Azure Storage, które implementuje powiązania dla obiektów blob, Queue i Table Storage.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Dodawanie funkcji do projektu

Nową funkcję można dodać do istniejącego projektu przy użyciu jednego ze wstępnie zdefiniowanych szablonów wyzwalaczy funkcji. Aby dodać nowy wyzwalacz funkcji, wybierz F1, aby otworzyć paletę poleceń, a następnie wyszukaj i uruchom polecenie **Azure Functions: Create Function**. Postępuj zgodnie z monitami, aby wybrać typ wyzwalacza i zdefiniować wymagane atrybuty wyzwalacza. Jeśli wyzwalacz wymaga klucza dostępu lub parametrów połączenia w celu nawiązania połączenia z usługą, przygotuj go przed utworzeniem wyzwalacza funkcji.

Wyniki tej akcji zależą od języka projektu:

# <a name="c"></a>[S\#](#tab/csharp)

Do projektu zostanie dodany nowy plik biblioteki klas C# (. cs).

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

W projekcie zostanie utworzony nowy folder. Folder zawiera nowe function.jsw pliku i nowym pliku kodu JavaScript.

---

## <a name="add-input-and-output-bindings"></a>Dodawanie powiązań wejściowych i wyjściowych

Funkcję można rozszerzyć przez dodanie powiązań wejściowych i wyjściowych. Proces dodawania powiązań zależy od języka projektu. Aby dowiedzieć się więcej na temat powiązań, zobacz temat [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md).

Poniższe przykłady nawiązują połączenie z kolejką magazynu o nazwie `outqueue` , gdzie parametry połączenia dla konta magazynu są ustawiane w `MyStorageConnection` ustawieniach aplikacji w local.settings.jsna.

# <a name="c"></a>[S\#](#tab/csharp)

Zaktualizuj metodę funkcji, aby dodać następujący parametr do `Run` definicji metody:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Ten kod wymaga dodania następującej `using` instrukcji:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

`msg`Parametr jest `ICollector<T>` typem, który reprezentuje kolekcję komunikatów, które są zapisywane do powiązania danych wyjściowych po zakończeniu działania funkcji. Do kolekcji dodasz co najmniej jeden komunikat. Te komunikaty są wysyłane do kolejki po zakończeniu działania funkcji.

Aby dowiedzieć się więcej, zobacz dokumentację [powiązania danych wyjściowych kolejki magazynu](functions-bindings-storage-queue-output.md) .

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

Visual Studio Code pozwala dodawać powiązania do function.jspliku przy użyciu dogodnego zestawu kolejnych wierszy. Aby utworzyć powiązanie, kliknij prawym przyciskiem myszy (Ctrl + kliknięcie na macOS) **function.jsw** pliku w folderze funkcji i wybierz polecenie **Dodaj powiązanie**:

![Dodawanie powiązania do istniejącej funkcji języka JavaScript ](media/functions-develop-vs-code/function-add-binding.png)

Poniżej przedstawiono przykładowe monity o zdefiniowanie nowego powiązania danych wyjściowych magazynu:

| Monit | Wartość | Opis |
| -------- | ----- | ----------- |
| **Wybierz kierunek powiązania** | `out` | Powiązanie jest powiązaniem wyjściowym. |
| **Wybieranie powiązania z kierunkiem** | `Azure Queue Storage` | Powiązanie to powiązanie kolejki usługi Azure Storage. |
| **Nazwa używana do identyfikowania tego powiązania w kodzie** | `msg` | Nazwa, która identyfikuje parametr powiązania przywoływany w kodzie. |
| **Kolejka, do której zostanie wysłany komunikat** | `outqueue` | Nazwa kolejki, w której ma zostać zapisywany powiązania. Gdy *Kolejka* nie istnieje, powiązanie tworzy je przy pierwszym użyciu. |
| **Wybierz ustawienie z "local.settings.json"** | `MyStorageConnection` | Nazwa ustawienia aplikacji, które zawiera parametry połączenia dla konta magazynu. `AzureWebJobsStorage`Ustawienie zawiera parametry połączenia dla konta magazynu utworzonego za pomocą aplikacji funkcji. |

W tym przykładzie następujące powiązanie jest dodawane do `bindings` tablicy w function.jsw pliku:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Możesz również dodać tę samą definicję powiązania bezpośrednio do function.jsna.

W kodzie funkcji do `msg` powiązania uzyskuje się dostęp z `context` , jak w poniższym przykładzie:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Aby dowiedzieć się więcej, zobacz informacje o [powiązaniach wyjściowych magazynu kolejki](functions-bindings-storage-queue-output.md) .

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Visual Studio Code umożliwia opublikowanie projektu funkcji bezpośrednio na platformie Azure. W ramach tego procesu tworzysz aplikację funkcji i powiązane zasoby w subskrypcji platformy Azure. Aplikacja funkcji zapewnia kontekst wykonywania dla Twoich funkcji. Projekt jest pakowany i wdrażany do nowej aplikacji funkcji w ramach subskrypcji platformy Azure.

Po opublikowaniu z Visual Studio Code do nowej aplikacji funkcji na platformie Azure jest oferowana zarówno szybka ścieżka tworzenia aplikacji funkcji, jak i ścieżka zaawansowana. 

Przy publikowaniu z Visual Studio Code Skorzystaj z technologii [zip Deploy](functions-deployment-technologies.md#zip-deploy) . 

### <a name="quick-function-app-create"></a>Szybka funkcja tworzenia aplikacji

Gdy wybierzesz pozycję **+ Utwórz nową aplikację funkcji na platformie Azure...**, rozszerzenie automatycznie generuje wartości zasobów platformy Azure wymaganych przez aplikację funkcji. Te wartości są zależne od wybranej nazwy aplikacji funkcji. Przykład użycia ustawień domyślnych do opublikowania projektu w nowej aplikacji funkcji na platformie Azure można znaleźć w artykule dotyczącym [szybkiego startu Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure).

Jeśli chcesz podać jawne nazwy dla utworzonych zasobów, musisz wybrać zaawansowane ścieżki tworzenia.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Publikowanie projektu w nowej aplikacji funkcji na platformie Azure przy użyciu opcji zaawansowanych

Poniższe kroki umożliwiają opublikowanie projektu w nowej aplikacji funkcji utworzonej przy użyciu opcji tworzenia zaawansowanego:

1. W obszarze **Azure: Functions** wybierz ikonę **Wdróż do aplikacja funkcji** .

    ![Ustawienia aplikacji funkcji](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Jeśli użytkownik nie jest zalogowany, zostanie wyświetlony monit o **zalogowanie się do platformy Azure**. Możesz również **utworzyć bezpłatne konto platformy Azure**. Po zalogowaniu się z przeglądarki Wróć do Visual Studio Code.

1. Jeśli masz wiele subskrypcji, **Wybierz subskrypcję** dla aplikacji funkcji, a następnie wybierz pozycję **+ Utwórz nowe aplikacja funkcji na platformie Azure... _Zaawansowane_**. Ta _zaawansowana_ opcja zapewnia większą kontrolę nad zasobami tworzonymi na platformie Azure. 

1. Postępując zgodnie z instrukcjami, podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie aplikacji funkcji na platformie Azure | Utwórz nowe aplikacja funkcji na platformie Azure | W następnym monicie wpisz globalnie unikatową nazwę, która identyfikuje nową aplikację funkcji, a następnie wybierz klawisz ENTER. Prawidłowe znaki dla nazwy aplikacji funkcji to `a-z`, `0-9` i `-`. |
    | Wybierz system operacyjny | Windows | Aplikacja funkcji jest uruchamiana w systemie Windows. |
    | Wybierz plan hostingu | Plan Zużycie | Jest używany [host planu zużycia](functions-scale.md#consumption-plan) bezserwerowego. |
    | Wybierz środowisko uruchomieniowe dla nowej aplikacji | Język projektu | Środowisko uruchomieniowe musi pasować do projektu, który jest publikowany. |
    | Wybierz grupę zasobów dla nowych zasobów | Utwórz nową grupę zasobów | W następnym monicie wpisz nazwę grupy zasobów, na przykład `myResourceGroup` , a następnie wybierz klawisz ENTER. Możesz również wybrać istniejącą grupę zasobów. |
    | Wybierz konto magazynu | Tworzenie nowego konta magazynu | W następnym monicie wpisz globalnie unikatową nazwę nowego konta magazynu używanego przez aplikację funkcji, a następnie wybierz klawisz ENTER. Nazwy kont magazynu muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również wybrać istniejące konto. |
    | Wybierz lokalizację dla nowych zasobów | region | Wybierz lokalizację w [regionie](https://azure.microsoft.com/regions/) blisko siebie lub niemal innych usług, do których dostęp ma funkcja. |

    Zostanie wyświetlone powiadomienie po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego. Wybierz pozycję **Wyświetl dane wyjściowe** w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym utworzone zasoby platformy Azure.

## <a name="republish-project-files"></a>Publikuj ponownie pliki projektu

Po skonfigurowaniu [ciągłego wdrażania](functions-continuous-deployment.md)aplikacja funkcji na platformie Azure jest aktualizowana za każdym razem, gdy pliki źródłowe zostaną zaktualizowane w połączonej lokalizacji źródłowej. Zalecamy ciągłe wdrażanie, ale możesz również ponownie opublikować aktualizacje plików projektu z Visual Studio Code.

> [!IMPORTANT]
> Publikowanie do istniejącej aplikacji funkcji spowoduje zastąpienie zawartości tej aplikacji na platformie Azure.

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="get-the-url-of-the-deployed-function"></a>Pobierz adres URL wdrożonej funkcji

Aby wywołać funkcję wyzwalaną przez protokół HTTP, należy uzyskać adres URL funkcji, gdy zostanie ona wdrożona w aplikacji funkcji. Ten adres URL zawiera wszystkie wymagane [klucze funkcji](functions-bindings-http-webhook-trigger.md#authorization-keys). Możesz użyć rozszerzenia, aby uzyskać te adresy URL dla wdrożonych funkcji.

1. Wybierz F1, aby otworzyć paletę poleceń, a następnie wyszukaj i uruchom polecenie **Azure Functions: Copy URL funkcji**.

1. Postępuj zgodnie z monitami, aby wybrać aplikację funkcji na platformie Azure, a następnie konkretny wyzwalacz HTTP, który chcesz wywołać.

Adres URL funkcji jest kopiowany do schowka wraz z dowolnymi wymaganymi kluczami przesyłanymi przez `code` parametr zapytania. Użyj narzędzia HTTP do przesyłania żądań POST lub przeglądarki, aby uzyskać żądania do funkcji zdalnej.  

## <a name="run-functions-locally"></a>Uruchamianie funkcji lokalnie

Rozszerzenie Azure Functions pozwala uruchamiać projekt Functions na lokalnym komputerze deweloperskim. Lokalne środowisko uruchomieniowe to to samo środowisko uruchomieniowe, które obsługuje aplikację funkcji na platformie Azure. Ustawienia lokalne są odczytywane z [local.settings.jspliku](#local-settings-file).

### <a name="additional-requirements-for-running-a-project-locally"></a>Dodatkowe wymagania dotyczące uruchamiania projektu lokalnie

Aby uruchomić projekt funkcji lokalnie, należy spełnić następujące wymagania dodatkowe:

* Zainstaluj wersję 2. x lub nowszą [Azure Functions Core Tools](functions-run-local.md#v2). Pakiet podstawowych narzędzi jest pobierany i instalowany automatycznie podczas lokalnego uruchamiania projektu. Podstawowe narzędzia obejmują cały Azure Functions środowiska uruchomieniowego, więc pobieranie i instalacja może zająć trochę czasu.

* Zainstaluj określone wymagania dla wybranego języka:

    | Język | Wymaganie |
    | -------- | --------- |
    | **C#** | [Rozszerzenie C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)<br/>[Narzędzia interfejsu wiersza polecenia platformy .NET Core](/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Debuger dla rozszerzenia Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)<br/>[Maven 3 lub nowszy](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Rozszerzenie języka Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>Zalecane środowisko [Python 3.6.8](https://www.python.org/downloads/)|

    <sup>*</sup>Aktywne wersje LTS LTS i Maintenance (zalecane 8.11.1 i 10.14.1).

### <a name="configure-the-project-to-run-locally"></a>Skonfiguruj uruchamianie projektu lokalnie

Środowisko uruchomieniowe funkcji używa konta usługi Azure Storage wewnętrznie dla wszystkich typów wyzwalaczy innych niż HTTP i elementy webhook. W związku z tym należy ustawić wartość **. AzureWebJobsStorage** klucza na prawidłowe parametry połączenia konta usługi Azure Storage.

Ta sekcja używa [rozszerzenia usługi Azure Storage dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) z [Eksplorator usługi Azure Storage](https://storageexplorer.com/) , aby nawiązać połączenie z usługą i pobrać parametry połączenia z magazynem.

Aby ustawić parametry połączenia konta magazynu:

1. W programie Visual Studio Otwórz program **Cloud Explorer**, rozwiń węzeł **konto magazynu**  >  **Your Storage Account**, a następnie wybierz pozycję **Właściwości** i skopiuj wartość **podstawowe parametry połączenia** .

2. W projekcie Otwórz local.settings.jsw pliku i ustaw wartość klucza **AzureWebJobsStorage** na skopiowane parametry połączenia.

3. Powtórz poprzedni krok, aby dodać unikatowe klucze do tablicy **wartości** dla innych połączeń wymaganych przez funkcje.

Aby uzyskać więcej informacji, zobacz [plik ustawień lokalnych](#local-settings-file).

### <a name="debugging-functions-locally"></a>Funkcje debugowania lokalnie  

Aby debugować funkcje, wybierz F5. Jeśli nie pobrano jeszcze [podstawowych narzędzi][Azure Functions Core Tools], zostanie wyświetlony monit. Gdy podstawowe narzędzia są zainstalowane i działają, dane wyjściowe są wyświetlane w terminalu. Jest to takie samo, jak uruchamianie `func host start` podstawowych narzędzi Narzędzia z terminalu, ale z dodatkowymi zadaniami kompilacji i dołączonym debugerem.  

Gdy projekt jest uruchomiony, można wyzwolić funkcje tak jak podczas wdrażania projektu na platformie Azure. Gdy projekt jest uruchomiony w trybie debugowania, punkty przerwania są trafi w Visual Studio Code zgodnie z oczekiwaniami.

Adres URL żądania dla wyzwalaczy HTTP jest wyświetlany w danych wyjściowych w terminalu. Klucze funkcji dla wyzwalaczy HTTP nie są używane, gdy projekt jest uruchomiony lokalnie. Aby uzyskać więcej informacji, zobacz [strategie testowania kodu w Azure Functions](functions-test-a-function.md).  

Aby dowiedzieć się więcej, zobacz [Work with Azure Functions Core Tools][Azure Functions Core Tools].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Domyślnie te ustawienia nie są migrowane automatycznie, gdy projekt jest publikowany na platformie Azure. Po zakończeniu publikowania otrzymujesz opcję publikowania ustawień z local.settings.jsdo aplikacji funkcji na platformie Azure. Aby dowiedzieć się więcej, zobacz  [Publikowanie ustawień aplikacji](#publish-application-settings).

Wartości w **connectionStrings** nigdy nie są publikowane.

Wartości ustawień aplikacji funkcji można także odczytać w kodzie jako zmienne środowiskowe. Aby uzyskać więcej informacji, zobacz sekcję zmienne środowiskowe w następujących artykułach referencyjnych dotyczących języka:

* [Wstępnie skompilowany język C#](functions-dotnet-class-library.md#environment-variables)
* [Skrypt języka C# (csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Ustawienia aplikacji na platformie Azure

Ustawienia w local.settings.jsw pliku w projekcie powinny być takie same jak ustawienia aplikacji w aplikacji funkcji na platformie Azure. Wszystkie ustawienia, które należy dodać do local.settings.jsna, muszą być również dodane do aplikacji funkcji na platformie Azure. Te ustawienia nie są przekazywane automatycznie przy publikowaniu projektu. Podobnie wszystkie ustawienia, które tworzysz w aplikacji funkcji [w portalu](functions-how-to-use-azure-function-app-settings.md#settings) , muszą zostać pobrane do projektu lokalnego.

### <a name="publish-application-settings"></a>Publikuj ustawienia aplikacji

Najprostszym sposobem opublikowania wymaganych ustawień w aplikacji funkcji na platformie Azure jest użycie linku **przekazywania ustawień** , który pojawia się po opublikowaniu projektu:

![Przekazywanie ustawień aplikacji](./media/functions-develop-vs-code/upload-app-settings.png)

Ustawienia można również opublikować przy użyciu polecenia **Azure Functions: upload Local ustawienia** w palecie poleceń. Poszczególne ustawienia można dodać do ustawień aplikacji na platformie Azure za pomocą polecenia **Azure Functions: Dodaj nowe ustawienie** .

> [!TIP]
> Pamiętaj, aby zapisać local.settings.jsw pliku przed jego opublikowaniem.

Jeśli plik lokalny jest szyfrowany, zostaje odszyfrowany, opublikowany i zaszyfrowany ponownie. Jeśli istnieją jakieś ustawienia, które zawierają wartości powodujące konflikt w dwóch lokalizacjach, zostanie wyświetlony monit o wybranie metody, która ma zostać wykonana.

Wyświetl istniejące ustawienia aplikacji w obszarze **Azure: Functions** , rozszerzając swoją subskrypcję, aplikację funkcji i **Ustawienia aplikacji**.

![Wyświetlanie ustawień aplikacji funkcji w Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Pobieranie ustawień z platformy Azure

Jeśli zostały utworzone ustawienia aplikacji na platformie Azure, możesz je pobrać do local.settings.jsw pliku przy użyciu polecenia **Azure Functions: Pobierz Ustawienia zdalne** .

Podobnie jak w przypadku przekazywania, jeśli plik lokalny jest szyfrowany, zostaje odszyfrowany, zaktualizowany i zaszyfrowany ponownie. Jeśli istnieją jakieś ustawienia, które zawierają wartości powodujące konflikt w dwóch lokalizacjach, zostanie wyświetlony monit o wybranie metody, która ma zostać wykonana.

## <a name="monitoring-functions"></a>Funkcje monitorowania

Po [uruchomieniu funkcji lokalnie](#run-functions-locally)dane dziennika są przesyłane strumieniowo do konsoli terminalowej. Możesz również uzyskać dane dziennika, gdy projekt funkcji działa w aplikacji funkcji na platformie Azure. Możesz połączyć się z dziennikami przesyłania strumieniowego na platformie Azure, aby zobaczyć dane dziennika niemal w czasie rzeczywistym lub można włączyć Application Insights, aby zrozumieć, jak działa aplikacja funkcji.

### <a name="streaming-logs"></a>Przesyłanie strumieniowe dzienników

Podczas tworzenia aplikacji często warto zobaczyć informacje o rejestrowaniu w czasie niemal rzeczywistym. Można wyświetlić strumień plików dziennika generowanych przez funkcje. Dane wyjściowe to przykład dzienników przesyłania strumieniowego dla żądania do funkcji wyzwalanej przez protokół HTTP:

![Dane wyjściowe dzienników przesyłania strumieniowego dla wyzwalacza HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Aby dowiedzieć się więcej, zobacz [dzienniki przesyłania strumieniowego](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Dzienniki przesyłania strumieniowego obsługują tylko pojedyncze wystąpienie hosta funkcji. Gdy funkcja jest skalowana do wielu wystąpień, dane z innych wystąpień nie są wyświetlane w strumieniu dziennika. [Live Metrics Stream](../azure-monitor/app/live-stream.md) w Application Insights obsługuje wiele wystąpień. A także w czasie niemal rzeczywistym usługa Stream Analytics opiera się na [przykładowych danych](configure-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

Zalecamy monitorowanie wykonywania funkcji przez integrację aplikacji funkcji z Application Insights. Podczas tworzenia aplikacji funkcji w Azure Portal Ta integracja odbywa się domyślnie. Gdy tworzysz aplikację funkcji podczas publikacji programu Visual Studio, musisz samodzielnie zintegrować Application Insights. Aby dowiedzieć się, jak to zrobić, zobacz [Włączanie integracji Application Insights](configure-monitoring.md#enable-application-insights-integration).

Aby dowiedzieć się więcej na temat monitorowania za pomocą Application Insights, zobacz [Azure Functions monitorowania](functions-monitoring.md).

## <a name="c-script-projects"></a>\#Projekty skryptów języka C

Domyślnie wszystkie projekty C# są tworzone jako [projekty bibliotek klas skompilowanych w języku c#](functions-dotnet-class-library.md). Jeśli wolisz pracować z projektami skryptów C# zamiast tego, musisz wybrać opcję skrypt C# jako język domyślny w ustawieniach rozszerzenia Azure Functions:

1. Wybierz **File** pozycję  >  **Preferences**  >  **Ustawienia** preferencji pliku.

1. Przejdź do pozycji **Ustawienia użytkownika**  >  **Extensions**  >  **Azure Functions**.

1. Wybierz **skrypt języka C #** z **funkcji platformy Azure: język projektu**.

Po wykonaniu tych kroków wywołania wykonane do podstawowych narzędzi podstawowych obejmują `--csx` opcję, która generuje i publikuje pliki projektu skryptu C# (. CSX). Jeśli masz określony domyślny język, wszystkie projekty, które tworzysz domyślnie do projektów skryptów C#. Po ustawieniu wartości domyślnej nie jest wyświetlany monit o wybranie języka projektu. Aby utworzyć projekty w innych językach, należy zmienić to ustawienie lub usunąć je z settings.jsużytkownika na pliku. Po usunięciu tego ustawienia zostanie wyświetlony monit o wybranie języka podczas tworzenia projektu.

## <a name="command-palette-reference"></a>Dokumentacja palety poleceń

Rozszerzenie Azure Functions zapewnia przydatny interfejs graficzny w obszarze do korzystania z aplikacji funkcji na platformie Azure. Ta sama funkcja jest również dostępna jako polecenie w palecie poleceń (F1). Następujące polecenia Azure Functions są dostępne:

|Polecenie Azure Functions  | Opis  |
|---------|---------|
|**Dodaj nowe ustawienia**  |  Tworzy nowe ustawienie aplikacji na platformie Azure. Aby dowiedzieć się więcej, zobacz [Publikowanie ustawień aplikacji](#publish-application-settings). Może być również konieczne [pobranie tego ustawienia do ustawień lokalnych](#download-settings-from-azure). |
| **Skonfiguruj źródło wdrożenia** | Nawiązuje połączenie aplikacji funkcji na platformie Azure z lokalnym repozytorium git. Aby dowiedzieć się więcej, zobacz [wdrażanie ciągłe dla Azure Functions](functions-continuous-deployment.md). |
| **Połącz z repozytorium GitHub** | Łączy aplikację funkcji z repozytorium GitHub. |
| **Kopiuj adres URL funkcji** | Pobiera zdalny adres URL funkcji wyzwalanej przez protokół HTTP działającej na platformie Azure. Aby dowiedzieć się więcej, zobacz [pobieranie adresu URL wdrożonej funkcji](#get-the-url-of-the-deployed-function). |
| **Tworzenie aplikacji funkcji na platformie Azure** | Tworzy nową aplikację funkcji w subskrypcji na platformie Azure. Aby dowiedzieć się więcej, zobacz sekcję dotyczącą [publikowania w nowej aplikacji funkcji na platformie Azure](#publish-to-azure).        |
| **Odszyfruj ustawienia** | Odszyfrowuje [Ustawienia lokalne](#local-settings-file) , które zostały zaszyfrowane za pomocą **Azure Functions: Szyfruj ustawienia**.  |
| **Usuń aplikacja funkcji** | Usuwa aplikację funkcji z subskrypcji na platformie Azure. Jeśli nie ma żadnych innych aplikacji w planie App Service, masz możliwość usunięcia tego programu. Inne zasoby, takie jak konta magazynu i grupy zasobów, nie są usuwane. Aby usunąć wszystkie zasoby, należy zamiast tego [usunąć grupę zasobów](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Nie dotyczy to projektu lokalnego. |
|**DELETE — funkcja**  | Usuwa istniejącą funkcję z aplikacji funkcji na platformie Azure. Ponieważ to usunięcie nie ma wpływu na projekt lokalny, należy rozważyć usunięcie funkcji lokalnie, a następnie [ponowne opublikowanie projektu](#republish-project-files). |
| **Usuń serwer proxy** | Usuwa serwer proxy Azure Functions z aplikacji funkcji na platformie Azure. Aby dowiedzieć się więcej na temat serwerów proxy, zobacz [Work with serwery proxy usługi Azure Functions](functions-proxies.md). |
| **Usuń ustawienie** | Usuwa ustawienie aplikacji funkcji na platformie Azure. To usunięcie nie ma wpływu na ustawienia w local.settings.jspliku. |
| **Odłącz od repozytorium**  | Usuwa połączenie [ciągłego wdrażania](functions-continuous-deployment.md) między aplikacją funkcji na platformie Azure a repozytorium kontroli źródła. |
| **Pobierz Ustawienia zdalne** | Pobiera ustawienia z wybranej aplikacji funkcji na platformie Azure do local.settings.jspliku. Jeśli plik lokalny jest szyfrowany, zostaje odszyfrowany, zaktualizowany i zaszyfrowany ponownie. Jeśli istnieją jakieś ustawienia, które zawierają wartości powodujące konflikt w dwóch lokalizacjach, zostanie wyświetlony monit o wybranie metody, która ma zostać wykonana. Przed uruchomieniem tego polecenia Pamiętaj, aby zapisać zmiany w local.settings.jspliku. |
| **Edytuj ustawienia** | Zmienia wartość istniejącego ustawienia aplikacji funkcji na platformie Azure. To polecenie nie ma wpływu na ustawienia w local.settings.jspliku.  |
| **Szyfruj ustawienia** | Szyfruje poszczególne elementy w `Values` tablicy w [ustawieniach lokalnych](#local-settings-file). W tym pliku `IsEncrypted` jest również ustawiona na `true` , co oznacza, że lokalne środowisko uruchomieniowe będzie odszyfrować ustawienia przed ich użyciem. Szyfruj ustawienia lokalne, aby zmniejszyć ryzyko przeciekania cennych informacji. Na platformie Azure ustawienia aplikacji są zawsze przechowywane jako zaszyfrowane. |
| **Wykonaj funkcję teraz** | Ręcznie uruchamia [funkcję wyzwalaną czasomierz](functions-bindings-timer.md) na platformie Azure. To polecenie służy do testowania. Aby dowiedzieć się więcej na temat wyzwalania funkcji innych niż HTTP na platformie Azure, zobacz [Ręczne uruchamianie funkcji niewyzwalanej przez protokół http](functions-manually-run-non-http.md). |
| **Zainicjuj projekt do użycia z VS Code** | Dodaje wymagane pliki projektu Visual Studio Code do istniejącego projektu funkcji. Użyj tego polecenia, aby współpracować z projektem utworzonym za pomocą podstawowych narzędzi. |
| **Instalowanie lub aktualizowanie Azure Functions Core Tools** | Instaluje lub aktualizuje [Azure Functions Core Tools], który jest używany do lokalnego uruchamiania funkcji. |
| **Ponowne wdrożenie**  | Umożliwia ponowne wdrożenie plików projektu z połączonego repozytorium git do określonego wdrożenia na platformie Azure. Aby ponownie opublikować aktualizacje lokalne z Visual Studio Code, [Opublikuj ponownie projekt](#republish-project-files). |
| **Zmień nazwę ustawień** | Zmienia nazwę klucza istniejącego ustawienia aplikacji funkcji na platformie Azure. To polecenie nie ma wpływu na ustawienia w local.settings.jspliku. Po zmianie nazwy ustawień na platformie Azure należy [pobrać te zmiany do projektu lokalnego](#download-settings-from-azure). |
| **Uruchom ponownie** | Uruchamia ponownie aplikację funkcji na platformie Azure. Wdrożenie aktualizacji powoduje również ponowne uruchomienie aplikacji funkcji. |
| **Ustaw AzureWebJobsStorage**| Ustawia wartość `AzureWebJobsStorage` Ustawienia aplikacji. To ustawienie jest wymagane przez Azure Functions. Jest on ustawiany podczas tworzenia aplikacji funkcji na platformie Azure. |
| **Początek** | Uruchamia zatrzymaną aplikację funkcji na platformie Azure. |
| **Uruchom dzienniki przesyłania strumieniowego** | Uruchamia dzienniki przesyłania strumieniowego dla aplikacji funkcji na platformie Azure. Użyj dzienników przesyłania strumieniowego podczas zdalnego rozwiązywania problemów na platformie Azure, jeśli chcesz zobaczyć informacje o rejestrowaniu w czasie niemal rzeczywistym. Aby dowiedzieć się więcej, zobacz [dzienniki przesyłania strumieniowego](#streaming-logs). |
| **Zatrzymaj** | Powoduje zatrzymanie aplikacji funkcji działającej na platformie Azure. |
| **Zatrzymywanie dzienników przesyłania strumieniowego** | Powoduje zatrzymanie dzienników przesyłania strumieniowego dla aplikacji funkcji na platformie Azure. |
| **Przełącz jako ustawienie miejsca** | Gdy ta opcja jest włączona, zapewnia, że ustawienie aplikacji będzie trwało dla danego miejsca wdrożenia. |
| **Odinstaluj Azure Functions Core Tools** | Usuwa Azure Functions Core Tools, które są wymagane przez rozszerzenie. |
| **Przekaż ustawienia lokalne** | Przekazuje ustawienia z local.settings.jsw pliku do wybranej aplikacji funkcji na platformie Azure. Jeśli plik lokalny jest szyfrowany, zostaje odszyfrowany, przekazany i zaszyfrowany ponownie. Jeśli istnieją jakieś ustawienia, które zawierają wartości powodujące konflikt w dwóch lokalizacjach, zostanie wyświetlony monit o wybranie metody, która ma zostać wykonana. Przed uruchomieniem tego polecenia Pamiętaj, aby zapisać zmiany w local.settings.jspliku. |
| **Wyświetl zatwierdzenie w usłudze GitHub** | Przedstawia najnowsze zatwierdzenie w określonym wdrożeniu, gdy aplikacja funkcji jest połączona z repozytorium. |
| **Wyświetlanie dzienników wdrożenia** | Przedstawia dzienniki określonego wdrożenia w aplikacji funkcji na platformie Azure. |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Functions Core Tools, zobacz [Work with Azure Functions Core Tools](functions-run-local.md).

Aby dowiedzieć się więcej na temat opracowywania funkcji jako bibliotek klas .NET, zobacz [Azure Functions C# Developer Reference](functions-dotnet-class-library.md). Ten artykuł zawiera również linki do przykładów użycia atrybutów do deklarowania różnych typów powiązań obsługiwanych przez Azure Functions.

[Rozszerzenie usługi Azure Functions dla programu Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md