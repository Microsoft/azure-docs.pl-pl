---
title: Content Moderator szybki start dla biblioteki klienta Java
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dowiesz się, jak rozpocząć pracę z biblioteką klienta usługi Azure Content Moderator dla języka Java. Wbuduj oprogramowanie do filtrowania zawartości w aplikację w celu zachowania zgodności z przepisami lub zachowania środowiska przeznaczonego dla użytkowników.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 10/16/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 76d62806541e0a3186cd17712b7af3930d9ccf9f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726164"
---
Wprowadzenie do biblioteki klienta usługi Azure Content Moderator dla języka Java. Wykonaj następujące kroki, aby zainstalować pakiet Maven i wypróbować przykładowy kod podstawowych zadań. 

Content Moderator to usługa AI, która umożliwia obsługę zawartości, która może być potencjalnie obraźliwa, ryzykowna lub w inny sposób niepożądana. Użyj usługi moderowania zawartości obsługiwanej przez AI, aby skanować tekst, obrazy i wideo oraz automatycznie stosować flagi zawartości. Wbuduj oprogramowanie do filtrowania zawartości w aplikację w celu zachowania zgodności z przepisami lub zachowania środowiska przeznaczonego dla użytkowników.

Użyj biblioteki Content Moderator klienta dla języka Java, aby:

* Moderuj tekst
* Moderowane obrazy

[Dokumentacja referencyjna](/java/api/overview/azure/cognitiveservices/client/contentmoderator)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-contentmoderator)  | [Artefakt (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator)  |  [Przykłady](/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Bieżąca wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Narzędzie [kompilacji Gradle](https://gradle.org/install/)lub inny menedżer zależności.
* Po utworzeniu subskrypcji platformy Azure utwórz zasób usługi Content Moderator utwórz zasób Content Moderator w witrynie Azure Portal, aby uzyskać <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Poczekaj na wdrożenie, a następnie kliknij **przycisk Przejdź do** zasobu.
    * Klucz i punkt końcowy z zasobu, który utworzysz, będą potrzebne do połączenia aplikacji z Content Moderator. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
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

## <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Znajdź *plik build.gradle.kts* i otwórz go za pomocą preferowanego środowiska IDE lub edytora tekstów. Następnie skopiuj w poniższej konfiguracji kompilacji. Ta konfiguracja definiuje projekt jako aplikację Java, której punktem wejścia jest klasa **ContentModeratorQuickstart**. Importuje on Content Moderator klienta i zestaw SDK GSON na celu serializację JSON.

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

### <a name="create-a-java-file"></a>Tworzenie pliku języka Java


Z katalogu roboczego uruchom następujące polecenie, aby utworzyć folder źródłowy projektu:

```console
mkdir -p src/main/java
```

Przejdź do nowego folderu i utwórz plik o nazwie *ContentModeratorQuickstart.java.* Otwórz go w preferowanym edytorze lub w języku IDE i dodaj następujące `import` instrukcje:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można go znaleźć w witrynie [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java)która zawiera przykłady kodu w tym przewodniku Szybki start.

W klasie **ContentModeratorQuickstart** aplikacji utwórz zmienne dla klucza i punktu końcowego zasobu.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_creds)]

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasób Content Moderator utworzony w sekcji **Wymagania wstępne** został pomyślnie wdrożony, kliknij przycisk **Przejdź** do zasobu w obszarze **Następne kroki.** Klucz i punkt końcowy można znaleźć  na stronie klucza i punktu końcowego zasobu w obszarze **zarządzanie zasobami**. 
>
> Pamiętaj, aby usunąć klucz z kodu, gdy wszystko będzie gotowe, i nigdy nie publikować go publicznie. W środowisku produkcyjnym rozważ użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł Cognitive Services [zabezpieczeń.](../../../cognitive-services-security.md)

W metodzie głównej **aplikacji** dodaj wywołania metod używanych w tym przewodniku Szybki start. Te metody zdefiniuje się później.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_maincalls)]


## <a name="object-model"></a>Model obiektów

Poniższe klasy obsługują niektóre główne funkcje biblioteki Content Moderator Java.

|Nazwa|Opis|
|---|---|
|[ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient)|Ta klasa jest potrzebna dla wszystkich Content Moderator funkcjonalności. Należy utworzyć jego wystąpienie z informacjami o subskrypcji i użyć ich do tworzenia wystąpień innych klas.|
|[ImageModeration](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations)|Ta klasa udostępnia funkcję analizowania obrazów pod celu analizowania zawartości dla dorosłych, informacji osobistych lub ludzkich twarzy.|
|[TextModerations](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations)|Ta klasa udostępnia funkcję analizowania tekstu pod temat języka, wulgaryzmów, błędów i informacji osobistych.|
|[Przeglądy](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews)|Ta klasa udostępnia funkcje interfejsów API przeglądu, w tym metody tworzenia zadań, niestandardowych przepływów pracy i recenzji człowieka.|


## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania przy użyciu Content Moderator klienta dla języka Java:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Moderuj tekst](#moderate-text)
* [Moderowane obrazy](#moderate-images)


## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

W metodzie aplikacji `main` utwórz obiekt [ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient) przy użyciu wartości punktu końcowego subskrypcji i klucza subskrypcji.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]



## <a name="moderate-text"></a>Moderuj tekst

### <a name="set-up-sample-text"></a>Konfigurowanie przykładowego tekstu

W górnej części **klasy ContentModeratorQuickstart** zdefiniuj odwołanie do lokalnego pliku tekstowego. Dodaj plik txt do katalogu projektu i wprowadź tekst, który chcesz przeanalizować.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_var)]

### <a name="analyze-text"></a>Analizowanie tekstu

Utwórz nową metodę, która odczytuje plik txt i wywołuje metodę **screenText** w każdym wierszu.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod)]

### <a name="print-text-moderation-results"></a>Drukowanie wyników moderowania tekstu

Dodaj następujący kod, aby wydrukować wyniki moderowania do pliku json w katalogu projektu.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_print)]

Zamknij instrukcje `try` i `catch` , aby ukończyć metodę .

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_catch)]


## <a name="moderate-images"></a>Moderowane obrazy

### <a name="set-up-sample-image"></a>Konfigurowanie przykładowego obrazu

W nowej metodzie skonstruuj obiekt **[BodyModelModel](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.bodymodelmodel)** z danym ciągiem adresu URL, który wskazuje na obraz.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod)]


### <a name="define-helper-class"></a>Definiowanie klasy pomocnika

Następnie w pliku *ContentModeratorQuickstart.java* dodaj następującą definicję klasy wewnątrz **klasy ContentModeratorQuickstart.** Ta klasa wewnętrzna jest używana w procesie moderowania obrazów.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]


### <a name="analyze-content"></a>Analizowanie zawartości
Ten wiersz kodu sprawdza, czy obraz pod danym adresem URL zawiera treści dla dorosłych lub treści racy. Aby uzyskać informacje na temat tych terminów, zobacz przewodnik koncepcyjny dotyczący moderowania obrazów.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Sprawdzanie tekstu
Ten wiersz kodu sprawdza, czy obraz jest widoczny tekst.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Sprawdzanie twarzy
Ten wiersz kodu sprawdza, czy na obrazie nie ma ludzkich twarzy.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Na koniec przechowuj zwrócone informacje na `EvaluationData` liście.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Drukowanie wyników

Po pętli dodaj następujący kod, który drukuje wyniki w konsoli i w pliku wyjściowym `while` *src/main/resources/ModerationOutput.jspliku*.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Zamknij instrukcje `try` i dodaj `catch` instrukcje , aby ukończyć metodę .

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aplikację można skompilować za pomocą:

```console
gradle build
```

Uruchom aplikację za pomocą `gradle run` polecenia :

```console
gradle run
```

Następnie przejdź do *pliku src/main/resources/ModerationOutput.js* i wyświetl wyniki moderowania zawartości.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie wszystkich innych skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano, jak używać biblioteki Content Moderator Java do wykonywania zadań moderowania. Następnie dowiedz się więcej na temat moderowania obrazów lub innych nośników, czytając przewodnik koncepcyjny.

> [!div class="nextstepaction"]
> [Pojęcia dotyczące moderowania obrazów](../../image-moderation-api.md)
