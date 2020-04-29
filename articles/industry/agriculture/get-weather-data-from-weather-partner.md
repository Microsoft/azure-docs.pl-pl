---
title: Pobieranie danych pogody od partnerów pogody
description: W tym artykule opisano sposób pobierania danych pogody od partnerów.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266164"
---
# <a name="get-weather-data-from-weather-partners"></a>Pobieranie danych pogody od partnerów pogody

Usługa Azure FarmBeats ułatwia wprowadzanie danych pogody od dostawców danych pogody przy użyciu struktury łączników opartych na platformie Docker. Korzystając z tej struktury, dostawcy danych pogody implementują platformę Docker, którą można zintegrować z FarmBeats. Obecnie obsługiwane są następujące dostawcy danych pogody:

[NOAA dane z usługi Azure Open DataSets](https://azure.microsoft.com/services/open-datasets/)

Dane pogodowe mogą służyć do generowania szczegółowych informacji z możliwością podejmowania działań i tworzenia modeli AI/ML w FarmBeats.

## <a name="before-you-start"></a>Przed rozpoczęciem

Aby uzyskać dane pogodowe, upewnij się, że zainstalowano FarmBeats. **Integracja pogody jest obsługiwana w wersji 1.2.11 lub nowszej**. Aby zainstalować usługę Azure FarmBeats, zobacz [Install FarmBeats](https://aka.ms/farmbeatsinstalldocumentation).

## <a name="enable-weather-integration-with-farmbeats"></a>Włączanie integracji pogody z FarmBeats

Aby rozpocząć pobieranie danych pogody z centrum danych FarmBeats, wykonaj następujące czynności:

1. Przejdź do centrum danych FarmBeats Swagger (https://yourdatahub.azurewebsites.net/swagger)

2. Przejdź do interfejsu API/partner i wprowadź żądanie POST z następującym ładunkiem wejściowym:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name. Default is azurefarmbeats/fambeats-noaa>",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Na przykład aby uzyskać dane o pogodzie z NOAA przez otwarte zestawy danych platformy Azure, użyj poniższego ładunku. Nazwę i opis można zmodyfikować zgodnie z preferencjami.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "azurefarmbeats/farmbeats-noaa",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerType": "Weather",
     "name": "ods-noaa",
     "description": "NOAA data from Azure Open Datasets registered as a Weather Partner"
   }  
   ```

   > [!NOTE]
   > Aby uzyskać więcej informacji o obiekcie partnerskim, zobacz [dodatek](get-weather-data-from-weather-partner.md#appendix)

   Poprzedni krok spowoduje udostępnienie zasobów w celu umożliwienia uruchomienia platformy Docker w środowisku FarmBeats klienta.  

   Udostępnienie powyższych zasobów zajmie około 10-15 minut.

3. Sprawdź stan obiektu/partner utworzonego w kroku 2. W tym celu należy wykonać żądanie GET w interfejsie API/partner i sprawdzić **stan** obiektu partnera. Po pomyślnym zainicjowaniu przez FarmBeats partnera stan jest ustawiony na **aktywny**.

4. Przejdź do interfejsu API/JobType i wprowadź żądanie GET. Sprawdź zadania pogodowe, które są tworzone w ramach procesu dodawania partnera w kroku 2. Pole **potokname** w zadaniach pogodowych będzie mieć następujący format: "partner-name_partner-type_job-Name".

5. Teraz wystąpienie usługi FarmBeats ma aktywnego partnera danych pogody i można uruchamiać zadania do żądania danych pogody dla określonej lokalizacji (Szerokość geograficzna/Długość geograficzna) i zakres dat. Typu zadania będzie zawierać szczegółowe informacje o parametrach wymaganych do uruchamiania zadań pogodowych.

   Na przykład w przypadku danych NOAA z usługi Azure Open DataSets zostanie utworzony następujący typu zadania (s):

   - get_weather_data (Pobierz dane pogody ISD/historyczne)
   - get_weather_forecast_data (pobieranie danych pogody GFS/prognoza)

6. Zanotuj **Identyfikator** i parametry typu zadaniaów.

7. Przejdź do interfejsu API/Jobs i wprowadź żądanie POST na/Jobs z następującym ładunkiem wejściowym:

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   Na przykład aby uruchomić **get_weather_data**, należy użyć następującego ładunku:

   ```json
   {
 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "start_date": "yyyy-mm-dd",
               "end_date": "yyyy-mm-dd"
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. W poprzednim kroku zadania pogodowe zostaną uruchomione zgodnie z definicją z platformy Docker partnera i pozyskają dane pogodowe do FarmBeats. Stan zadania można sprawdzić, wykonując żądanie GET w witrynie/Jobs i poszukaj **CurrentState** w odpowiedzi. Po zakończeniu currentState jest ustawiony na **powodzenie**.

## <a name="query-ingested-weather-data"></a>Kwerenda pozyskiwania danych pogodowych

Po zakończeniu zadań pogodowych możesz wysyłać zapytania do pozyskanych danych pogodowych, aby tworzyć modele lub szczegółowe informacje umożliwiające podjęcie odpowiednich działań. Istnieją dwa sposoby uzyskiwania dostępu do danych pogody z FarmBeats i wykonywania na nich zapytań:

- Interfejs API i
- Time Series Insights (TSI).

### <a name="query-using-rest-api"></a>Zapytanie przy użyciu interfejsu API REST

Aby zbadać dane pogodowe przy użyciu interfejsu API REST FarmBeats, wykonaj następujące czynności:

1. W centrum danych FarmBeats Swagger (https://yourdatahub.azurewebsites.net/swagger)przejdź do interfejsu API/WeatherDataLocation i Utwórz żądanie Get. Odpowiedź będzie mieć obiekty/WeatherDataLocation utworzone dla lokalizacji (Szerokość geograficzna/Długość geograficzna), która została określona w ramach uruchomienia zadania. Zanotuj **Identyfikator** i **weatherDataModelId** obiektów (y).

2. Utwórz funkcję GET/{ID} w interfejsie API/WeatherDataModel dla **weatherDataModelId** zgodnie z opisem w kroku 1. "Model danych pogody" zawiera wszystkie metadane i szczegółowe informacje o pozyskanych danych pogodowych. Na przykład **miara pogody** w obiekcie **modelu danych pogody** zawiera szczegółowe informacje o tym, jakie informacje o pogodzie są obsługiwane i w jakich typach i jednostkach. Na przykład:

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Zanotuj odpowiedź z wywołania GET/{ID} dla modelu danych pogody.

3. Przejdź do interfejsu API **telemetrii** i wprowadź żądanie post z następującym ładunkiem wejściowym:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. Odpowiedź zawierająca dane pogodowe dostępne dla określonego zakresu czasu będzie wyglądać następująco:

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

W poprzednim przykładzie odpowiedź zawiera dane dla dwóch sygnatur czasowych wraz z nazwą miary ("temperatura") i wartościami danych o raportowanych Pogoda w dwóch sygnaturach czasowych. Należy odwołać się do skojarzonego modelu danych pogody (zgodnie z opisem w kroku 2 powyżej), aby interpretować typ i jednostkę raportowanych wartości.

### <a name="query-using-azure-time-series-insights-tsi"></a>Zapytanie przy użyciu Azure Time Series Insights (TSI)

FarmBeats korzysta z [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) do pozyskiwania, przechowywania, wykonywania zapytań i wizualizacji danych w skali IoT — dane, które są wysoce kontekstowe i zoptymalizowane pod kątem szeregów czasowych.

Dane o pogodzie są odbierane w centrum EventHub, a następnie wypychane do środowiska TSI w ramach grupy zasobów FarmBeats. Dane mogą następnie być wysyłane bezpośrednio z TSI. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).

Postępuj zgodnie z instrukcjami, aby wizualizować dane w ramach TSI:

1. Przejdź do **Azure portal** > **grupy zasobów Azure Portal FarmBeats DataHub** > wybierz pozycję **Time Series Insights** Environment (TSI-xxxx) > **zasad dostępu do danych**. Dodaj użytkownika z dostępem czytelnika lub współautorem.

2. Przejdź do strony **Przegląd** środowiska **Time Series Insights** (TSI-xxxx) i wybierz **adres URL Eksploratora Time Series Insights**. Teraz można wizualizować pozyskane dane pogodowe.

Oprócz przechowywania, wykonywania zapytań i wizualizacji danych pogody, TSI również umożliwia integrację z pulpitem nawigacyjnym Power BI. Aby uzyskać więcej informacji, zobacz [Wizualizacja danych z Time Series Insights w Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi).

## <a name="appendix"></a>Dodatek

|        Partner   |  Szczegóły   |
| ------- | -------             |
|     DockerDetails-ImageName         |          Nazwa obrazu platformy Docker. Na przykład docker.io/azurefarmbeats/farmbeats-noaa (Image in hub.docker.com) lub myazureacr.azurecr.io/mydockerimage (Image in Azure Container Registry) i tak dalej. Jeśli nie podano żadnego rejestru, wartością domyślną jest hub.docker.com      |
|          DockerDetails - imageTag             |         Nazwa tagu obrazu platformy Docker. Wartość domyślna to "Najnowsza"     |
|  DockerDetails — poświadczenia      |  Poświadczenia umożliwiające dostęp do prywatnego platformy Docker. Klient zostanie dostarczony przez partnera   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch jednostki SKU maszyny wirtualnej. Zobacz [tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , aby znaleźć wszystkie dostępne maszyny wirtualne z systemem Linux. Prawidłowe wartości to: "Small", "ExtraLarge", "Large", "A8", "A9", "medium", "A5", "STANDARD_D1 A6", "", "STANDARD_D2", "STANDARD_D3", "STANDARD_D4", "STANDARD_D11", "STANDARD_D12", "STANDARD_D13", "STANDARD_D14", "A10", "A11", "STANDARD_D1_V2", "STANDARD_D2_V2", "STANDARD_D3_V2", "STANDARD_D4_V2", "STANDARD_D11_V2", "STANDARD_D12_V2", "STANDARD_D13_V2", "STANDARD_D14_V2", "STANDARD_G1", "STANDARD_G2", "STANDARD_G3", "STANDARD_G4" , "STANDARD_G5", "STANDARD_D5_V2", "BASIC_A1", "BASIC_A2", "BASIC_A3", "BASIC_A4", "STANDARD_A1", "STANDARD_A2", "STANDARD_A3", "STANDARD_A4", "STANDARD_A5", "STANDARD_A6", "STANDARD_A7", "STANDARD_A8", "STANDARD_A9", "STANDARD_A10", "STANDARD_A11", "STANDARD_D15_V2", "STANDARD_F1", "STANDARD_F2", "STANDARD_F4", "STANDARD_F8", "STANDARD_F16", "STANDARD_NV6", "STANDARD_NV12", "STANDARD_NV24", "STANDARD_NC6", "STANDARD_NC12", "STANDARD_NC24", "STANDARD_NC24r" "STANDARD_H8", "STANDARD_H8m", "STANDARD_H16", "STANDARD_H16m", "STANDARD_H16mr", "STANDARD_H16r", "STANDARD_A1_V2", "STANDARD_A2_V2", "STANDARD_A4_V2", "STANDARD_A8_V2", "STANDARD_A2m_V2", "STANDARD_A4m_V2", "STANDARD_A8m_V2", "STANDARD_M64ms", "STANDARD_M128s", "STANDARD_D2_V3". **Wartość domyślna to "standard_d2_v2"**  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  Nie. dedykowanych węzłów komputerów dla puli usługi Batch. Wartość domyślna to 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Identyfikator jednostki SKU agenta węzła Azure Batch. Obecnie jest obsługiwany tylko Agent węzła wsadowego "Batch. Node. Ubuntu 18,04".    |
| DockerDetails - partnerCredentials | poświadczenia dotyczące wywoływania interfejsu API partnera w programie Docker. Partner musi przekazać klientom te informacje na podstawie mechanizmu uwierzytelniania, który jest obsługiwany na przykład. Nazwa użytkownika/hasło lub klucze interfejsu API. |
| partnertype | "Pogoda" (inne typy partnerów w FarmBeats to "czujnik" i "obraz")  |
|  name   |   Wymagana nazwa partnera w systemie FarmBeats   |
|  description |  Opis   |

## <a name="next-steps"></a>Następne kroki

Masz teraz zapytanie o dane czujnika z wystąpienia usługi Azure FarmBeats. Teraz Dowiedz się, jak [generować mapy](generate-maps-in-azure-farmbeats.md#generate-maps) dla Farm.
