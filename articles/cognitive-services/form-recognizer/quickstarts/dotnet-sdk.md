---
title: 'Szybki start: biblioteka klienta aparatu rozpoznawania formularzy dla platformy .NET'
description: Wprowadzenie do biblioteki klienta aparatu rozpoznawania formularzy dla platformy .NET do szkolenia, wyodrębniania, analizowania i uzyskania danych wyjściowych strukturalnych za pomocą tego przewodnika Szybki start.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 8d05f5ad07f9b3affba9442d6caa6e63162b813c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399421"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Szybki start: biblioteka klienta aparatu rozpoznawania formularzy dla platformy .NET

Wprowadzenie do biblioteki klienta aparatu rozpoznawania formularzy dla platformy .NET. Rozpoznawanie formularzy to usługa cognitive, która używa technologii uczenia maszynowego do identyfikowania i wyodrębniania par kluczy/wartości i danych tabeli z dokumentów formularza. Następnie wyświetla ustrukturyzowane dane, które obejmują relacje w oryginalnym pliku. Wykonaj następujące kroki, aby zainstalować pakiet zestawu SDK i wypróbować przykładowy kod dla podstawowych zadań.

Użyj biblioteki klienta aparatu rozpoznawania formularzy dla platformy .NET, aby:

* [Szkolenie niestandardowego modelu aparatu rozpoznawania formularzy](#train-a-custom-model)
* [Pobierz listę wyodrębnionych kluczy](#get-a-list-of-extracted-keys)
* [Analizowanie formularzy za pomocą modelu niestandardowego](#analyze-forms-with-a-custom-model)
* [Wyświetlanie listy modeli niestandardowych](#get-a-list-of-custom-models)
* [Usuwanie modelu niestandardowego](#delete-a-custom-model)

[Dokumentacja](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | [referencyjna Pakiet kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/FormRecognizer) | [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję.](https://azure.microsoft.com/free/)
* Obiekt blob usługi Azure Storage zawierający zestaw danych szkoleniowych. Zobacz [Tworzenie zestawu danych szkoleniowych dla modelu niestandardowego,](../build-training-data-set.md) aby uzyskać porady i opcje dotyczące tworzenia danych szkoleniowych. W tym przewodniku Szybki start można użyć plików w folderze **Pociąg** [przykładowego zestawu danych](https://go.microsoft.com/fwlink/?linkid=2090451).
* Bieżąca wersja programu [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-form-recognizer-azure-resource"></a>Tworzenie zasobu platformy Azure aparatu rozpoznawania formularzy

[!INCLUDE [create resource](../includes/create-resource.md)]

Po otrzymasz klucz i punkt końcowy, [należy utworzyć zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza i punktu końcowego, nazwanych `FORM_RECOGNIZER_KEY` i `FORM_RECOGNIZER_ENDPOINT`, odpowiednio.

### <a name="create-a-new-c-application"></a>Tworzenie nowej aplikacji języka C#

W oknie konsoli (takim jak cmd, PowerShell `dotnet new` lub Bash) użyj polecenia, `formrecognizer-quickstart`aby utworzyć nową aplikację konsoli o nazwie . To polecenie tworzy prosty projekt "Hello World" C# z jednym plikiem źródłowym: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Następnie skompiluj aplikację za pomocą:

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

W katalogu projektu otwórz plik _Program.cs_ w preferowanym edytorze lub w programie IDE. Dodaj następujące instrukcje `using`:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_using)]

Następnie dodaj następujący kod w głównej **metody** aplikacji. Zdefiniujesz to zadanie asynchroniczne później.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

W katalogu aplikacji zainstaluj bibliotekę klienta rozpoznawania formularzy dla platformy .NET za pomocą następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Jeśli używasz środowiska IDE programu Visual Studio, biblioteka klienta jest dostępna jako pakiet NuGet do pobrania.

## <a name="object-model"></a>Model obiektu

Następujące klasy obsługują główne funkcje SDK aparatu rozpoznawania formularzy.

|Nazwa|Opis|
|---|---|
|[KształtRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Ta klasa jest potrzebna dla wszystkich funkcji rozpoznawania formularzy. Tworzenie wystąpienia z informacjami o subskrypcji i używać go do tworzenia wystąpień innych klas.|
|[Prośba o szkolenie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| Ta klasa służy do szkolenia niestandardowego modelu rozpoznawania formularzy przy użyciu własnych danych wejściowych szkolenia. |
|[TrainResult (Wynik pociągu)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Ta klasa zapewnia wyniki operacji train modelu niestandardowego, w tym identyfikator modelu, który można następnie użyć do analizowania formularzy. |
|[AnalizaWyskuj](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Ta klasa zapewnia wyniki operacji analizy modelu niestandardowego. Zawiera listę **extractedPage** wystąpień. |
|[Wyodrębniona strona](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Ta klasa reprezentuje wszystkie dane wyodrębnione z jednego dokumentu formularza.|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta rozpoznawania formularzy dla platformy .NET:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Szkolenie niestandardowego modelu aparatu rozpoznawania formularzy](#train-a-custom-model)
* [Pobierz listę wyodrębnionych kluczy](#get-a-list-of-extracted-keys)
* [Analizowanie formularzy za pomocą modelu niestandardowego](#analyze-forms-with-a-custom-model)
* [Wyświetlanie listy modeli niestandardowych](#get-a-list-of-custom-models)
* [Usuwanie modelu niestandardowego](#delete-a-custom-model)

## <a name="define-variables"></a>Definiowanie zmiennych

Przed zdefiniowaniem jakichkolwiek metod należy dodać następujące definicje zmiennych do górnej części klasy **Program.** Musisz samodzielnie wypełnić niektóre zmienne. 

* Aby pobrać adres URL sygnatury dostępu Współdzielonego dla danych szkoleniowych, otwórz Eksploratora magazynu Microsoft Azure Azure, kliknij prawym przyciskiem myszy kontener i wybierz pozycję **Pobierz podpis dostępu współdzielonego**. Upewnij się, że uprawnienia **Do odczytu** i **listy** są zaznaczone, a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość w sekcji **ADRES URL.** Powinien mieć formularz: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Jeśli potrzebujesz przykładowego formularza do analizy, możesz użyć jednego z plików w folderze **Test** [przykładowego zestawu danych](https://go.microsoft.com/fwlink/?linkid=2090451). W tym przewodniku używane są tylko formularze PDF.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

Poniżej `Main` metody zdefiniuj zadanie, do którego odwołuje się program `Main`. W tym miejscu można uwierzytelnić obiekt klienta przy użyciu zmiennych subskrypcji, które zostały zdefiniowane powyżej. Później zdefiniujesz inne metody.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

## <a name="train-a-custom-model"></a>Trenowanie modelu niestandardowego

Poniższa metoda używa obiektu klienta rozpoznawania formularzy do wytrenowania nowego modelu rozpoznawania w dokumentach przechowywanych w kontenerze obiektów blob platformy Azure. Używa metody pomocnika do wyświetlania informacji o nowo przeszkolonym modelu (reprezentowane przez [ModelResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview) obiektu) i zwraca identyfikator modelu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

Następująca metoda pomocnika wyświetla informacje o modelu rozpoznawania formularzy.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

## <a name="get-a-list-of-extracted-keys"></a>Pobierz listę wyodrębnionych kluczy

Po zakończeniu szkolenia model niestandardowy zachowa listę kluczy wyodrębnionych z dokumentów szkoleniowych. Oczekuje, że przyszłe dokumenty formularza będą zawierać te klucze i wyodrębni ich odpowiednie wartości w operacji Analizowanie. Aby pobrać listę wyodrębnionych kluczy i wydrukować ją na konsoli, należy użyć następującej metody. Jest to dobry sposób, aby sprawdzić, czy proces szkolenia był skuteczny.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getkeys)]

## <a name="analyze-forms-with-a-custom-model"></a>Analizowanie formularzy za pomocą modelu niestandardowego

Ta metoda używa klienta rozpoznawania formularzy i identyfikatora modelu do analizowania dokumentu formularza PDF i wyodrębniania danych klucza/wartości. Używa metody pomocnika do wyświetlania wyników (reprezentowane przez [AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview) obiektu).

> [!NOTE]
> Poniższa metoda analizuje formularz PDF. Podobne metody analizowania formularzy JPEG i PNG można znaleźć w pełnym przykładowym kodzie w [usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

Następująca metoda pomocnika wyświetla informacje o operacji analizowanie.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

## <a name="get-a-list-of-custom-models"></a>Wyświetlanie listy modeli niestandardowych

Można zwrócić listę wszystkich wyszkolonych modeli, które należą do Twojego konta i można pobrać informacje o tym, kiedy zostały utworzone. Lista modeli jest reprezentowana przez [ModelsResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview) obiektu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="delete-a-custom-model"></a>Usuwanie modelu niestandardowego

Jeśli chcesz usunąć model niestandardowy z konta, użyj następującej metody:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację, `dotnet run` wywołując polecenie z katalogu aplikacji.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Ponadto jeśli przeszkolono model niestandardowy, który chcesz usunąć z konta, uruchom metodę w [obszarze Usuń model niestandardowy](#delete-a-custom-model).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto biblioteki klienta programu .NET aparatu rozpoznawania formularzy do wytrenowania modelu niestandardowego i analizowania formularzy. Następnie zapoznaj się ze wskazówkami, jak utworzyć lepszy zestaw danych szkoleniowych i stworzyć dokładniejsze modele.

> [!div class="nextstepaction"]
> [Tworzenie zestawu danych szkoleniowych](../build-training-data-set.md)

* [Co to jest rozpoznawanie formularzy?](../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).
