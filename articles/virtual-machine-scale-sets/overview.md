---
title: Omówienie zestawów skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się więcej o zestawach skalowania maszyn wirtualnych platformy Azure i o sposobie automatycznego skalowania swoich aplikacji
author: mimckitt
ms.author: mimckitt
ms.topic: overview
ms.service: virtual-machine-scale-sets
ms.subservice: ''
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: dff83159511c6e8a08e8f212c01244e923ad1f5b
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376948"
---
# <a name="what-are-virtual-machine-scale-sets"></a>Co to są zestawy skalowania maszyn wirtualnych?
Zestawy skalowania maszyn wirtualnych platformy Azure umożliwiają tworzenie i Zarządzanie grupą maszyn wirtualnych o zrównoważonym obciążeniu. Liczba wystąpień maszyn wirtualnych może automatycznie zwiększać się lub zmniejszać w reakcji na zapotrzebowanie lub według zdefiniowanego harmonogramu. Zestawy skalowania zapewniają wysoką dostępność Twoim aplikacjom i pozwalają na centralnie zarządzanie, konfigurowanie i aktualizowanie dużej liczby maszyn wirtualnych. Za pomocą zestawów skalowania maszyn wirtualnych możesz tworzyć usługi na dużą skalę dla takich obszarów jak obliczenia, dane big data i obciążenia kontenera.


## <a name="why-use-virtual-machine-scale-sets"></a>Dlaczego warto używać zestawów skalowania maszyn wirtualnych?
Aby zapewnić nadmiarowość i poprawić wydajność, aplikacje są zazwyczaj dystrybuowane w wielu wystąpieniach. Klienci mogą uzyskać dostęp do Twojej aplikacji za pośrednictwem modułu równoważenia obciążenia, który rozdziela żądania do jednego z wystąpień aplikacji. Jeśli musisz przeprowadzić konserwację lub zaktualizować wystąpienie aplikacji, Twoi klienci muszą zostać przekierowani do innego dostępnego wystąpienia aplikacji. Aby nadążyć za dodatkowym zapotrzebowaniem klienta, może być konieczne zwiększenie liczby wystąpień aplikacji, gdzie jest uruchamiana Twoja aplikacja.

Zestawy skalowania maszyn wirtualnych platformy Azure zapewniają możliwości zarządzania aplikacjom działającym na wielu maszynach wirtualnych, [automatyczne skalowanie zasobów](virtual-machine-scale-sets-autoscale-overview.md) i równoważenie obciążenia ruchem. Zestawy skalowania zapewniają następujące kluczowe korzyści:

- **Łatwe tworzenie wielu maszyn wirtualnych i zarządzanie nimi**
    - Jeśli masz wiele maszyn wirtualnych, na których działa Twoja aplikacja, ważne jest zachowanie spójnej konfiguracji w całym środowisku. Zapewniana wydajność aplikacji, rozmiar maszyny wirtualnej, konfiguracja dysku i instalacje aplikacji powinny być zgodne na wszystkich maszynach wirtualnych.
    - Za pomocą zestawów skalowania wszystkie wystąpienia maszyn wirtualnych są tworzone na podstawie tego samego podstawowego obrazu systemu operacyjnego i jego konfiguracji. Takie podejście umożliwia łatwe zarządzanie setkami maszyn wirtualnych bez dodatkowych zadań konfiguracji lub zarządzania siecią.
    - Zestawy skalowania obsługują korzystanie z [modułu równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-overview.md) na potrzeby dystrybucji ruchu w warstwie 4 i na [platformie Azure Application Gateway](../application-gateway/overview.md) w celu uzyskania bardziej zaawansowanego rozkładu ruchu warstwy 7 i protokołu TLS.

- **Zapewnia wysoką dostępność i odporność aplikacji**
    - Zestawy skalowania służą do uruchamiania wielu wystąpień aplikacji. Jeśli jedno z tych wystąpień maszyny wirtualnej ma problem, klienci nadal mają dostęp do aplikacji za pośrednictwem jednego z innych wystąpień maszyn wirtualnych z minimalną przerwą.
    - Aby uzyskać dodatkową dostępność, możesz użyć [stref dostępności](../availability-zones/az-overview.md) w celu automatycznej dystrybucji wystąpień maszyn wirtualnych w zestawie skalowania w jednym centrum danych lub w wielu centrach danych.

- **Pozwala aplikacji na automatyczne skalowanie odpowiednio do zmian zapotrzebowania na zasoby**
    - Zapotrzebowanie klienta Twojej aplikacji może ulec zmianie w ciągu dnia lub tygodnia. Aby dostosować się do zapotrzebowania klienta, zestawy skalowania mogą automatycznie zwiększać liczbę wystąpień maszyn wirtualnych wraz ze wzrostem zapotrzebowania na aplikację, a następnie zmniejszać liczbę wystąpień maszyn wirtualnych, gdy zmniejsza się zapotrzebowanie.
    - Funkcja automatycznego skalowania minimalizuje również liczbę niepotrzebnych wystąpień maszyn wirtualnych, na których jest uruchomiona Twoja aplikacja, gdy zapotrzebowanie jest niskie, przy czym klienci nadal mają zapewniony akceptowalny poziom wydajności w miarę wzrostu zapotrzebowania, a dodatkowe wystąpienia maszyn wirtualnych są automatycznie dodawane. Ta możliwość ułatwia obniżenie kosztów i efektywnie tworzy zasoby platformy Azure zgodnie z potrzebami.

- **Działa na dużą skalę**
    - Zestaw skalowania obsługuje maksymalnie 1000 wystąpień maszyn wirtualnych. W przypadku utworzenia i przekazania własnych niestandardowych obrazów maszyn wirtualnych limit wynosi 600 wystąpień maszyn wirtualnych.
    - Aby uzyskać najlepszą wydajność dla obciążeń produkcyjnych, użyj funkcji [Dyski zarządzane platformy Azure](../virtual-machines/managed-disks-overview.md).


## <a name="differences-between-virtual-machines-and-scale-sets"></a>Różnice między maszynami wirtualnymi i zestawami skalowania
Zestawy skalowania są tworzone z maszyn wirtualnych. Zestawy skalowania udostępniają warstwy zarządzania i automatyzacji do uruchamiania i skalowania Twoich aplikacji. Zamiast tego możesz ręcznie tworzyć poszczególne maszyny wirtualne i zarządzać nimi lub integrować istniejące narzędzia do tworzenia podobnego poziomu automatyzacji. W poniższej tabeli przedstawiono zarys korzyści z zestawów skalowania w porównaniu do ręcznego zarządzania wieloma wystąpieniami maszyn wirtualnych.

| Scenariusz                           | Ręczne grupowanie maszyn wirtualnych                                                                    | Zestaw skalowania maszyn wirtualnych |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| Dodawanie kolejnych wystąpień maszyn wirtualnych        | Ręczny proces tworzenia, konfigurowania i zapewniania zgodności                             | Automatyczne tworzenie na podstawie centralnej konfiguracji |
| Równoważenie i dystrybucja ruchu | Ręczny proces tworzenia i konfigurowania modułu równoważenia obciążenia platformy Azure lub usługi Application Gateway      | Umożliwia automatyczne tworzenie i integrowanie z modułem równoważenia obciążenia platformy Azure lub usługą Application Gateway |
| Wysoka dostępność i nadmiarowość   | Ręczne tworzenie zestawu dostępności lub dystrybuowanie i śledzenie maszyn wirtualnych w różnych strefach dostępności | Automatyczna dystrybucja wystąpień maszyn wirtualnych w strefach dostępności lub zestawach dostępności |
| Skalowanie maszyn wirtualnych                     | Ręczne monitorowanie i usługa Azure Automation                                                 | Automatyczne skalowanie na podstawie metryk hosta, metryk gościa, usługi Application Insights lub harmonogramu |

Używanie zestawów skalowania nie pociąga za sobą dodatkowych kosztów. Płacisz tylko za podstawowe zasoby obliczeniowe, takie jak wystąpienia maszyn wirtualnych, moduł równoważenia obciążenia lub miejsce na dysku zarządzanym. Funkcje zarządzania i automatyzacji, takie jak skalowanie automatyczne i nadmiarowość, nie pociągają za sobą dodatkowych opłat za korzystanie z maszyn wirtualnych.

## <a name="how-to-monitor-your-scale-sets"></a>Jak monitorować zestawy skalowania

Użyj [Azure monitor dla maszyn wirtualnych](../azure-monitor/insights/vminsights-overview.md), który ma prosty proces dołączania i automatyzuje zbieranie ważnych liczników wydajności procesora CPU, pamięci, dysku i sieci z maszyn wirtualnych w zestawie skalowania. Zawiera również dodatkowe możliwości monitorowania i wstępnie zdefiniowane wizualizacje, które ułatwiają skoncentrowanie się na dostępności i wydajności zestawów skalowania.

Włącz monitorowanie [aplikacji zestawu skalowania maszyn wirtualnych](../azure-monitor/app/azure-vm-vmss-apps.md) za pomocą Application Insights, aby zbierać szczegółowe informacje o aplikacji, w tym o widokach stron, żądaniach aplikacji i wyjątkach. Sprawdź dostępność aplikacji, konfigurując [Test dostępności](../azure-monitor/app/monitor-web-app-availability.md) w celu zasymulowania ruchu użytkownika.

## <a name="data-residency"></a>Rezydencja danych

Na platformie Azure funkcja umożliwiająca przechowywanie danych klienta w jednym regionie jest obecnie dostępna tylko w regionie Azja Południowo-Wschodnia (Singapur) Azja i Pacyfik regionie geograficznym i Brazylia Południowa (stan Świętego Paulo) regionu Brazylia. W przypadku wszystkich innych regionów dane klienta są przechowywane w lokalizacji geograficznej. Aby uzyskać więcej informacji, zobacz [Centrum zaufania](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Następne kroki
Aby rozpocząć, utwórz swój pierwszy zestaw skalowania maszyn wirtualnych w witrynie Azure Portal.

> [!div class="nextstepaction"]
> [Tworzenie zestawu skalowania w witrynie Azure Portal](quick-create-portal.md)
