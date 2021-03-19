---
title: Interfejs API odtwarzania usługi Azure urządzenia Kinect
description: Dowiedz się, jak otworzyć plik nagrywania przy użyciu interfejsu API odtwarzania przy użyciu zestawu SDK usługi Azure urządzenia Kinect sensor.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: urządzenia Kinect, Azure, czujnik, zestaw SDK, Głębokość, RGB, nagrywanie, odtwarzanie, Matroska, MKV
ms.openlocfilehash: fe403f314c1df415537d090433f34627eb1249e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277469"
---
# <a name="the-azure-kinect-playback-api"></a>Interfejs API odtwarzania usługi Azure urządzenia Kinect

Zestaw SDK czujnika udostępnia interfejs API do rejestrowania danych urządzenia w pliku Matroska (. mkv). W formacie kontenera Matroska są przechowywane ścieżki wideo, próbki IMU i Kalibracja urządzenia. Nagrania można generować przy użyciu dostarczonego narzędzia wiersza polecenia [k4arecorder](record-sensor-streams-file.md) . Nagrania można również dostosowywać i rejestrować bezpośrednio przy użyciu interfejsu API nagrywania.

Aby uzyskać więcej informacji o interfejsie API rejestrowania, zobacz [`k4a_record_create()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gae14f4181e9688e710d1c80b215413831.html#gae14f4181e9688e710d1c80b215413831) .

Więcej informacji na temat specyfikacji formatu plików Matroska można znaleźć na stronie [Format pliku rejestrowania](record-file-format.md) .

## <a name="use-the-playback-api"></a>Korzystanie z interfejsu API odtwarzania

Nagrywanie plików można otworzyć za pomocą interfejsu API odtwarzania. Interfejs API odtwarzania zapewnia dostęp do danych czujników w tym samym formacie co pozostała część zestawu SDK czujnika.

### <a name="open-a-record-file"></a>Otwórz plik rekordu

W poniższym przykładzie otwieramy nagranie przy użyciu [`k4a_playback_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gacb254ac941b2ab3c202ca68f4537f368.html#gacb254ac941b2ab3c202ca68f4537f368) , drukuje długość nagrania, a następnie zamyka plik [`k4a_playback_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga76f415f2076f1c8c544e094a649306ff.html#ga76f415f2076f1c8c544e094a649306ff) .

```C
k4a_playback_t playback_handle = NULL;
if (k4a_playback_open("recording.mkv", &playback_handle) != K4A_RESULT_SUCCEEDED)
{
    printf("Failed to open recording\n");
    return 1;
}

uint64_t recording_length = k4a_playback_get_last_timestamp_usec(playback_handle);
printf("Recording is %lld seconds long\n", recording_length / 1000000);

k4a_playback_close(playback_handle);
```

### <a name="read-captures"></a>Przechwycenia odczytu

Po otwarciu pliku możemy rozpocząć odczytywanie przechwycenia z nagrania. W następnym przykładzie zostanie odczytany każdy przechwytywany plik.

```C
k4a_capture_t capture = NULL;
k4a_stream_result_t result = K4A_STREAM_RESULT_SUCCEEDED;
while (result == K4A_STREAM_RESULT_SUCCEEDED)
{
    result = k4a_playback_get_next_capture(playback_handle, &capture);
    if (result == K4A_STREAM_RESULT_SUCCEEDED)
    {
        // Process capture here
        k4a_capture_release(capture);
    }
    else if (result == K4A_STREAM_RESULT_EOF)
    {
        // End of file reached
        break;
    }
}
if (result == K4A_STREAM_RESULT_FAILED)
{
    printf("Failed to read entire recording\n");
    return 1;
}
```

### <a name="seek-within-a-recording"></a>Wyszukaj w obrębie nagrania

Po osiągnięciu końca pliku możemy chcieć wrócić i ponownie go odczytać. Ten proces można wykonać, odczytując do tyłu [`k4a_playback_get_previous_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga54732e3aa0717e1ca4eb76ee385e878c.html#ga54732e3aa0717e1ca4eb76ee385e878c) , ale może to być bardzo wolne w zależności od długości nagrania.
Zamiast tego można użyć [`k4a_playback_seek_timestamp()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaea748994a121543bd77f90417cf428f6.html#gaea748994a121543bd77f90417cf428f6) funkcji, aby przejść do określonego punktu w pliku.

W tym przykładzie określimy sygnatury czasowe w mikrosekundach, aby przejść do różnych punktów w pliku.

```C
// Seek to the beginning of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to the end of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the start
if (k4a_playback_seek_timestamp(playback_handle, 10 * 1000000, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the end
if (k4a_playback_seek_timestamp(playback_handle, -10 * 1000000, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}
```

### <a name="read-tag-information"></a>Odczytaj informacje o tagu

Nagrania mogą również zawierać różne metadane, takie jak numer seryjny urządzenia i wersje oprogramowania układowego. Te metadane są przechowywane w tagach rejestrowania, do których można uzyskać dostęp za pomocą [`k4a_playback_get_tag()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga320f966fc89b4ba0d758f787f70d5143.html#ga320f966fc89b4ba0d758f787f70d5143) funkcji.

```C
// Print the serial number of the device used to record
char serial_number[256];
size_t serial_number_size = 256;
k4a_buffer_result_t buffer_result = k4a_playback_get_tag(playback_handle, "K4A_DEVICE_SERIAL_NUMBER", &serial_number, &serial_number_size);
if (buffer_result == K4A_BUFFER_RESULT_SUCCEEDED)
{
    printf("Device serial number: %s\n", serial_number);
}
else if (buffer_result == K4A_BUFFER_RESULT_TOO_SMALL)
{
    printf("Device serial number too long.\n");
}
else
{
    printf("Tag does not exist. Device serial number was not recorded.\n");
}
```

### <a name="record-tag-list"></a>Lista tagów rekordu

Poniżej znajduje się lista wszystkich znaczników domyślnych, które mogą być zawarte w pliku nagrywania. Wiele z tych wartości jest dostępnych jako część [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) struktury i można je odczytać przy użyciu [`k4a_playback_get_record_configuration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaab54a85c1f1e98d170d009042b449255.html#gaab54a85c1f1e98d170d009042b449255) funkcji.

Jeśli tag nie istnieje, przyjmuje się, że ma wartość domyślną.

| Nazwa tagu                     | Wartość domyślna      | [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) Polami | Uwagi     |
|------------------------------|--------------------|--------------------------------------|----------------------------------------------------------------------------------------------------------------|
| `K4A_COLOR_MODE`             | Logowanie              | `color_format` / `color_resolution`  | Możliwe wartości: "OFF", "MJPG_1080P", "NV12_720P", "YUY2_720P" itd.                                      |
| `K4A_DEPTH_MODE`             | Logowanie              | `depth_mode` / `depth_track_enabled` | Możliwe wartości: "OFF", "NFOV_UNBINNED", "PASSIVE_IR" itd.                                                |
| `K4A_IR_MODE`                | Logowanie              | `depth_mode` / `ir_track_enabled`    | Możliwe wartości: "OFF", "ACTIVE", "PASYWNe"                                                                    |
| `K4A_IMU_MODE`               | Logowanie              | `imu_track_enabled`                  | Możliwe wartości: "ON", "OFF"                                                                                   |
| `K4A_CALIBRATION_FILE`       | "calibration.json" | Nie dotyczy                                  | Wyświetlania [`k4a_device_get_raw_calibration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8c4e46642cee3115aeb0b33e2b43b24f.html#ga8c4e46642cee3115aeb0b33e2b43b24f) |
| `K4A_DEPTH_DELAY_NS`         | „0”                | `depth_delay_off_color_usec`         | Wartość przechowywana w nanosekundach, interfejs API udostępnia mikrosekundy.                                                        |
| `K4A_WIRED_SYNC_MODE`        | AUTONOMICZNEJ       | `wired_sync_mode`                    | Możliwe wartości: "STANDALONE", "MASTER", "podrzędny"                                                         |
| `K4A_SUBORDINATE_DELAY_NS`   | „0”                | `subordinate_delay_off_master_usec`  | Wartość przechowywana w nanosekundach, interfejs API udostępnia mikrosekundy.                                                        |
| `K4A_COLOR_FIRMWARE_VERSION` | ""                 | Nie dotyczy                                  | Wersja oprogramowania układowego koloru urządzenia, na przykład "1. x. XX"                                                            |
| `K4A_DEPTH_FIRMWARE_VERSION` | ""                 | Nie dotyczy                                  | Wersja oprogramowania układowego głębi urządzenia, na przykład "1. x. XX"                                                            |
| `K4A_DEVICE_SERIAL_NUMBER`   | ""                 | Nie dotyczy                                  | Rejestrowanie numeru seryjnego urządzenia                                                                                 |
| `K4A_START_OFFSET_NS`        | „0”                | `start_timestamp_offset_usec`        | Zobacz [synchronizację sygnatur czasowych](record-playback-api.md#timestamp-synchronization) poniżej.                       |
| `K4A_COLOR_TRACK`            | Brak               | Nie dotyczy                                  | Zobacz [Rejestrowanie formatu pliku — identyfikowanie ścieżek](record-file-format.md#identifying-tracks).                     |
| `K4A_DEPTH_TRACK`            | Brak               | Nie dotyczy                                  | Zobacz [Rejestrowanie formatu pliku — identyfikowanie ścieżek](record-file-format.md#identifying-tracks).                     |
| `K4A_IR_TRACK`               | Brak               | Nie dotyczy                                  | Zobacz [Rejestrowanie formatu pliku — identyfikowanie ścieżek](record-file-format.md#identifying-tracks).                     |
| `K4A_IMU_TRACK`              | Brak               | Nie dotyczy                                  | Zobacz [Rejestrowanie formatu pliku — identyfikowanie ścieżek](record-file-format.md#identifying-tracks).                     |

## <a name="timestamp-synchronization"></a>Synchronizacja sygnatury czasowej

Format Matroska wymaga, aby nagrania były uruchamiane z sygnaturą czasową równą zero. Podczas [synchronizowania z zewnętrznymi kamer](record-external-synchronized-units.md)pierwsza sygnatura czasowa z każdego urządzenia może być różna od zera.

Aby zachować oryginalne sygnatury czasowe z urządzeń między nagrywaniem i odtwarzaniem, plik przechowuje przesunięcie do zastosowania do sygnatur czasowych.

`K4A_START_OFFSET_NS`Tag służy do określania przesunięcia sygnatury czasowej, aby można było ponownie synchronizować pliki po nagraniu. To przesunięcie sygnatury czasowej można dodać do każdego znacznika czasu w pliku, aby odtworzyć oryginalne sygnatury czasowe urządzeń.

Przesunięcie początkowe jest również dostępne w [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) strukturze.
