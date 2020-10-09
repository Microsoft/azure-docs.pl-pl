---
title: Przechwyć synchronizację urządzeń z usługą Azure urządzenia Kinect
description: Dowiedz się, jak synchronizować urządzenia przechwytywania urządzenia Kinect platformy Azure przy użyciu zestawu SDK czujnika urządzenia Kinect platformy Azure.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: urządzenia Kinect, Azure, czujnik, zestaw SDK, Głębokość, RGB, wewnętrzny, zewnętrzny, synchronizacja, łańcuch łańcucha, przesunięcie fazy
ms.openlocfilehash: 914a29882277c11c388d3f81f0b1fe827fc2f156
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277512"
---
# <a name="capture-azure-kinect-device-synchronization"></a>Przechwyć synchronizację urządzeń z usługą Azure urządzenia Kinect

Sprzęt usługi Azure urządzenia Kinect może wyrównać czas przechwytywania obrazów kolorowych i głębi. Wyrównanie między kamerami na tym samym urządzeniu jest **synchronizacją wewnętrzną**. Wyrównanie czasu przechwytywania między wieloma połączonymi urządzeniami jest **synchronizacją zewnętrzną**.

## <a name="device-internal-synchronization"></a>Wewnętrzna synchronizacja urządzeń

Przechwytywanie obrazów między indywidualnymi kamerami jest synchronizowane ze sprzętem. W każdym [k4a_capture_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__capture__t.html) , który zawiera obrazy z czujnika koloru i głębokości, sygnatury czasowe obrazów są wyrównane na podstawie trybu operacyjnego sprzętu. Domyślnie obrazy przechwytywania są wyrównane do środka ekspozycji. Względny czas przechwycenia głębi i koloru można dostosować przy użyciu `depth_delay_off_color_usec` pola [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html).

## <a name="device-external-synchronization"></a>Zewnętrzna synchronizacja urządzeń

Zobacz [Konfigurowanie synchronizacji zewnętrznej](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices) dla konfiguracji sprzętu.

Oprogramowanie dla każdego połączonego urządzenia musi być skonfigurowane do działania w trybie **głównym** lub **podrzędnym** . To ustawienie jest konfigurowane na [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html).

W przypadku korzystania z synchronizacji zewnętrznej kamery podrzędne powinny być zawsze uruchamiane przed poprawnym wyrównaniam wzorca dla sygnatur czasowych.

### <a name="subordinate-mode"></a>Tryb podrzędny

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_SUBORDINATE
```

### <a name="master-mode"></a>Tryb główny

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_MASTER;
```

### <a name="retrieving-synchronization-jack-state"></a>Pobieranie stanu wtyczki synchronizacji

Aby programowo pobrać bieżący stan gniazd danych wejściowych i wyjściowych synchronizacji, użyj funkcji [k4a_device_get_sync_jack](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0209ac87bfd055163677321b0304e962.html#ga0209ac87bfd055163677321b0304e962) .

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak włączyć i przechwycić synchronizację urządzeń. Możesz również zapoznać się z tematem jak używać 

>[!div class="nextstepaction"]
>[Interfejs API zapisywania i odtwarzania zestawu SDK czujnika usługi Azure urządzenia Kinect](record-playback-api.md)
