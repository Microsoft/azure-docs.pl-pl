---
title: Układy — aparat rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: Poznaj koncepcje związane z analizą układu przy użyciu interfejsu API rozpoznawania i limitów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: 3c2e691d673b385d597957cf5a4ce3c3f18ba466
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511552"
---
# <a name="form-recognizer-layout-service"></a>Usługa układu aparatu rozpoznawania formularzy

Aparat rozpoznawania formularzy platformy Azure może wyodrębnić tekst, tabele, znaczniki wyboru i informacje o strukturze z dokumentów przy użyciu usługi układu. Interfejs API układu umożliwia klientom korzystanie z dokumentów w różnych formatach i zwracanie danych strukturalnych i reprezentacji dokumentu. Łączy nasze zaawansowane funkcje [rozpoznawania znaków optycznych (OCR)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) z dokumentem opisującym modele uczenia głębokiego w celu wyodrębnienia tekstu, tabel, znaczników zaznaczenia i struktury dokumentów. 

## <a name="what-does-the-layout-service-do"></a>Do czego służy usługa układu?

Interfejs API układu wyodrębnia tekst, tabele, znaczniki wyboru i informacje o strukturze z dokumentów o wyjątkowej dokładności i zwraca je w zorganizowanej strukturalnej odpowiedzi JSON. Dokumenty mogą pochodzić z różnych formatów i jakości, w tym obrazów przechwyconych przez telefon, zeskanowanych dokumentów i cyfrowych plików PDF. Interfejs API układu będzie wyodrębniał strukturalne dane wyjściowe ze wszystkich tych dokumentów.

![Przykład układu](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Czas to wypróbować

Aby wypróbować usługę układu aparatu rozpoznawania formularzy, przejdź do narzędzia przykładowego interfejsu użytkownika w trybie online:

> [!div class="nextstepaction"]
> [Przykładowy interfejs użytkownika](https://fott-preview.azurewebsites.net/)

Potrzebna jest subskrypcja platformy Azure ([Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)) i punkt końcowy [zasobu aparatu rozpoznawania formularzy](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oraz klucz służący do wypróbowania interfejsu API układu aparatu rozpoznawania formularzy. 

![Przykładowy zrzut ekranu interfejsu użytkownika; tekst, tabele i znaczniki zaznaczenia dokumentu są analizowane](./media/analyze-layout.png)

### <a name="input-requirements"></a>Wymagania wejściowe 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>Operacja analizy układu

Operacja [analizy układu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) pobiera dokument (plik obrazu, TIFF lub PDF) jako dane wejściowe i wyodrębnia tekst, tabele, znaczniki wyboru i strukturę dokumentu. Wywołanie zwraca pole nagłówka odpowiedzi o nazwie `Operation-Location` . `Operation-Location`Wartość jest adresem URL, który zawiera identyfikator wynik do użycia w następnym kroku.

|Nagłówek odpowiedzi| Adres URL wyniku |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-layout-result-operation"></a>Operacja uzyskania wyniku w układzie analizy

Drugim krokiem jest wywołanie operacji [Get Analizuj wyniki układu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult) . Ta operacja przyjmuje jako dane wejściowe Identyfikator wyniku, który został utworzony przez operację analizy układu. Zwraca odpowiedź JSON, która zawiera pole **stanu** z następującymi możliwymi wartościami. 

|Pole| Typ | Możliwe wartości |
|:-----|:----:|:----|
|status | ciąg | `notStarted`: Operacja analizy nie została rozpoczęta.<br /><br />`running`: Operacja analizy jest w toku.<br /><br />`failed`: Operacja analizy zakończyła się niepowodzeniem.<br /><br />`succeeded`: Operacja analizy zakończyła się pomyślnie.|

Tę operację należy wywołać iteracyjnie, dopóki nie zostanie zwrócona z `succeeded` wartością. Użyj interwału od 3 do 5 sekund, aby uniknąć przekroczenia liczby żądań na sekundę (RPS pliku).

Gdy pole **stan** ma `succeeded` wartość, odpowiedź JSON będzie zawierać wyodrębnione wyniki wyodrębniania układu, tekst, tabele i znaczniki wyboru. Wyodrębnione dane zawierają wyodrębnione wiersze tekstu i wyrazy, pole ograniczenia, oznaczenie tekstu napisane ręcznie, tabele i znaczniki wyboru z oznaczeniem zaznaczony/niezaznaczone. 

### <a name="sample-json-output"></a>Przykładowe dane wyjściowe JSON

Odpowiedź na wynik operacji pobierania układu analizy będzie strukturalną reprezentacją dokumentu zawierającego wszystkie wyodrębnione informacje. Zobacz tutaj, aby znaleźć [przykładowy plik dokumentu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) i jego strukturalny [wynik przykładowe](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)dane wyjściowe.

Dane wyjściowe JSON mają dwie części: 
* `"readResults"` węzeł zawiera wszystkie rozpoznane znaki tekstowe i zaznaczenia. Tekst jest zorganizowany według strony, następnie według wiersza, a następnie poszczególnych słów. 
* `"pageResults"` węzeł zawiera tabele i komórki wyodrębnione przy użyciu ich pól ograniczenia, pewności i odwołania do wierszy i słów w "readResults".

## <a name="example-output"></a>Przykładowe dane wyjściowe

### <a name="text"></a>Tekst

Układ wyodrębnia tekst z dokumentów (PDF, TIFF) i obrazów (jpg, PNG, BMP) z różnymi kątami tekstu, kolorami, kątami, zdjęciami dokumentów, faksów, drukowanymi, odręcznymi (tylko w języku angielskim) i trybami mieszanymi. Tekst jest wyodrębniany z informacjami na temat wierszy, słów, pól ograniczenia, wyników pewności i stylu (odręczny lub inny). Wszystkie informacje tekstowe są zawarte w sekcji w `"readResults"` danych wyjściowych JSON. 

### <a name="tables"></a>Tabele

Układ wyodrębnia tabele z dokumentów (PDF, TIFF) i obrazów (jpg, PNG, BMP). Dokumenty mogą być skanowane, z grafem lub cyframi. Tabele mogą być złożonymi tabelami ze scalonymi komórkami lub kolumnami z obramowaniem lub bez. Wyodrębnione tabele obejmują liczbę kolumn i wierszy, zakres wierszy i zakres kolumn. Każda komórka jest wyodrębniana wraz z obwiednią i odwołaniem do tekstu wyodrębnionego w `"readResults"` sekcji. Informacje o tabeli znajdują się w `"pageResults"` sekcji danych wyjściowych JSON. 

![Przykład tabel](./media/tables-example.jpg)

### <a name="selection-marks"></a>Znaczniki wyboru

Układ wyodrębnia także znaczniki wyboru z dokumentów. Wyodrębnione znaczniki zaznaczenia obejmują pole ograniczenia, pewność i stan (wybrane/niezaznaczone). Informacje o zaznaczeniu są wyodrębniane w `"readResults"` sekcji danych wyjściowych JSON. 

<<< Dodawanie znaczników zaznaczenia>>>

## <a name="next-steps"></a>Następne kroki

- Wypróbuj własną ekstrakcję układu przy użyciu [przykładowego interfejsu użytkownika aparatu rozpoznawania formularzy](https://fott-preview.azurewebsites.net/)
- Można też postępować zgodnie z przewodnikiem Szybki Start do wdrażania [danych układu przy](./QuickStarts/python-layout.md) użyciu języka Python i interfejsu API REST.

## <a name="see-also"></a>Zobacz także

* [Co to jest rozpoznawanie formularzy?](./overview.md)
* [Dokumentacja interfejsu API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)




