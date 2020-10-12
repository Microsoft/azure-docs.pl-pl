---
title: Wymagania dotyczące pakietu rysowania w programie Azure Maps Creator
description: Dowiedz się więcej o wymaganiach dotyczących pakietów rysowania w celu przekonwertowania plików projektu funkcji na dane mapy
author: anastasia-ms
ms.author: v-stharr
ms.date: 6/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: 1f25aadf716b7768b6122a4fb165466aef7f8a16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90053396"
---
# <a name="drawing-package-requirements"></a>Wymagania dotyczące pakietu do rysowania

Przekazane pakiety rysowania można przekonwertować na dane mapy za pomocą [usługi konwersji Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion). W tym artykule opisano wymagania dotyczące pakietu rysowania dla interfejsu API konwersji. Aby wyświetlić przykładowy pakiet, można pobrać przykładowy [pakiet do rysowania](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="prerequisites"></a>Wymagania wstępne

Pakiet rysowania zawiera rysunki zapisane w formacie DWG, który jest natywnym formatem plików dla programu Autodesk AutoCAD®.

Możesz wybrać każde oprogramowanie CAD, aby utworzyć rysunki w pakiecie rysunku.  

[Usługa konwersji Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) konwertuje pakiet rysowania na dane mapy. Usługa konwersji współdziała z formatem pliku programu AutoCAD DWG. `AC1032` to wewnętrzna wersja formatu dla plików DWG. dobrym pomysłem jest wybranie opcji `AC1032` wersja formatu pliku w formacie wewnętrznym.  

## <a name="glossary-of-terms"></a>Słownik

Aby uzyskać łatwe odwołanie, poniżej przedstawiono niektóre terminy i definicje, które są ważne podczas czytania tego artykułu.

| Okres  | Definicja |
|:-------|:------------|
| Warstwa | Warstwa programu AutoCAD DWG.|
| Poziom | Obszar budynku z zestawem podniesienia uprawnień. Na przykład piętro budynku. |
| Linki XREF  |Plik w formacie programu AutoCAD DWG (. dwg), dołączony do podstawowego rysunku jako odwołanie zewnętrzne.  |
| Cecha | Obiekt, który łączy geometrię z dodatkowymi informacjami o metadanych. |
| Klasy funkcji | Typowy plan dla funkcji. Na przykład *Jednostka* jest klasą funkcji, a *pakiet Office* jest funkcją. |

## <a name="drawing-package-structure"></a>Struktura pakietu rysowania

Pakiet rysowania to archiwum zip, które zawiera następujące pliki:

* Pliki DWG w formacie pliku DWG programu AutoCAD.
* _manifest.jsw_ pliku dla pojedynczej funkcji.

Pliki DWG można organizować w dowolny sposób wewnątrz folderu, ale plik manifestu musi znajdować się w katalogu głównym folderu. Musisz mieć folder zip w jednym pliku archiwum z rozszerzeniem. zip. W następnych sekcjach szczegółowo przedstawiono wymagania dotyczące plików DWG, pliku manifestu i zawartości tych plików.  

## <a name="dwg-files-requirements"></a>Wymagania dotyczące plików DWG

Dla każdego poziomu funkcji wymagany jest pojedynczy plik DWG. Dane poziomu muszą być zawarte w pojedynczym pliku DWG. Wszystkie odwołania zewnętrzne (_XREFs_) muszą być powiązane z rysowaniem nadrzędnym. Ponadto każdy plik DWG:

* Należy zdefiniować warstwy _zewnętrzne_ i _jednostkowe_ . Opcjonalnie można zdefiniować następujące warstwy opcjonalne: _ściany_, _drzwi_, _UnitLabel_, _strefy_i _ZoneLabel_.
* Nie może zawierać funkcji z wielu poziomów.
* Nie może zawierać funkcji z wielu obiektów.

[Usługa konwersji Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) może wyodrębnić następujące klasy funkcji z pliku DWG:

* Poziomy
* Lekcji
* Strefy
* Wakaty
* Połącz
* Penetracja pionowa

Wszystkie zadania konwersji powodują minimalny zestaw kategorii domyślnych: pomieszczenie, struktura. ściany, otwieranie. drzwi, strefa i obiekt. Dodatkowe kategorie są dla każdej nazwy kategorii, do której odwołują się obiekty.  

Warstwa DWG musi zawierać funkcje pojedynczej klasy. Klasy nie mogą udostępniać warstwy. Na przykład jednostki i ściany nie mogą udostępniać warstwy.

Warstwy DWG muszą również spełniać następujące kryteria:

* Źródła rysunków dla wszystkich plików DWG muszą być wyrównane do tej samej szerokości i długości geograficznej.
* Każdy poziom musi być w tej samej orientacji co inne poziomy.
* Wielokąty samodzielne są automatycznie naprawiane, a [Usługa konwersji Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) zgłasza ostrzeżenie. Należy ręcznie sprawdzić naprawione wyniki, ponieważ mogą one być niezgodne z oczekiwanymi wynikami.

Wszystkie jednostki warstwy muszą być jednym z następujących typów: liniowy, łamaną, Wielokąt, łuk okręgu, okrąg lub tekst (pojedynczy wiersz). Wszystkie inne typy jednostek są ignorowane.

W poniższej tabeli przedstawiono obsługiwane typy jednostek i obsługiwane funkcje dla każdej warstwy. Jeśli warstwa zawiera nieobsługiwane typy jednostek, [Usługa konwersji Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) ignoruje te jednostki.  

| Warstwa | Typy jednostek | Funkcje |
| :----- | :-------------------| :-------
| [Krawężnik](#exterior-layer) | Wielokąt, łamane (zamknięte), okrąg | Poziomy
| [Jednostka](#unit-layer) |  Wielokąt, łamane (zamknięte), okrąg | Penetracja pionowa, jednostki
| [Osłon](#wall-layer)  | Wielokąt, łamane (zamknięte), okrąg | Nie dotyczy. Aby uzyskać więcej informacji, zobacz [ścianka warstwy](#wall-layer).
| [Drzwi](#door-layer) | Wielokąt, łamana, linia, CircularArc, okrąg | Wakaty
| [Strefa](#zone-layer) | Wielokąt, łamane (zamknięte), okrąg | Strefa
| [UnitLabel](#unitlabel-layer) | Tekst (pojedynczy wiersz) | Nie dotyczy. Ta warstwa może dodawać tylko właściwości do funkcji jednostki z warstwy jednostki. Aby uzyskać więcej informacji, zobacz [warstwę UnitLabel](#unitlabel-layer).
| [ZoneLabel](#zonelabel-layer) | Tekst (pojedynczy wiersz) | Nie dotyczy. Ta warstwa może dodawać tylko właściwości do funkcji strefy z ZonesLayer. Aby uzyskać więcej informacji, zobacz [warstwę ZoneLabel](#zonelabel-layer).

W następnych sekcjach opisano wymagania dotyczące każdej warstwy.

### <a name="exterior-layer"></a>Warstwa zewnętrzna

Plik DWG dla każdego poziomu musi zawierać warstwę, aby zdefiniować obwód tego poziomu. Ta warstwa jest określana jako warstwa *zewnętrzna* . Na przykład jeśli obiekt zawiera dwa poziomy, musi mieć dwa pliki DWG z warstwą zewnętrzną dla każdego pliku.

Niezależnie od tego, ile rysunków jednostek znajduje się w warstwie zewnętrznej, [wynikający z nich zestaw danych funkcji](tutorial-creator-indoor-maps.md#create-a-feature-stateset) będzie zawierać tylko jeden poziom funkcji dla każdego pliku DWG. Dodatkowo:

* Zewnętrzne muszą być rysowane jako Wielokąt, łamane (zamknięte) lub koło.
* Zewnętrzne dane mogą się nakładać, ale są rozwiązane w jednej geometrii.

Jeśli warstwa zawiera wiele nakładających się linii, linie łamane są rozwiązane w funkcji jednego poziomu. Alternatywnie, jeśli warstwa zawiera wiele nienakładających się linii przecinających, funkcja na poziomie wyniku ma wielowielokątną reprezentację.

Przykładową warstwę zewnętrzna można zobaczyć jako warstwę konspektu w [przykładowym pakiecie rysowania](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unit-layer"></a>Warstwa jednostkowa

Plik DWG dla każdego poziomu definiuje warstwę zawierającą jednostki. Jednostki są miejscami do nawigacji w budynku, takie jak biura, korytarzach, schody i windy. Warstwa jednostek powinna być zgodna z następującymi wymaganiami:

* Jednostki muszą być rysowane jako Wielokąt, łamane (zamknięte) lub koło.
* Jednostki muszą przypadać w granicach zewnętrznego obwodu funkcji.
* Jednostki nie mogą częściowo nakładać się na siebie.
* Jednostki nie mogą zawierać żadnej własnej geometrii.

Nadaj nazwę jednostce przez utworzenie obiektu tekstowego w warstwie UnitLabel, a następnie umieść obiekt wewnątrz granic jednostki. Aby uzyskać więcej informacji, zobacz [warstwę UnitLabel](#unitlabel-layer).

Można zobaczyć przykład warstwy jednostki w [przykładowym pakiecie rysowania](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="wall-layer"></a>Warstwa ścian

Plik DWG dla każdego poziomu może zawierać warstwę, która definiuje fizyczne zakresy ścian, kolumn i innych struktur konstrukcyjnych.

* Ściany muszą być rysowane jako Wielokąt, łamane (zamknięte) lub koło.
* Warstwa ścian lub warstwy powinna zawierać tylko geometrię, która jest interpretowana jako struktura budynku.

Przykład warstwy ściany można zobaczyć w przykładowym [pakiecie rysowania](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="door-layer"></a>Warstwa drzwi

Możesz dołączyć warstwę DWG zawierającą drzwi. Każde drzwi muszą nakładać się na brzeg jednostki z warstwy jednostkowej.

Otwarte drzwi w zestawie danych Azure Maps są reprezentowane jako segment jednowierszowy, który nakłada się na wiele granic jednostek. Na poniższych ilustracjach pokazano, jak przekonwertować geometrię w warstwie drzwi, aby otworzyć funkcje w zestawie danych.

![Cztery grafiki pokazujące kroki umożliwiające generowanie otwartych](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Warstwa strefy

Plik DWG dla każdego poziomu może zawierać warstwę strefy, która definiuje fizyczne zakresy stref. Strefa może być pustą spacją lub z tyłu.

* Strefy muszą być rysowane jako Wielokąt, łamane (zamknięte) lub koło.
* Strefy mogą nakładać się na siebie.
* Strefy mogą znajdować się wewnątrz lub poza obwodem zewnętrznym obiektu.

Nazwij strefę, tworząc obiekt tekstu w warstwie ZoneLabel i umieszczając obiekt tekstowy wewnątrz granic strefy. Aby uzyskać więcej informacji, zobacz [warstwa ZoneLabel](#zonelabel-layer).

Można zobaczyć przykład warstwy strefy w [przykładowym pakiecie rysowania](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unitlabel-layer"></a>Warstwa UnitLabel

Plik DWG dla każdego poziomu może zawierać warstwę UnitLabel. Warstwa UnitLabel dodaje właściwość Name do jednostek wyodrębnionych z warstwy jednostkowej. Jednostki o właściwości Name mogą zawierać dodatkowe szczegóły określone w pliku manifestu.

* Etykiety jednostek muszą być jednostkami tekstu jednowierszowego.
* Etykiety jednostek muszą znajdować się wewnątrz granic ich jednostek.
* Jednostki nie mogą zawierać wielu jednostek tekstu w warstwie UnitLabel.

Przykład warstwy UnitLabel można zobaczyć w przykładowym [pakiecie rysowania](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="zonelabel-layer"></a>Warstwa ZoneLabel

Plik DWG dla każdego poziomu może zawierać warstwę ZoneLabel. Ta warstwa dodaje właściwość Name do stref wyodrębnionych z warstwy strefy. Strefy z właściwością nazwa mogą zawierać dodatkowe szczegóły określone w pliku manifestu.

* Etykiety stref muszą być jednostkami tekstu jednowierszowego.
* Etykiety stref muszą znajdować się wewnątrz granic swojej strefy.
* Strefy nie mogą zawierać wielu jednostek tekstu w warstwie ZoneLabel.

Przykład warstwy ZoneLabel można zobaczyć w przykładowym [pakiecie rysowania](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="manifest-file-requirements"></a>Wymagania dotyczące pliku manifestu

Folder zip musi zawierać plik manifestu na poziomie głównym katalogu, a plik musi mieć nazwę **manifest.jsna**. Opisano w nim pliki DWG umożliwiające przeanalizowanie zawartości przez [usługę konwersji Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) . Zostaną pozyskane tylko pliki zidentyfikowane przez manifest. Pliki znajdujące się w folderze ZIP, ale nie są poprawnie wymienione w manifeście, są ignorowane.

Ścieżki plików w `buildingLevels` obiekcie manifestu pliku muszą być względne względem katalogu głównego folderu zip. Nazwa pliku DWG musi być dokładnie zgodna z nazwą poziomu funkcji. Na przykład plik DWG dla poziomu "Basement" ma wartość "Basement. dwg". Plik DWG dla poziomu 2 ma nazwę "level_2. dwg". Jeśli nazwa poziomu zawiera spację, użyj znaku podkreślenia.

Chociaż istnieją wymagania dotyczące korzystania z obiektów manifestu, nie wszystkie obiekty są wymagane. W poniższej tabeli przedstawiono wymagane i opcjonalne obiekty w wersji 1,1 [usługi konwersji Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion).

| Obiekt | Wymagane | Opis |
| :----- | :------- | :------- |
| `version` | true |Wersja schematu manifestu. Obecnie obsługiwana jest tylko wersja 1,1.|
| `directoryInfo` | true | Przedstawia informacje o lokalizacji geograficznej i kontakcie. Można go również użyć do zaprojektowania geograficznego i informacji kontaktowych. |
| `buildingLevels` | true | Określa poziomy budynków i pliki zawierające projekt poziomów. |
| `georeference` | true | Zawiera liczbowe informacje geograficzne dla rysowania obiektu. |
| `dwgLayers` | true | Wyświetla listę nazw warstw, a każda warstwa zawiera nazwy własnych funkcji. |
| `unitProperties` | fałsz | Może służyć do wstawiania dodatkowych metadanych dla funkcji jednostkowych. |
| `zoneProperties` | fałsz | Może służyć do wstawiania dodatkowych metadanych dla funkcji strefy. |

W następnych sekcjach szczegółowo przedstawiono wymagania dla każdego obiektu.

### `directoryInfo`

| Właściwość  | Typ | Wymagane | Opis |
|-----------|------|----------|-------------|
| `name`      | ciąg | true   |  Nazwa budynku. |
| `streetAddress`|    ciąg |    fałsz    | Adres budynku. |
|`unit`     | ciąg    |  fałsz    |  Trwa Kompilowanie. |
| `locality` |    ciąg |    fałsz |    Nazwa obszaru, klubu lub regionu. Na przykład "przewidziano" lub "Region Centralny". Miejscowość nie jest częścią adresu wysyłkowego. |
| `adminDivisions` |    Tablica JSON ciągów |    fałsz     | Tablica zawierająca oznaczenia adresów (kraj, Województwo, miasto) lub (kraj, Prefektura, miasto, miejscowość). Użyj kodów kraju ISO 3166 i kodów stanu/regionu ISO 3166-2. |
| `postalCode` |    ciąg    | fałsz    | Kod sortowania poczty. |
| `hoursOfOperation` |    ciąg |     fałsz | Stosuje się do formatu [godzin otwarcia OSM](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification) . |
| `phone`    | ciąg |    fałsz |    Numer telefonu skojarzony z kompilowaniem. Musi zawierać kod kraju. |
| `website`    | ciąg |    fałsz    | Witryna internetowa skojarzona z kompilowaniem. Musi zaczynać się od http lub https. |
| `nonPublic` |    bool    | fałsz | Flaga określająca, czy kompilacja jest otwarta do publicznej. |
| `anchorLatitude` | numeryczne |    fałsz | Szerokość i zakotwiczenie obiektu (pinezki). |
| `anchorLongitude` | numeryczne |    fałsz | Długość dokotwiczenia obiektu (pinezki). |
| `anchorHeightAboveSeaLevel`  | numeryczne | fałsz | Wysokość podłogi gruntowej w obiekcie powyżej poziomu morza w metrach. |
| `defaultLevelVerticalExtent` | numeryczne | fałsz | Domyślna wysokość (grubość) poziomu tej funkcji, która ma być używana, gdy poziom `verticalExtent` jest niezdefiniowany. |

### `buildingLevels`

`buildingLevels`Obiekt zawiera tablicę poziomów budynków w formacie JSON.

| Właściwość  | Typ | Wymagane | Opis |
|-----------|------|----------|-------------|
|`levelName`    |ciąg    |true |    Nazwa poziomu w opisie. Na przykład: piętro 1, poczekalni, niebieskie parkingi lub Basement.|
|`ordinal` | liczba całkowita |    true | Określa kolejność poziomów w pionie. Każda funkcja musi mieć poziom z liczbą porządkową 0. |
|`heightAboveFacilityAnchor` | numeryczne | fałsz |    Wysokość poziomu nad kotwicą w metrach. |
| `verticalExtent` | numeryczne | fałsz | Wysokość do sufitu (grubość) poziomu w licznikach. |
|`filename` |    ciąg |    true |    Ścieżka systemu plików rysunku CAD dla poziomu budynku. Musi być względem katalogu głównego pliku zip budynku. |

### `georeference`

| Właściwość  | Typ | Wymagane | Opis |
|-----------|------|----------|-------------|
|`lat`    | numeryczne |    true |    Reprezentacja dziesiętna stopni szerokości geograficznej na początku rysowania obiektu. Współrzędne pochodzenia muszą znajdować się w WGS84 Web Merkatora ( `EPSG:3857` ).|
|`lon`    |numeryczne|    true|    Reprezentacja dziesiętna wartości długości geograficznej w pochodzeniu rysowania obiektu. Współrzędne pochodzenia muszą znajdować się w WGS84 Web Merkatora ( `EPSG:3857` ). |
|`angle`|    numeryczne|    true|   Kąt ruchowy (w stopniach) między rzeczywistą i pionową osią na rysunku (Y).   |

### `dwgLayers`

| Właściwość  | Typ | Wymagane | Opis |
|-----------|------|----------|-------------|
|`exterior`    |tablica ciągów|    true|    Nazwy warstw definiujące zewnętrzny profil budynku.|
|`unit`|    tablica ciągów|    true|    Nazwy warstw definiujące jednostki.|
|`wall`|    tablica ciągów    |fałsz|    Nazwy warstw definiujące ściany.|
|`door`    |tablica ciągów|    fałsz   | Nazwy warstw, które definiują drzwi.|
|`unitLabel`    |tablica ciągów|    fałsz    |Nazwy warstw, które definiują nazwy jednostek.|
|`zone` | tablica ciągów    | fałsz    | Nazwy warstw, które definiują strefy.|
|`zoneLabel` | tablica ciągów |     fałsz |    Nazwy warstw, które definiują nazwy stref.|

### `unitProperties`

`unitProperties`Obiekt zawiera tablicę z właściwościami jednostki JSON.

| Właściwość  | Typ | Wymagane | Opis |
|-----------|------|----------|-------------|
|`unitName`    |ciąg    |true    |Nazwa jednostki do skojarzenia z tym `unitProperty` rekordem. Ten rekord jest prawidłowy tylko wtedy, gdy `unitName` w warstwach znajduje się dopasowanie do etykiet `unitLabel` . |
|`categoryName`|    ciąg|    fałsz    |Nazwa kategorii. Aby uzyskać pełną listę kategorii, zobacz [Kategorie](https://aka.ms/pa-indoor-spacecategories). |
|`navigableBy`| tablica ciągów |    fałsz    |Wskazuje typy agentów nawigacyjnych, które mogą przechodzić przez jednostkę. Ta właściwość informuje o możliwościach wayfinding. Dozwolone wartości to:,,,,,,,,,, `pedestrian` `wheelchair` `machine` `bicycle` `automobile` `hiredAuto` `bus` `railcar` `emergency` `ferry` `boat` , i `disallowed` .|
|`routeThroughBehavior`|    ciąg|    fałsz    |Zachowanie trasy dla jednostki. Dozwolone wartości to `disallowed` , `allowed` , i `preferred` . Wartość domyślna to `allowed`.|
|`occupants`    |Tablica obiektów directoryInfo |fałsz    |Lista osób zajmujących się jednostką. |
|`nameAlt`|    ciąg|    fałsz|    Alternatywna nazwa jednostki. |
|`nameSubtitle`|    ciąg    |fałsz|    Podtytuł jednostki. |
|`addressRoomNumber`|    ciąg|    fałsz|    Numer pomieszczenia, jednostki, Apartament lub zestawu jednostki.|
|`verticalPenetrationCategory`|    ciąg|    fałsz| Gdy ta właściwość jest zdefiniowana, wynikiem funkcji jest penetracja pionowa (VRT), a nie jednostka. Możesz użyć VRTs, aby przejść do innych funkcji VRT na poziomach powyżej lub poniżej. Penetracja pionowa to nazwa [kategorii](https://aka.ms/pa-indoor-spacecategories) . Jeśli ta właściwość jest zdefiniowana, `categoryName` Właściwość zostanie zastąpiona przez `verticalPenetrationCategory` . |
|`verticalPenetrationDirection`|    ciąg|    fałsz    |Jeśli `verticalPenetrationCategory` jest zdefiniowany, opcjonalnie Zdefiniuj prawidłowy kierunek podróży. Dozwolone wartości to: `lowToHigh` , `highToLow` , `both` i `closed` . Wartość domyślna to `both`.|
| `nonPublic` | bool | fałsz | Wskazuje, czy jednostka jest otwarta publicznie. |
| `isRoutable` | bool | fałsz | Gdy ta właściwość jest ustawiona na `false` , nie można przejść do jednostki ani przez nią. Wartość domyślna to `true`. |
| `isOpenArea` | bool | fałsz | Zezwala agentowi nawigacyjnemu na przejście do jednostki bez konieczności otwierania dołączonej do jednostki. Domyślnie ta wartość jest ustawiona na `true` dla jednostek bez otwartych i `false` dla jednostek z otwartymi. Ręczne ustawienie `isOpenArea` `false` w jednostce bez otwartych wyników powoduje ostrzeżenie. Wynika to z faktu, że jednostka, która nie będzie osiągalna przez agenta nawigowania.|

### `zoneProperties`

`zoneProperties`Obiekt zawiera tablicę JSON właściwości strefy.

| Właściwość  | Typ | Wymagane | Opis |
|-----------|------|----------|-------------|
|zoneName        |ciąg    |true    |Nazwa strefy do skojarzenia z `zoneProperty` rekordem. Ten rekord jest prawidłowy tylko wtedy, gdy `zoneName` w warstwie strefy znaleziono dopasowanie do etykiet `zoneLabel` .  |
|categoryName|    ciąg|    fałsz    |Nazwa kategorii. Aby uzyskać pełną listę kategorii, zobacz [Kategorie](https://aka.ms/pa-indoor-spacecategories). |
|zoneNameAlt|    ciąg|    fałsz    |Alternatywna nazwa strefy.  |
|zoneNameSubtitle|    ciąg |    fałsz    |Podtytuł strefy. |
|zoneSetId|    ciąg |    fałsz    | Ustaw identyfikator, aby ustanowić relację między wieloma strefami, aby można było wykonywać zapytania lub wybierać jako grupę. Na przykład strefy obejmujące wiele poziomów. |

### <a name="sample-drawing-package-manifest"></a>Przykładowy manifest pakietu rysowania

Poniżej znajduje się przykładowy plik manifestu dla przykładowego pakietu do rysowania. Aby pobrać cały pakiet, zobacz [przykładowy pakiet rysowania](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

#### <a name="manifest-file"></a>Plik manifestu

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddress": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>Następne kroki

Gdy pakiet rysowania spełnia wymagania, można użyć [usługi konwersji Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) , aby skonwertować pakiet do zestawu danych mapy. Następnie można użyć zestawu danych do wygenerowania mapy pomieszczeń przy użyciu modułu Maps.

> [!div class="nextstepaction"]
>[Kreator dla map pomieszczeń](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie mapy pomieszczeń dla twórców](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Dynamiczne style map](indoor-map-dynamic-styling.md)
