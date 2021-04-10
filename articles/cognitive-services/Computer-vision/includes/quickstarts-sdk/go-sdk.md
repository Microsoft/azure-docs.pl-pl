---
title: 'Szybki Start: Biblioteka kliencka rozpoznawania znaków optycznych dla języka go'
titleSuffix: Azure Cognitive Services
description: Rozpocznij pracę z biblioteką klienta rozpoznawania znaków optycznych dla języka go w tym przewodniku Szybki Start.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 0ae059054c68da662e05342525987f6925184906
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073632"
---
<a name="HOLTop"></a>

Użyj biblioteki klienta OCR do odczytywania wydrukowanych i odręcznych tekstu z obrazów.

[Dokumentacja](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision)  |  źródłowy biblioteki [Pakiet](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Najnowsza wersja języka [go](https://golang.org/dl/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" Utwórz zasób przetwarzanie obrazów "  target="_blank"> utwórz zasób przetwarzanie obrazów </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który utworzysz, aby połączyć aplikację z usługą przetwarzanie obrazów. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

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

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go), która zawiera przykłady kodu w tym przewodniku Szybki Start.

Otwórz `sample-app.go` w preferowanym środowisku IDE lub edytorze tekstu.

Zadeklaruj kontekst w katalogu głównym skryptu. Ten obiekt będzie potrzebny do wykonania większości przetwarzanie obrazów wywołań funkcji.

### <a name="find-the-subscription-key-and-endpoint"></a>Znajdowanie klucza subskrypcji i punktu końcowego

Przejdź do witryny Azure Portal. Jeśli pomyślnie wdrożono zasób przetwarzanie obrazów w sekcji **wymagania wstępne** , kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki**. Klucz subskrypcji i punkt końcowy można znaleźć na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**. 

Utwórz zmienne dla klucza subskrypcji przetwarzanie obrazów i punktu końcowego. Wklej swój klucz subskrypcji i punkt końcowy do poniższego kodu, gdzie zostało wskazane. Twój punkt końcowy przetwarzanie obrazów ma formularz `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Pamiętaj, aby usunąć klucz subskrypcji z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Na przykład [Magazyn kluczy platformy Azure](../../../../key-vault/general/overview.md).

Następnie dowiesz się, jak dodać kod, aby wykonać różne operacje OCR.

> [!div class="nextstepaction"]
> [Konfiguruję klienta,](?success=set-up-client#object-model) który [uruchomił problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client)

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu SDK OCR.

|Nazwa|Opis|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Ta klasa jest wymagana dla wszystkich funkcji przetwarzanie obrazów, takich jak analiza obrazów i odczytywanie tekstu. Utwórz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do wykonania większości operacji na obrazie.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Ten typ zawiera wyniki operacji odczytu partii. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań przy użyciu biblioteki klienckiej OCR dla języka go:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Odczytaj tekst drukowany i odręczny](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

> [!NOTE]
> W tym kroku przyjęto założenie, że [utworzono zmienne środowiskowe](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) dla klucza przetwarzanie obrazów i punktu końcowego o nazwie `COMPUTER_VISION_SUBSCRIPTION_KEY` i `COMPUTER_VISION_ENDPOINT` odpowiednio.

Utwórz `main` funkcję i Dodaj do niej następujący kod, aby utworzyć wystąpienie klienta z punktem końcowym i kluczem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Klient otrzymał uwierzytelnienie](?success=authenticate-client#read-printed-and-handwritten-text) [problemu](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>Odczytaj tekst drukowany i odręczny

Usługa OCR może odczytać widoczny tekst w obrazie i przekonwertować go na strumień znaków. Kod w tej sekcji definiuje funkcję, `RecognizeTextReadAPIRemoteImage` która używa obiektu klienta do wykrywania i wyodrębniania wydrukowanych lub odręcznych tekstu w obrazie.

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
> [Odczytaję tekst](?success=read-printed-handwritten-text#run-the-application) [, w którym wystąpił problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację z katalogu aplikacji za pomocą `go run` polecenia.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [Uruchomiono aplikację, której](?success=run-the-application#clean-up-resources) dotyczy [problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Wyczyszczono zasoby](?success=clean-up-resources#next-steps) [, w których wystąpił problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób instalowania biblioteki klienta OCR i używania interfejsu API odczytu. Następnie Dowiedz się więcej o funkcjach odczytu interfejsu API.

> [!div class="nextstepaction"]
>[Wywoływanie interfejsu API odczytu](../../Vision-API-How-to-Topics/call-read-api.md)

* [Omówienie OCR](../../overview-ocr.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).
