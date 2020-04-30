---
title: Co to jest łącze prywatne platformy Azure?
description: Omówienie funkcji łączy prywatnych platformy Azure, architektury i implementacji. Dowiedz się, jak działają prywatne punkty końcowe platformy Azure oraz usługa Azure Private link i jak z nich korzystać.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 02/27/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 1bef4e5f4129ddc8300d61d609392ce0b07b74b8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80656248"
---
# <a name="what-is-azure-private-link"></a>Co to jest łącze prywatne platformy Azure? 
Link prywatny platformy Azure umożliwia dostęp do usług Azure PaaS Services (na przykład Azure Storage i SQL Database) oraz hostowanych usług partnerów/partnerskich platformy Azure w ramach [prywatnego punktu końcowego](private-endpoint-overview.md) w sieci wirtualnej.

Ruch między siecią wirtualną a usługą porusza się w sieci szkieletowej firmy Microsoft. Ujawnienie usługi do publicznej sieci Internet nie jest już konieczne. Możesz utworzyć własną [prywatną usługę linku](private-link-service-overview.md) w sieci wirtualnej i dostarczyć jej klientom. Konfiguracja i użycie przy użyciu prywatnego linku platformy Azure jest spójne w ramach usług Azure PaaS, należących do klienta i współużytkowanych partnerów.

> [!IMPORTANT]
> Usługa Azure Private link jest teraz ogólnie dostępna. Ogólnie dostępne są zarówno prywatne, jak i prywatne usługi linkowe (usługa za usługę w warstwie Standardowa). Różne harmonogramy zostaną dołączone do prywatnego linku platformy Azure PaaS. Sprawdź poniższą sekcję [dostępności](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) , aby uzyskać dokładny stan usługi Azure PaaS on Private. Aby uzyskać znane ograniczenia, zobacz [prywatny punkt końcowy](private-endpoint-overview.md#limitations) i [Usługa łącza prywatnego](private-link-service-overview.md#limitations). 

![Omówienie prywatnego punktu końcowego](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Najważniejsze korzyści
Połączenie prywatne platformy Azure zapewnia następujące korzyści:  
- **Usługi dostępu prywatnego na platformie Azure**: łączenie sieci wirtualnej z usługami na platformie Azure bez publicznego adresu IP w źródle lub miejscu docelowym. Dostawcy usług mogą renderować swoje usługi w swoich własnych sieciach wirtualnych, a konsumenci mogą uzyskiwać dostęp do tych usług w lokalnej sieci wirtualnej. Platforma linków prywatnych będzie obsługiwała łączność między klientem i usługami za pośrednictwem sieci szkieletowej platformy Azure. 
 
- **Sieci lokalne i równorzędne**: usługi dostępu uruchomione na platformie Azure pochodzą z zasobów lokalnych za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute, tuneli VPN i równorzędnych sieci wirtualnych za pomocą prywatnych punktów końcowych. Nie trzeba konfigurować publicznej komunikacji równorzędnej ani przechodzić przez Internet w celu uzyskania dostępu do usługi. Link prywatny umożliwia bezpieczną metodę migrowania obciążeń do platformy Azure.
 
- **Ochrona przed wyciekiem danych**: prywatny punkt końcowy jest mapowany do wystąpienia zasobu PaaS zamiast całej usługi. Konsumenci mogą łączyć się tylko z określonym zasobem. Dostęp do dowolnego innego zasobu w usłudze jest zablokowany. Mechanizm ten zapewnia ochronę przed ryzykiem wycieku danych. 
 
- **Globalne zasięg**: Połącz się prywatnie z usługami działającymi w innych regionach. Sieć wirtualna konsumenta może znajdować się w regionie A i może łączyć się z usługami za linkiem prywatnym w regionie B.  
 
- Zapoznaj **się z własnymi usługami**: Włącz te same czynności i funkcje, aby umożliwić prywatną pracę usługi dla klientów na platformie Azure. Przez umieszczenie usługi za standardową Azure Load Balancer można ją włączyć dla linku prywatnego. Konsument może następnie połączyć się bezpośrednio z usługą przy użyciu prywatnego punktu końcowego we własnej sieci wirtualnej. Żądania połączenia można zarządzać przy użyciu przepływu wywołań zatwierdzenia. Połączenie prywatne platformy Azure działa dla klientów i usług należących do różnych dzierżawców Azure Active Directory. 

## <a name="availability"></a>Dostępność 
 Poniższa tabela zawiera listę prywatnych usług linków oraz regionów, w których są one dostępne. 

|Scenariusz  |Obsługiwane usługi  |Dostępne regiony | Stan  |
|:---------|:-------------------|:-----------------|:--------|
|Prywatne łącze dla usług należących do klienta |Usługi linków prywatnych za standardową Azure Load Balancer | Wszystkie regiony publiczne  | Ogólna dostępność <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
|Link prywatny dla usług Azure PaaS Services   | Azure Storage        |  Wszystkie regiony publiczne      | Ogólna dostępność <br/> [Dowiedz się więcej](/azure/storage/common/storage-private-endpoints)  |
|  | Usługa Azure Data Lake Storage 2. generacji        |  Wszystkie regiony publiczne      | Ogólna dostępność <br/> [Dowiedz się więcej](/azure/storage/common/storage-private-endpoints)  |
|  |  Azure SQL Database         | Wszystkie regiony publiczne      |   Ogólna dostępność <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)      |
|  |Analiza usługi Azure Synapse (SQL Data Warehouse)| Wszystkie regiony publiczne |Ogólna dostępność <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
|  |Azure Cosmos DB|  Wszystkie regiony publiczne |Ogólna dostępność <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints)|
|  |  Azure Database for PostgreSQL — pojedynczy serwer         | Wszystkie regiony publiczne      |   Ogólna dostępność <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  |  Azure Database for MySQL         | Wszystkie regiony publiczne      |   Ogólna dostępność <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  |  Azure Database for MariaDB         | Wszystkie regiony publiczne      |   Ogólna dostępność <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  |  W usłudze Azure Key Vault         | Wszystkie regiony publiczne      |   Ogólna dostępność   <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/key-vault/private-link-service)   |
|  |Usługa Azure Kubernetes Service — interfejs API Kubernetes | Wszystkie regiony publiczne      |   Ogólna dostępność   <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/aks/private-clusters)   |
|  |Azure Search | WSCHODNIE STANY USA, ZACHODNIE STANY USA 2, POŁUDNIOWO-ŚRODKOWE STANY USA |   Wersja zapoznawcza    |
|  |Azure Container Registry | Wszystkie regiony publiczne      |   Wersja zapoznawcza   |
|  |Azure App Configuration | Wszystkie regiony publiczne      |   Wersja zapoznawcza   |
|  |Azure Backup | WSCHODNIE STANY USA, ZACHODNIE STANY USA 2, POŁUDNIOWO-ŚRODKOWE STANY USA     |   Wersja zapoznawcza   |
|  |Centrum zdarzeń Azure | Wszystkie regiony publiczne      |   Wersja zapoznawcza    |
|  |Azure Service Bus | Wszystkie regiony publiczne      |   Wersja zapoznawcza   |
|  |Azure Relay | Wszystkie regiony publiczne      |   Wersja zapoznawcza   |
|  |Azure Event Grid| WSCHODNIE STANY USA, ZACHODNIE STANY USA 2, POŁUDNIOWO-ŚRODKOWE STANY USA      |   Wersja zapoznawcza   <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/event-grid/network-security)   |
|  |Azure Web Apps | WSCHODNIE STANY USA, ZACHODNIE STANY USA 2, POŁUDNIOWO-ŚRODKOWE STANY USA      |   Wersja zapoznawcza   <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)   |
|  |Azure Machine Learning | WSCHODNIE STANY USA, ZACHODNIE STANY USA 2, POŁUDNIOWO-ŚRODKOWE STANY USA      |   Wersja zapoznawcza   <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link)   |

Aby zapoznać się z najbardziej aktualnymi powiadomieniami, zapoznaj się ze [stroną usługi Azure Virtual Network Updates](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

Połączenie prywatne platformy Azure ma integrację z usługą Azure Monitor. Ta kombinacja umożliwia:

 - Archiwizowanie dzienników na koncie magazynu.
 - Przesyłanie strumieniowe zdarzeń do centrum zdarzeń.
 - Rejestrowanie Azure Monitor.

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


 
