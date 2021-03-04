---
title: Service Fabric zarządzane klastry (wersja zapoznawcza)
description: Service Fabric zarządzanych klastrów to ewolucja modelu zasobów klastra usługi Azure Service Fabric, który usprawnia zarządzanie wdrażaniem i klastrem.
ms.topic: overview
ms.date: 02/15/2021
ms.openlocfilehash: 271852214097ee96ba6b10de7a94904981cd8ef8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041233"
---
# <a name="service-fabric-managed-clusters-preview"></a>Service Fabric zarządzane klastry (wersja zapoznawcza)

Service Fabric zarządzanych klastrów to ewolucja modelu zasobów klastra usługi Azure Service Fabric, który usprawnia środowisko zarządzania wdrożeniami i klastrami.

Szablon modelu zasobów platformy Azure (ARM) dla tradycyjnych klastrów Service Fabric wymaga zdefiniowania zasobu klastra wraz z liczbą zasobów pomocniczych, z których wszystkie muszą być odpowiednio "przewodowe" (po wdrożeniu i w całym cyklu życia klastra), aby klaster i usługi działały prawidłowo. Z kolei model hermetyzacji dla Service Fabric zarządzanych klastrów składa się z pojedynczego *Service Fabric zarządzanego zasobu klastra* . Wszystkie zasoby bazowe dla klastra są wyodrębniane i zarządzane przez platformę Azure w Twoim imieniu.

**Service Fabric tradycyjny model klastra** 
 ![ Service Fabric tradycyjny model klastra][sf-composition]

**Service Fabric model** 
 ![ klastra zarządzanego Service Fabric zhermetyzowany model klastra][sf-encapsulation]

W zakresie rozmiaru i złożoności szablon ARM dla Service Fabric zarządzanego klastra ma około 100 wierszy JSON, a niektóre 1000 linie wymagane do zdefiniowania typowego klastra Service Fabric:

| Zasoby usługi Service Fabric | Service Fabric zarządzanych zasobów klastra |
|----------|-----------|
| Klaster usługi Service Fabric | Klaster zarządzany Service Fabric |
| Zestawy skalowania maszyn wirtualnych | |
| Moduł równoważenia obciążenia | |
| Publiczny adres IP | |
| Konta magazynu | |
| Sieć wirtualna | |

Service Fabric klastry zarządzane zapewniają wiele zalet w porównaniu do tradycyjnych klastrów:

**Uproszczone wdrażanie klastrów i zarządzanie nimi**
- Wdrażanie pojedynczego zasobu platformy Azure i zarządzanie nim
- Zarządzanie certyfikatami i ich autorotacja
- Uproszczone operacje skalowania

**Zapobieganie błędom operacyjnym**
- Zapobiegaj niezgodności konfiguracji z zasobami bazowymi
- Blokuj niebezpieczne operacje (na przykład usuwanie węzła inicjatora)

**Najlepsze rozwiązania domyślne**
- Uproszczone ustawienia niezawodności i trwałości

Nie ma dodatkowych kosztów Service Fabric zarządzanych klastrów wykraczających poza koszt zasobów podstawowych wymaganych przez klaster.

## <a name="service-fabric-managed-cluster-skus"></a>Service Fabric zarządzanych jednostek SKU klastra

Service Fabric zarządzane klastry są dostępne zarówno w podstawowych, jak i standardowych jednostkach SKU.

| Cecha | Podstawowa | Standardowa (Standard) |
| ------- | ----- | -------- |
| Zasób sieciowy (jednostka SKU dla [Load Balancer](../load-balancer/skus.md), [publiczny adres IP](../virtual-network/public-ip-addresses.md)) | Podstawowa | Standardowa (Standard) |
| Minimalna liczba węzłów (wystąpienie maszyny wirtualnej) | 3 | 5 |
| Maksymalna liczba węzłów na typ węzła | 100 | 100 |
| Maksymalna liczba typów węzłów | 1 | 20 |
| Dodawanie/usuwanie typów węzłów | Nie | Tak |
| Nadmiarowość stref | Nie | Tak |

## <a name="whats-new-for-service-fabric-managed-clusters"></a>Co nowego w Service Fabric zarządzanych klastrów

Najnowsze funkcje dla Service Fabric zarządzanych klastrów zapoznawczą obejmują:

* [Wdrażanie aplikacji przy użyciu szablonów ARM](how-to-managed-cluster-app-deployment-template.md)
* [Automatyczne uaktualnienia systemu operacyjnego](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades)
* [Szyfrowanie dysków](how-to-enable-managed-cluster-disk-encryption.md)
* [Stosowanie reguł sieciowej grupy zabezpieczeń](how-to-managed-cluster-networking.md)

Funkcje, które mają zostać dodane w przyszłych wydaniach, obejmują:

* Wdrażanie aplikacji przy użyciu programu Visual Studio
* Obsługa tożsamości zarządzanych
* Strefy dostępności
* Zwrotny serwer proxy
* Skalowanie automatyczne

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z klastrami zarządzanymi Service Fabric, wypróbuj Przewodnik Szybki Start:

> [!div class="nextstepaction"]
> [Utwórz klaster zarządzany Service Fabric (wersja zapoznawcza)](quickstart-managed-cluster-template.md)


[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png