---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: c28cfbbdff9e3fbf6cfe8d53ab7529c2ce9bd96e
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725187"
---
Wprowadzenie do biblioteki Custom Vision klienta dla programu .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod tworzenia modelu wykrywania obiektów. Utworzysz projekt, dodasz tagi, przeszkolisz projekt na przykładowych obrazach i użyjesz adresu URL punktu końcowego przewidywania projektu, aby go przetestować programowo. Użyj tego przykładu jako szablonu do tworzenia własnej aplikacji do rozpoznawania obrazów.

> [!NOTE]
> Jeśli chcesz skompilować i wytszkolić _model_ wykrywania obiektów bez pisania kodu, zapoznaj się ze wskazówkami opartymi [na przeglądarce.](../../get-started-build-detector.md)

Użyj biblioteki Custom Vision klienta dla programu .NET, aby:

* Tworzenie nowego projektu usługi Custom Vision
* Dodawanie tagów do projektu
* Przekazywanie i Tagi obrazów
* Uczenie projektu
* Opublikuj bieżącą iterację
* Testowanie punktu końcowego przewidywania

[Dokumentacja referencyjna](/dotnet/api/overview/azure/cognitiveservices/client/customvision) | Kod źródłowy [biblioteki (trenowania)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Training) [(przewidywanie) |](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Prediction) Przykłady pakietów [(NuGet) (trenowania)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/) [(przewidywanie)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)  |  [](/samples/browse/?products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— tworzenie bezpłatnej subskrypcji](https://azure.microsoft.com/free/cognitive-services/)
* Program [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) lub bieżącą wersję programu [.NET Core.](https://dotnet.microsoft.com/download/dotnet-core)
* Po utworzeniu subskrypcji platformy Azure utwórz zasób usługi Custom Vision, aby utworzyć zasób usługi Custom Vision w usłudze Azure Portal, aby utworzyć zasób trenowania i przewidywania oraz pobrać klucze i punkt <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> </a> końcowy. Poczekaj na wdrożenie i kliknij przycisk **Przejdź do** zasobu.
    * Klucz i punkt końcowy będą potrzebne z zasobów, które utworzysz, aby połączyć aplikację z Custom Vision. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
    * Możesz użyć bezpłatnej warstwy cenowej ( ), aby wypróbować usługę, a następnie uaktualnić ją do warstwy `F0` płatnej w środowisku produkcyjnym.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-c-application"></a>Tworzenie nowej aplikacji w języku C#

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Za Visual Studio utwórz nową aplikację .NET Core. 

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta 

Po utworzeniu nowego projektu zainstaluj bibliotekę klienta, klikając prawym przyciskiem myszy rozwiązanie projektu w Eksplorator rozwiązań **i** wybierając pozycję Zarządzaj pakietami **NuGet.** W menedżerze pakietów, który zostanie otwarty, wybierz **pozycję Przeglądaj,** zaznacz pole **wyboru Uwzględnij wstępną publikację** i wyszukaj `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training` i `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction` . Wybierz najnowszą wersję, a następnie **pozycję Zainstaluj.** 

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

W oknie konsoli (takim jak cmd, PowerShell lub Bash) użyj polecenia , aby utworzyć nową aplikację `dotnet new` konsolową o nazwie `custom-vision-quickstart` . To polecenie tworzy prosty projekt języka C# "Hello world" z pojedynczym plikiem źródłowym: *program.cs.* 

```console
dotnet new console -n custom-vision-quickstart
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

W katalogu aplikacji zainstaluj bibliotekę klienta Custom Vision dla programu .NET za pomocą następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

---

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Znajdziesz go w witrynie [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs)która zawiera przykłady kodu z tego przewodnika Szybki start.

W katalogu projektu otwórz *plik program.cs* i dodaj następujące `using` dyrektywy:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_imports)]

W metodzie **Main aplikacji** utwórz zmienne dla klucza i punktu końcowego zasobu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_creds)]

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasoby Custom Vision utworzone w sekcji  Wymagania wstępne zostały pomyślnie wdrożone, kliknij przycisk Przejdź do zasobu **w** obszarze **Następne kroki.** Klucze i punkt końcowy można znaleźć na stronach kluczy **i** punktów końcowych zasobów w obszarze **zarządzania zasobami.** Musisz uzyskać klucze dla zasobów trenowania i przewidywania wraz z punktem końcowym interfejsu API dla zasobu trenowania.
>
> Pamiętaj, aby usunąć klucze z kodu, gdy wszystko będzie gotowe, i nigdy nie publikować ich publicznie. W środowisku produkcyjnym rozważ użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz Cognitive Services [zabezpieczeń.](../../../cognitive-services-security.md)

W metodzie **Main aplikacji** dodaj wywołania metod używanych w tym przewodniku Szybki start. Zostaną one zaimplementowane później.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Model obiektu

|Nazwa|Opis|
|---|---|
|[CustomVisionTrainingClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Ta klasa obsługuje tworzenie, szkolenie i publikowanie modeli. |
|[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Ta klasa obsługuje wykonywanie zapytań o modele w celu przewidywania wykrywania obiektów.|
|[PredictionModel](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel)| Ta klasa definiuje przewidywanie pojedynczego obiektu na pojedynczym obrazie. Zawiera właściwości identyfikatora i nazwy obiektu, lokalizację pola granicznego obiektu oraz ocenę ufności.|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania przy użyciu biblioteki Custom Vision klienta dla programu .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Tworzenie nowego projektu usługi Custom Vision](#create-a-new-custom-vision-project)
* [Dodawanie tagów do projektu](#add-tags-to-the-project)
* [Przekazywanie i Tagi obrazów](#upload-and-tag-images)
* [Uczenie projektu](#train-the-project)
* [Opublikuj bieżącą iterację](#publish-the-current-iteration)
* [Testowanie punktu końcowego przewidywania](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

W nowej metodzie należy utworzyć wystąpienia klientów trenowania i przewidywania przy użyciu punktu końcowego i kluczy.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Tworzenie nowego projektu usługi Custom Vision

Ta następna metoda tworzy projekt wykrywania obiektów. Utworzony projekt pojawi się w witrynie [Custom Vision internetowej](https://customvision.ai/). Zobacz metodę [CreateProject,](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) aby określić inne opcje podczas tworzenia projektu (wyjaśniono w przewodniku [tworzenie](../../get-started-build-detector.md) portalu internetowego detektora).  

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>Dodawanie tagów do projektu

Ta metoda definiuje tagi, na których będzie trenowany model.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

Najpierw pobierz przykładowe obrazy dla tego projektu. Zapisz zawartość przykładowego [folderu Images na](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) urządzeniu lokalnym.

> [!NOTE]
> Czy potrzebujesz szerszego zestawu obrazów do ukończenia szkolenia? Trove, Microsoft Garage projektu, umożliwia zbieranie i kupowanie zestawów obrazów na potrzeby szkolenia. Po zebraniu obrazów możesz je pobrać, a następnie zaimportować do projektu Custom Vision w zwykły sposób. Odwiedź stronę [Trove, aby](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) dowiedzieć się więcej.

Po oznaczeniu obrazów w projektach wykrywania obiektów należy określić region każdego z otagowanych obiektów przy użyciu znormalizowanych współrzędnych. Poniższy kod kojarzy każdy z przykładowych obrazów ze oznaczonym regionem.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

> [!NOTE]
> W przypadku własnych projektów, jeśli nie masz narzędzia typu "kliknij i przeciągnij" do oznaczania współrzędnych regionów, możesz użyć internetowego interfejsu użytkownika w Custom Vision [internetowej](https://www.customvision.ai/). W tym przykładzie współrzędne są już podane.

Następnie to mapowanie skojarzeń jest używane, aby przekazać każdy przykładowy obraz z jego współrzędnymi regionu. W jednej partii można przekazać maksymalnie 64 obrazy. Może być konieczne zmiana wartości `imagePath` tak, aby wskazać prawidłowe lokalizacje folderów.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

W tym momencie zostały przekazane wszystkie obrazy próbek i otagowane każde z nich (**rozwidlenie** lub **nożyczki**) za pomocą skojarzonego prostokąta pikseli.

## <a name="train-the-project"></a>Uczenie projektu

Ta metoda tworzy pierwszą iterację trenowania w projekcie. Wysyła zapytanie do usługi do momentu ukończenia trenowania.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

> [!TIP]
> Trenuj przy użyciu wybranych tagów
>
> Opcjonalnie możesz trenować tylko podzestaw zastosowanych tagów. Możesz to zrobić, jeśli nie zastosowano jeszcze wystarczającej ilości niektórych tagów, ale masz wystarczająco dużo innych. W [wywołaniu TrainProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) użyj *parametru trainingParameters.* [Skonstruuj element TrainingParameters](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters) i ustaw jego właściwość **SelectedTags** na listę identyfikatorów tagów, których chcesz użyć. Model będzie trenować, aby rozpoznawał tylko tagi na tej liście.

## <a name="publish-the-current-iteration"></a>Opublikuj bieżącą iterację

Ta metoda udostępnia bieżącą iterację modelu do wykonywania zapytań. Nazwa modelu może być odwołaniem do wysyłania żądań przewidywania. Musisz wprowadzić własną wartość dla `predictionResourceId` . Identyfikator zasobu przewidywania można znaleźć na  karcie Przegląd zasobu w skrypcie Azure Portal identyfikator **subskrypcji.**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testowanie punktu końcowego przewidywania

Ta metoda ładuje obraz testowy, wysyła zapytanie do punktu końcowego modelu i wyprowadza dane przewidywania do konsoli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Uruchom aplikację, klikając przycisk **Debug (Debuguj)** w górnej części okna środowiska IDE.

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

Uruchom aplikację z katalogu aplikacji za pomocą `dotnet run` polecenia .

```dotnet
dotnet run
```

---

Gdy aplikacja jest uruchomiona, należy otworzyć okno konsoli i napisać następujące dane wyjściowe:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Następnie można sprawdzić, czy obraz testowy (znaleziony w **obrazach/teście/** ) jest odpowiednio oznakowany i czy region wykrywania jest poprawny. W tym momencie można nacisnąć dowolny klawisz, aby zamknąć aplikację.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz zostały wykonane wszystkie kroki procesu wykrywania obiektów w kodzie. Ten przykład wykonuje pojedynczą iterację trenowania, ale często trzeba będzie wielokrotnie trenować i testować model, aby był bardziej dokładny. Następny przewodnik dotyczy klasyfikacji obrazów. Jej zasady są podobne do wykrywania obiektów.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](../../test-your-model.md)

* [Co to jest usługa Custom Vision?](../../overview.md)
* Kod źródłowy tego przykładu można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs)
* [Dokumentacja referencyjna zestawu SDK](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
