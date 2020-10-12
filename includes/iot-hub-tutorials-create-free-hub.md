---
title: plik dołączany
description: plik dołączany
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 367a0b1d17f8d5ebe4f46835ace963b00e75354e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "68229299"
---
Aby utworzyć usługę IoT Hub przy użyciu witryny Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Utwórz zasób**  >  **Internet rzeczy**  >  **IoT Hub**.

    ![Wybierz, aby zainstalować usługę IoT Hub](media/iot-hub-tutorials-create-free-hub/selectiothub.png)

1. Aby utworzyć usługę IoT Hub z warstwą bezpłatną, użyj wartości z poniższych tabel:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Subskrypcja | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Grupa zasobów | Utwórz nową. W tym samouczku użyto nazwy **tutorials-iot-hub-rg**. |
    | Region | W tym samouczku jest używany region **Zachodnie stany USA**. Można wybrać region znajdujący się najbliżej. |
    | Nazwa | W poniższym zrzucie ekranu użyto nazwy **tutorials-iot-hub**. Podczas tworzenia centrum należy wybrać własną unikatową nazwę. |

    ![Ustawienia centrum 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Warstwa cenowa i warstwa skali | Bezpłatna F1. W ramach subskrypcji można mieć tylko jedno centrum z warstwą bezpłatną. |
    | Jednostki usługi IoT Hub | 1 |

    ![Ustawienia centrum 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. Kliknij przycisk **Utwórz**. Tworzenie centrum może zająć kilka minut.

    ![Ustawienia centrum 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. Zanotuj nazwę wybranego centrum IoT. Będzie potrzebna w dalszej części samouczka.
