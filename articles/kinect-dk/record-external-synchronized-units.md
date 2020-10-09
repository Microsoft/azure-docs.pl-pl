---
title: Korzystanie z usługi Azure urządzenia Kinect Recorder z zewnętrznymi urządzeniami synchronizowanymi
description: Dowiedz się, jak rejestrować dane z urządzeń skonfigurowanych do synchronizacji zewnętrznej za pomocą usługi Azure urządzenia Kinect Recorder.
author: tesych
ms.author: tesych
ms.reviewer: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Urządzenia Kinect, czujnik, Podgląd, synchronizacja zewnętrzna, opóźnienie fazy, Głębokość, RGB, kamera, kabel audio, rejestrator
ms.openlocfilehash: 052f6f1ac9f90e764de25d1d4d1b25b3d50a848d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277470"
---
# <a name="use-azure-kinect-recorder-with-external-synchronized-devices"></a>Korzystanie z usługi Azure urządzenia Kinect Recorder z zewnętrznymi urządzeniami synchronizowanymi

Ten artykuł zawiera wskazówki dotyczące tego, jak [usługa Azure urządzenia Kinect Recorder](azure-kinect-recorder.md) może rejestrować urządzenia z konfiguracją zewnętrznej synchronizacji danych.

## <a name="prerequisites"></a>Wymagania wstępne

- [Skonfiguruj wiele jednostek usługi Azure urządzenia Kinect DK dla synchronizacji zewnętrznej](https://support.microsoft.com/help/4494429).

## <a name="external-synchronization-constraints"></a>Ograniczenia synchronizacji zewnętrznej

- Nie można zsynchronizować urządzenia głównego z kablem.
- Aby można było włączyć synchronizację, urządzenie główne musi przesyłać strumieniowo kamerę RGB.
- Wszystkie jednostki muszą korzystać z tej samej konfiguracji aparatu (szybkość i rozdzielczość).
- Wszystkie jednostki muszą korzystać z tego samego oprogramowania układowego urządzenia ([Aktualizuj instrukcje oprogramowania układowego](update-device-firmware.md) ).
- Wszystkie urządzenia podrzędne muszą zostać uruchomione przed urządzeniem głównym.
- Ta sama wartość ekspozycji powinna być ustawiona na wszystkich urządzeniach.
- Ustawienia *wzorca opóźnienia* wszystkich podwładnych są powiązane z urządzeniem głównym.

## <a name="record-when-each-unit-has-a-host-pc"></a>Rejestruj, gdy każda jednostka ma komputer hosta

W poniższym przykładzie każde urządzenie ma własny dedykowany komputer-host.
Zalecamy podłączenie urządzeń do komputerów dedykowanych, aby zapobiec problemom z przepustowością USB i użyciem procesora CPU/procesora GPU.

### <a name="subordinate-1"></a>Podwładny 1

1. Konfigurowanie rejestratora dla pierwszej jednostki

      `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub1.mkv`

2. Urządzenie zaczyna czekać

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="subordinate-2"></a>Podwładny 2

1. Konfigurowanie rejestratora dla drugiej jednostki

    `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub2.mkv`

2. Urządzenie zaczyna czekać

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="master"></a>Główny

1. Rozpocznij nagrywanie na wzorcu

    `>k4arecorder.exe --external-sync master -e -8 -r 5 -l 10 master.mkv`

2. Zaczekaj na zakończenie rejestrowania

## <a name="recording-when-multiple-units-connected-to-single-host-pc"></a>Rejestrowanie w przypadku, gdy wiele jednostek jest podłączonych do jednego komputera hosta

Do jednego komputera hosta można podłączyć wiele urządzenia Kinect usługi Azure DKs. Jednak może to być bardzo wymaganie przepustowości USB i obliczeń hosta. Aby zmniejszyć zapotrzebowanie:

- Połącz każde urządzenie z własnym kontrolerem hosta USB.
- Oferuje wydajny procesor GPU, który może obsługiwać silnik głębokości dla każdego urządzenia.
- Rejestruj tylko wymaganą czujniki i używaj mniejszej szybkości klatek.

Zawsze uruchamiaj najpierw urządzenia podrzędne i główne.

## <a name="subordinate-1"></a>Podwładny 1

1. Uruchom rejestrator w podrzędnym

    `>k4arecorder.exe --device 1 --external-sync subordinate --imu OFF -e -8 -r 5 -l 5 output-2.mkv`

2. Urządzenie przechodzi w stan oczekiwania

## <a name="master"></a>Główny

1. Uruchom urządzenie główne

    `>k4arecorder.exe --device 0 --external-sync master --imu OFF -e -8 -r 5 -l 5 output-1.mkv`

2. Zaczekaj na zakończenie nagrywania

## <a name="playing-recording"></a>Odtwarzanie nagrania

Aby odtworzyć nagrywanie, możesz użyć [przeglądarki Azure urządzenia Kinect](azure-kinect-viewer.md) .



## <a name="tips"></a>Porady

- Używaj ręcznego narażenia na nagrywanie synchronizowanych kamer. Funkcja autoekspozycji aparatu RGB może mieć wpływ na synchronizację czasu.
- Ponowne uruchomienie urządzenia podrzędnego spowoduje utratę synchronizacji.
- Niektóre [tryby aparatu fotograficznego](hardware-specification.md#depth-camera-supported-operating-modes) obsługują maksymalnie 15 fps. Zaleca się, aby nie mieszać trybów/szybkości klatek między urządzeniami
- Połączenie wielu jednostek z pojedynczym komputerem może łatwo zwiększyć nasycenie przepustowości USB, należy rozważyć użycie oddzielnego komputera hosta na urządzenie. Należy również zwrócić uwagę na obliczenia procesora CPU i procesora GPU.
- Wyłącz mikrofon i IMU, jeśli nie są potrzebne, aby zwiększyć niezawodność.

Problemy można znaleźć w temacie [Rozwiązywanie problemów](troubleshooting.md)

## <a name="see-also"></a>Zobacz też

- [Konfigurowanie synchronizacji zewnętrznej](https://support.microsoft.com/help/4494429/sync-multiple-devices)
- [Usługa Azure urządzenia Kinect Recorder](azure-kinect-recorder.md) dla ustawień rejestratora i dodatkowych informacji.
- [Usługa Azure urządzenia Kinect Viewer](azure-kinect-viewer.md) do odtwarzania nagrań lub ustawiania właściwości kamery RGB nie jest dostępna za pomocą rejestratora.
- [Narzędzie oprogramowania układowego platformy Azure urządzenia Kinect](azure-kinect-firmware-tool.md) do aktualizacji oprogramowania układowego urządzenia.
