---
title: Udostępnianie procesora GPU na urządzeniu z systemem Azure Stack Edge z procesorem GPU Pro
description: Opisuje podejścia do udostępniania procesorów GPU na urządzeniu z systemem Azure Stack Edge w wersji brzegowej Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 6683e39cfa3601b1ae1fbbe02e69e4dc0a54e8e7
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565022"
---
# <a name="gpu-sharing-on-your-azure-stack-edge-pro-gpu-device"></a>Udostępnianie procesora GPU na urządzeniu z systemem Azure Stack Edge z procesorem GPU

Procesor graficzny (GPU) to wyspecjalizowany procesora zaprojektowany w celu przyspieszenia renderowania grafiki. Procesory GPU mogą jednocześnie przetwarzać wiele części danych, dzięki czemu są one przydatne do uczenia maszynowego, edycji wideo i aplikacji do gier. Oprócz procesora CPU do obliczeń ogólnego przeznaczenia, urządzenia GPU z systemem Azure Stack Edge mogą zawierać jeden lub dwa procesory GPU w technologii NVIDIA Tesla T4 dla obciążeń intensywnie korzystających z obliczeń, takich jak akceleracja inferencing. Aby uzyskać więcej informacji, zobacz [procesor GPU Tesla T4 firmy NVIDIA](https://www.nvidia.com/data-center/tesla-t4/).


## <a name="about-gpu-sharing"></a>Udostępnianie GPU — informacje

Wiele obciążeń związanych z przetwarzaniem maszynowym lub innymi obciążeniami obliczeniowymi może nie potrzebować dedykowanego procesora GPU. Procesory GPU mogą być współużytkowane i współużytkowane przez procesory GPU między obciążeniami kontenerów i maszyn wirtualnych ułatwiają zwiększenie użycia procesora GPU bez znaczącego wpływu na wydajność procesora GPU.  

## <a name="using-gpu-with-vms"></a>Korzystanie z procesora GPU z maszynami wirtualnymi

Na urządzeniu Azure Stack EDGE Pro nie można udostępnić procesora GPU podczas wdrażania obciążeń maszyny wirtualnej. Procesor GPU można zamapować tylko na jedną maszynę wirtualną. Oznacza to, że można mieć tylko jedną maszynę wirtualną GPU na urządzeniu z jednym procesorem GPU i dwiema maszynami wirtualnymi na urządzeniu wyposażonym w dwa procesory GPU. Istnieją również inne czynniki, które należy wziąć pod uwagę podczas korzystania z maszyn wirtualnych GPU na urządzeniu, które ma Kubernetes skonfigurowany dla obciążeń kontenerowych. Aby uzyskać więcej informacji, zobacz [maszyny wirtualne GPU i Kubernetes](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#gpu-vms-and-kubernetes).


## <a name="using-gpu-with-containers"></a>Korzystanie z procesora GPU z kontenerami

W przypadku wdrażania obciążeń kontenerowych procesor GPU może być współużytkowany na więcej niż jeden sposób na warstwie sprzętu i oprogramowania. W przypadku procesora GPU Tesla T4 na urządzeniu z systemem Azure Stack Edge jest ograniczone udostępnianie oprogramowania. Na urządzeniu są używane następujące dwa podejścia do udostępniania oprogramowania procesora GPU: 

- Pierwsze podejście obejmuje użycie zmiennych środowiskowych w celu określenia liczby procesorów GPU, które mogą być współużytkowane. Podczas korzystania z tego podejścia należy wziąć pod uwagę następujące zastrzeżenia:

    - W tej metodzie można określić jeden lub oba lub bez procesorów GPU. Nie można określić użycia ułamkowego.
    - Wiele modułów może mapować do jednego procesora GPU, ale tego samego modułu nie można zamapować na więcej niż jeden procesor GPU.
    - Przy użyciu danych wyjściowych NVIDIA SMI można zobaczyć ogólne użycie procesora GPU, w tym użycie pamięci.
    
    Aby uzyskać więcej informacji, zobacz jak [wdrożyć moduł IoT Edge, który używa procesora GPU](azure-stack-edge-gpu-configure-gpu-modules.md) na urządzeniu.

- Drugie podejście wymaga włączenia usługi wieloprocesowej w procesorach GPU firmy NVIDIA. MP to usługa środowiska uruchomieniowego, która umożliwia wiele procesów korzystających z CUDA jednocześnie na jednym udostępnionym procesorze GPU. Pakiety MP umożliwiają nakładanie się operacji jądra i memcopy z różnych procesów na procesorze GPU w celu osiągnięcia maksymalnego wykorzystania. Aby uzyskać więcej informacji, zobacz [Usługa wielu procesów](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf).

    Podczas korzystania z tego podejścia należy wziąć pod uwagę następujące zastrzeżenia:
    
    - Pakiety MP umożliwiają określanie większej liczby flag w ramach wdrażania procesora GPU.
    - Można określić użycie ułamkowe za pośrednictwem pakietów MP, ograniczając jednocześnie użycie każdej aplikacji wdrożonej na urządzeniu. Można określić wartość procentową użycia procesora GPU dla każdej aplikacji w `env` sekcji, `deployment.yaml` dodając następujący parametr: 

    ```yml
    // Example: application wants to limit gpu percentage to 20%
    
        env:
              - name: CUDA_MPS_ACTIVE_THREAD_PERCENTAGE 
                value: "20"    
    ```

## <a name="gpu-utilization"></a>Użycie procesora GPU
 
W przypadku udostępniania procesora GPU w ramach obciążeń zwirtualizowanych wdrożonych na urządzeniu można użyć interfejsu zarządzania systemem firmy NVIDIA (NVIDIA-SMI). NVIDIA-SMI to narzędzie wiersza polecenia, które ułatwia zarządzanie i monitorowanie urządzeń z procesorem GPU firmy NVIDIA. Aby uzyskać więcej informacji, zobacz [NVIDIA System Management Interface](https://developer.nvidia.com/nvidia-system-management-interface).

Aby wyświetlić użycie procesora GPU, najpierw Połącz się z interfejsem programu PowerShell urządzenia. Uruchom `Get-HcsNvidiaSmi` polecenie i Wyświetl dane wyjściowe NVIDIA SMI. Możesz również zobaczyć, jak zmiany wykorzystania procesora GPU przez włączenie planu MPS, a następnie wdrożenie wielu obciążeń na urządzeniu. Aby uzyskać więcej informacji, zobacz [Włączanie usługi wielu procesów](azure-stack-edge-gpu-connect-powershell-interface.md#enable-multi-process-service-mps).


## <a name="next-steps"></a>Następne kroki

- [Udostępnianie procesora GPU na potrzeby wdrożeń Kubernetes na serwerze Azure Stack z systemem EDGE Pro](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
- [Udostępnianie procesora GPU na potrzeby wdrożeń IoT na Azure Stack brzegowej](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md).
