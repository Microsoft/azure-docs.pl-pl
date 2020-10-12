---
title: plik dołączany
description: plik dołączany
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "68883754"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Aby uzyskać IoT Hub parametry połączenia dla zasad **registryReadWriteymi** , wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **grupy zasobów**. Wybierz grupę zasobów, w której znajduje się centrum, a następnie wybierz centrum z listy zasobów.

2. W okienku po lewej stronie centrum wybierz pozycję **zasady dostępu współdzielonego**.

3. Z listy zasad wybierz zasady **registryReadWrite** .

4. W obszarze **klucze dostępu współdzielonego**wybierz ikonę kopiowania **parametrów połączenia — klucz podstawowy** i Zapisz wartość.

    ![Sposób pobierania parametrów połączenia](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Aby uzyskać więcej informacji na temat IoT Hub zasad i uprawnień dostępu współdzielonego, zobacz [Kontrola dostępu i uprawnienia](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
