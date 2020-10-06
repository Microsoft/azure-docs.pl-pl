---
title: Korzystanie z interfejsów API i zestawów SDK usługi Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Zobacz, jak korzystać z interfejsów API Digital bliźniaczych reprezentacji na platformie Azure, w tym za pomocą zestawu SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2af4f2e2148c96a6059836f861cf56d4a4ac005b
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766180"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Korzystanie z interfejsów API i zestawów SDK usługi Azure Digital Twins

Usługa Azure Digital bliźniaczych reprezentacji jest wyposażona w **interfejsy API płaszczyzny kontroli** i **interfejsy API płaszczyzny danych** do zarządzania wystąpieniem i jego elementami. 
* Interfejsy API płaszczyzny kontroli to interfejsy API [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) i obejmują operacje zarządzania zasobami, takie jak tworzenie i usuwanie wystąpienia. 
* Interfejsy API płaszczyzny danych to interfejsy API Digital bliźniaczych reprezentacji systemu Azure, które są używane do operacji zarządzania danymi, takich jak Zarządzanie modelami, bliźniaczych reprezentacji i wykres.

Ten artykuł zawiera omówienie dostępnych interfejsów API oraz metod współpracy z nimi. Możesz użyć interfejsów API REST bezpośrednio ze skojarzonymi z nimi strukturami Swagger lub za pomocą zestawu SDK.

## <a name="overview-control-plane-apis"></a>Przegląd: sterowanie płaszczyzną interfejsów API

Interfejsy API płaszczyzny kontroli są interfejsami API [ARM](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) używanymi do zarządzania wystąpieniem usługi Azure Digital bliźniaczych reprezentacji jako całością, więc obejmują operacje, takie jak tworzenie lub usuwanie całego wystąpienia. Są one również używane do tworzenia i usuwania punktów końcowych.

Najbardziej aktualna wersja interfejsu API płaszczyzny kontroli dla publicznej wersji zapoznawczej to _**2020-03-01 — wersja zapoznawcza**_.

Aby używać interfejsów API płaszczyzny kontroli:
* Interfejsy API można wywoływać bezpośrednio, odwołując się do najnowszego [folderu Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins). To repozytorium zawiera również folder przykładów, które pokazują użycie.
* Obecnie możesz uzyskiwać dostęp do zestawów SDK dla formantów API w...
  - [.NET (C#)](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/1.0.0-preview.1) ([Źródło](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins)) ([odwołanie [generowane automatycznie]](https://docs.microsoft.com/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet-preview&preserve-view=true))
  - [Java](https://search.maven.org/artifact/com.microsoft.azure.digitaltwins.v2020_03_01_preview/azure-mgmt-digitaltwins) ([Źródło](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins)) ([odwołanie [generowane automatycznie]](https://docs.microsoft.com/java/api/overview/azure/digitaltwins/management?view=azure-java-preview&preserve-view=true))
  - [JavaScript](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([Źródło](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [Python](https://pypi.org/project/azure-mgmt-digitaltwins/) ([Źródło](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [Przejdź do źródła](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/digitaltwins/mgmt/2020-03-01-preview/digitaltwins)

Możesz również wykonywać interfejsy API płaszczyzny kontroli, współpracując z usługą Azure Digital bliźniaczych reprezentacji za pomocą [Azure Portal](https://portal.azure.com) i [interfejsu wiersza polecenia](how-to-use-cli.md).

## <a name="overview-data-plane-apis"></a>Przegląd: interfejsy API płaszczyzny danych

Interfejsy API płaszczyzny danych to interfejsy API Digital bliźniaczych reprezentacji platformy Azure służące do zarządzania elementami w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. Obejmują one operacje, takie jak tworzenie tras, przekazywanie modeli, tworzenie relacji i zarządzanie bliźniaczych reprezentacji. Mogą być szeroko podzielone na następujące kategorie:
* **DigitalTwinsModels** — Kategoria DigitalTwinsModels zawiera interfejsy API służące do zarządzania [modelami](concepts-models.md) w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. Działania związane z zarządzaniem obejmują przekazywanie, sprawdzanie poprawności, pobieranie i usuwanie modeli utworzonych w DTDL.
* **DigitalTwins** — Kategoria DigitalTwins zawiera interfejsy API, które umożliwiają deweloperom tworzenie, modyfikowanie i usuwanie [cyfrowych bliźniaczych reprezentacji](concepts-twins-graph.md) oraz ich relacji w wystąpieniu Digital bliźniaczych reprezentacji platformy Azure.
* **Zapytanie** — Kategoria zapytania umożliwia deweloperom [znalezienie zestawów bliźniaczych reprezentacji cyfrowych w grafie bliźniaczym](how-to-query-graph.md) między relacjami.
* **EventRoutes** — Kategoria EventRoutes zawiera interfejsy API służące do [przesyłania danych](concepts-route-events.md)za pomocą systemu i usług podrzędnych.

Najbardziej aktualna wersja interfejsu API płaszczyzny danych dla publicznej wersji zapoznawczej to _**2020-05-31 — wersja zapoznawcza**_. Wersja interfejsu API _2020-03-01-Preview_ dla operacji płaszczyzny danych jest obecnie przestarzała.

Aby używać interfejsów API płaszczyzny danych:
* Interfejsy API można wywoływać bezpośrednio, przez...
   - Odwoływanie się do najnowszego [folderu Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). To repozytorium zawiera również folder przykładów, które pokazują użycie. 
   - Wyświetlanie [dokumentacji dotyczącej interfejsu API](https://docs.microsoft.com/rest/api/azure-digitaltwins/).
* Możesz użyć zestawu SDK **platformy .NET (C#)** . Aby użyć zestawu SDK platformy .NET...
   - Możesz wyświetlić i dodać pakiet z programu NuGet: [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - Źródło zestawu SDK, w tym folder przykładów, można znaleźć w witrynie GitHub: [Biblioteka klienta usługi Azure IoT Digital bliźniaczych reprezentacji dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - można wyświetlić [dokumentację referencyjną zestawu SDK](https://docs.microsoft.com/dotnet/api/overview/azure/digitaltwins?view=azure-dotnet-preview&preserve-view=true).
   - Aby wyświetlić szczegółowe informacje i przykłady użycia, można kontynuować sekcję [zestawu SDK platformy .NET (C#)](#net-c-sdk-data-plane) w tym artykule.
* Możesz użyć zestawu SDK **języka JavaScript** . Aby użyć zestawu SDK języka JavaScript...
   - Możesz wyświetlić i zainstalować pakiet z npm: [Azure Digital bliźniaczych reprezentacji Client Library for JavaScript](https://www.npmjs.com/package/@azure/digital-twins/v/1.0.0-preview.1).
   - można wyświetlić [dokumentację referencyjną zestawu SDK](https://docs.microsoft.com/javascript/api/@azure/digital-twins/?view=azure-node-latest&preserve-view=true).
* Możesz użyć zestawu SDK **języka Java** . Aby użyć zestawu Java SDK...
   - Możesz wyświetlić i zainstalować pakiet z Maven: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0-beta.1/jar)
   - można wyświetlić [dokumentację referencyjną zestawu SDK](https://docs.microsoft.com/java/api/overview/azure/digitaltwins/client?view=azure-java-preview&preserve-view=true)
* Zestaw SDK dla innego języka można wygenerować przy użyciu funkcji AutoRest. Postępuj zgodnie z instrukcjami podanymi w temacie [*How to: Create Custom SDK for Azure Digital bliźniaczych reprezentacji with AutoRest*](how-to-create-custom-sdks.md).

Możesz również wykonywać interfejsy API płaszczyzny danych, współpracując z usługą Azure Digital bliźniaczych reprezentacji za pomocą [interfejsu wiersza polecenia](how-to-use-cli.md).

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (płaszczyzna danych)

Zestaw SDK platformy Azure Digital bliźniaczych reprezentacji .NET (C#) jest częścią zestawu Azure SDK dla platformy .NET. Jest to element Open Source, który jest oparty na interfejsie API płaszczyzny danych Digital bliźniaczych reprezentacji na platformie Azure.

> [!NOTE]
> Aby uzyskać więcej informacji na temat projektowania zestawu SDK, zobacz Ogólne [zasady projektowania dotyczące zestawów SDK platformy Azure](https://azure.github.io/azure-sdk/general_introduction.html) i szczegółowe [wskazówki dotyczące projektowania platformy .NET](https://azure.github.io/azure-sdk/dotnet_introduction.html).

Aby użyć zestawu SDK, Dołącz pakiet NuGet **Azure. DigitalTwins. Core** z projektem. Potrzebna będzie również Najnowsza wersja pakietu **Azure. Identity** .

* W programie Visual Studio można dodać pakiety za pomocą Menedżera pakietów NuGet (dostępne za pomocą *narzędzi > menedżerze pakietów nuget > zarządzanie pakietami NuGet dla rozwiązania*). 
* Za pomocą narzędzia wiersza polecenia platformy .NET można uruchomić polecenie:

    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

Aby zapoznać się ze szczegółowym opisem użycia interfejsów API w programie, zobacz [*Samouczek: Code a Client App*](tutorial-code.md). 

### <a name="net-sdk-usage-examples"></a>Przykłady użycia zestawu SDK dla platformy .NET

Oto przykłady kodu ilustrujące użycie zestawu .NET SDK.

Uwierzytelnianie względem usługi:

```csharp
// Authenticate against the service and create a client
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
```

Przekaż model i modele list:

```csharp
// Upload a model
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
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
```

Utwórz bliźniaczych reprezentacji zapytania:

```csharp
// Initialize twin metadata
BasicDigitalTwin twinData = new BasicDigitalTwin();

twinData.Id = $"firstTwin";
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", "Hello World!");
try {
    await client.CreateDigitalTwinAsync("firstTwin", JsonSerializer.Serialize(twinData));
} catch(RequestFailedException rex) {
    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
}
 
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    // Use JSON deserialization to pretty-print
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    // Or use BasicDigitalTwin for convenient property access
    BasicDigitalTwin btwin = JsonSerializer.Deserialize<BasicDigitalTwin>(twin);
}
```

Zobacz [*Samouczek: kod aplikacji klienckiej*](tutorial-code.md) w celu przechodzenia do tego kodu przykładowej aplikacji. 

Możesz również znaleźć dodatkowe przykłady w [repozytorium GitHub dla zestawu SDK platformy .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples).

#### <a name="serialization-helpers"></a>Pomocnicy serializacji

Pomocnicy serializacji to funkcje pomocnicze dostępne w ramach zestawu SDK umożliwiające szybkie tworzenie i deserializacja danych bliźniaczych w celu uzyskania dostępu do podstawowych informacji. Ponieważ podstawowe metody zestawu SDK zwracają dane dwuosiowe jako plik JSON domyślnie, pomocne może być użycie tych klas pomocniczych w celu dalszej przerwy w działaniu danych.

Dostępne są następujące klasy pomocnika:
* `BasicDigitalTwin`: Reprezentuje podstawowe dane dwuosiowa cyfra
* `BasicRelationship`: Reprezentuje podstawowe dane relacji
* `UpdateOperationUtility`: Reprezentuje informacje o poprawkach JSON używane w wywołaniach aktualizacji
* `WriteableProperty`: Reprezentuje metadane właściwości

##### <a name="deserialize-a-digital-twin"></a>Deserializacja wieloosiowa

Zawsze możesz deserializować dane przędzy przy użyciu wybranej biblioteki JSON, takiej jak `System.Test.Json` lub `Newtonsoft.Json` . W przypadku podstawowego dostępu do sznurka klasy pomocników sprawiają, że jest to nieco bardziej wygodne.

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
```

`BasicDigitalTwin`Klasa pomocnika zapewnia również dostęp do właściwości zdefiniowanych na sznurze, za pomocą `Dictionary<string, object>` . Aby wyświetlić listę właściwości sznurka, można użyć:

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-digital-twin"></a>Tworzenie dwucyfrowej dwuosiowej

Korzystając z `BasicDigitalTwin` klasy, można przygotować dane do tworzenia wystąpienia z przędzą:

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

Powyższy kod jest odpowiednikiem następującej "ręcznej" wariantu:

```csharp
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:example:Room;1"}
};
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", 25.0 }
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

##### <a name="deserialize-a-relationship"></a>Deserializacja relacji

Zawsze możesz deserializować dane relacji przy użyciu wybranej biblioteki JSON, takiej jak `System.Test.Json` lub `Newtonsoft.Json` . W przypadku podstawowego dostępu do relacji klasy pomocników sprawiają, że jest to nieco bardziej wygodne.

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
```

`BasicRelationship`Klasa pomocnika zapewnia również dostęp do właściwości zdefiniowanych w relacji za pomocą `Dictionary<string, object>` . Aby wyświetlić właściwości, można użyć:

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
foreach (string prop in rel.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-relationship"></a>Tworzenie relacji

Korzystając z `BasicRelationship` klasy, można również przygotować dane do tworzenia relacji w wystąpieniu bliźniaczych:

```csharp
BasicRelationship rel = new BasicRelationship();
rel.TargetId = "myTargetTwin";
rel.Name = "contains"; // a relationship with this name must be defined in the model
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("active", true);
rel.CustomProperties = props;
client.CreateRelationship("mySourceTwin", "rel001", JsonSerializer.Serialize<BasicRelationship>(rel));
```

##### <a name="create-a-patch-for-twin-update"></a>Utwórz poprawkę dla aktualizacji bliźniaczych

Aktualizacje wywołań bliźniaczych reprezentacji i Relationships używają struktury [poprawek JSON](http://jsonpatch.com/) . Aby utworzyć listę operacji poprawek w formacie JSON, można użyć `UpdateOperationsUtility` klasy, jak pokazano poniżej.

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
uou.AppendAddOp("/myComponent/Property", "Hello");
// Un-set a property
uou.AppendRemoveOp("/Humidity");
client.UpdateDigitalTwin("myTwin", uou.Serialize());
```

## <a name="general-apisdk-usage-notes"></a>Ogólne informacje o użyciu interfejsu API/zestawu SDK

> [!NOTE]
> Należy pamiętać, że w trakcie okresu zapoznawczego usługa Azure Digital bliźniaczych reprezentacji nie obsługuje **udostępniania zasobów między źródłami (CORS)**. W związku z tym, jeśli wywołujesz interfejs API REST z aplikacji przeglądarki, interfejsu [API Management (APIM)](../api-management/api-management-key-concepts.md) lub łącznika [aplikacji zaawansowanych](https://docs.microsoft.com/powerapps/powerapps-overview) , może zostać wyświetlony błąd zasad.
> Aby rozwiązać ten problem, można wykonać jedną z następujących czynności:
> * Przeplataj nagłówek CORS `Access-Control-Allow-Origin` z wiadomości. Ten nagłówek wskazuje, czy można udostępnić odpowiedź. 
> * Alternatywnie można utworzyć serwer proxy CORS i wprowadzić żądanie interfejsu API REST usługi Azure Digital bliźniaczych reprezentacji. 

Poniższa lista zawiera dodatkowe szczegółowe informacje i ogólne wskazówki dotyczące korzystania z interfejsów API i zestawów SDK.

* Aby użyć zestawu SDK, Utwórz wystąpienie `DigitalTwinsClient` klasy. Konstruktor wymaga poświadczeń, które można uzyskać przy użyciu różnych metod uwierzytelniania w `Azure.Identity` pakiecie. Więcej informacji `Azure.Identity` można znaleźć w dokumentacji dotyczącej [przestrzeni nazw](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true). 
* Może się to okazać `InteractiveBrowserCredential` przydatne podczas rozpoczynania pracy, ale istnieje kilka innych opcji, w tym poświadczenia [zarządzanej tożsamości](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true), które prawdopodobnie będą używane do uwierzytelniania usługi [Azure Functions w](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet) usłudze Azure Digital bliźniaczych reprezentacji. Więcej informacji `InteractiveBrowserCredential` można znaleźć w [dokumentacji swojej klasy](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true).
* Wszystkie wywołania interfejsu API usługi są udostępniane jako funkcje członkowskie w `DigitalTwinsClient` klasie.
* Wszystkie funkcje usługi istnieją w wersjach synchronicznych i asynchronicznych.
* Wszystkie funkcje usługi zwracają wyjątek dla dowolnego stanu powrotu 400 lub nowszego. Pamiętaj, aby zawinąć wywołania do `try` sekcji i przechwycić co najmniej `RequestFailedExceptions` . Aby uzyskać więcej informacji na temat tego typu wyjątku, zobacz [tutaj](https://docs.microsoft.com/dotnet/api/azure.requestfailedexception?view=azure-dotnet&preserve-view=true).
* Większość metod usługi zwraca `Response<T>` lub ( `Task<Response<T>>` dla wywołań asynchronicznych), gdzie `T` jest klasą obiektu zwrotnego dla wywołania usługi. [`Response`](https://docs.microsoft.com/dotnet/api/azure.response-1?view=azure-dotnet&preserve-view=true)Klasa hermetyzuje zwracaną usługę i prezentuje wartości zwracane w `Value` polu.  
* Metody obsługi z wynikami stronicowania zwracają `Pageable<T>` lub `AsyncPageable<T>` jako wyniki. Aby uzyskać więcej informacji na temat `Pageable<T>` klasy, zobacz [tutaj](https://docs.microsoft.com/dotnet/api/azure.pageable-1?view=azure-dotnet-preview&preserve-view=true). Aby uzyskać więcej informacji `AsyncPageable<T>` , zobacz [tutaj](https://docs.microsoft.com/dotnet/api/azure.asyncpageable-1?view=azure-dotnet-preview&preserve-view=true).
* Można wykonać iterację w wynikach ze strony przy użyciu `await foreach` pętli. Aby uzyskać więcej informacji o tym procesie, zobacz [tutaj](https://docs.microsoft.com/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* Podstawowy zestaw SDK to `Azure.Core` . Zobacz [dokumentację przestrzeni nazw platformy Azure](https://docs.microsoft.com/dotnet/api/azure?view=azure-dotnet-preview&preserve-view=true) , aby uzyskać informacje na temat infrastruktury i typów zestawu SDK.

Metody usługi zwracają obiekty silnie wpisane, wszędzie tam, gdzie to możliwe. Jednak ponieważ usługa Azure Digital bliźniaczych reprezentacji opiera się na modelach niestandardowych skonfigurowanych przez użytkownika w czasie wykonywania (za pośrednictwem modeli DTDL przekazanych do usługi), wiele interfejsów API usługi pobiera i zwraca dane z sznurka w formacie JSON.

## <a name="monitor-api-metrics"></a>Monitorowanie metryk interfejsu API

W [Azure Portal](https://portal.azure.com/)mogą być wyświetlane METRYKI interfejsu API, takie jak żądania, opóźnienia i częstotliwość niepowodzeń. 

Na stronie głównej portalu Wyszukaj wystąpienie usługi Azure Digital bliźniaczych reprezentacji, aby uzyskać szczegółowe informacje. Wybierz opcję **metryki** z menu wystąpienia usługi Azure Digital bliźniaczych reprezentacji, aby wyświetlić stronę *metryk* .

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Zrzut ekranu przedstawiający stronę metryki usługi Azure Digital bliźniaczych reprezentacji":::

W tym miejscu można wyświetlić metryki dla wystąpienia i utworzyć widoki niestandardowe.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z tematem jak używać interfejsów API do konfigurowania wystąpienia i uwierzytelniania usługi Azure Digital bliźniaczych reprezentacji:
* [*Instrukcje: Konfigurowanie wystąpienia i uwierzytelniania*](how-to-set-up-instance-portal.md)

Lub zapoznaj się z instrukcjami, aby utworzyć aplikację kliencką, taką jak ta, która jest używana w tym przewodniku:
* [*Samouczek: kod aplikacji klienckiej*](tutorial-code.md)
