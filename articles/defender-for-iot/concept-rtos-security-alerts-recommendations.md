---
title: Moduł zabezpieczeń dla usługi Azure RTO wbudowane & dostosowywalne alerty i zalecenia
description: Dowiedz się więcej o alertach zabezpieczeń i zalecanych korygowaniu przy użyciu modułu Azure IoT Security module — RTO.
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
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: cf4924f8a9b97487e64e12ab80df92f2b2a81de2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939516"
---
# <a name="security-module-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Moduł zabezpieczeń dla alertów zabezpieczeń i zaleceń usługi Azure RTO (wersja zapoznawcza)

Moduł zabezpieczeń dla usługi Azure RTO stale analizuje Twoje rozwiązanie IoT przy użyciu zaawansowanych analiz i analizy zagrożeń, aby ostrzec o potencjalnie złośliwych działaniach i podejrzanych modyfikacjach systemu. Możesz również tworzyć niestandardowe alerty na podstawie wiedzy o oczekiwanym zachowaniu i rozdziałach urządzeń.

Moduł zabezpieczeń dla alertu usługi Azure RTO działa jako wskaźnik potencjalnego naruszenia i należy go zbadać i skorygować. Zalecenie modułu zabezpieczeń dla platformy Azure RTO identyfikuje słabe stan zabezpieczeń, które mają zostać skorygowane i zaktualizowane. 

W tym artykule znajdziesz listę wbudowanych alertów i zaleceń, które są wyzwalane w oparciu o zakresy domyślne i można je dostosowywać do własnych wartości na podstawie oczekiwanych lub podstawowych zachowań. 

Aby uzyskać więcej informacji na temat sposobu działania dostosowywania alertów w usłudze Defender for IoT, zobacz [dostosowywalne alerty](concept-customizable-security-alerts.md). Określone alerty i zalecenia dostępne do dostosowania podczas korzystania z modułu zabezpieczeń usługi Azure RTO są szczegółowo opisane w poniższych tabelach. 

## <a name="security-module-for-azure-rtos-supported-security-alerts"></a>Moduł zabezpieczeń dla usługi Azure RTO — obsługiwane alerty zabezpieczeń

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

## <a name="security-module-for-azure-rtos-supported-customizable-alerts"></a>Moduł zabezpieczeń dla usługi Azure RTO obsługuje dostosowywalne alerty

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

## <a name="security-module-for-azure-rtos-supported-recommendations"></a>Moduł zabezpieczeń dla usługi Azure RTO — zalecenia obsługiwane

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

- [Szybki Start: moduł zabezpieczeń dla usługi Azure RTO](quickstart-azure-rtos-security-module.md)
- [Konfigurowanie i dostosowywanie modułu zabezpieczeń dla usługi Azure RTO](how-to-azure-rtos-security-module.md)
- Zapoznaj się z [modułem zabezpieczeń dla interfejsu API usługi Azure RTO](azure-rtos-security-module-api.md)