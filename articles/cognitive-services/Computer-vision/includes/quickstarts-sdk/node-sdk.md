---
title: 'Szybki Start: przetwarzanie obrazów bibliotekę kliencką dla Node.js'
description: Wprowadzenie do przetwarzanie obrazówj biblioteki klienckiej dla Node.js z tym przewodnikiem Szybki Start
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: 6753b9e50ec8304c85f4079cf917339b6beac42f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750488"
---
<a name="HOLTop"></a>

Użyj biblioteki klienta przetwarzanie obrazów, aby:

* Analizuj obraz pod kątem tagów, opisu tekstu, twarzy, treści dla dorosłych itd.
* Odczytywanie wydrukowanych i odręcznych tekstu za pomocą interfejsu API odczytu.

[Dokumentacja](/javascript/api/@azure/cognitiveservices-computervision/)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision)  |  źródłowy biblioteki [Pakiet (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision)  |  [Przykłady](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Bieżąca wersja [Node.js](https://nodejs.org/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" Utwórz zasób przetwarzanie obrazów "  target="_blank"> utwórz zasób przetwarzanie obrazów <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
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

Utwórz nowy plik, *index.js* i otwórz go w edytorze tekstu. Dodaj następujące instrukcje importu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js), która zawiera przykłady kodu w tym przewodniku Szybki Start.

Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli pomyślnie wdrożono zasób przetwarzanie obrazów w sekcji **wymagania wstępne** , kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki**. Klucz i punkt końcowy można znaleźć na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**. 
>
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [zabezpieczeń](../../../cognitive-services-security.md) Cognitive Services.

> [!div class="nextstepaction"]
> [Konfiguruję klienta,](?success=set-up-client#object-model) który [uruchomił problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client)

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu SDK przetwarzanie obrazów Node.js.

|Nazwa|Opis|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | Ta klasa jest wymagana dla wszystkich funkcji przetwarzanie obrazów. Utwórz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do wykonania większości operacji na obrazie.|
|[VisualFeatureTypes](/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes)| To Wyliczenie definiuje różne typy analizy obrazów, które można wykonać przy użyciu standardowej operacji analizy. Należy określić zestaw wartości **VisualFeatureTypes** w zależności od potrzeb. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienta przetwarzanie obrazów dla Node.js:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analizowanie obrazu](#analyze-an-image)
* [Odczytaj tekst drukowany i odręczny](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta


Utwórz wystąpienie klienta z punktem końcowym i kluczem. Utwórz obiekt [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials) z kluczem i punktem końcowym, a następnie użyj go do utworzenia obiektu [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

Następnie zdefiniuj funkcję `computerVision` i Zadeklaruj serię asynchroniczną z funkcją podstawową i funkcją wywołania zwrotnego. Kod szybkiego startu zostanie dodany do funkcji podstawowej i zostanie wywołana `computerVision` w dolnej części skryptu. Pozostała część kodu w tym przewodniku Szybki Start jest wbudowana w `computerVision` funkcję.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> [Klient otrzymał uwierzytelnienie](?success=authenticate-client#analyze-an-image) [problemu](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analizowanie obrazu

Kod w tej sekcji analizuje obrazy zdalne, aby wyodrębnić różne funkcje wizualne. Te operacje można wykonać jako część metody **analyzeImage** obiektu klienta lub można wywołać je za pomocą poszczególnych metod. Szczegółowe informacje znajdują się w [dokumentacji referencyjnej](/javascript/api/@azure/cognitiveservices-computervision/) .

> [!NOTE]
> Możesz również analizować obraz lokalny. Zobacz metody [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) , takie jak **analyzeImageInStream**. Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) , aby poznać scenariusze dotyczące obrazów lokalnych.

### <a name="get-image-description"></a>Pobierz opis obrazu

Poniższy kod pobiera listę wygenerowanych napisów dla obrazu. Aby uzyskać więcej informacji, zobacz [Opis obrazów](../../concept-describing-images.md) .

Najpierw Zdefiniuj adres URL obrazu do przeanalizowania:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Następnie Dodaj następujący kod, aby uzyskać opis obrazu i wydrukować go w konsoli programu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Pobierz kategorię obrazu

Poniższy kod pobiera wykrytą kategorię obrazu. Aby uzyskać więcej informacji, zobacz [kategoryzowanie obrazów](../../concept-categorizing-images.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Zdefiniuj funkcję pomocnika `formatCategories` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Pobierz Tagi obrazu

Poniższy kod pobiera zestaw wykrytych tagów z obrazu. Aby uzyskać więcej informacji, zobacz [Tagi zawartości](../../concept-tagging-images.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Zdefiniuj funkcję pomocnika `formatTags` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Wykrywanie obiektów

Poniższy kod wykrywa typowe obiekty w obrazie i drukuje je do konsoli programu. Aby uzyskać więcej informacji, zobacz [wykrywanie obiektów](../../concept-object-detection.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Zdefiniuj funkcję pomocnika, `formatRectObjects` aby zwracała współrzędne Top, Left, Bottom i Right wraz z szerokością i wysokością.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Wykrywanie marek

Poniższy kod wykrywa marki i logo firmy w obrazie i drukuje je do konsoli programu. Aby uzyskać więcej informacji, zobacz [wykrywanie marki](../../concept-brand-detection.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Wykrywanie twarzy

Poniższy kod zwraca wykryte twarze na obrazie ze współrzędnymi prostokątów i wybierz atrybuty twarzy. Aby uzyskać więcej informacji, zobacz [wykrywanie czołowe](../../concept-detecting-faces.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Zdefiniuj funkcję pomocnika `formatRectFaces` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Wykrywanie zawartości dla dorosłych, erotycznej lub gorii

Poniższy kod drukuje wykryte obecność treści dla dorosłych w obrazie. Aby uzyskać więcej informacji, zobacz [erotycznej, gorii Content](../../concept-detecting-adult-content.md) .

Zdefiniuj adres URL obrazu do użycia:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

Następnie Dodaj następujący kod, aby wykryć zawartość dla dorosłych i wydrukować wyniki w konsoli programu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Pobierz schemat kolorów obrazu

Poniższy kod drukuje wykryte atrybuty koloru w obrazie, takie jak kolory dominujące i kolor akcentu. Zobacz [schematy kolorów](../../concept-detecting-color-schemes.md) , aby uzyskać więcej szczegółów.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Zdefiniuj funkcję pomocnika, `printColorScheme` Aby drukować szczegóły schematu kolorów w konsoli programu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Pobieranie zawartości specyficznej dla domeny

Przetwarzanie obrazów może korzystać z wyspecjalizowanego modelu do dalszej analizy obrazów. Aby uzyskać więcej informacji, zobacz [zawartość specyficzną dla domeny](../../concept-detecting-domain-content.md) .

Najpierw Zdefiniuj adres URL obrazu do przeanalizowania:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

Poniższy kod analizuje dane dotyczące wykrytych punktów orientacyjnych w obrazie.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Zdefiniuj funkcję pomocnika, `formatRectDomain` Aby przeanalizować dane lokalizacji dotyczące wykrytych punktów orientacyjnych.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Pobierz typ obrazu

Poniższy kod drukuje informacje o typie obrazu, niezależnie od tego, &mdash; czy jest to obiekt clipart czy rysowanie liniowe.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

Zdefiniuj funkcję pomocnika `describeType` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

> [!div class="nextstepaction"]
> [Po przeanalizowaniu obrazu](?success=analyze-image#read-printed-and-handwritten-text) [pojawił się problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=analyze-image)

## <a name="read-printed-and-handwritten-text"></a>Odczytaj tekst drukowany i odręczny

Przetwarzanie obrazów może wyodrębnić widoczny tekst w obrazie i przekonwertować go na strumień znaków. Ten przykład używa operacji odczytu.

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

> [!div class="nextstepaction"]
>[Odwołanie interfejs API przetwarzania obrazów (Node.js)](/javascript/api/@azure/cognitiveservices-computervision/)


* [Czym jest przetwarzanie obrazów?](../../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).
