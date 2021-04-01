---
ms.openlocfilehash: 3f92bae608284c8b619be34a0e08f15e831bf88e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101751096"
---
W kroku [generowanie i wdrażanie manifestu wdrażania IoT Edge](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) w Visual Studio Code rozwiń węzeł **LVA-Sample-Device** w obszarze **Azure IoT Hub** (w lewej dolnej sekcji). Powinny zostać wyświetlone następujące wdrożone moduły:

* Moduł analizy wideo na żywo o nazwie `lvaEdge`
* `rtspsim`Moduł, który symuluje serwer RTSP, który działa jako źródło kanału informacyjnego wideo na żywo

  ![Moduły](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> W powyższych krokach przyjęto założenie, że używana jest maszyna wirtualna utworzona przez skrypt Instalatora. Jeśli używasz własnego urządzenia brzegowego, przejdź do urządzenia brzegowego i uruchom następujące polecenia z **uprawnieniami administratora**, aby ściągnąć i przechować przykładowy plik wideo używany do tego przewodnika Szybki Start:  

```
mkdir /home/lvaedgeuser/samples
mkdir /home/lvaedgeuser/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
```
