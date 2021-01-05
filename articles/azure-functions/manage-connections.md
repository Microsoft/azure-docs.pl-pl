---
title: Zarządzanie połączeniami w Azure Functions
description: Dowiedz się, jak uniknąć problemów z wydajnością w Azure Functions przy użyciu klientów połączeń statycznych.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 02/25/2018
ms.openlocfilehash: 53848e6273cf59439d44b431652981b18bdd5ba6
ms.sourcegitcommit: 90caa05809d85382c5a50a6804b9a4d8b39ee31e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97755960"
---
# <a name="manage-connections-in-azure-functions"></a>Zarządzanie połączeniami w Azure Functions

Działa w ramach zasobów funkcji udział aplikacji. Wśród tych udostępnionych zasobów są połączenia: połączenia HTTP, połączenia baz danych i połączenia z usługami, takimi jak Azure Storage. Gdy wiele funkcji jest uruchomionych współbieżnie, możliwe jest wypróbowanie dostępnych połączeń. W tym artykule wyjaśniono, jak zakodować funkcje, aby uniknąć używania większej liczby połączeń niż te, które są im potrzebne.

## <a name="connection-limit"></a>Limit połączeń

Liczba dostępnych połączeń jest ograniczona częściowo, ponieważ aplikacja funkcji jest uruchamiana w [środowisku piaskownicy](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Jednym z ograniczeń, które nakładają się w Twoim kodzie, jest ograniczenie liczby połączeń wychodzących, które obecnie są 600 aktywne (1 200 łącznie) dla każdego wystąpienia. Po osiągnięciu tego limitu środowisko uruchomieniowe funkcji zapisuje następujący komunikat do dzienników: `Host thresholds exceeded: Connections` . Aby uzyskać więcej informacji, zobacz [limity usługi Functions](functions-scale.md#service-limits).

Ten limit jest przypadany na wystąpienie. Gdy [kontroler skalowania dodaje wystąpienia aplikacji funkcji](functions-scale.md#how-the-consumption-and-premium-plans-work) do obsługi większej liczby żądań, każde wystąpienie ma limit połączeń niezależnych. Oznacza to, że nie ma limitu połączenia globalnego i można mieć znacznie więcej niż 600 aktywnych połączeń we wszystkich aktywnych wystąpieniach.

W przypadku rozwiązywania problemów upewnij się, że włączono Application Insights dla aplikacji funkcji. Application Insights umożliwia wyświetlanie metryk dla aplikacji funkcji, takich jak wykonania. Aby uzyskać więcej informacji, zobacz [Wyświetlanie telemetrii w Application Insights](analyze-telemetry-data.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Klienci statyczni

Aby uniknąć utrzymywania większej liczby połączeń, należy ponownie użyć wystąpień klienta zamiast tworzyć nowe przy użyciu każdego wywołania funkcji. Zalecamy ponowne użycie połączeń klientów dla dowolnego języka, w którym można napisać funkcję. Na przykład klienci platformy .NET, takie jak [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true), [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient)i klienci usługi Azure Storage, mogą zarządzać połączeniami, jeśli używany jest pojedynczy klient statyczny.

Poniżej przedstawiono niektóre wskazówki, które należy wykonać w przypadku korzystania z klienta specyficznego dla usługi w aplikacji Azure Functions:

- *Nie należy* tworzyć nowego klienta przy każdym wywołaniu funkcji.
- *Utwórz pojedynczego* , statycznego klienta, który może być używany przez każde wywołanie funkcji.
- *Rozważ* utworzenie jednego statycznego klienta w udostępnionej klasie pomocnika, jeśli różne funkcje korzystają z tej samej usługi.

## <a name="client-code-examples"></a>Przykłady kodu klienta

W tej sekcji przedstawiono najlepsze rozwiązania dotyczące tworzenia i używania klientów z kodu funkcji.

### <a name="httpclient-example-c"></a>Przykład HttpClient (C#)

Oto przykład kodu funkcji w języku C#, który tworzy statyczne wystąpienie [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) :

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Często zadawane pytania dotyczące [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) w programie .NET to "czy należy usunąć mój klient?" Ogólnie rzecz biorąc, można usunąć obiekty, które implementują się `IDisposable` po zakończeniu korzystania z nich. Nie można jednak usunąć klienta statycznego, ponieważ nie jest on używany podczas kończenia funkcji. Klient statyczny ma na żywo na czas trwania aplikacji.

### <a name="http-agent-examples-javascript"></a>Przykłady agenta HTTP (JavaScript)

Ponieważ zapewnia lepsze opcje zarządzania połączeniami, należy użyć klasy natywnej [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) zamiast metod nienatywnych, takich jak `node-fetch` moduł. Parametry połączenia są konfigurowane za pomocą opcji w `http.agent` klasie. Aby uzyskać szczegółowe opcje dostępne dla agenta HTTP, zobacz [Nowy Agent ( \[ Opcje \] )](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

Klasa globalna `http.globalAgent` używana przez `http.request()` ma wszystkie te wartości ustawione na ich wartości domyślne. Zalecanym sposobem skonfigurowania limitów połączeń w funkcjach jest ustawienie maksymalnej liczby globalnie. W poniższym przykładzie ustawiono maksymalną liczbę gniazd dla aplikacji funkcji:

```js
http.globalAgent.maxSockets = 200;
```

 Poniższy przykład tworzy nowe żądanie HTTP z niestandardowym agentem HTTP tylko dla tego żądania:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Przykład kodu DocumentClient (C#)

[DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) nawiązuje połączenie z wystąpieniem Azure Cosmos DB. W dokumentacji Azure Cosmos DB zaleca się [użycie pojedynczego klienta Azure Cosmos DB na potrzeby okresu istnienia aplikacji](../cosmos-db/performance-tips.md#sdk-usage). W poniższym przykładzie pokazano jeden wzorzec dla tego, że w funkcji:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```
W przypadku korzystania z funkcji v3. x należy odwołać się do Microsoft.Azure.DocumentDB. Core. Dodaj odwołanie w kodzie:

```cs
#r "Microsoft.Azure.DocumentDB.Core"
```
Ponadto Utwórz plik o nazwie "Function. proj" dla wyzwalacza i Dodaj poniższą zawartość:

```cs

<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netcoreapp3.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.DocumentDB.Core" Version="2.12.0" />
    </ItemGroup>
</Project>

```
### <a name="cosmosclient-code-example-javascript"></a>Przykład kodu CosmosClient (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) nawiązuje połączenie z wystąpieniem Azure Cosmos DB. W dokumentacji Azure Cosmos DB zaleca się [użycie pojedynczego klienta Azure Cosmos DB na potrzeby okresu istnienia aplikacji](../cosmos-db/performance-tips.md#sdk-usage). W poniższym przykładzie pokazano jeden wzorzec dla tego, że w funkcji:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>Połączenia SqlClient

Kod funkcji może używać Dostawca danych .NET Framework dla SQL Server ([SqlClient](/dotnet/api/system.data.sqlclient)) do nawiązywania połączeń z relacyjną bazą danych SQL. Jest to również podstawowy dostawca dla struktur danych, które opierają się na ADO.NET, na przykład [Entity Framework](/ef/ef6/). W przeciwieństwie do połączeń [HttpClient](/dotnet/api/system.net.http.httpclient) i [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) , ADO.NET implementuje buforowanie połączeń domyślnie. Mimo że nadal można korzystać z połączeń, należy zoptymalizować połączenia z bazą danych. Aby uzyskać więcej informacji, zobacz [SQL Servering pooling (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Niektóre struktury danych, takie jak Entity Framework, zazwyczaj pobierają parametry połączenia z sekcji **connectionStrings** w pliku konfiguracji. W takim przypadku należy jawnie dodać parametry połączenia z bazą danych SQL do kolekcji **parametrów połączenia** ustawień aplikacji funkcji i w [local.settings.jsw pliku](functions-run-local.md#local-settings-file) w projekcie lokalnym. Jeśli tworzysz wystąpienie elementu [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) w kodzie funkcji, należy zapisać wartość parametrów połączenia w **ustawieniach aplikacji** przy użyciu innych połączeń.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat przyczyn zalecanych klientów statycznych, zobacz [niewłaściwy Antywzorzec tworzenia wystąpienia](/azure/architecture/antipatterns/improper-instantiation/).

Aby uzyskać więcej Azure Functions porad dotyczących wydajności, zobacz [Optymalizacja wydajności i niezawodności Azure Functions](functions-best-practices.md).
