---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/26/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 91b6f1ed06fbf1f5575650f96f4622b3df9ca083
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98187330"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

W kolejnym kroku utwórz tożsamość urządzenia i zapisz jego klucz do późniejszego użycia. Ta tożsamość urządzenia jest używana przez aplikację symulacji do wysyłania komunikatów do centrum IoT Hub. Ta funkcja jest niedostępna w programie PowerShell lub w przypadku używania szablonu Azure Resource Manager. Poniższe kroki przedstawiają sposób tworzenia symulowanego urządzenia przy użyciu [Azure Portal](https://portal.azure.com).

1. Otwórz witrynę [Azure Portal](https://portal.azure.com) i zaloguj się do konta platformy Azure.

2. Wybierz pozycję **grupy zasobów** , a następnie wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**.

3. Na liście zasobów wybierz Centrum IoT. W tym samouczku jest używane centrum **ContosoTestHub**. Wybierz pozycję **Urządzenia IoT** w okienku centrum.

4. Wybierz pozycję **+ Nowe**. W okienku Dodawanie urządzenia wypełnij pole identyfikatora urządzenia. W tym samouczku jest używany identyfikator **Contoso-Test-Device**. Pozostaw klucze puste i zaznacz pole **Automatycznie generuj klucze**. Upewnij się, że pozycja **Połącz urządzenie z centrum IoT Hub** została włączona. Wybierz pozycję **Zapisz**.

   ![Ekran Dodaj urządzenie](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Teraz, gdy została utworzona, wybierz urządzenie, aby wyświetlić wygenerowane klucze. Wybierz ikonę kopiowania w kluczu podstawowym i Zapisz ją w tym notatniku dla fazy testowania w tym samouczku.

   ![Szczegóły urządzenia, w tym klucze](./media/iot-hub-include-create-simulated-device-portal/device-details.png)