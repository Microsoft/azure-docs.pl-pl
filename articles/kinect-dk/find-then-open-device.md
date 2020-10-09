---
title: Znajdź, a następnie otwórz urządzenie Azure urządzenia Kinect
description: Dowiedz się, jak znaleźć i otworzyć urządzenie Azure urządzenia Kinect przy użyciu zestawu Azure urządzenia Kinect Senor SDK.
author: cedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: urządzenia Kinect, Azure, czujnik, zestaw SDK, Głębokość, RGB, urządzenie, Znajdź, otwarte
ms.openlocfilehash: 67fc93b924d5d663bb43098969c54d1975bd5895
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277475"
---
# <a name="find-then-open-the-azure-kinect-device"></a>Znajdź, a następnie otwórz urządzenie Azure urządzenia Kinect

W tym artykule opisano, jak można znaleźć, a następnie otworzyć platformę Azure urządzenia Kinect DK. W tym artykule wyjaśniono, jak obsłużyć przypadek, w którym istnieje wiele urządzeń podłączonych do maszyny.

Możesz również zapoznać się z [przykładowym wyliczeniem zestawu SDK](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/enumerate) , który ilustruje sposób korzystania z funkcji w tym artykule.

Omówione są następujące funkcje:
 * [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
 * [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
 * [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
 * [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="discover-the-number-of-connected-devices"></a>Odnajdywanie liczby połączonych urządzeń

Najpierw Pobierz liczbę aktualnie połączonych urządzeń urządzenia Kinect platformy Azure przy użyciu programu [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc) .

```C
uint32_t device_count = k4a_device_get_installed_count();

printf("Found %d connected devices:\n", device_count);
```

## <a name="open-a-device"></a>Otwórz urządzenie

Aby uzyskać informacje o urządzeniu lub odczytać z niego dane, musisz najpierw otworzyć dojście do urządzenia za pomocą programu [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) .

```C
k4a_device_t device = NULL;

for (uint8_t deviceIndex = 0; deviceIndex < device_count; deviceIndex++)
{
    if (K4A_RESULT_SUCCEEDED != k4a_device_open(deviceIndex, &device))
    {
        printf("%d: Failed to open device\n", deviceIndex);
        continue;
    }

    ...

    k4a_device_close(device);
}
```

`index`Parametr wskazuje, [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) które urządzenie ma zostać otwarte, jeśli istnieje więcej niż jedno połączenie. Jeśli oczekujesz, że tylko jedno urządzenie ma być połączone, możesz przekazać argument z `K4A_DEVICE_DEFAULT` lub 0, aby wskazać pierwsze urządzenie.

Za każdym razem, gdy otworzysz urządzenie, musisz wywołać, [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423) gdy skończysz korzystać z dojścia. Żadne inne uchwyty nie mogą być otwierane na tym samym urządzeniu, dopóki nie zamknięto uchwytu.

## <a name="identify-a-specific-device"></a>Identyfikowanie określonego urządzenia

Kolejność, w jakiej urządzenia są wyliczane według indeksu, nie będzie zmieniana do czasu dołączenia lub odłączenia urządzeń. Aby zidentyfikować urządzenie fizyczne, należy użyć numeru seryjnego urządzenia.

Aby odczytać numer seryjny z urządzenia, użyj [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22) funkcji po otwarciu dojścia.

W tym przykładzie pokazano, jak przydzielić odpowiednią ilość pamięci do przechowywania numeru seryjnego.

```C
char *serial_number = NULL;
size_t serial_number_length = 0;

if (K4A_BUFFER_RESULT_TOO_SMALL != k4a_device_get_serialnum(device, NULL, &serial_number_length))
{
    printf("%d: Failed to get serial number length\n", deviceIndex);
    k4a_device_close(device);
    device = NULL;
    continue;
}

serial_number = malloc(serial_number_length);
if (serial_number == NULL)
{
    printf("%d: Failed to allocate memory for serial number (%zu bytes)\n", deviceIndex, serial_number_length);
    k4a_device_close(device);
    device = NULL;
    continue;
}

if (K4A_BUFFER_RESULT_SUCCEEDED != k4a_device_get_serialnum(device, serial_number, &serial_number_length))
{
    printf("%d: Failed to get serial number\n", deviceIndex);
    free(serial_number);
    serial_number = NULL;
    k4a_device_close(device);
    device = NULL;
    continue;
}

printf("%d: Device \"%s\"\n", deviceIndex, serial_number);
```

## <a name="open-the-default-device"></a>Otwieranie urządzenia domyślnego

W większości aplikacji będzie dostępna tylko jedna usługa Azure urządzenia Kinect DK dołączona do tego samego komputera. Jeśli musisz tylko połączyć się z pojedynczym oczekiwanym urządzeniem, możesz wywołać polecenie z, [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) `index` `K4A_DEVICE_DEFAULT` Aby otworzyć pierwsze urządzenie.

```C
k4a_device_t device = NULL;
uint32_t device_count = k4a_device_get_installed_count();

if (device_count != 1)
{
    printf("Unexpected number of devices found (%d)\n", device_count);
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_open(K4A_DEVICE_DEFAULT, &device))
{
    printf("Failed to open device\n");
    goto Exit;
}
```

## <a name="next-steps"></a>Następne kroki

>[!div class="nextstepaction"]
>[Pobierz obrazy](retrieve-images.md)

>[!div class="nextstepaction"]
>[Pobierz przykłady IMU](retrieve-imu-samples.md)

