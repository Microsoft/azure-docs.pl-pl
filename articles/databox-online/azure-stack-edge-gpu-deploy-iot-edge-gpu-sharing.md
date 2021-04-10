---
title: Wdrażanie obciążenia IoT Edge przy użyciu funkcji udostępniania procesora GPU na urządzeniu z systemem Azure Stack brzeg Pro GPU
description: Opisuje, jak można wdrożyć współużytkowane obciążenie procesora GPU za pośrednictwem IoT Edge na urządzeniu z systemem Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: b52d1e834772a2a6e0e000b3df15d8aa0fa866a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103565150"
---
# <a name="deploy-an-iot-edge-workload-using-gpu-sharing-on-your-azure-stack-edge-pro"></a>Wdrażanie obciążenia IoT Edge przy użyciu funkcji udostępniania procesora GPU na Azure Stack brzegowej Pro

W tym artykule opisano, jak obciążenia kontenerów mogą współużytkować procesory GPU na urządzeniu z systemem Azure Stack Edge na procesor GPU. Podejście obejmuje włączenie usługi wieloprocesowej (MPS), a następnie określenie obciążeń procesora GPU przy użyciu wdrożenia IoT Edge. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Masz dostęp do urządzenia z systemem Azure Stack Edge, które jest [aktywowane](azure-stack-edge-gpu-deploy-activate.md) i ma [skonfigurowane obliczenia](azure-stack-edge-gpu-deploy-configure-compute.md). Masz [punkt końcowy interfejsu API Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints) i dodano ten punkt końcowy do `hosts` pliku na kliencie, który będzie miał dostęp do urządzenia.

1. Masz dostęp do systemu klienckiego z [obsługiwanym systemem operacyjnym](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). W przypadku korzystania z klienta systemu Windows system powinien uruchomić program PowerShell w wersji 5,0 lub nowszej, aby uzyskać dostęp do urządzenia.

1. Zapisz następujące wdrożenie `json` w systemie lokalnym. Informacje z tego pliku zostaną użyte do uruchomienia wdrożenia IoT Edge. To wdrożenie jest oparte na [prostych kontenerach cuda](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#running-simple-containers) , które są publicznie dostępne z firmy NVIDIA. 

    ```json
    {
        "modulesContent": {
            "$edgeAgent": {
                "properties.desired": {
                    "modules": {
                        "cuda-sample1": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        },
                        "cuda-sample2": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        }
                    },
                    "runtime": {
                        "settings": {
                            "minDockerVersion": "v1.25"
                        },
                        "type": "docker"
                    },
                    "schemaVersion": "1.1",
                    "systemModules": {
                        "edgeAgent": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                                "createOptions": ""
                            },
                            "type": "docker"
                        },
                        "edgeHub": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                            },
                            "type": "docker",
                            "status": "running",
                            "restartPolicy": "always"
                        }
                    }
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    "routes": {
                        "route": "FROM /messages/* INTO $upstream"
                    },
                    "schemaVersion": "1.1",
                    "storeAndForwardConfiguration": {
                        "timeToLiveSecs": 7200
                    }
                }
            },
            "cuda-sample1": {
                "properties.desired": {}
            },
            "cuda-sample2": {
                "properties.desired": {}
            }
        }
    }
    ```

## <a name="verify-gpu-driver-cuda-version"></a>Weryfikowanie sterownika procesora GPU, wersji CUDA

Pierwszym krokiem jest sprawdzenie, czy na urządzeniu jest uruchomiony wymagany sterownik procesora GPU i wersje CUDA.

1. [Nawiąż połączenie z interfejsem programu PowerShell urządzenia](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Uruchom następujące polecenie:

    `Get-HcsGpuNvidiaSmi`

1. W danych wyjściowych NVIDIA SMI Zanotuj wersję procesora GPU i wersję CUDA na urządzeniu. W przypadku uruchamiania oprogramowania Azure Stack Edge 2102, ta wersja będzie odpowiadać następującym wersjom sterowników:

    - Wersja sterownika procesora GPU: 460.32.03
    - Wersja CUDA: 11,2
    
    Oto przykładowe dane wyjściowe:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Tue Feb 23 10:34:01 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000041F:00:00.0 Off |                    0 |
    | N/A   40C    P8    15W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>  
    ```

1. Nie otwieraj tej sesji, ponieważ będzie ona używana do wyświetlania danych wyjściowych usługi NVIDIA SMI w całym artykule.


## <a name="deploy-without-context-sharing"></a>Wdróż bez udostępniania kontekstu

Teraz można wdrożyć aplikację na urządzeniu, gdy usługa wieloprocesowa nie jest uruchomiona i nie ma udostępniania kontekstu. Wdrożenie jest realizowane za pośrednictwem Azure Portal w `iotedge` przestrzeni nazw, która istnieje na urządzeniu.

### <a name="create-user-in-iot-edge-namespace"></a>Utwórz użytkownika w IoT Edge przestrzeni nazw

Najpierw utworzysz użytkownika, który będzie łączył się z `iotedge` przestrzenią nazw. Moduły IoT Edge są wdrażane w przestrzeni nazw iotedge. Aby uzyskać więcej informacji, zobacz [Kubernetes przestrzenie nazw na urządzeniu](azure-stack-edge-gpu-kubernetes-rbac.md#namespaces-types).

Wykonaj następujące kroki, aby utworzyć użytkownika i udzielić użytkownikowi dostępu do `iotedge` przestrzeni nazw. 

1. [Nawiąż połączenie z interfejsem programu PowerShell urządzenia](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Utwórz nowego użytkownika w `iotedge` przestrzeni nazw. Uruchom następujące polecenie:

    `New-HcsKubernetesUser -UserName <user name>`

    Oto przykładowe dane wyjściowe:

    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName iotedgeuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: 
    ===========================//snipped //======================// snipped //=============================
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: iotedgeuser
      name: iotedgeuser@kubernetes
    current-context: iotedgeuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: iotedgeuser
      user:
        client-certificate-data: 
    ===========================//snipped //======================// snipped //=============================
        client-key-data: 
    ===========================//snipped //======================// snipped ============================
    PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
    ```

1. Skopiuj dane wyjściowe wyświetlane w postaci zwykłego tekstu. Zapisz dane wyjściowe jako plik *konfiguracji* (bez rozszerzenia) w `.kube` folderze profilu użytkownika na komputerze lokalnym, na przykład `C:\Users\<username>\.kube` . 

1. Przyznaj użytkownikowi, który został utworzony, uzyskuj dostęp do `iotedge` przestrzeni nazw. Uruchom następujące polecenie:

    `Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName <user name>`    

    Oto przykładowe dane wyjściowe:

    ```python
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName iotedgeuser
    [10.100.10.10]: PS>    
    ```
Aby uzyskać szczegółowe instrukcje, zobacz [nawiązywanie połączenia z klastrem Kubernetes i zarządzanie nim za pośrednictwem usługi polecenia kubectl na urządzeniu z systemem Azure Stack brzeg Pro GPU](azure-stack-edge-gpu-create-kubernetes-cluster.md#configure-cluster-access-via-kubernetes-rbac).

### <a name="deploy-modules-via-portal"></a>Wdrażanie modułów za pośrednictwem portalu

Wdróż moduły IoT Edge za pośrednictwem Azure Portal. Wdrażamy publicznie dostępne moduły przykładowe NVIDIA CUDA, które uruchamiają symulację n-Body. Aby uzyskać więcej informacji, zobacz [symulacja N-Body](https://physics.princeton.edu//~fpretori/Nbody/intro.htm).

1. Upewnij się, że na urządzeniu jest uruchomiona usługa IoT Edge.

    ![Usługa IoT Edge uruchomiona.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-1.png)

1. Wybierz kafelek IoT Edge w okienku po prawej stronie. Przejdź do **IoT Edge właściwości >**. W okienku po prawej stronie wybierz zasób IoT Hub skojarzony z Twoim urządzeniem.

    ![Wyświetl właściwości.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-2.png)

1. W zasobie IoT Hub przejdź do pozycji **Automatyczne zarządzanie urządzeniami > IoT Edge**. W okienku po prawej stronie Wybierz urządzenie IoT Edge skojarzone z Twoim urządzeniem.

    ![Przejdź do IoT Edge.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-3.png)

1. Wybierz pozycję **Ustaw moduły**.

    ![Przejdź do pozycji Ustawianie modułów.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-4.png)

1. Wybierz pozycję **+ Dodaj moduł > + IoT Edge**.

    ![Dodaj moduł IoT Edge.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-5.png)

1. Na karcie **Ustawienia modułu** Podaj **nazwę modułu IoT Edge** i **Identyfikator URI obrazu**. Ustaw **zasady ściągania obrazów** na **przy tworzeniu**.

    ![Ustawienia modułu.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-6.png)
1. Na karcie **zmienne środowiskowe** Określ **NVIDIA_VISIBLE_DEVICES** jako **0**.

    ![Zmienne środowiskowe.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-7.png)

1. Na karcie **Opcje tworzenia kontenera** podaj następujące opcje:

    ```json
    {
        "Entrypoint": [
            "/bin/sh"
        ],
        "Cmd": [
            "-c",
            "/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done"
        ],
        "HostConfig": {
            "IpcMode": "host",
            "PidMode": "host"
        }
    }    
    ```
    Opcje są wyświetlane w następujący sposób:

    ![Opcje tworzenia kontenera.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-8.png)

    Wybierz pozycję **Dodaj**.

1. Dodany moduł powinien być wyświetlany jako **uruchomiony**. 

    ![Przejrzyj i Utwórz wdrożenie.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-9.png)


1. Powtórz wszystkie kroki, aby dodać moduł, który został użyty podczas dodawania pierwszego modułu. W tym przykładzie Podaj nazwę modułu jako `cuda-sample2` . 

    ![Ustawienia modułu dla drugiego modułu.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-12.png)

    Użyj tej samej zmiennej środowiskowej, co oba moduły będą współużytkować ten sam procesor GPU.

    ![Zmienna środowiskowa dla drugiego modułu.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-13.png)

    Użyj tych samych opcji tworzenia kontenera, które zostały podane dla pierwszego modułu, a następnie wybierz pozycję **Dodaj**.

    ![Opcje tworzenia kontenera dla drugiego modułu.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-14.png)

1. Na stronie **Ustawianie modułów** wybierz pozycję **Przegląd + Utwórz** , a następnie wybierz pozycję **Utwórz**. 

    ![Przejrzyj i Utwórz drugie wdrożenie.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-15.png)

1. **Stan środowiska uruchomieniowego** obu modułów powinien teraz być widoczny jako **uruchomiony**.  

    ![stan drugiego wdrożenia.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-16.png)


### <a name="monitor-workload-deployment"></a>Monitorowanie wdrożenia obciążenia

1. Otwórz nową sesję programu PowerShell.

1. Wyświetl listę zasobników uruchomionych w `iotedge` przestrzeni nazw. Uruchom następujące polecenie:

    `kubectl get pods -n iotedge`   

    Oto przykładowe dane wyjściowe:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-ssng8   2/2     Running   0          5s
    cuda-sample2-6db6d98689-d74kb   2/2     Running   0          4s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          6d21h
    edgehub-d6c764847-l8v4m         2/2     Running   0          24h
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          6d21h
    PS C:\WINDOWS\system32>   
    ```
    Na urządzeniu są uruchomione dwa zasobniki `cuda-sample1-97c494d7f-lnmns` `cuda-sample2-d9f6c4688-2rld9` .

1. Chociaż w obu kontenerach działa symulacja n-treść, Wyświetl użycie procesora GPU z danych wyjściowych NVIDIA SMI. Przejdź do interfejsu programu PowerShell urządzenia i uruchom program `Get-HcsGpuNvidiaSmi` .

    Oto przykładowe dane wyjściowe, gdy oba kontenery działają w symulacji n treści:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:31:16 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   52C    P0    69W /  70W |    221MiB / 15109MiB |    100%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    188342      C   /tmp/nbody                        109MiB |
    |    0   N/A  N/A    188413      C   /tmp/nbody                        109MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
    Jak widać, istnieją dwa kontenery z funkcją symulacji n treścią procesora GPU 0. Możesz również wyświetlić odpowiednie użycie pamięci.

1. Po zakończeniu symulacji, dane wyjściowe NVIDIA SMI pokazują, że na urządzeniu nie ma uruchomionych procesów.

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:54:48 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   34C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
 
1. Po zakończeniu symulacji n-treści Przejrzyj dzienniki, aby poznać szczegóły wdrożenia i czas trwania symulacji. 

    Oto przykładowe dane wyjściowe z pierwszego kontenera:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample1-869989578c-ssng8 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170171.531 ms
    = 98.590 billion interactions per second
    = 1971.801 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```
    Oto przykładowe dane wyjściowe z drugiego kontenera:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-d74kb cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170054.969 ms
    = 98.658 billion interactions per second
    = 1973.152 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```

1. Zatrzymaj wdrożenie modułu. W zasobie IoT Hub dla urządzenia:
    1. Przejdź do pozycji **Automatyczne wdrażanie urządzenia > IoT Edge**. Wybierz urządzenie IoT Edge odpowiednie dla Twojego urządzenia.

    1. Przejdź do pozycji **Ustawianie modułów** i wybierz moduł. 

        ![Wybierz pozycję Ustaw moduł.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-1.png)

    1. Na karcie **moduły** wybierz moduł.
    
        ![Wybierz moduł.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-2.png)

    1.  Na karcie **Ustawienia modułu** Ustaw **żądany stan** na zatrzymany. Wybierz pozycję **Aktualizuj**.

        ![Zmodyfikuj ustawienia modułu.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-3.png)

    1. Powtórz kroki, aby zatrzymać drugi moduł wdrożony na urządzeniu. Wybierz pozycję **Przeglądanie i tworzenie**, a następnie wybierz pozycję **Utwórz**. Powinno to spowodować aktualizację wdrożenia.

        ![Przejrzyj i Utwórz zaktualizowane wdrożenie.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-6.png)
 
    1. Odświeżanie strony **ustawiania modułów** wiele razy. dopóki **stan środowiska uruchomieniowego** modułu zostanie wyświetlony jako **zatrzymany**.

        ![Sprawdź stan wdrożenia.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-8.png) 
    

## <a name="deploy-with-context-sharing"></a>Wdrażanie przy użyciu udostępniania kontekstu

Teraz można wdrożyć symulację n-ciała w dwóch kontenerach CUDA, gdy na urządzeniu działa pakiet MP. Najpierw należy włączyć funkcję MP na urządzeniu.


1. [Nawiąż połączenie z interfejsem programu PowerShell urządzenia](azure-stack-edge-gpu-connect-powershell-interface.md).

1. Aby włączyć pakiety MP na urządzeniu, uruchom `Start-HcsGpuMPS` polecenie.

    ```powershell
    [10.100.10.10]: PS>Start-HcsGpuMPS
    K8S-1HXQG13CL-1HXQG13:
    Set compute mode to EXCLUSIVE_PROCESS for GPU 0000191E:00:00.0.
    All done.
    Created nvidia-mps.service
    [10.100.10.10]: PS>    
    ```
1. Pobieranie danych wyjściowych usługi NVIDIA SMI z interfejsu programu PowerShell urządzenia. Na urządzeniu może być widoczny `nvidia-cuda-mps-server` proces lub usługa MP.

    Oto przykładowe dane wyjściowe:

    ```yml
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:37:39 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   36C    P8     9W /  70W |     28MiB / 15109MiB |      0%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```

1. Wdróż wcześniej zatrzymane moduły. Ustaw **żądany stan** do uruchomienia za pośrednictwem **zestawu modułów**.

    Oto przykładowe dane wyjściowe:

    ```yml
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-2zxh6   2/2     Running   0          44s
    cuda-sample2-6db6d98689-fn7mx   2/2     Running   0          44s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          5d20h
    edgehub-d6c764847-l8v4m         2/2     Running   0          27m
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          5d20h
    PS C:\WINDOWS\system32>
    ```
    Można sprawdzić, czy moduły są wdrożone i uruchomione na urządzeniu.

1. Po wdrożeniu modułów symulacja n-Body również zaczyna działać w obu kontenerach. Oto przykładowe dane wyjściowe po zakończeniu symulacji pierwszego kontenera:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge logs cuda-sample1-869989578c-2zxh6 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155256.062 ms
    = 108.062 billion interactions per second
    = 2161.232 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32> 
    ```
    Oto przykładowe dane wyjściowe, gdy symulacja została ukończona w drugim kontenerze:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-fn7mx cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155366.359 ms
    = 107.985 billion interactions per second
    = 2159.697 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>    
    ```      

1. Pobieranie danych wyjściowych usługi NVIDIA SMI z interfejsu programu PowerShell urządzenia, gdy w obu kontenerach jest uruchomiona symulacja n-treśćowa. Oto przykładowe dane wyjściowe. Istnieją trzy procesy, `nvidia-cuda-mps-server` proces (typ C) odnosi się do usługi MP i `/tmp/nbody` procesy (typ M + C) odpowiadają na obciążenia n-treści wdrożone przez moduły. 

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:59:44 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   54C    P0    69W /  70W |    242MiB / 15109MiB |    100%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A     56832    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A     56900    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```
    
## <a name="next-steps"></a>Następne kroki

- [Wdróż udostępnione obciążenie Kubernetes procesora GPU na Azure Stack brzegowej](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
