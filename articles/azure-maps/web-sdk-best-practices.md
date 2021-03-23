---
title: Azure Maps najlepszych rozwiązań dotyczących zestawu SDK sieci Web | Mapy Microsoft Azure
description: Poznaj porady & wskazówki, aby zoptymalizować korzystanie z zestawu SDK Azure Maps sieci Web.
author: rbrundritt
ms.author: richbrun
ms.date: 3/22/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: ba351053c876c31d945ec7e4127a5caebd6a71ce
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878348"
---
# <a name="azure-maps-web-sdk-best-practices"></a>Azure Maps najlepszych rozwiązań dotyczących zestawu SDK sieci Web

Ten dokument koncentruje się na najlepszych rozwiązaniach dotyczących zestawu SDK sieci Web Azure Maps, jednak wiele najlepszych rozwiązań i zoptymalizowanych optymalizacji można zastosować do wszystkich innych Azure Maps zestawów SDK.

Zestaw SDK sieci Web Azure Maps zapewnia zaawansowaną kanwę do renderowania dużych zestawów danych przestrzennych na wiele różnych sposobów. W niektórych przypadkach istnieje wiele sposobów renderowania danych w ten sam sposób, ale w zależności od rozmiaru zestawu danych i odpowiedniej funkcjonalności jedna metoda może być lepsza niż inne. W tym artykule przedstawiono najlepsze rozwiązania oraz porady i wskazówki dotyczące maksymalizowania wydajności i tworzenia płynnego środowiska użytkownika.

Ogólnie rzecz biorąc, gdy poszukujesz poprawy wydajności mapy, poszukaj sposobów zmniejszenia liczby warstw i źródeł oraz złożoności zestawów danych i używanych stylów renderowania.

## <a name="security-basics"></a>Podstawowe informacje o zabezpieczeniach

Jedną z najważniejszych części aplikacji jest jej zabezpieczenia. Jeśli aplikacja nie jest zabezpieczona, haker może ruin dowolną aplikację, niezależnie od tego, jak dobre środowisko użytkownika może być. Poniżej przedstawiono kilka porad dotyczących zabezpieczania aplikacji Azure Maps. W przypadku korzystania z platformy Azure Pamiętaj o zapoznaniu się z dostępnymi narzędziami zabezpieczeń. Zapoznaj się z tym dokumentem, aby uzyskać [wprowadzenie do zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/overview).

> [!IMPORTANT]
> Azure Maps oferuje dwie metody uwierzytelniania.
> * Uwierzytelnianie oparte na kluczach subskrypcji
> * Azure Active Directory uwierzytelniania Azure Active Directory we wszystkich aplikacjach produkcyjnych.
> Uwierzytelnianie oparte na kluczach subskrypcji jest proste, a większość platform mapowania używa jako metody jasnej do mierzenia użycia platformy na potrzeby rozliczeń. Nie jest to jednak bezpieczna forma uwierzytelniania i powinna być używana tylko lokalnie podczas tworzenia aplikacji. Niektóre platformy zapewniają możliwość ograniczenia, które adresy IP i/lub odwołujący HTTP znajdują się na żądanie, ale te informacje mogą być łatwo sfałszowane. W przypadku korzystania z kluczy subskrypcji należy koniecznie [obrócić je regularnie](how-to-manage-authentication.md#manage-and-rotate-shared-keys).
> Azure Active Directory to usługa tożsamości przedsiębiorstwa, która oferuje wiele funkcji zabezpieczeń i ustawień dla wszystkich rodzajów scenariuszy aplikacji. Firma Microsoft zaleca, aby wszystkie aplikacje produkcyjne korzystające z Azure Maps Azure Active Directory do uwierzytelniania.
> Dowiedz się więcej o [zarządzaniu uwierzytelnianiem w Azure Maps](how-to-manage-authentication.md) w tym dokumencie.

### <a name="secure-your-private-data"></a>Zabezpiecz swoje dane prywatne

Gdy dane są dodawane do Azure Maps zestawów SDK mapy interakcyjnej, są one renderowane lokalnie na urządzeniu użytkownika końcowego i nigdy nie są wysyłane z Internetu z jakiegokolwiek powodu.

Jeśli aplikacja ładuje dane, które nie powinny być publicznie dostępne, upewnij się, że dane są przechowywane w bezpiecznej lokalizacji, są dostępne w bezpieczny sposób i że sama aplikacja jest zablokowana i dostępna tylko dla żądanych użytkowników. Jeśli którykolwiek z tych kroków zostanie pominięty, osoba nieupoważniona będzie mogła uzyskać dostęp do tych danych. Azure Active Directory może pomóc w zablokowaniu tego programu.

Zapoznaj się z tym samouczkiem dotyczącym [dodawania uwierzytelniania do aplikacji sieci Web działającej na Azure App Service](https://docs.microsoft.com/azure/app-service/scenario-secure-app-authentication-app-service)

### <a name="use-the-latest-versions-of-azure-maps"></a>Użyj najnowszych wersji Azure Maps

Zestawy Azure Maps SDK przechodzą przez regularne testowanie zabezpieczeń wraz z wszelkimi zewnętrznymi bibliotekami zależności, które mogą być używane przez zestawy SDK. Wszystkie znane problemy z zabezpieczeniami są rozwiązywane w odpowiednim czasie i udostępniane w środowisku produkcyjnym. Jeśli aplikacja wskazuje najnowszą wersję główną hostowanej wersji Azure Maps Web SDK, automatycznie otrzyma wszystkie aktualizacje pomocnicze, które będą zawierać poprawki dotyczące zabezpieczeń.

W przypadku samoobsługowego Azure Maps zestawu SDK sieci Web za pośrednictwem modułu NPM upewnij się, że używasz symbolu karetki (^) w połączeniu z numerem wersji pakietu Azure Maps NPM w `package.json` pliku, aby zawsze wskazywał najnowszą wersję pomocniczą.

```json
"dependencies": {
  "azure-maps-control": "^2.0.30"
}
```

## <a name="optimize-initial-map-load"></a>Optymalizowanie ładowania początkowej mapy

Po załadowaniu strony sieci Web jeden z pierwszych rzeczy, które chcesz zrobić, zaczyna renderować coś tak szybko, jak to możliwe, aby użytkownik nie miał do nich gwiazdki na pustym ekranie.

### <a name="watch-the-maps-ready-event"></a>Obejrzyj zdarzenie gotowe do maps

Podobnie, gdy mapa początkowo ładuje często, trzeba załadować na niej dane tak szybko, jak to możliwe, aby użytkownik nie przeglądał pustej mapy. Ponieważ Mapa ładuje zasoby asynchronicznie, musisz poczekać, aż mapa będzie gotowa do pracy z nią przed próbą renderowania własnych danych. Istnieją dwa zdarzenia, które można oczekiwać na `load` zdarzenie i zdarzenie `ready` . Zdarzenie ładowania zostanie wyzwolone po całkowitym załadowaniu mapy początkowej widoku mapy i załadowaniu wszystkich kafelków mapy. Zdarzenie Ready zostanie uruchomione, gdy minimalna ilość zasobów mapy jest wymagana do rozpoczęcia pracy z mapą. Zdarzenie Ready może często być wyzwalane w połowie czasu zdarzenia ładowania i w ten sposób umożliwia ponowne rozpoczęcie ładowania danych do mapy.

### <a name="lazy-load-the-azure-maps-web-sdk"></a>Załaduj z opóźnieniem zestaw SDK sieci Web Azure Maps

Jeśli mapa nie jest jeszcze wymagana, należy poczekać na załadowanie Azure Maps Web SDK, dopóki nie będzie to konieczne. Spowoduje to opóźnienie ładowania plików JavaScript i CSS używanych przez zestaw SDK sieci Web Azure Maps do momentu, gdy będzie to potrzebne. Typowy scenariusz, w którym występuje, jest ładowany w przypadku załadowania mapy na karcie lub panelu wysuwanego, który nie jest wyświetlany podczas ładowania strony.
Poniższy przykład kodu pokazuje, jak opóźnić ładowanie Azure Maps Web SDK do momentu naciśnięcia przycisku.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ładowanie mapy z opóźnieniem" src="https://codepen.io/azuremaps/embed/vYEeyOv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióro <a href='https://codepen.io/azuremaps/pen/vYEeyOv'>Załaduj mapę</a> według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-a-placeholder-for-the-map"></a>Dodaj symbol zastępczy dla mapy

Jeśli mapa zajmuje trochę czasu, aby załadować z powodu ograniczeń sieci lub innych priorytetów w aplikacji, należy rozważyć dodanie małego obrazu tła do mapy `div` jako symbolu zastępczego dla mapy. Spowoduje to wypełnienie nieważnej mapy `div` podczas ładowania.

### <a name="set-initial-map-style-and-camera-options-on-initialization"></a>Ustawianie początkowego stylu mapy i opcji kamery podczas inicjowania

Często aplikacje chcą załadować mapę do określonej lokalizacji lub stylu. Czasami deweloperzy czekają na załadowanie mapy (lub oczekiwanie na `ready` zdarzenie), a następnie użyć `setCemer` `setStyle` funkcji lub z mapy. Będzie to często trwać dłużej, aby uzyskać dostęp do żądanego widoku mapy początkowej, ponieważ wiele zasobów jest domyślnie załadowanych przed załadowaniem zasobów wymaganych dla żądanego widoku mapy. Lepszym rozwiązaniem jest przekazanie odpowiednich opcji aparatu mapy i stylów do mapy podczas jej inicjalizacji.

## <a name="optimize-data-sources"></a>Optymalizowanie źródeł danych

Zestaw SDK sieci Web ma dwa źródła danych,

* **Źródło GEOJSON**: nazywane `DataSource` klasą, zarządza lokalnie danymi lokalizacji w formacie GEOJSON. Dobre dla małych i średnich zestawów danych (w górę setek tysięcy funkcji).
* **Źródło kafelków wektorowych**: znane w `VectorTileSource` klasie, ładuje dane sformatowane jako kafelki wektorów dla bieżącego widoku mapy, na podstawie systemu sąsiadującego z mapowaniem. Idealne rozwiązanie w przypadku dużych i wielkich zestawów danych (miliony lub miliardów funkcji).

### <a name="use-tile-based-solutions-for-large-datasets"></a>Używanie rozwiązań opartych na kafelkach do dużych zestawów danych

W przypadku pracy z większymi zestawami danych zawierającymi miliony funkcji zalecanym sposobem osiągnięcia optymalnej wydajności jest udostępnienie danych przy użyciu rozwiązania po stronie serwera, takiego jak Vector lub rastrowy kafelek obrazu usługi.  
Kafelki wektorowe są zoptymalizowane pod kątem ładowania tylko danych znajdujących się w widoku z geometrie przyciętym do obszaru koncentracji kafelka i uogólnione w celu dopasowania ich do rozdzielczości mapy dla poziomu powiększenia kafelka.

[Platforma twórcy Azure Maps](creator-indoor-maps.md) zapewnia możliwość pobierania danych w formacie kafelka wektorowego. Inne formaty danych mogą korzystać z narzędzi, takich jak [Tippecanoe](https://github.com/mapbox/tippecanoe) lub jedna z wielu [zasobów na tej stronie](https://github.com/mapbox/awesome-vector-tiles).

Istnieje również możliwość utworzenia niestandardowej usługi, która renderuje zestawy danych jako kafelki obrazu rastrowego po stronie serwera i ładuje dane przy użyciu klasy TileLayer w zestawie SDK mapy. Zapewnia to wyjątkową wydajność, ponieważ Mapa musi jedynie ładować kilka dziesiątych obrazów i zarządzać nimi. Jednak istnieją pewne ograniczenia dotyczące używania kafelków rastrowych, ponieważ pierwotne dane nie są dostępne lokalnie. Usługa pomocnicza jest często wymagana do przydzielenia dowolnego typu środowiska interakcji, na przykład dowiedzieć się, co kształt został kliknięty. Ponadto rozmiar pliku kafelka rastrowego jest często większy niż skompresowany kafelek wektora zawierający uogólnione i Zoptymalizowane geometrie.

Dowiedz się więcej o źródłach danych w dokumencie [Tworzenie źródła danych](create-data-source-web-sdk.md) .

### <a name="combine-multiple-datasets-into-a-single-vector-tile-source"></a>Łączenie wielu zestawów danych w jednym źródle kafelków wektora

Im mniejsze źródła danych, którymi ma zarządzać mapa, tym szybciej mogą przetwarzać wszystkie funkcje, które mają być wyświetlane. W szczególności, gdy przejdziesz do źródeł kafelków, połączenie dwóch źródeł kafelków wektorowych spowoduje przekazanie liczby żądań HTTP w celu pobrania kafelków w połowie, a całkowita ilość danych byłaby nieco mniejsza, ponieważ istnieje tylko jeden nagłówek pliku.

Łączenie wielu zestawów danych w jednym źródle kafelków wektorowych można osiągnąć przy użyciu narzędzia, takiego jak [Tippecanoe](https://github.com/mapbox/tippecanoe). Zestawy danych można łączyć w jedną kolekcję funkcji lub oddzielić je na osobne warstwy na kafelku wektora znanym jako warstwy źródłowe. Podczas łączenia źródła kafelka wektorowego z warstwą renderowania należy określić warstwę źródłową zawierającą dane, które mają być renderowane z warstwy.

### <a name="reduce-the-number-of-canvas-refreshes-due-to-data-updates"></a>Zmniejsz liczbę odświeżeń kanwy ze względu na aktualizacje danych

Istnieje kilka sposobów, `DataSource` Aby można było dodać lub zaktualizować dane w klasie. Poniżej wymieniono różne metody, a niektóre zagadnienia mają na celu zapewnienie dobrej wydajności.

* Funkcja źródeł danych `add` może służyć do dodawania jednej lub kilku funkcji do źródła danych. Za każdym razem, gdy ta funkcja jest wywoływana, spowoduje to wyzwolenie odświeżania kanwy mapy. W przypadku dodawania wielu funkcji Połącz je z tablicą lub kolekcją funkcji, a następnie Przekaż je do tej funkcji, zamiast zapętlenia przez zestaw danych i wywołania tej funkcji dla każdej funkcji.
* Funkcja źródeł danych `setShapes` może służyć do zastępowania wszystkich kształtów w źródle danych. Pod okapem łączy źródła danych `clear` i `add` działa razem i wykonuje odświeżenie kanwy z pojedynczą mapą zamiast dwóch, co jest znacznie szybsze. Pamiętaj, aby użyć tego elementu, gdy chcesz zaktualizować wszystkie dane w źródle danych.
* Funkcja źródeł danych `importDataFromUrl` może służyć do ładowania pliku GEOJSON za pośrednictwem adresu URL do źródła danych. Po pobraniu danych zostanie ono przesłane do funkcji źródeł danych `add` . Jeśli plik GEOJSON jest hostowany w innej domenie, należy się upewnić, że inna domena obsługuje żądania między domenami (CORs). Jeśli nie należy rozważyć kopiowania danych do pliku lokalnego w domenie lub tworzenia usługi serwera proxy z włączonym mechanizmem CORs. Jeśli plik jest duży, Rozważ przekonwertowanie go na źródło kafelka wektorowego.
* Jeśli funkcje są opakowane z `Shape` klasą, `addProperty` `setCoordinates` `setProperties` wszystkie funkcje kształtu wyzwalają aktualizację w źródle danych i odświeża kanwę mapy. Wszystkie funkcje zwrócone przez źródła danych `getShapes` i `getShapeById` funkcje są automatycznie opakowane z `Shape` klasą. Jeśli chcesz zaktualizować kilka kształtów, można je przekonwertować na format JSON przy użyciu funkcji źródła danych `toJson` , edytując kod JSON, a następnie przekazując te dane do funkcji źródła danych `setShapes` .

### <a name="avoid-calling-the-data-sources-clear-function-unnecessarily"></a>Unikaj niepotrzebnego wywoływania źródeł danych.

Wywołanie funkcji Clear `DataSource` klasy powoduje odświeżenie kanwy mapy. Jeśli `clear` Funkcja jest wywoływana wiele razy w wierszu, może wystąpić opóźnienie, gdy mapa czeka na wystąpienie każdego odświeżenia.

Typowy scenariusz, w którym często pojawia się w aplikacjach — gdy aplikacja czyści źródło danych, pobiera nowe dane, czyści to źródło danych, a następnie dodaje nowe dane do źródła danych. W zależności od wymaganego środowiska użytkownika można lepiej wykonać następujące alternatywy.

* Wyczyść dane przed pobraniem nowych danych, a następnie Przekaż nowe dane do źródeł danych `add` lub `setShapes` funkcji. Jeśli jest to jedyny zestaw danych na mapie, mapa będzie pusta podczas pobierania nowych danych.
* Pobierz nowe dane, a następnie Przekaż je do funkcji źródeł danych `setShapes` . Spowoduje to zamianę wszystkich danych na mapie.

### <a name="remove-unused-features-and-properties"></a>Usuń nieużywane funkcje i właściwości

Jeśli zestaw danych zawiera funkcje, które nie będą używane w aplikacji, usuń je. Podobnie należy usunąć wszelkie właściwości w niezbędnych funkcjach. Ma to kilka korzyści:

* Zmniejsza ilość danych, które mają zostać pobrane.
* Zmniejsza liczbę funkcji, które należy wykonać w pętli podczas renderowania danych.
* Może czasami ułatwić uproszczenie lub usunięcie wyrażeń i filtrów opartych na danych, co oznacza mniej przetwarzania wymagane w czasie renderowania.

Gdy funkcje mają wiele właściwości lub zawartości, jest znacznie bardziej wydajne, aby ograniczyć elementy dodawane do źródła danych tylko do tych, które są potrzebne do renderowania, i mają osobną metodę lub usługę do pobierania dodatkowej właściwości lub zawartości w razie potrzeby. Na przykład, jeśli masz prostą mapę wyświetlającą lokalizacje na mapie, gdy zostanie kliknięty zbiór szczegółowych treści jest wyświetlany. Jeśli chcesz użyć stylów opartych na danych, aby dostosować sposób renderowania lokalizacji na mapie, Załaduj tylko właściwości wymagane do źródła danych. Jeśli chcesz wyświetlić szczegółową zawartość, użyj identyfikatora funkcji, aby pobrać dodatkową zawartość osobno. Jeśli zawartość jest przechowywana na serwerze, można użyć usługi, aby ją pobrać asynchronicznie, co radykalnie zmniejsza ilość danych, które muszą zostać pobrane po początkowym załadowaniu mapy.

Ponadto zmniejszenie liczby cyfr znaczących we współrzędnych funkcji może znacznie zmniejszyć rozmiar danych. Nie jest to rzadko stosowane w przypadku współrzędnych, które zawierają 12 lub więcej miejsc dziesiętnych; jednak sześć miejsc dziesiętnych ma dokładność około 0,1 mierników, która jest często bardziej precyzyjna niż lokalizacja, którą reprezentuje Współrzędna (sześć miejsc dziesiętnych jest zalecanych podczas pracy z małymi danymi lokalizacji, takimi jak wewnętrzne układy budynku). Posiadanie więcej niż sześciu miejsc dziesiętnych prawdopodobnie nie spowoduje żadnej różnicy w sposobie renderowania danych i będzie wymagać od użytkownika tylko pobrania większej ilości danych.

Poniżej znajduje się lista [przydatnych narzędzi do pracy z danymi GEOJSON](https://github.com/tmcw/awesome-geojson).

### <a name="use-a-separate-data-source-for-rapidly-changing-data"></a>Używanie oddzielnego źródła danych do szybkiego zmieniania danych

Czasami istnieje potrzeba szybkiej aktualizacji danych na mapie, takich jak wyświetlanie aktualizacji danych przesyłanych strumieniowo lub animowanych funkcji. Gdy źródło danych zostanie zaktualizowane, aparat renderowania przejdzie w tryb pętli i renderuje wszystkie funkcje w źródle danych. Oddzielenie danych statycznych od szybko zmieniających się danych na różne źródła danych może znacząco zmniejszyć liczbę funkcji, które są odtwarzane w każdej aktualizacji do źródła danych i poprawić ogólną wydajność.

W przypadku używania kafelków wektorowych z danymi na żywo łatwym sposobem obsługi aktualizacji jest użycie `expires` nagłówka odpowiedzi. Domyślnie wszystkie źródła kafelków wektorowych lub kafelków rastrowych będą automatycznie ładować kafelki, gdy `expires` Data. Kafelki przepływ ruchu i incydenty na mapie używają tej funkcji, aby zapewnić, że na mapie są wyświetlane dane dotyczące ruchu w czasie rzeczywistym. Tę funkcję można wyłączyć, ustawiając `refreshExpiredTiles` opcję usługi Maps na `false` .

### <a name="adjust-the-buffer-and-tolerance-options-in-geojson-data-sources"></a>Dostosuj opcje buforu i tolerancji w źródłach danych GEOJSON

`DataSource`Klasa konwertuje nieprzetworzone dane lokalizacji na kafelki wektorowe lokalne na potrzeby renderowania na bieżąco. Te lokalne kafelki wektorowe obcinają dane pierwotne do granic obszaru kafelka z bitem bufora, aby zapewnić płynne renderowanie między kafelkami. Im mniejsza `buffer` jest opcja, tym mniej nakładające się na siebie dane są przechowywane w lokalnych kafelkach wektorowych i lepsza wydajność, jednak im większa jest zmiana artefaktów renderowania. Spróbuj dostosować tę opcję, aby uzyskać właściwą kombinację wydajności przy minimalnych artefaktach renderowania.

`DataSource`Klasa ma również `tolerance` opcję, która jest używana z algorytmem uproszczenia Douglas-Peucker podczas zmniejszania rozdzielczości geometrie na potrzeby renderowania. Zwiększenie tej wartości tolerancji spowoduje zmniejszenie rozdzielczości geometrie i z kolei zwiększenie wydajności. Dostosuj tę opcję, aby uzyskać właściwą kombinację rozpoznawania i wydajności geometrii dla Twojego zestawu danych.

### <a name="set-the-max-zoom-option-of-geojson-data-sources"></a>Ustawianie opcji maksymalnej powiększenia źródeł danych GEOJSON

`DataSource`Klasa konwertuje nieprzetworzone dane lokalizacji na kafelki wektorowe lokalne na potrzeby renderowania na bieżąco. Domyślnie będzie to możliwe do momentu powiększenia do poziomu 18, w którym punkt, w którym jest powiększony, będzie próbkowane dane z kafelków wygenerowanych dla poziomu powiększenia 18. To dobrze sprawdza się w przypadku większości zestawów danych, które wymagają wysokiej rozdzielczości przy powiększeniu na tych poziomach. Jednak podczas pracy z zestawami danych, które mogą być wyświetlane w czasie powiększania, na przykład podczas wyświetlania wielokątów stanów lub prowincji, ustawienie `minZoom` opcji źródła danych na mniejszą wartość, na przykład `12` zmniejszy wielkość obliczeń, lokalna generacja kafelków i ilość pamięci używanej przez źródło danych i zwiększenie wydajności.

### <a name="minimize-geojson-response"></a>Minimalizuj odpowiedź GEOJSON

Podczas ładowania danych GEOJSON z serwera za pośrednictwem usługi lub przez załadowanie pliku prostego upewnij się, że dane zostały zminimalizowane, aby usunąć niepotrzebne znaki spacji, które zwiększają rozmiar plików do pobrania.

### <a name="access-raw-geojson-using-a-url"></a>Dostęp do nieprzetworzonego GEOJSON przy użyciu adresu URL

Istnieje możliwość przechowywania obiektów GEOJSON wbudowanych wewnątrz języka JavaScript, jednak spowoduje to użycie dużej ilości pamięci, ponieważ kopie jej będą przechowywane w zmiennej utworzonej dla tego obiektu i w wystąpieniu źródła danych, które zarządza nim w osobnym internetowym procesie roboczym. Udostępnienie GEOJSON aplikacji przy użyciu adresu URL, a źródło danych załaduje pojedynczą kopię danych bezpośrednio do źródła danych internetowy proces roboczy.  

## <a name="optimize-rendering-layers"></a>Optymalizuj warstwy renderowania

Usługa Azure Maps oferuje kilka różnych warstw do renderowania danych na mapie. Istnieje wiele optymalizacji, których można używać do dostosowywania tych warstw do scenariusza w celu zwiększenia wydajności i ogólnego środowiska użytkownika.

### <a name="create-layers-once-and-reuse-them"></a>Utwórz warstwy raz i użyj ich ponownie

Zestaw SDK sieci Web Azure Maps ma być oparty na danych. Dane trafiają do źródeł danych, które następnie są połączone z warstwami renderowania. Jeśli chcesz zmienić dane na mapie, zaktualizuj dane w źródle danych lub zmień opcje stylu warstwy. Jest to często znacznie szybsze niż usunięcie i ponowne utworzenie warstw przy każdej zmianie.

### <a name="consider-bubble-layer-over-symbol-layer"></a>Rozważ użycie warstwy bąbelków nad warstwą symboli

Warstwa bąbelków renderuje punkty jako okręgi na mapie i łatwo może mieć swój promień i kolor stylu przy użyciu wyrażenia opartego na danych. Ponieważ okrąg jest prostym kształtem do rysowania WebGL, aparat renderowania będzie w stanie renderować te znacznie szybciej niż warstwa symboli, która wymaga załadowania i renderowania obrazu. Różnica wydajności tych dwóch warstw renderowania jest zauważalna podczas renderowania dziesiątek tysięcy punktów.

### <a name="use-html-markers-and-popups-sparingly"></a>Korzystanie ze znaczników HTML i wyskakujących okienek jest oszczędne

W przeciwieństwie do większości warstw w Azure Maps formancie sieci Web, który używa WebGL do renderowania, znaczniki HTML i okna podręczne używają tradycyjnych elementów DOM do renderowania. W związku z tym więcej znaczników HTML i wyskakujących okienek dodano do strony, tym więcej elementów DOM. Wydajność może być gorsza po dodaniu zaledwie setek znaczników HTML lub wyskakujących okienek. W przypadku większych zestawów danych należy rozważyć klastrowanie danych lub użycie symbolu lub warstwy bąbelkowej. W przypadku okien podręcznych częstą strategią jest utworzenie jednego podokna podręcznego i jego ponowne użycie przez aktualizację jego zawartości i pozycji, jak pokazano w poniższym przykładzie:

<br/>

<iframe height='500' scrolling='no' title='Używanie podręcznego z wieloma numerami PIN' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobaczysz piórem, <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>korzystając z okna podręcznego z wieloma</a> numerami pin przez Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W przypadku, gdy masz tylko kilka punktów do renderowania na mapie, prostota znaczników HTML może być preferowana. Ponadto znaczniki HTML można łatwo przeciągać w razie konieczności.

### <a name="combine-layers"></a>Połącz warstwy

Mapa jest w stanie renderować setki warstw, jednak im więcej warstw, tym dłużej zajmuje się renderowaniem sceny. Jedną z strategii, aby zmniejszyć liczbę warstw, jest łączenie warstw mających podobne style lub można je stylować za pomocą [stylów opartych na danych](data-driven-style-expressions-web-sdk.md).

Rozważmy na przykład zestaw danych, w którym wszystkie funkcje mają `isHealthy` Właściwość, która może mieć wartość `true` lub `false` . Jeśli tworzysz warstwę bąbelkową, która renderuje różne kolorowe bąbelki na podstawie tej właściwości, istnieje kilka sposobów, aby to zrobić, tak jak pokazano poniżej, co najmniej na najbardziej wydajny sposób.

* Podziel dane na dwa źródła danych na podstawie `isHealthy` wartości i Dołącz warstwę bąbelkową z ustaloną opcją koloru do każdego źródła danych.
* Umieść wszystkie dane w jednym źródle danych i Utwórz dwie warstwy bąbelkowe z ustaloną opcją koloru i filtrem opartym na `isHealthy` właściwości. 
* Umieść wszystkie dane w jednym źródle danych, Utwórz pojedynczą warstwę bąbelkową z `case` wyrażeniem stylu dla opcji Color na podstawie `isHealthy` właściwości. Oto przykład kodu, który pokazuje to.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Get the 'isHealthy' property from the feature.
        ['get', 'isHealthy'],

        //If true, make the color 'green'. 
        'green',

        //If false, make the color red.
        'red'
    ]
});
```

### <a name="create-smooth-symbol-layer-animations"></a>Tworzenie gładkich animacji warstwowych symboli

Warstwy symboli mają domyślnie włączone wykrywanie kolizji. To wykrywanie kolizji ma na celu zagwarantowanie, że nie nakładają się dwa symbole. Opcje ikon i tekstu warstwy symboli mają dwie opcje,

* `allowOverlap` -Określa, czy symbol będzie widoczny, jeśli koliduje z innymi symbolami.
* `ignorePlacement` -Określa, czy inne symbole mogą koliduje z symbolem.

Obie te opcje są domyślnie ustawione na wartość `false` . Podczas animowania symbolu obliczenia kolizji są uruchamiane na każdej klatce animacji, co może spowolnić animację i spowodować, że będzie wyglądać mniej więcej. Aby wygładzić animację, należy ustawić te opcje na `true` .

Poniższy kod przykład prosty sposób animacji warstwy symboli.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Animacja warstwy symboli" src="https://codepen.io/azuremaps/embed/oNgGzRd?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/oNgGzRd'>animację warstwy symboli</a> pióra według Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="specify-zoom-level-range"></a>Określ zakres poziomu powiększenia

Jeśli dane spełniają jedno z następujących kryteriów, należy określić minimalną i maksymalną wartość powiększenia warstwy, aby aparat renderowania mógł go pominąć, gdy jest poza zakresem poziomu powiększenia.

* Jeśli dane pochodzą ze źródła kafelka wektorowego, często warstwy źródłowe dla różnych typów danych są dostępne tylko za pośrednictwem zakresu poziomów powiększenia.
* Jeśli używasz warstwy kafelków, która nie ma kafelków dla wszystkich poziomów powiększenia od 0 do 24, i chcesz, aby była renderowana tylko na poziomach, w których ma kafelki, i nie próbuj wypełniać brakujących kafelków kafelków z innych poziomów powiększenia.
* Jeśli chcesz tylko renderować warstwę na określonych poziomach powiększenia.
Wszystkie warstwy mają `minZoom` opcję i, `maxZoom` w której warstwa będzie renderowana po obu poziomach powiększenia na podstawie tej logiki ` maxZoom > zoom >= minZoom` .

**Przykład**

```javascript
//Only render this layer between zoom levels 1 and 9. 
var layer = new atlas.layer.BubbleLayer(dataSource, null, {
    minZoom: 1,
    maxZoom: 10
});
```

### <a name="specify-tile-layer-bounds-and-source-zoom-range"></a>Określ granice warstwy kafelków i zakres powiększenia źródła

Domyślnie warstwy kafelków będą ładować kafelki na całym świecie. Jeśli jednak usługa kafelków ma tylko kafelki dla określonego obszaru, Mapa podejmie próbę załadowania kafelków, gdy jest ona poza tym obszarem. W takim przypadku zostanie wykonane żądanie dla każdego kafelka i zaczekasz na odpowiedź, która może blokować inne żądania wykonywane przez mapę i spowalniać renderowanie innych warstw. Określenie granic warstwy kafelka spowoduje, że mapa żąda kafelków, które znajdują się w tym polu. Ponadto, jeśli warstwa kafelków jest dostępna tylko między pewnymi poziomami powiększenia, określ minimalne i maksymalne powiększenie źródła z tego samego powodu.

**Przykład**

```javascript
var tileLayer = new atlas.layer.TileLayer({
    tileUrl: 'myTileServer/{z}/{y}/{x}.png',
    bounds: [-101.065, 14.01, -80.538, 35.176],
    minSourceZoom: 1,
    maxSourceZoom: 10
});
```

### <a name="use-a-blank-map-style-when-base-map-not-visible"></a>Użyj pustego stylu mapy, gdy mapa podstawowa nie jest widoczna

Jeśli warstwa jest nakładana na mapę, która całkowicie pokrywa się z mapą bazową, rozważ ustawienie stylu mapy na `blank` lub `blank_accessible` tak, aby mapa bazowa nie była renderowana. Typowym scenariuszem tego jest to, że w przypadku nakładki pełnego kafelka na całym świecie nie ma krycia ani przezroczystego obszaru powyżej mapy podstawowej.

### <a name="smoothly-animate-image-or-tile-layers"></a>Płynnie Animuj warstwy obrazu lub kafelków

Jeśli chcesz animować serię warstw obrazu lub kafelków na mapie. Często istnieje szybsze tworzenie warstwy dla każdej warstwy obrazu lub kafelka i zmiana nieprzejrzystości niż w celu zaktualizowania źródła pojedynczej warstwy w każdej klatce animacji. Ukrycie warstwy przez ustawienie nieprzezroczystości na zero i pokazanie nowej warstwy przez ustawienie jego nieprzezroczystości na wartość większą od zera jest znacznie szybsza niż aktualizacja źródła w warstwie. Alternatywnie widoczność warstw może być przełączana, ale pamiętaj o ustawieniu czasu trwania zanikania warstwy na zero. w przeciwnym razie animacja będzie animować warstwę, co spowoduje efekt migotania, ponieważ Poprzednia warstwa zostanie ukryta przed widoczną nową warstwą.

### <a name="tweak-symbol-layer-collision-detection-logic"></a>Dostrajanie logiki wykrywania kolizji warstwy symboli

Warstwa symboli ma dwie opcje, które istnieją dla obu ikon i tekstu o nazwie `allowOverlap` i `ignorePlacement` . Te dwie opcje określają, czy ikona lub tekst symbolu mogą się nakładać na siebie, czy też nakładać się na siebie. Gdy są one ustawione na `false` , warstwa symboli wykonuje obliczenia podczas renderowania każdego punktu, aby zobaczyć, czy koliduje z jakimkolwiek innym już renderowanym symbolem w warstwie, i jeśli tak, nie będzie renderować symbolicznego symbolu. Jest to dobre zmniejszenie wartości w mapie i zmniejszenie liczby renderowanych obiektów. Po ustawieniu tych opcji na `false` , ta logika wykrywania kolizji zostanie pominięta, a wszystkie symbole będą renderowane na mapie. Dostosowywanie tej opcji w celu uzyskania najlepszej kombinacji wydajności i środowiska użytkownika.

### <a name="cluster-large-point-data-sets"></a>Zestawy danych dużych punktów klastra

Podczas pracy z dużymi zestawami punktów danych może się okazać, że podczas renderowania na określonych poziomach powiększenia wiele punktów nakłada się na siebie i są widoczne tylko częściowo, jeśli w ogóle. Klastrowanie polega na tym, że punkty grupowania są blisko siebie i reprezentują je jako pojedyncze punkty klastrowane. Gdy użytkownik powiększa mapę w programie, klastry zostaną rozdzielone na poszczególne punkty. Może to znacznie zmniejszyć ilość danych, które muszą być renderowane, zmniejszyć poziom działania mapy i zwiększyć wydajność. `DataSource`Klasa zawiera opcje klastrowania danych lokalnie. Ponadto wiele narzędzi generujących kafelki wektorowe ma również opcje klastrowania.

Ponadto Zwiększ rozmiar promienia klastra, aby zwiększyć wydajność. Im większy promień klastra, tym mniej klastrowane punkty, w których należy śledzić i renderować.
Dowiedz się więcej z [dokumentu dane punktu klastrowania](clustering-point-data-web-sdk.md)

### <a name="use-weighted-clustered-heat-maps"></a>Korzystanie z ważonych, grupowanych map cieplnych

Warstwa mapy cieplnej umożliwia łatwe renderowanie dziesiątek tysięcy punktów danych. W przypadku większych zestawów danych należy rozważyć włączenie klastrowania w źródle danych i użycie małego promienia klastra i użycie właściwości klastry `point_count` jako wagi dla mapy wysokości. Gdy promień klastra ma tylko kilka pikseli, w renderowanej mapie cieplnej będzie niewielka różnica wizualna. Użycie większego promienia klastra poprawi wydajność, ale może obniżyć rozdzielczość renderowanej mapy cieplnej.

```javascript
var layer = new atlas.layer.HeatMapLayer(source, null, {
   weight: ['get', 'point_count']
});
```

Dowiedz się więcej z usługi [klastrowanie i mapy cieplne w tym dokumencie](clustering-point-data-web-sdk.md #clustering-and-the-heat-maps-layer)

### <a name="keep-image-resources-small"></a>Zachowaj małe zasoby obrazów

Obrazy można dodawać do ikon obrazu mapy na potrzeby renderowania ikon w warstwie symboli lub wzorcach w warstwie wielokąta. Zachowaj te obrazy jako małe, aby zminimalizować ilość danych, które muszą zostać pobrane, i ilość miejsca, jakie zajmują na ikonie obrazu mapy. W przypadku korzystania z warstwy symboli, która skaluje ikonę przy użyciu `size` opcji, Użyj obrazu, który jest maksymalnym rozmiarem planu, który ma być wyświetlany na mapie, i nie jest większy. Dzięki temu ikona będzie renderowana z wysoką rozdzielczością przy jednoczesnym zminimalizowaniu zasobów, z których korzystają. Ponadto można również użyć grafiki SVG jako mniejszego formatu pliku dla prostych obrazów ikon.

## <a name="optimize-expressions"></a>Optymalizowanie wyrażeń

[Wyrażenia oparte na danych](data-driven-style-expressions-web-sdk.md) zapewniają dużą elastyczność i możliwości filtrowania i stylów danych na mapie. Istnieje wiele sposobów, w których można zoptymalizować wyrażenia. Poniżej przedstawiono kilka wskazówek.

### <a name="reduce-the-complexity-of-filters"></a>Zmniejszanie złożoności filtrów

Filtruje pętlę dla wszystkich danych w źródle danych i sprawdza, czy każdy filtr pasuje do logiki w filtrze. Jeśli filtry stają się złożone, może to spowodować problemy z wydajnością. Poniżej przedstawiono niektóre możliwe strategie postępowania z tymi rozwiązaniami.

* W przypadku używania kafelków wektorowych należy podzielić dane na różne warstwy źródłowe.
* W przypadku używania `DataSource` klasy należy podzielić te dane na oddzielne źródła danych. Spróbuj zrównoważyć liczbę źródeł danych ze złożonością filtru. Zbyt wiele źródeł danych może powodować problemy z wydajnością, więc konieczne może być przeprowadzenie pewnych testów, aby dowiedzieć się, co najlepiej sprawdza się w danym scenariuszu.
* W przypadku korzystania z filtru złożonego na warstwie należy rozważyć użycie wielu warstw z wyrażeniami stylu, aby zmniejszyć złożoność filtru. Unikaj tworzenia licznych warstw przy użyciu stylów stałe, gdy wyrażenia stylu mogą być używane jako duża liczba warstw może również powodować problemy z wydajnością.

### <a name="make-sure-expressions-dont-produce-errors"></a>Upewnij się, że wyrażenia nie generują błędów

Wyrażenia są często używane do generowania kodu w celu wykonywania obliczeń lub operacji logicznych w czasie renderowania. Podobnie jak w przypadku kodu w pozostałej części aplikacji, należy się upewnić, że obliczenia i znaczenie logiczne nie są podatne na błędy. Błędy w wyrażeniach spowodują problemy podczas oceniania wyrażenia, co może spowodować zmniejszenie wydajności i problemów z renderowaniem.

Jednym z typowych błędów w celu zapamiętania jest posiadanie wyrażenia, które opiera się na właściwości funkcji, która może nie istnieć we wszystkich funkcjach. Na przykład poniższy kod używa wyrażenia, aby ustawić właściwość Color warstwy bąbelkowej na `myColor` Właściwość funkcji.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: ['get', 'myColor']
});
```

Powyższy kod będzie działać prawidłowo, jeśli wszystkie funkcje w źródle danych mają `myColor` Właściwość, a wartość tej właściwości jest kolorem. Może to nie być problem, jeśli masz pełną kontrolę nad danymi w źródle danych i wiadomo, że niektóre wszystkie funkcje będą mieć prawidłowy kolor we `myColor` właściwości. Wspomniane w celu zapewnienia bezpieczeństwa kodu przed błędami `case` można użyć wyrażenia z `has` wyrażeniem, aby sprawdzić, czy funkcja ma `myColor` Właściwość. Jeśli tak, `to-color` można użyć wyrażenia typu, aby spróbować skonwertować wartość tej właściwości na kolor. Jeśli kolor jest nieprawidłowy, można użyć koloru rezerwowego. Poniższy kod ilustruje, jak to zrobić, i ustawia kolor rezerwowy na zielony.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Check to see if the feature has a 'myColor' property.
        ['has', 'myColor'],

        //If true, try validating that 'myColor' value is a color, or fallback to 'green'. 
        ['to-color', ['get', 'myColor'], 'green'],

        //If false, return a fallback value.
        'green'
    ]
});
```

### <a name="order-boolean-expressions-from-most-specific-to-least-specific"></a>Kolejność wyrażeń wartości logicznych od najbardziej konkretnych do najmniej określonych

Korzystając z wyrażeń logicznych zawierających wiele testów warunkowych, należy zamówić testy warunkowe od najbardziej konkretnych do najmniej określonych. Dzięki temu pierwszy warunek powinien zmniejszyć ilość danych, dla których należy przetestować drugi warunek, co zmniejsza łączną liczbę testów warunkowych, które należy wykonać.

### <a name="simplify-expressions"></a>Uprość wyrażenia

Wyrażenia mogą być zaawansowane i czasami złożone. Prostsze wyrażenie ma wartość, tym szybciej zostanie obliczone. Na przykład jeśli potrzebujesz prostego porównania, wyrażenie takie jak `['==', ['get', 'category'], 'restaurant']` byłoby lepsze niż użycie wyrażenia dopasowania, takiego jak `['match', ['get', 'category'], 'restaurant', true, false]` . W takim przypadku, jeśli sprawdzana właściwość jest wartością logiczną, `get` wyrażenie byłoby jeszcze prostsze `['get','isRestaurant']` .

## <a name="web-sdk-troubleshooting"></a>Rozwiązywanie problemów dotyczących zestawu SDK sieci Web

Poniżej przedstawiono kilka wskazówek dotyczących debugowania niektórych typowych problemów występujących podczas tworzenia przy użyciu zestawu SDK sieci Web Azure Maps.

**Dlaczego mapa nie jest wyświetlana podczas ładowania kontrolki sieci Web?**

Wykonaj następujące czynności:

* Upewnij się, że dodano dodane opcje uwierzytelniania do mapy. Jeśli ta wartość nie zostanie dodana, mapa zostanie załadowana z pustą kanwą, ponieważ nie może uzyskać dostępu do danych mapy podstawowej bez uwierzytelniania i 401 błędy pojawią się na karcie Sieć narzędzi deweloperskich w przeglądarce.
* Upewnij się, że masz połączenie z Internetem.
* Sprawdź, czy w konsoli są błędy narzędzi deweloperskich w przeglądarce. Niektóre błędy mogą spowodować, że mapowanie nie zostanie renderowane. Debuguj aplikację.
* Upewnij się, że używasz [obsługiwanej przeglądarki](supported-browsers.md).

**Wszystkie moje dane są wyświetlane po drugiej stronie świata, co się dzieje?**
Współrzędne, nazywane również pozycjami, w zestawach SDK Azure Maps są wyrównane do standardowego formatu branżowego `[longitude, latitude]` . Ten sam format jest również sposobem definiowania współrzędnych w schemacie GEOJSON. podstawowe dane sformatowane w ramach zestawów SDK Azure Maps. Jeśli dane są wyświetlane na przeciwległej stronie świata, najprawdopodobniej z powodu odwrócenia wartości długości i szerokości geograficznej w informacjach o koordynacji/położeniu.

**Dlaczego znaczniki HTML pojawiają się w niewłaściwym miejscu w kontrolce sieci Web?**

Elementy do sprawdzenia:

* W przypadku używania niestandardowej zawartości znacznika upewnij się, że `anchor` `pixelOffset` Opcje i są poprawne. Domyślnie dolny środek zawartości jest wyrównany do położenia na mapie.
* Upewnij się, że plik CSS dla Azure Maps został załadowany.
* Zbadaj element DOM znacznika HTML, aby sprawdzić, czy dołączono do niego dowolny arkusz CSS z aplikacji i ma wpływ na jego pozycję.

**Dlaczego ikony lub tekst w warstwie symboli pojawiają się w niewłaściwym miejscu?**
Sprawdź, czy `anchor` Opcje i `offset` są poprawnie skonfigurowane do dopasowania do części obrazu lub tekstu, które mają być wyrównane do współrzędnych mapy.
Jeśli symbol jest poza miejscem, gdy mapa jest obrócona, zaznacz `rotationAlignment` opcję. Domyślnie symbole są obracane za pomocą okienka ekranu mapy, tak aby były widoczne na stronie użytkownika. Jednak w zależności od danego scenariusza może być wskazane zablokowanie symbolu do orientacji mapy. Ustaw `rotationAlignment` opcję, aby `’map’` to zrobić.
Jeśli symbol znajduje się tylko w miejscu, gdy mapa jest pochylona/pochylenie, zaznacz `pitchAlignment` opcję. Domyślnie symbole pozostają pionowe z okienkiem ekranu mapy, ponieważ mapa jest pochylona. Jednakże, w zależności od danego scenariusza, może być pożądane zablokowanie symbolu do szerokości mapy. Ustaw `pitchAlignment` opcję, aby `’map’` to zrobić.

**Dlaczego żadne dane nie są wyświetlane na mapie?**

Elementy do sprawdzenia:

* Sprawdź błędy w konsoli programu w narzędziach deweloperskich w przeglądarce.
* Upewnij się, że źródło danych zostało utworzone i dodane do mapy oraz że źródło danych zostało połączone z warstwą renderowania, która została również dodana do mapy.
* Dodaj punkty przerwania w kodzie i przechodząc do niego, aby upewnić się, że dane są dodawane do źródła danych, a źródło danych i warstwy są dodawane do mapy bez wystąpienia błędów.
* Spróbuj usunąć wyrażenia sterowane danymi z warstwy renderowania. Istnieje możliwość, że jedna z nich może zawierać błąd, który powoduje problem.

**Czy można użyć zestawu Web SDK Azure Maps w elemencie iframe w trybie piaskownicy?**

Tak. Należy zauważyć, że [przeglądarka Safari zawiera usterkę](https://bugs.webkit.org/show_bug.cgi?id=170075) , która uniemożliwia uruchamianie procesów roboczych w trybie piaskownicy, co jest wymagane przez zestaw SDK sieci Web Azure Maps. Rozwiązaniem jest dodanie `"allow-same-origin"` znacznika do właściwości piaskownicy elementu iframe.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Poniżej przedstawiono różne sposoby uzyskiwania pomocy technicznej dla Azure Maps w zależności od danego problemu.

**Jak mogę zgłosić problem dotyczący danych lub problem z adresem?**

Azure Maps zawiera narzędzie do przesyłania opinii o danych, w którym można raportować i śledzić problemy z danymi. [https://feedback.azuremaps.com/](https://feedback.azuremaps.com/) Każdy przesłany problem generuje unikatowy adres URL, którego można użyć do śledzenia postępu problemu z danymi. Czas potrzebny na rozwiązanie problemu z danymi różni się w zależności od typu problemu i sposobu, w jaki można sprawdzić, czy zmiana jest poprawna. Po naprawieniu usługa renderowania zobaczy aktualizację w cotygodniowej aktualizacji, podczas gdy inne usługi, takie jak geokodowanie i routing, będą widzieć aktualizację w comiesięcznej aktualizacji. Szczegółowe instrukcje dotyczące zgłaszania problemu dotyczącego danych znajdują się w tym [dokumencie](how-to-use-feedback-tool.md).

**Jak mogę zgłosić usterkę w usłudze lub interfejsie API?**

https://azure.com/support

**Gdzie mogę uzyskać pomoc techniczną dotyczącą Azure Maps?**

Jeśli jest to powiązane z wizualizacją Azure Maps w Power BI: https://powerbi.microsoft.com/support/ dla wszystkich innych Azure Maps usług: https://azure.com/support lub forów deweloperów: https://docs.microsoft.com/answers/topics/azure-maps.html

**Jak mogę utworzyć żądanie funkcji?**

Utwórz żądanie funkcji w naszej witrynie głosowej użytkownika: https://feedback.azure.com/forums/909172-azure-maps

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby uzyskać więcej porad dotyczących ulepszania środowiska użytkownika w aplikacji.

> [!div class="nextstepaction"]
> [Udostępnianie aplikacji](map-accessibility.md)

Dowiedz się więcej o terminologii używanej przez Azure Maps i branży geograficznej.

> [!div class="nextstepaction"]
> [Słownik usługi Azure Maps](glossary.md)
