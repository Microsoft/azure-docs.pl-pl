---
title: 'Szybki Start: Biblioteka klienta analizy obrazów dla języka Python'
description: Rozpocznij pracę z biblioteką klienta analizy obrazów dla języka Python, korzystając z tego przewodnika Szybki Start.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: aae780ad42c39789c0c4448a90a12737074e4176
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073375"
---
<a name="HOLTop"></a>

Biblioteka klienta analizy obrazów służy do analizowania obrazu pod kątem tagów, opisu tekstu, twarzy, treści dla dorosłych itd.

[Dokumentacja](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision)  |  źródłowy biblioteki [Pakiet (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/)  |  [Przykłady](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * Instalacja języka Python powinna obejmować [PIP](https://pip.pypa.io/en/stable/). Aby sprawdzić, czy jest zainstalowany program PIP, należy uruchomić `pip --version` polecenie w wierszu polecenia. Pobierz narzędzie PIP, instalując najnowszą wersję środowiska Python.
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" Utwórz zasób przetwarzanie obrazów "  target="_blank"> utwórz zasób przetwarzanie obrazów </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.

    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który utworzysz, aby połączyć aplikację z usługą przetwarzanie obrazów. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Bibliotekę kliencką można zainstalować za pomocą programu:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

Zainstaluj również bibliotekę Pillow.

```console
pip install pillow
```

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nowy plik Python &mdash; *QuickStart-File.py*, na przykład. Następnie otwórz go w preferowanym edytorze lub środowisku IDE i zaimportuj poniższe biblioteki.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py), która zawiera przykłady kodu w tym przewodniku Szybki Start.

Następnie utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_vars)]

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli pomyślnie wdrożono zasób przetwarzanie obrazów w sekcji **wymagania wstępne** , kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki**. Klucz i punkt końcowy można znaleźć na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**. 
>
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Na przykład [Magazyn kluczy platformy Azure](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Konfiguruję klienta,](?success=set-up-client#object-model) który [uruchomił problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=set-up-client)

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu SDK języka Python.

|Nazwa|Opis|
|---|---|
|[ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)| Ta klasa bezpośrednio obsługuje wszystkie operacje na obrazach, takie jak analiza obrazu, wykrywanie tekstu i generowanie miniatury.|
| [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) | Ta klasa jest wymagana dla wszystkich funkcji przetwarzanie obrazów. Tworzysz wystąpienie z informacjami o subskrypcji i używasz ich do tworzenia wystąpień innych klas. Implementuje **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| To Wyliczenie definiuje różne typy analizy obrazów, które można wykonać przy użyciu standardowej operacji analizy. Należy określić zestaw wartości **VisualFeatureTypes** w zależności od potrzeb. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań przy użyciu biblioteki klienta analizy obrazów dla języka Python:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analizowanie obrazu](#analyze-an-image)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz wystąpienie klienta z punktem końcowym i kluczem. Utwórz obiekt [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

> [!div class="nextstepaction"]
> [Klient otrzymał uwierzytelnienie](?success=authenticate-client#analyze-an-image) [problemu](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analizowanie obrazu

Użyj obiektu klienckiego, aby przeanalizować funkcje wizualizacji obrazu zdalnego. Najpierw Zapisz odwołanie do adresu URL obrazu, który chcesz przeanalizować.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

> [!TIP]
> Możesz również analizować obraz lokalny. Zobacz metody [ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin) , takie jak **analyze_image_in_stream**. Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) , aby poznać scenariusze dotyczące obrazów lokalnych.

### <a name="get-image-description"></a>Pobierz opis obrazu

Poniższy kod pobiera listę wygenerowanych napisów dla obrazu. Aby uzyskać więcej informacji, zobacz [Opis obrazów](../../concept-describing-images.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>Pobierz kategorię obrazu

Poniższy kod pobiera wykrytą kategorię obrazu. Aby uzyskać więcej informacji, zobacz [kategoryzowanie obrazów](../../concept-categorizing-images.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>Pobierz Tagi obrazu

Poniższy kod pobiera zestaw wykrytych tagów z obrazu. Aby uzyskać więcej informacji, zobacz [Tagi zawartości](../../concept-tagging-images.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>Wykrywanie obiektów

Poniższy kod wykrywa typowe obiekty w obrazie i drukuje je do konsoli programu. Aby uzyskać więcej informacji, zobacz [wykrywanie obiektów](../../concept-object-detection.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]        

### <a name="detect-brands"></a>Wykrywanie marek

Poniższy kod wykrywa marki i logo firmy w obrazie i drukuje je do konsoli programu. Aby uzyskać więcej informacji, zobacz [wykrywanie marki](../../concept-brand-detection.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>Wykrywanie twarzy

Poniższy kod zwraca wykryte twarze na obrazie ze współrzędnymi prostokątów i wybierz atrybuty twarzy. Aby uzyskać więcej informacji, zobacz [wykrywanie czołowe](../../concept-detecting-faces.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Wykrywanie zawartości dla dorosłych, erotycznej lub gorii

Poniższy kod drukuje wykryte obecność treści dla dorosłych w obrazie. Aby uzyskać więcej informacji, zobacz [erotycznej, gorii Content](../../concept-detecting-adult-content.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Pobierz schemat kolorów obrazu

Poniższy kod drukuje wykryte atrybuty koloru w obrazie, takie jak kolory dominujące i kolor akcentu. Zobacz [schematy kolorów](../../concept-detecting-color-schemes.md) , aby uzyskać więcej szczegółów.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Pobieranie zawartości specyficznej dla domeny

Analiza obrazu może korzystać z wyspecjalizowanego modelu do dalszej analizy obrazów. Aby uzyskać więcej informacji, zobacz [zawartość specyficzną dla domeny](../../concept-detecting-domain-content.md) . 

Poniższy kod analizuje dane dotyczące wykrytych osobistości w obrazie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

Poniższy kod analizuje dane dotyczące wykrytych punktów orientacyjnych w obrazie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Pobierz typ obrazu

Poniższy kod drukuje informacje o typie obrazu, niezależnie od tego, &mdash; czy jest to obiekt clipart czy rysowanie liniowe.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

> [!div class="nextstepaction"]
> [Po przeanalizowaniu obrazu](?success=analyze-image#run-the-application) [pojawił się problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=analyze-image)



## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `python` polecenia w pliku szybkiego startu.

```console
python quickstart-file.py
```

> [!div class="nextstepaction"]
> [Uruchomiono aplikację, której](?success=run-the-application#clean-up-resources) dotyczy [problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=run-the-application)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Wyczyszczono zasoby](?success=clean-up-resources#next-steps) [, w których wystąpił problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób instalowania biblioteki klienta analizy obrazów i wykonywania podstawowych wywołań analizy obrazu. Następnie Dowiedz się więcej na temat funkcji Analizuj interfejsy API.

> [!div class="nextstepaction"]
>[Wywoływanie interfejsu API analizy](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Analiza obrazu — Omówienie](../../overview-image-analysis.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
