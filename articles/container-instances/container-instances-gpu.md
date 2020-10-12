---
title: Wdróż wystąpienie kontenera z obsługą procesora GPU
description: Dowiedz się, jak wdrożyć usługę Azure Container Instances, aby uruchamiać aplikacje kontenera intensywnie korzystające z obliczeń przy użyciu zasobów procesora GPU.
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: 19240560baa0cebdb6777d7b63d8c91832b12e1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87387097"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Wdrażanie wystąpień kontenerów korzystających z zasobów procesora GPU

Aby uruchamiać pewne obciążenia intensywnie korzystające z obliczeń na Azure Container Instances, należy wdrożyć [grupy kontenerów](container-instances-container-groups.md) z *zasobami procesora GPU*. Wystąpienia kontenerów w grupie mogą uzyskać dostęp do co najmniej jednego procesora graficznego NVIDIA Tesla podczas wykonywania obciążeń kontenerów, takich jak CUDA i aplikacje do uczenia głębokiego.

W tym artykule przedstawiono sposób dodawania zasobów procesora GPU podczas wdrażania grupy kontenerów przy użyciu [pliku YAML](container-instances-multi-container-yaml.md) lub [szablonu Menedżer zasobów](container-instances-multi-container-group.md). Podczas wdrażania wystąpienia kontenera przy użyciu Azure Portal można także określić zasoby procesora GPU.

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej, a niektóre [ograniczenia mają zastosowanie](#preview-limitations). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

W wersji zapoznawczej poniższe ograniczenia mają zastosowanie w przypadku używania zasobów procesora GPU w grupach kontenerów. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

Obsługa zostanie dodana dla dodatkowych regionów w czasie.

**Obsługiwane typy systemów operacyjnych**: tylko system Linux

**Dodatkowe ograniczenia**: nie można używać zasobów procesora GPU podczas wdrażania grupy kontenerów w [sieci wirtualnej](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>Informacje o zasobach procesora GPU

### <a name="count-and-sku"></a>Liczba i jednostka SKU

Aby użyć procesorów GPU w wystąpieniu kontenera, określ *zasób procesora GPU* z następującymi informacjami:

* **Liczba** — liczba procesorów GPU: **1**, **2**lub **4**.
* **Jednostka SKU** — jednostka SKU procesora GPU: **K80**, **P100**lub **V100**. Każda jednostka SKU jest mapowana na procesor GPU NVIDIA Tesla w jednej z następujących rodzin maszyn wirtualnych z obsługą procesora GPU platformy Azure:

  | SKU | Rodzina maszyn wirtualnych |
  | --- | --- |
  | K80 | [NC](../virtual-machines/nc-series.md) |
  | P100 | [NCv2](../virtual-machines/ncv2-series.md) |
  | V100 | [Seria NCV3](../virtual-machines/ncv3-series.md) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

Podczas wdrażania zasobów procesora GPU należy ustawić zasoby procesora i pamięci odpowiednie dla obciążenia, maksymalnie do wartości maksymalnych przedstawionych w powyższej tabeli. Te wartości są obecnie większe niż zasoby procesora i pamięci dostępne w grupach kontenerów bez zasobów procesora GPU.  

> [!IMPORTANT]
> Domyślne [limity subskrypcji](container-instances-quotas.md) (limity przydziału) zasobów procesora GPU różnią się w zależności od jednostki SKU. Domyślne limity procesorów dla jednostek SKU P100 i V100 są początkowo ustawione na 0. Aby zażądać zwiększenia dostępnego regionu, Prześlij [żądanie pomocy technicznej systemu Azure][azure-support].

### <a name="things-to-know"></a>Co należy wiedzieć

* **Czas wdrożenia** — Tworzenie grupy kontenerów zawierającej zasoby procesora GPU trwa do **8-10 minut**. Wynika to z dodatkowego czasu na udostępnienie i skonfigurowanie maszyny wirtualnej procesora GPU na platformie Azure. 

* **Cennik** — podobnie jak w przypadku grup kontenerów bez zasobów procesora GPU, weksle dla zasobów używanych w *czasie trwania* grupy kontenerów z zasobami procesora GPU. Czas trwania jest obliczany od momentu pociągnięcia pierwszego obrazu kontenera do momentu zakończenia działania grupy kontenerów. Nie obejmuje czasu wdrożenia grupy kontenerów.

  Zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/container-instances/).

* **Sterowniki cuda** — wystąpienia kontenerów z zasobami procesora GPU są wstępnie udostępniane za pomocą sterowników NVIDIA CUDA i środowisk uruchomieniowych kontenera, dzięki czemu można używać obrazów kontenerów opracowanych dla obciążeń cuda.

  Na tym etapie obsługujemy tylko CUDA 9,0. Na przykład można użyć następujących obrazów podstawowych dla pliku Docker:
  * [NVIDIA/CUDA: 9.0-Base-Ubuntu 16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-GPU-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>Przykład YAML

Jednym ze sposobów dodawania zasobów procesora GPU jest wdrożenie grupy kontenerów przy użyciu [pliku YAML](container-instances-multi-container-yaml.md). Skopiuj następujący YAML do nowego pliku o nazwie *GPU-Deploy-ACI. YAML*, a następnie Zapisz plik. Ten YAML tworzy grupę kontenerów o nazwie *gpucontainergroup* określającą wystąpienie kontenera z procesorem GPU K80. Wystąpienie uruchamia przykładową aplikację do dodawania wektorów CUDA. Żądania zasobów są wystarczające do uruchomienia obciążenia.

```YAML
additional_properties: {}
apiVersion: '2019-12-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

Wdróż grupę kontenerów za pomocą polecenia [AZ Container Create][az-container-create] , określając nazwę pliku YAML dla `--file` parametru. Należy podać nazwę grupy zasobów i lokalizację dla grupy kontenerów, na przykład *Wschodnie* , która obsługuje zasoby procesora GPU.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Przeprowadzenie wdrożenia zajmuje kilka minut. Następnie zostanie uruchomiony kontener i zostanie uruchomiona operacja dodawania wektora CUDA. Uruchom polecenie [AZ Container Logs][az-container-logs] , aby wyświetlić dane wyjściowe dziennika:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Dane wyjściowe:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Przykład szablonu Menedżer zasobów

Innym sposobem wdrożenia grupy kontenerów z zasobami procesora GPU jest użycie [szablonu Menedżer zasobów](container-instances-multi-container-group.md). Zacznij od utworzenia pliku o nazwie `gpudeploy.json` , a następnie skopiuj do niego Poniższy kod JSON. W tym przykładzie wdrożono wystąpienie kontenera z procesorem GPU V100, który uruchamia zadanie szkolenia [TensorFlow](https://www.tensorflow.org/) z zestawem danych mnist ręcznie. Żądania zasobów są wystarczające do uruchomienia obciążenia.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

Wdróż szablon za pomocą polecenia [AZ Deployment Group Create][az-deployment-group-create] . Należy podać nazwę grupy zasobów, która została utworzona w regionie, takim jak *Wschodnie* , która obsługuje zasoby procesora GPU.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file gpudeploy.json
```

Przeprowadzenie wdrożenia zajmuje kilka minut. Następnie zostanie uruchomiony kontener i zostanie uruchomione zadanie TensorFlow. Uruchom polecenie [AZ Container Logs][az-container-logs] , aby wyświetlić dane wyjściowe dziennika:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

Dane wyjściowe:

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Ponieważ korzystanie z zasobów procesora GPU może być kosztowne, należy się upewnić, że kontenery nie działają nieoczekiwanie przez długi czas. Monitoruj kontenery w Azure Portal lub Sprawdź stan grupy kontenerów za pomocą polecenia [AZ Container show][az-container-show] . Na przykład:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Po zakończeniu pracy z utworzonymi wystąpieniami kontenerów usuń je za pomocą następujących poleceń:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o wdrażaniu grupy kontenerów przy użyciu [pliku YAML](container-instances-multi-container-yaml.md) lub [szablonu Menedżer zasobów](container-instances-multi-container-group.md).
* Dowiedz się więcej o [rozmiarach maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU](../virtual-machines/sizes-gpu.md) na platformie Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
