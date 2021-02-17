---
title: Często zadawane pytania dotyczące Service Fabric zarządzanych klastrów
description: Często zadawane pytania dotyczące Service Fabric zarządzanych klastrów, w tym możliwości, przypadków użycia i typowych scenariuszy.
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 02/15/2021
ms.custom: references_regions
ms.openlocfilehash: aa77896ba88d0ffd0a6f94a84603b5f4a1803357
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633091"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Service Fabric często zadawane pytania dotyczące klastrów zarządzanych

Poniżej przedstawiono kilka często zadawanych pytań i odpowiedzi na Service Fabric zarządzanych klastrów (wersja zapoznawcza).

## <a name="general"></a>Ogólne

### <a name="what-are-service-fabric-managed-clusters"></a>Co to są Service Fabric zarządzanych klastrów?

Service Fabric zarządzanych klastrów to ewolucja Service Fabric modelu zasobów klastra, który ułatwia wdrażanie klastrów i zarządzanie nimi. W Service Fabric zarządzanym klastrze jest używany model hermetyzacji Azure Resource Manager, dzięki czemu użytkownik musi tylko zdefiniować i wdrożyć pojedynczy zasób klastra w porównaniu z wieloma niezależnymi zasobami, które muszą zostać wdrożone dzisiaj (zestaw skalowania maszyn wirtualnych, Load Balancer, adres IP i inne).

### <a name="what-regions-are-supported-in-the-preview"></a>Jakie regiony są obsługiwane w wersji zapoznawczej?

Obsługiwane regiony dla Service Fabric zarządzanych klastrów zapoznawczych obejmują `centraluseuap` ,, `eastus2euap` ,, `eastasia` `northeurope` `westcentralus` i `eastus2` .

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>Czy można wykonać migrację w miejscu istniejącego klastra Service Fabric do zarządzanego zasobu klastra?

Nie. W tej chwili trzeba utworzyć nowy zasób klastra Service Fabric, aby użyć nowego Service Fabric typu zasobów klastra zarządzanego.

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Czy istnieją dodatkowe koszty Service Fabric zarządzanych klastrów?

Nie. Nie ma dodatkowych kosztów związanych z klastrem zarządzanym Service Fabric wykraczających poza koszty zasobów obliczeniowych, magazynu i sieci, które są wymagane przez klaster.

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Czy istnieje nowa umowa SLA wprowadzona przez Service Fabric zarządzany zasób klastra?

Umowa SLA nie zmienia się z bieżącego modelu zasobów Service Fabric.

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>Jaka jest różnica między podstawowym i standardowym klastrem SKU?

Podstawowy klaster SKU oznacza większość konfiguracji udostępnianych przez dostawcę zasobów Service Fabric. Podstawowe klastry SKU są przeznaczone do użycia na potrzeby testowania i środowiska produkcyjnego. Klaster standardowej jednostki SKU umożliwia użytkownikom skonfigurowanie klastra w celu spełnienia wymagań. Aby uzyskać więcej informacji, zobacz [Service Fabric zarządzanych jednostek SKU klastra](./overview-managed-cluster.md#service-fabric-managed-cluster-skus).

## <a name="cluster-deployment-and-management"></a>Wdrażanie i zarządzanie klastrami

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>Uruchamiam niestandardowe rozszerzenia skryptów w moim zestawie skalowania maszyn wirtualnych, czy mogę to zrobić z zarządzanym zasobem Service Fabric?

Tak, możesz określić rozszerzenia maszyn wirtualnych na zarządzanych typach węzłów klastra. Aby uzyskać więcej informacji, zobacz [Dodawanie rozszerzenia zestawu skalowania do Service Fabric typu węzła klastra zarządzanego](how-to-managed-cluster-vmss-extension.md).

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>Czy chcesz mieć tylko wewnętrzny moduł równoważenia obciążenia?

Nie. Obecnie nie jest możliwe posiadanie wyłącznie wewnętrznego modułu równoważenia obciążenia. Zalecamy zablokowanie reguł sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w celu blokowania dowolnego nieżądanego ruchu przychodzącego/wychodzącego.

### <a name="can-i-autoscale-my-cluster"></a>Czy mogę automatycznie skalować mój klaster?

Skalowanie automatyczne nie jest obecnie dostępne w wersji zapoznawczej.

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>Czy mogę wdrożyć klaster w strefach dostępności?

Klastry strefy Cross Availability nie są obecnie dostępne w wersji zapoznawczej.

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>Czy mogę wybrać automatyczne i ręczne uaktualnienia dla mojego środowiska uruchomieniowego klastra?

W wersji zapoznawczej wszystkie uaktualnienia środowiska uruchomieniowego zostaną wykonane automatycznie.

## <a name="applications"></a>Aplikacje

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Czy istnieje lokalne środowisko programistyczne dla Service Fabric zarządzanych klastrów?

Lokalne środowisko programistyczne pozostaje niezmienione z istniejących klastrów Service Fabric. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska deweloperskiego](./service-fabric-get-started.md) , aby uzyskać więcej informacji na temat lokalnego środowiska programistycznego.

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>Czy mogę wdrożyć aplikacje jako zasób Azure Resource Manager?

Tak. Dodano obsługę wdrażania aplikacji jako zasobów Azure Resource Manager (oprócz wdrożenia przy użyciu programu PowerShell i interfejsu wiersza polecenia). Aby rozpocząć, zobacz [wdrażanie aplikacji klastra zarządzanego Service Fabric przy użyciu szablonu usługi ARM](how-to-managed-cluster-app-deployment-template.md).
