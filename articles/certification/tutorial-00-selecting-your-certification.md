---
title: Program certyfikowany na platformie Azure — samouczek — Wybieranie programu certyfikacyjnego
description: Przewodnik krok po kroku umożliwiający wybranie odpowiednich programów certyfikacyjnych dla urządzenia
author: Chuckb1300
ms.author: cbroad
ms.service: certification
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: b6ab8a4f971a065764731abddf72e26c628ba6cb
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969801"
---
# <a name="tutorial-select-your-certification-program"></a>Samouczek: Wybieranie programu certyfikacyjnego

Gratulujemy wyboru programu certyfikatu platformy Azure. Przyjemnością się, że dołączymy do naszego ekosystemu certyfikowanych urządzeń. Aby rozpocząć, należy najpierw określić, które programy certyfikacji najlepiej pasują do możliwości urządzenia.

Z tego samouczka dowiesz się, jak wykonać następujące czynności:

> [!div class="checklist"]
> * Wybierz najlepsze programy certyfikacyjne dla Twojego urządzenia

## <a name="selecting-a-certification-program-for-your-device"></a>Wybieranie programu certyfikacyjnego dla urządzenia

Wszystkie urządzenia muszą spełniać wymagania podstawowe opisane przez certyfikat [**urządzenia certyfikowanego na platformie Azure**](./program-requirements-azure-certified-device.md) . Pozostałe trzy identyfikatory certyfikacji są kompilowane w ramach tego programu i zapewniają inną wartość klienta. Można wybrać co najmniej jeden z trzech przyrostek (IoT Plug and Play, zarządzane przez brzeg i wersja zapoznawcza).

1. Przejrzyj wszystkie wymagania programów certyfikacyjnych w poniższej tabeli. Szczegółowe wymagania dotyczące każdego programu można spowodowała wybranie z nagłówków.

    |Wymaganie|[IoT Plug and Play](./program-requirements-edge-secured-core.md)|[Zarządzane przez brzeg](./program-requirements-edge-managed.md)|[Zabezpieczenia na poziomie podstawowym](./program-requirements-edge-secured-core.md)|
    ---|---|---|---
    | Procesor | Dowolne|MPU/PROCESOR CPU|MPU/PROCESOR CPU|
    | System operacyjny | Dowolne|[System operacyjny warstwy 1](../iot-edge/support.md?view=iotedge-2018-06&preserve-view=true))|[System operacyjny warstwy 1](../iot-edge/support.md?view=iotedge-2018-06&preserve-view=true)|
    | Środowisko uruchomieniowe usługi IoT Edge | Nieobsługiwane |Wymagane|Wymagane|
    | Defender dla IoT | Nieobsługiwane|Wymagane|Wymagane|
    | ADU/Windows Update | Nieobsługiwane|Wymagane|Wymagane|

1. Jeśli uważasz, że rozumiesz, co jest wymagane przez urządzenie, zapoznaj się z wymaganiami technicznymi programu. Może to być certyfikat urządzenia certyfikowanego na platformie Azure lub kombinacja certyfikacji linii bazowej z jednym z trzech przyrostek. 

## <a name="next-steps"></a>Następne kroki

Teraz można przystąpić do rozpoczęcia certyfikowania urządzenia. Przejdź do następnego artykułu, aby rozpocząć projekt.
> [!div class="nextstepaction"]
>[Samouczek: Tworzenie projektu](tutorial-01-creating-your-project.md)
