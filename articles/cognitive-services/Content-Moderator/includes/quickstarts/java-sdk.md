---
title: Content Moderator Java Client Library — Szybki Start
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start dowiesz się, jak rozpocząć pracę z biblioteką kliencką Content Moderator platformy Azure dla języka Java. Twórz oprogramowanie do filtrowania zawartości w aplikacji, aby zachować zgodność z przepisami lub zachować zamierzone środowisko dla użytkowników.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 1e32cd924c8e0f713ebe7cedfca0466a1e07c3bf
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91332580"
---
Rozpocznij pracę z biblioteką kliencką Content Moderator platformy Azure dla języka Java. Wykonaj następujące kroki, aby zainstalować pakiet Maven i wypróbować przykładowy kod dla podstawowych zadań. 

Content Moderator to usługa AI, która umożliwia obsługę zawartości potencjalnie obraźliwej, ryzykownej lub niepożądanej. Użyj usługi moderowania zawartości opartej na AI do skanowania tekstu, obrazów i filmów wideo i automatycznego stosowania flag zawartości. Twórz oprogramowanie do filtrowania zawartości w aplikacji, aby zachować zgodność z przepisami lub zachować zamierzone środowisko dla użytkowników.

Użyj biblioteki klienta Content Moderator dla języka Java, aby:

* Umiarkowane obrazy dotyczące zawartości dla dorosłych lub erotycznej, tekstu lub ludzkich twarzy.

[Dokumentacja](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable)  |  referencyjna [Artefakt (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator)  |  [Przykłady](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Bieżąca wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Narzędzie kompilacji Gradle](https://gradle.org/install/)lub inny Menedżer zależności.

## <a name="create-a-content-moderator-resource"></a>Tworzenie zasobu Content Moderator

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla Content Moderator przy użyciu [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Wyświetl zasób na [Azure Portal](https://portal.azure.com/).

Po uzyskaniu klucza z zasobu [Utwórz zmienną środowiskową](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza o nazwie `AZURE_CONTENTMODERATOR_KEY` .

## <a name="create-a-new-gradle-project"></a>Utwórz nowy projekt Gradle

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```
Uruchom polecenie `gradle init`. To polecenie spowoduje utworzenie podstawowych plików kompilacji dla Gradle, w tym *Build. Gradle. KTS*, który jest używany w środowisku uruchomieniowym do tworzenia i konfigurowania aplikacji. Uruchom następujące polecenie w katalogu roboczym:

```console
gradle init --type basic
```

Po wyświetleniu monitu o wybranie skryptu kompilacji DSL wybierz pozycję **Kotlin**.

## <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Znajdź plik *Build. Gradle. KTS* i otwórz go za pomocą PREFEROWANEGO środowiska IDE lub edytora tekstu. Następnie skopiuj w poniższej konfiguracji kompilacji. Ta konfiguracja definiuje projekt jako aplikację Java, której punkt wejścia to Klasa **ContentModeratorQuickstart**. Importuje Content Moderator bibliotekę kliencką oraz zestaw SDK Gson dla serializacji JSON.

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

W katalogu roboczym Uruchom następujące polecenie, aby utworzyć folder źródłowy projektu.

```console
mkdir -p src/main/java
```

Następnie utwórz plik o nazwie *ContentModeratorQuickstart. Java* w nowym folderze. Otwórz plik w preferowanym edytorze lub środowisku IDE i zaimportuj następujące biblioteki w górnej części:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Model obiektów

Poniższe klasy obsługują niektóre główne funkcje biblioteki klienta Java Content Moderator.

|Nazwa|Opis|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Ta klasa jest wymagana dla wszystkich funkcji Content Moderator. Tworzysz wystąpienie z informacjami o subskrypcji i używasz ich do tworzenia wystąpień innych klas.|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Ta klasa udostępnia funkcje do analizowania obrazów na potrzeby zawartości dla dorosłych, danych osobowych lub osób ludzkich.|
|[Textmoderators](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Ta klasa udostępnia funkcje do analizowania tekstu dla języka, niewulgarności, błędów i informacji osobistych.|
|[Przeglądy](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Ta klasa udostępnia funkcje interfejsów API przeglądu, w tym metody tworzenia zadań, niestandardowych przepływów pracy i przeglądów ludzkich.|


## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienta Content Moderator dla języka Java:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Obrazy umiarkowane](#moderate-images)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

> [!NOTE]
> W tym kroku przyjęto założenie, że dla klucza Content Moderator została [utworzona zmienna środowiskowa](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) o nazwie `AZURE_CONTENTMODERATOR_KEY` .

W `main` metodzie aplikacji Utwórz obiekt [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) przy użyciu wartości punktu końcowego subskrypcji i zmiennej środowiskowej klucza subskrypcji. 

> [!NOTE]
> Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę, na których jest uruchomiona, aby uzyskać dostęp do zmiennej.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Obrazy umiarkowane

### <a name="get-sample-images"></a>Pobierz przykładowe obrazy

W folderze **src/Main/** folder projektu Utwórz folder **zasobów** i przejdź do niego. Następnie utwórz nowy plik tekstowy *ImageFiles.txt*. W tym pliku należy dodać adresy URL obrazów do analizowania &mdash; jednego adresu URL w każdym wierszu. Możesz użyć następujących przykładowych obrazów:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Zdefiniuj klasę pomocnika

Następnie w pliku *ContentModeratorQuickstart. Java* Dodaj następującą definicję klasy wewnątrz klasy **ContentModeratorQuickstart** . Ta wewnętrzna Klasa zostanie później użyta w procesie moderowania obrazu.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Wykonaj iterację obrazów

Następnie Dodaj następujący kod na końcu `main` metody. Można też dodać go do oddzielnej metody, która jest wywoływana z `main` . Ten kod jest wykonywany w każdym wierszu pliku _ImageFiles.txt_ .

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="analyze-content"></a>Analizowanie zawartości
Ten wiersz kodu sprawdza obraz pod podanym adresem URL dla zawartości dla dorosłych lub erotycznej. Informacje na temat tych warunków można znaleźć w przewodniku koncepcyjnym moderowania obrazu.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Sprawdź tekst
Ten wiersz kodu sprawdza obraz dla widocznego tekstu.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Sprawdź dostępność twarzy
Ten wiersz kodu sprawdza obraz dla ludzkich twarzy.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Na koniec Zapisz zwrócone informacje na `EvaluationData` liście.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Drukuj wyniki

Po `while` pętli Dodaj następujący kod, który drukuje wyniki do konsoli programu i do pliku wyjściowego, *src/Main/sources/ModerationOutput.json*.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Zamknij `try` instrukcję i Dodaj `catch` instrukcję, aby ukończyć metodę.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aplikację można skompilować przy użyciu:

```console
gradle build
```

Uruchom aplikację za pomocą `gradle run` polecenia:

```console
gradle run
```

Następnie przejdź do pliku *src/Main/sources/ModerationOutput.js* , a następnie Wyświetl wyniki moderowania zawartości.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób używania biblioteki języka Java Content Moderator do wykonywania zadań moderowania. Następnie Dowiedz się więcej o moderowaniu obrazów lub innych nośnikach, odczytując Przewodnik koncepcyjny.

> [!div class="nextstepaction"]
> [Pojęcia związane z moderowaniem obrazu](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Czym jest usługa Azure Content Moderator?](../../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java).