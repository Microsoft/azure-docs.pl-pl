---
title: Tworzenie niestandardowych zestawów SDK dla usługi Azure Digital bliźniaczych reprezentacji za pomocą AutoRest
titleSuffix: Azure Digital Twins
description: Zobacz, jak generować niestandardowe zestawy SDK, aby używać usługi Azure Digital bliźniaczych reprezentacji z innymi językami niż C#.
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 3d6fa1660f5204137dbebeae36f6d06852d88412
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84725804"
---
# <a name="create-custom-sdks-for-azure-digital-twins-with-autorest"></a>Tworzenie niestandardowych zestawów SDK dla usługi Azure Digital bliźniaczych reprezentacji za pomocą AutoRest

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Obecnie jedynym publikowanym zestawem SDK płaszczyzny danych na potrzeby współdziałania z interfejsami API Digital bliźniaczych reprezentacji na platformie Azure jest .NET (C#). Aby uzyskać ogólne informacje na temat zestawu .NET SDK i interfejsów API, zobacz [: korzystanie z interfejsów API i zestawów SDK Digital bliźniaczych reprezentacji na platformie Azure](how-to-use-apis-sdks.md). Jeśli pracujesz w innym języku, w tym artykule opisano sposób generowania własnego zestawu SDK w wybranym języku przy użyciu funkcji AutoRest.

## <a name="set-up-the-sdk"></a>Konfigurowanie zestawu SDK

Aby wygenerować zestaw SDK, potrzebne są:
* [AutoRest](https://github.com/Azure/autorest), wersja 2.0.4413 (wersja 3 nie jest obecnie obsługiwana)
* [Node.js](https://nodejs.org) jako warunek wstępny do AutoRest
* [Plik usługi Azure Digital bliźniaczych reprezentacji openapi (Swagger)](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/preview/2020-03-01-preview/digitaltwins.json)

Jeśli zainstalowano Node.js, możesz uruchomić to polecenie, aby upewnić się, że masz zainstalowaną odpowiednią wersję narzędzia AutoRest:
```cmd/sh
npm install -g autorest@2.0.4413
```

Aby uruchomić polecenie AutoRest dla pliku programu Azure Digital bliźniaczych reprezentacji Swagger, wykonaj następujące kroki:
1. Skopiuj plik programu Azure Digital bliźniaczych reprezentacji Swagger do katalogu roboczego.
2. W wierszu polecenia przejdź do katalogu roboczego.
3. Uruchom polecenie AutoRest przy użyciu poniższego polecenia.

```cmd/sh
autorest --input-file=adtApiSwagger.json --csharp --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

W związku z tym zobaczysz nowy folder o nazwie *ADTApi* w katalogu roboczym. Wygenerowane pliki zestawu SDK będą mieć *ADTApi*przestrzeni nazw, które będą nadal używane w pozostałej części przykładów.

AutoRest obsługuje szeroką gamę generatorów kodu języka.

## <a name="add-the-sdk-to-a-visual-studio-project"></a>Dodawanie zestawu SDK do projektu programu Visual Studio

Pliki generowane przez program AutoRest można uwzględnić bezpośrednio w rozwiązaniu platformy .NET. Ponieważ jednak prawdopodobnie potrzebujesz zestawu SDK usługi Azure Digital bliźniaczych reprezentacji w kilku oddzielnych projektach (aplikacje klienckie, aplikacje Azure Functions itd.), zalecamy utworzenie osobnego projektu (biblioteki klas .NET) z wygenerowanych plików. Następnie można dołączyć ten projekt biblioteki klas do innych rozwiązań jako odwołanie do projektu.

Ta sekcja zawiera instrukcje dotyczące sposobu tworzenia zestawu SDK jako biblioteki klas, która jest własnym projektem i może być uwzględniona w innych projektach. Oto kroki do wykonania:

1. Tworzenie nowego rozwiązania programu Visual Studio dla biblioteki klas
2. Użyj nazwy "ADTApi" jako nazwy projektu
3. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt *ADTApi* wygenerowanego rozwiązania i wybierz polecenie *Dodaj > istniejący element...*
4. Znajdź folder, w którym został wygenerowany zestaw SDK, a następnie wybierz pliki na poziomie głównym
5. Naciśnij przycisk "OK"
6. Dodaj folder do projektu (Zaznacz projekt prawym przyciskiem myszy w Eksplorator rozwiązań i wybierz polecenie *dodaj > nowy folder*)
7. Nadaj nazwę folderowi "models"
8. Kliknij prawym przyciskiem myszy folder *modele* w Eksploratorze rozwiązań i wybierz polecenie *Dodaj > istniejący element...*
9. Wybierz pliki w folderze *modele* wygenerowanego zestawu SDK i naciśnij przycisk "OK"

Aby pomyślnie skompilować zestaw SDK, potrzebne są następujące odwołania:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Aby je dodać, Otwórz *narzędzia > Menedżer pakietów nuget > zarządzanie pakietami NuGet dla rozwiązania.*...

1. Na panelu upewnij się, że wybrana jest karta *Przeglądaj*
2. Wyszukaj *firmę Microsoft. REST*
3. Wybierz pakiety *ClientRuntime* i *ClientRuntime. platformy Azure* , a następnie dodaj je do rozwiązania

Teraz można skompilować projekt i dołączyć go jako odwołanie do projektu w dowolnej zapisanej aplikacji Digital bliźniaczych reprezentacji systemu Azure.

## <a name="general-guidelines-for-generated-sdks"></a>Ogólne wytyczne dotyczące wygenerowanych zestawów SDK

Ta sekcja zawiera ogólne informacje na temat i wskazówki dotyczące korzystania z wygenerowanego zestawu SDK.

### <a name="synchronous-and-asynchronous-calls"></a>Wywołania synchroniczne i asynchroniczne

Wszystkie funkcje zestawu SDK są w wersji synchronicznej i asynchronicznej.

### <a name="typed-and-untyped-data"></a>Dane wpisane i nietypu

Wywołania interfejsu API REST zwykle zwracają obiekty o jednoznacznie określonym typie. Jednak ponieważ usługa Azure Digital bliźniaczych reprezentacji umożliwia użytkownikom Definiowanie własnych typów niestandardowych dla bliźniaczych reprezentacji, nie ma możliwości wstępnego definiowania statycznych danych zwrotnych dla wielu wywołań cyfrowych bliźniaczych reprezentacji platformy Azure. Zamiast tego interfejsy API zwracają typy otoki o jednoznacznie określonym typie, gdy ma to zastosowanie, a dane o niestandardowym typie są w Json.NET obiekty (używane wszędzie tam, gdzie typ danych "Object" pojawia się w sygnaturach interfejsu API). Te obiekty można rzutować odpowiednio w kodzie.

### <a name="error-handling"></a>Obsługa błędów

Za każdym razem, gdy w zestawie SDK wystąpi błąd (w tym błędy HTTP, takie jak 404), zestaw SDK zgłosi wyjątek. Z tego powodu ważne jest Hermetyzowanie wszystkich wywołań interfejsu API w blokach try/catch.

Oto fragment kodu, który próbuje dodać sznurek i przechwytuje wszystkie błędy w tym procesie:

```csharp
try
{
    await client.DigitalTwins.AddAsync(id, initData);
    Console.WriteLine($"Created a twin successfully: {id}");
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
}
```

### <a name="paging"></a>Stronicowanie

AutoRest generuje dwa typy wzorców stronicowania dla zestawu SDK:
* Jeden dla wszystkich interfejsów API z wyjątkiem interfejsu API zapytań
* Jeden dla interfejsu API zapytania

W wzorcu stronicowania niezwiązanego z kwerendą istnieją dwie wersje każdego wywołania:
* Wersja, która ma wykonać początkowe wywołanie (na przykład `DigitalTwins.ListEdges()` )
* Wersja do pobrania kolejnych stron z sufiksem "Next" (na przykład `DigitalTwins.ListEdgesNext()` )

Oto fragment kodu przedstawiający sposób pobierania stronicowanej listy relacji wychodzących z usługi Azure Digital bliźniaczych reprezentacji:
```csharp
try
{
    // List to hold the results in
    List<object> relList = new List<object>();
    // Enumerate the IPage object returned to get the results
    // ListAsync will throw if an error occurs
    IPage<object> relPage = await client.DigitalTwins.ListEdgesAsync(id);
    relList.AddRange(relPage);
    // If there are more pages, the NextPageLink in the page is set
    while (relPage.NextPageLink != null)
    {
        // Get more pages...
        relPage = await client.DigitalTwins.ListEdgesNextAsync(relPage.NextPageLink);
        relList.AddRange(relPage);
    }
    Console.WriteLine($"Found {relList.Count} relationships on {id}");
    // Do something with each object found
    // As relationships are custom types, they are JSON.Net types
    foreach (JObject r in relList)
    {
        string relId = r.Value<string>("$edgeId");
        string relName = r.Value<string>("$relationship");
        Console.WriteLine($"Found relationship {relId} from {id}");
    }
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error retrieving relationships on {id}: {e.Response.StatusCode}");
}
```

Drugi wzorzec jest generowany tylko dla interfejsu API zapytań. Używa `continuationToken` jawnie.

Oto przykład z tym wzorcem:

```csharp
string query = "SELECT * FROM digitaltwins";
string conToken = null; // continuation token from the query
int page = 0;
try
{
    // Repeat the query while there are pages
    do
    {
        QuerySpecification spec = new QuerySpecification(query, conToken);
        QueryResult qr = await client.Query.QueryTwinsAsync(spec);
        page++;
        Console.WriteLine($"== Query results page {page}:");
        if (qr.Items != null)
        {
            // Query returns are JObjects
            foreach(JObject o in qr.Items)
            {
                string twinId = o.Value<string>("$dtId");
                Console.WriteLine($"  Found {twinId}");
            }
        }
        Console.WriteLine($"== End query results page {page}");
        conToken = qr.ContinuationToken;
    } while (conToken != null);
} catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error in twin query: ${e.Response.StatusCode}");
}
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z instrukcjami, aby utworzyć aplikację kliencką, w której można korzystać z zestawu SDK:
* [Samouczek: kod aplikacji klienckiej](tutorial-code.md)