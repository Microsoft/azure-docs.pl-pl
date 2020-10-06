---
title: 'Samouczek: Migrowanie usług internetowych z map Bing | Mapy Microsoft Azure'
description: Jak przeprowadzić migrację usług sieci Web z map Bing do Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 14e0998b75e0e5bd3ae996f5f5010ecc50180f14
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91741673"
---
# <a name="migrate-web-service-from-bing-maps"></a>Migrowanie usługi sieci Web z map Bing

Zarówno usługa Azure, jak i mapy Bing zapewniają dostęp do przestrzennych interfejsów API za pomocą usług sieci Web REST. Interfejsy interfejsu API dla tych platform wykonują podobne funkcje, ale używają różnych konwencji nazewnictwa i obiektów odpowiedzi.

W poniższej tabeli przedstawiono interfejsy API usługi Azure Maps, które zapewniają podobną funkcjonalność do listy interfejsów API usługi mapy Bing.

| Interfejs API usługi mapy Bing                 | Interfejs API usługi Azure Maps      |
|---------------------------------------|-----------------------------|
| Automatyczne sugerowanie                           | [Wyszukiwanie](https://docs.microsoft.com/rest/api/maps/search)     |
| Kierunki (w tym ciężarówka)          | [Kierunki tras](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)                          |
| Macierz odległości                       | [Macierz trasy](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)                          |
| Obraz — Mapa statyczna                  | [Renderowanie](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                                   |
| Izochron                            | [Zakres tras](https://docs.microsoft.com/rest/api/maps/route/getrouterange)                                    |
| Lokalne szczegółowe informacje                        | [Wyszukaj](https://docs.microsoft.com/rest/api/maps/search)  +  [Zakres tras](https://docs.microsoft.com/rest/api/maps/route/getrouterange)    |
| Wyszukiwanie lokalne                          | [Wyszukiwanie](https://docs.microsoft.com/rest/api/maps/search)     |
| Rozpoznawanie lokalizacji (punktów POI)           | [Wyszukiwanie](https://docs.microsoft.com/rest/api/maps/search)     |
| Lokalizacje (do przodu/do tyłu geokodowania) | [Wyszukiwanie](https://docs.microsoft.com/rest/api/maps/search)                                               |
| Przyciągaj do drogi                          | [Wskazówki dotyczące trasy po trasie](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)                         |
| Data Services przestrzenne           | [Wyszukaj](https://docs.microsoft.com/rest/api/maps/search)  +  [Route](https://docs.microsoft.com/rest/api/maps/route) + inne usługi platformy Azure |
| Strefa czasowa                             | [Strefa czasowa](https://docs.microsoft.com/rest/api/maps/timezone)  |
| Zdarzenia dotyczące ruchu                     | [Szczegóły zdarzenia dotyczącego ruchu](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidentdetail)                     |

Następujące interfejsy API usługi nie są obecnie dostępne w Azure Maps:

-   Podniesienie uprawnień — planowane
-   Zoptymalizowane trasy trasy podróży — planowane. Interfejs API tras Azure Maps obsługuje optymalizację Salesmen dla pojedynczego pojazdu.
-   Metadane obrazów — służy głównie do uzyskiwania adresów URL kafelków w usłudze mapy Bing. Azure Maps ma autonomiczną usługę do bezpośredniego uzyskiwania dostępu do kafelków mapy.

Azure Maps ma kilka dodatkowych usług sieci Web REST, które mogą być interesujące;

-   [Azure Maps Creator](https://docs.microsoft.com/azure/azure-maps/creator-indoor-maps) — Utwórz niestandardową, prywatną dwuosiową liczbę budynków i miejsc.
-   [Operacje przestrzenne](https://docs.microsoft.com/rest/api/maps/spatial) — odciążanie złożonych obliczeń przestrzennych i operacji, takich jak geoogrodzenia, do usługi.
-   [Kafelki mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile) — dostęp do obrazów dróg i obrazów z Azure Maps jako kafelki rastrowe i wektorowe.
-   [Routing usługi Batch](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview) — umożliwia tworzenie do 1 000 żądań tras w jednej partii w danym okresie czasu. Trasy są obliczane równolegle na serwerze w celu przyspieszenia przetwarzania.
-   [Ruch sieciowy](https://docs.microsoft.com/rest/api/maps/traffic) Flow — dostęp do danych przepływu ruchu w czasie rzeczywistym jako kafelki rastrowe i wektorowe.
-   [Interfejs API geolokalizacji](https://docs.microsoft.com/rest/api/maps/geolocation/getiptolocationpreview) — Pobierz lokalizację adresu IP.
-   [Usługi pogodowe](https://docs.microsoft.com/rest/api/maps/weather) — uzyskiwanie dostępu do danych pogodowych w czasie rzeczywistym i prognozowanie.

Należy również zapoznać się z następującymi przewodnikami dotyczącymi najlepszych rozwiązań:

-   [Najlepsze rozwiązania dotyczące wyszukiwania](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search)
-   [Najlepsze rozwiązania dotyczące routingu](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-routing)

## <a name="geocoding-addresses"></a>Adresy geokodowania

Geokodowanie jest procesem konwertowania adresu ( `"1 Microsoft way, Redmond, WA"` na przykład) na współrzędną (na przykład Długość geograficzna:-122,1298, Szerokość geograficzna: 47,64005). Współrzędne są następnie często używane do pozycjonowania pinezki na mapie lub Wyśrodkuj mapę.

Azure Maps zapewnia kilka metod adresów geokodowania;

-   [Geokodowanie adresów w formie Geokodowanej](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): Określ pojedynczy ciąg adresu (na przykład `"1 Microsoft way, Redmond, WA"` ) i natychmiast przetwórz żądanie. Ta usługa jest zalecana, jeśli trzeba szybko odkodować poszczególne adresy.
-   [Geokodowanie adresów strukturalnych](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Określ części pojedynczego adresu, takie jak nazwa ulicy, miasto, kraj i kod pocztowy, i natychmiast przetwórz żądanie. Ta usługa jest zalecana, jeśli trzeba szybko odkodować poszczególne adresy, a dane są już analizowane w poszczególnych częściach adresu.
-   [Geokodowanie adresów partii](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 adresów i przetworzy je w danym okresie czasu. Wszystkie adresy będą kodowane geokodowowo na serwerze i po ukończeniu można pobrać pełny zestaw wyników. Ta usługa jest zalecana do geokodowania dużych zestawów danych.
-   [Wyszukiwanie rozmyte](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): ten interfejs API łączy geokodowanie adresów z punktem wyszukiwania zainteresowania. Ten interfejs API przyjmuje ciąg o dowolnej postaci, który może być adresem, miejscem, punktem orientacyjnym lub kategorią punktu zainteresowania, i natychmiastowo przetworzyć żądanie. Ten interfejs API jest zalecany w przypadku aplikacji, w których użytkownicy mogą wyszukiwać adresy lub punkty istotne z tego samego pola tekstowego.
-   [Wyszukiwanie rozmytej partii](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 adresów, miejsc, punktów orientacyjnych lub punktu zainteresowania i przetworzyć je w określonym czasie. Wszystkie dane zostaną przetworzone równolegle na serwerze i po ukończeniu można pobrać pełny zestaw wyników.

W poniższych tabelach odwołują się do parametrów interfejsu API usługi mapy Bing z porównywalnymi parametrami interfejsu API w Azure Maps na potrzeby geokodowania adresów geokodowanych i bezpłatnych.

**Lokalizacja według adresu (adres strukturalny)**

| Parametr interfejsu API usługi mapy Bing              | Porównywalny parametr interfejsu API Azure Maps                 |
|--------------------------------------|-----------------------------------------------------|
| `addressLine`                      | `streetNumber``streetName`lub`crossStreet` |
| `adminDistrict`                    | `countrySubdivision`                              |
| `countryRegion`                    | `country` i `countryCode`                     |
| `locality`                         | `municipality` lub `municipalitySubdivision`     |
| `postalCode`                       | `postalCode`                                      |
| `maxResults` (`maxRes`)          | `limit`                                           |
| `includeNeighborhood` (`inclnb`) | Nie dotyczy — zawsze zwracany przez Azure Maps, jeśli jest dostępny.   |
| `include` (`incl`)               | N/A — kod ISO2 zawsze zwracany przez Azure Maps. |
| `key`                              | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) . |
| `culture` (`c`)                  | `language` — Zobacz dokumentację [obsługiwanych języków](https://docs.microsoft.com/azure/azure-maps/supported-languages) . |
| `userRegion` (`ur`)              | `view` — Zobacz dokumentację [obsługiwanych widoków](https://aka.ms/AzureMapsLocalizationViews) . |

Azure Maps obsługuje także;

-   `countrySecondarySubdivision` — Powiat, okręgi
-   `countryTertiarySubdivision` -Nazwane obszary; boroughs, Cantons, gminy
-   `ofs` -Page poprzez wyniki w połączeniu z `maxResults` parametrem.

**Lokalizacja według zapytania (ciąg adresu w dowolnej postaci)**

| Parametr interfejsu API usługi mapy Bing              | Porównywalny parametr interfejsu API Azure Maps      |
|--------------------------------------|------------------------------------------|
| `query`                            | `query`                                |
| `maxResults` (`maxRes`)          | `limit`                                |
| `includeNeighborhood` (`inclnb`) | Nie dotyczy — zawsze zwracany przez Azure Maps, jeśli jest dostępny.  |
| `include` (`incl`)               | N/A — kod ISO2 zawsze zwracany przez Azure Maps.  |
| `key`                              | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) . |
| `culture` (`c`)                  | `language` — Zobacz dokumentację [obsługiwanych języków](https://docs.microsoft.com/azure/azure-maps/supported-languages) .  |
| `userRegion` (`ur`)              | `view` — Zobacz dokumentację [obsługiwanych widoków](https://aka.ms/AzureMapsLocalizationViews) . |

Azure Maps obsługuje także;

-   `typeahead` -Gatunek, jeśli zapytanie będzie interpretowane jako częściowe dane wejściowe, a wyszukiwanie spowoduje Przeanalizowanie trybu predykcyjnego (automatyczne sugerowanie/Autouzupełnianie).
-   `countrySet` — Rozdzielana przecinkami lista kodów krajów ISO2, w których ma zostać ograniczone wyszukiwanie.
-   `lat`/`lon`, `topLeft` / `btmRight` , `radius` — Określ lokalizację i obszar użytkownika, aby wyniki były bardziej istotne.
-   `ofs` -Page poprzez wyniki w połączeniu z `maxResults` parametrem.

Przykład użycia usługi wyszukiwania opisano [tutaj](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address). Pamiętaj, aby zapoznać się z [najlepszymi rozwiązaniami](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) dotyczącymi dokumentacji wyszukiwania.

## <a name="reverse-geocode-a-coordinate-find-a-location-by-point"></a>Odwróć kod do współrzędnych (Znajdź lokalizację według punktu)

Odwrócenie geokodowania to proces konwersji współrzędnych geograficznych (takich jak długość geograficzna:-122,1298, Szerokość geograficzna: 47,64005) na adres przybliżony (np `"1 Microsoft way, Redmond, WA"` .).

Azure Maps zapewnia kilka metod odwrotnego kodu geokodowania;

-   [Odwróć kod](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)geograficzny: Określ jedną współrzędną geograficzną, aby uzyskać przybliżony adres, i natychmiast przetworzyć żądanie.
-   [Odwrotny kod](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)geograficzny Cross-Street: Określ jedną współrzędną geograficzną, aby uzyskać informacje o skrzyżowaniu krzyżowym (na przykład 1st & Main) i natychmiast przetworzyć żądanie.
-   [Odwrotny geokod dotyczący adresu partii](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 współrzędnych i przetworzy je w określonym czasie. Wszystkie dane zostaną przetworzone równolegle na serwerze i po ukończeniu można pobrać pełny zestaw wyników.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi mapy Bing przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi mapy Bing              | Porównywalny parametr interfejsu API Azure Maps       |
|--------------------------------------|-------------------------------------------|
| `point`                              | `query`                                   |
| `includeEntityTypes`                 | `entityType` — Zobacz poniższą tabelę porównania typów jednostek.    |
| `includeNeighborhood` (`inclnb`)     | Nie dotyczy — zawsze zwracany przez Azure Maps, jeśli jest dostępny.         |
| `include` (`incl`)                   | N/A — kod ISO2 zawsze zwracany przez Azure Maps.    |
| `key`                                | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) . |
| `culture` (`c`)                      | `language` — Zobacz dokumentację [obsługiwanych języków](https://docs.microsoft.com/azure/azure-maps/supported-languages) .   |
| `userRegion` (`ur`)                  | `view` — Zobacz dokumentację [obsługiwanych widoków](https://aka.ms/AzureMapsLocalizationViews) . |

Pamiętaj, aby zapoznać się z [najlepszymi rozwiązaniami](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) dotyczącymi dokumentacji wyszukiwania.

Interfejs API odtworzenia geokodowanej Azure Maps zawiera kilka dodatkowych funkcji, które mogą być przydatne do integracji podczas migracji aplikacji:

-   Pobierz dane limitu szybkości.
-   Pobierz informacje o użyciu dróg; droga lokalna, Arterial, ograniczony dostęp, Pochylnia itp.
-   Strona ulica, na której znajduje się Współrzędna.

**Tabela porównania typów jednostek**

W poniższej tabeli odwołuje się do wartości typu jednostki mapy Bing do odpowiednich nazw właściwości w Azure Maps.

| Typ jednostki mapy Bing | Porównywalny typ jednostki Azure Maps               | Opis                                |
|-----------------------|-------------------------------------------------|--------------------------------------------|
| `Address`             |                                                 | *Adres*                                  |
| `Neighborhood`        | `Neighbourhood`                                 | *Otoczeni*                             |
| `PopulatedPlace`      | `Municipality` lub `MunicipalitySubdivision`     | *Miasto*, *miejscowość lub* *miasto lub miejscowość*     |
| `Postcode1`           | `PostalCodeArea`                                | *Kod pocztowy* lub *Kod pocztowy*                |
| `AdminDivision1`      | `CountrySubdivision`                            | *Województwo* *Province*                      |
| `AdminDivision2`      | `CountrySecondarySubdivison`                    | *Powiat* lub *okręgi*                    |
| `CountryRegion`       | `Country`                                       | *Nazwa kraju*                             |
|                       | `CountryTertiarySubdivision`                    | *Boroughs*, *Cantons*, *gminy*          |

## <a name="get-location-suggestions-autosuggest"></a>Uzyskaj sugestie dotyczące lokalizacji (automatyczne sugerowanie)

Kilka zastosowań interfejsu API wyszukiwania Azure Maps obsługują tryb predykcyjny, który może być używany w scenariuszach automatycznego sugerowania. Interfejs API [wyszukiwania rozmytego](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) Azure Maps jest najbardziej podobny do interfejsu API automatycznego sugerowania usługi mapy Bing. Poniższy interfejs API obsługuje również tryb predykcyjny, Dodaj `&typeahead=true` do zapytania;

-   [Geokodowanie adresów w formie Geokodowanej](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): Określ pojedynczy ciąg adresu (na przykład `"1 Microsoft way, Redmond, WA"` ) i natychmiast przetwórz żądanie. Ta usługa jest zalecana, jeśli trzeba szybko odkodować poszczególne adresy.
-   [Wyszukiwanie rozmyte](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): ten interfejs API łączy geokodowanie adresów z punktem wyszukiwania zainteresowania. Ten interfejs API przyjmuje ciąg o dowolnej postaci, który może być adresem, miejscem, punktem orientacyjnym lub kategorią punktu zainteresowania, i natychmiastowo przetworzyć żądanie. Ten interfejs API jest zalecany w przypadku aplikacji, w których użytkownicy mogą wyszukiwać adresy lub punkty istotne z tego samego pola tekstowego.
-   [Wyszukiwanie punkt POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): Wyszukiwanie punktów zainteresowania według nazwy. Na przykład: `"starbucks"`.
-   [Wyszukiwanie kategorii punkt POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): Wyszukiwanie punktów zainteresowania według kategorii. Na przykład: "restauracji".

## <a name="calculate-routes-and-directions"></a>Obliczanie tras i wskazówek

Azure Maps może służyć do obliczania tras i wskazówek. Azure Maps ma wiele takich samych funkcji, jak usługa routingu mapy Bing, taka jak;

-   czasy przybycia i wyruszenia
-   trasy ruchu w czasie rzeczywistym i predykcyjne
-   różne tryby transportu; Jazda, nauka, ciężarówka
-   Optymalizacja kolejności punkt nawigacyjny (Salesmen podróży)

> [!NOTE]
> Azure Maps wymaga współrzędnych wszystkich waypoints. Adresy muszą mieć najpierw kod geokodowania.

Usługa routingu Azure Maps udostępnia następujące interfejsy API do obliczania tras;

-   [Obliczanie trasy](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): Obliczanie trasy i natychmiastowe przetworzenie żądania. Ten interfejs API obsługuje zarówno żądania GET, jak i POST. Żądania POST są zalecane w przypadku określenia dużej liczby waypoints lub użycia wielu opcji trasy, aby zapewnić, że żądanie adresu URL nie stanie się zbyt długie i powoduje problemy.
-   [Trasa wsadowa](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): Utwórz żądanie zawierające maksymalnie 1 000 żądanie trasy i przetworzy je w danym okresie czasu. Wszystkie dane zostaną przetworzone równolegle na serwerze i po ukończeniu można pobrać pełny zestaw wyników.
-   [Usługi mobilności](https://docs.microsoft.com/rest/api/maps/mobility): obliczanie tras i wskazówek przy użyciu tranzytu publicznego.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi mapy Bing przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi mapy Bing                                    | Porównywalny parametr interfejsu API Azure Maps               |
|------------------------------------------------------------|---------------------------------------------------|
| `avoid`                                                    | `avoid`                                           |
| `dateTime` (`dt`)                                          | `departAt` lub `arriveAt`                          |
| `distanceBeforeFirstTurn` (`dbft`)                         | Brak                                               |
| `distanceUnit` (`du`)                                      | Nie dotyczy — Azure Maps używa tylko systemu metryk.     |
| `heading` (`hd`)                                           | `vehicleHeading`                                  |
| `maxSolutions` (`maxSolns`)                                | `maxAlternatives`, `alternativeType` , `minDeviationDistance` i `minDeviationTime`  |
| `optimize` (`optwz`)                                       | `routeType` i `traffic`                         |
| `optimizeWaypoints` (`optWp`)                              | `computeBestOrder`                                |
| `routeAttributes` (`ra`)                                   | `instructionsType`                                |
| `routePathOutput` (`rpo`)                                  | `routeRepresentation`                             |
| `timeType` (`tt`)                                          | `departAt` lub `arriveAt`                          |
| `tolerances` (`tl`)                                        | Brak                                               |
| `travelMode`                                               | `travelMode`                                      |
| `waypoint.n` ( `wp.n` ) lub `viaWaypoint.n` (`vwp.n`)         | `query` — Współrzędne w formacie `lat0,lon0:lat1,lon1….`   |
| `key`                                                      | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) . |
| `culture` (`c`)                                            | `language` — Zobacz dokumentację [obsługiwanych języków](https://docs.microsoft.com/azure/azure-maps/supported-languages) . |
| `userRegion` (`ur`)                                        | `view` — Zobacz dokumentację [obsługiwanych widoków](https://aka.ms/AzureMapsLocalizationViews) . |

Interfejs API routingu Azure Maps obsługuje również Routing ciężarówki w ramach tego samego interfejsu API. Poniższa tabela zawiera krzyżowe informacje o dodatkowych parametrach routingu ciężarówki mapy Bing przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi mapy Bing                  | Porównywalny parametr interfejsu API Azure Maps        |
|------------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                 | Nie dotyczy — wymiary w licznikach są obsługiwane tylko. |
| `weightUnit` (`wu`)                      | Nie dotyczy wag w kilogramach i jest obsługiwana. |
| `vehicleHeight` (`height`)               | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                 | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                   | `vehicleLength`                            |
| `vehicleWeight` (`weight`)               | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                 | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                 | **NIE DOTYCZY**                                    |
| `vehicleSemi` (`semi`)                   | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)             | **NIE DOTYCZY**                                    |
| `vehicleMinTurnRadius` (`vmtr`)          | **NIE DOTYCZY**                                    |
| `vehicleAvoidCrossWind` (`vacw`)         | **NIE DOTYCZY**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)     | **NIE DOTYCZY**                                    |
| `vehicleHazardousMaterials` (`vhm`)      | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)        | `vehicleLoadType`                          |

> [!TIP]
> Domyślnie interfejs API trasy Azure Maps zwraca tylko podsumowanie (odległość i czas) i współrzędne ścieżki trasy. Użyj `instructionsType` parametru, aby pobrać instrukcje włączania i wyłączania. `routeRepresentation`Parametr może służyć do odfiltrowania ścieżki podsumowania i trasy.

Należy również zapoznać się z [najlepszymi rozwiązaniami](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-routing) dotyczącymi dokumentacji routingu.

Interfejs API routingu Azure Maps ma wiele dodatkowych funkcji dostępnych w usłudze mapy Bing, które mogą być przydatne do integracji podczas migracji aplikacji:

-   Obsługa typu trasy: najkrótszy, najszybszy, TRILLING i większość wydajnych paliw.
-   Obsługa dodatkowych trybów podróży: rower, magistrala, motocykl, taksówka, ciężarówka i Van.
-   Obsługa 150 waypoints.
-   Obliczanie wielu czasów podróży w jednym żądaniu; ruch historyczny, ruch na żywo, brak ruchu.
-   Unikaj dodatkowych typów dróg: Carpool dróg, unpaved dróg, już używanych dróg.
-   Routing oparty na specyfikacji aparatu. Oblicz trasy dla spalania lub pojazdów elektrycznych w oparciu o pozostałe wymagania dotyczące paliwa/opłaty i aparatu.
-   Określ maksymalną prędkość pojazdu.

## <a name="snap-coordinates-to-road"></a>Przyciągnij współrzędne do drogi

Istnieje kilka sposobów przyciągania współrzędne do dróg w Azure Maps.

-   Użyj interfejsu API wskazówek dotyczących trasy, aby przyciągnąć współrzędne do trasy logicznej wzdłuż sieci drogowej.
-   Użyj zestawu Web SDK Azure Maps, aby przyciągnąć poszczególne współrzędne do najbliższej drogi w kafelkach wektora.
-   Użyj Azure Maps kafelków wektorowych bezpośrednio do przyciągania poszczególnych współrzędnych.

**Używanie interfejsu API kierunku trasy do przyciągania współrzędne**

Azure Maps można przyciągnąć współrzędne do dróg przy użyciu interfejsu API [wskazówek dotyczących trasy](https://docs.microsoft.com/rest/api/maps/route/postroutedirections) . Ta usługa może służyć do odtworzenia trasy logicznej między zestawem współrzędnych i jest porównywalna z przystawką usługi mapy Bing do interfejsu API Road.

Istnieją dwa różne sposoby, aby użyć interfejsu API wskazówek trasy do przyciągania współrzędne do dróg.

-   Jeśli istnieje 150 współrzędnych lub mniej, można je przekazać jako waypoints w interfejsie API wskazówki dotyczące uzyskiwania trasy. Korzystając z tego podejścia, można pobrać dwa różne typy przypiętych danych; instrukcje trasy będą zawierać pojedyncze przypięte waypoints, podczas gdy ścieżka trasy będzie miała interpolowany zestaw współrzędnych, które wypełniają pełną ścieżkę między współrzędnymi.
-   Jeśli istnieje więcej niż 150 współrzędnych, można użyć interfejsu API wskazówek dotyczących trasy. Współrzędne początku i końca muszą zostać przesłane do parametru zapytania, ale wszystkie współrzędne można przekazywać do `supportingPoints` parametru w treści żądania post i sformatować kolekcję geometryczną GEOJSON punktów. Jedyne przypięte dane, które są dostępne przy użyciu tej metody, będą ścieżką trasy, która jest zestawem interpolowanych współrzędnych, które wypełniają pełną ścieżkę między współrzędnymi. [Oto przykład](https://azuremapscodesamples.azurewebsites.net/?sample=Snap%20points%20to%20logical%20route%20path) tego podejścia przy użyciu modułu usług w Azure Maps Web SDK.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi mapy Bing przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi mapy Bing    | Porównywalny parametr interfejsu API Azure Maps                                 |
|----------------------------|---------------------------------------------------------------------|
| `points`                   | `supportingPoints` — Przekaż te punkty do treści żądania post  |
| `interpolate`              | NIE DOTYCZY                                                                 |
| `includeSpeedLimit`        | NIE DOTYCZY                                                                 |
| `includeTruckSpeedLimit`   | NIE DOTYCZY                                                                 |
| `speedUnit`                | NIE DOTYCZY                                                                 |
| `travelMode`               | `travelMode`                                                        |
| `key`                      | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) . |
| `culture` (`c`)            | `language` — Zobacz dokumentację [obsługiwanych języków](https://docs.microsoft.com/azure/azure-maps/supported-languages) .   |
| `userRegion` (`ur`)        | `view` — Zobacz dokumentację [obsługiwanych widoków](https://aka.ms/AzureMapsLocalizationViews) .   |

Interfejs API routingu Azure Maps obsługuje również parametr routingu ciężarówki w ramach tego samego interfejsu API, aby można było obliczyć ścieżki logiczne. Poniższa tabela zawiera krzyżowe informacje o dodatkowych parametrach routingu ciężarówki mapy Bing przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi mapy Bing                 | Porównywalny parametr interfejsu API Azure Maps        |
|-----------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                | Nie dotyczy — wymiary w licznikach są obsługiwane tylko. |
| `weightUnit` (`wu`)                     | Nie dotyczy wag w kilogramach i jest obsługiwana. |
| `vehicleHeight` (`height`)              | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                  | `vehicleLength`                            |
| `vehicleWeight` (`weight`)              | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                | **NIE DOTYCZY**                                    |
| `vehicleSemi` (`semi`)                  | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)            | **NIE DOTYCZY**                                    |
| `vehicleMinTurnRadius` (`vmtr`)         | **NIE DOTYCZY**                                    |
| `vehicleAvoidCrossWind` (`vacw`)        | **NIE DOTYCZY**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)    | **NIE DOTYCZY**                                    |
| `vehicleHazardousMaterials` (`vhm`)     | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)       | `vehicleLoadType`                          |

Ponieważ ta metoda korzysta z interfejsu API wskazówek dotyczących trasy, pełny zestaw opcji w tej usłudze może służyć do dostosowywania logiki służącej do przyciągania współrzędne do dróg. Na przykład określenie czasu wyruszenia spowoduje uwzględnienie danych o ruchu historycznym.

Interfejs API wskazówek dotyczących trasy Azure Maps nie zwraca obecnie danych o limicie szybkości, ale można go pobrać za pomocą interfejsu API odwrócenia geokodowania Azure Maps.

**Używanie zestawu SDK sieci Web do przyciągania współrzędnych**

Zestaw SDK sieci Web Azure Maps używa kafelków wektorowych do renderowania map. Te kafelki wektorowe zawierają informacje o geometrii nieprzetworzonych i mogą być używane do obliczania najbliższej drogi do współrzędnych prostego przyciągania poszczególnych współrzędnych. Jest to przydatne, jeśli chcesz, aby współrzędne wyglądały wizualnie na drogach, a już używasz Azure Maps Web SDK do wizualizacji danych.

Takie podejście będzie jednak przyciągać się tylko do segmentów dróg, które są ładowane w widoku mapy. W przypadku powiększania na poziomie kraju nie mogą istnieć żadne dane drogowe, więc nie można wykonać przyciągania, jednak na poziomie powiększenia jeden piksel może reprezentować obszar kilku bloków miast, aby przyciąganie nie było niezbędne. Aby rozwiązać ten potrzeba, można zastosować logikę przyciągania za każdym razem, gdy mapa została zakończona. [Oto przykład kodu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic) , który pokazuje to.

**Używanie kafelków wektorów Azure Maps bezpośrednio do współrzędnych przyciągania**

Azure Maps kafelki wektorowe zawierają dane nieprzetworzonej geometrii, których można użyć do obliczenia najbliższego punktu w drodze do współrzędnej, aby wykonać podstawowe przyciąganie poszczególnych współrzędnych. Wszystkie segmenty drogi pojawiają się w sektorach na poziomie powiększenia 15, dlatego chcesz pobrać z niego kafelki. Następnie można użyć funkcji [matematycznej ostrosłupa kafelka QuadTree](https://docs.microsoft.com/azure/azure-maps/zoom-levels-and-tile-grid) , aby określić, że kafelki są potrzebne, i przekonwertować kafelki na geometrie. Z poziomu biblioteki matematycznej, takiej jak [Turf js](http://turfjs.org/) lub [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) , można użyć do obliczenia najbliższych segmentów wiersza.

## <a name="retrieve-a-map-image-static-map"></a>Pobieranie obrazu mapy (mapa statyczna)

Azure Maps udostępnia interfejs API do renderowania obrazów mapy statycznej z przełożonymi danymi. Interfejs API [renderowania obrazu mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) Azure Maps jest porównywalny z interfejsem API mapy statycznej w usłudze mapy Bing.

> [!NOTE]
> Azure Maps wymaga, aby wszystkie pinezki i lokalizacje ścieżki były współrzędnymi w `longitude,latitude` formacie, podczas gdy mapy Bing używają tego `latitude,longitude` formatu.</p>
<p>Adresy muszą mieć najpierw kod geokodowania.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi mapy Bing przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi mapy Bing  | Porównywalny parametr interfejsu API Azure Maps            |
|--------------------------|------------------------------------------------|
| `centerPoint`            | `center`                                       |
| `format`                 | `format` — określony jako część ścieżki URL. Obecnie obsługiwane są tylko pliki PNG.  |
| `heading`                | Nie dotyczy — Streetside nie jest obsługiwane.                |
| `imagerySet`             | `layer` i `style` — Zobacz dokumentację [obsługiwanych stylów mapy](https://docs.microsoft.com/azure/azure-maps/supported-map-styles) .   |
| `mapArea` (`ma`)         | `bbox`                                         |
| `mapLayer` (`ml`)        | Brak                                            |
| `mapSize` (`ms`)         | `width` i `height` — może mieć rozmiar maksymalnie 8192x8192. |
| `declutterPins` (`dcl`)  | NIE DOTYCZY                                            |
| `dpi`                    | NIE DOTYCZY                                            |
| `drawCurve`              | `path`                                         |
| `mapMetadata`            | NIE DOTYCZY                                            |
| `pitch`                  | Nie dotyczy — Streetside nie jest obsługiwane.                |
| `pushpin` (`pp`)         | `pins`                                         |
| `zoomLevel`              | `zoom`                                         |
| `query`                  | Należy użyć pola centrum lub ograniczenia.     |
| `highlightEntity` (`he`) | NIE DOTYCZY                                            |
| `style`                  | NIE DOTYCZY                                            |
| parametry trasy         | Brak                                            |
| `key`                    | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) . |
| `culture` (`c`)          | `language` — Zobacz dokumentację [obsługiwanych języków](https://docs.microsoft.com/azure/azure-maps/supported-languages) .   |
| `userRegion` (`ur`)      | `view` — Zobacz dokumentację [obsługiwanych widoków](https://aka.ms/AzureMapsLocalizationViews) . |

> [!NOTE]
> Azure Maps używa systemu kafelków z kafelkami, które są dwa razy większym rozmiarem kafelków mapy używanych w usłudze mapy Bing. W związku z tym wartość poziomu powiększenia w Azure Maps będzie wyglądać o jeden poziom powiększenia bliżej Azure Maps w porównaniu z mapami Bing. Obniż poziom powiększenia w żądaniach, które są migrowane przez 1 w celu zrekompensowania tego.

Więcej informacji można znaleźć w [przewodniku jak w interfejsie API renderowania obrazu mapy](https://docs.microsoft.com/azure/azure-maps/how-to-render-custom-data) .

Oprócz możliwości generowania obrazu mapy statycznej, usługa Azure Maps renderuje również możliwość bezpośredniego dostępu do kafelków mapy w formacie rastrowym (PNG) i wektorowym;

-   [Kafelek mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile) — umożliwia pobranie kafelków rastrowych (PNG) i wektorów dla map bazowych (dróg, granic, tła).
-   [Kafelek mapy obrazów](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) — umożliwia pobranie kafelków zdjęć i obrazów satelitarnych.

### <a name="pushpin-url-parameter-format-comparison"></a>Porównanie formatu parametru adresu URL pinezki

**Przed: mapy Bing**

W usłudze mapy Bing pinezki można dodawać do obrazu mapy statycznej przy użyciu `pushpin` parametru w adresie URL. `pushpin`Parametr przyjmuje lokalizację w `latitude,longitude` formacie, styl ikony i etykietę tekstową (do trzech znaków), jak pokazano poniżej:

> `&pushpin=latitude,longitude;iconStyle;label`

Dodatkowe pinezki można dodać poprzez dodanie dodatkowych `pushpin` parametrów do adresu URL z innym zestawem wartości. Style ikon pinezki są ograniczone do jednego ze wstępnie zdefiniowanych stylów dostępnych w interfejsie API Maps usługi Bing.

Na przykład w usłudze mapy Bing czerwona Pinezka z etykietą "AB" można dodać do mapy według współrzędnych (Długość geograficzna:-110, Szerokość geograficzna: 45) z następującym parametrem adresu URL:

> `&pushpin=45,-110;7;AB`

<center>

![Numer PIN mapy statycznej mapy usługi Bing](media/migrate-bing-maps-web-service/bing-maps-static-map-pin.jpg)</center>

**Po: Azure Maps**

W Azure Maps, pinezki można także dodawać do obrazu mapy statycznej przez określenie `pins` parametru w adresie URL. Pinezki w Azure Maps są definiowane przez określenie stylu ikony i listy lokalizacji, które używają tego stylu ikon. Te informacje są następnie przesyłane do `pins` parametru można określić wiele razy, aby obsługiwać pinezki z różnymi stylami.

> `&pins=iconType|pinStyles||pinLocation1|pinLocation2|...`

Dodatkowe style mogą być używane przez dodanie dodatkowych `pins` parametrów do adresu URL przy użyciu innego stylu i zestawu lokalizacji.

Gdy powróci do lokalizacji przypinania, Azure Maps wymaga, aby współrzędne były w `longitude latitude` formacie, podczas gdy mapy Bing używają `latitude,longitude` formatu. Należy również zauważyć, że w Azure Maps występuje **spacja, a nie rozdzielona przecinkami** i szerokością geograficzną.

`iconType`Wartość określa typ kodu PIN do utworzenia i może mieć następujące wartości:

-   `default` — Domyślna ikona pinezki.
-   `none` — Nie jest wyświetlana ikona, tylko etykiety będą renderowane.
-   `custom` — Określa niestandardową ikonę, która ma być używana. Adres URL wskazujący ikonę obrazu można dodać na końcu `pins` parametru po informacjach o lokalizacji numeru PIN.
-   `{udid}` — Unikatowy identyfikator danych (UDID) dla ikony przechowywanej na platformie magazynu danych Azure Maps.

Style kodu PIN w Azure Maps są dodawane z formatem `optionNameValue` , z wieloma stylami oddzielonymi `|` znakami potoku () `iconType|optionName1Value1|optionName2Value2` . Zwróć uwagę na to, że nazwy i wartości opcji nie są rozdzielone. Następujące nazwy opcji stylu mogą służyć do stylu pinezki w Azure Maps:

-   `al` — Określa nieprzezroczystość (alfa) pinezki. Może być liczbą z przedziału od 0 do 1.
-   `an` — Określa kotwicę numeru PIN. Wartości X i y pikseli określone w formacie `x y` .
-   `co` — Kolor kodu PIN. Musi mieć 24-bitowy kolor szesnastkowy: `000000` do `FFFFFF` .
-   `la` — Określa zakotwiczenie etykiety. Wartości X i y pikseli określone w formacie `x y` .
-   `lc` — Kolor etykiety. Musi mieć kolor 24-i szesnastkowy: `000000` do `FFFFFF` .
-   `ls` — Rozmiar etykiety (w pikselach). Może być liczbą większą niż 0.
-   `ro` — Wartość w stopniach, aby obrócić ikonę. Może być liczbą z przedziału od-360 do 360.
-   `sc` — Wartość skali dla ikony pinezki. Może być liczbą większą niż 0.

Wartości etykiet są określane dla każdej lokalizacji kodu PIN, a nie mają wartości pojedynczej etykiety, która ma zastosowanie do wszystkich pinezki na liście lokalizacji. Wartość etykiety może być ciągiem zawierającym wiele znaków i być opakowana pojedynczymi cudzysłowami, aby upewnić się, że nie zostanie ona pomylona jako wartość stylu lub lokalizacji.

Na przykład w Azure Maps, dodając czerwoną ( `FF0000` ) ikonę z etykietą "obszar wskazówki" umieszczoną poniżej (15 50), na współrzędnej (Długość geograficzna:-122,349300, Szerokość geograficzna: 47,620180) można wykonać przy użyciu następującego parametru adresu URL:

> `&pins=default|coFF0000|la15 50||'Space Needle'-122.349300 47.620180`

<center>

![Numer PIN statycznej mapy Azure Maps](media/migrate-bing-maps-web-service/azure-maps-static-map-pin.jpg)</center>

Poniższy przykład dodaje trzy numery PIN z wartościami etykiet "1", "2" i "3":

> `&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12`

<center>

![Azure Maps statycznej mapy wielu pinów](media/migrate-bing-maps-web-service/azure-maps-static-map-multiple-pins.jpg)</center>

### <a name="draw-curve-url-parameter-format-comparison"></a>Porównanie formatu parametru adresu URL krzywizny

**Przed: mapy Bing**

W usłudze mapy Bing, linie i wielokąty mogą być dodawane do obrazu mapy statycznej przy użyciu `drawCurve` parametru w adresie URL. `drawCurve`Parametr przyjmuje typ kształtu, typ stylu i listę lokalizacji, które mają być renderowane na mapie, jak pokazano poniżej:

> `&drawCurve=shapeType,styleType,location1,location2...`

Dodatkowe style mogą być używane przez dodanie dodatkowych `drawCurve` parametrów do adresu URL przy użyciu innego stylu i zestawu lokalizacji.

Lokalizacje w usłudze mapy Bing są określane przy użyciu formatu `latitude1,longitude1_latitude2,longitude2_…` . Lokalizacje można także zakodować.

Typy kształtów w usłudze mapy Bing obejmują linie, wielokąty, okrąg i krzywą. Typy stylów obejmują kolor linii, grubość linii, kolor konturu, kolor wypełnienia, grubość konturu i promień cykliczny.

Na przykład w usłudze mapy Bing niebieska linia z nieprzezroczystością 50% i grubością czterech pikseli można dodać do mapy między współrzędnymi (Długość geograficzna: 110, Szerokość geograficzna: 45 i Długość geograficzna:-100, Latitude: 50) przy użyciu następującego parametru adresu URL:

`&drawCurve=l,FF000088,4;45,-110_50,-100`

<center>

![Statyczna linia mapy mapy Bing](media/migrate-bing-maps-web-service/bing-maps-static-map-line.jpg)</center>

**Po: Azure Maps**

W Azure Maps, linie i wielokąty mogą być również dodawane do statycznego obrazu mapy przez określenie parametru *Path* w adresie URL. Podobnie jak mapy Bing, styl i Lista lokalizacji można określić w tym parametrze, a parametr *Path* można określić wiele razy, aby renderować wiele okręgów, linii i wielokątów o różnych stylach.

> `&path=pathStyles||pathLocation1|pathLocation2|...`

Gdy powróci do lokalizacji ścieżki, Azure Maps wymaga, aby współrzędne były w `longitude latitude` formacie, podczas gdy mapy Bing używają `latitude,longitude` formatu. Należy również zauważyć, że w Azure Maps występuje **spacja, a nie rozdzielona przecinkami** i szerokością geograficzną. Azure Maps nie obsługuje obecnie zakodowanych ścieżek. Większe zestawy danych można przekazać jako plik GEOJSON do interfejsu API magazynu danych Azure Maps, jak opisano [tutaj](https://docs.microsoft.com/azure/azure-maps/how-to-render-custom-data#get-data-from-azure-maps-data-storage).

Style ścieżki w Azure Maps są dodawane z formatem `optionNameValue` , z wieloma stylami oddzielonymi znakami potoku ( `|` ) `optionName1Value1|optionName2Value2` . Zwróć uwagę na to, że nazwy i wartości opcji nie są rozdzielone. Następujące nazwy opcji stylu mogą służyć do nadawania stylu ścieżkom w Azure Maps:

-   `fa` — Nieprzezroczystość koloru wypełnienia (alfa) używana podczas renderowania wielokątów. Może być liczbą z przedziału od 0 do 1.
-   `fc` — Kolor wypełnienia używany do renderowania obszaru wielokąta.
-   `la` — Nieprzezroczystość koloru linii (alfa) używana podczas renderowania linii i konspektu wielokątów. Może być liczbą z przedziału od 0 do 1.
-   `lc` — Kolor linii używany do renderowania linii i konspektu wielokątów.
-   `lw` — Szerokość linii w pikselach.
-   `ra` – Określa promień okręgów w metrach.

Na przykład, w Azure Maps, niebieska linia z nieprzezroczystością 50% i grubością czterech pikseli można dodać do mapy między współrzędnymi (Długość geograficzna: 110, Szerokość geograficzna: 45 i Długość geograficzna:-100, Latitude: 50) przy użyciu następującego parametru adresu URL:

> `&path=lc0000FF|la.5|lw4||-110 45|-100 50`

<center>

![Azure Maps linia mapy statycznej](media/migrate-bing-maps-web-service/azure-maps-static-map-line.jpg)</center>

## <a name="calculate-a-distance-matrix"></a>Obliczanie macierzy odległości

Azure Maps udostępnia interfejs API do obliczania czasów podróży i odległości między zestawem lokalizacji jako matrycą odległości. Interfejs API macierzy Azure Mapsej odległości jest porównywalny z interfejsem API macierzy odległości w usłudze mapy Bing.

-   [Macierz trasy](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): asynchronicznie oblicza czasy podróży i odległości dla zestawu źródeł i miejsc docelowych. Obsługiwane są maksymalnie 700 komórki na żądanie (liczba źródeł pomnożona przez liczbę miejsc docelowych). Mając to na uwadze, przykłady możliwych wymiarów macierzy to: `700x1` ,,, `50x10` `10x10` `28x25` , `10x70` .

> [!NOTE]
> Żądanie interfejsu API macierzy odległości można wykonać tylko przy użyciu żądania POST z informacjami o lokalizacji źródłowej i docelowej w treści żądania.</p>
<p>Ponadto Azure Maps wymaga współrzędnych wszystkich źródeł i miejsc docelowych. Adresy muszą mieć najpierw kod geokodowania.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi mapy Bing przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi mapy Bing | Porównywalny parametr interfejsu API Azure Maps                         |
|-------------------------|-------------------------------------------------------------|
| `origins`               | `origins` — Określ w treści żądania POST jako GEOJSON.    |
| `destinations`          | `destination` — Określ w treści żądania POST jako GEOJSON. |
| `endTime`               | `arriveAt`                                                  |
| `startTime`             | `departAt`                                                  |
| `travelMode`            | `travelMode`                                                |
| `resolution`            | Brak                                                         |
| `distanceUnit`          | Nie dotyczy — wszystkie odległości w licznikach.                              |
| `timeUnit`              | Nie dotyczy — wszystkie razy w sekundach.                                 |
| `key`                   | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) . |
| `culture` (`c`)         | `language` — Zobacz dokumentację [obsługiwanych języków](https://docs.microsoft.com/azure/azure-maps/supported-languages) .  |
| `userRegion` (`ur`)     | `view` — Zobacz dokumentację [obsługiwanych widoków](https://aka.ms/AzureMapsLocalizationViews) .     |

> [!TIP]
> Wszystkie zaawansowane opcje routingu dostępne w interfejsie API routingu Azure Maps (Routing ciężarówki, Specyfikacja aparatu, unikanie...) są obsługiwane w interfejsie API macierzy na platformie Azure Maps.

## <a name="calculate-an-isochrone"></a>Oblicz isochrone

Azure Maps udostępnia interfejs API do obliczania isochrone, wielokąt obejmujący obszar, do którego można podróżować w dowolnym kierunku od punktu początkowego w określonym czasie lub w ilości paliwa/opłaty. Interfejs API zakresu tras Azure Maps jest porównywalny z interfejsem API isochrone w usłudze mapy Bing.

-   [Trasa](https://docs.microsoft.com/rest/api/maps/route/getrouterange) Range * *: oblicza Wielokąt obejmujący obszar, do którego można podróżować w dowolnym kierunku od punktu początkowego w określonym czasie, odległości lub ilości dostępnego paliwa/opłaty.

> [!NOTE]
> Azure Maps wymaga, aby pochodzenie zapytania było współrzędną. Adresy muszą mieć najpierw kod geokodowania.</p>
<p>Ponadto mapy Bing mogą obliczać izochron na podstawie czasu lub odległości, natomiast Azure Maps mogą obliczyć izochron na podstawie czasu, odległości lub ilości dostępnego paliwa/opłaty.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi mapy Bing przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi mapy Bing      | Porównywalny parametr interfejsu API Azure Maps            |
|------------------------------|------------------------------------------------|
| `waypoint` (`wp`)            | `query`                                        |
| `dateTime` (`dt`)            | `departAt`                                     |
| `maxTime`                    | `timeBudgetInSec`                              |
| `timeUnit` (`tu`)            | Nie dotyczy — wszystkie razy w sekundach.                    |
| `travelMode` (`mode`)        | `travelMode`                                   |
| `maxDistance` (`maxDis`)     | `distanceBudgetInMeters`                       |
| `distanceUnit` (`du`)        | Nie dotyczy — wszystkie odległości w licznikach.                 |
| `optimize` (`optmz`)         | `routeType`                                    |
| `key`                        | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) . |
| `culture` (`c`)              | `language` — Zobacz dokumentację [obsługiwanych języków](https://docs.microsoft.com/azure/azure-maps/supported-languages) .  |
| `userRegion` (`ur`)          | `view` — Zobacz dokumentację [obsługiwanych widoków](https://aka.ms/AzureMapsLocalizationViews) . |

> [!TIP]
> Wszystkie zaawansowane opcje routingu dostępne w interfejsie API routingu Azure Maps (Routing ciężarówki, Specyfikacja aparatu, unikanie...) są obsługiwane w interfejsie API Azure Maps isochrone.

## <a name="search-for-points-of-interest"></a>Wyszukiwanie punktów orientacyjnych

Punkty danych o zainteresowaniach mogą być przeszukiwane w usłudze mapy Bing przy użyciu następujących interfejsów API:

-   **Wyszukiwanie lokalne:** Wyszukuje punkty, które są w pobliżu (wyszukiwanie promieniowe), według nazwy lub typu jednostki (kategorii). Interfejsy API [wyszukiwania kategorii](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) Azure Maps [punkt POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) i punkt POI są podobne do tego interfejsu API.
-   **Rozpoznawanie lokalizacji**: wyszukuje punkty zainteresowań, które znajdują się w określonej odległości od lokalizacji. Interfejs API [wyszukiwania w pobliżu](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) Azure Maps przypomina ten interfejs API.
-   **Informacje lokalne:** Wyszukuje punkty zainteresowań, które znajdują się w określonym maksymalnym czasie napędowym lub odległości od określonej współrzędnej. Jest to możliwe do osiągalności przy użyciu Azure Maps, najpierw obliczając isochrone, a następnie przechodząc do [wyszukiwania w](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) interfejsie API geometrii.

Azure Maps udostępnia kilka interfejsów API wyszukiwania dla interesujących punktów:

-   [Wyszukiwanie punkt POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): Wyszukiwanie punktów zainteresowania według nazwy. Na przykład: `"starbucks"`.
-   [Wyszukiwanie kategorii punkt POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): Wyszukiwanie punktów zainteresowania według kategorii. Na przykład: "restauracji".
-   [Wyszukiwanie w pobliżu](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): wyszukuje punkty zainteresowań, które znajdują się w określonej odległości od lokalizacji.
-   [Wyszukiwanie rozmyte](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): ten interfejs API łączy geokodowanie adresów z punktem wyszukiwania zainteresowania. Ten interfejs API przyjmuje ciąg o dowolnej postaci, który może być adresem, miejscem, punktem orientacyjnym lub kategorią punktu zainteresowania, i natychmiastowo przetworzyć żądanie. Ten interfejs API jest zalecany w przypadku aplikacji, w których użytkownicy mogą wyszukiwać adresy lub punkty istotne z tego samego pola tekstowego.
-   [Wyszukaj w obrębie geometrii](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): Wyszukaj punkty zainteresowań, które znajdują się w określonej geometrii (Wielokąt).
-   [Wyszukiwanie wzdłuż trasy](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): Wyszukiwanie punktów zainteresowań, które znajdują się w określonej ścieżce trasy.
-   [Wyszukiwanie rozmytej partii](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 adresów, miejsc, punktów orientacyjnych lub punktu zainteresowania i przetworzyć je w określonym czasie. Wszystkie dane zostaną przetworzone równolegle na serwerze i po ukończeniu można pobrać pełny zestaw wyników.

Pamiętaj, aby zapoznać się z [najlepszymi rozwiązaniami](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) dotyczącymi dokumentacji wyszukiwania.

## <a name="get-traffic-incidents"></a>Pobierz zdarzenia dotyczące ruchu

Azure Maps udostępnia kilka interfejsów API do pobierania danych o ruchu. Dostępne są dwa typy danych ruchu;

-   **Dane przepływu** — dostarcza metryki przepływu ruchu w sekcjach dróg. Jest to często używane do kolorowania dróg o kodzie. Te dane są aktualizowane co 2 minuty.
-   **Dane zdarzenia** — udostępnia dane dotyczące konstruowania, zamykania podróży, wypadków i innych zdarzeń, które mogą wpływać na ruch. Te dane są aktualizowane co minutę.

Usługa mapy Bing udostępnia dane dotyczące przepływu ruchu i zdarzeń w formantach mapy interakcyjnej, a także udostępniają dane o zdarzeniach jako usługi.

Dane o ruchu są również zintegrowane z Azure Maps formantami mapy interaktywnej. Usługa Azure Maps udostępnia również następujące interfejsy API usług ruchu;

-   [Segmenty przepływów ruchu](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficflowsegment): zawiera informacje o szybkościach i godzinach podróży fragmentu drogi znajdującego się najbliżej podanych współrzędnych.
-   [Kafelki przepływu ruchu](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficflowtile): zawiera kafelki rastrowe i wektorowe zawierające dane przepływu ruchu. Mogą one być używane z kontrolkami Azure Maps lub kontrolkami mapy innych firm, takimi jak ulotka. Kafelki wektorowe mogą być również używane do zaawansowanej analizy danych.
-   [Szczegóły zdarzenia dotyczącego ruchu](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidentdetail): zawiera szczegółowe informacje o zdarzeniach, które znajdują się w obrębie pola ograniczenia, poziomu powiększenia i modelu ruchu.
-   [Kafelki zdarzeń ruchu](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidenttile): zawiera kafelki rastrowe i wektorowe zawierające dane zdarzenia dotyczącego ruchu.
-   [Okienko ekranu zdarzeń ruchu](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidentviewport): Pobiera informacje prawne i techniczne okienka ekranu opisane w żądaniu, takie jak identyfikator modelu ruchu.

W poniższej tabeli odwołuje się do parametrów interfejsu API ruchu Maps mapy Bing z porównywalnymi parametrami interfejsu API szczegółów zdarzenia dotyczącego ruchu w Azure Maps.

| Parametr interfejsu API usługi mapy Bing  | Porównywalny parametr interfejsu API Azure Maps   |
|--------------------------|---------------------------------------|
| `mapArea`                | `boundingBox` i `boundingZoom`      |
| `includeLocationCodes`   | Brak                                   |
| `severity` (`s`)         | Nie dotyczy — wszystkie zwrócone dane               |
| `type` (`t`)             | Nie dotyczy — wszystkie zwrócone dane               |
| `key`                    | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) . |
| `culture` (`c`)          | `language` — Zobacz dokumentację [obsługiwanych języków](https://docs.microsoft.com/azure/azure-maps/supported-languages) . |
| `userRegion` (`ur`)      | `view` — Zobacz dokumentację [obsługiwanych widoków](https://aka.ms/AzureMapsLocalizationViews) . |

## <a name="get-a-time-zone"></a>Pobierz strefę czasową

Azure Maps udostępnia interfejs API do pobierania strefy czasowej, w której znajduje się Współrzędna. Interfejs API strefy czasowej Azure Maps jest porównywalny z interfejsem API strefy czasowej w usłudze mapy Bing;

-   [Strefa czasowa według współrzędnych](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Określ współrzędną i uzyskaj szczegóły dla strefy czasowej, w której się znajduje.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi mapy Bing przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi mapy Bing | Porównywalny parametr interfejsu API Azure Maps          |
|-------------------------|----------------------------------------------|
| `point`                 | `query`                                      |
| `query`                 | Lokalizacje muszą mieć najpierw kod geokodowania.      |
| `dateTime`              | `timeStamp`                                  |
| `includeDstRules`       | Nie dotyczy — zawsze dołączane do odpowiedzi przez Azure Maps. |
| `key`                   | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą dokumentacji Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) . |
| `culture` (`c`)         | `language` — Zobacz dokumentację [obsługiwanych języków](https://docs.microsoft.com/azure/azure-maps/supported-languages) .  |
| `userRegion` (`ur`)     | `view` — Zobacz dokumentację [obsługiwanych widoków](https://aka.ms/AzureMapsLocalizationViews) .  |

Oprócz tego platforma Azure Maps udostępnia również kilka dodatkowych interfejsów API strefy czasowej, które ułatwiają konwersje z nazwami stref czasowych i identyfikatorami.

-   [Strefa czasowa według identyfikatora](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): zwraca bieżące, historyczne i przyszłe informacje o strefie czasowej dla określonego identyfikatora strefy czasowej organizacji IANA.
-   [Wyliczenie strefy czasowej Iana](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): zwraca pełną listę identyfikatorów stref czasowych organizacji IANA. Aktualizacje usługi IANA są uwzględniane w systemie w ciągu jednego dnia. 
-   [Okna wyliczania strefy czasowej](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): zwraca pełną listę identyfikatorów strefy czasowej systemu Windows.
-   [Wersja programu Iana strefy czasowej](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): zwraca bieżący numer wersji programu Iana używany przez Azure Maps. 
-   [System Windows strefy czasowej dla organizacji Iana](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): zwraca odpowiedni identyfikator Iana, uwzględniając prawidłowy identyfikator strefy czasowej systemu Windows. Dla pojedynczego identyfikatora systemu Windows można zwrócić wiele identyfikatorów IANA.

## <a name="spatial-data-services-sds"></a>Data Services przestrzenne

Usługi danych przestrzennych w usłudze mapy Bing zapewniają trzy kluczowe funkcje:

-   Geokodowanie wsadowe — przetwarzanie dużej partii adresów geokodowanych za pomocą pojedynczego żądania.
-   Pobierz dane graniczne administracyjne — Użyj współrzędnej i uzyskaj granicę przecinania dla określonego typu jednostki.
-   Dane biznesowe hosta i zapytania — Przekaż prostą tabelę 2D i uzyskaj dostęp do niej przy użyciu kilku prostych zapytań przestrzennych.

### <a name="batch-geocode-data"></a>Dane z geokodu partii

Geokodowanie wsadowe to proces pobierania dużej liczby adresów lub miejsc, przekazywania ich wszystkich w jednym żądaniu do usługi i ze wszystkich geokodowanych ze sobą elementów oraz wyników zwróconych w ramach jednej odpowiedzi.

Usługi mapy Bing umożliwiają przekazywanie do 200 000 adresów w ramach pojedynczego żądania geokodowania wsadowego. To żądanie przechodzi do kolejki i zwykle przetwarza w określonym czasie, w dowolnym miejscu od kilku minut do kilku godzin, w zależności od rozmiaru zestawu danych i obciążenia usługi. Każdy adres w żądaniu wygenerował transakcję.

Azure Maps ma usługę wsadowego geokodowania, ale umożliwia przekazywanie maksymalnie 10 000 adresów w jednym żądaniu i jest przetwarzana w ciągu kilku minut w zależności od rozmiaru zestawu danych i obciążenia usługi. Każdy adres w żądaniu wygenerował transakcję. W Azure Maps usługa wsadowych geokodowania jest dostępna tylko w warstwie S1.

Kolejną opcją geokodowania wielu adresów przy użyciu Azure Maps jest wykonywanie żądań równoległych do standardowych interfejsów API wyszukiwania. Te usługi akceptują tylko jeden adres na żądanie, ale mogą być używane z warstwą S0, która zapewnia także bezpłatne limity użycia. Warstwa S0 umożliwia do 50 żądań na sekundę na platformę Azure Maps z jednego konta. Dlatego jeśli przetworzysz limit thes w tym limicie, możliwe jest geokodowanie w górę o 180 000 godziny. Warstwa S1 nie ma udokumentowanego limitu liczby zapytań na sekundę, które mogą zostać wykonane z konta, dzięki czemu znacznie więcej danych można szybciej przetwarzać podczas korzystania z tej warstwy cenowej, jednak korzystanie z usługi Batch geokodowania nie pozwala zmniejszyć łącznej ilości transferowanych danych i znacząco obniżyć ruch sieciowy.

-   [Geokodowanie adresów w formie Geokodowanej](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): Określ pojedynczy ciąg adresu (na przykład `"1 Microsoft way, Redmond, WA"` ) i natychmiast przetwórz żądanie. Ta usługa jest zalecana, jeśli trzeba szybko odkodować poszczególne adresy.
-   [Geokodowanie adresów strukturalnych](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Określ części pojedynczego adresu, takie jak nazwa ulicy, miasto, kraj i kod pocztowy, i natychmiast przetwórz żądanie. Ta usługa jest zalecana, jeśli trzeba szybko odkodować poszczególne adresy, a dane są już analizowane w poszczególnych częściach adresu.
-   [Geokodowanie adresów partii](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 adresów i przetworzy je w danym okresie czasu. Wszystkie adresy będą kodowane geokodowowo na serwerze i po ukończeniu można pobrać pełny zestaw wyników. Ta usługa jest zalecana do geokodowania dużych zestawów danych.
-   [Wyszukiwanie rozmyte](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): ten interfejs API łączy geokodowanie adresów z punktem wyszukiwania zainteresowania. Ten interfejs API przyjmuje ciąg o dowolnej postaci, który może być adresem, miejscem, punktem orientacyjnym lub kategorią punktu zainteresowania, i natychmiastowo przetworzyć żądanie. Ten interfejs API jest zalecany w przypadku aplikacji, w których użytkownicy mogą wyszukiwać adresy lub punkty istotne z tego samego pola tekstowego.
-   [Wyszukiwanie rozmytej partii](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 adresów, miejsc, punktów orientacyjnych lub punktu zainteresowania i przetworzyć je w określonym czasie. Wszystkie dane zostaną przetworzone równolegle na serwerze i po ukończeniu można pobrać pełny zestaw wyników.

### <a name="get-administrative-boundary-data"></a>Pobierz dane z granicy administracyjnej

W usłudze mapy Bing granice administracyjne dla krajów, Stanów, powiatów, miast i kodów pocztowych są udostępniane za pośrednictwem interfejsu API geodanych. Ten interfejs API wykonuje współrzędną lub zapytanie w celu geokodowania. Jeśli zapytanie jest przesyłane, jest geokodowane i współrzędne od pierwszego wyniku są używane. Ten interfejs API przyjmuje współrzędne i pobiera granicę określonego typu obiektu, który przecina współrzędną. Należy zauważyć, że ten interfejs API nie musi zwrócić granicy dla zapytania, które zostało przesłane. Jeśli zostanie `"Seattle, WA"` przekazana kwerenda dla, ale wartość typu jednostki jest ustawiona na region kraju, zostanie zwrócona granica dla USA.

Azure Maps zapewnia również dostęp do granic administracyjnych (krajów, Stanów, powiatów, miast i kodów pocztowych). Aby pobrać granicę, należy wykonać zapytanie dotyczące jednego z interfejsów API wyszukiwania dla żądanej granicy (tj. `Seattle, WA` ). Jeśli wynik wyszukiwania ma skojarzoną granicę, w odpowiedzi wynikowej zostanie podany identyfikator geometrii. Można następnie użyć interfejsu API wyszukiwania wielokątnego, aby pobrać dokładne granice dla co najmniej jednego identyfikatora geometrii. Jest to bit inny niż mapy Bing, ponieważ Azure Maps zwraca granicę wyszukiwanego elementu, natomiast mapy Bing zwracają granicę dla określonego typu jednostki na określonej współrzędnych. Ponadto dane graniczne zwrócone przez Azure Maps są w formacie GEOJSON.

Do Podsumowanie:

1.  Przekaż zapytanie dotyczące granicy, która ma zostać przekazana do jednego z następujących interfejsów API wyszukiwania.
    -   [Geokodowanie adresów w postaci bezpłatnej](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
    -   [Geokodowanie adresów strukturalnych](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured)
    -   [Geokodowanie adresów partii](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)
    -   [Wyszukiwanie rozmyte](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
    -   [Wyszukiwanie rozmytej partii](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)
2.  Jeśli żądane wyniki mają identyfikatory geometrii, przekaż je do [interfejsu API wielokątów wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

### <a name="host-and-query-spatial-business-data"></a>Dane biznesowe hosta i zapytania

Usługi danych przestrzennych w usłudze mapy Bing zapewniają proste rozwiązanie magazynu danych przestrzennych do hostowania danych firmowych i uwidacznia je jako usługę REST. Ta usługa udostępnia cztery główne zapytania: Znajdź według właściwości, Znajdź w pobliżu, Znajdź w polu ograniczenia i Znajdź z 1 kilometrem trasy. Wiele firm korzystających z tej usługi często ma już swoje dane biznesowe, które są już przechowywane w bazie danych, i przekazywać do nich mały podzestaw, aby uzyskać aplikacje, takie jak lokalizatory magazynu. Ponieważ uwierzytelnianie oparte na kluczach zapewnia podstawowe zabezpieczenia, zaleca się korzystanie z tej usługi tylko z publicznymi danymi dostępnymi.

Większość danych lokalizacji biznesowej zaczyna się w bazie danych. W związku z tym zaleca się używanie istniejących rozwiązań usługi Azure Storage, takich jak Azure SQL czy Azure PostgreSQL (z dodatkiem PostGIS). Oba te rozwiązania magazynu obsługują dane przestrzenne i zapewniają bogaty zestaw funkcji zapytań przestrzennych. Gdy dane będą znajdować się w odpowiednim rozwiązaniu magazynu, można je następnie zintegrować z aplikacją przez utworzenie niestandardowej usługi sieci Web lub przy użyciu struktury, takiej jak ASP.NET lub Entity Framework. Użycie tej metody zapewnia więcej możliwości zapytań oraz znacznie wyższe opcje zabezpieczeń.

Azure Cosmos DB udostępnia również ograniczony zestaw możliwości przestrzennych, które w zależności od danego scenariusza mogą być wystarczające.

Oto kilka przydatnych zasobów związanych z hostingiem i wykonywaniem zapytań o dane przestrzenne na platformie Azure.

-   [Typy danych przestrzennych usługi Azure SQL — Omówienie](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview)
-   [Przestrzenny Azure SQL — zapytanie najbliższe sąsiada](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor)
-   [Omówienie funkcji geoprzestrzennych Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/geospatial)

## <a name="client-libraries"></a>Biblioteki klienta

Azure Maps udostępnia biblioteki klienckie dla następujących języków programowania;

-   JavaScript, TypeScript, Node.js — [Dokumentacja](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module) \| [pakietu npm](https://www.npmjs.com/package/azure-maps-rest)

Biblioteki klienckie Open Source dla innych języków programowania;

-   .NET Standard 2,0 — [GitHub project](https://github.com/perfahlen/AzureMapsRestServices) \| [pakiet NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/) projektu GitHub

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat Azure Maps usług REST.

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dotyczące korzystania z usługi wyszukiwania](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dotyczące korzystania z usługi routingu](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Jak używać modułu usług (Web SDK)](how-to-use-best-practices-for-routing.md)

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usługi REST Azure Maps](https://docs.microsoft.com/rest/api/maps/)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
