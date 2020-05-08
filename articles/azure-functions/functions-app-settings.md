---
title: Dokumentacja ustawień aplikacji dla usługi Azure Functions
description: Dokumentacja referencyjna dla ustawień aplikacji Azure Functions lub zmiennych środowiskowych.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 6f42c411263575040d4392b85542920e8f2463d4
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690759"
---
# <a name="app-settings-reference-for-azure-functions"></a>Dokumentacja ustawień aplikacji dla usługi Azure Functions

Ustawienia aplikacji w aplikacji funkcji zawierają globalne opcje konfiguracji, które mają wpływ na wszystkie funkcje tej aplikacji funkcji. Po uruchomieniu lokalnie te ustawienia są dostępne jako [zmienne środowisk](functions-run-local.md#local-settings-file)lokalnych. W tym artykule wymieniono ustawienia aplikacji, które są dostępne w aplikacjach funkcji.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

W pliku [host. JSON](functions-host-json.md) znajdują się inne opcje konfiguracji globalnej oraz plik [Local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Klucz Instrumentacji dla Application Insights. Użyj tylko jednego z `APPINSIGHTS_INSTRUMENTATIONKEY` lub `APPLICATIONINSIGHTS_CONNECTIONSTRING`. Aby uzyskać więcej informacji, zobacz [Monitor Azure Functions](functions-monitoring.md). 

|Klucz|Wartość przykładowa|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connectionstring"></a>APPLICATIONINSIGHTS_CONNECTIONSTRING

Parametry połączenia dla Application Insights. Użyj `APPLICATIONINSIGHTS_CONNECTIONSTRING` zamiast tego `APPINSIGHTS_INSTRUMENTATIONKEY` , gdy aplikacja funkcji wymaga dodanych dostosowań obsługiwanych przy użyciu parametrów połączenia. Aby uzyskać więcej informacji, zobacz [Parametry połączenia](../azure-monitor/app/sdk-connection-string.md). 

|Klucz|Wartość przykładowa|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTIONSTRING|InstrumentationKey = [Key]; IngestionEndpoint = [URL]; LiveEndpoint = [URL]; ProfilerEndpoint = [URL]; SnapshotEndpoint = [URL];|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

W wersji 2. x i nowszych wersjach środowiska uruchomieniowego funkcji program skonfiguruje zachowanie aplikacji na podstawie środowiska uruchomieniowego. Ta wartość jest [odczytywana podczas inicjalizacji](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Można ustawić `AZURE_FUNCTIONS_ENVIRONMENT` dowolną wartość, ale obsługiwane są [trzy wartości](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) : [programowanie](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [przemieszczanie](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)i [produkcja](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Gdy `AZURE_FUNCTIONS_ENVIRONMENT` nie jest ustawiona, domyślnie `Development` w środowisku lokalnym i `Production` na platformie Azure. To ustawienie powinno być używane zamiast `ASPNETCORE_ENVIRONMENT` ustawiania środowiska uruchomieniowego. 

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

W wersji 2. x i nowszych wersjach środowiska uruchomieniowego funkcji ustawienia aplikacji mogą zastąpić ustawienia pliku [host. JSON](functions-host-json.md) w bieżącym środowisku. Te zastąpienia są wyrażane jako ustawienia aplikacji `AzureFunctionsJobHost__path__to__setting`o nazwie. Aby uzyskać więcej informacji, zobacz [przesłanianie wartości hosta. JSON](functions-host-json.md#override-hostjson-values).

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Opcjonalne parametry połączenia konta magazynu służące do przechowywania dzienników i wyświetlania ich na karcie **monitor** w portalu. To ustawienie jest prawidłowe tylko dla aplikacji przeznaczonych dla wersji 1. x środowiska uruchomieniowego Azure Functions. Konto magazynu musi być kontem ogólnego przeznaczenia, które obsługuje obiekty blob, kolejki i tabele. Aby dowiedzieć się więcej, zobacz [wymagania dotyczące konta magazynu](storage-considerations.md#storage-account-requirements).

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName =<name>; AccountKey =<key>|

> [!NOTE]
> Aby uzyskać lepszą wydajność i doświadczenie, środowisko uruchomieniowe w wersji 2. x i nowszych wersjach korzysta z `AzureWebJobsDashboard`usługi APPINSIGHTS_INSTRUMENTATIONKEY i App Insights do monitorowania, a nie.

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true`oznacza wyłączenie domyślnej strony docelowej wyświetlanej dla głównego adresu URL aplikacji funkcji. Wartość domyślna to `false`.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Gdy to ustawienie aplikacji zostanie pominięte lub ustawione na `false`wartość, zostanie wyświetlona strona podobna do poniższego przykładu w odpowiedzi `<functionappname>.azurewebsites.net`na adres URL.

![Strona docelowa aplikacji funkcji](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true`oznacza użycie trybu wydania podczas kompilowania kodu platformy .NET; `false` oznacza użycie trybu debugowania. Wartość domyślna to `true`.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Rozdzielana przecinkami lista funkcji beta do włączenia. Funkcje beta włączone przez te flagi nie są gotowe do użycia w środowisku produkcyjnym, ale można je włączyć do eksperymentalnego użytku, zanim staną się aktywne.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Określa repozytorium lub dostawcę, który ma być używany na potrzeby magazynu kluczy. Obecnie obsługiwane repozytoria to magazyn obiektów BLOB ("BLOB") i lokalny system plików ("pliki"). Wartością domyślną jest obiekt BLOB w wersji 2 i system plików w wersji 1.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsSecretStorageType|Pliki|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Środowisko uruchomieniowe Azure Functions używa tych parametrów połączenia konta magazynu dla wszystkich funkcji z wyjątkiem funkcji wyzwalanych przez protokół HTTP. Konto magazynu musi być kontem ogólnego przeznaczenia, które obsługuje obiekty blob, kolejki i tabele. Zobacz wymagania dotyczące [konta magazynu](functions-infrastructure-as-code.md#storage-account) i [konta magazynu](storage-considerations.md#storage-account-requirements).

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [klucz]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Ścieżka do kompilatora używanego na potrzeby języka TypeScript. Umożliwia przesłonięcie ustawienia domyślnego, jeśli jest to konieczne.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>tryb\_edycji\_\_aplikacji funkcji

Określa, czy Edycja w Azure Portal jest włączona. Prawidłowe wartości to "ReadWrite" i "ReadOnly".

|Klucz|Wartość przykładowa|
|---|------------|
|tryb\_edycji\_\_aplikacji funkcji|readonly|

## <a name="functions_extension_version"></a>wersja\_rozszerzenia\_Functions

Wersja środowiska uruchomieniowego funkcji, która ma być używana w tej aplikacji funkcji. Tylda z wersją główną oznacza użycie najnowszej wersji tej wersji głównej (na przykład "~ 2"). Gdy dostępne są nowe wersje dla tej samej wersji głównej, są one automatycznie instalowane w aplikacji funkcji. Aby przypiąć aplikację do określonej wersji, użyj pełnego numeru wersji (na przykład "2.0.12345"). Wartość domyślna to "~ 2". Wartość `~1` przypinania aplikacji do wersji 1. x środowiska uruchomieniowego.

|Klucz|Wartość przykładowa|
|---|------------|
|wersja\_rozszerzenia\_Functions|~ 2|

## <a name="functions_v2_compatibility_mode"></a>Tryb\_zgodności\_\_funkcji w wersji 2

To ustawienie umożliwia uruchamianie aplikacji funkcji w trybie zgodności w wersji 2. x w wersji 3. x środowiska uruchomieniowego. Tego ustawienia należy używać tylko w przypadku napotkania problemów podczas [uaktualniania aplikacji funkcji z wersji 2. x do 3. x środowiska uruchomieniowego](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> To ustawienie jest przeznaczone tylko jako krótkoterminowe obejście podczas aktualizowania aplikacji tak, aby działała prawidłowo w wersji 3. x. To ustawienie jest obsługiwane, o ile jest [obsługiwane środowisko uruchomieniowe 2. x](functions-versions.md). Jeśli wystąpią problemy uniemożliwiające uruchomienie aplikacji w wersji 3. x bez użycia tego ustawienia, [Zgłoś problem](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Wymaga ustawienia [wersji\_rozszerzenia\_Functions](functions-app-settings.md#functions_extension_version) na `~3`.

|Klucz|Wartość przykładowa|
|---|------------|
|Tryb\_zgodności\_\_funkcji w wersji 2|true|

## <a name="functions_worker_process_count"></a>liczba\_procesów\_\_roboczych funkcji

Określa maksymalną liczbę procesów roboczych języka z wartością domyślną `1`. Maksymalna dozwolona wartość to `10`. Wywołania funkcji są równomiernie dystrybuowane między procesami roboczymi języka. Procesy robocze języka są duplikowane co 10 sekund do momentu osiągnięcia liczby ustawionej\_przez\_liczbę procesów\_roboczych. Używanie wielu procesów roboczych z wieloma językami nie jest takie samo jak [skalowanie](functions-scale.md). Należy rozważyć użycie tego ustawienia, jeśli obciążenie ma połączenie z wywołaniami związanymi z PROCESORem i we/wy. To ustawienie ma zastosowanie do wszystkich języków non-.NET.

|Klucz|Wartość przykładowa|
|---|------------|
|liczba\_procesów\_\_roboczych funkcji|2|


## <a name="functions_worker_runtime"></a>środowisko\_uruchomieniowe procesu roboczego\_

Środowisko uruchomieniowe procesów roboczych języka do załadowania w aplikacji funkcji.  Będzie to zgodne z językiem używanym w aplikacji (na przykład "dotnet"). W przypadku funkcji w wielu językach należy opublikować je w wielu aplikacjach z odpowiednimi wartościami środowiska wykonawczego procesu roboczego.  Prawidłowe wartości to `dotnet` (C#/f #), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) i `python` (Python).

|Klucz|Wartość przykładowa|
|---|------------|
|środowisko\_uruchomieniowe procesu roboczego\_|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Do użycia tylko w planach Premium &. Parametry połączenia dla konta magazynu, w którym są przechowywane kod i konfiguracja aplikacji funkcji. Zobacz [Tworzenie aplikacji funkcji](functions-infrastructure-as-code.md#create-a-function-app).

|Klucz|Wartość przykładowa|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [klucz]|

## <a name="website_contentshare"></a>CONTENTSHARE\_witryny sieci Web

Do użycia tylko w planach Premium &. Ścieżka pliku do kodu i konfiguracji aplikacji funkcji. Używany z WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Wartość domyślna to unikatowy ciąg, który rozpoczyna się od nazwy aplikacji funkcji. Zobacz [Tworzenie aplikacji funkcji](functions-infrastructure-as-code.md#create-a-function-app).

|Klucz|Wartość przykładowa|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>Maksymalna\_\_SKALOWANIe\_aplikacji\_sieci\_Web w poziomie

Maksymalna liczba wystąpień, do których aplikacja funkcji może skalować w poziomie. Wartość domyślna nie jest ograniczona.

> [!NOTE]
> To ustawienie jest funkcją w wersji zapoznawczej i jest niezawodne tylko wtedy, gdy ustawiono wartość <= 5

|Klucz|Wartość przykładowa|
|---|------------|
|Maksymalna\_\_SKALOWANIe\_aplikacji\_sieci\_Web w poziomie|5|

## <a name="website_node_default_version"></a>DEFAULT_VERSION\_węzła\_witryny sieci Web

_Tylko system Windows._  
Ustawia wersję środowiska Node. js do użycia podczas uruchamiania aplikacji funkcji w systemie Windows. Należy użyć tyldy (~), aby środowisko uruchomieniowe używało najnowszej dostępnej wersji wersji głównej. Na przykład gdy jest ustawiona na `~10`, używana jest Najnowsza wersja środowiska Node. js 10. W przypadku wersji głównej, której celem jest tylda, nie trzeba ręcznie aktualizować wersji pomocniczej. 

|Klucz|Wartość przykładowa|
|---|------------|
|DEFAULT_VERSION\_węzła\_witryny sieci Web|~ 10|

## <a name="website_run_from_package"></a>Witryna\_sieci\_Web\_uruchamiana z pakietu

Umożliwia uruchamianie aplikacji funkcji z zainstalowanego pliku pakietu.

|Klucz|Wartość przykładowa|
|---|------------|
|Witryna\_sieci\_Web\_uruchamiana z pakietu|1|

Prawidłowe wartości to adres URL, który jest rozpoznawany jako lokalizacja pliku pakietu wdrożeniowego lub `1`. Po ustawieniu na `1`, pakiet musi znajdować się w `d:\home\data\SitePackages` folderze. W przypadku korzystania z tego ustawienia podczas wdrażania zip pakiet jest automatycznie przekazywany do tej lokalizacji. W wersji zapoznawczej to ustawienie `WEBSITE_RUN_FROM_ZIP`miało nazwę. Aby uzyskać więcej informacji, zobacz [Uruchamianie funkcji z pliku pakietu](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Domyślnie serwery proxy funkcji będą używać skrótu do wysyłania wywołań interfejsu API z serwerów proxy bezpośrednio do funkcji w tym samym aplikacja funkcji, zamiast tworzyć nowe żądanie HTTP. To ustawienie umożliwia wyłączenie tego zachowania.

|Klucz|Wartość|Opis|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Wywołania z adresem URL zaplecza wskazujące funkcję w lokalnym aplikacja funkcji nie będą już wysyłane bezpośrednio do funkcji i będą kierowane z powrotem do frontonu HTTP dla aplikacja funkcji|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|fałsz|Jest to wartość domyślna. Wywołania z adresem URL zaplecza wskazujące funkcję w lokalnym aplikacja funkcji będą przekazywane bezpośrednio do tej funkcji|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

To ustawienie określa, czy% 2F jest dekodowane jako ukośniki w parametrach trasy, gdy są one wstawiane do adresu URL zaplecza. 

|Klucz|Wartość|Opis|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Parametry trasy z zakodowanymi ukośnikami zostaną zdekodowane. `example.com/api%2ftest`stanie się`example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|fałsz|Jest to zachowanie domyślne. Wszystkie parametry tras zostaną przesłane bez zmian|

### <a name="example"></a>Przykład

Oto przykład proxy. JSON w aplikacji funkcji pod adresem URL myfunction.com

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```
|Dekodowanie adresu URL|Dane wejściowe|Dane wyjściowe|
|-|-|-|
|true|myfunction.com/test%2fapi|example.com/test/api
|fałsz|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak zaktualizować ustawienia aplikacji](functions-how-to-use-azure-function-app-settings.md#settings)

[Zobacz ustawienia globalne w pliku host. JSON](functions-host-json.md)

[Zobacz inne ustawienia aplikacji dla aplikacji App Service](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
