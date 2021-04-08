---
title: Pobieranie wyników śledzenia treści przez usługę Azure urządzenia Kinect
description: Dowiedz się, jak pobierać wyniki śledzenia treści przy użyciu zestawu SDK śledzenia treści usługi Azure urządzenia Kinect.
author: qm13
ms.prod: kinect-dk
ms.author: quentinm
ms.reviewer: yijwan
ms.date: 06/26/2019
ms.topic: conceptual
keywords: urządzenia Kinect, Azure, czujnik, zestaw SDK, treść, śledzenie, wspólne
ms.openlocfilehash: 1b62022242144d5db51455a32ac04b67c3e5dd7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277558"
---
# <a name="get-body-tracking-results"></a>Pobieranie wyników śledzenia ciała

Zestaw SDK śledzenia treści używa obiektu śledzenia treści do przetwarzania przechwytywania usługi Azure urządzenia Kinect DK i generowania wyników śledzenia treści. Zachowuje także globalny stan śledzenia, przetwarza kolejki i kolejkę wyjściową. W przypadku korzystania z narzędzia do śledzenia treści należy wykonać trzy kroki:

- Tworzenie śledzenia
- Przechwytywanie głębokości i obrazów IR przy użyciu usługi Azure urządzenia Kinect DK
- Zakolejkowaniaj przechwytywanie i wyskakujące wyniki.

## <a name="create-a-tracker"></a>Tworzenie śledzenia


Pierwszym krokiem w korzystaniu z funkcji śledzenia treści jest utworzenie modułu śledzącego i wymaganie przechodzenia do struktury [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) kalibracji czujnika. Kalibrację czujnika można wykonać przy użyciu funkcji [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) zestawu SDK czujnika usługi Azure urządzenia Kinect.

```C
k4a_calibration_t sensor_calibration;
if (K4A_RESULT_SUCCEEDED != k4a_device_get_calibration(device, device_config.depth_mode, K4A_COLOR_RESOLUTION_OFF, &sensor_calibration))
{
    printf("Get depth camera calibration failed!\n");
    return 0;
}

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
if (K4A_RESULT_SUCCEEDED != k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker))
{
    printf("Body tracker initialization failed!\n");
    return 0;
}
```

## <a name="capture-depth-and-ir-images"></a>Przechwyć głębokość i obrazy IR

Przechwytywanie obrazów przy użyciu usługi Azure urządzenia Kinect DK jest omówione na stronie [pobieranie obrazów](retrieve-images.md) .

>[!NOTE]
> `K4A_DEPTH_MODE_NFOV_UNBINNED` lub `K4A_DEPTH_MODE_WFOV_2X2BINNED` tryby są zalecane w celu uzyskania najlepszej wydajności i dokładności. Nie używaj `K4A_DEPTH_MODE_OFF` `K4A_DEPTH_MODE_PASSIVE_IR` trybów ani.

Obsługiwane tryby usługi Azure urządzenia Kinect DK są opisane w [specyfikacji sprzętowej](hardware-specification.md) [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d) i wyliczeń platformy Azure urządzenia Kinect DK.

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

## <a name="enqueue-the-capture-and-pop-the-results"></a>Zakolejkowaniaj przechwytywanie i wyskakujące wyniki

Śledzenie wewnętrznie utrzymuje kolejkę wejściową i kolejkę wyjściową, aby asynchronicznie przetwarzać usługi Azure urządzenia Kinect DK. Użyj funkcji [k4abt_tracker_enqueue_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e) , aby dodać nowe przechwycenie do kolejki wejściowej. Użyj funkcji [k4abt_tracker_pop_result ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b) o obecności wyniku z kolejki wyjściowej. Użycie wartości limitu czasu jest zależne od aplikacji i kontroluje czas oczekiwania kolejkowania.

### <a name="real-time-processing"></a>Przetwarzanie w czasie rzeczywistym
Użyj tego wzorca dla aplikacji jednowątkowych, które potrzebują wyników w czasie rzeczywistym i można obsłużyć upuszczone klatki. `simple_3d_viewer`Przykładem znajdującym się w witrynie [GitHub Azure-urządzenia Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) jest przykładowe przetwarzanie w czasie rzeczywistym.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, 0);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, 0);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

### <a name="synchronous-processing"></a>Przetwarzanie synchroniczne
Użyj tego wzorca dla aplikacji, które nie potrzebują wyników w czasie rzeczywistym lub nie można obsłużyć porzuconych ramek.

Przepływność przetwarzania może być ograniczona.

`simple_sample.exe`Przykładem, który znajduje się w witrynie [GitHub Azure-urządzenia Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) , jest przykładowe przetwarzanie synchroniczne.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Popping body tracking result failed!\n");
    break;
}
// Successfully popped the body tracking result. Start your processing
...

k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Uzyskiwanie dostępu do danych w klatce ciała](access-data-body-frame.md)
