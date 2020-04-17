---
title: Tworzenie funkcji na platformie Azure, która odpowiada na żądania HTTP
description: Dowiedz się, jak utworzyć funkcję z wiersza polecenia, a następnie opublikować projekt lokalny do hostingu bezserwerowego w usłudze Azure Functions.
ms.date: 03/30/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: eb893491310850b141a0d68cb7a74041411843a9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457454"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Szybki start: tworzenie funkcji na platformie Azure, która odpowiada na żądania HTTP

W tym artykule za pomocą narzędzi wiersza polecenia można utworzyć funkcję odpowiadającą na żądania HTTP. Po przetestowaniu kodu lokalnie, można wdrożyć go w środowisku bezserwerowym usługi Azure Functions. Ukończenie tego przewodnika Szybki start wiąże się z niewielkim kosztem kilku centów USD lub mniej na koncie platformy Azure.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Istnieje również wersja tego artykułu [oparta na kodzie programu Visual Studio.](functions-create-first-function-vs-code.md)
::: zone-end  

::: zone pivot="programming-language-java"  
> [!NOTE]
> Jeśli Maven nie jest preferowanym narzędziem do tworzenia, sprawdź nasze podobne tutoriale dla devlopers Java za pomocą [Gradle](/azure/azure-functions/functions-create-first-java-gradle), [IntelliJ IDEA](/azure/java/intellij/azure-toolkit-for-intellij-quickstart-functions) i [VS Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java).
::: zone-end  

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-a-local-function-project"></a>Tworzenie lokalnego projektu funkcji

W usłudze Azure Functions projekt funkcji jest kontenerem dla co najmniej jednej pojedynczej funkcji, która odpowiada na określony wyzwalacz. Wszystkie funkcje w projekcie współużytkują te same konfiguracje lokalne i hostingowe. W tej sekcji utworzysz projekt funkcji, który zawiera jedną funkcję.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Uruchom `func init` polecenie w następujący sposób, aby utworzyć projekt funkcji w folderze o nazwie *LocalFunctionProj* z określonym czasem wykonywania:  
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

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype 
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" 
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" 
```
---

Maven prosi o wartości potrzebne do zakończenia generowania projektu podczas wdrażania.   
Po wyświetleniu monitu podaj następujące wartości:

| Monit | Wartość | Opis |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Wartość, która jednoznacznie identyfikuje projekt we wszystkich projektach, zgodnie z [regułami nazewnictwa pakietów](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) dla języka Java. |
| **artifactId (ida artefaktu)** | `fabrikam-functions` | Wartość, która jest nazwą słoika, bez numeru wersji. |
| **Wersja** | `1.0-SNAPSHOT` | Wybierz wartość domyślną. |
| **Pakiet** | `com.fabrikam.functions` | Wartość, która jest pakietem Java dla wygenerowanego kodu funkcji. Użyj wartości domyślnej. |

Wpisz `Y` lub naciśnij klawisz Enter, aby potwierdzić.

Maven tworzy pliki projektu w nowym folderze o nazwie _artifactId_, który w tym przykładzie jest `fabrikam-functions`. 
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
Ten folder zawiera różne pliki dla projektu, w tym pliki konfiguracji o nazwie [local.settings.json](functions-run-local.md#local-settings-file) i [host.json](functions-host-json.md). Ponieważ *local.settings.json* może zawierać wpisy tajne pobrane z platformy Azure, plik jest domyślnie wykluczony z kontroli źródła w pliku *gitignore.*

[!INCLUDE [functions-cli-add-function](../../includes/functions-cli-add-function.md)]

### <a name="optional-examine-the-file-contents"></a>(Opcjonalnie) Sprawdzanie zawartości pliku

W razie potrzeby można przejść do [uruchom funkcję lokalnie](#run-the-function-locally) i zbadać zawartość pliku później.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* `Run` zawiera metodę, która odbiera dane `req` żądania w zmiennej jest [HttpRequest,](/dotnet/api/microsoft.aspnetcore.http.httprequest) który jest ozdobiony **HttpTriggerAttribute**, który definiuje zachowanie wyzwalacza. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Obiekt zwracany jest [ActionResult,](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) który zwraca komunikat odpowiedzi jako [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) lub [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Aby dowiedzieć się więcej, zobacz [Wyzwalacze i powiązania HTTP usługi Azure Functions HTTP](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-java"
#### <a name="functionjava"></a>Funkcja.java
*Function.java* zawiera `run` metodę, która odbiera `request` dane żądania w zmiennej jest [HttpRequestMessage,](/java/api/com.microsoft.azure.functions.httprequestmessage) który jest ozdobiony [adnotacją HttpTrigger,](/java/api/com.microsoft.azure.functions.annotation.httptrigger) która definiuje zachowanie wyzwalacza. 

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java":::

Komunikat odpowiedzi jest generowany przez [interfejs API HttpResponseMessage.Builder.](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder)

#### <a name="pomxml"></a>pom.xml

Ustawienia zasobów platformy Azure utworzonych w celu hosta aplikacji są definiowane `com.microsoft.azure` w elemencie **konfiguracji** wtyczki z **groupId** w wygenerowanym pliku pom.xml. Na przykład element konfiguracji poniżej nakazuje wdrożenia opartego na Maven, aby utworzyć aplikację funkcji w grupie `java-functions-group` zasobów w `westus` regionie. Sama aplikacja funkcji działa w systemie `java-functions-app-service-plan` Windows hostowane w planie, który domyślnie jest bezserwerowy plan zużycia.    

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="116-155":::

Można zmienić te ustawienia, aby kontrolować sposób tworzenia `runtime.os` zasobów `windows` `linux` na platformie Azure, na przykład zmieniając się z przed początkowym wdrożeniem. Aby uzyskać pełną listę ustawień obsługiwanych przez wtyczkę Maven, zobacz [szczegóły konfiguracji](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).

#### <a name="functiontestjava"></a>FunkcjaTest.java

Archetyp generuje również test jednostkowy dla twojej funkcji. Po zmianie funkcji, aby dodać powiązania lub dodać nowe funkcje do projektu, należy również zmodyfikować testy w pliku *FunctionTest.java.*
::: zone-end  
::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* `main()` zawiera funkcję Języka Python, która jest wyzwalana zgodnie z konfiguracją w *function.json*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

W przypadku wyzwalacza HTTP funkcja odbiera `req` dane żądania w zmiennej zdefiniowanej w *pliku function.json*. `req`jest wystąpieniem [klasy azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Obiekt zwracany, `$return` zdefiniowany jako w *pliku function.json,* jest wystąpieniem [klasy azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Aby dowiedzieć się więcej, zobacz [Wyzwalacze i powiązania HTTP usługi Azure Functions HTTP](/azure/azure-functions/functions-bindings-http-webhook?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js


*index.js* eksportuje funkcję wyzwalaną zgodnie z konfiguracją w *pliku function.json*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

W przypadku wyzwalacza HTTP funkcja odbiera `req` dane żądania w zmiennej zdefiniowanej w *pliku function.json*. Obiekt zwracany, `$return` zdefiniowany jako w *function.json*, jest odpowiedzią. Aby dowiedzieć się więcej, zobacz [Wyzwalacze i powiązania HTTP usługi Azure Functions HTTP](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>indeks.ts

*index.ts* eksportuje funkcję wyzwalaną zgodnie z konfiguracją w *pliku function.json*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

W przypadku wyzwalacza HTTP funkcja odbiera `req` dane żądania w zmiennej typu **HttpRequest** zgodnie z definicją w *pliku function.json*. Obiekt zwracany, `$return` zdefiniowany jako w *function.json*, jest odpowiedzią. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

*run.ps1* definiuje skrypt funkcji, który jest wyzwalany zgodnie z konfiguracją w *pliku function.json*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

W przypadku wyzwalacza HTTP funkcja odbiera `$Request` dane żądania przekazywane do param zdefiniowanego w *pliku function.json*. Obiekt zwracany, `Response` zdefiniowany jako w *pliku function.json,* jest przekazywany do `Push-OutputBinding` polecenia cmdlet jako odpowiedź. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.json* jest plikiem konfiguracyjnym `bindings` definiującym dane wejściowe i wyjściowe dla funkcji, w tym typ wyzwalacza. 
::: zone-end

::: zone pivot="programming-language-python"
Można zmienić, `scriptFile` aby wywołać inny plik Języka Python w razie potrzeby.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Każde powiązanie wymaga kierunku, typu i unikatowej nazwy. Wyzwalacz HTTP ma powiązanie [`httpTrigger`](functions-bindings-http-webhook-trigger.md) wejściowe typu [`http`](functions-bindings-http-webhook-output.md)i wyjściowego typu .
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"    
## <a name="create-supporting-azure-resources-for-your-function"></a>Tworzenie zasobów platformy Azure dla swojej funkcji

Przed wdrożeniem kodu funkcji na platformie Azure należy utworzyć trzy zasoby:

- Grupa zasobów, która jest logicznym kontenerem dla powiązanych zasobów.
- Konto magazynu, które przechowuje stan i inne informacje o projektach.
- Aplikacja funkcji, która zapewnia środowisko do wykonywania kodu funkcji. Aplikacja funkcji mapuje do lokalnego projektu funkcji i umożliwia grupowanie funkcji jako jednostki logicznej w celu łatwiejszego zarządzania, wdrażania i udostępniania zasobów.

Aby utworzyć te elementy, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure. Każde polecenie zapewnia wyjście JSON po zakończeniu.

Jeśli jeszcze tego nie zrobiono, zaloguj się na platformę Azure za pomocą polecenia [logowania az:](/cli/azure/reference-index#az-login)

```azurecli
az login
```
    
Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów o nazwie `AzureFunctionsQuickstart-rg` w `westeurope` regionie. (Zazwyczaj tworzysz grupę zasobów i zasoby w regionie w pobliżu, używając dostępnego regionu z `az account list-locations` polecenia).

```azurecli
az group create --name AzureFunctionsQuickstart-rg --location westeurope
```

> [!NOTE]
> Nie można hostować aplikacji systemu Linux i Windows w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie `AzureFunctionsQuickstart-rg` z aplikacją funkcji systemu Windows lub aplikacją sieci Web, musisz użyć innej grupy zasobów.
 
    
Utwórz ogólne konto magazynu w grupie zasobów i regionie za pomocą [polecenia tworzenia konta magazynu az.](/cli/azure/storage/account#az-storage-account-create) W poniższym przykładzie zastąp `<STORAGE_NAME>` globalnie unikatową nazwą odpowiednią dla Ciebie. Nazwy muszą zawierać tylko od trzech do 24 cyfr i małych liter. `Standard_LRS`określa konto ogólnego przeznaczenia, które jest [obsługiwane przez funkcje](storage-considerations.md#storage-account-requirements).

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
```

Konto magazynu wiąże się tylko z kilkoma centami (USD) za ten szybki start.
    
Utwórz aplikację funkcji za pomocą polecenia [az functionapp create.](/cli/azure/functionapp#az-functionapp-create) W poniższym przykładzie zastąp `<STORAGE_NAME>` nazwą konta użytego w `<APP_NAME>` poprzednim kroku i zastąp globalnie unikatową nazwą odpowiednią dla Użytkownika. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. 
::: zone-end  

::: zone pivot="programming-language-python"  
Jeśli używasz Pythona 3.8, `--functions_version` `3`zmień `--runtime-version` na `3.8` i na .

Jeśli używasz Pythona 3.6, zmień na `--runtime-version` `3.6`.

```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Jeśli używasz node.js 8, `--runtime-version` `8`również zmień na .


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
To polecenie tworzy aplikację funkcji działającą w określonym czasie wykonywania języka w ramach [planu użycia funkcji platformy Azure](functions-scale.md#consumption-plan), która jest bezpłatna dla ilości użycia, które poniesiesz tutaj. Polecenie aplikuje również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą której można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitorowanie funkcji platformy Azure](functions-monitoring.md). Wystąpienie nie ponosi żadnych kosztów, dopóki go nie aktywujesz.
    
## <a name="deploy-the-function-project-to-azure"></a>Wdrażanie projektu funkcji na platformie Azure
::: zone-end  

::: zone pivot="programming-language-typescript"  
Przed użyciem narzędzi Core Tools do wdrożenia projektu na platformie Azure należy utworzyć gotową do produkcji kompilację plików JavaScript z plików źródłowych TypeScript.

Następujące polecenie przygotowuje projekt TypeScript do wdrożenia:

```
npm run build:production 
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
Z niezbędnych zasobów w miejscu, teraz można przystąpić do wdrażania projektu funkcji lokalnych do aplikacji funkcji na platformie Azure przy użyciu [polecenia publikowania func azure functionapp.](functions-run-local.md#project-file-deployment) W poniższym przykładzie zamień `<APP_NAME>` na nazwę aplikacji.

```
func azure functionapp publish <APP_NAME>
```

Jeśli zostanie wyświetlony błąd "Nie można znaleźć aplikacji o nazwie ...", odczekaj kilka sekund i spróbuj `az functionapp create` ponownie, ponieważ platforma Azure może nie zostać w pełni zainicjowana po poprzednim poleceniu.

Polecenie publish pokazuje wyniki podobne do następujących danych wyjściowych (obcięty dla uproszczenia):

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
## <a name="deploy-the-function-project-to-azure"></a>Wdrażanie projektu funkcji na platformie Azure

Aplikacja funkcji i powiązane zasoby są tworzone na platformie Azure podczas pierwszego wdrażania projektu funkcji. Ustawienia zasobów platformy Azure utworzonych w celu hosta aplikacji są definiowane w [pliku pom.xml](#pomxml). W tym artykule zaakceptujesz wartości domyślne.

> [!TIP]
> Aby utworzyć aplikację funkcyjną działającą w `runtime.os` systemie Linux zamiast windows, zmień `windows` `linux`element w pliku pom.xml z na . Uruchamianie systemu Linux w planie zużycia jest obsługiwane w [tych regionach](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions). Nie można mieć aplikacji, które działają w systemie Linux i aplikacji, które działają w systemie Windows w tej samej grupie zasobów.

Przed wdrożeniem należy użyć polecenia interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure [az,](/cli/azure/authenticate-azure-cli) aby zalogować się do subskrypcji platformy Azure. 

```azurecli
az login
```

Użyj następującego polecenia, aby wdrożyć projekt w nowej aplikacji funkcji. 

```
mvn azure-functions:deploy
```

Spowoduje to utworzenie następujących zasobów na platformie Azure:

+ Grupa zasobów. Nazwany jako _java-functions-group_.
+ Konto magazynu. Wymagane przez funkcje. Nazwa jest generowana losowo na podstawie wymagań dotyczących nazwy konta magazynu.
+ Plan hostingowy. Hosting bezserwerowy dla aplikacji funkcji w regionie _Westus._ Nazwa to _java-functions-app-service-plan_.
+ Aplikacja funkcyjna. Aplikacja funkcji jest jednostką wdrażania i wykonywania dla funkcji. Nazwa jest generowana losowo na podstawie twojego _artifactId,_ dołączonego do losowo wygenerowanej liczby. 

Wdrożenie pakiety plików projektu i wdraża je do nowej aplikacji funkcji przy użyciu [wdrożenia zip](functions-deployment-technologies.md#zip-deploy). Kod jest uruchamiany z pakietu wdrażania na platformie Azure.
::: zone-end

## <a name="invoke-the-function-on-azure"></a>Wywoływanie funkcji na platformie Azure

Ponieważ funkcja używa wyzwalacza HTTP, można wywołać go, wysyłając żądanie HTTP do jego adresu URL w przeglądarce lub za pomocą narzędzia, takiego jak curl. W obu przypadkach `code` parametr URL jest unikatowym [kluczem funkcji,](functions-bindings-http-webhook-trigger.md#authorization-keys) który autoryzuje wywołanie punktu końcowego funkcji.

# <a name="browser"></a>[Przeglądarka](#tab/browser)

Skopiuj pełny **adres URL invoke** wyświetlany w danych wyjściowych polecenia `&name=Functions`publikowania na pasku adresu przeglądarki, dołączając parametr kwerendy . Przeglądarka powinna wyświetlać podobne dane wyjściowe, jak po uruchomieniu funkcji lokalnie.

![Dane wyjściowe funkcji uruchamianej na platformie Azure w przeglądarce](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[Curl](#tab/curl)

Uruchom [`curl`](https://curl.haxx.se/) z **adresem URL invoke**, `&name=Functions`dołączając parametr . Dane wyjściowe polecenia powinny być tekst "Hello Functions".

![Dane wyjściowe funkcji uruchamianej na platformie Azure przy użyciu curl](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Aby wyświetlić dzienniki w czasie zbliżonym do rzeczywistego dla opublikowanej aplikacji funkcji, użyj [strumienia metryk na żywo usługi Application Insights.](functions-monitoring.md#streaming-logs)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przejdziesz do następnego kroku, [dodaj powiązanie danych wyjściowych kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md), zachowaj wszystkie zasoby w miejscu, ponieważ będziesz opierać się na tym, co już zrobiłeś.

W przeciwnym razie użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie jej zawarte zasoby, aby uniknąć ponoszenia dalszych kosztów.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Łączenie się z kolejką usługi Azure Storage](functions-add-output-binding-storage-queue-cli.md)
