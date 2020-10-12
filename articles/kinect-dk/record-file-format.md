---
title: Używanie zestawu SDK usługi Azure urządzenia Kinect sensor do rejestrowania formatu pliku
description: Dowiedz się, jak używać formatu zapisanego pliku zestawu SDK czujnika urządzenia Kinect platformy Azure.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: reference
keywords: urządzenia Kinect, Azure, czujnik, zestaw SDK, Głębokość, RGB, nagrywanie, odtwarzanie, Matroska, MKV
ms.openlocfilehash: 3a78ed158c78df1c9a6eadcc0bfb2e40b22750bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501784"
---
# <a name="use-azure-kinect-sensor-sdk-to-record-file-format"></a>Używanie zestawu SDK usługi Azure urządzenia Kinect sensor do rejestrowania formatu pliku

Aby zarejestrować dane czujnika, używany jest format kontenera Matroska (. mkv), który umożliwia przechowywanie wielu ścieżek przy użyciu szerokiego zakresu koderów-dekoder. Plik nagrania zawiera ścieżki do przechowywania kolorów, głębi, obrazów IR i IMU.

Szczegóły niskiego poziomu dla formatu kontenera. mkv można znaleźć w [witrynie sieci Web Matroska](https://www.matroska.org/index.html).

| Nazwa ścieżki | Format kodeka                          |
|------------|---------------------------------------|
| Kolor      | Mode-Dependent (MJPEG, NV12 lub YUY2) |
| ŚCISŁ      | b16g (16-bitowa Skala szarości, big-endian)   |
| IR         | b16g (16-bitowa Skala szarości, big-endian)   |
| Moduł IMU        | Struktura niestandardowa, zobacz [Przykładowa struktura IMU](record-file-format.md#imu-sample-structure) . |

## <a name="using-third-party-tools"></a>Korzystanie z narzędzi innych firm

Narzędzia takie jak `ffmpeg` lub `mkvinfo` polecenie z zestawu narzędzi [MKVToolNix](https://mkvtoolnix.download/) Toolkit mogą służyć do wyświetlania i wyodrębniania informacji z plików nagrywania.

Na przykład następujące polecenie wyodrębni ścieżkę głębokości jako sekwencję 16-bitowego PNGs do tego samego folderu:

```
ffmpeg -i output.mkv -map 0:1 -vsync 0 depth%04d.png
```

`-map 0:1`Parametr będzie wyodrębniać indeks śledzenia 1, który dla większości nagrań będzie głębi. Jeśli nagranie nie zawiera ścieżki koloru, `-map 0:0` zostanie użyta.

`-vsync 0`Parametr wymusza narzędzia FFmpeg, aby wyodrębnić ramki jako-jest zamiast próbować dopasować do szybkości 30 fps, 15 fps lub 5 fps.

## <a name="imu-sample-structure"></a>Przykładowa struktura IMU

Jeśli dane IMU są wyodrębniane z pliku bez użycia interfejsu API odtwarzania, dane będą w postaci binarnej.
Struktura danych IMU jest poniżej. Wszystkie pola są nieco-endian.

| Pole                        | Typ     |
|------------------------------|----------|
| Sygnatura czasowa przyspieszeniomierza (s) | UInt64   |
| Dane przyspieszeniomierza (x, y, z) | zmiennoprzecinkowe [3] |
| Sygnatura czasowa żyroskop (s)     | UInt64   |
| Dane żyroskop (x, y, z)     | zmiennoprzecinkowe [3] |

## <a name="identifying-tracks"></a>Identyfikowanie ścieżek

Może być konieczne ustalenie, która ścieżka zawiera kolor, Głębokość, IR i tak dalej. Identyfikowanie ścieżek jest konieczne podczas pracy z narzędziami innych firm w celu odczytania pliku Matroska.
Numery śledzenia różnią się w zależności od trybu aparatu i zestawu włączonych śledzenia. Tagi służą do identyfikowania znaczenia każdej ścieżki.

Lista poniższych tagów jest dołączona do określonego elementu Matroska i może służyć do wyszukania odpowiedniej ścieżki lub załącznika.

Tagi te są dostępne dla narzędzi takich jak `ffmpeg` i `mkvinfo` .
Pełna lista tagów jest wyświetlana na stronie [nagrywanie i odtwarzanie](record-playback-api.md) .

| Nazwa tagu             | Obiekt docelowy tagu             | Wartość tagu             |
|----------------------|------------------------|-----------------------|
| K4A_COLOR_TRACK      | Ścieżka koloru            | Identyfikator UID śledzenia Matroska    |
| K4A_DEPTH_TRACK      | Ścieżka głębokości            | Identyfikator UID śledzenia Matroska    |
| K4A_IR_TRACK         | Ścieżka IR               | Identyfikator UID śledzenia Matroska    |
| K4A_IMU_TRACK        | Śledzenie IMU              | Identyfikator UID śledzenia Matroska    |
| K4A_CALIBRATION_FILE | Załącznik do kalibracji | Nazwa pliku załącznika   |

## <a name="next-steps"></a>Następne kroki

[Nagrywanie i odtwarzanie](record-playback-api.md)
