---
title: Rejestrator usługi Azure urządzenia Kinect DK
description: Dowiedz się, jak rejestrować strumienie danych z zestawu SDK czujnika do pliku przy użyciu usługi Azure urządzenia Kinect Recorder.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: urządzenia Kinect, Record, playback, Reader, Matroska, MKV, strumienie, głębokości, RGB, Camera, Color, IMU, audio
ms.openlocfilehash: 5fb6895d4102956a991c67ffab836a26b7a3abb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277515"
---
# <a name="azure-kinect-dk-recorder"></a>Rejestrator usługi Azure urządzenia Kinect DK

W tym artykule opisano, jak można użyć `k4arecorder` narzędzia wiersza polecenia do rejestrowania strumieni danych z zestawu SDK czujnika do pliku.

>[!NOTE]
>Usługa Azure urządzenia Kinect Recorder nie rejestruje dźwięku.

## <a name="recorder-options"></a>Opcje rejestratora

`k4arecorder`Ma różne argumenty wiersza polecenia, aby określić plik wyjściowy i tryb rejestrowania.

Nagrania są przechowywane w [formacie Matroska. mkv](record-file-format.md). Nagrywanie używa wielu ścieżek wideo w celu uzyskania koloru i głębi, a także dodatkowych informacji, takich jak kalibracja i metadane aparatu.

```console
k4arecorder [options] output.mkv

 Options:
  -h, --help              Prints this help
  --list                  List the currently connected K4A devices
  --device                Specify the device index to use (default: 0)
  -l, --record-length     Limit the recording to N seconds (default: infinite)
  -c, --color-mode        Set the color sensor mode (default: 1080p), Available options:
                            3072p, 2160p, 1536p, 1440p, 1080p, 720p, 720p_NV12, 720p_YUY2, OFF
  -d, --depth-mode        Set the depth sensor mode (default: NFOV_UNBINNED), Available options:
                            NFOV_2X2BINNED, NFOV_UNBINNED, WFOV_2X2BINNED, WFOV_UNBINNED, PASSIVE_IR, OFF
  --depth-delay           Set the time offset between color and depth frames in microseconds (default: 0)
                            A negative value means depth frames will arrive before color frames.
                            The delay must be less than 1 frame period.
  -r, --rate              Set the camera frame rate in Frames per Second
                            Default is the maximum rate supported by the camera modes.
                            Available options: 30, 15, 5
  --imu                   Set the IMU recording mode (ON, OFF, default: ON)
  --external-sync         Set the external sync mode (Master, Subordinate, Standalone default: Standalone)
  --sync-delay            Set the external sync delay off the master camera in microseconds (default: 0)
                            This setting is only valid if the camera is in Subordinate mode.
  -e, --exposure-control  Set manual exposure value (-11 to 1) for the RGB camera (default: auto exposure)
```

## <a name="record-files"></a>Rejestruj pliki

Przykład 1. Tryb rejestrowania głębokości NFOV unbinned (640x576), RGB 1080p o 30 fps z IMU.
Naciśnij klawisze **Ctrl-C** , aby zatrzymać nagrywanie.

```
k4arecorder.exe output.mkv
```

Przykład 2. Zarejestruj WFOV inne niż Binned (1MP), 3072p RGB na 15 fps bez IMU, przez 10 sekund.

```
k4arecorder.exe -d WFOV_UNBINNED -c 3072p -r 15 -l 10 --imu OFF output.mkv
```

Przykład 3. Zarejestruj WFOV 2x2 Binned o 30 fps przez 5 sekund i Zapisz w danych wyjściowych. mkv.

```
k4arecorder.exe -d WFOV_2X2BINNED -c OFF --imu OFF -l 5 output.mkv
```

>[!TIP]
>Możesz użyć [przeglądarki Azure urządzenia Kinect](azure-kinect-viewer.md) , aby skonfigurować kontrolki kamery RGB przed nagrywaniem (np. w celu ustawienia ręcznego balansu bieli).

## <a name="verify-recording"></a>Weryfikuj nagrywanie

Plik Output. mkv można otworzyć za pomocą [usługi Azure urządzenia Kinect Viewer](azure-kinect-viewer.md).

Aby wyodrębnić ścieżki lub wyświetlić informacje o plikach, narzędzia takie jak `mkvinfo` są dostępne jako część zestawu narzędzi [MKVToolNix](https://mkvtoolnix.download/) .

## <a name="next-steps"></a>Następne kroki

[Korzystanie z rejestratora z zewnętrznymi synchronizowanymi jednostkami](record-external-synchronized-units.md)