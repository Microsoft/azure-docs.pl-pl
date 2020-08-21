---
ms.openlocfilehash: e46a56742ab8b98c53c1cd05e840e8ad4b8a73da
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682304"
---
W kroku [generowanie i wdrażanie manifestu wdrażania IoT Edge](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) w Visual Studio Code rozwiń węzeł **LVA-Sample-Device** w obszarze **Azure IoT Hub** (w lewej dolnej sekcji). Powinny zostać wyświetlone następujące wdrożone moduły:

* Moduł analizy wideo na żywo o nazwie `lvaEdge`
* `rtspsim`Moduł, który symuluje serwer RTSP, który działa jako źródło kanału informacyjnego wideo na żywo

  ![Moduły](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Jeśli używasz własnego urządzenia brzegowego zamiast zainicjowanego przez nasz skrypt Instalatora, przejdź do urządzenia brzegowego i uruchom następujące polecenia z **uprawnieniami administratora**, aby ściągnąć i przechować przykładowy plik wideo używany do tego przewodnika Szybki Start:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
