---
title: Azure Maps błędów i ostrzeżeń konwersji rysunku
description: Dowiedz się więcej na temat błędów konwersji i ostrzeżeń, które mogą wystąpić podczas korzystania z usługi konwersji Azure Maps. Zapoznaj się z zaleceniami dotyczącymi sposobu rozwiązywania błędów i ostrzeżeń z przykładami.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: 14cf5238d29ede1ea229604316eee875b417e50e
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361538"
---
# <a name="drawing-conversion-errors-and-warnings"></a>Błędy i ostrzeżenia dotyczące konwersji rysunków

[Usługa konwersji Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) pozwala skonwertować przekazane pakiety rysowania na dane mapy. Pakiety rysowania muszą być zgodne z [wymaganiami dotyczącymi pakietów rysowania](drawing-requirements.md). Jeśli co najmniej jedno wymaganie nie jest spełnione, usługa konwersji zwróci błędy lub ostrzeżenia. Ten artykuł zawiera informacje o błędach konwersji i kodach ostrzeżeń wraz z zaleceniami dotyczącymi ich rozwiązywania. Zawiera również przykłady rysunków, które mogą spowodować zwrócenie tych kodów przez usługę konwersji.

Usługa konwersji powiedzie się, jeśli wystąpią jakieś ostrzeżenia dotyczące konwersji. Zaleca się jednak przejrzenie i rozwiązanie wszystkich ostrzeżeń. Ostrzeżenie oznacza, że część konwersji została zignorowana lub automatycznie naprawiona. Niepowodzenie rozpoznania ostrzeżeń może spowodować błędy w ostatnim procesie.

## <a name="general-warnings"></a>Ogólne ostrzeżenia

### <a name="geometrywarning"></a>**geometryWarning**

#### <a name="description-for-geometrywarning"></a>*Opis elementu geometryWarning*

**GeometryWarning** występuje, gdy rysunek zawiera nieprawidłową jednostkę. Nieprawidłowa jednostka jest jednostką, która nie jest zgodna z ograniczeniami geometrycznymi. Przykładami nieprawidłowej jednostki jest Wielokąt samodzielny lub niezamknięta łamana w warstwie, która obsługuje tylko zamkniętą geometrię.

Usługa konwersji nie może utworzyć funkcji mapy z nieprawidłowej jednostki i zamiast tego ją ignoruje.

#### <a name="examples-for-geometrywarning"></a>*Przykłady dla geometryWarning*

* Dwa obrazy poniżej pokazują przykłady wieloskładnikowych wielokątów.

     ![Przykład samodzielnego wielokąta, przykładu.](./media/drawing-conversion-error-codes/geometry-warning-1.png)

     ![Przykład samodzielnego wielokątu, przykład dwa.](./media/drawing-conversion-error-codes/geometry-warning-2.png)

* Poniżej znajduje się obraz, który pokazuje niezamkniętą linię łamaną. Załóżmy, że warstwa obsługuje tylko zamkniętą geometrię.

    ![Przykład niezamkniętej linii łamanej](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-geometrywarning"></a>*Jak naprawić geometryWarning*

Sprawdź **geometryWarning** dla każdej jednostki, aby sprawdzić, czy obowiązują ograniczenia geometryczne.

### <a name="unexpectedgeometryinlayer"></a>**unexpectedGeometryInLayer**

#### <a name="description-for-unexpectedgeometryinlayer"></a>*Opis elementu unexpectedGeometryInLayer*

Ostrzeżenie **unexpectedGeometryInLayer** występuje, gdy rysunek zawiera geometrię, która jest niezgodna z oczekiwanym typem geometrii dla danej warstwy. Gdy usługa konwersji zwróci ostrzeżenie **unexpectedGeometryInLayer** , spowoduje to zignorowanie tej geometrii.

#### <a name="example-for-unexpectedgeometryinlayer"></a>*Przykład dla unexpectedGeometryInLayer*

Poniższy obraz przedstawia niezamkniętą linię łamaną. Załóżmy, że warstwa obsługuje tylko zamkniętą geometrię.

![Przykład niezamkniętej linii łamanej](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-unexpectedgeometryinlayer"></a>*Jak naprawić unexpectedGeometryInLayer*

Sprawdź wszystkie ostrzeżenia **unexpectedGeometryInLayer** i Przenieś niezgodną geometrię do zgodnej warstwy. Jeśli nie jest zgodny z żadną z pozostałych warstw, należy ją usunąć.

### <a name="unsupportedfeaturerepresentation"></a>**unsupportedFeatureRepresentation**

#### <a name="description-for-unsupportedfeaturerepresentation"></a>*Opis elementu unsupportedFeatureRepresentation*

Ostrzeżenie **unsupportedFeatureRepresentation** występuje, gdy rysunek zawiera nieobsługiwany typ jednostki.

#### <a name="example-for-unsupportedfeaturerepresentation"></a>*Przykład dla unsupportedFeatureRepresentation*

Poniższy obraz przedstawia nieobsługiwany typ jednostki jako obiekt tekstu wielowierszowego w warstwie etykiet.
  
![Przykładowy obiekt tekstu wielowierszowego w warstwie etykiet](./media/drawing-conversion-error-codes/multi-line.png)

#### <a name="how-to-fix-unsupportedfeaturerepresentation"></a>*Jak naprawić unsupportedFeatureRepresentation*

Upewnij się, że pliki DWG zawierają tylko obsługiwane typy jednostek. Obsługiwane typy są wymienione w [sekcji wymagania dotyczące plików rysunku w artykule wymagania pakietu rysowania](drawing-requirements.md#drawing-package-requirements).

### <a name="automaticrepairperformed"></a>**automaticRepairPerformed**

#### <a name="description-for-automaticrepairperformed"></a>*Opis elementu automaticRepairPerformed*

Ostrzeżenie **automaticRepairPerformed** występuje, gdy usługa konwersji automatycznie naprawia nieprawidłową geometrię.

#### <a name="examples-for-automaticrepairperformed"></a>*Przykłady dla automaticRepairPerformed*

* Na poniższej ilustracji przedstawiono, w jaki sposób usługa konwersji naprawi samodzielny wielokąt do prawidłowej geometrii.

    ![Przykład Naprawiono samodzielnego wielokąta](./media/drawing-conversion-error-codes/automatic-repair-1.png)

* Poniższy obraz pokazuje, jak usługa konwersji przypięta pierwszy i ostatni wierzchołek niezamkniętej linii łamanej w celu utworzenia zamkniętej linii łamanej, gdzie pierwszy i ostatni wierzchołek były mniejsze niż 1 mm.  

    ![Przykład przypiętej linii łamanej](./media/drawing-conversion-error-codes/automatic-repair-2.png)

* Na poniższym obrazie pokazano, jak w warstwie, która obsługuje tylko zamknięte linie łamane, usługa konwersji naprawia wiele niezamkniętych linii łamanej. Aby uniknąć odrzucania niezamkniętych linii łamanej, usługa połączy je w jedną zamkniętą łamaną.

    ![Przykład niezamkniętych linii łamane połączone w jedną zamkniętą łamaną](./media/drawing-conversion-error-codes/automatic-repair-3.png)

#### <a name="how-to-fix-automaticrepairperformed"></a>*Jak naprawić automaticRepairPerformed*

Aby naprawić ostrzeżenie **automaticRepairPerformed** , wykonaj następujące czynności:

1. Sprawdzanie geometrii poszczególnych ostrzeżeń i określonego tekstu ostrzegawczego.
2. Ustal, czy automatyczna naprawa jest poprawna.
3. Jeśli naprawa jest poprawna, Kontynuuj. W przeciwnym razie przejdź do pliku projektu i ręcznie usuń ostrzeżenie.

>[!TIP]
>Aby pominąć ostrzeżenie w przyszłości, wprowadź zmiany w oryginalnym rysunku, aby oryginalny rysunek pasował do naprawionego rysunku.

## <a name="manifest-warnings"></a>Ostrzeżenia manifestu

### <a name="redundantattribution"></a>**redundantAttribution**

#### <a name="description-for-redundantattribution"></a>*Opis elementu redundantAttribution*

Ostrzeżenie **redundantAttribution** występuje, gdy manifest zawiera nadmiarowe lub sprzeczne właściwości obiektu.

#### <a name="examples-for-redundantattribution"></a>*Przykłady dla redundantAttribution*

* Poniższy fragment kodu JSON zawiera dwa lub więcej `unitProperties` obiektów o tej samej `name` .

    ```json
    "unitProperties": [
        {
            "unitName": "L1-100",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        }
    ]
    ```

* W poniższym fragmencie kodu JSON co najmniej dwa `zoneProperties` obiekty są takie same `name` .

    ```json
     "zoneProperties": [
        {
            "zoneName": "Assembly Area 1",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        }
    ```

#### <a name="how-to-fix-redundantattribution"></a>*Jak naprawić redundantAttribution*

Aby naprawić **redundantAttribution* ostrzeżenie, Usuń nadmiarowe lub sprzeczne właściwości obiektu.

### <a name="manifestwarning"></a>**manifestWarning**

#### <a name="description-for-manifestwarning"></a>*Opis elementu manifestWarning*

**ManifestWarning** występuje, gdy manifest zawiera obiekty UnitProperties lub zoneProperties, które są nieużywane podczas konwersji.

#### <a name="examples-for-manifestwarning"></a>*Przykłady dla manifestWarning*

* Manifest zawiera `unitProperties` obiekt z `unitName` niezgodnymi etykietami w `unitLabel` warstwie.

* Manifest zawiera `zoneProperties` obiekt z `zoneName` niezgodnymi etykietami w `zoneLabel` warstwie.

#### <a name="how-to-fix-manifestwarning"></a>*Jak naprawić manifestWarning*

Aby naprawić **manifestWarning**, Usuń nieużywane `unitProperties` lub `zoneProperties` obiekt z manifestu lub Dodaj etykietę jednostki/strefy do rysunku, aby obiekt właściwości był używany podczas konwersji.

## <a name="wall-warnings"></a>Ostrzeżenia ściany

### <a name="walloutsidelevel"></a>**wallOutsideLevel**

#### <a name="description-for-walloutsidelevel"></a>*Opis elementu wallOutsideLevel*

Ostrzeżenie **wallOutsideLevel** występuje, gdy rysowanie zawiera geometrię ściany poza granicami konspektu poziomu.

#### <a name="example-for-walloutsidelevel"></a>*Przykład dla wallOutsideLevel*

* Na poniższej ilustracji przedstawiono ścianę wewnątrz, na czerwono, poza granicą żółtego poziomu.

    ![Przykład ściany wewnętrznej poza granicą poziomu](./media/drawing-conversion-error-codes/wall-outside-level.png)

* Na poniższej ilustracji przedstawiono ścianę zewnętrzną, na czerwono, poza granicą żółtego poziomu.

    ![Przykład zewnętrznej ściany poza granicą poziomu](./media/drawing-conversion-error-codes/wall-outside-level-exterior.png)

#### <a name="how-to-fix-walloutsidelevel"></a>*Jak naprawić wallOutsideLevel*

Aby naprawić ostrzeżenie **wallOutsideLevel** , rozwiń geometrię poziomu, aby uwzględnić wszystkie ściany. Lub zmodyfikuj granice ściany, aby mieściły się w granicach poziomu.

## <a name="unit-warnings"></a>Ostrzeżenia jednostki

### <a name="unitoutsidelevel"></a>**unitOutsideLevel**

#### <a name="description-for-unitoutsidelevel"></a>*Opis elementu unitOutsideLevel*

Ostrzeżenie **unitOutsideLevel** występuje, gdy rysunek zawiera geometrię jednostki poza granicami konspektu poziomu.

#### <a name="example-for-unitoutsidelevel"></a>*Przykład dla unitOutsideLevel*

 Na poniższej ilustracji geometria jednostki, w kolorze czerwonym, przekracza granice granicy żółtego poziomu.

 ![Przykład jednostki przekraczającej granicę poziomu](./media/drawing-conversion-error-codes/unit-outside-level.png)

#### <a name="how-to-fix-unitoutsidelevel"></a>*Jak naprawić unitOutsideLevel*

Aby naprawić ostrzeżenie **unitOutsideLevel** , rozwiń granicę poziomu w celu uwzględnienia wszystkich jednostek. Lub zmodyfikuj geometrię jednostki, aby dopasować ją do granicy poziomu.

### <a name="partiallyoverlappingunit"></a>**partiallyOverlappingUnit**

#### <a name="description-for-partiallyoverlappingunit"></a>*Opis elementu partiallyOverlappingUnit*

Ostrzeżenie **partiallyOverlappingUnit** występuje, gdy rysunek zawiera geometryczną jednostkę częściowo pokrywającą się z inną geometryczną jednostką. Usługa konwersji odrzuca nakładające się jednostki.

#### <a name="example-scenarios-partiallyoverlappingunit"></a>*Przykładowe scenariusze partiallyOverlappingUnit*

Na poniższej ilustracji nakładająca się jednostka zostanie wyróżniona kolorem czerwonym. `UNIT110` i `HALLWAY` są odrzucane.

![Przykład nakładających się jednostek](./media/drawing-conversion-error-codes/partially-overlapping-unit.png)

#### <a name="how-to-fix-partiallyoverlappingunit"></a>*Jak naprawić partiallyOverlappingUnit*

Aby naprawić ostrzeżenie **partiallyOverlappingUnit** , narysuj ponownie każdą częściowo nakładającą się jednostkę, tak aby nie zachodziła żadna inna jednostka.

## <a name="door-warnings"></a>Ostrzeżenia dotyczące drzwi

### <a name="dooroutsidelevel"></a>**doorOutsideLevel**

#### <a name="description-for-dooroutsidelevel"></a>*Opis elementu doorOutsideLevel*

Ostrzeżenie **doorOutsideLevel** występuje, gdy rysowanie zawiera geometrię drzwi poza granicami geometrii poziomu.

#### <a name="example-for-dooroutsidelevel"></a>*Przykład dla doorOutsideLevel*

Na poniższej ilustracji geometria drzwi wyróżniona kolorem czerwonym nakłada się na granicę żółtego poziomu.

![Przykład drzwi nakładających się na granicę poziomu](./media/drawing-conversion-error-codes/door-outside-level.png)

#### <a name="how-to-fix-dooroutsidelevel"></a>*Jak naprawić doorOutsideLevel*

Aby naprawić ostrzeżenie **doorOutsideLevel** , ponownie narysuj geometrię drzwi, tak aby była w granicach poziomu.

## <a name="zone-warnings"></a>Ostrzeżenia dotyczące stref

### <a name="zonewarning"></a>**zoneWarning**

#### <a name="description-for-zonewarning"></a>*Opis elementu zoneWarning*

**ZoneWarning** występuje, gdy strefa nie zawiera etykiety. Usługa konwersji odrzuca strefę, która nie ma etykiety. l

#### <a name="example-for-zonewarning"></a>*Przykład dla zoneWarning*

Na poniższej ilustracji przedstawiono strefę, która nie zawiera etykiety.

![Przykład strefy nie zawiera etykiety](./media/drawing-conversion-error-codes/zone-warning.png)

#### <a name="how-to-fix-zonewarning"></a>*Jak naprawić zoneWarning*

Aby naprawić **zoneWarning**, upewnij się, że każda strefa ma jedną etykietę.

## <a name="label-warnings"></a>Ostrzeżenia etykiet

### <a name="labelwarning"></a>*labelWarning*

#### <a name="description-for-labelwarning"></a>*Opis elementu labelWarning*

**LabelWarning** występuje, gdy rysunek zawiera niejednoznaczną lub sprzeczną funkcję etykiet.

**LabelWarning** występuje z powodu co najmniej jednego z następujących powodów:

* Etykieta jednostki nie znajduje się w żadnej jednostce.
* Etykieta strefy nie znajduje się w żadnej strefie.
* Etykieta strefy znajduje się w co najmniej dwóch strefach.

#### <a name="example-for-labelwarning"></a>*Przykład dla labelWarning*

Na poniższej ilustracji przedstawiono etykietę, która znajduje się wewnątrz dwóch stref.

![Przykład etykiety w dwóch strefach ](./media/drawing-conversion-error-codes/label-warning.png)

#### <a name="how-to-fix-labelwarning"></a>*Jak naprawić labelWarning*

Aby naprawić **labelWarning**, upewnij się, że:

* Wszystkie etykiety jednostek są w jednostkach.
* Wszystkie etykiety stref znajdują się wewnątrz stref.
* Wszystkie etykiety stref znajdują się w jednej i tylko jednej strefie.

## <a name="drawing-package-errors"></a>Błędy pakietu rysowania

### <a name="invalidarchiveformat"></a>**invalidArchiveFormat**

#### <a name="description-for-invalidarchiveformat"></a>*Opis elementu invalidArchiveFormat*

Błąd **invalidArchiveFormat** występuje, gdy pakiet rysowania znajduje się w nieprawidłowym formacie archiwum, takim jak gzip lub 7-zip. Obsługiwany jest tylko format archiwum ZIP.

Błąd **invalidArchiveFormat** również występuje, gdy archiwum zip jest puste.

#### <a name="how-to-fix-invalidarchiveformat"></a>*Jak naprawić invalidArchiveFormat*

Aby naprawić błąd **invalidArchiveFormat** , sprawdź, czy:

* Nazwa pliku archiwum zostanie zakończona na _. zip_.
* Archiwum ZIP zawiera dane.
* Możesz otworzyć archiwum ZIP.

### <a name="invaliduserdata"></a>**invalidUserData**

#### <a name="description-for-invaliduserdata"></a>*Opis elementu invalidUserData*

Błąd **invalidUserData** występuje, gdy usługa konwersji nie może odczytać obiektu danych użytkownika z magazynu.

#### <a name="example-scenario-for-invaliduserdata"></a>*Przykładowy scenariusz dla invalidUserData*

Podjęto próbę przekazania pakietu rysowania z nieprawidłowym `udid` parametrem.

#### <a name="how-to-fix-invaliduserdata"></a>*Jak naprawić invalidUserData*

Aby naprawić błąd **invalidUserData** , sprawdź, czy:

* Podano poprawny `udid` dla przekazanego pakietu.
* Azure Maps Creator został włączony dla konta Azure Maps użytego do przekazania pakietu rysowania.
* Żądanie interfejsu API do usługi konwersji zawiera klucz subskrypcji do konta Azure Maps użytego do przekazania pakietu rysowania.

### <a name="dwgerror"></a>**dwgError**

#### <a name="description-for-dwgerror"></a>*Opis elementu dwgError*

**DwgError** , gdy pakiet rysowania zawiera problem z co najmniej jednym plikem DWG w przekazanym archiwum zip.

**DwgError** występuje, gdy pakiet rysowania zawiera plik DWG, którego nie można otworzyć, ponieważ jest nieprawidłowy lub uszkodzony.

* Plik DWG nie jest prawidłowym rysunkiem formatu pliku programu AutoCAD DWG.
* Plik DWG jest uszkodzony.
* Plik DWG znajduje się na liście _manifest.jsw_ pliku, ale nie ma go w archiwum zip.

#### <a name="how-to-fix-dwgerror"></a>*Jak naprawić dwgError*

Aby naprawić **dwgError**, sprawdź, czy _manifest.jsw_ pliku upewnij się, że:

* Wszystkie pliki DWG w archiwum ZIP są prawidłowymi rysunkami formatu AutoCAD DWG, otwierają je w programie AutoCAD. Usuń lub Napraw wszystkie nieprawidłowe rysunki.
* Lista plików DWG w _manifest.jsna_  dopasowuje się do plików DWG w archiwum zip.

## <a name="manifest-errors"></a>Błędy manifestu

### <a name="invalidjsonformat"></a>**invalidJsonFormat**

#### <a name="description-for-invalidjsonformat"></a>Opis elementu invalidJsonFormat

Błąd **invalidJsonFormat** występuje, gdy nie można odczytać _manifest.jsw_ pliku.

On_file _manifest.jsnie można odczytać z powodu błędu formatowania lub składni JSON. Aby dowiedzieć się więcej na temat sposobu formatowania i składni JSON, zobacz [Format wymiany danych JavaScript Object Notation (JSON).](https://tools.ietf.org/html/rfc7159)

#### <a name="how-to-fix-invalidjsonformat"></a>*Jak naprawić invalidJsonFormat*

Aby naprawić błąd **invalidJsonFormat** , użyj Linter JSON do wykrywania i rozwiązywania błędów JSON.

### <a name="missingrequiredfield"></a>**missingRequiredField**

#### <a name="description-for-missingrequiredfield"></a>*Opis elementu missingRequiredField*

Błąd **missingRequiredField** występuje, gdy brakuje wymaganych danych _manifest.jsw_ pliku.

#### <a name="how-to-fix-missingrequiredfield"></a>*Jak naprawić missingRequiredField*

Aby naprawić błąd **missingRequiredField** , sprawdź, czy manifest zawiera wszystkie wymagane właściwości. Aby zapoznać się z pełną listą wymaganego obiektu manifestu, zapoznaj się z [sekcją manifestu w artykule wymagania pakietu rysowania](drawing-requirements.md#manifest-file-requirements)  

### <a name="missingmanifest"></a>**missingManifest**

#### <a name="description-for-missingmanifest"></a>*Opis elementu missingManifest*

Błąd **missingManifest** występuje, gdy brakuje _manifest.jsw_ pliku w archiwum zip.

Błąd **missingManifest** jest spowodowany jedną z następujących przyczyn:

* _manifest.js_ pliku jest błędnie wpisana.
* Brak _manifest.js_ .
* _manifest.json_ nie znajduje się w katalogu głównym archiwum zip.

#### <a name="how-to-fix-missingmanifest"></a>*Jak naprawić missingManifest*

Aby naprawić błąd **missingManifest** , upewnij się, że archiwum ma plik o nazwie _manifest.jsna_ poziomie głównym archiwum zip.

### <a name="conflict"></a>**kolizj**

#### <a name="description-for-conflict"></a>*Opis konfliktu*

Błąd **konfliktu** występuje, gdy _manifest.jsw_ pliku zawiera informacje powodujące konflikt.

#### <a name="example-scenario-for-conflict"></a>*Przykładowy scenariusz dla konfliktu*

Usługa konwersji zwróci błąd **konfliktu** , gdy zostanie zdefiniowany więcej niż jeden poziom z numerem porządkowym tego samego poziomu. Poniższy fragment kodu JSON przedstawia dwa poziomy zdefiniowane z tą samą liczbą porządkową.

```JSON
"buildingLevels":
{
    "levels": [
        {
            "levelName": "Ground",
            "ordinal": 0,
            "filename": "./Level_0.dwg"
        },
        {
            "levelName": "Parking",
            "ordinal": 0,
            "filename": "./Level_P.dwg"
        }
    ]
}
```

#### <a name="how-to-fix-conflict"></a>*Jak rozwiązać konflikt*

Aby naprawić błąd **konfliktu** , zbadaj _manifest.js_ i Usuń wszelkie informacje powodujące konflikt.

### <a name="invalidgeoreference"></a>**invalidGeoreference**

#### <a name="description-for-invalidgeoreference"></a>*Opis elementu invalidGeoreference*

Błąd **invalidGeoreference** występuje, gdy _manifest.jsw_ pliku zawiera nieprawidłowe geoodwołanie.

Błąd **invalidGeoreference** jest spowodowany jedną z następujących przyczyn:

* Użytkownik odwołuje się do wartości szerokości geograficznej lub długości geograficznej, która znajduje się poza zakresem.
* Użytkownik odwołuje się do wartości obrotu, która jest poza zakresem.

#### <a name="example-scenario-for-invalidgeoreference"></a>*Przykładowy scenariusz dla invalidGeoreference*

W poniższym fragmencie kodu JSON wartość szerokości geograficznej przekracza górny limit.

```json
"georeference"
{
    "lat": 88.0,
    "lon": -122.132600,
    "angle": 0
},
```

#### <a name="how-to-fix-invalidgeoreference"></a>*Jak naprawić invalidGeoreference*

Aby naprawić błąd **invalidGeoreference** , sprawdź, czy wartości georeferencyjne znajdują się w zakresie.

>[!IMPORTANT]
>W GeoJSON kolejność współrzędnych ma wartość długość i szerokość geograficzną. Jeśli nie używasz odpowiedniej kolejności, możesz przypadkowo odwołać się do wartości szerokości geograficznej lub długości geograficznej, która jest poza zakresem.

## <a name="wall-errors"></a>Błędy ściany

### <a name="wallerror"></a>**wallError**

#### <a name="description-for-wallerror"></a>*Opis elementu wallError*

**WallError** występuje, gdy rysunek zawiera błąd podczas próby utworzenia funkcji ściany.

#### <a name="example-scenario-for-wallerror"></a>*Przykładowy scenariusz dla wallError*

Na poniższej ilustracji przedstawiono funkcję ściany, która nie nakłada się na żadne jednostki.

![Przykład funkcji ściany, która nie nakłada się na żadną jednostkę](./media/drawing-conversion-error-codes/wall-error.png)

#### <a name="how-to-fix-wallerror"></a>*Jak naprawić wallError*

Aby naprawić błąd **wallError** , narysuj ponownie ścianę, aby pokrywał się z co najmniej jedną jednostką. Lub Utwórz nową jednostkę, która pokrywa się z ścianą.

## <a name="vertical-penetration-errors"></a>Błędy penetracji pionowej

### <a name="verticalpenetrationerror"></a>**verticalPenetrationError**

#### <a name="description-for-verticalpenetrationerror"></a>*Opis elementu verticalPenetrationError*

**VerticalPenetrationError** występuje, gdy rysunek zawiera niejednoznaczną funkcję penetracji pionowej.

**VerticalPenetrationError** występuje z powodu co najmniej jednego z następujących powodów:

* Rysunek zawiera pionowy obszar penetracji bez nakładających się obszarów penetracji pionowej na żadnym z poziomów powyżej lub poniżej.
* Pakiet rysowania zawiera poziom z co najmniej dwiema pionowymi funkcjami penetracji, które jednocześnie nakładają się na jedną pionową funkcję penetracji na innym poziomie bezpośrednio powyżej lub poniżej.

#### <a name="example-scenario-for-verticalpenetrationerror"></a>*Przykładowy scenariusz dla verticalPenetrationError*

Poniższy obraz pokazuje pionowy obszar penetracji bez nakładających się obszarów penetracji pionowej na poziomach powyżej lub poniżej.

![Przykład penetracji pionowej 1](./media/drawing-conversion-error-codes/vrt-2.png)

Na poniższej ilustracji przedstawiono pionowy obszar penetracji, który nakłada się na więcej niż jeden pionowy obszar penetracji na sąsiednim poziomie.

![Przykład penetracji pionowej 2](./media/drawing-conversion-error-codes/vrt-1.png)

#### <a name="how-to-fix-verticalpenetrationerror"></a>Jak naprawić verticalPenetrationError

Aby naprawić błąd **verticalPenetrationError** , przeczytaj artykuł dotyczący sposobu korzystania z funkcji penetracji pionowej w artykule [wymagania pakietu rysowania](drawing-requirements.md) .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jak używać wizualizatora błędów rysowania Azure Maps](drawing-error-visualizer.md)

> [!div class="nextstepaction"]
> [Kreator dla mapowania pomieszczeń](creator-indoor-maps.md)