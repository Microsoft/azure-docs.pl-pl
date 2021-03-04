---
title: Zarządzanie grafem bliźniaczych reprezentacji z relacjami
titleSuffix: Azure Digital Twins
description: Zobacz, jak zarządzać grafem cyfrowego bliźniaczych reprezentacji, łącząc je z relacjami.
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a2732c3979998ea3429833f96056b88bc2dccf75
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050940"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Zarządzanie grafem cyfrowego bliźniaczych reprezentacji przy użyciu relacji

Bliźniaczych reprezentacji na platformie Azure Digital to [Wykres dwuosiowy](concepts-twins-graph.md) reprezentujący całe środowisko. Wykres dwuosiowy jest wykonany z pojedynczej bliźniaczych reprezentacji cyfrowej połączonej za pośrednictwem **relacji**. 

Gdy korzystasz z działającego [wystąpienia usługi Azure Digital bliźniaczych reprezentacji](how-to-set-up-instance-portal.md) i ustawisz kod [uwierzytelniania](how-to-authenticate-client.md) w aplikacji klienckiej, możesz użyć [**interfejsów API DigitalTwins**](/rest/api/digital-twins/dataplane/twins) , aby tworzyć, modyfikować i usuwać bliźniaczych reprezentacji cyfrowe oraz ich relacje w wystąpieniu bliźniaczych reprezentacji cyfrowych platformy Azure. Można również użyć [zestawu SDK platformy .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)lub [interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md).

Ten artykuł koncentruje się na zarządzaniu relacjami i wykresem jako całościowym; Aby korzystać z pojedynczych bliźniaczych reprezentacji cyfrowych, zobacz [*How to: Manage Digital bliźniaczych reprezentacji*](how-to-manage-twin.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>Sposoby zarządzania programem Graph

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

Możesz również wprowadzać zmiany w grafie przy użyciu przykładowej usługi Azure Digital bliźniaczych reprezentacji Explorer, która pozwala na wizualizację bliźniaczych reprezentacji i grafu oraz korzystanie z zestawu SDK w tle. W następnej sekcji opisano ten przykład szczegółowo.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Tworzenie relacji

Relacje opisują sposób, w jaki różne bliźniaczych reprezentacji cyfrowe są połączone ze sobą, które stanowią podstawę grafu sznurka.

Relacje są tworzone przy użyciu `CreateOrReplaceRelationshipAsync()` wywołania. 

Aby utworzyć relację, należy określić:
* Identyfikator przędzy źródłowej ( `srcId` w poniższym przykładzie kodu): Identyfikator przędzy, z której pochodzi relacja.
* Identyfikator przędzy docelowej ( `targetId` w poniższym przykładzie kodu): Identyfikator dwuosiowy, do którego nadeszła relacja.
* Nazwa relacji ( `relName` w poniższym przykładzie kodu): ogólny typ relacji, co _zawiera_.
* Identyfikator relacji ( `relId` w poniższym przykładzie kodu): Nazwa określona dla tej relacji, taka jak _Relationship1_.

Identyfikator relacji musi być unikatowy w obrębie danej przędzy źródłowej. Nie musi być globalnie unikatowa.
Na przykład dla sznurka *foo* każdy określony identyfikator relacji musi być unikatowy. Jednak inny *pasek* dwuosiowy może mieć relację wychodzącą zgodną z tym samym identyfikatorem relacji *foo* .

Poniższy przykład kodu ilustruje sposób tworzenia relacji w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. Używa wywołania zestawu SDK (wyróżnionego) wewnątrz metody niestandardowej, która może pojawić się w kontekście większego programu.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod" highlight="13":::

Ta funkcja niestandardowa może teraz zostać wywołana, aby utworzyć relację _zawiera_ , taką jak: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

Jeśli chcesz utworzyć wiele relacji, możesz powtórzyć wywołania do tej samej metody, przekazując różne typy relacji do argumentu. 

Aby uzyskać więcej informacji na temat klasy pomocnik `BasicRelationship` , zobacz [*How to: Use the Azure Digital bliźniaczych reprezentacji API and SDK*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Tworzenie wielu relacji między bliźniaczych reprezentacji

Relacje mogą być klasyfikowane jako: 

* Relacje wychodzące: relacje należące do tego sznurka, które wskazują na zewnątrz, aby połączyć je z innymi bliźniaczych reprezentacji. `GetRelationshipsAsync()`Metoda jest używana do uzyskiwania wychodzących relacji dla sznurka.
* Relacje przychodzące: relacje należące do innych bliźniaczych reprezentacji, które wskazują na ten dwuosiowy, aby utworzyć link "przychodzący". `GetIncomingRelationshipsAsync()`Metoda jest używana do uzyskiwania ruchu przychodzącego z sznurka.

Nie ma ograniczeń dotyczących liczby relacji, które mogą mieć między dwoma bliźniaczych reprezentacji — można mieć dowolną liczbę relacji między bliźniaczych reprezentacji. 

Oznacza to, że można wyrazić kilka różnych typów relacji między dwoma bliźniaczych reprezentacji jednocześnie. Na przykład *przędza a* może mieć zarówno *przechowywaną* relację, jak i *wytworzoną* relację z *przędzą B*.

W razie potrzeby można nawet utworzyć wiele wystąpień tego samego typu relacji między tymi samymi dwoma bliźniaczych reprezentacji. W tym przykładzie *sznurek A* może mieć dwie różne *przechowywane* relacje z *przędzą B*, o ile relacje mają różne identyfikatory relacji.

## <a name="list-relationships"></a>Wyświetl relacje

Aby uzyskać dostęp do listy relacji **wychodzących** dla danego przędzy na grafie, można użyć `GetRelationships()` metody podobnej do tego:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

To zwraca `Azure.Pageable<T>` lub `Azure.AsyncPageable<T>` , w zależności od tego, czy używana jest wersja synchroniczna lub asynchroniczna wywołania.

Oto przykład, który pobiera listę relacji. Używa wywołania zestawu SDK (wyróżnionego) wewnątrz metody niestandardowej, która może pojawić się w kontekście większego programu.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod" highlight="8":::

Teraz można wywołać tę metodę niestandardową, aby wyświetlić wychodzące relacje bliźniaczych reprezentacji w następujący sposób:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

Możesz użyć pobranych relacji, aby przejść do innych bliźniaczych reprezentacji na grafie. Aby to zrobić, Przeczytaj `target` pole z zwróconej relacji i użyj go jako identyfikatora dla następnego wywołania `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Znajdź przychodzące relacje z dwuosiową cyfrą

Usługa Azure Digital bliźniaczych reprezentacji ma także interfejs API, aby znaleźć wszystkie **przychodzące** relacje z danym przędzą. Jest to często przydatne w przypadku nawigacji odwrotnej lub podczas usuwania sznurka.

>[!NOTE]
> `IncomingRelationship` wywołania nie zwracają pełnej treści relacji. Aby uzyskać więcej informacji na temat `IncomingRelationship` klasy, zapoznaj się z [dokumentacją referencyjną](/dotnet/api/azure.digitaltwins.core.incomingrelationship?view=azure-dotnet&preserve-view=true).

Przykładowy kod w poprzedniej sekcji koncentruje się na znajdowaniu relacji wychodzących od sznurka. Poniższy przykład ma strukturę podobną, ale zamiast tego znajduje *przychodzące* relacje z przędzą. Ten przykład używa również wywołania zestawu SDK (wyróżnione) wewnątrz metody niestandardowej, która może pojawić się w kontekście większego programu.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod" highlight="8":::

Teraz można wywołać tę metodę niestandardową, aby zobaczyć przychodzące relacje bliźniaczych reprezentacji w następujący sposób:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>Wyświetl wszystkie właściwości i relacje z przędzą

Korzystając z podanych powyżej metod wyświetlania wychodzących i przychodzących relacji do sznurka, można utworzyć metodę, która drukuje pełne informacje o pojedynczej bliźniaczyej, w tym właściwości przędzy i oba typy relacji. Oto przykładowa Metoda niestandardowa pokazująca, jak połączyć powyższe metody niestandardowe w tym celu.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

Teraz można wywołać tę funkcję niestandardową w następujący sposób: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="update-relationships"></a>Aktualizuj relacje

Relacje są aktualizowane przy użyciu `UpdateRelationship` metody. 

>[!NOTE]
>Ta metoda służy do aktualizowania **Właściwości** relacji. Jeśli trzeba zmienić źródło [pojedynczej](#create-relationships) lub docelową dwuosiową relację, należy [usunąć relację](#delete-relationships) i utworzyć ją ponownie przy użyciu nowego bliźniaczych reprezentacji.

Parametry wymagane dla wywołania klienta to identyfikator przędzy źródłowej (przędza, z której pochodzi relacja), identyfikator relacji do zaktualizowania i dokument [poprawki JSON](http://jsonpatch.com/) zawierający właściwości i nowe wartości, które chcesz zaktualizować.

Poniżej przedstawiono przykładowy kod przedstawiający sposób użycia tej metody. W tym przykładzie używane jest wywołanie zestawu SDK (wyróżnione) wewnątrz metody niestandardowej, która może pojawić się w kontekście większego programu.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UpdateRelationshipMethod" highlight="6":::

Oto przykład wywołania tej metody niestandardowej, przekazując do dokumentu poprawki JSON informacje służące do aktualizowania właściwości.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseUpdateRelationship":::

## <a name="delete-relationships"></a>Usuń relacje

Pierwszy parametr określa przędzę źródłową (dwuosiową, z której pochodzi relacja). Drugim parametrem jest identyfikator relacji. Wymagany jest zarówno identyfikator przędzy, jak i identyfikator relacji, ponieważ identyfikatory relacji są unikatowe tylko w zakresie sznurka.

Poniżej przedstawiono przykładowy kod przedstawiający sposób użycia tej metody. W tym przykładzie używane jest wywołanie zestawu SDK (wyróżnione) wewnątrz metody niestandardowej, która może pojawić się w kontekście większego programu.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod" highlight="5":::

Teraz możesz wywołać tę metodę niestandardową, aby usunąć relację podobną do tej:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="runnable-twin-graph-sample"></a>Przykład grafu możliwy do uruchomienia bliźniaczy

Poniższy fragment kodu możliwy do uruchomienia używa operacji relacji z tego artykułu, aby utworzyć wykres Wieloosiowy z bliźniaczych reprezentacjiami i relacjami cyfrowymi.

### <a name="set-up-the-runnable-sample"></a>Konfigurowanie przykładu możliwy do uruchomienia

Fragment kodu używa [*Room.json*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) i [*Floor.jsw*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) definicjach modelu z [*samouczka: Poznaj usługę Azure Digital bliźniaczych reprezentacji z przykładową aplikacją kliencką*](tutorial-command-line-app.md). Możesz użyć tych linków, aby przejść bezpośrednio do plików lub pobrać je jako część pełnego, kompleksowego [projektu](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)przykładu. 

Przed uruchomieniem przykładu należy wykonać następujące czynności:
1. Pobierz pliki modelu, umieść je w projekcie i Zastąp `<path-to>` symbole zastępcze w poniższym kodzie, aby poinformować program, gdzie go znaleźć.
2. Zastąp symbol zastępczy `<your-instance-hostname>` nazwą hosta usługi Azure Digital bliźniaczych reprezentacji.
3. Dodaj dwie zależności do projektu, które będą konieczne do pracy z usługą Azure Digital bliźniaczych reprezentacji. Pierwszy to pakiet [usługi Azure Digital bliźniaczych reprezentacji SDK dla platformy .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true), a drugi zawiera narzędzia, które ułatwiają uwierzytelnianie na platformie Azure.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

Musisz również skonfigurować poświadczenia lokalne, jeśli chcesz uruchomić próbkę bezpośrednio. Następna sekcja przeprowadzi Cię przez ten temat.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Po wykonaniu powyższych kroków można bezpośrednio uruchomić następujący przykładowy kod.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

Oto dane wyjściowe konsoli powyższego programu: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Dane wyjściowe konsoli pokazujące szczegóły sznurka, przychodzące i wychodzące relacje bliźniaczych reprezentacji." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> Wykres bliźniaczy jest koncepcją tworzenia relacji między bliźniaczych reprezentacji. Jeśli chcesz wyświetlić wizualną reprezentację grafu bliźniaczyego, zobacz sekcję [*wizualizacje*](how-to-manage-graph.md#visualization) w tym artykule. 

## <a name="create-graph-from-a-csv-file"></a>Utwórz wykres z pliku CSV

W praktyce przypadki użycia hierarchie bliźniaczych są często tworzone na podstawie danych przechowywanych w innej bazie danych lub w arkuszu kalkulacyjnym lub pliku CSV. W tej sekcji przedstawiono sposób odczytywania danych z pliku CSV i tworzenia wykresu dwuosiowego.

Rozważmy poniższą tabelę danych opisującą zestaw bliźniaczych reprezentacji cyfrowych i relacji.

|  Identyfikator modelu    | Identyfikator przędzy (musi być unikatowy) | Nazwa relacji  | Identyfikator przędzy docelowej  | Dane inicjowania przędzy |
| --- | --- | --- | --- | --- |
| dtmi: przykład: Floor; 1    | Floor1 | zawiera | Room1 | |
| dtmi: przykład: Floor; 1    | Floor0 | zawiera | Room0 | |
| dtmi: przykład: Pokój; 1    | Room1 | | | {"Temperatura": 80} |
| dtmi: przykład: Pokój; 1    | Room0 | | | {"Temperatura": 70} |

Jednym ze sposobów uzyskania tych danych w usłudze Azure Digital bliźniaczych reprezentacji jest przekonwertowanie tabeli do pliku CSV i zapisanie kodu w celu zinterpretowania pliku w poleceniach w celu utworzenia bliźniaczych reprezentacji i relacji. Poniższy przykład kodu ilustruje odczytywanie danych z pliku CSV i tworzenie wykresu dwuosiowego w usłudze Azure Digital bliźniaczych reprezentacji.

W poniższym kodzie plik CSV jest nazywany *data.csv* i istnieje symbol zastępczy reprezentujący **nazwę hosta** wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Przykład korzysta również z kilku pakietów, które można dodać do projektu, aby pomóc w tym procesie.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat wykonywania zapytań dotyczących grafu wieloosiowego usługi Azure Digital bliźniaczych reprezentacji:
* [*Koncepcje: język zapytań*](concepts-query-language.md)
* [*Instrukcje: zapytanie o wykres bliźniaczy*](how-to-query-graph.md)