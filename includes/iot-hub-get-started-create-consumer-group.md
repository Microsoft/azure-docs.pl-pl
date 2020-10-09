---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "66249104"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Dodawanie grupy odbiorców do centrum IoT Hub

[Grupy konsumentów](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) zapewniają niezależne widoki w strumieniu zdarzeń, które umożliwiają aplikacjom i usługom platformy Azure niezależne korzystanie z danych z tego samego punktu końcowego centrum zdarzeń. W tej sekcji dodasz grupę odbiorców do wbudowanego punktu końcowego usługi IoT Hub, który jest używany w dalszej części tego samouczka, aby pobrać dane z punktu końcowego.

Aby dodać grupę odbiorców do centrum IoT Hub, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) otwórz swoje centrum IoT Hub.

2. W okienku po lewej stronie wybierz pozycję **wbudowane punkty końcowe**, wybierz pozycję **zdarzenia** w okienku po prawej stronie, a następnie wprowadź nazwę w obszarze **grupy odbiorców**. Wybierz pozycję **Zapisz**.

   ![Tworzenie grupy odbiorców w centrum IoT Hub](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
