---
title: Opracowywanie funkcji .NET Standard dla zadań Azure Stream Analytics (wersja zapoznawcza)
description: Dowiedz się, jak napisać funkcje zdefiniowane przez użytkownika w języku c# dla Stream Analytics zadań.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: 4f9d117ccc763744411bfe24163ed955532e8e56
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921860"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Opracowywanie .NET Standard funkcji zdefiniowanych przez użytkownika dla zadań Azure Stream Analytics (wersja zapoznawcza)

Azure Stream Analytics oferuje język zapytań przypominający SQL, służący do wykonywania transformacji i obliczeń na strumieniach danych zdarzeń. Istnieje wiele wbudowanych funkcji, ale niektóre złożone scenariusze wymagają dodatkowej elastyczności. Za pomocą .NET Standard funkcji zdefiniowanych przez użytkownika (UDF) można wywoływać własne funkcje, które są zapisywane w dowolnym języku .NET Standard (C#, F # itp.), aby zwiększyć Stream Analytics języka zapytań. UDF umożliwiają wykonywanie złożonych obliczeń matematycznych, importowanie niestandardowych modeli ml przy użyciu ml.NET i używanie niestandardowych reguł przypisywania w przypadku brakujących danych. Funkcja UDF dla Stream Analytics zadań jest obecnie w wersji zapoznawczej i nie powinna być używana w obciążeniach produkcyjnych.

Funkcja zdefiniowana przez użytkownika platformy .NET dla zadań w chmurze jest dostępna w:
* Zachodnio-środkowe stany USA
* Europa Północna
* Wschodnie stany USA
* Zachodnie stany USA
* Wschodnie stany USA 2
* Europa Zachodnia

Jeśli interesuje Cię korzystanie z tej funkcji w innym regionie, możesz [poprosić o dostęp](https://aka.ms/ccodereqregion).

## <a name="overview"></a>Omówienie
Narzędzia Visual Studio Tools for Azure Stream Analytics ułatwiają pisanie UDF, testowanie zadań lokalnie (nawet w trybie offline) i publikowanie zadań Stream Analytics na platformie Azure. Po opublikowaniu na platformie Azure można wdrożyć zadanie na urządzeniach IoT przy użyciu IoT Hub.

Istnieją trzy sposoby implementowania funkcji zdefiniowanych przez użytkownika:

* CodeBehind pliki w projekcie ASA
* UDF z lokalnego projektu
* Istniejący pakiet z konta usługi Azure Storage

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

## <a name="codebehind"></a>CodeBehind
Funkcje zdefiniowane przez użytkownika można napisać w **skrypcie. ASQL** Codebehind. Narzędzia programu Visual Studio automatycznie kompilują plik CodeBehind do pliku zestawu. Zestawy są spakowane jako plik zip i przekazywane do konta magazynu podczas przesyłania zadania do platformy Azure. Możesz dowiedzieć się, jak napisać UDF języka C# przy użyciu CodeBehind, postępując zgodnie z samouczkiem [UDF języka c# for Stream Analytics Edge](stream-analytics-edge-csharp-udf.md) . 

## <a name="local-project"></a>Projekt lokalny
Funkcje zdefiniowane przez użytkownika mogą być zapisywane w zestawie, który jest później przywoływany w kwerendzie Azure Stream Analytics. Jest to zalecana opcja dla funkcji złożonych, które wymagają pełnej mocy języka .NET Standard poza językiem wyrażeń, na przykład logiki proceduralnej lub rekursji. UDF z projektu lokalnego może być również używany, gdy trzeba udostępnić logikę funkcji w kilku Azure Stream Analyticsych zapytaniach. Dodanie UDF do projektu lokalnego umożliwia debugowanie i testowanie funkcji lokalnie w programie Visual Studio.

Aby odwołać się do projektu lokalnego:

1. Utwórz nową bibliotekę klas w rozwiązaniu.
2. Napisz kod w klasie. Należy pamiętać, że klasy muszą być zdefiniowane jako *publiczne* , a obiekty muszą być zdefiniowane jako *statyczne publiczne*. 
3. Skompilowanie projektu. Narzędzia będą pakować wszystkie artefakty w folderze bin do pliku zip i przekazać plik zip na konto magazynu. W przypadku odwołań zewnętrznych należy użyć odwołania do zestawu zamiast pakietu NuGet.
4. Odwołuje się do nowej klasy w projekcie Azure Stream Analytics.
5. Dodaj nową funkcję w projekcie Azure Stream Analytics.
6. Skonfiguruj ścieżkę zestawu w pliku konfiguracji zadania, `JobConfig.json` . Ustaw ścieżkę zestawu na **odwołanie do projektu lokalnego lub CodeBehind**.
7. Kompiluj zarówno projekt funkcji, jak i projekt Azure Stream Analytics.  

### <a name="example"></a>Przykład

W tym przykładzie **UDFTest** jest projektem biblioteki klas języka C#, a **ASAUDFDemo** to projekt Azure Stream Analytics, który będzie odwoływać się do **UDFTest**.

![Azure Stream Analytics IoT Edge projektu w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Skompiluj projekt C#, który umożliwi dodanie odwołania do formatu UDF języka C# z kwerendy Azure Stream Analytics.
    
   ![Tworzenie projektu IoT Edge Azure Stream Analytics w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Dodaj odwołanie do projektu C# w projekcie ASA. Kliknij prawym przyciskiem myszy węzeł odwołania i wybierz polecenie Dodaj odwołanie.

   ![Dodawanie odwołania do projektu C# w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Wybierz nazwę projektu C# z listy. 
    
   ![Wybierz nazwę projektu C# z listy referencyjnej](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. W obszarze **References** ( **Eksplorator rozwiązań**) powinna zostać wyświetlona pozycja **UDFTest** .

   ![Wyświetl odwołanie do funkcji zdefiniowanej przez użytkownika w Eksploratorze rozwiązań](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Kliknij prawym przyciskiem myszy folder **Functions** i wybierz polecenie **nowy element**.

   ![Dodaj nowy element do funkcji w rozwiązaniu Azure Stream Analytics Edge](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Dodaj funkcję języka C# **SquareFunction.js** do projektu Azure Stream Analytics.

   ![Wybierz funkcję CSharp z elementów krawędzi Stream Analytics w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Kliknij dwukrotnie funkcję w **Eksplorator rozwiązań** , aby otworzyć okno dialogowe konfiguracji.

   ![Konfiguracja funkcji Sharp języka C w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. W konfiguracji funkcji języka C# wybierz pozycję **Załaduj z odwołania do projektu ASA** i powiązanego zestawu, klasy i nazw metod z listy rozwijanej. Aby odwołać się do metod, typów i funkcji w kwerendzie Stream Analytics, klasy muszą być zdefiniowane jako *publiczne* , a obiekty muszą być zdefiniowane jako *statyczne publiczne*.

   ![Konfiguracja funkcji Sharp Stream Analytics C](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

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

`StreamingContext`jest przenoszona jako parametr wejściowy do metody UDF i może być używany w formacie UDF do publikowania informacji o dzienniku niestandardowym. W poniższym przykładzie definiuje dane `MyUdfMethod` wejściowe, **data** które są dostarczane przez zapytanie oraz dane wejściowe **kontekstu** jako `StreamingContext` , dostarczone przez aparat środowiska uruchomieniowego. 

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

* .NET Standard UDF można tworzyć tylko w programie Visual Studio i publikować je na platformie Azure. Wersje .NET Standard UDF w trybie tylko do odczytu mogą być wyświetlane w obszarze **funkcje** w Azure Portal. Tworzenie funkcji .NET Standard nie jest obsługiwane w Azure Portal.

* Edytor zapytań Azure Portal zawiera błąd podczas korzystania z .NET Standard UDF w portalu. 

* Ponieważ niestandardowy kod udostępnia kontekst z aparatem Azure Stream Analytics, kod niestandardowy nie może odwoływać się do wszystkich elementów, które mają sprzeczną przestrzeń nazw/dll_name z kodem Azure Stream Analytics. Na przykład nie można odwołać się do *Newtonsoft JSON*.

* Pliki pomocnicze zawarte w projekcie są kopiowane do pliku zip niestandardowego kodu użytkownika, który jest używany podczas publikowania zadania w chmurze. Wszystkie pliki w podfolderach są kopiowane bezpośrednio do katalogu głównego folderu niestandardowego kodu użytkownika w chmurze, gdy jest on niespakowany. Archiwum zip jest "spłaszczone" podczas dekompresji.

* Niestandardowy kod użytkownika nie obsługuje pustych folderów. Nie dodawaj pustych folderów do plików pomocniczych w projekcie.

## <a name="next-steps"></a>Następne kroki

* [Samouczek: pisanie funkcji zdefiniowanej przez użytkownika w języku C# dla zadania Azure Stream Analytics (wersja zapoznawcza)](stream-analytics-edge-csharp-udf.md)
* [Samouczek: funkcje języka JavaScript zdefiniowane przez użytkownika w usłudze Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)
