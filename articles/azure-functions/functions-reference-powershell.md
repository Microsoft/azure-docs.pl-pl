---
title: Dokumentacja dewelopera programu PowerShell dla Azure Functions
description: Dowiedz się, jak opracowywać funkcje przy użyciu programu PowerShell.
author: eamonoreilly
ms.topic: conceptual
ms.custom: devx-track-dotnet, devx-track-azurepowershell
ms.date: 04/22/2019
ms.openlocfilehash: 796aca02e6f70da8f5b94f6bbdbd2fd1d535bd77
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108477"
---
# <a name="azure-functions-powershell-developer-guide"></a>Przewodnik dewelopera programu Azure Functions PowerShell

W tym artykule przedstawiono szczegółowe informacje dotyczące sposobu pisania Azure Functions przy użyciu programu PowerShell.

Funkcja PowerShell platformy Azure (funkcja) jest reprezentowana przez skrypt programu PowerShell, który jest wykonywany po wyzwoleniu. Każdy skrypt funkcji zawiera plik powiązany `function.json` , który definiuje sposób zachowania funkcji, na przykład sposób wyzwalania i parametry wejściowe i wyjściowe. Aby dowiedzieć się więcej, zobacz [artykuł wyzwalacze i powiązania](functions-triggers-bindings.md). 

Podobnie jak w przypadku innych rodzajów funkcji, funkcja skryptu programu PowerShell przyjmuje parametry, które pasują do nazw wszystkich powiązań wejściowych zdefiniowanych w `function.json` pliku. `TriggerMetadata`Parametr jest również przekazywać, który zawiera dodatkowe informacje na temat wyzwalacza, który uruchomił funkcję.

W tym artykule przyjęto założenie, że już odczytano [informacje dotyczące deweloperów Azure Functions](functions-reference.md). Należy również ukończyć funkcję [Szybki Start dla programu PowerShell](./functions-create-first-function-vs-code.md?pivots=programming-language-powershell) , aby utworzyć pierwszą funkcję programu PowerShell.

## <a name="folder-structure"></a>Struktura folderów

Wymagana struktura folderów dla projektu programu PowerShell wygląda następująco. Tę wartość domyślną można zmienić. Aby uzyskać więcej informacji, zobacz sekcję [scriptFile](#configure-function-scriptfile) poniżej.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

W katalogu głównym projektu znajduje się plik udostępniony, za pomocą [`host.json`](functions-host-json.md) którego można skonfigurować aplikację funkcji. Każda funkcja ma folder z własnym plikiem kodu (. ps1) i plikiem konfiguracji powiązania ( `function.json` ). Nazwa function.jsw katalogu nadrzędnym pliku jest zawsze nazwą funkcji.

Niektóre powiązania wymagają obecności `extensions.csproj` pliku. Rozszerzenia powiązań wymagane w [wersji 2. x i nowszych wersjach](functions-versions.md) środowiska uruchomieniowego funkcji są zdefiniowane w `extensions.csproj` pliku z rzeczywistymi plikami biblioteki w `bin` folderze. Podczas programowania lokalnego należy [zarejestrować rozszerzenia powiązań](functions-bindings-register.md#extension-bundles). Podczas tworzenia funkcji w Azure Portal Rejestracja jest wykonywana.

W aplikacjach funkcji programu PowerShell możesz opcjonalnie mieć, `profile.ps1` które są uruchamiane, gdy rozpocznie się uruchamianie aplikacji funkcji (w przeciwnym razie jako *[zimny start](#cold-start)*). Aby uzyskać więcej informacji, zobacz [profil programu PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definiowanie skryptu programu PowerShell jako funkcji

Domyślnie środowisko uruchomieniowe funkcji wyszukuje funkcję w programie `run.ps1` , gdzie jest w niej `run.ps1` udostępniony ten sam katalog nadrzędny `function.json` .

Skrypt przeszedł wiele argumentów podczas wykonywania. Aby obsłużyć te parametry, Dodaj `param` blok na początku skryptu, tak jak w poniższym przykładzie:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata — parametr

Ten `TriggerMetadata` parametr służy do dostarczania dodatkowych informacji na temat wyzwalacza. Dodatkowe metadane różnią się od powiązań do powiązania, ale wszystkie zawierają `sys` Właściwość, która zawiera następujące dane:

```powershell
$TriggerMetadata.sys
```

| Właściwość   | Opis                                     | Typ     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Kiedy, w UTC, funkcja została wyzwolona        | DateTime |
| MethodName | Nazwa funkcji, która została wyzwolona     | ciąg   |
| RandGuid   | unikatowy identyfikator GUID dla tego wykonania funkcji | ciąg   |

Każdy typ wyzwalacza ma inny zestaw metadanych. Na przykład, `$TriggerMetadata` dla `QueueTrigger` zawiera,, `InsertionTime` `Id` `DequeueCount` , między innymi. Aby uzyskać więcej informacji na temat metadanych wyzwalacza kolejki, przejdź do [oficjalnej dokumentacji wyzwalaczy kolejek](functions-bindings-storage-queue-trigger.md#message-metadata). Zapoznaj się z dokumentacją dotyczącą [wyzwalaczy](functions-triggers-bindings.md) , z którymi pracujesz, aby zobaczyć, co znajduje się w metadanych wyzwalacza.

## <a name="bindings"></a>Powiązania

W programie PowerShell [powiązania](functions-triggers-bindings.md) są konfigurowane i definiowane w function.jsfunkcji w programie. Funkcje współdziałają z powiązaniami na wiele sposobów.

### <a name="reading-trigger-and-input-data"></a>Odczytywanie wyzwalacza i danych wejściowych

Wyzwalacze i powiązania wejściowe są odczytywane jako parametry przesłane do funkcji. Powiązania wejściowe mają `direction` ustawioną wartość `in` w function.jsna. `name`Właściwość zdefiniowana w `function.json` jest nazwą parametru w `param` bloku. Ponieważ program PowerShell używa nazwanych parametrów do powiązania, kolejność parametrów nie ma znaczenia. Jednak najlepszym rozwiązaniem jest przestrzeganie kolejności powiązań zdefiniowanych w `function.json` .

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Zapisywanie danych wyjściowych

W funkcjach powiązanie danych wyjściowych ma `direction` ustawiony na wartość `out` w function.jsna. Możesz zapisywać do powiązania danych wyjściowych za pomocą `Push-OutputBinding` polecenia cmdlet, które jest dostępne dla środowiska uruchomieniowego usługi Functions. We wszystkich przypadkach `name` właściwość powiązania, zgodnie z definicją w, `function.json` odpowiada `Name` parametrowi `Push-OutputBinding` polecenia cmdlet.

Poniżej pokazano, jak wywołać `Push-OutputBinding` w skrypcie funkcji:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Możesz również przekazać wartość dla określonego powiązania za pomocą potoku.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` zachowuje się inaczej w zależności od wartości określonej dla `-Name` :

* Gdy nie można rozpoznać określonej nazwy jako prawidłowego powiązania danych wyjściowych, zostanie zgłoszony błąd.

* Gdy powiązanie danych wyjściowych akceptuje zbiór wartości, można wielokrotnie wywoływać `Push-OutputBinding` wiele wartości.

* Gdy powiązanie danych wyjściowych akceptuje tylko wartość singleton, wywołanie `Push-OutputBinding` drugiego czasu powoduje wystąpienie błędu.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` obowiązuje

Poniżej podano prawidłowe parametry wywołania `Push-OutputBinding` :

| Nazwa | Typ | Położenie | Opis |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Ciąg | 1 | Nazwa powiązania danych wyjściowych, które chcesz ustawić. |
| **`-Value`** | Obiekt | 2 | Wartość powiązania danych wyjściowych, które ma zostać ustawione, które jest akceptowane z potoku ByValue. |
| **`-Clobber`** | SwitchParameter | Nazywany | Obowiązkowe Gdy jest określony, wymusza wartość ustawioną dla określonego powiązania danych wyjściowych. | 

Obsługiwane są również następujące typowe parametry: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Aby uzyskać więcej informacji, zobacz [Informacje o parametry](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

#### <a name="push-outputbinding-example-http-responses"></a>Przykład Push-OutputBinding: odpowiedzi HTTP

Wyzwalacz HTTP zwraca odpowiedź przy użyciu powiązania wyjściowego o nazwie `response` . W poniższym przykładzie powiązanie danych wyjściowych `response` ma wartość "output #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Ponieważ dane wyjściowe to HTTP, który akceptuje tylko pojedynczą wartość, zwracany jest błąd, gdy `Push-OutputBinding` jest wywoływana po raz drugi.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

W przypadku danych wyjściowych, które akceptują tylko pojedyncze wartości, można użyć `-Clobber` parametru, aby zastąpić starą wartość zamiast próbować dodać do kolekcji. W poniższym przykładzie przyjęto założenie, że została już dodana wartość. Przy użyciu `-Clobber` , odpowiedź z następującego przykładu zastępuje istniejącą wartość, aby zwrócić wartość "output #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Przykład Push-OutputBinding: powiązanie danych wyjściowych kolejki

`Push-OutputBinding` służy do wysyłania danych do powiązań wyjściowych, takich jak [powiązanie danych wyjściowych usługi Azure queue storage](functions-bindings-storage-queue-output.md). W poniższym przykładzie komunikat zapisany w kolejce ma wartość "Output #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

Powiązanie danych wyjściowych dla kolejki magazynu akceptuje wiele wartości wyjściowych. W takim przypadku należy wywołać Poniższy przykład po pierwszym zapisie w kolejce listy z dwoma elementami: "Output #1" i "Output #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

Poniższy przykład, gdy wywoływany po poprzednich dwóch, dodaje do kolekcji wyjściowej dwie więcej wartości:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Po zapisaniu w kolejce komunikat zawiera te cztery wartości: "Output #1", "Output #2", "Output #3" i "Output #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` parametr

Za pomocą `Get-OutputBinding` polecenia cmdlet można pobrać wartości aktualnie ustawionych dla powiązań wyjściowych. To polecenie cmdlet pobiera tablicę skrótów, która zawiera nazwy powiązań wyjściowych z odpowiednimi wartościami. 

Oto przykład użycia `Get-OutputBinding` do zwracania bieżących wartości powiązania:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` zawiera również parametr o nazwie `-Name` , który może służyć do filtrowania zwracanego powiązania, jak w poniższym przykładzie:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Symbole wieloznaczne (*) są obsługiwane w programie `Get-OutputBinding` .

## <a name="logging"></a>Rejestrowanie

Logowanie w funkcjach programu PowerShell działa jak regularne rejestrowanie programu PowerShell. Polecenia cmdlet rejestrowania można użyć do zapisu w każdym strumieniu wyjściowym. Każde polecenie cmdlet mapuje na poziom rejestrowania używany przez funkcje.

| Poziom rejestrowania funkcji | Polecenie cmdlet rejestrowania |
| ------------- | -------------- |
| Błąd | **`Write-Error`** |
| Ostrzeżenie | **`Write-Warning`**  | 
| Informacyjny | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informacyjny | Zapisuje dane w dzienniku na poziomie _informacji_ . |
| Debugowanie | **`Write-Debug`** |
| Ślad | **`Write-Progress`** <br /> **`Write-Verbose`** |

Oprócz tych poleceń cmdlet wszystkie elementy zapisywane w potoku są przekierowywane do `Information` poziomu dziennika i wyświetlane z domyślnym formatowaniem programu PowerShell.

> [!IMPORTANT]
> Korzystanie z `Write-Verbose` `Write-Debug` poleceń cmdlet lub nie wystarcza do wyświetlenia pełnych informacji i rejestrowania na poziomie debugowania. Należy również skonfigurować próg poziomu dziennika, który deklaruje żądany poziom dzienników. Aby dowiedzieć się więcej, zobacz [Konfigurowanie poziomu dziennika aplikacji funkcji](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Konfigurowanie poziomu dziennika aplikacji funkcji

Azure Functions umożliwia zdefiniowanie poziomu progu, aby ułatwić kontrolowanie sposobu zapisu w dziennikach. Aby ustawić wartość progową dla wszystkich śladów, które są zapisywane w konsoli programu, należy użyć `logging.logLevel.default` właściwości w [ `host.json` pliku] [host.json Reference]. To ustawienie ma zastosowanie do wszystkich funkcji w aplikacji funkcji.

Poniższy przykład ustawia próg w celu włączenia pełnego rejestrowania dla wszystkich funkcji, ale ustawia próg włączania rejestrowania debugowania dla funkcji o nazwie `MyFunction` :

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

Aby uzyskać więcej informacji, zobacz [host.json Reference].

### <a name="viewing-the-logs"></a>Wyświetlanie dzienników

Jeśli aplikacja funkcji działa na platformie Azure, można użyć Application Insights do monitorowania. Przeczytaj [Azure Functions monitorowania](functions-monitoring.md) , aby dowiedzieć się więcej o przeglądaniu i wysyłaniu zapytań o dzienniki funkcji.

W przypadku korzystania z aplikacja funkcji lokalnie na potrzeby programowania program domyślnie rejestruje w systemie plików. Aby wyświetlić dzienniki w konsoli programu, należy ustawić `AZURE_FUNCTIONS_ENVIRONMENT` zmienną środowiskową na `Development` przed rozpoczęciem aplikacja funkcji.

## <a name="triggers-and-bindings-types"></a>Wyzwalacze i typy powiązań

Istnieje wiele wyzwalaczy i powiązań dostępnych do użycia z aplikacją funkcji. Pełną listę wyzwalaczy i powiązań [można znaleźć tutaj](functions-triggers-bindings.md#supported-bindings).

Wszystkie wyzwalacze i powiązania są reprezentowane w kodzie jako kilka rzeczywistych typów danych:

* Elemencie
* ciąg
* Byte []
* int
* double
* HttpRequestContext
* HttpResponseContext

Pierwsze pięć typów na tej liście są standardowymi typami .NET. Ostatnie dwa są używane tylko przez [wyzwalacz HttpTrigger](#http-triggers-and-bindings).

Każdy parametr powiązania w funkcjach musi być jednym z tych typów.

### <a name="http-triggers-and-bindings"></a>Wyzwalacze i powiązania HTTP

Wyzwalacze HTTP i webhook oraz powiązania wyjściowe HTTP używają obiektów Request i Response do reprezentowania komunikatów HTTP.

#### <a name="request-object"></a>Obiekt żądania

Obiekt żądania, który jest przesyłany do skryptu, jest typu `HttpRequestContext` , który ma następujące właściwości:

| Właściwość  | Opis                                                    | Typ                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Obiekt, który zawiera treść żądania. `Body` jest serializowany do najlepszego typu na podstawie danych. Na przykład, jeśli dane są w formacie JSON, są one przenoszone jako tablica skrótów. Jeśli dane są ciągami, są one przenoszone jako ciąg. | object |
| **`Headers`** | Słownik zawierający nagłówki żądania.                | Ciąg<słownika, ciąg><sup>*</sup> |
| **`Method`** | Metoda HTTP żądania.                                | ciąg                    |
| **`Params`**  | Obiekt, który zawiera parametry routingu żądania. | Ciąg<słownika, ciąg><sup>*</sup> |
| **`Query`** | Obiekt, który zawiera parametry zapytania.                  | Ciąg<słownika, ciąg><sup>*</sup> |
| **`Url`** | Adres URL żądania.                                        | ciąg                    |

<sup>*</sup> We wszystkich `Dictionary<string,string>` kluczach nie jest rozróżniana wielkość liter.

#### <a name="response-object"></a>Obiekt odpowiedzi

Obiekt odpowiedzi, który ma zostać wysłany z tyłu, ma typ `HttpResponseContext` , który ma następujące właściwości:

| Właściwość      | Opis                                                 | Typ                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Obiekt, który zawiera treść odpowiedzi.           | object                    |
| **`ContentType`** | Krótkie ustawienie typu zawartości odpowiedzi. | ciąg                    |
| **`Headers`** | Obiekt, który zawiera nagłówki odpowiedzi.               | Słownik lub Hashtable   |
| **`StatusCode`**  | Kod stanu HTTP odpowiedzi.                       | ciąg lub int             |

#### <a name="accessing-the-request-and-response"></a>Uzyskiwanie dostępu do żądania i odpowiedzi

Podczas pracy z wyzwalaczami HTTP możesz uzyskać dostęp do żądania HTTP w taki sam sposób, jak w przypadku dowolnego innego powiązania danych wejściowych. Znajduje się w `param` bloku.

Użyj `HttpResponseContext` obiektu, aby zwrócić odpowiedź, jak pokazano na poniższej liście:

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

Wynikiem wywołania tej funkcji będzie:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Rzutowanie typu dla wyzwalaczy i powiązań

W przypadku niektórych powiązań, takich jak powiązanie obiektu BLOB, można określić typ parametru.

Na przykład, aby dane z magazynu obiektów BLOB były dostarczane jako ciąg, Dodaj następujące rzutowanie typu do mojego `param` bloku:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Profil programu PowerShell

W programie PowerShell istnieje koncepcja profilu programu PowerShell. Jeśli nie znasz profilów programu PowerShell, zobacz [Informacje o profilach](/powershell/module/microsoft.powershell.core/about/about_profiles).

W funkcjach programu PowerShell skrypt profilu jest wykonywany raz dla każdego wystąpienia procesu roboczego programu PowerShell w aplikacji podczas pierwszego wdrożenia i po zakończeniu bezczynności ([zimny start](#cold-start)). Po włączeniu współbieżności przez ustawienie wartości [PSWorkerInProcConcurrencyUpperBound](#concurrency) skrypt profilu jest uruchamiany dla każdego utworzonego obszaru działania.

Po utworzeniu aplikacji funkcji przy użyciu narzędzi, takich jak Visual Studio Code i Azure Functions Core Tools, zostanie utworzona wartość domyślna `profile.ps1` . Profil domyślny jest przechowywany [w repozytorium usługi podstawowe narzędzia usługi GitHub](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) i zawiera następujące funkcje:

* Automatyczne uwierzytelnianie MSI na platformie Azure.
* Możliwość włączenia `AzureRM` aliasów programu Azure PowerShell PowerShell, jeśli chcesz.

## <a name="powershell-versions"></a>Wersje programu PowerShell

W poniższej tabeli przedstawiono wersje programu PowerShell dostępne dla każdej wersji głównej środowiska uruchomieniowego funkcji, a wymagana wersja platformy .NET:

| Wersja funkcji | Wersja programu PowerShell                               | Wersja platformy .NET  | 
|-------------------|--------------------------------------------------|---------------|
| 3. x (zalecane) | PowerShell 7 (zalecane)<br/>Program PowerShell Core 6 | .NET Core 3,1<br/>.NET Core 2.1 |
| 2.x               | Program PowerShell Core 6                                | .NET Core 2.2 |

Bieżącą wersję można zobaczyć, drukując ją `$PSVersionTable` z dowolnej funkcji.

### <a name="running-local-on-a-specific-version"></a>Uruchamianie lokalne w określonej wersji

W przypadku uruchamiania lokalnego Azure Functions ustawień domyślnych środowiska uruchomieniowego przy użyciu programu PowerShell Core 6. Aby zamiast tego używać programu PowerShell 7 w przypadku uruchamiania lokalnego, należy dodać ustawienie `"FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"` do `Values` tablicy w local.setting.jsw pliku w katalogu głównym projektu. W przypadku uruchamiania lokalnego w programie PowerShell 7 local.settings.jsna pliku wygląda podobnie do poniższego przykładu: 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

### <a name="changing-the-powershell-version"></a>Zmiana wersji programu PowerShell

Aplikacja funkcji musi być uruchomiona w wersji 3. x, aby można było uaktualnić program PowerShell Core 6 do programu PowerShell 7. Aby dowiedzieć się, jak to zrobić, zobacz [Wyświetlanie i aktualizowanie bieżącej wersji środowiska uruchomieniowego](set-runtime-version.md#view-and-update-the-current-runtime-version).

Aby zmienić wersję programu PowerShell używaną przez aplikację funkcji, należy wykonać poniższe czynności. Można to zrobić w Azure Portal lub przy użyciu programu PowerShell.

# <a name="portal"></a>[Portal](#tab/portal)

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do swojej aplikacji funkcji.

1. W obszarze **Ustawienia**wybierz pozycję **Konfiguracja**. Na karcie **Ustawienia ogólne** Znajdź **wersję programu PowerShell**. 

    :::image type="content" source="media/functions-reference-powershell/change-powershell-version-portal.png" alt-text="Wybieranie wersji programu PowerShell używanej przez aplikację funkcji"::: 

1. Wybierz żądaną **wersję rdzenia programu PowerShell** i wybierz pozycję **Zapisz**. Po wyświetleniu ostrzeżenia o oczekującym ponownym uruchomieniu wybierz pozycję **Kontynuuj**. Aplikacja funkcji jest uruchamiana ponownie w wybranej wersji programu PowerShell. 

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Uruchom następujący skrypt, aby zmienić wersję programu PowerShell: 

```powershell
Set-AzResource -ResourceId "/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<FUNCTION_APP>/config/web" -Properties @{  powerShellVersion  = '<VERSION>' } -Force -UsePatchSemantics

```

Zastąp wartości `<SUBSCRIPTION_ID>` , `<RESOURCE_GROUP>` , i `<FUNCTION_APP>` identyfikatorem subskrypcji platformy Azure, odpowiednio nazwą grupy zasobów i aplikacji funkcji.  Ponadto Zastąp ciąg albo `<VERSION>` `~6` lub `~7` . Możesz sprawdzić zaktualizowaną wartość `powerShellVersion` Ustawienia w `Properties` tabeli zwracanych skrótów. 

---

Aplikacja funkcji zostanie ponownie uruchomiona po wprowadzeniu zmian w konfiguracji.

## <a name="dependency-management"></a>Zarządzanie zależnościami

Funkcje umożliwiają używanie [Galerii programu PowerShell](https://www.powershellgallery.com) do zarządzania zależnościami. W przypadku włączenia zarządzania zależnością plik requirements.psd1 jest używany do automatycznego pobierania wymaganych modułów. To zachowanie jest włączane przez ustawienie `managedDependency` właściwości na `true` wartość w katalogu głównym [host.jsw pliku](functions-host-json.md), jak w poniższym przykładzie:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

W przypadku tworzenia nowego projektu funkcji programu PowerShell zarządzanie zależnościami jest domyślnie włączone, w tym [ `Az` module](/powershell/azure/new-azureps-module-az) platformy Azure. Maksymalna obsługiwana liczba modułów to 10. Obsługiwana składnia to _`MajorNumber`_ `.*` lub dokładna wersja modułu, jak pokazano na poniższym requirements.psd1 przykład:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Po zaktualizowaniu pliku requirements.psd1 zaktualizowane moduły zostaną zainstalowane po ponownym uruchomieniu.

> [!NOTE]
> Zarządzane zależności wymagają dostępu do www.powershellgallery.com do pobierania modułów. W przypadku uruchamiania lokalnego upewnij się, że środowisko uruchomieniowe ma dostęp do tego adresu URL, dodając wszystkie wymagane reguły zapory.

> [!NOTE]
> Zarządzane zależności obecnie nie obsługują modułów, które wymagają od użytkownika zaakceptowania licencji, przez zaakceptowanie licencji w trybie interaktywnym lub przez udostępnienie `-AcceptLicense` przełącznika podczas wywoływania `Install-Module` .

Następujące ustawienia aplikacji mogą służyć do zmiany sposobu pobierania i instalowania zarządzanych zależności. Uaktualnienie aplikacji rozpocznie się w programie `MDMaxBackgroundUpgradePeriod` , a proces uaktualniania zakończy się w przybliżeniu `MDNewSnapshotCheckPeriod` .

| Ustawienie aplikacja funkcji              | Wartość domyślna             | Opis                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00` (7 dni)     | Każdy proces roboczy programu PowerShell inicjuje sprawdzanie uaktualnień modułu w Galeria programu PowerShell na początku procesu i co jakiś dzień `MDMaxBackgroundUpgradePeriod` . Gdy w Galeria programu PowerShell jest dostępna nowa wersja modułu, zostanie ona zainstalowana w systemie plików i udostępniona dla procesów roboczych programu PowerShell. Zmniejszenie tej wartości pozwala aplikacji funkcji uzyskiwać nowsze wersje modułów wcześniej, ale również zwiększa użycie zasobów aplikacji (we/wy sieci, procesor CPU, magazyn). Zwiększenie tej wartości zmniejsza użycie zasobów aplikacji, ale może również opóźnić dostarczenie nowej wersji modułu do aplikacji. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00` (1 godzina)       | Po zainstalowaniu nowych wersji modułu w systemie plików należy ponownie uruchomić każdy proces roboczy programu PowerShell. Ponowne uruchomienie programu PowerShell wpływa na dostępność aplikacji, ponieważ może przerwać bieżące wykonywanie funkcji. Do momentu ponownego uruchomienia wszystkich procesów roboczych programu PowerShell wywołania funkcji mogą używać starych lub nowych wersji modułu. Ponowne uruchamianie wszystkich procesów roboczych programu PowerShell kończy się w programie `MDNewSnapshotCheckPeriod` . Zwiększenie tej wartości zmniejsza częstotliwość przerw, ale może również wydłużyć czas, w którym wywołania funkcji używają starej lub nowej wersji modułu w sposób Niedeterministyczny. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00` (1 dzień)     | Aby uniknąć nadmiernych uaktualnień modułów podczas częstego ponownego uruchamiania procesów roboczych, sprawdzanie uaktualnień modułów nie jest wykonywane, gdy jakikolwiek proces roboczy został już zainicjowany `MDMinBackgroundUpgradePeriod` . |

Korzystanie z własnych modułów niestandardowych jest nieco inne niż w normalny sposób.

Na komputerze lokalnym moduł jest instalowany w jednym z dostępnych globalnie folderów w usłudze `$env:PSModulePath` . W przypadku uruchamiania na platformie Azure nie masz dostępu do modułów zainstalowanych na maszynie. Oznacza to, że `$env:PSModulePath` aplikacja funkcji programu PowerShell różni się od `$env:PSModulePath` zwykłego skryptu programu PowerShell.

W funkcjach `PSModulePath` zawiera dwie ścieżki:

* `Modules`Folder, który istnieje w katalogu głównym aplikacji funkcji.
* Ścieżka do `Modules` folderu, który jest kontrolowany przez proces roboczy języka programu PowerShell.


### <a name="function-app-level-modules-folder"></a>Folder poziomu aplikacji funkcji `Modules`

Aby korzystać z modułów niestandardowych, można umieścić moduły, na których funkcje są zależne od `Modules` folderu. Z tego folderu moduły są automatycznie dostępne dla środowiska uruchomieniowego usługi Functions. Każda funkcja w aplikacji funkcji może korzystać z tych modułów. 

> [!NOTE]
> Moduły określone w pliku requirements.psd1 są automatycznie pobierane i uwzględniane w ścieżce, więc nie trzeba ich dołączać do folderu modules. Są one przechowywane lokalnie w `$env:LOCALAPPDATA/AzureFunctions` folderze i w folderze w `/data/ManagedDependencies` przypadku uruchamiania w chmurze.

Aby skorzystać z funkcji niestandardowego modułu, Utwórz `Modules` folder w katalogu głównym aplikacji funkcji. Skopiuj moduły, których chcesz używać w swoich funkcjach, do tej lokalizacji.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

W przypadku `Modules` folderu aplikacja funkcji powinna mieć następującą strukturę folderów:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

Po uruchomieniu aplikacji funkcji proces roboczy języka PowerShell dodaje ten `Modules` folder do programu, `$env:PSModulePath` dzięki czemu można polegać na automatyczne ładowanie modułu, tak jak w przypadku zwykłego skryptu programu PowerShell.

### <a name="language-worker-level-modules-folder"></a>Folder poziomu procesów roboczych języka `Modules`

Proces roboczy języka programu PowerShell często korzysta z kilku modułów. Te moduły są zdefiniowane w ostatniej pozycji `PSModulePath` . 

Bieżąca lista modułów jest następująca:

* [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): moduł używany do pracy z archiwami, takimi jak `.zip` , `.nupkg` i innymi.
* **ThreadJob**: implementacja oparta na wątkach interfejsów API zadań programu PowerShell.

Domyślnie funkcje używają najnowszej wersji tych modułów. Aby użyć określonej wersji modułu, należy umieścić tę określoną wersję w `Modules` folderze aplikacji funkcji.

## <a name="environment-variables"></a>Zmienne środowiskowe

W funkcjach, [Ustawienia aplikacji](functions-app-settings.md), takie jak parametry połączenia usługi, są ujawniane jako zmienne środowiskowe podczas wykonywania. Możesz uzyskać dostęp do tych ustawień przy użyciu `$env:NAME_OF_ENV_VAR` , jak pokazano w następującym przykładzie:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

W przypadku uruchamiania lokalnego ustawienia aplikacji są odczytywane z [local.settings.jsw](functions-run-local.md#local-settings-file) pliku projektu.

## <a name="concurrency"></a>Współbieżność

Domyślnie środowisko uruchomieniowe programu PowerShell może przetwarzać tylko jedno wywołanie funkcji w danym momencie. Jednak ten poziom współbieżności może być niewystarczający w następujących sytuacjach:

* Gdy próbujesz obsłużyć dużą liczbę wywołań w tym samym czasie.
* Gdy masz funkcje, które wywołują inne funkcje w tej samej aplikacji funkcji.

Istnieje kilka modeli współbieżności, które można poznać w zależności od typu obciążenia:

* Zwiększ ```FUNCTIONS_WORKER_PROCESS_COUNT``` . Pozwala to obsługiwać wywołania funkcji w wielu procesach w tym samym wystąpieniu, co wprowadza pewne obciążenie procesora i pamięci. Ogólnie rzecz biorąc, funkcje powiązane we/wy nie pogorszą się od tego obciążenia. W przypadku funkcji powiązanych z PROCESORem wpływ może być znaczny.

* Zwiększ ```PSWorkerInProcConcurrencyUpperBound``` wartość ustawienia aplikacji. Pozwala to na tworzenie wielu obszarami działania w ramach tego samego procesu, co znacznie zmniejsza obciążenie procesora i pamięci.

Te zmienne środowiskowe są ustawiane w [ustawieniach aplikacji](functions-app-settings.md) aplikacji funkcji.

W zależności od przypadków użycia Durable Functions może znacząco poprawić skalowalność. Aby dowiedzieć się więcej, zobacz [Durable Functions wzorców aplikacji](./durable/durable-functions-overview.md?tabs=powershell#application-patterns).

>[!NOTE]
> Może zostać wyświetlony komunikat "żądania są umieszczane w kolejce ze względu na brak dostępnych obszarami działania", pamiętaj, że to nie jest błąd. Komunikat informuje o tym, że żądania są umieszczane w kolejce i będą obsługiwane po zakończeniu poprzednich żądań.

### <a name="considerations-for-using-concurrency"></a>Zagadnienia dotyczące korzystania z współbieżności

Domyślnie program PowerShell jest _wielowątkowym_ językiem skryptowym. Współbieżność można jednak dodać przy użyciu wielu obszarami działania programu PowerShell w tym samym procesie. Ilość utworzonych obszarami działania będzie zgodna z ```PSWorkerInProcConcurrencyUpperBound``` ustawieniem aplikacji. Przepustowość będzie miała wpływ na ilość dostępnego procesora i pamięci w wybranym planie.

Azure PowerShell używa niektórych kontekstów i Stanów na _poziomie procesu_ , aby pomóc zaoszczędzić przed nadmiarowym wpisywaniem. Jeśli jednak włączysz współbieżność w aplikacji funkcji i wywołująsz akcje, które zmieniają stan, możesz zakończyć z użyciem warunków wyścigu. Te sytuacje wyścigu są trudne do debugowania, ponieważ jedno wywołanie jest zależne od pewnego stanu, a inne wywołanie zmieniło stan.

Ogromną wartość współbieżności z Azure PowerShell, ponieważ niektóre operacje mogą zająć dużo czasu. Należy jednak zachować ostrożność. Jeśli podejrzewasz, że masz sytuację wyścigu, ustaw ustawienie aplikacji PSWorkerInProcConcurrencyUpperBound na, `1` a zamiast tego użyj [izolacji poziomu procesu roboczego języka](functions-app-settings.md#functions_worker_process_count) dla współbieżności.

## <a name="configure-function-scriptfile"></a>Konfiguruj funkcję `scriptFile`

Domyślnie funkcja programu PowerShell jest wykonywana z `run.ps1` , plik, który współużytkuje ten sam katalog nadrzędny `function.json` .

`scriptFile`Właściwość w w programie `function.json` może służyć do uzyskania struktury folderów, która wygląda podobnie do poniższego przykładu:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

W takim przypadku w `function.json` przypadku `myFunction` zawiera `scriptFile` Właściwość odwołującą się do pliku z wyeksportowaną funkcją do uruchomienia.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Używanie modułów programu PowerShell przez skonfigurowanie punktu wejścia

W tym artykule przedstawiono funkcje programu PowerShell w domyślnym `run.ps1` pliku skryptu generowanym przez szablony.
Można jednak również dołączać funkcje w modułach programu PowerShell. Można odwołać się do określonego kodu funkcji w module przy użyciu `scriptFile` pól i `entryPoint` w function.jsw pliku konfiguracyjnym.

W tym przypadku `entryPoint` jest nazwą funkcji lub polecenia cmdlet w module programu PowerShell, do którego odwołuje się `scriptFile` .

Weź pod uwagę następującą strukturę folderów:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Gdzie `PSFunction.psm1` zawiera:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

W tym przykładzie konfiguracja programu `myFunction` zawiera `scriptFile` Właściwość, która odwołuje się do `PSFunction.psm1` , czyli modułu programu PowerShell w innym folderze.  `entryPoint`Właściwość odwołuje się do `Invoke-PSTestFunc` funkcji, która jest punktem wejścia w module.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

W przypadku tej konfiguracji to polecenie jest `Invoke-PSTestFunc` wykonywane dokładnie tak jak w przypadku `run.ps1` .

## <a name="considerations-for-powershell-functions"></a>Zagadnienia dotyczące funkcji programu PowerShell

Podczas pracy z funkcjami programu PowerShell należy pamiętać o zagadnieniach w poniższych sekcjach.

### <a name="cold-start"></a>Zimne uruchomienie

Podczas opracowywania Azure Functions w [modelu hostingu bezserwerowego](functions-scale.md#consumption-plan)zimny start jest rzeczywistości. *Zimny start* odnosi się do okresu czasu potrzebnego na uruchomienie aplikacji funkcji w celu przetworzenia żądania. Zimny przebieg występuje częściej od planu zużycia, ponieważ aplikacja funkcji jest zamykana w okresach braku aktywności.

### <a name="bundle-modules-instead-of-using-install-module"></a>Moduły pakietu zamiast używania `Install-Module`

Skrypt jest uruchamiany na każdym wywołaniu. Unikaj używania `Install-Module` w skrypcie. Zamiast tego należy użyć `Save-Module` przed opublikowaniem, aby funkcja nie mogła tracić czasu na pobranie modułu. Jeśli zimne uruchomienie ma wpływ na funkcje, warto rozważyć wdrożenie aplikacji funkcji w planie [App servicem](functions-scale.md#app-service-plan) ustawionym na *zawsze włączony* lub do [planu Premium](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Następne kroki

Więcej informacji można znaleźć w następujących zasobach:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md)

[Dokumentacja pliku host.json]: functions-host-json.md