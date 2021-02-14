---
author: PatrickFarley
ms.custom: devx-track-java
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/13/2020
ms.openlocfilehash: 14b72398afca0fa5103303219c67811a65cb4cb3
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100105814"
---
Rozpocznij korzystanie z biblioteki klienta Custom Vision dla języka Java w celu utworzenia modelu wykrywania obiektów. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Użyj tego przykładu jako szablonu do tworzenia własnej aplikacji rozpoznawania obrazu.

> [!NOTE]
> Jeśli chcesz skompilować i przeszkolić model wykrywania obiektów _bez_ pisania kodu, zamiast tego zapoznaj się ze [wskazówkami w przeglądarce](../../get-started-build-detector.md) .

Użyj biblioteki klienta Custom Vision dla języka Java, aby:

* Tworzenie nowego projektu usługi Custom Vision
* Dodawanie tagów do projektu
* Przekazywanie i Tagi obrazów
* Uczenie projektu
* Opublikuj bieżącą iterację
* Testowanie punktu końcowego przewidywania

[Dokumentacja referencyjna](/java/api/overview/azure/cognitiveservices/client/customvision) | Kod źródłowy biblioteki [(szkolenie)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-training) [(przewidywania)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-prediction)| Artefakty (Maven) ( [uczenie](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-training/1.1.0-preview.2/jar) [) —](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-prediction/1.1.0-preview.2/jar)  | 
 [przykłady](/samples/browse/?products=azure&terms=custom%20vision)


## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Bieżąca wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Narzędzie kompilacji Gradle](https://gradle.org/install/)lub inny Menedżer zależności.
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title=" Utwórz zasób Custom Vision "  target="_blank"> utwórz zasób Custom Vision <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby utworzyć zasób szkoleniowy i predykcyjny oraz pobrać klucze i punkt końcowy. Zaczekaj na jego wdrożenie i kliknij przycisk **Przejdź do zasobu** .
    * Będziesz potrzebować klucza i punktu końcowego z zasobów, które tworzysz, aby połączyć aplikację z Custom Vision. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-gradle-project"></a>Utwórz nowy projekt Gradle

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `gradle init` polecenie z katalogu roboczego. To polecenie spowoduje utworzenie podstawowych plików kompilacji dla Gradle, w tym *Build. Gradle. KTS*, który jest używany w środowisku uruchomieniowym do tworzenia i konfigurowania aplikacji.

```console
gradle init --type basic
```

Po wyświetleniu monitu wybierz pozycję **Język DSL**, a następnie **Kotlin**.

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Znajdź element *Build. Gradle. KTS* i otwórz go za pomocą PREFEROWANEGO środowiska IDE lub edytora tekstu. Następnie skopiuj w poniższej konfiguracji kompilacji. Ta konfiguracja definiuje projekt jako aplikację Java, której punkt wejścia to Klasa **CustomVisionQuickstart**. Importuje Custom Vision biblioteki.

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


W katalogu roboczym Uruchom następujące polecenie, aby utworzyć folder źródłowy projektu:

```console
mkdir -p src/main/java
```

Przejdź do nowego folderu i Utwórz plik o nazwie *CustomVisionQuickstart. Java*. Otwórz go w preferowanym edytorze lub środowisku IDE i Dodaj następujące `import` instrukcje:

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_imports)]

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java), która zawiera przykłady kodu w tym przewodniku Szybki Start.


W klasie **CustomVisionQuickstart** aplikacji Utwórz zmienne dla kluczy i punktów końcowych zasobu.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_creds)]


> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli pomyślnie wdrożono zasoby Custom Vision utworzone w sekcji **wymagania wstępne** , kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki**. Klucze i punkt końcowy można znaleźć na stronach "klucze zasobów" **i punkt końcowy** w obszarze **Zarządzanie zasobami**. Musisz pobrać klucze szkoleniowe i prognozowe wraz z punktem końcowym zasobów szkoleniowych.
>
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [zabezpieczeń](../../../cognitive-services-security.md) Cognitive Services.

W metodzie **głównej** aplikacji Dodaj wywołania metod używanych w tym przewodniku Szybki Start. Zdefiniujesz je później.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_maincalls_od)]

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje biblioteki klienta Java Custom Vision.

|Nazwa|Opis|
|---|---|
|[CustomVisionTrainingClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Ta klasa obsługuje tworzenie, uczenie i publikowanie modeli. |
|[CustomVisionPredictionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Ta klasa obsługuje zapytania dotyczące modeli na potrzeby przewidywania wykrywania obiektów.|
|[ImagePrediction](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Ta klasa definiuje jednokrotne prognozowanie obiektów na pojedynczym obrazie. Obejmuje ona właściwości identyfikatora obiektu i nazwy, lokalizacji pola ograniczenia obiektu i oceny zaufania.|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienta Custom Vision dla języka Java:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Tworzenie nowego projektu usługi Custom Vision](#create-a-new-custom-vision-project)
* [Dodawanie tagów do projektu](#add-tags-to-the-project)
* [Przekazywanie i Tagi obrazów](#upload-and-tag-images)
* [Uczenie projektu](#train-the-project)
* [Opublikuj bieżącą iterację](#publish-the-current-iteration)
* [Testowanie punktu końcowego przewidywania](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

W **głównej** metodzie Utwórz wystąpienie klientów szkoleń i prognoz przy użyciu punktu końcowego i kluczy.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Tworzenie nowego projektu usługi Custom Vision

Ta Następna Metoda tworzy projekt wykrywania obiektu. Utworzony projekt będzie widoczny w odwiedzonej wcześniej [witrynie internetowej Custom Vision](https://customvision.ai/). Zobacz przeciążania metod tworzenia [projektu](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_&preserve-view=true) , aby określić inne opcje podczas tworzenia projektu (wyjaśnione w przewodniku po portalu sieci Web dla [czujnika](../../get-started-build-detector.md) ).

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

## <a name="add-tags-to-your-project"></a>Dodanie tagów do projektu

Ta metoda definiuje Tagi, w których będzie nauczyć się model.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

## <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

Najpierw pobierz przykładowe obrazy dla tego projektu. Zapisz zawartość [folderu Sample images](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) na urządzeniu lokalnym.

> [!NOTE]
> Potrzebujesz szerszego zestawu obrazów, aby dokończyć szkolenia? Skarbnica, projekt garażu firmy Microsoft, umożliwia zbieranie i kupowanie zestawów obrazów do celów szkoleniowych. Po zebraniu obrazów można je pobrać, a następnie zaimportować do projektu Custom Vision w zwykły sposób. Odwiedź [stronę skarbnica](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) , aby dowiedzieć się więcej.

Po oznaczeniu obrazów w projektach wykrywania obiektów należy określić region każdego z otagowanych obiektów przy użyciu znormalizowanych współrzędnych. Poniższy kod kojarzy każdy z przykładowych obrazów ze oznaczonym regionem.

> [!NOTE]
> Jeśli nie masz narzędzia do klikania i przeciągania, aby oznaczyć współrzędne regionów, możesz użyć internetowego interfejsu użytkownika w [Customvision.AI](https://www.customvision.ai/). W tym przykładzie współrzędne zostały już podane.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Następny blok kodu dodaje obrazy do projektu. Należy zmienić argumenty `GetImage` wywołań, aby wskazywały lokalizacje pobranych folderów **rozwidlenia** i **nożyczków** .

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

W poprzednim fragmencie kodu są używane dwie funkcje pomocnika, które pobierają obrazy jako strumienie zasobów i przekazują je do usługi (w jednej partii można przekazać do 64 obrazów). Zdefiniuj te metody. 

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-the-project"></a>Uczenie projektu

Ta metoda tworzy pierwszą iterację szkoleniową w projekcie. Wysyła zapytanie do usługi do momentu ukończenia szkolenia.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]


## <a name="publish-the-current-iteration"></a>Opublikuj bieżącą iterację

Ta metoda powoduje, że bieżąca iteracja modelu jest dostępna na potrzeby wykonywania zapytań. Możesz użyć nazwy modelu jako odwołania do wysyłania żądań przewidywania. Musisz wprowadzić własną wartość dla `predictionResourceId` . Identyfikator zasobu przewidywania można znaleźć na karcie **Przegląd** zasobu w Azure Portal, na liście jako **Identyfikator subskrypcji**.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_publishOD)]

## <a name="test-the-prediction-endpoint"></a>Testowanie punktu końcowego przewidywania

Ta metoda służy do ładowania obrazu testowego, wysyłania zapytań do punktu końcowego modelu i wyprowadzania danych prognoz do konsoli.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict_od)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aplikację można skompilować przy użyciu:

```console
gradle build
```

Uruchom aplikację za pomocą `gradle run` polecenia:

```console
gradle run
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz każdy krok procesu wykrywania obiektów można wykonać w kodzie. Ten przykład wykonuje pojedynczą iterację szkoleniową, ale często należy przeprowadzić uczenie i testowanie modelu wiele razy, aby zwiększyć jego dokładność. Następny przewodnik dotyczy klasyfikacji obrazów. Jej zasady są podobne do wykrywania obiektów.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](../../test-your-model.md)

* [Co to jest usługa Custom Vision?](../../overview.md)
* Kod źródłowy dla tego przykładu można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java)
