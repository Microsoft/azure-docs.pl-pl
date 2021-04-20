---
title: Content Moderator szybki start dla biblioteki klienta .NET
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dowiesz się, jak rozpocząć pracę z biblioteką klienta usługi Azure Content Moderator dla platformy .NET. Wbuduj oprogramowanie do filtrowania zawartości w aplikację w celu zachowania zgodności z przepisami lub zachowania środowiska przeznaczonego dla użytkowników.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.author: pafarley
ms.custom: devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: 573778316802b6e445b4c9d78576a8813af514a6
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726142"
---
Wprowadzenie do biblioteki klienta usługi Azure Content Moderator dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet NuGet i wypróbować przykładowy kod podstawowych zadań. 

Content Moderator to usługa AI, która umożliwia obsługę zawartości, która może być potencjalnie obraźliwa, ryzykowna lub w inny sposób niepożądana. Użyj usługi moderowania zawartości obsługiwanej przez AI, aby skanować tekst, obrazy i wideo oraz automatycznie stosować flagi zawartości. Następnie zintegruj aplikację z narzędziem Review , środowiskiem moderatora online dla zespołu recenzentów. Wbuduj oprogramowanie do filtrowania zawartości w aplikację w celu zachowania zgodności z przepisami lub zachowania środowiska przeznaczonego dla użytkowników.

Użyj biblioteki Content Moderator klienta dla programu .NET, aby:

* Moderuj tekst
* Moderowane obrazy
* Tworzenie przeglądu

[Dokumentacja referencyjna](/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator)  |  [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)  |  [Przykłady](../../samples-dotnet.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Program [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) lub bieżącą wersję programu [.NET Core.](https://dotnet.microsoft.com/download/dotnet-core)
* Po utworzeniu subskrypcji platformy Azure utwórz zasób usługi Content Moderator utwórz zasób Content Moderator w witrynie Azure Portal, aby uzyskać <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Poczekaj na wdrożenie, a następnie kliknij **przycisk Przejdź do** zasobu.
    * Klucz i punkt końcowy z zasobu, który utworzysz, będą potrzebne do połączenia aplikacji z Content Moderator. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
    * Możesz użyć bezpłatnej warstwy cenowej ( ), aby wypróbować usługę, a następnie uaktualnić ją do warstwy `F0` płatnej w środowisku produkcyjnym.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-c-application"></a>Tworzenie nowej aplikacji w języku C#

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Za Visual Studio utwórz nową aplikację .NET Core. 

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta 

Po utworzeniu nowego projektu zainstaluj bibliotekę klienta, klikając prawym przyciskiem myszy rozwiązanie projektu w Eksplorator rozwiązań **i** wybierając pozycję Zarządzaj pakietami **NuGet.** W menedżerze pakietów, który zostanie otwarty, wybierz **pozycję Przeglądaj,** zaznacz pole **wyboru Uwzględnij wstępną publikację** i `Microsoft.Azure.CognitiveServices.ContentModerator` wyszukaj . Wybierz wersję `2.0.0` , a następnie pozycję **Zainstaluj.** 

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

W oknie konsoli (takim jak cmd, PowerShell lub Bash) użyj polecenia , aby utworzyć nową aplikację `dotnet new` konsolową o nazwie `content-moderator-quickstart` . To polecenie tworzy prosty projekt języka C# "Hello world" z pojedynczym plikiem źródłowym: *Program.cs.*

```console
dotnet new console -n content-moderator-quickstart
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

W katalogu aplikacji zainstaluj bibliotekę Content Moderator klienta dla programu .NET za pomocą następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

---

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można go znaleźć w witrynie [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs)która zawiera przykłady kodu w tym przewodniku Szybki start.

W katalogu projektu otwórz plik *Program.cs* w preferowanym edytorze lub w preferowanym idee. Dodaj następujące instrukcje `using`:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

W **klasie Program** utwórz zmienne dla klucza i punktu końcowego zasobu.

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasób Content Moderator utworzony w sekcji **Wymagania wstępne** został pomyślnie wdrożony, kliknij przycisk **Przejdź** do zasobu w obszarze **Następne kroki.** Klucz i punkt końcowy można znaleźć  na stronie klucza i punktu końcowego zasobu w obszarze **zarządzanie zasobami**. 
>
> Pamiętaj, aby usunąć klucz z kodu, gdy wszystko będzie gotowe, i nigdy nie publikować go publicznie. W środowisku produkcyjnym rozważ użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz Cognitive Services [zabezpieczeń.](../../../cognitive-services-security.md)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]


W metodzie aplikacji dodaj wywołania metod używanych `main()` w tym przewodniku Szybki start. Utworzysz je później.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]


## <a name="object-model"></a>Model obiektów

Poniższe klasy obsługują niektóre główne funkcje biblioteki klienta Content Moderator .NET.

|Nazwa|Opis|
|---|---|
|[ContentModeratorClient](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient)|Ta klasa jest potrzebna dla wszystkich Content Moderator funkcjonalności. Należy utworzyć jego wystąpienie z informacjami o subskrypcji i użyć ich do tworzenia wystąpień innych klas.|
|[ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation)|Ta klasa udostępnia funkcję analizowania obrazów pod celu analizowania zawartości dla dorosłych, informacji osobistych lub ludzkich twarzy.|
|[TextModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation)|Ta klasa udostępnia funkcję analizowania tekstu pod temat języka, wulgaryzmów, błędów i informacji osobistych.|
|[Przeglądy](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews)|Ta klasa udostępnia funkcje interfejsów API przeglądu, w tym metody tworzenia zadań, niestandardowych przepływów pracy i recenzji człowieka.|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania przy użyciu Content Moderator klienta dla programu .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Moderuj tekst](#moderate-text)
* [Moderowane obrazy](#moderate-images)
* [Tworzenie przeglądu](#create-a-review)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

W nowej metodzie należy utworzyć wystąpienia obiektów klienta z punktem końcowym i kluczem.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_auth)]

## <a name="moderate-text"></a>Moderuj tekst

Poniższy kod używa klienta Content Moderator do analizowania treści tekstu i drukowania wyników w konsoli. W katalogu głównym klasy **Program** zdefiniuj pliki wejściowe i wyjściowe:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

Następnie w katalogu głównym projektu dodaj *TextFile.txt* plik. Dodaj własny tekst do tego pliku lub użyj następującego przykładowego tekstu:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```


Następnie zdefiniuj metodę moderowania tekstu w klasie **Program:**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Moderowane obrazy

Poniższy kod używa klienta Content Moderator, wraz z [obiektem ImageModeration,](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation) do analizowania obrazów zdalnych pod Content Moderator zawartości dla dorosłych i zawartości erze.

> [!NOTE]
> Można również analizować zawartość obrazu lokalnego. Zapoznaj się z [dokumentacją referencyjną](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) dotyczącą metod i operacji, które działają z obrazami lokalnymi.

### <a name="get-sample-images"></a>Uzyskiwanie przykładowych obrazów

Zdefiniuj pliki wejściowe i wyjściowe w katalogu głównym klasy **Program:**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Następnie utwórz plik wejściowy *ImageFiles.txt* w katalogu głównym projektu. W tym pliku dodasz adresy URL obrazów, aby przeanalizować &mdash; jeden adres URL w każdym wierszu. Możesz użyć następujących przykładowych obrazów:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Definiowanie klasy pomocnika

Dodaj następującą definicję klasy w **klasie Program.** Ta klasa wewnętrzna będzie obsługiwać wyniki moderowania obrazów.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Definiowanie metody moderowania obrazów

Następująca metoda iteruje po adresach URL obrazów w pliku tekstowym, tworzy wystąpienie **evaluationData** i analizuje obraz pod celu przeanalizowania zawartości dla dorosłych/zawartości erze, tekstu i ludzkich twarzy. Następnie dodaje końcowe wystąpienie **EvaluationData** do listy i zapisuje pełną listę zwróconych danych w konsoli.

#### <a name="iterate-through-images"></a>Iteruj po obrazach

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Analizowanie zawartości

Aby uzyskać więcej informacji na temat atrybutów obrazu, dla Content Moderator, zobacz Przewodnik po pojęciach [dotyczących moderowania](../../image-moderation-api.md) obrazów.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Zapis wyników moderowania do pliku

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Tworzenie przeglądu

Możesz użyć biblioteki klienta Content Moderator .NET, aby [](https://contentmoderator.cognitive.microsoft.com) nasyłać zawartość do narzędzia do przeglądu, aby moderatorzy human moderatorzy mogą ją przeglądać. Aby dowiedzieć się więcej na temat narzędzia do przeglądu, zobacz przewodnik koncepcyjny dotyczący [narzędzia do przeglądu.](../../review-tool-user-guide/human-in-the-loop.md)

Metoda w tej sekcji używa klasy [Reviews,](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews) aby utworzyć przegląd, pobrać jego identyfikator i sprawdzić jego szczegóły po otrzymaniu ludzkich danych wejściowych za pośrednictwem portalu internetowego narzędzia do przeglądu. Rejestruje wszystkie te informacje w wyjściowym pliku tekstowym. 

### <a name="get-sample-images"></a>Uzyskiwanie przykładowych obrazów

Zadeklaruj następującą tablicę w katalogu głównym **klasy Program.** Ta zmienna odwołuje się do przykładowego obrazu służącego do tworzenia przeglądu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Uzyskiwanie poświadczeń przeglądu

Zaloguj się do narzędzia [do przeglądu i](https://contentmoderator.cognitive.microsoft.com) pobierz nazwę zespołu. Następnie przypisz ją do odpowiedniej zmiennej w **klasie Program.** Opcjonalnie możesz skonfigurować punkt końcowy wywołania zwrotnego, aby otrzymywać aktualizacje dotyczące działania przeglądu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Definiowanie klasy pomocnika

Dodaj następującą definicję klasy w **klasie Program.** Ta klasa będzie służyć do reprezentowania pojedynczego wystąpienia przeglądu przesłanego do narzędzia review.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Definiowanie metody pomocnika

Dodaj następującą metodę do klasy **Program**. Ta metoda zapisze wyniki zapytań przeglądu do wyjściowego pliku tekstowego.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Definiowanie metody tworzenia przeglądu

Teraz możesz zdefiniować metodę, która będzie obsługiwać tworzenie przeglądu i wykonywanie zapytań. Dodaj nową metodę **CreateReviews** i zdefiniuj następujące zmienne lokalne.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Publikować przeglądy w narzędziu do przeglądu

Następnie dodaj następujący kod, aby iterować po danych przykładowych obrazach, dodać metadane i wysłać je do narzędzia Review w jednej partii. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

Obiekt zwrócony z wywołania interfejsu API będzie zawierać unikatowe wartości identyfikatorów dla każdego przekazanego obrazu. Poniższy kod analizuje te identyfikatory, a następnie używa ich do Content Moderator o stan każdego obrazu w partii.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Uzyskiwanie szczegółów przeglądu

Poniższy kod powoduje, że program czeka na dane wejściowe użytkownika. Po dojściu do tego kroku w [](https://contentmoderator.cognitive.microsoft.com) czasie wykonywania możesz samodzielnie przejść do narzędzia Do przeglądu, sprawdzić, czy przykładowy obraz został przekazany, i wejść z nim w interakcję. Aby uzyskać informacje na temat interakcji z przeglądem, zobacz [Przewodnik po recenzjach.](../../review-tool-user-guide/review-moderated-images.md) Po zakończeniu możesz nacisnąć dowolny klawisz, aby kontynuować program i pobrać wyniki procesu przeglądu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

Jeśli w tym scenariuszu został użyty punkt końcowy wywołania zwrotnego, powinien on otrzymać zdarzenie w tym formacie:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

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

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services zasobów, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie wszystkich innych skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano, jak używać biblioteki Content Moderator .NET do wykonywania zadań moderowania. Następnie dowiedz się więcej na temat moderowania obrazów lub innych nośników, czytając przewodnik koncepcyjny.

> [!div class="nextstepaction"]
> [Pojęcia dotyczące moderowania obrazów](../../image-moderation-api.md)