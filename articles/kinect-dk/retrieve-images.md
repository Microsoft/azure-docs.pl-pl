---
title: Pobieranie danych obrazu usługi Azure urządzenia Kinect
description: Dowiedz się, jak pobrać dane obrazu usługi Azure urządzenia Kinect za pomocą zestawu SDK czujnika urządzenia Kinect.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: urządzenia Kinect, Azure, pobieranie, czujnik, aparat, zestaw SDK, Głębokość, RGB, obrazy, kolor, przechwytywanie, rozdzielczość, bufor
ms.openlocfilehash: fed5c7340d287d9103ba35f0fd3d80c0fff6e3ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "87538918"
---
# <a name="retrieve-azure-kinect-image-data"></a>Pobieranie danych obrazu usługi Azure urządzenia Kinect

Ta strona zawiera szczegółowe informacje dotyczące pobierania obrazów z usługi Azure urządzenia Kinect. W tym artykule przedstawiono sposób przechwytywania i uzyskiwania dostępu do obrazów koordynowanych między kolorem i głębokością urządzenia. Aby uzyskać dostęp do obrazów, należy najpierw otworzyć i skonfigurować urządzenie, a następnie można przechwytywać obrazy.
Przed skonfigurowaniem i przechwyceniem obrazu należy [znaleźć i otworzyć urządzenie](find-then-open-device.md).

Możesz również zapoznać się z [przykładem przesyłania strumieniowego zestawu SDK](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/streaming) , który pokazuje, jak używać funkcji w tym artykule.

Omówione są następujące funkcje:

- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_get_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4dac757a33657f4d3dbf1ae8b21c158a.html#ga4dac757a33657f4d3dbf1ae8b21c158a)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_image_get_buffer()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)
- [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f)
- [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  
## <a name="configure-and-start-the-device"></a>Konfigurowanie i uruchamianie urządzenia

Dwie kamery dostępne na urządzeniu urządzenia Kinect obsługują wiele trybów, rozdzielczości i formatów danych wyjściowych. Aby zapoznać się z pełną listą, zobacz [specyfikacje sprzętu](hardware-specification.md)zestawu Azure urządzenia Kinect Development Kit.

Konfiguracja przesyłania strumieniowego jest ustawiana przy użyciu wartości w [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) strukturze.

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;
config.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start device\n");
    goto Exit;
}
```

Po uruchomieniu aparatów nadal będą przechwytywane dane do momentu [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8) wywołania lub zamknięcia urządzenia.

## <a name="stabilization"></a>Stabilizacji

W przypadku uruchamiania urządzeń przy użyciu funkcji synchronizacji wiele urządzeń zdecydowanie zaleca się to przy użyciu ustawienia stałego ekspozycji.
Przy ręcznym rozświetlaniu można pobrać maksymalnie osiem przechwycenia z urządzenia, aby umożliwić stabilne obrazy i szybkość klatek. Dzięki autoekspozycji może upłynąć do 20 przechwytywania przed ustabilizowaniem obrazów i skalowania klatek.

## <a name="get-a-capture-from-the-device"></a>Pobieranie przechwytywania z urządzenia

Obrazy są przechwytywane z urządzenia w sposób skorelowany. Każdy przechwycony obraz zawiera obraz głębokości, obraz IR, obraz koloru lub kombinację obrazów.

Domyślnie interfejs API zwróci funkcję przechwytywania dopiero po odebraniu wszystkich żądanych obrazów dla trybu przesyłania strumieniowego. Interfejs API można skonfigurować tak, aby zwracał częściowe przechwycenia z obrazem głębokości lub kolorem tylko wtedy, gdy tylko staną się dostępne przez wyczyszczenie [`synchronized_images_only`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t_a8208974f05d89fc1362c6a0900bdef4d.html#a8208974f05d89fc1362c6a0900bdef4d) parametru [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) .

```C
// Capture a depth frame
switch (k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a capture\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a capture\n");
    goto Exit;
}
```

Po pomyślnym zwróceniu przechwytywania przez interfejs API należy wywołać [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) po zakończeniu korzystania z obiektu przechwytywania.

## <a name="get-an-image-from-the-capture"></a>Pobierz obraz z przechwytywania

Aby pobrać przechwycony obraz, wywołaj odpowiednią funkcję dla każdego typu obrazu. Jeden z:

- [`k4a_capture_get_color_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga683e440b5f22215a2de58d7fa140488c.html#ga683e440b5f22215a2de58d7fa140488c)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_capture_get_ir_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga1531c3fa76a7c174b8f2eab24de91794.html#ga1531c3fa76a7c174b8f2eab24de91794)

[`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f) [`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) Po zakończeniu korzystania z obrazu należy wywołać wszystkie dojścia zwrócone przez te funkcje.

## <a name="access-image-buffers"></a>Bufory obrazu dostępu

[`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) ma wiele funkcji metody dostępu do pobierania właściwości obrazu.

Aby uzyskać dostęp do bufora pamięci obrazu, użyj [k4a_image_get_buffer](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e).

W poniższym przykładzie pokazano, jak uzyskać dostęp do przechwyconego obrazu głębokości. Ta sama zasada ma zastosowanie do innych typów obrazów. Pamiętaj jednak, aby zastąpić zmienną typu obrazu prawidłowym typem obrazu, na przykład IR lub kolorem.

```C
// Access the depth16 image
k4a_image_t image = k4a_capture_get_depth_image(capture);
if (image != NULL)
{
    printf(" | Depth16 res:%4dx%4d stride:%5d\n",
            k4a_image_get_height_pixels(image),
            k4a_image_get_width_pixels(image),
            k4a_image_get_stride_bytes(image));

    // Release the image
    k4a_image_release(image);
}

// Release the capture
k4a_capture_release(capture);
```

## <a name="next-steps"></a>Następne kroki

Teraz wiesz, jak przechwytywać i koordynować obrazy kamer między kolorem i głębią przy użyciu urządzenia urządzenia Kinect platformy Azure. Można również:

>[!div class="nextstepaction"]
>[Pobieranie próbek z czujnika IMU](retrieve-imu-samples.md)

>[!div class="nextstepaction"]
>[Dostęp do mikrotelefonu](access-mics.md)
