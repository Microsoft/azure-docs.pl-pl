---
title: Wbudowana & lista alertów niestandardowych
description: Dowiedz się więcej o alertach zabezpieczeń i zalecanych korygowaniu za pomocą usługi Defender dla funkcji i usług IoT Hub.
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
ms.openlocfilehash: ef33851600c576494e4e0903c6ab8ffefc9a1a59
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636496"
---
# <a name="defender-for-iot-hub-security-alerts"></a>Alerty zabezpieczeń usługi Defender dla IoT Hub

Usługa Defender for IoT stale analizuje Twoje rozwiązanie IoT za pomocą zaawansowanej analizy i analizy zagrożeń, aby ostrzec użytkownika o złośliwych działaniach.
Ponadto można tworzyć niestandardowe alerty na podstawie wiedzy o oczekiwanym zachowaniu urządzenia.
Alert działa jako wskaźnik potencjalnego naruszenia i należy go zbadać i skorygować.

W tym artykule znajduje się lista wbudowanych alertów, które mogą być wyzwalane na IoT Hub.
Oprócz wbudowanych alertów usługa Defender for IoT umożliwia Definiowanie niestandardowych alertów na podstawie oczekiwanych IoT Hub i/lub zachowań urządzeń.
Aby uzyskać więcej informacji, zobacz [dostosowywalne alerty](concept-customizable-security-alerts.md).

## <a name="built-in-alerts-for-iot-hub"></a>Wbudowane alerty dla IoT Hub

| Ważność | Nazwa | Opis | Sugerowane korygowanie |
|--|--|--|--|
| **Średnia** ważność |  |  |  |
| Dodano nowy certyfikat do IoT Hub | Śred. | Dodano certyfikat o nazwie \' % {DescCertificateName} \' do IoT Hub \' % {DescIoTHubName} \' . Jeśli ta akcja została wykonana przez nieautoryzowaną stronę, może to oznaczać złośliwe działanie. | 1. Upewnij się, że certyfikat został dodany przez autoryzowaną stronę. <br> 2. Jeśli nie została dodana przez autoryzowaną stronę, Usuń certyfikat i przeeskalacjj alertu do zespołu ds. zabezpieczeń. |
| Certyfikat został usunięty z IoT Hub | Śred. | Certyfikat o nazwie \' % {DescCertificateName} \' został usunięty z IoT Hub \' % {DescIoTHubName} \' . Jeśli ta akcja została wykonana przez nieautoryzowaną stronę, może to oznaczać złośliwe działanie. | 1. Upewnij się, że certyfikat został usunięty przez autoryzowaną stronę. <br> 2. Jeśli certyfikat nie został usunięty przez autoryzowaną stronę, należy dodać certyfikat ponownie i eskalować alert do zespołu ds. zabezpieczeń. |
| Wykryto nieudaną próbę dodania certyfikatu do IoT Hub | Śred. | Wystąpił błąd podczas próby dodania certyfikatu \' % {DescCertificateName} \' do IoT Hub \' % {DescIoTHubName} \' . Jeśli ta akcja została wykonana przez nieautoryzowaną stronę, może to oznaczać złośliwe działanie. | Upewnij się, że uprawnienia do zmiany certyfikatów są udzielane tylko autoryzowanym stronom. |
| Próba usunięcia certyfikatu z IoT Hub nie powiodła się | Śred. | Wystąpił błąd podczas próby usunięcia certyfikatu \' % {DescCertificateName} \' z IoT Hub \' % {DescIoTHubName} \' . Jeśli ta akcja została wykonana przez nieautoryzowaną stronę, może to oznaczać złośliwe działanie. | Upewnij się, że uprawnienia do zmiany certyfikatów są udzielane tylko autoryzowanym podmiotom. |
| niezgodność odcisku palca certyfikatu urządzenia x. 509 | Śred. | odcisk palca certyfikatu urządzenia x. 509 nie jest zgodny z konfiguracją. | Przejrzyj alerty na urządzeniach. Nie są wymagane żadne dalsze działania. |
| certyfikat x. 509 wygasł | Śred. | Certyfikat urządzenia X. 509 wygasł. | Może to być legalne urządzenie z wygasłym certyfikatem lub próbą personifikacji legalnego urządzenia. Jeśli wiarygodne urządzenie aktualnie komunikuje się prawidłowo, prawdopodobnie zostanie podjęta próba personifikacji. |
| **Niska** ważność |  |  |  |
| Podjęto próbę dodania lub edycji ustawienia diagnostycznego IoT Hub wykrytego | Niski | Wykryto próbę dodania lub edytowania ustawień diagnostycznych IoT Hub. Ustawienia diagnostyczne umożliwiają ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia zabezpieczeń lub zabezpieczenia sieci. Jeśli ta akcja nie została wykonana przez autoryzowaną stronę, może ona wskazywać na złośliwe działanie. | 1. Upewnij się, że certyfikat został usunięty przez autoryzowaną stronę.<br> 2. Jeśli certyfikat nie został usunięty przez autoryzowaną stronę, należy dodać certyfikat ponownie i eskalować alert do zespołu ds. zabezpieczeń informacji. |
| Podjęto próbę usunięcia ustawienia diagnostycznego z wykrytym IoT Hub | Niski | % {DescAttemptStatusMessage} \' podjęto próbę dodania lub edycji ustawienia diagnostycznego \' % {DescDiagnosticSettingName} \' dla IoT Hub \' % {DescIoTHubName} \' . Ustawienie diagnostyczne umożliwia ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. Jeśli ta akcja nie została wykonana przez autoryzowaną stronę, może to oznaczać złośliwe działanie. | Upewnij się, że uprawnienia do zmiany ustawień diagnostycznych są udzielane tylko autoryzowanym podmiotom. |
| Wygasły token SAS | Niski | Wygasły token SAS używany przez urządzenie | Może być uprawnionym urządzeniem z wygasłym tokenem lub próbować personifikować legalne urządzenie. Jeśli wiarygodne urządzenie aktualnie komunikuje się prawidłowo, jest to prawdopodobnie próba personifikacji. |
| Nieprawidłowa sygnatura tokenu sygnatury dostępu współdzielonego | Niski | Token sygnatury dostępu współdzielonego używany przez urządzenie ma nieprawidłową sygnaturę. Podpis nie jest zgodny z kluczem podstawowym lub pomocniczym. | Przejrzyj alerty na urządzeniach. Nie są wymagane żadne dalsze działania. |

## <a name="next-steps"></a>Następne kroki

- Usługa Defender for IoT [— Omówienie](overview.md)
- Dowiedz się, jak [uzyskać dostęp do danych zabezpieczeń](how-to-security-data-access.md)
- Dowiedz się więcej [na temat badania urządzenia](how-to-investigate-device.md)
