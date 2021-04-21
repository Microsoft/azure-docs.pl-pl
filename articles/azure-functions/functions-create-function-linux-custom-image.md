---
title: Tworzenie Azure Functions w systemie Linux przy użyciu obrazu niestandardowego
description: Informacje o sposobie tworzenia usługi Azure Functions działającej w niestandardowym obrazie systemu Linux.
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: 7950bfb4a57db812da87f4e5f76f3075d50a8293
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782281"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Tworzenie funkcji w systemie Linux przy użyciu kontenera niestandardowego

W tym samouczku utworzysz i wdrożysz kod w kontenerze Azure Functions kontenerze platformy Docker przy użyciu obrazu podstawowego systemu Linux. Obraz niestandardowy jest zwykle używany, gdy funkcje wymagają określonej wersji języka lub mają określoną zależność lub konfigurację, która nie jest dostarczana przez wbudowany obraz.

::: zone pivot="programming-language-other"
Azure Functions obsługuje dowolny język lub środowisko uruchomieniowe przy użyciu [niestandardowych programów obsługi](functions-custom-handlers.md). W przypadku niektórych języków, takich jak język programowania R używany w tym samouczku, należy zainstalować środowisko uruchomieniowe lub dodatkowe biblioteki jako zależności, które wymagają użycia kontenera niestandardowego.
::: zone-end

Wdrażanie kodu funkcji w niestandardowym kontenerze systemu Linux wymaga planu [Premium](functions-premium-plan.md) lub hostingu App Service [(dedykowanego).](dedicated-plan.md) Wykonanie kroków tego samouczka wiąże się z naliczeniem na [](#clean-up-resources) koncie platformy Azure kosztów w 2000 USD, które można zminimalizować, czyszcząc zasoby po zakończeniu.

Możesz również użyć domyślnego kontenera Azure App Service zgodnie z opisem w te tematu Create your first function hosted on Linux (Tworzenie [pierwszej funkcji hostowanej w systemie Linux).](./create-first-function-cli-csharp.md?pivots=programming-language-python) Obsługiwane obrazy podstawowe dla Azure Functions znajdują się w Azure Functions [obrazów podstawowych.](https://hub.docker.com/_/microsoft-azure-functions-base)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
> [!div class="checklist"]
> * Utwórz aplikację funkcji i plik Dockerfile przy użyciu Azure Functions Core Tools.
> * Kompilowanie obrazu niestandardowego na platformie Docker.
> * Publikowanie obrazu niestandardowego w rejestrze kontenerów.
> * Tworzenie zasobów obsługi aplikacji funkcji na platformie Azure
> * Wdrażanie aplikacji funkcji z usługi Docker Hub.
> * Dodawanie ustawień aplikacji do aplikacji funkcji.
> * Włącz ciągłe wdrażanie.
> * Włącz połączenia SSH z kontenerem.
> * Dodaj powiązanie wyjściowe usługi Queue Storage. 
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * Utwórz aplikację funkcji i plik Dockerfile przy użyciu Azure Functions Core Tools.
> * Kompilowanie obrazu niestandardowego na platformie Docker.
> * Publikowanie obrazu niestandardowego w rejestrze kontenerów.
> * Tworzenie zasobów obsługi aplikacji funkcji na platformie Azure
> * Wdrażanie aplikacji funkcji z usługi Docker Hub.
> * Dodawanie ustawień aplikacji do aplikacji funkcji.
> * Włącz ciągłe wdrażanie.
> * Włącz połączenia SSH z kontenerem.
::: zone-end

Ten samouczek można wykonać na dowolnym komputerze z systemem Windows, macOS lub Linux. 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ Identyfikator [platformy Docker](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Uruchom `docker login` , aby zalogować się do platformy Docker. To polecenie kończy się niepowodzeniem, jeśli docker nie jest uruchomiona. W takim przypadku uruchom polecenie Docker i ponów próbę wykonania polecenia.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Tworzenie i testowanie projektu funkcji lokalnych

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
W terminalu lub wierszu polecenia uruchom następujące polecenie dla wybranego języka, aby utworzyć projekt aplikacji funkcji w folderze o nazwie `LocalFunctionsProject` .  
::: zone-end  
::: zone pivot="programming-language-csharp"  
```console
func init LocalFunctionsProject --worker-runtime dotnet --docker
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```console
func init LocalFunctionsProject --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```console
func init LocalFunctionsProject --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```console
func init LocalFunctionsProject --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
func init LocalFunctionsProject --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
W pustym folderze uruchom następujące polecenie, aby wygenerować projekt usługi Functions z [archetypu narzędzia Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[Program PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

Parametr `-DjavaVersion` informuje środowisko uruchomieniowe usługi Functions, której wersji języka Java użyć. Użyj polecenia `-DjavaVersion=11`, jeśli chcesz, aby funkcje działały w języku Java 11. Jeśli nie określisz wartości `-DjavaVersion` , program Maven domyślnie wybierze środowisko Java 8. Aby uzyskać więcej informacji, zobacz [Wersje języka Java](functions-reference-java.md#java-versions).

> [!IMPORTANT]
> Aby ukończyć ten artykuł, zmienna środowiskowa musi być ustawiona na lokalizację instalacji odpowiedniej wersji zestawu `JAVA_HOME` JDK.

Program Maven prosi o wartości potrzebne do ukończenia generowania projektu podczas wdrażania.   
Po wyświetleniu monitu podaj następujące wartości:

| Monit | Wartość | Opis |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Wartość, która jednoznacznie identyfikuje projekt we wszystkich projektach, zgodnie z regułami [nazewnictwa pakietów](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) dla języka Java. |
| **artifactId** | `fabrikam-functions` | Wartość, która jest nazwą pliku jar, bez numeru wersji. |
| **Wersja** | `1.0-SNAPSHOT` | Wybierz wartość domyślną. |
| **Pakiet** | `com.fabrikam.functions` | Wartość, która jest pakietem Java dla wygenerowanego kodu funkcji. Użyj wartości domyślnej. |

Wpisz `Y` lub naciśnij klawisz Enter, aby potwierdzić.

Maven tworzy pliki projektu w nowym folderze o nazwie _artifactId_, która w tym przykładzie to `fabrikam-functions` . 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init LocalFunctionsProject --worker-runtime custom --docker
```
::: zone-end

Opcja generuje dla projektu , który definiuje odpowiedni kontener niestandardowy do użycia z Azure Functions `--docker` `Dockerfile` i wybranym środowiskiem uruchomieniowym.

Przejdź do folderu projektu:
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
```console
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Dodaj funkcję do projektu przy użyciu następującego polecenia, gdzie argument jest unikatową nazwą funkcji, a argument określa `--name` `--template` wyzwalacz funkcji. `func new` Tworzy podfolder pasujący do nazwy funkcji, który zawiera plik kodu odpowiedni dla wybranego języka projektu i plik konfiguracji o *nazwiefunction.jspliku*.

```console
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end

::: zone pivot="programming-language-other" 
Dodaj funkcję do projektu przy użyciu następującego polecenia, gdzie argument jest unikatową nazwą funkcji, a argument określa `--name` `--template` wyzwalacz funkcji. `func new` Tworzy podfolder pasujący do nazwy funkcji zawierającej plik konfiguracji o *nazwiefunction.jssystemie*.

```console
func new --name HttpExample --template "HTTP trigger"
```

W edytorze tekstów utwórz plik w folderze projektu o nazwie *handler. R*. Dodaj następujący kod jako zawartość.

```r
library(httpuv)

PORTEnv <- Sys.getenv("FUNCTIONS_CUSTOMHANDLER_PORT")
PORT <- strtoi(PORTEnv , base = 0L)

http_not_found <- list(
  status=404,
  body='404 Not Found'
)

http_method_not_allowed <- list(
  status=405,
  body='405 Method Not Allowed'
)

hello_handler <- list(
  GET = function (request) {
    list(body=paste(
      "Hello,",
      if(substr(request$QUERY_STRING,1,6)=="?name=") 
        substr(request$QUERY_STRING,7,40) else "World",
      sep=" "))
  }
)

routes <- list(
  '/api/HttpExample' = hello_handler
)

router <- function (routes, request) {
  if (!request$PATH_INFO %in% names(routes)) {
    return(http_not_found)
  }
  path_handler <- routes[[request$PATH_INFO]]

  if (!request$REQUEST_METHOD %in% names(path_handler)) {
    return(http_method_not_allowed)
  }
  method_handler <- path_handler[[request$REQUEST_METHOD]]

  return(method_handler(request))
}

app <- list(
  call = function (request) {
    response <- router(routes, request)
    if (!'status' %in% names(response)) {
      response$status <- 200
    }
    if (!'headers' %in% names(response)) {
      response$headers <- list()
    }
    if (!'Content-Type' %in% names(response$headers)) {
      response$headers[['Content-Type']] <- 'text/plain'
    }

    return(response)
  }
)

cat(paste0("Server listening on :", PORT, "...\n"))
runServer("0.0.0.0", PORT, app)
```

W *host.jsw* sekcji `customHandler` zmodyfikuj sekcję , aby skonfigurować polecenie uruchamiania niestandardowego programu obsługi.

```json
"customHandler": {
  "description": {
      "defaultExecutablePath": "Rscript",
      "arguments": [
      "handler.R"
    ]
  },
  "enableForwardingHttpRequest": true
}
```
::: zone-end

Aby przetestować funkcję lokalnie, uruchom lokalnego Azure Functions uruchomieniowego w folderze głównym folderu projektu: 
::: zone pivot="programming-language-csharp"  
```console
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
mvn clean package  
mvn azure-functions:run
```
::: zone-end
::: zone pivot="programming-language-other"
```console
R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"
func start
```
::: zone-end 

Gdy punkt końcowy pojawi `HttpExample` się w danych wyjściowych, przejdź do . `http://localhost:7071/api/HttpExample?name=Functions` W przeglądarce powinien zostać wyświetlony komunikat "hello" z powrotem `Functions` , wartość dostarczona do `name` parametru zapytania.

Użyj **klawisza Ctrl** - **C,** aby zatrzymać hosta.

## <a name="build-the-container-image-and-test-locally"></a>Kompilowanie obrazu kontenera i lokalne testowanie

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
(Opcjonalnie) Sprawdź plik *Dockerfile* w katalogu głównym folderu projektu. Plik Dockerfile opisuje środowisko wymagane do uruchomienia aplikacji funkcji w systemie Linux.  Pełną listę obsługiwanych obrazów podstawowych dla Azure Functions można znaleźć na stronie Azure Functions [obrazów podstawowych.](https://hub.docker.com/_/microsoft-azure-functions-base)
::: zone-end

::: zone pivot="programming-language-other"
Sprawdź plik *Dockerfile* w katalogu głównym folderu projektu. Plik Dockerfile opisuje środowisko wymagane do uruchomienia aplikacji funkcji w systemie Linux. Niestandardowe aplikacje obsługi używają `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice` obrazu jako podstawy.

*Zmodyfikuj plik Dockerfile,* aby zainstalować plik R. Zastąp zawartość pliku *Dockerfile* poniższym kodem.

```dockerfile
FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice 
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

RUN apt update && \
    apt install -y r-base && \
    R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"

COPY . /home/site/wwwroot
```
::: zone-end

W folderze projektu głównego uruchom polecenie [docker build](https://docs.docker.com/engine/reference/commandline/build/) i podaj nazwę oraz `azurefunctionsimage` tag `v1.0.0` . Zastąp ciąg `<DOCKER_ID>` identyfikatorem konta usługi Docker Hub. To polecenie powoduje skompilowanie obrazu platformy Docker dla kontenera.

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Po zakończeniu polecenia możesz uruchomić nowy kontener lokalnie.
    
Aby przetestować kompilację, uruchom obraz w kontenerze lokalnym przy użyciu polecenia [docker run,](https://docs.docker.com/engine/reference/commandline/run/) zastępując ponownie identyfikatorem platformy Docker i dodając `<DOCKER_ID` argument ports: `-p 8080:80`

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
Po uruchomieniu obrazu w kontenerze lokalnym otwórz w przeglądarce plik , który powinien wyświetlić `http://localhost:8080` obraz zastępczy pokazany poniżej. Obraz zostanie wyświetlony w tym momencie, ponieważ funkcja jest uruchomiona w kontenerze lokalnym, tak jak na platformie Azure, co oznacza, że jest ona chroniona za pomocą klucza dostępu zgodnie z definicją wfunction.js *przy* użyciu `"authLevel": "function"` właściwości . Kontener nie został jeszcze opublikowany w aplikacji funkcji na platformie Azure, więc klucz nie jest jeszcze dostępny. Jeśli chcesz przetestować kontener lokalny, zatrzymaj program Docker, zmień właściwość autoryzacji na , ponownie sbuduj obraz `"authLevel": "anonymous"` i uruchom ponownie usługę Docker. Następnie `"authLevel": "function"` zresetuj *function.jsna stronie*. Aby uzyskać więcej informacji, zobacz [klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys).

![Obraz zastępczy wskazujący, że kontener jest uruchomiony lokalnie](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
Po uruchomieniu obrazu w kontenerze lokalnym przejdź do lokalizacji , w której powinien zostać wyświetlony ten `http://localhost:8080/api/HttpExample?name=Functions` sam komunikat "hello", co wcześniej. Ponieważ archetyp maven generuje funkcję wyzwalaną przez protokół HTTP, która korzysta z autoryzacji anonimowej, nadal można wywołać funkcję, nawet jeśli jest uruchomiona w kontenerze. 
::: zone-end  

Po zweryfikowaniu aplikacji funkcji w kontenerze zatrzymaj aplikację docker za pomocą **klawisza Ctrl** + **C.**

## <a name="push-the-image-to-docker-hub"></a>Wypychanie obrazu do Docker Hub

Docker Hub to rejestr kontenerów, który hostuje obrazy i udostępnia usługi obrazów i kontenerów. Aby udostępnić obraz, co obejmuje wdrażanie na platformie Azure, należy wypchnąć go do rejestru.

1. Jeśli jeszcze nie zalogowano się do platformy Docker, zrób to za pomocą polecenia [docker login,](https://docs.docker.com/engine/reference/commandline/login/) zastępując go `<docker_id>` identyfikatorem platformy Docker. To polecenie wyświetla monit o nazwę użytkownika i hasło. Komunikat "Logowanie zakończyło się pomyślnie" potwierdza zalogowanie.

    ```console
    docker login
    ```
    
1. Po zalogowaniu wypchń obraz do usługi Docker Hub przy użyciu polecenia [docker push,](https://docs.docker.com/engine/reference/commandline/push/) ponownie zastępując identyfikator `<docker_id>` identyfikatorem platformy Docker.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. W zależności od szybkości sieci wypychanie obrazu po raz pierwszy może potrwać kilka minut (wypychanie kolejnych zmian jest znacznie szybsze). Podczas oczekiwania możesz przejść do następnej sekcji i utworzyć zasoby platformy Azure w innym terminalu.

## <a name="create-supporting-azure-resources-for-your-function"></a>Tworzenie zasobów obsługi platformy Azure dla funkcji

Aby wdrożyć kod funkcji na platformie Azure, musisz utworzyć trzy zasoby:

- Grupa zasobów, która jest kontenerem logicznym dla powiązanych zasobów.
- Konto usługi Azure Storage, które przechowuje informacje o stanie i inne informacje o projektach.
- Aplikacja funkcji, która zapewnia środowisko do wykonywania kodu funkcji. Aplikacja funkcji mapuje do lokalnego projektu funkcji i umożliwia grupowanie funkcji jako jednostki logicznej w celu łatwiejszego zarządzania, wdrażania i udostępniania zasobów.

Aby utworzyć te elementy, użyj poleceń interfejsu wiersza polecenia platformy Azure. Każde polecenie dostarcza dane wyjściowe JSON po zakończeniu.

1. Zaloguj się do platformy Azure za pomocą [polecenia az login:](/cli/azure/reference-index#az_login)

    ```azurecli
    az login
    ```
    
1. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Poniższy przykład tworzy grupę zasobów o `AzureFunctionsContainers-rg` nazwie w `westeurope` regionie . (Zazwyczaj grupę zasobów i zasoby tworzy się w regionie w pobliżu przy użyciu dostępnego regionu z `az account list-locations` polecenia ).

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Nie można hostowania aplikacji systemów Linux i Windows w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie z aplikacją funkcji lub aplikacją internetową systemu `AzureFunctionsContainers-rg` Windows, musisz użyć innej grupy zasobów.
    
1. Utwórz konto magazynu ogólnego przeznaczenia w grupie zasobów i regionie za pomocą polecenia [az storage account create.](/cli/azure/storage/account#az_storage_account_create) W poniższym przykładzie zastąp `<storage_name>` nazwą globalnie unikatową. Nazwy muszą zawierać od trzech do 24 znaków tylko cyfry i małe litery. `Standard_LRS` Określa typowe konto ogólnego przeznaczenia.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    W przypadku tego samouczka na koncie magazynu naliczane jest tylko kilka centów w USD.
    
1. Użyj polecenia , aby utworzyć plan Premium dla usługi Azure Functions o nazwie w warstwie cenowej `myPremiumPlan` **Elastic Premium 1** ( ), w regionie Europa Zachodnia ( lub użyj odpowiedniego regionu w pobliżu) i w kontenerze systemu `--sku EP1` `-location westeurope` Linux ( `--is-linux` ).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    W tym miejscu używamy planu Premium, który można skalować zgodnie z potrzebami. Aby dowiedzieć się więcej o hostingu, zobacz [Porównanie planów hostingu usługi Azure Functions](functions-scale.md). Aby obliczyć koszty, zobacz stronę [cennika usługi Functions](https://azure.microsoft.com/pricing/details/functions/).

    Polecenie aplikuje również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą której można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitorowanie Azure Functions](functions-monitoring.md). Wystąpienie nie ponosi żadnych kosztów, dopóki nie zostanie aktywowane.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Tworzenie i konfigurowanie aplikacji funkcji na platformie Azure przy użyciu obrazu

Aplikacja funkcji na platformie Azure zarządza wykonywaniem funkcji w planie hostingu. W tej sekcji użyjesz zasobów platformy Azure z poprzedniej sekcji, aby utworzyć aplikację funkcji na podstawie obrazu w usłudze Docker Hub i skonfigurować ją przy użyciu parametrów połączenia z usługą Azure Storage.

1. Utwórz aplikację usługi Functions za pomocą [polecenia az functionapp create.](/cli/azure/functionapp#az_functionapp_create) W poniższym przykładzie zastąp nazwą używaną w `<storage_name>` poprzedniej sekcji dla konta magazynu. Zastąp również nazwą globalnie unikatową, odpowiednią dla Ciebie, oraz `<app_name>` `<docker_id>` identyfikatorem platformy Docker.

    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime <functions runtime stack> --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    ::: zone pivot="programming-language-other"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime custom --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    
    Parametr *deployment-container-image-name* określa obraz do użycia dla aplikacji funkcji. Możesz użyć polecenia [az functionapp config container show,](/cli/azure/functionapp/config/container#az_functionapp_config_container_show) aby wyświetlić informacje o obrazie używanym do wdrożenia. Możesz również użyć polecenia [az functionapp config container set,](/cli/azure/functionapp/config/container#az_functionapp_config_container_set) aby wdrożyć z innego obrazu.

1. Wyświetl ciąg połączenia dla utworzonego konta magazynu za pomocą polecenia [az storage account show-connection-string.](/cli/azure/storage/account) Zastąp nazwą utworzonego powyżej konta `<storage-name>` magazynu:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Dodaj to ustawienie do aplikacji funkcji za pomocą [polecenia az functionapp config appsettings set.](/cli/azure/functionapp/config/appsettings#az_functionapp_config_ppsettings_set) W poniższym poleceniu zastąp ciąg nazwą swojej aplikacji funkcji, a ciąg ciągiem połączenia z poprzedniego kroku (długim zakodowanym ciągiem, który zaczyna się od `<app_name>` `<connection_string>` "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

    > [!TIP]
    > W powłoce Bash można użyć zmiennej powłoki do przechwycenia parametrów połączenia zamiast korzystania ze schowka. Najpierw użyj następującego polecenia, aby utworzyć zmienną z parametrów połączenia:
    > 
    > ```bash
    > storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
    > ```
    > 
    > Następnie odwołaj się do zmiennej w drugim poleceniu:
    > 
    > ```azurecli
    > az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
    > ```

1. Funkcja może teraz używać tych parametrów połączenia do uzyskiwania dostępu do konta magazynu.

> [!NOTE]    
> Jeśli opublikujesz obraz niestandardowy na prywatnym koncie kontenera, zamiast tego użyj zmiennych środowiskowych w pliku Dockerfile jako parametrów połączenia. Aby uzyskać więcej informacji, zobacz instrukcję [ENV](https://docs.docker.com/engine/reference/builder/#env). Należy również ustawić zmienne i `DOCKER_REGISTRY_SERVER_USERNAME` `DOCKER_REGISTRY_SERVER_PASSWORD` . Aby użyć tych wartości, należy ponownie skompilować obraz, wypchnąć obraz do rejestru, a następnie ponownie uruchomić aplikację funkcji na platformie Azure.

## <a name="verify-your-functions-on-azure"></a>Weryfikowanie funkcji na platformie Azure

Obraz wdrożony w aplikacji funkcji na platformie Azure umożliwia teraz wywoływanie funkcji za pośrednictwem żądań HTTP. Ponieważ *function.js* definicji zawiera właściwość , należy najpierw uzyskać klucz dostępu (nazywany również "kluczem funkcji") i dołączyć go jako parametr adresu URL we wszystkich żądaniach do punktu `"authLevel": "function"` końcowego.

1. Pobierz adres URL funkcji z kluczem dostępu (funkcja) przy użyciu Azure Portal lub za pomocą interfejsu wiersza polecenia platformy Azure za pomocą `az rest` polecenia .

    # <a name="portal"></a>[Portal](#tab/portal)

    1. Zaloguj się do aplikacji Azure Portal, a następnie wyszukaj i wybierz **pozycję Aplikacja funkcji.**

    1. Wybierz funkcję, którą chcesz zweryfikować.

    1. W panelu nawigacyjnym po lewej stronie wybierz **pozycję Funkcje,** a następnie wybierz funkcję, którą chcesz zweryfikować.

        ![Wybierz funkcję w Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-select-function.png)   

    
    1. Wybierz **pozycję Pobierz adres URL funkcji.**

        ![Pobierz adres URL funkcji z Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-function-url.png)   

    
    1. W oknie podręcznym wybierz pozycję **domyślne (klucz funkcji),** a następnie skopiuj adres URL do schowka. Klucz jest ciągiem znaków następującym po `?code=` .

        ![Wybierz domyślny klucz dostępu funkcji](./media/functions-create-function-linux-custom-image/functions-portal-copy-url.png)   


    > [!NOTE]  
    > Ponieważ aplikacja funkcji jest wdrażana jako kontener, nie można wprowadzać zmian w kodzie funkcji w portalu. Zamiast tego należy zaktualizować projekt na obrazie lokalnym, ponownie wypchnąć obraz do rejestru, a następnie ponownie go wdepnąć na platformie Azure. Ciągłe wdrażanie można skonfigurować w kolejnej sekcji.
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

    1. Skonstruuj ciąg adresu URL w następującym formacie, zastępując elementy , i odpowiednio identyfikatorem subskrypcji platformy Azure, grupą zasobów aplikacji funkcji i nazwą `<subscription_id>` `<resource_group>` aplikacji `<app_name>` funkcji:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Na przykład adres URL może wyglądać następująco:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Dla wygody możesz zamiast tego przypisać adres URL do zmiennej środowiskowej i użyć go w `az rest` poleceniu .
    
    1. Uruchom następujące polecenie (dostępne w interfejsie wiersza polecenia platformy Azure w wersji 2.0.77 lub nowszej), zastępując ciąg ciągiem URI z ostatniego kroku, w tym `az rest` `<uri>` cudzysłowami:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. Dane wyjściowe polecenia to klucz funkcji. Pełny adres URL funkcji to `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>` , zastępując wartości , i `<app_name>` `<function_name>` `<key>` konkretnymi wartościami.
    
        > [!NOTE]
        > Klucz pobrany w tym miejscu to *klucz hosta,* który działa dla wszystkich funkcji w aplikacji funkcji. Metoda pokazana dla portalu pobiera klucz tylko dla jednej funkcji.

    ---

1. Wklej adres URL funkcji na pasku adresu przeglądarki, dodając parametr `&name=Azure` na końcu tego adresu URL. W przeglądarce powinien zostać wyświetlony tekst podobny do "Hello, Azure".

    ![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Aby przetestować autoryzację, usuń parametr z adresu URL i sprawdź, czy nie `code=` otrzymasz odpowiedzi z funkcji.


## <a name="enable-continuous-deployment-to-azure"></a>Włączanie ciągłego wdrażania na platformie Azure

Możesz włączyć Azure Functions automatycznego aktualizowania wdrożenia obrazu przy każdej aktualizacji obrazu w rejestrze.

1. Włącz ciągłe wdrażanie za pomocą [polecenia az functionapp deployment container config,](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_config) zastępując zastąp nazwą `<app_name>` swojej aplikacji funkcji:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    To polecenie umożliwia ciągłe wdrażanie i zwraca adres URL deployment webhook. (Ten adres URL można pobrać w dowolnym późniejszym czasie za pomocą polecenia [az functionapp deployment container show-cd-url).](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_show_cd_url)

1. Skopiuj adres URL obiektu webhook wdrożenia do schowka.

1. Otwórz [Docker Hub](https://hub.docker.com/), zaloguj się, a następnie wybierz **pozycję Repozytoria** na pasku nav. Znajdź i wybierz obraz, wybierz kartę **Webhook,** określ nazwę urządzenia **webhook,** wklej adres URL w polu **Adres URL** tego urządzenia, a następnie wybierz **pozycję Utwórz:**

    ![Dodawanie urządzenia webhook w swoim repocie usługi DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Po skonfigurowaniu tego zestawu Azure Functions obraz jest ponownie wdowy po każdej aktualizacji obrazu w Docker Hub.

## <a name="enable-ssh-connections"></a>Włączanie połączeń SSH

Protokół SSH umożliwia bezpieczną komunikację między kontenerem i klientem. Po włączeniu połączenia SSH możesz nawiązać połączenie z kontenerem przy użyciu App Service Advanced Tools (Kudu). Aby ułatwić nawiązywanie połączenia z kontenerem przy użyciu połączenia SSH, Azure Functions udostępnia obraz podstawowy, który ma już włączoną obsługę SSH. Wystarczy tylko edytować plik Dockerfile, a następnie ponownie skompilować i ponownie wdązywać obraz. Następnie możesz nawiązać połączenie z kontenerem za pomocą narzędzi zaawansowanych (Kudu)

1. W pliku Dockerfile dołącz ciąg do `-appservice` obrazu podstawowego w `FROM` instrukcji:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end
    
1. Ponownie skompilować obraz `docker build` przy użyciu polecenia , zastępując go `<docker_id>` identyfikatorem platformy Docker:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Wypchń zaktualizowany obraz do Docker Hub, co powinno zająć znacznie mniej czasu niż pierwsze wypchanie tylko zaktualizowanych segmentów obrazu, które muszą zostać przekazane.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions automatycznie ponownie wdycha obraz w aplikacji funkcji; Proces odbywa się w mniej niż minutę.

1. W przeglądarce otwórz okno `https://<app_name>.scm.azurewebsites.net/` , zastępując `<app_name>` swoją unikatową nazwą. Ten adres URL to punkt końcowy narzędzi zaawansowanych (Kudu) dla kontenera aplikacji funkcji.

1. Zaloguj się do konta platformy Azure, a następnie wybierz pozycję **SSH,** aby nawiązać połączenie z kontenerem. Nawiązanie połączenia może potrwać kilka minut, jeśli platforma Azure nadal aktualizuje obraz kontenera.

1. Po nawiązaniu połączenia z kontenerem uruchom polecenie `top` , aby wyświetlić aktualnie uruchomione procesy. 

    ![Najważniejsze polecenie systemu Linux uruchomione w sesji SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-an-azure-storage-queue"></a>Zapis w kolejce usługi Azure Storage

Azure Functions umożliwia łączenie funkcji z innymi usługami i zasobami platformy Azure bez konieczności pisania własnego kodu integracji. Te *powiązania*, które reprezentują zarówno dane wejściowe, jak i wyjściowe, są deklarowane w definicji funkcji. Dane z powiązań są podawane do funkcji jako parametry. Wyzwalacz *jest* specjalnym typem powiązania wejściowego. Mimo że funkcja ma tylko jeden wyzwalacz, może mieć wiele powiązań wejściowych i wyjściowych. Aby dowiedzieć się więcej, [zobacz Azure Functions wyzwalaczy i powiązań](functions-triggers-bindings.md).

W tej sekcji pokazano, jak zintegrować funkcję z kolejką usługi Azure Storage. Powiązanie wyjściowe, które dodajesz do tej funkcji, zapisuje dane z żądania HTTP do komunikatu w kolejce.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]
::: zone-end

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-code-to-use-the-output-binding"></a>Dodawanie kodu w celu użycia powiązania wyjściowego

Po zdefiniowanym powiązaniu kolejki możesz teraz zaktualizować funkcję tak, aby odbierała parametr `msg` wyjściowy i zapisywała komunikaty w kolejce.
::: zone-end

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
### <a name="update-the-image-in-the-registry"></a>Aktualizowanie obrazu w rejestrze

1. W folderze głównym uruchom ponownie plik , a tym razem zaktualizuj `docker build` wersję w tagu na `v1.0.1` . Tak jak wcześniej zastąp `<docker_id>` Docker Hub identyfikatorem konta:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. Wypchnie zaktualizowany obraz z powrotem do repozytorium za pomocą pliku `docker push` :

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Ponieważ skonfigurowano ciągłe dostarczanie, zaktualizowanie obrazu w rejestrze ponownie spowoduje automatyczne zaktualizowanie aplikacji funkcji na platformie Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Wyświetlanie komunikatu w kolejce usługi Azure Storage

W przeglądarce użyj tego samego adresu URL co poprzednio, aby wywołać funkcję. W przeglądarce powinna być wyświetlana ta sama odpowiedź co poprzednio, ponieważ nie zmodyfikowano tej części kodu funkcji. Jednak dodany kod zapisał komunikat przy użyciu `name` parametru adresu URL w `outqueue` kolejce magazynu.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz kontynuować pracę z funkcją platformy Azure przy użyciu zasobów utworzonych w tym samouczku, możesz pozostawić wszystkie te zasoby na miejscu. Ponieważ utworzono plan Premium dla Azure Functions, koszty bieżące będą naliczane w wysokości 1 lub 2 USD dziennie.

Aby uniknąć kosztów bieżących, usuń `AzureFunctionsContainer-rg` grupę zasobów, aby wyczyścić wszystkie zasoby w tej grupie: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Następne kroki

+ [Funkcje monitorowania](functions-monitoring.md)
+ [Opcje skalowania i hostingu](functions-scale.md)
+ [Hostowanie bez serwera oparte na użytce Kubernetes](functions-kubernetes-keda.md)
