---
title: 'Szybki Start: Tworzenie aplikacji do śledzenia treści usługi Azure urządzenia Kinect'
description: Instrukcje krok po kroku dotyczące kompilowania pierwszej aplikacji do śledzenia treści usługi Azure urządzenia Kinect
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: urządzenia Kinect, Azure, czujnik, zestaw SDK, treść, śledzenie, wspólne, aplikacja, pierwsze
ms.openlocfilehash: bdf8ee7a14bf59a151dfa316b11159830b4f63b8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277982"
---
# <a name="quickstart-build-an-azure-kinect-body-tracking-application"></a>Szybki Start: Tworzenie aplikacji do śledzenia treści usługi Azure urządzenia Kinect

Wprowadzenie do zestawu SDK śledzenia treści? Ten przewodnik Szybki Start umożliwi rozpoczęcie pracy z śledzeniem treści. Więcej przykładów można znaleźć w tym [repozytorium Azure-urządzenia Kinect-Sample](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples).

## <a name="prerequisites"></a>Wymagania wstępne

- [Konfigurowanie zestawu Azure Kinect DK](set-up-azure-kinect-dk.md)
- [Konfigurowanie zestawu Body Tracking SDK](body-sdk-setup.md)
- Zapoznaj się z tematem Tworzenie pierwszego przewodnika Szybki Start dla [aplikacji Azure urządzenia Kinect](build-first-app.md) .
- Zapoznaj się z następującymi funkcjami zestawu SDK czujnika:
  - [k4a_device_open ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
  - [k4a_device_start_cameras ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
  - [k4a_device_stop_cameras ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  - [k4a_device_close ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)
- Zapoznaj się z dokumentacją dotyczącą następujących funkcji zestawu SDK śledzenia treści:
  - [k4abt_tracker_create ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1aa71481b8441def94de11b29e0e3cbc.html#ga1aa71481b8441def94de11b29e0e3cbc)
  - [k4abt_tracker_enqueue_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e)
  - [k4abt_tracker_pop_result ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b)
  - [k4abt_tracker_shutdown ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga94036969ef94cbc414c78b3f6d04bfa5.html#ga94036969ef94cbc414c78b3f6d04bfa5)
  - [k4abt_tracker_destroy ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gab2c9afca092130976cd66077c0557ed1.html#gab2c9afca092130976cd66077c0557ed1)

## <a name="headers"></a>Nagłówki

Śledzenie treści używa jednego nagłówka, `k4abt.h` . Dołącz ten nagłówek oprócz `k4a.h` . Upewnij się, że wybrany kompilator jest skonfigurowany zarówno dla zestawu SDK czujnika, jak i dla zestawu SDK `lib` i `include` folderów śledzenia treści. Należy również połączyć się z `k4a.lib` plikami i `k4abt.lib` . Uruchomienie aplikacji wymaga `k4a.dll` , `k4abt.dll` , `onnxruntime.dll` , i musi `dnn_model.onnx` znajdować się w ścieżce wykonywania aplikacji.

```C
#include <k4a/k4a.h>
#include <k4abt.h>
```

## <a name="open-device-and-start-the-camera"></a>Otwórz urządzenie i uruchom kamerę

Twoja pierwsza aplikacja do śledzenia treści zakłada, że pojedyncze urządzenie urządzenia Kinect platformy Azure jest połączone z komputerem.

Śledzenie treści jest kompilacją w zestawie SDK czujnika. Aby można było korzystać z funkcji śledzenia treści, należy najpierw otworzyć i skonfigurować urządzenie. Użyj funkcji [k4a_device_open ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) , aby otworzyć urządzenie, a następnie skonfiguruj je za pomocą obiektu [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) . Aby uzyskać najlepsze wyniki, ustaw tryb głębokości na `K4A_DEPTH_MODE_NFOV_UNBINNED` lub `K4A_DEPTH_MODE_WFOV_2X2BINNED` . Śledzenie treści nie zostanie uruchomione, jeśli tryb głębokości jest ustawiony na  `K4A_DEPTH_MODE_OFF` lub `K4A_DEPTH_MODE_PASSIVE_IR` .

Więcej informacji na temat znajdowania i otwierania urządzenia można znaleźć na [tej stronie](find-then-open-device.md).

Więcej informacji na temat trybów głębokości usługi Azure urządzenia Kinect można znaleźć na następujących stronach: [specyfikacji sprzętu](hardware-specification.md) i wyliczeń [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d) .

```C
k4a_device_t device = NULL;
k4a_device_open(0, &device);

// Start camera. Make sure depth camera is enabled.
k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
k4a_device_start_cameras(device, &deviceConfig);
```

## <a name="create-the-tracker"></a>Tworzenie modułu śledzącego

Pierwszym krokiem w celu uzyskania wyników śledzenia treści jest utworzenie modułu śledzącego treść. Musi ona mieć strukturę [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) kalibracji czujnika. Kalibrację czujnika można zbadać przy użyciu funkcji [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) .

```C
k4a_calibration_t sensor_calibration;
k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration);

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker);
```

## <a name="get-captures-from-the-azure-kinect-device"></a>Pobieranie przechwytywania z urządzenia Azure urządzenia Kinect

Więcej informacji na temat pobierania danych obrazu można znaleźć na [tej stronie](retrieve-images.md).

```C
// Capture a depth frame
k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS);
```

## <a name="enqueue-the-capture-and-pop-the-results"></a>Zakolejkowaniaj przechwytywanie i wyskakujące wyniki

Śledzenie wewnętrznie utrzymuje kolejkę wejściową i kolejkę wyjściową, aby asynchronicznie przetwarzać usługi Azure urządzenia Kinect DK. Następnym krokiem jest użycie `k4abt_tracker_enqueue_capture()` funkcji w celu dodania nowego przechwytywania do kolejki wejściowej. Użyj `k4abt_tracker_pop_result()` funkcji, aby wyprowadzić wynik z kolejki wyjściowej. Wartość limitu czasu jest zależna od aplikacji i kontroluje czas oczekiwania kolejki.

Twoja pierwsza aplikacja do śledzenia treści używa wzorca przetwarzania w czasie rzeczywistym. Zapoznaj się z tematem [pobieranie wyników śledzenia treści](get-body-tracking-results.md) , aby uzyskać szczegółowy opis innych wzorców.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

## <a name="access-the-body-tracking-result-data"></a>Dostęp do danych wyników śledzenia treści

Wyniki śledzenia treści dla każdego przechwycenia czujnika są przechowywane w ramce Body [k4abt_frame_t](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/structk4abt__frame__t.html) Structure. Każda ramka treści zawiera trzy kluczowe składniki: kolekcję struktur treści, mapę indeksu treści 2D i przechwytywanie danych wejściowych.

Twoja pierwsza aplikacja do śledzenia treści uzyskuje dostęp tylko do wykrytych jednostek. Zapoznaj się z tematem [dostęp do danych w ramce treści](access-data-body-frame.md) , aby uzyskać szczegółowe wyjaśnienie danych w ramce treść.

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
printf("%zu bodies are detected!\n", num_bodies);
```

## <a name="clean-up"></a>Czyszczenie

Ostatnim krokiem jest zamknięcie modułu śledzącego treść i zwolnienie obiektu śledzenia treści. Należy również zatrzymać i zamknąć urządzenie.

```C
k4abt_tracker_shutdown(tracker);
k4abt_tracker_destroy(tracker);
k4a_device_stop_cameras(device);
k4a_device_close(device);
```

## <a name="full-source"></a>Pełne Źródło

```C
#include <stdio.h>
#include <stdlib.h>

#include <k4a/k4a.h>
#include <k4abt.h>

#define VERIFY(result, error)                                                                            \
    if(result != K4A_RESULT_SUCCEEDED)                                                                   \
    {                                                                                                    \
        printf("%s \n - (File: %s, Function: %s, Line: %d)\n", error, __FILE__, __FUNCTION__, __LINE__); \
        exit(1);                                                                                         \
    }                                                                                                    \

int main()
{
    k4a_device_t device = NULL;
    VERIFY(k4a_device_open(0, &device), "Open K4A Device failed!");

    // Start camera. Make sure depth camera is enabled.
    k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
    deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
    deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
    VERIFY(k4a_device_start_cameras(device, &deviceConfig), "Start K4A cameras failed!");

    k4a_calibration_t sensor_calibration;
    VERIFY(k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration),
        "Get depth camera calibration failed!");

    k4abt_tracker_t tracker = NULL;
    k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
    VERIFY(k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker), "Body tracker initialization failed!");

    int frame_count = 0;
    do
    {
        k4a_capture_t sensor_capture;
        k4a_wait_result_t get_capture_result = k4a_device_get_capture(device, &sensor_capture, K4A_WAIT_INFINITE);
        if (get_capture_result == K4A_WAIT_RESULT_SUCCEEDED)
        {
            frame_count++;
            k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
            k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
            if (queue_capture_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                // It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Add capture to tracker process queue timeout!\n");
                break;
            }
            else if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
            {
                printf("Error! Add capture to tracker process queue failed!\n");
                break;
            }

            k4abt_frame_t body_frame = NULL;
            k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
            if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
            {
                // Successfully popped the body tracking result. Start your processing

                size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
                printf("%zu bodies are detected!\n", num_bodies);

                k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
            }
            else if (pop_frame_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                //  It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Pop body frame result timeout!\n");
                break;
            }
            else
            {
                printf("Pop body frame result failed!\n");
                break;
            }
        }
        else if (get_capture_result == K4A_WAIT_RESULT_TIMEOUT)
        {
            // It should never hit time out when K4A_WAIT_INFINITE is set.
            printf("Error! Get depth frame time out!\n");
            break;
        }
        else
        {
            printf("Get depth capture returned error: %d\n", get_capture_result);
            break;
        }

    } while (frame_count < 100);

    printf("Finished body tracking processing!\n");

    k4abt_tracker_shutdown(tracker);
    k4abt_tracker_destroy(tracker);
    k4a_device_stop_cameras(device);
    k4a_device_close(device);

    return 0;
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Pobieranie wyników śledzenia ciała](get-body-tracking-results.md)
