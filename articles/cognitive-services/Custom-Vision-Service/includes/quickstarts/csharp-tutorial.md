---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: efd2f35a24ac762ce78889d99c375613239f2604
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500085"
---
Wprowadzenie do biblioteki klienta Custom Vision dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod służący do tworzenia modelu klasyfikacji obrazu. Utworzysz projekt, dodasz Tagi, nauczysz projekt, a następnie użyjesz w adresie URL punktu końcowego przewidywania projektu do programistycznego testowania. Użyj tego przykładu jako szablonu do tworzenia własnej aplikacji rozpoznawania obrazu.

> [!NOTE]
> Jeśli chcesz skompilować i przeszkolić model klasyfikacji _bez_ pisania kodu, zamiast tego zapoznaj się ze [wskazówkami w przeglądarce](../../getting-started-build-a-classifier.md) .

Użyj biblioteki klienta Custom Vision dla platformy .NET, aby:

* Tworzenie nowego projektu usługi Custom Vision
* Dodawanie tagów do projektu
* Przekazywanie i Tagi obrazów
* Uczenie projektu
* Opublikuj bieżącą iterację
* Testowanie punktu końcowego przewidywania

[Dokumentacja referencyjna](/dotnet/api/overview/azure/cognitiveservices/client/customvision) | Kod źródłowy biblioteki [(szkolenie)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Training) [(przewidywania)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Prediction) | Pakiety (NuGet) ( [uczenie](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/) [) —](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)  |  [przykłady](/samples/browse/?products=azure&term=vision&terms=vision)


## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* [Środowisko IDE programu Visual Studio](https://visualstudio.microsoft.com/vs/) lub bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" Utwórz zasób Custom Vision "  target="_blank"> utwórz zasób Custom Vision <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby utworzyć zasób szkoleniowy i predykcyjny oraz pobrać klucze i punkt końcowy. Zaczekaj na jego wdrożenie i kliknij przycisk **Przejdź do zasobu** .
    * Będziesz potrzebować klucza i punktu końcowego z zasobów, które tworzysz, aby połączyć aplikację z Custom Vision. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-c-application"></a>Utwórz nową aplikację w języku C#

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Za pomocą programu Visual Studio Utwórz nową aplikację platformy .NET Core. 

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką 

Po utworzeniu nowego projektu Zainstaluj bibliotekę kliencką, klikając prawym przyciskiem myszy rozwiązanie projektu w **Eksplorator rozwiązań** i wybierając pozycję **Zarządzaj pakietami NuGet**. W Menedżerze pakietów, który otwiera Wybierz pozycję **Przeglądaj**, zaznacz pozycję **Uwzględnij wersję wstępną**, a następnie wyszukaj pozycję `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training` i `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction` . Wybierz najnowszą wersję, a następnie **Zainstaluj**. 

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `custom-vision-quickstart` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: *program.cs*. 

```console
dotnet new console -n custom-vision-quickstart
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

W katalogu aplikacji zainstaluj Custom Visioną bibliotekę kliencką dla platformy .NET przy użyciu następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

---

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs), która zawiera przykłady kodu w tym przewodniku Szybki Start.

W katalogu projektu Otwórz plik *program.cs* i Dodaj następujące `using` dyrektywy:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_imports)]


W metodzie **głównej** aplikacji Utwórz zmienne dla klucza i punktu końcowego zasobu. Należy również zadeklarować niektóre obiekty podstawowe do użycia później.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_creds)]

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli pomyślnie wdrożono zasoby Custom Vision utworzone w sekcji **wymagania wstępne** , kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki**. Klucze i punkt końcowy można znaleźć na stronach "klucze zasobów" **i punkt końcowy** w obszarze **Zarządzanie zasobami**. Musisz pobrać klucze szkoleniowe i prognozowe wraz z punktem końcowym zasobów szkoleniowych.
>
> Pamiętaj, aby usunąć klucze z kodu, gdy skończysz, i nigdy nie Publikuj ich publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [zabezpieczeń](../../../cognitive-services-security.md) Cognitive Services.

W metodzie **głównej** aplikacji Dodaj wywołania metod używanych w tym przewodniku Szybki Start. Zostaną zaimplementowane później.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Model obiektów

|Nazwa|Opis|
|---|---|
|[CustomVisionTrainingClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Ta klasa obsługuje tworzenie, uczenie i publikowanie modeli. |
|[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Ta klasa obsługuje zapytania dotyczące modeli na potrzeby prognoz klasyfikacji obrazu.|
|[PredictionModel](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel)| Ta klasa definiuje pojedyncze prognozowanie na pojedynczym obrazie. Zawiera ona właściwości identyfikatora i nazwy obiektu oraz ocenę ufności.|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań przy użyciu biblioteki klienta Custom Vision dla platformy .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Tworzenie nowego projektu usługi Custom Vision](#create-a-new-custom-vision-project)
* [Dodawanie tagów do projektu](#add-tags-to-the-project)
* [Przekazywanie i Tagi obrazów](#upload-and-tag-images)
* [Uczenie projektu](#train-the-project)
* [Opublikuj bieżącą iterację](#publish-the-current-iteration)
* [Testowanie punktu końcowego przewidywania](#test-the-prediction-endpoint)


## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

W nowej metodzie Utwórz wystąpienie klientów szkoleń i prognoz przy użyciu punktu końcowego i kluczy.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Tworzenie nowego projektu usługi Custom Vision

Ten następny bit kodu tworzy projekt klasyfikacji obrazu. Utworzony projekt będzie widoczny w [witrynie sieci web Custom Vision](https://customvision.ai/). Aby określić inne opcje podczas tworzenia projektu (wyjaśnione w przewodniku tworzenia portalu sieci Web [klasyfikatora](../../getting-started-build-a-classifier.md) ), zobacz metodę " [setproject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) ".  

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Dodawanie tagów do projektu

Ta metoda definiuje Tagi, w których będzie nauczyć się model.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_addtags)]

## <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

Najpierw pobierz przykładowe obrazy dla tego projektu. Zapisz zawartość [folderu Sample images](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) na urządzeniu lokalnym.

> [!NOTE]
> Potrzebujesz szerszego zestawu obrazów, aby dokończyć szkolenia? Skarbnica, projekt garażu firmy Microsoft, umożliwia zbieranie i kupowanie zestawów obrazów do celów szkoleniowych. Po zebraniu obrazów można je pobrać, a następnie zaimportować do projektu Custom Vision w zwykły sposób. Odwiedź [stronę skarbnica](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3) , aby dowiedzieć się więcej.

Następnie zdefiniuj metodę pomocnika, aby przekazać obrazy w tym katalogu. Może być konieczne edytowanie argumentu **GetFiles** , aby wskazywał lokalizację, w której zapisano obrazy.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_loadimages)]

Następnie zdefiniuj metodę przekazywania obrazów, stosując Tagi zgodnie z ich lokalizacją folderu (obrazy są już posortowane). Można przekazać i oznakować obrazy iteracyjnie lub w partii (do 64 na partię). Ten fragment kodu zawiera przykłady obu tych elementów. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]


## <a name="train-the-project"></a>Uczenie projektu

Ta metoda tworzy pierwszą iterację szkoleniową w projekcie. Wysyła zapytanie do usługi do momentu ukończenia szkolenia.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

> [!TIP]
> Uczenie z wybranymi tagami
>
> Opcjonalnie możesz nauczyć tylko podzestaw zastosowanych tagów. Możesz to zrobić, jeśli jeszcze nie zastosowano wystarczającej liczby niektórych tagów, ale masz wystarczającą ilość innych. W wywołaniu [TrainProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) Użyj parametru *trainingParameters* . Utwórz [TrainingParameters](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters) i ustaw jej właściwość **SelectedTags** na listę identyfikatorów tagów, których chcesz użyć. Model będzie szkolić, aby rozpoznawał tylko Tagi na tej liście.

## <a name="publish-the-current-iteration"></a>Opublikuj bieżącą iterację

Ta metoda powoduje, że bieżąca iteracja modelu jest dostępna na potrzeby wykonywania zapytań. Możesz użyć nazwy modelu jako odwołania do wysyłania żądań przewidywania. Musisz wprowadzić własną wartość dla `predictionResourceId` . Identyfikator zasobu przewidywania można znaleźć na karcie **Przegląd** zasobu w Azure Portal, na liście jako **Identyfikator subskrypcji**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testowanie punktu końcowego przewidywania

Ta część skryptu ładuje obraz testowy, wysyła zapytanie do punktu końcowego modelu i wyprowadza dane prognoz do konsoli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_test)]


## <a name="run-the-application"></a>Uruchamianie aplikacji

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Uruchom aplikację, klikając przycisk **Debuguj** w górnej części okna środowiska IDE.

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

Uruchom aplikację z katalogu aplikacji za pomocą `dotnet run` polecenia.

```dotnet
dotnet run
```

---

Gdy aplikacja jest uruchomiona, należy otworzyć okno konsoli i napisać następujące dane wyjściowe:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Następnie możesz sprawdzić, czy obraz testowy (znajdujący się w folderze **Images/Test/**) został odpowiednio otagowany. Naciśnij dowolny klawisz, aby zakończyć działanie aplikacji. Możesz też wrócić do [witryny internetowej Custom Vision](https://customvision.ai) i wyświetlić bieżący stan nowo utworzonego projektu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz każdy krok procesu klasyfikacji obrazu można wykonać w kodzie. Ten przykład wykonuje pojedynczą iterację szkoleniową, ale często należy przeprowadzić uczenie i testowanie modelu wiele razy, aby zwiększyć jego dokładność.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](../../test-your-model.md)

* [Co to jest usługa Custom Vision?](../../overview.md)
* Kod źródłowy dla tego przykładu można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs)
* [Dokumentacja referencyjna zestawu SDK](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
