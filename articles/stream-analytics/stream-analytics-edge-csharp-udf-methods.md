---
title: Opracowywanie funkcji .NET Standard dla zadań Azure Stream Analytics (wersja zapoznawcza)
description: Dowiedz się, jak napisać funkcje zdefiniowane przez użytkownika w języku c# dla Stream Analytics zadań.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: f07c02df1b8e0032c9e1b4ef9a24c345fee20a40
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75426314"
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

Format dowolnego pakietu UDF ma ścieżkę `/UserCustomCode/CLR/*`. Biblioteki dołączane dynamicznie (dll) i zasoby są kopiowane `/UserCustomCode/CLR/*` do folderu, co ułatwia izolowanie bibliotek DLL użytkowników z systemów i Azure Stream Analytics bibliotek DLL. Ta ścieżka pakietu jest używana dla wszystkich funkcji niezależnie od metody używanej do ich używania.

## <a name="supported-types-and-mapping"></a>Obsługiwane typy i mapowanie

|**UDF — typ (C#)**  |**Typ Azure Stream Analytics**  |
|---------|---------|
|długi  |  bigint   |
|double  |  double   |
|ciąg  |  nvarchar (max)   |
|Data i godzina  |  Data i godzina   |
|struktura   |  IRecord   |
|obiekt  |  IRecord   |
|>\<obiektu tablicy  |  IArray   |
|<słownika, ciąg>  |  IRecord   |

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
6. Skonfiguruj ścieżkę zestawu w pliku konfiguracji zadania, `JobConfig.json`. Ustaw ścieżkę zestawu na **odwołanie do projektu lokalnego lub CodeBehind**.
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

6. Dodaj funkcję języka C# **SquareFunction. JSON** do projektu Azure Stream Analytics.

   ![Wybierz funkcję CSharp z elementów krawędzi Stream Analytics w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Kliknij dwukrotnie funkcję w **Eksplorator rozwiązań** , aby otworzyć okno dialogowe konfiguracji.

   ![Konfiguracja funkcji Sharp języka C w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. W konfiguracji funkcji języka C# wybierz pozycję **Załaduj z odwołania do projektu ASA** i powiązanego zestawu, klasy i nazw metod z listy rozwijanej. Aby odwołać się do metod, typów i funkcji w kwerendzie Stream Analytics, klasy muszą być zdefiniowane jako *publiczne* , a obiekty muszą być zdefiniowane jako *statyczne publiczne*.

   ![Konfiguracja funkcji Sharp Stream Analytics C](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Istniejące pakiety

Możesz tworzyć .NET Standard UDF w dowolnym dowolnym środowisku IDE i wywoływać je z poziomu zapytania Azure Stream Analytics. Najpierw Skompiluj swój kod i Spakuj wszystkie biblioteki DLL. Format pakietu ma ścieżkę `/UserCustomCode/CLR/*`. Następnie Przekaż `UserCustomCode.zip` do katalogu głównego kontenera na koncie usługi Azure Storage.

Po przekazaniu pakietów zip zestawu do konta usługi Azure Storage można użyć funkcji w Azure Stream Analytics zapytaniach. Wszystko, co należy zrobić, obejmuje informacje o magazynie w konfiguracji zadania Stream Analytics. Nie można przetestować funkcji lokalnie przy użyciu tej opcji, ponieważ narzędzia Visual Studio Tools nie pobierają pakietu. Ścieżka pakietu jest analizowana bezpośrednio w usłudze. 

Aby skonfigurować ścieżkę zestawu w pliku konfiguracji zadania `JobConfig.json`:

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
   |Źródło niestandardowego zestawu kodu|UserCustomCode. zip|

## <a name="limitations"></a>Ograniczenia
Wersja zapoznawcza UDF ma obecnie następujące ograniczenia:

* .NET Standard UDF można tworzyć tylko w programie Visual Studio i publikować je na platformie Azure. Wersje .NET Standard UDF w trybie tylko do odczytu mogą być wyświetlane w obszarze **funkcje** w Azure Portal. Tworzenie funkcji .NET Standard nie jest obsługiwane w Azure Portal.

* Edytor zapytań Azure Portal zawiera błąd podczas korzystania z .NET Standard UDF w portalu. 

* Ponieważ niestandardowy kod udostępnia kontekst z aparatem Azure Stream Analytics, kod niestandardowy nie może odwoływać się do wszystkich elementów, które mają sprzeczną przestrzeń nazw/dll_name z kodem Azure Stream Analytics. Na przykład nie można odwołać się do *Newtonsoft JSON*.

## <a name="next-steps"></a>Następne kroki

* [Samouczek: pisanie funkcji zdefiniowanej przez użytkownika w języku C# dla zadania Azure Stream Analytics (wersja zapoznawcza)](stream-analytics-edge-csharp-udf.md)
* [Samouczek: funkcje języka JavaScript zdefiniowane przez użytkownika w usłudze Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)
