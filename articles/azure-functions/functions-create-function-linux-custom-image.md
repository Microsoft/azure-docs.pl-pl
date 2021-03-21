---
title: Tworzenie Azure Functions w systemie Linux przy użyciu obrazu niestandardowego
description: Informacje o sposobie tworzenia usługi Azure Functions działającej w niestandardowym obrazie systemu Linux.
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: 1c7a9fd83131ea6282d2ef4860b744fa348153ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98070921"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Tworzenie funkcji w systemie Linux przy użyciu kontenera niestandardowego

W tym samouczku utworzysz i wdrożono kod w celu Azure Functions jako niestandardowego kontenera Docker przy użyciu podstawowego obrazu systemu Linux. Obraz niestandardowy jest zazwyczaj używany, gdy funkcje wymagają określonej wersji językowej lub mają określoną zależność lub konfigurację, która nie jest dostarczana przez wbudowany obraz.

::: zone pivot="programming-language-other"
Azure Functions obsługuje dowolny język lub środowisko uruchomieniowe przy użyciu [niestandardowych programów obsługi](functions-custom-handlers.md). W przypadku niektórych języków, takich jak język programowania R używany w tym samouczku, należy zainstalować środowisko uruchomieniowe lub dodatkowe biblioteki jako zależności wymagające użycia kontenera niestandardowego.
::: zone-end

Wdrożenie kodu funkcji w niestandardowym kontenerze systemu Linux wymaga [planu Premium](functions-premium-plan.md) lub [dedykowanego planu (App Service)](dedicated-plan.md) . Ukończenie tego samouczka wiąże się z pozostałymi kosztami korzystania z kilku dolarów amerykańskich na koncie platformy Azure, które można zminimalizować przez [oczyszczenie zasobów](#clean-up-resources) po zakończeniu.

Można również użyć domyślnego kontenera Azure App Service, zgodnie z opisem w temacie [Tworzenie pierwszej funkcji hostowanej w systemie Linux](./create-first-function-cli-csharp.md?pivots=programming-language-python). Obsługiwane obrazy podstawowe dla Azure Functions są dostępne w [repozytorium Azure Functions obrazów podstawowych](https://hub.docker.com/_/microsoft-azure-functions-base).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
> [!div class="checklist"]
> * Utwórz aplikację funkcji i pliku dockerfile przy użyciu Azure Functions Core Tools.
> * Kompilowanie obrazu niestandardowego na platformie Docker.
> * Publikowanie obrazu niestandardowego w rejestrze kontenerów.
> * Tworzenie zasobów pomocniczych na platformie Azure dla aplikacji funkcji
> * Wdrażanie aplikacji funkcji z usługi Docker Hub.
> * Dodawanie ustawień aplikacji do aplikacji funkcji.
> * Włącz ciągłe wdrażanie.
> * Włącz połączenia SSH z kontenerem.
> * Dodawanie powiązania wyjściowego magazynu kolejki. 
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * Utwórz aplikację funkcji i pliku dockerfile przy użyciu Azure Functions Core Tools.
> * Kompilowanie obrazu niestandardowego na platformie Docker.
> * Publikowanie obrazu niestandardowego w rejestrze kontenerów.
> * Tworzenie zasobów pomocniczych na platformie Azure dla aplikacji funkcji
> * Wdrażanie aplikacji funkcji z usługi Docker Hub.
> * Dodawanie ustawień aplikacji do aplikacji funkcji.
> * Włącz ciągłe wdrażanie.
> * Włącz połączenia SSH z kontenerem.
::: zone-end

Ten samouczek można wykonać na dowolnym komputerze z systemem Windows, macOS lub Linux. 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ [Identyfikator platformy Docker](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Uruchom `docker login` , aby zalogować się do platformy Docker. To polecenie kończy się niepowodzeniem, jeśli program Docker nie jest uruchomiony, w tym przypadku Uruchom platformę Docker i spróbuj ponownie wykonać polecenie.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Tworzenie i testowanie projektu funkcji lokalnych

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
W terminalu lub wierszu polecenia Uruchom następujące polecenie dla wybranego języka, aby utworzyć projekt aplikacji funkcji w folderze o nazwie `LocalFunctionsProject` .  
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

`-DjavaVersion`Parametr informuje środowisko uruchomieniowe funkcji, którego wersja języka Java ma używać. Użyj polecenia `-DjavaVersion=11`, jeśli chcesz, aby funkcje działały w języku Java 11. Jeśli nie określisz `-DjavaVersion` , Maven domyślnie Java 8. Aby uzyskać więcej informacji, zobacz [wersje Java](functions-reference-java.md#java-versions).

> [!IMPORTANT]
> `JAVA_HOME`Aby można było ukończyć ten artykuł, zmienna środowiskowa musi być ustawiona na lokalizację instalacji poprawnej wersji JDK.

Maven prosi o podanie wartości, które są potrzebne, aby zakończyć Generowanie projektu przy wdrożeniu.   
Po wyświetleniu monitu podaj następujące wartości:

| Monit | Wartość | Opis |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Wartość, która jednoznacznie identyfikuje projekt we wszystkich projektach, zgodnie z [regułami nazewnictwa pakietów](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) dla języka Java. |
| **artifactId** | `fabrikam-functions` | Wartość, która jest nazwą jar, bez numeru wersji. |
| **Wersja** | `1.0-SNAPSHOT` | Wybierz wartość domyślną. |
| **Package** | `com.fabrikam.functions` | Wartość, która jest pakiet języka Java dla wygenerowanego kodu funkcji. Użyj wartości domyślnej. |

Wpisz `Y` lub naciśnij klawisz ENTER, aby potwierdzić.

Maven tworzy pliki projektu w nowym folderze o nazwie _artifactId_, w tym przykładzie `fabrikam-functions` . 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init LocalFunctionsProject --worker-runtime custom --docker
```
::: zone-end

`--docker`Opcja generuje `Dockerfile` dla projektu, który definiuje odpowiedni kontener niestandardowy do użycia z Azure Functions i wybranym środowiskiem uruchomieniowym.

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
Dodaj funkcję do projektu za pomocą następującego polecenia, gdzie `--name` argument jest unikatową nazwą funkcji, a `--template` argument określa wyzwalacz funkcji. `func new` tworzy podfolder pasujący do nazwy funkcji, która zawiera plik kodu odpowiedni dla wybranego języka projektu i plik konfiguracji o nazwie *function.json*.

```console
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end

::: zone pivot="programming-language-other" 
Dodaj funkcję do projektu za pomocą następującego polecenia, gdzie `--name` argument jest unikatową nazwą funkcji, a `--template` argument określa wyzwalacz funkcji. `func new` tworzy podfolder pasujący do nazwy funkcji zawierającej plik konfiguracji o nazwie *function.jsna*.

```console
func new --name HttpExample --template "HTTP trigger"
```

W edytorze tekstów Utwórz plik w folderze projektu o nazwie *Handler. R*. Dodaj następujący przykład jako jego zawartość.

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

W *host.jsna* Zmień sekcję, `customHandler` Aby skonfigurować polecenie uruchamiania niestandardowej procedury obsługi.

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

Aby przetestować funkcję lokalnie, Uruchom lokalny host środowiska uruchomieniowego Azure Functions w folderze głównym folderu projektu: 
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

Gdy `HttpExample` punkt końcowy zostanie wyświetlony w danych wyjściowych, przejdź do `http://localhost:7071/api/HttpExample?name=Functions` . W przeglądarce powinien zostać wyświetlony komunikat "Hello" z powrotem `Functions` , wartość dostarczona do `name` parametru zapytania.

Użyj **klawisza Ctrl** - **C** , aby zatrzymać hosta.

## <a name="build-the-container-image-and-test-locally"></a>Kompiluj obraz kontenera i przetestuj go lokalnie

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
Obowiązkowe Zapoznaj się z *pliku dockerfile* w folderze głównym folderu projektu. Pliku dockerfile opisuje środowisko wymagane do uruchomienia aplikacji funkcji w systemie Linux.  Pełną listę obsługiwanych obrazów podstawowych dla Azure Functions można znaleźć na [stronie Azure Functions Image Base](https://hub.docker.com/_/microsoft-azure-functions-base).
::: zone-end

::: zone pivot="programming-language-other"
Zapoznaj się z *pliku dockerfile* w folderze głównym folderu projektu. Pliku dockerfile opisuje środowisko wymagane do uruchomienia aplikacji funkcji w systemie Linux. Niestandardowe aplikacje obsługi używają `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice` obrazu jako bazy.

Zmodyfikuj *pliku dockerfile* w celu zainstalowania języka R. Zastąp zawartość *pliku dockerfile* następującym.

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

W folderze głównym projektu uruchom polecenie [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) i podaj nazwę, `azurefunctionsimage` i tag `v1.0.0` . Zastąp ciąg `<DOCKER_ID>` identyfikatorem konta usługi Docker Hub. To polecenie powoduje skompilowanie obrazu platformy Docker dla kontenera.

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Po zakończeniu działania polecenia można uruchomić nowy kontener lokalnie.
    
Aby przetestować kompilację, uruchom obraz w lokalnym kontenerze przy użyciu polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , ZASTĘPUJĄC ponownie `<DOCKER_ID` identyfikatorem platformy Docker i dodając argument porty `-p 8080:80` :

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
Gdy obraz jest uruchomiony w lokalnym kontenerze, Otwórz przeglądarkę do, w `http://localhost:8080` której powinien zostać wyświetlony obraz zastępczy poniżej. Obraz jest wyświetlany w tym momencie, ponieważ funkcja jest uruchomiona w kontenerze lokalnym, tak jak w przypadku platformy Azure, co oznacza, że jest chroniona przez klucz dostępu zdefiniowany w *function.jsna* z `"authLevel": "function"` właściwością. Kontener nie został jeszcze opublikowany w aplikacji funkcji na platformie Azure, więc klucz nie jest jeszcze dostępny. Jeśli chcesz przeprowadzić test w odniesieniu do lokalnego kontenera, Zatrzymaj platformę Docker, Zmień właściwość autoryzacji na `"authLevel": "anonymous"` , Odbuduj obraz i ponownie uruchom platformę Docker. Następnie zresetuj `"authLevel": "function"` w *function.jsna*. Aby uzyskać więcej informacji, zobacz [klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys).

![Obraz zastępczy wskazujący, że kontener jest uruchomiony lokalnie](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
Gdy obraz jest uruchomiony w lokalnym kontenerze, przejdź do `http://localhost:8080/api/HttpExample?name=Functions` , który powinien wyświetlać ten sam komunikat "Hello" jak wcześniej. Ponieważ Maven Archetype generuje funkcję wyzwalaną przez protokół HTTP, która korzysta z anonimowej autoryzacji, można nadal wywołać funkcję, mimo że jest uruchomiona w kontenerze. 
::: zone-end  

Po zweryfikowaniu aplikacji funkcji w kontenerze Zatrzymaj platformę Docker z **klawiszem Ctrl** + **C**.

## <a name="push-the-image-to-docker-hub"></a>Wypchnij obraz do centrum Docker

Docker Hub to rejestr kontenerów, który hostuje obrazy i udostępnia usługi obrazów i kontenerów. Aby udostępnić obraz, który obejmuje wdrażanie na platformie Azure, należy wypchnąć go do rejestru.

1. Jeśli jeszcze nie zalogowano się do platformy Docker, zrób to za pomocą polecenia [Docker login](https://docs.docker.com/engine/reference/commandline/login/) , zastępując `<docker_id>` identyfikatorem platformy Docker. To polecenie powoduje zapytanie o nazwę użytkownika i hasło. Komunikat "Logowanie pomyślne" potwierdza, że użytkownik jest zalogowany.

    ```console
    docker login
    ```
    
1. Po zalogowaniu wypchnij obraz do usługi Docker Hub przy użyciu polecenia [Docker push](https://docs.docker.com/engine/reference/commandline/push/) , ZASTĘPUJĄC go `<docker_id>` identyfikatorem Docker.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. W zależności od szybkości sieci, wypychanie obrazu po raz pierwszy może potrwać kilka minut (wypychanie kolejnych zmian jest znacznie szybsze). Podczas oczekiwania możesz przejoć do następnej sekcji i utworzyć zasoby platformy Azure w innym terminalu.

## <a name="create-supporting-azure-resources-for-your-function"></a>Tworzenie obsługi zasobów platformy Azure dla funkcji

Aby wdrożyć kod funkcji na platformie Azure, musisz utworzyć trzy zasoby:

- Grupa zasobów, która jest kontenerem logicznym dla powiązanych zasobów.
- Konto usługi Azure Storage, które zachowuje stan i inne informacje o Twoich projektach.
- Aplikacja funkcji, która zapewnia środowisko do wykonywania kodu funkcji. Aplikacja funkcji jest mapowana na projekt funkcji lokalnej i umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie i udostępnianie zasobów.

Aby utworzyć te elementy, Użyj poleceń interfejsu wiersza polecenia platformy Azure. Każde polecenie zawiera dane wyjściowe JSON po zakończeniu.

1. Zaloguj się do platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login) :

    ```azurecli
    az login
    ```
    
1. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów o nazwie `AzureFunctionsContainers-rg` w `westeurope` regionie. (Zazwyczaj tworzysz grupę zasobów i zasoby w regionie, w którym się znajdujesz, przy użyciu dostępnego regionu z tego `az account list-locations` polecenia).

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Nie można hostować aplikacji systemu Linux i Windows w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie `AzureFunctionsContainers-rg` przy użyciu aplikacji funkcji systemu Windows lub aplikacji sieci Web, musisz użyć innej grupy zasobów.
    
1. Utwórz konto magazynu ogólnego przeznaczenia w grupie zasobów i regionie przy użyciu polecenia [AZ Storage account Create](/cli/azure/storage/account#az-storage-account-create) . W poniższym przykładzie Zastąp `<storage_name>` wartość globalnie unikatową nazwą, która jest odpowiednia dla Ciebie. Nazwy muszą zawierać od 3 do 24 znaków cyfry i małe litery. `Standard_LRS` Określa typowe konto ogólnego przeznaczenia.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    W tym samouczku konto magazynu odnosi się tylko do kilku centów USD.
    
1. Użyj polecenia, aby utworzyć plan Premium dla Azure Functions o nazwie `myPremiumPlan` w warstwie cenowej **elastyczne 1** ( `--sku EP1` ), w regionie Europa Zachodnia ( `-location westeurope` lub użyć odpowiedniego regionu w sąsiedztwie) i kontenera systemu Linux ( `--is-linux` ).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    W tym miejscu jest używany plan Premium, który można skalować odpowiednio do wymagań. Aby dowiedzieć się więcej o hostingu, zobacz [Porównanie planów hostingu usługi Azure Functions](functions-scale.md). Aby obliczyć koszty, zobacz [stronę cennika funkcji](https://azure.microsoft.com/pricing/details/functions/).

    Polecenie udostępnia również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą którego można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitor Azure Functions](functions-monitoring.md). Wystąpienie nie wiąże się z żadnymi kosztami, dopóki nie zostanie uaktywnione.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Tworzenie i Konfigurowanie aplikacji funkcji na platformie Azure przy użyciu obrazu

Aplikacja funkcji na platformie Azure zarządza wykonywaniem funkcji w Twoim planie hostingu. W tej sekcji użyjesz zasobów platformy Azure z poprzedniej sekcji, aby utworzyć aplikację funkcji z obrazu w usłudze Docker Hub i skonfigurować ją za pomocą parametrów połączenia do usługi Azure Storage.

1. Utwórz aplikację Functions za pomocą polecenia [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) . W poniższym przykładzie Zastąp `<storage_name>` wartość nazwą użytą w poprzedniej sekcji dla konta magazynu. Należy również zastąpić `<app_name>` globalnie unikatową nazwą, która jest odpowiednia dla Ciebie, i `<docker_id>` identyfikatorem platformy Docker.

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
    
    Parametr *Deployment-Container-Image-Name* określa obraz do użycia dla aplikacji funkcji. Możesz użyć polecenia [AZ functionapp config Container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) , aby wyświetlić informacje o obrazie używanym do wdrożenia. Można również użyć polecenia [AZ functionapp config Container Set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) do wdrożenia z innego obrazu.

1. Wyświetl parametry połączenia dla konta magazynu utworzonego za pomocą polecenia [AZ Storage account show-Connection-String](/cli/azure/storage/account) . Zamień `<storage-name>` na nazwę utworzonego powyżej konta magazynu:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Dodaj to ustawienie do aplikacji funkcji przy użyciu polecenia [AZ functionapp config AppSettings Set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) . W poniższym poleceniu Zastąp `<app_name>` nazwą swojej aplikacji funkcji i Zamień na `<connection_string>` Parametry połączenia z poprzedniego kroku (długi zakodowany ciąg zaczynający się od "DefaultEndpointProtocol ="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

    > [!TIP]
    > W bash można użyć zmiennej powłoki, aby przechwycić parametry połączenia zamiast używać Schowka. Najpierw użyj następującego polecenia, aby utworzyć zmienną z parametrami połączenia:
    > 
    > ```bash
    > storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
    > ```
    > 
    > Następnie odwołując się do zmiennej w drugim poleceniu:
    > 
    > ```azurecli
    > az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
    > ```

1. Funkcja może teraz używać tych parametrów połączenia w celu uzyskania dostępu do konta magazynu.

> [!NOTE]    
> W przypadku opublikowania niestandardowego obrazu na koncie kontenera prywatnego należy zamiast tego użyć zmiennych środowiskowych w pliku dockerfile jako parametrów połączenia. Aby uzyskać więcej informacji, zapoznaj się z [instrukcją ENV](https://docs.docker.com/engine/reference/builder/#env). Należy również ustawić zmienne `DOCKER_REGISTRY_SERVER_USERNAME` i `DOCKER_REGISTRY_SERVER_PASSWORD` . Aby użyć wartości, należy ponownie skompilować obraz, wypchnąć obraz do rejestru, a następnie ponownie uruchomić aplikację funkcji na platformie Azure.

## <a name="verify-your-functions-on-azure"></a>Weryfikowanie funkcji na platformie Azure

W przypadku obrazu wdrożonego w aplikacji funkcji na platformie Azure można teraz wywołać funkcję za pomocą żądań HTTP. Ponieważ *function.jsw* definicji zawiera właściwość `"authLevel": "function"` , należy najpierw uzyskać klucz dostępu (nazywany także "kluczem funkcji") i uwzględnić go jako parametr adresu URL w dowolnych żądaniach do punktu końcowego.

1. Pobierz adres URL funkcji z kluczem dostępu (funkcji) za pomocą Azure Portal lub przy użyciu interfejsu wiersza polecenia platformy Azure z `az rest` poleceniem).

    # <a name="portal"></a>[Portal](#tab/portal)

    1. Zaloguj się do Azure Portal, a następnie wyszukaj i wybierz pozycję **aplikacja funkcji**.

    1. Wybierz funkcję, którą chcesz zweryfikować.

    1. W lewym panelu nawigacyjnym wybierz pozycję **funkcje**, a następnie wybierz funkcję, którą chcesz zweryfikować.

        ![Wybierz funkcję w Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-select-function.png)   

    
    1. Wybierz pozycję **Pobierz adres URL funkcji**.

        ![Pobierz adres URL funkcji z Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-function-url.png)   

    
    1. W oknie podręcznym wybierz pozycję **domyślne (klawisz funkcji)** , a następnie skopiuj adres URL do Schowka. Klucz jest ciągiem znaków poniżej `?code=` .

        ![Wybierz domyślny klucz dostępu do funkcji](./media/functions-create-function-linux-custom-image/functions-portal-copy-url.png)   


    > [!NOTE]  
    > Ponieważ aplikacja funkcji jest wdrażana jako kontener, nie można wprowadzać zmian w kodzie funkcji w portalu. Zamiast tego należy zaktualizować projekt w lokalnym obrazie, ponownie wypchnąć obraz do rejestru, a następnie wdrożyć go na platformie Azure. Wdrożenie ciągłe można skonfigurować w dalszej części artykułu.
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

    1. Utwórz ciąg adresu URL w następującym formacie: zastępowanie `<subscription_id>` , `<resource_group>` , i `<app_name>` z identyfikatorem subskrypcji platformy Azure, grupą zasobów aplikacji funkcji i nazwą aplikacji funkcji, odpowiednio

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Na przykład adres URL może wyglądać następująco:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Dla wygody możesz zamiast tego przypisać adres URL do zmiennej środowiskowej i użyć jej w `az rest` poleceniu.
    
    1. Uruchom następujące `az rest` polecenie (dostępne w interfejsie wiersza polecenia platformy Azure w wersji 2.0.77 i nowszych), zastępując `<uri>` wartość ciągiem identyfikatora URI z ostatniego kroku, włącznie z cudzysłowami:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. Dane wyjściowe polecenia to klucz funkcji. Pełny adres URL funkcji to `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>` , zastępując `<app_name>` , `<function_name>` i `<key>` z określonymi wartościami.
    
        > [!NOTE]
        > Pobrany tutaj klucz jest kluczem *hosta* , który działa dla wszystkich funkcji w aplikacji Functions. Metoda pokazana dla portalu Pobiera klucz tylko dla jednej funkcji.

    ---

1. Wklej adres URL funkcji na pasku adresu przeglądarki, dodając parametr `&name=Azure` do końca tego adresu URL. Tekst, taki jak "Hello, Azure", powinien pojawić się w przeglądarce.

    ![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Aby przetestować autoryzację, Usuń `code=` parametr z adresu URL i sprawdź, czy nie otrzymujesz odpowiedzi z funkcji.


## <a name="enable-continuous-deployment-to-azure"></a>Włączanie ciągłego wdrażania na platformie Azure

Możesz włączyć Azure Functions, aby automatycznie aktualizować wdrożenie obrazu przy każdej aktualizacji obrazu w rejestrze.

1. Włącz ciągłe wdrażanie za pomocą polecenia [AZ functionapp Deployment Container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) , zastępując `<app_name>` je nazwą aplikacji funkcji:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    To polecenie umożliwia ciągłe wdrażanie i zwraca adres URL elementu webhook wdrożenia. (Ten adres URL można pobrać w dowolnym momencie za pomocą polecenia [AZ functionapp Deployment Container show-CD-URL](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) ).

1. Skopiuj adres URL elementu webhook wdrożenia do Schowka.

1. Otwórz [centrum platformy Docker](https://hub.docker.com/), zaloguj się i wybierz pozycję **repozytoria** na pasku nawigacyjnym. Znajdź i wybierz pozycję obraz, wybierz kartę elementy **webhook** , określ **nazwę elementu webhook**, wklej adres URL w **adresie URL elementu webhook**, a następnie wybierz pozycję **Utwórz**:

    ![Dodawanie elementu webhook w repozytorium DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. W przypadku zestawu elementów webhook Azure Functions ponownie wdraża obraz przy każdej aktualizacji w usłudze Docker Hub.

## <a name="enable-ssh-connections"></a>Włącz połączenia SSH

Protokół SSH umożliwia bezpieczną komunikację między kontenerem i klientem. Po włączeniu protokołu SSH można nawiązać połączenie z kontenerem za pomocą App Service Advanced Tools (kudu). Aby ułatwić łączenie się z kontenerem przy użyciu protokołu SSH, Azure Functions zapewnia podstawowy obraz, który ma już włączony protokół SSH. Musisz edytować tylko pliku dockerfile, a następnie ponownie skompilować i wdrożyć obraz. Następnie można połączyć się z kontenerem za pomocą narzędzi zaawansowanych (kudu)

1. W pliku dockerfile Dołącz ciąg `-appservice` do obrazu podstawowego w `FROM` instrukcji:

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
    
1. Ponownie skompiluj obraz przy użyciu `docker build` polecenia, zastępując go `<docker_id>` identyfikatorem platformy Docker:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Wypchnij zaktualizowany obraz do centrum platformy Docker, który powinien trwać znacznie krócej niż w przypadku pierwszej wypychania tylko zaktualizowanych segmentów obrazu.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions automatycznie ponownie wdraża obraz w aplikacji funkcji; proces odbywa się w czasie krótszym niż minutę.

1. W przeglądarce Otwórz plik `https://<app_name>.scm.azurewebsites.net/` , zastępując `<app_name>` go unikatową nazwą. Ten adres URL to punkt końcowy narzędzi Advanced Tools (kudu) dla kontenera aplikacji funkcji.

1. Zaloguj się do konta platformy Azure, a następnie wybierz protokół **SSH** , aby nawiązać połączenie z kontenerem. Łączenie może potrwać kilka minut, jeśli platforma Azure nadal aktualizuje obraz kontenera.

1. Po nawiązaniu połączenia z kontenerem Uruchom `top` polecenie, aby wyświetlić aktualnie uruchomione procesy. 

    ![Najważniejsze polecenie systemu Linux działające w sesji SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-an-azure-storage-queue"></a>Zapisywanie w kolejce usługi Azure Storage

Azure Functions umożliwia łączenie funkcji z innymi usługami i zasobami platformy Azure bez konieczności pisania kodu integracji. Te *powiązania*, które reprezentują zarówno dane wejściowe, jak i wyjściowe, są zadeklarowane w definicji funkcji. Dane z powiązań są podawane do funkcji jako parametry. *Wyzwalacz* jest specjalnym typem powiązania danych wejściowych. Chociaż funkcja ma tylko jeden wyzwalacz, może mieć wiele powiązań wejściowych i wyjściowych. Aby dowiedzieć się więcej, zobacz temat [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md).

W tej sekcji pokazano, jak zintegrować funkcję z kolejką usługi Azure Storage. Powiązanie danych wyjściowych dodawane do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce.

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

## <a name="add-code-to-use-the-output-binding"></a>Dodaj kod, aby użyć powiązania danych wyjściowych

Po zdefiniowaniu powiązania kolejki można teraz zaktualizować funkcję w celu otrzymywania `msg` parametru wyjściowego i zapisu komunikatów do kolejki.
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

1. W folderze głównym `docker build` ponownie uruchom program, a następnie zaktualizuj wersję w znaczniku do `v1.0.1` . Tak jak wcześniej, Zastąp ciąg `<docker_id>` identyfikatorem konta centrum Docker:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. Wypchnij zaktualizowany obraz z powrotem do repozytorium za pomocą polecenia `docker push` :

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Ze względu na to, że skonfigurowano ciągłe dostarczanie, Aktualizacja obrazu w rejestrze jest ponownie automatycznie aktualizowana przez aplikację funkcji na platformie Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Wyświetlanie komunikatu w kolejce usługi Azure Storage

W przeglądarce Użyj tego samego adresu URL, co wcześniej, aby wywołać funkcję. W przeglądarce powinna zostać wyświetlona taka sama odpowiedź jak wcześniej, ponieważ nie została zmodyfikowana ta część kodu funkcji. Dodany kod, jednak zapisał komunikat przy użyciu `name` parametru adresu URL do `outqueue` kolejki magazynu.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz kontynuować pracę z funkcją platformy Azure przy użyciu zasobów utworzonych w tym samouczku, możesz pozostawić wszystkie te zasoby na miejscu. Ze względu na to, że został utworzony plan Premium dla Azure Functions, ponosisz jeden lub dwa USD dziennie.

Aby uniknąć bieżących kosztów, Usuń `AzureFunctionsContainer-rg` grupę zasobów, aby wyczyścić wszystkie zasoby w tej grupie: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Następne kroki

+ [Funkcje monitorowania](functions-monitoring.md)
+ [Opcje skalowania i hostowania](functions-scale.md)
+ [Hosting bezserwerowy oparty na Kubernetes](functions-kubernetes-keda.md)
