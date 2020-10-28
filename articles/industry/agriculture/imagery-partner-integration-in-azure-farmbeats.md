---
title: Integracja z partnerami obsługującymi obrazy
description: W tym artykule opisano integrację z partnerem obrazów.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.custom: has-adal-ref
ms.openlocfilehash: 40e17a9f73d98f24c6c7fbb55ef38cc2bc088861
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675935"
---
# <a name="imagery-partner-integration"></a>Integracja z partnerami obsługującymi obrazy

W tym artykule opisano, jak za pomocą składnika usługi Azure FarmBeats translator wysyłać dane obrazów do FarmBeats. Dane o obrazach rolniczych można generować z różnych źródeł, takich jak kamery wielowidmowe, satelity i dronom. Partnerzy w społecznościach społecznościowych mogą zintegrować się z usługą FarmBeats, aby zapewnić klientom niestandardowe mapy dla swoich Farm.

Dane, które są dostępne, mogą być wizualizowane za pomocą akceleratora FarmBeats i mogą być używane do modelu łączenia danych i uczenia maszynowego/sztucznej analizy (ML/AI), które są tworzone przez firmy rolnicze lub Integratory systemów klientów.

FarmBeats zapewnia możliwość:

- Definiowanie niestandardowych typów obrazów, źródła i formatu pliku przy użyciu interfejsów API/ExtendedType.
- Pozyskiwanie danych obrazów z różnych źródeł za pośrednictwem interfejsów API/Scene i/SceneFile.

Poniższe informacje koncentrują się na pobieraniu obrazów do systemu FarmBeats.

Po wybraniu sekcji **drona obrazy** zostanie otwarte okno podręczne pokazujące obraz o wysokiej rozdzielczości drona orthomosaic. Możesz uzyskać dostęp do oprogramowania partnerskiego, które ułatwia planowanie lotów drona oraz uzyskiwanie danych pierwotnych. Będziesz nadal używać oprogramowania partnera do planowania ścieżki i orthomosaic obrazu.

Partnerzy drona muszą umożliwić klientom połączenie ich konta klienta z ich wystąpieniem FarmBeats na platformie Azure.

Aby połączyć FarmBeats, należy użyć następujących poświadczeń w oprogramowaniu partnerskim drona:

- Punkt końcowy interfejsu API
- Identyfikator dzierżawy
- Identyfikator klienta
- Klucz tajny klienta

## <a name="api-development"></a>Programowanie interfejsu API

Interfejsy API zawierają dokumentację techniczną struktury Swagger. Aby uzyskać informacje o interfejsach API i odpowiadających im żądania lub odpowiedzi, zobacz [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="authentication"></a>Authentication

FarmBeats używa [Active Directory](../../app-service/overview-authentication-authorization.md) Microsoft Azure (Azure AD). Azure App Service zapewnia wbudowaną obsługę uwierzytelniania i autoryzacji. 

Aby uzyskać więcej informacji na temat usługi Azure AD, zobacz [Azure Active Directory](../../app-service/overview-authentication-authorization.md).   

FarmBeats Datahub używa uwierzytelniania okaziciela, które wymaga następujących poświadczeń:

- Identyfikator klienta
- Klucz tajny klienta
- Identyfikator dzierżawy

Przy użyciu poprzednich poświadczeń wywołujący może zażądać tokenu dostępu, który musi zostać wysłany w kolejnych żądaniach interfejsu API, w sekcji nagłówka w następujący sposób:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Poniższy przykład kodu w języku Python pobiera token dostępu. Następnie można użyć tokenu do kolejnych wywołań interfejsu API do FarmBeats.

```python
import requests
import json
import msal

# Your service principal App ID
CLIENT_ID = "<CLIENT_ID>"
# Your service principal password
CLIENT_SECRET = "<CLIENT_SECRET>"
# Tenant ID for your Azure subscription
TENANT_ID = "<TENANT_ID>"

AUTHORITY_HOST = 'https://login.microsoftonline.com'
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID

ENDPOINT = "https://<yourfarmbeatswebsitename-api>.azurewebsites.net"
SCOPE = ENDPOINT + "/.default"

context = msal.ConfidentialClientApplication(CLIENT_ID, authority=AUTHORITY, client_credential=CLIENT_SECRET)
token_response = context.acquire_token_for_client(SCOPE)
# We should get an access token here
access_token = token_response.get('access_token')
```

## <a name="http-request-headers"></a>Nagłówki żądań HTTP

Poniżej znajdują się najczęstsze nagłówki żądań, które należy określić podczas wywołania interfejsu API do FarmBeats Datahub.

**Nagłówki** | **Opis i przykład**
--- | ---
Content-Type  | Format żądania (Content-Type: Application/ <format> ). W przypadku interfejsów API FarmBeats Datahub format jest JSON. Content-Type: Application/JSON
Autoryzacja | Określa token dostępu wymagany do wywołania interfejsu API. Autoryzacja: <okaziciela Access-Token>
Zaakceptuj  | Format odpowiedzi. W przypadku interfejsów API FarmBeats Datahub format jest JSON. Akceptuj: Application/JSON


## <a name="api-requests"></a>Żądania interfejsu API

Aby wykonać żądanie interfejsu API REST, Połącz:

- Metoda HTTP (GET, POST i PUT).
- Adres URL usługi interfejsu API.
- Identyfikator URI zasobu (do wykonywania zapytań, przesyłania danych, aktualizowania lub usuwania).
- Co najmniej jeden nagłówek żądania HTTP.

Opcjonalnie można uwzględnić parametry zapytania dla wywołań GET do filtrowania, ograniczyć rozmiar i sortować dane w odpowiedziach.

Następujące przykładowe żądanie ma na celu uzyskanie listy urządzeń:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

Większość wywołań GET, POST i PUT wymaga treści żądania JSON.

Poniższe przykładowe żądanie polega na utworzeniu urządzenia. Ten przykład zawiera wejściowy kod JSON z treścią żądania.


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"accept: application/json" -H
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Format danych

JSON to typowy format danych niezależny od języka, który zapewnia prostą reprezentację dowolnych struktur danych. Aby uzyskać więcej informacji, zobacz temat [org JSON](https://JSON.org).

## <a name="ingest-imagery-into-farmbeats"></a>Pozyskiwanie obrazów w FarmBeats

Gdy partner ma poświadczenia, aby nawiązać połączenie z usługą FarmBeats Datahub, partner wykonuje następujące czynności w składniku usługi Translator.

1.  Utwórz nowy typ rozszerzony dla następujących pól, zgodnie z typem obrazów do załadowania:

    - **Źródło sceny** : na przykład drone_partner_name
    - **Typ sceny** : na przykład drona
    - **Typ pliku sceny** : na przykład indeks CHLOROPHYLL
    - **Typ zawartości pliku sceny** : na przykład Image/TIFF

2.  Wywołaj interfejs API/Farms, aby uzyskać listę Farm z systemu Azure FarmBeats.
3.  Zapewnij klientowi możliwość wyboru pojedynczej farmy z listy Farm.

    W systemie partnerskim musi zostać wyświetlona Farma w ramach oprogramowania partnerskiego, która umożliwia planowanie ścieżki i drona oraz zbieranie obrazów.

4.  Wywołaj interfejs API/Scene i podaj wymagane szczegóły, aby utworzyć nową scenę z unikatowym IDENTYFIKATORem sceny.
5.  Odbierz adres URL sygnatury dostępu współdzielonego obiektu BLOB, aby przekazać wymagane obrazy do FarmBeats Datahub w kontekście wybranej farmy w systemie FarmBeats.

Oto szczegółowy przepływ wywołań interfejsu API.

### <a name="step-1-extendedtype"></a>Krok 1: Rozszerzonytype

Zapoznaj się z interfejsem API/ExtendedType, aby sprawdzić, czy typ i źródło plików są dostępne w FarmBeats. Aby to zrobić, wywołaj funkcję GET w interfejsie API/ExtendedType.

Oto wartości zdefiniowane przez system:

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

Ten krok to jednorazowa konfiguracja. Zakres tego nowego typu sceny jest ograniczony do subskrypcji, w której zainstalowano usługę Azure FarmBeats.

Na przykład, aby dodać SceneSource: "SlantRange", należy wprowadzić identyfikator interfejsu API/ExtendedType przy użyciu ładunku klucza "SceneSource".

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

Zielonym polem jest nowe dodanie do wartości źródłowej sceny zdefiniowanej przez system.

### <a name="step-2-get-farm-details"></a>Krok 2. uzyskiwanie szczegółowych informacji o farmie

Sceny (pliki TIFF lub CSV) znajdują się w kontekście farmy. Aby uzyskać szczegółowe informacje dotyczące farmy, należy wykonać instrukcje GET in API/Farm. Interfejs API zwraca listę Farm, które są dostępne w FarmBeats. Można wybrać farmę, dla której mają zostać pozyskiwane dane.

Pobierz odpowiedź/Farm:

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-a-scene-id-post-call"></a>Krok 3. Tworzenie identyfikatora sceny (wywołanie POST)

Utwórz nową scenę (plik TIFF lub CSV) o podanych informacjach, która zawiera datę, sekwencję i identyfikator farmy, z którą jest skojarzona scena. Metadane skojarzone z sceną można zdefiniować w obszarze właściwości, które obejmują czas trwania i typ miary.

Utworzenie nowej sceny powoduje utworzenie nowego identyfikatora sceny, który jest skojarzony z farmą. Po utworzeniu identyfikatora sceny użytkownik może użyć tego samego do utworzenia nowego pliku (TIFF lub CSV) i zapisania zawartości pliku.

Przykładowy ładunek wejściowy dla wywołania POST w interfejsie API/Scene:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Odpowiedź interfejsu API:

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**Tworzenie pliku sceny**

Identyfikator sceny zwracany w kroku 3 to dane wejściowe dla pliku sceny. Plik sceny zwraca token adresu URL sygnatury dostępu współdzielonego, który jest ważny przez 24 godziny.

Jeśli użytkownik wymaga programistycznego sposobu przekazywania strumienia obrazów, zestaw SDK usługi BLOB Storage może służyć do definiowania metody przy użyciu identyfikatora pliku sceny, lokalizacji i adresu URL.

Przykładowy ładunek wejściowy dla wywołania POST w interfejsie API/SceneFile:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
Odpowiedź interfejsu API:

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

Wywołanie POST interfejsu API/SceneFile zwraca adres URL przekazywania sygnatury dostępu współdzielonego, który może służyć do przekazywania pliku CSV lub TIFF przy użyciu klienta lub biblioteki usługi Azure Blob Storage.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o szczegółach integracji opartych na interfejsie API REST, zobacz [interfejs API REST](rest-api-in-azure-farmbeats.md).