---
title: Dokumentacja ustawień aplikacji dla usługi Azure Functions
description: Dokumentacja referencyjna dla ustawień aplikacji Azure Functions lub zmiennych środowiskowych.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 327f120d387a3a08f0de9db2da718d530346e545
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773083"
---
# <a name="app-settings-reference-for-azure-functions"></a>Dokumentacja ustawień aplikacji dla usługi Azure Functions

Ustawienia aplikacji w aplikacji funkcji zawierają globalne opcje konfiguracji, które mają wpływ na wszystkie funkcje tej aplikacji funkcji. Po uruchomieniu lokalnie te ustawienia są dostępne jako [zmienne środowisk](functions-run-local.md#local-settings-file)lokalnych. W tym artykule wymieniono ustawienia aplikacji, które są dostępne w aplikacjach funkcji.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Istnieją inne opcje konfiguracji globalnej w [host.js](functions-host-json.md) plik i w [local.settings.js](functions-run-local.md#local-settings-file) pliku.

> [!NOTE]  
> Ustawienia aplikacji umożliwiają przesłonięcie host.jsprzy ustawianiu wartości bez konieczności zmiany host.jssamego pliku. Jest to przydatne w scenariuszach, w których trzeba skonfigurować lub zmodyfikować określone host.jsw ustawieniach dla określonego środowiska. Pozwala to również na zmianę host.jsw ustawieniach bez konieczności ponownego publikowania projektu. Aby dowiedzieć się więcej, zobacz [ artykułhost.json Reference](functions-host-json.md#override-hostjson-values). Zmiany ustawień aplikacji funkcji wymagają ponownego uruchomienia aplikacji funkcji.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Klucz Instrumentacji dla Application Insights. Użyj tylko jednego z `APPINSIGHTS_INSTRUMENTATIONKEY` lub `APPLICATIONINSIGHTS_CONNECTION_STRING` . Gdy Application Insights działa w chmurze suwerennej, użyj `APPLICATIONINSIGHTS_CONNECTION_STRING` . Aby uzyskać więcej informacji, zobacz [jak skonfigurować monitorowanie dla Azure Functions](configure-monitoring.md). 

|Klucz|Wartość przykładowa|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

Parametry połączenia dla Application Insights. Użyj `APPLICATIONINSIGHTS_CONNECTION_STRING` zamiast tego `APPINSIGHTS_INSTRUMENTATIONKEY` w następujących przypadkach:

+ Gdy aplikacja funkcji wymaga dodanych dostosowań obsługiwanych przy użyciu parametrów połączenia. 
+ Gdy wystąpienie Application Insights działa w chmurze suwerennej, która wymaga niestandardowego punktu końcowego.

Aby uzyskać więcej informacji, zobacz [Parametry połączenia](../azure-monitor/app/sdk-connection-string.md). 

|Klucz|Wartość przykładowa|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|InstrumentationKey = [Key]; IngestionEndpoint = [URL]; LiveEndpoint = [URL]; ProfilerEndpoint = [URL]; SnapshotEndpoint = [URL];|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Domyślnie [serwery proxy usługi Functions](functions-proxies.md) używają skrótu do wysyłania wywołań interfejsu API z serwerów proxy bezpośrednio do funkcji w tej samej aplikacji funkcji. Ten skrót jest używany zamiast tworzenia nowego żądania HTTP. To ustawienie umożliwia wyłączenie tego zachowania skrótu.

|Klucz|Wartość|Opis|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Wywołania z adresem URL zaplecza wskazujące funkcję w lokalnej aplikacji funkcji nie będą wysyłane bezpośrednio do funkcji. Zamiast tego żądania są kierowane z powrotem do frontonu HTTP dla aplikacji funkcji.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|fałsz|Wywołania z adresem URL zaplecza wskazujące funkcję w lokalnej aplikacji funkcji są przekazywane bezpośrednio do funkcji. Jest to wartość domyślna. |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

To ustawienie określa, czy znaki `%2F` są dekodowane jako ukośniki w parametrach trasy, gdy są wstawiane do adresu URL zaplecza. 

|Klucz|Wartość|Opis|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Parametry trasy z zakodowanymi ukośnikami są zdekodowane. |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|fałsz|Wszystkie parametry tras są przesyłane bez zmian, co jest zachowaniem domyślnym. |

Rozważmy na przykład proxies.jsw pliku dla aplikacji funkcji w `myfunction.com` domenie.

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

Gdy `AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES` jest ustawiona na `true` , adres URL jest `example.com/api%2ftest` rozpoznawany jako `example.com/api/test` . Domyślnie adres URL pozostaje niezmieniony `example.com/test%2fapi` . Aby uzyskać więcej informacji, zobacz temat usługi [proxy usługi Functions](functions-proxies.md).

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

W wersji 2. x i nowszych wersjach środowiska uruchomieniowego funkcji program skonfiguruje zachowanie aplikacji na podstawie środowiska uruchomieniowego. Ta wartość jest odczytywana podczas inicjowania i można ją ustawić na dowolną wartość. Tylko wartości `Development` , `Staging` i `Production` są honorowane przez środowisko uruchomieniowe. Jeśli to ustawienie aplikacji nie jest obecne w przypadku uruchamiania na platformie Azure, zakłada się, że jest to środowisko `Production` . Użyj tego ustawienia zamiast tego, `ASPNETCORE_ENVIRONMENT` Jeśli musisz zmienić środowisko uruchomieniowe na platformie Azure na coś innego niż `Production` . Azure Functions Core Tools ustawiona `AZURE_FUNCTIONS_ENVIRONMENT` na `Development` uruchomienie na komputerze lokalnym i nie można jej zastąpić w local.settings.jspliku. Aby dowiedzieć się więcej, zobacz [Klasa startowa oparta na środowisku i metody](/aspnet/core/fundamentals/environments#environment-based-startup-class-and-methods).

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

W wersji 2. x i nowszych wersjach środowiska uruchomieniowego funkcji ustawienia aplikacji mogą zastąpić [host.jsw](functions-host-json.md) ustawieniach w bieżącym środowisku. Te zastąpienia są wyrażane jako ustawienia aplikacji o nazwie `AzureFunctionsJobHost__path__to__setting` . Aby uzyskać więcej informacji, zobacz [Zastąp host.jswartościami](functions-host-json.md#override-hostjson-values).

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Opcjonalne parametry połączenia konta magazynu służące do przechowywania dzienników i wyświetlania ich na karcie **monitor** w portalu. To ustawienie jest prawidłowe tylko dla aplikacji przeznaczonych dla wersji 1. x środowiska uruchomieniowego Azure Functions. Konto magazynu musi być kontem ogólnego przeznaczenia, które obsługuje obiekty blob, kolejki i tabele. Aby dowiedzieć się więcej, zobacz [wymagania dotyczące konta magazynu](storage-considerations.md#storage-account-requirements).

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = <name> ; AccountKey =<key>|

> [!NOTE]
> Aby uzyskać lepszą wydajność i doświadczenie, środowisko uruchomieniowe w wersji 2. x i nowszych wersjach korzysta z usługi APPINSIGHTS_INSTRUMENTATIONKEY i App Insights do monitorowania, a nie `AzureWebJobsDashboard` .

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` oznacza wyłączenie domyślnej strony docelowej wyświetlanej dla głównego adresu URL aplikacji funkcji. Wartość domyślna to `false`.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Gdy to ustawienie aplikacji zostanie pominięte lub ustawione na wartość `false` , zostanie wyświetlona strona podobna do poniższego przykładu w odpowiedzi na adres URL `<functionappname>.azurewebsites.net` .

![Strona docelowa aplikacji funkcji](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation&quot;></a>AzureWebJobsDotNetReleaseCompilation

`true` oznacza użycie trybu wydania podczas kompilowania kodu platformy .NET; `false` oznacza użycie trybu debugowania. Wartość domyślna to `true`.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name=&quot;azurewebjobsfeatureflags&quot;></a>AzureWebJobsFeatureFlags

Rozdzielana przecinkami lista funkcji beta do włączenia. Funkcje beta włączone przez te flagi nie są gotowe do użycia w środowisku produkcyjnym, ale można je włączyć do eksperymentalnego użytku, zanim staną się aktywne.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name=&quot;azurewebjobssecretstoragetype&quot;></a>AzureWebJobsSecretStorageType

Określa repozytorium lub dostawcę, który ma być używany na potrzeby magazynu kluczy. Obecnie obsługiwane repozytoria to magazyn obiektów BLOB (&quot;BLOB") i lokalny system plików ("pliki"). Wartością domyślną jest obiekt BLOB w wersji 2 i system plików w wersji 1.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsSecretStorageType|Pliki|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Środowisko uruchomieniowe Azure Functions używa tych parametrów połączenia konta magazynu do normalnego działania. Niektóre zastosowania tego konta magazynu obejmują zarządzanie kluczami, zarządzanie wyzwalaczem czasomierza i Event Hubs punkty kontrolne. Konto magazynu musi być kontem ogólnego przeznaczenia, które obsługuje obiekty blob, kolejki i tabele. Zobacz wymagania dotyczące [konta magazynu](functions-infrastructure-as-code.md#storage-account) i [konta magazynu](storage-considerations.md#storage-account-requirements).

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [klucz]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Ścieżka do kompilatora używanego na potrzeby języka TypeScript. Umożliwia przesłonięcie ustawienia domyślnego, jeśli jest to konieczne.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>\_ \_ tryb edycji aplikacji \_ funkcji

Określa, czy Edycja w Azure Portal jest włączona. Prawidłowe wartości to "ReadWrite" i "ReadOnly".

|Klucz|Wartość przykładowa|
|---|------------|
|\_ \_ tryb edycji aplikacji \_ funkcji|readonly|

## <a name="functions_extension_version"></a>\_wersja rozszerzenia Functions \_

Wersja środowiska uruchomieniowego funkcji, która hostuje aplikację funkcji. Tylda ( `~` ) z wersją główną oznacza użycie najnowszej wersji tej wersji głównej (na przykład "~ 3"). Gdy dostępne są nowe wersje dla tej samej wersji głównej, są one automatycznie instalowane w aplikacji funkcji. Aby przypiąć aplikację do określonej wersji, użyj pełnego numeru wersji (na przykład "3.0.12345"). Wartość domyślna to "~ 3". Wartość `~1` przypinania aplikacji do wersji 1. x środowiska uruchomieniowego. Aby uzyskać więcej informacji, zobacz [Omówienie wersji środowiska uruchomieniowego Azure Functions](functions-versions.md).

|Klucz|Wartość przykładowa|
|---|------------|
|\_wersja rozszerzenia Functions \_|~ 3|

## <a name="functions_v2_compatibility_mode"></a>Tryb zgodności funkcji w \_ wersji 2 \_ \_

To ustawienie umożliwia uruchamianie aplikacji funkcji w trybie zgodności w wersji 2. x w wersji 3. x środowiska uruchomieniowego. Tego ustawienia należy używać tylko w przypadku napotkania problemów podczas [uaktualniania aplikacji funkcji z wersji 2. x do 3. x środowiska uruchomieniowego](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> To ustawienie jest przeznaczone tylko jako krótkoterminowe obejście podczas aktualizowania aplikacji tak, aby działała prawidłowo w wersji 3. x. To ustawienie jest obsługiwane, o ile jest [obsługiwane środowisko uruchomieniowe 2. x](functions-versions.md). Jeśli wystąpią problemy uniemożliwiające uruchomienie aplikacji w wersji 3. x bez użycia tego ustawienia, [Zgłoś problem](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Wymaga ustawienia [ \_ \_ wersji rozszerzenia Functions](functions-app-settings.md#functions_extension_version) na `~3` .

|Klucz|Wartość przykładowa|
|---|------------|
|Tryb zgodności funkcji w \_ wersji 2 \_ \_|true|

## <a name="functions_worker_process_count"></a>\_ \_ liczba procesów roboczych \_ funkcji

Określa maksymalną liczbę procesów roboczych języka z wartością domyślną `1` . Maksymalna dozwolona wartość to `10` . Wywołania funkcji są równomiernie dystrybuowane między procesami roboczymi języka. Procesy robocze języka są duplikowane co 10 sekund do momentu osiągnięcia liczby ustawionej przez \_ \_ liczbę procesów roboczych \_ . Używanie wielu procesów roboczych z wieloma językami nie jest takie samo jak [skalowanie](functions-scale.md). Należy rozważyć użycie tego ustawienia, jeśli obciążenie ma połączenie z wywołaniami związanymi z PROCESORem i we/wy. To ustawienie ma zastosowanie do wszystkich języków non-.NET.

|Klucz|Wartość przykładowa|
|---|------------|
|\_ \_ liczba procesów roboczych \_ funkcji|2|

## <a name="functions_worker_runtime"></a>\_ \_ środowisko uruchomieniowe procesu roboczego

Środowisko uruchomieniowe procesów roboczych języka do załadowania w aplikacji funkcji.  Odnosi się to do języka używanego w aplikacji (na przykład `dotnet` ). Począwszy od wersji 2. x środowiska uruchomieniowego Azure Functions, dana aplikacja funkcji może obsługiwać tylko jeden język.   

|Klucz|Wartość przykładowa|
|---|------------|
|\_ \_ środowisko uruchomieniowe procesu roboczego|węzeł|

Prawidłowe wartości:

| Wartość | Language |
|---|---|
| `dotnet` | [C# (biblioteka klas)](functions-dotnet-class-library.md)<br/>[C# (skrypt)](functions-reference-csharp.md) |
| `dotnet-isolated` | [C# (proces izolowany)](dotnet-isolated-process-guide.md) |
| `java` | [Java](functions-reference-java.md) |
| `node` | [JavaScript](functions-reference-node.md)<br/>[TypeScript](functions-reference-node.md#typescript) |
| `powershell` | [Program PowerShell](functions-reference-powershell.md) |
| `python` | [Python](functions-reference-python.md) |

## <a name="pip_extra_index_url"></a>\_ \_ adres URL dodatkowego indeksu PIP \_

Wartość tego ustawienia wskazuje niestandardowy adres URL indeksu pakietu dla aplikacji języka Python. Użyj tego ustawienia, jeśli chcesz uruchomić zdalną kompilację przy użyciu zależności niestandardowych, które znajdują się w dodatkowym indeksie pakietu.   

|Klucz|Wartość przykładowa|
|---|------------|
|\_ \_ adres URL dodatkowego indeksu PIP \_|http://my.custom.package.repo/simple |

Aby dowiedzieć się więcej, zobacz [zależności niestandardowe](functions-reference-python.md#remote-build-with-extra-index-url) w dokumentacji dewelopera języka Python.

## <a name="python_threadpool_thread_count"></a>\_Liczba wątków puli wątków języka Python \_ \_

Określa maksymalną liczbę wątków, które będą używane przez proces roboczy języka Python do wykonywania wywołań funkcji, z wartością domyślną `1` dla wersji języka Python `3.8` i poniżej. W przypadku wersji w języku Python `3.9` i nowszych wartość jest ustawiona na `None` . Należy zauważyć, że to ustawienie nie gwarantuje liczby wątków, które zostaną ustawione podczas wykonywania. Ustawienie umożliwia Python rozszerzanie liczby wątków do określonej wartości. To ustawienie dotyczy tylko aplikacji w języku Python. Ponadto to ustawienie ma zastosowanie do wywołań funkcji synchronicznych, a nie dla wspólnych procedur.

|Klucz|Wartość przykładowa|Wartość maksymalna|
|---|------------|---------|
|\_Liczba wątków puli wątków języka Python \_ \_|2|32|

## <a name="scale_controller_logging_enabled"></a>\_Rejestrowanie kontrolera skalowania \_ \_ włączone

_To ustawienie jest obecnie dostępne w wersji zapoznawczej._  

To ustawienie steruje rejestrowaniem z kontrolera skalowania Azure Functions. Aby uzyskać więcej informacji, zobacz [skalowanie dzienników kontrolera](functions-monitoring.md#scale-controller-logs).

|Klucz|Wartość przykładowa|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLED|AppInsights: verbose|

Wartość tego klucza jest podawana w formacie `<DESTINATION>:<VERBOSITY>` zdefiniowanym w następujący sposób:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="website_contentazurefileconnectionstring"></a>CONTENTAZUREFILECONNECTIONSTRING witryny sieci Web \_

Parametry połączenia dla konta magazynu, w którym kod i konfiguracja aplikacji funkcji są przechowywane w planach skalowania sterowanego zdarzeniami uruchomionymi w systemie Windows. Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji funkcji](functions-infrastructure-as-code.md#windows).

|Klucz|Wartość przykładowa|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [klucz]|

Używany tylko w przypadku wdrażania w planie Premium lub planu zużycia działającego w systemie Windows. Nieobsługiwane w przypadku planów użycia z systemem Linux. Zmiana lub usunięcie tego ustawienia może spowodować, że aplikacja funkcji nie zostanie uruchomiona. Aby dowiedzieć się więcej, zobacz [ten artykuł dotyczący rozwiązywania problemów](functions-recover-storage-account.md#storage-account-application-settings-were-deleted). 

## <a name="website_contentovervnet"></a>CONTENTOVERVNET witryny sieci Web \_

Tylko w przypadku planów Premium. Wartość `1` umożliwia skalowanie aplikacji funkcji, gdy konto magazynu jest ograniczone do sieci wirtualnej. To ustawienie należy włączyć w przypadku ograniczania konta magazynu do sieci wirtualnej. Aby dowiedzieć się więcej, zobacz [ograniczanie konta magazynu do sieci wirtualnej](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network).

|Klucz|Wartość przykładowa|
|---|------------|
|WEBSITE_CONTENTOVERVNET|1|

## <a name="website_contentshare"></a>CONTENTSHARE witryny sieci Web \_

Ścieżka do kodu i konfiguracji aplikacji funkcji w planie skalowania sterowanego zdarzeniami w systemie Windows. Używany z WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Wartość domyślna to unikatowy ciąg, który rozpoczyna się od nazwy aplikacji funkcji. Zobacz [Tworzenie aplikacji funkcji](functions-infrastructure-as-code.md#windows).

|Klucz|Wartość przykładowa|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

Używany tylko w przypadku wdrażania w planie Premium lub planu zużycia działającego w systemie Windows. Nieobsługiwane w przypadku planów użycia z systemem Linux. Zmiana lub usunięcie tego ustawienia może spowodować, że aplikacja funkcji nie zostanie uruchomiona. Aby dowiedzieć się więcej, zobacz [ten artykuł dotyczący rozwiązywania problemów](functions-recover-storage-account.md#storage-account-application-settings-were-deleted).

W przypadku korzystania z Azure Resource Manager do tworzenia aplikacji funkcji podczas wdrażania nie należy dołączać WEBSITE_CONTENTSHARE do szablonu. To ustawienie aplikacji jest generowane podczas wdrażania. Aby dowiedzieć się więcej, zobacz [Automatyzowanie wdrażania zasobów dla aplikacji funkcji](functions-infrastructure-as-code.md#windows).   

## <a name="website_dns_server"></a>\_serwer DNS witryny sieci Web \_

Ustawia serwer DNS używany przez aplikację podczas rozpoznawania adresów IP. To ustawienie jest często wymagane w przypadku korzystania z określonych funkcji sieciowych, takich jak [Azure DNS stref prywatnych](functions-networking-options.md#azure-dns-private-zones) i [prywatnych punktów końcowych](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network).   

|Klucz|Wartość przykładowa|
|---|------------|
|\_serwer DNS witryny sieci Web \_|168.63.129.16|

## <a name="website_max_dynamic_application_scale_out"></a>\_Maksymalna \_ \_ \_ SKALOWANIe aplikacji \_ sieci Web w poziomie

Maksymalna liczba wystąpień, do których aplikacja może skalować w poziomie. Wartość domyślna nie jest ograniczona.

> [!IMPORTANT]
> To ustawienie jest w wersji zapoznawczej.  Dodano [Właściwość App dla funkcji maksymalnego skalowania w](./event-driven-scaling.md#limit-scale-out) poziomie i jest to zalecany sposób ograniczania skalowania w poziomie.

|Klucz|Wartość przykładowa|
|---|------------|
|\_Maksymalna \_ \_ \_ SKALOWANIe aplikacji \_ sieci Web w poziomie|5|

## <a name="website_node_default_version"></a>\_DEFAULT_VERSION węzła witryny sieci Web \_

_Tylko system Windows._  
Ustawia wersję Node.js, która ma być używana podczas uruchamiania aplikacji funkcji w systemie Windows. Należy użyć tyldy (~), aby środowisko uruchomieniowe używało najnowszej dostępnej wersji wersji głównej. Na przykład po ustawieniu na wartość `~10` zostanie użyta Najnowsza wersja Node.js 10. W przypadku wersji głównej, której celem jest tylda, nie trzeba ręcznie aktualizować wersji pomocniczej. 

|Klucz|Wartość przykładowa|
|---|------------|
|\_DEFAULT_VERSION węzła witryny sieci Web \_|~ 10|

## <a name="website_run_from_package"></a>Witryna sieci Web \_ uruchamiana \_ z \_ pakietu

Umożliwia uruchamianie aplikacji funkcji z zainstalowanego pliku pakietu.

|Klucz|Wartość przykładowa|
|---|------------|
|Witryna sieci Web \_ uruchamiana \_ z \_ pakietu|1|

Prawidłowe wartości to adres URL, który jest rozpoznawany jako lokalizacja pliku pakietu wdrożeniowego lub `1` . Po ustawieniu na `1` , pakiet musi znajdować się w `d:\home\data\SitePackages` folderze. W przypadku korzystania z tego ustawienia podczas wdrażania zip pakiet jest automatycznie przekazywany do tej lokalizacji. W wersji zapoznawczej to ustawienie miało nazwę `WEBSITE_RUN_FROM_ZIP` . Aby uzyskać więcej informacji, zobacz [Uruchamianie funkcji z pliku pakietu](run-functions-from-deployment-package.md).

## <a name="website_time_zone"></a>\_strefa czasowa witryny sieci Web \_

Umożliwia ustawienie strefy czasowej dla aplikacji funkcji. 

|Klucz|System operacyjny|Wartość przykładowa|
|---|--|------------|
|\_strefa czasowa witryny sieci Web \_|Windows|Wschodni czas standardowy|
|\_strefa czasowa witryny sieci Web \_|Linux|Ameryka/New_York|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="website_vnet_route_all"></a>Sieć wirtualna sieci Web — \_ \_ \_ wszystkie trasy

Wskazuje, czy cały ruch wychodzący z aplikacji jest kierowany za pośrednictwem sieci wirtualnej. Wartość ustawienia `1` wskazuje, że cały ruch jest kierowany przez sieć wirtualną. Tego ustawienia należy użyć w przypadku korzystania z funkcji [integracji regionalnej sieci wirtualnej](functions-networking-options.md#regional-virtual-network-integration). Jest on również używany, gdy [brama NAT sieci wirtualnej jest używana do definiowania statycznego wychodzącego adresu IP](functions-how-to-use-nat-gateway.md). 

|Klucz|Wartość przykładowa|
|---|------------|
|Sieć wirtualna sieci Web — \_ \_ \_ wszystkie trasy|1|

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak zaktualizować ustawienia aplikacji](functions-how-to-use-azure-function-app-settings.md#settings)

[Zobacz ustawienia globalne w host.jspliku](functions-host-json.md)

[Zobacz inne ustawienia aplikacji dla aplikacji App Service](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
