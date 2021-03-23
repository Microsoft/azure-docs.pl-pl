---
title: Usługa Defender-IoT-Micro-Agent for Azure RTO — & wbudowana, dostosowywalne alerty i zalecenia
description: Dowiedz się więcej o alertach zabezpieczeń i zalecanych korygowaniu za pomocą usługi Azure IoT Defender-IoT-Micro-RTO.
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: cfbd411617a0b80f4857e08f9803b34b80b873d4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784683"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Usługa Defender-IoT-Micro-Agent na potrzeby alertów zabezpieczeń i zaleceń usługi Azure RTO (wersja zapoznawcza)

Usługa Defender-IoT-Micro-Agent for Azure RTO stale analizuje Twoje rozwiązanie IoT przy użyciu zaawansowanych analiz i analizy zagrożeń, aby ostrzec o potencjalnie złośliwych działaniach i podejrzanych modyfikacjach systemu. Możesz również tworzyć niestandardowe alerty na podstawie wiedzy o oczekiwanym zachowaniu i rozdziałach urządzeń.

Alert usługi Defender-IoT-Micro-Agent for Azure RTO działa jako wskaźnik potencjalnego naruszenia i należy go zbadać i skorygować. Zalecenie Defender-IoT-Micro-Agent for Azure RTO identyfikuje słabe zabezpieczenia stan do skorygowania i zaktualizowania. 

W tym artykule znajdziesz listę wbudowanych alertów i zaleceń, które są wyzwalane w oparciu o zakresy domyślne i można je dostosowywać do własnych wartości na podstawie oczekiwanych lub podstawowych zachowań. 

Aby uzyskać więcej informacji na temat sposobu działania dostosowywania alertów w usłudze Defender for IoT, zobacz [dostosowywalne alerty](concept-customizable-security-alerts.md). W poniższych tabelach opisano szczegółowe alerty i zalecenia dostępne do dostosowania podczas korzystania z usługi Defender-IoT-Micro-Agent for Azure RTO. 

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-security-alerts"></a>Defender-IoT-Micro-Agent for Azure RTO — obsługiwane alerty zabezpieczeń

### <a name="device-related-security-alerts"></a>Alerty zabezpieczeń dotyczące urządzeń

|Działanie alertu zabezpieczeń związanego z urządzeniem  |Nazwa alertu  |
|---------|---------|
|Adres IP| Wykryto komunikację z podejrzanym adresem IP|
|Odcisk palca certyfikatu urządzenia X. 509|Niezgodność odcisku palca certyfikatu urządzenia X. 509|
|Certyfikat X. 509| Certyfikat X. 509 wygasł|
|Token SAS| Wygasły token SAS|
|Token SAS| Nieprawidłowa sygnatura tokenu sygnatury dostępu współdzielonego|

### <a name="iot-hub-related-security-alerts"></a>Alerty zabezpieczeń dotyczące IoT Hub

|Działanie alertu zabezpieczeń IoT Hub  |Nazwa alertu  |
|---------|---------|
|Dodawanie certyfikatu    |  Wykryto nieudaną próbę dodania certyfikatu do IoT Hub       |
|Dodawanie lub edytowanie ustawień diagnostycznych    | Wykryto próbę dodania lub edycji ustawienia diagnostycznego IoT Hub      |
|Usuwanie certyfikatu    |  Wykryto nieudaną próbę usunięcia certyfikatu z IoT Hub       |
|Usuń ustawienie diagnostyczne    |  Wykryto próbę usunięcia ustawienia diagnostycznego z IoT Hub      |
|Usunięto certyfikat    | Wykryto usunięcie certyfikatu z IoT Hub        |
|Nowy certyfikat     |  Wykryto dodanie nowego certyfikatu do IoT Hub       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-customizable-alerts"></a>Defender-IoT-Micro-Agent for Azure RTO — obsługiwane dostosowywalne alerty

### <a name="device-related-customizable-alerts"></a>Dostosowywalne alerty powiązane z urządzeniem

|Aktywność związana z urządzeniem |Nazwa alertu  |
|---------|---------|
|Aktywne połączenia|Liczba aktywnych połączeń jest poza dozwolonym zakresem|
|Komunikaty z chmury do urządzeń w protokole **MQTT**|Liczba komunikatów z chmury do urządzeń w protokole **MQTT** nie jest w dozwolonym zakresie|
|Połączenie wychodzące| Połączenie wychodzące z niedozwolonym adresem IP|

### <a name="hub-related-customizable-alerts"></a>Dostosowywalne alerty powiązane z centrum 

|Aktywność związana z centrum  |Nazwa alertu  |
|---------|---------|
|Przeczyszczanie kolejki poleceń     |  Liczba przeczyszczania kolejki poleceń poza dozwolonym zakresem       |
|Komunikaty z chmury do urządzeń w protokole **MQTT**    |  Liczba komunikatów z chmury do urządzeń w protokole **MQTT** poza dozwolonym zakresem       |
|Urządzenie do komunikatów w chmurze w protokole **MQTT**    | Liczba urządzeń do komunikatów w chmurze w protokole **MQTT** poza dozwolonym zakresem        |
|Wywołanie metody bezpośredniej     |  Liczba wywoływanych metod bezpośrednich poza dozwolonym zakresem       |
|Odrzucono chmurę do komunikatów urządzenia w protokole **MQTT**     |   Liczba odrzuconych komunikatów z chmury do urządzeń w protokole **MQTT** poza dozwolonym zakresem      |
|Aktualizacje modułów bliźniaczych     |  Liczba aktualizacji modułów bliźniaczych poza dozwolonym zakresem       |
|Nieautoryzowane operacje    |  Liczba nieautoryzowanych operacji poza dozwolonym zakresem       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-recommendations"></a>Defender-IoT-Micro-Agent for Azure RTO — obsługiwane rekomendacje

### <a name="device-related-recommendations"></a>Zalecenia dotyczące urządzenia

|Aktywność związana z urządzeniem  |Nazwa zalecenia |
|---------|---------|
|Poświadczenia uwierzytelniania    |  Identyczne poświadczenia uwierzytelniania używane przez wiele urządzeń       |

### <a name="hub-related-recommendations"></a>Zalecenia dotyczące centrów

|Działanie związane z IoT Hub  |Nazwa zalecenia |
|---------|---------|
|Zasady filtrowania adresów IP   |  Domyślne zasady filtru IP powinny być ustawione na **Odmów**  |
|Reguła filtrowania adresów IP| Reguła filtru IP zawiera duży zakres adresów IP|
|Dzienniki diagnostyczne|Sugestia włączenia dzienników diagnostycznych w IoT Hub|

### <a name="all-defender-for-iot-alerts-and-recommendations"></a>Wszystkie alerty i zalecenia dotyczące usługi Defender dla IoT

Aby uzyskać pełną listę wszystkich alertów i zaleceń związanych z usługą Defender, zobacz [alerty zabezpieczeń](concept-security-alerts.md)IoT, [zalecenia dotyczące](concept-recommendations.md)zabezpieczeń IoT.

## <a name="next-steps"></a>Następne kroki

- [Szybki Start: Defender-IoT-Micro-Agent for Azure RTO](quickstart-azure-rtos-security-module.md)
- [Konfigurowanie i dostosowywanie usługi Defender-IoT-Micro-Agent for Azure RTO](how-to-azure-rtos-security-module.md)
- Zapoznaj się z [interfejsem API usługi Defender-IoT-Micro-Agent for Azure RTO](azure-rtos-security-module-api.md)