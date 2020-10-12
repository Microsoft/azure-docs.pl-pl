---
title: plik dołączany
description: plik dołączany
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "70050454"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Aby utworzyć zasady dostępu współdzielonego, które przyznają uprawnienia do **odczytu i rejestracji** **usługi** , i Pobierz parametry połączenia dla tych zasad, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **grupy zasobów**. Wybierz grupę zasobów, w której znajduje się centrum, a następnie wybierz centrum z listy zasobów.

1. W okienku po lewej stronie centrum wybierz pozycję **zasady dostępu współdzielonego**.

1. W górnym menu powyżej listy zasad wybierz pozycję **Dodaj**.

1. W obszarze **Dodawanie zasad dostępu współdzielonego**Wprowadź opisową nazwę zasad, taką jak *serviceAndRegistryRead*. W obszarze **uprawnienia**wybierz pozycję **Rejestr odczyt** i **Usługa łączenie**, a następnie wybierz pozycję **Utwórz**.

    ![Pokaż, jak dodać nowe zasady dostępu współdzielonego](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Wybierz nowe zasady z listy zasad.

1. W obszarze **klucze dostępu współdzielonego**wybierz ikonę kopiowania **parametrów połączenia — klucz podstawowy** i Zapisz wartość.

    ![Sposób pobierania parametrów połączenia](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Aby uzyskać więcej informacji na temat IoT Hub zasad i uprawnień dostępu współdzielonego, zobacz [Kontrola dostępu i uprawnienia](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
