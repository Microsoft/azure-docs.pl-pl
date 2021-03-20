---
ms.openlocfilehash: 882ba60e16f770651a1d9fe3b02b61be2b4c34c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99531884"
---
Użyjesz usługi Analiza filmów wideo na żywo w IoT Edge module, aby wykryć ruch w przychodzącym strumieniu wideo na żywo i wysyłać zdarzenia do IoT Hub. Aby wyświetlić te zdarzenia, wykonaj następujące kroki:

1. Otwórz okienko Eksploratora w Visual Studio Code i Wyszukaj usługę Azure IoT Hub w lewym dolnym rogu.
1. Rozwiń węzeł **urządzenia** .
1. Kliknij prawym przyciskiem myszy pozycję **LVA-Sample-Device** i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

    ![Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia](../../../media/quickstarts/start-monitoring-iothub-events.png)

> [!NOTE]
> Może zostać wyświetlony monit o podanie wbudowanych informacji o punkcie końcowym dla IoT Hub. Aby uzyskać te informacje, w Azure Portal przejdź do IoT Hub i poszukaj opcji **wbudowane punkty końcowe** w lewym okienku nawigacji. Kliknij tam i Wyszukaj **punkt końcowy zgodny z centrum zdarzeń** w sekcji **punkt końcowy zgodny z centrum zdarzeń** . Skopiuj i Użyj tekstu w polu. Punkt końcowy będzie wyglądać następująco:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
