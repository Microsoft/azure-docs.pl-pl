---
title: Szybki Start — rejestrowanie strumieni czujników usługi Azure urządzenia Kinect do pliku
description: W tym przewodniku szybki start dowiesz się, jak rejestrować strumienie danych z zestawu SDK czujnika do pliku.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: Azure, urządzenia Kinect, nagrywanie, odtwarzanie z tyłu, czytelnik, Matroska, MKV, strumienie, Głębokość, RGB, kamera, kolor, IMU, audio, czujnik
ms.openlocfilehash: 3dab147b593bf012bd6cd9c95d0195e84a2cbcf1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277956"
---
# <a name="quickstart-record-azure-kinect-sensor-streams-to-a-file"></a>Szybki Start: rejestrowanie strumieni czujników usługi Azure urządzenia Kinect do pliku

Ten przewodnik Szybki Start zawiera informacje o sposobach rejestrowania strumieni danych z zestawu SDK czujnika do pliku przy użyciu narzędzia [Azure urządzenia Kinect Recorder](azure-kinect-recorder.md) .

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku szybki start przyjęto:

- Usługa Azure urządzenia Kinect DK jest połączona z komputerem hosta i jest prawidłowo włączona.
- Zakończono [Konfigurowanie](set-up-azure-kinect-dk.md) sprzętu.

## <a name="create-recording"></a>Utwórz nagranie

1. Otwórz wiersz polecenia i podaj ścieżkę do [usługi Azure urządzenia Kinect Recorder](azure-kinect-recorder.md), która znajduje się w katalogu zainstalowane narzędzia jako `k4arecorder.exe` . Przykład: `C:\Program Files\Azure Kinect SDK\tools\k4arecorder.exe`.
2. Rekord 5 sekund.

    `k4arecorder.exe -l 5 %TEMP%\output.mkv`

3. Domyślnie Rejestrator używa trybu głębokości NFOV Unbinned i wyprowadza wartość 1080p RGB z 30 fps włącznie z danymi IMU. Pełny przegląd opcji nagrywania i porad można znaleźć w temacie [Azure urządzenia Kinect Recorder](azure-kinect-recorder.md).

## <a name="play-back-recording"></a>Odtwórz nagrywanie

Możesz użyć [podglądu usługi Azure urządzenia Kinect](azure-kinect-viewer.md) , aby odtworzyć nagranie.

1. Paska [`k4aviewer.exe`](azure-kinect-viewer.md)
2. Unfold kartę **Otwórz nagranie** i Otwórz nagranie.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zarejestrować strumienie czujnika do pliku, należy

> [!div class="nextstepaction"]
> [Tworzenie pierwszej aplikacji Azure Kinect](build-first-app.md)
