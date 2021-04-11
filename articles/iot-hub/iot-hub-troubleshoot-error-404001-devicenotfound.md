---
title: Rozwiązywanie problemów z usługą Azure IoT Hub błąd 404001 DeviceNotFound
description: Dowiedz się, jak naprawić błąd 404001 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 09f3c00dadc6e95aae01fb8082fb746e155d4688
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061285"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

W tym artykule opisano przyczyny i rozwiązania **404001 błędów DeviceNotFound** .

## <a name="symptoms"></a>Objawy

Podczas komunikacji między chmurą a urządzeniem (C2D), takim jak wiadomość C2D, Aktualizacja z przędzą lub metoda bezpośrednia, operacja kończy się niepowodzeniem z powodu błędu **404001 DeviceNotFound**.

## <a name="cause"></a>Przyczyna

Operacja nie powiodła się, ponieważ nie można odnaleźć urządzenia przez IoT Hub. Urządzenie nie jest zarejestrowane lub wyłączone.

## <a name="solution"></a>Rozwiązanie

Zarejestruj użyty identyfikator urządzenia, a następnie spróbuj ponownie.