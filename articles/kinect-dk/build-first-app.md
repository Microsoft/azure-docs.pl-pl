---
title: Szybki Start — Tworzenie pierwszej aplikacji Azure urządzenia Kinect
description: Ten przewodnik Szybki Start przeprowadzi Cię przez proces tworzenia nowej aplikacji przy użyciu usługi Azure urządzenia Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: urządzenia Kinect, Azure, czujnik, zestaw SDK, mikrofon, dostęp MICS, dane MIC
ms.openlocfilehash: 3632145b3f3b63023e0c66e3cf99903231802edf
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277952"
---
# <a name="quickstart-build-your-first-azure-kinect-application"></a>Szybki Start: Tworzenie pierwszej aplikacji usługi Azure urządzenia Kinect

Rozpoczynanie pracy z usługą Azure urządzenia Kinect DK? Ten przewodnik Szybki Start umożliwi rozpoczęcie pracy z urządzeniem.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Omówione są następujące funkcje:

- [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
- [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
- [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
- [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="prerequisites"></a>Wymagania wstępne

1. [Skonfiguruj urządzenie Azure urządzenia Kinect DK](set-up-azure-kinect-dk.md).
2. [Pobierz](sensor-sdk-download.md) i Zainstaluj zestaw SDK czujnika usługi Azure urządzenia Kinect.

## <a name="headers"></a>Nagłówki

Istnieje tylko jeden nagłówek, który będzie potrzebny `k4a.h` . Upewnij się, że wybrany kompilator jest skonfigurowany przy użyciu biblioteki zestawu SDK i folderów dołączania. Potrzebne są również `k4a.lib` `k4a.dll` pliki i. Warto zapoznać się z tematem [Dodawanie biblioteki usługi Azure urządzenia Kinect do projektu](add-library-to-project.md).

```C
#include <k4a/k4a.h>
```

## <a name="finding-an-azure-kinect-dk-device"></a>Znajdowanie urządzenia z platformą Azure urządzenia Kinect DK

Do komputera można podłączyć wiele urządzeń z usługą Azure urządzenia Kinect DK. Najpierw rozpocznie się wyszukiwanie, ile lub w ogóle są połączone przy użyciu [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc) funkcji. Ta funkcja powinna działać od razu, bez żadnej dodatkowej konfiguracji.

```C
uint32_t count = k4a_device_get_installed_count();
```

Po ustaleniu, że urządzenie jest podłączone do komputera, można je otworzyć za pomocą polecenia [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) . Możesz podać indeks urządzenia, które chcesz otworzyć, lub po prostu użyć go `K4A_DEVICE_DEFAULT` jako pierwszego.

```C
// Open the first plugged in Kinect device
k4a_device_t device = NULL;
k4a_device_open(K4A_DEVICE_DEFAULT, &device);
```
Tak jak w przypadku większości elementów w bibliotece usługi Azure urządzenia Kinect, po otwarciu elementu należy zamknąć go również po zakończeniu pracy z! Po zamknięciu programu Pamiętaj, aby wykonać wywołanie [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423) .

```C
k4a_device_close(device);
```

Gdy urządzenie zostanie otwarte, możemy wykonać test, aby upewnić się, że działa. Przejdźmy do numeru seryjnego urządzenia!

```C
// Get the size of the serial number
size_t serial_size = 0;
k4a_device_get_serialnum(device, NULL, &serial_size);

// Allocate memory for the serial, then acquire it
char *serial = (char*)(malloc(serial_size));
k4a_device_get_serialnum(device, serial, &serial_size);
printf("Opened device: %s\n", serial);
free(serial);
```

## <a name="starting-the-cameras"></a>Uruchamianie kamer

Po otwarciu urządzenia należy skonfigurować kamerę przy użyciu [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) obiektu. Konfiguracja aparatu fotograficznego ma różne opcje. Wybierz ustawienia, które najlepiej pasują do Twojego scenariusza.

```C
// Configure a stream of 4096x3072 BRGA color data at 15 frames per second
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps       = K4A_FRAMES_PER_SECOND_15;
config.color_format     = K4A_IMAGE_FORMAT_COLOR_BGRA32;
config.color_resolution = K4A_COLOR_RESOLUTION_3072P;

// Start the camera with the given configuration
k4a_device_start_cameras(device, &config);

// ...Camera capture and application specific code would go here...

// Shut down the camera when finished with application logic
k4a_device_stop_cameras(device);
```

## <a name="error-handling"></a>Obsługa błędów

W przypadku zwięzłości i przejrzystości nie pokazujemy obsługi błędów w niektórych przykładach wbudowanych. Jednak obsługa błędów jest zawsze ważna! Wiele funkcji zwróci ogólny typ sukcesu/niepowodzenia [`k4a_result_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga4b419a99aa2220b076a4520dc2afd1e5.html#ga4b419a99aa2220b076a4520dc2afd1e5) lub bardziej specyficzny wariant ze szczegółowymi informacjami, takimi jak [`k4a_wait_result_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga44c7c0c1cfba7c879e9e2da1a869e4ee.html#ga44c7c0c1cfba7c879e9e2da1a869e4ee) . Sprawdź dokumenty lub IntelliSense dla każdej funkcji, aby zobaczyć, jakie komunikaty o błędach powinny być widoczne od!

[`K4A_SUCCEEDED`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___macros_ga8e5b48150bc243c6052793bd830c2fcd.html#ga8e5b48150bc243c6052793bd830c2fcd) [`K4A_FAILED`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___macros_ga7c2e32349135d008b6f836c571d434b4.html#ga7c2e32349135d008b6f836c571d434b4) Aby sprawdzić wynik funkcji, można użyć makr i. W związku z tym zamiast otwierania urządzenia z platformą Azure urządzenia Kinect DK możemy chronić wywołanie funkcji w następujący sposób:

```C
// Open the first plugged in Kinect device
k4a_device_t device = NULL;
if ( K4A_FAILED( k4a_device_open(K4A_DEVICE_DEFAULT, &device) ) )
{
    printf("Failed to open k4a device!\n");
    return;
}
```

## <a name="full-source"></a>Pełne Źródło

```C
#pragma comment(lib, "k4a.lib")
#include <k4a/k4a.h>

#include <stdio.h>
#include <stdlib.h>

int main()
{
    uint32_t count = k4a_device_get_installed_count();
    if (count == 0)
    {
        printf("No k4a devices attached!\n");
        return 1;
    }

    // Open the first plugged in Kinect device
    k4a_device_t device = NULL;
    if (K4A_FAILED(k4a_device_open(K4A_DEVICE_DEFAULT, &device)))
    {
        printf("Failed to open k4a device!\n");
        return 1;
    }

    // Get the size of the serial number
    size_t serial_size = 0;
    k4a_device_get_serialnum(device, NULL, &serial_size);

    // Allocate memory for the serial, then acquire it
    char *serial = (char*)(malloc(serial_size));
    k4a_device_get_serialnum(device, serial, &serial_size);
    printf("Opened device: %s\n", serial);
    free(serial);

    // Configure a stream of 4096x3072 BRGA color data at 15 frames per second
    k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
    config.camera_fps       = K4A_FRAMES_PER_SECOND_15;
    config.color_format     = K4A_IMAGE_FORMAT_COLOR_BGRA32;
    config.color_resolution = K4A_COLOR_RESOLUTION_3072P;

    // Start the camera with the given configuration
    if (K4A_FAILED(k4a_device_start_cameras(device, &config)))
    {
        printf("Failed to start cameras!\n");
        k4a_device_close(device);
        return 1;
    }

    // Camera capture and application specific code would go here

    // Shut down the camera when finished with application logic
    k4a_device_stop_cameras(device);
    k4a_device_close(device);

    return 0;
}

```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak znaleźć i otworzyć urządzenie Azure urządzenia Kinect DK przy użyciu zestawu SDK czujnika
> [!div class="nextstepaction"]
>[Znajdowanie i otwieranie urządzenia](find-then-open-device.md)
