---
title: Niestandardowe alerty zabezpieczeń dla IoT Hub
description: Dowiedz się więcej o dostosowywaniu alertów zabezpieczeń i zalecanych korygowaniu za pomocą usługi Defender dla funkcji i usług IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/16/2021
ms.author: shhazam
ms.openlocfilehash: 04198432f2b600a3c703d5e4f253656f116000db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636530"
---
# <a name="defender-for-iot-hub-custom-security-alerts"></a>Usługi Defender dla IoT Hub niestandardowe alerty zabezpieczeń

Usługa Defender for IoT stale analizuje Twoje rozwiązanie IoT za pomocą zaawansowanej analizy i analizy zagrożeń, aby ostrzec użytkownika o złośliwych działaniach.

Zachęcamy do tworzenia niestandardowych alertów na podstawie wiedzy o oczekiwanym zachowaniu urządzenia, aby zapewnić, że alerty działają jak najbardziej wydajne wskaźniki potencjalnego naruszenia w unikatowym wdrożeniu organizacji i poziomie.

Następujące listy alertów usługi Defender for IoT są definiowane w oparciu o oczekiwane zachowanie IoT Hub. Aby uzyskać więcej informacji na temat sposobu dostosowywania poszczególnych alertów, zobacz [tworzenie alertów niestandardowych](quickstart-create-custom-alerts.md).

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Wbudowane alerty niestandardowe w IoT Hub

| Ważność | Nazwa alertu | Źródło danych | Opis | Sugerowane korygowanie |
|--|--|--|--|--|
| Niski | Alert niestandardowy — liczba komunikatów z chmury do urządzeń w protokole AMQP jest poza dozwolonym zakresem | Usługa IoT Hub | Liczba komunikatów z chmury do urządzeń (protokół AMQP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |  |
| Niski | Alert niestandardowy — Liczba odrzuconych komunikatów z chmury do urządzenia w protokole AMQP jest poza dozwolonym zakresem | Usługa IoT Hub | Liczba komunikatów z chmury do urządzeń (protokół AMQP) odrzuconych przez urządzenie w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |  |
| Niski | Alert niestandardowy — liczba urządzeń z komunikatami w chmurze w protokole AMQP jest poza dozwolonym zakresem | Usługa IoT Hub | Ilość urządzenia do komunikatów w chmurze (protokół AMQP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |  |
| Niski | Alert niestandardowy — liczba wywoływanych metod bezpośrednich jest poza dozwolonym zakresem | Usługa IoT Hub | Liczba metod bezpośrednich wywoływanych w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |  |
| Niski | Alert niestandardowy — liczba operacji przekazywania plików jest poza dozwolonym zakresem | Usługa IoT Hub | Ilość przekazywania plików w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |  |
| Niski | Alert niestandardowy — liczba komunikatów z chmury do urządzeń w protokole HTTP jest poza dozwolonym zakresem | Usługa IoT Hub | Ilość komunikatów z chmury do urządzeń (protokół HTTP) w przedziale czasu nie jest w skonfigurowanym dozwolonym zakresie |
| Niski | Alert niestandardowy — Liczba odrzuconych komunikatów z chmury do urządzenia w protokole HTTP nie jest w dozwolonym zakresie | Usługa IoT Hub | Ilość komunikatów z chmury do urządzeń (protokół HTTP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |
| Niski | Alert niestandardowy — liczba urządzeń z komunikatami w chmurze w protokole HTTP jest poza dozwolonym zakresem | Usługa IoT Hub | Ilość urządzenia do komunikatów w chmurze (protokół HTTP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |  |
| Niski | Alert niestandardowy — liczba komunikatów z chmury do urządzeń w protokole MQTT jest poza dozwolonym zakresem | Usługa IoT Hub | Ilość komunikatów z chmury do urządzeń (protokół MQTT) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |  |
| Niski | Alert niestandardowy — Liczba odrzuconych komunikatów z chmury do urządzenia w protokole MQTT jest poza dozwolonym zakresem | Usługa IoT Hub | Ilość komunikatów z chmury do urządzeń (protokół MQTT) odrzuconych przez urządzenie w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |
| Niski | Alert niestandardowy — liczba urządzeń z komunikatami w chmurze w protokole MQTT jest poza dozwolonym zakresem | Usługa IoT Hub | Ilość urządzenia do komunikatów w chmurze (protokół MQTT) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |
| Niski | Alert niestandardowy — liczba przeczyszczania kolejki poleceń, które znajdują się poza dozwolonym zakresem | Usługa IoT Hub | Ilość przeczyszczania kolejki poleceń w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |  |
| Niski | Alert niestandardowy — liczba aktualizacji bliźniaczych modułu znajduje się poza dozwolonym zakresem | Usługa IoT Hub | Liczba aktualizacji sieci dwuosiowych modułu w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |
| Niski | Alert niestandardowy — liczba niedozwolonych operacji jest poza dozwolonym zakresem | Usługa IoT Hub | Ilość niedozwolonych operacji w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [dostosować alert](quickstart-create-custom-alerts.md)
- Usługa Defender for IoT [— Omówienie](overview.md)
- Dowiedz się, jak [uzyskać dostęp do danych zabezpieczeń](how-to-security-data-access.md)
- Dowiedz się więcej [na temat badania urządzenia](how-to-investigate-device.md)
