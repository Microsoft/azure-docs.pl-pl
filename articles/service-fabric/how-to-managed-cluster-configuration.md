---
title: Skonfiguruj klaster zarządzany Service Fabric (wersja zapoznawcza)
description: Dowiedz się, jak skonfigurować klaster zarządzany Service Fabric na potrzeby automatycznych uaktualnień systemu operacyjnego, reguł sieciowej grupy zabezpieczeń i innych.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 44b1b949fe314231cb44f190c31b53903e47a904
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732636"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Opcje konfiguracji klastra zarządzanego (wersja zapoznawcza) Service Fabric

Oprócz wybrania [Service Fabric zarządzanej jednostki SKU klastra](overview-managed-cluster.md#service-fabric-managed-cluster-skus) podczas tworzenia klastra istnieją różne sposoby ich konfiguracji. W bieżącej wersji zapoznawczej można:

* Konfigurowanie [opcji sieciowych](how-to-managed-cluster-networking.md) dla klastra
* Dodawanie [rozszerzenia zestawu skalowania maszyn wirtualnych](how-to-managed-cluster-vmss-extension.md) do typu węzła
* Skonfiguruj [zarządzaną tożsamość](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) w typach węzłów
* Włączanie [automatycznych uaktualnień systemu operacyjnego](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) dla węzłów
* Włączanie [szyfrowania dysków systemu operacyjnego i danych](how-to-enable-managed-cluster-disk-encryption.md) w węzłach

## <a name="enable-automatic-os-image-upgrades"></a>Włącz automatyczne uaktualnianie obrazu systemu operacyjnego

Można włączyć automatyczne uaktualnienia obrazu systemu operacyjnego do maszyn wirtualnych, na których są uruchomione zarządzane węzły klastra. Mimo że zasoby zestawu skalowania maszyn wirtualnych są zarządzane w Twoim imieniu przy użyciu Service Fabric zarządzanych klastrów, wybór umożliwia włączenie automatycznych uaktualnień obrazów systemu operacyjnego dla węzłów klastra. Podobnie jak w przypadku [klasycznych klastrów Service Fabric](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) , zarządzane węzły klastra nie są domyślnie uaktualniane, aby zapobiec niezamierzonym zakłóceniom w klastrze.

Aby włączyć automatyczne uaktualnienia systemu operacyjnego:

* Użyj `2021-01-01-preview` wersji (lub nowszej) *Microsoft. servicefabric/Managedclusters* i *Microsoft. servicefabric/managedclusters/elementów NodeType* Resources
* Ustaw właściwość klastra `enableAutoOSUpgrade` na *wartość true*
* Ustaw właściwość zasobu elementów NodeType klastra na wartość `vmImageVersion` *Najnowsza*

Na przykład:

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

Po włączeniu Service Fabric rozpocznie wykonywanie zapytań i śledzenie wersji obrazu systemu operacyjnego w zarządzanym klastrze. Jeśli dostępna jest nowa wersja systemu operacyjnego, typy węzłów klastra (zestawy skalowania maszyn wirtualnych) zostaną uaktualnione pojedynczo. Uaktualnienia środowiska uruchomieniowego Service Fabric są wykonywane dopiero po potwierdzeniu braku uaktualnienia obrazu systemu operacyjnego węzła klastra.

Jeśli uaktualnienie nie powiedzie się, Service Fabric ponowi próbę po upływie 24 godzin. Podobnie jak w przypadku uaktualnień Service Fabric klasyczny (niezarządzany), aplikacje w złej kondycji lub węzły mogą blokować uaktualnienie obrazu systemu operacyjnego.

Aby uzyskać więcej informacji na temat uaktualnień obrazów, zobacz [automatyczne uaktualnienia obrazu systemu operacyjnego za pomocą zestawów skalowania maszyn wirtualnych platformy Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

## <a name="next-steps"></a>Następne kroki

[Omówienie Service Fabric zarządzanych klastrów](overview-managed-cluster.md)

[Service Fabric cluster templates (Szablony klastrów usługi Service Fabric)](https://github.com/Azure-Samples/service-fabric-cluster-templates)
