---
title: Najlepsze rozwiązania dotyczące sieci Service Fabric platformy Azure
description: Najlepsze rozwiązania i zagadnienia dotyczące projektowania dotyczące zarządzania łącznością sieciową za pomocą usługi Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 853e53d32f87f81e5db587de2654f83037930da7
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261136"
---
# <a name="networking"></a>Networking

Podczas tworzenia klastrów Service Fabric platformy Azure i zarządzania nimi można zapewnić łączność sieciową dla węzłów i aplikacji. Zasoby sieci obejmują zakresy adresów IP, sieci wirtualne, moduły równoważenia obciążenia i sieciowe grupy zabezpieczeń. W tym artykule przedstawiono najlepsze rozwiązania dotyczące tych zasobów.

Przejrzyj [wzorce sieci Service Fabric](./service-fabric-patterns-networking.md) platformy Azure, aby dowiedzieć się, jak tworzyć klastry korzystające z następujących funkcji: istniejąca sieć wirtualna lub podsieć, statyczny publiczny adres IP, moduł równoważenia obciążenia wyłącznie wewnętrznie lub wewnętrzny i zewnętrzny moduł równoważenia obciążenia.

## <a name="infrastructure-networking"></a>Sieć infrastruktury
Zmaksymalizuj wydajność maszyny wirtualnej za pomocą przyspieszonej sieci, deklarując Właściwość enableAcceleratedNetworking w szablonie Menedżer zasobów, Poniższy fragment kodu jest NetworkInterfaceConfigurations zestawu skalowania maszyn wirtualnych, który umożliwia przyspieszenie sieci:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
Klaster Service Fabric może być inicjowany w systemie [Linux przy użyciu przyspieszonej sieci](../virtual-network/create-vm-accelerated-networking-cli.md)i [systemu Windows z przyspieszoną siecią](../virtual-network/create-vm-accelerated-networking-powershell.md).

Przyspieszona sieć jest obsługiwana w przypadku jednostek SKU serii maszyn wirtualnych platformy Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 i MS/MMS. Przyspieszona sieć została pomyślnie przetestowana przy użyciu jednostki SKU Standard_DS8_v3 na 1/23/2019 dla Service Fabric klastra systemu Windows i przy użyciu Standard_DS12_v2 na 01/29/2019 dla klastra Service Fabric Linux.

Aby włączyć przyspieszone sieci w istniejącym klastrze Service Fabric, należy najpierw [skalować klaster Service Fabric przez dodanie zestawu skalowania maszyn wirtualnych](./virtual-machine-scale-set-scale-node-type-scale-out.md), aby wykonać następujące czynności:
1. Inicjowanie obsługi administracyjnej NodeType z włączoną obsługą przyspieszonej sieci
2. Migrowanie usług i ich stanu do aprowizacji NodeType z włączoną obsługą przyspieszonej sieci

Skalowanie w górę infrastruktury jest wymagane do włączenia przyspieszonej sieci w istniejącym klastrze, ponieważ włączenie przyspieszonej sieci spowoduje przestoje, ponieważ wymaga ono zatrzymania i cofnięcia przydziału wszystkich maszyn wirtualnych w zestawie dostępności [przed włączeniem przyspieszonej sieci na dowolnej istniejącej karcie sieciowej](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Sieć klastrów

* Klastry Service Fabric można wdrożyć w istniejącej sieci wirtualnej, wykonując czynności opisane w temacie [Service Fabric wzorców sieci](./service-fabric-patterns-networking.md).

* Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) są zalecane w przypadku typów węzłów, które ograniczają ruch przychodzący i wychodzący do klastra. Upewnij się, że wymagane porty są otwarte w sieciowej grupy zabezpieczeń. Na przykład: ![ Service Fabric reguł sieciowej grupy zabezpieczeń][NSGSetup]

* Typ węzła podstawowego, który zawiera Service Fabric usług systemu nie musi być narażony za pośrednictwem zewnętrznego modułu równoważenia obciążenia i może być narażony przez [wewnętrzny moduł równoważenia obciążenia](./service-fabric-patterns-networking.md#internal-only-load-balancer)

* Użyj [statycznego publicznego adresu IP](./service-fabric-patterns-networking.md#static-public-ip-address-1) dla klastra.

## <a name="application-networking"></a>Sieć aplikacji

* Aby można było uruchomić obciążenia kontenerów systemu Windows, należy użyć [trybu otwartej sieci](./service-fabric-networking-modes.md#set-up-open-networking-mode) do ułatwienia komunikacji między usługami.

* Użyj zwrotnego serwera proxy, takiego jak [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) lub [Service Fabric zwrotnego serwera proxy](./service-fabric-reverseproxy.md) w celu udostępnienia typowych portów aplikacji, takich jak 80 lub 443.

* W przypadku kontenerów systemu Windows hostowanych na maszynach gapped powietrznych, które nie mogą pobierać warstw podstawowych z magazynu w chmurze platformy Azure, Zastąp zachowanie warstwy obcej przy użyciu flagi [--Allow-undystrybucyjne-artefakty](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) w demona platformy Docker.

## <a name="next-steps"></a>Następne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Service Fabric tworzenia klastra dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
