---
title: 'Przykład: Eksplorowanie aplikacji do przetwarzania obrazów w języku C #'
titleSuffix: Azure Cognitive Services
description: Poznaj podstawową aplikację systemu Windows, która używa interfejs API przetwarzania obrazów w usłudze Azure Cognitive Services. Przeprowadź proces optycznego rozpoznawania znaków (OCR), twórz miniatury i wykorzystuj funkcje wizualizacji w obrazie.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 0a1878268beb135151a23b87d03839f3a51f4a96
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102432591"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Przykład: Eksplorowanie aplikacji do przetwarzania obrazów przy użyciu języka C #

Zapoznaj się z podstawową aplikacją dla systemu Windows, w której zastosowano przetwarzanie obrazów do optycznego rozpoznawania znaków (OCR), inteligentnego przycinania miniatur oraz wykrywania, kategoryzowania, tagowania i opisywania elementów wizualnych, w tym twarzy, na obrazie. Poniższy przykład umożliwia przesłanie adresu URL obrazu lub lokalnego pliku. Ten przykład typu „open source” może służyć jako szablon do tworzenia własnej aplikacji systemu Windows korzystającej z interfejsu API przetwarzania obrazów i platformy WPF (Windows Presentation Foundation), która jest częścią programu .NET Framework.

> [!div class="checklist"]
> * Pobieranie przykładowej aplikacji z usługi GitHub
> * Otwieranie i kompilowanie przykładowej aplikacji w programie Visual Studio
> * Uruchamianie przykładowej aplikacji i wchodzenie z nią w interakcje, aby wykonywać różne scenariusze
> * Poznawanie różnych scenariuszy dołączonych do przykładowej aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z przykładową aplikacją upewnij się, że zostały spełnione następujące wymagania wstępne:

* Musisz mieć program [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) lub nowszy.
* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" Utwórz zasób przetwarzanie obrazów "  target="_blank"> utwórz zasób przetwarzanie obrazów </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który utworzysz, aby połączyć aplikację z usługą przetwarzanie obrazów. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="get-the-sample-app"></a>Pobieranie przykładowej aplikacji

Przykładowa aplikacja przetwarzania obrazów jest dostępna w serwisie GitHub z poziomu repozytorium `Microsoft/Cognitive-Vision-Windows`. To repozytorium zawiera również repozytorium `Microsoft/Cognitive-Common-Windows` jako moduł podrzędny usługi Git. Możesz rekursywnie klonować to repozytorium, łącznie z modułem podrzędnym, przy użyciu polecenia `git clone --recurse-submodules` z wiersza polecenia albo przy użyciu programu GitHub Desktop.

Aby na przykład rekursywnie klonować repozytorium przykładowej aplikacji przetwarzania obrazów z wiersza polecenia, uruchom następujące polecenie:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Nie pobieraj tego repozytorium w formie pliku ZIP. Usługa Git nie dołącza modułów podrzędnych podczas pobierania repozytorium w formie pliku ZIP.

### <a name="get-optional-sample-images"></a>Pobieranie opcjonalnych przykładowych obrazów

Opcjonalnie możesz skorzystać z przykładowych obrazów dołączonych do przykładowej aplikacji [Rozpoznawanie twarzy](../../Face/Overview.md) dostępnych w repozytorium `Microsoft/Cognitive-Face-Windows` w serwisie GitHub. Ta przykładowa aplikacja zawiera folder, `/Data`, zawierający wiele obrazów osób. Możesz rekursywnie klonować również to repozytorium za pomocą metod opisanych dla przykładowej aplikacji przetwarzania obrazów.

Aby na przykład rekursywnie klonować repozytorium przykładowej aplikacji rozpoznawania twarzy z wiersza polecenia, uruchom następujące polecenie:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Otwieranie i kompilowanie przykładowej aplikacji w programie Visual Studio

Musisz najpierw utworzyć przykładową aplikację, aby program Visual Studio mógł rozwiązać zależności, zanim będzie można uruchomić przykładową aplikację lub zapoznać się z nią. Aby otworzyć i skompilować przykładową aplikację, wykonaj następujące czynności:

1. Otwórz plik rozwiązania programu Visual Studio, `/Sample-WPF/VisionAPI-WPF-Samples.sln`, w programie Visual Studio.
1. Upewnij się, że rozwiązanie programu Visual Studio zawiera dwa projekty:  

   * SampleUserControlLibrary
   * VisionAPI-WPF-Samples  

   Jeśli projekt SampleUserControlLibrary jest niedostępny, upewnij się, że repozytorium `Microsoft/Cognitive-Vision-Windows` zostało rekursywnie sklonowane.
1. W programie Visual Studio naciśnij klawisze Ctrl+Shift+B albo wybierz pozycję **Kompilacja** z menu wstążki, a następnie wybierz pozycję **Kompiluj rozwiązanie**, aby skompilować rozwiązanie.

## <a name="run-and-interact-with-the-sample-app"></a>Uruchamianie przykładowej aplikacji i interakcja z nią

Możesz uruchomić przykładową aplikację, aby zobaczyć sposób jej interakcji z Tobą i z biblioteką klienta przetwarzania obrazów podczas wykonywania różnych zadań, takich jak generowanie miniatur lub znakowanie obrazów. Aby uruchomić przykładową aplikację i wejść z nią w interakcję, wykonaj następujące czynności:

1. Po ukończeniu kompilacji naciśnij klawisz **F5** albo wybierz pozycję **Debugowanie** z menu wstążki, a następnie wybierz pozycję **Rozpocznij debugowanie**, aby uruchomić przykładową aplikację.
1. Gdy zostanie wyświetlona przykładowa aplikacja, wybierz pozycję **Zarządzanie kluczami subskrypcji** z okienka nawigacji, aby wyświetlić stronę Zarządzanie kluczami subskrypcji.
   ![Strona Zarządzanie kluczami subskrypcji](../Images/Vision_UI_Subscription.PNG)  
1. Wprowadź swój klucz subskrypcji w polu **Klucz subskrypcji**.
1. Wprowadź adres URL punktu końcowego w **punkcie końcowym**.  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Jeśli nie chcesz wprowadzać klucza subskrypcji ani adresu URL punktu końcowego przy następnym uruchomieniu przykładowej aplikacji, wybierz pozycję **Zapisz ustawienie**, aby zapisać klucz subskrypcji i adres URL punktu końcowego na komputerze. Jeśli chcesz usunąć uprzednio zapisany klucz subskrypcji i adres URL punktu końcowego, wybierz pozycję **Usuń ustawienie**.

   > [!NOTE]
   > Przykładowa aplikacja korzysta z wydzielonej pamięci masowej i `System.IO.IsolatedStorage`, aby zapisać klucz subskrypcji i adres URL punktu końcowego.

1. W obszarze **Wybierz scenariusz** w okienku nawigacji wybierz jeden ze scenariuszy obecnie dołączonych do przykładowej aplikacji:  

   | Scenariusz | Opis |
   |----------|-------------|
   |Analizowanie obrazu | Używa operacji [Analizowanie obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) do analizy lokalnego lub zdalnego obrazu. Możesz wybrać funkcje wizualne i język do analizy oraz zobaczyć zarówno obraz, jak i wyniki.  |
   |Analizowanie obrazu za pomocą modelu domeny | Używa operacji [Wyświetlanie listy modeli specyficznych dla domeny](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd), aby wyświetlić listę modeli domeny, z których możesz wybierać, oraz operacji [Rozpoznawanie zawartości specyficznej dla domeny](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200), aby przeanalizować lokalne lub zdalne obrazy przy użyciu wybranego modelu domeny. Możesz też wybrać język do analizy. |
   |Opisywanie obrazu | Używa operacji [Opisanie obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe), aby utworzyć zrozumiały dla użytkownika opis lokalnego lub zdalnego obrazu. Możesz też wybrać język dla opisu. |
   |Generowanie tagów | Używa operacji [Tagowanie obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff), aby oznaczyć wizualne cechy lokalnego lub zdalnego obrazu. Możesz też wybrać język użyty do tagów. |
   |Rozpoznawanie tekstu (OCR) | Używa operacji [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc), aby rozpoznawać i wyodrębniać drukowany tekst z obrazu. Możesz wybrać używany język albo umożliwić funkcji przetwarzania obrazów automatyczne wykrywanie języka. |
   |Rozpoznawanie tekstu w wersji 2 (język angielski) | Używa operacji [Rozpoznaj tekst](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) i [Pobierz wynik operacji rozpoznawania tekstu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201), aby asynchronicznie rozpoznać i wyodrębniać tekst drukowany lub pismo odręczne z obrazu. |
   |Pobieranie miniatury | Używa operacji [Pobieranie miniatury](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb), aby wygenerować miniaturę lokalnego lub zdalnego obrazu. |

   Poniższy zrzut ekranu przedstawia stronę przewidzianą w scenariuszu analizowania obrazów po przeanalizowaniu przykładowego obrazu.
   ![Zrzut ekranu przedstawiający stronę analizy obrazu](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>Poznawanie przykładowej aplikacji

Rozwiązanie programu Visual Studio dla przykładowej aplikacji przetwarzania obrazów zawiera dwa projekty:

* SampleUserControlLibrary  
  Projekt SampleUserControlLibrary udostępnia funkcje wspólne dla wielu przykładów usług Cognitive Services. Projekt zawiera następujące elementy:
  * SampleScenarios  
    Kontrolka UserControl zapewniająca standaryzowaną prezentację przykładów obejmującą pasek tytułu, okienko nawigacji i okienko zawartości. Przykładowa aplikacja przetwarzania obrazów używa tej kontrolki w oknie MainWindow.xaml do wyświetlania stron scenariusza i uzyskiwania dostępu do informacji wspólnych dla scenariuszy, takich jak klucz subskrypcji i adres URL punktu końcowego.
  * SubscriptionKeyPage  
    Strona, która udostępnia standardowy układ do wprowadzania klucza subskrypcji i adresu URL punktu końcowego dla przykładowej aplikacji. Przykładowa aplikacja przetwarzania obrazów używa tej strony do zarządzania kluczem subskrypcji i adresem URL punktu końcowego, które są używane przez strony scenariusza.
  * VideoResultControl  
    Kontrolka UserControl zapewniająca standaryzowaną prezentację informacji wideo. Przykładowa aplikacja przetwarzania obrazów nie korzysta z tej kontrolki.
* VisionAPI-WPF-Samples  
  Główny projekt dla przykładowej aplikacji przetwarzania obrazów, który zawiera wszystkie ciekawe funkcje przetwarzania obrazów. Projekt zawiera następujące elementy:
  * AnalyzeInDomainPage.xaml  
    Strona scenariusza analizowania obrazu za pomocą scenariusza modelu domeny.
  * AnalyzeImage.xaml  
    Strona scenariusza dla scenariusza analizowania obrazu.
  * DescribePage.xaml  
    Strona scenariusza dla scenariusza opisu obrazu.
  * ImageScenarioPage.cs  
    Klasa ImageScenarioPage, z której pochodzą wszystkie strony scenariusza w przykładowej aplikacji. Ta klasa zarządza funkcjami, takimi jak dostarczanie poświadczeń i formatowanie danych wyjściowych, które są wspólne dla wszystkich stron scenariusza.
  * MainWindow.xaml  
    Główne okno przykładowej aplikacji, które używa kontrolki SampleScenarios do prezentacji strony SubscriptionKeyPage i stron scenariusza.
  * OCRPage.xaml  
    Strona scenariusza dla scenariusza rozpoznawania tekstu (OCR).
  * RecognizeLanguage.cs  
    Klasa RecognizeLanguage, która udostępnia informacje o językach obsługiwanych przez różne metody w przykładowej aplikacji.
  * TagsPage.xaml  
    Strona scenariusza dla scenariusza generowania tagów.
  * TextRecognitionPage.xaml  
    Strona scenariusza dla scenariusza rozpoznawania tekstu wersja 2 (język angielski).
  * ThumbnailPage.xaml  
    Strona scenariusza dla scenariusza pobierania miniatury.

### <a name="explore-the-sample-code"></a>Poznawanie przykładowego kodu

Kluczowe fragmenty kodu przykładowego są obramowane blokami komentarza, które rozpoczynają się od `KEY SAMPLE CODE STARTS HERE` i kończą się `KEY SAMPLE CODE ENDS HERE`, aby ułatwić Ci zapoznanie się z przykładową aplikacją. Te kluczowe fragmenty kodu przykładowego zawierają kod, który jest najbardziej odpowiedni do nauki korzystania z biblioteki klienta interfejsu API przetwarzania obrazów do wykonywania różnych zadań. W programie Visual Studio możesz wyszukać `KEY SAMPLE CODE STARTS HERE`, aby przenosić się między najbardziej odpowiednimi sekcjami kodu w przykładowej aplikacji przetwarzania obrazów. 

Na przykład metoda `UploadAndAnalyzeImageAsync` przedstawiona poniżej i zawarta w pliku AnalyzePage.xaml przedstawia sposób używania biblioteki klienta do analizy lokalnego obrazu za pomocą wywołania metody `ComputerVisionClient.AnalyzeImageInStreamAsync`.

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>Zapoznanie z biblioteką klienta

Ta przykładowa aplikacja używa biblioteki klienta interfejsu API przetwarzania obrazów, otoki cienkiego klienta języka C# dla interfejsu API przetwarzania obrazów w usługach Azure Cognitive Services. Biblioteka klienta jest dostępna z narzędzia NuGet w pakiecie [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/). Podczas tworzenia aplikacji programu Visual Studio pobierasz bibliotekę klienta z odpowiedniego pakietu NuGet. Możesz również wyświetlić kod źródłowy biblioteki klienta w folderze `/ClientLibrary` repozytorium `Microsoft/Cognitive-Vision-Windows`.

Funkcje biblioteki klienckiej koncentrują się wokół klasy `ComputerVisionClient` w przestrzeni nazw `Microsoft.Azure.CognitiveServices.Vision.ComputerVision`, zaś modele używane przez klasę `ComputerVisionClient` podczas interakcji z przetwarzaniem obrazów znajdują się w przestrzeni nazw `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models`. Na różnych stronach scenariuszy XAML dołączonych do przykładowej aplikacji znajdziesz następujące dyrektywy `using` dla tych przestrzeni nazw:

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

Dowiesz się więcej na temat różnych metod dołączonych do klasy `ComputerVisionClient` w miarę poznawania scenariuszy dołączonych do przykładowej aplikacji przetwarzania obrazów.

## <a name="explore-the-analyze-image-scenario"></a>Poznawanie scenariusza analizowania obrazu

Ten scenariusz jest zarządzany przez stronę AnalyzePage.xaml. Możesz wybrać funkcje wizualne i język do analizy oraz zobaczyć zarówno obraz, jak i wyniki. Strona scenariusza realizuje to przy użyciu jednej z następujących metod w zależności od źródła obrazu:

* UploadAndAnalyzeImageAsync  
  Ta metoda jest używana do obrazów lokalnych, w których obraz musi być zakodowany jako `Stream` i wysłany do przetwarzania obrazów za pomocą wywołania metody `ComputerVisionClient.AnalyzeImageInStreamAsync`.
* AnalyzeUrlAsync  
  Ta metoda jest używana dla obrazów zdalnych, w których adres URL obrazu jest wysyłany do przetwarzania obrazów za pomocą wywołania metody `ComputerVisionClient.AnalyzeImageAsync`.

Metoda `UploadAndAnalyzeImageAsync` tworzy nowe wystąpienie `ComputerVisionClient`, używając określonego klucza subskrypcji i adresu URL punktu końcowego. Ponieważ przykładowa aplikacja analizuje lokalny obraz, musi wysłać zawartość tego obrazu do przetwarzania obrazów. Spowoduje to otwarcie pliku lokalnego określonego w `imageFilePath` do odczytu jako `Stream`, a następnie pobranie funkcji wizualnych i języka wybranego na stronie scenariusza. Wywołuje to metodę `ComputerVisionClient.AnalyzeImageInStreamAsync`, przekazując `Stream` dla pliku, funkcje wizualne i język, a następnie zwraca wynik w postaci wystąpienia `ImageAnalysis`. Metody odziedziczone po klasie `ImageScenarioPage` przedstawiają zwracane wyniki na stronie scenariusza.

Metoda `AnalyzeUrlAsync` tworzy nowe wystąpienie `ComputerVisionClient`, używając określonego klucza subskrypcji i adresu URL punktu końcowego. Pobiera ona funkcje wizualne i język wybrany na stronie scenariusza. Wywołuje ona metodę `ComputerVisionClient.AnalyzeImageInStreamAsync`, przekazując adres URL obrazu, funkcje wizualne i język, a następnie zwraca wynik w postaci wystąpienia `ImageAnalysis`. Metody odziedziczone po klasie `ImageScenarioPage` przedstawiają zwracane wyniki na stronie scenariusza.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Poznawanie scenariusza analizowania obrazu za pomocą modelu domeny

Ten scenariusz jest zarządzany przez stronę AnalyzeInDomainPage.xaml. Możesz wybrać model domeny, taki jak `celebrities` lub `landmarks`, i język, aby przeprowadzić specyficzną dla domeny analizę obrazu i zobaczyć zarówno obraz, jak i wyniki. Strona scenariusza korzysta z następujących metod w zależności od źródła obrazu:

* GetAvailableDomainModelsAsync  
  Ta metoda pobiera listę dostępnych modeli domeny z przetwarzania obrazów i zapełnia kontrolkę ComboBox `_domainModelComboBox` na stronie przy użyciu metody `ComputerVisionClient.ListModelsAsync`.
* UploadAndAnalyzeInDomainImageAsync  
  Ta metoda jest używana do obrazów lokalnych, w których obraz musi być zakodowany jako `Stream` i wysłany do przetwarzania obrazów za pomocą wywołania metody `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync`.
* AnalyzeInDomainUrlAsync  
  Ta metoda jest używana dla obrazów zdalnych, w których adres URL obrazu jest wysyłany do przetwarzania obrazów za pomocą wywołania metody `ComputerVisionClient.AnalyzeImageByDomainAsync`.

Metoda `UploadAndAnalyzeInDomainImageAsync` tworzy nowe wystąpienie `ComputerVisionClient`, używając określonego klucza subskrypcji i adresu URL punktu końcowego. Ponieważ przykładowa aplikacja analizuje lokalny obraz, musi wysłać zawartość tego obrazu do przetwarzania obrazów. Spowoduje to otwarcie pliku lokalnego określonego w `imageFilePath` do odczytu jako `Stream`, a następnie pobranie języka wybranego na stronie scenariusza. Wywołuje to metodę `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` przekazującą `Stream` dla pliku, nazwę modelu domeny i język, a następnie zwraca wynik w postaci wystąpienia `DomainModelResults`. Metody odziedziczone po klasie `ImageScenarioPage` przedstawiają zwracane wyniki na stronie scenariusza.

Metoda `AnalyzeInDomainUrlAsync` tworzy nowe wystąpienie `ComputerVisionClient`, używając określonego klucza subskrypcji i adresu URL punktu końcowego. Pobiera ona język wybrany na stronie scenariusza. Wywołuje ona metodę `ComputerVisionClient.AnalyzeImageByDomainAsync`, przekazując adres URL obrazu, funkcje wizualne i język, a następnie zwraca wynik w postaci wystąpienia `DomainModelResults`. Metody odziedziczone po klasie `ImageScenarioPage` przedstawiają zwracane wyniki na stronie scenariusza.

## <a name="explore-the-describe-image-scenario"></a>Poznawanie scenariusza opisywania obrazu

Ten scenariusz jest zarządzany przez stronę DescribePage.xaml. Możesz wybrać język do utworzenia zrozumiałego dla użytkownika opisu obrazu i zobaczyć zarówno obraz, jak wyniki. Strona scenariusza korzysta z następujących metod w zależności od źródła obrazu:

* UploadAndDescribeImageAsync  
  Ta metoda jest używana do obrazów lokalnych, w których obraz musi być zakodowany jako `Stream` i wysłany do przetwarzania obrazów za pomocą wywołania metody `ComputerVisionClient.DescribeImageInStreamAsync`.
* DescribeUrlAsync  
  Ta metoda jest używana dla obrazów zdalnych, w których adres URL obrazu jest wysyłany do przetwarzania obrazów za pomocą wywołania metody `ComputerVisionClient.DescribeImageAsync`.

Metoda `UploadAndDescribeImageAsync` tworzy nowe wystąpienie `ComputerVisionClient`, używając określonego klucza subskrypcji i adresu URL punktu końcowego. Ponieważ przykładowa aplikacja analizuje lokalny obraz, musi wysłać zawartość tego obrazu do przetwarzania obrazów. Spowoduje to otwarcie pliku lokalnego określonego w `imageFilePath` do odczytu jako `Stream`, a następnie pobranie języka wybranego na stronie scenariusza. Wywołuje to metodę `ComputerVisionClient.DescribeImageInStreamAsync` przekazującą `Stream` dla pliku, maksymalną liczbę kandydatów (w tym przypadku 3) i język, a następnie zwraca wynik w postaci wystąpienia `ImageDescription`. Metody odziedziczone po klasie `ImageScenarioPage` przedstawiają zwracane wyniki na stronie scenariusza.

Metoda `DescribeUrlAsync` tworzy nowe wystąpienie `ComputerVisionClient`, używając określonego klucza subskrypcji i adresu URL punktu końcowego. Pobiera ona język wybrany na stronie scenariusza. Wywołuje to metodę `ComputerVisionClient.DescribeImageAsync` przekazującą adres URL obrazu, maksymalną liczbę kandydatów (w tym przypadku 3) i język, a następnie zwraca wynik w postaci wystąpienia `ImageDescription`. Metody odziedziczone po klasie `ImageScenarioPage` przedstawiają zwracane wyniki na stronie scenariusza.

## <a name="explore-the-generate-tags-scenario"></a>Poznawanie scenariusza generowania tagów

Ten scenariusz jest zarządzany przez stronę TagsPage.xaml. Możesz wybrać język do oznaczenia funkcji wizualnych obrazu oraz zobaczyć zarówno obraz, jak i wyniki. Strona scenariusza korzysta z następujących metod w zależności od źródła obrazu:

* UploadAndGetTagsForImageAsync  
  Ta metoda jest używana do obrazów lokalnych, w których obraz musi być zakodowany jako `Stream` i wysłany do przetwarzania obrazów za pomocą wywołania metody `ComputerVisionClient.TagImageInStreamAsync`.
* GenerateTagsForUrlAsync  
  Ta metoda jest używana dla obrazów zdalnych, w których adres URL obrazu jest wysyłany do przetwarzania obrazów za pomocą wywołania metody `ComputerVisionClient.TagImageAsync`.

Metoda `UploadAndGetTagsForImageAsync` tworzy nowe wystąpienie `ComputerVisionClient`, używając określonego klucza subskrypcji i adresu URL punktu końcowego. Ponieważ przykładowa aplikacja analizuje lokalny obraz, musi wysłać zawartość tego obrazu do przetwarzania obrazów. Spowoduje to otwarcie pliku lokalnego określonego w `imageFilePath` do odczytu jako `Stream`, a następnie pobranie języka wybranego na stronie scenariusza. Wywołuje to metodę `ComputerVisionClient.TagImageInStreamAsync`, przekazując `Stream` dla pliku i język, a następnie zwraca wynik w postaci wystąpienia `TagResult`. Metody odziedziczone po klasie `ImageScenarioPage` przedstawiają zwracane wyniki na stronie scenariusza.

Metoda `GenerateTagsForUrlAsync` tworzy nowe wystąpienie `ComputerVisionClient`, używając określonego klucza subskrypcji i adresu URL punktu końcowego. Pobiera ona język wybrany na stronie scenariusza. Wywołuje to metodę `ComputerVisionClient.TagImageAsync`, przekazując adres URL i język, a następnie zwraca wynik w postaci wystąpienia `TagResult`. Metody odziedziczone po klasie `ImageScenarioPage` przedstawiają zwracane wyniki na stronie scenariusza.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Poznawanie scenariusza rozpoznawania tekstu (OCR)

Ten scenariusz jest zarządzany przez stronę OCRPage.xaml. Możesz wybrać język do rozpoznawania i wyodrębniania tekstu drukowanego z obrazu oraz zobaczyć zarówno obraz, jak i wyniki. Strona scenariusza korzysta z następujących metod w zależności od źródła obrazu:

* UploadAndRecognizeImageAsync  
  Ta metoda jest używana do obrazów lokalnych, w których obraz musi być zakodowany jako `Stream` i wysłany do przetwarzania obrazów za pomocą wywołania metody `ComputerVisionClient.RecognizePrintedTextInStreamAsync`.
* RecognizeUrlAsync  
  Ta metoda jest używana dla obrazów zdalnych, w których adres URL obrazu jest wysyłany do przetwarzania obrazów za pomocą wywołania metody `ComputerVisionClient.RecognizePrintedTextAsync`.

Metoda `UploadAndRecognizeImageAsync` tworzy nowe wystąpienie `ComputerVisionClient`, używając określonego klucza subskrypcji i adresu URL punktu końcowego. Ponieważ przykładowa aplikacja analizuje lokalny obraz, musi wysłać zawartość tego obrazu do przetwarzania obrazów. Spowoduje to otwarcie pliku lokalnego określonego w `imageFilePath` do odczytu jako `Stream`, a następnie pobranie języka wybranego na stronie scenariusza. Wywołuje to metodę `ComputerVisionClient.RecognizePrintedTextInStreamAsync` wskazującą, że orientacja nie została wykryta i przekazującą `Stream` dla pliku i język, a następnie zwraca wynik w postaci wystąpienia `OcrResult`. Metody odziedziczone po klasie `ImageScenarioPage` przedstawiają zwracane wyniki na stronie scenariusza.

Metoda `RecognizeUrlAsync` tworzy nowe wystąpienie `ComputerVisionClient`, używając określonego klucza subskrypcji i adresu URL punktu końcowego. Pobiera ona język wybrany na stronie scenariusza. Wywołuje to metodę `ComputerVisionClient.RecognizePrintedTextAsync` wskazującą, że orientacja nie została wykryta i przekazującą adres URL i język, a następnie zwraca wynik w postaci wystąpienia `OcrResult`. Metody odziedziczone po klasie `ImageScenarioPage` przedstawiają zwracane wyniki na stronie scenariusza.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Poznawanie scenariusza rozpoznawania tekstu w wersji 2 (język angielski)

Ten scenariusz jest zarządzany przez stronę TextRecognitionPage.xaml. Możesz wybrać tryb rozpoznawania i język, aby asynchronicznie rozpoznawać i wyodrębniać tekst drukowany albo pismo odręczne z obrazu oraz wyświetlić zarówno obraz, jak i wyniki. Strona scenariusza korzysta z następujących metod w zależności od źródła obrazu:

* UploadAndRecognizeImageAsync  
  Ta metoda jest używana do obrazów lokalnych, w których obraz musi być zakodowany jako `Stream` i wysłane do przetwarzania obrazów za pomocą metody `RecognizeAsync`, oraz przekazuje sparametryzowanego delegata dla metody `ComputerVisionClient.RecognizeTextInStreamAsync`.
* RecognizeUrlAsync  
  Ta metoda jest używana do obrazów zdalnych, w których adres URL obrazu jest wysyłany do przetwarzania obrazów za pomocą wywołania metody `RecognizeAsync`, oraz przekazuje sparametryzowanego delegata dla metody `ComputerVisionClient.RecognizeTextAsync`.
* RecognizeAsync Ta metoda obsługuje wywołania asynchroniczne zarówno dla metody `UploadAndRecognizeImageAsync`, jak i `RecognizeUrlAsync` oraz sondowanie wyników za pomocą wywołania metody `ComputerVisionClient.GetTextOperationResultAsync`.

W odróżnieniu od innych scenariuszy dołączonych do przykładowej aplikacji przetwarzania obrazów ten scenariusz jest asynchroniczny w tym sensie, że jedna metoda jest wywoływana, aby uruchomić proces, ale inna metoda jest wywoływana, aby sprawdzić stan i zwrócić wyniki tego procesu. Logiczny przepływ w tym scenariuszu jest nieco inny niż w pozostałych scenariuszach.

Metoda `UploadAndRecognizeImageAsync` otwiera plik lokalny określony w `imageFilePath` do odczytu jako `Stream`, a następnie wywołuje metodę `RecognizeAsync`, przekazując:

* Wyrażenie lambda dla sparametryzowanego asynchronicznego delegata metody `ComputerVisionClient.RecognizeTextInStreamAsync` z `Stream` dla pliku i trybem rozpoznawania jako parametrami w `GetHeadersAsyncFunc`.
* Wyrażenie lambda dla delegata, aby pobrać wartości nagłówka odpowiedzi `Operation-Location` w `GetOperationUrlFunc`.

Metoda `RecognizeUrlAsync` wywołuje metodę `RecognizeAsync`, przekazując:

* Wyrażenie lambda dla sparametryzowanego asynchronicznego delegata metody `ComputerVisionClient.RecognizeTextAsync` z adresem URL zdalnego obrazu i trybem rozpoznawania jako parametrami w `GetHeadersAsyncFunc`.
* Wyrażenie lambda dla delegata, aby pobrać wartości nagłówka odpowiedzi `Operation-Location` w `GetOperationUrlFunc`.

Gdy metoda `RecognizeAsync` zostanie zakończona, zarówno metoda `UploadAndRecognizeImageAsync`, jak i `RecognizeUrlAsync` zwracają wynik jako wystąpienie `TextOperationResult`. Metody odziedziczone po klasie `ImageScenarioPage` przedstawiają zwracane wyniki na stronie scenariusza.

Metoda `RecognizeAsync` wywołuje sparametryzowanego delegata dla metody `ComputerVisionClient.RecognizeTextInStreamAsync` albo `ComputerVisionClient.RecognizeTextAsync` przekazanej w `GetHeadersAsyncFunc` i czeka na odpowiedź. Następnie metoda wywołuje delegata przekazanego w `GetOperationUrlFunc`, aby pobrać wartość nagłówka odpowiedzi `Operation-Location` z odpowiedzi. Ta wartość to adres URL służący do pobierania wyników metody przekazanej w `GetHeadersAsyncFunc` z przetwarzania obrazów.

Następnie metoda `RecognizeAsync` wywołuje metodę `ComputerVisionClient.GetTextOperationResultAsync`, przekazując adres URL pobrany z nagłówka odpowiedzi `Operation-Location`, aby pobrać stan i wynik metody przekazanej w `GetHeadersAsyncFunc`. Jeśli stan nie wskazuje, że metoda została zakończona, pomyślnie lub niepomyślnie, metoda `RecognizeAsync` wywołuje `ComputerVisionClient.GetTextOperationResultAsync` jeszcze 3 razy, oczekując 3 sekundy między wywołaniami. Metoda `RecognizeAsync` zwraca wyniki do metody, która ją wywołała.

## <a name="explore-the-get-thumbnail-scenario"></a>Poznawanie scenariusza pobierania miniatur

Ten scenariusz jest zarządzany przez stronę ThumbnailPage.xaml. Możesz wskazać, czy ma być używane inteligentne przycinanie, i określić żądaną wysokość i szerokość w celu wygenerowania miniatury z obrazu oraz wyświetlić zarówno obraz, jak i wyniki. Strona scenariusza korzysta z następujących metod w zależności od źródła obrazu:

* UploadAndThumbnailImageAsync  
  Ta metoda jest używana do obrazów lokalnych, w których obraz musi być zakodowany jako `Stream` i wysłany do przetwarzania obrazów za pomocą wywołania metody `ComputerVisionClient.GenerateThumbnailInStreamAsync`.
* ThumbnailUrlAsync  
  Ta metoda jest używana dla obrazów zdalnych, w których adres URL obrazu jest wysyłany do przetwarzania obrazów za pomocą wywołania metody `ComputerVisionClient.GenerateThumbnailAsync`.

Metoda `UploadAndThumbnailImageAsync` tworzy nowe wystąpienie `ComputerVisionClient`, używając określonego klucza subskrypcji i adresu URL punktu końcowego. Ponieważ przykładowa aplikacja analizuje lokalny obraz, musi wysłać zawartość tego obrazu do przetwarzania obrazów. Spowoduje to otwarcie pliku lokalnego określonego w `imageFilePath` do odczytu jako `Stream`. Wywołuje to metodę `ComputerVisionClient.GenerateThumbnailInStreamAsync` przekazującą szerokość, wysokość, `Stream` dla pliku oraz to, czy ma być użyte inteligentne przycinanie, a następnie zwraca wynik w postaci `Stream`. Metody odziedziczone po klasie `ImageScenarioPage` przedstawiają zwracane wyniki na stronie scenariusza.

Metoda `RecognizeUrlAsync` tworzy nowe wystąpienie `ComputerVisionClient`, używając określonego klucza subskrypcji i adresu URL punktu końcowego. Wywołuje to metodę `ComputerVisionClient.GenerateThumbnailAsync` przekazującą szerokość, wysokość, adres URL dla pliku oraz to, czy ma być użyte inteligentne przycinanie, a następnie zwraca wynik w postaci `Stream`. Metody odziedziczone po klasie `ImageScenarioPage` przedstawiają zwracane wyniki na stronie scenariusza.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebny, usuń folder, do którego zostało sklonowane repozytorium `Microsoft/Cognitive-Vision-Windows`. Jeśli postanowisz użyć przykładowych obrazów, również usuń folder, do którego zostało sklonowane repozytorium `Microsoft/Cognitive-Face-Windows`.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do usługi Front Service](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
