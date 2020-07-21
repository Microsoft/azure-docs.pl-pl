---
title: Żądaj danych tranzytowych | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak żądać danych tranzytu publicznego przy użyciu usługi mobilności w Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 135da2102b77f1d6a94b4458e10fba8c4f2b8534
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517505"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Żądanie danych tranzytu publicznego przy użyciu usługi Azure Maps Mobility 

W tym artykule pokazano, jak za pomocą [usługi mobilności](https://aka.ms/AzureMapsMobilityService) Azure Maps żądania danych tranzytu publicznego. Dane tranzytowe obejmują zatrzymanie tranzytu, informacje o trasie i oszacowania czasu podróży.

W tym artykule dowiesz się, jak:

* Pobieranie identyfikatora obszaru Metro przy użyciu [interfejsu API pobierania Metro](https://aka.ms/AzureMapsMobilityMetro)
* Zażądaj zatrzymywania tranzytu w pobliżu przy użyciu usługi [pobierania w pobliżu](https://aka.ms/AzureMapsMobilityNearbyTransit) .
* Zapytanie [pobieranie tras tranzytowych interfejsu API](https://aka.ms/AzureMapsMobilityTransitRoute) w celu zaplanowania trasy przy użyciu tranzytu publicznego.
* Żądaj geometrii trasy tranzytowej i szczegółowego harmonogramu dla trasy przy użyciu [interfejsu API uzyskiwania tranzytu](https://aka.ms/https://azure.microsoft.com/services/azure-maps/)trasy.


## <a name="prerequisites"></a>Wymagania wstępne

Najpierw musisz mieć konto Azure Maps i klucz subskrypcji, aby wykonać dowolne wywołania interfejsów API Azure Maps publicznego przesyłania. Aby uzyskać więcej informacji, postępuj zgodnie z instrukcjami podanymi w temacie [Tworzenie konta](quick-demo-map-app.md#create-an-azure-maps-account) w celu utworzenia konta Azure Maps. Postępuj zgodnie z instrukcjami w temacie [Pobieranie klucza podstawowego](quick-demo-map-app.md#get-the-primary-key-for-your-account) , aby uzyskać klucz podstawowy dla Twojego konta. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](./how-to-manage-authentication.md).


W tym artykule jest wykorzystywana [aplikacja Poster](https://www.getpostman.com/apps) do kompilowania wywołań REST. Możesz użyć dowolnego preferowanego środowiska deweloperskiego interfejsu API.


## <a name="get-a-metro-area-id"></a>Pobieranie identyfikatora obszaru Metro

Aby żądać informacji o tranzycie dla określonego obszaru metropolitalnych, będziesz potrzebować tego `metroId` obszaru. [Interfejs API uzyskiwania obszaru Metro](https://aka.ms/AzureMapsMobilityMetro) umożliwia zażądanie obszarów Metro, w których dostępna jest usługa Azure Maps Mobility. Odpowiedź zawiera szczegóły, takie jak `metroId` , `metroName` i reprezentacja geometrii obszaru Metro w formacie GEOJSON.

Utwórzmy żądanie pobrania obszaru Metro dla identyfikatora obszaru Metro Seattle-Tacoma. Aby zażądać identyfikatora dla obszaru Metro, wykonaj następujące czynności:

1. Otwórz aplikację Poster i Utwórz kolekcję do przechowywania żądań. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Kolekcja**.  Nadaj kolekcji nazwę i wybierz przycisk **Utwórz** .

2. Aby utworzyć żądanie, wybierz pozycję **nowe** ponownie. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzednim kroku jako lokalizację, w której chcesz zapisać żądanie. Następnie wybierz pozycję **Zapisz**.
    
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

5. Skopiuj `metroId` , należy użyć go później.

## <a name="request-nearby-transit-stops"></a>Żądaj zatrzymywania tranzytu w pobliżu

Azure Maps [pobrania usługi tranzytu w pobliżu](https://aka.ms/AzureMapsMobilityNearbyTransit) umożliwia wyszukiwanie obiektów tranzytowych.  Interfejs API zwraca szczegóły obiektu tranzytowego, takie jak tranzyt publiczny, i udostępnione rowery wokół danej lokalizacji. Następnie wyślemy żądanie do usługi w celu wyszukania nieoczekiwanego tranzytu publicznego w ramach promienia 300-metrów wokół danej lokalizacji. W żądaniu musimy uwzględnić `metroId` pobrane wcześniej.

Aby wysłać żądanie do [procedury pobierania w pobliżu](https://aka.ms/AzureMapsMobilityNearbyTransit), wykonaj następujące czynności:

1. W programie Poster kliknij pozycję **nowe żądanie**  |  **Pobierz żądanie** i nazwij **Get Nearby stops**ją.

2. Na karcie Konstruktor wybierz metodę **Get** http, wprowadź następujący adres URL żądania dla punktu końcowego interfejsu API i kliknij przycisk **Wyślij**.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
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

[Interfejs API uzyskiwania tras tranzytowych](https://aka.ms/AzureMapsMobilityTransitRoute) Azure Maps umożliwia planowanie podróży. Zwraca najlepsze możliwe opcje trasy ze źródła do miejsca docelowego. Usługa udostępnia różne rodzaje trybów podróży, w tym idące przechodzenie, pisanie odręczne i tranzyt publiczny. Następnie przeszukamy trasę od najbliższej magistrali do obudowy "Wieża" w Seattle.

### <a name="get-location-coordinates-for-destination"></a>Pobierz współrzędne lokalizacji dla miejsca docelowego

Aby uzyskać współrzędne lokalizacji obudowy typu wieża, można użyć Azure Maps [rozmyte Search Service](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Aby wysłać żądanie do usługi wyszukiwania rozmytego, wykonaj następujące czynności:

1. W programie Poster kliknij pozycję **nowe żądanie**  |  **Pobierz żądanie** , a następnie nadaj jej nazwę **współrzędne lokalizacji**.

2.  Na karcie Konstruktor wybierz metodę **Get** http, wprowadź następujący adres URL żądania, a następnie kliknij przycisk **Wyślij**.
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. Jeśli dokładnie sprawdzasz odpowiedź, zawiera wiele lokalizacji w wynikach wyszukiwania wskazówki dotyczącej miejsca. Każdy wynik zawiera współrzędne lokalizacji pod **pozycją**. Skopiuj `lat` i `lon` pod **pozycją** pierwszego wyniku.
    
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

1. W programie Poster kliknij pozycję **nowe żądanie**  |  **Pobierz żądanie** i nadaj jej nazwę **Uzyskaj informacje o trasie**.

2. Na karcie Konstruktor wybierz metodę **Get** http, wprowadź następujący adres URL żądania dla punktu końcowego interfejsu API i kliknij przycisk **Wyślij**.

    Będziemy żądać tras tranzytu publicznego dla magistrali, określając `modeType` Parametry i `transitType` . Adres URL żądania zawiera lokalizacje pobrane w poprzednich sekcjach. Dla `originType` mamy teraz **stopid**. W przypadku `destionationType` , mamy **położeniu**.

    Zapoznaj się z [listą parametrów identyfikatora URI](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) , których można użyć w żądaniu do [interfejsu API uzyskiwania tras tranzytowych](https://aka.ms/AzureMapsMobilityTransitRoute). 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
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
                ]
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
                ]
            }
        ]
    }
    ```

4. Jeśli dokładnie zaobserwujesz, w odpowiedzi występuje wiele tras **magistrali** . Każda trasa ma unikatowy **Identyfikator trasy** i podsumowanie opisujące każdy etap trasy. Etap trasy jest częścią trasy między dwoma waypoints Stop. Następnie będziemy żądać szczegółowych informacji o najszybszej trasie przy użyciu `itineraryId` w odpowiedzi.

## <a name="request-fastest-route-itinerary"></a>Żądaj najszybszych tras trasy

Usługa Azure Maps [pobierania trasy tranzytowej](https://aka.ms/AzureMapsMobilityTransitItinerary) umożliwia żądanie danych dla określonej trasy przy użyciu **identyfikatora** trasy tras zwróconego przez usługę [API uzyskiwania tras tranzytowych](https://aka.ms/AzureMapsMobilityTransitRoute) . Aby wykonać żądanie, wykonaj poniższe czynności:

1. W programie Poster kliknij pozycję **nowe żądanie**  |  **Pobierz żądanie** , a następnie nadaj mu **informacje dotyczące tranzytu**.

2. Na karcie Konstruktor wybierz metodę **Get** http. Wprowadź następujący adres URL żądania dla punktu końcowego interfejsu API i kliknij przycisk **Wyślij**.

    Ustawimy `detailType` parametr na **geometrię** , tak aby odpowiedź zawierała informacje o zatrzymaniu dla tranzytu publicznego i przełączać nawigację w ramach tej trasy.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. Po pomyślnym żądaniu struktura odpowiedzi powinna wyglądać podobnie do poniższej:

    ```JSON
    {
        "departureTime": "2019-09-07T01:01:50Z",
        "arrivalTime": "2019-09-07T02:16:33Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:01:50Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
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
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:26:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
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
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2060604",
                        "stopKey": "71310",
                        "stopName": "NE 24th St & 160th Ave NE",
                        "stopCode": "71310",
                        "position": {
                            "latitude": 47.631565,
                            "longitude": -122.127808
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    ...,
                    ...,
                    {
                        "stopId": "522---2061704",
                        "stopKey": "74451",
                        "stopName": "Northup Way & NE 33rd Pl",
                        "stopCode": "74451",
                        "position": {
                            "latitude": 47.640911,
                            "longitude": -122.194443
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.12527,
                            47.63143
                        ],
                        [
                            -122.12529,
                            47.63143
                        ],
                        [
                            -122.12561,
                            47.63144
                        ],
                        [
                            -122.12701,
                            47.63148
                        ],
                        ...,
                        ...,
                        ...,
                        [
                            -122.19601,
                            47.64304
                        ],
                        [
                            -122.19584,
                            47.64315
                        ],
                        [
                            -122.19677,
                            47.6438
                        ]
                    ]
                }
            },
            ...,
            ...,
            ...,
            {
                "legType": "Walk",
                "legStartTime": "2019-09-07T02:12:42Z",
                "legEndTime": "2019-09-07T02:16:33Z",
                "steps": [
                    {
                        "direction": {
                            "relativeDirection": "depart"
                        },
                        "streetName": "Denny Way"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "4th Avenue North"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "Broad Street"
                    }
                ],
                "origin": {
                    "position": {
                        "latitude": 47.618578,
                        "longitude": -122.348058
                    }
                },
                "destination": {
                    "position": {
                        "latitude": 47.62039,
                        "longitude": -122.34928
                    }
                },
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.34806,
                            47.61857
                        ],
                        [
                            -122.3481,
                            47.61857
                        ],
                        [
                            -122.34894,
                            47.61858
                        ],
                        [
                            -122.34892,
                            47.61964
                        ],
                        [
                            -122.34877,
                            47.61975
                        ],
                        [
                            -122.3492,
                            47.62001
                        ],
                        [
                            -122.34918,
                            47.62003
                        ],
                        [
                            -122.34917,
                            47.62006
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34928,
                            47.62039
                        ]
                    ]
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Następne kroki

Informacje na temat żądania danych w czasie rzeczywistym przy użyciu usługi mobilności:

> [!div class="nextstepaction"]
> [Jak żądać danych w czasie rzeczywistym](how-to-request-real-time-data.md)

Poznaj dokumentację interfejsu API usługi mobilności Azure Maps

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usługi mobilności](https://aka.ms/AzureMapsMobilityService)

