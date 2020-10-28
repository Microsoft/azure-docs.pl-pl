---
title: Dane telemetryczne pozyskiwane z zapytań
description: W tym artykule opisano, jak wykonywać zapytania dotyczące pozyskiwanych danych telemetrycznych.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: b9067e2f78c8098d4a21263ac89caf03da631274
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677479"
---
# <a name="query-ingested-telemetry-data"></a>Dane telemetryczne pozyskiwane z zapytań

W tym artykule opisano sposób wykonywania zapytań dotyczących pozyskiwanych danych z czujnika z usługi Azure FarmBeats.

Pozyskiwanie danych z zasobów Internet rzeczy (IoT), takich jak urządzenia i czujniki, jest typowym scenariuszem w FarmBeats. Tworzysz metadane dla urządzeń i czujników, a następnie pozyskasz dane historyczne do FarmBeats w postaci kanonicznej. Gdy dane czujnika są dostępne w FarmBeats Datahub, możemy wysyłać zapytania do tego samego, aby generować szczegółowe dane lub modele kompilacji z możliwością działania.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed przejściem do tego artykułu upewnij się, że zainstalowano FarmBeats i dane telemetryczne czujnika z urządzeń IoT w FarmBeats.

Aby pozyskać dane telemetryczne czujnika, odwiedź stronę pozyskiwania [danych telemetrycznych](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Przed kontynuowaniem musisz upewnić się, że znasz interfejsy API REST FarmBeats, ponieważ będziesz wykonywać zapytania dotyczące pozyskanych danych telemetrycznych za pomocą interfejsów API. Aby uzyskać więcej informacji na temat interfejsów API FarmBeats, zobacz [FarmBeats API REST](rest-api-in-azure-farmbeats.md). **Upewnij się, że możesz wykonywać żądania interfejsu API w punkcie końcowym usługi FarmBeats Datahub** .

## <a name="query-ingested-sensor-telemetry-data"></a>Kwerenda pozyskiwania danych telemetrycznych czujnika

Istnieją dwa sposoby uzyskiwania dostępu do danych telemetrycznych z usługi FarmBeats i wykonywania na nich zapytań:

- Interfejs API i
- Time Series Insights (TSI).

### <a name="query-using-rest-api"></a>Zapytanie przy użyciu interfejsu API REST

Postępuj zgodnie z instrukcjami, aby wykonać zapytanie o dane telemetryczne czujnika pozyskiwania przy użyciu interfejsów API REST FarmBeats:

1. Zidentyfikuj żądany czujnik. Można to zrobić, wykonując żądanie GET w interfejsie API/sensor.

> [!NOTE]
> **Identyfikator** i **sensorModelId** zainteresowanego obiektu czujnika.

2. Utwórz funkcję GET/{ID} w interfejsie API/SensorModel dla **sensorModelId** zgodnie z opisem w kroku 1. "Model czujnika" zawiera wszystkie metadane i szczegóły pozyskiwanej danych telemetrycznych z czujnika. Na przykład **pomiar czujnika** w obiekcie **modelu czujnika** zawiera szczegółowe informacje o tym, jakie miary są wysyłane przez czujnik i w jakich typach i jednostkach. Przykład:

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
Zanotuj odpowiedź z wywołania GET/{ID} dla modelu czujnika.

3. Wykonaj wywołanie POST interfejsu API/Telemetry z następującym ładunkiem wejściowym

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. Odpowiedź z interfejsu API/Telemetry będzie wyglądać następująco:

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
W powyższym przykładzie odpowiedzi dane telemetryczne czujnika są dostępne dla dwóch sygnatur czasowych wraz z nazwą miary ("moist_soil_last") i wartościami raportowanych danych telemetrycznych w dwóch sygnaturach czasowych. Należy odwołać się do skojarzonego modelu czujnika (zgodnie z opisem w kroku 2), aby interpretować typ i jednostkę zgłoszonych wartości.

### <a name="query-using-azure-time-series-insights-tsi"></a>Zapytanie przy użyciu Azure Time Series Insights (TSI)

FarmBeats wykorzystuje [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) do pozyskiwania, przechowywania, wykonywania zapytań i wizualizacji danych w skali IoT — dane, które są wysoce kontekstowe i zoptymalizowane pod kątem szeregów czasowych.

Dane telemetryczne są odbierane w centrum EventHub, a następnie przetwarzane i wypychane do środowiska TSI w ramach grupy zasobów FarmBeats. Dane mogą następnie być wysyłane bezpośrednio z TSI. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą TSI](../../time-series-insights/time-series-insights-explorer.md) .

Postępuj zgodnie z instrukcjami, aby wizualizować dane w ramach TSI:

1. Przejdź do **Azure Portal**  >  **grupy zasobów FarmBeats DataHub** w witrynie Azure Portal, > Wybierz środowisko **Time Series Insights** Environment (TSI-xxxx) > **zasad dostępu do danych** . Dodaj użytkownika z dostępem czytelnika lub współautorem.
2. Przejdź do strony **Przegląd** środowiska **Time Series Insights** (TSI-xxxx) i wybierz **adres URL Eksploratora Time Series Insights** . Teraz będzie można wizualizować pozyskiwaną telemetrię.

Oprócz przechowywania, wykonywania zapytań i wizualizacji danych telemetrycznych, TSI również umożliwia integrację z pulpitem nawigacyjnym Power BI. Aby uzyskać więcej informacji, zobacz [tutaj]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>Następne kroki

Masz teraz zapytanie o dane czujnika z wystąpienia usługi Azure FarmBeats. Teraz Dowiedz się, jak [generować mapy](generate-maps-in-azure-farmbeats.md#generate-maps) dla Farm.