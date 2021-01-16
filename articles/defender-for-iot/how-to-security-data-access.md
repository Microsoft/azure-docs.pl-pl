---
title: Dostęp do danych rekomendacji & zabezpieczeń
description: Dowiedz się, jak uzyskać dostęp do alertów zabezpieczeń i danych rekomendacji w przypadku korzystania z usługi Defender for IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: 495f9d568760421c7f42df3acf74217c15b01a05
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246343"
---
# <a name="access-your-security-data"></a>Dostęp do danych zabezpieczeń

Usługa Defender for IoT przechowuje alerty zabezpieczeń, zalecenia i pierwotne dane zabezpieczeń (Jeśli użytkownik zdecyduje się je zapisać) w obszarze roboczym Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Aby skonfigurować, który obszar roboczy Log Analytics jest używany:

1. Otwórz Centrum IoT Hub.
1. Kliknij blok **Ustawienia** w sekcji **zabezpieczenia** .
1. Kliknij pozycję **zbieranie danych** i Zmień konfigurację obszaru roboczego log Analytics.

Aby uzyskać dostęp do alertów i rekomendacji w obszarze roboczym Log Analytics po zakończeniu konfiguracji:

1. Wybierz Alert lub rekomendację w usłudze Defender for IoT.
1. Kliknij pozycję **dalsze badanie**, a następnie kliknij, **Aby zobaczyć, które urządzenia mają ten alert kliknij tutaj i Wyświetl kolumnę DeviceID**.

Aby uzyskać szczegółowe informacje na temat wykonywania zapytań dotyczących danych z Log Analytics, zobacz [Rozpoczynanie pracy z zapytaniami w log Analytics](../azure-monitor/log-query/get-started-queries.md).

## <a name="security-alerts"></a>Alerty zabezpieczeń

Alerty zabezpieczeń są przechowywane w tabeli _AzureSecurityOfThings. SecurityAlert_ w obszarze roboczym log Analytics skonfigurowanym dla rozwiązania Defender for IoT.

Udostępniamy kilka przydatnych zapytań, które ułatwiają rozpoczęcie eksplorowania alertów zabezpieczeń.

### <a name="sample-records"></a>Przykładowe rekordy

Wybierz kilka losowych rekordów

```
// Select a few random records
//
SecurityAlert
| project
    TimeGenerated,
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | Nazwa wyświetlana                           | Opis                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018 r-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wys.          | Atak typu "odprowadzenie" zakończył się pomyślnie           | Atak z przeprowadzeniem pełnego wymuszenia na urządzeniu zakończył się pomyślnie        |    {"Pełny adres źródłowy": "[ \" 10.165.12.18: \" ]", "nazwy użytkowników": "[ \" \" ]", "DeviceID": "IoT-Device-Linux"}                                                                       |
| 2018 r-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wys.          | Pomyślne logowanie lokalne na urządzeniu      | Wykryto Pomyślne logowanie lokalne na urządzeniu     | {"Adres zdalny": "?", "Port zdalny": "", "Port lokalny": "", "Shell logowania": "/bin/Su", "Identyfikator procesu logowania": "28207", "Nazwa użytkownika": "osoba atakująca", "DeviceId": "IoT-Device-Linux"} |
| 2018 r-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wys.          | Nieudana próba logowania lokalnego na urządzeniu  | Wykryto nieudaną próbę zalogowania lokalnego do urządzenia |    {"Adres zdalny": "?", "Port zdalny": "", "Port lokalny": "", "Shell logowania": "/bin/Su", "Identyfikator procesu logowania": "22644", "Nazwa użytkownika": "osoba atakująca", "DeviceId": "IoT-Device-Linux"} |

### <a name="device-summary"></a>Podsumowanie urządzenia

Pobierz liczbę unikatowych alertów zabezpieczeń wykrytych w ostatnim tygodniu, pogrupowanych według IoT Hub, urządzenia, ważności alertu, typu alertu.

```
// Get the number of distinct security alerts detected in the last week, grouped by
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | Nazwa wyświetlana                           | Liczba |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wys.          | Atak typu "odprowadzenie" zakończył się pomyślnie           | 9   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Śred.        | Nieudana próba logowania lokalnego na urządzeniu  | 242 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wys.          | Pomyślne logowanie lokalne na urządzeniu      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Śred.        | Miner monety kryptograficznej                     | 4   |

### <a name="iot-hub-summary"></a>Podsumowanie Centrum IoT

Wybierz wiele różnych urządzeń z alertami w ciągu ostatniego tygodnia według IoT Hub, ważności alertu, typu alertu

```
// Select number of distinct devices which had alerts in the last week, by
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId,
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | Nazwa wyświetlana                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Wys.          | Atak typu "odprowadzenie" zakończył się pomyślnie           | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Śred.        | Nieudana próba logowania lokalnego na urządzeniu  | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Wys.          | Pomyślne logowanie lokalne na urządzeniu      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Śred.        | Miner monety kryptograficznej                     | 1          |

## <a name="security-recommendations"></a>Zalecenia dotyczące zabezpieczeń

Zalecenia dotyczące zabezpieczeń są przechowywane w tabeli _AzureSecurityOfThings. SecurityRecommendation_ w obszarze roboczym log Analytics skonfigurowanym dla rozwiązania Defender for IoT.

Udostępniamy kilka przydatnych zapytań, które ułatwiają rozpoczęcie eksplorowania zaleceń dotyczących zabezpieczeń.

### <a name="sample-records"></a>Przykładowe rekordy

Wybierz kilka losowych rekordów

```
// Select a few random records
//
SecurityRecommendation
| project
    TimeGenerated,
    IoTHubId=AssessedResourceId,
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```

| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | Opis | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 |    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Śred. | Aktywna | Znaleziono ograniczającą regułę zapory w łańcuchu wejściowym | Znaleziono regułę w zaporze, która zawiera oddzielny wzorzec dla szerokiego zakresu adresów IP lub portów | {"Rules": "[{ \" SourceAddress \" : \" \" , \" SourcePort \" : \" \" , \" DestinationAddress \" : \" \" , \" DestinationPort \" : \" 1337 \" }]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Śred. | Aktywna | Znaleziono ograniczającą regułę zapory w łańcuchu wejściowym | Znaleziono regułę w zaporze, która zawiera oddzielny wzorzec dla szerokiego zakresu adresów IP lub portów | {"Rules": "[{ \" SourceAddress \" : \" \" , \" SourcePort \" : \" \" , \" DestinationAddress \" : \" \" , \" DestinationPort \" : \" 1337 \" }]"} |

### <a name="device-summary"></a>Podsumowanie urządzenia

Uzyskaj liczbę odrębnych zaleceń dotyczących zabezpieczeń, pogrupowanych według IoT Hub, urządzeń, ważności i rodzaju zalecenia.

```
// Get the number of distinct active security recommendations, grouped by by
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | DeviceId      | RecommendationSeverity | Liczba |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wys.          | 2   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Śred.        | 1 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wys.          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Śred.        | 4   |

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [omówieniem](overview.md) usługi Defender for IoT
- Informacje o [architekturze](architecture.md) usługi Defender for IoT
- Zrozumienie i eksplorowanie [alertów usługi Defender for IoT](concept-security-alerts.md)
- Zrozumienie i eksplorowanie [zaleceń usługi Defender for IoT](concept-recommendations.md)