---
title: Azure Private Link dostępności
description: Z tego artykułu dowiesz się, które usługi platformy Azure obsługują Private Link.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 3/15/2021
ms.custom: template-concept,references_regions
ms.openlocfilehash: 866eb9feb152c0094cd5281fe4820ccc4589386f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778323"
---
# <a name="azure-private-link-availability"></a>Azure Private Link dostępności

Azure Private Link umożliwia dostęp do usług Azure PaaS (na przykład Azure Storage i SQL Database) oraz usług partnerów/klientów hostowanych na platformie Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. [](private-endpoint-overview.md) 

> [!IMPORTANT]
> Azure Private Link jest teraz ogólnie dostępna. Prywatny punkt końcowy i Private Link (usługa za standardowym równoważeniem obciążenia) są ogólnie dostępne. Różne usługi PaaS platformy Azure będą dołączane do Azure Private Link w różnych harmonogramach. Aby uzyskać znane ograniczenia, zobacz [Prywatny punkt końcowy](private-endpoint-overview.md#limitations) i usługa Private Link [Service.](private-link-service-overview.md#limitations) 

## <a name="service-availability"></a>Dostępność usługi

W poniższych tabelach przedstawiono Private Link usług i regionów, w których są dostępne. 

### <a name="ai--machine-learning"></a>SI i uczenie maszynowe

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Machine Learning | Wszystkie regiony publiczne    |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Machine Learning.](../machine-learning/how-to-configure-private-link.md)   |

### <a name="analytics"></a>Analiza

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Synapse Analytics| Wszystkie regiony publiczne <br/> Wszystkie regiony dla instytucji rządowych |  Obsługiwane w przypadku zasad [połączenia serwera proxy](../azure-sql/database/connectivity-architecture.md#connection-policy) |Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Synapse Analytics.](../azure-sql/database/private-endpoint-overview.md)|
|Centrum zdarzeń Azure | Wszystkie regiony publiczne<br/>Wszystkie regiony dla instytucji rządowych      |   | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Azure Event Hub.](../event-hubs/private-link-service.md)  |
| Azure Monitor <br/>(Log Analytics & Application Insights) | Wszystkie regiony publiczne      |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Monitor.](../azure-monitor/logs/private-link-security.md)   |
|Azure Data Factory | Wszystkie regiony publiczne<br/> Wszystkie regiony dla instytucji rządowych<br/>Wszystkie regiony w Chinach    | Poświadczenia muszą być przechowywane w usłudze Azure Key Vault| Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Data Factory.](../data-factory/data-factory-private-link.md)   |

### <a name="compute"></a>Compute

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure App Configuration | Wszystkie regiony publiczne      |  | Wersja zapoznawcza  </br> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure App Configuration](../azure-app-configuration/concept-private-endpoint.md) |
|Dyski zarządzane na platformie Azure | Wszystkie regiony publiczne<br/> Wszystkie regiony dla instytucji rządowych<br/>Wszystkie regiony w Chinach    | [Wybierz, aby uzyskać znane ograniczenia](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Azure Dyski zarządzane.](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |

### <a name="containers"></a>Kontenery

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Container Registry | Wszystkie regiony publiczne<br/> Wszystkie regiony dla instytucji rządowych    | Obsługiwane w warstwie Premium rejestru kontenerów. [Wybieranie dla warstw](../container-registry/container-registry-skus.md)| Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Container Registry.](../container-registry/container-registry-private-link.md)   |
|Azure Kubernetes Service — interfejs API platformy Kubernetes | Wszystkie regiony publiczne      |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Kubernetes Service.](../aks/private-clusters.md)   |

### <a name="databases"></a>Bazy danych

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure SQL Database         | Wszystkie regiony publiczne <br/> Wszystkie regiony dla instytucji rządowych<br/>Wszystkie regiony w Chinach      |  Obsługiwane w przypadku zasad [połączenia serwera proxy](../azure-sql/database/connectivity-architecture.md#connection-policy) | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure SQL](create-private-endpoint-portal.md)      |
|Azure Cosmos DB|  Wszystkie regiony publiczne<br/> Wszystkie regiony dla instytucji rządowych</br> Wszystkie regiony w Chinach | |Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Cosmos DB.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL — pojedynczy serwer         | Wszystkie regiony publiczne <br/> Wszystkie regiony dla instytucji rządowych<br/>Wszystkie regiony w Chinach     | Obsługiwane w przypadku Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Database for PostgreSQL.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | Wszystkie regiony publiczne<br/> Wszystkie regiony dla instytucji rządowych<br/>Wszystkie regiony w Chinach      |  | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Database for MySQL.](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | Wszystkie regiony publiczne<br/> Wszystkie regiony dla instytucji rządowych<br/>Wszystkie regiony w Chinach     |  | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Database for MariaDB.](../mariadb/concepts-data-access-security-private-link.md)      |

### <a name="integration"></a>Integracja

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Event Grid| Wszystkie regiony publiczne<br/> Wszystkie regiony dla instytucji rządowych       |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Event Grid.](../event-grid/network-security.md) |
|Usługa Azure Service Bus | Cały region publiczny<br/>Wszystkie regiony dla instytucji rządowych  | Obsługiwane w warstwie Premium usługi Azure Service Bus. [Wybieranie dla warstw](../service-bus-messaging/service-bus-premium-messaging.md) | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Service Bus.](../service-bus-messaging/private-link-service.md)    |

### <a name="internet-of-things-iot"></a>Internet rzeczy (IoT)

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure IoT Hub | Wszystkie regiony publiczne    |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure IoT Hub.](../iot-hub/virtual-network-support.md) |
|  Azure Digital Twins         | Wszystkie regiony publiczne obsługiwane przez Azure Digital Twins     |  | Wersja zapoznawcza <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Digital Twins.](../digital-twins/how-to-enable-private-link-portal.md)      |

### <a name="management-and-governance"></a>Zarządzanie + ład

| Obsługiwane usługi | Dostępne regiony | Inne zagadnienia | Stan  |
| ------------ | ----------------| ------------| ----------------|
| Azure Automation  | Wszystkie regiony publiczne<br/> Wszystkie regiony dla instytucji rządowych |  | Wersja zapoznawcza </br> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Automation.](../automation/how-to/private-link-security.md)|
|Azure Backup | Wszystkie regiony publiczne<br/> Wszystkie regiony dla instytucji rządowych   |  | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Backup.](../backup/private-endpoints.md)   |

### <a name="security"></a>Zabezpieczenia

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure Key Vault         | Wszystkie regiony publiczne<br/> Wszystkie regiony dla instytucji rządowych      |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Key Vault.](../key-vault/general/private-link-service.md)   |

### <a name="storage"></a>Storage
|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure Blob Storage (w tym Data Lake Storage Gen2)       |  Wszystkie regiony publiczne<br/> Wszystkie regiony dla instytucji rządowych       |  Obsługiwane w typie konta Ogólnego przeznaczenia wersji 2 | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla magazynu obiektów blob.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Files | Wszystkie regiony publiczne<br/> Wszystkie regiony dla instytucji rządowych      | |   Ogólna dostępność <br/> [Dowiedz się, jak tworzyć Azure Files sieci.](../storage/files/storage-files-networking-endpoints.md)   |
| Azure File Sync | Wszystkie regiony publiczne      | |   Ogólna dostępność <br/> [Dowiedz się, jak tworzyć Azure Files sieci.](../storage/file-sync/file-sync-networking-endpoints.md)   |
| Azure Queue Storage       |  Wszystkie regiony publiczne<br/> Wszystkie regiony dla instytucji rządowych       |  Obsługiwane w typie konta Ogólnego przeznaczenia wersji 2 | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla magazynu kolejek.](tutorial-private-endpoint-storage-portal.md) |
| Usługa Azure Table Storage       |  Wszystkie regiony publiczne<br/> Wszystkie regiony dla instytucji rządowych       |  Obsługiwane w typie konta Ogólnego przeznaczenia wersji 2 | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla magazynu tabel.](tutorial-private-endpoint-storage-portal.md)  |
| Usługa Azure Batch | Wszystkie regiony publiczne z wyjątkiem: Niemcy ŚRODKOWE, Niemcy PÓŁNOCNO-WSCHODNIE <br/> Wszystkie regiony dla instytucji rządowych  | | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Batch.](../batch/private-connectivity.md) |

### <a name="web"></a>Internet
|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure SignalR | WSCHODNIE USA, POŁUDNIOWO-ŚRODKOWE USA,<br/>ZACHODNIE STANY USA 2, Wszystkie regiony Chin      |  | Wersja zapoznawcza   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Azure SignalR.](../azure-signalr/howto-private-endpoints.md)   |
|Aplikacje internetowe platformy Azure | Wszystkie regiony publiczne<br/> Chiny Północne 2 & Wschodnie 2    | Obsługiwane w przypadku planu PremiumV2, PremiumV3 lub Function Premium  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Azure Web Apps.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Search | Wszystkie regiony publiczne <br/> Wszystkie regiony dla instytucji rządowych | Obsługiwane z usługą w trybie prywatnym | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Azure Search.](../search/service-create-private-endpoint.md)    |
|Azure Relay | Wszystkie regiony publiczne      |  | Wersja zapoznawcza <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Relay.](../azure-relay/private-link-service.md)  |

### <a name="private-link-service-with-a-standard-load-balancer"></a>Private Link usługi za pomocą standardowego usługi równoważenia obciążenia

|Obsługiwane usługi  |Dostępne regiony | Inne zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
|Private Link usługami w standardowych usługach Azure Load Balancer | Wszystkie regiony publiczne<br/> Wszystkie regiony dla instytucji rządowych<br/>Wszystkie regiony w Chinach  | Obsługiwane na usługa Load Balancer w warstwie Standardowa | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć usługę łącza prywatnego.](create-private-link-service-portal.md) |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Azure Private Link service:
- [Co to jest łącze prywatne platformy Azure?](private-link-overview.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu witryny Azure Portal](create-private-endpoint-portal.md)
