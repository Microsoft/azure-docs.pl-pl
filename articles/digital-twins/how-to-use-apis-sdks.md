---
title: Korzystanie z interfejsów API i zestawów SDK usługi Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Zobacz, jak korzystać z interfejsów API Digital bliźniaczych reprezentacji na platformie Azure, w tym za pomocą zestawu SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3724586ae9773e135905cddd2a9a33a3a6803ce5
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211791"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Korzystanie z interfejsów API i zestawów SDK usługi Azure Digital Twins

Usługa Azure Digital bliźniaczych reprezentacji jest wyposażona w **interfejsy API płaszczyzny kontroli** i **interfejsy API płaszczyzny danych** do zarządzania wystąpieniem i jego elementami. 
* Interfejsy API płaszczyzny kontroli to interfejsy API [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md) i obejmują operacje zarządzania zasobami, takie jak tworzenie i usuwanie wystąpienia. 
* Interfejsy API płaszczyzny danych to interfejsy API Digital bliźniaczych reprezentacji systemu Azure, które są używane do operacji zarządzania danymi, takich jak Zarządzanie modelami, bliźniaczych reprezentacji i wykres.

Ten artykuł zawiera omówienie dostępnych interfejsów API oraz metod współpracy z nimi. Możesz użyć interfejsów API REST bezpośrednio ze skojarzonymi z nimi strukturami Swagger (za pomocą narzędzia, takiego jak program [Poster](how-to-use-postman.md)) lub zestawu SDK.

## <a name="overview-control-plane-apis"></a>Przegląd: sterowanie płaszczyzną interfejsów API

Interfejsy API płaszczyzny kontroli są interfejsami API [ARM](../azure-resource-manager/management/overview.md) używanymi do zarządzania wystąpieniem usługi Azure Digital bliźniaczych reprezentacji jako całością, więc obejmują operacje, takie jak tworzenie lub usuwanie całego wystąpienia. Są one również używane do tworzenia i usuwania punktów końcowych.

Najbardziej aktualna wersja interfejsu API płaszczyzny kontroli to _**2020-12-01**_.

Aby używać interfejsów API płaszczyzny kontroli:
* Interfejsy API można wywoływać bezpośrednio, odwołując się do najnowszego folderu Swagger w [repozytorium Swagger płaszczyzny kontroli](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable). Ten folder zawiera również folder przykładów, które pokazują użycie.
* Obecnie możesz uzyskiwać dostęp do zestawów SDK dla formantów API w...
  - [**.NET (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([odwołanie [generowane automatycznie]](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true)) ([Źródło](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [**Java**](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([odwołanie [generowane automatycznie]](/java/api/overview/azure/digitaltwins)) ([Źródło](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([Źródło](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([Źródło](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [**Go**](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt/2020-10-31/digitaltwins) ([Źródło](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt/2020-10-31/digitaltwins))

Możesz również wykonywać interfejsy API płaszczyzny kontroli, współpracując z usługą Azure Digital bliźniaczych reprezentacji za pomocą [Azure Portal](https://portal.azure.com) i [interfejsu wiersza polecenia](how-to-use-cli.md).

## <a name="overview-data-plane-apis"></a>Przegląd: interfejsy API płaszczyzny danych

Interfejsy API płaszczyzny danych to interfejsy API Digital bliźniaczych reprezentacji platformy Azure służące do zarządzania elementami w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. Obejmują one operacje, takie jak tworzenie tras, przekazywanie modeli, tworzenie relacji i zarządzanie bliźniaczych reprezentacji. Mogą być szeroko podzielone na następujące kategorie:
* **DigitalTwinModels** — Kategoria DigitalTwinModels zawiera interfejsy API służące do zarządzania [modelami](concepts-models.md) w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. Działania związane z zarządzaniem obejmują przekazywanie, sprawdzanie poprawności, pobieranie i usuwanie modeli utworzonych w DTDL.
* **DigitalTwins** — Kategoria DigitalTwins zawiera interfejsy API, które umożliwiają deweloperom tworzenie, modyfikowanie i usuwanie [cyfrowych bliźniaczych reprezentacji](concepts-twins-graph.md) oraz ich relacji w wystąpieniu Digital bliźniaczych reprezentacji platformy Azure.
* **Zapytanie** — Kategoria zapytania umożliwia deweloperom [znalezienie zestawów bliźniaczych reprezentacji cyfrowych w grafie bliźniaczym](how-to-query-graph.md) między relacjami.
* **Trasy zdarzeń** — Kategoria trasy zdarzeń zawiera interfejsy API do [przesyłania danych](concepts-route-events.md)za pomocą systemu i usług podrzędnych.

Najbardziej aktualna wersja interfejsu API płaszczyzny danych to _**2020-10-31**_.

Aby używać interfejsów API płaszczyzny danych:
* Interfejsy API można wywoływać bezpośrednio, przez...
   - Odwoływanie się do najnowszego folderu Swagger w [repozytorium Swagger płaszczyzny danych](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Ten folder zawiera również folder przykładów, które pokazują użycie. 
   - Wyświetlanie [dokumentacji dotyczącej interfejsu API](/rest/api/azure-digitaltwins/).
* Możesz użyć **zestawu SDK platformy .NET (C#)**. Aby użyć zestawu SDK platformy .NET...
   - Możesz wyświetlić i dodać pakiet z programu NuGet: [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - można wyświetlić [dokumentację referencyjną zestawu SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).
   - Źródło zestawu SDK, w tym folder przykładów, można znaleźć w witrynie GitHub: [Biblioteka klienta usługi Azure IoT Digital bliźniaczych reprezentacji dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - Aby wyświetlić szczegółowe informacje i przykłady użycia, można kontynuować sekcję [*zestawu SDK platformy .NET (C#)*](#net-c-sdk-data-plane) w tym artykule.
* Możesz użyć **zestawu SDK języka Java**. Aby użyć zestawu Java SDK...
   - Możesz wyświetlić i zainstalować pakiet z Maven: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - można wyświetlić [dokumentację referencyjną zestawu SDK](/java/api/overview/azure/digitaltwins/client)
   - Źródło zestawu SDK można znaleźć w usłudze GitHub: [Biblioteka klienta usługi Azure IoT Digital bliźniaczych reprezentacji dla języka Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Możesz użyć **zestawu SDK języka JavaScript**. Aby użyć zestawu SDK języka JavaScript...
   - Możesz wyświetlić i zainstalować pakiet z npm: [Azure Azure Digital bliźniaczych reprezentacji Core Client Library for JavaScript](https://www.npmjs.com/package/@azure/digital-twins-core).
   - można wyświetlić [dokumentację referencyjną zestawu SDK](/javascript/api/@azure/digital-twins-core/).
   - Źródło zestawu SDK można znaleźć w usłudze GitHub: [podstawowa Biblioteka kliencka usługi Azure Azure Digital bliźniaczych reprezentacji dla języka JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* Możesz użyć **zestawu SDK języka Python**. Aby użyć zestawu SDK języka Python...
   - Możesz wyświetlić i zainstalować pakiet z PyPi: [Azure Azure Digital bliźniaczych reprezentacji Core Client Library dla języka Python](https://pypi.org/project/azure-digitaltwins-core/).
   - można wyświetlić [dokumentację referencyjną zestawu SDK](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true).
   - Źródło zestawu SDK można znaleźć w usłudze GitHub: [podstawowa Biblioteka kliencka usługi Azure Azure Digital bliźniaczych reprezentacji dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Zestaw SDK dla innego języka można wygenerować przy użyciu funkcji AutoRest. Postępuj zgodnie z instrukcjami podanymi w temacie [*How to: Create Custom SDK for Azure Digital bliźniaczych reprezentacji with AutoRest*](how-to-create-custom-sdks.md).

Możesz również wykonywać interfejsy API płaszczyzny danych, współpracując z usługą Azure Digital bliźniaczych reprezentacji za pomocą [interfejsu wiersza polecenia](how-to-use-cli.md).

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (płaszczyzna danych)

Zestaw SDK platformy Azure Digital bliźniaczych reprezentacji .NET (C#) jest częścią zestawu Azure SDK dla platformy .NET. Jest to element Open Source, który jest oparty na interfejsie API płaszczyzny danych Digital bliźniaczych reprezentacji na platformie Azure.

> [!NOTE]
> Aby uzyskać więcej informacji na temat projektowania zestawu SDK, zobacz Ogólne [zasady projektowania dotyczące zestawów SDK platformy Azure](https://azure.github.io/azure-sdk/general_introduction.html) i szczegółowe [wskazówki dotyczące projektowania platformy .NET](https://azure.github.io/azure-sdk/dotnet_introduction.html).

Aby użyć zestawu SDK, Dołącz pakiet NuGet **Azure. DigitalTwins. Core** z projektem. Potrzebna będzie również Najnowsza wersja pakietu **Azure. Identity** . W programie Visual Studio można dodać te pakiety przy użyciu Menedżera pakietów NuGet (dostępne za pośrednictwem *narzędzi > menedżerze pakietów nuget > zarządzanie pakietami NuGet dla rozwiązania*). Alternatywnie można użyć narzędzia wiersza polecenia platformy .NET z poleceniami znajdującymi się w poniższych linkach pakietów NuGet, aby dodać je do projektu:
* [**Azure. DigitalTwins. Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Jest to pakiet dla [zestawu SDK Digital bliźniaczych reprezentacji systemu Azure dla platformy .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 
* [**Azure. Identity**](https://www.nuget.org/packages/Azure.Identity). Ta biblioteka zawiera narzędzia, które ułatwiają uwierzytelnianie na platformie Azure.

Aby zapoznać się ze szczegółowym opisem użycia interfejsów API w programie, zobacz [*Samouczek: Code a Client App*](tutorial-code.md). 

### <a name="net-sdk-usage-examples"></a>Przykłady użycia zestawu SDK dla platformy .NET

Oto przykłady kodu ilustrujące użycie zestawu .NET SDK.

Uwierzytelnianie względem usługi:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_basic":::

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

Przekaż model:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Listy modeli:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Utwórz bliźniaczych reprezentacji:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Zapytania bliźniaczych reprezentacji i pętle w wyniku:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

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

`BasicDigitalTwin`Klasa pomocnika zapewnia również dostęp do właściwości zdefiniowanych na sznurze, za pomocą `Dictionary<string, object>` . Aby wyświetlić listę właściwości sznurka, można użyć:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin":::

##### <a name="create-a-digital-twin"></a>Tworzenie dwucyfrowej dwuosiowej

Korzystając z `BasicDigitalTwin` klasy, można przygotować dane do tworzenia wystąpienia z przędzą:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Powyższy kod jest odpowiednikiem następującej "ręcznej" wariantu:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

##### <a name="deserialize-a-relationship"></a>Deserializacja relacji

Zawsze możesz deserializować dane relacji do wybranego typu. Aby uzyskać podstawowy dostęp do relacji, należy użyć typu `BasicRelationship` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

`BasicRelationship`Klasa pomocnika zapewnia również dostęp do właściwości zdefiniowanych w relacji za pomocą `IDictionary<string, object>` . Aby wyświetlić właściwości, można użyć:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

##### <a name="create-a-relationship"></a>Tworzenie relacji

Korzystając z `BasicRelationship` klasy, można również przygotować dane do tworzenia relacji w wystąpieniu bliźniaczych:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

##### <a name="create-a-patch-for-twin-update"></a>Utwórz poprawkę dla aktualizacji bliźniaczych

Aktualizacje wywołań bliźniaczych reprezentacji i Relationships używają struktury [poprawek JSON](http://jsonpatch.com/) . Aby utworzyć listę operacji poprawek w formacie JSON, można użyć, `JsonPatchDocument` jak pokazano poniżej.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

## <a name="general-apisdk-usage-notes"></a>Ogólne informacje o użyciu interfejsu API/zestawu SDK

> [!NOTE]
> Należy pamiętać, że usługa Azure Digital bliźniaczych reprezentacji obecnie nie obsługuje **udostępniania zasobów między źródłami (CORS)**. Aby uzyskać więcej informacji na temat strategii wpływu i rozwiązywania problemów, zobacz sekcję dotyczącą [*współużytkowania zasobów między źródłami (CORS, cross-Origin Resource Sharing)*](concepts-security.md#cross-origin-resource-sharing-cors) *koncepcji: Security for Azure Digital bliźniaczych reprezentacji Solutions*.

Poniższa lista zawiera dodatkowe szczegółowe informacje i ogólne wskazówki dotyczące korzystania z interfejsów API i zestawów SDK.

* Możesz użyć narzędzia do testowania HTTP REST, takiego jak Poster, aby nawiązywać bezpośrednie wywołania interfejsów API Digital bliźniaczych reprezentacji platformy Azure. Aby uzyskać więcej informacji o tym procesie, zobacz [*How to: wykonywanie żądań za pomocą programu Poster*](how-to-use-postman.md).
* Aby użyć zestawu SDK, Utwórz wystąpienie `DigitalTwinsClient` klasy. Konstruktor wymaga poświadczeń, które można uzyskać przy użyciu różnych metod uwierzytelniania w `Azure.Identity` pakiecie. Więcej informacji `Azure.Identity` można znaleźć w dokumentacji dotyczącej [przestrzeni nazw](/dotnet/api/azure.identity?preserve-view=true&view=azure-dotnet). 
* Może się to okazać `InteractiveBrowserCredential` przydatne podczas rozpoczynania pracy, ale istnieje kilka innych opcji, w tym poświadczenia [zarządzanej tożsamości](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet), które prawdopodobnie będą używane do uwierzytelniania usługi [Azure Functions w](../app-service/overview-managed-identity.md?tabs=dotnet) usłudze Azure Digital bliźniaczych reprezentacji. Więcej informacji `InteractiveBrowserCredential` można znaleźć w [dokumentacji swojej klasy](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet).
* Wszystkie wywołania interfejsu API usługi są udostępniane jako funkcje członkowskie w `DigitalTwinsClient` klasie.
* Wszystkie funkcje usługi istnieją w wersjach synchronicznych i asynchronicznych.
* Wszystkie funkcje usługi zwracają wyjątek dla dowolnego stanu powrotu 400 lub nowszego. Pamiętaj, aby zawinąć wywołania do `try` sekcji i przechwycić co najmniej `RequestFailedExceptions` . Aby uzyskać więcej informacji na temat tego typu wyjątku, zobacz [tutaj](/dotnet/api/azure.requestfailedexception?preserve-view=true&view=azure-dotnet).
* Większość metod usługi zwraca `Response<T>` lub ( `Task<Response<T>>` dla wywołań asynchronicznych), gdzie `T` jest klasą obiektu zwrotnego dla wywołania usługi. [`Response`](/dotnet/api/azure.response-1?preserve-view=true&view=azure-dotnet)Klasa hermetyzuje zwracaną usługę i prezentuje wartości zwracane w `Value` polu.  
* Metody obsługi z wynikami stronicowania zwracają `Pageable<T>` lub `AsyncPageable<T>` jako wyniki. Aby uzyskać więcej informacji na temat `Pageable<T>` klasy, zobacz [tutaj](/dotnet/api/azure.pageable-1?preserve-view=true&view=azure-dotnet). Aby uzyskać więcej informacji `AsyncPageable<T>` , zobacz [tutaj](/dotnet/api/azure.asyncpageable-1?preserve-view=true&view=azure-dotnet).
* Można wykonać iterację w wynikach ze strony przy użyciu `await foreach` pętli. Aby uzyskać więcej informacji o tym procesie, zobacz [tutaj](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* Podstawowy zestaw SDK to `Azure.Core` . Zobacz [dokumentację przestrzeni nazw platformy Azure](/dotnet/api/azure?preserve-view=true&view=azure-dotnet) , aby uzyskać informacje na temat infrastruktury i typów zestawu SDK.

Metody usługi zwracają obiekty silnie wpisane, wszędzie tam, gdzie to możliwe. Jednak ponieważ usługa Azure Digital bliźniaczych reprezentacji opiera się na modelach niestandardowych skonfigurowanych przez użytkownika w czasie wykonywania (za pośrednictwem modeli DTDL przekazanych do usługi), wiele interfejsów API usługi pobiera i zwraca dane z sznurka w formacie JSON.

## <a name="monitor-api-metrics"></a>Monitorowanie metryk interfejsu API

W [Azure Portal](https://portal.azure.com/)mogą być wyświetlane METRYKI interfejsu API, takie jak żądania, opóźnienia i częstotliwość niepowodzeń. 

Na stronie głównej portalu Wyszukaj wystąpienie usługi Azure Digital bliźniaczych reprezentacji, aby uzyskać szczegółowe informacje. Wybierz opcję **metryki** z menu wystąpienia usługi Azure Digital bliźniaczych reprezentacji, aby wyświetlić stronę *metryk* .

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Zrzut ekranu przedstawiający stronę metryki usługi Azure Digital bliźniaczych reprezentacji":::

W tym miejscu można wyświetlić metryki dla wystąpienia i utworzyć widoki niestandardowe.

## <a name="next-steps"></a>Następne kroki

Zobacz, jak wykonywać bezpośrednie żądania do interfejsów API za pomocą programu Poster:
* [*Instrukcje: wykonywanie żądań za pomocą programu Poster*](how-to-use-postman.md)

Można też użyć zestawu .NET SDK, tworząc aplikację kliencką w tym samouczku:
* [*Samouczek: kod aplikacji klienckiej*](tutorial-code.md)