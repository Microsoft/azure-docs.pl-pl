---
title: Układy — aparat rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: Poznaj koncepcje związane z analizą układu przy użyciu interfejsu API rozpoznawania i limitów.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 73bef21f430bde1c6c2c95d7c3f685cccbbd9179
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467021"
---
# <a name="form-recognizer-layout-service"></a>Usługa układu aparatu rozpoznawania formularzy

Aparat rozpoznawania formularzy platformy Azure może wyodrębnić tekst, tabele, znaczniki wyboru i informacje o strukturze z dokumentów przy użyciu usługi układu. Interfejs API układu umożliwia klientom korzystanie z dokumentów w różnych formatach i zwracanie uporządkowanych reprezentacji danych. Łączy nasze zaawansowane funkcje [rozpoznawania znaków optycznych (OCR)](../computer-vision/concept-recognizing-text.md) z modelami uczenia głębokiego, aby wyodrębnić tekst, tabele, znaczniki wyboru i strukturę dokumentu. 

## <a name="what-does-the-layout-service-do"></a>Do czego służy usługa układu?

Interfejs API układu wyodrębnia tekst, tabele, znaczniki wyboru i informacje o strukturze z dokumentów o wyjątkowej dokładności i zwraca uporządkowaną, strukturalną odpowiedź JSON. Dokumenty mogą mieć różne formaty i jakość, w tym obrazy przechwycone przez telefon, zeskanowane dokumenty i cyfrowe pliki PDF. Interfejs API układu dokładnie wyodrębni strukturalne dane wyjściowe ze wszystkich tych dokumentów.

![Przykład układu](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Czas to wypróbować

Aby wypróbować usługę układu aparatu rozpoznawania formularzy, przejdź do narzędzia przykładowego interfejsu użytkownika w trybie online:

> [!div class="nextstepaction"]
> [Narzędzie testowe OCR (FOTT)](https://fott-preview.azurewebsites.net)

Potrzebna jest subskrypcja platformy Azure ([Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)) i punkt końcowy [zasobu aparatu rozpoznawania formularzy](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oraz klucz służący do wypróbowania interfejsu API układu aparatu rozpoznawania formularzy. 

![Przykładowy zrzut ekranu interfejsu użytkownika; tekst, tabele i znaczniki zaznaczenia dokumentu są analizowane](./media/analyze-layout.png)

### <a name="input-requirements"></a>Wymagania wejściowe 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>Operacja analizy układu

Najpierw Wywołaj operację [analizy układu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync) . Analiza układu pobiera dokument (plik obrazu, TIFF lub PDF) jako dane wejściowe i wyodrębnia tekst, tabele, znaczniki wyboru i strukturę dokumentu. Wywołanie zwraca pole nagłówka odpowiedzi o nazwie `Operation-Location` . `Operation-Location`Wartość jest adresem URL, który zawiera identyfikator wynik do użycia w następnym kroku.

|Nagłówek odpowiedzi| Adres URL wyniku |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

### <a name="natural-reading-order-output-latin-only"></a>Dane wyjściowe z porządkiem naturalnym odczytywania (tylko łacińskie)

Można określić kolejność, w której wiersze tekstu są wyprowadzane za pomocą `readingOrder` parametru zapytania. Użyj, `natural` Aby uzyskać bardziej przyjazny dla człowieka wynik z kolejnością odczytywania, jak pokazano w poniższym przykładzie. Ta funkcja jest obsługiwana tylko w przypadku języków łacińskich.

:::image type="content" source="../Computer-vision/Images/ocr-reading-order-example.png" alt-text="Przykład kolejności odczytywania układu" lightbox="../Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>Wybierz numery stron lub zakresy do wyodrębniania tekstu

W przypadku dużych dokumentów wielostronicowych Użyj `pages` parametru zapytania, aby wskazać określone numery stron lub zakresy stron na potrzeby wyodrębniania tekstu. Poniższy przykład pokazuje dokument zawierający 10 stron z tekstem wyodrębnionym dla obu przypadków — wszystkie strony (1-10) i wybrane strony (3-6).

:::image type="content" source="../Computer-vision/Images/ocr-select-pages.png" alt-text="Dane wyjściowe układu wybranych stron":::

## <a name="the-get-analyze-layout-result-operation"></a>Operacja uzyskania wyniku w układzie analizy

Drugim krokiem jest wywołanie operacji [Get Analizuj wyniki układu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult) . Ta operacja przyjmuje jako dane wejściowe Identyfikator wyniku, który został utworzony przez operację analizy układu. Zwraca odpowiedź JSON, która zawiera pole **stanu** z następującymi możliwymi wartościami. 

|Pole| Typ | Możliwe wartości |
|:-----|:----:|:----|
|status | ciąg | `notStarted`: Operacja analizy nie została rozpoczęta.<br /><br />`running`: Operacja analizy jest w toku.<br /><br />`failed`: Operacja analizy zakończyła się niepowodzeniem.<br /><br />`succeeded`: Operacja analizy zakończyła się pomyślnie.|

Wywołaj tę operację iteracyjnie, dopóki nie zwróci `succeeded` wartości. Użyj interwału od 3 do 5 sekund, aby uniknąć przekroczenia liczby żądań na sekundę (RPS pliku).

Gdy pole **stan** ma `succeeded` wartość, odpowiedź JSON będzie zawierać wyodrębniony układ, tekst, tabele i znaczniki wyboru. Wyodrębnione dane obejmują wyodrębnione wiersze tekstu i słowa, pola powiązane, wygląd tekstu z oznaczeniem ręcznym, tabelami i znacznikami wyboru z zaznaczonym/niezaznaczonym zaznaczeniem. 

### <a name="handwritten-classification-for-text-lines-latin-only"></a>Klasyfikacja odręczna dla linii tekstowych (tylko łaciński)

Odpowiedź obejmuje klasyfikowanie niezależnie od tego, czy każdy wiersz tekstu ma styl pisma ręcznego, czy nie, wraz z oceną ufności. Ta funkcja jest obsługiwana tylko w przypadku języków łacińskich. Poniższy przykład pokazuje klasyfikację odręczne dla tekstu w obrazie.

:::image type="content" source="../Computer-vision/Images/ocr-handwriting-classification.png" alt-text="przykład klasyfikacji pisma ręcznego":::

### <a name="sample-json-output"></a>Przykładowe dane wyjściowe JSON

Odpowiedź do operacji *pobierania wyniku układu Get* jest strukturalną reprezentacją dokumentu zawierającego wszystkie wyodrębnione informacje. Zobacz tutaj, aby znaleźć [przykładowy plik dokumentu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) i jego strukturalny [wynik przykładowe](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)dane wyjściowe.

Dane wyjściowe JSON mają dwie części:

* `readResults` węzeł zawiera wszystkie rozpoznane znaki tekstowe i zaznaczenia. Tekst jest zorganizowany według strony, następnie według wiersza, a następnie poszczególnych słów. 
* `pageResults` węzeł zawiera tabele i komórki wyodrębnione przy użyciu ich pól ograniczenia, pewności i odwołania do wierszy i słów w "readResults".

## <a name="example-output"></a>Przykładowe dane wyjściowe

### <a name="text"></a>Tekst

Interfejs API układu wyodrębnia tekst z dokumentów (PDF, TIFF) i obrazów (JPG, PNG, BMP) z wieloma kątami i kolorami tekstu. Akceptuje on Zdjęcia dokumentów, faksów, drukowanych i/lub odręcznych (tylko w języku angielskim) oraz tryby mieszane. Tekst jest wyodrębniany z informacjami udostępnianymi na wierszach, słowach, polach powiązanych, wynikach pewności i stylu (odręcznym lub innym). Wszystkie informacje tekstowe są zawarte w sekcji w `readResults` danych wyjściowych JSON. 

### <a name="tables"></a>tabelami

Interfejs API układu wyodrębnia tabele z dokumentów (PDF, TIFF) i obrazów (JPG, PNG, BMP). Dokumenty mogą być skanowane, z grafem lub cyframi. Tabele mogą być złożone z scalonymi komórkami lub kolumnami, z obramowaniem lub bez. Wyodrębnione informacje tabeli obejmują liczbę kolumn i wierszy, zakres wierszy i zakres kolumn. Każda komórka jest wyodrębniana wraz z obwiednią i odwołaniem do tekstu wyodrębnionego w `readResults` sekcji. Informacje o tabeli znajdują się w `pageResults` sekcji danych wyjściowych JSON. 

![Przykład tabel](./media/tables-example.jpg)

### <a name="selection-marks"></a>Znaczniki wyboru

Interfejs API układu wyodrębnia również znaczniki wyboru z dokumentów. Wyodrębnione znaczniki zaznaczenia obejmują pole ograniczenia, pewność i stan (wybrane/niezaznaczone). Informacje o zaznaczeniu są wyodrębniane w `readResults` sekcji danych wyjściowych JSON. 

## <a name="next-steps"></a>Następne kroki

* Wypróbuj własną ekstrakcję układu przy użyciu [przykładowego narzędzia interfejsu użytkownika rozpoznawania formularzy](https://fott-preview.azurewebsites.net/)
* Ukończ [Przewodnik Szybki Start dla aparatu rozpoznawania](quickstarts/client-library.md) , aby rozpocząć wyodrębnianie układów w wybranym języku programistycznym.

## <a name="see-also"></a>Zobacz też

* [Co to jest rozpoznawanie formularzy?](./overview.md)
* [Dokumentacja interfejsu API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)
