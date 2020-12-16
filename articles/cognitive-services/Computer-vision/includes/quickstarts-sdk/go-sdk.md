---
title: 'Szybki Start: przetwarzanie obrazów bibliotekę kliencką dla języka go'
titleSuffix: Azure Cognitive Services
description: Zacznij korzystać z biblioteki klienta przetwarzanie obrazów dla języka go w tym przewodniku Szybki Start.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: df171b74014179c36466a2683a8d0d7c0c3b5e90
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560886"
---
<a name="HOLTop"></a>

Użyj biblioteki klienta przetwarzanie obrazów, aby:

* Analizuj obraz pod kątem tagów, opisu tekstu, twarzy, treści dla dorosłych itd.
* Odczytywanie wydrukowanych i odręcznych tekstu za pomocą interfejsu API odczytu.

[Dokumentacja](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision)  |  źródłowy biblioteki [Pakiet](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Najnowsza wersja języka [go](https://golang.org/dl/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" Utwórz zasób przetwarzanie obrazów "  target="_blank"> utwórz zasób przetwarzanie obrazów <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który utworzysz, aby połączyć aplikację z usługą przetwarzanie obrazów. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.
* [Utwórz zmienne środowiskowe](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) dla adresu URL klucza i punktu końcowego `COMPUTER_VISION_SUBSCRIPTION_KEY` , `COMPUTER_VISION_ENDPOINT` odpowiednio nazwane i.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-go-project-directory"></a>Utwórz katalog projektu języka go

W oknie konsoli (cmd, PowerShell, Terminal, bash) Utwórz nowy obszar roboczy dla projektu go, nazwanego `my-app` i przejdź do niego.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Obszar roboczy będzie zawierać trzy foldery:

* **src** — ten katalog będzie zawierać kod źródłowy i pakiety. Wszystkie pakiety zainstalowane z `go get` poleceniem zostaną umieszczone w tym katalogu.
* **pkg** — ten katalog będzie zawierać skompilowane obiekty pakietu języka go. Wszystkie te pliki mają `.a` rozszerzenie.
* **bin** — ten katalog będzie zawierać binarne pliki wykonywalne, które są tworzone podczas uruchamiania programu `go install` .

> [!TIP]
> Aby dowiedzieć się więcej na temat struktury obszaru roboczego go, zobacz [dokumentację języka](https://golang.org/doc/code.html#Workspaces)go. Ten przewodnik zawiera informacje dotyczące ustawiania `$GOPATH` i `$GOROOT` .

### <a name="install-the-client-library-for-go"></a>Zainstaluj bibliotekę kliencką dla języka go

Następnie zainstaluj bibliotekę kliencką dla języka go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

a jeśli używasz programu dep, w ramach repozytorium uruchom:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Tworzenie aplikacji języka go 

Następnie utwórz plik w katalogu **src** o nazwie `sample-app.go` :

```bash
cd src
touch sample-app.go
```

Otwórz `sample-app.go` w preferowanym środowisku IDE lub edytorze tekstu. Następnie Dodaj nazwę pakietu i zaimportuj następujące biblioteki:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Ponadto Zadeklaruj kontekst w katalogu głównym skryptu. Ten obiekt będzie potrzebny do wykonania większości przetwarzanie obrazów wywołań funkcji:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Następnie dowiesz się, jak dodać kod, aby wykonać różne operacje przetwarzanie obrazów.

> [!div class="nextstepaction"]
> [Konfiguruję klienta,](?success=set-up-client#object-model) który [uruchomił problem](https://www.research.net/r/7QYZKHL?issue=set-up-client)

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu przetwarzanie obrazów go SDK.

|Nazwa|Opis|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Ta klasa jest wymagana dla wszystkich funkcji przetwarzanie obrazów, takich jak analiza obrazów i odczytywanie tekstu. Utwórz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do wykonania większości operacji na obrazie.|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Ten typ zawiera wyniki wywołania funkcji **AnalyzeImage** . Dla każdej funkcji specyficznej dla kategorii są podobne typy.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Ten typ zawiera wyniki operacji odczytu partii. |
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Ten typ definiuje różne rodzaje analizy obrazów, które można wykonać przy użyciu standardowej operacji analizy. Należy określić zestaw wartości VisualFeatureTypes w zależności od potrzeb. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienta przetwarzanie obrazów dla języka go:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analizowanie obrazu](#analyze-an-image)
* [Odczytaj tekst drukowany i odręczny](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

> [!NOTE]
> W tym kroku przyjęto założenie, że [utworzono zmienne środowiskowe](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) dla klucza przetwarzanie obrazów i punktu końcowego o nazwie `COMPUTER_VISION_SUBSCRIPTION_KEY` i `COMPUTER_VISION_ENDPOINT` odpowiednio.

Utwórz `main` funkcję i Dodaj do niej następujący kod, aby utworzyć wystąpienie klienta z punktem końcowym i kluczem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Klient otrzymał uwierzytelnienie](?success=authenticate-client#analyze-an-image) [problemu](https://www.research.net/r/7QYZKHL?issue=authenticate-client)

## <a name="analyze-an-image"></a>Analizowanie obrazu

Poniższy kod używa obiektu klienta do analizowania obrazu zdalnego i drukowania wyników w konsoli programu. Możesz uzyskać opis tekstowy, kategoryzację, listę tagów, wykrytych obiektów, wykryte marki, wykrytych twarzy, flag zawartości dla dorosłych, kolorów głównych i typu obrazu.

### <a name="set-up-test-image"></a>Konfigurowanie obrazu testu

Najpierw Zapisz odwołanie do adresu URL obrazu, który chcesz przeanalizować. Umieść to wewnątrz `main` funkcji.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!TIP]
> Możesz również analizować obraz lokalny. Zobacz metody [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) , takie jak **DescribeImageInStream**. Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) , aby poznać scenariusze dotyczące obrazów lokalnych.

### <a name="specify-visual-features"></a>Określ funkcje wizualne

Poniższe wywołania funkcji wyodrębniają różne funkcje wizualne z przykładowego obrazu. Te funkcje zostaną zdefiniowane w poniższych sekcjach.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Pobierz opis obrazu

Poniższa funkcja pobiera listę wygenerowanych napisów dla obrazu. Aby uzyskać więcej informacji na temat opisu obrazu, zobacz [opisywanie obrazów](../../concept-describing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Pobierz kategorię obrazu

Poniższa funkcja pobiera wykrytą kategorię obrazu. Aby uzyskać więcej informacji, zobacz [kategoryzowanie obrazów](../../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Pobierz Tagi obrazu

Poniższa funkcja pobiera zestaw wykrytych tagów z obrazu. Aby uzyskać więcej informacji, zobacz [Tagi zawartości](../../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Wykrywanie obiektów

Poniższa funkcja wykrywa typowe obiekty w obrazie i drukuje je do konsoli programu. Aby uzyskać więcej informacji, zobacz [wykrywanie obiektów](../../concept-object-detection.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Wykrywanie marek

Poniższy kod wykrywa marki i logo firmy w obrazie i drukuje je do konsoli programu. Aby uzyskać więcej informacji, [wykrycie marki](../../concept-brand-detection.md).

Najpierw Zadeklaruj odwołanie do nowego obrazu w `main` funkcji.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

Poniższy kod definiuje funkcję wykrywania marki.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Wykrywanie twarzy

Poniższa funkcja zwraca wykryte twarze na obrazie ze współrzędnymi prostokątów i niektórych atrybutów twarzy. Aby uzyskać więcej informacji, zobacz [wykrywanie czołowe](../../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Wykrywanie zawartości dla dorosłych, erotycznej lub gorii

Poniższa funkcja drukuje wykrytą obecność treści dla dorosłych w obrazie. Aby uzyskać więcej informacji, zobacz [erotycznej i gorii Content](../../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Pobierz schemat kolorów obrazu

Poniższa funkcja drukuje wykryte atrybuty koloru w obrazie, takie jak kolory dominujące i kolor akcentu. Aby uzyskać więcej informacji, zobacz [schematy kolorów](../../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Pobieranie zawartości specyficznej dla domeny

Przetwarzanie obrazów mogą korzystać z wyspecjalizowanych modeli w celu przeprowadzenia dalszej analizy obrazów. Aby uzyskać więcej informacji, zobacz [zawartość specyficzna dla domeny](../../concept-detecting-domain-content.md). 

Poniższy kod analizuje dane dotyczące wykrytych osobistości w obrazie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

Poniższy kod analizuje dane dotyczące wykrytych punktów orientacyjnych w obrazie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Pobierz typ obrazu

Poniższa funkcja drukuje informacje o typie obrazu, niezależnie od tego, &mdash; czy jest to obiekt clipart czy rysowanie liniowe.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

> [!div class="nextstepaction"]
> [Po przeanalizowaniu obrazu](?success=analyze-image#read-printed-and-handwritten-text) [pojawił się problem](https://www.research.net/r/7QYZKHL?issue=analyze-image)

## <a name="read-printed-and-handwritten-text"></a>Odczytaj tekst drukowany i odręczny

Przetwarzanie obrazów może odczytać widoczny tekst w obrazie i przekonwertować go na strumień znaków. Kod w tej sekcji definiuje funkcję, `RecognizeTextReadAPIRemoteImage` która używa obiektu klienta do wykrywania i wyodrębniania wydrukowanych lub odręcznych tekstu w obrazie.

Dodaj przykładowe odwołanie do obrazu i wywołanie funkcji w `main` funkcji.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> Możesz również wyodrębnić tekst z obrazu lokalnego. Zobacz metody [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) , takie jak **BatchReadFileInStream**. Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) , aby poznać scenariusze dotyczące obrazów lokalnych.

### <a name="call-the-read-api"></a>Wywoływanie interfejsu API odczytu

Zdefiniuj nową funkcję do odczytu tekstu, `RecognizeTextReadAPIRemoteImage` . Dodaj poniższy kod, który wywołuje metodę **BatchReadFile** dla danego obrazu. Ta metoda zwraca identyfikator operacji i uruchamia proces asynchroniczny w celu odczytania zawartości obrazu.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Pobierz wyniki odczytu

Następnie Pobierz identyfikator operacji zwrócony z wywołania **BatchReadFile** i użyj go z metodą **GetReadOperationResult** w celu wysyłania zapytań do usługi w celu uzyskania wyników operacji. Poniższy kod sprawdza operacje w okresach jednego sekundy do momentu zwrócenia wyników. Następnie program drukuje wyodrębnione dane tekstowe do konsoli programu.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Wyświetl wyniki odczytu

Dodaj następujący kod, aby przeanalizować i wyświetlić pobrane dane tekstowe, a następnie Zakończ definicję funkcji.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [Odczytaję tekst](?success=read-printed-handwritten-text#run-the-application) [, w którym wystąpił problem](https://www.research.net/r/7QYZKHL?issue=read-printed-handwritten-text)

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację z katalogu aplikacji za pomocą `go run` polecenia.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [Uruchomiono aplikację, której](?success=run-the-application#clean-up-resources) dotyczy [problem](https://www.research.net/r/7QYZKHL?issue=run-the-application)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Wyczyszczono zasoby](?success=clean-up-resources#next-steps) [, w których wystąpił problem](https://www.research.net/r/7QYZKHL?issue=clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Odwołanie interfejs API przetwarzania obrazów (go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)


* [Czym jest przetwarzanie obrazów?](../../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).