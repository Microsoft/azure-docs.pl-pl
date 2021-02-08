---
title: Zbadaj zalecenie dotyczące testów porównawczych CIS
titleSuffix: Azure Defender for IoT
description: Wykonaj podstawowe i zaawansowane badania na podstawie zaleceń dotyczących linii bazowej systemu operacyjnego.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 2f68ebedb229f7295bc9c5dcc3b3349808970e8c
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809837"
---
# <a name="investigate-os-baseline-based-on-cis-benchmark-recommendation"></a>Zbadaj plan bazowy systemu operacyjnego (oparty na standardzie CIS) 

Wykonaj podstawowe i zaawansowane badania na podstawie zaleceń dotyczących linii bazowej systemu operacyjnego.

## <a name="basic-os-baseline-security-recommendation-investigation"></a>Badanie zalecenia dotyczącego zabezpieczeń podstawowych systemów operacyjnych OS  

Zalecenia dotyczące podstawy systemu operacyjnego można sprawdzić, przechodząc do portalu usługi Azure Defender dla IoT w obszarze **IoT Hub**. Aby uzyskać więcej informacji, zobacz jak [zbadać zalecenia dotyczące zabezpieczeń](quickstart-investigate-security-recommendations.md).

## <a name="advanced-os-baseline-security-recommendation-investigation"></a>Zaawansowane badanie zalecenia bezpieczeństwa systemu operacyjnego OS  

W tej sekcji opisano, jak lepiej zrozumieć wyniki testów linii bazowej systemu operacyjnego i zbadać zdarzenia w usłudze Azure Log Analytics.  

Badanie zalecenia dotyczącego zabezpieczeń w ramach zaawansowanego systemu operacyjnego jest obsługiwane tylko przy użyciu usługi log Analytics. Przed kontynuowaniem Połącz usługę Defender dla IoT z obszarem roboczym Log Analytics. Więcej informacji o zaawansowanych zaleceniach dotyczących zabezpieczeń linii bazowej systemu operacyjnego znajduje się w temacie How to [Configure Azure Defender for IoT agent based](how-to-configure-agent-based-solution.md).

Aby zbadać zdarzenia zabezpieczeń IoT w Log Analytics dla alertów:

1. Przejdź do strony **alerty** .

1. Wybierz pozycję **Zbadaj zalecenia w obszarze roboczym log Analytics**.

Aby wykonać zapytanie dotyczące zdarzeń zabezpieczeń IoT w Log Analytics dla zaleceń:

1. Przejdź do strony **zalecenia** .

1. Wybierz pozycję **Zbadaj zalecenia w obszarze roboczym log Analytics**.

1. Wybierz pozycję **Pokaż szczegóły reguł odniesienia systemu operacyjnego (OS)** na stronie szybki podgląd **szczegółów zalecenia** , aby wyświetlić szczegóły określonego urządzenia.

   :::image type="content" source="media/how-to-investigate-cis-benchmark/recommendation-details.png" alt-text="Zobacz szczegóły konkretnego urządzenia."::: 

Aby zbadać zdarzenia zabezpieczeń IoT w Log Analytics obszarze roboczym bezpośrednio:

1. Przejdź do strony **dzienniki** .

    :::image type="content" source="media/how-to-investigate-cis-benchmark/logs.png" alt-text="Wybierz pozycję Dzienniki z okienka po lewej stronie.":::

1. Wybierz opcję **Zbadaj alerty** lub wybierz opcję **Zbadaj alerty w log Analytics** z dowolnego zalecenia dotyczącego zabezpieczeń lub alertu.   

## <a name="useful-queries-to-investigate-the-os-baseline-resources"></a>Przydatne zapytania do badania zasobów bazowych systemu operacyjnego: 

> [!Note]
> Pamiętaj, aby zamienić na `<device-id>` nazwy, które nadała urządzeniu w każdym z poniższych zapytań. 


### <a name="retrieve-the-latest-information"></a>Pobierz najnowsze informacje

- **Niepowodzenie floty urządzenia**: Uruchom następujące zapytanie, aby pobrać najnowsze informacje o kontrolach zakończonych niepowodzeniem w ramach floty urządzenia: 

    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **Awaria konkretnego urządzenia** — Uruchom następujące zapytanie, aby pobrać najnowsze informacje o kontrolach zakończonych niepowodzeniem na określonym urządzeniu:  

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```

- **Błąd określonego urządzenia** — Uruchom to zapytanie, aby pobrać najnowsze informacje o testach, które mają błąd na określonym urządzeniu: 

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "ERROR" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **Zaktualizuj listę urządzeń dla floty urządzenia, która nie powiodła się,** aby pobrać zaktualizowaną listę urządzeń (w ramach floty urządzenia), która nie powiodła się:  
 
    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    where event.CceId contains "6.2.8" | 
    
    project DeviceId; 
    ```
 
## <a name="next-steps"></a>Następne kroki

[Zbadaj zalecenia dotyczące zabezpieczeń](quickstart-investigate-security-recommendations.md).
 