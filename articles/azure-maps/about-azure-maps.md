---
title: Przegląd Microsoft Azure Maps
description: Dowiedz się więcej o usługach i możliwościach w usłudze Microsoft Azure Maps i sposobach ich używania w aplikacjach.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, references_regions
ms.openlocfilehash: fc70e6370f53848e9f3672611a3dfda685bcb9fe
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011780"
---
# <a name="what-is-azure-maps"></a>Co to jest usługa Azure Maps?

Azure Maps to zbiór usług geoprzestrzennych i zestawów SDK, które wykorzystują dane umożliwiające odwzorowania geograficznego do obsługi aplikacji sieci Web i mobilnych. Azure Maps zapewnia:

* Interfejsy API REST do renderowania map wektorowych i rastrowych w wielu stylach i obrazach satelitarnych.
* Usługi Creator Services (wersja zapoznawcza) do tworzenia i renderowania map na podstawie prywatnych danych mapy pomieszczeń.
* Usługi wyszukiwania umożliwiają lokalizowanie adresów, miejsc i punktów orientacyjnych na całym świecie.
* Różne opcje routingu; takie jak punkt-punkt, funkcje MultiPoint, optymalizacja MultiPoint, isochrone, pojazdy elektryczne, pojazdy komercyjne, wpływ na ruch i Routing macierzy.
* Widok przepływu ruchu i widoku zdarzeń dla aplikacji, które wymagają informacji o ruchu w czasie rzeczywistym.
* Usługi mobilności (wersja zapoznawcza) do żądania informacji o tranzycie publicznym, planowania tras przez mieszanie różnych trybów podróży i przyjęć w czasie rzeczywistym.
* Usługi strefy czasowej i geolokalizacji (wersja zapoznawcza).
* Usługi podniesienia uprawnień z modelem podniesienia uprawnień cyfrowych
* Usługa geogeofencingu i mapowanie magazynu danych przy użyciu informacji o lokalizacji hostowanej na platformie Azure.
* Analiza lokalizacji przy użyciu analizy geograficznej.

Ponadto usługi Azure Maps są dostępne za pomocą zestawu Web SDK i Android SDK. Te narzędzia ułatwiają deweloperom szybkie tworzenie i skalowanie rozwiązań, które integrują informacje o lokalizacji z rozwiązaniami platformy Azure.

Możesz zarejestrować się w celu uzyskania bezpłatnego [konta Azure Maps](https://azure.microsoft.com/services/azure-maps/) i rozpocząć tworzenie aplikacji.

W poniższym filmie wideo wyjaśniono dokładnie działanie usługi Azure Maps:

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny]

## <a name="map-controls"></a>Kontrolki mapy

### <a name="web-sdk"></a>Zestaw SDK sieci Web

Zestaw SDK sieci Web Azure Maps umożliwia dostosowywanie interaktywnych map przy użyciu własnej zawartości i obrazów. Tej mapy interaktywnej można używać zarówno dla aplikacji sieci Web, jak i mobilnych. Kontrolka mapy korzysta z WebGL, dzięki czemu można renderować duże zestawy danych o wysokiej wydajności. Można opracowywać z zestawem SDK przy użyciu języka JavaScript lub TypeScript.

:::image type="content" source="./media/about-azure-maps/intro_web_map_control.png" alt-text="Przykładowa Mapa zmiany populacji utworzona przy użyciu Azure Maps Web SDK":::

### <a name="android-sdk"></a>Android SDK

Użyj Android SDK Azure Maps, aby utworzyć aplikacje do mapowania aplikacji mobilnych.

:::image type="content" source="./media/about-azure-maps/android_sdk.png" border="false" alt-text="Przykłady mapowania na urządzeniu przenośnym":::

## <a name="services-in-azure-maps"></a>Usługi Azure Maps

Azure Maps składa się z następujących usług, które mogą zapewnić kontekst geograficzny aplikacji platformy Azure.

### <a name="data-service-preview"></a>Usługa danych (wersja zapoznawcza)

Dane są konieczne dla map. Usługa danych umożliwia przekazywanie i przechowywanie danych geoprzestrzennych do użycia z operacjami przestrzennymi i kompozycjami obrazów.  Przełączenie danych klienta bliżej usługi Azure Maps spowoduje skrócenie opóźnienia, zwiększenie produktywności i utworzenie nowych scenariuszy w aplikacjach. Aby uzyskać szczegółowe informacje na temat tej usługi, zobacz [dokumentację usługi danych](/rest/api/maps/data).

### <a name="geolocation-service-preview"></a>Usługa geolokalizacji (wersja zapoznawcza)

Użyj usługi geolokalizacji, aby wyświetlić podgląd pobranego dwuliterowego kodu kraju/regionu dla adresu IP. Ta usługa może pomóc zwiększyć komfort pracy użytkowników, dostarczając dostosowanej zawartości aplikacji na podstawie lokalizacji geograficznej.

Aby uzyskać więcej informacji, Przeczytaj [dokumentację dotyczącą usługi geolokalizacyjnej](/rest/api/maps/geolocation).

### <a name="mobility-services-preview"></a>Usługi mobilności (wersja zapoznawcza) 

Usługi mobilności Azure Maps zwiększają czas projektowania aplikacji z funkcjami tranzytu publicznego, takimi jak routing tranzytowy i wyszukiwanie w pobliżu tranzytu publicznego. Użytkownicy mogą pobierać szczegółowe informacje o zatrzymaniu, wierszach i harmonogramach tranzytu. Usługa mobilności umożliwia również użytkownikom pobieranie zatrzymań i geometrie wierszy, alertów dotyczących punktów przerwania, wierszy i usług oraz przychodzących tranzytów publicznych i alertów usług w czasie rzeczywistym. Ponadto usługi mobilności udostępniają możliwości routingu z opcjami planowania podróży Multimodal. Multimodale w trakcie podróży, w jednej podróży, umożliwia planowanie przechodzenia między firmami i opcjami tranzytu publicznego. Użytkownicy mogą również uzyskać dostęp do szczegółowych multimodalów krok po kroku.

Aby dowiedzieć się więcej na temat usługi, zobacz [dokumentację usług mobilności](/rest/api/maps/mobility).

### <a name="render-service"></a>Render Service

[Usługa renderowania w wersji 2 (wersja zapoznawcza)](/rest/api/maps/renderv2) zawiera nową wersję [interfejsu API uzyskiwania kafelków mapy](/rest/api/maps/renderv2/getmaptilepreview)w wersji 2. Interfejs API uzyskiwania mapy kafelka v2 umożliwia teraz klientom żądanie Azure Maps kafelków dróg, kafelków pogody lub kafelków mapy utworzonych przy użyciu Azure Maps Creator. Zalecamy użycie nowego interfejsu API programu Get map kafelka v2.  

:::image type="content" source="./media/about-azure-maps/intro_map.png" border="false" alt-text="Przykład mapy z usługi renderowania w wersji 2":::

Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją usługi renderowania w wersji 2](/rest/api/maps/renderv2).

Aby dowiedzieć się więcej na temat usługi renderowania w wersji 1 (ogólnie dostępna), zobacz [dokumentację usługi renderowania w wersji 1](/rest/api/maps/render).  

### <a name="route-service"></a>Route Service

Usługi Route Services mogą służyć do obliczania szacowanego czasu przybycia (ETAs) dla każdej żądanej trasy. Interfejsy API tras uwzględniają czynniki, takie jak informacje o ruchu w czasie rzeczywistym i historyczne dane o ruchu, takie jak typowe szybkości jazdy w dniu tygodnia i godziny dnia. Interfejsy API zwracają najkrótsze lub najszybsze trasy dostępne dla wielu miejsc docelowych jednocześnie w sekwencji lub w kolejności zoptymalizowanej na podstawie czasu lub odległości. Usługa umożliwia deweloperom Obliczanie kierunków w kilku trybach podróży, takich jak samochód, ciężarówka, rower lub pojazd elektryczny. Usługa uwzględnia również dane wejściowe, takie jak czas wyruszenia, ograniczenia wagi lub transport materiału niebezpiecznego.

:::image type="content" source="./media/about-azure-maps/intro_route.png" border="false" alt-text="Przykład mapy z usługi Route Service":::

Usługa Route oferuje zaawansowane funkcje zestawu, takie jak:

* Przetwarzanie wsadowe wielu żądań trasy.
* Macierze czasu podróży i odległości między zestawem źródeł i miejscami docelowymi.
* Znajdowanie tras lub odległości, które użytkownicy mogą podróżować na podstawie wymagań dotyczących czasu lub paliwa.

Aby uzyskać szczegółowe informacje o możliwościach routingu, Przeczytaj [dokumentację usługi Route Service](/rest/api/maps/route).

### <a name="search-service"></a>Search Service

Usługa Search ułatwia deweloperom wyszukiwanie adresów, miejsc, aukcji w biznesie według nazwy lub kategorii oraz innych informacji geograficznych. Ponadto usługi mogą [odwracać adresy geokodowe](https://en.wikipedia.org/wiki/Reverse_geocoding) i krzyżyki na podstawie Latitudes i długości geograficznej.

:::image type="content" source="./media/about-azure-maps/intro_search.png" border="false" alt-text="Przykład wyszukiwania na mapie":::

Usługa Search oferuje także zaawansowane funkcje, takie jak:

* Wyszukiwanie wzdłuż trasy.
* Przeszukaj w szerszym obszarze.
* Tworzenie wsadowe grup żądań wyszukiwania.
* Przeszukaj elektrycznie naliczanie stacji i danych punktu orientacyjnego (punkt POI) według nazwy marki.

Aby uzyskać więcej informacji na temat możliwości wyszukiwania, Przeczytaj [dokumentację usługi wyszukiwania](/rest/api/maps/search).

### <a name="spatial-service"></a>Usługa danych przestrzennych

Usługa przestrzenny szybko analizuje informacje o lokalizacji, aby pomóc w poinformowaniu klientów o bieżących zdarzeniach w czasie i w miejscu. Umożliwia analizę niemal w czasie rzeczywistym i predykcyjne modelowanie zdarzeń.

Usługa pozwala klientom ulepszyć swoją analizę lokalizacji za pomocą biblioteki wspólnych obliczeń matematycznych geograficznych. Typowe obliczenia obejmują Najbliższe punkty, duże kółka i bufory. Aby dowiedzieć się więcej o usłudze i różnych funkcjach, Przeczytaj [dokumentację usługi przestrzennej](/rest/api/maps/spatial).

### <a name="timezone-service"></a>Timezone Service

Usługa strefa czasowa umożliwia wykonywanie zapytań dotyczących bieżących, historycznych i przyszłych informacji o strefie czasowej. Jako danych wejściowych można użyć par szerokość i Długość geograficzna lub [identyfikator organizacji Iana](https://www.iana.org/) . Usługa strefy czasowej umożliwia również:

* Konwertowanie identyfikatorów strefy czasowej systemu Microsoft Windows na strefy czasowe organizacji IANA.
* Pobieranie przesunięcia strefy czasowej do czasu UTC.
* Pobieranie bieżącego czasu w wybranej strefie czasowej.

Typowa odpowiedź JSON dla zapytania do usługi strefy czasowej wygląda jak w następującym przykładzie:

```JSON
{
  "Version": "2020a",
  "ReferenceUtcTimestamp": "2020-07-31T19:15:14.4570053Z",
  "TimeZones": [
    {
      "Id": "America/Los_Angeles",
      "Names": {
        "ISO6391LanguageCode": "en",
        "Generic": "Pacific Time",
        "Standard": "Pacific Standard Time",
        "Daylight": "Pacific Daylight Time"
      },
      "ReferenceTime": {
        "Tag": "PDT",
        "StandardOffset": "-08:00:00",
        "DaylightSavings": "01:00:00",
        "WallTime": "2020-07-31T12:15:14.4570053-07:00",
        "PosixTzValidYear": 2020,
        "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
      }
    }
  ]
}
```

Aby uzyskać szczegółowe informacje na temat tej usługi, Przeczytaj [dokumentację usługi czasowej](/rest/api/maps/timezone).

### <a name="traffic-service"></a>Traffic Service

Usługa Traffic Service to zestaw usług sieci Web, których deweloperzy mogą używać dla aplikacji sieci Web lub mobilnych, które wymagają informacji o ruchu. Usługa udostępnia dwa typy danych:

* Przepływ ruchu: zaobserwowane szybkości i czasy podróży dla wszystkich kluczowych dróg w sieci.
* Zdarzenia dotyczące ruchu: aktualny widok zakleszczenia ruchu i zdarzeń w sieci drogowej.

![Przykład mapy z informacjami o ruchu](media/about-azure-maps/intro_traffic.png)

Aby uzyskać więcej informacji, zobacz [dokumentację usługi Traffic Service](/rest/api/maps/traffic).

### <a name="weather-services-preview"></a>Usługi pogodowe (wersja zapoznawcza) 

Usługi pogody oferują interfejsy API, których deweloperzy mogą używać do pobierania informacji o pogodzie dla określonej lokalizacji. Informacje te zawierają szczegóły, takie jak Data i godzina obserwacji, Krótki opis warunków pogodowych, ikona Pogoda, flagi wskaźnika opadów, temperatura i informacje o szybkości wiatru. Zwracane są również dodatkowe szczegóły, takie jak RealFeel™ temperatura i indeks UV.

Deweloperzy mogą używać [interfejsu API Get Pogoda i trasy](/rest/api/maps/weather/getweatheralongroute) , aby pobierać informacje o pogodzie i określonej trasie. Ponadto usługa obsługuje generowanie powiadomień pogodowych dla waypoints, na które mają wpływ ryzyko pogodowe, takie jak zalanie lub ciężki deszcz.

[Interfejs API uzyskiwania mapy kafelka v2](/rest/api/maps/renderv2/getmaptilepreview) umożliwia żądanie przeszłych, bieżących i przyszłych kafelków radarowych i satelitarnych.

![Przykład mapy z kafelkami radaru w czasie rzeczywistym](media/about-azure-maps/intro_weather.png)

### <a name="maps-creator-service-preview"></a>Usługa Maps — twórca (wersja zapoznawcza) 

Usługa Maps Creator Service to pakiet usług sieci Web, których deweloperzy mogą używać do tworzenia aplikacji z funkcjami mapy opartymi na danych mapy pomieszczeń.

Twórca Maps oferuje trzy podstawowe usługi:

* [Usługa DataSet](/rest/api/maps/dataset). Użyj usługi DataSet, aby utworzyć zestaw danych na podstawie przekonwertowanych danych pakietu rysunku. Aby uzyskać informacje o wymaganiach dotyczących pakietów rysowania, Zobacz Rysowanie wymagań pakietu.

* [Usługa konwersji](/rest/api/maps/dataset). Użyj usługi konwersji, aby skonwertować plik projektu DWG do rysowania danych pakietu dla map pomieszczeń.

* [Usługa tileset](/rest/api/maps/tileset). Użyj usługi tileset, aby utworzyć reprezentację zestawu danych opartego na wektorach. Aplikacje mogą używać tileset, aby przedstawić wizualny widok zestawu danych oparty na kafelkach.

* [Usługa stanu funkcji](/rest/api/maps/featurestate). Użyj usługi stanu funkcji do obsługi stylów mapy dynamicznej. Funkcja stylów mapy dynamicznej umożliwia aplikacjom odzwierciedlenie zdarzeń w czasie rzeczywistym w miejscach dostarczonych przez systemy IoT.

* [Usługa WFS](/rest/api/maps/featurestate). Użyj usługi WFS, aby wykonać zapytanie dotyczące danych mapy pomieszczeń. Usługa WFS jest zgodna ze standardami [interfejsu API Open Geospatial Consortium](http://docs.opengeospatial.org/is/17-069r3/17-069r3.html) na potrzeby wykonywania zapytań dotyczących pojedynczego zestawu danych.

### <a name="elevation-service-preview"></a>Usługa podniesienia uprawnień (wersja zapoznawcza)

Usługa podniesienia uprawnień Azure Maps to usługa sieci Web, której deweloperzy mogą używać do pobierania podniesienia uprawnień z dowolnego miejsca na powierzchni ziemi.

Usługa podniesienia uprawnień umożliwia pobieranie danych podniesienia uprawnień w dwóch formatach:

* **Format rastrowy GeoTIFF**. Użyj [interfejsu API renderowania w wersji 2-get mapy](/rest/api/maps/renderv2) , aby pobrać dane podniesienia uprawnień w formacie kafelka.

* **Format GEOJSON**. Użyj [interfejsów API podniesienia uprawnień](/rest/api/maps/elevation) , aby zażądać próbkowania podniesionych danych wzdłuż ścieżek, wewnątrz zdefiniowanego pola ograniczenia lub określonych współrzędnych. 

:::image type="content" source="./media/about-azure-maps/elevation.png" alt-text="Przykład mapy z danymi podniesienia uprawnień":::


## <a name="programming-model"></a>Model programowania

Azure Maps jest zbudowana na potrzeby mobilności i mogą pomóc w tworzeniu aplikacji dla wielu platform. Używa modelu programowania niezależny od języka i obsługuje dane wyjściowe JSON za pośrednictwem [interfejsów API REST](/rest/api/maps/).

Ponadto Azure Maps oferuje wygodną [kontrolkę mapy JavaScript](/javascript/api/azure-maps-control) z prostym modelem programowania. Programowanie jest szybkie i łatwe w przypadku aplikacji sieci Web i mobilnych.





## <a name="power-bi-visual"></a>Wizualizacja usługi Power BI

Wizualizacja Azure Maps dla Power BI oferuje bogaty zestaw danych graficznych dla danych przestrzennych na mapie. Szacuje się, że ponad 80% danych gospodarczych ma kontekst lokalizacji. Wizualizacja Azure Maps oferuje niekodowe rozwiązanie do uzyskiwania wglądu w sposób odnoszący się do tego kontekstu lokalizacji i ma wpływ na dane biznesowe.

:::image type="content" source="./media/about-azure-maps/intro-power-bi.png" border="false" alt-text="Power BI pulpicie z wizualizacją Azure Maps wyświetlającą dane biznesowe":::

Aby uzyskać więcej informacji, zobacz artykuł Wprowadzenie do [Azure Maps Power BI dokumentacji wizualnej](power-bi-visual-getting-started.md) .

## <a name="usage"></a>Użycie

Aby uzyskać dostęp do usług Azure Maps, przejdź do [Azure Portal](https://portal.azure.com) i utwórz konto Azure Maps.

Usługa Azure Maps korzysta ze schematu uwierzytelniania opartego na kluczach. Podczas tworzenia konta generowane są dwa klucze. Aby przeprowadzić uwierzytelnianie w ramach usług Azure Maps Services, możesz użyć dowolnego klucza.

Uwaga: Azure Maps udostępnia zapytania dotyczące adresu i lokalizacji dostarczone przez klienta ("zapytania") za pomocą TomTom innych firm na potrzeby mapowania funkcji. Zapytania nie są połączone z żadnym klientem ani użytkownikiem końcowym, gdy są udostępniane za pomocą TomTom i nie mogą być używane do identyfikowania osób. Usługi mobilności i pogody, które obejmują integrację z usługą Moovit, i AccuWeather są obecnie w [wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Firma Microsoft jest obecnie w trakcie dodawania TomTom, Moovit i AccuWeather do listy podwykonawców usług online.

## <a name="supported-regions"></a>Obsługiwane regiony

Usługi Azure Maps są obecnie dostępne z wyjątkiem następujących krajów/regionów:

* Chiny
* Korea Południowa

Sprawdź, czy lokalizacja bieżącego adresu IP znajduje się w obsługiwanym kraju/regionie.

## <a name="next-steps"></a>Następne kroki

Wypróbuj przykładową aplikację, która przedstawia Azure Maps:

[Szybki Start: Tworzenie aplikacji sieci Web](quick-demo-map-app.md)

Bądź na bieżąco z Azure Maps:

[Blog Azure Maps](https://azure.microsoft.com/blog/topics/azure-maps/)