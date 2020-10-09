---
title: Migrowanie usług internetowych z usługi Google Maps | Mapy Microsoft Azure
description: Jak przeprowadzić migrację usług sieci Web z usługi Google Maps do Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 57d71d517cb953a2a2c84b7e003fd08541416539
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319575"
---
# <a name="migrate-web-service-from-google-maps"></a>Migruj usługę sieci Web z usługi Google Maps

Zarówno platforma Azure, jak i usługa Google Maps zapewniają dostęp do przestrzennych interfejsów API za pomocą usług sieci Web REST. Interfejsy interfejsu API tych platform wykonują podobne funkcje. Ale każdy z nich używa różnych konwencji nazewnictwa i obiektów odpowiedzi.

W tabeli przedstawiono interfejsy API usługi Azure Maps, które mają podobną funkcjonalność do listy interfejsów API usługi Mapy Google.

| Interfejs API usługi Mapy Google | Interfejs API usługi Azure Maps                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Wskazówki              | [Trasa](https://docs.microsoft.com/rest/api/maps/route)                                     |
| Macierz odległości         | [Macierz trasy](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)       |
| Geokodowanie               | [Wyszukiwanie](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Wyszukiwanie miejsc           | [Wyszukiwanie](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Umieść Autouzupełnianie      | [Wyszukiwanie](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Przyciągaj do drogi            | Zobacz sekcję [obliczanie tras i wskazówek](#calculate-routes-and-directions) .            |
| Limity szybkości            | Zobacz sekcję [Odwróć do kodu współrzędnej](#reverse-geocode-a-coordinate) .                  |
| Mapa statyczna              | [Renderowanie](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                       |
| Strefa czasowa               | [Strefa czasowa](https://docs.microsoft.com/rest/api/maps/timezone)                              |

Następujące interfejsy API usługi nie są obecnie dostępne w Azure Maps:

- Noszeniu
- Geolokalizacja
- Umieszcza szczegóły i zdjęcia — numery telefonów i adresy URL witryn internetowych są dostępne w interfejsie API wyszukiwania Azure Maps.
- Adresy URL map
- Najbliższe drogi — można to uzyskać za pomocą zestawu SDK sieci Web, jak pokazano [tutaj](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic
), ale nie jest to obecnie dostępne jako usługa.
- Widok statyczny

Azure Maps ma kilka dodatkowych usług sieci Web REST, które mogą być interesujące:

- [Operacje przestrzenne](https://docs.microsoft.com/rest/api/maps/spatial): odciążać złożone obliczenia przestrzenne i operacje, takie jak geoogrodzenia, do usługi.
- [Ruch](https://docs.microsoft.com/rest/api/maps/traffic): dostęp do przepływu ruchu i danych zdarzeń w czasie rzeczywistym.

## <a name="geocoding-addresses"></a>Adresy geokodowania

Geokodowanie jest procesem konwertowania adresu na współrzędną. Na przykład "1 Microsoft Way, Redmond, WA" jest konwertowana na długość geograficzna:-122,1298, Szerokość geograficzna: 47,64005. Następnie współrzędne mogą być używane dla różnych rodzajów celów, takich jak, pozycjonowanie środkowego znacznika na mapie.

Azure Maps udostępnia kilka metod adresów geokodowania:

- [**Geokodowanie adresów w formie Geokodowanej**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): Określ pojedynczy ciąg adresu i natychmiast przetwórz żądanie. "1 Microsoft Way, Redmond, WA" to przykład pojedynczego ciągu adresu. Ten interfejs API jest zalecany, jeśli trzeba szybko odkodować poszczególne adresy.
- [**Geokodowanie adresów strukturalnych**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Określ części pojedynczego adresu, takie jak nazwa ulicy, miasto, kraj/region i kod pocztowy, i natychmiast przetwórz żądanie. Ten interfejs API jest zalecany, jeśli trzeba szybko odkodować poszczególne adresy, a dane są już analizowane w poszczególnych częściach adresu.
- [**Geokodowanie adresów partii**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 adresów i przetworzy je w danym okresie czasu. Wszystkie adresy będą kodowane geokodowowo na serwerze i po ukończeniu można pobrać pełny zestaw wyników. Jest to zalecane w przypadku dużych zestawów danych geokodowania.
- [**Wyszukiwanie rozmyte**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): ten interfejs API łączy geokodowanie adresów z punktem wyszukiwania zainteresowania. Ten interfejs API zajmuje ciąg o dowolnej postaci. Ten ciąg może być adresem, miejscem, punktem orientacyjnym lub kategorią punktu zainteresowania. Ten interfejs API przetwarza żądanie niemal w czasie rzeczywistym. Ten interfejs API jest zalecany w przypadku aplikacji, w których użytkownicy szukają adresów lub punktów orientacyjnych w tym samym polu tekstowym.
- [**Wyszukiwanie rozmytej partii**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 adresów, miejsc, punktów orientacyjnych lub punktu zainteresowania i przetworzyć je w określonym czasie. Wszystkie dane zostaną przetworzone równolegle na serwerze i po ukończeniu można pobrać pełny zestaw wyników.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi Google Maps | Porównywalny parametr interfejsu API Azure Maps  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` i `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` — Miasto/Miasto<br/>`municipalitySubdivision` — Kluby, sub/Super miasto<br/>`countrySubdivision` — Województwo<br/>`countrySecondarySubdivision` — Powiat<br/>`countryTertiarySubdivision` — Dystrykt<br/>`countryCode` -dwuliterowy kod kraju/regionu |
| `key`                       | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](azure-maps-authentication.md) . |
| `language`                  | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .  |
| `region`                    | `countrySet`                       |

Przykład użycia usługi wyszukiwania opisano [tutaj](how-to-search-for-address.md). Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi wyszukiwania](how-to-use-best-practices-for-search.md).

> [!TIP]
> W trybie Autouzupełnianie można używać interfejsów API geokodowania adresów i wyszukiwania rozmytego, dodając `&typeahead=true` do adresu URL żądania. Poinformuje o tym, że tekst wejściowy jest prawdopodobnie częściowy, a wyszukiwanie przejdzie do trybu predykcyjnego.

## <a name="reverse-geocode-a-coordinate"></a>Odwróć współrzędne geokodowe

Odwrócenie geokodowania to proces konwersji współrzędnych geograficznych na przybliżony adres. Koordynuje ze znakiem "Długość geograficzna:-122,1298, Latitude: 47,64005" Konwertuj na "1 Microsoft Way, Redmond, WA".

Azure Maps oferuje kilka metod odwrotnego kodu geokodowania:

- [**Odwróć kod**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)geograficzny: Określ jedną współrzędną geograficzną, aby uzyskać przybliżony adres odpowiadający tej koordynacji. Przetwarza żądanie niemal w czasie rzeczywistym.
- [**Odwrotny kod**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)geograficzny Cross-Street: Określ jedną współrzędną geograficzną, aby uzyskać informacje o skrzyżowaniu i natychmiast przetworzyć żądanie. Na przykład możesz otrzymać następujące zbiory dla wielu ulic 1 i główne St.
- [**Odwrotny geokod dotyczący adresu partii**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 współrzędnych i przetworzy je w określonym czasie. Wszystkie dane będą przetwarzane równolegle na serwerze. Po zakończeniu żądania można pobrać pełny zestaw wyników.

Ta tabela krzyżowo odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi Google Maps   | Porównywalny parametr interfejsu API Azure Maps   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](azure-maps-authentication.md) . |
| `language`                  | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .  |
| `latlng`                    | `query`  |
| `location_type`             | *Brak*     |
| `result_type`               | `entityType`    |

Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi wyszukiwania](how-to-use-best-practices-for-search.md).

Interfejs API odtwarzania geokodowanej Azure Maps ma kilka dodatkowych funkcji, które nie są dostępne w usłudze Google Maps. Te funkcje mogą być przydatne do integracji z aplikacją podczas migracji aplikacji:

- Pobieranie danych limitu szybkości
- Pobierz informacje o użyciu dróg: droga lokalna, Arterial, ograniczony dostęp, Pochylnia i tak dalej
- Pobierz bok ulicy, w którym znajduje się Współrzędna

## <a name="search-for-points-of-interest"></a>Wyszukiwanie punktów orientacyjnych

Punkt danych zainteresowania można wyszukiwać w usłudze Google Maps przy użyciu interfejsu API wyszukiwania miejsc. Ten interfejs API udostępnia trzy różne sposoby wyszukiwania interesujących punktów:

- **Znajdź miejsce z tekstu:** Wyszukuje punkt orientacyjny na podstawie jego nazwy, adresu lub numeru telefonu.
- **Wyszukiwanie w pobliżu**: wyszukuje punkty zainteresowań, które znajdują się w określonej odległości od lokalizacji.
- **Wyszukiwanie tekstu:** Wyszukuje miejsca przy użyciu swobodnego tekstu, który obejmuje punkt orientacyjny i informacje o lokalizacji. Na przykład "Pizza w Nowym Jorku" lub "Restauracje blisko głównego St".

Azure Maps udostępnia kilka interfejsów API wyszukiwania dla interesujących punktów:

- [**Wyszukiwanie punkt POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): Wyszukiwanie punktów zainteresowania według nazwy. Na przykład "Starbucks".
- [**Wyszukiwanie kategorii punkt POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): Wyszukiwanie punktów zainteresowania według kategorii. Na przykład "restauracji".
- [**Wyszukiwanie w pobliżu**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): wyszukuje punkty zainteresowań, które znajdują się w określonej odległości od lokalizacji.
- [**Wyszukiwanie rozmyte**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): ten interfejs API łączy geokodowanie adresów z punktem wyszukiwania zainteresowania. Ten interfejs API przyjmuje ciąg o dowolnej postaci, który może być adresem, miejscem, punktem orientacyjnym lub kategorią punktu zainteresowania. Przetwarza żądanie niemal w czasie rzeczywistym. Ten interfejs API jest zalecany w przypadku aplikacji, w których użytkownicy szukają adresów lub punktów orientacyjnych w tym samym polu tekstowym.
- [**Wyszukaj w obrębie geometrii**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): Wyszukaj punkty zainteresowań, które znajdują się w określonej geometrii. Na przykład Przeszukaj punkt zainteresowania w obrębie wielokąta.
- [**Wyszukiwanie wzdłuż trasy**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): Wyszukiwanie punktów zainteresowań, które znajdują się w określonej ścieżce trasy.
- [**Wyszukiwanie rozmytej partii**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 adresów, miejsc, punktów orientacyjnych lub punktu zainteresowania. Przetwarzanie żądania w danym okresie czasu. Wszystkie dane będą przetwarzane równolegle na serwerze. Po zakończeniu przetwarzania żądania można pobrać pełny zestaw wyników.

Obecnie Azure Maps nie ma porównywalnego interfejsu API do interfejsu API wyszukiwania tekstu w usłudze Google Maps.

> [!TIP]
> W trybie Autouzupełnianie można używać interfejsów API wyszukiwania punkt POI, wyszukiwania kategorii punkt POI i wyszukiwania rozmytego, dodając `&typeahead=true` do adresu URL żądania. Spowoduje to wyświetlenie serwera, który jest prawdopodobnie częściowym tekstem wejściowym. Interfejs API przeprowadzi wyszukiwanie w trybie predykcyjnym.

Zapoznaj się z [najlepszymi rozwiązaniami](how-to-use-best-practices-for-search.md) dotyczącymi dokumentacji wyszukiwania.

### <a name="find-place-from-text"></a>Znajdź miejsce z tekstu

Użyj Azure Maps [Wyszukiwanie punkt POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) i [Wyszukiwanie rozmyte](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) , aby wyszukać punkty zainteresowań według nazwy lub adresu.

Tabela krzyżowo odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów Azure Maps interfejsu API.

| Parametr interfejsu API usługi Google Maps | Porównywalny parametr interfejsu API Azure Maps |
|---------------------------|-------------------------------------|
| `fields`                  | *Brak*                               |
| `input`                   | `query`                             |
| `inputtype`               | *Brak*                               |
| `key`                     | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](azure-maps-authentication.md) . |
| `language`                | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .  |
| `locationbias`            | `lat``lon`i`radius`<br/>`topLeft` i `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Wyszukiwanie w pobliżu

Użyj interfejsu API [wyszukiwania w pobliżu](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) , aby pobrać bliskie punkty zainteresowania w Azure Maps.

W tabeli przedstawiono parametry interfejsu API usługi Google Maps z porównywalnymi parametrami interfejsu API Azure Maps.

| Parametr interfejsu API usługi Google Maps | Porównywalny parametr interfejsu API Azure Maps  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](azure-maps-authentication.md) . |
| `keyword`                   | `categorySet` i `brandSet`        |
| `language`                  | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .  |
| `location`                  | `lat` i `lon`                     |
| `maxprice`                  | *Brak*                               |
| `minprice`                  | *Brak*                               |
| `name`                      | `categorySet` i `brandSet`        |
| `opennow`                   | *Brak*                               |
| `pagetoken`                 | `ofs` i `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *Brak*                               |
| `type`                      | `categorySet –` Zapoznaj się z dokumentacją dotyczącą [obsługiwanych kategorii wyszukiwania](supported-search-categories.md) .   |

## <a name="calculate-routes-and-directions"></a>Obliczanie tras i wskazówek

Obliczaj trasy i wskazówki przy użyciu Azure Maps. Azure Maps ma wiele z tych samych funkcji co usługa routingu Google Maps, na przykład:

- Czasy przybycia i wyjazdu.
- Trasy ruchu w czasie rzeczywistym i predykcyjne.
- Różne tryby transportu. Takie jak kierowanie, idącie i przechodzenie.

> [!NOTE]
> Azure Maps wymaga współrzędnych wszystkich waypoints. Adresy muszą mieć najpierw kod geokodowania.

Usługa routingu Azure Maps udostępnia następujące interfejsy API do obliczania tras:

- [**Obliczanie trasy**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): Obliczanie trasy i natychmiastowe przetworzenie żądania. Ten interfejs API obsługuje zarówno żądania GET, jak i POST. Żądania POST są zalecane w przypadku określenia dużej liczby waypoints lub użycia wielu opcji trasy, aby zapewnić, że żądanie adresu URL nie stanie się zbyt długie i powoduje problemy. Kierunek po trasie w Azure Maps może przyjmować tysiące [punktów pomocniczych](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints) i użyć ich do ponownego utworzenia ścieżki trasy logicznej między nimi (przyciąganie do drogi). 
- [**Trasa wsadowa**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): Utwórz żądanie zawierające maksymalnie 1 000 żądanie trasy i przetworzy je w danym okresie czasu. Wszystkie dane zostaną przetworzone równolegle na serwerze i po ukończeniu można pobrać pełny zestaw wyników.
- [**Usługi mobilności**](https://docs.microsoft.com/rest/api/maps/mobility): obliczanie tras i wskazówek przy użyciu tranzytu publicznego.

Tabela krzyżowo odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi Google Maps    | Porównywalny parametr interfejsu API Azure Maps  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` — Współrzędne w formacie `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](azure-maps-authentication.md) . |
| `language`                     | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | Nie *dotyczy — ta* funkcja jest związana z geokodowaniem. Użyj parametru *countrySet* w przypadku korzystania z interfejsu API geokodowania Azure Maps.  |
| `traffic_model`               | Nie *dotyczy — można* określić tylko wtedy, gdy dane o ruchu mają być używane z parametrem *ruchu* . |
| `transit_mode`                | Zobacz [dokumentację usług mobilności](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Zobacz [dokumentację usług mobilności](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | Nie *dotyczy — Azure Maps* używa tylko systemu metryk.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Domyślnie interfejs API trasy Azure Maps zwraca tylko podsumowanie. Zwraca odległość i godziny oraz współrzędne ścieżki trasy. Użyj `instructionsType` parametru, aby pobrać instrukcje włączania i wyłączania. I użyj parametru, `routeRepresentation` Aby odfiltrować podsumowanie i ścieżkę trasy.

Interfejs API routingu Azure Maps ma dodatkowe funkcje, które nie są dostępne w usłudze Google Maps. Podczas migrowania aplikacji Rozważ użycie tych funkcji, które mogą być przydatne.

- Obsługa typu trasy: najkrótszy, najszybszy, TRILLING i większość wydajnych paliw.
- Obsługa dodatkowych trybów podróży: Magistrala, motocykl, taksówka, ciężarówka i Van.
- Obsługa 150 waypoints.
- Obliczanie wielu czasów podróży w jednym żądaniu; ruch historyczny, ruch na żywo, brak ruchu.
- Unikaj dodatkowych typów dróg: Carpool dróg, unpaved dróg, już używanych dróg.
- Określ obszary niestandardowe, które mają być stosowane.
- Ogranicz podniesienie uprawnień, które trasa może wyznaczać.
- Trasa oparta na specyfikacjach silnika. Obliczaj trasy dla spalania lub pojazdów elektrycznych w oparciu o specyfikacje silnika oraz pozostałe paliwo lub opłaty.
- Obsługa komercyjnych parametrów trasy pojazdu. Takie jak wymiary pojazdu, waga, liczba axels i typ ładunku.
- Określ maksymalną prędkość pojazdu.

Oprócz tego usługa Route w Azure Maps obsługuje [Obliczanie zakresów routingu](https://docs.microsoft.com/rest/api/maps/route/getrouterange). Obliczanie zakresów routingu jest również znane jako izochron. Pociąga za sobą generowanie wielokąta obejmującego obszar, do którego można podróżować w dowolnym kierunku od punktu początkowego. Wszystkie w określonym czasie lub w ilości paliwa lub opłaty.

Zapoznaj się z [najlepszymi rozwiązaniami](how-to-use-best-practices-for-routing.md) dotyczącymi routingu.

## <a name="retrieve-a-map-image"></a>Pobieranie obrazu mapy

Azure Maps udostępnia interfejs API do renderowania obrazów mapy statycznej z przełożonymi danymi. Interfejs API [renderowania obrazu mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) w Azure Maps jest porównywalny z interfejsem API mapy statycznej w usłudze mapy Google.

> [!NOTE]
> Azure Maps wymaga centrum, wszystkich znaczników i lokalizacji ścieżki do współrzędnych w formacie "Długość geograficzna". W tym przypadku usługi Google Maps używają formatu "szerokości geograficznej". Adresy muszą mieć najpierw kod geokodowania.

Tabela krzyżowo odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi Google Maps | Porównywalny parametr interfejsu API Azure Maps  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` — określony jako część ścieżki URL. Obecnie obsługiwane są tylko pliki PNG. |
| `key`                       | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](azure-maps-authentication.md) . |
| `language`                  | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .  |
| `maptype`                   | `layer` i `style` — Zobacz dokumentację [obsługiwanych stylów mapy](supported-map-styles.md) . |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | Nie *dotyczy — jest* to funkcja powiązana z geokodowaniem. Użyj `countrySet` parametru podczas korzystania z interfejsu API Azure Maps geokodowania.  |
| `scale`                     | *Brak*                              |
| `size`                      | `width` i `height` — może mieć rozmiar maksymalnie 8192x8192. |
| `style`                     | *Brak*                              |
| `visible`                   | *Brak*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> W systemie kafelków Azure Maps kafelki są dwa razy większe niż rozmiar kafelków mapy używanych w usłudze mapy Google. W takiej postaci wartość poziomu powiększenia w Azure Maps będzie wyglądać o jeden poziom powiększenia bliżej Azure Maps w porównaniu do usługi Google Maps. Aby skompensować te różnice, zmniejsz poziom powiększenia w migrowanych żądaniach.

Aby uzyskać więcej informacji, zobacz [Przewodnik dotyczący korzystania z interfejsu API renderowania obrazu mapy](how-to-render-custom-data.md).

Oprócz możliwości generowania obrazu mapy statycznej, usługa renderowania Azure Maps zapewnia możliwość bezpośredniego dostępu do kafelków mapy w formacie rastrowym (PNG) i wektorowym:

- [**Kafelek mapy**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): pobieranie kafelków rastrowych (PNG) i wektorów dla map bazowych (dróg, granic, tła).
- [**Kafelek mapy zdjęć**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): pobieranie obrazów anten i satelity.

> [!TIP]
> Wiele aplikacji usługi Google Maps, które przełączają się z interaktywnego środowiska mapy do obrazów mapy statycznej kilka lat temu. Zostało to wykonane jako metoda oszczędności kosztów. W Azure Maps jest zazwyczaj bardziej opłacalne używanie interaktywnej kontrolki mapy w zestawie SDK sieci Web. Na podstawie liczby obciążeń kafelków jest naliczana opłata za kontrolka mapy interaktywnej. Kafelki mapy w Azure Maps są duże. Często tylko kilka kafelków umożliwia odtworzenie tego samego widoku mapy jako mapy statycznej. Kafelki mapy są buforowane automatycznie przez przeglądarkę. W związku z tym interaktywna kontrolka mapy często generuje ułamek transakcji podczas odtwarzania widoku mapy statycznej. Kadrowanie i powiększanie spowoduje załadowanie większej liczby kafelków; Istnieją jednak opcje w kontrolce mapy, aby wyłączyć to zachowanie. Interaktywna kontrolka mapy udostępnia również wiele opcji wizualizacji niż usługi map statycznych.

### <a name="marker-url-parameter-format-comparison"></a>Porównanie formatu parametru adresu URL znacznika

**Wcześniej: Google Maps**

Dodaj znaczniki przy użyciu `markers` parametru w adresie URL. `markers`Parametr przyjmuje styl i listę lokalizacji, które mają być renderowane na mapie z tym stylem, jak pokazano poniżej:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Aby dodać dodatkowe style, użyj `markers` parametrów do adresu URL o innym stylu i zestawie lokalizacji.

Określ lokalizacje znaczników z formatem "szerokości geograficznej".

Dodawanie stylów znacznika w `optionName:value` formacie z wieloma stylami oddzielonymi znakami potoku ( \| ), takimi jak ten "optionName1: wartość1 \| optionName2: wartość2". Zwróć uwagę, że nazwy i wartości opcji są oddzielone dwukropkiem (:). Użyj następujących nazw opcji stylu do znaczników stylu w usłudze Google Maps:

- `color` — Kolor ikony znacznika domyślnego. Może to być 24-bitowy kolor szesnastkowy ( `0xrrggbb` ) lub jedna z następujących wartości:,,,,,,,, `black` `brown` `green` `purple` `yellow` `blue` `gray` `orange` `red` , `white` .
- `label` — Pojedynczy znak alfanumeryczny pisany wielkimi literami, który ma być wyświetlany na górze ikony.
- `size` — Rozmiar znacznika. Może być `tiny` , `mid` lub `small` .

Użyj następujących nazw opcji stylu dla ikon niestandardowych w usłudze Google Maps:

- `anchor` — Określa sposób wyrównania obrazu ikony do współrzędnej. Może to być wartość pikseli (x, y) lub jedna z następujących wartości: ,,,,,, `top` `bottom` `left` `right` `center` `topleft` `topright` , `bottomleft` lub `bottomright` .
- `icon` – Adres URL wskazujący obraz ikony.

Na przykład Dodajmy znacznik czerwony, średni rozmiar do mapy na długości geograficznej: 110, Szerokość geograficzna: 45:

```
&markers=color:red|size:mid|45,-110
```


![Znacznik Google Maps](media/migrate-google-maps-web-services/google-maps-marker.png)

**Po: Azure Maps**

Dodaj znaczniki do obrazu mapy statycznej przez określenie `pins` parametru w adresie URL. Podobnie jak w przypadku usługi Google Maps, określ styl i listę lokalizacji w parametrze. `pins`Parametr można określić wiele razy, aby obsługiwać znaczniki o różnych stylach.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Aby użyć dodatkowych stylów, Dodaj dodatkowe `pins` parametry do adresu URL przy użyciu innego stylu i zestawu lokalizacji.

W Azure Maps lokalizacja numeru PIN musi być w formacie "Długość geograficzna". Usługi Google Maps używają formatu "szerokości geograficznej". Spacja, a nie przecinek, oddziela długość i szerokość geograficzną w formacie Azure Maps.

`iconType`Określa typ kodu PIN, który ma zostać utworzony. Może mieć następujące wartości:

- `default` — Domyślna ikona pinezki.
- `none` — Nie jest wyświetlana ikona, tylko etykiety będą renderowane.
- `custom` — Określa niestandardową ikonę, która ma być używana. Adres URL wskazujący ikonę obrazu można dodać na końcu `pins` parametru po informacjach o lokalizacji numeru PIN.
- `{udid}` — Unikatowy identyfikator danych (UDID) dla ikony przechowywanej na platformie magazynu danych Azure Maps.

Dodaj style kodu PIN w `optionNameValue` formacie. Oddziel wiele stylów znakami potoku ( \| ). Przykład: `iconType|optionName1Value1|optionName2Value2`. Nazwy i wartości opcji nie są oddzielone. Użyj następujących nazw opcji stylu do znaczników stylu:

- `al` — Określa nieprzezroczystość (alfa) znacznika. Wybierz liczbę z zakresu od 0 do 1.
- `an` — Określa kotwicę numeru PIN. Określ wartości X i y pikseli w formacie "X y".
- `co` — Kolor kodu PIN. Określ 24-bitowy kolor szesnastkowy: `000000` do `FFFFFF` .
- `la` — Określa zakotwiczenie etykiety. Określ wartości X i y pikseli w formacie "X y".
- `lc` — Kolor etykiety. Określ 24-bitowy kolor szesnastkowy: `000000` do `FFFFFF` .
- `ls` — Rozmiar etykiety (w pikselach). Wybierz liczbę większą niż 0.
- `ro` — Wartość w stopniach, aby obrócić ikonę. Wybierz liczbę z przedziału od-360 do 360.
- `sc` — Wartość skali dla ikony pinezki. Wybierz liczbę większą niż 0.

Określ wartości etykiet dla każdej lokalizacji numeru PIN. Takie podejście jest bardziej wydajne niż stosowanie pojedynczej wartości etykiety do wszystkich znaczników na liście lokalizacji. Wartość etykiety może być ciągiem z wieloma znakami. Zawiń ciąg z pojedynczymi cudzysłowami, aby upewnić się, że nie zostanie on pomylony jako wartość stylu lub lokalizacji.

Dodajmy domyślną ikonę czerwoną ( `FF0000` ) z etykietą "obszar etykietka", która została umieszczona poniżej (15 50). Ikona ma długość:-122,349300, Szerokość geograficzna: 47,620180:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```


![Znacznik Azure Maps](media/migrate-google-maps-web-services/azure-maps-marker.png)

Dodaj trzy numery PIN z wartościami etykiet "1", "2" i "3":

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```



![Azure Maps wiele znaczników](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)

### <a name="path-url-parameter-format-comparison"></a>Porównanie formatu parametru adresu URL

**Wcześniej: Google Maps**

Dodaj linie i wielokąt do obrazu mapy statycznej przy użyciu `path` parametru w adresie URL. `path`Parametr przyjmuje styl i listę lokalizacji, które mają być renderowane na mapie, jak pokazano poniżej:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Użyj dodatkowych stylów, dodając dodatkowe `path` parametry do adresu URL przy użyciu innego stylu i zestawu lokalizacji.

Lokalizacje ścieżki są określone w `latitude1,longitude1|latitude2,longitude2|…` formacie. Ścieżki mogą być kodowane lub zawierać adresy dla punktów.

Dodaj style ścieżki w `optionName:value` formacie, Oddziel wiele stylów według znaków potoku ( \| ). I Oddzielaj nazwy opcji i wartości średnikami (:). W następujący sposób: `optionName1:value1|optionName2:value2` . Następujące nazwy opcji stylu mogą służyć do nadawania stylu ścieżkom w usłudze Google Maps:

- `color` — Kolor ścieżki lub konturu wielokąta. Może to być 24-bitowy kolor szesnastkowy ( `0xrrggbb` ), 32-bitowy kolor szesnastkowy ( `0xrrggbbbaa` ) lub jedna z następujących wartości: Black, brązowy, zielony, purpurowy, żółty, niebieski, szary, pomarańczowy, czerwony, biały.
- `fillColor` — Kolor wypełnienia obszaru ścieżki przy użyciu (Wielokąt). Może to być 24-bitowy kolor szesnastkowy ( `0xrrggbb` ), 32-bitowy kolor szesnastkowy ( `0xrrggbbbaa` ) lub jedna z następujących wartości: Black, brązowy, zielony, purpurowy, żółty, niebieski, szary, pomarańczowy, czerwony, biały.
- `geodesic` — Wskazuje, czy ścieżka powinna być linią, która jest zgodna z krzywizną ziemi.
- `weight` — Grubość linii ścieżki w pikselach.

Dodaj nieprzezroczystą linię i grubość pikseli do mapy między współrzędnymi, w parametrze adresu URL. W poniższym przykładzie linia ma nieprzezroczystość 50% i grubość czterech pikseli. Współrzędne to długość geograficzna:-110, Szerokość geograficzna: 45 i Długość geograficzna:-100, Szerokość geograficzna: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

![Google Maps — łamana](media/migrate-google-maps-web-services/google-maps-polyline.png)

**Po: Azure Maps**

Dodaj linie i wielokąty do statycznego obrazu mapy, określając `path` parametr w adresie URL. Podobnie jak w przypadku usługi Google Maps, określ styl i listę lokalizacji w tym parametrze. Określ `path` parametr wiele razy, aby renderować wiele okręgów, linii i wielokątów o różnych stylach.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Gdy powróci do lokalizacji ścieżki, Azure Maps wymaga, aby współrzędne były w formacie "szerokości geograficznej". Usługi Google Maps używają formatu "szerokości geograficznej". Spacja, a nie przecinek, oddziela długość i szerokość geograficzną w formacie Azure Maps. Azure Maps nie obsługuje zakodowanych ścieżek ani adresów dla punktów. Przekaż większe zbiory danych jako plik GEOJSON do interfejsu API magazynu danych Azure Maps, jak opisano [tutaj](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

Dodaj style ścieżki w `optionNameValue` formacie. Oddziel wiele stylów według znaków potoku ( \| ), takich jak ten `optionName1Value1|optionName2Value2` . Nazwy i wartości opcji nie są oddzielone. Użyj następujących nazw opcji stylu do ścieżek stylów w Azure Maps:

- `fa` — Nieprzezroczystość koloru wypełnienia (alfa) użyta podczas renderowania wielokątów. Wybierz liczbę z zakresu od 0 do 1.
- `fc` — Kolor wypełnienia używany do renderowania obszaru wielokąta.
- `la` — Nieprzezroczystość koloru linii (alfa) używana podczas renderowania linii i konspektu wielokątów. Wybierz liczbę z zakresu od 0 do 1.
- `lc` — Kolor linii używany do renderowania linii i konspektu wielokątów.
- `lw` — Szerokość linii w pikselach.
- `ra` – Określa promień okręgów w metrach.

Dodaj nieprzezroczystość czerwoną linii i grubość pikseli między współrzędnymi, w parametrze adresu URL. W poniższym przykładzie linia ma wartość nieprzezroczystość 50% i grubość czterech pikseli. Współrzędne mają następujące wartości: Długość geograficzna:-110, Szerokość geograficzna: 45 i Długość geograficzna:-100, Szerokość geograficzna: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

![Azure Maps łamana](media/migrate-google-maps-web-services/azure-maps-polyline.png)

## <a name="calculate-a-distance-matrix"></a>Obliczanie macierzy odległości

Azure Maps udostępnia interfejs API macierzy odległości. Ten interfejs API umożliwia obliczanie czasów podróży i odległości między zestawem lokalizacji przy użyciu macierzy odległości. Jest on porównywalny z interfejsem API macierzy odległości w usłudze mapy Google.

- [**Macierz trasy**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): asynchronicznie oblicza czasy podróży i odległości dla zestawu źródeł i miejsc docelowych. Obsługuje do 700 komórek na żądanie. Jest to liczba źródeł pomnożona przez liczbę miejsc docelowych. Mając to na uwadze, przykłady możliwych wymiarów macierzy to: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Żądanie interfejsu API macierzy odległości można wykonać tylko przy użyciu żądania POST z informacjami o lokalizacji źródłowej i docelowej w treści żądania. Ponadto Azure Maps wymaga współrzędnych wszystkich źródeł i miejsc docelowych. Adresy muszą mieć najpierw kod geokodowania.

Ta tabela krzyżowo odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów Azure Maps interfejsu API.

| Parametr interfejsu API usługi Google Maps      | Porównywalny parametr interfejsu API Azure Maps  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` — Określ w treści żądania POST jako GEOJSON. |
| `key`                          | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](azure-maps-authentication.md) . |
| `language`                     | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` — Określ w treści żądania POST jako GEOJSON.  |
| `region`                       | Nie *dotyczy — ta* funkcja jest związana z geokodowaniem. Użyj `countrySet` parametru podczas korzystania z interfejsu API Azure Maps geokodowania. |
| `traffic_model`                | Nie *dotyczy — można* określić tylko wtedy, gdy dane o ruchu mają być używane z `traffic` parametrem. |
| `transit_mode`                 | Macierze *oparte na* trasach nie są obecnie obsługiwane.  |
| `transit_routing_preference`   | Macierze *oparte na* trasach nie są obecnie obsługiwane.  |
| `units`                        | Nie *dotyczy — Azure Maps* używa tylko systemu metryk. |

> [!TIP]
> Wszystkie zaawansowane opcje routingu dostępne w interfejsie API routingu Azure Maps są obsługiwane w interfejsie API macierzy na odległość Azure Maps. Zaawansowane opcje routingu obejmują: Routing ciężarówki, specyfikacje aparatu i tak dalej.

Zapoznaj się z [najlepszymi rozwiązaniami](how-to-use-best-practices-for-routing.md) dotyczącymi routingu.

## <a name="get-a-time-zone"></a>Pobierz strefę czasową

Azure Maps udostępnia interfejs API do pobierania strefy czasowej współrzędnych. Interfejs API strefy czasowej Azure Maps jest porównywalny z interfejsem API strefy czasowej w usłudze Google Maps:

- [**Strefa czasowa według współrzędnych**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Określ współrzędną i Odbierz szczegóły strefy czasowej współrzędnych.

Ta tabela krzyżowo odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi Google Maps | Porównywalny parametr interfejsu API Azure Maps   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](azure-maps-authentication.md) .       |
| `language`                  | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Oprócz tego interfejsu API Azure Maps udostępnia wiele interfejsów API strefy czasowej. Te interfejsy API konwertują czas na podstawie nazw lub identyfikatorów strefy czasowej:

- [**Strefa czasowa według identyfikatora**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): zwraca bieżące, historyczne i przyszłe informacje o strefie czasowej dla określonego identyfikatora strefy czasowej organizacji IANA.
- [**Wyliczenie strefy czasowej Iana**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): zwraca pełną listę identyfikatorów stref czasowych organizacji IANA. Aktualizacje usługi IANA są uwzględniane w systemie w ciągu jednego dnia.
- [**Okna wyliczania strefy czasowej**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): zwraca pełną listę identyfikatorów strefy czasowej systemu Windows.
- [**Wersja programu Iana strefy czasowej**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): zwraca bieżący numer wersji programu Iana używany przez Azure Maps.
- [**System Windows strefy czasowej dla organizacji Iana**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): zwraca odpowiedni identyfikator Iana, uwzględniając prawidłowy identyfikator strefy czasowej systemu Windows. Dla pojedynczego identyfikatora systemu Windows można zwrócić wiele identyfikatorów IANA.

## <a name="client-libraries"></a>Biblioteki klienta

Azure Maps udostępnia biblioteki klienckie dla następujących języków programowania:

- JavaScript, TypeScript, Node.js — [Dokumentacja](how-to-use-services-module.md) \| [pakietu npm](https://www.npmjs.com/package/azure-maps-rest)

Te biblioteki klienckie typu "open source" są przeznaczone dla innych języków programowania:

- .NET Standard 2,0 — [GitHub project](https://github.com/perfahlen/AzureMapsRestServices) \| [pakiet NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/) projektu GitHub

## <a name="additional-resources"></a>Zasoby dodatkowe

Poniżej znajdują się dodatkowe dokumenty i zasoby dotyczące Azure Maps usług REST.

- [Najlepsze rozwiązania dotyczące wyszukiwania](how-to-use-best-practices-for-search.md)
- [Wyszukiwanie adresu](how-to-search-for-address.md)
- [Najlepsze rozwiązania dotyczące routingu](how-to-use-best-practices-for-routing.md)
- [Dokumentacja interfejsu API usługi REST Azure Maps](https://docs.microsoft.com/rest/api/maps/)
- [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
- [Jak używać modułu usług (Web SDK)](how-to-use-best-practices-for-routing.md)