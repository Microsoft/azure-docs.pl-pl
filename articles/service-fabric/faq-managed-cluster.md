---
title: Często zadawane pytania dotyczące Service Fabric zarządzanych klastrów
description: Często zadawane pytania dotyczące Service Fabric zarządzanych klastrów, w tym możliwości, przypadków użycia i typowych scenariuszy.
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 77d7b3fba62fb9f059df97c4f7fae99db783f3f0
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91729002"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Service Fabric często zadawane pytania dotyczące klastrów zarządzanych

Istnieje wiele często zadawanych pytań na temat tego, co mogą robić Service Fabric zarządzane klastry (wersja zapoznawcza) i jak powinny być używane. Ten dokument zawiera wiele typowych pytań i odpowiedzi na nie.

## <a name="general"></a>Ogólne

### <a name="what-are-service-fabric-managed-clusters"></a>Co to są Service Fabric zarządzanych klastrów?

Service Fabric zarządzanych klastrów to ewolucja Service Fabric modelu zasobów klastra, który ułatwia wdrażanie klastrów i zarządzanie nimi. W Service Fabric zarządzanym klastrze jest używany model hermetyzacji Azure Resource Manager, dzięki czemu użytkownik musi tylko zdefiniować i wdrożyć pojedynczy zasób klastra w porównaniu z wieloma niezależnymi zasobami, które muszą zostać wdrożone dzisiaj (zestaw skalowania maszyn wirtualnych, Load Balancer, adres IP i inne).

### <a name="what-regions-are-supported-in-the-preview"></a>Jakie regiony są obsługiwane w wersji zapoznawczej?

Obsługiwane regiony dla Service Fabric zarządzanych klastrów zapoznawczych obejmują `centraluseuap` ,, `eastus2euap` ,, `eastasia` `northeurope` `westcentralus` i `eastus2` .

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>Czy można wykonać migrację w miejscu istniejącego klastra Service Fabric do zarządzanego zasobu klastra?

W tej chwili trzeba utworzyć nowy zasób klastra Service Fabric, aby użyć nowego Service Fabric typu zasobów klastra zarządzanego.

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Czy istnieją dodatkowe koszty Service Fabric zarządzanych klastrów?

Nie. nie ma dodatkowego kosztu związanego z klastrem zarządzanym Service Fabric wykraczający poza koszty zasobów obliczeniowych, magazynu i sieci, które są wymagane przez klaster.

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Czy istnieje nowa umowa SLA wprowadzona przez Service Fabric zarządzany zasób klastra?

Umowa SLA nie zmienia się z bieżącego modelu zasobów Service Fabric.

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>Jaka jest różnica między podstawowym i standardowym klastrem SKU?

Podstawowy klaster SKU oznacza większość konfiguracji udostępnianych przez dostawcę zasobów Service Fabric. Podstawowe klastry SKU są przeznaczone do użycia na potrzeby testowania i środowiska produkcyjnego. Klaster standardowej jednostki SKU umożliwia użytkownikom skonfigurowanie klastra w celu spełnienia wymagań. Aby uzyskać więcej informacji, zobacz [Service Fabric zarządzanych jednostek SKU klastra](https://docs.microsoft.com/azure/service-fabric/overview-managed-cluster#service-fabric-managed-cluster-skus) , aby uzyskać więcej szczegółów.

## <a name="cluster-deployment-and-management"></a>Wdrażanie i zarządzanie klastrami

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>Uruchamiam niestandardowe rozszerzenia skryptów w moim zestawie skalowania maszyn wirtualnych, czy mogę to zrobić z zarządzanym zasobem Service Fabric?

Tak, można nadal określić rozszerzenia maszyn wirtualnych dla typu węzła. Aby uzyskać więcej informacji, zobacz przykład rozszerzenia typu węzła, aby uzyskać więcej szczegółów.

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>Czy chcę mieć tylko wewnętrzny moduł równoważenia obciążenia?

Obecnie nie jest możliwe posiadanie tylko wewnętrznego modułu równoważenia obciążenia. Zalecamy zablokowanie reguł sieciowej grupy zabezpieczeń w celu blokowania dowolnego nieżądanego ruchu przychodzącego/wychodzącego.

### <a name="can-i-autoscale-my-cluster"></a>Czy mogę automatycznie skalować mój klaster? 
Skalowanie automatyczne nie jest obecnie dostępne w wersji zapoznawczej.

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>Czy mogę wdrożyć klaster w strefach dostępności? 
Klastry strefy Cross Availability nie są obecnie dostępne w wersji zapoznawczej.

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>Czy mogę wybrać automatyczne i ręczne uaktualnienia dla mojego środowiska uruchomieniowego klastra? 
W wersji zapoznawczej wszystkie uaktualnienia środowiska uruchomieniowego zostaną wykonane automatycznie.

## <a name="applications"></a>Aplikacje

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Czy istnieje lokalne środowisko programistyczne dla Service Fabric zarządzanych klastrów?

Lokalne środowisko programistyczne pozostaje niezmienione z istniejących klastrów Service Fabric. Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji platformy .NET](https://docs.microsoft.com/azure/service-fabric/service-fabric-quickstart-dotnet) , aby uzyskać więcej informacji na temat lokalnego środowiska programistycznego.

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>Czy mogę wdrożyć aplikacje jako zasób Azure Resource Manager?

W wersji zapoznawczej nie można wdrażać aplikacji jako zasobów Azure Resource Manager. Aplikacje muszą być wdrażane przez bezpośrednie połączenie z klastrem za pomocą programu PowerShell lub interfejsu wiersza polecenia. Ta funkcja zostanie dodana przed udostępnieniem klastrów Service Fabric, które będą miały dostęp do ogólnej dostępności.
