---
title: Kodowanie aplikacji klienckiej
titleSuffix: Azure Digital Twins
description: Samouczek, w którym można napisać minimalny kod aplikacji klienckiej przy użyciu zestawu SDK platformy .NET (C#).
author: cschormann
ms.author: cschorm
ms.date: 05/05/2020
ms.topic: tutorial
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 7798151d0aeaa553c21db0fd8e71f4ac674a9a56
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85355713"
---
# <a name="coding-with-the-azure-digital-twins-apis"></a>Kodowanie za pomocą cyfrowych interfejsów API usługi Azure bliźniaczych reprezentacji

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

W przypadku deweloperów pracujących z usługą Azure Digital bliźniaczych reprezentacji można napisać aplikację kliencką do współdziałania z ich wystąpieniem usługi Azure Digital bliźniaczych reprezentacji. Ten samouczek ukierunkowany na dewelopera zawiera wprowadzenie do programowania w usłudze Azure Digital bliźniaczych reprezentacji, przy użyciu [biblioteki klienckiej Digital bliźniaczyej usługi Azure IoT dla platformy .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Przeprowadzi Cię przez proces pisania aplikacji klienckiej w języku C# krok po kroku, rozpoczynając od podstaw.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku zostanie użyty wiersz polecenia dla Instalatora i pracy w projekcie. W związku z tym możesz użyć dowolnego edytora kodu, aby zapoznać się z ćwiczeniami.

Co należy zrobić:
* Dowolny edytor kodu
* **Platforma .NET Core 3,1** na komputerze deweloperskim. Tę wersję zestaw .NET Core SDK można pobrać z wielu platform [pobrać z programu .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

[!INCLUDE [Azure Digital Twins tutorials: instance prereq](../../includes/digital-twins-tutorial-prereq-instance.md)]

## <a name="set-up-project"></a>Konfigurowanie projektu

Gdy wszystko będzie gotowe do pracy z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji, Rozpocznij Konfigurowanie projektu aplikacji klienckiej. 

Otwórz wiersz polecenia lub inne okno konsoli na maszynie i Utwórz pusty katalog projektu, w którym chcesz przechowywać swoją służbę w trakcie tego samouczka. Nazwij katalog, którego chcesz dotyczyć (na przykład *DigitalTwinsCodeTutorial*).

Przejdź do nowego katalogu.

W katalogu projektu Utwórz pusty projekt aplikacji konsoli .NET. W oknie polecenia Uruchom następujące polecenie, aby utworzyć minimalny projekt w języku C# dla konsoli programu:

```cmd/sh
dotnet new console
```

Spowoduje to utworzenie kilku plików znajdujących się w katalogu, w tym jeden o nazwie *program.cs* , w którym będzie można napisać większość kodu.

Następnie Dodaj dwa niezbędne zależności do pracy z usługą Azure Digital bliźniaczych reprezentacji:

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.2
dotnet add package Azure.identity
```

Pierwsza zależność to [Biblioteka kliencka Digital bliźniaczy usługi Azure IoT dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Druga zależność zawiera narzędzia, które ułatwiają uwierzytelnianie na platformie Azure.

Pozostaw otwarte okno polecenia, ponieważ będziesz nadal korzystać z niego w całym samouczku.

## <a name="get-started-with-project-code"></a>Wprowadzenie do kodu projektu

W tej sekcji rozpocznie się pisanie kodu dla nowego projektu aplikacji do pracy z usługą Azure Digital bliźniaczych reprezentacji. Objęte są następujące działania:
* Uwierzytelnianie w usłudze
* Przekazywanie modelu
* Przechwytywanie błędów
* Tworzenie bliźniaczych reprezentacji cyfrowych
* Tworzenie relacji
* Wykonywanie zapytania dotyczącego cyfrowego bliźniaczych reprezentacji

Istnieje również sekcja przedstawiająca kompletny kod na końcu samouczka. Można jej użyć jako odwołania do sprawdzenia programu w trakcie pracy.

Aby rozpocząć, Otwórz plik *program.cs* w dowolnym edytorze kodu. Zobaczysz minimalny szablon kodu, który wygląda następująco:

```csharp
using System;

namespace DigitalTwinsCodeTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

Najpierw Dodaj wiersze w `using` górnej części kodu, aby ściągnąć w zależności od potrzeb.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
```

Następnie dodasz kod do tego pliku w celu wypełnienia niektórych funkcji. 

### <a name="authenticate-against-the-service"></a>Uwierzytelnianie w usłudze

W pierwszej kolejności aplikacja musi być uwierzytelniana w usłudze Azure Digital bliźniaczych reprezentacji. Następnie można utworzyć klasę klienta usługi, aby uzyskać dostęp do funkcji zestawu SDK.

Aby można było przeprowadzić uwierzytelnianie, potrzebne są trzy informacje:
* *Identyfikator katalogu (dzierżawcy)* dla subskrypcji
* *Identyfikator aplikacji (klienta)* utworzony podczas wcześniejszego konfigurowania wystąpienia usługi Azure Digital bliźniaczych reprezentacji
* *Nazwa hosta* wystąpienia usługi Azure Digital bliźniaczych reprezentacji

>[!TIP]
> Jeśli nie znasz *identyfikatora katalogu (dzierżawy)*, możesz go uzyskać, uruchamiając następujące polecenie w [Azure Cloud Shell](https://shell.azure.com):
> 
> ```azurecli-interactive
> az account show --query tenantId
> ```

W *program.cs*wklej poniższy kod poniżej "Hello, World!" Linia wydruku w `Main` metodzie. Ustaw wartość w polu `adtInstanceUrl` *Nazwa hosta*usługi Azure Digital bliźniaczych reprezentacji, `clientId` *Identyfikator aplikacji*i `tenantId` *Identyfikator katalogu*.

```csharp
string clientId = "<your-application-ID>";
string tenantId = "<your-directory-ID>";
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
Console.WriteLine($"Service client created – ready to go");
```

Zapisz plik. 

Należy zauważyć, że w tym przykładzie używane jest logowanie interakcyjne przeglądarki:
```csharp
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
```

Ten typ poświadczenia spowoduje otwarcie okna przeglądarki z prośbą o podanie poświadczeń platformy Azure. 

>[!NOTE]
> Informacje o innych typach poświadczeń można znaleźć w dokumentacji dotyczącej [bibliotek uwierzytelniania platformy tożsamości firmy Microsoft](../active-directory/develop/reference-v2-libraries.md).

W oknie polecenia Uruchom kod przy użyciu tego polecenia: 

```cmd/sh
dotnet run
```

Spowoduje to przywrócenie zależności od pierwszego uruchomienia, a następnie wykonanie programu. 
* Jeśli wystąpi błąd, program drukuje *klienta usługi, który został utworzony — gotowy do użycia*.
* Ponieważ nie ma jeszcze żadnej obsługi błędów w tym projekcie, jeśli coś się nie dzieje, zobaczysz wyjątek zgłoszony przez kod.

### <a name="upload-a-model"></a>Przekaż model

Usługa Azure Digital bliźniaczych reprezentacji nie ma słownika domeny wewnętrznej. Typy elementów w środowisku, które można reprezentować w usłudze Azure Digital bliźniaczych reprezentacji, są definiowane przez użytkownika przy użyciu **modeli**. [Modele](concepts-models.md) są podobne do klas w języku programowania zorientowanym na obiekty; udostępniają one szablony zdefiniowane przez użytkownika na potrzeby [cyfrowego bliźniaczych reprezentacji](concepts-twins-graph.md) i tworzenia wystąpienia w przyszłości. Są one zapisywane w języku podobnym do notacji JSON o nazwie **Digital bliźniaczych reprezentacji Definition Language (DTDL)**.

Pierwszym krokiem tworzenia rozwiązania Digital bliźniaczych reprezentacji na platformie Azure jest zdefiniowanie co najmniej jednego modelu w pliku DTDL.

W katalogu, w którym został utworzony projekt, Utwórz nowy plik *JSON* o nazwie *SampleModel.json*. Wklej w następującej treści pliku: 

```json
{
  "@id": "dtmi:com:contoso:SampleModel;1",
  "@type": "Interface",
  "displayName": "SampleModel",
  "contents": [
    {
      "@type": "Relationship",
      "name": "contains"
    },
    {
      "@type": "Property",
      "name": "data",
      "schema": "string"
    }
  ],
  "@context": "dtmi:dtdl:context;2"
}
```

> [!TIP]
> Jeśli używasz programu Visual Studio na potrzeby tego samouczka, możesz chcieć wybrać nowo utworzony plik JSON i ustawić właściwość *Kopiuj do katalogu wyjściowego* w Inspektorze właściwości, aby *skopiować w przypadku, gdy nowszy* lub *Kopiuj zawsze*. Umożliwi to programowi Visual Studio znalezienie pliku JSON z domyślną ścieżką podczas uruchamiania programu przy użyciu klawisza **F5** w pozostałej części tego samouczka.

> [!TIP] 
> Istnieje [przykład modułu sprawdzania poprawności](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator) języka niezależny od DTDL, którego można użyć do sprawdzenia dokumentów modelu, aby upewnić się, że DTDL jest prawidłowy. Jest ona oparta na bibliotece analizatora DTDL, którą można dowiedzieć się więcej na temat postanowień [: analizowanie i weryfikowanie modeli](how-to-use-parser.md).

Następnie Dodaj więcej kodu do *program.cs* , aby przekazać model, który został właśnie utworzony w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji.

Najpierw Dodaj kilka `using` instrukcji na początku pliku:

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Models;
```

Następnie przygotuj się do użycia metod asynchronicznych w zestawie SDK usługi C#, zmieniając `Main` sygnaturę metody, aby umożliwić wykonywanie asynchroniczne. 

```csharp
static async Task Main(string[] args)
```

> [!NOTE]
> Użycie `async` nie jest ściśle wymagane, ponieważ zestaw SDK udostępnia również synchroniczne wersje wszystkich wywołań. W tym samouczku przedstawiono sposób użycia programu `async` .

Następnie jest dostarczany pierwszy bit kodu, który współdziała z usługą Azure Digital bliźniaczych reprezentacji. Ten kod ładuje plik DTDL utworzony z dysku, a następnie przekazuje go do wystąpienia usługi Azure Digital bliźniaczych reprezentacji. 

Wklej następujący kod w obszarze kodu autoryzacji, który został dodany wcześniej.

```csharp
Console.WriteLine();
Console.WriteLine($"Upload a model");
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
// Upload the model to the service
await client.CreateModelsAsync(typeList);
```

W oknie polecenia Uruchom program za pomocą tego polecenia: 

```cmd/sh
dotnet run
```
"Przekaż model" zostanie wydrukowany w danych wyjściowych, ale nie ma jeszcze żadnych danych wyjściowych, aby wskazać, czy modele zostały pomyślnie przekazane.

Aby dodać instrukcję Print wskazującą, czy modele zostały pomyślnie przekazane, Dodaj następujący kod bezpośrednio po poprzedniej sekcji:

```csharp
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Przed ponownym uruchomieniem programu w celu przetestowania nowego kodu, należy odwołać się do ostatniego uruchomienia programu, przekazano już swój model. Usługa Azure Digital bliźniaczych reprezentacji nie pozwoli na przekazanie tego samego modelu dwa razy, dlatego należy napotkać wyjątek podczas ponownego uruchamiania programu.

Teraz ponownie uruchom program przy użyciu tego polecenia w oknie poleceń:

```cmd/sh
dotnet run
```

Program powinien zgłosić wyjątek. Podczas próby przekazania modelu, który został już przekazany, usługa zwraca błąd "złe żądanie" za pośrednictwem interfejsu API REST. W związku z tym zestaw SDK klienta Digital bliźniaczych reprezentacji platformy Azure będzie z kolei zgłaszał wyjątek dla każdego kodu powrotu usługi innego niż powodzenie. 

W następnej sekcji znajdują się informacje o wyjątkach, takich jak te i jak je obsłużyć w kodzie.

### <a name="catch-errors"></a>Błędy przechwytywania

Aby zapobiec awariom programu, można dodać kod wyjątku wokół kodu przekazanego przez model. Zawiń istniejące wywołanie klienta `client.CreateModelsAsync` w obsłudze try/catch, takie jak:

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```

Jeśli program zostanie uruchomiony z poziomu `dotnet run` okna poleceń teraz, zobaczysz kod błędu z powrotem. Dane wyjściowe wyglądają następująco:

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Load model: 409:Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"DocumentAlreadyExists","message":"A document with same identifier already exists.","details":[]}}

Headers:
api-supported-versions: REDACTED
Date: Tue, 05 May 2020 01:57:51 GMT
Content-Length: 115
Content-Type: application/json; charset=utf-8

Type name: : dtmi:com:contoso:SampleModel;1
```

Od tego momentu samouczek umieści wszystkie wywołania metod usługi w obsłudze try/catch.

### <a name="create-digital-twins"></a>Utwórz cyfrowy bliźniaczych reprezentacji

Po przekazaniu modelu do usługi Azure Digital bliźniaczych reprezentacji można użyć tej definicji modelu do utworzenia **cyfrowego bliźniaczych reprezentacji**. [Digital bliźniaczych reprezentacji](concepts-twins-graph.md) to wystąpienia modelu i reprezentujące jednostki w środowisku biznesowym — takie jak czujniki w farmie, pokoje w budynku lub lampy w kabinie. Ta sekcja tworzy kilka bliźniaczych reprezentacji cyfrowych na podstawie przekazanego wcześniej modelu.

Dodaj nową `using` instrukcję u góry, ponieważ będzie potrzebny wbudowany serializator JSON programu .NET w programie `System.Text.Json` :

```csharp
using System.Text.Json;
```

Następnie Dodaj następujący kod na końcu `Main` metody, aby utworzyć i zainicjować trzy bliźniaczych reprezentacji cyfrowe w oparciu o ten model.

```csharp
// Initialize twin data
BasicDigitalTwin twinData = new BasicDigitalTwin();
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", $"Hello World!");

string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        twinData.Id = $"{prefix}{i}";
        await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
    }
}
```

W oknie polecenia Uruchom program z `dotnet run` . Następnie powtórz te czynności, aby ponownie uruchomić program. 

Należy zauważyć, że żaden błąd nie jest zgłaszany, gdy bliźniaczych reprezentacji jest tworzony drugi raz, nawet jeśli bliźniaczych reprezentacji już istnieje po pierwszym uruchomieniu. W przeciwieństwie do tworzenia modelu, tworzenie sznurka to, na poziomie REST, wywołanie *Put* z semantyką *upsert* . Oznacza to, że w przypadku, gdy już istnieje przędza, próba jej ponownego zamienia się. Nie jest wymagany żaden błąd.

### <a name="create-relationships"></a>Utwórz relacje

Następnie można utworzyć **relacje** między utworzonym bliźniaczych reprezentacji, aby połączyć je z **wykresem bliźniaczym**. [Wykresy bliźniaczy](concepts-twins-graph.md) są używane do reprezentowania całego środowiska.

Aby można było utworzyć relacje, Dodaj `using` instrukcję dla typu podstawowego relacji w zestawie SDK:
```csharp
using Azure.DigitalTwins.Core.Serialization;
```

Następnie Dodaj nową metodę statyczną do `Program` klasy, poniżej `Main` metody:
```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Następnie Dodaj następujący kod na końcu `Main` metody, aby wywołać `CreateRelationship` kod:
```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

W oknie polecenia Uruchom program z `dotnet run` .

Należy pamiętać, że usługa Azure Digital bliźniaczych reprezentacji nie pozwala na utworzenie relacji, jeśli istnieje już taka o takim samym IDENTYFIKATORze, więc jeśli program zostanie uruchomiony wielokrotnie, zobaczysz wyjątki podczas tworzenia relacji. Ten kod przechwytuje wyjątki i ignoruje je. 

### <a name="list-relationships"></a>Wyświetl relacje

Następny kod, który dodasz, umożliwia wyświetlenie listy utworzonych relacji.

Dodaj następującą nową metodę do klasy `Program`:

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (string rel in results)
        {
            var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
            Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
        }
    } catch (RequestFailedException rex) {
        Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
    }
}
```

Następnie Dodaj następujący kod na końcu `Main` metody, aby wywołać `ListRelationships` kod:

```csharp
//List the relationships
await ListRelationships(client, "sampleTwin-0");
```

W oknie polecenia Uruchom program z `dotnet run` . Powinna zostać wyświetlona lista wszystkich utworzonych relacji.

### <a name="query-digital-twins"></a>Bliźniaczych reprezentacji cyfrowe zapytania

Główną funkcją usługi Azure Digital bliźniaczych reprezentacji jest możliwość łatwego i wydajnego [wykonywania zapytań](concepts-query-language.md) na pytania dotyczące środowiska.

Ostatnia sekcja kodu do dodania w tym samouczku uruchamia zapytanie względem wystąpienia bliźniaczych reprezentacji Digital na platformie Azure. Zapytanie użyte w tym przykładzie zwraca wszystkie bliźniaczych reprezentacji cyfrowe w wystąpieniu.

Dodaj następujący kod na końcu `Main` metody:

```csharp
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    Console.WriteLine("---------------");
}
```

W oknie polecenia Uruchom program z `dotnet run` . Wszystkie bliźniaczych reprezentacji cyfrowe w tym wystąpieniu powinny być widoczne w danych wyjściowych.

## <a name="complete-code-example"></a>Pełny przykład kodu

W tym punkcie samouczka masz kompletną aplikację kliencką, która może wykonywać podstawowe działania związane z usługą Azure Digital bliźniaczych reprezentacji. W przypadku odwołania do kodu w programie *program.cs* jest wyświetlany następujący kod:

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Models;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string clientId = "<your-application-ID>";
            string tenantId = "<your-directory-ID>";
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
            var credentials = new InteractiveBrowserCredential(tenantId, clientId);
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            var typeList = new List<string>();
            string dtdl = File.ReadAllText("SampleModel.json");
            typeList.Add(dtdl);

            // Upload the model to the service
            try {
                await client.CreateModelsAsync(typeList);
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
            }
            // Read a list of models back from the service
            AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
            await foreach (ModelData md in modelDataList)
            {
                Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
            }

            // Initialize twin data
            BasicDigitalTwin twinData = new BasicDigitalTwin();
            twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
            twinData.CustomProperties.Add("data", $"Hello World!");
    
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    twinData.Id = $"{prefix}{i}";
                    await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
                    Console.WriteLine($"Created twin: {prefix}{i}");
                } catch(RequestFailedException rex) {
                    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
                }
            }

            // Connect the twins with relationships
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");

            //List the relationships
            await ListRelationships(client, "sampleTwin-0");

            // Run a query    
            AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
            await foreach (string twin in result)
            {
                object jsonObj = JsonSerializer.Deserialize<object>(twin);
                string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
                Console.WriteLine(prettyTwin);
                Console.WriteLine("---------------");
            }
        }

        public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = "contains"
            };
        
            try
            {
                string relId = $"{srcId}-contains->{targetId}";
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine("Created relationship successfully");
            }
            catch (RequestFailedException rex) {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
        }
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (string rel in results)
                {
                    var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
                    Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
                }
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
            }
        }

    }
}
```
## <a name="clean-up-resources"></a>Oczyszczanie zasobów
 
Wystąpienie używane w tym samouczku można ponownie wykorzystać w następnym samouczku, [samouczku: Eksplorowanie podstaw za pomocą przykładowej aplikacji klienckiej](tutorial-command-line-app.md). Jeśli planujesz przejść do następnego samouczka, możesz zachować tutaj skonfigurowane wystąpienie usługi Azure Digital bliźniaczych reprezentacji.
 
Jeśli zasoby utworzone w tym samouczku nie są już potrzebne, wykonaj następujące kroki, aby je usunąć.

Za pomocą [Azure Cloud Shell](https://shell.azure.com)można usunąć wszystkie zasoby platformy Azure w grupie zasobów za pomocą polecenia [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) . Spowoduje to usunięcie grupy zasobów i wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. 

Otwórz Azure Cloud Shell i uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie elementy, które zawiera.

```azurecli-interactive
az group delete --name <your-resource-group>
```

Następnie usuń Azure Active Directory rejestrację aplikacji utworzoną dla aplikacji klienckiej przy użyciu tego polecenia:

```azurecli
az ad app delete --id <your-application-ID>
```

Na koniec Usuń folder projektu utworzony na komputerze lokalnym.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzysz aplikację kliencką programu .NET Console od podstaw. Zapisano kod dla tej aplikacji klienckiej w celu wykonywania podstawowych akcji w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji.

Przejdź do następnego samouczka, aby poznać czynności, które możesz wykonać za pomocą takiej przykładowej aplikacji klienckiej: 

> [!div class="nextstepaction"]
> [Samouczek: Eksplorowanie podstaw za pomocą przykładowej aplikacji klienckiej](tutorial-command-line-app.md)

Możesz również dodać do kodu napisanego w tym samouczku, aby poznać więcej operacji zarządzania w artykułach z przewodnikiem lub zacząć od dokumentacji koncepcji, aby dowiedzieć się więcej o elementach, z którymi pracujesz w samouczku.
* [Instrukcje: Zarządzanie modelami niestandardowymi](how-to-manage-model.md)
* [Koncepcje: modele niestandardowe](concepts-models.md)
