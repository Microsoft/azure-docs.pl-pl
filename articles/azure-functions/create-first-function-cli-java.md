---
title: Tworzenie funkcji języka Java przy użyciu wiersza polecenia — Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka Java z poziomu wiersza polecenia, a następnie opublikować projekt lokalny na potrzeby hostingu bezserwerowego w Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-java-uiex
ms.openlocfilehash: 0ef52da8400cfadfaaa26664d984d8b898a3b9a3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729304"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Szybki Start: Tworzenie funkcji języka Java na platformie Azure z poziomu wiersza polecenia

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

W tym artykule opisano użycie narzędzi wiersza polecenia w celu utworzenia funkcji języka Java, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions.

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

> [!NOTE]
> Jeśli Maven nie jest preferowanym narzędziem programistycznym, zapoznaj się z naszymi samouczkami dotyczącymi deweloperów języka Java za pomocą [Gradle](./functions-create-first-java-gradle.md), [IntelliJ pomysłu](/azure/developer/java/toolkit-for-intellij/quickstart-functions) i [Visual Studio Code](create-first-function-vs-code-java.md).

## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem należy wykonać następujące czynności:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) wersja 3. x...

+ [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2,4 lub nowszej.

+ [Zestaw Java developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), wersja 8 lub 11. `JAVA_HOME`Zmienna środowiskowa musi być ustawiona na lokalizację instalacji poprawnej wersji JDK.     

+ [Apache Maven](https://maven.apache.org), wersja 3,0 lub nowsza.

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

+ W terminalu lub oknie poleceń Uruchom polecenie, `func --version` Aby sprawdzić, czy Azure Functions Core Tools są w wersji 3. x.

+ Uruchom, `az --version` Aby sprawdzić, czy wersja interfejsu wiersza polecenia platformy Azure to 2,4 lub nowszego.

+ Uruchom, `az login` Aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

## <a name="create-a-local-function-project"></a>Utwórz projekt funkcji lokalnej

W Azure Functions, projekt funkcji jest kontenerem dla jednej lub kilku poszczególnych funkcji, które reagują na konkretny wyzwalacz. Wszystkie funkcje w projekcie mają takie same konfiguracje lokalne i hostingowe. W tej sekcji utworzysz projekt funkcji zawierający pojedynczą funkcję.

1. W pustym folderze uruchom następujące polecenie, aby wygenerować projekt usługi Functions z [archetypu narzędzia Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html). 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
    ```
    
    # <a name="powershell"></a>[Program PowerShell](#tab/powershell)
    
    ```powershell
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
    ```
    
    ---

    > [!IMPORTANT]
    > + Użyj polecenia `-DjavaVersion=11`, jeśli chcesz, aby funkcje działały w języku Java 11. Aby dowiedzieć się więcej, zobacz [wersje Java](functions-reference-java.md#java-versions). 
    > + `JAVA_HOME`Aby można było ukończyć ten artykuł, zmienna środowiskowa musi być ustawiona na lokalizację instalacji poprawnej wersji JDK.

1. Maven prosi o podanie wartości, które są potrzebne, aby zakończyć Generowanie projektu przy wdrożeniu.   
    Po wyświetleniu monitu podaj następujące wartości:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | Wartość, która jednoznacznie identyfikuje projekt we wszystkich projektach, zgodnie z [regułami nazewnictwa pakietów](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) dla języka Java. |
    | **artifactId** | `fabrikam-functions` | Wartość, która jest nazwą jar, bez numeru wersji. |
    | **Wersja** | `1.0-SNAPSHOT` | Wybierz wartość domyślną. |
    | **Package** | `com.fabrikam` | Wartość, która jest pakiet języka Java dla wygenerowanego kodu funkcji. Użyj wartości domyślnej. |

1. Wpisz `Y` lub naciśnij klawisz ENTER, aby potwierdzić.

    Maven tworzy pliki projektu w nowym folderze o nazwie _artifactId_, w tym przykładzie `fabrikam-functions` . 

1. Przejdź do folderu projektu:

    ```console
    cd fabrikam-functions
    ```

    Ten folder zawiera różne pliki dla projektu, w tym pliki konfiguracji o nazwie [local.settings.js](functions-run-local.md#local-settings-file) i [host.jsna](functions-host-json.md). Ponieważ *local.settings.json* może zawierać wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *. gitignore* .

### <a name="optional-examine-the-file-contents"></a>Obowiązkowe Sprawdzanie zawartości pliku

W razie potrzeby możesz pominąć, aby [uruchomić funkcję lokalnie](#run-the-function-locally) i później przejrzeć zawartość pliku.

#### <a name="functionjava"></a>Function. Java
*Funkcja. Java* zawiera `run` metodę, która odbiera dane żądania w `request` zmiennej to [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) , który jest uzupełniony adnotacją [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) , która definiuje zachowanie wyzwalacza. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Komunikat odpowiedzi jest generowany przez interfejs API [HttpResponseMessage. Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) .

#### <a name="pomxml"></a>pom.xml

Ustawienia zasobów platformy Azure utworzonych na potrzeby hostowania aplikacji są definiowane w elemencie **konfiguracji** wtyczki z identyfikatorem **grupy** `com.microsoft.azure` w wygenerowanym pliku pom.xml. Na przykład poniższy element konfiguracji instruuje wdrożenie oparte na Maven, aby utworzyć aplikację funkcji w `java-functions-group` grupie zasobów w `westus` regionie. Sama aplikacja funkcji działa w systemie Windows hostowanym w `java-functions-app-service-plan` planie, który domyślnie jest planem zużycia bezserwerowego.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

Możesz zmienić te ustawienia, aby kontrolować sposób tworzenia zasobów na platformie Azure, na przykład przez zmianę `runtime.os` z `windows` na `linux` przed początkowym wdrożeniem. Aby uzyskać pełną listę ustawień obsługiwanych przez wtyczkę Maven, zobacz [szczegóły konfiguracji](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).

#### <a name="functiontestjava"></a>FunctionTest. Java

Archetype generuje również test jednostkowy dla funkcji. W przypadku zmiany funkcji w celu dodania powiązań lub dodania nowych funkcji do projektu należy również zmodyfikować testy w pliku *FunctionTest. Java* .

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

1. Uruchom funkcję przez uruchomienie lokalnego hosta Azure Functions środowiska uruchomieniowego z folderu *LocalFunctionProj* :

    ```console
    mvn clean package
    mvn azure-functions:run
    ```
    
    W kierunku końca danych wyjściowych powinny być wyświetlane następujące wiersze:
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    > [!NOTE]  
    > Jeśli HttpExample nie pojawia się jak pokazano powyżej, prawdopodobnie uruchomiono hosta spoza folderu głównego projektu. W takim przypadku użyj **klawiszy CTRL** + **C** , aby zatrzymać hosta, przejdź do folderu głównego projektu i ponownie uruchom poprzednie polecenie.

1. Skopiuj adres URL `HttpExample` funkcji z tego danych wyjściowych do przeglądarki i dołącz ciąg zapytania `?name=<YOUR_NAME>` , wprowadzając pełny adres URL, taki jak `http://localhost:7071/api/HttpExample?name=Functions` . W przeglądarce powinien zostać wyświetlony następujący komunikat `Hello Functions` :

    ![Wynik funkcji uruchomionej lokalnie w przeglądarce](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    Na terminalu, w którym rozpoczął się projekt, są również wyświetlane dane wyjściowe dziennika podczas zgłaszania żądań.

1. Gdy skończysz, użyj **klawiszy CTRL** + **C** i wybierz opcję `y` zatrzymania hosta funkcji.

## <a name="deploy-the-function-project-to-azure"></a>Wdróż projekt funkcji na platformie Azure

Aplikacja funkcji i powiązane zasoby są tworzone na platformie Azure podczas pierwszego wdrożenia projektu usługi Functions. Ustawienia zasobów platformy Azure utworzonych na potrzeby hostowania aplikacji są zdefiniowane w [ plikupom.xml](#pomxml). W tym artykule zaakceptujesz ustawienia domyślne.

> [!TIP]
> Aby utworzyć aplikację funkcji działającą w systemie Linux zamiast systemu Windows, Zmień `runtime.os` element w pliku pom.xml z `windows` na `linux` . W [tych regionach](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)jest obsługiwany system Linux w planie zużycia. W tej samej grupie zasobów nie można używać aplikacji działających w systemie Linux i aplikacji działających w ramach systemu Windows.

1. Przed wdrożeniem programu Zaloguj się do subskrypcji platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. 

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Polecenie [AZ login umożliwia zalogowanie](/cli/azure/reference-index#az-login) się do konta platformy Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Polecenie cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) umożliwia zalogowanie się do konta platformy Azure.

    ---

1. Użyj następującego polecenia, aby wdrożyć projekt w nowej aplikacji funkcji.

    ```console
    mvn azure-functions:deploy
    ```
    
    Spowoduje to utworzenie następujących zasobów na platformie Azure:
    
    + Grupa zasobów. Nazwana jako _Java-Functions-Group_.
    + Konto magazynu. Wymagane przez funkcje. Nazwa jest generowana losowo na podstawie wymagań dotyczących nazw kont magazynu.
    + Plan hostingu. Hosting bezserwerowy dla aplikacji funkcji w regionie _zachodnim_ . Nazwa to _Java-Functions-App-Service-plan_.
    + Aplikacja funkcji. Aplikacja funkcji jest jednostką wdrażania i wykonywania dla funkcji. Nazwa jest generowana losowo na podstawie _artifactId_, z uwzględnieniem losowo wygenerowanego numeru.
    
    Wdrożenie pakuje pliki projektu i wdraża je w nowej aplikacji funkcji przy użyciu polecenia [zip Deployment](functions-deployment-technologies.md#zip-deploy). Kod jest uruchamiany z pakietu wdrożeniowego na platformie Azure.

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przejdziesz do [następnego kroku](#next-steps) i dodasz powiązanie danych wyjściowych kolejki usługi Azure Storage, Zachowaj wszystkie swoje zasoby w miejscu, w którym będziesz kompilować zawartość już wykonane.

W przeciwnym razie użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby, aby uniknąć ponoszenia dalszych kosztów.

 # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az group delete --name java-functions-group
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name java-functions-group
```

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
