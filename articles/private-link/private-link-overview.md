---
title: Co to jest łącze prywatne platformy Azure?
description: Omówienie funkcji łączy prywatnych platformy Azure, architektury i implementacji. Dowiedz się, jak działają prywatne punkty końcowe platformy Azure oraz usługa Azure Private link i jak z nich korzystać.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 09/03/2020
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 3c03a207e91ae969836e208f4e6bf4ea32086c7d
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121326"
---
# <a name="what-is-azure-private-link"></a>Co to jest łącze prywatne platformy Azure? 
Link prywatny platformy Azure umożliwia dostęp do usług Azure PaaS Services (na przykład Azure Storage i SQL Database) oraz hostowanych usług partnerów/partnerskich platformy Azure w ramach [prywatnego punktu końcowego](private-endpoint-overview.md) w sieci wirtualnej.

Ruch między siecią wirtualną a usługą porusza się w sieci szkieletowej firmy Microsoft. Ujawnienie usługi do publicznej sieci Internet nie jest już konieczne. Możesz utworzyć własną [prywatną usługę linku](private-link-service-overview.md) w sieci wirtualnej i dostarczyć jej klientom. Konfiguracja i użycie przy użyciu prywatnego linku platformy Azure jest spójne w ramach usług Azure PaaS, należących do klienta i współużytkowanych partnerów.

> [!IMPORTANT]
> Usługa Azure Private link jest teraz ogólnie dostępna. Ogólnie dostępne są zarówno prywatne, jak i prywatne usługi linkowe (usługa za usługę w warstwie Standardowa). Różne harmonogramy zostaną dołączone do prywatnego linku platformy Azure PaaS. Sprawdź sekcję [dostępność](#availability) w tym artykule, aby uzyskać dokładny stan usługi Azure PaaS on Private link. Aby uzyskać znane ograniczenia, zobacz [prywatny punkt końcowy](private-endpoint-overview.md#limitations) i [Usługa łącza prywatnego](private-link-service-overview.md#limitations). 

:::image type="content" source="./media/private-link-overview/private-link-center.png" alt-text="Prywatne centrum połączenia platformy Azure w Azure Portal" border="false":::

## <a name="key-benefits"></a>Najważniejsze korzyści
Połączenie prywatne platformy Azure zapewnia następujące korzyści:  
- **Usługi dostępu prywatnego na platformie Azure**: łączenie sieci wirtualnej z usługami na platformie Azure bez publicznego adresu IP w źródle lub miejscu docelowym. Dostawcy usług mogą renderować swoje usługi w swoich własnych sieciach wirtualnych, a konsumenci mogą uzyskiwać dostęp do tych usług w lokalnej sieci wirtualnej. Platforma linków prywatnych będzie obsługiwała łączność między klientem i usługami za pośrednictwem sieci szkieletowej platformy Azure. 
 
- **Sieci lokalne i równorzędne**: usługi dostępu uruchomione na platformie Azure pochodzą z zasobów lokalnych za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute, tuneli VPN i równorzędnych sieci wirtualnych za pomocą prywatnych punktów końcowych. Nie ma potrzeby konfigurowania ExpressRoute komunikacji równorzędnej firmy Microsoft lub przechodzenia przez Internet w celu uzyskania dostępu do usługi. Link prywatny umożliwia bezpieczną metodę migrowania obciążeń do platformy Azure.
 
- **Ochrona przed wyciekiem danych**: prywatny punkt końcowy jest mapowany do wystąpienia zasobu PaaS zamiast całej usługi. Konsumenci mogą łączyć się tylko z określonym zasobem. Dostęp do dowolnego innego zasobu w usłudze jest zablokowany. Mechanizm ten zapewnia ochronę przed ryzykiem wycieku danych. 
 
- **Globalne zasięg**: Połącz się prywatnie z usługami działającymi w innych regionach. Sieć wirtualna konsumenta może znajdować się w regionie A i może łączyć się z usługami za linkiem prywatnym w regionie B.  
 
- Zapoznaj **się z własnymi usługami**: Włącz te same czynności i funkcje, aby umożliwić prywatną pracę usługi dla klientów na platformie Azure. Przez umieszczenie usługi za standardową Azure Load Balancer można ją włączyć dla linku prywatnego. Konsument może następnie połączyć się bezpośrednio z usługą przy użyciu prywatnego punktu końcowego we własnej sieci wirtualnej. Żądania połączenia można zarządzać przy użyciu przepływu wywołań zatwierdzenia. Połączenie prywatne platformy Azure działa dla klientów i usług należących do różnych dzierżawców Azure Active Directory. 

## <a name="availability"></a>Dostępność 
 Poniższa tabela zawiera listę prywatnych usług linków oraz regionów, w których są one dostępne. 

|Obsługiwane usługi  |Dostępne regiony | Dodatkowe zagadnienia | Stan  |
|:-------------------|:-----------------|:----------------|:--------|
|Usługi linków prywatnych za standardową Azure Load Balancer | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe<br/>Wszystkie regiony Chin  | Obsługiwane na usługa Load Balancer w warstwie Standardowa | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć usługę łącza prywatnego.](create-private-link-service-portal.md) |
| Magazyn obiektów blob platformy Azure (w tym Data Lake Storage Gen2)       |  Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe       |  Obsługiwane dla rodzaju konta Ogólnego przeznaczenia v2 | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla magazynu obiektów BLOB.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Files | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe      | |   Ogólna dostępność <br/> [Dowiedz się, jak utworzyć Azure Files punkty końcowe sieci.](../storage/files/storage-files-networking-endpoints.md)   |
| Azure File Sync | Wszystkie regiony publiczne      | |   Ogólna dostępność <br/> [Dowiedz się, jak utworzyć Azure Files punkty końcowe sieci.](../storage/files/storage-sync-files-networking-endpoints.md)   |
| Azure Queue Storage       |  Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe       |  Obsługiwane dla rodzaju konta Ogólnego przeznaczenia v2 | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla magazynu kolejek.](tutorial-private-endpoint-storage-portal.md) |
| Usługa Azure Table Storage       |  Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe       |  Obsługiwane dla rodzaju konta Ogólnego przeznaczenia v2 | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Table Storage.](tutorial-private-endpoint-storage-portal.md)  |
|  Azure SQL Database         | Wszystkie regiony publiczne <br/> Wszystkie regiony rządowe<br/>Wszystkie regiony Chin      |  Obsługiwane dla [zasad połączenia](../azure-sql/database/connectivity-architecture.md#connection-policy) serwera proxy | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Azure SQL](create-private-endpoint-portal.md)      |
|Azure Synapse Analytics| Wszystkie regiony publiczne <br/> Wszystkie regiony rządowe |  Obsługiwane dla [zasad połączenia](../azure-sql/database/connectivity-architecture.md#connection-policy) serwera proxy |Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Azure Synapse Analytics.](../azure-sql/database/private-endpoint-overview.md)|
|Azure Cosmos DB|  Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe</br> Wszystkie regiony Chin | |Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Cosmos DB.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL — pojedynczy serwer         | Wszystkie regiony publiczne <br/> Wszystkie regiony rządowe<br/>Wszystkie regiony Chin     | Obsługiwane w przypadku warstw cenowych Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Database for PostgreSQL.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe<br/>Wszystkie regiony Chin      |  | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Database for MySQL.](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe<br/>Wszystkie regiony Chin     |  | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Database for MariaDB.](../mariadb/concepts-data-access-security-private-link.md)      |
|  Azure Key Vault         | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe      |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Key Vault.](../key-vault/general/private-link-service.md)   |
|Usługa Azure Kubernetes Service — interfejs API Kubernetes | Wszystkie regiony publiczne      |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Azure Kubernetes.](../aks/private-clusters.md)   |
|Azure Search | Wszystkie regiony publiczne <br/> Wszystkie regiony rządowe | Obsługiwane z usługą w trybie prywatnym | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Search.](../search/service-create-private-endpoint.md)    |
|Azure Container Registry | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe    | Obsługiwane w przypadku warstwy Premium rejestru kontenerów. [Wybierz dla warstw](../container-registry/container-registry-skus.md)| Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Container Registry.](../container-registry/container-registry-private-link.md)   |
|Azure App Configuration | Wszystkie regiony publiczne      |  | Wersja zapoznawcza  </br> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla konfiguracji aplikacji platformy Azure](../azure-app-configuration/concept-private-endpoint.md) |
|Azure Backup | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe   |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Backup.](../backup/private-endpoints.md)   |
|Centrum zdarzeń Azure | Wszystkie regiony publiczne<br/>Wszystkie regiony rządowe      |   | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Azure Event Hub.](../event-hubs/private-link-service.md)  |
|Usługa Azure Service Bus | Cały region publiczny<br/>Wszystkie regiony rządowe  | Obsługiwane w warstwie Premium Azure Service Bus. [Wybierz dla warstw](../service-bus-messaging/service-bus-premium-messaging.md) | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Service Bus.](../service-bus-messaging/private-link-service.md)    |
|Azure Relay | Wszystkie regiony publiczne      |  | Wersja zapoznawcza <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Relay.](../azure-relay/private-link-service.md)  |
|Azure Event Grid| Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe       |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Event Grid.](../event-grid/network-security.md) |
|Aplikacje internetowe platformy Azure | Wszystkie regiony publiczne      | Obsługiwane z planem Premium PremiumV2, PremiumV3 lub Function  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Web Apps platformy Azure.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Machine Learning | Wszystkie regiony publiczne    |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Machine Learning.](../machine-learning/how-to-configure-private-link.md)   |
| Usługa Azure Automation  | Wszystkie regiony publiczne |  | Wersja zapoznawcza </br> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Automation.](../automation/how-to/private-link-security.md)| |
| Azure IoT Hub | Wszystkie regiony publiczne    |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla IoT Hub platformy Azure.](../iot-hub/virtual-network-support.md) |
| Azure SignalR | WSCHODNIE STANY USA, POŁUDNIOWO-ŚRODKOWE STANY USA,<br/>ZACHODNIe stany USA 2, wszystkie regiony Chin      |  | Wersja zapoznawcza   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla usługi Azure Signal.](../azure-signalr/howto-private-endpoints.md)   |
| Azure Monitor <br/>(Log Analytics & Application Insights) | Wszystkie regiony publiczne      |  | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Monitor.](../azure-monitor/platform/private-link-security.md)   | 
| Usługa Azure Batch | Wszystkie regiony publiczne z wyjątkiem: Niemcy środkowe, Niemcy PÓŁNOCno-Wschodnie <br/> Wszystkie regiony rządowe  | | Ogólna dostępność <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Batch.](../batch/private-connectivity.md) |
|Azure Data Factory | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe<br/>Wszystkie regiony Chin    | Poświadczenia muszą być przechowywane w magazynie kluczy platformy Azure| Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Azure Data Factory.](../data-factory/data-factory-private-link.md)   |
|Dyski zarządzane platformy Azure | Wszystkie regiony publiczne<br/> Wszystkie regiony rządowe<br/>Wszystkie regiony Chin    | [Kliknij tutaj, aby uzyskać znane ograniczenia](https://docs.microsoft.com/azure/virtual-machines/disks-enable-private-links-for-import-export-portal#limitations) | Ogólna dostępność   <br/> [Dowiedz się, jak utworzyć prywatny punkt końcowy dla Managed Disks platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/disks-enable-private-links-for-import-export-portal)   |



Aby zapoznać się z najbardziej aktualnymi powiadomieniami, sprawdź [stronę aktualizacje linku prywatnego platformy Azure](https://azure.microsoft.com/updates/?product=private-link).

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

Połączenie prywatne platformy Azure ma integrację z usługą Azure Monitor. Ta kombinacja umożliwia:

 - Archiwizowanie dzienników na koncie magazynu.
 - Przesyłanie strumieniowe zdarzeń do centrum zdarzeń.
 - Rejestrowanie usługi Azure Monitor

Na Azure Monitor można uzyskać dostęp do następujących informacji: 
- **Prywatny punkt końcowy**: 
    - Dane przetworzone przez prywatny punkt końcowy (WE/OUT)
 
- **Usługa link prywatny**:
    - Dane przetwarzane przez usługę linku prywatnego (WE/wychodzącym)
    - Dostępność portów NAT  
 
## <a name="pricing"></a>Cennik   
Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Często zadawane pytania  
Często zadawane pytania można znaleźć w sekcji [często zadawane pytania dotyczące linków prywatnych platformy Azure](private-link-faq.md).
 
## <a name="limits"></a>Limity  
Aby uzyskać limity, zobacz [limity linków prywatnych platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>Umowa dotycząca poziomu usług
W przypadku umowy SLA Zobacz temat [Umowa SLA dla prywatnego linku platformy Azure](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Następne kroki

- [Szybki Start: Tworzenie prywatnego punktu końcowego przy użyciu Azure Portal](create-private-endpoint-portal.md)
- [Szybki Start: Tworzenie usługi linku prywatnego przy użyciu Azure Portal](create-private-link-service-portal.md)


