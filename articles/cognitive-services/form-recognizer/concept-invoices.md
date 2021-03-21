---
title: Faktury — aparat rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: Poznaj koncepcje związane z analizą faktur przy użyciu interfejsu API rozpoznawania i limitów.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 46cf34bd40832488985008a645f1da25eb87b9d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467395"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>Wbudowany model faktury aparatu rozpoznawania formularzy

Aparat rozpoznawania formularzy platformy Azure umożliwia analizowanie i wyodrębnianie informacji z faktur sprzedaży przy użyciu wbudowanych modeli faktur. Interfejs API faktur umożliwia klientom korzystanie z faktur w różnych formatach i zwracanie danych strukturalnych w celu zautomatyzowania przetwarzania faktur. Łączy nasze zaawansowane funkcje [rozpoznawania znaków optycznych (OCR)](../computer-vision/concept-recognizing-text.md) z fakturą zrozumienie modeli uczenia głębokiego, aby wyodrębnić informacje o kluczu z faktur w języku angielskim. Wyodrębnia tekst, tabele i informacje, takie jak klient, dostawca, Identyfikator faktury, Data płatności faktury, suma, kwota faktury należna, kwota podatku, Wysyłka do, rachunek za wiersz i wiele innych. Prekompilowany interfejs API fakturowania jest publicznie dostępny w postaci aparatu rozpoznawania w wersji zapoznawczej.

## <a name="what-does-the-invoice-service-do"></a>Co robi usługa Invoice?

Interfejs API fakturowania wyodrębnia pola kluczy i elementy wiersza z faktur i zwraca je w zorganizowanej strukturalnej odpowiedzi JSON. Faktury mogą pochodzić z różnych formatów i jakości, w tym obrazów przechwyconych przez telefon, zeskanowanych dokumentów i cyfrowych plików PDF. Interfejs API faktur będzie wyodrębniał strukturalne dane wyjściowe ze wszystkich tych faktur. 

![Przykład faktury contoso](./media/invoice-example-new.jpg)

## <a name="try-it-out"></a>Czas to wypróbować

Aby wypróbować tę usługę, przejdź do narzędzia przykładowego interfejsu użytkownika w trybie online:

> [!div class="nextstepaction"]
> [Wypróbuj wbudowane modele](https://fott-preview.azurewebsites.net/)

Potrzebna jest subskrypcja platformy Azure ([Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)) i punkt końcowy [zasobu aparatu rozpoznawania formularzy](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oraz klucz służący do wypróbowania usługi faktury dla aparatu rozpoznawania formularzy. 

:::image type="content" source="media/analyze-invoice-new.png" alt-text="Przykład analizowanej faktury" lightbox="media/analyze-invoice-new.png":::

### <a name="input-requirements"></a>Wymagania wejściowe

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-invoice-operation"></a>Operacja analizy faktury

Operacja [Analizuj fakturę](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291) pobiera obraz lub plik PDF faktury jako dane wejściowe i wyodrębnia wartości zainteresowania. Wywołanie zwraca pole nagłówka odpowiedzi o nazwie `Operation-Location` . `Operation-Location`Wartość jest adresem URL, który zawiera identyfikator wynik do użycia w następnym kroku.

|Nagłówek odpowiedzi| Adres URL wyniku |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-invoice-result-operation"></a>Operacja pobrania wyniku analizy faktury

Drugim krokiem jest wywołanie operacji [Get analizowanie wyniku faktury](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9acb78c40a2533aee83) . Ta operacja przyjmuje jako dane wejściowe Identyfikator wyniku, który został utworzony przez operację analizowanie faktury. Zwraca odpowiedź JSON, która zawiera pole **stanu** z następującymi możliwymi wartościami. Tę operację można wywołać iteracyjnie, dopóki nie zwróci wartości z wartością **sukces** . Użyj interwału od 3 do 5 sekund, aby uniknąć przekroczenia liczby żądań na sekundę (RPS pliku).

|Pole| Typ | Możliwe wartości |
|:-----|:----:|:----|
|status | ciąg | notStarted: operacja analizy nie została rozpoczęta.<br /><br />Uruchamianie: operacja analizy jest w toku.<br /><br />Niepowodzenie: operacja analizy zakończyła się niepowodzeniem.<br /><br />powodzenie: operacja analizy zakończyła się pomyślnie.|

W przypadku wartości pola **stan** **zakończyła się pomyślnie** , odpowiedź JSON będzie zawierać wyniki zrozumienie faktury, wyodrębnione tabele i opcjonalne wyniki rozpoznawania tekstu, jeśli jest to wymagane. Wynik zrozumienie faktury jest zorganizowany jako słownik nazwanych wartości pól, gdzie każda wartość zawiera wyodrębniony tekst, znormalizowana wartość, pole ograniczenia, pewność i odpowiednie elementy programu Word. Zawiera również elementy wiersza wyodrębnione, gdzie każdy element wiersza zawiera kwotę, opis, CenaJednostkowa, ilość itp. Wynik rozpoznawania tekstu jest zorganizowany jako hierarchia wierszy i słów, z tekstem, obwiednią i informacjami o ufności.

### <a name="sample-json-output"></a>Przykładowe dane wyjściowe JSON

Odpowiedź na operację Pobierz wyniki analizy faktury będzie strukturalną reprezentacją faktury ze wszystkimi wyodrębnionymi informacjami. Zobacz tutaj, aby zapoznać się z [przykładowym plikiem faktury](media/sample-invoice.jpg) i danymi wyjściowymi [przykładowych](media/invoice-example-new.jpg)danych wyjściowych ze strukturą.

Dane wyjściowe JSON zawierają 3 części: 
* `"readResults"` węzeł zawiera wszystkie rozpoznane znaki tekstowe i zaznaczenia. Tekst jest zorganizowany według strony, następnie według wiersza, a następnie poszczególnych słów. 
* `"pageResults"` węzeł zawiera tabele i komórki wyodrębnione wraz z ich polami ograniczenia, pewność i odwołanie do wierszy i słów w "readResults".
* `"documentResults"` węzeł zawiera wartości poszczególnych faktur i pozycje wierszy, które zostały odnalezione przez model. Jest to miejsce, w którym znajdziesz wszystkie pola z faktury, takie jak identyfikator faktury, Wysyłka do, rachunek, klient, łącznie, elementy wiersza i wiele więcej.

## <a name="example-output"></a>Przykładowe dane wyjściowe

Usługa Invoice wyodrębni pola tekstu, tabel i 26 pól faktury. Poniżej przedstawiono pola wyodrębnione z faktury w odpowiedzi wyjściowej JSON (poniższe dane wyjściowe używają tej [przykładowej faktury](media/sample-invoice.jpg)).

|Nazwa| Typ | Opis | Tekst | Wartość (standardowe dane wyjściowe) |
|:-----|:----|:----|:----| :----|
| CustomerName | ciąg | Odbiorca jest fakturowany | Microsoft Corp |  |
| CustomerId | ciąg | Identyfikator odwołania dla klienta | IDENTYFIKATOR CID 12345 |  |
| PurchaseOrder | ciąg | Numer referencyjny zamówienia zakupu | ZZ – 3333 | | 
| InvoiceId | ciąg | Identyfikator dla tej konkretnej faktury (często "numer faktury") | INV-100 | | 
| InvoiceDate | data | Data wystawienia faktury | 11/15/2019 | 2019-11-15 | 
| DueDate | data | Data płatności dla tej faktury przypada | 12/15/2019 | 2019-12-15 |
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

Poniżej znajdują się elementy wiersza wyodrębnione z faktury w odpowiedzi wyjściowej JSON (poniższe dane wyjściowe używają tej [przykładowej faktury](./media/sample-invoice.jpg))  

|Nazwa| Typ | Opis | Tekst (wiersz elementu #1) | Wartość (standardowe dane wyjściowe) |
|:-----|:----|:----|:----| :----|
| Elementy | ciąg | Wiersz tekstu pełnego ciągu elementu wiersza | 3/4/2021 A123 Consulting Services 2 godz. $30,00 10% $60,00 | |
| Kwota | liczba | Ilość elementu wiersza | $60,00 | 100 |
| Opis | ciąg | Opis tekstowy dla elementu wiersza faktury. | Usługa doradcza | Usługa doradcza |
| Liczba | liczba | Ilość dla tego elementu wiersza faktury | 2 | 2 |
| UnitPrice | liczba | Cena netto lub brutto (w zależności od ustawienia faktury brutto faktury) jednej jednostki tego elementu | $30,00 | 30 |
| ProductCode | ciąg| Kod produktu, numer produktu lub jednostka SKU skojarzona z określonym elementem wiersza | A123 | |
| Jednostka | ciąg| Jednostka elementu wiersza, np. kg, lb itd. | godziny | |
| Data | data| Data odpowiadająca każdemu elementowi wiersza. Często jest to Data wysłania elementu wiersza | 3/4/2021| 2021-03-04 |
| Podatek | liczba | Podatek skojarzony z każdym elementem wiersza. Możliwe wartości to: kwota podatku, podatek% i podatek Y/N | 10% | |


## <a name="next-steps"></a>Następne kroki

- Wypróbuj własne faktury i przykłady w [przykładowym interfejsie użytkownika aparatu rozpoznawania formularzy](https://fott-preview.azurewebsites.net/).
- Ukończ [Przewodnik Szybki Start dla aparatu rozpoznawania](quickstarts/client-library.md) , aby rozpocząć pisanie aplikacji do przetwarzania faktur przy użyciu aparatu rozpoznawania formularzy w wybranym języku programistycznym.

## <a name="see-also"></a>Zobacz też

* [Co to jest rozpoznawanie formularzy?](./overview.md)
* [Dokumentacja interfejsu API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)
