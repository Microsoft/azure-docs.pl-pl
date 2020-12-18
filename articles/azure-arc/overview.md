---
title: Omówienie usługi Azure Arc
description: Dowiedz się więcej na temat tego, co to jest usługa Azure Arc i jak pomaga klientom w zarządzaniu zasobami hybrydowymi i zarządzaniem nimi za pomocą innych usług i funkcji platformy Azure.
ms.date: 09/23/2020
ms.topic: overview
ms.openlocfilehash: 8f39dff16037b5f52a050b7d07193b0ad7cab93d
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672690"
---
# <a name="azure-arc-overview"></a>Omówienie usługi Azure Arc

Obecnie firmy są zoptymalizowaniem do kontrolowania i zarządzania środowiskiem, które staną się bardziej skomplikowane. Te środowiska rozciągają się między centrami danych, wieloma chmurami i krawędziami. Każde środowisko i chmura mają swój własny zestaw rozłącznych narzędzi do zarządzania, które trzeba poznać i obsługiwać.

Równolegle, nowe modele operacyjne DevOps i ITOps są trudne do zaimplementowania, ponieważ istniejące narzędzia nie zapewniają obsługi nowych wzorców natywnych w chmurze.

Usługa Azure Arc upraszcza zarządzanie, zapewniając spójną platformę zarządzania w chmurze i lokalną. Usługa Azure Arc umożliwia zarządzanie całym środowiskiem przy użyciu jednego okienka ze szkła, poprzez projekcję istniejących zasobów do Azure Resource Manager. Teraz możesz zarządzać maszynami wirtualnymi, klastrami Kubernetes i bazami danych, tak jakby działały na platformie Azure. Niezależnie od tego, gdzie się znajdują, możesz używać znanych usług platformy Azure i możliwości zarządzania. Usługa Azure Arc umożliwia kontynuowanie korzystania z tradycyjnych ITOps przy jednoczesnym wprowadzeniu praktyk DevOps do obsługi nowych wzorców natywnych w chmurze w środowisku.

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Diagram płaszczyzny kontroli zarządzania usługą Azure Arc" border="false":::

Teraz usługa Azure Arc umożliwia zarządzanie następującymi typami zasobów hostowanymi poza platformą Azure:

* Serwery — zarówno fizyczne, jak i wirtualne maszyny z systemem Windows lub Linux.
* Klastry Kubernetes — obsługa wielu dystrybucji Kubernetes.
* Usługi danych platformy Azure — Azure SQL Database i PostgreSQL.

## <a name="what-does-azure-arc-deliver"></a>Co zapewnia usługa Azure Arc?

Najważniejsze funkcje usługi Azure Arc obejmują:

* Zaimplementuj spójne spisy, zarządzanie, nadzór i zabezpieczenia dla serwerów w środowisku użytkownika.

* Skonfiguruj [rozszerzenia maszyny wirtualnej platformy Azure](./servers/manage-vm-extensions.md) , aby używać usług zarządzania platformy Azure do monitorowania, zabezpieczania i aktualizowania serwerów.

* Zarządzanie klastrami Kubernetes na dużą skalę i zarządzania nimi.

* Użyj konfiguracji opartej na GitOps jako zarządzania kodem, aby wdrażać aplikacje i konfigurację w jednym lub większej liczbie klastrów bezpośrednio z kontroli źródła, takich jak GitHub.

* Niezgodność i konfiguracja dla klastrów Kubernetes przy użyciu Azure Policy.

* Uruchamiaj usługi danych platformy Azure w dowolnym środowisku Kubernetes, w tym w przypadku wystąpienia zarządzanego usługi Azure SQL i skalowania Azure Database for PostgreSQL, z korzyściami, takimi jak uaktualnienia/aktualizacje, zabezpieczenia i monitorowanie, tak jakby działały na platformie Azure. Korzystaj z elastycznej skali, stosuj aktualizacje bez przestojów aplikacji, nawet jeśli nie ma ciągłego połączenia z platformą Azure.

* Ujednolicone środowisko wyświetlania zasobów z obsługą usługi Azure ARC, niezależnie od tego, czy używasz Azure Portal, interfejsu wiersza polecenia platformy Azure, Azure PowerShell czy usługi Azure REST API.

## <a name="how-much-does-azure-arc-cost"></a>Ile kosztuje usługa Azure Arc?

Poniżej znajdują się szczegółowe informacje o cenach dla funkcji dostępnych dzisiaj dzięki usłudze Azure Arc.

### <a name="arc-enabled-servers"></a>Serwery z obsługą usługi Arc

Funkcja płaszczyzny kontroli usługi Azure Arc jest oferowana bez dodatkowych kosztów. Obejmuje to następujące działania:

* Organizacja zasobów za pomocą grup i tagów zarządzania platformy Azure.

* Wyszukiwanie i indeksowanie za pomocą grafu zasobów platformy Azure.

* Dostęp i zabezpieczenia za pomocą RBAC i subskrypcji platformy Azure.

* Środowiska i Automatyzacja za poorednictwem szablonów i rozszerzeń.

* Zarządzanie aktualizacjami

Dla każdej usługi platformy Azure, która jest używana na serwerach z włączonym łukiem, na przykład Azure Security Center lub Azure Monitor, zostanie naliczona opłata zgodnie z cennikiem tej usługi. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem platformy Azure](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-kubernetes"></a>Platforma Kubernetes z włączoną usługą Azure Arc

W bieżącej fazie wersji zapoznawczej usługa Azure Arc Kubernetes jest oferowana bez dodatkowych kosztów.

### <a name="azure-arc-enabled-data-services"></a>Usługi danych z obsługą usługi Azure Arc

W bieżącej fazie wersji zapoznawczej usługi danych z włączonym usługą Azure Arc są oferowane bez dodatkowych kosztów.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat serwerów z obsługą łuku, zobacz następujące [Omówienie](./servers/overview.md)

* Aby dowiedzieć się więcej na temat Kubernetes z włączonym łukiem, zobacz następujące [Omówienie](./kubernetes/overview.md)

* Aby dowiedzieć się więcej na temat usług danych z obsługą łuku, zobacz następujące [Omówienie](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/)

* Usługi z obsługą łuku z [szybko Rozpocznij pracę Weryfikacja koncepcji](https://azurearcjumpstart.io/azure_arc_jumpstart/)
