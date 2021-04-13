---
title: 'Szybki start: biblioteka klienta optycznego rozpoznawania znaków dla programu .NET'
description: W tym przewodniku Szybki start rozpoczniesz pracę z biblioteką klienta optycznego rozpoznawania znaków dla programu .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 538b3ce5a268464b9f014dd00b924875824cab3b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327535"
---
<a name="HOLTop"></a>

Użyj biblioteki klienta OCR, aby odczytać z obrazu tekst drukowany i odręczny.

[Dokumentacja referencyjna](/dotnet/api/overview/azure/cognitiveservices/client/computervision)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision)  |  [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)  |  [Przykłady](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Program [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) lub bieżącą wersję programu [.NET Core.](https://dotnet.microsoft.com/download/dotnet-core)
* Po utworzeniu subskrypcji platformy Azure utwórz zasób przetwarzanie obrazów zasobów przetwarzanie obrazów witrynie Azure Portal, aby uzyskać <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Po wdrożeniu kliknij pozycję **Przejdź do zasobu**.
    * Klucz i punkt końcowy z zasobu, który utworzysz, będą potrzebne do połączenia aplikacji z przetwarzanie obrazów usługą. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
    * Możesz użyć warstwy cenowej Bezpłatna ( ), aby wypróbować usługę, i przejść później na warstwę płatną `F0` na użytek produkcji.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-c-application"></a>Tworzenie nowej aplikacji w języku C#

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Za Visual Studio utwórz nową aplikację .NET Core. 

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta 

Po utworzeniu nowego projektu zainstaluj bibliotekę klienta, klikając prawym przyciskiem myszy rozwiązanie projektu na stronie **Eksplorator rozwiązań** wybierając pozycję Zarządzaj **pakietami NuGet.** W menedżerze pakietów, który zostanie otwarty, wybierz **pozycję Przeglądaj,** zaznacz **pole wyboru Uwzględnij wyewidencjonuj** i `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` wyszukaj . Wybierz wersję `7.0.0` , a następnie pozycję **Zainstaluj.** 

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

W oknie konsoli (takim jak cmd, PowerShell lub Bash) użyj polecenia , aby utworzyć nową aplikację `dotnet new` konsolową o nazwie `computer-vision-quickstart` . To polecenie tworzy prosty projekt języka C# "Hello world" z pojedynczym plikiem źródłowym: *Program.cs.*

```console
dotnet new console -n computer-vision-quickstart
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

W katalogu aplikacji zainstaluj bibliotekę przetwarzanie obrazów klienta dla programu .NET za pomocą następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 7.0.0
```

---

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można go znaleźć w witrynie [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)która zawiera przykłady kodu w tym przewodniku Szybki start.

W katalogu projektu otwórz plik *Program.cs* w preferowanym edytorze lub w preferowanym idee.

### <a name="find-the-subscription-key-and-endpoint"></a>Znajdowanie klucza subskrypcji i punktu końcowego

Przejdź do witryny Azure Portal. Jeśli zasób przetwarzanie obrazów utworzony w sekcji **Wymagania** wstępne został pomyślnie wdrożony, kliknij przycisk Przejdź do **zasobu** w obszarze Następne **kroki.** Klucz subskrypcji i punkt końcowy można znaleźć  na stronie klucza i punktu końcowego zasobu w obszarze **zarządzanie zasobami.** 

W klasie **Program** aplikacji utwórz zmienne dla klucza przetwarzanie obrazów i punktu końcowego. Wklej klucz subskrypcji i punkt końcowy do poniższego kodu tam, gdzie jest to wskazane. Punkt przetwarzanie obrazów ma postać `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using_and_vars)]

> [!IMPORTANT]
> Pamiętaj, aby usunąć klucz subskrypcji z kodu, gdy wszystko będzie gotowe, i nigdy nie publikować go publicznie. W środowisku produkcyjnym rozważ użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Na przykład usługa [Azure Key Vault](../../../../key-vault/general/overview.md).

W metodzie aplikacji dodaj wywołania metod używanych `Main` w tym przewodniku Szybki start. Utworzysz je później.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

> [!div class="nextstepaction"]
> [Po skonfigurowaniu klienta,](?success=set-up-client#object-model) [w przypadku których występuje problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu SDK platformy .NET OCR.

|Nazwa|Opis|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Ta klasa jest potrzebna dla wszystkich przetwarzanie obrazów funkcjonalności. Należy utworzyć jego wystąpienia z informacjami o subskrypcji i używać ich do większości operacji na obrazach.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| Ta klasa zawiera dodatkowe metody klasy **ComputerVisionClient.**|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta OCR dla programu .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Odczytywanie tekstu drukowanego i odręcznego](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

W nowej metodzie w klasie **Program** należy utworzyć wystąpienia klienta z punktem końcowym i kluczem subskrypcji. Utwórz obiekt **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** za pomocą klucza subskrypcji i użyj go z punktem końcowym, aby utworzyć **[obiekt ComputerVisionClient.](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Uwierzytelniono klienta,](?success=authenticate-client#read-printed-and-handwritten-text) [na który natłożyłem problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>Odczytywanie tekstu drukowanego i odręcznego

Usługa OCR może odczytywać widoczny tekst na obrazie i konwertować go na strumień znaków. Aby uzyskać więcej informacji na temat rozpoznawania tekstu, zobacz Omówienie optycznego rozpoznawania znaków [(OCR).](../../overview-ocr.md) Kod w tej sekcji używa najnowszej wersji zestawu PRZETWARZANIE OBRAZÓW SDK dla wersji [Read 3.0](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) i definiuje metodę , , która używa obiektu klienta do wykrywania i wyodrębniania tekstu na `BatchReadFileUrl` obrazie.

> [!TIP]
> Można również wyodrębnić tekst z obrazu lokalnego. Zobacz metody [ComputerVisionClient,](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) takie jak **ReadInStreamAsync**. Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) uzyskać scenariusze obejmujące obrazy lokalne.

### <a name="set-up-test-image"></a>Konfigurowanie obrazu testowego

W klasie **Program** zapisz odwołanie do adresu URL obrazu, z którego chcesz wyodrębnić tekst. Ten fragment kodu zawiera przykładowe obrazy dla tekstu drukowanego i odręcznego.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>Wywołanie interfejsu API odczytu

Zdefiniuj nową metodę odczytywania tekstu. Dodaj poniższy kod, który wywołuje metodę **ReadAsync** dla danego obrazu. Zwraca identyfikator operacji i uruchamia asynchroniczny proces odczytu zawartości obrazu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_1)]

### <a name="get-read-results"></a>Uzyskiwanie wyników odczytu

Następnie pobierz identyfikator operacji zwrócony z wywołania **ReadAsync** i użyj go do wykonywania zapytań o wyniki operacji w usłudze. Poniższy kod sprawdza operację do momentu zwrócenia wyników. Następnie wypisuje wyodrębnione dane tekstowe w konsoli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_2)]

### <a name="display-read-results"></a>Wyświetlanie wyników odczytu

Dodaj następujący kod w celu analizowania i wyświetlania pobranych danych tekstowych, a następnie zakończ definicję metody.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_3)]

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

W tym przewodniku Szybki start opisano sposób instalowania biblioteki klienta OCR i używania interfejsu API odczytu. Następnie dowiedz się więcej o funkcjach interfejsu API Read.

> [!div class="nextstepaction"]
>[Wywołanie interfejsu API odczytu](../../Vision-API-How-to-Topics/call-read-api.md)

* [Omówienie OCR](../../overview-ocr.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).
