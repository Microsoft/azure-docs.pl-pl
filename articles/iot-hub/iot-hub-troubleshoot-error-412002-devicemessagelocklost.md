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
ms.openlocfilehash: 7d48474d88a60c73f6094d3b9e65017c23404d8a
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92144261"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

W tym artykule opisano przyczyny i rozwiązania **412002 błędów DeviceMessageLockLost** .

## <a name="symptoms"></a>Objawy

Podczas próby wysłania komunikatu z chmury do urządzenia żądanie kończy się niepowodzeniem z błędem **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Przyczyna

Gdy urządzenie odbiera komunikat z chmury do urządzenia z kolejki (na przykład za pomocą [`ReceiveAsync()`](/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet) ), komunikat jest zablokowany przez IoT Hub dla czasu trwania blokady o długości jednej minuty. Jeśli urządzenie próbuje zakończyć komunikat po upływie limitu czasu blokady, IoT Hub zgłasza ten wyjątek.

## <a name="solution"></a>Rozwiązanie

Jeśli IoT Hub nie otrzyma powiadomienia w czasie trwania limitu czasu blokady jednominutowej, ustawia komunikat z powrotem do stanu z *kolejki* . Urządzenie może spróbować ponownie otrzymać komunikat. Aby zapobiec wystąpieniu błędu w przyszłości, należy wdrożyć logikę po stronie urządzenia, aby zakończyć komunikat w ciągu minuty od odebrania komunikatu. Nie można zmienić tego czasu na minutę.