---
title: Korzystanie z istniejącego Centrum IoT Hub z rozwiązaniem symulacji urządzenia — Azure | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania akceleratora rozwiązania urządzenia do używania istniejącego IoT Hub.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 1f89e23d7bb279e7cce5c104060cc7898517f8b7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009368"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Use an existing IoT hub with the Device Simulation solution accelerator (Używanie istniejącego centrum IoT Hub z akceleratorem rozwiązania do symulacji urządzenia)

Podczas wdrażania symulacji urządzenia można także wybrać wdrożenie Centrum IoT Hub do użycia w symulacji. Ta opcja służy do wdrażania [Centrum IoT w warstwie S2 z pojedynczą jednostką skalowania](../iot-hub/iot-hub-scaling.md). W przypadku wdrożenia tego opcjonalnego Centrum IoT można nadal wybrać inną IoT Hub do uruchomienia symulacji.

Jeśli nie zdecydujesz się na wdrożenie opcjonalnej IoT Hub, musisz użyć własnego centrum dla wszystkich symulacji, które są uruchamiane.

Jeśli nie masz usługi IoT Hub, możesz zawsze utworzyć nową aplikację na podstawie [Azure Portal](https://portal.azure.com).

Aby użyć istniejącego już Centrum IoT Hub, potrzebne są parametry połączenia dla zasad dostępu współdzielonego **iothubowner** . Te parametry połączenia można uzyskać z [Azure Portal](https://portal.azure.com):

1. Na stronie Konfiguracja centrum w portalu kliknij pozycję **zasady dostępu współużytkowanego**.

1. Kliknij pozycję **iothubowner**.

1. Skopiuj podstawowe lub pomocnicze parametry połączenia.

[![Pobieranie parametrów połączenia](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Użyj parametrów połączenia skopiowanych podczas konfigurowania symulacji:

![Konfigurowanie symulacji](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Następne kroki

W tym przewodniku krok po kroku przedstawiono sposób użycia istniejącego Centrum IoT w symulacji. Następnie warto dowiedzieć się, jak [utworzyć zaawansowany model urządzenia](iot-accelerators-device-simulation-advanced-device.md) dla symulacji.
