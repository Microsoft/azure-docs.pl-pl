---
title: Opracowywanie funkcji .NET Standard dla zadań Azure Stream Analytics (wersja zapoznawcza)
description: Dowiedz się, jak napisać funkcje zdefiniowane przez użytkownika w języku C# dla Stream Analytics zadań.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/10/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 52fa6f05db5452a2e7b8ec4f93d65525873c8c7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020574"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Opracowywanie .NET Standard funkcji zdefiniowanych przez użytkownika dla zadań Azure Stream Analytics (wersja zapoznawcza)

Azure Stream Analytics oferuje język zapytań przypominający SQL, służący do wykonywania transformacji i obliczeń na strumieniach danych zdarzeń. Istnieje wiele wbudowanych funkcji, ale niektóre złożone scenariusze wymagają dodatkowej elastyczności. Za pomocą .NET Standard funkcji zdefiniowanych przez użytkownika (UDF) można wywoływać własne funkcje, które są zapisywane w dowolnym języku .NET Standard (C#, F # itp.), aby zwiększyć Stream Analytics języka zapytań. UDF umożliwiają wykonywanie złożonych obliczeń matematycznych, importowanie niestandardowych modeli ml przy użyciu ml.NET i używanie niestandardowych reguł przypisywania w przypadku brakujących danych. Funkcja UDF dla Stream Analytics zadań jest obecnie w wersji zapoznawczej i nie powinna być używana w obciążeniach produkcyjnych.

Funkcja zdefiniowana przez użytkownika platformy .NET dla zadań w chmurze jest dostępna w:
* Zachodnio-środkowe stany USA
* Europa Północna
* East US
* Zachodnie stany USA
* Wschodnie stany USA 2
* West Europe

Jeśli interesuje Cię korzystanie z tej funkcji w innym regionie, możesz [poprosić o dostęp](https://aka.ms/ccodereqregion).

## <a name="package-path"></a>Ścieżka pakietu

Format dowolnego pakietu UDF ma ścieżkę `/UserCustomCode/CLR/*` . Biblioteki dołączane dynamicznie (dll) i zasoby są kopiowane do `/UserCustomCode/CLR/*` folderu, co ułatwia izolowanie bibliotek DLL użytkowników z systemów i Azure Stream Analytics bibliotek DLL. Ta ścieżka pakietu jest używana dla wszystkich funkcji niezależnie od metody używanej do ich używania.

## <a name="supported-types-and-mapping"></a>Obsługiwane typy i mapowanie

Aby wartości Azure Stream Analytics, które mają być używane w języku C#, muszą być organizowane z jednego środowiska do drugiego. Kierowanie odbywa się dla wszystkich parametrów wejściowych UDF. Każdy typ Azure Stream Analytics ma odpowiadający mu typ w języku C# przedstawionym w poniższej tabeli:

|**Typ Azure Stream Analytics** |**Typ C#** |
|---------|---------|
|bigint | długi |
|float | double |
|nvarchar (max) | ciąg |
|datetime | DateTime |
|Rekord | Słownik\<string, object> |
|Tablica | Obiekt [] |

To samo jest prawdziwe, gdy dane muszą być organizowane z języka C# do Azure Stream Analytics, co odbywa się na wartości wyjściowej UDF. W poniższej tabeli pokazano, jakie typy są obsługiwane:

|**Typ C#**  |**Typ Azure Stream Analytics**  |
|---------|---------|
|długi  |  bigint   |
|double  |  float   |
|ciąg  |  nvarchar (max)   |
|DateTime  |  Data i godzina   |
|struktura  |  Rekord   |
|object  |  Rekord   |
|Obiekt []  |  Tablica   |
|Słownik\<string, object>  |  Rekord   |

## <a name="develop-a-udf-in-visual-studio-code"></a>Tworzenie plików UDF w Visual Studio Code

[Visual Studio Code narzędzia dla Azure Stream Analytics](quick-create-visual-studio-code.md) ułatwiają pisanie UDF, testowanie zadań lokalnie (nawet w trybie offline) i publikowanie zadań Stream Analytics na platformie Azure.

Istnieją dwa sposoby implementacji programu .NET Standard UDF w narzędziach Visual Studio Code.

* UDF z lokalnych bibliotek DLL
* UDF z lokalnego projektu

### <a name="local-project"></a>Projekt lokalny

Funkcje zdefiniowane przez użytkownika mogą być zapisywane w zestawie, który jest później przywoływany w kwerendzie Azure Stream Analytics. Jest to zalecana opcja dla funkcji złożonych, które wymagają pełnej mocy języka .NET Standard poza językiem wyrażeń, na przykład logiki proceduralnej lub rekursji. UDF z projektu lokalnego może być również używany, gdy trzeba udostępnić logikę funkcji w kilku Azure Stream Analyticsych zapytaniach. Dodanie UDF do projektu lokalnego umożliwia debugowanie i testowanie funkcji lokalnie.

Aby odwołać się do projektu lokalnego:

1. Utwórz nową bibliotekę klas .NET Standard na komputerze lokalnym.
2. Napisz kod w klasie. Należy pamiętać, że klasy muszą być zdefiniowane jako *publiczne* , a obiekty muszą być zdefiniowane jako *statyczne publiczne*.
3. Dodaj nowy plik konfiguracji funkcji CSharp w projekcie Azure Stream Analytics i odwołuje się do projektu biblioteki klas CSharp.
4. Skonfiguruj ścieżkę zestawu w pliku konfiguracji zadania, w `JobConfig.json` sekcji **CustomCodeStorage** . Ten krok nie jest wymagany do testowania lokalnego.

### <a name="local-dlls"></a>Lokalne biblioteki DLL

Można także odwoływać się do lokalnych bibliotek DLL, które zawierają funkcje zdefiniowane przez użytkownika.

### <a name="example"></a>Przykład

W tym przykładzie **CSharpUDFProject** jest projektem biblioteki klas języka C#, a **ASAUDFDemo** to projekt Azure Stream Analytics, który będzie odwoływać się do **CSharpUDFProject**.

:::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-demo.png" alt-text="Azure Stream Analytics projekt w Visual Studio Code":::

Poniższy element UDF zawiera funkcję, która mnoży liczbę całkowitą przez siebie w celu wyprodukowania kwadratu liczby całkowitej. Klasy muszą być zdefiniowane jako *publiczne* , a obiekty muszą być zdefiniowane jako *statyczne publiczne*.

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

Poniższe kroki pokazują, jak dodać funkcję UDF języka C# do projektu Stream Analytics.

1. Kliknij prawym przyciskiem myszy folder **Functions** i wybierz polecenie **Dodaj element**.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function.png" alt-text="Dodaj nową funkcję w projekcie Azure Stream Analytics":::

2. Dodaj funkcję języka C# **SquareFunction** do projektu Azure Stream Analytics.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function-2.png" alt-text="Wybierz funkcję CSharp z projektu Stream Analytics w VS Code":::

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function-name.png" alt-text="Wprowadź nazwę funkcji CSharp w VS Code":::

3. W obszarze Konfiguracja funkcji w języku C# wybierz pozycję **Wybierz ścieżkę projektu biblioteki** , aby wybrać projekt C# z listy rozwijanej, a następnie wybierz opcję **Kompiluj projekt** , aby skompilować projekt. Następnie wybierz **pozycję Wybierz klasę** i **Wybierz pozycję Metoda** , aby wybrać powiązaną klasę i nazwę metody z listy rozwijanej. Aby odwołać się do metod, typów i funkcji w kwerendzie Stream Analytics, klasy muszą być zdefiniowane jako *publiczne* , a obiekty muszą być zdefiniowane jako *statyczne publiczne*.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-choose-project.png" alt-text="Konfiguracja funkcji Sharp Stream Analytics C VS Code":::

    Jeśli chcesz użyć formatu UDF języka C# z biblioteki DLL, wybierz pozycję **Wybierz bibliotekę DLL ścieżka** , aby wybrać bibliotekę DLL. Następnie wybierz **pozycję Wybierz klasę** i **Wybierz pozycję Metoda** , aby wybrać powiązaną klasę i nazwę metody z listy rozwijanej.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-choose-dll.png" alt-text="Konfiguracja funkcji Sharp Stream Analytics C":::

4. Wywołaj funkcję UDF w zapytaniu Azure Stream Analytics.

   ```sql
    SELECT price, udf.SquareFunction(price)
    INTO Output
    FROM Input 
   ```

5. Przed przesłaniem zadania na platformę Azure Skonfiguruj ścieżkę pakietu w pliku konfiguracji zadania, w `JobConfig.json` sekcji **CustomCodeStorage** . Użyj **opcji wybierz z subskrypcji** w programie CodeLens, aby wybrać subskrypcję, a następnie wybierz konto magazynu i nazwę kontenera z listy rozwijanej. Pozostaw **ścieżkę** jako domyślną. Ten krok nie jest wymagany do testowania lokalnego.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-configure-storage-account.png" alt-text="Wybierz ścieżkę biblioteki":::

## <a name="develop-a-udf-in-visual-studio"></a>Tworzenie plików UDF w programie Visual Studio

Istnieją trzy sposoby implementacji UDF w narzędziach programu Visual Studio.

* CodeBehind pliki w projekcie ASA
* UDF z lokalnego projektu
* Istniejący pakiet z konta usługi Azure Storage

### <a name="codebehind"></a>CodeBehind

Funkcje zdefiniowane przez użytkownika można napisać w **skrypcie. ASQL** Codebehind. Narzędzia programu Visual Studio automatycznie kompilują plik CodeBehind do pliku zestawu. Zestawy są spakowane jako plik zip i przekazywane do konta magazynu podczas przesyłania zadania do platformy Azure. Możesz dowiedzieć się, jak napisać UDF języka C# przy użyciu CodeBehind, postępując zgodnie z samouczkiem [UDF języka c# for Stream Analytics Edge](stream-analytics-edge-csharp-udf.md) . 

### <a name="local-project"></a>Projekt lokalny

Aby odwołać się do projektu lokalnego w programie Visual Studio:

1. Utwórz nową bibliotekę klas .NET standard w rozwiązaniu
2. Napisz kod w klasie. Należy pamiętać, że klasy muszą być zdefiniowane jako *publiczne* , a obiekty muszą być zdefiniowane jako *statyczne publiczne*. 
3. Skompilowanie projektu. Narzędzia będą pakować wszystkie artefakty w folderze bin do pliku zip i przekazać plik zip na konto magazynu. W przypadku odwołań zewnętrznych należy użyć odwołania do zestawu zamiast pakietu NuGet.
4. Odwołuje się do nowej klasy w projekcie Azure Stream Analytics.
5. Dodaj nową funkcję w projekcie Azure Stream Analytics.
6. Skonfiguruj ścieżkę zestawu w pliku konfiguracji zadania, `JobConfig.json` . Ustaw ścieżkę zestawu na **odwołanie do projektu lokalnego lub CodeBehind**.
7. Kompiluj zarówno projekt funkcji, jak i projekt Azure Stream Analytics.  

### <a name="example"></a>Przykład

W tym przykładzie **UDFTest** jest projektem biblioteki klas języka C#, a **ASAUDFDemo** to projekt Azure Stream Analytics, który będzie odwoływać się do **UDFTest**.

:::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png" alt-text="Azure Stream Analytics IoT Edge projektu w programie Visual Studio":::

1. Skompiluj projekt C#, który umożliwi dodanie odwołania do formatu UDF języka C# z kwerendy Azure Stream Analytics.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png" alt-text="Tworzenie projektu IoT Edge Azure Stream Analytics w programie Visual Studio":::

2. Dodaj odwołanie do projektu C# w projekcie ASA. Kliknij prawym przyciskiem myszy węzeł odwołania i wybierz polecenie Dodaj odwołanie.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png" alt-text="Dodawanie odwołania do projektu C# w programie Visual Studio":::

3. Wybierz nazwę projektu C# z listy.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png" alt-text="Wybierz nazwę projektu C# z listy referencyjnej":::

4. W obszarze **References** ( **Eksplorator rozwiązań**) powinna zostać wyświetlona pozycja **UDFTest** .

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png" alt-text="Wyświetl odwołanie do funkcji zdefiniowanej przez użytkownika w Eksploratorze rozwiązań":::

5. Kliknij prawym przyciskiem myszy folder **Functions** i wybierz polecenie **nowy element**.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png" alt-text="Dodaj nowy element do funkcji w rozwiązaniu Azure Stream Analytics Edge":::

6. Dodaj funkcję języka C# **SquareFunction.js** do projektu Azure Stream Analytics.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png" alt-text="Wybierz funkcję CSharp z elementów krawędzi Stream Analytics w programie Visual Studio":::

7. Kliknij dwukrotnie funkcję w **Eksplorator rozwiązań** , aby otworzyć okno dialogowe konfiguracji.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png" alt-text="Konfiguracja funkcji Sharp języka C w programie Visual Studio":::

8. W konfiguracji funkcji języka C# wybierz pozycję **Załaduj z odwołania do projektu ASA** i powiązanego zestawu, klasy i nazw metod z listy rozwijanej. Aby odwołać się do metod, typów i funkcji w kwerendzie Stream Analytics, klasy muszą być zdefiniowane jako *publiczne* , a obiekty muszą być zdefiniowane jako *statyczne publiczne*.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png" alt-text="Konfiguracja funkcji Sharp w Stream Analytics języku C Visual Studio":::

## <a name="existing-packages"></a>Istniejące pakiety

Możesz tworzyć .NET Standard UDF w dowolnym dowolnym środowisku IDE i wywoływać je z poziomu zapytania Azure Stream Analytics. Najpierw Skompiluj swój kod i Spakuj wszystkie biblioteki DLL. Format pakietu ma ścieżkę `/UserCustomCode/CLR/*` . Następnie Przekaż `UserCustomCode.zip` do katalogu głównego kontenera na koncie usługi Azure Storage.

Po przekazaniu pakietów zip zestawu do konta usługi Azure Storage można użyć funkcji w Azure Stream Analytics zapytaniach. Wszystko, co należy zrobić, obejmuje informacje o magazynie w konfiguracji zadania Stream Analytics. Nie można przetestować funkcji lokalnie przy użyciu tej opcji, ponieważ narzędzia Visual Studio Tools nie pobierają pakietu. Ścieżka pakietu jest analizowana bezpośrednio w usłudze. 

Aby skonfigurować ścieżkę zestawu w pliku konfiguracji zadania `JobConfig.json` :

Rozwiń sekcję **Konfiguracja kodu skonfigurowana przez użytkownika**, a następnie wypełnij konfigurację za pomocą następujących sugerowanych wartości:

   |**Ustawienie**|**Sugerowana wartość**|
   |-------|---------------|
   |Zasób ustawień magazynu globalnego|Wybierz źródło danych z bieżącego konta|
   |Subskrypcja ustawień magazynu globalnego| < subskrypcję >|
   |Konto magazynu ustawień globalnych magazynu| < konta magazynu >|
   |Zasób ustawień niestandardowego magazynu kodu|Wybierz źródło danych z bieżącego konta|
   |Konto magazynu ustawień niestandardowych magazynu kodu|< konta magazynu >|
   |Kontener ustawień niestandardowego magazynu kodu|< kontener magazynu >|
   |Źródło niestandardowego zestawu kodu|Istniejące pakiety zestawu z chmury|
   |Źródło niestandardowego zestawu kodu|UserCustomCode.zip|

## <a name="user-logging"></a>Rejestrowanie użytkowników

Mechanizm rejestrowania umożliwia przechwytywanie informacji niestandardowych, gdy zadanie jest uruchomione. Za pomocą danych dzienników można debugować lub oceniać poprawność niestandardowego kodu w czasie rzeczywistym.

`StreamingContext`Klasa umożliwia publikowanie informacji diagnostycznych za pomocą `StreamingDiagnostics.WriteError` funkcji. Poniższy kod przedstawia interfejs uwidoczniony przez Azure Stream Analytics.

```csharp
public abstract class StreamingContext
{
    public abstract StreamingDiagnostics Diagnostics { get; }
}

public abstract class StreamingDiagnostics
{
    public abstract void WriteError(string briefMessage, string detailedMessage);
}
```

`StreamingContext` jest przenoszona jako parametr wejściowy do metody UDF i może być używany w formacie UDF do publikowania informacji o dzienniku niestandardowym. W poniższym przykładzie definiuje dane `MyUdfMethod` wejściowe,  które są dostarczane przez zapytanie oraz dane wejściowe **kontekstu** jako `StreamingContext` , dostarczone przez aparat środowiska uruchomieniowego. 

```csharp
public static long MyUdfMethod(long data, StreamingContext context)
{
    // write log
    context.Diagnostics.WriteError("User Log", "This is a log message");
    
    return data;
}
```

`StreamingContext`Wartość nie musi być przesyłana przez zapytanie SQL. Azure Stream Analytics udostępnia obiekt kontekstu automatycznie, jeśli parametr wejściowy jest obecny. Użycie `MyUdfMethod` nie zmienia się, jak pokazano w następującej kwerendzie:

```sql
SELECT udf.MyUdfMethod(input.value) as udfValue FROM input
```

Możesz uzyskać dostęp do dzienników [diagnostycznych](data-errors.md).

## <a name="limitations"></a>Ograniczenia

Wersja zapoznawcza UDF ma obecnie następujące ograniczenia:

* .NET Standard UDF można tworzyć tylko w Visual Studio Code lub Visual Studio i publikować je na platformie Azure. Wersje .NET Standard UDF w trybie tylko do odczytu mogą być wyświetlane w obszarze **funkcje** w Azure Portal. Tworzenie funkcji .NET Standard nie jest obsługiwane w Azure Portal.

* Edytor zapytań Azure Portal zawiera błąd podczas korzystania z .NET Standard UDF w portalu. 

* Ponieważ niestandardowy kod udostępnia kontekst z aparatem Azure Stream Analytics, kod niestandardowy nie może odwoływać się do wszystkich elementów, które mają sprzeczną przestrzeń nazw/dll_name z kodem Azure Stream Analytics. Na przykład nie można odwołać się do *Newtonsoft JSON*.

* Pliki pomocnicze zawarte w projekcie są kopiowane do pliku zip niestandardowego kodu użytkownika, który jest używany podczas publikowania zadania w chmurze. Wszystkie pliki w podfolderach są kopiowane bezpośrednio do katalogu głównego folderu niestandardowego kodu użytkownika w chmurze, gdy jest on niespakowany. Archiwum zip jest "spłaszczone" podczas dekompresji.

* Niestandardowy kod użytkownika nie obsługuje pustych folderów. Nie dodawaj pustych folderów do plików pomocniczych w projekcie.

## <a name="next-steps"></a>Następne kroki

* [Samouczek: pisanie funkcji zdefiniowanej przez użytkownika w języku C# dla zadania Azure Stream Analytics (wersja zapoznawcza)](stream-analytics-edge-csharp-udf.md)
* [Samouczek: funkcje języka JavaScript zdefiniowane przez użytkownika w usłudze Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Utwórz zadanie Azure Stream Analytics w Visual Studio Code](quick-create-visual-studio-code.md)