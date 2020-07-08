---
title: Dodawanie typu węzła do klastra usługi Azure Service Fabric
description: Dowiedz się, jak skalować w poziomie klaster Service Fabric, dodając zestaw skalowania maszyn wirtualnych.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 1e7bae89561d43d717eb4d15e95183761b077443
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75463979"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Skalowanie klastra Service Fabric przez dodanie zestawu skalowania maszyn wirtualnych
W tym artykule opisano sposób skalowania klastra Service Fabric platformy Azure przez dodanie nowego typu węzła do istniejącego klastra. Klaster Service Fabric jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym są wdrażane i zarządzane mikrousługi. Maszyna lub maszyna wirtualna będąca częścią klastra nazywa się węzłem. Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy platformy Azure, który służy do wdrażania kolekcji maszyn wirtualnych jako zestawu i zarządzania nią. Każdy typ węzła, który jest zdefiniowany w klastrze platformy Azure [, jest ustawiany jako oddzielny zestaw skalowania](service-fabric-cluster-nodetypes.md). Każdy typ węzła może być następnie zarządzany osobno. Po utworzeniu klastra Service Fabric można skalować klaster w poziomie, dodając nowy typ węzła (zestaw skalowania maszyn wirtualnych) do istniejącego klastra.  Klaster można skalować w dowolnym momencie, nawet w przypadku uruchamiania obciążeń w klastrze.  W miarę skalowania klastra aplikacje są automatycznie skalowane.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Dodawanie dodatkowego zestawu skalowania do istniejącego klastra
Dodanie nowego typu węzła (który jest obsługiwany przez zestaw skalowania maszyn wirtualnych) do istniejącego klastra jest podobne do [uaktualnienia podstawowego typu węzła](service-fabric-scale-up-node-type.md), z tą różnicą, że nie będzie można używać tego samego NodeTypeRef; Oczywiście nie należy wyłączać żadnych aktywnie używanych zestawów skalowania maszyn wirtualnych i nie utracisz dostępności klastra, jeśli nie zaktualizujesz podstawowego typu węzła. 

Właściwość NodeTypeRef jest zadeklarowana w ramach zestawu skalowania maszyn wirtualnych Service Fabric właściwości rozszerzenia:
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

Ponadto trzeba będzie dodać ten nowy typ węzła do zasobu klastra Service Fabric:

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się [, jak skalować w górę typ węzła podstawowego](service-fabric-scale-up-node-type.md)
* Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra platformy Azure w poziomie lub na zewnątrz](service-fabric-tutorial-scale-cluster.md).
* [Programowo Skaluj klaster platformy Azure](service-fabric-cluster-programmatic-scaling.md) przy użyciu zestawu Azure COMPUTE SDK.
* [Skalowanie klastra autonomicznego w poziomie lub na zewnątrz](service-fabric-cluster-windows-server-add-remove-nodes.md).

