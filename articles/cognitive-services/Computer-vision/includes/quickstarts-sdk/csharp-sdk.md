---
title: 'Szybki start: biblioteka klienta przetwarzania obrazów .NET'
description: W tym przewodniku Szybki start rozpocznij pracę z biblioteką klienta usługi .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: ce311d2deaf0eec76ef0dec3ea279a387d14fd22
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81637132"
---
<a name="HOLTop"></a>

[Przykłady](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) | [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | referencyjnej[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Samples](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Bieżąca wersja programu [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-computer-vision-azure-resource"></a>Tworzenie zasobu platformy Computer Vision Azure

Usługi Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla przetwarzania obrazów komputerowych przy użyciu [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Pobierz [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez siedem dni za darmo. Po zarejestrowaniu się będzie on dostępny w [witrynie sieci Web platformy Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Wyświetl swój zasób w [witrynie Azure portal](https://portal.azure.com/).

Po otrzymasz klucz z subskrypcji próbnej lub zasobu, [utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza i adresu URL punktu końcowego, odpowiednio `COMPUTER_VISION_SUBSCRIPTION_KEY` i `COMPUTER_VISION_ENDPOINT`, odpowiednio.

### <a name="create-a-new-c-application"></a>Tworzenie nowej aplikacji języka C#

Utwórz nową aplikację .NET Core w preferowanym edytorze lub ide. 

W oknie konsoli (takim jak cmd, PowerShell `dotnet new` lub Bash) użyj polecenia, `computer-vision-quickstart`aby utworzyć nową aplikację konsoli o nazwie . To polecenie tworzy prosty projekt "Hello World" C# z jednym plikiem źródłowym: *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można utworzyć za pomocą:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać żadnych ostrzeżeń ani błędów. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

W katalogu projektu otwórz plik *Program.cs* w preferowanym edytorze lub w programie IDE. Dodaj następujące `using` dyrektywy:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_using)]

W klasie **Program** aplikacji utwórz zmienne dla punktu końcowego i klucza platformy Azure zasobu.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_vars)]

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

W katalogu aplikacji zainstaluj bibliotekę klienta usługi Computer Vision dla platformy .NET za pomocą następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 5.0.0
```

Jeśli używasz środowiska IDE programu Visual Studio, biblioteka klienta jest dostępna jako pakiet NuGet do pobrania.

## <a name="object-model"></a>Model obiektu

Następujące klasy i interfejsy obsługują niektóre z głównych funkcji interfejsu SDK .NET vision.

|Nazwa|Opis|
|---|---|
| [ComputerVisionClient (System obsługi komputerów)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | Ta klasa jest potrzebna dla wszystkich funkcji przetwarzania. Tworzenie wystąpienia z informacjami o subskrypcji i używać go do wykonywania większości operacji obrazu.|
|[ComputerVisionClientExtensions (Listy do)-eksclientextensions ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| Ta klasa zawiera dodatkowe metody dla **ComputerVisionClient**.|
|[VisualFeatureTytyty](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| Wyliczenie to definiuje różne typy analizy obrazu, które można wykonać w standardowej operacji analizy. Należy określić zestaw visualfeatureTypes wartości w zależności od potrzeb. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta przetwarzania.NET:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Analizowanie obrazu](#analyze-an-image)
* [Czytanie drukowanego i odręcznego tekstu](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

> [!NOTE]
> Ten przewodnik Szybki start zakłada, że [utworzono zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza i punktu końcowego przetwarzania wizji komputerowej, nazwanych `COMPUTER_VISION_SUBSCRIPTION_KEY` i `COMPUTER_VISION_ENDPOINT` odpowiednio.

W nowej metodzie wystąpienia klienta z punktem końcowym i kluczem. Utwórz **[obiekt ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials?view=azure-dotnet)** za pomocą klucza i użyj go z punktem końcowym do utworzenia obiektu **[ComputerVisionClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet)**

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_auth)]

Prawdopodobnie będziesz chciał wywołać tę `Main` metodę w metodzie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_client)]

## <a name="analyze-an-image"></a>Analizowanie obrazu

Poniższy kod definiuje metodę `AnalyzeImageUrl`, która używa obiektu klienta do analizowania obrazu zdalnego i drukowania wyników. Metoda zwraca opis tekstowy, kategoryzację, listę znaczników, wykryte twarze, flagi zawartości dla dorosłych, główne kolory i typ obrazu.

Dodaj wywołanie metody `Main` w metodzie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyzeinmain)]

### <a name="set-up-test-image"></a>Konfigurowanie obrazu testowego

W klasie **Program** zapisz odwołanie do adresu URL obrazu, który chcesz przeanalizować.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_url)]

> [!NOTE]
> Można również analizować obraz lokalny. Zobacz przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) uzyskać scenariusze dotyczące obrazów lokalnych.

### <a name="specify-visual-features"></a>Określanie obiektów wizualnych

Zdefiniuj nową metodę analizy obrazu. Dodaj poniższy kod, który określa funkcje wizualne, które chcesz wyodrębnić w analizie. Zobacz **[VisualFeatureTypes wyliczenia](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)** dla pełnej listy.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_visualfeatures)]

Wstaw dowolny z następujących bloków kodu do **analyzeimageurl** metody, aby zaimplementować ich funkcje. Pamiętaj, aby dodać nawias zamykający na końcu.

```csharp
}
```

### <a name="analyze"></a>Analiza

**Metoda AnalyzeImageAsync** zwraca obiekt **ImageAnalysis,** który zawiera wszystkie wyodrębnione informacje.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_call)]

W poniższych sekcjach pokazano, jak szczegółowo przeanalizować te informacje.

### <a name="get-image-description"></a>Pobierz opis obrazu

Poniższy kod pobiera listę wygenerowanych podpisów dla obrazu. Zobacz [Opisz obrazy,](../../concept-describing-images.md) aby uzyskać więcej informacji.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Pobierz kategorię obrazu

Poniższy kod pobiera wykrytą kategorię obrazu. Aby uzyskać więcej informacji, [zobacz Kategoryzowanie obrazów.](../../concept-categorizing-images.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Pobierz znaczniki obrazów

Poniższy kod pobiera zestaw wykrytych tagów na obrazie. Aby uzyskać więcej informacji, zobacz [Tagi zawartości.](../../concept-tagging-images.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Wykrywanie obiektów

Poniższy kod wykrywa typowe obiekty na obrazie i drukuje je na konsoli. Zobacz [Wykrywanie obiektów,](../../concept-object-detection.md) aby uzyskać więcej informacji.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Wykrywanie marek

Poniższy kod wykrywa marki firmowe i logo na obrazie i drukuje je na konsoli. Aby uzyskać więcej informacji, zobacz [Wykrywanie marki.](../../concept-brand-detection.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Wykrywanie twarzy

Poniższy kod zwraca wykryte twarze na obrazie z ich współrzędne prostokąta i wybierz atrybuty twarzy. Zobacz [Wykrywanie twarzy, aby](../../concept-detecting-faces.md) uzyskać więcej informacji.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Wykrywanie treści dla dorosłych, rasistowskich lub gory

Poniższy kod drukuje wykrytą obecność zawartości dla dorosłych na obrazie. Zobacz [Adult, racy, gory treści](../../concept-detecting-adult-content.md) więcej szczegółów.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Pobierz schemat kolorów obrazu

Poniższy kod drukuje wykryte atrybuty kolorów obrazu, takie jak kolory dominujące i kolor akcentu. Zobacz [schematy kolorów, aby](../../concept-detecting-color-schemes.md) uzyskać więcej informacji.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Pobierz zawartość specyficzną dla domeny

Usługa Computer Vision może używać specjalistycznych modeli do dalszej analizy obrazów. Aby uzyskać więcej informacji, zobacz [Zawartość specyficzna](../../concept-detecting-domain-content.md) dla domeny. 

Poniższy kod analizuje dane dotyczące wykrytych gwiazd na obrazie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_celebs)]

Poniższy kod analizuje dane dotyczące wykrytych punktów orientacyjnych na obrazie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Pobierz typ obrazu

Poniższy kod drukuje informacje o&mdash;typie obrazu, niezależnie od tego, czy jest to obiekt clipart, czy rysunek liniowy.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Czytanie drukowanego i odręcznego tekstu

Funkcja Wizja komputerowa może odczytywać widoczny tekst na obrazie i konwertować go na strumień znaków. Kod w tej sekcji definiuje `ExtractTextUrl`metodę, która używa obiektu klienta do wykrywania i wyodrębniania drukowanego lub odręcznego tekstu na obrazie.

Dodaj wywołanie metody `Main` w metodzie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttextinmain)]

### <a name="set-up-test-image"></a>Konfigurowanie obrazu testowego

W klasie **Program** zapisz adres URL obrazu, z którego chcesz wyodrębnić tekst.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttext_url)]

> [!NOTE]
> Można również wyodrębnić tekst z obrazu lokalnego. Zobacz przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) uzyskać scenariusze dotyczące obrazów lokalnych.

### <a name="call-the-read-api"></a>Wywołanie interfejsu API odczytu

Zdefiniuj nową metodę czytania tekstu. Dodaj poniższy kod, który wywołuje **BatchReadFileAsync** metody dla danego obrazu. Zwraca identyfikator operacji i rozpoczyna proces asynchroniczne, aby odczytać zawartość obrazu.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_call)]

### <a name="get-read-results"></a>Uzyskaj wyniki odczytu

Następnie pobierz identyfikator operacji zwrócony z **wywołania BatchReadFileAsync** i użyj go do kwerendy usługi dla wyników operacji. Poniższy kod sprawdza operację w odstępach jednosekundowych, dopóki wyniki nie zostaną zwrócone. Następnie drukuje wyodrębnione dane tekstowe na konsoli.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_response)]

### <a name="display-read-results"></a>Wyświetlanie wyników odczytu

Dodaj następujący kod, aby przeanalizować i wyświetlić pobrane dane tekstowe i zakończyć definicję metody.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_display)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację z katalogu aplikacji `dotnet run` za pomocą polecenia.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Odwołanie do interfejsu API przetwarzania obrazów komputerowych (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)

* [Czym jest przetwarzanie obrazów?](../../Home.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs).
