---
title: 'Samouczek: używanie twórcy Maps Microsoft Azure (wersja zapoznawcza) do tworzenia map wewnętrznych'
description: Samouczek dotyczący korzystania z programu Microsoft Azure Maps Creator (wersja zapoznawcza) w celu tworzenia map wewnętrznych
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: eab8a2729209bb0023662b652f862b4fa678470e
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905727"
---
# <a name="tutorial-use-creator-preview-to-create-indoor-maps"></a>Samouczek: Tworzenie map wewnętrznych przy użyciu kreatora (wersja zapoznawcza)

> [!IMPORTANT]
> Usługi Azure Maps Creator Services są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



W tym samouczku pokazano, jak utworzyć mapy wewnętrzne. W tym samouczku dowiesz się, jak używać interfejsu API w celu:

> [!div class="checklist"]
> * Przekaż swój pakiet rysowania mapy w postaci pomieszczeń
> * Konwertuj pakiet rysowania na dane mapy
> * Tworzenie zestawu danych na podstawie danych mapy
> * Utwórz element tileset na podstawie danych w zestawie danych
> * Zapoznaj się z interfejsem API usługi Azure Maps Web Feature (WFS), aby dowiedzieć się więcej na temat funkcji mapy
> * Tworzenie funkcji stateset przy użyciu funkcji mapy i danych w zestawie danych
> * Aktualizowanie funkcji stateset

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć mapy wewnętrzne:

1. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji.
3. [Tworzenie zasobu twórcy (wersja zapoznawcza)](how-to-manage-creator.md)
4. Pobierz [przykładowy pakiet rysowania](https://github.com/Azure-Samples/am-creator-indoor-data-examples/blob/master/Sample%20-%20Contoso%20Drawing%20Package.zip).

W tym samouczku jest stosowana aplikacja programu [Poster](https://www.postman.com/) , ale można wybrać inne środowisko deweloperskie interfejsu API.

>[!IMPORTANT]
> Adresy URL interfejsu API w tym dokumencie mogą być dostosowane do lokalizacji Twojego zasobu twórcy. Aby uzyskać więcej informacji, zobacz [dostęp do usługi Creator Services](how-to-manage-creator.md#access-to-creator-services).

## <a name="upload-a-drawing-package"></a>Przekaż pakiet rysowania

Użyj [interfejsu API przekazywania danych](/rest/api/maps/data/uploadpreview) , aby przekazać pakiet rysowania do zasobów Azure Maps.

Interfejs API przekazywania danych to długotrwała transakcja implementująca wzorzec zdefiniowany w tym miejscu. Po zakończeniu operacji będziemy korzystać z programu w `udid` celu uzyskania dostępu do przekazanego pakietu w celu jego konwersji. Wykonaj poniższe kroki, aby uzyskać `udid` .

1. Otwórz aplikację Poster. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Kolekcja**.  Nadaj kolekcji nazwę i wybierz przycisk **Utwórz** .

2. Aby utworzyć żądanie, wybierz pozycję **nowe** ponownie. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzednim kroku, a następnie wybierz pozycję **Zapisz**.

3. Wybierz metodę **post** http na karcie Konstruktor i wprowadź następujący adres URL, aby przekazać pakiet rysowania do usługi Azure Maps. W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym.

    ```http
    https://atlas.microsoft.com/mapData/upload?api-version=1.0&dataFormat=zip&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Na karcie **nagłówki** Określ wartość `Content-Type` klucza. Pakiet rysowania jest folderem spakowanym, więc Użyj `application/octet-stream` wartości. Na karcie **treść** wybierz pozycję dane **binarne**. Kliknij pozycję **Wybierz plik** i wybierz pakiet rysowania.

     ![Zarządzanie danymi](./media/tutorial-creator-indoor-maps/enter-content-type-dialog.png)

5. Kliknij niebieski przycisk **Wyślij** i poczekaj na przetworzenie żądania. Po zakończeniu żądania przejdź na kartę **nagłówki** odpowiedzi. Skopiuj wartość klucza **lokalizacji** , czyli `status URL` .

6. Aby sprawdzić stan wywołania interfejsu API, Utwórz żądanie **Get** http na stronie `status URL` . Musisz dołączyć podstawowy klucz subskrypcji do adresu URL na potrzeby uwierzytelniania. Żądanie **Get** powinno wyglądać następująco:

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

7. Pomyślne zakończenie żądania **Get** http spowoduje zwrócenie elementu `resourceLocation` . `resourceLocation`Zawiera unikatowy `udid` dla przekazanej zawartości. Opcjonalnie możesz użyć `resourceLocation` adresu URL, aby pobrać metadane z tego zasobu w następnym kroku.

    ```json
    {
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
    }
    ```

8. Aby pobrać metadane zawartości, Utwórz żądanie **Get** http na `resourceLocation` adresie URL, który został pobrany w kroku 7. Pamiętaj o dołączeniu podstawowego klucza subskrypcji do adresu URL na potrzeby uwierzytelniania. Żądanie **Get** powinno wyglądać następująco:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. Po pomyślnym zakończeniu żądania **Get** http treść odpowiedzi będzie zawierać `udid` określone w `resourceLocation` kroku 7, lokalizację dostępu/pobrać zawartość w przyszłości oraz inne metadane dotyczące zawartości, takie jak Data utworzenia/zaktualizowana, rozmiar itd. Przykładem ogólnej odpowiedzi jest:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "2020-02-03T02:32:25.0509366+00:00",
        "updated": "2020-02-11T06:12:13.0309351+00:00",
        "sizeInBytes": 766,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>Konwertowanie pakietu rysowania

 Teraz, gdy pakiet do rysowania zostanie przekazany, zostanie użyty `udid` dla przekazanego pakietu do przekonwertowania pakietu na dane mapy. Interfejs API konwersji używa długotrwałej transakcji implementującej wzorzec zdefiniowany w [tym miejscu](creator-long-running-operation.md). Po zakończeniu operacji będziemy korzystać z programu `conversionId` w celu uzyskania dostępu do przekonwertowanych danych. Wykonaj poniższe kroki, aby uzyskać `conversionId` .

1. Wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** i wybierz kolekcję. Kliknij pozycję **Zapisz**.

2. Wybierz metodę **post** http na karcie Konstruktor i wprowadź następujący adres URL, aby przekonwertować przekazany pakiet rysowania na dane mapy. Użyj `udid` do przekazanego pakietu.

    ```http
    https://atlas.microsoft.com/conversion/convert?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&udid={udid}&inputType=DWG
    ```

    >[!IMPORTANT]
    > Adresy URL interfejsu API w tym dokumencie mogą być dostosowane do lokalizacji Twojego zasobu twórcy. Aby uzyskać więcej informacji, zobacz [dostęp do usługi Creator Services (wersja zapoznawcza) ](how-to-manage-creator.md#access-to-creator-services).

3. Kliknij przycisk **Wyślij** i poczekaj na przetworzenie żądania. Po zakończeniu żądania przejdź do karty **nagłówki** w odpowiedzi i Wyszukaj klucz **lokalizacji** . Skopiuj wartość klucza **lokalizacji** , która jest `status URL` dla żądania konwersji. Zostanie ona użyta w następnym kroku.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/copy-location-uri-dialog.png" border="true" alt-text="Skopiuj wartość klucza lokalizacji":::

4. Rozpocznij nową metodę **Get** http na karcie Konstruktor. Dołącz klucz subskrypcji Azure Maps do programu `status URL` . Wykonaj żądanie **Get** w lokalizacji `status URL` skopiowanej w kroku 3. `status URL`Wygląda jak następujący adres URL:

    ```http
    https://atlas.microsoft.com/conversion/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

    Jeśli proces konwersji jeszcze nie zakończył się, może zostać wyświetlony komunikat podobny do następującego:

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Running"
    }
    ```

5. Po pomyślnym zakończeniu żądania zobaczysz komunikat o stanie sukces w treści odpowiedzi.  Skopiuj `conversionId` `resourceLocation` adres URL przekonwertowanego pakietu. `conversionId`Jest używany przez inny interfejs API do uzyskiwania dostępu do przekonwertowanych danych mapy.

    ```json
   {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {}
    }
    ```

>[!NOTE]
>Aplikacja Poster nie obsługuje natywnie żądań HTTP o długotrwałym działaniu. W związku z tym może wystąpić duże opóźnienie podczas wykonywania żądania **Get** w adresie URL stanu.  Poczekaj około 30 sekund i spróbuj ponownie kliknąć przycisk **Wyślij** , dopóki odpowiedź nie zostanie wyświetlona lub nie powiedzie się.

Przykładowy pakiet rysowania powinien zostać skonwertowany bez błędów lub ostrzeżeń. Jednak w przypadku otrzymania błędów lub ostrzeżeń z własnego pakietu rysowania odpowiedź w formacie JSON będzie zawierać link do [wizualizatora błędu rysowania](drawing-error-visualizer.md). Wizualizator błędu rysowania umożliwia sprawdzenie szczegółów błędów i ostrzeżeń. Aby uzyskać zalecenia dotyczące sposobu rozwiązywania błędów i ostrzeżeń konwersji, zobacz [rysowanie błędów i ostrzeżeń dotyczących konwersji](drawing-conversion-error-codes.md).

```json
{
    "operationId": "<operationId>",
    "created": "2020-04-22T19:39:54.9518496+00:00",
    "status": "Failed",
    "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>Utwórz zestaw danych

Zestaw danych to zbiór funkcji mapy, takich jak budynki, poziomy i pokoje. Aby utworzyć zestaw danych, użyj [interfejsu API tworzenia zestawu danych](/rest/api/maps/dataset/createpreview). Interfejs API tworzenia zestawu danych przyjmuje `conversionId` dla przekonwertowanego pakietu rysowania i zwraca `datasetId` z utworzonego zestawu danych. Poniższe kroki pokazują, jak utworzyć zestaw danych.

1. W aplikacji pocztowej wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** i wybierz kolekcję. Kliknij pozycję **Zapisz**.

2. Wprowadź żądanie **post** do [zestawu danych Utwórz interfejs API](/rest/api/maps/dataset/createpreview) , aby utworzyć nowy zestaw danych. Przed przesłaniem żądania Dołącz zarówno klucz subskrypcji, jak i `conversionId` `conversionId` uzyskany podczas procesu konwersji w kroku 5.  Żądanie powinno wyglądać następująco:

    ```http
    https://atlas.microsoft.com/dataset/create?api-version=1.0&conversionID={conversionId}&type=facility&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Uzyskaj `statusURL` w kluczu **lokalizacji** **nagłówków** odpowiedzi.

4. Utwórz żądanie **Get** w usłudze, `statusURL` Aby uzyskać `datasetId` . Dołącz podstawowy klucz subskrypcji Azure Maps na potrzeby uwierzytelniania. Żądanie powinno wyglądać następująco:

    ```http
    https://atlas.microsoft.com/dataset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

5. Po pomyślnym zakończeniu żądania **Get** http nagłówek odpowiedzi będzie zawierać `datasetId` dla utworzonego zestawu danych. Skopiuj `datasetId` . Musisz użyć `datasetId` elementu, aby utworzyć tileset.

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:52:38.9352189+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://azure.microsoft.com/dataset/{datasetiId}?api-version=1.0"
     }
    ```

## <a name="create-a-tileset"></a>Utwórz tileset

Tileset to zbiór kafelków wektorowych, które są renderowane na mapie. Tilesets są tworzone na podstawie istniejących zestawów danych. Jednak tileset jest niezależny od zestawu danych, z którego pochodzi. Jeśli zestaw danych zostanie usunięty, tileset będzie nadal istnieć. Aby utworzyć tileset, wykonaj następujące czynności:

1. W aplikacji pocztowej wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** i wybierz kolekcję. Kliknij pozycję **Zapisz**.

2. Utwórz żądanie **post** na karcie Konstruktor. Adres URL żądania powinien wyglądać następująco:

    ```http
    https://atlas.microsoft.com/tileset/create/vector?api-version=1.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Utwórz żądanie **Get** w `statusURL` tileset. Dołącz podstawowy klucz subskrypcji Azure Maps na potrzeby uwierzytelniania. Żądanie powinno wyglądać następująco:

   ```http
    https://atlas.microsoft.com/tileset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Po pomyślnym zakończeniu żądania **Get** http nagłówek odpowiedzi będzie zawierać `tilesetId` dla utworzonego tileset. Skopiuj `tilesetId` .

    ```json
    {
        "operationId": "<operationId>",
        "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/tileset/{tilesetId}?api-version=1.0"
    }
    ```

## <a name="query-datasets-with-wfs-api"></a>Wykonywanie zapytań dotyczących zestawów danych za pomocą interfejsu API WFS

 Do zestawów danych można wykonywać zapytania przy użyciu  [interfejsu API WFS](/rest/api/maps/wfs). Za pomocą interfejsu API WFS można wykonywać zapytania dotyczące kolekcji funkcji, określonej kolekcji lub konkretnej funkcji z **identyfikatorem** funkcji. **Identyfikator** funkcji jednoznacznie identyfikuje funkcję w zestawie danych. Jest on używany na przykład w celu określenia, który stan funkcji należy zaktualizować w danym stateset.

1. W aplikacji pocztowej wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** i wybierz kolekcję. Kliknij pozycję **Zapisz**.

2. Utwórz żądanie **Get** , aby wyświetlić listę kolekcji w zestawie danych. Zamień `<dataset-id>` na swoje `datasetId` . Użyj Azure Maps klucza podstawowego zamiast symbolu zastępczego. Żądanie powinno wyglądać następująco:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

3. Treść odpowiedzi zostanie dostarczona w formacie GEOJSON i będzie zawierać wszystkie kolekcje w zestawie danych. Dla uproszczenia w tym przykładzie jest wyświetlana tylko `unit` kolekcja. Aby zobaczyć przykład, który zawiera wszystkie kolekcje, zobacz [WFS opisywanie interfejsu API kolekcji](/rest/api/maps/wfs/collectiondescriptionpreview). Aby dowiedzieć się więcej na temat dowolnej kolekcji, można kliknąć dowolny z adresów URL wewnątrz `link` elementu.

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

4. Utwórz żądanie **Get** dla `unit` kolekcji funkcji.  Zamień `{datasetId}` na swoje `datasetId` . Użyj Azure Maps klucza podstawowego zamiast symbolu zastępczego. Treść odpowiedzi będzie zawierać wszystkie funkcje `unit` kolekcji. Żądanie powinno wyglądać następująco:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. Skopiuj funkcję `id` dla funkcji jednostki, która ma właściwości stylu, które mogą być modyfikowane dynamicznie.  Ze względu na to, że stan zajętości jednostki i temperatury można dynamicznie aktualizować, użyjemy tej funkcji `id` w następnej sekcji. W poniższym przykładzie funkcja `id` ma wartość "UNIT26". do właściwości stylu tej funkcji można odwoływać się jako Stany i będziemy używać tej funkcji do stateset.

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>Tworzenie funkcji stateset

1. W aplikacji pocztowej wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** i wybierz kolekcję. Kliknij pozycję **Zapisz**.

2. Wprowadź żądanie **post** do [interfejsu API tworzenia Stateset](/rest/api/maps/featurestate/createstatesetpreview). Użyj `datasetId` zestawu danych, który zawiera stan, który chcesz zmodyfikować. Żądanie powinno wyglądać następująco:

    ```http
    https://atlas.microsoft.com/featureState/stateset?api-version=1.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. W **nagłówkach** żądania **post** ustaw wartość `Content-Type` `application/json` . W **treści** podaj poniższe style, aby odzwierciedlić zmiany w `occupied` `temperature` *Stanach* i. Gdy skończysz, kliknij przycisk **Wyślij**.

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          },
          {
             "keyname":"temperature",
             "type":"number",
             "rules":[
                {
                   "range":{
                      "exclusiveMaximum":66
                   },
                   "color":"#00204e"
                },
                {
                   "range":{
                      "minimum":66,
                      "exclusiveMaximum":70
                   },
                   "color":"#0278da"
                },
                {
                   "range":{
                      "minimum":70,
                      "exclusiveMaximum":74
                   },
                   "color":"#187d1d"
                },
                {
                   "range":{
                      "minimum":74,
                      "exclusiveMaximum":78
                   },
                   "color":"#fef200"
                },
                {
                   "range":{
                      "minimum":78,
                      "exclusiveMaximum":82
                   },
                   "color":"#fe8c01"
                },
                {
                   "range":{
                      "minimum":82
                   },
                   "color":"#e71123"
                }
             ]
          }
       ]
    }
    ```

4. Skopiuj `statesetId` z treści odpowiedzi.

5. Utwórz żądanie **post** , aby zaktualizować stan: Przekaż statesetId i funkcję `ID` za pomocą klucza subskrypcji Azure Maps. Żądanie powinno wyglądać następująco:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID={featureId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. W **nagłówkach** żądania **post** ustaw wartość `Content-Type` `application/json` . W **treści** żądania **post** skopiuj i wklej kod JSON w poniższym przykładzie.

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!NOTE]
    > Aktualizacja zostanie zapisana tylko wtedy, gdy sygnatura czasowa jest późniejsza niż sygnatura czasowa poprzedniego żądania. Możemy przekazać wszystkie KeyName, które zostały wcześniej skonfigurowane podczas tworzenia.

7. Po pomyślnej aktualizacji otrzymasz `200 OK` kod stanu HTTP. Jeśli masz  [zaimplementowane style dynamiczne](indoor-map-dynamic-styling.md) dla mapy pomieszczeń, aktualizacja zostanie wyświetlona na renderowanej mapie w określonej sygnaturze czasowej.

[Interfejs API Get States funkcji](/rest/api/maps/featurestate/getstatespreview) umożliwia pobranie stanu funkcji przy użyciu jej funkcji `ID` . Stateset i jego zasoby można także usunąć za pomocą [interfejsu API usuwania stanu funkcji](/rest/api/maps/featurestate/deletestatesetpreview).

Aby dowiedzieć się więcej na temat różnych usług Azure Maps Creator Services (wersja zapoznawcza) omówionych w tym artykule, zobacz Tworzenie wbudowanych [map programu Creator](creator-indoor-maps.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak używać modułu Maps (mapy wewnętrzne), zobacz

> [!div class="nextstepaction"]
> [Korzystanie z modułu Maps (mapy wewnętrzne)](how-to-use-indoor-module.md)