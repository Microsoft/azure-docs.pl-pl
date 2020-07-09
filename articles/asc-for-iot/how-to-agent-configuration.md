---
title: Konfigurowanie alertów zabezpieczeń
description: Dowiedz się, jak skonfigurować Azure Security Center dla agentów zabezpieczeń IoT do użycia z Azure Security Center dla usługi IoT Security.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: e41b7222ca89976674973e1eb700d62765b306f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311327"
---
# <a name="tutorial-configure-security-agents"></a>Samouczek: Konfigurowanie agentów zabezpieczeń

W tym artykule wyjaśniono Azure Security Center dla agentów zabezpieczeń IoT oraz szczegółowo, jak je zmienić i skonfigurować.

> [!div class="checklist"]
> * Konfigurowanie alertów zabezpieczeń
> * Zmień zachowanie agenta, edytując właściwości przędzy
> * Odkryj konfigurację domyślną

## <a name="agents"></a>Agenci

Azure Security Center dla agentów zabezpieczeń IoT zbiera dane z urządzeń IoT i wykonuje działania w celu ograniczenia wykrytych luk w zabezpieczeniach. Konfigurację agenta zabezpieczeń można sterować za pomocą zestawu właściwości wieloosiowego modułu, które możesz dostosować. Ogólnie rzecz biorąc, pomocnicze aktualizacje tych właściwości są rzadko używane.

Azure Security Center dla obiektu konfiguracji przędzy agenta zabezpieczeń IoT jest obiektem formatu JSON. Obiekt konfiguracji to zestaw właściwości, które można kontrolować, aby kontrolować zachowanie agenta.

Te konfiguracje umożliwiają dostosowanie agenta dla każdego wymaganego scenariusza. Na przykład automatyczne wyłączenie niektórych zdarzeń lub utrzymywanie zużycia mocy na minimalnym poziomie jest możliwe przez skonfigurowanie tych właściwości.

Aby wprowadzić zmiany, użyj [schematu](https://aka.ms/iot-security-github-module-schema) konfiguracji usługi Azure Security Center dla usługi IoT Security Agent.

## <a name="configuration-objects"></a>Obiekty konfiguracji

Właściwości powiązane z każdym Azure Security Centerm agenta zabezpieczeń IoT znajdują się w obiekcie konfiguracji agenta, w sekcji żądane właściwości modułu **azureiotsecurity** .

Aby zmodyfikować konfigurację, Utwórz i zmodyfikuj ten obiekt w ramach tożsamości **azureiotsecurity** w module.

Jeśli obiekt konfiguracji agenta nie istnieje w wieloosiowce modułu **azureiotsecurity** , wszystkie wartości właściwości agenta zabezpieczeń są ustawione na wartość domyślne.

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  }
}
```

## <a name="configuration-schema-and-validation"></a>Schemat i sprawdzanie poprawności konfiguracji

Upewnij się, że konfiguracja agenta została sprawdzona względem tego [schematu](https://aka.ms/iot-security-github-module-schema). Agent nie zostanie uruchomiony, jeśli obiekt konfiguracji nie jest zgodny ze schematem.

Jeśli Agent jest uruchomiony, obiekt konfiguracji zostanie zmieniony na nieprawidłową konfigurację (Konfiguracja jest niezgodna ze schematem), Agent zignoruje nieprawidłową konfigurację i będzie nadal korzystać z bieżącej konfiguracji.

### <a name="configuration-validation"></a>Weryfikacja konfiguracji

Azure Security Center dla agenta zabezpieczeń IoT raportuje bieżącą konfigurację w sekcji zgłoszonych właściwości tożsamości bliźniaczyej modułu **azureiotsecurity** .
Agent raportuje wszystkie dostępne właściwości, jeśli właściwość nie została ustawiona przez użytkownika, Agent zgłasza konfigurację domyślną.

Aby sprawdzić poprawność konfiguracji, Porównaj wartości ustawione w odpowiedniej sekcji z wartościami zgłoszonymi w sekcji zgłoszone.

W przypadku niezgodności między pożądanymi i zgłoszonymi właściwościami Agent nie mógł przeanalizować konfiguracji.

Sprawdź poprawność żądanych właściwości względem [schematu](https://aka.ms/iot-security-github-module-schema), Usuń błędy i ponownie Ustaw żądane właściwości.

> [!NOTE]
> Alert błędu konfiguracji zostanie wygenerowany z agenta w przypadku, gdy Agent nie mógł przeanalizować odpowiedniej konfiguracji.
> Porównaj zgłoszoną i pożądaną sekcję, aby zrozumieć, czy alert nadal dotyczy

## <a name="editing-a-property"></a>Edytowanie właściwości

Wszystkie właściwości niestandardowe muszą być ustawione wewnątrz obiektu konfiguracji agenta w ramach sznurka modułu **azureiotsecurity** .
Aby użyć domyślnej wartości właściwości, Usuń właściwość z obiektu konfiguracji.

### <a name="setting-a-property"></a>Ustawianie właściwości

1. W IoT Hub Znajdź i wybierz urządzenie, które chcesz zmienić.

1. Kliknij urządzenie, a następnie w module **azureiotsecurity** .

1. Kliknij pozycję **identyfikator modułu — sznurek**.

1. Edytuj właściwości, które chcesz zmienić w module zabezpieczeń.

   Na przykład, aby skonfigurować zdarzenia połączeń jako wysoki priorytet i zbierać zdarzenia o wysokim priorytecie co 7 minut, użyj poniższej konfiguracji.

    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. Kliknij pozycję **Zapisz**.

### <a name="using-a-default-value"></a>Używanie wartości domyślnej

Aby użyć domyślnej wartości właściwości, Usuń właściwość z obiektu konfiguracji.

## <a name="default-properties"></a>Właściwości domyślne

Poniższa tabela zawiera właściwości, które mają być kontrolowane Azure Security Center dla agentów zabezpieczeń IoT.

Wartości domyślne są dostępne w odpowiednim schemacie w serwisie [GitHub](https\://aka.ms/iot-security-module-default).

| Nazwa| Stan | Prawidłowe wartości| Wartości domyślne| Opis |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Wymagane: FAŁSZ |Prawidłowe wartości: czas trwania w formacie ISO 8601 |Wartość domyślna: PT7M |Maksymalny przedział czasu przed wysłaniem komunikatów o wysokim priorytecie.|
|lowPriorityMessageFrequency |Wymagane: FAŁSZ|Prawidłowe wartości: czas trwania w formacie ISO 8601 |Wartość domyślna: PT5H |Maksymalny czas przed wysłaniem komunikatów o niskim priorytecie.|
|snapshotFrequency |Wymagaj: false|Prawidłowe wartości: czas trwania w formacie ISO 8601 |Wartość domyślna PT13H |Interwał tworzenia migawek stanu urządzenia.|
|maxLocalCacheSizeInBytes |Wymagane: FAŁSZ |Prawidłowe wartości: |Wartość domyślna: 2560000, większa niż 8192 | Maksymalny rozmiar magazynu (w bajtach) dozwolony dla pamięci podręcznej komunikatów agenta. Maksymalna ilość miejsca na przechowywanie komunikatów na urządzeniu przed wysłaniem komunikatów.|
|maxMessageSizeInBytes |Wymagane: FAŁSZ |Prawidłowe wartości: liczba dodatnia większa niż 8192, mniejsza niż 262144 |Wartość domyślna: 204800 |Maksymalny dozwolony rozmiar agenta do komunikatu w chmurze. To ustawienie Steruje ilością maksymalnych danych wysyłanych w poszczególnych wiadomościach. |
|eventPriority $ {EventName} |Wymagane: FAŁSZ |Prawidłowe wartości: wysoka, niska, wyłączona |Wartości domyślne: |Priorytet każdego zdarzenia generowanego przez agenta |

### <a name="supported-security-events"></a>Obsługiwane zdarzenia zabezpieczeń

|Nazwa zdarzenia| PropertyName | Wartość domyślna| Zdarzenie migawki| Szczegóły stanu  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Zdarzenie diagnostyczne|eventPriorityDiagnostic| Wyłączone| Fałsz| Zdarzenia diagnostyczne powiązane z agentem. To zdarzenie służy do pełnego rejestrowania.|
|Błąd konfiguracji |eventPriorityConfigurationError |Małe |Fałsz |Agent nie może przeanalizować konfiguracji. Sprawdź konfigurację względem schematu.|
|Dane statystyczne dotyczące porzuconego zdarzenia |eventPriorityDroppedEventsStatistics |Małe |Prawda|Statystyki zdarzeń związanych z agentem. |
|Podłączony sprzęt|eventPriorityConnectedHardware |Małe |Prawda |Migawka wszystkich urządzeń podłączonych do urządzenia.|
|Nasłuchiwanie portów|eventPriorityListeningPorts |Wysoki |Prawda |Migawka wszystkich otwartych portów nasłuchiwania na urządzeniu.|
|Tworzenie procesu |eventPriorityProcessCreate |Małe |Fałsz |Przeprowadza inspekcję tworzenia procesu na urządzeniu.|
|Zakończenie procesu|eventPriorityProcessTerminate |Małe |Fałsz |Przeprowadza inspekcję zakończenia procesu na urządzeniu.|
|Informacje o systemie |eventPrioritySystemInformation |Małe |Prawda |Migawka informacji o systemie (na przykład: system operacyjny lub procesor CPU).|
|Użytkownicy lokalni| eventPriorityLocalUsers |Wysoki |Prawda|Migawka zarejestrowanych użytkowników lokalnych w systemie. |
|Zaloguj się|  eventPriorityLogin |Wysoki|Fałsz|Przeprowadź inspekcję zdarzeń logowania na urządzeniu (logowanie lokalne i zdalne).|
|Tworzenie połączenia |eventPriorityConnectionCreate|Małe|Fałsz|Przeprowadza inspekcję połączeń TCP utworzonych do i z urządzenia. |
|Konfiguracja zapory| eventPriorityFirewallConfiguration|Małe|Prawda|Migawka konfiguracji zapory urządzenia (reguły zapory). |
|Linia bazowa systemu operacyjnego| eventPriorityOSBaseline| Małe|Prawda|Migawka kontroli linii bazowej systemu operacyjnego urządzenia.|
|

## <a name="next-steps"></a>Następne kroki

- [Opis Azure Security Center dotyczących zaleceń IoT](concept-recommendations.md)
- [Eksplorowanie Azure Security Center dotyczących alertów IoT](concept-security-alerts.md)
- [Dostęp do nieprzetworzonych danych zabezpieczeń](how-to-security-data-access.md)
