---
title: Limity i przydziały subskrypcji platformy Azure
description: Zawiera listę typowych limitów subskrypcji i usług platformy Azure, przydziałów i ograniczeń. Ten artykuł zawiera informacje na temat zwiększania limitów wraz z wartościami maksymalnymi.
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 00bd85febe34a7bd9b4e993d5697fa810347711b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768731"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure subscription and service limits, quotas, and constraints (Limity subskrypcji i usług, limity przydziału oraz ograniczenia platformy Azure)

W tym dokumencie wymieniono niektóre z najczęstszych limitów platformy Microsoft Azure, które są również czasami nazywane przydziałami.

Aby dowiedzieć się więcej o cenach platformy Azure, zobacz [Omówienie cen platformy Azure](https://azure.microsoft.com/pricing/). Tam możesz oszacować swoje koszty za pomocą [kalkulatora cen.](https://azure.microsoft.com/pricing/calculator/) Można również przejść do strony szczegółów cennika dla konkretnej usługi, na przykład [maszyn wirtualnych z systemem Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Aby uzyskać wskazówki ułatwiające zarządzanie kosztami, zobacz [Zapobieganie nieoczekiwanym kosztom dzięki rozliczaniu i zarządzaniu kosztami platformy Azure.](../../billing/billing-getting-started.md)

## <a name="managing-limits"></a>Zarządzanie limitami

> [!NOTE]
> Niektóre usługi mają regulowane limity.
>
> Jeśli usługa nie ma regulowanych limitów, w poniższych tabelach używa się nagłówka **Limit**. W takich przypadkach domyślne i maksymalne limity są takie same.
>
> Gdy limit można dostosować, tabele zawierają **domyślne nagłówki limitu** i **maksymalnego limitu.** Limit może być podniesiony powyżej domyślnego limitu, ale nie powyżej maksymalnego limitu.
>
> Jeśli chcesz podnieść limit lub przydział powyżej domyślnego limitu, [otwórz żądanie obsługi klienta online bez żadnych opłat](../templates/error-resource-quota.md).

[Bezpłatne subskrypcje próbne](https://azure.microsoft.com/offers/ms-azr-0044p) nie kwalifikują się do zwiększenia limitu lub przydziału. Jeśli masz [bezpłatną subskrypcję próbną,](https://azure.microsoft.com/offers/ms-azr-0044p)możesz uaktualnić subskrypcję [płatności zgodnie z rzeczywistymu.](https://azure.microsoft.com/offers/ms-azr-0003p/) Aby uzyskać więcej informacji, zobacz [Uaktualnianie subskrypcji bezpłatnej wersji próbnej platformy Azure do subskrypcji płatności zgodnie z rzeczywistym przejściem](../../billing/billing-upgrade-azure-subscription.md) oraz [często zadawane pytania dotyczące subskrypcji bezpłatnej wersji próbnej.](https://azure.microsoft.com/free/free-account-faq)

Niektóre limity są zarządzane na szczeblu regionalnym.

Użyjmy na przykład przydziałów vCPU. Aby zażądać zwiększenia przydziału z obsługą procesorów wirtualnych, należy zdecydować, ile procesorów wirtualnych chcesz użyć w których regionach. Następnie należy wykonać określone żądanie dla przydziałów vCPU grupy zasobów platformy Azure dla żądanych kwot i regionów. Jeśli musisz użyć 30 procesorów wirtualnych w Europie Zachodniej, aby uruchomić tam aplikację, należy w szczególności zażądać 30 procesorów wirtualnych w Europie Zachodniej. Przydział procesora wirtualnego nie jest zwiększany w żadnym innym regionie — tylko Europa Zachodnia ma przydział 30 vCPU.

W rezultacie zdecyduj, jakie przydziały grupy zasobów platformy Azure muszą być dla obciążenia w dowolnym regionie. Następnie zażądaj tej kwoty w każdym regionie, w którym chcesz wdrożyć. Aby uzyskać pomoc dotyczącą określania bieżących przydziałów dla określonych regionów, zobacz [Rozwiązywanie błędów przydziałów zasobów](../templates/error-resource-quota.md).

## <a name="general-limits"></a>Ogólne limity

Aby uzyskać limity nazw zasobów, zobacz [Reguły nazewnictwa i ograniczenia dotyczące zasobów platformy Azure](resource-name-rules.md).

Aby uzyskać informacje na temat limitów odczytu i zapisu interfejsu API Menedżera zasobów, zobacz [Ograniczanie żądań Menedżera zasobów](request-limits-and-throttling.md).

### <a name="management-group-limits"></a>Limity grupy zarządzania

Następujące limity dotyczą [grup zarządzania](../../governance/management-groups/overview.md).

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Limity subskrypcji

Następujące limity mają zastosowanie podczas korzystania z usługi Azure Resource Manager i grup zasobów platformy Azure.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limity grup zasobów

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Limity usługi Active Directory

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>Limity zarządzania interfejsami API

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>Limity usługi aplikacji

Następujące limity usługi app service obejmują limity dotyczące aplikacji sieci Web, aplikacji mobilnych i aplikacji interfejsu API.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Limity automatyzacji

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Usługa Azure Cache dla limitów Redis

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Limity usług w chmurze platformy Azure

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Limity wyszukiwania funkcji Azure Cognitive Search

Warstwy cenowe określają pojemność i limity usługi wyszukiwania. Poziomy obejmują:

* **Bezpłatna** usługa wielodostępna, współużytkowana innym subskrybentom platformy Azure, jest przeznaczona do oceny i małych projektów deweloperskich.
* **Basic** zapewnia dedykowane zasoby obliczeniowe dla obciążeń produkcyjnych na mniejszą skalę, z maksymalnie trzema replikami dla obciążeń zapytań o wysokiej dostępności.
* **Standard**, który obejmuje S1, S2, S3 i S3 High Density, jest dla większych obciążeń produkcyjnych. W warstwie Standardowa istnieje wiele poziomów, dzięki czemu można wybrać konfigurację zasobów, która najlepiej odpowiada profilowi obciążenia.

**Limity na subskrypcję**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Limity na usługę wyszukiwania**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Aby dowiedzieć się więcej o limitach na bardziej szczegółowym poziomie, takich jak rozmiar dokumentu, zapytania na sekundę, klucze, żądania i odpowiedzi, zobacz [Limity usług w usłudze Azure Cognitive Search](../../search/search-limits-quotas-capacity.md).

## <a name="azure-cognitive-services-limits"></a>Limity usług Azure Cognitive Services

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Limity usługi Azure Cosmos DB

W przypadku limitów usługi Azure Cosmos DB zobacz [Limity w usłudze Azure Cosmos DB](../../cosmos-db/concepts-limits.md).

## <a name="azure-data-explorer-limits"></a>Limity Eksploratora danych platformy Azure

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

W przypadku usługi Azure Database dla limitów MySQL zobacz [Ograniczenia w usłudze Azure Database for MySQL](../../mysql/concepts-limits.md).

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

W przypadku usługi Azure Database dla limitów postgreSQL zobacz [Ograniczenia w bazie danych platformy Azure dla postgreSQL](../../postgresql/concepts-limits.md).

## <a name="azure-functions-limits"></a>Limity funkcji platformy Azure

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

## <a name="azure-kubernetes-service-limits"></a>Limity usługi Azure Kubernetes

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Limity usługi Azure Machine Learning

Najnowsze wartości przydziałów obliczeń usługi Azure Machine Learning można znaleźć na [stronie przydziału usługi Azure Machine Learning](../../machine-learning/how-to-manage-quotas.md)

## <a name="azure-maps-limits"></a>Limity usługi Azure Maps

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Limity usługi Azure Monitor

### <a name="alerts"></a>Alerty

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Grupy akcji

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="log-queries-and-language"></a>Rejestrowanie zapytań i języka

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Obszary robocze usługi Log Analytics

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Limity zasad platformy Azure

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-signalr-service-limits"></a>Limity usługi Azure SignalR

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>Limity kopii zapasowych

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Limity partii

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Klasyczne limity modeli wdrażania

Jeśli używasz klasycznego modelu wdrażania zamiast modelu wdrażania usługi Azure Resource Manager, obowiązują następujące limity.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Limity wystąpień kontenerów

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Limity rejestru kontenerów

W poniższej tabeli przedstawiono funkcje i limity [warstw usług](../../container-registry/container-registry-skus.md)Basic, Standard i Premium .

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Limity sieci dostarczania zawartości

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Limity fabryki danych

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Limity analizy usługi Data Lake Analytics

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-store-limits"></a>Limity data lake store

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Limity udziału danych

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Limity usług migracji bazy danych

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="event-grid-limits"></a>Limity siatki zdarzeń

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Limity centrów zdarzeń

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="identity-manager-limits"></a>Limity Menedżera tożsamości

[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

## <a name="iot-central-limits"></a>Centralne limity IoT
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>Limity koncentratora IoT

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>Limity usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Limity przechowalni kluczy

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="media-services-limits"></a>Limity usług multimedialnych

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Media Services w wersji 2 (starsza wersja)

Aby uzyskać limity specyficzne dla usługi Media Services w wersji 2 (starsza wersja), zobacz [Media Services w wersji 2 (starsza wersja)](https://docs.microsoft.com/azure/media-services/previous/media-services-quotas-and-limitations)

## <a name="mobile-services-limits"></a>Limity usług mobilnych

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Limity uwierzytelniania wieloskładnikowego

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Limity sieci

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>Limity usługi ExpressRoute

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-wan-limits"></a>Wirtualne limity sieci WAN

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Limity bramy aplikacji

Poniższa tabela dotyczy jednostek SKU w wersji 1, v2, standard i WAF, chyba że określono inaczej.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Limity obserwatora sieci

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Limity usługi Private Link

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

### <a name="traffic-manager-limits"></a>Limity menedżera ruchu

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Limity bastionu platformy Azure

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Limity DNS platformy Azure

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Limity zapory platformy Azure

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Limity usługi Azure Front Door Service

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Limity centrów powiadomień

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="role-based-access-control-limits"></a>Limity kontroli dostępu oparte na rolach

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control-limits.md)]

## <a name="service-bus-limits"></a>Limity usługi Service Bus

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Limity usługi Site Recovery

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>Limity bazy danych SQL

W przypadku limitów bazy danych SQL zobacz [Limity zasobów bazy danych SQL dla pojedynczych baz danych,](../../sql-database/sql-database-vcore-resource-limits-single-databases.md) [limity zasobów bazy danych SQL dla pul elastycznych i puli baz danych](../../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)oraz [limity zasobów bazy danych SQL dla wystąpień zarządzanych](../../sql-database/sql-database-managed-instance-resource-limits.md).

## <a name="sql-data-warehouse-limits"></a>Limity magazynu danych SQL

Aby zapoznać się z limitami magazynu danych SQL, zobacz [limity zasobów magazynu danych SQL](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="storage-limits"></a>Limity magazynowania

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Aby uzyskać więcej informacji na temat limitów dla standardowych kont magazynu, zobacz [Cele skalowalności dla standardowych kont magazynu](../../storage/common/scalability-targets-standard-account.md).

### <a name="storage-resource-provider-limits"></a>Limity dostawcy zasobów magazynu

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Limity magazynu obiektów Blob platformy Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-files-limits"></a>Limity plików platformy Azure

Aby uzyskać więcej informacji na temat limitów plików platformy Azure, zobacz [Cele skalowalności i wydajności usług Azure Files](../../storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-limits"></a>Limity synchronizacji plików platformy Azure

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Limity magazynu kolejki platformy Azure

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Limity magazynu tabel platformy Azure

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Limity dysków maszyny wirtualnej

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Aby uzyskać więcej informacji, zobacz [Rozmiary maszyn wirtualnych](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="managed-virtual-machine-disks"></a>Zarządzane dyski maszyn wirtualnych

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Niezarządzane dyski maszyn wirtualnych

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>StorSimple Limity systemu

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Limity analizy strumienia

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Limity maszyn wirtualnych

### <a name="virtual-machines-limits"></a>Limity maszyn wirtualnych

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Limity maszyn wirtualnych — Usługa Azure Resource Manager

Następujące limity mają zastosowanie podczas korzystania z usługi Azure Resource Manager i grup zasobów platformy Azure.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Limity galerii obrazów udostępnionych

Istnieją limity dla subskrypcji, do wdrażania zasobów przy użyciu galerii obrazów udostępnionych:

- 100 udostępnionych galerii zdjęć, na subskrypcję, na region
- 1000 definicji obrazów na subskrypcję, na region
- 10 000 wersji obrazów na subskrypcję dla regionu

## <a name="virtual-machine-scale-sets-limits"></a>Skala maszyny wirtualnej ustawia limity

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>Zobacz też

* [Opis limitów i podwyżek platformy Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Rozmiary maszyn wirtualnych i usług w chmurze dla platformy Azure](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Rozmiary usług w chmurze platformy Azure](../../cloud-services/cloud-services-sizes-specs.md)
* [Reguły i ograniczenia nazewnictwa zasobów platformy Azure](resource-name-rules.md)
