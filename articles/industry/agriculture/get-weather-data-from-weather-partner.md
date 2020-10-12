---
title: Pobieranie danych pogody od partnerów pogody
description: W tym artykule opisano sposób pobierania danych pogody od partnerów.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 35acf4e9bd338a0e67b046a59d8884df0626e516
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87429262"
---
# <a name="get-weather-data-from-weather-partners"></a>Pobieranie danych pogody od partnerów pogody

Usługa Azure FarmBeats ułatwia wprowadzanie danych pogody od dostawców danych pogody przy użyciu struktury łączników opartych na platformie Docker. Korzystając z tej struktury, dostawcy danych pogody implementują platformę Docker, którą można zintegrować z FarmBeats. Obecnie obsługiwany jest następujący dostawca danych pogody.

  ![Partnerzy FarmBeats](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)
  
   [DTN](https://www.dtn.com/dtn-content-integration/)

Dane pogodowe mogą służyć do generowania szczegółowych informacji umożliwiających podjęcie działań i kompilowania modeli AI lub ML w FarmBeats.

## <a name="before-you-start"></a>Przed rozpoczęciem

Aby uzyskać dane pogodowe, upewnij się, że [zainstalowano FarmBeats](https://aka.ms/farmbeatsinstalldocumentation). Integracja pogody jest obsługiwana w wersjach 1.2.11 i wyższych. 

## <a name="enable-weather-integration-with-farmbeats"></a>Włączanie integracji pogody z FarmBeats

Aby rozpocząć pobieranie danych pogody z FarmBeats Datahub:

1. Przejdź do FarmBeats Datahub Swagger `https://farmbeatswebsite-api.azurewebsites.net/swagger` .

2. Przejdź do interfejsu API/partner, a następnie wprowadź żądanie POST. Użyj następującego ładunku wejściowego:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
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

   Na przykład aby uzyskać dane pogodowe z DTN, użyj następującego ładunku. Nazwę i opis można zmodyfikować zgodnie z preferencjami.

   > [!NOTE]
   > Poniższy krok wymaga klucza interfejsu API. Aby uzyskać klucz dla subskrypcji usługi DTN, skontaktuj się z DTN.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > Aby uzyskać więcej informacji na temat obiektu partnerskiego, zobacz [dodatek](get-weather-data-from-weather-partner.md#appendix) w tym artykule.

   Poprzedni krok spowoduje udostępnienie zasobów w celu umożliwienia uruchomienia platformy Docker w środowisku FarmBeats klienta.  

   Udostępnienie zasobów trwa od 10 do 15 minut.

3. Sprawdź stan obiektu/partner utworzonego w poprzednim kroku. Aby sprawdzić stan, należy wykonać żądanie GET w interfejsie API/partner i sprawdzić stan obiektu partnera. Po pomyślnym zainicjowaniu przez FarmBeats partnera stan jest ustawiony na **aktywny**.

4. W interfejsie API/JobType wprowadź żądanie GET. Wyszukaj zadania pogodowe utworzone wcześniej w procesie dodawania partnera. W zadaniach pogodowych pole **potokname** ma następujący format: **partner-name_partner-type_job-Name**.

      Teraz wystąpienie usługi FarmBeats ma aktywnego partnera danych o pogodzie. Można uruchamiać zadania w celu żądania danych pogody dla określonej lokalizacji (szerokości geograficznej i długości geograficznej) oraz zakresu dat. Typy zadań będą zawierać szczegółowe informacje o parametrach wymaganych do uruchamiania zadań pogodowych.

      Na przykład dla DTN zostaną utworzone następujące typy zadań:
   
      - **get_dtn_daily_observations**: Uzyskaj codzienne obserwacje dla lokalizacji i okresu.
      - **get_dtn_daily_forecasts**: Pobierz prognozy dzienne dla lokalizacji i okresu.
      - **get_dtn_hourly_observations**: Uzyskaj godzinyowe obserwacje dla lokalizacji i okresu.
      - **get_dtn_hourly_forecasts**: Uzyskaj prognozy godzinowe dla lokalizacji i okresu.

6. Zanotuj identyfikator i parametry typów zadań.

7. Przejdź do interfejsu API/Jobs i wprowadź żądanie POST w witrynie/jobs. Użyj następującego ładunku wejściowego:

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

   Na przykład aby uruchomić **get_dtn_daily_observations**, należy użyć następującego ładunku:

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. Poprzedni krok uruchamia zadania pogodowe zgodnie z definicją w Docker partnera i pozyskuje dane pogodowe do FarmBeats. Stan zadania można sprawdzić, wysyłając żądanie GET w witrynie/jobs. W odpowiedzi Znajdź pozycję **CurrentState**. Po zakończeniu **CurrentState** jest ustawiony na **powodzenie**.

## <a name="query-ingested-weather-data"></a>Kwerenda pozyskiwania danych pogodowych

Po zakończeniu zadań pogodowych możesz wysyłać zapytania do pozyskanych danych pogodowych, aby tworzyć modele lub szczegółowe informacje umożliwiające podejmowanie działań za pomocą interfejsów API REST FarmBeats Datahub.

Aby zbadać dane pogodowe przy użyciu interfejsu API REST FarmBeats:

1. W FarmBeats Datahub [Swagger](https://yourdatahub.azurewebsites.net/swagger)przejdź do interfejsu API/WeatherDataLocation i Utwórz żądanie Get. Odpowiedź obejmuje obiekty/WeatherDataLocation utworzone dla lokalizacji (Szerokość geograficzna i Długość geograficzna), które zostały określone przez uruchomienie zadania. Zanotuj **Identyfikator** i **weatherDataModelId** obiektów.

2. Utwórz żądanie GET/{ID} w interfejsie API/WeatherDataModel dla **weatherDataModelId** , tak jak wcześniej. Model danych pogody przedstawia wszystkie metadane i szczegółowe informacje o pozyskanych danych pogodowych. Na przykład, w obiekcie modelu danych pogody, miara pogody wskazuje, jakie informacje o pogodzie są obsługiwane i w jakich typach i jednostkach. Na przykład:

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

3. Przejdź do interfejsu API telemetrii i wprowadź żądanie POST. Użyj następującego ładunku wejściowego:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

    Odpowiedź pokazuje dane pogodowe dostępne dla określonego zakresu czasu:

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

W poprzednim przykładzie odpowiedź pokazuje dane dla dwóch sygnatur czasowych. Pokazuje także nazwę miary (temperatury) i wartości raportowanych danych pogody w dwóch sygnaturach czasowych. Zapoznaj się ze skojarzonym modelem danych pogody, aby interpretować typ i jednostkę zgłoszonych wartości.

## <a name="troubleshoot-job-failures"></a>Rozwiązywanie problemów z błędami zadań

Aby rozwiązać problemy z błędami zadań, [Sprawdź dzienniki zadań](troubleshoot-azure-farmbeats.md#weather-data-job-failures).


## <a name="appendix"></a>Dodatek

|        Partner   |  Szczegóły   |
| ------- | -------             |
|     DockerDetails-ImageName         |          Nazwa obrazu platformy Docker. Na przykład docker.io/mydockerimage (Image in hub.docker.com) lub myazureacr.azurecr.io/mydockerimage (Image in Azure Container Registry) i tak dalej. Jeśli nie jest podany żaden rejestr, wartość domyślna to hub.docker.com.      |
|          DockerDetails - imageTag             |         Nazwa tagu obrazu platformy Docker. Wartość domyślna to "Najnowsza".     |
|  DockerDetails — poświadczenia      |  Poświadczenia umożliwiające dostęp do prywatnego platformy Docker. Partner udostępnia poświadczenia.   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch jednostki SKU maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [wszystkie dostępne maszyny wirtualne z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <BR> <BR> Prawidłowe wartości to "Small", "ExtraLarge", "Large", "A8", "A9", "medium", "A5", "STANDARD_D1 A6", "", "STANDARD_D2", "STANDARD_D3", "STANDARD_D4", "STANDARD_D11", "STANDARD_D12", "STANDARD_D13", "STANDARD_D14", "A10", "A11", "STANDARD_D1_V2", "STANDARD_D2_V2", "STANDARD_D3_V2", "STANDARD_D4_V2", "STANDARD_D11_V2", "STANDARD_D12_V2", "STANDARD_D13_V2", "STANDARD_D14_V2", "STANDARD_G1", "STANDARD_G2", "STANDARD_G3", "STANDARD_G4" , "STANDARD_G5", "STANDARD_D5_V2", "BASIC_A1", "BASIC_A2", "BASIC_A3", "BASIC_A4", "STANDARD_A1", "STANDARD_A2", "STANDARD_A3", "STANDARD_A4", "STANDARD_A5", "STANDARD_A6", "STANDARD_A7", "STANDARD_A8", "STANDARD_A9", "STANDARD_A10", "STANDARD_A11", "STANDARD_D15_V2", "STANDARD_F1", "STANDARD_F2", "STANDARD_F4", "STANDARD_F8", "STANDARD_F16", "STANDARD_NV6", "STANDARD_NV12", "STANDARD_NV24", "STANDARD_NC6", "STANDARD_NC12", "STANDARD_NC24", "STANDARD_NC24r" , "STANDARD_H8", "STANDARD_H8m", "STANDARD_H16", "STANDARD_H16m", "STANDARD_H16mr", "STANDARD_H16r", "STANDARD_A1_V2", "STANDARD_A2_V2", "STANDARD_A4_V2", "STANDARD_A8_V2", "STANDARD_A2m_V2", "STANDARD_A4m_V2", "STANDARD_A8m_V2", "STANDARD_M64ms", "STANDARD_M128s" i "STANDARD_D2_V3". *Wartość domyślna to "STANDARD_D2_V2".*  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  Liczba węzłów komputerów dedykowanych na pulę partii. Wartość domyślna to 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Identyfikator jednostki SKU agenta węzła Azure Batch. Obecnie obsługiwany jest tylko Agent węzła wsadowego "Batch. Node. Ubuntu 18,04".    |
| DockerDetails - partnerCredentials | Poświadczenia do wywoływania interfejsu API partnera w platformie Docker. Partner zawiera te informacje na podstawie obsługiwanego mechanizmu autoryzacji; na przykład nazwa użytkownika i hasło lub klucze interfejsu API. |
| partnertype | "Pogoda". Inne typy partnerów w FarmBeats to "czujnik" i "obraz".  |
|  name   |   Wymagana nazwa partnera w systemie FarmBeats.   |
|  description |  Opis   |

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz zapytanie o dane czujnika z wystąpienia usługi Azure FarmBeats, Dowiedz się, jak [generować mapy](generate-maps-in-azure-farmbeats.md#generate-maps) dla Farm.
