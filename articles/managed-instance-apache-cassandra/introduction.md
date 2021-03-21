---
title: Wprowadzenie do usługi Azure Managed instance for Apache Cassandra
description: Dowiedz się więcej o wystąpieniu zarządzanym platformy Azure dla oprogramowania Apache Cassandra. Ta usługa zarządza wdrażaniem i skalowaniem natywnych wystąpień typu "open source" platformy Apache Cassandra na platformie Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 03/02/2021
ms.openlocfilehash: d99c62e7d88510c351f87d85b4f8c5c271767cb3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101748677"
---
# <a name="what-is-azure-managed-instance-for-apache-cassandra-preview"></a>Co to jest wystąpienie zarządzane platformy Azure dla oprogramowania Apache Cassandra? (Wersja zapoznawcza)

> [!IMPORTANT]
> Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wystąpienie zarządzane przez platformę Azure dla usługi Apache Cassandra zapewnia zautomatyzowane operacje wdrażania i skalowania dla centrów danych w zarządzanych źródłach oprogramowania Apache Cassandra. Ta usługa ułatwia przyspieszenie scenariuszy hybrydowych i zmniejszenie trwającej konserwacji. Będzie ona miała ścisłą integrację i możliwości przenoszenia danych z [Azure Cosmos DB interfejs API Cassandra](../cosmos-db/cassandra-introduction.md) na jej ogólnej wersji.

:::image type="content" source="./media/introduction/icon.gif" alt-text="Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra to usługa zarządzana dla oprogramowania Apache Cassandra." border="false":::

## <a name="key-benefits"></a>Najważniejsze korzyści

### <a name="hybrid-deployments"></a>Wdrożenia hybrydowe

Za pomocą tej usługi można łatwo umieścić zarządzane wystąpienia centrów danych Apache Cassandra, które są wdrażane automatycznie jako zestawy skalowania maszyn wirtualnych, w nowym lub istniejącym Virtual Network platformy Azure. Te centra danych można dodać do istniejącego pierścienia Apache Cassandra działającego lokalnie za pośrednictwem [usługi Azure ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) na platformie Azure lub innego środowiska chmury.

- **Uproszczone wdrażanie:** Wdrożenie łączności hybrydowej jest łatwe w użyciu protokołu Gossip.
- **Hostowane metryki:** Metryki są hostowane w usłudze [Prometheus](https://prometheus.io/docs/introduction/overview/) , aby monitorować aktywność w klastrze.

### <a name="simplified-scaling"></a>Uproszczone skalowanie

W przypadku wystąpienia zarządzanego skalowanie węzłów w górę i w dół w centrum danych jest w pełni zarządzane. Wprowadzasz wymaganą liczbę węzłów, a koordynator skalowania zajmuje się nawiązaniem działania w ramach pierścienia Cassandra.

### <a name="managed-and-cost-effective"></a>Zarządzane i ekonomiczne

Usługa udostępnia operacje zarządzania dla następujących typowych zadań usługi Apache Cassandra:

- Inicjowanie obsługi administracyjnej klastra
- Inicjowanie obsługi administracyjnej centrum danych
- Skalowanie centrum danych
- Usuwanie centrum danych
- Rozpocznij akcję naprawy na przestrzeni kluczy
- Zmiana konfiguracji centrum danych
- Skonfiguruj kopie zapasowe

Model cen jest elastyczny, na żądanie, oparty na wystąpieniach i nie ma opłat licencyjnych. Ten model cen umożliwia dostosowanie do konkretnych potrzeb związanych z obciążeniem. Możesz wybrać liczbę rdzeni, dla których ilość pamięci maszyny wirtualnej (SKU) i rozmiar miejsca na dysku jest wymagana.

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z jednym z naszych przewodników szybki start:

* [Utwórz klaster wystąpienia zarządzanego na podstawie Azure Portal](create-cluster-portal.md)
* [Wdróż zarządzany Klaster Apache Spark z Azure Databricks](deploy-cluster-databricks.md)
* [Zarządzanie wystąpieniem zarządzanym platformy Azure dla zasobów Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure](manage-resources-cli.md)
