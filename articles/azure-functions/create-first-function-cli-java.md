---
title: Tworzenie funkcji języka Java z wiersza polecenia — Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka Java z wiersza polecenia, a następnie opublikować projekt lokalny w hostingu bez serwera w Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
- devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-java-uiex
ms.openlocfilehash: 69c4129feacf36b6b98cdcd7c2f4f5f36d7839b2
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831928"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Szybki start: tworzenie funkcji języka Java na platformie Azure z wiersza polecenia

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

W tym artykule użyjemy narzędzi wiersza polecenia do utworzenia funkcji języka Java, która odpowiada na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bez serwera Azure Functions.

Wykonanie kroków tego przewodnika Szybki start wiąże się z niewielkim kosztem konta platformy Azure w wysokości nie mniejszej niż kilka centów USD.

> [!NOTE]
> Jeśli narzędzie Maven nie jest preferowanym narzędziem dewelopera, zapoznaj się z naszymi podobnymi samouczkami dla deweloperów języka Java korzystającymi z narzędzi [Gradle,](./functions-create-first-java-gradle.md) [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) [i Visual Studio Code](create-first-function-vs-code-java.md).

## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem musisz mieć następujące elementy:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Wersja [Azure Functions Core Tools](functions-run-local.md#v2) 3.x..

+ Interfejs [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.4 lub nowszej.

+ Zestaw [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support)w wersji 8 lub 11. Zmienna `JAVA_HOME` środowiskowa musi być ustawiona na lokalizację instalacji poprawnej wersji zestawu JDK.     

+ [Apache Maven,](https://maven.apache.org)wersja 3.0 lub nowsza.

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

+ W terminalu lub oknie polecenia uruchom polecenie , aby sprawdzić, `func --version` czy Azure Functions Core Tools są w wersji 3.x.

+ Uruchom `az --version` narzędzie , aby sprawdzić, czy interfejs wiersza polecenia platformy Azure jest w wersji 2.4 lub nowszej.

+ Uruchom, `az login` aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

## <a name="create-a-local-function-project"></a>Tworzenie lokalnego projektu funkcji

W Azure Functions projekt funkcji jest kontenerem dla co najmniej jednej funkcji, która odpowiada na określony wyzwalacz. Wszystkie funkcje w projekcie mają te same konfiguracje lokalne i konfiguracje hostingu. W tej sekcji utworzysz projekt funkcji, który zawiera jedną funkcję.

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
    > + Użyj polecenia `-DjavaVersion=11`, jeśli chcesz, aby funkcje działały w języku Java 11. Aby dowiedzieć się więcej, zobacz [Wersje języka Java](functions-reference-java.md#java-versions). 
    > + Aby ukończyć ten artykuł, zmienna środowiskowa musi być ustawiona na lokalizację instalacji odpowiedniej wersji zestawu `JAVA_HOME` JDK.

1. Program Maven prosi o wartości potrzebne do ukończenia generowania projektu podczas wdrażania.   
    Po wyświetleniu monitu podaj następujące wartości:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | Wartość, która jednoznacznie identyfikuje projekt we wszystkich projektach, zgodnie z regułami [nazewnictwa pakietów](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) dla języka Java. |
    | **artifactId** | `fabrikam-functions` | Wartość, która jest nazwą pliku jar, bez numeru wersji. |
    | **Wersja** | `1.0-SNAPSHOT` | Wybierz wartość domyślną. |
    | **Pakiet** | `com.fabrikam` | Wartość, która jest pakietem Java dla wygenerowanego kodu funkcji. Użyj wartości domyślnej. |

1. Wpisz `Y` lub naciśnij klawisz Enter, aby potwierdzić.

    Program Maven tworzy pliki projektu w nowym folderze o nazwie _artifactId_, która w tym przykładzie to `fabrikam-functions` . 

1. Przejdź do folderu projektu:

    ```console
    cd fabrikam-functions
    ```

    Ten folder zawiera różne pliki projektu, w tym pliki konfiguracji o [nazwachlocal.settings.jsi](functions-run-local.md#local-settings-file) [host.jsna .](functions-host-json.md) Ponieważ *local.settings.jsmoże zawierać* wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *.gitignore.*

### <a name="optional-examine-the-file-contents"></a>(Opcjonalnie) Badanie zawartości pliku

W razie potrzeby możesz przejść do [lokalnego](#run-the-function-locally) uruchomienia funkcji i później zbadać zawartość pliku.

#### <a name="functionjava"></a>Function.java
*Plik Function.java* zawiera metodę, która odbiera dane żądania w zmiennej, to `run` `request` element [HttpRequestMessage,](/java/api/com.microsoft.azure.functions.httprequestmessage) który jest dekorowany adnotacją [HttpTrigger,](/java/api/com.microsoft.azure.functions.annotation.httptrigger) która definiuje zachowanie wyzwalacza. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Komunikat odpowiedzi jest generowany przez interfejs API [HttpResponseMessage.Builder.](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder)

#### <a name="pomxml"></a>pom.xml

Ustawienia zasobów platformy Azure utworzonych do hostowania aplikacji są definiowane w elemencie konfiguracji wtyczki z elementem **groupId** w wygenerowanym pom.xml  `com.microsoft.azure` pliku. Na przykład poniższy element konfiguracji instruuje wdrożenie oparte na programie Maven, aby utworzyć aplikację funkcji w grupie zasobów `java-functions-group` w `westus` regionie. Sama aplikacja funkcji działa w systemie Windows hostowany w planie, który domyślnie jest planem użycia `java-functions-app-service-plan` bez serwera.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

Możesz zmienić te ustawienia, aby kontrolować sposób tworzenia zasobów na platformie Azure, na przykład zmieniając ustawienia `runtime.os` z `windows` na przed `linux` początkowym wdrożeniem. Aby uzyskać pełną listę ustawień obsługiwanych przez wtyczkę Maven, zobacz [szczegóły konfiguracji](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).

#### <a name="functiontestjava"></a>FunctionTest.java

Archetyp generuje również test jednostkowy dla funkcji. W przypadku zmiany funkcji w celu dodania powiązań lub dodania nowych funkcji do projektu należy również zmodyfikować testy w pliku *FunctionTest.java.*

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

1. Uruchom funkcję, uruchamiać lokalnego hosta Azure Functions uruchomieniowego z folderu *LocalFunctionProj:*

    ```console
    mvn clean package
    mvn azure-functions:run
    ```
    
    Pod koniec danych wyjściowych powinny pojawić się następujące wiersze:
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    > [!NOTE]  
    > Jeśli przykład HttpExample nie jest wyświetlany, jak pokazano powyżej, prawdopodobnie uruchomiliśmy hosta spoza folderu głównego projektu. W takim przypadku użyj **klawisza Ctrl** C, aby zatrzymać hosta, przejdź do folderu głównego projektu +  i ponownie uruchom poprzednie polecenie.

1. Skopiuj adres URL funkcji z tych danych wyjściowych do przeglądarki i dołącz ciąg zapytania , dzięki czemu pełny `HttpExample` adres URL będzie taki jak `?name=<YOUR_NAME>` `http://localhost:7071/api/HttpExample?name=Functions` . W przeglądarce powinien zostać wyświetlony komunikat, który zwraca wartość ciągu zapytania. Terminal, w którym uruchomiliśmy projekt, również wyświetla dane wyjściowe dziennika podczas żądań.

1. Gdy wszystko będzie gotowe, użyj **klawisza Ctrl** + **C** i `y` zatrzymaj hosta funkcji.

## <a name="deploy-the-function-project-to-azure"></a>Wdrażanie projektu funkcji na platformie Azure

Aplikacja funkcji i powiązane zasoby są tworzone na platformie Azure podczas pierwszego wdrażania projektu funkcji. Ustawienia dla zasobów platformy Azure utworzonych w celu hostować aplikację są zdefiniowane w [pom.xml pliku](#pomxml). W tym artykule zaakceptujesz wartości domyślne.

> [!TIP]
> Aby utworzyć aplikację funkcji uruchamianą w systemie Linux zamiast w systemie Windows, zmień `runtime.os` element w pliku pom.xml z na `windows` `linux` . Uruchamianie systemu Linux w planie zużycia jest obsługiwane w [tych regionach.](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions) W tej samej grupie zasobów nie można mieć aplikacji uruchamianych w systemie Linux i aplikacji uruchamianych w systemie Windows.

1. Przed wdrożeniem zaloguj się do subskrypcji platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. 

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Polecenie [az login](/cli/azure/reference-index#az_login) umożliwia zalogowanie się do konta platformy Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Polecenie cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) umożliwia wykonanie połączeń z kontem platformy Azure.

    ---

1. Użyj następującego polecenia, aby wdrożyć projekt w nowej aplikacji funkcji.

    ```console
    mvn azure-functions:deploy
    ```
    
    Powoduje to utworzenie następujących zasobów na platformie Azure:
    
    + Grupa zasobów. Nazwane jako _java-functions-group_.
    + Konto magazynu. Wymagane przez funkcje. Nazwa jest generowana losowo na podstawie wymagań dotyczących nazwy konta magazynu.
    + Plan hostingu. Hosting bez serwera dla aplikacji funkcji w regionie _westus._ Nazwa to _java-functions-app-service-plan_.
    + Aplikacja funkcji. Aplikacja funkcji to jednostka wdrażania i wykonywania dla funkcji. Nazwa jest generowana losowo na podstawie twojego _artifactId_ i jest dołączana z losowo wygenerowaną liczbą.
    
    Wdrożenie pakuje pliki projektu i wdraża je w nowej aplikacji funkcji przy użyciu [wdrożenia zip.](functions-deployment-technologies.md#zip-deploy) Kod jest uruchamiany z pakietu wdrożeniowego na platformie Azure.

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli dodasz [powiązanie](#next-steps) wyjściowe kolejki usługi Azure Storage do następnego kroku, zachowaj wszystkie zasoby na miejscu, ponieważ będziesz tworzyć na podstawie już wykonanej pracy.

W przeciwnym razie użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby, aby uniknąć dodatkowych kosztów.

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
