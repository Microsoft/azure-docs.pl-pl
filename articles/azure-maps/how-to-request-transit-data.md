---
title: Żądanie danych tranzytowych za pomocą usługi Mobility Microsoft Azure Maps
description: Dowiedz się, jak za pomocą usługi mobilności Azure Maps zażądać danych tranzytu publicznego, takich jak identyfikatory obszaru Metro, zatrzymania podróży, tras i trasy tras.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/22/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3f6f50d0ffeb48b5f359221992cc9a51d2ebb056
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895668"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Żądanie danych tranzytu publicznego przy użyciu usługi Azure Maps Mobility

W tym artykule pokazano, jak za pomocą [usługi mobilności](/rest/api/maps/mobility) Azure Maps żądania danych tranzytu publicznego. Dane tranzytowe obejmują zatrzymanie tranzytu, informacje o trasie i oszacowania czasu podróży.

W tym artykule dowiesz się, jak:

* Pobieranie identyfikatora obszaru Metro przy użyciu [interfejsu API pobierania Metro](/rest/api/maps/mobility/getmetroareapreview)
* Zażądaj zatrzymywania tranzytu w pobliżu przy użyciu usługi [pobierania w pobliżu](/rest/api/maps/mobility/getnearbytransitpreview) .
* Zapytanie [pobieranie tras tranzytowych interfejsu API](/rest/api/maps/mobility/gettransitroutepreview) w celu zaplanowania trasy przy użyciu tranzytu publicznego.
* Żądaj geometrii trasy tranzytowej i szczegółowego harmonogramu dla trasy przy użyciu [interfejsu API uzyskiwania tranzytu](https://aka.ms/https://azure.microsoft.com/services/azure-maps/)trasy.

## <a name="prerequisites"></a>Wymagania wstępne

1. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](./how-to-manage-authentication.md).

W tym samouczku jest stosowana aplikacja programu [Poster](https://www.postman.com/) , ale można wybrać inne środowisko deweloperskie interfejsu API.

## <a name="get-a-metro-area-id"></a>Pobieranie identyfikatora obszaru Metro

Aby zażądać szczegółowych informacji o agencjach tranzytowych i obsługiwanych typach tranzytowych dla określonego obszaru metropolitalnych, będziesz potrzebować tego `metroId` obszaru. [Interfejs API uzyskiwania obszaru Metro](/rest/api/maps/mobility/getmetroareapreview) umożliwia zażądanie obszarów Metro, w których dostępna jest usługa Azure Maps Mobility. Odpowiedź zawiera szczegóły, takie jak `metroId` , `metroName` i reprezentacja geometrii obszaru Metro w formacie GEOJSON.

Utwórzmy żądanie pobrania obszaru Metro dla Seattle-Tacomaowego identyfikatora obszaru. Aby zażądać identyfikatora dla obszaru Metro, wykonaj następujące czynności:

1. Otwórz aplikację Poster i Utwórz kolekcję do przechowywania żądań. W górnej części aplikacji Poster wybierz pozycję **Nowy** . W oknie **Tworzenie nowego** okna wybierz pozycję **Kolekcja** .  Nadaj kolekcji nazwę i wybierz przycisk **Utwórz** .

2. Aby utworzyć żądanie, wybierz pozycję **nowe** ponownie. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj** . Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzednim kroku jako lokalizację, w której chcesz zapisać żądanie. Następnie wybierz pozycję **Zapisz** .
  
    ![Utwórz żądanie w programie Poster](./media/how-to-request-transit-data/postman-new.png)

3. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL, aby utworzyć żądanie Get. Zamień na `{subscription-key}` klucz podstawowy Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. Po pomyślnym żądaniu otrzymasz następującą odpowiedź:

    ```JSON
    {
        "results": [
            {
                "metroId": 522,
                "metroName": "Seattle–Tacoma–Bellevue, WA",
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                },
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -121.99604,
                                47.16147
                            ],
                            [
                                -121.97051,
                                47.17222
                            ],
                            [
                                -121.96308,
                                47.17671
                            ],
                            ...,
                            ...,
                            ...,
                            [
                                -122.01525,
                                47.16008
                            ],
                            [
                                -122.00553,
                                47.15919
                            ],
                            [
                                -121.99604,
                                47.16147
                            ]
                        ]
                    ]
                }
            }
        ]
    }
    ```

## <a name="request-nearby-transit-stops"></a>Żądaj zatrzymywania tranzytu w pobliżu

Azure Maps [pobrania usługi tranzytu w pobliżu](/rest/api/maps/mobility/getnearbytransitpreview) umożliwia wyszukiwanie obiektów tranzytowych. Interfejs API zwraca szczegóły obiektu tranzytowego, takie jak tranzyt publiczny, i udostępnione rowery wokół danej lokalizacji. Następnie wyślemy żądanie do usługi w celu wyszukania nieoczekiwanego tranzytu publicznego w ramach promienia 300-metrów wokół danej lokalizacji.

Aby wysłać żądanie do [procedury pobierania w pobliżu](/rest/api/maps/mobility/getnearbytransitpreview), wykonaj następujące czynności:

1. W programie Poster kliknij pozycję **nowe żądanie**  |  **Pobierz żądanie** i nazwij **Get Nearby stops** ją.

2. Na karcie Konstruktor wybierz metodę **Get** http, wprowadź następujący adres URL żądania dla punktu końcowego interfejsu API i kliknij przycisk **Wyślij** .

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. Po pomyślnym żądaniu struktura odpowiedzi powinna wyglądać podobnie do poniższej:

    ```JSON
    {
        "results": [
            {
                "id": "522---2060603",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632413,
                        "longitude": -122.12659
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594,
                        "longitude": -122.123959
                    }
                }
            },
            {
                "id": "522---2061020",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "68372",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318,
                        "longitude": -122.128451
                    },
                    "btmRightPoint": {
                        "latitude": 47.630499,
                        "longitude": -122.12582
                    }
                }
            },
            {
                "id": "522---2060604",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "71310",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474,
                        "longitude": -122.129124
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655,
                        "longitude": -122.126492
                    }
                }
            }
        ]
    } 
    ```

Jeśli uważnie obserwujemy strukturę odpowiedzi, zobaczysz, że zawiera ona parametry dla każdego obiektu tranzytowego. Każdy obiekt tranzytowy ma parametry, takie jak,,,, `id` `type` `stopName` `mainTransitType` `mainAgencyName` i położenie, we współrzędnych obiektu.

Na potrzeby nauki będziemy korzystać `id` z magistrali jako punktu początkowego dla naszej trasy w następnej sekcji.  

## <a name="request-a-transit-route"></a>Żądanie trasy tranzytowej

[Interfejs API uzyskiwania tras tranzytowych](/rest/api/maps/mobility/gettransitroutepreview) Azure Maps umożliwia planowanie podróży. Zwraca najlepsze możliwe opcje trasy ze źródła do miejsca docelowego. Usługa udostępnia różne rodzaje trybów podróży, w tym idące przechodzenie, pisanie odręczne i tranzyt publiczny. Następnie przeszukamy trasę od najbliższej magistrali do obudowy "Wieża" w Seattle.

### <a name="get-location-coordinates-for-destination"></a>Pobierz współrzędne lokalizacji dla miejsca docelowego

Aby uzyskać współrzędne lokalizacji obudowy typu Tower, użyjemy Azure Maps funkcji [wyszukiwania rozmytego](/rest/api/maps/search/getsearchfuzzy).

Aby wysłać żądanie do usługi wyszukiwania rozmytego, wykonaj następujące czynności:

1. W programie Poster kliknij pozycję **nowe żądanie**  |  **Pobierz żądanie** , a następnie nadaj jej nazwę **współrzędne lokalizacji** .

2. Na karcie Konstruktor wybierz metodę **Get** http, wprowadź następujący adres URL żądania, a następnie kliknij przycisk **Wyślij** .

    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```

3. Jeśli dokładnie sprawdzasz odpowiedź, zawiera wiele lokalizacji w wynikach wyszukiwania wskazówki dotyczącej miejsca. Każdy wynik zawiera współrzędne lokalizacji pod **pozycją** . Skopiuj `lat` i `lon` pod **pozycją** pierwszego wyniku.

   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 35,
            "numResults": 8,
            "offset": 0,
            "totalResults": 11,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/6993440",
                "score": 4.67369,
                "info": "search:ta:840539001406144-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "categorySet": [
                        {
                            "id": 7376009
                        }
                    ],
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "tower"
                    ],
                    "classifications": [
                        {
                            "code": "IMPORTANT_TOURIST_ATTRACTION",
                            "names": [
                                {
                                    "nameLocale": "en-US",
                                    "name": "important tourist attraction"
                                },
                                {
                                    "nameLocale": "en-US",
                                    "name": "tower"
                                }
                            ]
                        }
                    ]
                },
                "address": {
                    "streetNumber": "400",
                    "streetName": "Broad St",
                    "municipalitySubdivision": "South Lake Union, Seattle, Lower Queen Anne",
                    "municipality": "Seattle",
                    "countrySecondarySubdivision": "King",
                    "countryTertiarySubdivision": "Seattle",
                    "countrySubdivision": "WA",
                    "postalCode": "98109",
                    "countryCode": "US",
                    "country": "United States",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
                    "localName": "Seattle",
                    "countrySubdivisionName": "Washington"
                },
                "position": {
                    "lat": 47.62039,
                    "lon": -122.34928
                },
                "viewport": {
                    "topLeftPoint": {
                        "lat": 47.62129,
                        "lon": -122.35061
                    },
                    "btmRightPoint": {
                        "lat": 47.61949,
                        "lon": -122.34795
                    }
                },
                "entryPoints": [
                    {
                        "type": "main",
                        "position": {
                            "lat": 47.61982,
                            "lon": -122.34886
                        }
                    }
                ]
            },
            ...,
            ...,
            ...
        ]
    }
    ```

### <a name="request-route"></a>Trasa żądania

Aby wykonać żądanie trasy, wykonaj poniższe czynności:

1. W programie Poster kliknij pozycję **nowe żądanie**  |  **Pobierz żądanie** i nadaj jej nazwę **Uzyskaj informacje o trasie** .

2. Na karcie Konstruktor wybierz metodę **Get** http, wprowadź następujący adres URL żądania dla punktu końcowego interfejsu API i kliknij przycisk **Wyślij** .

    Będziemy żądać tras tranzytu publicznego dla magistrali, określając `modeType` Parametry i `transitType` . Adres URL żądania zawiera lokalizacje pobrane w poprzednich sekcjach. Dla `originType` mamy teraz **stopid** . W przypadku `destionationType` , mamy **położeniu** .

    Zapoznaj się z [listą parametrów identyfikatora URI](/rest/api/maps/mobility/gettransitroutepreview#uri-parameters) , których można użyć w żądaniu do [interfejsu API uzyskiwania tras tranzytowych](/rest/api/maps/mobility/gettransitroutepreview).
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. Po pomyślnym żądaniu struktura odpowiedzi powinna wyglądać podobnie do poniższej:

    ```JSON
    {
        "results": [
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:0---522",
                "departureTime": "2019-09-07T01:01:50Z",
                "arrivalTime": "2019-09-07T02:16:33Z",
                "travelTimeInSeconds": 4483,
                "numberOfLegs": 8,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:01:50Z",
                        "caption": "249"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:26:00Z",
                        "caption": "249",
                        "lengthInMeters": 9139
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:26:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "255"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:57:21Z",
                        "caption": "255",
                        "lengthInMeters": 13136
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:57:22Z",
                        "legEndTime": "2019-09-07T02:01:27Z",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T02:01:27Z",
                        "legEndTime": "2019-09-07T02:06:33Z",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:06:33Z",
                        "legEndTime": "2019-09-07T02:12:41Z",
                        "caption": "8",
                        "lengthInMeters": 1060
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:12:42Z",
                        "legEndTime": "2019-09-07T02:16:33Z",
                        "lengthInMeters": 251
                    }
                ],
                "itineraryFare": {
                    "price": {
                        "amount": 550,
                        "currencyCode": "USD"
                    },
                    "tickets": [
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        },
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        }
                    ]
                }
            },
            ...,
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:2---522",
                "departureTime": "2019-09-07T00:49:32Z",
                "arrivalTime": "2019-09-07T02:20:06Z",
                "travelTimeInSeconds": 5434,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T00:49:32Z",
                        "caption": "226"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T01:15:00Z",
                        "caption": "226",
                        "lengthInMeters": 6792
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:15:00Z",
                        "legEndTime": "2019-09-07T01:20:00Z",
                        "caption": "241"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:20:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "241",
                        "lengthInMeters": 3397
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:33:00Z",
                        "caption": "550"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:33:00Z",
                        "legEndTime": "2019-09-07T01:58:00Z",
                        "caption": "550",
                        "lengthInMeters": 12899
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:58:01Z",
                        "legEndTime": "2019-09-07T01:59:21Z",
                        "caption": "4th Avenue South",
                        "lengthInMeters": 99
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:59:21Z",
                        "legEndTime": "2019-09-07T02:01:00Z",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:01:00Z",
                        "legEndTime": "2019-09-07T02:13:29Z",
                        "caption": "33,24",
                        "lengthInMeters": 2447
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:13:30Z",
                        "legEndTime": "2019-09-07T02:20:06Z",
                        "lengthInMeters": 457
                    }
                ],
                "itineraryFare": {
                    "price": {
                        "amount": 550,
                        "currencyCode": "USD"
                    },
                    "tickets": [
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        },
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        }
                    ]
                }
            }
        ]
    }
    ```

4. Jeśli dokładnie zaobserwujesz, w odpowiedzi występuje wiele tras **magistrali** . Każda trasa ma unikatowy **Identyfikator trasy** , podsumowanie opisujące każdy z etapów trasy, a w `itineraryFare` tym zarówno element, jak i łączną cenę biletów magistrali. Etap trasy jest częścią trasy między dwoma waypoints Stop. Następnie będziemy żądać szczegółowych informacji o najszybszej trasie przy użyciu `itineraryId` w odpowiedzi.

## <a name="request-fastest-route-itinerary"></a>Żądaj najszybszych tras trasy

Usługa Azure Maps [pobierania trasy tranzytowej](/rest/api/maps/mobility/gettransititinerarypreview) umożliwia żądanie danych dla określonej trasy przy użyciu **identyfikatora** trasy tras zwróconego przez usługę [API uzyskiwania tras tranzytowych](/rest/api/maps/mobility/gettransitroutepreview) . Aby wykonać żądanie, wykonaj poniższe czynności:

1. W programie Poster kliknij pozycję **nowe żądanie**  |  **Pobierz żądanie** , a następnie nadaj mu **informacje dotyczące tranzytu** .

2. Na karcie Konstruktor wybierz metodę **Get** http. Wprowadź następujący adres URL żądania dla punktu końcowego interfejsu API i kliknij przycisk **Wyślij** .

    Ustawimy `detailType` parametr na **geometrię** , tak aby odpowiedź zawierała informacje o zatrzymaniu dla tranzytu publicznego i przełączać nawigację w ramach tej trasy.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```

3. Po pomyślnym żądaniu struktura odpowiedzi powinna wyglądać jak poniżej.  Jeśli zobaczysz odpowiedź JSON, Zauważ, że każdy składnik magistrali zawiera `legfare` element. `legfare`Element zawiera koszt (w procentach) każdej trasy magistrali, która została zakupiona osobno. Na końcu odpowiedzi zobaczysz `itineraryFare` element, który zawiera koszt (w procentach) całej trasy. W tym przykładzie istnieją cztery trasy magistrali `$2.75` . Jednak w przypadku zakupienia pojedynczego biletu dla całej trasy koszt wynosi `$5.50` .

    ```JSON
   {
        "departureTime": "2020-07-22T19:54:47Z",
        "arrivalTime": "2020-07-22T21:12:21Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2020-07-22T19:54:47Z",
                "legEndTime": "2020-07-22T19:54:47Z",
                "lineGroup": {
                    "lineGroupId": "522---666063",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "caption1": "Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "caption2": "226 Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872",
                        "mainAgencyName": "Metro Transit"
                    },
                    {
                        "stopId": "522---2062263",
                        "stopKey": "85630",
                        "stopName": "Bellevue Tc",
                        "stopCode": "85630",
                        "position": {
                            "latitude": 47.615591,
                            "longitude": -122.196491
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872",
                        "mainAgencyName": "Metro Transit"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T19:54:47Z",
                "legEndTime": "2020-07-22T20:15:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666063",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "caption1": "Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "caption2": "226 Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                       ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "pay"
                        }
                    ]
                }
            },
            ...,
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:20:00Z",
                "legEndTime": "2020-07-22T20:28:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666071",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "241",
                    "caption1": "Eastgate P&R - Bellevue Transit Center",
                    "caption2": "241 Eastgate P&R - Bellevue Transit Center",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756619",
                    "lineGroupId": "522---666071",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "241",
                    "lineDestination": "Eastgate P&R Factoria"
                },
                "stops": [
                ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                   ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "transfer"
                        }
                    ]
                }
            },
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:31:00Z",
                "legEndTime": "2020-07-22T20:54:13Z",
                "lineGroup": {
                    "lineGroupId": "522---312636",
                    "agencyId": "522---854535",
                    "agencyName": "Sound Transit",
                    "lineNumber": "550",
                    "caption1": "Bellevue - Seattle",
                    "caption2": "550 Bellevue - Seattle",
                    "color": "00008B",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---962201",
                    "lineGroupId": "522---312636",
                    "direction": "backward",
                    "agencyId": "522---854535",
                    "lineNumber": "550",
                    "lineDestination": "Seattle"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                 ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "pay"
                        }
                    ]
                }
            },
            ...,
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:57:00Z",
                "legEndTime": "2020-07-22T21:06:00Z",
                "lineGroup": {
                    "lineGroupId": "522---480518",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "13",
                    "caption1": "Seattle Pacific - Downtown Seattle",
                    "caption2": "13 Seattle Pacific - Downtown Seattle",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---1744932",
                    "lineGroupId": "522---480518",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "13",
                    "lineDestination": "Seattle Pacific University Seattle Center W"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                      ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "transfer"
                        }
                    ]
                }
            },
            ...,
        ],
        "itineraryFare": {
            "price": {
                "amount": 550,
                "currencyCode": "USD"
            },
            "tickets": [
                {
                    "amount": 275,
                    "currencyCode": "USD"
                },
                {
                    "amount": 275,
                    "currencyCode": "USD"
                }
            ]
        }
    }

    ```

## <a name="next-steps"></a>Następne kroki

Informacje na temat żądania danych w czasie rzeczywistym przy użyciu usługi mobilności:

> [!div class="nextstepaction"]
> [Jak żądać danych w czasie rzeczywistym](how-to-request-real-time-data.md)

Poznaj dokumentację interfejsu API usługi mobilności Azure Maps

> [!div class="nextstepaction"]
> [Dokumentacja usługi mobilności](/rest/api/maps/mobility)