---
title: 'Szybki start: biblioteka klienta analizy obrazów dla programu .NET'
description: W tym przewodniku Szybki start rozpoczniesz pracę z biblioteką klienta analizy obrazów dla programu .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 03/29/2021
ms.author: pafarley
ms.openlocfilehash: 0af6c97d6179a645b078f2335ff38f48890c42a3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728198"
---
<a name="HOLTop"></a>

Za pomocą biblioteki klienta analizy obrazów można analizować obraz pod względu na tagi, opis tekstu, twarze, zawartość dla dorosłych i nie tylko.

[Dokumentacja referencyjna](/dotnet/api/overview/azure/cognitiveservices/client/computervision)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision)  |  [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)  |  [Przykłady](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [bezpłatne tworzenie subskrypcji](https://azure.microsoft.com/free/cognitive-services/)
* Program [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) lub bieżącą wersję programu [.NET Core.](https://dotnet.microsoft.com/download/dotnet-core)
* Po utworzeniu subskrypcji platformy Azure utwórz zasób przetwarzanie obrazów zasobów przetwarzanie obrazów w witrynie Azure Portal, aby uzyskać <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Po wdrożeniu kliknij pozycję **Przejdź do zasobu**.
    * Klucz i punkt końcowy będą potrzebne z zasobu, który utworzysz, aby połączyć aplikację z przetwarzanie obrazów usługą. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
    * Możesz użyć bezpłatnej warstwy cenowej ( ), aby wypróbować usługę, a następnie uaktualnić ją do warstwy `F0` płatnej w środowisku produkcyjnym.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-c-application"></a>Tworzenie nowej aplikacji w języku C#

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Za Visual Studio utwórz nową aplikację .NET Core. 

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta 

Po utworzeniu nowego projektu zainstaluj bibliotekę klienta, klikając prawym przyciskiem myszy rozwiązanie projektu w Eksplorator rozwiązań **i** wybierając pozycję Zarządzaj pakietami **NuGet.** W menedżerze pakietów, który zostanie otwarty, wybierz **pozycję Przeglądaj,** zaznacz pole **wyboru Uwzględnij wstępną publikację** i `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` wyszukaj . Wybierz wersję `7.0.0` , a następnie pozycję **Zainstaluj.** 

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

W oknie konsoli (takim jak cmd, PowerShell lub Bash) użyj polecenia , aby utworzyć nową aplikację `dotnet new` konsolową o nazwie `computer-vision-quickstart` . To polecenie tworzy prosty projekt języka C# "Hello world" z pojedynczym plikiem źródłowym: *Program.cs.*

```console
dotnet new console -n computer-vision-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować za pomocą:

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

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

W katalogu aplikacji zainstaluj bibliotekę klienta przetwarzanie obrazów dla programu .NET za pomocą następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 7.0.0
```

---

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Znajdziesz go w witrynie [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)która zawiera przykłady kodu z tego przewodnika Szybki start.

W katalogu projektu otwórz plik *Program.cs* w preferowanym edytorze lub w środowiskach IDE. Dodaj następujące `using` dyrektywy:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using)]

W klasie **Program** aplikacji utwórz zmienne dla punktu końcowego i klucza platformy Azure zasobu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_vars)]

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasób przetwarzanie obrazów utworzony w sekcji **Wymagania wstępne** został pomyślnie wdrożony, kliknij przycisk Przejdź do zasobu **w** obszarze **Następne kroki.** Klucz i punkt końcowy można znaleźć na stronie klucza **i** punktu końcowego zasobu w obszarze **zarządzania zasobami**. 
>
> Pamiętaj, aby usunąć klucz z kodu, gdy wszystko będzie gotowe, i nigdy nie publikować go publicznie. W środowisku produkcyjnym rozważ użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Zobacz artykuł Cognitive Services [zabezpieczeń,](../../../cognitive-services-security.md) aby uzyskać więcej informacji.

W metodzie aplikacji dodaj wywołania metod używanych `Main` w tym przewodniku Szybki start. Utworzysz je później.


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyzeinmain)]

> [!div class="nextstepaction"]
> [Po skonfigurowaniu klienta,](?success=set-up-client#object-model) [dla których występuje problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu SDK .NET analizy obrazów.

|Nazwa|Opis|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Ta klasa jest potrzebna dla wszystkich przetwarzanie obrazów funkcjonalności. Należy utworzyć jego wystąpienia z informacjami o subskrypcji i używać ich do większości operacji na obrazach.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| Ta klasa zawiera dodatkowe metody klasy **ComputerVisionClient.**|
|[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| To wylinie definiuje różne typy analizy obrazów, które można wykonać w standardowej operacji analizy. W zależności od potrzeb należy określić zestaw wartości VisualFeatureTypes. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta analizy obrazów dla programu .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analizowanie obrazu](#analyze-an-image)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

> [!NOTE]
> W tym przewodniku Szybki [](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) start założono, że utworzono zmienne środowiskowe dla klucza przetwarzanie obrazów punktu końcowego o nazwach `COMPUTER_VISION_SUBSCRIPTION_KEY` i `COMPUTER_VISION_ENDPOINT` .

W nowej metodzie w klasie **Program** należy utworzyć wystąpienia klienta z punktem końcowym i kluczem. Utwórz obiekt **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** za pomocą klucza i użyj go z punktem końcowym, aby utworzyć obiekt **[ComputerVisionClient.](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Uwierzytelniono klienta,](?success=authenticate-client#analyze-an-image) [na który natłożyłem problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analizowanie obrazu

Poniższy kod definiuje metodę , która używa obiektu klienta do analizowania obrazu zdalnego `AnalyzeImageUrl` i drukowania wyników. Metoda zwraca opis tekstowy, kategoryzację, listę tagów, wykryte twarze, flagi zawartości dla dorosłych, główne kolory i typ obrazu.

> [!TIP]
> Można również analizować obraz lokalny. Zobacz metody [ComputerVisionClient,](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) takie jak **AnalyzeImageInStreamAsync.** Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) uzyskać scenariusze obejmujące obrazy lokalne.

### <a name="set-up-test-image"></a>Konfigurowanie obrazu testowego

W klasie **Program** zapisz odwołanie do adresu URL obrazu, który chcesz przeanalizować.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_url)]

### <a name="specify-visual-features"></a>Określanie cech wizualnych

Zdefiniuj nową metodę analizy obrazów. Dodaj poniższy kod, który określa cechy wizualne, które chcesz wyodrębnić w analizie. Pełną listę można znaleźć w wylicie **[VisualFeatureTypes.](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_visualfeatures)]

### <a name="call-the-analyze-api"></a>Wywołanie interfejsu API analizy

Metoda **AnalyzeImageAsync** zwraca obiekt **ImageAnalysis,** który zawiera wszystkie wyodrębnione informacje.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_call)]

W poniższych sekcjach przedstawiono sposób szczegółowej analizy tych informacji.

Wstaw dowolny z poniższych bloków kodu do metody **AnalyzeImageUrl,** aby przeanalizować dane z żądanych powyżej funkcji wizualnych. Pamiętaj, aby dodać nawias zamykający na końcu.

```csharp
}
```

### <a name="get-image-description"></a>Uzyskiwanie opisu obrazu

Poniższy kod pobiera listę wygenerowanych podpisów dla obrazu. Aby [uzyskać więcej informacji,](../../concept-describing-images.md) zobacz Opisywanie obrazów.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Uzyskiwanie kategorii obrazów

Poniższy kod pobiera wykrytą kategorię obrazu. Aby uzyskać więcej informacji, zobacz [Kategoryzowanie](../../concept-categorizing-images.md) obrazów.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Uzyskiwanie tagów obrazów

Poniższy kod pobiera zestaw wykrytych tagów na obrazie. Aby [uzyskać więcej informacji,](../../concept-tagging-images.md) zobacz Tagi zawartości.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Wykrywanie obiektów

Poniższy kod wykrywa typowe obiekty na obrazie i drukuje je w konsoli. Aby [uzyskać więcej informacji,](../../concept-object-detection.md) zobacz Wykrywanie obiektów.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Wykrywanie marek

Poniższy kod wykrywa firmowe marki i logo na obrazie i drukuje je w konsoli. Aby [uzyskać więcej informacji,](../../concept-brand-detection.md) zobacz Wykrywanie marki.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Wykrywanie twarzy

Poniższy kod zwraca wykryte twarze na obrazie z ich współrzędnymi prostokąta i wybiera atrybuty twarzy. Aby uzyskać [więcej informacji,](../../concept-detecting-faces.md) zobacz Wykrywanie twarzy.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Wykrywanie zawartości dla dorosłych, racy lub gory

Poniższy kod drukuje wykrytą obecność treści dla dorosłych na obrazie. Aby uzyskać więcej informacji, zobacz zawartość dla [dorosłych, racy i gory.](../../concept-detecting-adult-content.md)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Uzyskiwanie schematu kolorów obrazu

Poniższy kod drukuje wykryte atrybuty kolorów na obrazie, takie jak dominujący kolor i kolor akcentu. Aby [uzyskać więcej informacji,](../../concept-detecting-color-schemes.md) zobacz Schematy kolorów.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Uzyskiwanie zawartości specyficznej dla domeny

Analiza obrazów może używać wyspecjalizowanych modeli do dalszej analizy obrazów. Aby uzyskać [więcej informacji,](../../concept-detecting-domain-content.md) zobacz Zawartość specyficzna dla domeny. 

Poniższy kod analizuje dane dotyczące wykrytych osobistości na obrazie.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_celebs)]

Poniższy kod analizuje dane dotyczące wykrytych punktów orientacyjnych na obrazie.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Uzyskiwanie typu obrazu

Poniższy kod drukuje informacje o typie obrazu, niezależnie od tego, czy jest &mdash; to obiekt clipart, czy rysunek liniowy.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_type)]

> [!div class="nextstepaction"]
> [Przeanalizowano obraz,](?success=analyze-image#run-the-application) [na który natłożyłem problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=analyze-image)



## <a name="run-the-application"></a>Uruchamianie aplikacji

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Uruchom aplikację, klikając przycisk **Debug (Debuguj)** w górnej części okna środowiska IDE.

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

Uruchom aplikację z katalogu aplikacji za pomocą `dotnet run` polecenia .

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie wszystkich innych skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób instalowania biblioteki klienta analizy obrazów i dokonywania podstawowych wywołań analizy obrazów. Następnie dowiedz się więcej o funkcjach interfejsu API analizowania.


> [!div class="nextstepaction"]
>[Wywołanie interfejsu API analizy](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Omówienie analizy obrazów](../../overview-image-analysis.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).
