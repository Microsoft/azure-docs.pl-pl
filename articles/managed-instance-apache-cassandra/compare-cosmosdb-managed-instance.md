---
title: Różnice między wystąpieniami zarządzanymi platformy Azure dla usług Apache Cassandra i Azure Cosmos DB interfejs API Cassandra
description: Dowiedz się więcej o różnicach między wystąpieniem zarządzanym platformy Azure a programem Apache Cassandra i interfejs API Cassandra w Azure Cosmos DB. Poznasz również zalety każdej z tych usług i kiedy je wybierzesz.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 3050dda4b3c0e1a05d751a4f5969bba69ad0506d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748761"
---
# <a name="differences-between-azure-managed-instance-for-apache-cassandra-preview-and-azure-cosmos-db-cassandra-api"></a>Różnice między wystąpieniami zarządzanymi platformy Azure dla oprogramowania Apache Cassandra (wersja zapoznawcza) i Azure Cosmos DB interfejs API Cassandra 

Ten artykuł zawiera informacje o różnicach między wystąpieniem zarządzanym platformy Azure a programem Apache Cassandra oraz interfejs API Cassandra w Azure Cosmos DB. Ten artykuł zawiera zalecenia dotyczące wyboru między obiema usługami lub w przypadku hostowania własnego środowiska Apache Cassandra.

> [!IMPORTANT]
> Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-differences"></a>Podstawowe różnice

Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra zapewnia Zautomatyzowane wdrażanie, skalowanie i wykonywanie operacji w celu zapewnienia kondycji węzłów dla wystąpień platformy Apache Cassandra Open Source na platformie Azure. Oferuje również możliwość skalowania pojemności istniejących klastrów usługi Apache Cassandra lokalnie lub w chmurze. Można je skalować przez dodanie zarządzanych centrów danych Cassandra do istniejącego pierścienia klastra.

[Interfejs API Cassandra](../cosmos-db/cassandra-introduction.md) w Azure Cosmos DB to warstwa zgodności oparta na globalnie rozproszonej usłudze baz danych firmy Microsoft w chmurze, [Azure Cosmos DB](../cosmos-db/index.yml). Połączenie tych usług na platformie Azure zapewnia użytkownikom możliwości wyboru składnika Apache Cassandra w złożonych środowiskach chmury hybrydowej.

## <a name="how-to-choose"></a>Jak wybrać?

W poniższej tabeli przedstawiono typowe scenariusze, wymagania dotyczące obciążeń i sposoby, w których każda z tych metod wdrażania jest zgodna:

| |Samodzielne środowisko Apache Cassandra w środowisku lokalnym lub na platformie Azure | Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra | Interfejs API Cassandra usługi Azure Cosmos DB |
|---------|---------|---------|---------|
|**Typ wdrożenia**| Masz wysoce dostosowane wdrożenie Apache Cassandra z niestandardowymi poprawkami lub snitches. | Masz standardowe wdrożenie Apache Cassandra typu "open source" bez kodu niestandardowego. | Jest to zawartość z platformą, która nie jest Cassandra Apache, ale jest zgodna ze wszystkimi sterownikami klienta typu "open source" na poziomie [protokołu](../cosmos-db/cassandra-support.md) sieci. |
| **Narzuty operacyjne**| Masz istniejących ekspertów Cassandra, którzy mogą wdrażać, konfigurować i obsługiwać swoje klastry.  | Chcesz obniżyć koszty operacyjne dotyczące kondycji węzłów Apache Cassandra, ale nadal zachować kontrolę nad konfiguracjami poziomu platformy, takimi jak replikacja i spójność. | Aby wyeliminować koszty operacyjne, należy użyć w pełni zarządzanej bazy danych platformy jako usługi w chmurze. |
| **Wymagania dotyczące systemu operacyjnego**| Istnieje wymóg, aby zachować niestandardowe lub złote obrazy systemu operacyjnego maszyn wirtualnych. | Można używać obrazów IOPS, ale chcą mieć kontrolę nad jednostkami SKU, pamięcią, dyskami i operacjami IOPS. | Alokacja pojemności ma być uproszczona i wyrażana jako pojedyncza znormalizowana Metryka, z relacją jeden-do-jednego do przepływności, na przykład [jednostki żądań](../cosmos-db/request-units.md) w Azure Cosmos DB. |
| **Model cen**| Chcesz używać oprogramowania do zarządzania, takiego jak narzędzia DataStax, i są zadowolony z kosztów licencjonowania. | Preferujesz wycenę czystych licencji Open Source i opartych na wystąpieniach maszyn wirtualnych. | Chcesz użyć cen natywnych w chmurze, które obejmują [Automatyczne skalowanie](../cosmos-db/manage-scale-cassandra.md#use-autoscale) i oferty [bezserwerowe](../cosmos-db/serverless.md) . |
| **Analiza**| Chcesz mieć pełną kontrolę nad aprowizacjim potoków analitycznych niezależnie od nakładu na ich Kompilowanie i konserwowanie. | Chcesz korzystać z usług analitycznych opartych na chmurze, takich jak Azure Databricks. | Chcesz, Cosmos DB aby w czasie rzeczywistym była oparta na platformie [Azure Synapse](../cosmos-db/synapse-link.md), która umożliwia tworzenie hybrydowej analizy transakcyjnej. |
| **Wzorzec obciążenia**| Obciążenie jest dość stabilne i nie wymaga częstego skalowania węzłów w klastrze. | Obciążenie jest nietrwałe i musisz mieć możliwość skalowania węzłów w górę lub w dół w centrum danych lub łatwego dodawania/usuwania centrów danych. | Obciążenie jest często nietrwałe i należy mieć możliwość szybkiego skalowania w górę lub w dół w znaczący sposób. |
| **Umowy SLA**| Twoje procesy są zadowalające, aby zapewnić obsługę umowy SLA na temat spójności, przepustowości, dostępności i odzyskiwania po awarii. | Masz zadowoleni z Twoich procesów, aby utrzymywać umowy SLA na temat spójności, przepływności i dostępności, ale potrzebujesz pomocy przy tworzeniu kopii zapasowych. | Chcesz całkowicie kompleksowo umowy SLA na temat spójności, przepływności, dostępności i odzyskiwania po awarii. |

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z jednym z naszych przewodników szybki start:

* [Utwórz klaster wystąpienia zarządzanego na podstawie Azure Portal](create-cluster-portal.md)