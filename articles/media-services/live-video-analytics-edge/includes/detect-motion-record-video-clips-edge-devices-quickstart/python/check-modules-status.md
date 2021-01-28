---
ms.openlocfilehash: 53052097fa6616f889b710c58488a9f7a616168d
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956328"
---
W kroku [generowanie i wdrażanie manifestu wdrażania IoT Edge](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) w Visual Studio Code rozwiń węzeł **LVA-Sample-Device** w obszarze **Azure IoT Hub** (w lewej dolnej sekcji). Powinny zostać wyświetlone następujące wdrożone moduły:

* Moduł analizy wideo na żywo o nazwie `lvaEdge`
* `rtspsim`Moduł, który symuluje serwer RTSP, który działa jako źródło kanału informacyjnego wideo na żywo

  ![Moduły](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> W powyższych krokach przyjęto założenie, że używana jest maszyna wirtualna utworzona przez skrypt Instalatora. Jeśli używasz własnego urządzenia brzegowego, przejdź do urządzenia brzegowego i uruchom następujące polecenia z **uprawnieniami administratora**, aby ściągnąć i przechować przykładowy plik wideo używany do tego przewodnika Szybki Start:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
