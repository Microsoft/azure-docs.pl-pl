---
title: Ograniczenia i limity subskrypcji platformy Azure
description: Zawiera listę typowych limitów subskrypcji i usług platformy Azure, przydziałów i ograniczeń. Ten artykuł zawiera informacje o sposobach zwiększania limitów oraz wartości maksymalnych.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 3ef3e6f3b67e8c92e0137f36e19c62cba79e1c14
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030829"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Limity subskrypcji i usług, limity przydziału oraz ograniczenia platformy Azure

Ten dokument zawiera listę najbardziej typowych limitów Microsoft Azure, które są również czasami nazywane przydziałami.

Aby dowiedzieć się więcej na temat cen platformy Azure, zobacz [Omówienie cen platformy Azure](https://azure.microsoft.com/pricing/). W tym miejscu możesz oszacować swoje koszty za pomocą [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/). Możesz również przejść do strony szczegóły cennika dla konkretnej usługi, na przykład [maszyn wirtualnych z systemem Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Aby uzyskać porady ułatwiające zarządzanie kosztami, zobacz [zapobieganie nieoczekiwanym kosztom w rozliczeniach i zarządzaniu kosztami platformy Azure](../../cost-management-billing/cost-management-billing-overview.md).

## <a name="managing-limits"></a>Zarządzanie limitami

> [!NOTE]
> Niektóre usługi mają regulowane limity.
>
> Jeśli usługa nie ma przystosowanych limitów, następujące tabele używają **limitu** nagłówka. W takich przypadkach domyślne i maksymalne limity są takie same.
>
> Gdy limit można dostosować, tabele zawierają **domyślny limit** i **maksymalny limit** nagłówków. Limit może być podniesiony powyżej limitu domyślnego, ale nie przekracza maksymalnego limitu.
>
> Jeśli chcesz podnieść limit lub przydział powyżej domyślnego limitu, [Otwórz żądanie obsługi klienta online bez dodatkowych opłat](../templates/error-resource-quota.md).
>
> Terminy *nietrwałe* i *sztywny limit* często są używane do opisywania bieżącego, regulowanego limitu (limit elastyczny) i maksymalnego limitu (sztywny limit). Jeśli limit nie jest regulowany, nie będzie można określić limitu, ale tylko na stałym limicie.
>

[Bezpłatna subskrypcja wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p) nie kwalifikuje się do zwiększenia limitu przydziału. Jeśli masz [bezpłatną subskrypcję wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p), możesz przeprowadzić uaktualnienie do subskrypcji [płatnej zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0003p/) użyciem. Aby uzyskać więcej informacji, zobacz [uaktualnianie subskrypcji bezpłatnej wersji próbnej platformy Azure do subskrypcji płatnej zgodnie z rzeczywistym](../../cost-management-billing/manage/upgrade-azure-subscription.md) użyciem i [subskrypcji bezpłatnej wersji próbnej](https://azure.microsoft.com/free/free-account-faq).

Niektóre ograniczenia są zarządzane na poziomie regionalnym.

Użyjmy przydziałów vCPU jako przykładu. Aby zażądać zwiększenia limitu przydziału z obsługą procesorów wirtualnych vCPU, należy określić liczbę procesorów wirtualnych vCPU, które mają być używane w regionach. Następnie należy zażądać zwiększenia limitów przydziału vCPU dla żądanych ilości i regionów. Jeśli konieczne jest użycie 30 procesorów wirtualnych vCPU w Europie Zachodniej, aby uruchomić aplikację, należy zażądać 30 procesorów wirtualnych vCPU w Europie Zachodniej. Limit przydziału vCPU nie został zwiększony w żadnym innym regionie — tylko Europa Zachodnia ma przydział 30-vCPU.

W związku z tym należy określić, jakie limity przydziału muszą być dla obciążenia w jednym regionie. Następnie Zażądaj tej kwoty w każdym regionie, w którym chcesz wdrożyć. Aby uzyskać pomoc w ustalaniu bieżących przydziałów dla określonych regionów, zobacz [Rozwiązywanie problemów dotyczących przydziałów zasobów](../templates/error-resource-quota.md).

## <a name="general-limits"></a>Ogólne limity

Aby uzyskać ograniczenia dotyczące nazw zasobów, zobacz [reguły nazewnictwa i ograniczenia dotyczące zasobów platformy Azure](resource-name-rules.md).

Aby uzyskać informacje na temat limitów odczytu i zapisu interfejsu API Menedżer zasobów, zobacz [ograniczanie Menedżer zasobów żądań](request-limits-and-throttling.md).

### <a name="management-group-limits"></a>Limity grupy zarządzania

Do [grup zarządzania](../../governance/management-groups/overview.md)mają zastosowanie następujące ograniczenia.

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Limity subskrypcji

W przypadku używania Azure Resource Manager i grup zasobów platformy Azure obowiązują następujące ograniczenia.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limity grupy zasobów

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Limity Active Directory

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>Limity API Management

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>Limity App Service

Poniższe App Service limity obejmują limity dla Web Apps, Mobile Apps i API Apps.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Limity automatyzacji

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-app-configuration"></a>Azure App Configuration

[!INCLUDE [app-configuration-limits](../../../includes/app-configuration-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Pamięć podręczna systemu Azure dla limitów Redis

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Limity Cloud Services platformy Azure

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Limity Wyszukiwanie poznawcze platformy Azure

Warstwy cenowe określają pojemność i limity usługi wyszukiwania. Warstwy obejmują:

* **Bezpłatna** usługa wielodostępna, współdzielona z innymi subskrybentami platformy Azure, jest przeznaczona do oceny i małych projektów deweloperskich.
* Funkcja **Basic** zapewnia dedykowane zasoby obliczeniowe dla obciążeń produkcyjnych w mniejszej skali, z maksymalnie trzema replikami dla obciążeń zapytań o wysokiej dostępności.
* **Standard**, który obejmuje wysoką gęstość S1, S2, S3 i S3, jest przeznaczony dla większych obciążeń produkcyjnych. W warstwie Standardowa istnieje wiele poziomów, dzięki czemu można wybrać konfigurację zasobów najlepiej zgodną z Twoim profilem obciążenia.

**Limity na subskrypcję**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Limity dla usługi wyszukiwania**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Aby dowiedzieć się więcej na temat limitów na bardziej szczegółowym poziomie, takich jak rozmiar dokumentu, zapytania na sekundę, klucze, żądania i odpowiedzi, zobacz [limity usługi w usłudze Azure wyszukiwanie poznawcze](../../search/search-limits-quotas-capacity.md).

## <a name="azure-cognitive-services-limits"></a>Limity Cognitive Services platformy Azure

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Limity Azure Cosmos DB

Aby uzyskać Azure Cosmos DB limitów, zobacz [limity w Azure Cosmos DB](../../cosmos-db/concepts-limits.md).

## <a name="azure-data-explorer-limits"></a>Limity Eksplorator danych platformy Azure

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Aby uzyskać Azure Database for MySQL limitów, zobacz [ograniczenia w Azure Database for MySQL](../../mysql/concepts-limits.md).

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

Aby uzyskać Azure Database for PostgreSQL limitów, zobacz [ograniczenia w Azure Database for PostgreSQL](../../postgresql/concepts-limits.md).

## <a name="azure-functions-limits"></a>Limity Azure Functions

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

Aby uzyskać więcej informacji, zobacz temat [funkcje — porównanie planów hostingu](../../azure-functions/functions-scale.md).

## <a name="azure-kubernetes-service-limits"></a>Limity usługi Azure Kubernetes

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Limity Azure Machine Learning

Najnowsze wartości przydziałów obliczeń Azure Machine Learning można znaleźć na [stronie przydziału Azure Machine Learning](../../machine-learning/how-to-manage-quotas.md)

## <a name="azure-maps-limits"></a>Limity Azure Maps

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Limity Azure Monitor

### <a name="alerts"></a>Alerty

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Grupy akcji

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="autoscale"></a>Automatyczne skalowanie

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-autoscale.md)]

### <a name="log-queries-and-language"></a>Rejestruj zapytania i język

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Obszary robocze usługi Log Analytics

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/application-insights-limits.md)]

## <a name="azure-policy-limits"></a>Limity Azure Policy

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-quantum-limits"></a>Limity Quantum platformy Azure

[!INCLUDE [quantum-limits](../../../includes/azure-quantum-limits.md)]

## <a name="azure-role-based-access-control-limits"></a>Limity kontroli dostępu opartej na rolach na platformie Azure

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control/limits.md)]

## <a name="azure-signalr-service-limits"></a>Limity usługi Azure sygnalizujące

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="azure-vmware-solution-limits"></a>Limity rozwiązań VMware platformy Azure

[!INCLUDE [azure-vmware-solutions-limits](../../azure-vmware/includes/azure-vmware-solutions-limits.md)]

## <a name="backup-limits"></a>Limity kopii zapasowych

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Limity partii

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Ograniczenia klasycznego modelu wdrażania

W przypadku korzystania z klasycznego modelu wdrażania zamiast Azure Resource Managergo modelu wdrażania obowiązują następujące ograniczenia.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Limity Container Instances

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Limity Container Registry

W poniższej tabeli przedstawiono funkcje i limity [warstw usług](../../container-registry/container-registry-skus.md)podstawowa, standardowa i Premium.

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Limity Content Delivery Network

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Limity Data Factory

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Limity Data Lake Analytics

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-storage-limits"></a>Limity Data Lake Storage

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Limity udziału danych

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Limity Database Migration Service

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="digital-twins-limits"></a>Limity bliźniaczych reprezentacji cyfrowych

> [!NOTE]
> Niektóre obszary tej usługi mają regulowane limity, a inne nie. Jest to reprezentowane w poniższych tabelach z kolumną z możliwością *dopasowywania?* Aby można było dostosować limit, wartość *ustawiana* to *tak*.

[!INCLUDE [digital-twins-limits](../../../includes/digital-twins-limits.md)]

## <a name="event-grid-limits"></a>Limity Event Grid

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Limity Event Hubs

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="iot-central-limits"></a>Limity IoT Central
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>Limity IoT Hub

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>Limity IoT Hub Device Provisioning Service

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Limity Key Vault

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="managed-identity-limits"></a>Zarządzane limity tożsamości

[!INCLUDE [Managed-Identity-Limits](../../../includes/managed-identity-limits.md)]


## <a name="media-services-limits"></a>Limity Media Services

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Media Services w wersji 2 (starsza wersja)

Aby uzyskać ograniczenia dotyczące Media Services V2 (starsza wersja), zobacz [Media Services V2 (starsza wersja)](../../media-services/previous/media-services-quotas-and-limitations.md)

## <a name="mobile-services-limits"></a>Limity Mobile Services

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Limity Multi-Factor Authentication

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Limity sieci

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>Limity ExpressRoute

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-network-gateway-limits"></a>Limity Virtual Network bramy

[!INCLUDE [virtual-network-gateway-limits](../../../includes/azure-virtual-network-gateway-limits.md)]

### <a name="nat-gateway-limits"></a>Limity bramy translatora adresów sieciowych

[!INCLUDE [nat-gateway-limits](../../../includes/azure-nat-gateway-limits.md)]

### <a name="virtual-wan-limits"></a>Limity wirtualnej sieci WAN

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Limity Application Gateway

W poniższej tabeli przedstawiono jednostki SKU V1, v2, standard i WAF, chyba że określono inaczej.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Limity Network Watcher

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Limity usługi Private Link

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

## <a name="purview-limits"></a>Limity kontrolą

Najnowsze wartości przydziałów usługi Azure kontrolą można znaleźć na [stronie limitu przydziału usługi Azure kontrolą](../../purview/how-to-manage-quotas.md)

### <a name="traffic-manager-limits"></a>Limity Traffic Manager

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Limity usługi Azure bastionu

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Limity Azure DNS

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Limity zapory platformy Azure

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Limity usługi Azure Front-drzwiczk

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Limity Notification Hubs

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="service-bus-limits"></a>Limity Service Bus

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Limity usługi Site Recovery

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>Limity SQL Database

Aby uzyskać SQL Database limitów, zobacz [SQL Database limitów zasobów dla pojedynczych baz danych](../../azure-sql/database/resource-limits-vcore-single-databases.md), [SQL Database limitów zasobów dla pul elastycznych i baz danych w puli](../../azure-sql/database/resource-limits-vcore-elastic-pools.md)oraz [limity zasobów SQL Database dla wystąpienia zarządzanego SQL](../../azure-sql/managed-instance/resource-limits.md).

## <a name="azure-synapse-analytics-limits"></a>Limity usługi Azure Synapse Analytics

Ograniczenia dotyczące usługi Azure Synapse Analytics można znaleźć w temacie [limity zasobów usługi Azure Synapse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="azure-files-and-azure-file-sync"></a>Azure Files i Azure File Sync
Aby dowiedzieć się więcej na temat limitów Azure Files i File Sync, zobacz [Azure Files cele dotyczące skalowalności i wydajności](../../storage/files/storage-files-scale-targets.md).

## <a name="storage-limits"></a>Limity magazynu

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Aby uzyskać więcej informacji na temat limitów dla kont magazynu w warstwie Standardowa, zobacz [elementy docelowe skalowalności dla kont magazynu w warstwie Standardowa](../../storage/common/scalability-targets-standard-account.md).

### <a name="storage-resource-provider-limits"></a>Limity dostawcy zasobów magazynu

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Limity magazynu obiektów blob platformy Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Limity magazynu kolejki platformy Azure

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Limity magazynu tabel platformy Azure

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Limity dysku maszyny wirtualnej

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="disk-encryption-sets"></a>Zestawy szyfrowania dysków

Istnieje ograniczenie 1000 zestawów szyfrowania dysków na region, na subskrypcję. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą szyfrowania dla maszyn wirtualnych z systemem [Linux](../../virtual-machines/disk-encryption.md#restrictions) lub [Windows](../../virtual-machines/disk-encryption.md#restrictions) . Jeśli musisz zwiększyć limit przydziału, skontaktuj się z pomocą techniczną platformy Azure.

### <a name="managed-virtual-machine-disks"></a>Zarządzane dyski maszyny wirtualnej

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Niezarządzane dyski maszyny wirtualnej

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>Limity systemu StorSimple

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Limity Stream Analytics

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Limity Virtual Machines

### <a name="virtual-machines-limits"></a>Limity Virtual Machines

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Limity Virtual Machines — Azure Resource Manager

W przypadku używania Azure Resource Manager i grup zasobów platformy Azure obowiązują następujące ograniczenia.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Limity galerii obrazów udostępnionych

Istnieją limity dla każdej subskrypcji dotyczące wdrażania zasobów przy użyciu udostępnionych galerii obrazów:

- 100 udostępnione Galerie obrazów na subskrypcję na region
- 1 000 definicji obrazów na subskrypcję na region
- 10 000 wersje obrazów na subskrypcję na region

## <a name="virtual-machine-scale-sets-limits"></a>Limity zestawów skalowania maszyn wirtualnych

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>Zobacz też

* [Informacje o limitach i ograniczeniach platformy Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Rozmiary maszyn wirtualnych i usług w chmurze dla platformy Azure](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Rozmiary dla Cloud Services platformy Azure](../../cloud-services/cloud-services-sizes-specs.md)
* [Reguły i ograniczenia nazewnictwa zasobów platformy Azure](resource-name-rules.md)
