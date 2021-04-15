---
title: Tworzenie funkcji języka Java z wiersza polecenia — Azure Functions
description: Dowiedz się, jak utworzyć funkcję języka Java z wiersza polecenia, a następnie opublikować projekt lokalny w hostingu bez serwera w Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: b5bc453e2e0371ee0412824f01d99863b12d91e2
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375376"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Szybki start: tworzenie funkcji języka Java na platformie Azure z wiersza polecenia

> [!div class="op_single_selector" title1="Wybierz język funkcji: "]
> - [Java](create-first-function-cli-java.md)
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [Program PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Użyj narzędzi wiersza polecenia, aby utworzyć funkcję języka Java, która odpowiada na żądania HTTP. Przetestuj kod lokalnie, a następnie wdaj go w środowisku bez serwera Azure Functions.

Wykonanie kroków tego przewodnika Szybki start wiąże się z niewielkim kosztem w wysokości kilku centów USD lub mniej <abbr title="Profil, który przechowuje informacje rozliczeniowe dotyczące użycia platformy Azure.">Konto platformy Azure</abbr>.

Jeśli narzędzie Maven nie jest preferowanym narzędziem dewelopera, zapoznaj się z naszymi podobnymi samouczkami dla deweloperów języka Java korzystającymi z narzędzi [Gradle,](./functions-create-first-java-gradle.md) [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) [i Visual Studio Code](create-first-function-vs-code-java.md).

## <a name="1-prepare-your-environment"></a>1. Przygotowywanie środowiska

Przed rozpoczęciem musisz mieć następujące elementy:

+ Konto platformy Azure z aktywnym <abbr title="Podstawowa struktura organizacyjna, w której zarządzasz zasobami na platformie Azure, zwykle skojarzona z jednostką lub działem w organizacji.">subskrypcja</abbr>. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Wersja [Azure Functions Core Tools](functions-run-local.md#v2) 3.x.

+ Interfejs [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.4 lub nowszej.

+ Zestaw [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support)w wersji 8 lub 11. Zmienna `JAVA_HOME` środowiskowa musi być ustawiona na lokalizację instalacji poprawnej wersji zestawu JDK.

+ [Apache Maven,](https://maven.apache.org)wersja 3.0 lub nowsza.

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

+ W terminalu lub oknie polecenia uruchom polecenie , `func --version` aby sprawdzić, czy <abbr title="Zestaw narzędzi wiersza polecenia do pracy z Azure Functions na komputerze lokalnym.">Azure Functions Core Tools</abbr> to wersja 3.x.

+ Uruchom `az --version` , aby sprawdzić, czy interfejs wiersza polecenia platformy Azure jest w wersji 2.4 lub nowszej.

+ Uruchom `az login` , aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.

<br>
<hr/>

## <a name="2-create-a-local-function-project"></a>2. Tworzenie lokalnego projektu funkcji

W Azure Functions projekt funkcji jest kontenerem dla co najmniej jednej funkcji, która odpowiada na określoną <abbr title="Typ zdarzenia, które wywołuje kod funkcji, taki jak żądanie HTTP, komunikat w kolejce lub określony czas.">Wyzwalacz</abbr>. Wszystkie funkcje w projekcie mają te same konfiguracje lokalne i konfiguracje hostingu. W tej sekcji utworzysz projekt funkcji, który zawiera jedną funkcję.

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
    <summary><strong>Aby uruchomić funkcje w języku Java 11</strong></summary>

    Użyj polecenia `-DjavaVersion=11`, jeśli chcesz, aby funkcje działały w języku Java 11. Aby dowiedzieć się więcej, zobacz [Wersje języka Java](functions-reference-java.md#java-versions).
    </details>

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

<br/>
<details>
<summary><strong>Co utworzono w folderze LocalFunctionProj?</strong></summary>

Ten folder zawiera różne pliki projektu, takie jak *Function.java,* *FunctionTest.java* i *pom.xml*. Istnieją również pliki konfiguracji o nazwach [local.settings.jsna](functions-run-local.md#local-settings-file) i [host.jsna](functions-host-json.md). Ponieważ *local.settings.jsmoże* zawierać wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *gitignore.*
</details>

<br/>
<details>
<summary><strong>Kod dla function.java</strong></summary>

*Plik Function.java* zawiera metodę, która odbiera dane żądania w zmiennej, to `run` `request` element [HttpRequestMessage,](/java/api/com.microsoft.azure.functions.httprequestmessage) który jest dekorowany adnotacją [HttpTrigger,](/java/api/com.microsoft.azure.functions.annotation.httptrigger) która definiuje zachowanie wyzwalacza. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Komunikat odpowiedzi jest generowany przez interfejs API [HttpResponseMessage.Builder.](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder)

Archetyp generuje również test jednostkowy dla funkcji. W przypadku zmiany funkcji w celu dodania powiązań lub dodania nowych funkcji do projektu należy również zmodyfikować testy w pliku *FunctionTest.java.*
</details>

<br/>
<details>
<summary><strong>Kod pom.xml</strong></summary>

Ustawienia zasobów platformy Azure utworzonych w celu  hostowania aplikacji są definiowane w elemencie konfiguracji wtyczki z elementem **groupId** w wygenerowanym `com.microsoft.azure` *pom.xml* pliku. Na przykład poniższy element konfiguracji instruuje wdrożenie oparte na programie Maven, aby utworzyć aplikację funkcji w `java-functions-group` grupie zasobów w `westus` <abbr title="Geograficzne odwołanie do określonego centrum danych platformy Azure, w którym są przydzielane zasoby.">region</abbr>. Sama aplikacja funkcji działa w systemie Windows hostowany w planie, który domyślnie jest planem użycia `java-functions-app-service-plan` bez serwera.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

Możesz zmienić te ustawienia, aby kontrolować sposób tworzenia zasobów na platformie Azure, na przykład zmieniając z `runtime.os` `windows` na przed `linux` początkowym wdrożeniem. Aby uzyskać pełną listę ustawień obsługiwanych przez wtyczkę Maven, zobacz [szczegóły konfiguracji](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).
</details>

<br>
<hr/>

## <a name="3-run-the-function-locally"></a>3. Uruchom funkcję lokalnie

1. **Uruchom funkcję,** uruchamiać lokalnego hosta Azure Functions uruchomieniowego z folderu *LocalFunctionProj:*

    ```console
    mvn clean package
    mvn azure-functions:run
    ```

    Pod koniec danych wyjściowych powinny pojawić się następujące wiersze:

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    </pre>

    Jeśli przykład HttpExample nie wygląda jak pokazano powyżej, host prawdopodobnie został uruchomiony spoza folderu głównego projektu. W takim przypadku naciśnij klawisze <kbd>Ctrl+C,</kbd> aby zatrzymać hosta, przejść do folderu głównego projektu i ponownie uruchomić poprzednie polecenie.

1. **Skopiuj adres URL** funkcji z tych danych wyjściowych do przeglądarki i dołącz ciąg zapytania , dzięki czemu pełny `HttpExample` adres `?name=<YOUR_NAME>` URL, taki jak `http://localhost:7071/api/HttpExample?name=Functions` . W przeglądarce powinien zostać wyświetlony komunikat, taki jak `Hello Functions` :

    ![Wynik działania funkcji lokalnie w przeglądarce](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)

    Terminal, w którym uruchomiliśmy projekt, również wyświetla dane wyjściowe dziennika podczas żądań.

1. Gdy wszystko będzie gotowe, naciśnij klawisze <kbd>Ctrl+C</kbd> i wybierz <kbd>pozycję y,</kbd> aby zatrzymać hosta funkcji.

<br>
<hr/>

## <a name="4-deploy-the-function-project-to-azure"></a>4. Wdrażanie projektu funkcji na platformie Azure

Aplikacja funkcji i powiązane zasoby są tworzone na platformie Azure podczas pierwszego wdrażania projektu funkcji. Ustawienia dla zasobów platformy Azure utworzonych w celu hostować aplikację są zdefiniowane w *pom.xml* pliku. W tym artykule zaakceptujesz wartości domyślne.

<br/>
<details>
<summary><strong>Aby utworzyć aplikację funkcji w systemie Linux</strong></summary>

Aby utworzyć aplikację funkcji uruchamianą w systemie Linux zamiast w systemie Windows, zmień `runtime.os` element w pliku *pom.xml* z `windows` na `linux` . Uruchamianie systemu Linux w planie zużycia jest obsługiwane w [tych regionach.](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions) W tej samej grupie zasobów nie można mieć aplikacji uruchamianych w systemie Linux i aplikacji uruchamianych w systemie Windows.
</details>

1. Przed wdrożeniem zaloguj się do subskrypcji platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. 

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Polecenie [az login](/cli/azure/reference-index#az-login) loguje Cię do konta platformy Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Polecenie cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) umożliwia twoje konto platformy Azure.

    ---

1. Użyj następującego polecenia, aby wdrożyć projekt w nowej aplikacji funkcji.

    ```console
    mvn azure-functions:deploy
    ```

    Wdrożenie pakuje pliki projektu i wdraża je w nowej aplikacji funkcji przy użyciu [wdrożenia zip.](functions-deployment-technologies.md#zip-deploy) Kod jest uruchamiany z pakietu wdrożeniowego na platformie Azure.

<br/>
<details>
<summary><strong>Co jest tworzone na platformie Azure?</strong></summary>

+ Grupa zasobów. Nazwana jako _java-functions-group_.
+ Konto magazynu. Wymagane przez funkcje. Nazwa jest generowana losowo na podstawie wymagań dotyczących nazwy konta magazynu.
+ Plan hostingu. Hosting bez serwera dla aplikacji funkcji w regionie _westus._ Nazwa to _java-functions-app-service-plan_.
+ Aplikacja funkcji. Aplikacja funkcji to jednostka wdrażania i wykonywania funkcji. Nazwa jest generowana losowo na podstawie twojego _artifactId_ i jest dołączana z losowo wygenerowaną liczbą.
</details>

<br>
<hr/>

## <a name="5-invoke-the-function-on-azure"></a>5. Wywoływanie funkcji na platformie Azure

Ponieważ funkcja używa wyzwalacza HTTP, wywołujesz go, wysyłasz żądanie HTTP do jej adresu **URL** w przeglądarce lub za pomocą narzędzia, takiego jak <abbr title="Narzędzie wiersza polecenia do generowania żądań HTTP do adresu URL; Zobacz https://curl.se/">curl</abbr>.

# <a name="browser"></a>[Przeglądarka](#tab/browser)

Skopiuj pełny adres **URL wywołania** widoczny w danych wyjściowych polecenia na pasku adresu przeglądarki, `publish` dołączając parametr zapytania `&name=Functions` . W przeglądarce powinny być wyświetlane podobne dane wyjściowe, jak w przypadku lokalnego działania funkcji.

![Dane wyjściowe funkcji uruchamianej na platformie Azure w przeglądarce](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Uruchom [`curl`](https://curl.haxx.se/) z adresem URL **wywołania**, dołączając parametr `&name=Functions` . Dane wyjściowe polecenia powinny być tekstem "Hello Functions".

![Dane wyjściowe funkcji uruchamianej na platformie Azure przy użyciu narzędzia curl](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

<br>
<hr/>

## <a name="6-clean-up-resources"></a>6. Czyszczenie zasobów

Jeśli dodasz usługę Azure Storage [do](#next-steps) następnego kroku <abbr title="W usłudze Azure Storage oznacza skojarzenie funkcji z kolejką magazynu, aby można było tworzyć komunikaty w kolejce.">powiązanie wyjściowe kolejki</abbr>, zachowaj wszystkie zasoby, ponieważ będziesz tworzyć na podstawie już wykonanej pracy.

W przeciwnym razie użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby, aby uniknąć ponoszenia dalszych kosztów.

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
> [Łączenie z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
