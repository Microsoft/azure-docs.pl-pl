---
title: Często zadawane pytania dotyczące wystąpienia zarządzanego platformy Azure dla programu Apache Cassandra z Azure Portal
description: Często zadawane pytania dotyczące wystąpienia zarządzanego platformy Azure dla oprogramowania Apache Cassandra. W tym artykule przedstawiono pytania dotyczące sytuacji, w których należy używać wystąpień zarządzanych, korzyści, limitów przepływności, obsługiwanych regionów i innych szczegółów konfiguracji.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 1ba2b7d648c86912118b83a566bf2eb0800baee2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748696"
---
# <a name="frequently-asked-questions-about-azure-managed-instance-for-apache-cassandra-preview"></a>Często zadawane pytania dotyczące wystąpienia zarządzanego platformy Azure dla oprogramowania Apache Cassandra (wersja zapoznawcza)

W tym artykule opisano często zadawane pytania dotyczące wystąpienia zarządzanego platformy Azure dla oprogramowania Apache Cassandra. Dowiesz się, kiedy używać wystąpień zarządzanych, ich korzyści, limitów przepływności, obsługiwanych regionów i ich szczegółów konfiguracji.

> [!IMPORTANT]
> Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="general-faq"></a>Ogólne często zadawane pytania

### <a name="what-are-the-benefits-azure-managed-instance-for-apache-cassandra"></a>Jakie są wystąpienia zarządzane przez platformę Azure dla platformy Apache Cassandra?

Baza danych Apache Cassandra jest właściwym wyborem, gdy potrzebna jest skalowalność i wysoka dostępność bez kompromisu wydajności. Jest to świetna platforma dla danych o kluczowym znaczeniu z powodu skalowalności liniowej i sprawdzonej odporności na uszkodzenia sprzętu lub infrastruktury chmurowej. Wystąpienie zarządzane Azure dla platformy Apache Cassandra to usługa służąca do zarządzania wystąpieniami centrów danych typu "open source" wdrożonych na platformie Azure.

Może być używany w całości w chmurze lub w ramach hybrydowego klastra chmurowego i lokalnego. Ta usługa jest doskonałym wyborem, gdy potrzebna jest szczegółowa konfiguracja i kontrola w programie Apache Cassandra typu open source, bez konieczności konserwacji.

### <a name="why-should-i-use-this-service-instead-of-azure-cosmos-db-cassandra-api"></a>Dlaczego należy używać tej usługi zamiast Azure Cosmos DB interfejs API Cassandra?

Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra jest dostarczane przez zespół Azure Cosmos DB. Jest to autonomiczna zarządzana usługa służąca do wdrażania, konserwowania i skalowania danych typu "open source" platformy Apache Cassandra. [Azure Cosmos DB interfejs API Cassandra](../cosmos-db/cassandra-introduction.md) z drugiej strony jest platformą jako usługą, która zapewnia warstwę współdziałania z protokołem sieci Apache Cassandra. Jeśli oczekujesz, że platforma ma działać w taki sam sposób jak dowolny Klaster Apache Cassandra, należy wybrać usługę wystąpienia zarządzanego. Aby dowiedzieć się więcej, zobacz artykuł [Azure Managed instance for Apache Cassandra Vs Azure Cosmos DB interfejs API Cassandra](compare-cosmosdb-managed-instance.md) .

### <a name="is-azure-managed-instance-for-apache-cassandra-dependent-on-azure-cosmos-db"></a>Czy wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra jest zależne od Azure Cosmos DB?

Nie, nie istnieje zależność architektury między wystąpieniem zarządzanym platformy Azure dla oprogramowania Apache Cassandra i zapleczem Azure Cosmos DB. 

#### <a name="can-i-deploy-azure-managed-instance-for-apache-cassandra-in-any-region"></a>Czy można wdrożyć wystąpienie zarządzane platformy Azure dla oprogramowania Apache Cassandra w dowolnym regionie?

Wystąpienie zarządzane będzie dostępne w ograniczonej liczbie regionów w trakcie okresu zapoznawczego.

### <a name="what-are-the-storage-and-throughput-limits-of-azure-managed-instance-for-apache-cassandra"></a>Jakie są limity magazynu i przepływności wystąpienia zarządzanego platformy Azure dla oprogramowania Apache Cassandra?

Te limity zależą od wybranych jednostek SKU maszyn wirtualnych.

### <a name="what-is-the-cost-of-azure-managed-instance-for-apache-cassandra"></a>Jaki jest koszt wystąpienia zarządzanego na platformie Azure dla platformy Apache Cassandra?

Opłaty za wystąpienie zarządzane są zależne od kosztu maszyny wirtualnej z niewielkim znacznikiem. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/managed-instance-apache-cassandra/).

### <a name="can-i-use-yaml-file-settings-to-configure-behavior"></a>Czy mogę użyć ustawień pliku YAML, aby skonfigurować zachowanie?

Tak. w ramach wdrożenia szablonu Azure Resource Manager można osadzić konfiguracje plików YAML.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Jak mogę monitorować infrastrukturę wraz z przepływem?

Serwer [Prometheus](https://prometheus.io/docs/introduction/overview/) jest hostowany do monitorowania aktywności w klastrze i uwidacznia punkt końcowy. Zapewnia to 10 minut lub 10 GB danych (w zależności od progu zostanie osiągnięty pierwszy). Aby można było korzystać z tego monitorowania, należy skonfigurować [Federacji](https://prometheus.io/docs/prometheus/latest/federation/) i odpowiednie narzędzie pulpitu nawigacyjnego, takie jak Grafana.

### <a name="does-azure-managed-instance-for-apache-cassandra-provide-full-backups"></a>Czy wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra zapewnia pełne kopie zapasowe?

Tak, zapewnia pełne kopie zapasowe w usłudze Azure Storage i przywraca nowy klaster

### <a name="how-can-i-migrate-data-from-my-existing-apache-cassandra-cluster-to-azure-managed-instance-for-apache-cassandra"></a>Jak przeprowadzić migrację danych z istniejącego klastra Apache Cassandra do wystąpienia zarządzanego platformy Azure dla systemu Apache Cassandra?

Wystąpienie zarządzane platformy Azure dla usługi Apache Cassandra obsługuje wszystkie funkcje w programie Apache Cassandra w celu replikowania i przesyłania strumieniowego danych między centrami danych.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-azure-managed-instance-for-apache-cassandra"></a>Czy można sparować lokalny Klaster Apache Cassandra z wystąpieniem zarządzanym platformy Azure dla systemu Apache Cassandra?

Tak, można skonfigurować klaster hybrydowy przy użyciu platformy Azure Virtual Network wstrzykiwanych centrów danych wdrożonych przez usługę. Dane wystąpienia zarządzanego — centra mogą komunikować się z lokalnymi centrami danych w ramach tego samego pierścienia klastra.

### <a name="where-can-i-give-feedback-on-azure-managed-instance-for-apache-cassandra-features"></a>Gdzie mogę przekazać opinię na temat wystąpienia zarządzanego na platformie Azure dla funkcji Apache Cassandra?

Prześlij opinię za pomocą [opinii głosowych użytkownika](https://feedback.azure.com/forums/263030-azure-cosmos-db?category_id=398548) przy użyciu kategorii "Managed Apache Cassandra".

Aby rozwiązać problem z Twoim kontem, wyślij [żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure Portal.

## <a name="deployment-specific-faq"></a>Często zadawane pytania dotyczące wdrożenia

### <a name="will-the-managed-instance-support-node-addition-cluster-status-and-node-status-commands"></a>Czy zarządzane wystąpienie będzie obsługiwać Dodawanie węzła, stan klastra i polecenia stanu węzła?

Wszystkie polecenia Nodetool *tylko do odczytu* , takie jak `status` są dostępne za pomocą interfejsu wiersza polecenia platformy Azure. Jednak operacje takie jak *dodanie węzła* nie są dostępne, ponieważ zarządzamy kondycją węzłów w zarządzanym wystąpieniu. W trybie hybrydowym można nawiązać połączenie z klastrem za pomocą *Nodetool*. Jednak korzystanie z Nodetool nie jest zalecane, ponieważ może to spowodować jego stabilizację. Może również unieważnić wszystkie umowy SLA dotyczące pomocy technicznej dotyczące kondycji centrów danych wystąpienia zarządzanego w klastrze.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>Co się dzieje z różnymi ustawieniami metadanych tabeli?

Ustawienia metadanych tabeli, takie jak filtr rozwinięty, buforowanie, szansa naprawy odczytu, gc_grace i kompresja memtable_flush_period są w pełni obsługiwane, tak samo jak w przypadku dowolnego środowiska Apache Cassandra.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat często zadawanych pytań w innych interfejsach API, zobacz:

* [Utwórz klaster wystąpienia zarządzanego na podstawie Azure Portal](create-cluster-portal.md)
* [Wdróż zarządzany Klaster Apache Spark z Azure Databricks](deploy-cluster-databricks.md)
* [Zarządzanie wystąpieniem zarządzanym platformy Azure dla zasobów Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure](manage-resources-cli.md)