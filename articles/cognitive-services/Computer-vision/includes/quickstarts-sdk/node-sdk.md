---
title: 'Szybki Start: Biblioteka kliencka rozpoznawania znaków optycznych dla Node.js'
description: Rozpocznij pracę z biblioteką klienta rozpoznawania znaków optycznych dla Node.js z tym przewodnikiem Szybki Start
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: cb4679152740b73d6bb9cf7288fcaa811b6d6141
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073706"
---
<a name="HOLTop"></a>

Biblioteka klienta rozpoznawania znaków optycznych umożliwia odczytywanie wydrukowanych i odręcznych tekstu za pomocą interfejsu API odczytu.

[Dokumentacja](/javascript/api/@azure/cognitiveservices-computervision/)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision)  |  źródłowy biblioteki [Pakiet (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision)  |  [Przykłady](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Bieżąca wersja [Node.js](https://nodejs.org/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" Utwórz zasób przetwarzanie obrazów "  target="_blank"> utwórz zasób przetwarzanie obrazów </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który utworzysz, aby połączyć aplikację z usługą przetwarzanie obrazów. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir myapp && cd myapp
```

Uruchom `npm init` polecenie, aby utworzyć aplikację Node z `package.json` plikiem.

```console
npm init
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Zainstaluj `ms-rest-azure` pakiet i `@azure/cognitiveservices-computervision` npm:

```console
npm install @azure/cognitiveservices-computervision
```

Zainstaluj również moduł Async:

```console
npm install async
```

`package.json`Plik aplikacji zostanie zaktualizowany z zależnościami.

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js), która zawiera przykłady kodu w tym przewodniku Szybki Start.

Utwórz nowy plik, *index.js* i otwórz go w edytorze tekstu.

### <a name="find-the-subscription-key-and-endpoint"></a>Znajdowanie klucza subskrypcji i punktu końcowego

Przejdź do witryny Azure Portal. Jeśli pomyślnie wdrożono zasób przetwarzanie obrazów w sekcji **wymagania wstępne** , kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki**. Klucz subskrypcji i punkt końcowy można znaleźć na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**. 

Utwórz zmienne dla klucza subskrypcji przetwarzanie obrazów i punktu końcowego. Wklej swój klucz subskrypcji i punkt końcowy do poniższego kodu, gdzie zostało wskazane. Twój punkt końcowy przetwarzanie obrazów ma formularz `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Pamiętaj, aby usunąć klucz subskrypcji z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Na przykład [Magazyn kluczy platformy Azure](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Konfiguruję klienta,](?success=set-up-client#object-model) który [uruchomił problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client)

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu SDK OCR Node.js.

|Nazwa|Opis|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | Ta klasa jest wymagana dla wszystkich funkcji przetwarzanie obrazów. Utwórz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do wykonania większości operacji na obrazie.|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań z biblioteką kliencką OCR dla Node.js:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Odczytaj tekst drukowany i odręczny](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta


Utwórz wystąpienie klienta z punktem końcowym i kluczem. Utwórz obiekt [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials) z kluczem i punktem końcowym, a następnie użyj go do utworzenia obiektu [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

Następnie zdefiniuj funkcję `computerVision` i Zadeklaruj serię asynchroniczną z funkcją podstawową i funkcją wywołania zwrotnego. Kod szybkiego startu zostanie dodany do funkcji podstawowej i zostanie wywołana `computerVision` w dolnej części skryptu. Pozostała część kodu w tym przewodniku Szybki Start jest wbudowana w `computerVision` funkcję.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> [Klient otrzymał uwierzytelnienie](?success=authenticate-client#read-printed-and-handwritten-text) [problemu](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>Odczytaj tekst drukowany i odręczny

Usługa OCR może wyodrębnić widoczny tekst w obrazie i przekonwertować go na strumień znaków. Ten przykład używa operacji odczytu.

### <a name="set-up-test-images"></a>Konfigurowanie obrazów testowych

Zapisz odwołanie do adresu URL obrazów, z których ma zostać wyodrębniony tekst.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

> [!NOTE]
> Możesz również odczytać tekst z lokalnego obrazu. Zobacz metody [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) , takie jak **readInStream**. Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) , aby poznać scenariusze dotyczące obrazów lokalnych.

### <a name="call-the-read-api"></a>Wywoływanie interfejsu API odczytu

Zdefiniuj następujące pola w funkcji, aby określić wartości odczytywania stanu wywołań.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_statuses)]

Dodaj poniższy kod, który wywołuje `readTextFromURL` funkcję dla danego obrazu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Zdefiniuj `readTextFromURL` funkcję. Wywołuje metodę **Read** w obiekcie Client, która zwraca identyfikator operacji i uruchamia proces asynchroniczny w celu odczytania zawartości obrazu. Następnie używa identyfikatora operacji do sprawdzenia stanu operacji do momentu zwrócenia wyników. Zwraca wyodrębnione wyniki.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Następnie zdefiniuj funkcję pomocnika `printRecText` , która drukuje wyniki operacji odczytu w konsoli programu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

> [!div class="nextstepaction"]
> [Odczytaję tekst](?success=read-printed-handwritten-text#run-the-application) [, w którym wystąpił problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node` polecenia w pliku szybkiego startu.

```console
node index.js
```

> [!div class="nextstepaction"]
> [Uruchomiono aplikację, której](?success=run-the-application#clean-up-resources) dotyczy [problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=run-the-application)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Wyczyszczono zasoby](?success=clean-up-resources#next-steps) [, w których wystąpił problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób instalowania biblioteki klienta OCR i używania interfejsu API odczytu. Następnie Dowiedz się więcej o funkcjach odczytu interfejsu API.

> [!div class="nextstepaction"]
>[Wywoływanie interfejsu API odczytu](../../Vision-API-How-to-Topics/call-read-api.md)

* [Omówienie OCR](../../overview-ocr.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).
