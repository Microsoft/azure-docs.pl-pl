---
title: Rozwiązywanie problemów z usługą Azure IoT Hub błąd 403006 DeviceMaximumActiveFileUploadLimitExceeded
description: Dowiedz się, jak naprawić błąd 403006 DeviceMaximumActiveFileUploadLimitExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960843"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

W tym artykule opisano przyczyny i rozwiązania **403006 błędów DeviceMaximumActiveFileUploadLimitExceeded** .

## <a name="symptoms"></a>Objawy

Żądanie przekazania pliku kończy się niepowodzeniem z kodem błędu **403006** i komunikat "Liczba aktywnych żądań przekazywania plików nie może przekroczyć 10".

## <a name="cause"></a>Przyczyna

Każdy klient urządzenia jest ograniczony do [10 współbieżnych przekazywania plików](./iot-hub-devguide-quotas-throttling.md#other-limits). 

Możesz łatwo przekroczyć limit, jeśli urządzenie nie powiadomi IoT Hub o zakończeniu przekazywania plików. Ten problem jest często spowodowany przez niezawodną sieć po stronie urządzenia.

## <a name="solution"></a>Rozwiązanie

Upewnij się, że urządzenie może monitować o [zapełnienie przekazywania plików IoT Hub](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload). Następnie spróbuj [zmniejszyć czas wygaśnięcia tokenu sygnatury dostępu współdzielonego dla konfiguracji przekazywania plików](iot-hub-configure-file-upload.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat przekazywania plików, zobacz [przekazywanie plików IoT Hub](./iot-hub-devguide-file-upload.md) i [Konfigurowanie IoT Hub przekazywania plików przy użyciu Azure Portal](./iot-hub-configure-file-upload.md).