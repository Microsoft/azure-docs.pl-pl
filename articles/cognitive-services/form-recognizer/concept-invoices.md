---
title: Faktury — aparat rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: Poznaj koncepcje związane z analizą faktur przy użyciu interfejsu API rozpoznawania i limitów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: af3935bab5cd4d6a2aa4a9e3250f6beec19a8ea4
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95736683"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>Wbudowany model faktury aparatu rozpoznawania formularzy

Aparat rozpoznawania formularzy platformy Azure umożliwia analizowanie i wyodrębnianie informacji z faktur sprzedaży przy użyciu wbudowanych modeli faktur. Interfejs API faktur umożliwia klientom korzystanie z faktur w różnych formatach i zwracanie danych strukturalnych w celu zautomatyzowania przetwarzania faktur. Łączy nasze zaawansowane funkcje [rozpoznawania znaków optycznych (OCR)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) z fakturą zrozumienie modeli uczenia głębokiego, aby wyodrębnić informacje o kluczu z faktur w języku angielskim. Zostanie wyodrębniony tekst, tabele i informacje, takie jak klient, dostawca, Identyfikator faktury, Data płatności faktury, suma, kwota faktury należna, kwota podatku, Wysyłka do, rachunek do i inne. Prekompilowany interfejs API fakturowania jest publicznie dostępny w postaci aparatu rozpoznawania w wersji zapoznawczej.

## <a name="what-does-the-invoice-service-do"></a>Co robi usługa Invoice?

Interfejs API fakturowania wyodrębnia pola kluczy z faktur i zwraca je w zorganizowanej strukturalnej odpowiedzi JSON. Faktury mogą pochodzić z różnych formatów i jakości, w tym obrazów przechwyconych przez telefon, zeskanowanych dokumentów i cyfrowych plików PDF. Interfejs API faktur będzie wyodrębniał strukturalne dane wyjściowe ze wszystkich tych faktur. 

![Przykład faktury contoso](./media/invoice-example.jpg)

## <a name="try-it-out"></a>Czas to wypróbować

Aby wypróbować tę usługę, przejdź do narzędzia przykładowego interfejsu użytkownika w trybie online:

> [!div class="nextstepaction"]
> [Wypróbuj wbudowane modele](https://fott-preview.azurewebsites.net/)

Potrzebna jest subskrypcja platformy Azure ([Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)) i Recognzier punkt końcowy [zasobu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oraz klucz do wypróbowania usługi faktury rozpoznawania formularzy. 

![Przykład analizowanej faktury](./media/analyze-invoice.png)


### <a name="input-requirements"></a>Wymagania wejściowe 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-invoice-operation"></a>Operacja analizy faktury

Operacja [Analizuj fakturę](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeInvoiceAsync) pobiera obraz lub plik PDF faktury jako dane wejściowe i wyodrębnia wartości zainteresowania. Wywołanie zwraca pole nagłówka odpowiedzi o nazwie `Operation-Location` . `Operation-Location`Wartość jest adresem URL, który zawiera identyfikator wynik do użycia w następnym kroku.

|Nagłówek odpowiedzi| Adres URL wyniku |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-invoice-result-operation"></a>Operacja pobrania wyniku analizy faktury

Drugim krokiem jest wywołanie operacji [Get analizowanie wyniku faktury](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeInvoiceResult) . Ta operacja przyjmuje jako dane wejściowe Identyfikator wyniku, który został utworzony przez operację analizowanie faktury. Zwraca odpowiedź JSON, która zawiera pole **stanu** z następującymi możliwymi wartościami. Tę operację można wywołać iteracyjnie, dopóki nie zwróci wartości z wartością **sukces** . Użyj interwału od 3 do 5 sekund, aby uniknąć przekroczenia liczby żądań na sekundę (RPS pliku).

|Pole| Typ | Możliwe wartości |
|:-----|:----:|:----|
|status | ciąg | notStarted: operacja analizy nie została rozpoczęta.<br /><br />Uruchamianie: operacja analizy jest w toku.<br /><br />Niepowodzenie: operacja analizy zakończyła się niepowodzeniem.<br /><br />powodzenie: operacja analizy zakończyła się pomyślnie.|

W przypadku wartości pola **stan** **zakończyła się pomyślnie** , odpowiedź JSON będzie zawierać wyniki zrozumienie faktury, wyodrębnione tabele i opcjonalne wyniki rozpoznawania tekstu, jeśli jest to wymagane. Wynik zrozumienie faktury jest zorganizowany jako słownik nazwanych wartości pól, gdzie każda wartość zawiera wyodrębniony tekst, znormalizowana wartość, pole ograniczenia, pewność i odpowiednie elementy programu Word. Wynik rozpoznawania tekstu jest zorganizowany jako hierarchia wierszy i słów, z tekstem, obwiednią i informacjami o ufności.

### <a name="sample-json-output"></a>Przykładowe dane wyjściowe JSON

Odpowiedź na operację Pobierz wyniki analizy faktury będzie strukturalną reprezentacją faktury ze wszystkimi wyodrębnionymi informacjami. Zobacz tutaj, aby zapoznać się z [przykładowym plikiem faktury](./media/sample-invoice.jpg) i danymi wyjściowymi [przykładowych](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice-output.json)danych wyjściowych ze strukturą.

Dane wyjściowe JSON zawierają 3 części: 
* `"readResults"` węzeł zawiera wszystkie rozpoznane znaki tekstowe i zaznaczenia. Tekst jest zorganizowany według strony, następnie według wiersza, a następnie poszczególnych słów. 
* `"pageResults"` węzeł zawiera tabele i komórki wyodrębnione wraz z ich polami ograniczenia, pewność i odwołanie do wierszy i słów w "readResults".
* `"documentResults"` węzeł zawiera wartości poszczególnych faktur, które zostały odnalezione przez model. W tym miejscu znajdziesz wszystkie pola z faktury, takie jak identyfikator faktury, Wysyłka do, rachunek do, klient, suma i wiele innych.

## <a name="example-output"></a>Przykładowe dane wyjściowe

Usługa Invoice wyodrębni pola tekstu, tabel i 26 pól faktury. Poniżej przedstawiono pola wyodrębnione z faktury w odpowiedzi wyjściowej JSON (poniższe dane wyjściowe używają tej [przykładowej faktury](./media/sample-invoice.jpg))  

|Nazwa| Typ | Opis | Tekst | Wartość (standardowe dane wyjściowe) |
|:-----|:----|:----|:----| :----|
| CustomerName | ciąg | Odbiorca jest fakturowany | Microsoft Corp |  |
| CustomerId | ciąg | Identyfikator odwołania dla klienta | IDENTYFIKATOR CID 12345 |  |
| PurchaseOrder | ciąg | Numer referencyjny zamówienia zakupu | ZZ – 3333 | |  |
| InvoiceId | ciąg | Identyfikator dla tej konkretnej faktury (często "numer faktury") | INV-100 | |  |
| InvoiceDate | data | Data wystawienia faktury | 11/15/2019 | 
| DueDate | data | Data płatności dla tej faktury przypada | 12/15/2019 | 2019-12-15 | 2019-11-15 |
| NazwaDostawcy | ciąg | Dostawca, który utworzył tę fakturę | CONTOSO LTD. | |
| VendorAddress | ciąg | Adres korespondencyjny dla dostawcy | 123 456th St New York, NY, 10001 | |
| VendorAddressRecipient | ciąg | Nazwa skojarzona z VendorAddress | Oddział firmy Contoso | |
| CustomerAddress | ciąg | Adres korespondencyjny dla klienta | 123 inne St, Redmond WA, 98052 | |
| CustomerAddressRecipient | ciąg | Nazwa skojarzona z CustomerAddress | Microsoft Corp | |
| BillingAddress | ciąg | Jawny adres rozliczeniowy dla klienta | 123 Bill St, Redmond WA, 98052 | |
| BillingAddressRecipient | ciąg | Nazwa skojarzona z BillingAddress | Usługi firmy Microsoft | |
| ShippingAddress | ciąg | Jawny adres wysyłkowy dla klienta | 123 — St, Redmond WA, 98052 | |
| ShippingAddressRecipient | ciąg | Nazwa skojarzona z ShippingAddress | Microsoft Delivery | |
| Suma częściowa | liczba | Pole sumy częściowej zidentyfikowane na tej fakturze | $100,00 | 100 | 
| TotalTax | liczba | Pole łącznego podatku zidentyfikowane na tej fakturze | $10,00 | 10 |
| InvoiceTotal | liczba | Łączna liczba nowych opłat skojarzonych z tą fakturą | $110,00 | 110 |
| AmountDue |  liczba | Łączna kwota ze względu na dostawcę | $610,00 | 610 |
| Adres serviceAddress | ciąg | Jawny adres usługi lub adres właściwości dla klienta | 123 usługa St, Redmond WA, 98052 | |
| ServiceAddressRecipient | ciąg | Nazwa skojarzona z elementem serviceAddress | Usługi firmy Microsoft | |
| RemittanceAddress | ciąg | Jawny przelew lub adres płatności dla klienta | 123 — Nowy Jork, NY, 10001 |  |
| RemittanceAddressRecipient | ciąg | Nazwa skojarzona z RemittanceAddress | Rozliczenia firmy Contoso |  |
| ServiceStartDate | data | Pierwszy dzień okresu usługi (na przykład okres usługi Billa narzędzia) | 14/10/2019 | 2019-10-14 |
| Serviceenddate | data | Data końcowa okresu usługi (na przykład okres usługi Billa narzędzia) | 11/14/2019 | 2019-11-14 |
| PreviousUnpaidBalance | liczba | Jawne wcześniej niepłatne saldo | $500,00 | 500 |


## <a name="next-steps"></a>Następne kroki

- Wypróbuj własne faktury i przykłady w [przykładowym interfejsie użytkownika aparatu rozpoznawania formularzy](https://fott-preview.azurewebsites.net/).
- Wykonaj kroki z [przewodnikiem Szybki Start dla biblioteki klienta aparatu rozpoznawania](quickstarts/client-library.md) , aby rozpocząć pisanie aplikacji do przetwarzania faktur przy użyciu aparatu rozpoznawania formularzy w wybranym języku.
- Lub postępuj zgodnie z przewodnikiem Szybki Start dla [danych faktury](./quickstarts/python-invoices.md) , aby zaimplementować wyodrębnianie danych z faktury za pomocą języka Python i interfejsu API REST.
## <a name="see-also"></a>Zobacz także

* [Co to jest rozpoznawanie formularzy?](./overview.md)
* [Dokumentacja interfejsu API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeInvoiceAsync)

