---
title: Pobierz przykłady usługi Azure urządzenia Kinect IMU
description: Dowiedz się, jak pobrać przykłady usługi Azure urządzenia Kinect IMU przy użyciu zestawu Azure urządzenia Kinect SDK.
author: qm13
ms.author: cedmonds
ms.date: 06/26/2019
ms.prod: kinect-dk
ms.topic: conceptual
keywords: urządzenia Kinect, Azure, konfiguracja, Głębokość, kolor, RBG, kamera, czujnik, zestaw SDK, IMU, czujnik ruchu, ruch, żyroskop, Gyro, przyspieszeniomierz, FPS
ms.openlocfilehash: 649dd5b9de62d43d59e74d53adff1ec7de8dfd32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277499"
---
# <a name="retrieve-azure-kinect-imu-samples"></a>Pobierz przykłady usługi Azure urządzenia Kinect IMU

Urządzenie Azure urządzenia Kinect zapewnia dostęp do jednostek ruchu bezwładnościowego (IMUs), łącznie z typami przyspieszeniomierz i żyroskop. Aby uzyskać dostęp do przykładów IMUs, musisz najpierw otworzyć i skonfigurować urządzenie, a następnie przechwycić dane IMU. Aby uzyskać więcej informacji, zobacz [Znajdowanie i otwieranie urządzenia](find-then-open-device.md).

Przykłady IMU są generowane na znacznie wyższej częstotliwości niż obrazy. Próbki są raportowane dla hosta z niższą szybkością niż próbki. Podczas oczekiwania na próbkę IMU, wiele przykładów jest często dostępnych w tym samym czasie.

Szczegółowe informacje na temat stawki raportowania IMU można znaleźć w temacie [specyfikacja sprzętowa](hardware-specification.md) usługi Azure urządzenia Kinect DK.

## <a name="configure-and-start-cameras"></a>Konfigurowanie i uruchamianie kamer

> [!NOTE]
> Czujniki IMU mogą działać tylko wtedy, gdy kolor i/lub kamery głębokości są uruchomione. Czujniki IMU nie działają samodzielnie.

Aby uruchomić aparaty fotograficzne, użyj [k4a_device_start_cameras ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a).

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start cameras\n");
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_start_imu(device))
{
    printf("Failed to start imu\n");
    goto Exit;
}
```

## <a name="access-imu-samples"></a>Dostęp do przykładów IMU

 Każdy [k4a_imu_sample_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__imu__sample__t.html#details) zawiera czas przechwycenia i przechwycony żyroskop.

Próbki IMU można pobrać w tym samym wątku, w którym są przechwytywane obrazy, lub w oddzielnych wątkach.

Aby pobrać IMU próbki natychmiast po ich udostępnieniu, możesz chcieć wywołać [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) swój własny wątek. Interfejs API ma również wystarczającą wewnętrzną kolejkowanie, aby umożliwić sprawdzanie tylko próbek po zwróceniu poszczególnych funkcji przechwytywania obrazu.

Ponieważ istnieje pewna wewnętrzna Kolejka przykładów IMU, można użyć następującego wzorca bez porzucania danych:

1. Zaczekaj na przechwycenie w dowolnej szybkości.
2. Przetwórz przechwytywanie.
3. Pobierz wszystkie Zakolejkowane próbki IMU.
4. Poczekaj na następne przechwycenie.

Aby pobrać wszystkie dostępne w kolejce próbki IMU, można wywołać [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) z wartością `timeout_in_ms` 0 w pętli do momentu zwrócenia funkcji `K4A_WAIT_RESULT_TIMEOUT` . `K4A_WAIT_RESULT_TIMEOUT` wskazuje, że nie ma żadnych próbek umieszczonych w kolejce i nie dotarły do określonego limitu czasu.

## <a name="usage-example"></a>Przykład użycia

```C
k4a_imu_sample_t imu_sample;

// Capture a imu sample
switch (k4a_device_get_imu_sample(device, &imu_sample, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a imu sample\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a imu sample\n");
    goto Exit;
}

// Access the accelerometer readings
if (imu_sample != NULL)
{
    printf(" | Accelerometer temperature:%.2f x:%.4f y:%.4f z: %.4f\n",
            imu_sample.temperature,
            imu_sample.acc_sample.xyz.x,
            imu_sample.acc_sample.xyz.y,
            imu_sample.acc_sample.xyz.z);
}

```

## <a name="next-steps"></a>Następne kroki

Teraz wiesz, jak korzystać z przykładów IMU, ale możesz też
>[!div class="nextstepaction"]
>[Dostęp do danych wejściowych mikrofonu](access-mics.md)
