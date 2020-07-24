---
title: Dostosowywalne alerty zabezpieczeń
description: Dowiedz się więcej o dostosowywaniu alertów zabezpieczeń i zalecanych korygowaniu za pomocą Azure Security Center dla funkcji i usług IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2020
ms.author: mlottner
ms.openlocfilehash: f676c4129b79499eb9ed524821a336b3859dbb3c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004855"
---
# <a name="azure-security-center-for-iot-customizable-security-alerts"></a>Azure Security Center do dostosowywalnych alertów zabezpieczeń usługi IoT

Azure Security Center IoT stale analizuje Twoje rozwiązanie IoT przy użyciu zaawansowanych analiz i analizy zagrożeń, aby ostrzec użytkownika o złośliwych działaniach.

Zachęcamy do tworzenia niestandardowych alertów na podstawie wiedzy o oczekiwanym zachowaniu urządzenia, aby zapewnić, że alerty działają jak najbardziej wydajne wskaźniki potencjalnego naruszenia w unikatowym wdrożeniu organizacji i poziomie.

Poniższa lista Azure Security Center dla alertów IoT jest definiowanych przez użytkownika w oparciu o oczekiwane IoT Hub i/lub zachowanie urządzeń. Aby uzyskać więcej informacji na temat sposobu dostosowywania poszczególnych alertów, zobacz [tworzenie alertów niestandardowych](quickstart-create-custom-alerts.md).

## <a name="iot-hub-alerts-available-for-customization"></a>IoT Hub alerty dostępne do dostosowania

| Ważność | Nazwa alertu | Źródło danych | Opis | Sugerowane korygowanie|
|---|---|---|---|---|
| Nis.      | Alert niestandardowy — liczba komunikatów z chmury do urządzeń w protokole AMQP jest poza dozwolonym zakresem          | IoT Hub     | Liczba komunikatów z chmury do urządzeń (protokół AMQP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.||
| Nis.      | Alert niestandardowy — Liczba odrzuconych chmur do komunikatów urządzenia w protokole AMQP jest poza dozwolonym zakresem | IoT Hub     | Liczba komunikatów z chmury do urządzeń (protokół AMQP) odrzuconych przez urządzenie w określonym przedziale czasu poza aktualnie skonfigurowanym i dozwolonym zakresem.||
| Nis.      | Alert niestandardowy — liczba urządzeń z komunikatami w chmurze w protokole AMQP jest poza dozwolonym zakresem      | IoT Hub     | Ilość urządzenia do komunikatów w chmurze (protokół AMQP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|   |
| Nis.      | Alert niestandardowy — liczba wywoływanych metod bezpośrednich jest poza dozwolonym zakresem | IoT Hub     | Liczba metod bezpośrednich wywoływanych w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.||
| Nis.      | Alert niestandardowy — liczba operacji przekazywania plików jest poza dozwolonym zakresem | IoT Hub     | Ilość przekazywania plików w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.| |
| Nis.      | Alert niestandardowy — liczba komunikatów z chmury do urządzeń w protokole HTTP jest poza dozwolonym zakresem | IoT Hub     | Ilość komunikatów z chmury do urządzeń (protokół HTTP) w przedziale czasu nie jest w skonfigurowanym dozwolonym zakresie                                  |
| Nis.      | Alert niestandardowy — Liczba odrzuconych chmur do komunikatów urządzenia w protokole HTTP nie jest w dozwolonym zakresie | IoT Hub     | Ilość komunikatów z chmury do urządzeń (protokół HTTP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |
| Nis.      | Alert niestandardowy — liczba urządzeń z komunikatami w chmurze w protokole HTTP jest poza dozwolonym zakresem | IoT Hub| Ilość urządzenia do komunikatów w chmurze (protokół HTTP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|    |
| Nis.      | Alert niestandardowy — liczba komunikatów z chmury do urządzeń w protokole MQTT jest poza dozwolonym zakresem | IoT Hub     | Ilość komunikatów z chmury do urządzeń (protokół MQTT) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|   |
| Nis.      | Alert niestandardowy — Liczba odrzuconych chmur do komunikatów urządzenia w protokole MQTT jest poza dozwolonym zakresem | IoT Hub     | Ilość komunikatów z chmury do urządzeń (protokół MQTT) odrzuconych przez urządzenie w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |
| Nis.      | Alert niestandardowy — liczba urządzeń z komunikatami w chmurze w protokole MQTT jest poza dozwolonym zakresem          | IoT Hub     | Ilość urządzenia do komunikatów w chmurze (protokół MQTT) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|
| Nis.      | Alert niestandardowy — liczba przeczyszczania kolejki poleceń jest poza dozwolonym zakresem                               | IoT Hub     | Ilość przeczyszczania kolejki poleceń w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.||
| Nis.      | Alert niestandardowy — liczba aktualizacji bliźniaczych modułu jest poza dozwolonym zakresem                                       | IoT Hub     | Liczba aktualizacji sieci dwuosiowych modułu w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|
| Nis.      | Alert niestandardowy — liczba niedozwolonych operacji jest poza dozwolonym zakresem  | IoT Hub     | Ilość niedozwolonych operacji w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|
|

## <a name="agent-alerts-available-for-customization"></a>Alerty agentów dostępne do dostosowania

| Ważność | Nazwa alertu | Źródło danych | Opis | Sugerowane korygowanie|
|---|---|---|---|---|
| Nis.      | Alert niestandardowy — liczba aktywnych połączeń jest poza dozwolonym zakresem  | Agent       | Liczba aktywnych połączeń w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|  Sprawdź dzienniki urządzeń. Dowiedz się, skąd pochodzi połączenie i ustal, czy jest ono niegroźne lub złośliwe. Jeśli jest to złośliwe, Usuń możliwe złośliwe oprogramowanie i poznanie źródła. Jeśli niegroźne, Dodaj źródło do listy dozwolonych połączeń.  |
| Nis.      | Alert niestandardowy — połączenie wychodzące utworzone w niedozwolonym adresie IP                             | Agent       | Połączenie wychodzące zostało utworzone w adresie IP spoza listy dozwolonych adresów IP. |Sprawdź dzienniki urządzeń. Dowiedz się, skąd pochodzi połączenie i ustal, czy jest ono niegroźne lub złośliwe. Jeśli jest to złośliwe, Usuń możliwe złośliwe oprogramowanie i poznanie źródła. Jeśli niegroźne, Dodaj źródło do listy dozwolonych adresów IP.                        |
| Nis.      | Alert niestandardowy — liczba nieudanych logowań lokalnych jest poza dozwolonym zakresem                               | Agent       | Liczba nieudanych logowań lokalnych w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |   |
| Nis.      | Niestandardowy alert — Logowanie użytkownika, który nie znajduje się na liście dozwolonych użytkowników | Agent       | Użytkownik lokalny spoza listy dozwolonych użytkowników zalogował się na urządzeniu.|  Jeśli zapisujesz dane pierwotne, przejdź do swojego konta usługi log Analytics i Użyj danych do zbadania urządzenia, Zidentyfikuj źródło, a następnie napraw listę dozwolonych/zablokowanych dla tych ustawień. Jeśli obecnie nie zapisujesz danych pierwotnych, przejdź do urządzenia i popraw listę dozwolonych/zablokowanych dla tych ustawień.|
| Nis.      | Alert niestandardowy — proces został wykonany, który jest niedozwolony | Agent       | Proces, który jest niedozwolony, został wykonany na urządzeniu. |Jeśli zapisujesz dane pierwotne, przejdź do swojego konta usługi log Analytics i Użyj danych do zbadania urządzenia, Zidentyfikuj źródło, a następnie napraw listę dozwolonych/zablokowanych dla tych ustawień. Jeśli obecnie nie zapisujesz danych pierwotnych, przejdź do urządzenia i popraw listę dozwolonych/zablokowanych dla tych ustawień.  |
|

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [dostosować alert](quickstart-create-custom-alerts.md)
- Azure Security Center usługi IoT [— Omówienie](overview.md)
- Dowiedz się, jak [uzyskać dostęp do danych zabezpieczeń](how-to-security-data-access.md)
- Dowiedz się więcej [na temat badania urządzenia](how-to-investigate-device.md)
