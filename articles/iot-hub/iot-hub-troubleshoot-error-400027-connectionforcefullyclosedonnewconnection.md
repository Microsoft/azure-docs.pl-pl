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
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960531"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

W tym artykule opisano przyczyny i rozwiązania **400027 błędów ConnectionForcefullyClosedOnNewConnection** .

## <a name="symptoms"></a>Objawy

Nie można wykonać operacji krzyżowej urządzenia z chmurą (na przykład właściwości zgłoszonych odczyt lub poprawka) lub bezpośredniego wywołania metody. kod błędu **400027**.

## <a name="cause"></a>Przyczyna

Inny klient utworzył nowe połączenie, aby IoT Hub przy użyciu tych samych poświadczeń, dlatego IoT Hub zamknięte poprzednie połączenie. IoT Hub nie zezwala więcej niż jednemu klientowi na łączenie się przy użyciu tego samego zestawu poświadczeń.

## <a name="solution"></a>Rozwiązanie

Upewnij się, że każdy klient łączy się z IoT Hub przy użyciu własnej tożsamości.