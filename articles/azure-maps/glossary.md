---
title: Słownik Azure Maps | Microsoft Docs
description: Słownik często używanych terminów związanych z usługami Azure Maps, Location-Based Services i GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 05071be4fc1705787a649d331613e8d9399f983f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92897181"
---
# <a name="glossary"></a>Słownik

Poniższa lista zawiera opis typowych słów używanych z usługami Azure Maps.

## <a name="a"></a>A

<a name="address-validation"></a>**Weryfikacja adresów**: proces weryfikacji istnienia adresu.

<a name="advanced-routing"></a>**Zaawansowane Routing**: kolekcja usług, które wykonują operacje z wyprzedzeniem przy użyciu danych routingu drogowego; takie jak obliczanie zakresów osiągalnych (izochron), macierzy odległości i żądań tras wsadowych.

<a name="aerial-imagery"></a>**Zdjęcia z anteny**: Zobacz zdjęcia [satelitarne](#satellite-imagery). 

<a name="along-a-route-search"></a>**Wyszukiwanie przy użyciu trasy**: zapytanie przestrzenne, które wyszukuje dane w określonym czasie lub odległości od ścieżki trasy.

<a name="altitude"></a>**Wysokość**: wysokość lub pozioma poszerzenie punktu nad powierzchnią odniesienia. Pomiary wysokości są oparte na danym wymiarze odniesienia, takim jak średni poziom morza. Zobacz również podniesienie uprawnień.

<a name="ambiguous"></a>**Niejednoznaczny**: stan niepewności klasyfikacji danych, który istnieje, gdy obiekt może być odpowiednio przypisany do dwóch lub więcej wartości dla danego atrybutu. Na przykład podczas geokodowania "CA" zwracane są dwa niejednoznaczne wyniki: "Kanada" i "California". "CA" jest krajem/regionem i kodem stanu, odpowiednio dla "Kanada" i "California". 

<a name="annotation"></a>**Adnotacja**: tekst lub grafika wyświetlana na mapie, aby zapewnić użytkownikowi informacje. Adnotacja może identyfikować lub opisywać określoną jednostkę mapy, podawać ogólne informacje o obszarze mapy lub podawać informacje o samej mapie.

<a name="antimeridian"></a>- **Południka**: znana także <sup>jako 180name</sup> . Jest to punkt, gdzie-180 stopni i 180 stopni długości geograficznej. Jest to przeciwieństwo do podstawy Południowej na świecie.

<a name="application-programming-interface-api"></a>**Interfejs programowania aplikacji (API)**: Specyfikacja, która umożliwia deweloperom tworzenie aplikacji.

<a name="api-key"></a>**Klucz interfejsu API**: zobacz [uwierzytelnianie klucza wspólnego](#shared-key-authentication).

<a name="area-of-interest-aoi"></a>**Obszar zainteresowania (AOI)**: zakres używany do definiowania obszaru koncentracji dla środowiska produkcyjnego mapy lub bazy danych.

<a name="asset-tracking"></a>**Śledzenie zasobów**: proces śledzenia lokalizacji elementu zawartości, na przykład osoby, pojazdu lub innego obiektu.

<a name="asynchronous-request"></a>**Żądanie asynchroniczne**: żądanie HTTP, które otwiera połączenie i wysyła żądanie do serwera zwracającego identyfikator dla żądania asynchronicznego, a następnie zamyka połączenie. Serwer nadal przetwarza żądanie, a użytkownik może sprawdzić stan przy użyciu identyfikatora. Po zakończeniu przetwarzania żądania użytkownik może pobrać odpowiedź. Ten typ żądania jest używany do długotrwałych procesów.

<a name="autocomplete"></a>**Autouzupełnianie**: funkcja w aplikacji, która przewiduje pozostałą część wyrazu wpisywaną przez użytkownika. 

<a name="autosuggest"></a>Automatyczne **sugerowanie**: funkcja w aplikacji, która przewiduje logiczne możliwości wpisywania przez użytkownika.

<a name="azure-location-based-services-lbs"></a>**Azure Location based Services (funt)**: wcześniejsza nazwa Azure Maps, gdy była w wersji zapoznawczej.

<a name="azure-active-directory"></a>**Azure Active Directory (Azure AD)**: usługa Azure AD jest usługą zarządzania tożsamościami i dostępem opartymi na chmurze firmy Microsoft. Azure Maps integracja z usługą Azure AD jest obecnie dostępna w wersji zapoznawczej dla wszystkich Azure Maps interfejsów API. Usługa Azure AD obsługuje funkcję kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure w celu umożliwienia szczegółowego dostępu do zasobów Azure Maps. Aby dowiedzieć się więcej na temat Azure Maps integracji z usługą Azure AD, zobacz [Azure Maps i usługi Azure AD](azure-maps-authentication.md) oraz [Zarządzanie uwierzytelnianiem w Azure Maps](how-to-manage-authentication.md).

<a name="azure-maps-key"></a>**Klucz Azure Maps**: zobacz [uwierzytelnianie klucza wspólnego](#shared-key-authentication).

## <a name="b"></a>B

<a name="base-map"></a>**Mapa podstawowa**: część aplikacji mapy, która wyświetla informacje o odwołaniach w tle, takie jak drogi, punkty orientacyjne i granice polityczne.

<a name="batch-request"></a>**Żądanie wsadowe**: proces łączenia wielu żądań w jedno żądanie.

<a name="bearing"></a>**Mając**: poziomy kierunek punktu w odniesieniu do innego punktu. Jest to wyrażone jako kąt względem północy, od 0 do 360 stopni w kierunku w prawo. 

<a name="boundary"></a>**Granica**: linia lub Wielokąt oddzielający przyległe jednostki polityczne, takie jak kraje/regiony, okręgi i właściwości. Granica to linia, która może być niezgodna z funkcjami fizycznymi, takimi jak rzek, górach czy ściany.

<a name="bounds"></a>**Ograniczenia**: zobacz [obwiednia](#bounding-box).

<a name="bounding-box"></a>**Pole ograniczenia**: zestaw współrzędnych używany do reprezentowania prostokątnego obszaru na mapie. 

## <a name="c"></a>C

<a name="cadastre"></a>**Cadastre**: rejestr zarejestrowanych gruntów i właściwości. Zobacz również artykuł [Parcel](#parcel).

<a name="camera"></a>**Aparat fotograficzny**: w kontekście interaktywnej kontrolki mapy aparat definiuje pole Maps widoku. Okienko ekranu aparatu jest określane na podstawie kilku parametrów mapy: centrum, poziom powiększenia, gęstość, z uwzględnieniem. 

<a name="centroid"></a>**Centroida**: środek geometryczny funkcji. Centroidaem wiersza będzie punkt środkowy, podczas gdy centroida wielokąta będzie centrum obszaru.

<a name="choropleth-map"></a>**Na mapie choropleth**: mapa tematyczna, w której obszary są cieniowane proporcjonalnie do miary zmiennej statystycznej. Ta zmienna statystyczna jest wyświetlana na mapie. Na przykład kolorowanie granicy każdego stanu Stanów Zjednoczonych w oparciu o jego względną populację na wszystkie inne Stany.

<a name="concave-hull"></a>**Kadłub**: kształt, który reprezentuje możliwą geometryczną geometrię, która zawiera wszystkie kształty w określonym zestawie danych. Wygenerowany kształt jest podobny do zawijania danych z otoką plastikową, a następnie podgrzewania go, co sprawia, że duże zakresy między punktami mogą być wklęsłe w kierunku innych punktów danych.

<a name="consumption-model"></a>**Model użycia**: informacje definiujące szybkość, z jaką pojazd zużywa paliwo lub energię elektryczną. Zapoznaj się również z [dokumentacją modelu zużycia](consumption-model.md).

<a name="control"></a>**Kontrolka**: składnik samodzielny lub wielokrotnego użytku składający się z graficznego interfejsu użytkownika, który definiuje zestaw zachowań dla interfejsu. Na przykład kontrolka mapy jest ogólnie częścią interfejsu użytkownika, który ładuje mapę interaktywną.

<a name="convex-hull"></a>**Kadłub wypukły**: kadłub wypukły to kształt reprezentujący minimalną geometrię wypukłą, która zawiera wszystkie kształty w określonym zestawie danych. Wygenerowany kształt jest podobny do zawijania elastycznego pasma wokół zestawu danych.

<a name="coordinate"></a>**Koordynuj**: składa się z wartości długości geograficznej i szerokości geograficznej używanej do reprezentowania lokalizacji na mapie.

<a name="coordinate-system"></a>**Układ współrzędnych**: Struktura referencyjna służąca do definiowania pozycji punktów w przestrzeni w dwóch lub trzech wymiarach.

<a name="country-code"></a>**Kod kraju**: unikatowy identyfikator kraju/regionu oparty na standardzie ISO. ISO2 to dwuznakowy kod dla kraju/regionu (na przykład US), który ISO3 reprezentuje kod składający się z trzech znaków (na przykład USA).

<a name="country-subdivision"></a>Obszar **podziału kraju**: pierwszy poziom części kraju/regionu, często znany jako Województwo.

<a name="country-secondary-subdivision"></a>**Pomocnicza jednostka podziału kraju**: Dwupoziomowa część kraju/regionu, często znana jako powiat.

<a name="country-tertiary-subdivision"></a>**Podpodział**(trzeciego szczebla): Dwupoziomowa część kraju/regionu, zazwyczaj nazwany obszar, taki jak do wewnątrz.

<a name="cross-street"></a>**Krzyżyk**: punkt, w którym dwa lub więcej ulice przecinają się.

<a name="cylindrical-projection"></a>**Rzutowanie cylindryczne**: projekcja, która przekształca punkty z spheroid lub sfery na cylinder styczny lub secans. Cylinder jest następnie pofragmentowany od góry do dołu i spłaszczony do płaszczyzny.

## <a name="d"></a>D

<a name="datum"></a>**Datum**: specyfikacje referencyjne systemu pomiarowego, system położenia układu współrzędnych na powierzchni (w pionie) lub wysokości powyżej lub poniżej powierzchni (wymiar pionowy).

<a name="dbf-file"></a>**DBF**: plik bazy danych, który jest używany w połączeniu z plikami kształtów (SHP).

<a name="degree-minutes-seconds-dms"></a>**Stopień minut w sekundach (DMS)**: jednostka miary opisująca szerokość i długość geograficzną. Stopień to 1/360<sup>ty</sup> koła. Stopień jest dalej podzielony na 60 minut, a minuta jest dzielona na 60 sekund.

<a name="delaunay-triangulation"></a>**Triangulacja Delaunay**: technika tworzenia siatki ciągłego, nienakładających się trójkątów z zestawu danych punktów. Okrąg circumscribing każdego trójkąta nie zawiera żadnych punktów z zestawu danych w jego wnętrzu.

<a name="demographics"></a>Dane **demograficzne**: cechy statystyczne (takie jak wiek, wskaźnik urodzenia i dochód) populacji ludzkiej.

<a name="destination"></a>**Miejsce docelowe**: punkt końcowy lub lokalizacja, w której ktoś jest Podróżujący.

<a name="digital-elevation-model-dem"></a>**Model podniesienia uprawnień cyfrowych (DEM)**: zestaw danych z wartościami podniesienia uprawnień związanymi z powierzchnią, przechwyconych przez obszar w regularnych odstępach czasu przy użyciu wspólnego punktu odniesienia. DEMs są zwykle używane do reprezentowania ulgi terenowej.

<a name="dijkstra's-algorithm"></a>**Algorytm Dijkstra**: algorytm, który analizuje łączność sieci, aby znaleźć najkrótszą ścieżkę między dwoma punktami.

<a name="distance-matrix"></a>**Macierz odległości**: macierz, która zawiera czas podróży i informacje o odległości między zestawem źródeł i miejscami docelowymi. 

## <a name="e"></a>E

<a name="elevation"></a>**Podniesienie poziomu**: pionowa odległość punktu lub obiektu powyżej lub poniżej wymiarowanej powierzchni lub środka odniesienia. Ogólnie rzecz biorąc, Powierzchnia odniesienia to poziom morza. Podniesienie uprawnień ogólnie odnosi się do pionowej wysokości terenu.

<a name="envelope"></a>**Koperta**: zobacz [pole ograniczenia](#bounding-box).

<a name="extended-postal-code"></a>**Rozszerzony kod pocztowy**: kod pocztowy, który może zawierać dodatkowe informacje. Na przykład w Stanach Zjednoczonych kody ZIP mają pięć cyfr. Jednak rozszerzony kod pocztowy znany jako zip + 4 zawiera cztery dodatkowe cyfry. Te dodatkowe cyfry są używane do identyfikowania segmentu geograficznego w obrębie obszaru dostarczania pięciu cyfr, takiego jak blok miasto, Grupa apartamentach lub Skrytka pocztowa. Znajomość poznania segmentów geograficznych w efektywnym sortowaniu i dostarczaniu poczty.

<a name="extent"></a>**Zakres**: zobacz [obwiednia](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a>**Uwierzytelnianie federacyjne**: Metoda uwierzytelniania, która umożliwia użycie jednego mechanizmu logowania/uwierzytelniania w wielu aplikacjach sieci Web i mobilnych. 

<a name="feature"></a>**Funkcja**: obiekt łączący geometrię z dodatkowymi informacjami o metadanych. 

<a name="feature-collection"></a>**Kolekcja funkcji**: Kolekcja obiektów funkcji.

<a name="find-along-route"></a>**Znajdź wzdłuż trasy**: zapytanie przestrzenne, które wyszukuje dane znajdujące się w określonym czasie lub odległości od ścieżki trasy.

<a name="find-nearby"></a>**Znajdź w pobliżu**: zapytanie przestrzenne, które przeszukuje stałą odległość prostą (jak w linii prostej) od punktu.

<a name="fleet-management"></a>**Zarządzanie flotą**: Zarządzanie samochodami komercyjnymi, takimi jak samochody, samochody, statki i płaszczyzny. Zarządzanie flotą może obejmować szereg funkcji, takich jak finansowanie pojazdów, konserwacja, telematyka (śledzenie i Diagnostyka), jak również sterownik, szybkość, paliwo i kondycja oraz zarządzanie bezpieczeństwem. Zarządzanie flotą jest procesem używanym przez firmy, które opierają się na transportach w firmie. Firmy chcą zminimalizować ryzyko i obniżyć ich ogólny koszt transportu i personelu, zapewniając zgodność z przepisami obowiązującymi w instytucji rządowych.

<a name="free-flow-speed"></a>**Bezpłatna szybkość przepływu**: oczekiwana szybkość przepływu w warunkach idealnych. Zwykle jest to limit szybkości.

<a name="free-form-address"></a>**Adres w postaci bezpłatnej**: pełny adres, który jest reprezentowany jako pojedynczy wiersz tekstu.

<a name="fuzzy-search"></a>**Wyszukiwanie rozmyte**: wyszukiwanie, które zajmuje bezpłatny ciąg tekstowy, który może być adresem lub punktem zainteresowania. 

## <a name="g"></a>G

<a name="geocode"></a>**Geokod**: adres lub lokalizacja, która została przekonwertowana na współrzędną, która może być używana do wyświetlania tej lokalizacji na mapie. 

<a name="geocoding"></a>**Geokodowanie**: jest to również nazywane geokodowaniem geokodowania, to proces konwersji adresów danych lokalizacji na współrzędne. 

<a name="geodesic-path"></a>**Ścieżka geodesic**: najkrótsza ścieżka między dwoma punktami na zakrzywionej powierzchni. Gdy jest renderowany na Azure Maps Ta ścieżka pojawia się jako linia zakrzywiona ze względu na projekcję Merkatora.

<a name="geofence"></a>**Geoogrodzenie**: zdefiniowany region geograficzny, który może służyć do wyzwalania zdarzeń, gdy urządzenie przejdzie do regionu lub już istnieje.

<a name="geojson"></a>**GEOJSON**: jest typowym FORMATEM pliku JSON używanym do przechowywania danych wektorów geograficznych, takich jak punkty, linie i wielokąty. **Uwaga**: Azure Maps używa rozszerzonej wersji GEOJSON, jak [opisano tutaj](extend-geojson.md).

<a name="geometry"></a>**Geometria**: reprezentuje obiekt przestrzenny, taki jak punkt, linia lub Wielokąt.

<a name="geometrycollection"></a>**GeometryCollection**: Kolekcja obiektów geometrycznych.

<a name="geopol"></a>**GeoPol**: odnosi się do danych geopolitycznych poufnych, takich jak obramowania sporne i nazwy miejsc.

<a name="georeference"></a>**Georeferencja**: proces wyrównywania danych geograficznych lub obrazów do znanego systemu współrzędnych. Ten proces może obejmować przesuwanie, obracanie, skalowanie lub pochylanie danych.

<a name="georss"></a>**GeoRSS**: rozszerzenie XML umożliwiające dodawanie danych przestrzennych do kanałów informacyjnych RSS.

<a name="gis"></a>**GIS**: akronim dla "systemu informacji geograficznej". Wspólny termin używany do opisywania branży mapowania.

<a name="gml"></a>**GML**: znany również jako język znaczników geograficznych. Rozszerzenie pliku XML do przechowywania danych przestrzennych.

<a name="gps"></a>**GPS**: znany również jako GPS, to system satelitów służący do określania pozycji urządzenia na ziemi. Na stronie orbity satelity są przesyłane sygnały umożliwiające odbiornikowi GPS w dowolnym miejscu na ziemi, aby obliczyć własną lokalizację za pośrednictwem trilateration.

<a name="gpx"></a>**GPX**: jest również znany jako format GPS Exchange Format pliku XML często tworzony z urządzeń GPS.  

<a name="great-circle-distance"></a>**Odległość**: najkrótsza odległość między dwoma punktami na powierzchni sfery.

<a name="greenwich-mean-time-gmt"></a>**Czas uniwersalny Greenwich (GMT)**: czas od pierwszego południka, który jest uruchamiany przez Obserwatorium wzdziału w Greenwich, Anglii.

<a name="guid"></a>**Identyfikator GUID**: unikatowy identyfikator globalny. Ciąg służący do unikatowego identyfikowania interfejsu, klasy, biblioteki typów, kategorii składników lub rekordu.

## <a name="h"></a>H

<a name="haversine-formula"></a>**Formuła Haversine**: typowe równanie używane do obliczania odległości między dwoma punktami w sferze.

<a name="hd-maps"></a>**Mapy HD**: są również znane jako mapy o wysokiej rozdzielczości, składają się z informacji o wysokiej wierności sieci dróg, takich jak oznaczenia Lane, znakowanie i światła kierunkowe wymagane do samodzielnego prowadzenia.

<a name="heading"></a>**Nagłówek**: kierunek jest wskazywany lub skierowany. Zobacz również [temat](#heading).

<a name="heatmap"></a>**Mapę cieplną**: Wizualizacja danych, w której zakres kolorów reprezentuje gęstość punktów w konkretnym obszarze. Zobacz również plan tematyczny.

<a name="hybrid-imagery"></a>Obrazy **hybrydowe**: Zdjęcia satelitarne lub satelitarne, które mają nałożone na siebie dane drogowe i etykiety.

## <a name="i"></a>I

<a name="iana"></a>**Organizacja Iana**: akronim do urzędu przypisanych numerów internetowych. Grupa niedochodowa, która widzi globalną alokację adresów IP.

<a name="isochrone"></a>**Isochrone**: isochrone definiuje obszar, w którym ktoś może podróżować w określonym czasie dla trybu transportu w dowolnym kierunku z danej lokalizacji. Zobacz też [osiągalny zakres](#reachable-range).

<a name="isodistance"></a>**Isodistance**: podano lokalizację, a isochrone definiuje obszar, w którym ktoś może poruszać się w określonej odległości dla trybu transportu w dowolnym kierunku. Zobacz też [osiągalny zakres](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a>**KML**: znany również jako język oznakowania, jest typowym FORMATEM pliku XML do przechowywania danych wektorów geograficznych, takich jak punkty, linie i wielokąty. 

## <a name="l"></a>L

<a name="landsat"></a>**LANDSAT**: wielowidmowe i ziemskie satelity opracowane przez NASA, które gromadzą obraz terenu. Ten obraz jest używany w wielu branżach, takich jak rolnictwo, Leśnictwo i Cartography.

<a name="latitude"></a>**Szerokość geograficzna**: odległość kątowa mierzona w stopniach od równika w kierunku północnym lub południe.

<a name="level-of-detail"></a>**Poziom szczegółowości**: Zobacz poziom powiększenia.

<a name="lidar"></a>**LIDAR**: akronim dla wykrywania światła i zakresu. Technika zdalna, która używa laserów do mierzenia odległości do powierzchni odbijających.

<a name="linear-interpolation"></a>**Interpolacja liniowa**: oszacowanie nieznanej wartości przy użyciu odległości liniowej między znanymi wartościami.

<a name="linestring"></a>**LineString**: geometria użyta do reprezentowania wiersza. Nazywana również linią łamaną. 

<a name="localization"></a>**Lokalizacja**: Obsługa różnych języków i kultur.

<a name="logistics"></a>**Logistyka**: proces przemieszczania ludzi, pojazdów, dostaw lub zasobów w skoordynowany sposób.

<a name="longitude"></a>**Długość geograficzna**: odległość kątowa mierzona w stopniach od zachodniej południka w kierunku wschodnim lub zachodnim.

## <a name="m"></a>M

<a name="map-tile"></a>**Kafelek mapy**: prostokątny obraz reprezentujący partycję kanwy mapy. Aby uzyskać więcej informacji, zobacz [poziomy powiększenia i dokumentacja siatki kafelków](zoom-levels-and-tile-grid.md).

<a name="marker"></a>**Znacznik**: znany również jako numer PIN lub Pinezka, jest ikoną reprezentującą lokalizację punktu na mapie.

<a name="mercator-projection"></a>**Rzutowanie Merkatora**: Rzutowanie mapy cylindrycznej, która stała się tablicą standardową, w celach morskich ze względu na możliwość reprezentowania linii stałych kursów, znanych jako linie Rhumb, jako segmenty proste, które współużytkują kąty z południki. Wszystkie projekcje prostej mapy zniekształcają kształty lub rozmiary mapy w porównaniu do rzeczywistego układu powierzchni ziemi. Projekcja Merkatora exaggeratesa obszary daleko od równika, tak że mniejsze obszary pojawiają się na mapie w miarę podejścia do Poles. 

<a name="multilinestring"></a>**MultiLineString**: geometria, która reprezentuje kolekcję obiektów LineString. 

<a name="multipoint"></a>**MultiPoint**: geometria, która reprezentuje kolekcję obiektów punktu.

<a name="multipolygon"></a>**MultiPolygon**: geometria, która reprezentuje kolekcję obiektów wielokąt. Na przykład, aby pokazać granicę Hawaje, każda wyspa zostanie zakreślona wielokątem. W rezultacie granica Hawaje powinna być MultiPolygon.

<a name="municipality"></a>**Gmina**: miasto lub miejscowość. 

<a name="municipality-subdivision"></a>**Podpodział (gmina miejska**): część gminy, na przykład nazwę klubu lub lokalnego obszaru, na przykład "centrum".

## <a name="n"></a>N

<a name="navigation-bar"></a>**Pasek nawigacyjny**: zestaw kontrolek na mapie używany do dostosowywania poziomu powiększenia, skoku, obrotu i przełączania podstawowej warstwy mapy.

<a name="nearby-search"></a>**Wyszukiwanie w pobliżu**: zapytanie przestrzenne, które przeszukuje stałą odległość prostą (jak w linii prostej) z punktu.

<a name="neutral-ground-truth"></a>**Neutralna wartość prawdy**: Mapa, która renderuje etykiety w języku urzędowym regionu, który reprezentuje i w lokalnych skryptach, jeśli jest dostępny.

## <a name="o"></a>O

<a name="origin"></a>**Źródło**: punkt początkowy lub lokalizacja, w której znajduje się użytkownik.

## <a name="p"></a>P

<a name="panning"></a>**Panoramowanie**: proces przesuwania mapy w dowolnym kierunku przy zachowaniu stałego poziomu powiększenia.

<a name="parcel"></a>**Działka**: Działka gruntu lub właściwości.

<a name="pitch"></a>**Gęstość**: wielkość pochylenia dla mapy względem wartości pionowej, gdzie 0 oznacza prostą w dół na mapie.

<a name="point"></a>**Point**: geometria reprezentująca pojedynczą pozycję na mapie. 

<a name="points-of-interest-poi"></a>**Punkty orientacyjne (punkt POI)**: działalność, dzielnica lub typowe miejsce.

<a name="polygon"></a>**Wielokąt**: solidna geometria, która reprezentuje obszar na mapie. 

<a name="polyline"></a>**Łamana**: geometria użyta do reprezentowania wiersza. Znana także jako LineString. 

<a name="position"></a>**Położenie**: Długość geograficzna, Szerokość geograficzna i wysokość (współrzędne x, y, z) punktu.

<a name="post-code"></a>**Kod post**: zobacz [Kod pocztowy](#postal-code).

<a name="postal-code"></a>**Kod pocztowy**: Seria liter lub cyfr lub oba w określonym formacie. Kod pocztowy jest używany przez usługę pocztową kraju/regionu do dzielenia obszarów geograficznych na strefy w celu uproszczenia dostarczania poczty.

<a name="primary-key"></a>**Klucz podstawowy**: pierwsze dwa klucze subskrypcji udostępniane do Azure Maps uwierzytelniania klucza współużytkowanego. Zobacz [uwierzytelnianie klucza wspólnego](#shared-key-authentication).

<a name="prime-meridian"></a>**Podstawowa południka**: wiersz długości geograficznej reprezentujący 0 stopni długości geograficznej. Ogólnie rzecz biorąc, wartości długości geograficznej zmniejszają się w czasie, gdy podróżują w kierunku zachodnim do 180 180 stopni 

<a name="prj"></a>**PRJ**: plik tekstowy, który często towarzyszy plikowi kształtu zawierającego informacje o przewidywanym systemie współrzędnych, w których znajduje się zestaw danych.

<a name="projection"></a>**Projekcja**: rzutowany układ współrzędnych oparty na projekcji mapy, takiej jak Merkatora poprzeczny, Albers równy obszar i Robinson. Zapewniają one możliwość projektu mapy sferycznej powierzchni ziemi na dwuwymiarowej płaszczyźnie współrzędnych kartezjańskiego. Planowane systemy współrzędnych są czasami określane jako projekcje map.

## <a name="q"></a>Q

<a name="quadkey"></a>**Quadkey**: podstawowy indeks adresu dla kafelka w systemie QuadTree. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [poziomów powiększenia i siatki kafelków](zoom-levels-and-tile-grid.md) , aby uzyskać więcej informacji.

<a name="quadtree"></a>**QuadTree**: struktura danych, w której każdy węzeł ma dokładnie cztery elementy podrzędne. System rozmieszczania używany w Azure Maps używa struktury QuadTree, takiej jak powiększenie użytkownika na jednym poziomie, a każdy kafelek mapy dzieli się na cztery kafelki.  Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [poziomów powiększenia i siatki kafelków](zoom-levels-and-tile-grid.md) , aby uzyskać więcej informacji.

<a name="queries-per-second-qps"></a>**Zapytania na sekundę (zapytań)**: liczba zapytań lub żądań, które można wykonać w usłudze lub platformie w jednej sekundzie. 

## <a name="r"></a>R

<a name="radial-search"></a>**Wyszukiwanie promieniowe**: zapytanie przestrzenne, które przeszukuje stałą odległość liniową (jak w linii prostej) z punktu. 

<a name="raster-data"></a>**Dane rastrowe**: Macierz komórek (lub pikseli) zorganizowanych w wiersze i kolumny (lub siatka), gdzie każda komórka zawiera wartość reprezentującą informacje, takie jak temperatura. Grafiki rastrowe obejmują fotografie cyfrowe, obrazy z satelitów, zdjęć cyfrowych i zeskanowanych map.

<a name="raster-layer"></a>**Warstwa rastrowa**: warstwa kafelków, która składa się z obrazów rastrowych.

<a name="reachable-range"></a>**Osiągalny zakres**: dostępny zakres definiuje obszar, w którym ktoś może poruszać się w określonym czasie lub na odległość, w przypadku trybu transportu do podróży w dowolnym kierunku od lokalizacji. Zobacz również [isochrone](#isochrone) i [Isodistance](#isodistance).

<a name="remote-sensing"></a>**Wykrywanie zdalne**: proces zbierania i interpretowania danych czujników z odległości.

<a name="rest-service"></a>**Usługa REST**: akronim jest rozmieszczeniem dla reprezentacji stanu. Usługa REST to oparta na adresach URL usługa sieci Web, która opiera się na podstawowej technologii sieci Web do komunikowania się, najczęściej używane metody HTTP GET i POST. Te typy usług są znacznie szybsze i mniejsze niż tradycyjne usługi oparte na protokole SOAP.

<a name="reverse-geocode"></a>**Odwrócony geokod**: proces podejmowania współrzędnych i ustalania adresu, który jest reprezentowany na mapie.

<a name="reproject"></a>**Przeprojektowanie**: zobacz [transformacja](#transformation).

<a name="rest-service"></a>**Usługa REST**: akronim w przypadku przenoszenia stanu dla reprezentacji. Architektura wymiany informacji między elementami równorzędnymi w zdecentralizowanym, rozproszonym środowisku. REST umożliwia programom na różnych komputerach komunikowanie się niezależnie od systemu operacyjnego lub platformy. Usługa może wysłać żądanie protokołu HTTP (Hypertext Transfer Protocol) do adresu URL (Uniform Resource Locator) i uzyskać dane.

<a name="route"></a>**Route**: ścieżka między dwiema lub więcej lokalizacjami, które mogą również zawierać dodatkowe informacje, takie jak instrukcje dotyczące waypoints na trasie.

<a name="requests-per-second-rps"></a>**Liczba żądań na sekundę (RPS pliku)**: zobacz [zapytania na sekundę (zapytań)](#queries-per-second-qps). 

<a name="rss"></a>**RSS**: akronim dla opcji naprawdę Simple Syndication, Resource Description Framework (RDF) lub obszerne Podsumowanie witryny, w zależności od źródła. Prosty, strukturalny format XML służący do udostępniania zawartości między różnymi witrynami sieci Web. Dokumenty RSS zawierają kluczowe elementy metadanych, takie jak autor, Data, tytuł, Krótki opis i link hipertekst. Te informacje pomagają użytkownikowi (lub usłudze wydawcy RSS) zdecydować, jakie materiały są bardziej szczegółowe.

## <a name="s"></a>S

<a name="satellite-imagery"></a>Obraz **satelitarny**: obraz, który został przechwycony przez płaszczyzny i satelity wskazujące prostą.

<a name="secondary-key"></a>**Klucz pomocniczy**: drugi z dwóch kluczy subskrypcji dostarczonych do Azure Maps uwierzytelniania klucza współużytkowanego. Zobacz [uwierzytelnianie klucza wspólnego](#shared-key-authentication).

<a name="shapefile-shp"></a>Plik **kształtu (SHP)**: znany również jako Esri, jest formatem magazynu danych wektorowych do przechowywania lokalizacji, kształtu i atrybutów funkcji geograficznych. Plik kształtu jest przechowywany w zestawie powiązanych plików.

<a name="shared-key-authentication"></a>**Uwierzytelnianie klucza wspólnego**: uwierzytelnianie klucza wspólnego polega na przekazaniu kluczy wygenerowanych przez konto Azure Maps przy użyciu poszczególnych żądań do Azure Maps. Klucze te są często określane mianem kluczy subskrypcji. Zaleca się, aby klucze były regularnie generowane ponownie w celu zabezpieczenia. Dostarczone są dwa klucze, dzięki czemu można zachować połączenia przy użyciu jednego klucza podczas ponownego generowania drugiego. Po ponownym wygenerowaniu kluczy należy zaktualizować wszystkie aplikacje uzyskujące dostęp do tego konta pod kątem używania nowych kluczy. Aby dowiedzieć się więcej o uwierzytelnianiu Azure Maps, zobacz [Azure Maps i Azure AD](azure-maps-authentication.md) oraz [Zarządzanie uwierzytelnianiem w Azure Maps](how-to-manage-authentication.md).

<a name="software-development-kit-sdk"></a>**Zestaw SDK (Software Development Kit)**: zbiór dokumentacji, przykładowy kod i przykładowe aplikacje, które ułatwiają deweloperom tworzenie aplikacji przy użyciu interfejsu API.

<a name="spherical-mercator-projection"></a>**Rzutowanie sferyczne Merkatora**: zobacz [Merkatora sieci Web](#web-mercator). 

<a name="spatial-query"></a>**Zapytanie przestrzenne**: żądanie wysłane do usługi, która wykonuje operację przestrzenną. Na przykład wyszukiwanie promieniowe lub wyszukiwanie w trasie.

<a name="spatial-reference"></a>**Odwołanie przestrzenne**: System lokalny, regionalny lub globalny oparty na współrzędnych służący do precyzyjnego lokalizowania jednostek geograficznych. Definiuje układ współrzędnych służący do powiązania współrzędnych mapy z lokalizacjami w świecie rzeczywistym. Odwołania przestrzenne zapewniają, że dane przestrzenne z różnych warstw lub źródeł można zintegrować w celu dokładnego wyświetlenia lub analizy. Azure Maps używa systemu odniesienia współrzędnych [EPSG: 3857](https://epsg.io/3857) i WGS 84 do wprowadzania danych geometrycznych.

<a name="sql-spatial"></a>**Przestrzenne SQL**: odnosi się do funkcji przestrzennych wbudowanych w usługę SQL Azure oraz SQL Server 2008 i nowszych. Ta funkcjonalność przestrzenna jest również dostępna jako biblioteka .NET, która może być używana niezależnie od SQL Server. Aby uzyskać więcej informacji, zobacz [dokumentację danych przestrzennych (SQL Server)](/sql/relational-databases/spatial/spatial-data-sql-server) , aby uzyskać więcej informacji.

<a name="subscription-key"></a>**Klucz subskrypcji**: zobacz [uwierzytelnianie klucza wspólnego](#shared-key-authentication).

<a name="synchronous-request"></a>**Żądanie synchroniczne**: żądanie HTTP otwiera połączenie i czeka na odpowiedź. Przeglądarki ograniczają liczbę współbieżnych żądań HTTP, które można wprowadzać ze strony. Jeśli wiele długotrwałych żądań synchronicznych jest wykonywanych w tym samym czasie, ten limit można osiągnąć. Żądania zostaną opóźnione do momentu ukończenia jednego z innych żądań.

## <a name="t"></a>T

<a name="telematics"></a>**Telematyka**: wysyłanie, otrzymywanie i przechowywanie informacji za pośrednictwem urządzeń telekomunikacyjnych w połączeniu z efektem kontroli nad obiektami zdalnymi. 

<a name="temporal-data"></a>**Dane** czasowe: dane, które odwołują się do godzin lub dat. Dane czasowe mogą odwoływać się do zdarzeń dyskretnych, takich jak uderzenia. przeniesienie obiektów, takich jak pociągi; lub powtórzone obserwacje, na przykład liczniki z czujników ruchu.

<a name="terrain"></a>**Teren**: obszar terenu z określoną cechą, taki jak teren piaskowobrązowy lub górski teren.

<a name="thematic-maps"></a>**Mapy tematyczne**: mapa tematyczna to prosta mapa zastosowana w celu odzwierciedlenia motywu obszaru geograficznego. Typowym scenariuszem dla tego typu mapy jest kolorowanie regionów administracyjnych, takich jak kraje/regiony, na podstawie pewnej metryki danych.

<a name="tile-layer"></a>**Warstwa kafelków**: warstwa wyświetlana przez asembler kafelków mapy (sekcje prostokątne) do warstwy ciągłej. Kafelki to kafelki obrazów rastrowych lub kafelki wektorowe. Warstwy kafelków rastrowych są zwykle renderowane przed czasem i są przechowywane jako obrazy na serwerze. Warstwy kafelków rastrowych mogą korzystać z dużej ilości miejsca do magazynowania. Warstwy kafelków wektorowych są renderowane niemal w czasie rzeczywistym w aplikacji klienckiej. W rezultacie wymagania dotyczące magazynu po stronie serwera są mniejsze dla warstw kafelków wektorowych.

<a name="time-zone"></a>**Strefa czasowa**: region globusa, który obserwuje jednolity czas standardowy dla celów prawnych, komercyjnych i społecznych. Strefy czasowe są zgodne z granicami krajów/regionów i ich podziałów.

<a name="transaction"></a>**Transakcja**: Azure Maps używa transakcyjnego modelu licencjonowania, gdzie;

- Jedna transakcja jest tworzona dla każdego żądanego 15 mapy lub kafelków ruchu.
- Jedna transakcja jest tworzona dla każdego wywołania interfejsu API do jednej z usług w Azure Maps. Wyszukiwanie i Routing są przykładami usługi Azure Maps.

<a name="transformation"></a>**Transformacja**: proces konwersji danych między różnymi systemami współrzędnych geograficznych. Możesz na przykład mieć pewne dane, które zostały przechwycone w Zjednoczonym Królestwie i oparte na układzie współrzędnych geograficznych OSGB 1936. Azure Maps używa wariantu [EPSG: 3857](https://epsg.io/3857) system Reference of WGS84. W związku z tym, że dane mają być prawidłowo wyświetlane, jego współrzędne muszą być przekształcone z jednego systemu na drugi.

<a name="traveling-salesmen-problem-tsp"></a>**Problem z podróżą Salesmen (TSP)**: problem ze obwodem hamiltonian, w którym sprzedawca musi znaleźć najbardziej wydajny sposób na odwiedzenie serii zatrzymań, a następnie powrócić do lokalizacji początkowej.  

<a name="trilateration"></a>**Trilateration**: proces określania pozycji punktu na powierzchni ziemi, w odniesieniu do dwóch innych punktów, mierząc odległość między wszystkimi trzema punktami.

<a name="turn-by-turn-navigation"></a>**Nawigacja z przełączaniem**: aplikacja udostępniająca instrukcje dotyczące trasy dla każdego kroku trasy, gdy użytkownicy zbliżają się do następnego manewrówu.

## <a name="v"></a>V

<a name="vector-data"></a>**Dane wektorowe**: dane oparte na współrzędnych, które są reprezentowane jako punkty, linie lub wielokąty.

<a name="vector-tile"></a>**Kafelek wektor**: otwarta Specyfikacja danych do przechowywania danych wektorów geoprzestrzennych przy użyciu tego samego systemu kafelków co formant mapy. Zobacz również [warstwa kafelków](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a>**Problem z routingiem pojazdu (VRP)**: Klasa problemów, w której zestaw uporządkowanych tras dla floty pojazdów jest obliczany podczas uwzględniania zestawu ograniczeń. Te ograniczenia mogą obejmować okna czasowe dostaw, wiele dyspozycyjności tras i ograniczenia czasu podróży.

<a name="voronoi-diagram"></a>**Diagram Voronoi**: podział przestrzeni na obszary lub komórki, które są otoczone zestawem obiektów geometrycznych, zazwyczaj funkcji punktów. Te komórki lub wielokąty muszą spełniać kryteria dla trójkątów Delaunay. Wszystkie lokalizacje w obszarze znajdują się bliżej obiektu, który jest otaczający, niż każdy inny obiekt w zestawie. Diagramy Voronoi są często używane do odróżnić obszarów mających wpływ na funkcje geograficzne. 

## <a name="w"></a>W

<a name="waypoint"></a>**Punkt nawigacyjny**: punkt nawigacyjny jest określoną lokalizacją geograficzną zdefiniowaną na podstawie długości geograficznej i szerokości geograficznej, która jest używana do celów nawigacyjnych. Często używany do reprezentowania punktu, w którym ktoś nawiguje do trasy przez.

<a name="waypoint-optimization"></a>**Optymalizacja punkt nawigacyjny**: proces zmiany kolejności zestawu waypoints w celu zminimalizowania czasu podróży lub odległości wymaganej do przechodzenia przez wszystkie podane waypoints. W zależności od złożoności optymalizacji Ta optymalizacja jest często określana mianem [problemu z Salesmenem](#traveling-salesmen-problem-tsp) lub problemem z [routingiem pojazdu](#vehicle-routing-problem-vrp).

<a name="web-map-service-wms"></a>**Usługa mapy sieci Web (WMS)**: WMS jest standardem Open OGC Consortium, który definiuje usługi map opartych na obrazie. Usługi WMS zapewniają obrazy map dla określonych obszarów w ramach mapy na żądanie. Obrazy zawierają wstępnie renderowane Symbology i mogą być renderowane w jednym z kilku nazwanych stylów, jeśli są zdefiniowane przez usługę.

<a name="web-mercator"></a>**Merkatora sieci Web**: znany również jako sferyczny rzut Merkatora. Jest to niewielka odmiana projekcji Merkatora, która jest używana głównie w przypadku programów mapowania opartego na sieci Web. Używa tych samych formuł co w przypadku standardowego rzutowania Merkatora, który jest używany dla map małych. Jednak Merkatora sieci Web korzysta ze wzorów sferycznych we wszystkich skalach, ale mapy Merkatora o dużej skali zwykle używają formy projekcji ellipsoidal. Niezgodność jest niezauważalna w skali globalnej, ale powoduje, że mapy obszarów lokalnych odbiegają nieco inaczej niż prawdziwe ellipsoidal Merkatora Maps w tej samej skali.

<a name="wgs84"></a>**WGS84**: zestaw stałych służący do powiązania współrzędnych przestrzennych z lokalizacjami na powierzchni mapy. WGS84 podstawą jest standardem używanym przez większość dostawców mapowania online i urządzeń GPS. Azure Maps używa wariantu [EPSG: 3857](https://epsg.io/3857) system Reference of WGS84.

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Współrzędna Z**: zobacz [wysokość](#altitude). 

<a name="zip-code"></a>**Kod pocztowy**: zobacz [Kod pocztowy](#postal-code).

<a name="Zoom level"></a>**Poziom powiększenia**: określa poziom szczegółowości i ilość widocznej mapy. Po powiększeniu do poziomu 0 będzie często widoczna pełna mapa świata. Jednak mapa będzie zawierać ograniczone szczegóły, takie jak nazwy kraju/regionu, obramowania i nazwy oceanu. W przypadku powiększenia do poziomu 17 mapa będzie wyświetlać obszar kilku bloków miast ze szczegółowymi informacjami o podróży. W Azure Maps najwyższy poziom powiększenia to 22. Aby uzyskać więcej informacji, zobacz [poziomy powiększenia i dokumentacja siatki kafelków](zoom-levels-and-tile-grid.md) .