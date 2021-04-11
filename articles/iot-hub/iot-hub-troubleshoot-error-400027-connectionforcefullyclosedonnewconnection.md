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
ms.openlocfilehash: cd531abe1a10abead26055c6b0d4dd328cd3e836
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061370"
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