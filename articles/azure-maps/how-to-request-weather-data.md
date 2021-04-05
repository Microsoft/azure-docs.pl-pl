---
title: Żądaj danych pogodowych w czasie rzeczywistym i prognozowanie przy użyciu Azure Maps usług pogodowych (wersja zapoznawcza)
description: Dowiedz się, jak prosić o dane pogodowe w czasie rzeczywistym (bieżące) i prognozowane (co godzinę, codziennie), korzystając z Microsoft Azure Maps usług Pogoda (wersja zapoznawcza)
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: fe1b337fe3e1dcf499f9a7428f66543108d0c050
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680416"
---
# <a name="request-real-time-and-forecasted-weather-data-using-azure-maps-weather-services-preview"></a>Żądaj danych pogodowych w czasie rzeczywistym i prognozowanie przy użyciu Azure Maps usług pogodowych (wersja zapoznawcza) 

> [!IMPORTANT]
> Usługi Azure Maps Pogoda są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Maps [usługi pogodowe](/rest/api/maps/weather) to zestaw interfejsów API RESTful, dzięki którym deweloperzy mogą integrować wysoce dynamiczne historyczne dane pogodowe, w czasie rzeczywistym oraz wizualizacje do swoich rozwiązań. W tym artykule przedstawiono sposób żądania danych pogodowych w czasie rzeczywistym i prognozowanych.

W tym artykule dowiesz się, jak:

* Żądaj danych pogody (Current) w czasie rzeczywistym za pomocą [interfejsu API uzyskiwania bieżących warunków](/rest/api/maps/weather/getcurrentconditionspreview).
* Żądaj poważnych alertów pogodowych przy użyciu [interfejsu API uzyskiwania poważnych alertów pogodowych](/rest/api/maps/weather/getsevereweatheralertspreview).
* Żądaj prognoz dziennych za pomocą [interfejsu API pobierania dziennej prognozy](/rest/api/maps/weather/getdailyforecastpreview).
* Żądaj prognoz godzinowych przy użyciu [interfejsu API uzyskiwania godzinnych prognoz](/rest/api/maps/weather/gethourlyforecastpreview).
* Zażądaj prognoz minutowo według minut przy użyciu [interfejsu API prognoz Get minut](/rest/api/maps/weather/getminuteforecastpreview).

Ten film wideo zawiera przykłady tworzenia wywołań REST w celu Azure Maps usług pogodowych.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps-Weather-services-for-developers/player?format=ny]

## <a name="prerequisites"></a>Wymagania wstępne

1. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](./how-to-manage-authentication.md).

    >[!IMPORTANT]
    >[Interfejs API prognozy Get minut](/rest/api/maps/weather/getminuteforecastpreview) wymaga klucza warstwy cenowej S1. Wszystkie inne interfejsy API wymagają klucza warstwy cenowej S0.

W tym samouczku jest stosowana aplikacja programu [Poster](https://www.postman.com/) , ale można wybrać inne środowisko deweloperskie interfejsu API.

## <a name="request-real-time-weather-data"></a>Żądaj danych pogodowych w czasie rzeczywistym

[Interfejs API uzyskiwania bieżących warunków](/rest/api/maps/weather/getcurrentconditionspreview) zwraca szczegółowe warunki pogodowe, takie jak opadowanie, temperatura i wiatr dla danej lokalizacji współrzędnych. Ponadto można pobrać obserwacje z ostatnich 6 lub 24 godzin dla określonej lokalizacji. Odpowiedź zawiera szczegółowe informacje, takie jak Data i godzina obserwacji, Krótki opis warunków pogodowych, ikona Pogoda, flagi wskaźnika opadów oraz temperatura. Zwracany jest również indeks RealFeel™ temperatury i ultrafioletu (UV).

W tym przykładzie użyjesz [interfejsu API Get Current Conditions](/rest/api/maps/weather/getcurrentconditionspreview) , aby pobrać bieżące warunki pogodowe we współrzędnych znajdujących się w Seattle, WA.

1. Otwórz aplikację Poster. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Kolekcja**.  Nadaj kolekcji nazwę i wybierz przycisk **Utwórz** . Ta kolekcja zostanie użyta w pozostałej części przykładów tego dokumentu.

2. Aby utworzyć żądanie, wybierz pozycję **nowe** ponownie. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzednim kroku, a następnie wybierz pozycję **Zapisz**.

3. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL. W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym.

    ```http
    https://atlas.microsoft.com/weather/currentConditions/json?api-version=1.0&query=47.60357,-122.32945&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Kliknij przycisk **Wyślij** niebieską. Treść odpowiedzi zawiera bieżące informacje o pogodzie.

    ```json
    {
    "results": [
        {
            "dateTime": "2020-10-19T20:39:00+00:00",
            "phrase": "Cloudy",
            "iconCode": 7,
            "hasPrecipitation": false,
            "isDayTime": true,
            "temperature": {
                "value": 12.4,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperatureShade": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "relativeHumidity": 87,
            "dewPoint": {
                "value": 10.3,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 23.0,
                    "localizedDescription": "NNE"
                },
                "speed": {
                    "value": 4.5,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 9.0,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "obstructionsToVisibility": "",
            "cloudCover": 100,
            "ceiling": {
                "value": 1494.0,
                "unit": "m",
                "unitType": 5
            },
            "pressure": {
                "value": 1021.2,
                "unit": "mb",
                "unitType": 14
            },
            "pressureTendency": {
                "localizedDescription": "Steady",
                "code": "S"
            },
            "past24HourTemperatureDeparture": {
                "value": -2.1,
                "unit": "C",
                "unitType": 17
            },
            "apparentTemperature": {
                "value": 15.0,
                "unit": "C",
                "unitType": 17
            },
            "windChillTemperature": {
                "value": 12.2,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 11.3,
                "unit": "C",
                "unitType": 17
            },
            "precipitationSummary": {
                "pastHour": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past3Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past6Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past9Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past12Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past18Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past24Hours": {
                    "value": 0.4,
                    "unit": "mm",
                    "unitType": 3
                }
            },
            "temperatureSummary": {
                "past6Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past12Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past24Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 15.6,
                        "unit": "C",
                        "unitType": 17
                    }
                }
            }
        }
    ]
    }
    ```

## <a name="request-severe-weather-alerts"></a>Żądaj poważnych alertów pogodowych

[Interfejs API uzyskiwania silnych alertów pogodowych](/rest/api/maps/weather/getsevereweatheralertspreview) zwraca alerty o silnych pogodach, które są dostępne na całym świecie od oficjalnych agencji meteorologicznych instytucji rządowych i wiodących globalnych dla regionalnych dostawców alertów pogody. Azure Maps Usługa może zwrócić szczegóły, takie jak typ alertu, Kategoria, poziom i szczegółowe opisy aktywnych poważnych alertów dla żądanych lokalizacji, takich jak huragany), thunderstorms, pioruny, fale termiczne lub pożary lasu. Na przykład menedżerowie logistyczni mogą wizualizować poważne warunki pogodowe na mapie, wraz z lokalizacjami biznesowymi i planowanymi trasami, a także koordynować sterowniki i lokalnych procesów roboczych.

W tym przykładzie użyjesz [interfejsu API alertów o silnych pogodach](/rest/api/maps/weather/getsevereweatheralertspreview) , aby pobrać bieżące warunki pogodowe we współrzędnych, które znajdują się w Cheyenne, wy.

>[!NOTE]
>Ten przykład pobiera poważne alerty pogodowe w momencie pisania. Prawdopodobnie nie ma już żadnych poważnych alertów pogodowych w wybranej lokalizacji. Aby pobrać rzeczywiste poważne dane alertu podczas uruchamiania tego przykładu, należy pobrać dane z innej lokalizacji współrzędnych.

1. Otwórz aplikację Poster, kliknij pozycję **New (nowy**), a następnie wybierz pozycję **żądanie**. Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzedniej sekcji lub utworzoną nową, a następnie wybierz pozycję **Zapisz**.

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL. W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym.

    ```http
    https://atlas.microsoft.com/weather/severe/alerts/json?api-version=1.0&query=41.161079,-104.805450&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Kliknij przycisk **Wyślij** niebieską. Jeśli nie ma żadnych poważnych alertów pogodowych, treść odpowiedzi będzie zawierać pustą `results[]` tablicę. W przypadku poważnych alertów pogodowych treść odpowiedzi zawiera coś takiego jak następująca odpowiedź JSON:

    ```json
    {
    "results": [
        {
            "countryCode": "US",
            "alertId": 2194734,
            "description": {
                "localized": "Red Flag Warning",
                "english": "Red Flag Warning"
            },
            "category": "FIRE",
            "priority": 54,
            "source": "U.S. National Weather Service",
            "sourceId": 2,
            "alertAreas": [
                {
                    "name": "Platte/Goshen/Central and Eastern Laramie",
                    "summary": "Red Flag Warning in effect until 7:00 PM MDT.  Source: U.S. National Weather Service",
                    "startTime": "2020-10-05T15:00:00+00:00",
                    "endTime": "2020-10-06T01:00:00+00:00",
                    "latestStatus": {
                        "localized": "Continue",
                        "english": "Continue"
                    },
                    "alertDetails": "...RED FLAG WARNING REMAINS IN EFFECT FROM 9 AM THIS MORNING TO\n7 PM MDT THIS EVENING FOR STRONG GUSTY WINDS AND LOW HUMIDITY...\n\n* WHERE...Fire weather zones 303, 304, 305, 306, 307, 308, 309,\n  and 310 in southeast Wyoming. Fire weather zone 313 in Nebraska.\n\n* WIND...West to northwest 15 to 30 MPH with gusts around 40 MPH.\n\n* HUMIDITY...10 to 15 percent.\n\n* IMPACTS...Any fires that develop will likely spread rapidly.\n  Outdoor burning is not recommended.\n\nPRECAUTIONARY/PREPAREDNESS ACTIONS...\n\nA Red Flag Warning means that critical fire weather conditions\nare either occurring now...or will shortly. A combination of\nstrong winds...low relative humidity...and warm temperatures can\ncontribute to extreme fire behavior.\n\n&&",
                    "alertDetailsLanguageCode": "en"
                }
            ]
            },...
        ]
    }
    ```

## <a name="request-daily-weather-forecast-data"></a>Żądaj codziennych danych prognozy pogody

[Interfejs API uzyskiwania dziennej prognozy](/rest/api/maps/weather/getdailyforecastpreview) zwraca szczegółowe dzienne prognozy pogody, takie jak temperatura i wiatr. Żądanie może określić liczbę dni do zwrócenia: 1, 5, 10, 15, 25 lub 45 dni dla danej lokalizacji współrzędnych. Odpowiedź zawiera szczegółowe informacje, takie jak temperatura, wiatr, opady, jakość powietrza i indeks UV.  W tym przykładzie poprosimy o podanie pięciu dni przez ustawienie `duration=5` .

>[!IMPORTANT]
>W warstwie cenowej S0 możesz zażądać codziennej prognozy dla następnych 1, 5, 10 i 15 dni. W warstwie cenowej S1 można także zażądać codziennej prognozy w ciągu następnych 25 dni i 45 dni.

W tym przykładzie użyjesz [interfejsu API uzyskiwania dziennej prognozy](/rest/api/maps/weather/getdailyforecastpreview) , aby pobrać 5-dniową prognozę pogody dla współrzędnych znajdujących się w Seattle, WA.

1. Otwórz aplikację Poster, kliknij pozycję **New (nowy**), a następnie wybierz pozycję **żądanie**. Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzedniej sekcji lub utworzoną nową, a następnie wybierz pozycję **Zapisz**.

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL. W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym.

    ```http
    https://atlas.microsoft.com/weather/forecast/daily/json?api-version=1.0&query=47.60357,-122.32945&duration=5&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Kliknij przycisk **Wyślij** niebieską. Treść odpowiedzi zawiera dane prognozy pogody na pięć dni. W odpowiedzi na zwięzłości poniżej przedstawiono prognozę dla pierwszego dnia.
    ```json
    {
    "summary": {
        "startDate": "2020-10-18T17:00:00+00:00",
        "endDate": "2020-10-19T23:00:00+00:00",
        "severity": 2,
        "phrase": "Snow, mixed with rain at times continuing through Monday evening and a storm total of 3-6 cm",
        "category": "snow/rain"
    },
    "forecasts": [
        {
            "date": "2020-10-19T04:00:00+00:00",
            "temperature": {
                "minimum": {
                    "value": -1.1,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 1.3,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperature": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.5,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperatureShade": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.7,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "hoursOfSun": 1.8,
            "degreeDaySummary": {
                "heating": {
                    "value": 18.0,
                    "unit": "C",
                    "unitType": 17
                },
                "cooling": {
                    "value": 0.0,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "airAndPollen": [
                {
                    "name": "AirQuality",
                    "value": 23,
                    "category": "Good",
                    "categoryValue": 1,
                    "type": "Ozone"
                },
                {
                    "name": "Grass",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Mold",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Ragweed",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Tree",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "UVIndex",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                }
            ],
            "day": {
                "iconCode": 22,
                "iconPhrase": "Snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Chilly with snow, 2-4 cm",
                "longPhrase": "Chilly with snow, accumulating an additional 2-4 cm",
                "precipitationProbability": 90,
                "thunderstormProbability": 0,
                "rainProbability": 54,
                "snowProbability": 85,
                "iceProbability": 8,
                "wind": {
                    "direction": {
                        "degrees": 36.0,
                        "localizedDescription": "NE"
                    },
                    "speed": {
                        "value": 9.3,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 70.0,
                        "localizedDescription": "ENE"
                    },
                    "speed": {
                        "value": 25.9,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 0.5,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 2.72,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 9.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 9.0,
                "hoursOfIce": 0.0,
                "cloudCover": 96
            },
            "night": {
                "iconCode": 29,
                "iconPhrase": "Rain and snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Showers of rain and snow",
                "longPhrase": "A couple of showers of rain or snow this evening; otherwise, cloudy; storm total snowfall 1-3 cm",
                "precipitationProbability": 65,
                "thunderstormProbability": 0,
                "rainProbability": 60,
                "snowProbability": 54,
                "iceProbability": 4,
                "wind": {
                    "direction": {
                        "degrees": 16.0,
                        "localizedDescription": "NNE"
                    },
                    "speed": {
                        "value": 16.7,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 1.0,
                        "localizedDescription": "N"
                    },
                    "speed": {
                        "value": 35.2,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 3.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 0.79,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 4.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 3.0,
                "hoursOfIce": 0.0,
                "cloudCover": 94
            },
            "sources": [
                "AccuWeather"
            ]
        },...
    ]
    }
    ```

## <a name="request-hourly-weather-forecast-data"></a>Żądaj danych prognozy pogody godzinowo

[Interfejs API prognozy Get-hourd](/rest/api/maps/weather/gethourlyforecastpreview) zwraca szczegółową prognozę pogody o godzinę dla następnego 1, 12, 24 (1 dzień), 72 (3 dni), 120 (5 dni) i 240 godz. (10 dni) dla danej lokalizacji współrzędnych. Interfejs API zwraca szczegóły, takie jak temperatura, wilgotność, wiatr, opady i indeks UV.

>[!IMPORTANT]
>W warstwie cenowej S0 możesz zażądać prognozy co godzinę w ciągu następnych 1, 12, 24 godzin (1 dzień) i 72 godzin (3 dni). W warstwie cenowej S1 można także żądać prognozy co godzinę dla następnego 120 (5 dni) i 240 godzin (10 dni).

W tym przykładzie użyjesz [interfejsu API prognozowania godzinowego](/rest/api/maps/weather/gethourlyforecastpreview) , aby pobrać prognozę pogody godzinowego w ciągu następnych 12 godzin przy współrzędnych znajdujących się w Seattle, WA.

1. Otwórz aplikację Poster, kliknij pozycję **New (nowy**), a następnie wybierz pozycję **żądanie**. Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzedniej sekcji lub utworzoną nową, a następnie wybierz pozycję **Zapisz**.

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL. W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym.

    ```http
    https://atlas.microsoft.com/weather/forecast/hourly/json?api-version=1.0&query=47.60357,-122.32945&duration=12&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Kliknij przycisk **Wyślij** niebieską. Treść odpowiedzi zawiera dane prognozy pogody przez następne 12 godzin. W odpowiedzi na zwięzłości poniżej przedstawiono prognozę dla pierwszej godziny.

    ```json
    {
    "forecasts": [
        {
            "date": "2020-10-19T21:00:00+00:00",
            "iconCode": 12,
            "iconPhrase": "Showers",
            "hasPrecipitation": true,
            "precipitationType": "Rain",
            "precipitationIntensity": "Light",
            "isDaylight": true,
            "temperature": {
                "value": 14.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.3,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 12.0,
                "unit": "C",
                "unitType": 17
            },
            "dewPoint": {
                "value": 9.5,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 242.0,
                    "localizedDescription": "WSW"
                },
                "speed": {
                    "value": 9.3,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 14.8,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "relativeHumidity": 71,
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "cloudCover": 100,
            "ceiling": {
                "value": 1128.0,
                "unit": "m",
                "unitType": 5
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "precipitationProbability": 51,
            "rainProbability": 51,
            "snowProbability": 0,
            "iceProbability": 0,
            "totalLiquid": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "rain": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "snow": {
                "value": 0.0,
                "unit": "cm",
                "unitType": 4
            },
            "ice": {
                "value": 0.0,
                "unit": "mm",
                "unitType": 3
            }
        }...
    ]
    }
    ```
## <a name="request-minute-by-minute-weather-forecast-data"></a>Żądaj danych prognozy pogody o minucie minutowej

 [Interfejs API prognozy Get minut](/rest/api/maps/weather/getminuteforecastpreview) zwraca prognozy co minutę dla danej lokalizacji przez następne 120 minut. Użytkownicy mogą żądać prognoz pogody w odstępach od 1, 5 do 15 minut. Odpowiedź zawiera szczegóły, takie jak Typ opadu (w tym deszcz, śnieg lub kombinacja obu tych elementów), czas rozpoczęcia i wartość intensywności opadów (dBZ).

W tym przykładzie użyjesz [interfejsu API prognozowania minut](/rest/api/maps/weather/getminuteforecastpreview) , aby pobrać prognozę pogody na minutę w czasie, w którym znajdują się w Seattle, WA. Prognoza pogody jest podawana w ciągu następnych 120 minut. Nasza kwerenda żąda, aby Prognoza była podawana w odstępach 15-minutowych, ale można dostosować wartość parametru do 1 lub 5 minut.

1. Otwórz aplikację Poster, kliknij pozycję **New (nowy**), a następnie wybierz pozycję **żądanie**. Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzedniej sekcji lub utworzoną nową, a następnie wybierz pozycję **Zapisz**.

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL. W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym.

    ```http
    https://atlas.microsoft.com/weather/forecast/minute/json?api-version=1.0&query=47.60357,-122.32945&interval=15&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Kliknij przycisk **Wyślij** niebieską. Treść odpowiedzi zawiera dane prognozy pogody dla następnych 120 minut, w odstępach 15-minutowych.

    ```json
    {
    "summary": {
        "briefPhrase60": "No precipitation for at least 60 min",
        "shortPhrase": "No precip for 120 min",
        "briefPhrase": "No precipitation for at least 120 min",
        "longPhrase": "No precipitation for at least 120 min",
        "iconCode": 7
    },
    "intervalSummaries": [
        {
            "startMinute": 0,
            "endMinute": 119,
            "totalMinutes": 120,
            "shortPhrase": "No precip for %MINUTE_VALUE min",
            "briefPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "longPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "iconCode": 7
        }
    ],
    "intervals": [
        {
            "startTime": "2020-10-19T20:51:00+00:00",
            "minute": 0,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:06:00+00:00",
            "minute": 15,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:21:00+00:00",
            "minute": 30,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:36:00+00:00",
            "minute": 45,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:51:00+00:00",
            "minute": 60,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:06:00+00:00",
            "minute": 75,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:21:00+00:00",
            "minute": 90,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:36:00+00:00",
            "minute": 105,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        }
        ]
    }
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Pojęcia dotyczące Azure Maps usług pogodowych (wersja zapoznawcza)](./weather-services-concepts.md)

> [!div class="nextstepaction"]
> [Interfejs API REST usług Azure Maps Pogoda (wersja zapoznawcza)](/rest/api/maps/weather)