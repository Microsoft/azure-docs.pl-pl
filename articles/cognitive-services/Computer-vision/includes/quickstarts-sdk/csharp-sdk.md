---
title: 'Szybki Start: Biblioteka kliencka rozpoznawania znaków optycznych dla platformy .NET'
description: W tym przewodniku szybki start Rozpocznij pracę z biblioteką klienta rozpoznawania znaków optycznych dla platformy .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 410ced99b1c5053c084921edf4d9bbde1a9443c4
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073317"
---
<a name="HOLTop"></a>

Użyj biblioteki klienta OCR do odczytywania wydrukowanych i odręcznych tekstu z obrazu.

[Dokumentacja](/dotnet/api/overview/azure/cognitiveservices/client/computervision)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)  |  [Przykłady](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* [Środowisko IDE programu Visual Studio](https://visualstudio.microsoft.com/vs/) lub bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" Utwórz zasób przetwarzanie obrazów "  target="_blank"> utwórz zasób przetwarzanie obrazów </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który utworzysz, aby połączyć aplikację z usługą przetwarzanie obrazów. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-c-application"></a>Utwórz nową aplikację w języku C#

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Za pomocą programu Visual Studio Utwórz nową aplikację platformy .NET Core. 

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką 

Po utworzeniu nowego projektu Zainstaluj bibliotekę kliencką, klikając prawym przyciskiem myszy rozwiązanie projektu w **Eksplorator rozwiązań** i wybierając pozycję **Zarządzaj pakietami NuGet**. W Menedżerze pakietów, który otwiera Wybierz pozycję **Przeglądaj**, zaznacz pozycję **Uwzględnij wersję wstępną** i Wyszukaj `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` . Wybierz wersję `6.0.0-preview.1` , a następnie **Zainstaluj**. 

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `computer-vision-quickstart` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: *program. cs*.

```console
dotnet new console -n computer-vision-quickstart
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

W katalogu aplikacji zainstaluj przetwarzanie obrazówą bibliotekę kliencką dla platformy .NET przy użyciu następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

---

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs), która zawiera przykłady kodu w tym przewodniku Szybki Start.

W katalogu projektu Otwórz plik *program. cs* w preferowanym edytorze lub środowisku IDE.

### <a name="find-the-subscription-key-and-endpoint"></a>Znajdowanie klucza subskrypcji i punktu końcowego

Przejdź do witryny Azure Portal. Jeśli pomyślnie wdrożono zasób przetwarzanie obrazów w sekcji **wymagania wstępne** , kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki**. Klucz subskrypcji i punkt końcowy można znaleźć na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**. 

W klasie **programu** aplikacji Utwórz zmienne dla klucza subskrypcji przetwarzanie obrazów i punktu końcowego. Wklej swój klucz subskrypcji i punkt końcowy do poniższego kodu, gdzie zostało wskazane. Twój punkt końcowy przetwarzanie obrazów ma formularz `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using_and_vars)]

> [!IMPORTANT]
> Pamiętaj, aby usunąć klucz subskrypcji z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Na przykład [Magazyn kluczy platformy Azure](../../../../key-vault/general/overview.md).

W `Main` metodzie aplikacji Dodaj wywołania metod używanych w tym przewodniku Szybki Start. Zostaną one utworzone później.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

> [!div class="nextstepaction"]
> [Konfiguruję klienta,](?success=set-up-client#object-model) który [uruchomił problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu SDK programu .NET dla OCR.

|Nazwa|Opis|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Ta klasa jest wymagana dla wszystkich funkcji przetwarzanie obrazów. Utwórz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do wykonania większości operacji na obrazie.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| Ta klasa zawiera dodatkowe metody dla **ComputerVisionClient**.|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań przy użyciu biblioteki klienckiej OCR dla platformy .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Odczytaj tekst drukowany i odręczny](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

W nowej metodzie w klasie **program** Utwórz wystąpienie klienta z punktem końcowym i kluczem subskrypcji. Utwórz obiekt **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** z kluczem subskrypcji i użyj go w punkcie końcowym, aby utworzyć obiekt **[ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)** .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Klient otrzymał uwierzytelnienie](?success=authenticate-client#read-printed-and-handwritten-text) [problemu](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>Odczytaj tekst drukowany i odręczny

Usługa OCR może odczytać widoczny tekst w obrazie i przekonwertować go na strumień znaków. Aby uzyskać więcej informacji na temat rozpoznawania tekstu, zobacz Omówienie [optycznego rozpoznawania znaków (OCR)](../../overview-ocr.md) . Kod w tej sekcji używa najnowszej [wersji zestawu SDK przetwarzanie obrazów dla odczytu 3,0](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/6.0.0-preview.1) i definiuje metodę, `BatchReadFileUrl` która używa obiektu klienta do wykrywania i wyodrębniania tekstu w obrazie.

> [!TIP]
> Możesz również wyodrębnić tekst z obrazu lokalnego. Zobacz metody [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) , takie jak **ReadInStreamAsync**. Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) , aby poznać scenariusze dotyczące obrazów lokalnych.

### <a name="set-up-test-image"></a>Konfigurowanie obrazu testu

W klasie **programu** Zapisz odwołanie do adresu URL obrazu, z którego ma zostać wyodrębniony tekst. Ten fragment kodu zawiera przykładowe obrazy dla wydrukowanych i odręcznych tekstu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>Wywoływanie interfejsu API odczytu

Zdefiniuj nową metodę odczytywania tekstu. Dodaj poniższy kod, który wywołuje metodę **ReadAsync** dla danego obrazu. Zwraca identyfikator operacji i uruchamia proces asynchroniczny w celu odczytania zawartości obrazu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_1)]

### <a name="get-read-results"></a>Pobierz wyniki odczytu

Następnie Pobierz identyfikator operacji zwrócony z wywołania **ReadAsync** i użyj go do wysyłania zapytań do usługi w celu uzyskania wyników operacji. Poniższy kod sprawdza operację do momentu zwrócenia wyników. Następnie program drukuje wyodrębnione dane tekstowe do konsoli programu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_2)]

### <a name="display-read-results"></a>Wyświetl wyniki odczytu

Dodaj następujący kod, aby przeanalizować i wyświetlić pobrane dane tekstowe, a następnie Zakończ definicję metody.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_3)]

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

W tym przewodniku szybki start przedstawiono sposób instalowania biblioteki klienta OCR i używania interfejsu API odczytu. Następnie Dowiedz się więcej o funkcjach odczytu interfejsu API.

> [!div class="nextstepaction"]
>[Wywoływanie interfejsu API odczytu](../../Vision-API-How-to-Topics/call-read-api.md)

* [Omówienie OCR](../../overview-ocr.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).
