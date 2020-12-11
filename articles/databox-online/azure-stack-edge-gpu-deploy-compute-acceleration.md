---
title: Korzystanie z przyspieszenia obliczeń GPU lub VPU na Azure Stack urządzeniach brzegowych na potrzeby wdrożeń Kubernetes | Microsoft Docs
description: Zawiera opis sposobu używania przyspieszania obliczeniowego procesora GPU lub VPU w systemie Azure Stack brzegowej procesora GPU, Azure Stack brzeg Pro R lub Azure Stack Edge mini RI dla wdrożeń Kubernetes.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/05/2020
ms.author: alkohli
ms.openlocfilehash: cf70b24dae70ad2e64f3443e4c4d959d46fb4ea4
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095036"
---
# <a name="use-compute-acceleration-on-azure-stack-edge-pro-gpu-for-kubernetes-deployment"></a>Korzystanie z przyspieszenia obliczeń na Azure Stack Kubernetes Edge procesor GPU

W tym artykule opisano sposób używania przyspieszenia obliczeń na Azure Stack urządzeniach brzegowych podczas korzystania z wdrożeń Kubernetes. Artykuł ma zastosowanie do Azure Stack brzegowych procesorów GPU, Azure Stack EDGE Pro R i Azure Stack Edge.


## <a name="about-compute-acceleration"></a>Informacje o przyspieszaniu obliczeń 

Centralna jednostka przetwarzania (CPU) to domyślne obliczenia ogólnego przeznaczenia dla większości procesów uruchomionych na komputerze. Często wyspecjalizowany sprzęt komputerowy jest używany do bardziej wydajnego wykonywania niektórych funkcji niż w oprogramowaniu w procesorze CPU. Na przykład jednostka procesora graficznego (GPU) może służyć do przyspieszenia przetwarzania danych pikseli.  

Przyspieszenie obliczeniowe to termin używany w odniesieniu do Azure Stack urządzeń brzegowych, które są używane do przyspieszania sprzętowego (GPU), jednostki przetwarzania wzrokowego (VPU) lub pola programowalnej bramy modułu (FPGA). Większość obciążeń wdrożonych na urządzeniu Azure Stack Edge obejmuje czas krytyczny, wiele strumieni aparatu i/lub dużą liczbę klatek, z których wszystkie wymagają określonego przyspieszenia sprzętowego.

W tym artykule omówiono przyspieszenie obliczeniowe tylko przy użyciu procesora GPU lub VPU dla następujących urządzeń:

- **Azure Stack Edge — procesor GPU** — te urządzenia mogą mieć 1 lub 2 procesor NVIDIA T4 dwurdzeniowy. Aby uzyskać więcej informacji, zobacz [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).
- **Azure Stack EDGE Pro R** — te urządzenia mają 1 procesor NVIDIA T4 dwurdzeniowy. Aby uzyskać więcej informacji, zobacz [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).
- **Azure Stack Edge mini R** — te urządzenia mają 1 Intel Movidius WYPOSAŻONO X VPU. Aby uzyskać więcej informacji, zobacz [Intel Movidius wyposażono X VPU](https://www.movidius.com/MyriadX).


## <a name="use-gpu-for-kubernetes-deployment"></a>Użycie procesora GPU do wdrożenia Kubernetes

Poniższego przykładu `yaml` można użyć w przypadku urządzeń z procesorem GPU w systemie Azure Stack Edge lub Azure Stack EDGE Pro R.

<!--In a production scenario, Pods are not used directly and these are wrapped around higher level constructs like Deployment, ReplicaSet which maintain the desired state in case of pod restarts, failures.-->

```yml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
    - name: cuda-container
      image: nvidia/cuda:9.0-devel
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
    - name: digits-container
      image: nvidia/digits:6.0
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
```


## <a name="use-vpu-for-kubernetes-deployment"></a>Użyj VPU do wdrożenia Kubernetes

Poniższego przykładu `yaml` można użyć w przypadku urządzenia z systemem Azure Stack Edge mini R, które ma VPU.

```yml
apiVersion: batch/v1
kind: Job
metadata:
 name: intelvpu-demo-job
 labels:
   jobgroup: intelvpu-demo
spec:
 template:
   metadata:
     labels:
       jobgroup: intelvpu-demo
   spec:
     restartPolicy: Never
     containers:
       -
         name: intelvpu-demo-job-1
         image: ubuntu-demo-openvino:devel
         imagePullPolicy: IfNotPresent
         command: [ "/do_classification.sh" ]
         resources:
           limits:
             vpu.intel.com/hddl: 1
```


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [używać polecenia kubectl do uruchamiania Kubernetesej aplikacji stanowej przy użyciu PersistentVolume na urządzeniu GPU Azure Stack Edge](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).
