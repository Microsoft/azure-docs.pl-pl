---
title: Dostosowywalne alerty zabezpieczeń
description: Dowiedz się więcej na temat dostosowywalnych alertów zabezpieczeń i zalecanych korekt przy użyciu usługi Defender dla funkcji i usług IoT.
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
ms.date: 03/04/2020
ms.author: mlottner
ms.openlocfilehash: 5a5b9182081e267f8e20cb0818202b9cb5ecd904
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939531"
---
# <a name="defender-for-iot-customizable-security-alerts"></a>Alerty zabezpieczeń umożliwiające dostosowanie usługi Defender for IoT

Usługa Defender for IoT stale analizuje Twoje rozwiązanie IoT za pomocą zaawansowanej analizy i analizy zagrożeń, aby ostrzec użytkownika o złośliwych działaniach.

Zachęcamy do tworzenia niestandardowych alertów na podstawie wiedzy o oczekiwanym zachowaniu urządzenia, aby zapewnić, że alerty działają jak najbardziej wydajne wskaźniki potencjalnego naruszenia w unikatowym wdrożeniu organizacji i poziomie.

Poniższa lista alertów usługi Defender for IoT jest określana na podstawie oczekiwanych IoT Hub i/lub zachowania urządzenia. Aby uzyskać więcej informacji na temat sposobu dostosowywania poszczególnych alertów, zobacz [tworzenie alertów niestandardowych](quickstart-create-custom-alerts.md).

## <a name="iot-hub-alerts-available-for-customization"></a>IoT Hub alerty dostępne do dostosowania

| Ważność | Nazwa alertu | Źródło danych | Opis | Sugerowane korygowanie|
|---|---|---|---|---|
| Niski      | Alert niestandardowy — liczba komunikatów z chmury do urządzeń w protokole AMQP jest poza dozwolonym zakresem          | Usługa IoT Hub     | Liczba komunikatów z chmury do urządzeń (protokół AMQP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.||
| Niski      | Alert niestandardowy — Liczba odrzuconych chmur do komunikatów urządzenia w protokole AMQP jest poza dozwolonym zakresem | Usługa IoT Hub     | Liczba komunikatów z chmury do urządzeń (protokół AMQP) odrzuconych przez urządzenie w określonym przedziale czasu poza aktualnie skonfigurowanym i dozwolonym zakresem.||
| Niski      | Alert niestandardowy — liczba urządzeń z komunikatami w chmurze w protokole AMQP jest poza dozwolonym zakresem      | Usługa IoT Hub     | Ilość urządzenia do komunikatów w chmurze (protokół AMQP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|   |
| Niski      | Alert niestandardowy — liczba wywoływanych metod bezpośrednich jest poza dozwolonym zakresem | Usługa IoT Hub     | Liczba metod bezpośrednich wywoływanych w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.||
| Niski      | Alert niestandardowy — liczba operacji przekazywania plików jest poza dozwolonym zakresem | Usługa IoT Hub     | Ilość przekazywania plików w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.| |
| Niski      | Alert niestandardowy — liczba komunikatów z chmury do urządzeń w protokole HTTP jest poza dozwolonym zakresem | Usługa IoT Hub     | Ilość komunikatów z chmury do urządzeń (protokół HTTP) w przedziale czasu nie jest w skonfigurowanym dozwolonym zakresie                                  |
| Niski      | Alert niestandardowy — Liczba odrzuconych chmur do komunikatów urządzenia w protokole HTTP nie jest w dozwolonym zakresie | Usługa IoT Hub     | Ilość komunikatów z chmury do urządzeń (protokół HTTP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |
| Niski      | Alert niestandardowy — liczba urządzeń z komunikatami w chmurze w protokole HTTP jest poza dozwolonym zakresem | Usługa IoT Hub| Ilość urządzenia do komunikatów w chmurze (protokół HTTP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|    |
| Niski      | Alert niestandardowy — liczba komunikatów z chmury do urządzeń w protokole MQTT jest poza dozwolonym zakresem | Usługa IoT Hub     | Ilość komunikatów z chmury do urządzeń (protokół MQTT) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|   |
| Niski      | Alert niestandardowy — Liczba odrzuconych chmur do komunikatów urządzenia w protokole MQTT jest poza dozwolonym zakresem | Usługa IoT Hub     | Ilość komunikatów z chmury do urządzeń (protokół MQTT) odrzuconych przez urządzenie w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |
| Niski      | Alert niestandardowy — liczba urządzeń z komunikatami w chmurze w protokole MQTT jest poza dozwolonym zakresem          | Usługa IoT Hub     | Ilość urządzenia do komunikatów w chmurze (protokół MQTT) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|
| Niski      | Alert niestandardowy — liczba przeczyszczania kolejki poleceń jest poza dozwolonym zakresem                               | Usługa IoT Hub     | Ilość przeczyszczania kolejki poleceń w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.||
| Niski      | Alert niestandardowy — liczba aktualizacji bliźniaczych modułu jest poza dozwolonym zakresem                                       | Usługa IoT Hub     | Liczba aktualizacji sieci dwuosiowych modułu w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|
| Niski      | Alert niestandardowy — liczba niedozwolonych operacji jest poza dozwolonym zakresem  | Usługa IoT Hub     | Ilość niedozwolonych operacji w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|
|

## <a name="agent-alerts-available-for-customization"></a>Alerty agentów dostępne do dostosowania

| Ważność | Nazwa alertu | Źródło danych | Opis | Sugerowane korygowanie|
|---|---|---|---|---|
| Niski      | Alert niestandardowy — liczba aktywnych połączeń jest poza dozwolonym zakresem  | Agent       | Liczba aktywnych połączeń w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|  Sprawdź dzienniki urządzeń. Dowiedz się, skąd pochodzi połączenie i ustal, czy jest ono niegroźne lub złośliwe. Jeśli jest to złośliwe, Usuń możliwe złośliwe oprogramowanie i poznanie źródła. Jeśli niegroźne, Dodaj źródło do listy dozwolonych połączeń.  |
| Niski      | Alert niestandardowy — połączenie wychodzące utworzone w niedozwolonym adresie IP                             | Agent       | Połączenie wychodzące zostało utworzone w adresie IP spoza listy dozwolonych adresów IP. |Sprawdź dzienniki urządzeń. Dowiedz się, skąd pochodzi połączenie i ustal, czy jest ono niegroźne lub złośliwe. Jeśli jest to złośliwe, Usuń możliwe złośliwe oprogramowanie i poznanie źródła. Jeśli niegroźne, Dodaj źródło do listy dozwolonych adresów IP.                        |
| Niski      | Alert niestandardowy — liczba nieudanych logowań lokalnych jest poza dozwolonym zakresem                               | Agent       | Liczba nieudanych logowań lokalnych w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |   |
| Niski      | Niestandardowy alert — Logowanie użytkownika, który nie znajduje się na liście dozwolonych użytkowników | Agent       | Użytkownik lokalny spoza listy dozwolonych użytkowników zalogował się na urządzeniu.|  Jeśli zapisujesz dane pierwotne, przejdź do swojego konta usługi log Analytics i Użyj danych do zbadania urządzenia, Zidentyfikuj źródło, a następnie napraw listę dozwolonych/zablokowanych dla tych ustawień. Jeśli obecnie nie zapisujesz danych pierwotnych, przejdź do urządzenia i popraw listę dozwolonych/zablokowanych dla tych ustawień.|
| Niski      | Alert niestandardowy — proces został wykonany, który jest niedozwolony | Agent       | Proces, który jest niedozwolony, został wykonany na urządzeniu. |Jeśli zapisujesz dane pierwotne, przejdź do swojego konta usługi log Analytics i Użyj danych do zbadania urządzenia, Zidentyfikuj źródło, a następnie napraw listę dozwolonych/zablokowanych dla tych ustawień. Jeśli obecnie nie zapisujesz danych pierwotnych, przejdź do urządzenia i popraw listę dozwolonych/zablokowanych dla tych ustawień.  |
|

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [dostosować alert](quickstart-create-custom-alerts.md)
- Usługa Defender for IoT [— Omówienie](overview.md)
- Dowiedz się, jak [uzyskać dostęp do danych zabezpieczeń](how-to-security-data-access.md)
- Dowiedz się więcej [na temat badania urządzenia](how-to-investigate-device.md)
