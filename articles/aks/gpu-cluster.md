---
title: Korzystanie z procesorów GPU w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak korzystać z procesorów GPU w przypadku obliczeń o wysokiej wydajności lub obciążeń intensywnie wykorzystujących grafikę w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 08/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: d7e312f049acc0b74aa0a253864bfce6100044bd
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929144"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Korzystanie z procesorów GPU na potrzeby obciążeń intensywnie korzystających z obliczeń w usłudze Azure Kubernetes Service (AKS)

Jednostki procesora graficznego (GPU) są często używane do obciążeń intensywnie korzystających z mocy obliczeniowej, takich jak obciążenia grafiki i wizualizacji. AKS obsługuje tworzenie pul węzłów z obsługą procesora GPU w celu uruchamiania tych obciążeń intensywnie korzystających z obliczeń w Kubernetes. Aby uzyskać więcej informacji na temat dostępnych maszyn wirtualnych z obsługą procesora GPU, zobacz [rozmiary maszyn wirtualnych zoptymalizowane według procesora GPU na platformie Azure][gpu-skus]. W przypadku węzłów AKS zaleca się minimalny rozmiar *Standard_NC6*.

> [!NOTE]
> Maszyny wirtualne z obsługą procesora GPU zawierają wyspecjalizowany sprzęt, który podlega wyższej cenie i dostępności regionów. Aby uzyskać więcej informacji, zobacz [Cennik][azure-pricing] narzędzia i [dostępność regionów][azure-availability].

Obecnie użycie pul węzłów z obsługą procesora GPU jest dostępne tylko dla pul węzłów systemu Linux.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster AKS z węzłami obsługującymi procesory GPU. Klaster AKS musi mieć uruchomioną Kubernetes 1,10 lub nowszą. Jeśli potrzebujesz klastra AKS, który spełnia te wymagania, zapoznaj się z pierwszą sekcją tego artykułu, aby [utworzyć klaster AKS](#create-an-aks-cluster).

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Jeśli potrzebujesz klastra AKS, który spełnia minimalne wymagania (węzeł z obsługą procesora GPU i Kubernetes w wersji 1,10 lub nowszej), wykonaj następujące czynności. Jeśli masz już klaster AKS, który spełnia te wymagania, [Przejdź do następnej sekcji](#confirm-that-gpus-are-schedulable).

Najpierw utwórz grupę zasobów dla klastra za pomocą polecenia [AZ Group Create][az-group-create] . Poniższy przykład umożliwia utworzenie grupy *zasobów nazwa zasobu* w regionie *wschodniego* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teraz Utwórz klaster AKS za pomocą polecenia [AZ AKS Create][az-aks-create] . Poniższy przykład tworzy klaster z jednym węzłem o rozmiarze `Standard_NC6` :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

Pobierz poświadczenia dla klastra AKS za pomocą polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-device-plugin"></a>Zainstaluj wtyczkę urządzenia NVIDIA

Aby można było korzystać z procesorów GPU w węzłach, należy wdrożyć elementu daemonset dla wtyczki urządzenia NVIDIA. Ten elementu daemonset uruchamia w każdym węźle, aby udostępnić wymagane sterowniki dla procesorów GPU.

Najpierw utwórz przestrzeń nazw za pomocą polecenia [polecenia kubectl Create Namespace][kubectl-create] , takiego jak *zasoby procesora GPU*:

```console
kubectl create namespace gpu-resources
```

Utwórz plik o nazwie *NVIDIA-Device-plugin-ds. YAML* i wklej następujący manifest YAML. Ten manifest jest dostarczany jako część [wtyczki urządzenia NVIDIA dla projektu Kubernetes][nvidia-github].

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      containers:
      - image: mcr.microsoft.com/oss/nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

Teraz użyj polecenia [polecenia kubectl Apply][kubectl-apply] , aby utworzyć elementu daemonset i upewnić się, że wtyczka urządzenia NVIDIA została utworzona pomyślnie, jak pokazano w poniższym przykładzie danych wyjściowych:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="use-the-aks-specialized-gpu-image-preview"></a>Korzystanie z AKS wyspecjalizowanego obrazu procesora GPU (wersja zapoznawcza)

Jako alternatywa dla tych kroków AKS udostępnia w pełni skonfigurowany obraz AKS, który zawiera już [wtyczkę urządzenia NVIDIA dla Kubernetes][nvidia-github].

> [!WARNING]
> Nie należy ręcznie instalować zestawu demona wtyczki urządzenia NVIDIA dla klastrów przy użyciu nowego obrazu AKS wyspecjalizowanego procesora GPU.


Zarejestruj `GPUDedicatedVHDPreview` funkcję:

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

Wyświetlenie stanu jako **zarejestrowanego** może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list](/cli/azure/feature#az-feature-list) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

Gdy stan jest wyświetlany jako zarejestrowane, Odśwież rejestrację `Microsoft.ContainerService` dostawcy zasobów przy użyciu polecenia [AZ Provider Register](/cli/azure/provider#az-provider-register) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Aby zainstalować rozszerzenie interfejsu wiersza polecenia AKS-Preview, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension add --name aks-preview
```

Aby zaktualizować rozszerzenie interfejsu wiersza polecenia AKS-Preview, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension update --name aks-preview
```

### <a name="use-the-aks-specialized-gpu-image-on-new-clusters-preview"></a>Korzystanie z AKS wyspecjalizowanego obrazu procesora GPU w nowych klastrach (wersja zapoznawcza)    

Skonfiguruj klaster tak, aby korzystał z AKS wyspecjalizowanego obrazu procesora GPU podczas tworzenia klastra. Użyj `--aks-custom-headers` flagi dla węzłów agenta procesora GPU w nowym klastrze, aby użyć obrazu wyspecjalizowanego procesora GPU AKS.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Jeśli chcesz utworzyć klaster przy użyciu zwykłych obrazów AKS, możesz to zrobić, pomijając `--aks-custom-headers` znacznik niestandardowy. Możesz również dodać bardziej wyspecjalizowane pule węzłów procesora GPU jak poniżej.


### <a name="use-the-aks-specialized-gpu-image-on-existing-clusters-preview"></a>Korzystanie z AKS wyspecjalizowanego obrazu procesora GPU w istniejących klastrach (wersja zapoznawcza)

Skonfiguruj nową pulę węzłów, aby używać AKS wyspecjalizowanego obrazu procesora GPU. Użyj `--aks-custom-headers` flagi flagi dla węzłów agenta procesora GPU w nowej puli węzłów, aby użyć obrazu AKS wyspecjalizowanego procesora GPU.

```azurecli
az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Jeśli chcesz utworzyć pulę węzłów przy użyciu zwykłych obrazów AKS, możesz to zrobić, pomijając `--aks-custom-headers` tag niestandardowy. 

> [!NOTE]
> Jeśli jednostka SKU procesora GPU wymaga maszyn wirtualnych generacji 2, można utworzyć:
> ```azurecli
> az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6s_v2 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true
> ```

## <a name="confirm-that-gpus-are-schedulable"></a>Upewnij się, że procesory GPU są harmonogramie

Po utworzeniu klastra AKS upewnij się, że procesory GPU są harmonogramie w Kubernetes. Najpierw należy wyświetlić listę węzłów w klastrze za pomocą polecenia [polecenia kubectl Get nodes][kubectl-get] :

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

Teraz użyj polecenia [polecenia kubectl opis węzła][kubectl-describe] , aby upewnić się, że procesory GPU są harmonogramie. W sekcji *pojemność* procesor GPU powinien zostać wystawiony jako `nvidia.com/gpu:  1` .

Następujący wąski przykład pokazuje, że procesor GPU jest dostępny w węźle o nazwie *AKS-nodepool1-18821093-0*:

```console
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 attachable-volumes-azure-disk:  24
 cpu:                            6
 ephemeral-storage:              101584140Ki
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         57713784Ki
 nvidia.com/gpu:                 1
 pods:                           110
Allocatable:
 attachable-volumes-azure-disk:  24
 cpu:                            5916m
 ephemeral-storage:              93619943269
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         51702904Ki
 nvidia.com/gpu:                 1
 pods:                           110
System Info:
 Machine ID:                 b0cd6fb49ffe4900b56ac8df2eaa0376
 System UUID:                486A1C08-C459-6F43-AD6B-E9CD0F8AEC17
 Boot ID:                    f134525f-385d-4b4e-89b8-989f3abb490b
 Kernel Version:             4.15.0-1040-azure
 OS Image:                   Ubuntu 16.04.6 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.12.7
 Kube-Proxy Version:         v1.12.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  kube-system                nvidia-device-plugin-daemonset-bbjlq     0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m39s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Uruchamianie obciążenia z obsługą procesora GPU

Aby zobaczyć, jak działa procesor GPU, Zaplanuj obciążenie procesora GPU odpowiednimi żądaniami zasobów. W tym przykładzie uruchomimy zadanie [Tensorflow](https://www.tensorflow.org/) z [zestawem danych mnist ręcznie](http://yann.lecun.com/exdb/mnist/).

Utwórz plik o nazwie *Samples-TF-mnist ręcznie-demonstracyjn. YAML* i wklej następujący manifest YAML. Następujący manifest zadania zawiera limit zasobów `nvidia.com/gpu: 1` :

> [!NOTE]
> Jeśli wystąpi błąd niezgodności wersji podczas wywoływania sterowników, na przykład wersja sterownika CUDA jest niewystarczająca dla wersji środowiska uruchomieniowego CUDA, zapoznaj się z wykresem zgodności macierzy sterowników NVIDIA — [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

Użyj polecenia [polecenia kubectl Apply][kubectl-apply] , aby uruchomić zadanie. To polecenie analizuje plik manifestu i tworzy zdefiniowane obiekty Kubernetes:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Wyświetlanie stanu i danych wyjściowych obciążenia z obsługą procesora GPU

Monitoruj postęp zadania za pomocą polecenia [polecenia kubectl Pobierz zadania][kubectl-get] z `--watch` argumentem. Pierwsze pobranie obrazu i przetworzenie zestawu danych może potrwać kilka minut. Gdy kolumna *ukończenia* zawiera *1/1*, zadanie zostało pomyślnie zakończone. Wyjdź `kubetctl --watch` z polecenia *Ctrl-C*:

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Aby wyszukać dane wyjściowe obciążenia z obsługą procesora GPU, należy najpierw pobrać nazwę pod z [polecenia kubectl Get][kubectl-get] — polecenie:

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Teraz Użyj [dzienników polecenia kubectl][kubectl-logs] , aby wyświetlić dzienniki pod. W poniższym przykładzie dzienników można potwierdzić, że odpowiednie urządzenie GPU zostało wykryte, `Tesla K80` . Podaj nazwę własnego:

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć skojarzone obiekty Kubernetes utworzone w tym artykule, użyj polecenia [Usuń zadanie polecenia kubectl][kubectl delete] w następujący sposób:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Następne kroki

Aby uruchomić zadania Apache Spark, zobacz [uruchamianie Apache Spark zadań w AKS][aks-spark].

Aby uzyskać więcej informacji na temat uruchamiania obciążeń uczenia maszynowego (ML) w witrynie Kubernetes, zobacz [Kubeflow Labs][kubeflow-labs].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
