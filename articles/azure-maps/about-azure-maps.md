---
title: Przegląd | Mapy Microsoft Azure
description: Dowiedz się więcej o usługach i możliwościach w usłudze Microsoft Azure Maps i sposobach ich używania w aplikacjach.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, references_regions
ms.openlocfilehash: 2b6c61c5c144923ece80fddf8a1412d68f35ad52
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86242881"
---
# <a name="what-is-azure-maps"></a>Co to jest usługa Azure Maps?

Usługa Azure Maps to zbiór usług geoprzestrzennych wykorzystujących aktualne dane z map w celu zapewnienia kontekstu geograficznego do użycia w aplikacjach internetowych i mobilnych. Azure Maps zapewnia:

* Interfejsy API REST do renderowania map w wielu stylach i obrazach satelitarnych. ' '
* Usługi wyszukiwania umożliwiają lokalizowanie adresów, miejsc i punktów orientacyjnych na całym świecie.
* Różne opcje routingu; takie jak punkt-punkt, funkcje MultiPoint, optymalizacja MultiPoint, isochroney, pojazd komercyjny, wpływ na ruch i Routing macierzy.
* Widok przepływu ruchu i widok zdarzeń dla aplikacji, które wymagają informacji o ruchu.
* Usługa mobilności, która żąda reklamy publicznego tranzytu, planowania tras w czasie rzeczywistym oraz żądania informacji dla alternatywnych trybów transportu.
* Strefy czasowe i usługi geolokalizacyjne oraz konwertowanie lokalizacji na strefy czasowe.
* Usługa geogeofencingu i mapowanie magazynu danych przy użyciu informacji o lokalizacji hostowanej na platformie Azure. 
* Analiza lokalizacji przy użyciu analizy geograficznej. 

Ponadto usługi Azure Maps są dostępne za pomocą zestawu SDK sieci Web lub Android SDK. Te narzędzia ułatwiają deweloperom szybkie tworzenie i skalowanie rozwiązań, które integrują informacje o lokalizacji z rozwiązaniami platformy Azure. 

Możesz zarejestrować się w celu uzyskania bezpłatnego [konta Azure Maps](https://azure.microsoft.com/services/azure-maps/) i rozpocząć tworzenie aplikacji.

W poniższym filmie wideo wyjaśniono dokładnie działanie usługi Azure Maps:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Kontrolki mapy

### <a name="web-sdk"></a>Zestaw SDK sieci Web

Zestaw SDK sieci Web Azure Maps umożliwia dostosowywanie interaktywnych map przy użyciu własnej zawartości i obrazów. Tej mapy interaktywnej można używać zarówno dla aplikacji sieci Web, jak i mobilnych. Kontrolka mapy korzysta z WebGL, dzięki czemu można renderować duże zestawy danych o wysokiej wydajności. Programowanie za pomocą zestawu SDK przy użyciu języka JavaScript lub TypeScript.

![Przykładowa Mapa zmiany populacji](media/about-azure-maps/intro_web_map_control.png)

### <a name="android-sdk"></a>Android SDK

Użyj Android SDK Azure Maps, aby utworzyć aplikacje do mapowania aplikacji mobilnych. 

![Przykłady mapowania na urządzeniu przenośnym](media/about-azure-maps/android_sdk.png)

## <a name="services-in-azure-maps"></a>Usługi Azure Maps

Azure Maps składa się z następujących dziewięciu usług, które mogą zapewnić kontekst geograficzny aplikacji platformy Azure.

### <a name="data-service"></a>Usługa danych

Dane są konieczne dla map. Usługa danych umożliwia przekazywanie i przechowywanie danych geoprzestrzennych do użycia z operacjami przestrzennymi i kompozycjami obrazów.  Przełączenie danych klienta bliżej usługi Azure Maps spowoduje skrócenie opóźnienia, zwiększenie produktywności i utworzenie nowych scenariuszy w aplikacjach. Aby uzyskać szczegółowe informacje na temat tej usługi, zobacz [dokumentację interfejsu API usługi danych](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Usługa mobilności

Usługa mobilności Azure Maps umożliwia planowanie podróży w czasie rzeczywistym. Zwraca najlepsze możliwe opcje trasy i oferuje różne rodzaje trybów podróży. W przypadku obszarów Metro (miasto) te tryby mogą obejmować przechodzenie między nimi i tranzytem publicznym. Możesz zażądać podróży tranzytowej, geometrii liniowej, list zatrzymań, zaplanowanych przyjęć, przyjęć w czasie rzeczywistym i alertów dotyczących usług.

Usługa umożliwia również wyszukiwanie określonych typów obiektów wokół lokalizacji. Użytkownicy mogą wyszukiwać udostępnione rowery, Scooters lub samochody wokół lokalizacji. Użytkownicy mogą zażądać liczby dostępnych elementów Bikes w najbliższym doku i przeszukiwać dostępne pojazdy do udostępniania przez siebie. Ponadto użytkownicy mogą znaleźć szczegółowe informacje, takie jak przyszłe udostępnienie pojazdów i bieżącego poziomu paliwa.

Aby dowiedzieć się więcej na temat usługi, zobacz [dokumentację interfejsu API mobilności](https://docs.microsoft.com/rest/api/maps/mobility).

### <a name="render-service"></a>Render Service

Usługa renderowania ułatwia deweloperom tworzenie aplikacji sieci Web i mobilnych przy użyciu funkcji mapowania. Usługa używa rastrowych obrazów graficznych o wysokiej jakości — dostępnych z 19 poziomami powiększenia — lub w pełni konfigurowalnych obrazów map w formacie wektorowym.

![Przykład mapy z usługi renderowania](media/about-azure-maps/intro_map.png)

Usługa Render Service oferuje teraz interfejsy API w wersji zapoznawczej umożliwiające deweloperom pracę z obrazami satelitarnymi. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją interfejsu API renderowania](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Route Service

Usługa Route Service zawiera niezawodne obliczenia geometryczne rzeczywistej infrastruktury oraz tras dla wielu środków transportu. Usługa umożliwia deweloperom Obliczanie kierunków w różnych trybach podróży, takich jak samochód, ciężarówka, rower lub przechodzenie. Usługa uwzględnia również dane wejściowe, takie jak warunki ruchu, ograniczenia wagi lub transport materiału niebezpiecznego.

![Przykład mapy z usługi Route Service](media/about-azure-maps/intro_route.png)

Usługa Route Service oferuje wersję zapoznawczą zaawansowanych funkcji, takich jak: 

* Przetwarzanie wsadowe wielu żądań trasy.
* Macierze czasu podróży i odległości między zestawem źródeł i miejscami docelowymi.
* Znajdowanie tras lub odległości, które użytkownicy mogą podróżować na podstawie wymagań dotyczących czasu lub paliwa. 

Aby uzyskać szczegółowe informacje o możliwościach routingu, zapoznaj się z [dokumentacją interfejsu API tras](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Search Service

Usługa Search ułatwia deweloperom wyszukiwanie adresów, miejsc, aukcji w biznesie według nazwy lub kategorii oraz innych informacji geograficznych. Usługa wyszukiwania może [odwrócić adresy geokodowe](https://en.wikipedia.org/wiki/Reverse_geocoding) i krzyżyki na podstawie Latitudes i długości geograficznej.

![Przykład wyszukiwania na mapie](media/about-azure-maps/intro_search.png)

Usługa Search oferuje także zaawansowane funkcje, takie jak:

* Wyszukiwanie wzdłuż trasy.
* Przeszukaj w szerszym obszarze.
* Tworzenie wsadowe grup żądań wyszukiwania.
* Wyszukaj większy obszar zamiast punktu lokalizacji. 

Interfejsy API do wyszukiwania grupowego i wyszukiwania obszaru są obecnie dostępne w wersji zapoznawczej. Aby uzyskać więcej informacji na temat możliwości wyszukiwania, Przeczytaj [dokumentację interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Usługa operacji przestrzennych

Usługa operacji przestrzennych Azure Maps pobiera informacje o lokalizacji. Pozwala ona szybko analizować informacje o lokalizacji, aby pomóc w poinformowaniu klientów o bieżących zdarzeniach w czasie i w miejscu. Umożliwia analizę niemal w czasie rzeczywistym i predykcyjne modelowanie zdarzeń. 

Usługa pozwala klientom ulepszyć swoją analizę lokalizacji za pomocą biblioteki wspólnych obliczeń matematycznych geograficznych. Typowe obliczenia obejmują Najbliższe punkty, duże kółka i bufory. Aby dowiedzieć się więcej o usłudze i różnych funkcjach, Przeczytaj [dokumentację interfejsu API operacje przestrzenne](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Time Zone Service

Usługa strefa czasowa umożliwia wykonywanie zapytań dotyczących bieżących, historycznych i przyszłych informacji o strefie czasowej. Można użyć pary szerokość i Długość geograficzna lub [identyfikatora organizacji Iana](https://www.iana.org/). Usługa strefy czasowej umożliwia również:

* Konwertowanie identyfikatorów strefy czasowej systemu Microsoft Windows na strefy czasowe organizacji IANA.
* Pobieranie przesunięcia strefy czasowej do czasu UTC.
* Pobieranie bieżącego czasu w wybranej strefie czasowej. 

Typowa odpowiedź JSON dla zapytania do usługi strefy czasowej wygląda jak w następującym przykładzie:

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

Aby uzyskać szczegółowe informacje na temat tej usługi, Przeczytaj [dokumentację interfejsu API strefy czasowej](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Traffic Service

Usługa Traffic Service to zestaw usług sieci Web, których deweloperzy mogą używać dla aplikacji sieci Web lub mobilnych, które wymagają informacji o ruchu. Usługa udostępnia dwa typy danych:

* Przepływ ruchu: zaobserwowane szybkości i czasy podróży dla wszystkich kluczowych dróg w sieci.
* Zdarzenia dotyczące ruchu: aktualny widok zakleszczenia ruchu i zdarzeń w sieci drogowej.

![Przykład mapy z informacjami o ruchu](media/about-azure-maps/intro_traffic.png)

Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API ruchu](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location-service"></a>Adres IP do usługi lokalizacji

Użyj funkcji IP do lokalizacji, aby wyświetlić podgląd pobranego dwuliterowego kodu kraju/regionu dla adresu IP. Ta usługa może pomóc zwiększyć komfort pracy użytkowników, dostarczając dostosowanej zawartości aplikacji na podstawie lokalizacji geograficznej.

Aby uzyskać więcej informacji o usłudze IP do lokalizacji w interfejsie API REST, zapoznaj się z [dokumentacją interfejsu api Azure Maps geolokalizacji](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Model programowania

Azure Maps jest zbudowana na potrzeby mobilności i mogą pomóc w tworzeniu aplikacji dla wielu platform. Używa modelu programowania niezależny od języka i obsługuje dane wyjściowe JSON za pośrednictwem [interfejsów API REST](https://docs.microsoft.com/rest/api/maps/).

Ponadto Azure Maps oferuje wygodną [kontrolkę mapy JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) z prostym modelem programowania. Programowanie jest szybkie i łatwe w przypadku aplikacji sieci Web i mobilnych.

## <a name="power-bi-visual"></a>Wizualizacja usługi Power BI

Wizualizacja Azure Maps dla Power BI oferuje bogaty zestaw danych graficznych dla danych przestrzennych na mapie. Szacuje się, że ponad 80% danych gospodarczych ma kontekst lokalizacji. Wizualizacja Azure Maps oferuje niekodowe rozwiązanie do uzyskiwania wglądu w sposób odnoszący się do tego kontekstu lokalizacji i ma wpływ na dane biznesowe.

![Power BI pulpicie z wizualizacją Azure Maps wyświetlającą dane biznesowe](media/power-bi-visual/azure-maps-visual-hero.png)

Aby uzyskać więcej informacji, zobacz artykuł Wprowadzenie do [Azure Maps Power BI dokumentacji wizualnej](power-bi-visual-getting-started.md) .

## <a name="usage"></a>Użycie

Uzyskiwanie dostępu do Azure Maps usług jest kwestią przechodzenia do [Azure Portal](https://portal.azure.com) i tworzenia konta Azure Maps.

Usługa Azure Maps korzysta ze schematu uwierzytelniania opartego na kluczach. Twoje konto zawiera dwa klucze już wygenerowane dla Ciebie, Użyj obu kluczy. Rozpocznij integrację możliwości tej lokalizacji w aplikacji i wprowadź żądania do usług Azure Maps.

Uwaga: Azure Maps udostępnia zapytania dotyczące adresu i lokalizacji dostarczone przez klienta ("zapytania") za pomocą TomTom innych firm na potrzeby mapowania funkcji. Zapytania nie są połączone z żadnym klientem ani użytkownikiem końcowym w przypadku udostępnienia z TomTom i nie mogą być używane do identyfikowania osób. Firma Microsoft jest obecnie w trakcie dodawania TomTom do listy podwykonawców usług online. Należy pamiętać, że usługi mobilności i pogody, które obejmują integrację z Moovit i AccuWeather są obecnie w [wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-regions"></a>Obsługiwane regiony

Interfejsy API Azure Maps są obecnie dostępne we wszystkich krajach i regionach, z wyjątkiem następujących:

* Chiny
* Korea Południowa

Sprawdź, czy lokalizacja bieżącego adresu IP znajduje się w obsługiwanym kraju/regionie.

## <a name="next-steps"></a>Następne kroki

Wypróbuj przykładową aplikację, która przedstawia Azure Maps:

> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie aplikacji sieci Web](quick-demo-map-app.md)

Bądź na bieżąco z Azure Maps: 

> [!div class="nextstepaction"]
> [Blog Azure Maps](https://azure.microsoft.com/blog/topics/azure-maps/)
