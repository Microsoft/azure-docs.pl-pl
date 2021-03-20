---
title: Rozwiązywanie problemów z usługą Azure IoT Hub błąd 400027 ConnectionForcefullyClosedOnNewConnection
description: Dowiedz się, jak naprawić błąd 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: e5d1dc345c72d77be6172fb9c3a10eb2f38d186a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94506332"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

W tym artykule opisano przyczyny i rozwiązania **400027 błędów ConnectionForcefullyClosedOnNewConnection** .

## <a name="symptoms"></a>Objawy

Urządzenie zostało rozłączone za pomocą **Communication_Error** jako **ConnectionStatusChangeReason** przy użyciu zestawu .NET SDK i typu transportu MQTT.

Nie można wykonać operacji krzyżowej urządzenia z chmurą (na przykład właściwości zgłoszonych odczyt lub poprawka) lub bezpośredniego wywołania metody. kod błędu **400027**.

## <a name="cause"></a>Przyczyna

Inny klient utworzył nowe połączenie, aby IoT Hub przy użyciu tych samych poświadczeń, dlatego IoT Hub zamknięte poprzednie połączenie. IoT Hub nie zezwala więcej niż jednemu klientowi na łączenie się przy użyciu tego samego zestawu poświadczeń.

## <a name="solution"></a>Rozwiązanie

Upewnij się, że każdy klient łączy się z IoT Hub przy użyciu własnej tożsamości.