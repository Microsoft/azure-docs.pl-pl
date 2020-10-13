---
title: Rozwiązywanie problemów z usługą Azure IoT Hub błąd 412002 DeviceMessageLockLost
description: Dowiedz się, jak naprawić błąd 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 66461b23432a3e8b7ae4ad1fdc078fba9ca05646
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960765"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

W tym artykule opisano przyczyny i rozwiązania **412002 błędów DeviceMessageLockLost** .

## <a name="symptoms"></a>Objawy

Podczas próby wysłania komunikatu z chmury do urządzenia żądanie kończy się niepowodzeniem z błędem **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Przyczyna

Gdy urządzenie odbiera komunikat z chmury do urządzenia z kolejki (na przykład za pomocą [`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet) ), komunikat jest zablokowany przez IoT Hub dla czasu trwania blokady o długości jednej minuty. Jeśli urządzenie próbuje zakończyć komunikat po upływie limitu czasu blokady, IoT Hub zgłasza ten wyjątek.

## <a name="solution"></a>Rozwiązanie

Jeśli IoT Hub nie otrzyma powiadomienia w czasie trwania limitu czasu blokady jednominutowej, ustawia komunikat z powrotem do stanu z *kolejki* . Urządzenie może spróbować ponownie otrzymać komunikat. Aby zapobiec wystąpieniu błędu w przyszłości, należy wdrożyć logikę po stronie urządzenia, aby zakończyć komunikat w ciągu minuty od odebrania komunikatu. Nie można zmienić tego czasu na minutę.