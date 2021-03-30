---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80756906"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Aby utworzyć zasady dostępu współdzielonego, które przyznają uprawnienia do **nawiązywania połączeń z usługą** i **zapisu w rejestrze** i uzyskać parametry połączenia dla tych zasad, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **grupy zasobów**. Wybierz grupę zasobów, w której znajduje się centrum, a następnie wybierz centrum z listy zasobów.

1. W okienku po lewej stronie centrum wybierz pozycję **zasady dostępu współdzielonego**.

1. W górnym menu powyżej listy zasad wybierz pozycję **Dodaj**.

1. W obszarze **Dodawanie zasad dostępu współdzielonego** Wprowadź opisową nazwę zasad, taką jak *serviceAndRegistryReadWrite*. W obszarze **uprawnienia** wybierz pozycję **zapis rejestru** i **Usługa połączenie usługi**, a następnie wybierz pozycję **Utwórz**. (W przypadku wybrania **zapisu w rejestrze** zostanie automatycznie dołączone uprawnienie **odczyt rejestru** ).

    ![Pokaż, jak dodać nowe zasady dostępu współdzielonego](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. Wybierz nowe zasady z listy zasad.

1. W obszarze **klucze dostępu współdzielonego** wybierz ikonę kopiowania **parametrów połączenia — klucz podstawowy** i Zapisz wartość.

    ![Sposób pobierania parametrów połączenia](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

Aby uzyskać więcej informacji na temat IoT Hub zasad i uprawnień dostępu współdzielonego, zobacz [Kontrola dostępu i uprawnienia](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
