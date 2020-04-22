---
title: 'Szybki start: biblioteka klienta moderatora zawartości dla języka Java'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dowiedz się, jak rozpocząć pracę z biblioteką klienta moderatora zawartości usług Azure Cognitive Services dla oprogramowania Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: f86a54b53b73da9c798564d13d659844842e6f67
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768708"
---
# <a name="quickstart-content-moderator-client-library-for-java"></a>Szybki start: biblioteka klienta moderatora zawartości dla języka Java

Wprowadzenie do biblioteki klienta Moderatora zawartości w języku Java. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Content Moderator to usługa kognitywna, która sprawdza zawartość tekstową, obrazową i wideo pod kątem materiałów potencjalnie obraźliwych, ryzykownych lub w inny sposób niepożądanych. W przypadku znalezienia takich treści usługa stosuje odpowiednie etykiety (flagi) do zawartości. Aplikacja może następnie obsłużyć oflagowaną zawartość w celu zachowania zgodności z przepisami lub zapewnienia odpowiedniego środowiska dla użytkowników.

Użyj biblioteki klienta Moderatora zawartości dla oprogramowania Java, aby:

* Moderowanie obrazów dla treści dorosłych lub rasistowskich, tekstowych lub ludzkich twarzy.

[Przykłady artefaktu](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [(Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | dokumentacji[referencyjnej](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Aktualna wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Narzędzie kompilacji Gradle](https://gradle.org/install/)lub inny menedżer zależności.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-content-moderator-azure-resource"></a>Tworzenie zasobu platformy Azure moderatora zawartości

Usługi Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla moderatora zawartości przy użyciu [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Pobierz [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez siedem dni za darmo. Po zarejestrowaniu się będzie on dostępny w [witrynie sieci Web platformy Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Wyświetl swój zasób w [witrynie Azure portal](https://portal.azure.com/).

Po otrzymaniu klucza z subskrypcji próbnej lub zasobu [utwórz zmienną środowiskową](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza o nazwie `AZURE_CONTENTMODERATOR_KEY`.

### <a name="create-a-new-gradle-project"></a>Tworzenie nowego projektu Gradle

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```
Uruchom polecenie `gradle init`. To polecenie spowoduje utworzenie podstawowych plików kompilacji dla gradle, w tym *build.gradle.kts*, który jest używany w czasie wykonywania do tworzenia i konfigurowania aplikacji. Uruchom następujące polecenie w katalogu roboczym:

```console
gradle init --type basic
```

Po wyświetleniu monitu o wybranie skryptu kompilacji DSL wybierz **kotlin**.

Znajdź *build.gradle.kts* i otwórz go za pomocą preferowanego edytora IDE lub tekstu. Następnie skopiuj w następującej konfiguracji kompilacji. Ta konfiguracja definiuje projekt jako aplikację Java, której punktem wejścia jest klasa **ContentModeratorQuickstart**. Importuje SDK moderatora zawartości, a także Sdk Gson do serializacji JSON.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

W katalogu roboczym uruchom następujące polecenie, aby utworzyć folder źródłowy projektu.

```console
mkdir -p src/main/java
```

Następnie utwórz plik o nazwie *ContentModeratorQuickstart.java* w nowym folderze. Otwórz plik w preferowanym edytorze lub w ucho i zaimportuj następujące biblioteki u góry:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Model obiektu

Następujące klasy obsługują niektóre z głównych funkcji SDK Java Moderatora zawartości.

|Nazwa|Opis|
|---|---|
|[ContentModeratorClient (łak.](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Ta klasa jest potrzebna dla wszystkich funkcji moderatora zawartości. Tworzenie wystąpienia z informacjami o subskrypcji i używać go do tworzenia wystąpień innych klas.|
|[ImageModeracja](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Ta klasa zapewnia funkcje do analizowania obrazów dla zawartości dla dorosłych, informacji osobistych lub ludzkich twarzy.|
|[TextModerations ( TextModerations )](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Ta klasa zapewnia funkcje do analizowania tekstu dla języka, wulgaryzmów, błędów i informacji osobistych.|
|[Przeglądy](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Ta klasa udostępnia funkcje interfejsu API przeglądu, w tym metody tworzenia zadań, niestandardowych przepływów pracy i przeglądów ludzkich.|


## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta moderatora zawartości w języku Java:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Umiarkowane obrazy](#moderate-images)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

> [!NOTE]
> W tym kroku przyjęto założenie, że [utworzono zmienną środowiskową](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza Moderatora zawartości o nazwie `AZURE_CONTENTMODERATOR_KEY`.

W `main` metodzie aplikacji utwórz [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) obiektu przy użyciu wartości punktu końcowego subskrypcji i zmiennej środowiskowej klucza subskrypcji. 

> [!NOTE]
> Jeśli po uruchomieniu aplikacji utworzono zmienną środowiskową, należy zamknąć i ponownie otworzyć edytor, IDE lub powłokę uruchamianą ją, aby uzyskać dostęp do zmiennej.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Umiarkowane obrazy

### <a name="get-images"></a>Pobieranie obrazów

W folderze **src/main/** projektu utwórz folder **zasobów** i przejdź do niego. Następnie utwórz nowy plik tekstowy *ImageFiles.txt*. W tym pliku dodasz adresy URL&mdash;obrazów, aby przeanalizować jeden adres URL w każdym wierszu. Można użyć następujących przykładowych obrazów:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Definiowanie klasy pomocnika

Następnie w pliku *ContentModeratorQuickstart.java* dodaj następującą definicję klasy wewnątrz klasy **ContentModeratorQuickstart.** Ta klasa wewnętrzna będzie używana w dalszej części procesu moderowania obrazu.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Iteruj przez obrazy

Następnie dodaj następujący kod na dole `main` metody. Możesz też dodać go do osobnej metody, `main`która jest wywoływana z pliku . Ten kod przechodzi przez każdy wiersz pliku _ImageFiles.txt._

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Sprawdź, czy treści dla dorosłych/rasy
Ten wiersz kodu sprawdza obraz pod podanym adresem URL dla treści dla dorosłych lub rasistowskich. Zobacz przewodnik koncepcyjny moderowania obrazu, aby uzyskać informacje na temat tych terminów.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Sprawdzanie tekstu
Ten wiersz kodu sprawdza obraz pod kątem widocznego tekstu.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Sprawdź, czy twarze
Ten wiersz kodu sprawdza obraz dla ludzkich twarzy.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Na koniec należy zapisać zwrócone informacje na `EvaluationData` liście.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Drukowanie wyników

Po `while` pętli dodaj następujący kod, który drukuje wyniki do konsoli i do pliku wyjściowego, *src/main/resources/ModerationOutput.json*.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Zamknij instrukcję `try` i `catch` dodaj instrukcję, aby zakończyć metodę.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Możesz utworzyć aplikację za pomocą:

```console
gradle build
```

Uruchom aplikację za `gradle run` pomocą polecenia:

```console
gradle run
```

Następnie przejdź do pliku *src/main/resources/ModerationOutput.json* i wyświetl wyniki moderowania zawartości.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak korzystać z biblioteki Java moderatora zawartości do wykonywania zadań moderowania. Następnie dowiedz się więcej o moderowaniu obrazów lub innych mediów, czytając przewodnik koncepcyjny.

> [!div class="nextstepaction"]
>[Pojęcia dotyczące moderowania obrazu](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Czym jest usługa Azure Content Moderator?](./overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java).