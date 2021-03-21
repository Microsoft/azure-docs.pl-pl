---
title: Dostępność linku prywatnego platformy Azure
description: W tym artykule dowiesz się, które usługi platformy Azure obsługują link prywatny.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 3/15/2021
ms.custom: template-concept,references_regions
ms.openlocfilehash: 26485c84749b7d4c91159476b3f683c2b0f3831b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555680"
---
# <a name="azure-private-link-availability"></a>Dostępność linku prywatnego platformy Azure

Link prywatny platformy Azure umożliwia dostęp do usług Azure PaaS Services (na przykład Azure Storage i SQL Database) oraz hostowanych usług partnerów/partnerskich platformy Azure w ramach [prywatnego punktu końcowego](private-endpoint-overview.md) w sieci wirtualnej. 

> [!IMPORTANT]
> Usługa Azure Private link jest teraz ogólnie dostępna. Ogólnie dostępne są zarówno prywatne, jak i prywatne usługi linkowe (usługa za usługę w warstwie Standardowa). Różne harmonogramy zostaną dołączone do prywatnego linku platformy Azure PaaS. Aby uzyskać znane ograniczenia, zobacz [prywatny punkt końcowy](private-endpoint-overview.md#limitations) i [Usługa łącza prywatnego](private-link-service-overview.md#limitations). 

## <a name="service-availability"></a>Dostępność usługi

W poniższej tabeli wymieniono usługi linków prywatnych i regiony, w których są one dostępne. 

### <a name="ai--machine-learning"></a>SI i uczenie maszynowe

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Machine Learning | Wszystkie regiony publiczne    |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Machine Learning.](../machine-learning/how-to-configure-private-link.md)   |

### <a name="analytics"></a>Analiza

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Synapse Analytics| Wszystkie regiony publiczne <br/> Wszystkie regiony rządowe |  Obsługiwane dla [zasad połączenia](../azure-sql/database/connectivity-architecture.md#connection-policy) serwera proxy |Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Azure Synapse Analytics.](../azure-sql/database/private-endpoint-overview.md)|
|Centrum zdarzeń Azure | Wszystkie regiony publiczne<br/>Wszystkie regiony rządowe      |   | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Azure Event Hub.](../event-hubs/private-link-service.md)  |
| Azure Monitor <br/>(Log Analytics & Application Insights) | Wszystkie regiony publiczne      |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Monitor.](../azure-monitor/logs/private-link-security.md)   |
|Azure Data Factory | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe<br/>Wszystkie regiony Chin    | Poświadczenia muszą być przechowywane w magazynie kluczy platformy Azure| Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Data Factory.](../data-factory/data-factory-private-link.md)   |

### <a name="compute"></a>Compute

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure App Configuration | Wszystkie regiony publiczne      |  | Wersja zapoznawcza  </br> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla konfiguracji aplikacji platformy Azure](../azure-app-configuration/concept-private-endpoint.md) |
|Dyski zarządzane przez platformę Azure | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe<br/>Wszystkie regiony Chin    | [Wybierz, aby uzyskać znane ograniczenia](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Managed Disks platformy Azure.](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |

### <a name="containers"></a>Kontenery

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Container Registry | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe    | Obsługiwane w przypadku warstwy Premium rejestru kontenerów. [Wybierz dla warstw](../container-registry/container-registry-skus.md)| Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Container Registry.](../container-registry/container-registry-private-link.md)   |
|Usługa Azure Kubernetes Service — interfejs API Kubernetes | Wszystkie regiony publiczne      |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Azure Kubernetes.](../aks/private-clusters.md)   |

### <a name="databases"></a>Bazy danych

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure SQL Database         | Wszystkie regiony publiczne <br/> Wszystkie regiony rządowe<br/>Wszystkie regiony Chin      |  Obsługiwane dla [zasad połączenia](../azure-sql/database/connectivity-architecture.md#connection-policy) serwera proxy | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Azure SQL](create-private-endpoint-portal.md)      |
|Azure Cosmos DB|  Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe</br> Wszystkie regiony Chin | |Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Cosmos DB.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL — pojedynczy serwer         | Wszystkie regiony publiczne <br/> Wszystkie regiony rządowe<br/>Wszystkie regiony Chin     | Obsługiwane w przypadku warstw cenowych Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Database for PostgreSQL.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe<br/>Wszystkie regiony Chin      |  | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Database for MySQL.](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe<br/>Wszystkie regiony Chin     |  | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Database for MariaDB.](../mariadb/concepts-data-access-security-private-link.md)      |

### <a name="integration"></a>Integracja

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Event Grid| Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe       |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Event Grid.](../event-grid/network-security.md) |
|Usługa Azure Service Bus | Cały region publiczny<br/>Wszystkie regiony rządowe  | Obsługiwane w warstwie Premium Azure Service Bus. [Wybierz dla warstw](../service-bus-messaging/service-bus-premium-messaging.md) | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Service Bus.](../service-bus-messaging/private-link-service.md)    |

### <a name="internet-of-things-iot"></a>Internet rzeczy (IoT)

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure IoT Hub | Wszystkie regiony publiczne    |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla IoT Hub platformy Azure.](../iot-hub/virtual-network-support.md) |
|  Azure Digital Twins         | Wszystkie regiony publiczne obsługiwane przez usługę Azure Digital bliźniaczych reprezentacji     |  | Wersja zapoznawcza <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Azure Digital bliźniaczych reprezentacji.](../digital-twins/how-to-enable-private-link-portal.md)      |

### <a name="management-and-governance"></a>Zarządzanie + ład

| Obsługiwane usługi | Dostępne regiony | Inne zagadnienia | Stan  |
| ------------ | ----------------| ------------| ----------------|
| Azure Automation  | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe |  | Wersja zapoznawcza </br> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Automation.](../automation/how-to/private-link-security.md)|
|Azure Backup | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe   |  | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Backup.](../backup/private-endpoints.md)   |

### <a name="security"></a>Zabezpieczenia

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure Key Vault         | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe      |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Key Vault.](../key-vault/general/private-link-service.md)   |

### <a name="storage"></a>Storage
|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
| Magazyn obiektów blob platformy Azure (w tym Data Lake Storage Gen2)       |  Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe       |  Obsługiwane dla rodzaju konta Ogólnego przeznaczenia v2 | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla magazynu obiektów BLOB.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Files | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe      | |   Ogólna dostępność <br/> [Dowiedz się, jak utworzyć Azure Files punkty końcowe sieci.](../storage/files/storage-files-networking-endpoints.md)   |
| Azure File Sync | Wszystkie regiony publiczne      | |   Ogólna dostępność <br/> [Dowiedz się, jak utworzyć Azure Files punkty końcowe sieci.](../storage/files/storage-sync-files-networking-endpoints.md)   |
| Azure Queue Storage       |  Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe       |  Obsługiwane dla rodzaju konta Ogólnego przeznaczenia v2 | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla magazynu kolejek.](tutorial-private-endpoint-storage-portal.md) |
| Usługa Azure Table Storage       |  Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe       |  Obsługiwane dla rodzaju konta Ogólnego przeznaczenia v2 | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Table Storage.](tutorial-private-endpoint-storage-portal.md)  |
| Usługa Azure Batch | Wszystkie regiony publiczne z wyjątkiem: Niemcy środkowe, Niemcy PÓŁNOCno-Wschodnie <br/> Wszystkie regiony rządowe  | | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Batch.](../batch/private-connectivity.md) |

### <a name="web"></a>Internet
|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure SignalR | WSCHODNIE STANY USA, POŁUDNIOWO-ŚRODKOWE STANY USA,<br/>ZACHODNIe stany USA 2, wszystkie regiony Chin      |  | Wersja zapoznawcza   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Azure Signal.](../azure-signalr/howto-private-endpoints.md)   |
|Aplikacje internetowe platformy Azure | Wszystkie regiony publiczne<br/> Chiny Północne 2 & wschód 2    | Obsługiwane z planem Premium PremiumV2, PremiumV3 lub Function  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Web Apps platformy Azure.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Search | Wszystkie regiony publiczne <br/> Wszystkie regiony rządowe | Obsługiwane z usługą w trybie prywatnym | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Search.](../search/service-create-private-endpoint.md)    |
|Azure Relay | Wszystkie regiony publiczne      |  | Wersja zapoznawcza <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Relay.](../azure-relay/private-link-service.md)  |

### <a name="private-link-service-with-a-standard-load-balancer"></a>Usługa linków prywatnych z użyciem standardowego modułu równoważenia obciążenia

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
|Usługi linków prywatnych za standardową Azure Load Balancer | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe<br/>Wszystkie regiony Chin  | Obsługiwane na usługa Load Balancer w warstwie Standardowa | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć usługę łącza prywatnego.](create-private-link-service-portal.md) |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usłudze Azure Private Link Service:
- [Co to jest łącze prywatne platformy Azure?](private-link-overview.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu witryny Azure Portal](create-private-endpoint-portal.md)
