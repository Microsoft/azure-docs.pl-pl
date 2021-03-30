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
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "76960830"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

W tym artykule opisano przyczyny i rozwiązania **404001 błędów DeviceNotFound** .

## <a name="symptoms"></a>Objawy

Podczas komunikacji między chmurą a urządzeniem (C2D), takim jak wiadomość C2D, Aktualizacja z przędzą lub metoda bezpośrednia, operacja kończy się niepowodzeniem z powodu błędu **404001 DeviceNotFound**.

## <a name="cause"></a>Przyczyna

Operacja nie powiodła się, ponieważ nie można odnaleźć urządzenia przez IoT Hub. Urządzenie nie jest zarejestrowane lub wyłączone.

## <a name="solution"></a>Rozwiązanie

Zarejestruj użyty identyfikator urządzenia, a następnie spróbuj ponownie.