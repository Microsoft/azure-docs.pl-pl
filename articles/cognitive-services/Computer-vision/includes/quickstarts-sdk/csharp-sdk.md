---
title: 'Szybki Start: przetwarzanie obrazówa Biblioteka kliencka dla platformy .NET'
description: W tym przewodniku Szybki Start zacznij korzystać z biblioteki klienta przetwarzanie obrazów dla platformy .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: d48d0cbac4e0e8b366170d979be3bbc5dd3064ca
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92886679"
---
<a name="HOLTop"></a>

[Dokumentacja](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)  |  [Przykłady](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* [Środowisko IDE programu Visual Studio](https://visualstudio.microsoft.com/vs/) lub bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" Utwórz zasób przetwarzanie obrazów "  target="_blank"> utwórz zasób przetwarzanie obrazów <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu** .
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który utworzysz, aby połączyć aplikację z usługą przetwarzanie obrazów. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-c-application"></a>Utwórz nową aplikację w języku C#

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Za pomocą programu Visual Studio Utwórz nową aplikację platformy .NET Core. 

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką 

Po utworzeniu nowego projektu Zainstaluj bibliotekę kliencką, klikając prawym przyciskiem myszy rozwiązanie projektu w **Eksplorator rozwiązań** i wybierając pozycję **Zarządzaj pakietami NuGet** . W Menedżerze pakietów, który otwiera Wybierz pozycję **Przeglądaj** , zaznacz pozycję **Uwzględnij wersję wstępną** i Wyszukaj `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` . Wybierz wersję `6.0.0-preview.1` , a następnie **Zainstaluj** . 

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `computer-vision-quickstart` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: *ComputerVisionQuickstart.cs* .

```console
dotnet new console -n (product-name)-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

W katalogu aplikacji zainstaluj przetwarzanie obrazówą bibliotekę kliencką dla platformy .NET przy użyciu następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

---

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs), która zawiera przykłady kodu w tym przewodniku Szybki Start.

W katalogu projektu Otwórz plik *ComputerVisionQuickstart.cs* w preferowanym edytorze lub w środowisku IDE. Dodaj następujące `using` dyrektywy:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using)]

W klasie **programu** aplikacji Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_vars)]

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli pomyślnie wdrożono zasób przetwarzanie obrazów w sekcji **wymagania wstępne** , kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki** . Klucz i punkt końcowy można znaleźć na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami** . 
>
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [zabezpieczeń](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) Cognitive Services.

W `Main` metodzie aplikacji Dodaj wywołania metod używanych w tym przewodniku Szybki Start. Zostaną one utworzone później.


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyzeinmain)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu SDK przetwarzanie obrazów .NET.

|Nazwa|Opis|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | Ta klasa jest wymagana dla wszystkich funkcji przetwarzanie obrazów. Utwórz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do wykonania większości operacji na obrazie.|
|[ComputerVisionClientExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| Ta klasa zawiera dodatkowe metody dla **ComputerVisionClient** .|
|[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| To Wyliczenie definiuje różne typy analizy obrazów, które można wykonać przy użyciu standardowej operacji analizy. Należy określić zestaw wartości VisualFeatureTypes w zależności od potrzeb. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań przy użyciu biblioteki klienta przetwarzanie obrazów dla platformy .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analizowanie obrazu](#analyze-an-image)
* [Odczytaj tekst drukowany i odręczny](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

> [!NOTE]
> W tym przewodniku szybki start założono, że [utworzono zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza przetwarzanie obrazów i punktu końcowego o nazwie `COMPUTER_VISION_SUBSCRIPTION_KEY` i `COMPUTER_VISION_ENDPOINT` odpowiednio.

W nowej metodzie Utwórz wystąpienie klienta z punktem końcowym i kluczem. Utwórz obiekt **[ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials?view=azure-dotnet)** z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt **[ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet)** .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]



## <a name="analyze-an-image"></a>Analizowanie obrazu

Poniższy kod definiuje metodę, `AnalyzeImageUrl` która używa obiektu klienta do analizowania obrazu zdalnego i drukowania wyników. Metoda zwraca opis tekstowy, kategoryzację, listę tagów, wykrytych twarzy, flag zawartości dla dorosłych, kolory główne i typ obrazu.

> [!TIP]
> Możesz również analizować obraz lokalny. Zobacz metody [ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) , takie jak **AnalyzeImageInStreamAsync** . Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) , aby poznać scenariusze dotyczące obrazów lokalnych.

### <a name="set-up-test-image"></a>Konfigurowanie obrazu testu

W klasie **programu** Zapisz odwołanie do adresu URL obrazu, który chcesz przeanalizować.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_url)]

### <a name="specify-visual-features"></a>Określ funkcje wizualne

Zdefiniuj nową metodę analizy obrazu. Dodaj poniższy kod, który określa funkcje wizualne, które chcesz wyodrębnić w analizie. Aby uzyskać pełną listę, zobacz Wyliczenie **[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)** .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_visualfeatures)]

Wstaw dowolny z następujących bloków kodu do metody **AnalyzeImageUrl** w celu zaimplementowania ich funkcji. Pamiętaj, aby dodać do końca nawias zamykający.

```csharp
}
```

### <a name="analyze"></a>Analiza

Metoda **AnalyzeImageAsync** zwraca obiekt **ImageAnalysis** , który zawiera wszystkie wyodrębnione informacje.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_call)]

W poniższych sekcjach pokazano, jak szczegółowo analizować te informacje.

### <a name="get-image-description"></a>Pobierz opis obrazu

Poniższy kod pobiera listę wygenerowanych napisów dla obrazu. Aby uzyskać więcej informacji, zobacz [Opis obrazów](../../concept-describing-images.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Pobierz kategorię obrazu

Poniższy kod pobiera wykrytą kategorię obrazu. Aby uzyskać więcej informacji, zobacz [kategoryzowanie obrazów](../../concept-categorizing-images.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Pobierz Tagi obrazu

Poniższy kod pobiera zestaw wykrytych tagów z obrazu. Aby uzyskać więcej informacji, zobacz [Tagi zawartości](../../concept-tagging-images.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Wykrywanie obiektów

Poniższy kod wykrywa typowe obiekty w obrazie i drukuje je do konsoli programu. Aby uzyskać więcej informacji, zobacz [wykrywanie obiektów](../../concept-object-detection.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Wykrywanie marek

Poniższy kod wykrywa marki i logo firmy w obrazie i drukuje je do konsoli programu. Aby uzyskać więcej informacji, zobacz [wykrywanie marki](../../concept-brand-detection.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Wykrywanie twarzy

Poniższy kod zwraca wykryte twarze na obrazie ze współrzędnymi prostokątów i wybierz atrybuty twarzy. Aby uzyskać więcej informacji, zobacz [wykrywanie czołowe](../../concept-detecting-faces.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Wykrywanie zawartości dla dorosłych, erotycznej lub gorii

Poniższy kod drukuje wykryte obecność treści dla dorosłych w obrazie. Aby uzyskać więcej informacji, zobacz [erotycznej, gorii Content](../../concept-detecting-adult-content.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Pobierz schemat kolorów obrazu

Poniższy kod drukuje wykryte atrybuty koloru w obrazie, takie jak kolory dominujące i kolor akcentu. Zobacz [schematy kolorów](../../concept-detecting-color-schemes.md) , aby uzyskać więcej szczegółów.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Pobieranie zawartości specyficznej dla domeny

Przetwarzanie obrazów mogą korzystać z wyspecjalizowanych modeli w celu przeprowadzenia dalszej analizy obrazów. Aby uzyskać więcej informacji, zobacz [zawartość specyficzną dla domeny](../../concept-detecting-domain-content.md) . 

Poniższy kod analizuje dane dotyczące wykrytych osobistości w obrazie.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_celebs)]

Poniższy kod analizuje dane dotyczące wykrytych punktów orientacyjnych w obrazie.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Pobierz typ obrazu

Poniższy kod drukuje informacje o typie obrazu, niezależnie od tego, &mdash; czy jest to obiekt clipart czy rysunek liniowy.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Odczytaj tekst drukowany i odręczny

Przetwarzanie obrazów może odczytać widoczny tekst w obrazie i przekonwertować go na strumień znaków. Aby uzyskać więcej informacji na temat rozpoznawania tekstu, zobacz dokument koncepcyjny koncepcyjnego [rozpoznawania znaków (OCR)](../../concept-recognizing-text.md#read-api) . Kod w tej sekcji używa najnowszej [wersji zestawu SDK przetwarzanie obrazów dla odczytu 3,0](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/6.0.0-preview.1) i definiuje metodę, `BatchReadFileUrl` która używa obiektu klienta do wykrywania i wyodrębniania tekstu w obrazie.

> [!TIP]
> Możesz również wyodrębnić tekst z obrazu lokalnego. Zobacz metody [ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) , takie jak **ReadInStreamAsync** . Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) , aby poznać scenariusze dotyczące obrazów lokalnych.

### <a name="set-up-test-image"></a>Konfigurowanie obrazu testu

W klasie **programu** Zapisz odwołanie do adresu URL obrazu, z którego ma zostać wyodrębniony tekst. Ten fragment kodu zawiera przykładowe obrazy dla wydrukowanych i odręcznych tekstu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>Wywoływanie interfejsu API odczytu

Zdefiniuj nową metodę odczytywania tekstu. Dodaj poniższy kod, który wywołuje metodę **ReadAsync** dla danego obrazu. Zwraca identyfikator operacji i uruchamia proces asynchroniczny w celu odczytania zawartości obrazu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_url)]

### <a name="get-read-results"></a>Pobierz wyniki odczytu

Następnie Pobierz identyfikator operacji zwrócony z wywołania **ReadAsync** i użyj go do wysyłania zapytań do usługi w celu uzyskania wyników operacji. Poniższy kod sprawdza operację do momentu zwrócenia wyników. Następnie program drukuje wyodrębnione dane tekstowe do konsoli programu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_response)]

### <a name="display-read-results"></a>Wyświetl wyniki odczytu

Dodaj następujący kod, aby przeanalizować i wyświetlić pobrane dane tekstowe, a następnie Zakończ definicję metody.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_display)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Uruchom aplikację, klikając przycisk **Debuguj** w górnej części okna środowiska IDE.

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

Uruchom aplikację z katalogu aplikacji za pomocą `dotnet run` polecenia.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Odwołanie interfejs API przetwarzania obrazów (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)

* [Czym jest przetwarzanie obrazów?](../../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).
