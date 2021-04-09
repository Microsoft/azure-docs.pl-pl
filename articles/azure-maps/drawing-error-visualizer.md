---
title: Użyj wizualizatora błędów rysowania Azure Maps
description: Ten artykuł zawiera informacje na temat wizualizacji ostrzeżeń i błędów zwracanych przez interfejs API konwersji twórcy (wersja zapoznawcza).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: db88e347e12783205ea8c31fed0bb374fccb4736
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96903585"
---
# <a name="using-the-azure-maps-drawing-error-visualizer-with-creator-preview"></a>Używanie wizualizatora błędów rysowania Azure Maps z twórcą (wersja zapoznawcza)

> [!IMPORTANT]
> Usługi Azure Maps Creator Services są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Wizualizator błędu rysowania to autonomiczna aplikacja sieci Web, która wyświetla [ostrzeżenia i błędy pakietu rysowania](drawing-conversion-error-codes.md) wykryte podczas procesu konwersji. Aplikacja sieci Web wizualizatora błędów składa się ze strony statycznej, której można użyć bez łączenia się z Internetem.  Możesz użyć wizualizatora błędów, aby naprawić błędy i ostrzeżenia zgodnie z [wymaganiami dotyczącymi pakietów rysowania](drawing-requirements.md). [Interfejs API konwersji Azure Maps](/rest/api/maps/conversion) zwraca tylko odpowiedź z linkiem do wizualizatora błędu tylko w przypadku wykrycia błędu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było pobrać wizualizator błędu rysowania, należy wykonać następujące:

1. [Tworzenie konta usługi Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji.
3. [Tworzenie zasobu twórcy (wersja zapoznawcza)](how-to-manage-creator.md)

W tym samouczku jest stosowana aplikacja programu [Poster](https://www.postman.com/) , ale można wybrać inne środowisko deweloperskie interfejsu API.

## <a name="download"></a>Pobierz

1. Przekaż pakiet rysowania do usługi Azure Maps Creator (wersja zapoznawcza), aby uzyskać dostęp do `udid` przekazanego pakietu. Aby uzyskać instrukcje dotyczące sposobu przekazywania pakietu, zobacz [przekazywanie pakietu rysowania](tutorial-creator-indoor-maps.md#upload-a-drawing-package).

2. Teraz, gdy pakiet do rysowania zostanie przekazany, zostanie użyty `udid` dla przekazanego pakietu do przekonwertowania pakietu na dane mapy. Aby uzyskać instrukcje dotyczące sposobu konwersji pakietu, zobacz [konwertowanie pakietu rysowania](tutorial-creator-indoor-maps.md#convert-a-drawing-package).

    >[!NOTE]
    >Jeśli proces konwersji zakończy się powodzeniem, nie zostanie wyświetlony link do narzędzia wizualizatora błędów.

3. Na karcie **nagłówki** odpowiedzi w aplikacji Poster Znajdź `diagnosticPackageLocation` Właściwość ZWRACANĄ przez interfejs API konwersji. Odpowiedź powinna wyglądać tak jak w poniższym kodzie JSON:

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Failed",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {
            "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
        }
    }
    ```

4. Pobierz wizualizator błędu pakietu rysowania, wysyłając `HTTP-GET` żądanie na `diagnosticPackageLocation` adres URL.

## <a name="setup"></a>Konfiguracja

W ramach pobranego spakowanego pakietu z linku znajdują się `diagnosticPackageLocation` dwa pliki.

* _VisualizationTool.zip_: zawiera kod źródłowy, nośnik i stronę sieci Web wizualizatora błędu rysowania.
* _ConversionWarningsAndErrors.js_: zawiera sformatowaną listę ostrzeżeń, błędów i dodatkowych szczegółów, które są używane przez wizualizator błędów rysowania.

Rozpakuj folder _VisualizationTool.zip_ . Zawiera następujące elementy:

* folder _zasobów_ : zawiera obrazy i pliki multimedialne
* folder _statyczny_ : kod źródłowy
* Plik _index.html_ : aplikacja sieci Web.

Otwórz plik _index.html_ przy użyciu dowolnej z poniższych przeglądarek z odpowiednimi numerami wersji. Możesz użyć innej wersji, jeśli wersja oferuje równie zgodne zachowanie jak wyświetlana wersja.

* Microsoft Edge 80
* Przeglądarka Safari 13
* Chrome 80
* Firefox 74

## <a name="using-the-drawing-error-visualizer-tool"></a>Korzystanie z narzędzia wizualizator błędu rysowania

Po uruchomieniu narzędzia wizualizatora błędów rysowania zostanie wyświetlona strona przekazywania. Strona przekazywanie zawiera pole przeciągnij i upuść. Pole upuszczania & przeciągnij również działa jako przycisk, który uruchamia okno dialogowe Eksploratora plików.

:::image type="content" source="./media/drawing-errors-visualizer/start-page.png" alt-text="Aplikacja wizualizatora błędów rysowania — Strona początkowa":::

_ConversionWarningsAndErrors.jsw_ pliku został umieszczony w katalogu głównym pobranego katalogu. Aby załadować _ConversionWarningsAndErrors.js_ , możesz przeciągnąć & usunąć plik do pola lub kliknąć pole, znaleźć plik w oknie dialogowym Eksploratora plików, a następnie przekazać plik.

:::image type="content" source="./media/drawing-errors-visualizer/loading-data.gif" alt-text="Wizualizator błędu rysowania — przeciąganie i upuszczanie w celu załadowania danych":::

Po załadowaniu pliku _ConversionWarningsAndErrors.js_ zostanie wyświetlona lista błędów i ostrzeżeń pakietu rysowania. Każdy błąd lub ostrzeżenie jest określany na podstawie warstwy, poziomu i komunikatu szczegółowego. Aby wyświetlić szczegółowe informacje dotyczące błędu lub ostrzeżenia, kliknij link **szczegóły** . Sekcja, która może zostać przemieszczona, zostanie wyświetlona poniżej listy. Możesz teraz przejść do każdego błędu, aby uzyskać więcej szczegółowych informacji na temat sposobu rozwiązania błędu.

:::image type="content" source="./media/drawing-errors-visualizer/errors.png" alt-text="Wizualizator błędu aplikacji — błędy i ostrzeżenia":::

## <a name="next-steps"></a>Następne kroki

Gdy [pakiet rysowania spełnia wymagania](drawing-requirements.md), możesz użyć [usługi Azure Maps DataSet](/rest/api/maps/conversion) , aby skonwertować pakiet rysowania do zestawu danych. Następnie możesz użyć modułu sieci Web Maps (mapy wewnętrzne) do tworzenia aplikacji. Dowiedz się więcej, czytając następujące artykuły:

> [!div class="nextstepaction"]
> [Kody błędów konwersji rysowania](drawing-conversion-error-codes.md)

> [!div class="nextstepaction"]
> [Twórca (wersja zapoznawcza) dla map pomieszczeń](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Korzystanie z modułu Maps (mapy wewnętrzne)](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Implementuj dynamiczne style mapy](indoor-map-dynamic-styling.md)