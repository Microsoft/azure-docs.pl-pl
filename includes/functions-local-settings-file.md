---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 190524251d139e1421c1aac93d5a4dd523068a7a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958069"
---
## <a name="local-settings-file"></a>Plik ustawień lokalnych

local.settings.jsw plikach przechowuje ustawienia aplikacji, parametry połączenia i ustawienia używane przez lokalne narzędzia deweloperskie. Ustawienia w local.settings.jspliku są używane tylko w przypadku lokalnego uruchamiania projektów. Plik ustawień lokalnych ma następującą strukturę:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

Te ustawienia są obsługiwane podczas lokalnego uruchamiania projektów:

| Ustawienie      | Opis                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Gdy to ustawienie jest ustawione na `true` , wszystkie wartości są szyfrowane za pomocą klucza komputera lokalnego. Używany z `func settings` poleceniami. Wartość domyślna to `false`. Możesz chcieć zaszyfrować local.settings.jspliku na komputerze lokalnym, jeśli zawiera on klucze tajne, takie jak parametry połączenia usługi. Host automatycznie odszyfrowuje ustawienia podczas jego uruchamiania. Użyj `func settings decrypt` polecenia przed próbą odczytania ustawień zaszyfrowanych lokalnie. |
| **`Values`** | Tablica ustawień aplikacji i parametrów połączenia używanych, gdy projekt jest uruchomiony lokalnie. Pary klucz-wartość (String-String) odpowiadają ustawieniom aplikacji w aplikacji funkcji na platformie Azure, takich jak [`AzureWebJobsStorage`] . Wiele wyzwalaczy i powiązań ma właściwość, która odwołuje się do ustawienia aplikacji parametrów połączenia, takiego jak `Connection` dla [wyzwalacza usługi BLOB Storage](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Dla tych właściwości potrzebne jest ustawienie aplikacji zdefiniowane w `Values` tablicy. Lista najczęściej używanych ustawień znajduje się w dalszej części tabeli. <br/>Wartości muszą być ciągami, a nie obiektami JSON ani tablicami. Nazwy ustawień nie mogą zawierać dwukropka ( `:` ) ani podwójnego podkreślenia ( `__` ). Znaki podwójnego podkreślenia są zarezerwowane przez środowisko uruchomieniowe, a dwukropek jest zarezerwowany do obsługi [iniekcji zależności](../articles/azure-functions/functions-dotnet-dependency-injection.md#working-with-options-and-settings). |
| **`Host`** | Ustawienia w tej sekcji dostosowują proces hosta funkcji w przypadku uruchamiania projektów lokalnie. Te ustawienia są oddzielone od host.jsw ustawieniach, które również są stosowane podczas uruchamiania projektów na platformie Azure. |
| **`LocalHttpPort`** | Ustawia domyślny port używany podczas uruchamiania hosta funkcji lokalnych ( `func host start` i `func run` ). `--port`Opcja wiersza polecenia ma pierwszeństwo przed tym ustawieniem. Na przykład w przypadku uruchamiania w środowisku IDE programu Visual Studio można zmienić numer portu, przechodząc do okna "właściwości projektu-> Debuguj" i jawnie określając numer portu w `host start --port <your-port-number>` poleceniu, które można podać w polu "argumenty aplikacji". |
| **`CORS`** | Definiuje źródła dozwolone dla [udostępniania zasobów między źródłami (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Źródła są dostarczane jako rozdzielana przecinkami lista bez spacji. Wartość symbolu wieloznacznego ( \* ) jest obsługiwana, co pozwala na żądania z dowolnego źródła. |
| **`CORSCredentials`** |  Gdy ustawiona na `true` , zezwala na `withCredentials` żądania. |
| **`ConnectionStrings`** | Kolekcja. Nie używaj tej kolekcji dla parametrów połączenia używanych przez powiązania funkcji. Ta kolekcja jest używana tylko przez platformy, które zazwyczaj pobierają parametry połączenia z `ConnectionStrings` sekcji pliku konfiguracji, na przykład [Entity Framework](/ef/ef6/). Parametry połączenia w tym obiekcie są dodawane do środowiska z typem dostawcy [System. Data. SqlClient](/dotnet/api/system.data.sqlclient). Elementy w tej kolekcji nie są publikowane na platformie Azure przy użyciu innych ustawień aplikacji. Należy jawnie dodać te wartości do `Connection strings` kolekcji ustawień aplikacji funkcji. Jeśli tworzysz [`SqlConnection`](/dotnet/api/system.data.sqlclient.sqlconnection) w kodzie funkcji, należy przechowywać wartość parametrów połączenia z innymi połączeniami w **ustawieniach aplikacji** w portalu. |

Następujące ustawienia aplikacji mogą być dołączone do macierzy w **`Values`** przypadku uruchamiania lokalnego:

| Ustawienie | Wartości | Opis |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| Parametry połączenia konta magazynu lub<br/>`UseDevelopmentStorage=true`| Zawiera parametry połączenia dla konta usługi Azure Storage. Wymagane, gdy używane są wyzwalacze inne niż HTTP. Aby uzyskać więcej informacji, zobacz [`AzureWebJobsStorage`] odwołanie.<br/>Jeśli [emulator usługi Azure Storage](../articles/storage/common/storage-use-emulator.md) jest zainstalowany lokalnie i ustawisz [`AzureWebJobsStorage`] jako `UseDevelopmentStorage=true` , narzędzia podstawowe używają emulatora. Emulator jest przydatny podczas opracowywania, ale przed wdrożeniem należy przetestować rzeczywiste połączenie z magazynem.| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | Aby wyłączyć funkcję w przypadku uruchamiania lokalnego, Dodaj `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` do kolekcji, gdzie `<FUNCTION_NAME>` to nazwa funkcji. Aby dowiedzieć się więcej, zobacz [Jak wyłączyć funkcje w Azure Functions](../articles/azure-functions/disable-function.md#localsettingsjson) |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| Wskazuje język, w którym znajduje się środowisko uruchomieniowe funkcji. Wymagane dla wersji 2. x i nowszych środowiska uruchomieniowego Functions. To ustawienie jest generowane dla projektu według podstawowych narzędzi. Aby dowiedzieć się więcej, zobacz [`FUNCTIONS_WORKER_RUNTIME`](../articles/azure-functions/functions-app-settings.md#functions_worker_runtime) Dokumentacja.|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |Wskazuje, że program PowerShell 7 ma być używany podczas uruchamiania lokalnego. Jeśli nie zostanie ustawiona, używany jest program PowerShell Core 6. To ustawienie jest używane tylko w przypadku uruchamiania lokalnego. W przypadku uruchamiania na platformie Azure wersja środowiska uruchomieniowego programu PowerShell jest określana na podstawie `powerShellVersion` Ustawienia konfiguracji lokacji, które można [ustawić w portalu](../articles/azure-functions/functions-reference-powershell.md#changing-the-powershell-version). | 

[AzureWebJobsStorage]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
