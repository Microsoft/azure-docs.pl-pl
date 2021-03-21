---
title: Tworzenie funkcji języka Java przy użyciu wiersza polecenia — Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka Java z poziomu wiersza polecenia, a następnie opublikować projekt lokalny na potrzeby hostingu bezserwerowego w Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 32d91f01fcebec3e1ae0d19b1bff29a71f41c0d1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050430"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Szybki Start: Tworzenie funkcji języka Java na platformie Azure z poziomu wiersza polecenia

> [!div class="op_single_selector" title1="Wybierz język funkcji: "]
> - [Java](create-first-function-cli-java.md)
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [Program PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Użyj narzędzi wiersza polecenia, aby utworzyć funkcję języka Java, która reaguje na żądania HTTP. Przetestuj kod lokalnie, a następnie wdróż go w środowisku bezserwerowym Azure Functions.

Po ukończeniu tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej <abbr title="Profil, który przechowuje informacje o rozliczeniach dotyczące użycia platformy Azure.">Konto platformy Azure</abbr>.

Jeśli Maven nie jest preferowanym narzędziem programistycznym, zapoznaj się z naszymi samouczkami dotyczącymi deweloperów języka Java za pomocą [Gradle](./functions-create-first-java-gradle.md), [IntelliJ pomysłu](/azure/developer/java/toolkit-for-intellij/quickstart-functions) i [Visual Studio Code](create-first-function-vs-code-java.md).

## <a name="1-prepare-your-environment"></a>1. Przygotuj środowisko

Przed rozpoczęciem należy wykonać następujące czynności:

+ Konto platformy Azure z aktywnym <abbr title="Podstawowa struktura organizacyjna, w której zarządzane są zasoby na platformie Azure, zwykle skojarzone z osobą lub działem w organizacji.">subskrypcja</abbr>. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) w wersji 3. x.

+ [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2,4 lub nowszej.

+ [Zestaw Java developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), wersja 8 lub 11. `JAVA_HOME`Zmienna środowiskowa musi być ustawiona na lokalizację instalacji poprawnej wersji JDK.

+ [Apache Maven](https://maven.apache.org), wersja 3,0 lub nowsza.

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

+ W terminalu lub oknie poleceń Uruchom polecenie, `func --version` Aby sprawdzić, czy <abbr title="Zestaw narzędzi wiersza polecenia do pracy z Azure Functions na komputerze lokalnym.">Azure Functions Core Tools</abbr> są w wersji 3. x.

+ Uruchom, `az --version` Aby sprawdzić, czy wersja interfejsu wiersza polecenia platformy Azure to 2,4 lub nowszego.

+ Uruchom, `az login` Aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

<br>
<hr/>

## <a name="2-create-a-local-function-project"></a>2. Utwórz projekt funkcji lokalnej

W Azure Functions, projekt funkcji jest kontenerem dla jednej lub kilku poszczególnych funkcji, które reagują na konkretną <abbr title="Typ zdarzenia, które wywołuje kod funkcji, taki jak żądanie HTTP, komunikat kolejki lub określony czas.">uruchamiać</abbr>. Wszystkie funkcje w projekcie mają takie same konfiguracje lokalne i hostingowe. W tej sekcji utworzysz projekt funkcji zawierający pojedynczą funkcję.

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

    <br/>
    <details>
    <summary><strong>Aby uruchamiać funkcje w języku Java 11</strong></summary>

    Użyj polecenia `-DjavaVersion=11`, jeśli chcesz, aby funkcje działały w języku Java 11. Aby dowiedzieć się więcej, zobacz [wersje Java](functions-reference-java.md#java-versions).
    </details>

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

<br/>
<details>
<summary><strong>Co jest tworzone w folderze LocalFunctionProj?</strong></summary>

Ten folder zawiera różne pliki dla projektu, takie jak *Functions. Java*, *FunctionTest. Java* i *pom.xml*. Istnieją także konfiguracje plików o nazwach [local.settings.jswłączone](functions-run-local.md#local-settings-file) i [host.jsna](functions-host-json.md). Ponieważ *local.settings.json* może zawierać wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *. gitignore* .
</details>

<br/>
<details>
<summary><strong>Code for Function. Java</strong></summary>

*Funkcja. Java* zawiera `run` metodę, która odbiera dane żądania w `request` zmiennej to [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) , który jest uzupełniony adnotacją [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) , która definiuje zachowanie wyzwalacza. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Komunikat odpowiedzi jest generowany przez interfejs API [HttpResponseMessage. Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) .

Archetype generuje również test jednostkowy dla funkcji. W przypadku zmiany funkcji w celu dodania powiązań lub dodania nowych funkcji do projektu należy również zmodyfikować testy w pliku *FunctionTest. Java* .
</details>

<br/>
<details>
<summary><strong>Kod dla pom.xml</strong></summary>

Ustawienia zasobów platformy Azure utworzonych na potrzeby hostowania aplikacji są definiowane w elemencie **konfiguracji** wtyczki z identyfikatorem **grupy** `com.microsoft.azure` w wygenerowanym pliku *pom.xml* . Na przykład poniższy element konfiguracji instruuje wdrożenie oparte na Maven, aby utworzyć aplikację funkcji w `java-functions-group` grupie zasobów w `westus` <abbr title="Odniesienia geograficzne do określonego centrum danych platformy Azure, w którym są przydzielono zasoby.">region</abbr>. Sama aplikacja funkcji działa w systemie Windows hostowanym w `java-functions-app-service-plan` planie, który domyślnie jest planem zużycia bezserwerowego.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

Możesz zmienić te ustawienia, aby kontrolować sposób tworzenia zasobów na platformie Azure, na przykład przez zmianę `runtime.os` z `windows` na `linux` przed początkowym wdrożeniem. Aby uzyskać pełną listę ustawień obsługiwanych przez wtyczkę Maven, zobacz [szczegóły konfiguracji](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).
</details>

<br>
<hr/>

## <a name="3-run-the-function-locally"></a>3. Uruchom funkcję lokalnie

1. **Uruchom funkcję** przez uruchomienie lokalnego hosta Azure Functions środowiska uruchomieniowego z folderu *LocalFunctionProj* :

    ```console
    mvn clean package
    mvn azure-functions:run
    ```

    W kierunku końca danych wyjściowych powinny być wyświetlane następujące wiersze:

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    </pre>

    Jeśli HttpExample nie pojawia się jak pokazano powyżej, prawdopodobnie uruchomiono hosta spoza folderu głównego projektu. W takim przypadku użyj <kbd>kombinacji klawiszy CTRL + C</kbd> , aby zatrzymać hosta, przejdź do folderu głównego projektu i ponownie uruchom poprzednie polecenie.

1. **Skopiuj adres URL** `HttpExample` funkcji z tego danych wyjściowych do przeglądarki i dołącz ciąg zapytania `?name=<YOUR_NAME>` , wprowadzając pełny adres URL, taki jak `http://localhost:7071/api/HttpExample?name=Functions` . W przeglądarce powinien zostać wyświetlony następujący komunikat `Hello Functions` :

    ![Wynik funkcji uruchomionej lokalnie w przeglądarce](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)

    Na terminalu, w którym rozpoczął się projekt, są również wyświetlane dane wyjściowe dziennika podczas zgłaszania żądań.

1. Gdy skończysz, użyj <kbd>kombinacji klawiszy CTRL + C</kbd> i wybierz pozycję <kbd>y</kbd> , aby zatrzymać host funkcji.

<br>
<hr/>

## <a name="4-deploy-the-function-project-to-azure"></a>4. Wdróż projekt funkcji na platformie Azure

Aplikacja funkcji i powiązane zasoby są tworzone na platformie Azure podczas pierwszego wdrożenia projektu usługi Functions. Ustawienia zasobów platformy Azure utworzonych na potrzeby hostowania aplikacji są zdefiniowane w pliku *pom.xml* . W tym artykule zaakceptujesz ustawienia domyślne.

<br/>
<details>
<summary><strong>Aby utworzyć aplikację funkcji działającą w systemie Linux</strong></summary>

Aby utworzyć aplikację funkcji działającą w systemie Linux zamiast systemu Windows, Zmień `runtime.os` element w pliku *pom.xml* z `windows` na `linux` . W [tych regionach](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)jest obsługiwany system Linux w planie zużycia. W tej samej grupie zasobów nie można używać aplikacji działających w systemie Linux i aplikacji działających w ramach systemu Windows.
</details>

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

    Wdrożenie pakuje pliki projektu i wdraża je w nowej aplikacji funkcji przy użyciu polecenia [zip Deployment](functions-deployment-technologies.md#zip-deploy). Kod jest uruchamiany z pakietu wdrożeniowego na platformie Azure.

<br/>
<details>
<summary><strong>Co zostało utworzone na platformie Azure?</strong></summary>

+ Grupa zasobów. Nazwana jako _Java-Functions-Group_.
+ Konto magazynu. Wymagane przez funkcje. Nazwa jest generowana losowo na podstawie wymagań dotyczących nazw kont magazynu.
+ Plan hostingu. Hosting bezserwerowy dla aplikacji funkcji w regionie _zachodnim_ . Nazwa to _Java-Functions-App-Service-plan_.
+ Aplikacja funkcji. Aplikacja funkcji jest jednostką wdrażania i wykonywania dla funkcji. Nazwa jest generowana losowo na podstawie _artifactId_, z uwzględnieniem losowo wygenerowanego numeru.
</details>

<br>
<hr/>

## <a name="5-invoke-the-function-on-azure"></a>5. wywołaj funkcję na platformie Azure

Ponieważ funkcja używa wyzwalacza HTTP, **wywołuje ją, wysyłając żądanie HTTP do jego adresu URL** w przeglądarce lub za pomocą narzędzia takiego jak <abbr title="Narzędzie wiersza polecenia do generowania żądań HTTP na adres URL; wyświetlania https://curl.se/">curl</abbr>.

# <a name="browser"></a>[Przeglądarka](#tab/browser)

Skopiuj pełny **adres URL Wywołaj** pokazany w danych wyjściowych `publish` polecenia na pasku adresu przeglądarki, dołączając parametr zapytania `&name=Functions` . Przeglądarka powinna wyświetlać podobne dane wyjściowe, jak w przypadku lokalnego uruchomienia funkcji.

![Dane wyjściowe funkcji uruchamianej na platformie Azure w przeglądarce](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Uruchom [`curl`](https://curl.haxx.se/) polecenie **Invoke URL**, dodając parametr `&name=Functions` . Danymi wyjściowymi polecenia powinien być tekst "Hello Functions".

![Dane wyjściowe funkcji uruchamianej na platformie Azure przy użyciu programu zwinięcie](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

<br>
<hr/>

## <a name="6-clean-up-resources"></a>6. Wyczyść zasoby

Jeśli chcesz przejść do [następnego kroku](#next-steps) i dodać usługę Azure Storage <abbr title="W usłudze Azure Storage istnieje możliwość skojarzenia funkcji z kolejką magazynu, dzięki czemu można tworzyć komunikaty w kolejce.">powiązanie danych wyjściowych w kolejce</abbr>Zachowaj wszystkie Twoje zasoby w miarę kompilowania już wykonanej pracy.

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

<br>
<hr/>

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
