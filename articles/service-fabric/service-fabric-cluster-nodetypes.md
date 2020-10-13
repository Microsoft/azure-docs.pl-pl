---
title: Typy węzłów i zestawy skalowania maszyn wirtualnych
description: Dowiedz się, jak typy węzłów usługi Azure Service Fabric są powiązane z zestawami skalowania maszyn wirtualnych i jak zdalnie łączyć się z wystąpieniem zestawu skalowania lub węzłem klastra.
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: 870467760a2baaa887b06fb8e01335f225f04d6e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90561894"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Typy węzłów usługi Azure Service Fabric i zestawy skalowania maszyn wirtualnych

[Zestawy skalowania maszyn wirtualnych](../virtual-machine-scale-sets/index.yml) to zasób obliczeniowy platformy Azure. Zestawy skalowania umożliwiają wdrażanie kolekcji maszyn wirtualnych jako zestawu i zarządzanie nimi. Każdy typ węzła, który definiujesz w klastrze Service Fabric platformy Azure, ustawia dokładnie jeden zestaw skalowania: nie można wykonać kopii zapasowej wielu typów węzłów za pomocą tego samego zestawu skalowania, a jeden typ węzła nie powinien być obsługiwany przez wiele zestawów skalowania. Wyjątkiem jest w rzadkim przypadku [skalowania w pionie](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations) typu węzła, gdy program ma tymczasowo dwa zestawy skalowania o tej samej `nodeTypeRef` wartości, podczas gdy repliki są migrowane z oryginału do uaktualnionego zestawu skalowania.

Środowisko uruchomieniowe Service Fabric jest instalowane na każdej maszynie wirtualnej w zestawie skalowania za pomocą rozszerzenia maszyny wirtualnej *Microsoft. Azure. servicefabric* . Można niezależnie skalować każdy typ węzła w górę lub w dół, zmieniać jednostkę SKU systemu operacyjnego działającą w każdym węźle klastra, mieć otwarte różne zestawy portów i korzystać z różnych metryk pojemności.

Na poniższej ilustracji przedstawiono klaster, który ma dwa typy węzłów o nazwie *fronton* i *zaplecze*. Każdy typ węzła ma pięć węzłów.

![Klaster, który ma dwa typy węzłów][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapuj wystąpienia zestawu skalowania maszyn wirtualnych na węzły

Jak pokazano na powyższym rysunku, wystąpienia zestawu skalowania rozpoczynają się w wystąpieniu 0, a następnie zwiększają się o 1. Numerowanie jest odzwierciedlone w nazwach węzłów. Na przykład węzeł BackEnd_0 jest wystąpieniem 0 zestawu skalowania zaplecza. Ten konkretny zestaw skalowania ma pięć wystąpień o nazwie BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 i BackEnd_4.

Podczas skalowania w poziomie zestawu skalowania jest tworzone nowe wystąpienie. Nowa nazwa wystąpienia zestawu skalowania jest zwykle nazwą zestawu skalowania i kolejnym numerem wystąpienia. W naszym przykładzie jest BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Moduły równoważenia obciążenia zestawu skalowania mapy z typami węzłów i zestawami skalowania

Jeśli klaster został wdrożony w Azure Portal lub użyto przykładowego szablonu Azure Resource Manager, zostanie wyświetlona lista wszystkich zasobów w grupie zasobów. Moduły równoważenia obciążenia są widoczne dla każdego zestawu skalowania lub typu węzła. Nazwa usługi równoważenia obciążenia używa następującego formatu: ** &lt; &gt; Nazwa typu węzła lb**. Przykładem jest LB-sfcluster4doc-0, jak pokazano na poniższym rysunku:

![Zrzut ekranu przedstawia grupę zasobów z wyróżnionymi dwoma usługami równoważenia obciążenia.][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Service Fabric rozszerzenie maszyny wirtualnej

Service Fabric rozszerzenie maszyny wirtualnej jest używane do ładowania początkowego Service Fabric do Virtual Machines platformy Azure i konfigurowania zabezpieczeń węzła.

Poniżej znajduje się fragment Service Fabric rozszerzenia maszyny wirtualnej:

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "dataPath": "D:\\\\SvcFab",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

Poniżej przedstawiono opisy właściwości:

| **Nazwa** | **Dozwolone wartości** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
| name | ciąg | Unikatowa nazwa rozszerzenia |
| typ | "ServiceFabricLinuxNode" lub "ServiceFabricWindowsNode" | Identyfikuje Service Fabric systemu operacyjnego |
| Włączoną flagą autoupgrademinorversion | true lub false | Włącz autouaktualnienie wersji pomocniczych w środowisku uruchomieniowym SF |
| publisher | Microsoft. Azure. servicefabric | Nazwa wydawcy rozszerzenia Service Fabric |
| clusterEndpont | ciąg | URI: PORT do punktu końcowego zarządzania |
| nodeTypeRef | ciąg | Nazwa nodeType |
| durabilityLevel | brąz, Silver, Gold, Platinum | Czas, w którym można wstrzymać niezmienne infrastruktury platformy Azure |
| enableParallelJobs | true lub false | Włącz funkcję COMPUTE ParallelJobs, na przykład Usuń maszynę wirtualną i ponownie uruchom maszynę wirtualną w zestawie skalowania równoległego |
| nicPrefixOverride | ciąg | Prefiks podsieci, taki jak "10.0.0.0/24" |
| commonNames | ciąg [] | Typowe nazwy zainstalowanych certyfikatów klastra |
| x509StoreName | ciąg | Nazwa magazynu, w którym znajduje się zainstalowany certyfikat klastra |
| typeHandlerVersion | 1,1 | Wersja rozszerzenia. 1,0 klasycznej wersji rozszerzenia zaleca się uaktualnienie do 1,1 |
| Ścieżka datapath | ciąg | Ścieżka do dysku używanego do zapisywania stanu Service Fabric usług systemowych i danych aplikacji.

## <a name="next-steps"></a>Następne kroki

* Zobacz [Omówienie funkcji "wdróż gdziekolwiek" i porównanie z klastrami zarządzanymi przez platformę Azure](service-fabric-deploy-anywhere.md).
* Dowiedz się więcej o [zabezpieczeniach klastra](service-fabric-cluster-security.md).
* [Połączenie zdalne](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) z określonym wystąpieniem zestawu skalowania
* [Aktualizowanie wartości zakresu portów protokołu RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) na maszynach wirtualnych klastra po wdrożeniu
* [Zmień nazwę użytkownika i hasło administratora](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) dla maszyn wirtualnych klastra

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
