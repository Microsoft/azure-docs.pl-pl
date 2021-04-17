---
title: Korzystanie z interfejsów API i zestawów SDK usługi Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Zobacz, jak pracować z interfejsami API Azure Digital Twins, w tym za pośrednictwem zestawu SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 21247f6b396cb1f7016c74cbec528149c0583724
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587208"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Korzystanie z interfejsów API i zestawów SDK usługi Azure Digital Twins

Azure Digital Twins jest wyposażony w interfejsy **API** płaszczyzny sterowania i interfejsy API płaszczyzny **danych** do zarządzania wystąpieniem i jego elementami. 
* Interfejsy API płaszczyzny sterowania są [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md) i obejmują operacje zarządzania zasobami, takie jak tworzenie i usuwanie wystąpienia. 
* Interfejsy API płaszczyzny danych są Azure Digital Twins API i są używane do operacji zarządzania danymi, takich jak zarządzanie modelami, bliźniaczymi reprezentacjiami i grafem.

Ten artykuł zawiera omówienie dostępnych interfejsów API i metod interakcji z nimi. Interfejsów API REST można używać bezpośrednio ze skojarzonymi z nimi elementami Swagger (za pomocą narzędzia takiego jak [Postman)](how-to-use-postman.md)lub za pośrednictwem zestawu SDK.

## <a name="overview-control-plane-apis"></a>Omówienie: interfejsy API płaszczyzny sterowania

Interfejsy API płaszczyzny sterowania są interfejsami [API](../azure-resource-manager/management/overview.md) arm używanymi do zarządzania Azure Digital Twins jako całością, dzięki czemu obejmują operacje takie jak tworzenie lub usuwanie całego wystąpienia. Będą one również służące do tworzenia i usuwania punktów końcowych.

Najnowsza wersja interfejsu API płaszczyzny sterowania to _**2020-12-01.**_

Aby użyć interfejsów API płaszczyzny sterowania:
* Interfejsy API można wywołać bezpośrednio, odwołując się do najnowszego folderu struktury Swagger w [repocie struktury Swagger płaszczyzny sterowania](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable). Ten folder zawiera również folder z przykładami, które pokazują użycie.
* Obecnie dostęp do zestawów SDK dla interfejsów API sterowania można uzyskać w...
  - [**.NET (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([odwołanie [wygenerowane automatycznie]](/dotnet/api/overview/azure/digitaltwins/management)) ([źródło](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [**Java**](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([odwołanie [wygenerowane automatycznie]](/java/api/overview/azure/digitaltwins)) ([źródło](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([źródło](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) [(źródło](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [**Go**](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt) [(źródło](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt))

Interfejsy API płaszczyzny sterowania można również wykonywać, korzystając Azure Digital Twins za pośrednictwem [interfejsu Azure Portal](https://portal.azure.com) interfejsu wiersza [polecenia.](how-to-use-cli.md)

## <a name="overview-data-plane-apis"></a>Przegląd: interfejsy API płaszczyzny danych

Interfejsy API płaszczyzny danych to Azure Digital Twins API używane do zarządzania elementami w ramach Azure Digital Twins danych. Obejmują one operacje takie jak tworzenie tras, przekazywanie modeli, tworzenie relacji i zarządzanie bliźniaczych reprezentacji. Można je ogólnie podzielić na następujące kategorie:
* **DigitalTwinModels —** kategoria DigitalTwinModels zawiera interfejsy API do zarządzania modelami w Azure Digital Twins wystąpienia. [](concepts-models.md) Działania związane z zarządzaniem obejmują przekazywanie, walidację, pobieranie i usuwanie modeli authored w języku DTDL.
* **DigitalTwins** — kategoria DigitalTwins zawiera interfejsy API, które [](concepts-twins-graph.md) umożliwiają deweloperom tworzenie, modyfikowanie i usuwanie bliźniaczych reprezentacji cyfrowych oraz ich relacji w Azure Digital Twins wystąpienia.
* **Zapytanie** — kategoria Zapytanie umożliwia deweloperom znajdowanie zestawów bliźniaczych reprezentacji cyfrowych [w grafie bliźniaczych reprezentacji w](how-to-query-graph.md) relacjach.
* **Trasy zdarzeń** — kategoria Trasy zdarzeń zawiera interfejsy API do rozsyłania danych za pośrednictwem systemu i usług podrzędnego. [](concepts-route-events.md)

Najnowsza wersja interfejsu API płaszczyzny danych to _**2020-10-31.**_

Aby użyć interfejsów API płaszczyzny danych:
* Interfejsy API można wywołać bezpośrednio, aby...
   - odwołanie do najnowszego folderu struktury Swagger w [repo programu Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)płaszczyzny danych . Ten folder zawiera również folder z przykładami, które pokazują użycie. 
   - wyświetlanie dokumentacji [interfejsu API](/rest/api/azure-digitaltwins/).
* Możesz użyć **zestawu SDK platformy .NET (C#).** Aby użyć zestawu SDK platformy .NET...
   - Możesz wyświetlić i dodać pakiet z pakietu NuGet: [Azure.DigitalTwins.Core.](https://www.nuget.org/packages/Azure.DigitalTwins.Core) 
   - Możesz wyświetlić dokumentację [referencyjną zestawu SDK](/dotnet/api/overview/azure/digitaltwins/client).
   - Źródło zestawu SDK, w tym folder przykładów, można znaleźć w witrynie GitHub: [Azure IoT Digital Twins biblioteki klienta dla platformy .NET.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) 
   - Szczegółowe informacje i przykłady użycia można znaleźć w sekcji [*.NET (C#) SDK (data plane)*](#net-c-sdk-data-plane) tego artykułu.
* Możesz użyć zestawu **Java SDK**. Aby użyć zestawu JAVA SDK...
   - Możesz wyświetlić i zainstalować pakiet z programu Maven: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - Możesz wyświetlić dokumentację [referencyjną zestawu SDK](/java/api/overview/azure/digitaltwins/client)
   - Źródło zestawu SDK można znaleźć w witrynie GitHub: [Biblioteka klienta usługi Azure IoT Digital Twins dla języka Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Możesz użyć zestawu **SDK języka JavaScript.** Aby użyć zestawu SDK języka JavaScript...
   - Pakiet można wyświetlić i zainstalować za pomocą narzędzia npm: [biblioteka klienta azure Azure Digital Twins Core dla języka JavaScript.](https://www.npmjs.com/package/@azure/digital-twins-core)
   - Możesz wyświetlić dokumentację [referencyjną zestawu SDK](/javascript/api/@azure/digital-twins-core/).
   - Źródło zestawu SDK można znaleźć w witrynie GitHub: [Biblioteka klienta Azure Azure Digital Twins Core dla języka JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* Możesz użyć zestawu **SDK języka Python.** Aby użyć zestawu SDK języka Python...
   - Pakiet można wyświetlić i zainstalować z witryny PyPi: [Biblioteka klienta azure Azure Digital Twins Core dla języka Python.](https://pypi.org/project/azure-digitaltwins-core/)
   - Możesz wyświetlić dokumentację [referencyjną zestawu SDK](/python/api/azure-digitaltwins-core/azure.digitaltwins.core).
   - Źródło zestawu SDK można znaleźć w witrynie GitHub: [Biblioteka klienta Azure Azure Digital Twins Core dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Zestaw SDK dla innego języka można wygenerować przy użyciu funkcji AutoRest. Postępuj zgodnie z instrukcjami [*w sekcji Instrukcje: Tworzenie niestandardowych zestawów SDK dla Azure Digital Twins za pomocą funkcji AutoRest.*](how-to-create-custom-sdks.md)

Interfejsy API płaszczyzny dat można również ćwiczyć, korzystając Azure Digital Twins interfejsu wiersza [polecenia](how-to-use-cli.md).

## <a name="net-c-sdk-data-plane"></a>Zestaw SDK platformy .NET (C#) (płaszczyzna danych)

Zestaw SDK Azure Digital Twins .NET (C#) jest częścią zestawu Azure SDK dla platformy .NET. Jest ona open source i jest oparta na interfejsach API Azure Digital Twins danych.

> [!NOTE]
> Aby uzyskać więcej informacji na temat projektowania zestawu SDK, zobacz ogólne zasady projektowania zestawów [Azure SDK](https://azure.github.io/azure-sdk/general_introduction.html) i specyficzne wytyczne dotyczące projektowania [platformy .NET.](https://azure.github.io/azure-sdk/dotnet_introduction.html)

Aby użyć zestawu SDK, dołącz pakiet NuGet **Azure.DigitalTwins.Core** do projektu. Potrzebna będzie również najnowsza wersja pakietu **Azure.Identity.** W Visual Studio można dodać te pakiety przy użyciu narzędzia NuGet Menedżer pakietów (dostępnego za pośrednictwem narzędzi *> NuGet Menedżer pakietów >* Zarządzaj pakietami NuGet dla rozwiązania). Alternatywnie możesz użyć narzędzia wiersza polecenia .NET z poleceniami dostępnymi w poniższych linkach pakietu NuGet, aby dodać je do projektu:
* [**Azure.DigitalTwins.Core.**](https://www.nuget.org/packages/Azure.DigitalTwins.Core) Jest to pakiet dla zestawu [SDK usługi Azure Digital Twins dla platformy .NET.](/dotnet/api/overview/azure/digitaltwins/client) 
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). Ta biblioteka zawiera narzędzia, które ułatwiają uwierzytelnianie na platformie Azure.

Aby uzyskać szczegółowe instrukcje korzystania z interfejsów API w praktyce, zobacz [*Samouczek: kodowanie aplikacji klienckiej.*](tutorial-code.md) 

### <a name="net-sdk-usage-examples"></a>Przykłady użycia zestawu SDK platformy .NET

Poniżej przedstawiono przykłady kodu ilustrujące użycie zestawu SDK platformy .NET.

Uwierzytelnianie względem usługi:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_basic":::

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

Przekaż model:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Lista modeli:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Tworzenie bliźniaczych reprezentacji:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Wykonywanie zapytań o bliźniacze reprezentacji i wykonywanie w pętli wyników:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

Zobacz [*Samouczek: kod aplikacji klienckiej,*](tutorial-code.md) aby uzyskać przewodnik po tym przykładowym kodzie aplikacji. 

Dodatkowe przykłady można również znaleźć w repozytorium [GitHub dla zestawu SDK platformy .NET (C#).](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)

#### <a name="serialization-helpers"></a>Pomocnicy serializacji

Pomocniki serializacji to funkcje pomocnika dostępne w zestawie SDK do szybkiego tworzenia lub deserializacji danych bliźniaczych reprezentacji w celu uzyskania dostępu do podstawowych informacji. Ponieważ podstawowe metody zestawu SDK domyślnie zwracają dane bliźniaczej reprezentacji w postaci danych JSON, pomocne może być dalsze rozbicie danych bliźniaczych za pomocą tych klas pomocników.

Dostępne klasy pomocników to:
* `BasicDigitalTwin`: ogólnie reprezentuje podstawowe dane bliźniaczej reprezentacji cyfrowej
* `BasicDigitalTwinComponent`: ogólnie reprezentuje składnik we `Contents` właściwościach elementu `BasicDigitalTwin`
* `BasicRelationship`: ogólnie reprezentuje podstawowe dane relacji
* `DigitalTwinsJsonPropertyName`: zawiera stałe ciągu do użycia w serializacji i deserializacji JSON dla niestandardowych cyfrowych typów bliźniaczych reprezentacji

##### <a name="deserialize-a-digital-twin"></a>Deserializowanie bliźniaczej reprezentacji cyfrowej

Zawsze można deserializować dane bliźniaczej reprezentacji przy użyciu wybranej biblioteki JSON, na przykład `System.Text.Json` lub `Newtonsoft.Json` . W przypadku podstawowego dostępu do bliźniaczej reprezentacji klasy pomocników mogą to ułatwić.

Klasa `BasicDigitalTwin` pomocnika zapewnia również dostęp do właściwości zdefiniowanych w bliźniaczej reprezentacji za pośrednictwem obiektu `Dictionary<string, object>` . Aby wyświetlić listę właściwości bliźniaczej reprezentacji, możesz użyć:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin":::

> [!NOTE]
> `BasicDigitalTwin` używa `System.Text.Json` atrybutów. Aby można było korzystać z klasy DigitalTwinsClient, należy zainicjować klienta przy użyciu konstruktora domyślnego lub, jeśli chcesz dostosować opcję serializatora, użyć interfejsu `BasicDigitalTwin` [JsonObjectSerializer.](/dotnet/api/azure.core.serialization.jsonobjectserializer?view=azure-dotnet&preserve-view=true) [](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient?view=azure-dotnet&preserve-view=true)

##### <a name="create-a-digital-twin"></a>Tworzenie bliźniaczej reprezentacji cyfrowej

Przy użyciu `BasicDigitalTwin` klasy można przygotować dane do utworzenia wystąpienia bliźniaczej reprezentacji:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Powyższy kod jest odpowiednikiem następującego wariantu "ręcznego":

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

##### <a name="deserialize-a-relationship"></a>Deserializuj relację

Zawsze możesz deserializować dane relacji do wybranego typu. Aby uzyskać podstawowy dostęp do relacji, użyj typu `BasicRelationship` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

Klasa `BasicRelationship` pomocnika zapewnia również dostęp do właściwości zdefiniowanych w relacji za pośrednictwem klasy `IDictionary<string, object>` . Aby wyświetlić listę właściwości, można użyć:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

##### <a name="create-a-relationship"></a>Tworzenie relacji

Przy użyciu `BasicRelationship` klasy można również przygotować dane do tworzenia relacji w wystąpieniu bliźniaczej reprezentacji:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

##### <a name="create-a-patch-for-twin-update"></a>Tworzenie poprawki dla aktualizacji bliźniaczej reprezentacji

Wywołania aktualizacji dla bliźniaczych reprezentacji i relacji używają [struktury poprawki JSON.](http://jsonpatch.com/) Aby utworzyć listy operacji poprawek JSON, możesz użyć `JsonPatchDocument` poniższego przykładu.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

## <a name="general-apisdk-usage-notes"></a>Ogólne informacje o użyciu interfejsu API/zestawu SDK

> [!NOTE]
> Należy pamiętać, Azure Digital Twins obecnie nie obsługuje udostępniania zasobów między źródłami **(CORS).** Aby uzyskać więcej informacji na temat strategii wpływu i rozwiązywania problemów, zobacz sekcję [*Cross-Origin Resource Sharing (CORS)*](concepts-security.md#cross-origin-resource-sharing-cors) artykułu *Concepts: Security for Azure Digital Twins solutions (Pojęcia:* zabezpieczenia dla rozwiązań Azure Digital Twins źródła).

Na poniższej liście przedstawiono dodatkowe szczegóły i ogólne wskazówki dotyczące korzystania z interfejsów API i zestawów SDK.

* Możesz użyć narzędzia testowania REST protokołu HTTP, takiego jak Postman, aby wykonać bezpośrednie wywołania do Azure Digital Twins API. Aby uzyskać więcej informacji na temat tego procesu, zobacz [*How-to: Make requests with Postman (3.0:3.0: 3.03.2.*](how-to-use-postman.md)Aby uzyskać więcej informacji na temat tego procesu, zobacz How-to
* Aby użyć zestawu SDK, należy utworzyć wystąpienia `DigitalTwinsClient` klasy . Konstruktor wymaga poświadczeń, które można uzyskać przy użyciu różnych metod uwierzytelniania w `Azure.Identity` pakiecie. Aby uzyskać więcej informacji `Azure.Identity` na temat usługi , zobacz [dokumentację przestrzeni nazw .](/dotnet/api/azure.identity) 
* Może się okazać, że jest to przydatne podczas rozpoczynania pracy, ale istnieje kilka innych opcji, w tym poświadczenia tożsamości zarządzanej , które prawdopodobnie będą używane do uwierzytelniania funkcji platformy Azure, które zostały ustawione przy użyciu tożsamości usługi zarządzanej `InteractiveBrowserCredential` Azure Digital Twins. [](/dotnet/api/azure.identity.interactivebrowsercredential) [](../app-service/overview-managed-identity.md?tabs=dotnet) Aby uzyskać więcej informacji `InteractiveBrowserCredential` na temat klasy , zobacz [dokumentację klasy](/dotnet/api/azure.identity.interactivebrowsercredential).
* Żądania do interfejsów API Azure Digital Twins wymagają użytkownika lub jednostki usługi, która jest częścią tej samej dzierżawy [usługi Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD), w której znajduje się Azure Digital Twins usługi. Aby zapobiec złośliwemu skanowaniu Azure Digital Twins końcowych, żądania z tokenami dostępu spoza dzierżawy pochodzącej zostaną zwrócone komunikat o błędzie "Nie znaleziono Sub-Domain 404". Ten błąd zostanie  zwrócony nawet wtedy, gdy użytkownik lub nazwa główna usługi otrzymała rolę Azure Digital Twins Data Owner lub Azure Digital Twins Data Reader za pośrednictwem współpracy [B2B](../active-directory/external-identities/what-is-b2b.md) w usłudze Azure AD. Aby uzyskać informacje na temat uzyskiwania dostępu do wielu dzierżaw, zobacz [*Jak napisać kod uwierzytelniania aplikacji.*](how-to-authenticate-client.md#authenticate-across-tenants)
* Wszystkie wywołania interfejsu API usługi są udostępniane jako funkcje członkowskie w `DigitalTwinsClient` klasie .
* Wszystkie funkcje usługi istnieją w wersjach synchronicznych i asynchronicznych.
* Wszystkie funkcje usługi zwracają wyjątek dla dowolnego stanu zwracanego 400 lub więcej. Upewnij się, że opakujesz wywołania `try` w sekcji i przechwyć co najmniej `RequestFailedExceptions` . Aby uzyskać więcej informacji o tym typie wyjątku, [zobacz tutaj](/dotnet/api/azure.requestfailedexception).
* Większość metod usługi zwraca `Response<T>` lub ( `Task<Response<T>>` dla wywołań asynchronicznych), gdzie `T` jest klasą obiektu zwracanego dla wywołania usługi. Klasa [`Response`](/dotnet/api/azure.response-1) hermetyzuje zwracany przez usługę i przedstawia wartości zwracane w polu `Value` .  
* Metody usług z wynikami stronicowania zwracają `Pageable<T>` wyniki lub `AsyncPageable<T>` jako wyniki. Aby uzyskać więcej informacji `Pageable<T>` na temat klasy , zobacz [tutaj](/dotnet/api/azure.pageable-1); aby uzyskać więcej informacji na temat klasy , `AsyncPageable<T>` zobacz [tutaj](/dotnet/api/azure.asyncpageable-1).
* Możesz iterować po stronicowych wynikach za pomocą `await foreach` pętli. Aby uzyskać więcej informacji na temat tego procesu, [zobacz tutaj](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* Podstawowy zestaw SDK to `Azure.Core` . Zapoznaj się z [dokumentacją przestrzeni nazw platformy Azure,](/dotnet/api/azure) aby uzyskać informacje na temat infrastruktury i typów zestawu SDK.


Metody usługi zwracają silnie typowane obiekty wszędzie tam, gdzie to możliwe. Jednak ponieważ Azure Digital Twins są oparte na modelach niestandardowych skonfigurowanych przez użytkownika w czasie wykonywania (za pośrednictwem modeli DTDL przekazanych do usługi), wiele interfejsów API usługi bierze i zwraca dane bliźniaczej reprezentacji w formacie JSON.

## <a name="monitor-api-metrics"></a>Monitorowanie metryk interfejsu API

Metryki interfejsu API, takie jak żądania, opóźnienia i szybkość niepowodzeń, można wyświetlić w [Azure Portal](https://portal.azure.com/). 

Na stronie głównej portalu wyszukaj wystąpienie Azure Digital Twins, aby uzyskać jego szczegóły. Wybierz opcję **Metryki** z menu Azure Digital Twins wystąpienia, aby wyprowadzić *stronę Metryki.*

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Zrzut ekranu przedstawiający stronę metryk dla Azure Digital Twins":::

W tym miejscu możesz wyświetlać metryki dla wystąpienia i tworzyć widoki niestandardowe.

## <a name="next-steps"></a>Następne kroki

Zobacz, jak kierować żądania do interfejsów API przy użyciu narzędzia Postman:
* [*How-to: Make requests with Postman*](how-to-use-postman.md)

Możesz też poćwiczyć korzystanie z zestawu SDK platformy .NET, tworząc aplikację klienutną za pomocą tego samouczka:
* [*Samouczek: kod aplikacji klienckiej*](tutorial-code.md)
