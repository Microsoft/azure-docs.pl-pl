---
title: Niestandardowe alerty zabezpieczeń oparte na agentach
titleSuffix: Azure Defender for IoT
description: Dowiedz się więcej o dostosowywaniu alertów zabezpieczeń i zalecanych korygowaniu za pomocą usługi Defender dla funkcji i usług urządzenia IoT.
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
ms.openlocfilehash: 5d0eeb046d7a4ba474a1ed4a2cfb07a07f1c3888
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493309"
---
# <a name="defender-for-iot-devices-custom-security-alerts"></a>Niestandardowe alerty zabezpieczeń urządzeń usługi Defender for IoT

Usługa Defender for IoT stale analizuje Twoje rozwiązanie IoT za pomocą zaawansowanej analizy i analizy zagrożeń, aby ostrzec użytkownika o złośliwych działaniach.

Zachęcamy do tworzenia niestandardowych alertów na podstawie wiedzy o oczekiwanym zachowaniu urządzenia, aby zapewnić, że alerty działają jak najbardziej wydajne wskaźniki potencjalnego naruszenia w unikatowym wdrożeniu organizacji i poziomie.

Następujące listy alertów usługi Defender for IoT są definiowane przez użytkownika w oparciu o oczekiwane zachowanie urządzenia IoT. Aby uzyskać więcej informacji na temat sposobu dostosowywania poszczególnych alertów, zobacz [tworzenie alertów niestandardowych](quickstart-create-custom-alerts.md).

## <a name="agent-based-security-custom-alerts"></a>Niestandardowe alerty zabezpieczeń oparte na agentach

| Ważność | Nazwa alertu | Źródło danych | Opis | Sugerowane korygowanie |
|--|--|--|--|--|
| Niski | Alert niestandardowy — liczba aktywnych połączeń znajduje się poza dozwolonym zakresem | Klasyczna usługa Defender-IoT-Micro-Agent, Azure RTO | Liczba aktywnych połączeń w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. | Sprawdź dzienniki urządzeń. Dowiedz się, skąd pochodzi połączenie i ustal, czy jest ono niegroźne lub złośliwe. Jeśli jest to złośliwe, Usuń możliwe złośliwe oprogramowanie i poznanie źródła. Jeśli niegroźne, Dodaj źródło do listy dozwolonych połączeń. |
| Niski | Alert niestandardowy — połączenie wychodzące utworzone w niedozwolonym adresie IP | Klasyczna usługa Defender-IoT-Micro-Agent, Azure RTO | Połączenie wychodzące zostało utworzone w adresie IP spoza listy dozwolonych adresów IP. | Sprawdź dzienniki urządzeń. Dowiedz się, skąd pochodzi połączenie i ustal, czy jest ono niegroźne lub złośliwe. Jeśli jest to złośliwe, Usuń możliwe złośliwe oprogramowanie i poznanie źródła. Jeśli niegroźne, Dodaj źródło do listy dozwolonych adresów IP. |
| Niski | Alert niestandardowy — liczba nieudanych logowań lokalnych jest poza dozwolonym zakresem | Klasyczna usługa Defender-IoT-Micro-Agent, Azure RTO | Liczba nieudanych logowań lokalnych w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |  |
| Niski | Alert niestandardowy — Logowanie użytkownika, który nie znajduje się na liście dozwolonych użytkowników | Klasyczna usługa Defender-IoT-Micro-Agent, Azure RTO | Użytkownik lokalny spoza listy dozwolonych użytkowników zalogował się na urządzeniu. | Jeśli zapisujesz dane pierwotne, przejdź do swojego konta usługi log Analytics i Użyj danych do zbadania urządzenia, Zidentyfikuj źródło, a następnie napraw listę dozwolonych/zablokowanych dla tych ustawień. Jeśli obecnie nie zapisujesz danych pierwotnych, przejdź do urządzenia i popraw listę dozwolonych/zablokowanych dla tych ustawień. |
| Niski | Alert niestandardowy — proces został wykonany, który jest niedozwolony | Klasyczna usługa Defender-IoT-Micro-Agent, Azure RTO | Proces, który jest niedozwolony, został wykonany na urządzeniu. | Jeśli zapisujesz dane pierwotne, przejdź do swojego konta usługi log Analytics i Użyj danych do zbadania urządzenia, Zidentyfikuj źródło, a następnie napraw listę dozwolonych/zablokowanych dla tych ustawień. Jeśli obecnie nie zapisujesz danych pierwotnych, przejdź do urządzenia i popraw listę dozwolonych/zablokowanych dla tych ustawień. |
|

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [dostosować alert](quickstart-create-custom-alerts.md)
- Usługa Defender for IoT [— Omówienie](overview.md)
- Dowiedz się, jak [uzyskać dostęp do danych zabezpieczeń](how-to-security-data-access.md)
- Dowiedz się więcej [na temat badania urządzenia](how-to-investigate-device.md)
