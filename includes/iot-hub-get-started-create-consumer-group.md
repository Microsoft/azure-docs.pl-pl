---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: fbb4e53e0047b9768a70c01aecfb7f31ae213b3f
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560568"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Dodawanie grupy odbiorców do centrum IoT Hub

[Grupy konsumentów](../articles/event-hubs/event-hubs-features.md#event-consumers) zapewniają niezależne widoki w strumieniu zdarzeń, które umożliwiają aplikacjom i usługom platformy Azure niezależne korzystanie z danych z tego samego punktu końcowego centrum zdarzeń. W tej sekcji dodasz grupę odbiorców do wbudowanego punktu końcowego usługi IoT Hub, który jest używany w dalszej części tego samouczka, aby pobrać dane z punktu końcowego.

Aby dodać grupę odbiorców do centrum IoT Hub, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) otwórz swoje centrum IoT Hub.

2. W okienku po lewej stronie wybierz pozycję **wbudowane punkty końcowe**, wybierz pozycję **zdarzenia** w okienku po prawej stronie, a następnie wprowadź nazwę w obszarze **grupy odbiorców**. Wybierz pozycję **Zapisz**.

   ![Tworzenie grupy odbiorców w centrum IoT Hub](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)