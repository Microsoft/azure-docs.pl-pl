---
title: Tworzenie funkcji w programie go lub Rust przy użyciu Visual Studio Code-Azure Functions
description: Dowiedz się, jak utworzyć funkcję go jako niestandardową procedurę obsługi Azure Functions, a następnie opublikować projekt lokalny na potrzeby hostingu bezserwerowego w Azure Functions przy użyciu rozszerzenia Azure Functions w Visual Studio Code.
ms.topic: quickstart
ms.date: 12/4/2020
ms.openlocfilehash: 8b53031315cce3651a2de581d71da6ef6fe909c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103470374"
---
# <a name="quickstart-create-a-go-or-rust-function-in-azure-using-visual-studio-code"></a>Szybki Start: Tworzenie funkcji go lub Rust na platformie Azure przy użyciu Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

W tym artykule użyto Visual Studio Code, aby utworzyć [niestandardową funkcję obsługi](functions-custom-handlers.md) , która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions.

Niestandardowe programy obsługi mogą służyć do tworzenia funkcji w dowolnym języku lub środowisku uruchomieniowym przez uruchomienie procesu serwera HTTP. Ten artykuł obsługuje zarówno funkcję [go](create-first-function-vs-code-other.md?tabs=go) , jak i [Rust](create-first-function-vs-code-other.md?tabs=rust).

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

## <a name="configure-your-environment"></a>Konfigurowanie środowiska

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

# <a name="go"></a>[Przejdź](#tab/go)

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Program Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Rozszerzenie obsługujące usługę Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dla programu Visual Studio Code.

+ [Azure Functions Core Tools](./functions-run-local.md#v2) w wersji 3. x. Użyj `func --version` polecenia, aby sprawdzić, czy jest poprawnie zainstalowana.

+ Zalecane [jest, aby](https://golang.org/doc/install)uzyskać najnowszą wersję. Użyj `go version` polecenia, aby sprawdzić wersję.

# <a name="rust"></a>[Rust](#tab/rust)

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Program Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Rozszerzenie obsługujące usługę Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dla programu Visual Studio Code.

+ [Azure Functions Core Tools](./functions-run-local.md#v2) w wersji 3. x. Użyj `func --version` polecenia, aby sprawdzić, czy jest poprawnie zainstalowana.

+ Rust łańcucha narzędzi przy użyciu [rustup](https://www.rust-lang.org/tools/install). Użyj `rustc --version` polecenia, aby sprawdzić wersję.

---

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Utwórz projekt lokalny

W tej sekcji użyjesz Visual Studio Code, aby utworzyć projekt lokalnego Azure Functions obsługi niestandardowej. W dalszej części tego artykułu opublikujesz kod funkcji na platformie Azure.

1. Wybierz ikonę platformy Azure na pasku działań, a następnie w obszarze **Azure: Funkcje** wybierz ikonę **Utwórz nowy projekt...**.

    ![Wybieranie pozycji Utwórz nowy projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu, a następnie wybierz pozycję **Wybierz**.

    > [!NOTE]
    > Te kroki zostały zaprojektowane do ukończenia poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Podaj następujące informacje po wyświetleniu monitów:

    + **Wybierz język dla projektu funkcji**: Wybierz plik `Custom`.

    + **Wybierz szablon dla pierwszej funkcji projektu**: Wybierz plik `HTTP trigger`.

    + **Podaj nazwę funkcji**: Wpisz `HttpExample`.

    + **Poziom autoryzacji**: Wybierz `Anonymous` , co umożliwi wszystkim wywoływanie punktu końcowego funkcji. Aby dowiedzieć się więcej o poziomie autoryzacji, zobacz [klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Wybierz, jak chcesz otworzyć projekt**: Wybierz plik `Add to workspace`.

1. Korzystając z tych informacji, Visual Studio Code generuje projekt Azure Functions z funkcją wyzwalacza HTTP. Pliki projektu lokalnego można wyświetlić w Eksploratorze. Aby dowiedzieć się więcej na temat tworzonych plików, zobacz [pliki wygenerowanego projektu](functions-develop-vs-code.md#generated-project-files). 

## <a name="create-and-build-your-function"></a>Tworzenie i kompilowanie funkcji

*function.jsw* pliku w folderze *HttpExample* deklaruje funkcję wyzwalacza http. Kończenie funkcji przez dodanie procedury obsługi i skompilowanie jej do pliku wykonywalnego.

# <a name="go"></a>[Przejdź](#tab/go)

1. Naciśnij <kbd>klawisze CTRL + n</kbd> (<kbd>cmd + n</kbd> w systemie macOS), aby utworzyć nowy plik. Zapisz go jako *procedurę obsługi. Przejdź* do katalogu głównego aplikacji funkcji (w tym samym folderze co *host.json*).

1. W obszarze *obsługi. Przejdź* do Dodaj następujący kod i Zapisz plik. To jest obsługa niestandardowa języka go.

    ```go
    package main
    
    import (
        "fmt"
        "log"
        "net/http"
        "os"
    )
    
    func helloHandler(w http.ResponseWriter, r *http.Request) {
        message := "This HTTP triggered function executed successfully. Pass a name in the query string for a personalized response.\n"
        name := r.URL.Query().Get("name")
        if name != "" {
            message = fmt.Sprintf("Hello, %s. This HTTP triggered function executed successfully.\n", name)
        }
        fmt.Fprint(w, message)
    }
    
    func main() {
        listenAddr := ":8080"
        if val, ok := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT"); ok {
            listenAddr = ":" + val
        }
        http.HandleFunc("/api/HttpExample", helloHandler)
        log.Printf("About to listen on %s. Go to https://127.0.0.1%s/", listenAddr, listenAddr)
        log.Fatal(http.ListenAndServe(listenAddr, nil))
    }
    ```

1. Naciśnij <kbd>klawisze Ctrl + Shift + '</kbd> lub wybierz pozycję *Nowy terminal* z menu *Terminal* , aby otworzyć nowy, zintegrowany terminal w vs Code.

1. Skompiluj niestandardową obsługę przy użyciu następującego polecenia. Plik wykonywalny o nazwie `handler` ( `handler.exe` w systemie Windows) jest wyprowadzany w folderze głównym aplikacji funkcji.

    ```bash
    go build handler.go
    ```

    ![Procedura obsługi niestandardowej kompilacji VS Code](./media/functions-create-first-function-vs-code/functions-vscode-go.png)

# <a name="rust"></a>[Rust](#tab/rust)

1. Naciśnij <kbd>klawisze Ctrl + Shift + '</kbd> lub wybierz pozycję *Nowy terminal* z menu *Terminal* , aby otworzyć nowy, zintegrowany terminal w vs Code.

1. W katalogu głównym aplikacji funkcji (ten sam folder, w którym *host.json*) zainicjuj projekt Rust o nazwie `handler` .

    ```bash
    cargo init --name handler
    ```

1. W polu *ładunek. toml* Dodaj następujące zależności niezbędne do wykonania tego przewodnika Szybki Start. W przykładzie zastosowano strukturę serwera sieci Web [osnowy](https://docs.rs/warp/) .

    ```toml
    [dependencies]
    warp = "0.3"
    tokio = { version = "1", features = ["rt", "macros", "rt-multi-thread"] }
    ```

1. W polu *src/Main. RS* Dodaj następujący kod i Zapisz plik. To jest program obsługi niestandardowej Rust.

    ```rust
    use std::collections::HashMap;
    use std::env;
    use std::net::Ipv4Addr;
    use warp::{http::Response, Filter};

    #[tokio::main]
    async fn main() {
        let example1 = warp::get()
            .and(warp::path("api"))
            .and(warp::path("HttpExample"))
            .and(warp::query::<HashMap<String, String>>())
            .map(|p: HashMap<String, String>| match p.get("name") {
                Some(name) => Response::builder().body(format!("Hello, {}. This HTTP triggered function executed successfully.", name)),
                None => Response::builder().body(String::from("This HTTP triggered function executed successfully. Pass a name in the query string for a personalized response.")),
            });

        let port_key = "FUNCTIONS_CUSTOMHANDLER_PORT";
        let port: u16 = match env::var(port_key) {
            Ok(val) => val.parse().expect("Custom Handler port is not a number!"),
            Err(_) => 3000,
        };

        warp::serve(example1).run((Ipv4Addr::UNSPECIFIED, port)).await
    }
    ```

1. Kompiluj plik binarny dla niestandardowej procedury obsługi. Plik wykonywalny o nazwie `handler` ( `handler.exe` w systemie Windows) jest wyprowadzany w folderze głównym aplikacji funkcji.

    ```bash
    cargo build --release
    cp target/release/handler .
    ```

    ![VS Code — procedura obsługi niestandardowej Rust kompilacji](./media/functions-create-first-function-vs-code/functions-vscode-rust.png)

---

## <a name="configure-your-function-app"></a>Konfigurowanie aplikacji funkcji

Host funkcji musi być skonfigurowany do uruchamiania pliku binarnego obsługi niestandardowej podczas jego uruchamiania.

1. Otwórz *host.jsna*.

1. W `customHandler.description` sekcji Ustaw wartość `defaultExecutablePath` na `handler` (w systemie Windows Ustaw ją na `handler.exe` ).

1. W `customHandler` sekcji Dodaj właściwość o nazwie `enableForwardingHttpRequest` i ustaw jej wartość na `true` . W przypadku funkcji składających się tylko z wyzwalacza HTTP, to ustawienie upraszcza programowanie, umożliwiając pracę z typowym żądaniem HTTP zamiast [ładunkiem żądania](functions-custom-handlers.md#request-payload)obsługi niestandardowej.

1. Potwierdź, że `customHandler` sekcja wygląda podobnie do tego przykładu. Zapisz plik.

    ```
    "customHandler": {
      "description": {
        "defaultExecutablePath": "handler",
        "workingDirectory": "",
        "arguments": []
      },
      "enableForwardingHttpRequest": true
    }
    ```

Aplikacja funkcji jest skonfigurowana do uruchamiania pliku wykonywalnego programu obsługi niestandardowej.

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Ten projekt można uruchomić na lokalnym komputerze deweloperskim przed opublikowaniem na platformie Azure.

1. W zintegrowanym terminalu uruchom aplikację funkcji przy użyciu Azure Functions Core Tools.

    ```bash
    func start
    ```

1. Po uruchomieniu podstawowych narzędzi przejdź do następującego adresu URL, aby wykonać żądanie GET, które zawiera `?name=Functions` ciąg zapytania.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Zostanie zwrócona odpowiedź, która wygląda podobnie do następującej w przeglądarce:

    ![Przeglądarka — przykładowe dane wyjściowe hosta lokalnego](./media/create-first-function-vs-code-other/functions-test-local-browser.png)

1. Informacje o żądaniu są wyświetlane w panelu **terminalu** .

    ![Rozpoczęcie VS Code hosta zadań — dane wyjściowe terminalu](../../includes/media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Naciśnij <kbd>klawisze CTRL + C</kbd> , aby zatrzymać podstawowe narzędzia.

Po sprawdzeniu, że funkcja działa poprawnie na komputerze lokalnym, można użyć Visual Studio Code do opublikowania projektu bezpośrednio na platformie Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="compile-the-custom-handler-for-azure"></a>Kompiluj obsługę niestandardową dla platformy Azure

Ta sekcja umożliwia opublikowanie projektu na platformie Azure w aplikacji funkcji z systemem Linux. W większości przypadków należy ponownie skompilować plik binarny i dostosować konfigurację tak, aby była zgodna z platformą docelową przed opublikowaniem na platformie Azure.

# <a name="go"></a>[Przejdź](#tab/go)

1. W zintegrowanym terminalu Skompiluj procedurę obsługi do systemu Linux/x64. Plik binarny o nazwie `handler` jest tworzony w katalogu głównym aplikacji funkcji.

    # <a name="macos"></a>[macOS](#tab/macos)

    ```bash
    GOOS=linux GOARCH=amd64 go build handler.go
    ```

    # <a name="linux"></a>[Linux](#tab/linux)

    ```bash
    GOOS=linux GOARCH=amd64 go build handler.go
    ```

    # <a name="windows"></a>[Windows](#tab/windows)
    ```cmd
    set GOOS=linux
    set GOARCH=amd64
    go build handler.go
    ```

    Zmień wartość `defaultExecutablePath` w *host.jsna* od `handler.exe` do `handler` . Powoduje to, że aplikacja funkcji ma uruchamiać plik binarny systemu Linux.
    
    ---

# <a name="rust"></a>[Rust](#tab/rust)

1. Utwórz plik o *. ładunku/konfiguracji*. Dodaj następującą zawartość i Zapisz plik.

    ```
    [target.x86_64-unknown-linux-musl]
    linker = "rust-lld"
    ```

1. W zintegrowanym terminalu Skompiluj procedurę obsługi do systemu Linux/x64. Utworzono plik binarny o nazwie `handler` . Skopiuj go do katalogu głównego aplikacji funkcji.

    ```bash
    rustup target add x86_64-unknown-linux-musl
    cargo build --release --target=x86_64-unknown-linux-musl
    cp target/x86_64-unknown-linux-musl/release/handler .
    ```

1. W przypadku korzystania z systemu Windows zmień wartość `defaultExecutablePath` w *host.jsna* z `handler.exe` na `handler` . Powoduje to, że aplikacja funkcji ma uruchamiać plik binarny systemu Linux.

1. Dodaj następujący wiersz do pliku *funcignore* :

    ```
    target
    ```

    Zapobiega to opublikowaniu zawartości folderu *docelowego* .

---

## <a name="publish-the-project-to-azure"></a>Publikowanie projektu na platformie Azure

W tej sekcji utworzysz aplikację funkcji i powiązane zasoby w ramach subskrypcji platformy Azure, a następnie wdrożono swój kod. 

> [!IMPORTANT]
> Publikowanie do istniejącej aplikacji funkcji spowoduje zastąpienie zawartości tej aplikacji na platformie Azure. 


1. Wybierz ikonę platformy Azure na pasku działania, a następnie w obszarze **Azure: Functions** wybierz przycisk **Wdróż do aplikacji funkcji...** .

    ![Publikowanie projektu na platformie Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Podaj następujące informacje po wyświetleniu monitów:

    + **Wybierz folder**: Wybierz folder z obszaru roboczego lub przejdź do folderu, który zawiera aplikację funkcji. Nie zobaczysz tego, jeśli masz już otwartą prawidłową aplikację funkcji.

    + **Wybierz subskrypcję**: wybierz subskrypcję do użycia. Nie zobaczysz tego, jeśli masz tylko jedną subskrypcję.

    + **Wybierz aplikację funkcji na platformie Azure**: Wybierz plik `+ Create new Function App (advanced)`. 
    
        > [!IMPORTANT]
        > `advanced`Opcja pozwala wybrać konkretny system operacyjny, na którym działa aplikacja funkcji na platformie Azure, co w tym przypadku jest systemem Linux.

        ![VS Code — wybierz pozycję Zaawansowane Tworzenie nowej aplikacji funkcji](./media/functions-create-first-function-vs-code/functions-vscode-create-azure-advanced.png)

    + **Wprowadź globalnie unikatową nazwę aplikacji funkcji**: wpisz nazwę, która jest prawidłowa w ścieżce URL. Wpisana nazwa zostanie sprawdzona, aby upewnić się, że jest ona unikatowa w Azure Functions.

    + **Wybierz stos środowiska uruchomieniowego**: Wybierz `Custom Handler` .

    + **Wybierz system operacyjny**: Wybierz `Linux` .

    + **Wybierz plan hostingu**: Wybierz `Consumption` .

    + **Wybierz grupę zasobów**: Wybierz `+ Create new resource group` . Wprowadź nazwę grupy zasobów. Ta nazwa musi być unikatowa w ramach subskrypcji platformy Azure. Możesz użyć proponowanej nazwy w wierszu polecenia.

    + **Wybierz konto magazynu**: wybierz opcję `+ Create new storage account` . Ta nazwa musi być globalnie unikatowa w ramach platformy Azure. Możesz użyć proponowanej nazwy w wierszu polecenia.

    + **Wybierz zasób Application Insights**: wybierz opcję `+ Create Application Insights resource` . Ta nazwa musi być globalnie unikatowa w ramach platformy Azure. Możesz użyć proponowanej nazwy w wierszu polecenia.

    + **Wybierz lokalizację dla nowych zasobów**: Aby uzyskać lepszą wydajność, wybierz [region](https://azure.microsoft.com/regions/) w sąsiedztwie. Rozszerzenie pokazuje stan poszczególnych zasobów w miarę ich tworzenia na platformie Azure w obszarze powiadomień.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Powiadomienie o tworzeniu zasobów platformy Azure":::

1. Po zakończeniu następujące zasoby platformy Azure są tworzone w ramach subskrypcji:

    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. 

4. Wybierz pozycję **Wyświetl dane wyjściowe** w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym utworzone zasoby platformy Azure. W przypadku odrzucenia powiadomienia wybierz ikonę dzwonka w prawym dolnym rogu, aby zobaczyć ją ponownie.

    ![Utwórz pełne powiadomienie](./media/functions-create-first-function-vs-code/function-create-notifications.png)

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Azure Functions niestandardowych obsługi](functions-custom-handlers.md)
