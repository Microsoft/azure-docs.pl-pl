---
title: plik dołączany
description: plik dołączany
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "69558442"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Aby uzyskać IoT Hub parametry połączenia dla zasad **usługi** , wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **grupy zasobów**. Wybierz grupę zasobów, w której znajduje się centrum, a następnie wybierz centrum z listy zasobów.

1. W okienku po lewej stronie Centrum IoT wybierz pozycję **zasady dostępu współdzielonego**.

1. Z listy zasad wybierz pozycję zasady **usługi** .

1. W obszarze **klucze dostępu współdzielonego**wybierz ikonę kopiowania **parametrów połączenia — klucz podstawowy** i Zapisz wartość.

    ![Sposób pobierania parametrów połączenia](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Aby uzyskać więcej informacji na temat IoT Hub zasad i uprawnień dostępu współdzielonego, zobacz [Kontrola dostępu i uprawnienia](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
