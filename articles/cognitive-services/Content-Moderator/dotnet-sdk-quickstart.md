---
title: 'Szybki start: biblioteka klienta moderatora zawartości dla platformy .NET'
titleSuffix: Azure Cognitive Services
description: Wprowadzenie do biblioteki klienta moderatora zawartości dla platformy .NET za pomocą tego przewodnika Szybki start.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: e93cfd6d44e6a59b4b3d94a49bef766fadf4381c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770627"
---
# <a name="quickstart-content-moderator-client-library-for-net"></a>Szybki start: biblioteka klienta moderatora zawartości dla platformy .NET

Wprowadzenie do biblioteki klienta moderatora zawartości dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Content Moderator to usługa kognitywna, która sprawdza zawartość tekstową, obrazową i wideo pod kątem materiałów potencjalnie obraźliwych, ryzykownych lub w inny sposób niepożądanych. W przypadku znalezienia takich treści usługa stosuje odpowiednie etykiety (flagi) do zawartości. Aplikacja może następnie obsługiwać oflagowane treści, aby zachować zgodność z przepisami lub zachować zamierzone środowisko dla użytkowników.

Użyj biblioteki klienta Moderatora zawartości dla platformy .NET, aby:

* [Moderowanie tekstu](#moderate-text)
* [Umiarkowane obrazy](#moderate-images)
* [Tworzenie recenzji](#create-a-review)

[Przykłady](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | referencyjnej[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [Samples](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Bieżąca wersja programu [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-content-moderator-azure-resource"></a>Tworzenie zasobu platformy Azure moderatora zawartości

Usługi Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla moderatora zawartości przy użyciu [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Pobierz [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez siedem dni za darmo. Po zarejestrowaniu się będzie on dostępny w [witrynie sieci Web platformy Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Wyświetlanie zasobu w [witrynie Azure portal](https://portal.azure.com/)

Po otrzymasz klucz z subskrypcji próbnej lub zasobu, [utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza i adresu URL punktu końcowego, odpowiednio `CONTENT_MODERATOR_SUBSCRIPTION_KEY` i `CONTENT_MODERATOR_ENDPOINT`, odpowiednio.

### <a name="create-a-new-c-application"></a>Tworzenie nowej aplikacji języka C#

Utwórz nową aplikację .NET Core w preferowanym edytorze tekstu lub ide. 

W oknie konsoli (takim jak cmd, PowerShell `dotnet new` lub Bash) użyj polecenia, `content-moderator-quickstart`aby utworzyć nową aplikację konsoli o nazwie . To polecenie tworzy prosty projekt "Hello World" C# z jednym plikiem źródłowym: *Program.cs*.

```console
dotnet new console -n content-moderator-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można utworzyć za pomocą:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać żadnych ostrzeżeń ani błędów. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

W katalogu projektu otwórz plik *Program.cs* w preferowanym edytorze lub w programie IDE. Dodaj następujące instrukcje `using`:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

W **klasie Program** utwórz zmienne dla lokalizacji punktu końcowego zasobu i klucz jako zmienne środowiskowe.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> Jeśli utworzono zmienne środowiskowe po uruchomieniu aplikacji, należy zamknąć i ponownie otworzyć edytor, IDE lub powłoki z systemem go, aby uzyskać dostęp do zmiennych.

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

W katalogu aplikacji zainstaluj bibliotekę klienta Content Moderator dla platformy .NET za pomocą następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Jeśli używasz środowiska IDE programu Visual Studio, biblioteka klienta jest dostępna jako pakiet NuGet do pobrania.

## <a name="object-model"></a>Model obiektu

Następujące klasy obsługują niektóre z głównych funkcji content moderatora .NET SDK.

|Nazwa|Opis|
|---|---|
|[ContentModeratorClient (łak.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Ta klasa jest potrzebna dla wszystkich funkcji moderatora zawartości. Tworzenie wystąpienia z informacjami o subskrypcji i używać go do tworzenia wystąpień innych klas.|
|[ImageModeracja](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Ta klasa zapewnia funkcje do analizowania obrazów dla zawartości dla dorosłych, informacji osobistych lub ludzkich twarzy.|
|[TextModeration ( TextModeration )](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Ta klasa zapewnia funkcje do analizowania tekstu dla języka, wulgaryzmów, błędów i informacji osobistych.|
|[Przeglądy](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Ta klasa udostępnia funkcje interfejsu API przeglądu, w tym metody tworzenia zadań, niestandardowych przepływów pracy i przeglądów ludzkich.|

## <a name="code-examples"></a>Przykłady kodu


Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta programu .NET moderatora zawartości:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Moderowanie tekstu](#moderate-text)
* [Umiarkowane obrazy](#moderate-images)
* [Tworzenie recenzji](#create-a-review)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

W nowej metodzie wystąpienia obiektów klienta z punktu końcowego i klucza. Nie potrzebujesz innego klienta dla każdego scenariusza, ale może pomóc utrzymać porządek kodu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>Moderowanie tekstu

Poniższy kod używa klienta content moderatora do analizowania treści tekstu i drukowania wyników na konsoli. W katalogu głównym klasy **Program** zdefiniuj pliki wejściowe i wyjściowe:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

Następnie w katalogu głównym projektu i dodać plik *TextFile.txt.* Dodaj własny tekst do tego pliku lub użyj następującego przykładowego tekstu:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Dodaj następujące wywołanie `Main` metody do metody:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

Następnie zdefiniuj metodę moderowania tekstu gdzieś w klasie **Program:**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Umiarkowane obrazy

Poniższy kod używa klienta moderatora zawartości, wraz z [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) obiektu, do analizowania obrazów zdalnych dla dorosłych i zawartości rasistowskie.

> [!NOTE]
> Można również analizować zawartość obrazu lokalnego. Zobacz [dokumentację referencyjną](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) dla metod i operacji, które działają z obrazów lokalnych.

### <a name="get-sample-images"></a>Pobieranie przykładowych obrazów

Zdefiniuj pliki wejściowe i wyjściowe:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Następnie utwórz plik wejściowy *ImageFiles.txt*w katalogu głównym projektu. W tym pliku dodasz adresy URL&mdash;obrazów, aby przeanalizować jeden adres URL w każdym wierszu. Można użyć następujących przykładowych obrazów:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

Przekaż pliki wejściowe i wyjściowe do `Main` następującego wywołania metody w metodzie. Tę metodę zdefiniujesz w późniejszym kroku.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

### <a name="define-helper-class"></a>Definiowanie klasy pomocnika

Dodaj następującą definicję klasy w klasie **Program.** Ta klasa wewnętrzna będzie obsługiwać wyniki moderowania obrazu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Definiowanie metody moderowania obrazu

Poniższa metoda iteruje za pośrednictwem adresów URL obrazu w pliku tekstowym, tworzy **wystąpienie EvaluationData** i analizuje obraz dla dorosłych/rasistowskich zawartości, tekstu i ludzkich twarzy. Następnie dodaje końcowe **wystąpienie EvaluationData** do listy i zapisuje pełną listę zwróconych danych do konsoli.

#### <a name="iterate-through-image-urls"></a>Iteruje za pomocą adresów URL obrazów

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Analizowanie zawartości

Aby uzyskać więcej informacji na temat atrybutów obrazu, dla których ekrany moderatora zawartości, zobacz przewodnik [pojęć moderowania obrazu.](./image-moderation-api.md)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Zapisuj wyniki moderowania do pliku

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Tworzenie recenzji

Za pomocą zestawu Content Moderator .NET SDK można przesyłać zawartość do [narzędzia Recenzja,](https://contentmoderator.cognitive.microsoft.com) aby moderatorzy mogli je przejrzeć. Aby dowiedzieć się więcej o narzędziu Recenzja, zobacz [przewodnik koncepcyjny narzędzia Recenzji](./review-tool-user-guide/human-in-the-loop.md).

Metoda w tej sekcji używa [Reviews](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) klasy do tworzenia przeglądu, pobrać jego identyfikator i sprawdzić jego szczegóły po otrzymaniu danych wejściowych człowieka za pośrednictwem portalu sieci web narzędzia przeglądu. Rejestruje wszystkie te informacje w wyjściowym pliku tekstowym. Wywołaj metodę `Main` z metody:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>Pobieranie przykładowych obrazów

Zadeklaruj następującą tablicę w katalogu głównym klasy **Program.** Ta zmienna odwołuje się do przykładowego obrazu do utworzenia recenzji.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Uzyskaj poświadczenia recenzji

Zaloguj się do [narzędzia Recenzja](https://contentmoderator.cognitive.microsoft.com) i odzyskaj nazwę swojego zespołu. Następnie przypisz go do odpowiedniej zmiennej w **klasie Program.** Opcjonalnie można skonfigurować punkt końcowy wywołania zwrotnego, aby otrzymywać aktualizacje dotyczące działania przeglądu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Definiowanie klasy pomocnika

Dodaj następującą definicję klasy w klasie **Program.** Ta klasa będzie używana do reprezentowania pojedynczego wystąpienia przeglądu, który jest przesyłany do narzędzia Recenzja.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Definiowanie metody pomocnika

Dodaj następującą metodę do klasy **Program**. Ta metoda zapisze wyniki zapytań przeglądu do wyjściowego pliku tekstowego.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Definiowanie metody tworzenia recenzji

Teraz możesz przystąpić do definiowania metody, która będzie obsługiwać tworzenie recenzji i wykonywanie zapytań. Dodaj nową metodę **CreateReviews**i zdefiniuj następujące zmienne lokalne.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Publikowanie recenzji w narzędziu Recenzja

Następnie dodaj następujący kod do iteracji za pośrednictwem podanych przykładowych obrazów, dodaj metadane i wyślij je do narzędzia Recenzja w jednej partii. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

Obiekt zwrócony z wywołania interfejsu API będzie zawierać unikatowe wartości identyfikatora dla każdego przesłanego obrazu. Poniższy kod analizuje te identyfikatory, a następnie używa ich do wykonywania zapytań o moderatora zawartości pod kątem stanu każdego obrazu w partii.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Uzyskaj szczegółowe informacje o recenzji

Poniższy kod powoduje, że program czekać na dane wejściowe użytkownika. Po dojściu do tego kroku w czasie wykonywania, można przejść do [narzędzia przeglądu](https://contentmoderator.cognitive.microsoft.com) samodzielnie, sprawdź, czy przykładowy obraz został przekazany i interakcji z nim. Aby uzyskać informacje na temat interakcji z recenzją, zobacz [przewodnik Ekwiastyki](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). Po zakończeniu można nacisnąć dowolny klawisz, aby kontynuować program i pobrać wyniki procesu przeglądu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

Jeśli użyto punktu końcowego wywołania zwrotnego w tym scenariuszu, powinien on otrzymać zdarzenie w tym formacie:

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

Uruchom aplikację z katalogu aplikacji `dotnet run` za pomocą polecenia.

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak wykonywać zadania moderowania za pomocą biblioteki .NET moderatora zawartości. Następnie dowiedz się więcej o moderowaniu obrazów lub innych mediów, czytając przewodnik koncepcyjny.

> [!div class="nextstepaction"]
> [Pojęcia dotyczące moderowania obrazu](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Czym jest usługa Azure Content Moderator?](./overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs).
