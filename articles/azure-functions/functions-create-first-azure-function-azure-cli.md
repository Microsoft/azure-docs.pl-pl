---
title: Tworzenie na platformie Azure funkcji, która odpowiada na żądania HTTP
description: Dowiedz się, jak utworzyć funkcję z wiersza polecenia, a następnie opublikować projekt lokalny do hostingu bezserwerowego w Azure Functions.
ms.date: 03/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli, devx-track-azurepowershell
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: b457c3b0ec0f68dd6a8213fbebe7a2596bed4c2e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519677"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Szybki Start: Tworzenie funkcji na platformie Azure, która odpowiada na żądania HTTP

::: zone pivot="programming-language-csharp"  
W tym artykule opisano użycie narzędzi wiersza polecenia w celu utworzenia funkcji opartej na bibliotece klasy C#, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-javascript"
W tym artykule opisano użycie narzędzi wiersza polecenia w celu utworzenia funkcji języka JavaScript, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions. 
::: zone-end
::: zone pivot="programming-language-typescript"
W tym artykule opisano użycie narzędzi wiersza polecenia w celu utworzenia funkcji języka TypeScript, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions. 
::: zone-end   
::: zone pivot="programming-language-powershell"
W tym artykule opisano użycie narzędzi wiersza polecenia w celu utworzenia funkcji programu PowerShell, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-python" 
W tym artykule opisano użycie narzędzi wiersza polecenia w celu utworzenia funkcji języka Python, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-java" 
W tym artykule opisano użycie narzędzi wiersza polecenia w celu utworzenia funkcji języka Java, która reaguje na żądania HTTP. Po przetestowaniu kodu lokalnie należy wdrożyć go w środowisku bezserwerowym Azure Functions. 
::: zone-end

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Istnieje również oparta na [Visual Studio Code wersja](functions-create-first-function-vs-code.md) tego artykułu.
::: zone-end  
::: zone pivot="programming-language-java"  
> [!NOTE]
> Jeśli Maven nie jest preferowanym narzędziem programistycznym, zapoznaj się z naszymi samouczkami dotyczącymi deweloperów języka Java za pomocą [Gradle](./functions-create-first-java-gradle.md), [IntelliJ pomysłu](/azure/developer/java/toolkit-for-intellij/quickstart-functions) i [Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-java).
::: zone-end  

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-a-local-function-project"></a>Utwórz projekt funkcji lokalnej

W Azure Functions, projekt funkcji jest kontenerem dla jednej lub kilku poszczególnych funkcji, które reagują na konkretny wyzwalacz. Wszystkie funkcje w projekcie mają takie same konfiguracje lokalne i hostingowe. W tej sekcji utworzysz projekt funkcji zawierający pojedynczą funkcję.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Uruchom `func init` polecenie w następujący sposób, aby utworzyć projekt Functions w folderze o nazwie *LocalFunctionProj* z określonym środowiskiem uruchomieniowym:  
::: zone-end  
::: zone pivot="programming-language-python"  
```
func init LocalFunctionProj --python
```
::: zone-end  
::: zone pivot="programming-language-csharp"  
```
func init LocalFunctionProj --dotnet
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```
func init LocalFunctionProj --javascript
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```
func init LocalFunctionProj --typescript
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```
func init LocalFunctionProj --powershell
```
::: zone-end    
::: zone pivot="programming-language-java"  
W pustym folderze uruchom następujące polecenie, aby wygenerować projekt usługi Functions z [archetypu narzędzia Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html). 

> [!IMPORTANT]
> + Użyj `-DjavaVersion=11` , jeśli chcesz, aby działały w języku Java 11. Aby dowiedzieć się więcej, zobacz [wersje Java](functions-reference-java.md#java-versions). 
> + `JAVA_HOME`Aby można było ukończyć ten artykuł, zmienna środowiskowa musi być ustawiona na lokalizację instalacji poprawnej wersji JDK.

# <a name="bash"></a>[bash](#tab/bash)
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

Maven prosi o podanie wartości, które są potrzebne, aby zakończyć Generowanie projektu przy wdrożeniu.   
Po wyświetleniu monitu podaj następujące wartości:

| Monit | Wartość | Opis |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Wartość, która jednoznacznie identyfikuje projekt we wszystkich projektach, zgodnie z [regułami nazewnictwa pakietów](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) dla języka Java. |
| **artifactId** | `fabrikam-functions` | Wartość, która jest nazwą jar, bez numeru wersji. |
| **Wersja** | `1.0-SNAPSHOT` | Wybierz wartość domyślną. |
| **Package** | `com.fabrikam` | Wartość, która jest pakiet języka Java dla wygenerowanego kodu funkcji. Użyj wartości domyślnej. |

Wpisz `Y` lub naciśnij klawisz ENTER, aby potwierdzić.

Maven tworzy pliki projektu w nowym folderze o nazwie _artifactId_, w tym przykładzie `fabrikam-functions` . 

::: zone-end  
Przejdź do folderu projektu:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionProj
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
Ten folder zawiera różne pliki dla projektu, w tym pliki konfiguracji o nazwie [local.settings.js](functions-run-local.md#local-settings-file) i [host.jsna](functions-host-json.md). Ponieważ *local.settings.json* może zawierać wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *. gitignore* .

[!INCLUDE [functions-cli-add-function](../../includes/functions-cli-add-function.md)]

### <a name="optional-examine-the-file-contents"></a>Obowiązkowe Sprawdzanie zawartości pliku

W razie potrzeby możesz pominąć, aby [uruchomić funkcję lokalnie](#run-the-function-locally) i później przejrzeć zawartość pliku.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* zawiera `Run` metodę, która odbiera dane żądania w `req` zmiennej, jest identyfikatorem [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) , który jest uzupełniony o **HttpTriggerAttribute**, który definiuje zachowanie wyzwalacza. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Zwracany obiekt to element [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) , który zwraca komunikat odpowiedzi jako [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) lub [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Aby dowiedzieć się więcej, zobacz [Azure Functions wyzwalacze i powiązania HTTP](./functions-bindings-http-webhook.md?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-java"
#### <a name="functionjava"></a>Function. Java
*Funkcja. Java* zawiera `run` metodę, która odbiera dane żądania w `request` zmiennej to [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) , który jest uzupełniony adnotacją [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) , która definiuje zachowanie wyzwalacza. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Komunikat odpowiedzi jest generowany przez interfejs API [HttpResponseMessage. Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) .

#### <a name="pomxml"></a>pom.xml

Ustawienia zasobów platformy Azure utworzonych na potrzeby hostowania aplikacji są definiowane w elemencie **konfiguracji** wtyczki z identyfikatorem **grupy** `com.microsoft.azure` w wygenerowanym pliku pom.xml. Na przykład poniższy element konfiguracji instruuje wdrożenie oparte na Maven, aby utworzyć aplikację funkcji w `java-functions-group` grupie zasobów w `westus` regionie. Sama aplikacja funkcji działa w systemie Windows hostowanym w `java-functions-app-service-plan` planie, który domyślnie jest planem zużycia bezserwerowego.    

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-102":::

Możesz zmienić te ustawienia, aby kontrolować sposób tworzenia zasobów na platformie Azure, na przykład przez zmianę `runtime.os` z `windows` na `linux` przed początkowym wdrożeniem. Aby uzyskać pełną listę ustawień obsługiwanych przez wtyczkę Maven, zobacz [szczegóły konfiguracji](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).

#### <a name="functiontestjava"></a>FunctionTest. Java

Archetype generuje również test jednostkowy dla funkcji. W przypadku zmiany funkcji w celu dodania powiązań lub dodania nowych funkcji do projektu należy również zmodyfikować testy w pliku *FunctionTest. Java* .
::: zone-end  
::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init \_ \_ . PR

* \_ \_ init \_ \_ . PR* zawiera `main()` funkcję języka Python, która jest wyzwalana zgodnie z konfiguracją w *function.jsna*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Dla wyzwalacza HTTP funkcja otrzymuje dane żądania w zmiennej `req` , zgodnie z definicją w *function.json*. `req` jest wystąpieniem [klasy Azure. Functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Obiekt zwracany, zdefiniowany jako `$return` w *function.json*, jest wystąpieniem [klasy Azure. Functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Aby dowiedzieć się więcej, zobacz [Azure Functions wyzwalacze i powiązania HTTP](./functions-bindings-http-webhook.md?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js


*index.js* eksportuje funkcję, która jest wyzwalana zgodnie z konfiguracją w *function.jsna*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

Dla wyzwalacza HTTP funkcja otrzymuje dane żądania w zmiennej `req` , zgodnie z definicją w *function.json*. Obiekt zwracany, zdefiniowany jako `$return` w *function.json*, jest odpowiedzią. Aby dowiedzieć się więcej, zobacz [Azure Functions wyzwalacze i powiązania HTTP](./functions-bindings-http-webhook.md?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>indeks. TS

*index. TS* eksportuje funkcję, która jest wyzwalana zgodnie z konfiguracją w *function.jsna*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Dla wyzwalacza HTTP funkcja otrzymuje dane żądania w zmiennej `req` typu **HttpRequest** zgodnie z definicją w *function.json*. Obiekt zwracany, zdefiniowany jako `$return` w *function.json*, jest odpowiedzią. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

*run.ps1* definiuje skrypt funkcji, który jest wyzwalany zgodnie z konfiguracją w *function.jsna*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Dla wyzwalacza HTTP funkcja otrzymuje dane żądania przekazywane do `$Request` param zdefiniowanego w *function.json*. Obiekt zwracany, zdefiniowany jako `Response` w *function.json*, jest przesyłany do `Push-OutputBinding` polecenia cmdlet jako odpowiedź. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.json* to plik konfiguracji, który definiuje dane wejściowe i wyjściowe `bindings` dla funkcji, w tym typ wyzwalacza. 
::: zone-end

::: zone pivot="programming-language-python"
`scriptFile`W razie potrzeby można zmienić w taki sposób, aby wywoływać inny plik Python.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Każde powiązanie wymaga kierunku, typu i unikatowej nazwy. Wyzwalacz HTTP ma powiązanie wejściowe typu [`httpTrigger`](functions-bindings-http-webhook-trigger.md) i powiązania danych wyjściowych typu [`http`](functions-bindings-http-webhook-output.md) .
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"    
## <a name="create-supporting-azure-resources-for-your-function"></a>Tworzenie obsługi zasobów platformy Azure dla funkcji

Aby można było wdrożyć kod funkcji na platformie Azure, należy utworzyć trzy zasoby:

- Grupa zasobów, która jest kontenerem logicznym dla powiązanych zasobów.
- Konto magazynu, które zachowuje stan i inne informacje o projektach.
- Aplikacja funkcji, która zapewnia środowisko do wykonywania kodu funkcji. Aplikacja funkcji jest mapowana na projekt funkcji lokalnej i umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie i udostępnianie zasobów.

Użyj następujących poleceń interfejsu wiersza polecenia platformy Azure, aby utworzyć te elementy. Każde polecenie zawiera dane wyjściowe JSON po zakończeniu.

Jeśli jeszcze tego nie zrobiono, zaloguj się do platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login) :

```azurecli
az login
```
    
Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów o nazwie `AzureFunctionsQuickstart-rg` w `westeurope` regionie. (Zazwyczaj tworzysz grupę zasobów i zasoby w regionie, w którym się znajdujesz, przy użyciu dostępnego regionu z tego `az account list-locations` polecenia).

```azurecli
az group create --name AzureFunctionsQuickstart-rg --location westeurope
```

> [!NOTE]
> Nie można hostować aplikacji systemu Linux i Windows w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie `AzureFunctionsQuickstart-rg` przy użyciu aplikacji funkcji systemu Windows lub aplikacji sieci Web, musisz użyć innej grupy zasobów.
 
    
Utwórz konto magazynu ogólnego przeznaczenia w grupie zasobów i regionie przy użyciu polecenia [AZ Storage account Create](/cli/azure/storage/account#az-storage-account-create) . W poniższym przykładzie Zastąp `<STORAGE_NAME>` wartość globalnie unikatową nazwą, która jest odpowiednia dla Ciebie. Nazwy muszą zawierać od 3 do 24 znaków cyfry i małe litery. `Standard_LRS` Określa konto ogólnego przeznaczenia, które jest [obsługiwane przez funkcje](storage-considerations.md#storage-account-requirements).

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
```

W tym przewodniku szybki start konto magazynu odnosi się tylko do kilku centów (USD).
    
Utwórz aplikację funkcji przy użyciu polecenia [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) . W poniższym przykładzie Zastąp ciąg `<STORAGE_NAME>` nazwą konta, które zostało użyte w poprzednim kroku, i Zastąp ciąg `<APP_NAME>` globalnie unikatową nazwą, która jest odpowiednia dla Ciebie. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. 
::: zone-end  

::: zone pivot="programming-language-python"  
Jeśli używasz języka Python 3,8, Zmień `--runtime-version` na `3.8` i `--functions_version` na `3` .

Jeśli używasz języka Python 3,6, przejdź `--runtime-version` do `3.6` .

```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Jeśli używasz Node.js 8, Zmień również `--runtime-version` na `8` .


```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-csharp"  
```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-powershell"  
```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
To polecenie tworzy aplikację funkcji działającą w określonym środowisku uruchomieniowym języka zgodnie z [planem zużycia Azure Functions](functions-scale.md#consumption-plan), który jest bezpłatny dla nakładu pracy w tym miejscu. Polecenie udostępnia również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą którego można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitor Azure Functions](functions-monitoring.md). Wystąpienie nie wiąże się z żadnymi kosztami, dopóki nie zostanie uaktywnione.
    
## <a name="deploy-the-function-project-to-azure"></a>Wdróż projekt funkcji na platformie Azure
::: zone-end  

::: zone pivot="programming-language-typescript"  
Przed rozpoczęciem wdrażania projektu na platformie Azure za pomocą podstawowych narzędzi można utworzyć kompilację plików JavaScript w środowisku produkcyjnym na podstawie plików źródłowych TypeScript.

Następujące polecenie przygotowuje projekt TypeScript do wdrożenia:

```
npm run build:production 
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
W przypadku niezbędnych zasobów możesz teraz przystąpić do wdrażania projektu funkcji lokalnych w aplikacji funkcji na platformie Azure przy użyciu polecenia [Func Azure functionapp Publish](functions-run-local.md#project-file-deployment) . W poniższym przykładzie Zastąp `<APP_NAME>` nazwą swojej aplikacji.

```
func azure functionapp publish <APP_NAME>
```

Jeśli zobaczysz błąd, "nie można odnaleźć aplikacji o nazwie...", poczekaj kilka sekund i spróbuj ponownie, ponieważ platforma Azure mogła nie zostać w pełni zainicjowana przez poprzednie `az functionapp create` polecenie.

Polecenie Publikuj wyświetla wyniki podobne do następujących danych wyjściowych (obcięty dla uproszczenia):

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
</pre>

::: zone-end  
::: zone pivot="programming-language-java"  
## <a name="deploy-the-function-project-to-azure"></a>Wdróż projekt funkcji na platformie Azure

Aplikacja funkcji i powiązane zasoby są tworzone na platformie Azure podczas pierwszego wdrożenia projektu usługi Functions. Ustawienia zasobów platformy Azure utworzonych na potrzeby hostowania aplikacji są zdefiniowane w [ plikupom.xml](#pomxml). W tym artykule zaakceptujesz ustawienia domyślne.

> [!TIP]
> Aby utworzyć aplikację funkcji działającą w systemie Linux zamiast systemu Windows, Zmień `runtime.os` element w pliku pom.xml z `windows` na `linux` . W [tych regionach](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)jest obsługiwany system Linux w planie zużycia. W tej samej grupie zasobów nie można używać aplikacji działających w systemie Linux i aplikacji działających w ramach systemu Windows.

Przed wdrożeniem programu Użyj polecenia [AZ login](/cli/azure/authenticate-azure-cli) Azure CLI, aby zalogować się do subskrypcji platformy Azure. 

```azurecli
az login
```

Użyj następującego polecenia, aby wdrożyć projekt w nowej aplikacji funkcji. 

```
mvn azure-functions:deploy
```

Spowoduje to utworzenie następujących zasobów na platformie Azure:

+ Grupa zasobów. Nazwana jako _Java-Functions-Group_.
+ Konto magazynu. Wymagane przez funkcje. Nazwa jest generowana losowo na podstawie wymagań dotyczących nazw kont magazynu.
+ Plan hostingu. Hosting bezserwerowy dla aplikacji funkcji w regionie _zachodnim_ . Nazwa to _Java-Functions-App-Service-plan_.
+ Aplikacja funkcji. Aplikacja funkcji jest jednostką wdrażania i wykonywania dla funkcji. Nazwa jest generowana losowo na podstawie _artifactId_, z uwzględnieniem losowo wygenerowanego numeru. 

Wdrożenie pakuje pliki projektu i wdraża je w nowej aplikacji funkcji przy użyciu polecenia [zip Deployment](functions-deployment-technologies.md#zip-deploy). Kod jest uruchamiany z pakietu wdrożeniowego na platformie Azure.
::: zone-end

## <a name="invoke-the-function-on-azure"></a>Wywoływanie funkcji na platformie Azure

Ponieważ funkcja używa wyzwalacza HTTP, wywołuje ją, wysyłając żądanie HTTP do jego adresu URL w przeglądarce lub przy użyciu narzędzia, takiego jak zwinięcie. W obu wystąpieniach `code` parametr URL jest unikatowym [kluczem funkcji](functions-bindings-http-webhook-trigger.md#authorization-keys) , który autoryzuje wywołanie punktu końcowego funkcji.

# <a name="browser"></a>[Przeglądarka](#tab/browser)

Skopiuj pełny **adres URL Wywołaj** pokazany w danych wyjściowych polecenia Publikuj na pasku adresu przeglądarki, dołączając parametr zapytania `&name=Functions` . Przeglądarka powinna wyświetlać podobne dane wyjściowe, jak w przypadku lokalnego uruchomienia funkcji.

![Dane wyjściowe funkcji uruchamianej na platformie Azure w przeglądarce](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[odsłon](#tab/curl)

Uruchom [`curl`](https://curl.haxx.se/) polecenie **Invoke URL**, dodając parametr `&name=Functions` . Danymi wyjściowymi polecenia powinien być tekst "Hello Functions".

![Dane wyjściowe funkcji uruchamianej na platformie Azure przy użyciu programu zwinięcie](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Aby przeglądać dzienniki niemal w czasie rzeczywistym dla opublikowanej aplikacji funkcji, użyj [Live Metrics Stream Application Insights](functions-monitoring.md#streaming-logs).
>
> Uruchom następujące polecenie, aby otworzyć strumień metryk na żywo w przeglądarce.
>   ```
>   func azure functionapp logstream <APP_NAME> --browser
>   ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przejdziesz do następnego kroku, [Dodaj powiązanie danych wyjściowych kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md), Zachowaj wszystkie Twoje zasoby w miarę kompilowania już wykonanej pracy.

W przeciwnym razie użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby, aby uniknąć ponoszenia dalszych kosztów.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end
::: zone pivot="programming-language-java"
```azurecli
az group delete --name java-functions-group
```
::: zone-end
::: zone pivot="programming-language-python"
Aby wyjść z środowiska wirtualnego, uruchom polecenie `deactivate` .
::: zone-end

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md)
 
