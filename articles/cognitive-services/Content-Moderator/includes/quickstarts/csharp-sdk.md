---
title: Content Moderator przewodniku szybki start dotyczącej biblioteki klienta .NET
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start dowiesz się, jak rozpocząć pracę z biblioteką kliencką Content Moderator platformy Azure dla platformy .NET. Twórz oprogramowanie do filtrowania zawartości w aplikacji, aby zachować zgodność z przepisami lub zachować zamierzone środowisko dla użytkowników.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.author: pafarley
ms.custom: devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: 793dc6d210751d0605ab756cd1c72b1dc5dd308f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92925002"
---
Rozpocznij pracę z biblioteką kliencką Content Moderator platformy Azure dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet NuGet i wypróbować przykładowy kod dla podstawowych zadań. 

Content Moderator to usługa AI, która umożliwia obsługę zawartości potencjalnie obraźliwej, ryzykownej lub niepożądanej. Użyj usługi moderowania zawartości opartej na AI do skanowania tekstu, obrazów i filmów wideo i automatycznego stosowania flag zawartości. Następnie Zintegruj swoją aplikację za pomocą narzędzia do przeglądu, środowiska moderatora online dla zespołu recenzentów ludzkich. Twórz oprogramowanie do filtrowania zawartości w aplikacji, aby zachować zgodność z przepisami lub zachować zamierzone środowisko dla użytkowników.

Użyj biblioteki klienta Content Moderator dla platformy .NET, aby:

* [Tekst umiarkowany](#moderate-text)
* [Obrazy umiarkowane](#moderate-images)
* [Utwórz recenzję](#create-a-review)

[Dokumentacja](/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)  |  [Przykłady](../../samples-dotnet.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* [Środowisko IDE programu Visual Studio](https://visualstudio.microsoft.com/vs/) lub bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Gdy masz subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" Utwórz zasób Content moderator "  target="_blank"> utwórz zasób Content Moderator <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Zaczekaj na jego wdrożenie i kliknij przycisk **Przejdź do zasobu** .
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć swoją aplikację z Content Moderator. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-c-application"></a>Utwórz nową aplikację w języku C#

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Za pomocą programu Visual Studio Utwórz nową aplikację platformy .NET Core. 

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką 

Po utworzeniu nowego projektu Zainstaluj bibliotekę kliencką, klikając prawym przyciskiem myszy rozwiązanie projektu w **Eksplorator rozwiązań** i wybierając pozycję **Zarządzaj pakietami NuGet** . W Menedżerze pakietów, który otwiera Wybierz pozycję **Przeglądaj** , zaznacz pozycję **Uwzględnij wersję wstępną** i Wyszukaj `Microsoft.Azure.CognitiveServices.ContentModerator` . Wybierz wersję `2.0.0` , a następnie **Zainstaluj** . 

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `content-moderator-quickstart` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: *program.cs* .

```console
dotnet new console -n content-moderator-quickstart
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

W katalogu aplikacji zainstaluj Content Moderatorą bibliotekę kliencką dla platformy .NET przy użyciu następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

---

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs), która zawiera przykłady kodu w tym przewodniku Szybki Start.

W katalogu projektu Otwórz plik *program.cs* w preferowanym edytorze lub w środowisku IDE. Dodaj następujące instrukcje `using`:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

W klasie **program** Utwórz zmienne dla klucza i punktu końcowego zasobu.

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli pomyślnie wdrożono zasób Content Moderator w sekcji **wymagania wstępne** , kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki** . Klucz i punkt końcowy można znaleźć na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami** . 
>
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [zabezpieczeń](../../../cognitive-services-security.md) Cognitive Services.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]


W `main()` metodzie aplikacji Dodaj wywołania metod używanych w tym przewodniku Szybki Start. Zostaną one utworzone później.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]


## <a name="object-model"></a>Model obiektów

Poniższe klasy obsługują niektóre główne funkcje biblioteki klienta Content Moderator .NET.

|Nazwa|Opis|
|---|---|
|[ContentModeratorClient](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Ta klasa jest wymagana dla wszystkich funkcji Content Moderator. Tworzysz wystąpienie z informacjami o subskrypcji i używasz ich do tworzenia wystąpień innych klas.|
|[ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Ta klasa udostępnia funkcje do analizowania obrazów na potrzeby zawartości dla dorosłych, danych osobowych lub osób ludzkich.|
|[Moderowanie](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Ta klasa udostępnia funkcje do analizowania tekstu dla języka, niewulgarności, błędów i informacji osobistych.|
|[Przeglądy](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Ta klasa udostępnia funkcje interfejsów API przeglądu, w tym metody tworzenia zadań, niestandardowych przepływów pracy i przeglądów ludzkich.|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań przy użyciu biblioteki klienta Content Moderator dla platformy .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Tekst umiarkowany](#moderate-text)
* [Obrazy umiarkowane](#moderate-images)
* [Utwórz recenzję](#create-a-review)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

W nowej metodzie Utwórz wystąpienie obiektów klienta z punktem końcowym i kluczem.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_auth)]

## <a name="moderate-text"></a>Tekst umiarkowany

Poniższy kod używa klienta Content Moderator, aby analizować treść tekstu i drukować wyniki w konsoli programu. W katalogu głównym klasy **programu** Zdefiniuj pliki wejściowe i wyjściowe:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

Następnie w katalogu głównym projektu Dodaj plik *TextFile.txt* . Dodaj własny tekst do tego pliku lub użyj następującego przykładowego tekstu:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```


Następnie zdefiniuj metodę moderowania tekstu w klasie **programu** :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Obrazy umiarkowane

Poniższy kod używa klienta Content Moderator wraz z obiektem [ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) , aby analizować obrazy zdalne pod kątem zawartości dla dorosłych i erotycznej.

> [!NOTE]
> Możesz również analizować zawartość lokalnego obrazu. Zapoznaj się z [dokumentacją](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) dotyczącą metod i operacji, które działają z obrazami lokalnymi.

### <a name="get-sample-images"></a>Pobierz przykładowe obrazy

Zdefiniuj pliki wejściowe i wyjściowe w katalogu głównym klasy **programu** :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Następnie utwórz plik wejściowy, *ImageFiles.txt* , w katalogu głównym projektu. W tym pliku należy dodać adresy URL obrazów do analizowania &mdash; jednego adresu URL w każdym wierszu. Możesz użyć następujących przykładowych obrazów:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Zdefiniuj klasę pomocnika

Dodaj następującą definicję klasy w ramach klasy **program** . Ta wewnętrzna Klasa będzie obsługiwać wyniki moderowania obrazu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Zdefiniuj metodę moderowania obrazu

Poniższa metoda wykonuje iterację przez adresy URL obrazu w pliku tekstowym, tworzy wystąpienie **EvaluationData** i analizuje obraz pod kątem zawartości dla dorosłych/erotycznej, tekstu i ludzkich twarzy. Następnie dodaje wystąpienie **EvaluationData** końcowego do listy i zapisuje pełną listę zwracanych danych w konsoli.

#### <a name="iterate-through-images"></a>Wykonaj iterację obrazów

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Analizowanie zawartości

Aby uzyskać więcej informacji na temat atrybutów obrazu, które Content Moderator ekrany dla programu, zobacz Przewodnik dotyczący [pojęć związanych z moderowaniem obrazu](../../image-moderation-api.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Zapisz wyniki moderowania do pliku

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Utwórz recenzję

Możesz użyć biblioteki klienta programu Content Moderator .NET, aby pokazywać zawartość do [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com) , dzięki czemu mogą je przeglądać moderatorzy. Aby dowiedzieć się więcej na temat narzędzia do przeglądu, zobacz [Przewodnik dotyczący koncepcyjnego narzędzia do przeglądu](../../review-tool-user-guide/human-in-the-loop.md).

Metoda w tej sekcji używa klasy [Reviews](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) w celu utworzenia przeglądu, pobrania jego identyfikatora i sprawdzenia jego szczegółów po odebraniu danych przez użytkownika za pośrednictwem portalu sieci Web narzędzia do przeglądu. Wszystkie te informacje są rejestrowane w wyjściowym pliku tekstowym. 

### <a name="get-sample-images"></a>Pobierz przykładowe obrazy

Zadeklaruj następującą tablicę w katalogu głównym klasy **programu** . Ta zmienna odwołuje się do przykładowego obrazu, który zostanie użyty do utworzenia przeglądu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Pobierz poświadczenia przeglądu

Zaloguj się do [Narzędzia Recenzja](https://contentmoderator.cognitive.microsoft.com) i Pobierz swoją nazwę zespołu. Następnie przypisz go do odpowiedniej zmiennej w klasie **program** . Opcjonalnie można skonfigurować punkt końcowy wywołania zwrotnego, aby otrzymywać aktualizacje dotyczące działania przeglądu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Zdefiniuj klasę pomocnika

Dodaj następującą definicję klasy w klasie **programu** . Ta klasa zostanie użyta do reprezentowania pojedynczego wystąpienia przeglądu przesłanego do narzędzia do przeglądu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Zdefiniuj metodę pomocnika

Dodaj następującą metodę do klasy **Program** . Ta metoda spowoduje zapisanie wyników zapytań przeglądu do wyjściowego pliku tekstowego.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Zdefiniuj metodę tworzenia przeglądu

Teraz można przystąpić do definiowania metody, która będzie obsługiwać tworzenie przeglądu i wykonywanie zapytań. Dodaj nową metodę, Utwórz **Przegląd** i Zdefiniuj następujące zmienne lokalne.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Publikuj przeglądy w narzędziu do przeglądu

Następnie Dodaj następujący kod, aby wykonać iterację w podanych przykładowych obrazach, dodać metadane i wysłać je do narzędzia przeglądu w pojedynczej partii. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

Obiekt zwrócony z wywołania interfejsu API będzie zawierać unikatowe wartości identyfikatora dla każdego przekazanego obrazu. Poniższy kod analizuje te identyfikatory, a następnie używa ich do Content Moderator zapytań dla stanu każdego obrazu w partii.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Pobierz szczegóły przeglądu

Poniższy kod powoduje, że program czeka na dane wejściowe użytkownika. Po wykonaniu tego kroku w środowisku uruchomieniowym można ponownie przejść do [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com) , sprawdzić, czy przykładowy obraz został przekazany i korzystać z niego. Aby uzyskać informacje na temat sposobu korzystania z przeglądu, zobacz [Przewodnik](../../review-tool-user-guide/review-moderated-images.md)dotyczący przeglądu. Gdy skończysz, możesz nacisnąć dowolny klawisz, aby kontynuować program i pobrać wyniki procesu przeglądu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

Jeśli w tym scenariuszu użyto punktu końcowego wywołania zwrotnego, powinien on otrzymać zdarzenie w tym formacie:

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

Uruchom aplikację, klikając przycisk **Debuguj** w górnej części okna środowiska IDE.

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

Uruchom aplikację z katalogu aplikacji za pomocą `dotnet run` polecenia.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób korzystania z biblioteki Content Moderator .NET do wykonywania zadań moderowania. Następnie Dowiedz się więcej o moderowaniu obrazów lub innych nośnikach, odczytując Przewodnik koncepcyjny.

> [!div class="nextstepaction"]
> [Pojęcia związane z moderowaniem obrazu](../../image-moderation-api.md)

* [Czym jest usługa Azure Content Moderator?](../../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs).