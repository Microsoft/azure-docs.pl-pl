---
title: Wdrażanie obciążenia Kubernetes przy użyciu udostępniania procesora GPU na urządzeniu Azure Stack brzegowym procesorze GPU Pro
description: Opisuje, w jaki sposób można wdrożyć współużytkowane obciążenie procesora GPU za pośrednictwem Kubernetes na urządzeniu GPU z Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 04299ba4028de313f640074ca98c0b611f734981
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103565142"
---
# <a name="deploy-a-kubernetes-workload-using-gpu-sharing-on-your-azure-stack-edge-pro"></a>Wdrażanie obciążenia Kubernetes przy użyciu funkcji udostępniania procesora GPU na Azure Stack brzegowej Pro

W tym artykule opisano, jak obciążenia kontenerów mogą współużytkować procesory GPU na urządzeniu z systemem Azure Stack Edge na procesor GPU. W tym artykule zostaną uruchomione dwa zadania: jeden bez udostępniania kontekstu procesora GPU, a drugi z włączonym współdzieleniem kontekstu za pośrednictwem usługi wieloprocesowej (MPS) na urządzeniu. Aby uzyskać więcej informacji, zobacz [Usługa wielu procesów](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Masz dostęp do urządzenia z systemem Azure Stack Edge, które jest [aktywowane](azure-stack-edge-gpu-deploy-activate.md) i ma [skonfigurowane obliczenia ](azure-stack-edge-gpu-deploy-configure-compute.md). Masz [punkt końcowy interfejsu API Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints) i dodano ten punkt końcowy do `hosts` pliku na kliencie, który będzie miał dostęp do urządzenia.

1. Masz dostęp do systemu klienckiego z [obsługiwanym systemem operacyjnym](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). W przypadku korzystania z klienta systemu Windows system powinien uruchomić program PowerShell w wersji 5,0 lub nowszej, aby uzyskać dostęp do urządzenia.

1. Utworzono przestrzeń nazw i użytkownika. Przyznano Ci również użytkownikowi dostęp do tej przestrzeni nazw. Plik kubeconfig tej przestrzeni nazw jest zainstalowany w systemie klienckim, który będzie używany do uzyskiwania dostępu do urządzenia. Aby uzyskać szczegółowe instrukcje, zobacz [nawiązywanie połączenia z klastrem Kubernetes i zarządzanie nim za pośrednictwem usługi polecenia kubectl na urządzeniu z systemem Azure Stack brzeg Pro GPU](azure-stack-edge-gpu-create-kubernetes-cluster.md#configure-cluster-access-via-kubernetes-rbac). 

1. Zapisz następujące wdrożenie `yaml` w systemie lokalnym. Ten plik będzie używany do uruchamiania wdrażania Kubernetes. To wdrożenie jest oparte na [prostych kontenerach cuda](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#running-simple-containers) , które są publicznie dostępne z firmy NVIDIA. 

    ```yml
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: cuda-sample1
    spec:
      template:
        spec:
          hostPID: true
          hostIPC: true
          containers:
            - name: cuda-sample-container1
              image: nvidia/samples:nbody
              command: ["/tmp/nbody"]
              args: ["-benchmark", "-i=1000"]
              env:
              - name: NVIDIA_VISIBLE_DEVICES
                value: "0"
          restartPolicy: "Never"
      backoffLimit: 1
    ---
    
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: cuda-sample2
    spec:
      template:
        metadata:
        spec:
          hostPID: true
          hostIPC: true
          containers:
            - name: cuda-sample-container2
              image: nvidia/samples:nbody
              command: ["/tmp/nbody"]
              args: ["-benchmark", "-i=1000"]
              env:
              - name: NVIDIA_VISIBLE_DEVICES
                value: "0"
          restartPolicy: "Never"
      backoffLimit: 1
    ```

## <a name="verify-gpu-driver-cuda-version"></a>Weryfikowanie sterownika procesora GPU, wersji CUDA

Pierwszym krokiem jest sprawdzenie, czy na urządzeniu jest uruchomiony wymagany sterownik procesora GPU i wersje CUDA.

1. [Nawiąż połączenie z interfejsem programu PowerShell urządzenia](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Uruchom następujące polecenie:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```

1. W danych wyjściowych NVIDIA SMI Zanotuj wersję procesora GPU i wersję CUDA na urządzeniu. W przypadku uruchamiania oprogramowania Azure Stack Edge 2102, ta wersja będzie odpowiadać następującym wersjom sterowników:

    - Wersja sterownika procesora GPU: 460.32.03
    - Wersja CUDA: 11,2
    
    Oto przykładowe dane wyjściowe:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Wed Mar  3 12:24:27 2021
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

1. Nie otwieraj tej sesji, ponieważ będzie ona używana do wyświetlania danych wyjściowych usługi NVIDIA SMI w całym artykule.



## <a name="job-without-context-sharing"></a>Zadanie bez udostępniania kontekstu

Pierwsze zadanie zostanie uruchomione w celu wdrożenia aplikacji na urządzeniu w przestrzeni nazw `mynamesp1` . To wdrożenie aplikacji również pokazuje, że udostępnianie kontekstu procesora GPU nie jest domyślnie włączone. 

1. Wyświetl listę wszystkich uruchomionych w przestrzeni nazw. Uruchom następujące polecenie: 

    ```powershell
    kubectl get pods -n <Name of the namespace>
    ```

    Oto przykładowe dane wyjściowe:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    No resources found.
    ```
1. Rozpocznij zadanie wdrażania na urządzeniu przy użyciu dostarczonego wcześniej elementu Deployment. YAML. Uruchom następujące polecenie: 

    ```powershell
    kubectl apply -f <Path to the deployment .yaml> -n <Name of the namespace> 
    ```  

    To zadanie tworzy dwa kontenery i uruchamia symulację n-treści w obu kontenerach. Liczba iteracji symulacji jest określona w `.yaml` . Aby uzyskać więcej informacji, zobacz [symulacja N-Body](https://physics.princeton.edu//~fpretori/Nbody/intro.htm).
    
    Oto przykładowe dane wyjściowe:

    ```powershell
    PS C:\WINDOWS\system32> kubectl apply -f -n mynamesp1 C:\gpu-sharing\k8-gpusharing.yaml
    job.batch/cuda-sample1 created
    job.batch/cuda-sample2 created
    PS C:\WINDOWS\system32>
    ```

1. Aby wyświetlić listę zasobników uruchomionych we wdrożeniu, uruchom następujące polecenie:

    ```powershell
    kubectl get pods -n <Name of the namespace>
    ```   

    Oto przykładowe dane wyjściowe:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS    RESTARTS   AGE
    cuda-sample1-27srm   1/1     Running   0          28s
    cuda-sample2-db9vx   1/1     Running   0          27s
    PS C:\WINDOWS\system32>
    ```

    Na urządzeniu są uruchomione dwa zasobniki `cuda-sample1-cf979886d-xcwsq` `cuda-sample2-68b4899948-vcv68` .

1. Pobierz szczegóły dotyczące zasobników. Uruchom następujące polecenie:

    ```powershell
    kubectl -n <Name of the namespace> describe <Name of the job> 
    ```  

    Oto przykładowe dane wyjściowe:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 describe job.batch/cuda-sample1;  kubectl -n mynamesp1 describe job.batch/cuda-sample2
    Name:           cuda-sample1
    Namespace:      mynamesp1
    Selector:       controller-uid=22783f76-6af1-490d-b6eb-67dd4cda0e1f
    Labels:         controller-uid=22783f76-6af1-490d-b6eb-67dd4cda0e1f
                    job-name=cuda-sample1
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample1","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 12:25:34 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=22783f76-6af1-490d-b6eb-67dd4cda0e1f
               job-name=cuda-sample1
      Containers:
       cuda-sample-container1:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  60s   job-controller  Created pod: cuda-sample1-27srm
    Name:           cuda-sample2
    Namespace:      mynamesp1
    Selector:       controller-uid=e68c8d5a-718e-4880-b53f-26458dc24381
    Labels:         controller-uid=e68c8d5a-718e-4880-b53f-26458dc24381
                    job-name=cuda-sample2
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample2","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 12:25:35 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=e68c8d5a-718e-4880-b53f-26458dc24381
               job-name=cuda-sample2
      Containers:
       cuda-sample-container2:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  60s   job-controller  Created pod: cuda-sample2-db9vx
    PS C:\WINDOWS\system32>
    ```
    Dane wyjściowe wskazują, że oba te elementy zostały pomyślnie utworzone przez zadanie. 

1. Chociaż w obu kontenerach działa symulacja n-treść, Wyświetl użycie procesora GPU z danych wyjściowych NVIDIA SMI. Przejdź do interfejsu programu PowerShell urządzenia i uruchom program `Get-HcsGpuNvidiaSmi` .

    Oto przykładowe dane wyjściowe, gdy oba kontenery działają w symulacji n treści:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Wed Mar  3 12:26:41 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   64C    P0    69W /  70W |    221MiB / 15109MiB |    100%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    197976      C   /tmp/nbody                        109MiB |
    |    0   N/A  N/A    198051      C   /tmp/nbody                        109MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>    
    ```
    Jak widać, istnieją dwa kontenery (typ = C) działające z symulacją mikrotreści w procesorze GPU 0. 

1. Monitoruj symulację n-treśćowej. Uruchom `get pod` polecenia. Oto przykładowe dane wyjściowe, gdy Symulacja jest uruchomiona. 

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS    RESTARTS   AGE
    cuda-sample1-27srm   1/1     Running   0          70s
    cuda-sample2-db9vx   1/1     Running   0          69s
    PS C:\WINDOWS\system32>
    ```

    Po zakończeniu symulacji dane wyjściowe będą wskazywały, że. Oto przykładowe dane wyjściowe:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS      RESTARTS   AGE
    cuda-sample1-27srm   0/1     Completed   0          2m54s
    cuda-sample2-db9vx   0/1     Completed   0          2m53s
    PS C:\WINDOWS\system32>
    ```
 
1. Po zakończeniu symulacji można wyświetlić dzienniki i łączny czas ukończenia symulacji. Uruchom następujące polecenie:

    ```powershell
    kubectl logs -n <Name of the namespace> <pod name>
    ``` 

    Oto przykładowe dane wyjściowe: 

    ```powershell
    PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample1-27srm
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ===========// CUT //===================// CUT //=====================  
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170398.766 ms
    = 98.459 billion interactions per second
    = 1969.171 single-precision GFLOP/s at 20 flops per interaction
    PS C:\WINDOWS\system32>
    ```

    ```powershell
    PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample2-db9vx
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ===========// CUT //===================// CUT //=====================
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170368.859 ms
    = 98.476 billion interactions per second
    = 1969.517 single-precision GFLOP/s at 20 flops per interaction
    PS C:\WINDOWS\system32>    
    ```
1. W tej chwili nie powinny istnieć żadne procesy uruchomione w procesorze GPU. Można to sprawdzić, wyświetlając użycie procesora GPU przy użyciu danych wyjściowych NVIDIA SMI.

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Wed Mar  3 12:32:52 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   38C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
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

## <a name="job-with-context-sharing"></a>Zadanie z udostępnianiem kontekstu

Należy uruchomić drugie zadanie, aby wdrożyć symulację n-treści w dwóch kontenerach CUDA, gdy jest włączone udostępnianie kontekstu GPU. Najpierw należy włączyć funkcję MP na urządzeniu.

1. [Nawiąż połączenie z interfejsem programu PowerShell urządzenia](azure-stack-edge-gpu-connect-powershell-interface.md).

1. Aby włączyć pakiety MP na urządzeniu, uruchom `Start-HcsGpuMPS` polecenie.

    ```powershell
    [10.100.10.10]: PS>Start-HcsGpuMPS
    K8S-1HXQG13CL-1HXQG13:
    
    Set compute mode to EXCLUSIVE_PROCESS for GPU 00002C74:00:00.0.
    All done.
    Created nvidia-mps.service
    [10.100.10.10]: PS>    
    ```
1. Uruchom zadanie, używając `yaml` wcześniej użytego wdrożenia. Może być konieczne usunięcie istniejącego wdrożenia. Zobacz [usuwanie wdrożenia](#delete-deployment).

    Oto przykładowe dane wyjściowe:

    ```yml
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 delete -f C:\gpu-sharing\k8-gpusharing.yaml
    job.batch "cuda-sample1" deleted
    job.batch "cuda-sample2" deleted
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    No resources found.
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 apply -f C:\gpu-sharing\k8-gpusharing.yaml
    job.batch/cuda-sample1 created
    job.batch/cuda-sample2 created
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS    RESTARTS   AGE
    cuda-sample1-vcznt   1/1     Running   0          21s
    cuda-sample2-zkx4w   1/1     Running   0          21s
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 describe job.batch/cuda-sample1;  kubectl -n mynamesp1 describe job.batch/cuda-sample2
    Name:           cuda-sample1
    Namespace:      mynamesp1
    Selector:       controller-uid=ed06bdf0-a282-4b35-a2a0-c0d36303a35e
    Labels:         controller-uid=ed06bdf0-a282-4b35-a2a0-c0d36303a35e
                    job-name=cuda-sample1
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample1","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 21:51:51 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=ed06bdf0-a282-4b35-a2a0-c0d36303a35e
               job-name=cuda-sample1
      Containers:
       cuda-sample-container1:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  46s   job-controller  Created pod: cuda-sample1-vcznt
    Name:           cuda-sample2
    Namespace:      mynamesp1
    Selector:       controller-uid=6282b8fa-e76d-4f45-aa85-653ee0212b29
    Labels:         controller-uid=6282b8fa-e76d-4f45-aa85-653ee0212b29
                    job-name=cuda-sample2
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample2","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 21:51:51 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=6282b8fa-e76d-4f45-aa85-653ee0212b29
               job-name=cuda-sample2
      Containers:
       cuda-sample-container2:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  47s   job-controller  Created pod: cuda-sample2-zkx4w
    PS C:\WINDOWS\system32>
    ```

1. Gdy Symulacja jest uruchomiona, można wyświetlić dane wyjściowe NVIDIA SMI. Dane wyjściowe przedstawiają procesy odpowiadające kontenerom cuda (typ M + C) z symulacją typu "n-Body" i usługą MP (typu C) jako uruchomioną. Wszystkie te procesy współużytkują procesor GPU 0.

    ```powershell
    PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Mon Mar  3 21:54:50 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000E00B:00:00.0 Off |                    0 |
    | N/A   45C    P0    68W /  70W |    242MiB / 15109MiB |    100%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    144377    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    144379    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    144443      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    ```

1. Po zakończeniu symulacji można wyświetlić dzienniki i łączny czas ukończenia symulacji. Uruchom następujące polecenie:

    ```powershell
        PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
        NAME                 READY   STATUS      RESTARTS   AGE
        cuda-sample1-vcznt   0/1     Completed   0          5m44s
        cuda-sample2-zkx4w   0/1     Completed   0          5m44s
        PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample1-vcznt
        Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
        ===========// CUT //===================// CUT //=====================    
        > Windowed mode
        > Simulation data stored in video memory
        > Single precision floating point simulation
        > 1 Devices used for simulation
        GPU Device 0: "Turing" with compute capability 7.5
        
        > Compute 7.5 CUDA device: [Tesla T4]
        40960 bodies, total time for 10000 iterations: 154979.453 ms
        = 108.254 billion interactions per second
        = 2165.089 single-precision GFLOP/s at 20 flops per interaction


        PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample2-zkx4w
        Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
        ===========// CUT //===================// CUT //=====================
        > Windowed mode
        > Simulation data stored in video memory
        > Single precision floating point simulation
        > 1 Devices used for simulation
        GPU Device 0: "Turing" with compute capability 7.5
        
        > Compute 7.5 CUDA device: [Tesla T4]
        40960 bodies, total time for 10000 iterations: 154986.734 ms
        = 108.249 billion interactions per second
        = 2164.987 single-precision GFLOP/s at 20 flops per interaction
        PS C:\WINDOWS\system32>
    ```
1. Po zakończeniu symulacji możesz ponownie wyświetlić dane wyjściowe NVIDIA SMI. Tylko proces NVIDIA-CUDA-MP-Server dla usługi MPS jest wyświetlany jako uruchomiony. 

    ```powershell
    PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Mon Mar  3 21:59:55 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000E00B:00:00.0 Off |                    0 |
    | N/A   37C    P8     9W /  70W |     28MiB / 15109MiB |      0%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    144443      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    ```

## <a name="delete-deployment"></a>Usuń wdrożenie

Może być konieczne usunięcie wdrożeń podczas działania z włączonym systemem MP i wyłączenie pakietu MP na urządzeniu.

Aby usunąć wdrożenie na urządzeniu, uruchom następujące polecenie: 

```powershell
kubectl delete -f <Path to the deployment .yaml> -n <Name of the namespace> 
```

Oto przykładowe dane wyjściowe:

```powershell
PS C:\WINDOWS\system32> kubectl delete -f 'C:\gpu-sharing\k8-gpusharing.yaml' -n mynamesp1
deployment.apps "cuda-sample1" deleted
deployment.apps "cuda-sample2" deleted
PS C:\WINDOWS\system32>
```
    
## <a name="next-steps"></a>Następne kroki

- [Wdróż obciążanie IoT Edge za pomocą udostępniania procesora GPU na Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md).
