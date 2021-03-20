---
title: Najlepsze rozwiązania dotyczące Azure Maps Search Service | Mapy Microsoft Azure
description: Dowiedz się, jak stosować najlepsze rozwiązania w przypadku korzystania z Search Service z map Microsoft Azure.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 11c1938c3c1ccba533f52336fad81ebeaae53b24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895481"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Najlepsze rozwiązania dotyczące Azure Maps Search Service

Azure Maps [Search Service](/rest/api/maps/search) obejmuje interfejsy API oferujące różne funkcje, które ułatwiają deweloperom wyszukiwanie adresów, miejsc, aukcji w biznesie według nazw lub kategorii oraz innych informacji geograficznych. Na przykład[interfejs API wyszukiwania rozmytego](/rest/api/maps/search/getsearchfuzzy) umożliwia użytkownikom wyszukiwanie adresów lub punktów orientacyjnych (punkt POI).

W tym artykule wyjaśniono, jak stosować metody dźwiękowe podczas wywoływania danych z Azure Maps Search Service. Omawiane tematy:
> [!div class="checklist"]
> * Twórz zapytania, aby zwracać odpowiednie dopasowania
> * Ogranicz wyniki wyszukiwania
> * Poznaj różnice między typami wyników
> * Przeczytaj strukturę wyszukiwania adresów — odpowiedź

## <a name="prerequisites"></a>Wymagania wstępne

1. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji.

W tym artykule jest wykorzystywana [aplikacja Poster](https://www.postman.com/downloads/) do kompilowania wywołań REST, ale można wybrać dowolne środowisko deweloperskie interfejsu API.

## <a name="best-practices-to-geocode-addresses"></a>Najlepsze rozwiązania dotyczące adresów geokodowych

W przypadku wyszukiwania pełnych lub częściowych adresów przy użyciu Azure Maps Search Service interfejs API odczytuje słowa kluczowe z zapytania wyszukiwania. Następnie zwraca współrzędne długości i szerokości geograficznej adresu. Ten proces jest nazywany *geokodowaniem*.

Możliwość geokodu w kraju/regionie zależy od dostępności danych drogowych i dokładności usługi geokodowania. Aby uzyskać więcej informacji na temat możliwości geokodowania Azure Maps według kraju lub regionu, zobacz [pokrycie geokodowania](./geocoding-coverage.md).

### <a name="limit-search-results"></a>Ogranicz wyniki wyszukiwania

 Interfejs API wyszukiwania Azure Maps może pomóc w odpowiednim ograniczeniu wyników wyszukiwania. Możesz ograniczyć wyniki, aby umożliwić użytkownikom wyświetlanie odpowiednich danych.

> [!NOTE]
> Interfejsy API wyszukiwania obsługują więcej parametrów niż te, które omówiono w tym artykule.

#### <a name="geobiased-search-results"></a>Wyniki wyszukiwania geoobciążonego

Aby uzyskać efekt geobias do odpowiedniego obszaru dla użytkownika, należy zawsze dodać tyle szczegółów lokalizacji jak to możliwe. Możesz chcieć ograniczyć wyniki wyszukiwania, określając typy danych wejściowych:

* Ustaw `countrySet` parametr. Można to zrobić na `US,FR` przykład. Domyślnie interfejs API przeszukuje cały świat, dzięki czemu może zwracać niepotrzebne wyniki. Jeśli zapytanie nie ma `countrySet` parametru, wyszukiwanie może zwracać niedokładne wyniki. Na przykład wyszukiwanie miasta o nazwie *Bellevue* zwraca wyniki ze Stanów Zjednoczonych i Francji, ponieważ oba kraje/regiony zawierają miasto o nazwie *Bellevue*.

* Możesz użyć `btmRight` parametrów i, `topleft` Aby ustawić pole ograniczenia. Te parametry ograniczają wyszukiwanie do określonego obszaru na mapie.

* Aby mieć wpływ na obszar istotności dla wyników, zdefiniuj `lat` Parametry i `lon` współrzędne. Użyj `radius` parametru, aby ustawić promień obszaru wyszukiwania.


#### <a name="fuzzy-search-parameters"></a>Parametry wyszukiwania rozmytego

Zalecamy korzystanie z interfejsu API "Azure Maps [przeszukiwania rozmytego](/rest/api/maps/search/getsearchfuzzy) ", gdy nie są znane dane wejściowe użytkownika dotyczące zapytania wyszukiwania. Na przykład dane wejściowe od użytkownika mogą być adresami lub typu punktu orientacyjnego (punkt POI), np. *zakupów* świątecznych. Interfejs API łączy punkt POI wyszukiwanie i geokodowanie do kanonicznego *wyszukiwania jednowierszowego*: 

* `minFuzzyLevel`Parametry i `maxFuzzyLevel` pomagają zwracać odpowiednie dopasowania nawet wtedy, gdy parametry zapytania nie są dokładnie zgodne z informacjami, które użytkownik chce. Aby zmaksymalizować wydajność i zmniejszyć nietypowe wyniki, należy ustawić zapytania wyszukiwania na wartości domyślne `minFuzzyLevel=1` i `maxFuzzyLevel=2` . 

    Na przykład, gdy `maxFuzzyLevel` parametr ma wartość 2, termin wyszukiwania *restrant* jest dopasowywany do *restauracji*. Można zastąpić domyślne poziomy rozmyte, gdy zachodzi taka potrzeba. 

* Użyj `idxSet` parametru, aby określić priorytet dokładnego zestawu typów wyników. Aby określić priorytet dokładnego zestawu wyników, można przesłać listę indeksów rozdzielonych przecinkami. Na liście nie ma znaczenia kolejność elementów. Azure Maps obsługuje następujące indeksy:

* `Addr` - **Zakresy adresów**: punkty adresów, które są interpolowane od początku i końca ulicy. Te punkty są reprezentowane jako zakresy adresów.
* `Geo` - **Lokalizacje geograficzne**: działy administracyjne gruntów. Obszar geografii może być na przykład krajem/regionem, stanem lub miastom.
* `PAD` - **Adresy punktów**: adresy, które zawierają nazwę ulicy i numer. Adresy punktów można znaleźć w indeksie. Przykładem jest *Soquel Dr 2501*. Adres punktu zapewnia najwyższy poziom dokładności dostępny dla adresów.  
* `POI` - **Punkty orientacyjne**: punkty na mapie, które są uważane za będące uwagami lub które mogą być interesujące. [Interfejs API adresu wyszukiwania](/rest/api/maps/search/getsearchaddress) nie zwraca punktów POI.  
* `Str` - **Streets**: Streets na mapie.
* `XStr` - **Cross Streets lub przedziały**: połączenia lub miejsca, w których dwa ulice przecinają się.


#### <a name="usage-examples"></a>Przykłady użycia

* `idxSet=POI` — Tylko Wyszukiwanie punktów POI. 

* `idxSet=PAD,Addr` — Tylko adresy wyszukiwania. `PAD` wskazuje adres punktu i `Addr` wskazuje zakres adresów.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Odwróć kod i filtr dla typu jednostki geografii

W przypadku przeszukiwania odwrotnego kodu w [interfejsie API odwrotnego adresu wyszukiwania](/rest/api/maps/search/getsearchaddressreverse)usługa może zwracać wielokąty dla obszarów administracyjnych. Na przykład możesz chcieć pobrać Wielokąt obszaru dla miasta. Aby zawęzić wyszukiwanie do określonych typów jednostek geograficznych, Uwzględnij `entityType` parametr w żądaniach. 

Odpowiedź będąca wynikiem zawiera identyfikator geografii i typ jednostki, który został dopasowany. Jeśli podano więcej niż jedną jednostkę, punkt końcowy zwraca *najmniejszą dostępną jednostkę*. Możesz użyć zwróconego identyfikatora geometrii, aby uzyskać geometrię geografii za pomocą [usługi wyszukiwania wielokątów](/rest/api/maps/search/getsearchpolygon).

#### <a name="sample-request"></a>Przykładowe żądanie

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>Reakcja

```JSON
{
    "summary": {
        "queryTime": 14,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="set-the-results-language"></a>Ustawianie języka wyników

Użyj `language` parametru, aby ustawić język dla zwracanych wyników wyszukiwania. Jeśli żądanie nie ustawi języka, domyślnie Search Service używa najczęściej używanego języka w kraju lub regionie. Gdy żadne dane nie są dostępne w określonym języku, używany jest język domyślny. 

Aby uzyskać więcej informacji, zobacz [Azure Maps obsługiwanych języków](./supported-languages.md).


### <a name="use-predictive-mode-automatic-suggestions"></a>Użyj trybu predykcyjnego (sugestie automatyczne)

Aby znaleźć więcej dopasowań dla zapytań częściowych, ustaw `typeahead` parametr na `true` . To zapytanie jest interpretowane jako częściowe dane wejściowe, a wyszukiwanie przechodzi do trybu predykcyjnego. Jeśli parametr nie zostanie ustawiony `typeahead` na `true` , usługa przyjmie, że wszystkie odpowiednie informacje zostały przekazano.

W poniższym przykładowym zapytaniu usługa adresów wyszukiwania jest wysyłana do *Microso*. W tym miejscu `typeahead` parametr ustawiony na `true` . Odpowiedź pokazuje, że usługa wyszukiwania interpretuje zapytanie jako częściowe zapytanie. Odpowiedź zawiera wyniki dla automatycznie sugerowanych zapytań.

#### <a name="sample-query"></a>Przykładowe zapytanie

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>Reakcja

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="encode-a-uri-to-handle-special-characters"></a>Kodowanie identyfikatora URI w celu obsługi znaków specjalnych 

Aby znaleźć adresy krzyżowe, należy zakodować identyfikator URI w celu obsługi znaków specjalnych w adresie. Rozważmy następujący adres przykład: *& 1*. W tym miejscu Zakoduj znak handlowego "i" ( `&` ) przed wysłaniem żądania. 

Zalecamy zakodowanie danych znakowych identyfikatorem URI. W identyfikatorze URI wszystkie znaki są kodowane przy użyciu znaku procentu ( `%` ) i dwubajtowej wartości szesnastkowej, która odpowiada kodowi kodu UTF-8.

#### <a name="usage-examples"></a>Przykłady użycia

Zacznij od tego adresu:

```
query=1st Avenue & E 111th St, New York
```

Koduj adres:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

Można użyć następujących metod.

JavaScript lub TypeScript:
```javascript
encodeURIComponent(query)
```

C# lub Visual Basic:
```csharp
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++:
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

Obsługa
```PHP
urlencode(query)
```

Dopisek
```Ruby
CGI::escape(query) 
```

Adres
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Udać
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>Najlepsze rozwiązania dotyczące wyszukiwania punkt POI

W wyszukiwaniu punkt POI można zażądać punkt POI wyników według nazwy. Można na przykład wyszukać firmę według nazwy. 

Zdecydowanie zalecamy użycie `countrySet` parametru, aby określić kraje/regiony, w których aplikacja wymaga pokrycia. Domyślnym zachowaniem jest przeszukanie całego świata. To szerokie wyszukiwanie może zwracać niepotrzebne wyniki i wyszukiwanie może zająć dużo czasu.

### <a name="brand-search"></a>Wyszukiwanie marki

Aby poprawić znaczenie wyników i informacji w odpowiedzi, odpowiedź wyszukiwania punkt POI zawiera informacje o marce. Możesz użyć tych informacji, aby przeanalizować odpowiedź.

W żądaniu można przesłać listę nazw marki rozdzieloną przecinkami. Użyj listy, aby ograniczyć wyniki do określonych marek przez ustawienie `brandSet` parametru. Na liście zamówienie elementu nie ma znaczenia. Gdy podajesz wiele list marek, zwracane wyniki muszą należeć do co najmniej jednej z list.

Aby poznać wyszukiwanie markowe, przyjrzyjmy się żądaniu [wyszukiwania kategorii punkt POI](/rest/api/maps/search/getsearchpoicategory) . W poniższym przykładzie szukamy stacji gazowych blisko kampusu firmy Microsoft w Redmond w stanie Waszyngton. Odpowiedź zawiera informacje o marce dla każdej zwróconej punkt POI.

#### <a name="sample-query"></a>Przykładowe zapytanie

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>Reakcja

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Wyszukiwanie portów lotniczych

Za pomocą interfejsu API wyszukiwania punkt POI można wyszukiwać porty lotnicze przy użyciu ich urzędowego kodu. Na przykład można użyć *Sea* , aby znaleźć Seattle-Tacoma Międzynarodowy Port lotniczy: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Wyszukiwanie w pobliżu

Aby pobrać wyniki punkt POI wokół określonej lokalizacji, możesz spróbować użyć [interfejsu API wyszukiwania w pobliżu](/rest/api/maps/search/getsearchnearby). Punkt końcowy zwraca tylko wyniki punkt POI. Nie przyjmuje parametru zapytania wyszukiwania. 

Aby ograniczyć wyniki, zalecamy ustawienie promienia.

## <a name="understanding-the-responses"></a>Zrozumienie odpowiedzi

Znajdź adres w Seattle, wysyłając żądanie Search Address do Search Service Azure Maps. W poniższym adresie URL żądania ustawiamy parametr na, aby `countrySet` `US` wyszukać adres w Stanach Zjednoczonych.

### <a name="sample-query"></a>Przykładowe zapytanie

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Obsługiwane typy wyników

* **Adres punktu**: wskazuje na mapę, która ma określony adres z nazwą ulicy i numerem. Adres punktu zapewnia najwyższy poziom dokładności dla adresów. 

* **Zakres adresów**: zakres punktów adresowych, które są interpolowane od początku i końca ulicy.  

* **Lokalizacja geograficzna**: obszary na mapie, które reprezentują działy administracyjne gruntów, na przykład kraj/region, stan lub miasto. 

* **Punkt POI**: wskazuje na mapę, które są cenną uwagę i mogą być interesujące.

* **Ulica**: ulice na mapie. Adresy są rozpoznawane jako współrzędne szerokości geograficznej ulicy, która zawiera adres. Numer domu może nie być przetwarzany. 

* **Skrzyżowanie**: przedziały. Cross Streets reprezentuje połączenia, w których dwa ulice przecinają się.

### <a name="response"></a>Reakcja

Przyjrzyjmy się strukturze odpowiedzi. W poniższej odpowiedzi typy obiektów wynikowych są różne. Jeśli szukasz uważnie, zobaczysz trzy typy obiektów wynikowych:

* Adres punktu
* Ulica
* Cross-ulica

Zwróć uwagę, że wyszukiwanie adresów nie zwraca punktów POI.  

`Score`Parametr dla każdego obiektu odpowiedzi wskazuje, w jaki sposób pasujący wynik odnosi się do wyników innych obiektów w tej samej odpowiedzi. Aby uzyskać więcej informacji na temat parametrów obiektu odpowiedzi, zobacz [Get Search Address](/rest/api/maps/search/getsearchaddress).

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometria

Typ odpowiedzi *geometrycznej* może zawierać identyfikator geometrii, który jest zwracany w `dataSources` obiekcie w `geometry` i `id` . Na przykład można użyć [usługi wyszukiwania Wielokąt](/rest/api/maps/search/getsearchpolygon) , aby zażądać danych geometrycznych w formacie GEOJSON. Korzystając z tego formatu, można uzyskać szkic miasta lub lotniska dla zestawu jednostek. Następnie możesz użyć tych danych granicznych, aby [skonfigurować geoogrodzenie](./tutorial-geofence.md) lub [przeszukać punktów POI wewnątrz geometrii](/rest/api/maps/search/postsearchinsidegeometry).


Odpowiedzi dla interfejsu API [adresu wyszukiwania](/rest/api/maps/search/getsearchaddress) lub interfejsu API [wyszukiwania rozmytego](/rest/api/maps/search/getsearchfuzzy) umożliwiają uwzględnienie identyfikatora geometrii, który jest zwracany w `dataSources` obiekcie w `geometry` i `id` :


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz:

> [!div class="nextstepaction"]
> [Jak kompilować żądania Search Service Azure Maps](./how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API Search Service](/rest/api/maps/search)