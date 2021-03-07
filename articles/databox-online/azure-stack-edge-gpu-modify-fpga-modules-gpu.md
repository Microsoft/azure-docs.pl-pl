---
title: Modyfikowanie modułów IoT Edge na urządzeniu FPGA do uruchamiania na urządzeniu z systemem Azure Stack EDGE Pro GPU
description: Opisuje modyfikacje, które są potrzebne dla istniejących modułów IoT Edge na istniejących urządzeniach FPGA do uruchamiania na urządzeniu GPU Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 660fbf7cc4dd28c800d8f49fd5d990c99f97c4c8
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442999"
---
# <a name="run-existing-iot-edge-modules-from-azure-stack-edge-pro-fpga-devices-on-azure-stack-edge-pro-gpu-device"></a>Uruchamianie istniejących modułów IoT Edge z urządzeń Azure Stack EDGE Pro FPGA na urządzeniu z systemem Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Ten artykuł zawiera szczegółowe informacje o zmianach wymaganych dla modułu IoT Edge opartego na platformie Docker, który działa na Azure Stack EDGE Pro FPGA, tak aby można go było uruchomić na podstawie Kubernetes platformy IoT Edge na urządzeniu Azure Stack brzeg Pro GPU. 

## <a name="about-iot-edge-implementation"></a>Informacje o implementacji IoT Edge 

Implementacja IoT Edge jest różna na urządzeniach z systemem Azure Stack EDGE Pro FPGA a tym, że na urządzeniach z procesorem GPU w systemie Azure Stack Edge. W przypadku urządzeń z procesorem GPU Kubernetes jest używany jako platforma hostingu dla IoT Edge. IoT Edge na urządzeniach FPGA korzysta z platformy Docker. Model aplikacji oparty na platformie Docker IoT Edge jest automatycznie tłumaczony na natywny model aplikacji Kubernetes. Jednak niektóre zmiany mogą nadal być potrzebne, ponieważ obsługiwany jest tylko niewielki podzbiór modelu aplikacji Kubernetes.

W przypadku migrowania obciążeń z urządzenia FPGA do urządzenia GPU należy wprowadzić zmiany w istniejących modułach IoT Edge dla tych, które mają zostać pomyślnie uruchomione na platformie Kubernetes. Może być konieczne określenie innych wymagań dotyczących magazynu, sieci, użycia zasobów i serwera proxy sieci Web. 

## <a name="storage"></a>Storage

Podczas określania magazynu dla modułów IoT Edge należy wziąć pod uwagę następujące informacje.

- Magazyn kontenerów w systemie Kubernetes jest określany przy użyciu instalacji woluminów.
- Wdrożenie w usłudze Kubernetes nie może mieć powiązań do kojarzenia magazynu trwałego lub ścieżek hosta.
    - W przypadku magazynu trwałego należy użyć `Mounts` typu z typem `volume` .
    - W przypadku ścieżek hosta Użyj `Mounts` typu z `bind` .
- Aby uzyskać IoT Edge w Kubernetes, powiąż za poorednictwem `Mounts` programu Works tylko dla katalogu, a nie dla pliku.

#### <a name="example---storage-via-volume-mounts"></a>Przykład — magazyn za pośrednictwem instalacji woluminów 

W przypadku IoT Edge na platformie Docker ścieżka hosta powiązania są używane do mapowania udziałów na urządzeniu na ścieżki wewnątrz kontenera. Poniżej przedstawiono opcje tworzenia kontenera używane na urządzeniach FPGA:

```
{
  "HostConfig": 
  {
   "Binds": 
    [
     "<Host storage path for Edge local share>:<Module storage path>"
    ]
   }
}
```
<!-- is this how it will look on GPU device?-->
W przypadku ścieżek hosta dla IoT Edge w Kubernetes, przykład użycia `Mounts` z typem `bind` jest przedstawiony tutaj:
```
{
    "HostConfig": {
        "Mounts": [
            {
                "Target": "<Module storage path>",
                "Source": "<Host storage path>",
                "Type": "bind"
            }
        ]
    }
}
```


<!--following example is for persistent storage where we use mounts w/ type volume-->

W przypadku urządzeń GPU z systemem IoT Edge w systemie Kubernetes instalacje woluminów są używane do określania magazynu. Aby udostępnić magazyn przy użyciu udziałów, wartość będzie `Mounts.Source` nazwą udziału SMB lub NFS, który został zainicjowany na urządzeniu GPU. `/home/input`Jest to ścieżka, w której wolumin jest dostępny w ramach kontenera. Poniżej przedstawiono opcje tworzenia kontenera używane na urządzeniach GPU:

```
{
    "HostConfig": {
        "Mounts": [
        {
            "Target": "/home/input",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        },
        {
            "Target": "/home/output",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        }]
    }
}
```



## <a name="network"></a>Sieć

Podczas określania sieci dla modułów IoT Edge należy wziąć pod uwagę następujące informacje. 

- `HostPort` Specyfikacja jest wymagana do udostępnienia usługi zarówno wewnątrz, jak i poza klastrem.
    - K8sExperimental opcje ograniczające narażenie usługi tylko na klaster.
- Komunikacja między modułami wymaga `HostPort` specyfikacji i połączenia przy użyciu zamapowanego portu (i nie jest używany port uwidoczniony kontenerów).
- Sieć hostów współpracuje z usługą `dnsPolicy = ClusterFirstWithHostNet` , dzięki czemu wszystkie kontenery (zwłaszcza `edgeHub` ) nie muszą znajdować się również w sieci hosta. <!--Need further clarifications on this one-->
- Dodawanie mapowań portów dla protokołu TCP, UDP w tym samym żądaniu nie działa.

#### <a name="example---external-access-to-modules"></a>Przykład — zewnętrzny dostęp do modułów 

Dla wszystkich modułów IoT Edge, które określają powiązania portów, adres IP jest przypisywany przy użyciu zakresu adresów IP usług zewnętrznych Kubernetes, który został określony w lokalnym interfejsie użytkownika urządzenia. Nie wprowadzono żadnych zmian w kontenerze opcje tworzenia między IoT Edge na platformie Docker a IoT Edge na Kubernetes, jak pokazano w poniższym przykładzie.  

```json 
{
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
        }
    }
}
```

Aby jednak wysyłać zapytania do adresu IP przypisanego do modułu, można użyć pulpitu nawigacyjnego Kubernetes, zgodnie z opisem w temacie [pobieranie adresu IP dla usług lub modułów](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules). 

Alternatywnie można [nawiązać połączenie z interfejsem programu PowerShell urządzenia](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) i użyć `iotedge` polecenia list, aby wyświetlić listę wszystkich modułów uruchomionych na urządzeniu. [Dane wyjściowe polecenia](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge) również wskazują zewnętrzne adresy IP skojarzone z modułem.


## <a name="resource-usage"></a>Użycie zasobu 

W przypadku konfiguracji IoT Edge opartej na Kubernetes na urządzeniach GPU zasoby takie jak przyspieszenie sprzętowe, pamięć i wymagania dotyczące procesora są określone inaczej niż w przypadku urządzeń FPGA. 

#### <a name="compute-acceleration-usage"></a>Użycie przyspieszenia obliczeń

Aby wdrożyć moduły w programie FPGA, użyj opcji tworzenia kontenera <!--with Device Bindings--> jak pokazano w następującej konfiguracji: <!--not sure where are device bindings in this config--> 

```json
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "microsoft.com/fpga_catapult": 2
        },
        "requests": {
        "microsoft.com/fpga_catapult": 2
        }
    }
    },
    "Env": [
    "WIRESERVER_ADDRESS=10.139.218.1"
    ]
}
``` 

    
<!--Note: The IP address assigned to your FPGA module's service can be used to send inferencing requests from outside the cluster OR your ML module can be used along with DBE Simple Module Flow by passing files to the module using an input share.-->
    
W przypadku procesora GPU należy użyć specyfikacji żądań zasobów zamiast powiązań urządzeń, jak pokazano w poniższej minimalnej konfiguracji. Żądasz zasobów firmy NVIDIA zamiast podnosi, a ty nie musi określisz `wireserver` . 

```json     
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "nvidia.com/gpu": 2
        }    
    }
}
```

#### <a name="memory-and-cpu-usage"></a>Użycie pamięci i procesora CPU
 
Aby ustawić użycie pamięci i procesora CPU, użyj limitów procesorów dla modułów w `k8s-experimental` sekcji. <!--can we verify if this is how we set limits of memory and CPU-->

```json
    "k8s-experimental": {
    "resources": {
        "limits": {
            "memory": "128Mi",
            "cpu": "500m",
            "nvidia.com/gpu": 2
        },
        "requests": {
            "nvidia.com/gpu": 2
        }
}
```
Specyfikacja pamięci i procesora CPU nie jest konieczna, ale ogólnie dobre rozwiązanie. Jeśli `requests` nie jest określony, wartości ustawione w limitach będą stosowane jako wymagane minimum. 

Użycie pamięci współużytkowanej dla modułów wymaga również innego sposobu. Na przykład można użyć trybu IPC hosta na potrzeby dostępu do pamięci udostępnionej między rozwiązaniami do analizy filmów wideo i wnioskowania, zgodnie z opisem w temacie [wdrażanie usługi Live Video Analytics na Azure Stack Edge](../media-services/live-video-analytics-edge/deploy-azure-stack-edge-how-to.md#deploy-live-video-analytics-edge-module-using-azure-portal).


## <a name="web-proxy"></a>Serwer proxy sieci Web 

Podczas konfigurowania serwera proxy sieci Web należy wziąć pod uwagę następujące informacje:

Jeśli masz skonfigurowany serwer proxy sieci Web w sieci, skonfiguruj następujące zmienne środowiskowe dla `edgeHub` wdrożenia w ramach konfiguracji IoT Edge opartej na platformie Docker na urządzeniach FPGA:

- `https_proxy : <proxy URL>`
- `UpstreamProtocol : AmqpWs` (chyba że serwer proxy sieci Web zezwala na `Amqp` ruch)

W przypadku konfiguracji IoT Edge opartych na Kubernetes na urządzeniach GPU należy skonfigurować tę dodatkową zmienną podczas wdrażania:

- `no_proxy`: localhost

- Serwer proxy IoT Edge na platformie Kubernetes używa portów 35000 i 35001. Upewnij się, że moduł nie jest uruchomiony na tych portach lub może spowodować konflikty portów. 

## <a name="other-differences"></a>Inne różnice

- **Strategia wdrażania**: może zajść potrzeba zmiany zachowania wdrożenia dla wszystkich aktualizacji modułu. Domyślne zachowanie dla modułów IoT Edge jest aktualizacją kroczącą. Takie zachowanie uniemożliwia ponowne uruchomienie zaktualizowanego modułu, jeśli moduł korzysta z zasobów, takich jak przyspieszenie sprzętowe lub porty sieciowe. Takie zachowanie może mieć nieoczekiwane skutki, szczególnie podczas rozwiązywania trwałych woluminów na platformie Kubernetes dla urządzeń GPU. Aby zastąpić to zachowanie domyślne, można określić `Recreate` w `k8s-experimental` sekcji w module.

    ```    
    {
      "k8s-experimental": {
        "strategy": {
          "type": "Recreate"
        }
      }
    }
    ```

- **Nazwy modułów**: nazwy modułów powinny przestrzegać konwencji nazewnictwa Kubernetes. Po przeniesieniu tych modułów do IoT Edge z Kubernetes może zajść potrzeba zmiany nazwy modułów uruchomionych na IoT Edge z platformą Docker. Aby uzyskać więcej informacji na temat nazewnictwa, zobacz [konwencje nazewnictwa Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/).
- **Inne opcje**: 
    - Niektóre opcje tworzenia platformy Docker, które działały na urządzeniach z systemem FPGA, nie będą działały w środowisku Kubernetes na urządzeniach GPU. Na przykład:, like — EntryPoint.<!--can we confirm what exactly is required here-->
    - Zmienne środowiskowe, takie jak `:` muszą zostać zastąpione przez `__` .
    - **Utworzenie kontenera** ze stanem Kubernetes pod względem stanu **wycofywania** dla modułu w zasobie IoT Hub. Istnieje wiele powodów, dla których nie ma w tym stanie, typowym powodem jest to, że duży obraz kontenera jest ściągany przez niską przepustowość sieci. Gdy pod tym stanem jest w tym stanie, stan modułu jest wyświetlany jako **wycofywania** w usłudze IoT Hub, chociaż moduł jest właśnie uruchamiany.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o sposobie [konfigurowania procesora GPU do korzystania z modułu](azure-stack-edge-j-series-configure-gpu-modules.md).
