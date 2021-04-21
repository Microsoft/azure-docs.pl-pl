---
title: Wdrażanie wystąpienia kontenera z obsługą procesora GPU
description: Dowiedz się, jak wdrażać wystąpienia kontenerów platformy Azure w celu uruchamiania aplikacji kontenera intensywnie korzystających z mocy obliczeniowej przy użyciu zasobów procesora GPU.
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: 6ffe4840d024c1e1f551966d05673c4ba83e1259
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764067"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Wdrażanie wystąpień kontenerów, które korzystają z zasobów procesora GPU

Aby uruchomić niektóre obciążenia intensywnie obciążane obliczeniami na Azure Container Instances, wd wdrażaj grupy [kontenerów](container-instances-container-groups.md) z *zasobami procesora GPU.* Wystąpienia kontenerów w grupie mogą uzyskać dostęp do co najmniej jednego procesorów GPU NVIDIA Tesla podczas uruchamiania obciążeń kontenerów, takich jak cuda i aplikacje uczenia głębokiego.

W tym artykule przedstawiono sposób dodawania zasobów procesora GPU podczas wdrażania grupy kontenerów przy użyciu pliku [YAML](container-instances-multi-container-yaml.md) [lub Resource Manager szablonu](container-instances-multi-container-group.md). Zasoby procesora GPU można również określić podczas wdrażania wystąpienia kontenera przy użyciu Azure Portal.

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej i obowiązują [pewne ograniczenia.](#preview-limitations) Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

W wersji zapoznawczej w przypadku korzystania z zasobów procesora GPU w grupach kontenerów obowiązują następujące ograniczenia. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

Obsługa zostanie dodana dla dodatkowych regionów w czasie.

**Obsługiwane typy systemów operacyjnych:** tylko system Linux

**Dodatkowe ograniczenia:** nie można używać zasobów procesora GPU podczas wdrażania grupy kontenerów w sieci [wirtualnej.](container-instances-vnet.md)

## <a name="about-gpu-resources"></a>Informacje o zasobach procesora GPU

### <a name="count-and-sku"></a>Liczba i SKU

Aby użyć procesorów GPU w wystąpieniu kontenera, określ zasób *procesora GPU* z następującymi informacjami:

* **Count** — liczba procesorów GPU: **1,** **2** lub **4.**
* **SKU** — karta SKU procesora GPU: **K80,** **P100** lub **V100.** Każda sku mapuje do procesora GPU NVIDIA Tesla w jednej z następujących rodzin maszyn wirtualnych z obsługą procesora GPU platformy Azure:

  | SKU | Rodzina maszyn wirtualnych |
  | --- | --- |
  | K80 | [NC](../virtual-machines/nc-series.md) |
  | P100 | [NCv2](../virtual-machines/ncv2-series.md) |
  | Wersja 100 | [NCv3](../virtual-machines/ncv3-series.md) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

Podczas wdrażania zasobów procesora GPU należy ustawić zasoby procesora i pamięci odpowiednie dla obciążenia do maksymalnej wartości przedstawionej w powyższej tabeli. Te wartości są obecnie większe niż zasoby procesora CPU i pamięci dostępne w grupach kontenerów bez zasobów procesora GPU.  

> [!IMPORTANT]
> Domyślne [limity subskrypcji](container-instances-quotas.md) (limity przydziału) dla zasobów procesora GPU różnią się w zależności od sku. Domyślne limity procesora dla jednostki SKU P100 i V100 są początkowo ustawione na 0. Aby zażądać zwiększenia liczby dostępnych regionów, prześlij wniosek o pomoc [techniczną platformy Azure.][azure-support]

### <a name="things-to-know"></a>Co należy wiedzieć

* **Czas wdrożenia** — tworzenie grupy kontenerów zawierającej zasoby procesora GPU trwa do **8–10 minut.** Jest to spowodowane dodatkowym czasem na aprowizowanie i skonfigurowanie maszyny wirtualnej z procesorem GPU na platformie Azure. 

* **Cennik** — podobnie jak w przypadku grup kontenerów bez  zasobów procesora GPU, platforma Azure nalicza opłaty za zasoby używane przez grupę kontenerów z zasobami procesora GPU. Czas trwania jest obliczany od czasu ściągnięcie obrazu pierwszego kontenera do momentu zakończenia grupy kontenerów. Nie obejmuje czasu wdrożenia grupy kontenerów.

  Zobacz [szczegóły cennika.](https://azure.microsoft.com/pricing/details/container-instances/)

* **Sterowniki CUDA** — wystąpienia kontenerów z zasobami procesora GPU są wstępnie aprowowane za pomocą sterowników CUDA firmy NVIDIA i środowisk uruchomieniowych kontenerów, dzięki czemu można używać obrazów kontenerów opracowanych dla obciążeń CUDA.

  Na tym etapie obsługujemy tylko cuda 9.0. Na przykład można użyć następujących obrazów podstawowych dla pliku platformy Docker:
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>Przykład YAML

Jednym ze sposobów dodawania zasobów procesora GPU jest wdrożenie grupy kontenerów przy użyciu [pliku YAML.](container-instances-multi-container-yaml.md) Skopiuj następujący kod YAML do nowego pliku o nazwie *gpu-deploy-aci.yaml, a* następnie zapisz plik. Ten plik YAML tworzy grupę kontenerów o *nazwie gpucontainergroup* określającą wystąpienie kontenera z procesorem GPU K80. Wystąpienie uruchamia przykładową aplikację do dodatku wektorów CUDA. Żądania zasobów są wystarczające do uruchomienia obciążenia.

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

Wdróż grupę kontenerów za pomocą [polecenia az container create,][az-container-create] określając nazwę pliku YAML dla `--file` parametru . Należy podać nazwę grupy zasobów i lokalizację dla grupy kontenerów, taką jak *eastus,* która obsługuje zasoby procesora GPU.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Przeprowadzenie wdrożenia zajmuje kilka minut. Następnie kontener uruchamia i uruchamia operację dodatku wektora CUDA. Uruchom polecenie [az container logs,][az-container-logs] aby wyświetlić dane wyjściowe dziennika:

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

## <a name="resource-manager-template-example"></a>Resource Manager przykład szablonu

Innym sposobem wdrożenia grupy kontenerów z zasobami procesora GPU jest użycie [szablonu Resource Manager gpu.](container-instances-multi-container-group.md) Rozpocznij od utworzenia pliku o nazwie , a `gpudeploy.json` następnie skopiuj do niego następujący kod JSON. W tym przykładzie jest wdrażane wystąpienie kontenera z procesorem GPU v100, który uruchamia zadanie szkoleniowe [TensorFlow](https://www.tensorflow.org/) względem zestaw danych MNIST. Żądania zasobów są wystarczające do uruchomienia obciążenia.

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
      "containerimage": "mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu"
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

Wd wdrażaj szablon za pomocą [polecenia az deployment group create.][az-deployment-group-create] Należy podać nazwę grupy zasobów, która została utworzona w regionie, takim jak *eastus,* który obsługuje zasoby procesora GPU.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file gpudeploy.json
```

Przeprowadzenie wdrożenia zajmuje kilka minut. Następnie kontener uruchamia i uruchamia zadanie TensorFlow. Uruchom polecenie [az container logs,][az-container-logs] aby wyświetlić dane wyjściowe dziennika:

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

Ponieważ korzystanie z zasobów procesora GPU może być kosztowne, upewnij się, że kontenery nie działają nieoczekiwanie przez długi czas. Monitoruj kontenery w Azure Portal kontenerów lub sprawdź stan grupy kontenerów za pomocą [polecenia az container show.][az-container-show] Na przykład:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Po pracy z utworzonymi wystąpieniami kontenerów usuń je za pomocą następujących poleceń:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o wdrażaniu grupy kontenerów przy użyciu pliku [YAML](container-instances-multi-container-yaml.md) [lub Resource Manager szablonu](container-instances-multi-container-group.md).
* Dowiedz się więcej o rozmiarach [maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU na](../virtual-machines/sizes-gpu.md) platformie Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
