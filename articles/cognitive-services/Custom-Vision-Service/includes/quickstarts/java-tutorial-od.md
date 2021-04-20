---
author: PatrickFarley
ms.custom: devx-track-java
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/13/2020
ms.openlocfilehash: 16112ffe7ba5fbc23335f9b60cdcbc045ea7cd2b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725137"
---
Rozpoczynanie korzystania z biblioteki Custom Vision klienta dla języka Java w celu kompilowania modelu wykrywania obiektów. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Użyj tego przykładu jako szablonu do tworzenia własnej aplikacji do rozpoznawania obrazów.

> [!NOTE]
> Jeśli chcesz utworzyć i wytszkolić _model_ wykrywania obiektów bez pisania kodu, zapoznaj się ze wskazówkami opartymi [na przeglądarce.](../../get-started-build-detector.md)

Użyj biblioteki Custom Vision klienta dla języka Java, aby:

* Tworzenie nowego projektu usługi Custom Vision
* Dodawanie tagów do projektu
* Przekazywanie i Tagi obrazów
* Uczenie projektu
* Opublikuj bieżącą iterację
* Testowanie punktu końcowego przewidywania

[Dokumentacja referencyjna](/java/api/overview/azure/cognitiveservices/client/customvision) | Kod źródłowy [biblioteki (trenowania)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-training) [(przewidywanie) |](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-prediction) Przykłady artefaktu [(Maven) (trenowania)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-training/1.1.0-preview.2/jar) [(przewidywanie)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-prediction/1.1.0-preview.2/jar)  | 
 [](/samples/browse/?products=azure&terms=custom%20vision)


## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Bieżąca wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Narzędzie [kompilacji Gradle](https://gradle.org/install/)lub inny menedżer zależności.
* Po utworzeniu subskrypcji platformy Azure utwórz zasób usługi Custom Vision, aby utworzyć zasób usługi Custom Vision w usłudze Azure Portal, aby utworzyć zasób trenowania i przewidywania oraz uzyskać klucze i punkt <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> </a> końcowy. Poczekaj na wdrożenie, a następnie kliknij **przycisk Przejdź do** zasobu.
    * Będziesz potrzebować klucza i punktu końcowego z zasobów, które utworzysz, aby połączyć aplikację z Custom Vision. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
    * Możesz użyć warstwy cenowej Bezpłatna ( ), aby wypróbować usługę, i przejść później na warstwę płatną `F0` na użytek produkcji.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-gradle-project"></a>Tworzenie nowego projektu gradle

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom polecenie `gradle init` z katalogu roboczego. To polecenie spowoduje utworzenie podstawowych plików kompilacji dla programu Gradle, w tym *pliku build.gradle.kts,* który jest używany w czasie wykonywania do tworzenia i konfigurowania aplikacji.

```console
gradle init --type basic
```

Po wyświetleniu monitu wybierz pozycję **Język DSL**, a następnie **Kotlin**.

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Znajdź *plik build.gradle.kts* i otwórz go za pomocą preferowanego środowiska IDE lub edytora tekstów. Następnie skopiuj w poniższej konfiguracji kompilacji. Ta konfiguracja definiuje projekt jako aplikację Java, której punktem wejścia jest klasa **CustomVisionQuickstart.** Importuje on Custom Vision biblioteki.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "CustomVisionQuickstart"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-training", version = "1.1.0-preview.2")
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-prediction", version = "1.1.0-preview.2")
}
```

### <a name="create-a-java-file"></a>Tworzenie pliku języka Java


Z katalogu roboczego uruchom następujące polecenie, aby utworzyć folder źródłowy projektu:

```console
mkdir -p src/main/java
```

Przejdź do nowego folderu i utwórz plik o nazwie *CustomVisionQuickstart.java.* Otwórz go w preferowanym edytorze lub w języku IDE i dodaj następujące `import` instrukcje:

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_imports)]

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można go znaleźć w witrynie [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java)która zawiera przykłady kodu w tym przewodniku Szybki start.


W klasie **CustomVisionQuickstart** aplikacji utwórz zmienne dla kluczy i punktu końcowego zasobu.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_creds)]


> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasoby Custom Vision utworzone w sekcji  Wymagania wstępne zostały wdrożone pomyślnie, kliknij przycisk Przejdź do **zasobu** w obszarze **Następne kroki.** Klucze i punkt końcowy można znaleźć na stronach kluczy i **punktów** końcowych zasobów w obszarze **zarządzanie zasobami.** Musisz uzyskać klucze dla zasobów trenowania i przewidywania wraz z punktem końcowym interfejsu API dla zasobu trenowania.
>
> Pamiętaj, aby usunąć klucz z kodu, gdy wszystko będzie gotowe, i nigdy nie publikować go publicznie. W środowisku produkcyjnym rozważ użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł Cognitive Services [zabezpieczeń.](../../../cognitive-services-security.md)

W metodzie main **aplikacji** dodaj wywołania metod używanych w tym przewodniku Szybki start. Zdefiniujesz je później.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_maincalls_od)]

## <a name="object-model"></a>Model obiektu

Poniższe klasy i interfejsy obsługują niektóre główne funkcje biblioteki Custom Vision Java.

|Nazwa|Opis|
|---|---|
|[CustomVisionTrainingClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Ta klasa obsługuje tworzenie, szkolenie i publikowanie modeli. |
|[CustomVisionPredictionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Ta klasa obsługuje wykonywanie zapytań o modele w celu przewidywania wykrywania obiektów.|
|[ImagePrediction](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Ta klasa definiuje przewidywanie pojedynczego obiektu na pojedynczym obrazie. Zawiera właściwości identyfikatora i nazwy obiektu, lokalizację pola granicy obiektu oraz ocenę ufności.|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania przy użyciu Custom Vision klienta dla języka Java:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Tworzenie nowego projektu usługi Custom Vision](#create-a-new-custom-vision-project)
* [Dodawanie tagów do projektu](#add-tags-to-the-project)
* [Przekazywanie i Tagi obrazów](#upload-and-tag-images)
* [Uczenie projektu](#train-the-project)
* [Opublikuj bieżącą iterację](#publish-the-current-iteration)
* [Testowanie punktu końcowego przewidywania](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

W metodzie **głównej** należy utworzyć wystąpienia klientów trenowania i przewidywania przy użyciu punktu końcowego i kluczy.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Tworzenie nowego projektu usługi Custom Vision

Ta następna metoda tworzy projekt wykrywania obiektów. Utworzony projekt będzie widoczny w odwiedzonej wcześniej [witrynie internetowej Custom Vision](https://customvision.ai/). Zobacz [przeciążenia metody CreateProject,](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_&preserve-view=true) aby określić inne opcje podczas tworzenia projektu (wyjaśniono w przewodniku po portalu internetowym kompilowania [detektora).](../../get-started-build-detector.md)

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

## <a name="add-tags-to-your-project"></a>Dodanie tagów do projektu

Ta metoda definiuje tagi, na których będzie trenowany model.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

## <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

Najpierw pobierz przykładowe obrazy dla tego projektu. Zapisz zawartość przykładowego [folderu Images na](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) urządzeniu lokalnym.

> [!NOTE]
> Czy potrzebujesz szerszego zestawu obrazów do ukończenia szkolenia? Trove, Microsoft Garage projektu, umożliwia zbieranie i kupowanie zestawów obrazów na potrzeby szkolenia. Po zebraniu obrazów możesz je pobrać, a następnie zaimportować do projektu Custom Vision w zwykły sposób. Odwiedź stronę [Trove, aby](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) dowiedzieć się więcej.

Po oznaczeniu obrazów w projektach wykrywania obiektów należy określić region każdego z otagowanych obiektów przy użyciu znormalizowanych współrzędnych. Poniższy kod kojarzy każdy z przykładowych obrazów ze oznaczonym regionem.

> [!NOTE]
> Jeśli nie masz narzędzia do klikania i przeciągania w celu oznaczania współrzędnych regionów, możesz użyć internetowego interfejsu użytkownika na [stronie Customvision.ai](https://www.customvision.ai/). W tym przykładzie współrzędne są już podane.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Następny blok kodu dodaje obrazy do projektu. Należy zmienić argumenty wywołań, aby wskazać lokalizacje pobranych folderów widelec `GetImage` i  **noży.**

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

Poprzedni fragment kodu korzysta z dwóch funkcji pomocnika, które pobierają obrazy jako strumienie zasobów i przekażą je do usługi (można przekazać maksymalnie 64 obrazy w jednej partii). Zdefiniuj te metody. 

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-the-project"></a>Uczenie projektu

Ta metoda tworzy pierwszą iterację trenowania w projekcie. Wysyła zapytanie do usługi do momentu ukończenia trenowania.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]


## <a name="publish-the-current-iteration"></a>Opublikuj bieżącą iterację

Ta metoda udostępnia bieżącą iterację modelu do wykonywania zapytań. Nazwa modelu może być odwołaniem do wysyłania żądań przewidywania. Musisz wprowadzić własną wartość dla `predictionResourceId` . Identyfikator zasobu przewidywania można znaleźć na  karcie Przegląd zasobu w skrypcie Azure Portal identyfikator **subskrypcji.**

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_publishOD)]

## <a name="test-the-prediction-endpoint"></a>Testowanie punktu końcowego przewidywania

Ta metoda ładuje obraz testowy, wysyła zapytanie do punktu końcowego modelu i wyprowadza dane przewidywania do konsoli.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict_od)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aplikację można skompilować za pomocą:

```console
gradle build
```

Uruchom aplikację za pomocą `gradle run` polecenia :

```console
gradle run
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services zasobów, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie wszystkich innych skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz zostały wykonane wszystkie kroki procesu wykrywania obiektów w kodzie. Ten przykład wykonuje pojedynczą iterację trenowania, ale często trzeba będzie wielokrotnie trenować i testować model, aby był bardziej dokładny. Następny przewodnik dotyczy klasyfikacji obrazów. Jej zasady są podobne do wykrywania obiektów.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](../../test-your-model.md)

* [Co to jest usługa Custom Vision?](../../overview.md)
* Kod źródłowy tego przykładu można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java)
