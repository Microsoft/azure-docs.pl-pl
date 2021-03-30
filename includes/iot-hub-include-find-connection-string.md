---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d7ac457041474f4e774414b1d5e6f9ed09dc856
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67183328"
---
<!-- this tells how to get the connection string for your hub -->
<!-- This assumes the user is looking at his hub in the portal. -->

Po utworzeniu centrum pobierz dla niego parametry połączenia. Umożliwiają one łączenie z centrum urządzeń i aplikacji. 

1. Kliknij centrum, aby wyświetlić okienko usługi IoT Hub z ustawieniami i tak dalej. Kliknij pozycję **Zasady dostępu współużytkowanego**.
   
2. W obszarze **Zasady dostępu współużytkowanego** wybierz zasady **iothubowner**. 

3. W obszarze **Klucze dostępu współużytkowanego** skopiuj pozycję **Parametry połączenia — klucz podstawowy** do użycia później.

    ![Sposób pobierania parametrów połączenia](./media/iot-hub-include-find-connection-string/iot-hub-get-connection-string.png)

    Aby uzyskać więcej informacji, zobacz sekcję [Access control (Kontrola dostępu)](../articles/iot-hub/iot-hub-devguide-security.md) w temacie „IoT Hub developer guide” (Przewodnik deweloperów usługi IoT Hub).
